# Smart Contract Tutorial

A step-by-step guide to writing, compiling, deploying, and interacting with a Solidity smart contract using Hardhat, Web3.js, and Node.js.

## Title
Smart Contract Deployment and Interaction Tutorial

## Usage
This project demonstrates how to:
- Write a Solidity smart contract.
- Compile the contract to generate its ABI and bytecode.
- Deploy the contract using Web3.js and Hardhat.
- Interact with the deployed contract to update and retrieve data.

## Demo Screenshots
### Compiling the Smart Contract
![Compile Demo](demo-compile.png)

### Deploying the Smart Contract
![Deploy Demo](demo-deploy.png)

### Interacting with the Smart Contract
![Interact Demo](demo-interact.png)

## Examples
- **Getting the value of `myNumber`:**
  ```javascript
  const myNumber = await myContract.methods.myNumber().call();
  console.log('myNumber:', myNumber);
  ```
- **Updating the value of `myNumber`:**
  ```javascript
  await myContract.methods.setMyNumber(42).send({ from: defaultAccount });
  ```

---

## Prerequisites
Before starting, ensure you have the following installed:
- **Node.js** (Recommended: v18.16.1 or later)
- **npm** (Recommended: v9.5.1 or later)

Verify the versions with the following commands:
```bash
$ node -v
$ npm -v
```

Basic familiarity with the command line, JavaScript, and Node.js is required.

---

## Steps

### Step 1: Set Up the Project
1. Create a new directory and navigate into it:
   ```bash
   mkdir smart-contract-tutorial
   cd smart-contract-tutorial
   ```
2. Initialize a Node.js project:
   ```bash
   npm init -y
   ```

### Step 2: Write the Smart Contract
Create a file named `MyContract.sol` and add the following Solidity code:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyContract {
    uint256 public myNumber;

    constructor(uint256 _myNumber) {
        myNumber = _myNumber;
    }

    function setMyNumber(uint256 _myNumber) public {
        myNumber = _myNumber;
    }
}
```

### Step 3: Compile the Smart Contract
1. Install the Solidity compiler:
   ```bash
   npm install solc@^0.8.0
   ```
2. Create a file named `compile.js` and add the following code:
   ```javascript
   const solc = require('solc');
   const path = require('path');
   const fs = require('fs');

   const contractName = 'MyContract';
   const fileName = `${contractName}.sol`;

   const contractPath = path.join(__dirname, fileName);
   const sourceCode = fs.readFileSync(contractPath, 'utf8');

   const input = {
       language: 'Solidity',
       sources: {
           [fileName]: { content: sourceCode },
       },
       settings: {
           outputSelection: {
               '*': { '*': ['*'] },
           },
       },
   };

   const compiledCode = JSON.parse(solc.compile(JSON.stringify(input)));

   const bytecode = compiledCode.contracts[fileName][contractName].evm.bytecode.object;
   const bytecodePath = path.join(__dirname, 'MyContractBytecode.bin');
   fs.writeFileSync(bytecodePath, bytecode);

   console.log('Contract Bytecode:', bytecode);

   const abi = compiledCode.contracts[fileName][contractName].abi;
   const abiPath = path.join(__dirname, 'MyContractAbi.json');
   fs.writeFileSync(abiPath, JSON.stringify(abi, null, 2));

   console.log('Contract ABI:', abi);
   ```
3. Compile the contract:
   ```bash
   node compile.js
   ```

### Step 4: Set Up Web3.js and Hardhat
1. Install required dependencies:
   ```bash
   npm install web3 hardhat
   ```
2. Initialize a Hardhat project:
   ```bash
   npx hardhat init
   ```
3. Start the Hardhat development network:
   ```bash
   npx hardhat node
   ```

### Step 5: Deploy the Contract
1. Create a `deploy.js` file:
   ```javascript
   const { Web3 } = require('web3');
   const path = require('path');
   const fs = require('fs');

   const web3 = new Web3('http://127.0.0.1:8545/');

   const bytecode = fs.readFileSync(path.join(__dirname, 'MyContractBytecode.bin'), 'utf8');
   const abi = require('./MyContractAbi.json');
   const myContract = new web3.eth.Contract(abi);

   async function deploy() {
       const accounts = await web3.eth.getAccounts();
       const defaultAccount = accounts[0];

       const contract = myContract.deploy({
           data: '0x' + bytecode,
           arguments: [1],
       });

       const gas = await contract.estimateGas({ from: defaultAccount });
       const deployedContract = await contract.send({ from: defaultAccount, gas });

       console.log('Contract deployed at:', deployedContract.options.address);
   }

   deploy();
   ```
2. Deploy the contract:
   ```bash
   node deploy.js
   ```

### Step 6: Interact with the Contract
1. Create an `interact.js` file:
   ```javascript
   const { Web3 } = require('web3');
   const path = require('path');
   const fs = require('fs');

   const web3 = new Web3('http://127.0.0.1:8545/');

   const deployedAddress = fs.readFileSync(path.join(__dirname, 'MyContractAddress.txt'), 'utf8');
   const abi = require('./MyContractAbi.json');
   const myContract = new web3.eth.Contract(abi, deployedAddress);

   async function interact() {
       const accounts = await web3.eth.getAccounts();
       const defaultAccount = accounts[0];

       const myNumber = await myContract.methods.myNumber().call();
       console.log('myNumber:', myNumber);

       await myContract.methods.setMyNumber(42).send({ from: defaultAccount });
       const updatedMyNumber = await myContract.methods.myNumber().call();
       console.log('Updated myNumber:', updatedMyNumber);
   }

   interact();
   ```
2. Run the interaction script:
   ```bash
   node interact.js
   ```

## How to Connect Hardhat to MetaMask
1. Add a custom network in MetaMask:
   - **Network Name:** Hardhat Network
   - **RPC URL:** http://127.0.0.1:8545/
   - **Chain ID:** 31337
   - **Currency Symbol:** ETH
2. Import a test account:
   - Use one of the private keys displayed when you start the Hardhat node.

---

## License
This project is licensed under the [MIT License](LICENSE).

