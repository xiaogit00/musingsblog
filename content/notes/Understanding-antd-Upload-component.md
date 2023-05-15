---
title: "Understanding Antd's Upload Component"
date: 2023-05-15T15:42:11+08:00
# weight: 1
# aliases: ["/first"]
tags: ["upload", "forms", "antd", "files", "blob"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
So, whilst working on the file upload and Docusign feature of Kotakit, I realized that I needed to get a deeper understanding of Antd's [upload component](https://ant.design/components/upload#api) in order to tune it to my desired effects. 

Invoking the upload component is easy. All you need to do is to wrap a `<Button>` component up within an `<Upload>` component:

```javascript 
  <Upload {...props}>
    <Button icon={<UploadOutlined />}>Click to Upload</Button>
  </Upload>
```

Where it gets tricky is the `props` used. The Upload component has many flags which allow you to adjust the desired feature of your upload component. 

By default, you supply the following props to the Upload component: 

```javascript
const props = {
  name: 'file',
  action: 'https://www.mocky.io/v2/5cc8019d300000980a055e76',
  headers: {
    authorization: 'authorization-text',
  },
  onChange(info) {
    if (info.file.status !== 'uploading') {
      console.log(info.file, info.fileList);
    }
    if (info.file.status === 'done') {
      message.success(`${info.file.name} file uploaded successfully`);
    } else if (info.file.status === 'error') {
      message.error(`${info.file.name} file upload failed.`);
    }
  },
};
```

The upload component is designed such that the default behavior *whenever you select a file to upload* is to send a POST request to the URL in the `action` prop, which, in this case, is '*www.mocky.io...*'. 

In many cases, you *dont* want to upload it directly to the route, but only on a click of a button. 

In order to do that, first you'll have to add a `beforeUpload` prop to the `<Upload>` component: 

```javascript
 const props = {
    beforeUpload: () => false;
    },
```

This will disable the default sending of file to the uploading URL. 

Now, according to Antd's rather cryptic documentation, the `beforeUpload` function is executed before uploading. So if you return false, it prevents the actual uploading from happening. 

The next thing to understand is how the file is being accessed within the code itself. By default, you supply an `onChange` prop to the `<Upload>` component. This function is executed whenever uploading state is changing. When uploading state changes, this function returns the following object: 
```javascript
{
  file: { /* ... */ },
  fileList: [ /* ... */ ],
  event: { /* ... */ },
}
```

This is how it's implemented in the default example:

```javascript
onChange: (info) => {
    if (info.file.status !== 'uploading') {
    console.log(info.file, info.fileList);
    }
    if (info.file.status === 'done') {
    message.success(`${info.file.name} file uploaded successfully`);
    } else if (info.file.status === 'error') {
    message.error(`${info.file.name} file upload failed.`);
    }
}
```

However, for our case, since we're not directly sending the request via our action prop, this onChange prop is less relevant. Rather, our variation will use a manual approach to handle the upload.

In Antd parlace, this variation is demonstrated within the [Upload Manually](https://ant.design/components/upload#components-upload-demo-upload-manually) example. 

**Upload manually**
```javascript
const App = () => {
  const [fileList, setFileList] = useState([]);
  const [uploading, setUploading] = useState(false);

  const handleUpload = () => {
    const formData = new FormData();
    fileList.forEach((file) => {
      formData.append('files[]', file);
    });
    setUploading(true);
    // You can use any AJAX library you like
    fetch('https://www.mocky.io/v2/5cc8019d300000980a055e76', {
      method: 'POST',
      body: formData,
    })
      .then((res) => res.json())
      .then(() => {
        setFileList([]);
        message.success('upload successfully.');
      })
      .catch(() => {
        message.error('upload failed.');
      })
      .finally(() => {
        setUploading(false);
      });
  };

  const props = {
    onRemove: (file) => {
      const index = fileList.indexOf(file);
      const newFileList = fileList.slice();
      newFileList.splice(index, 1);
      setFileList(newFileList);
    },
    beforeUpload: (file) => {
      setFileList([...fileList, file]);
      return false;
    },
    fileList,
  };

  return (
    <>
      <Upload {...props}>
        <Button icon={<UploadOutlined />}>Select File</Button>
      </Upload>
      <Button
        type="primary"
        onClick={handleUpload}
        disabled={fileList.length === 0}
        loading={uploading}
        style={{
          marginTop: 16,
        }}
      >
        {uploading ? 'Uploading' : 'Start Upload'}
      </Button>
    </>
  );
};
export default App;
```

I've also implemented a variation of this within my MVP for [Flashcards app](https://github.com/xiaogit00/asmarterwaytolearnx/blob/main/components/exercisesPage/UploadCSV.tsx). 

Let's break down what is going on in this code sample. 

The first thing to understand is what else is attached to the `beforeUpload` prop:

```javascript
beforeUpload: (file) => {
      setFileList([...fileList, file]);
      return false;
},
```
Here, the `beforeUpload` prop will take a `file` as argument. This is essentially the file that you've uploaded. Going into `beforeUpload`'s type definition, we see the following: 

`beforeUpload?: (file: RcFile, FileList: RcFile[]) => BeforeUploadValueType | Promise<BeforeUploadValueType>;`

So apparently, it is a function that takes a `file` or `fileList` of `RcFile` type, and returns

What is RcFile? Doing some type drilldown, I got the following:

```javascript
export interface RcFile extends OriRcFile {
    readonly lastModifiedDate: Date;
}
```

```javascript
export interface RcFile extends File {
    uid: string;
}
```

```javascript
interface File extends Blob {
    readonly lastModified: number;
    readonly name: string;
    readonly webkitRelativePath: string;
}
```

So basically, an RcFile is an OriRcFile just with a `lastModifiedDate` field, which in turn is a `File` with a `uid` field, which in turn is a Blob with a `lastModified` and `name` field. 

Essentially, `beforeUpload` allows us access to the Blob that we uploaded through the file variable. 

A concrete example of the `file` data we can access is: 
```javascript
{
    uid: "rc-upload-1684141256691-2",
    lastModified: 1683611584422,
    lastModifiedDate: Tue May 09 2023 13:53:04 GMT+0800 (Singapore Standard Time) {},
    name: "receipt.pdf",
    size: 109116,
    type: "application/pdf",
    webkitRelativePath: ""
}
```

Okay, great. Thus, this line:  

`setFileList([...fileList, file]);`

essentially simply appends the file uploaded into the `fileList` variable. If we're uploading just 1 file, we could of course just create a file variable. 

To sum it up, the purpose of the `beforeUpload` prop is to attach the uploaded file object to the `fileList` / `file` state variable, so that the `handleUpload` function can have access to it. 

### handleUpload

The handleUpload function 

```javascript
const handleUpload = () => {
    const formData = new FormData();
    fileList.forEach((file) => {
      formData.append('files[]', file);
    });
    setUploading(true);
    // You can use any AJAX library you like
    fetch('https://www.mocky.io/v2/5cc8019d300000980a055e76', {
      method: 'POST',
      body: formData,
    })
      .then((res) => res.json())
      .then(() => {
        setFileList([]);
        message.success('upload successfully.');
      })
      .catch(() => {
        message.error('upload failed.');
      })
      .finally(() => {
        setUploading(false);
      });
  };
```

So a more novel construct here is the use of the `FormData()` API. 

According to the [Mozilla Web API documentation](https://developer.mozilla.org/en-US/docs/Web/API/FormData), the 'FormData interface provides a way to construct a set of key/value pairs representing form fields and their values, which can be used in `fetch()` methods. It uses the same format a form would use if the encoding type were set to "multipart/form-data".'

It's an object with the following methods:
- .append()
- .delete()
- .entries()
, etc. 

Here's how you can use it. 

```javascript
const formData = new FormData()

formData.append("username", "Jack")
formData.append("userFile", fileInputElement.files[0])

fetch('https://www.mocky.io/v2/5cc8019d300000980a055e76', {
      method: 'POST',
      body: formData,
})
```

It basically is a way for you to programatically generate form data, an alternative to using forms and submitting:

```javascript
function handleSubmit(e) {
    // Prevent the browser from reloading the page
    e.preventDefault();
    // Read the form data
    const form = e.target;
    const formData = new FormData(form);
    // You can pass formData as a fetch body directly:
    fetch('/some-api', { method: form.method, body: formData });}
...
<form method="post" onSubmit={handleSubmit}>
```
Here, your form `onSubmit` calls the event handler, which captures the event as well as all the form values, which are sent to the `e.target` variable. You use this variable to create the formData, which you then pass as body to some API route. 

So essentially, you can specify the form values either using `formData.append()` or using the values specified within the `form` itself. 

Okay great!

Time to move back to my code in question. 

```javascript
const handleUpload = () => {
    const formData = new FormData();
    fileList.forEach((file) => {
      formData.append('files[]', file);
    });
```

Here, all you're doing is creating a new formData object, and appending each file in the `fileList` to `formData`. In my case, if I am just uploading one file, then there's no need to use a fileList. 

### Conclusion
Alright, this is great. In this short deep dive, I've managed to figure out the precise implementation of the Upload component. Turns out, it follows quite a standard file upload design. To summarize, the steps are below:

1. Invoke the Upload Component
2. Define `beforeUpload` as prop that allows you to set the file as state variable, as well as cancel default request. 
3. Within submit handler, access the file / fileList, assign it to formData (if necessary), and send it as body of a POST request. 

That's how you handle files on the frontend. 