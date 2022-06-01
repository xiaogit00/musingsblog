---
title: "Exploring Hardhat P2"
date: 2022-06-01T12:26:06+08:00
# weight: 1
# aliases: ["/first"]
tags: ["hardhat", "smart contract", "solidity"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
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
Alright, so ytd I was exposed to the basics of Hardhat. Today, I'll get a bit more in detail on what certain pieces of the development workflow works.

A big part of hardhat development is interacting with the ethers.js object. This object allows you to:

1. Simulate accounts through `signers`
2. Compile contracts through contract factories `.getContractFactory`
3. Deploy contracts `.deploy()`
4. Interact with deployed contracts
5. Act as other others through `.connect()`

Then, there are some unique test conditions that I havn't seen before: `revertedWith()`.

### Signers

First of all, **Signers** are basically objects that simulate accounts in ethers. To create a signer, you can simply do:

`const [owner] = await ethers.getSigners();`  
owner is now a signer, and you can get owner address with `owner.address`

### .getSigners() method

Second, the `.getSigners();` method of ethers returns all the accounts in the node we're connected to. A common way of assigning accts is:  
`const [owner, addr1, addr2, ...addrs] = await ethers.getSigners();`

### Contract Factories
Contract factories are abstractions that allow you to *deploy* instances of contracts. They're literally factories that churn out new instances of whatever smart contract you assign the factory to.

A typical process goes as follows: after a contract is compiled, we can supply the name of the contract into the argument of `.getContractFactory()` method of ethers, like so:

`const Token = await ethers.getContractFactory("Token")`

This will create a Token factory, so to speak, whose job would be to create instances of the Token contract.

(The analogous version on Remix is that once you compile a contract, you'll create a compilation version, and then you can then click 'deploy' whichever version you want.)

### Deployment from Contract Factory.
You can then instantiate/deploy the contract:
`const hardhatToken = await Token.deploy()`  

This will **return a promise that resolves to a contract**.

It's important to note that the `.deploy()` method is called on the contract factory object. At least in a test environment, that is the case.

### Interacting with a deployed contract

Once a contract is deployed, you can interact with it by calling its methods:  
`const ownerBalance = await hardhatToken.balanceOf(owner.address)`


### By default, contracts are deployed by first signer.

One quirk: ContractFactory and contract are connected to the first signer by default. This is significant as token contract gives supply to whoever that deployed it.

### Connecting to a different account for contract interactions
Again, by default, the person interacting with the contract is the owner. Consider this method:
``` javascript
const [owner, addr1, addr2] = await ethers.getSigners();
await hardhatToken.transfer(addr1.address, 50);
```
The above would transfer 50 tokens from the owner to address 1.

If you want to transfer 50 tokens from addr1 to addr2, you'll need to do the following:
`await hardhatToken.connect(addr1).transfer(addr2.address, 50);`

The gist is, you can simply use `.connect()` method to interact with the contract using another acct.

### Some idiosyncratic test condition keywords:
To equal (standard):  
`expect(await hardhatToken.totalSupply()).to.equal(ownerBalance)`
Expect transactions to be reverted:  
`expect(
    hardhatToken.connect(addr1).transfer(owner.address, 1)
      ).to.be.revertedWith("Not enough tokens")`

### Debugging
There's this neat feature in hardhat that helps you debug your code in solidity. You can basically console.log within a solidity contract.

`import "hardhat/console.sol";`

`console.log(("Sender balance is %s tokens", balances[msg.sender]))`

That's it.

### Deploying to live network
First, you'll need two things. The URL of any ethereum node/gateway (in this case, I'll use Alchemy), and the Ropsten private key.  

``` javascript
const ALCHEMY_API_KEY = "KEY";
const ROPSTEN_PRIVATE_KEY = "YOUR ROPSTEN PRIVATE KEY";
module.exports = {
  solidity: "0.7.3",
  networks: {
    ropsten: {
      url: `https://eth-ropsten.alchemyapi.io/v2/${ALCHEMY_API_KEY}`,
      accounts: [`${ROPSTEN_PRIVATE_KEY}`]
    }
  }
};
```
To deploy on Ropsten you'll of course need some ropsten eth.
Then, change metamask network to Ropsten before transacting.

Then, run:
`npx hardhat run scripts/deploy.js --network ropsten`


Let's say you have this script called `deploy.js`

``` javascript
async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account:", deployer.address);

  console.log("Account balance:", (await deployer.getBalance()).toString());

  const Token = await ethers.getContractFactory("Token");
  const token = await Token.deploy();

  console.log("Token address:", token.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

To run the script on other network, you run:
`npx hardhat run scripts/deploy.js --network <network-name>`
