---
title: "Exploring Hardhat"
date: 2022-05-31T16:33:38+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Hardhat", "Web3", "Solidity", "Smart contracts"]
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

Okay so after some digging around, I've made the decision on the 21st day of my journey into Web3 that I'll be sticking to Hardhat for full stack dApp development.

At this point, I literally know *nothing* about Hardhat.

No matter. My first step is to use the Hardhat documentation.

# Basics of Hardhat

## Installing Hardhat
`npm init`
`npm install --save-dev hardhat`

## Creating a Hardhat project
`npx hardhat`

## Getting the available commands
`npx hardhat` in project folder

## Getting the accounts
`npx hardhat accounts`

# Compiling, testing, deploying

## Compiling all the contracts in the 'contracts/' folder
`npx hardhat compile`

## Running the tests in test folder*
`npm hardhat test`

*This is what the test file, `sample-test.js` looks like. This is a classic javascript test file.
``` javascript
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("Greeter", function () {
  it("Should return the new greeting once it's changed", async function () {
    const Greeter = await ethers.getContractFactory("Greeter");
    const greeter = await Greeter.deploy("Hello, world!");
    await greeter.deployed();

    expect(await greeter.greet()).to.equal("Hello, world!");

    const setGreetingTx = await greeter.setGreeting("Hola, mundo!");

    // wait until the transaction is mined
    await setGreetingTx.wait();

    expect(await greeter.greet()).to.equal("Hola, mundo!");
  });
});
```
## Deploying the contracts
To deploy contracts, we use the Hardhat script. It is inside 'scripts' folder.
`npx hardhat run scripts/sample-script.js`

sample-scripts.js looks like this:
``` javascript

// We require the Hardhat Runtime Environment explicitly here. This is optional
// but useful for running the script in a standalone fashion through `node <script>`.
//
// When running the script with `npx hardhat run <script>` you'll find the Hardhat
// Runtime Environment's members available in the global scope.

const hre = require("hardhat");

async function main() {
  // Hardhat always runs the compile task when running scripts with its command
  // line interface.
  //
  // If this script is run directly using `node` you may want to call compile
  // manually to make sure everything is compiled
  // await hre.run('compile');

  // We get the contract to deploy
  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello, Hardhat!");

  await greeter.deployed();

  console.log("Greeter deployed to:", greeter.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });

```

So here, the main thing that happens is that the async main function runs. Before that, the `hre` actually refers to the hardhat runtime environment. It says in the comments that this is actually optional, but it's required when you want to run the script in a standalone fashion using `node <script>`. Cool.

Within the code, the `hre.ethers` object allows us to get contract factory, and we deploy the contract supplying "Hello Hardhat!" as argument. We wait for it to be deployed, and then we print the greeter address. Pretty intuitive, apart from some of the methods of the `hre` object, and the deploy() method.

## On Hardhat network
Hardhat always spins up an in-memory instance of Hardhat Network on startup by default.

You can also run a Hardhat network in a standalone fashion so that external clients can connect to it - it could be Metamask, Dapp front-end, or a script.

### Running Hardhat network as standalone:
`npx hardhat node`
This will expose a JSON-RPC interface to Hardhat network. To use it, connect your wallet or app to http://localhost:8545.

If you want to connect hardhat to this node to run a deployment script against it, you'll neeed to add `--network localhost`
`npx hardhat run scripts/sample-script.js --network localhost`

That's about it for this beginning exploration.

Thoughts:
Wow okay so basically it allows me to spin up a network, deploy contracts through command line, write tests, and perform scripts. Everything that I need. Now I'll just need to get into the details.
