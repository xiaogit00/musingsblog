---
title: "Dropping Into Djikstra (P1)"
date: 2024-01-27T23:44:22+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Skating", "Data structures and Algorithms", "Software engineering"]
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
<!-- 
Main point: there are remarkable similarities between skateboarding (ramps and bowls) and data structure and algorithms. 
Over the last few months I've been doing 2 things concurrently: skateboarding and coding interviews. I found that there's a remarkable similarity between both of them. 
 -->


My skateboard routine is fairly standard. I'll pack my gear into my Adidas backpack, throw in a bottle and my keys, and head out the door grabbing my skateboard and the helmet. I don't bother bringing my phone. I'll get to my bike parked beneath my block, mount the skateboard onto the backseat, and cycle towards the direction of Bukit Batok MRT - passing by the station, passing by Princess Elizabeth Primary, over the green fields before reaching Yuhua, followed by Chinese Garden MRT, and then finally reaching the skatepark at Lakeside. The entire journey takes around 20mins or so, and I treat it as warm up. 

I got into skateboarding mainly to get better at surfing. For the readers who are unawares, yes, it is a highly transferable skill. This is especially true if you skate ramps and bowls. Technically I've started skateboarding when I was 16, but I never got beyond mere cruising. So when I restarted my skateboarding journey a few months ago, I was still fairly new. 

Lakeside skate park is a dream. Situated right beside Jurong lake, the skatepark is pretty immense for an infrastructure in Singapore. It's immaculately designed, full of trails, ramps, bowls, inclines, and various sections with their own characteristics. In the middle there is a little pond, and around the pond, a fun bumpy trail loved by all kids, bikers, and also skaters. It wouldn't be a stretch to say that this facility is world class. Every time I get out of the bowl and peer across the lake, listening to the soft rumbling of the train and the tacking of the skateboard wheels, I feel a sense of calm. I do a quick mental and emotional check in and it's always 9/10. 

When I restarted my skateboarding journey again 3 months ago, I really wanted to ride ramps. And so I watched Youtube on how to do that. Someone suggested getting used to the feeling of rocking back and forth the ramps first. I located some small ramps at the skatepark where there were no one and did just that. 


Starting from the bottom, I'll give one kick with my left foot and quickly get into position. I'll roll a little and the top of the skateboard will hit the incline. There's nothing like the mix of fear and thrill when you first start doing that, as your body adjusts to this new sensation. As you roll back down, you realize you need to shift your attention and weight slightly, in anticipation of rolling up the incline on the other side. You'll lose speed, and you'll need to give a little boost again. All the while, your legs are getting used to the optimal positioning in order to pull off these rapid sequence of actions, getting enough speed, and standing in the right position to tackle the inclines. 

These were the basics. It wasn't difficult to master, all it took was around 2 to 3 sessions of repeatedly doing the same thing. Once I got better, I was looking around for what to do next. And according to the same video, I should be learning how to pump. 

What is pumping? Just like how you'd pull back on the rails in order to swing higher on a swing, pumping is the action of bouncing yourself slightly right before you hit the incline, in order to use an upward momentum to propel yourself higher up the face of the ramp. It's little like jumping, except your feet never leave the board. All it takes a slight bend of the knee at the right moments. 

Pumping was tedious at the start, but with a bit of practice, I quickly got the hang of it. Now, pumping is not what I want to speak about.

What I really want to speak about is the drop in. 

In every skateboarder's journey, they'll encounter this beast called the drop in. The drop in is a true beast because it tests every part of you: your coordination, your self-belief, and most importantly whether you can overcome your fear. (Don't know what a drop in is? Check out this [video](https://www.youtube.com/watch?v=6wIUnmPlGNo&t=218s&ab_channel=SkateParkLessons)).

The theory of a drop in is simple: at the top of the ramp, lock the back of your truck onto the coping; and once you're ready to go down, stomp your front foot onto the board, and lean in. You should be riding down.

Now, that's the theory. I still remember when I first locked my truck onto the coping and looked down a 0.5m ramp, I froze, utterly. I had no idea what I was supposed to do - or rather, I had an idea, but every nerve in my body was fighting that idea. When you think about it, it is in fact our defensive reflex kicking in. Since childhood, we've been conditioned to avoid falling forward. Whilst a backward fall will end in some bruises on your bum or thighs, a forward fall, especially on an incline, will typically mean something way more nasty.

So yes, in theory, I knew how to do it. I knew I was supposed to stamp down on the board with my left foot, and then lean in. But there was an invisible force operating inside me that is preventing me from doing that. I stayed in that position for a long time, battling that force. My guts were wrenching. I ran through the theory again and again in my head, thinking, don't think, just do it. I plucked my courage after a few minutes and finally stomped down with my left foot. As my board hit the ramp, I leaned back instinctively. In a split second, I was on my back.

What went wrong? I went against the advice of all the videos I've watched, which emphasized the need to lean forward. "It's way more dangerous to fall backwards...", every skater bro in Youtube video went. But I just couldn't lean forward. The action of leaning forward did not come naturally. In fact, leaning forward, in this case, is fucking hard. Our bodies are just not wired in this way. 

It's ironic, isn't it? In certain contexts, it's precisely the natural reflex to fall back (pardon the pun) onto patterns of safety that is more dangerous for you. Since then, I've used the metaphor of 'leaning in' to map certain things in my life. When something feels risky, or uncertain, sometimes it's the counter intuitive thing of leaning in that'll get you out of trouble. Poker provides the perfect analogy. When you're doing a bluff, it's best to lean in and bluff big. (I am really not an authority on poker, I've only ever lost money and this is just what I hear from my friends.) If you cower, you can expect a painful fall. Investing provides another example: the best times to buy into a stock is when prices are low and times are uncertain. When every reflex in your body is telling you to pull back, you *need* to lean forward.

In investing, as in skateboarding, the risk of failure can be greatly reduced if you know what you're doing. During a drop in, you need to know the 'secrets' of doing it right. Not only must you know that in theory, your body needs to know that - and your body needs to be very familiar with that feeling. That familiarity only comes with deliberate applied practice, where you gain foundational competencies in key skills such as rocking up and down a ramp, leaning forward, and committing. A similar process applies to investing. There exists a set of core competencies which you must master for you to not fall face flat. And those are: knowing how to read financial statements, understanding the key drivers of protifability in industries and businesses, knowing how sustainable competitive advantage is being achieved, and knowing what truly gives rise to consistent earnings power. All these take a lot of time to train and deeply embody. And when all these is done and your research is telling you that it's a go, then you have to lean in, even when the steep price drop down the ramp looks scary. If you hesitate, you *will* fall. 

Now, I started this post intending to write about the parallels between skateboarding and data structure and algorithms. But I ended up writing about skateboarding and investing instead lol. I guess I'll write about it next week. 

