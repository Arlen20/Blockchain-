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
   mkdir Blockcahin_1
   cd Blockchain_1
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
    address public owner;

    constructor(uint256 _myNumber) {
        myNumber = _myNumber;
        owner = msg.sender;
    }

    // Function to receive Ether
    receive() external payable {}

    // Fallback function to handle non-matching function calls
    fallback() external payable {}

    // Function to withdraw Ether
    function withdraw() external {
        require(msg.sender == owner, "Only the owner can withdraw");
        payable(owner).transfer(address(this).balance);
    }

    // Function to get the contract's Ether balance
    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }

    // Function to update myNumber
    function setMyNumber(uint256 _myNumber) public {
        myNumber = _myNumber;
    }
}


### Step 3: Compile the Smart Contract
1. Install the Solidity compiler:
   ```bash
   npm install solc@^0.8.0
   ```
2. Create a file named `compile.js` and add the following code:
   ```javascript
	const solc = require('solc')
	const path = require('path')
	const fs = require('fs')

	const contractName = 'MyContract'
	const fileName = `${contractName}.sol`
	
	// Read the Solidity source code from the file system
	const contractPath = path.join(__dirname, fileName)
	const sourceCode = fs.readFileSync(contractPath, 'utf8')
	
		// solc compiler config
		const input = {
		language: 'Solidity',
		sources: {
			[fileName]: {
				content: sourceCode,
			},
		},
		settings: {
			outputSelection: {
				'*': {
					'*': ['*'],
				},
			},
		},
	}

	// Compile the Solidity code using solc
	const compiledCode = JSON.parse(solc.compile(JSON.stringify(input)))
	
	// Get the bytecode from the compiled contract
	const bytecode =
		compiledCode.contracts[fileName][contractName].evm.bytecode.object
	
	// Write the bytecode to a new file
	const bytecodePath = path.join(__dirname, 'MyContractBytecode.bin')
	fs.writeFileSync(bytecodePath, bytecode)
	
	// Log the compiled contract code to the console
	console.log('Contract Bytecode:\n', bytecode)
	
	// Get the ABI from the compiled contract
	const abi = compiledCode.contracts[fileName][contractName].abi
	
	// Write the Contract ABI to a new file
	const abiPath = path.join(__dirname, 'MyContractAbi.json')
	fs.writeFileSync(abiPath, JSON.stringify(abi, null, '\t'))
	
	// Log the Contract ABI to the console
	console.log('Contract ABI:\n', abi)

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
![Screenshot 2024-12-29 183716](https://github.com/user-attachments/assets/cf5b6c2c-e0da-49c9-9ae4-ce081bb74f70)

### Step 5: Deploy the Contract
1. Create a `deploy.js` file:
   ```javascript
	   const { Web3 } = require('web3')
	const path = require('path')
	const fs = require('fs')
	
	const web3 = new Web3('http://127.0.0.1:8545/')
	
	const bytecodePath = path.join(__dirname, 'MyContractBytecode.bin')
	const bytecode = fs.readFileSync(bytecodePath, 'utf8')
	
		const abi = require('./MyContractAbi.json')
		const myContract = new web3.eth.Contract(abi)
		
		async function deploy() {
			const accounts = await web3.eth.getAccounts()
			const deployer = accounts[0]
			console.log('Deployer account:', deployer)
		
			const contract = myContract.deploy({
			data: '0x' + bytecode,
			arguments: [1],
		})
	
		const gasEstimate = await contract.estimateGas({ from: deployer })
		console.log('Estimated gas:', gasEstimate)
	
		const instance = await contract.send({
			from: deployer,
			gas: gasEstimate,
		})
	
		console.log('Contract deployed at:', instance.options.address)
		fs.writeFileSync('./MyContractAddress.txt', instance.options.address)
	}

	deploy().catch(console.error)


      
2. Deploy the contract:
   ```bash
   node deploy.js
   ```

### Step 6: Interact with the Contract
1. Create an `interact.js` file:
   ```javascript
   const { Web3 } = require('web3')

	// Initialize Web3 instance with Remix's provider
	const web3 = new Web3(window.ethereum) // This works with MetaMask
	
	const deployedAddress = 'your_contract_address_here' // Replace with your contract address from Remix
		const abi = [
			/* ABI from Remix (copy and paste) */
	]
	
	const myContract = new web3.eth.Contract(abi, deployedAddress)
	
	async function interact() {
		// Request account access if needed (MetaMask)
		await window.ethereum.request({ method: 'eth_requestAccounts' })
	
		const accounts = await web3.eth.getAccounts()
		const user = accounts[0]
	
		console.log('Interacting with contract at address:', deployedAddress)
	
		// Check initial balance
		try {
			const balance = await myContract.methods.getBalance().call()
			console.log(
				'Contract balance:',
				web3.utils.fromWei(balance, 'ether'),
			'ETH'
		)
	} catch (error) {
		console.error('Error getting contract balance:', error)
	}

	// Send Ether to the contract
	try {
		const tx = await web3.eth.sendTransaction({
			from: user,
			to: deployedAddress,
			value: web3.utils.toWei('0.1', 'ether'),
			gas: 2000000, // Set appropriate gas limit
		})
		console.log(
			'Sent 0.1 Ether to the contract. Transaction Hash:',
			tx.transactionHash
		)
	} catch (error) {
		console.error('Error sending Ether to contract:', error)
	}

	// Check updated balance
	try {
		const updatedBalance = await myContract.methods.getBalance().call()
		console.log(
			'Updated contract balance:',
			web3.utils.fromWei(updatedBalance, 'ether'),
			'ETH'
		)
	} catch (error) {
		console.error('Error getting updated contract balance:', error)
	}

	// Update myNumber
	try {
		const tx = await myContract.methods.setMyNumber(100).send({ from: user })
		console.log(
			'Updated myNumber to 100. Transaction Hash:',
			tx.transactionHash
		)
	} catch (error) {
		console.error('Error updating myNumber:', error)
	}

	// Withdraw funds (only the owner can withdraw)
	try {
		const tx = await myContract.methods.withdraw().send({ from: user })
		console.log(
			'Funds withdrawn by owner. Transaction Hash:',
			tx.transactionHash
		)
	} catch (error) {
		console.error('Error withdrawing funds:', error)
	}
	}

	interact().catch(console.error)

2. Run the interaction script:
   ```bash
   node interact.js
   ```
![Screenshot 2024-12-29 183235](https://github.com/user-attachments/assets/4aba8ec4-eaaa-4f02-aede-5734a3117d9a)
![Screenshot 2024-12-29 183926](https://github.com/user-attachments/assets/b8b92c0e-f535-430c-9273-e5a99bbee71d)

## How to Connect Hardhat to MetaMask
1. Add a custom network in MetaMask:
   - **Network Name:** Hardhat Network
   - **RPC URL:** http://127.0.0.1:8545/
   - **Chain ID:** 31337
   - **Currency Symbol:** ETH
   - ![Screenshot 2024-12-29 183611](https://github.com/user-attachments/assets/6bf8e01c-0d41-4a6d-8b52-ecedfdb16bff)
![Screenshot 2024-12-29 183651](https://github.com/user-attachments/assets/8c2a62d2-4d54-4fef-87bb-577e182ed4ee)

2. Import a test account:
   - Use one of the private keys displayed when you start the Hardhat node.

---
![Screenshot 2024-12-29 184106](https://github.com/user-attachments/assets/80bf015c-1698-4d96-acf0-1ef94048aff8)
![Screenshot 2024-12-29 184435](https://github.com/user-attachments/assets/8d1e25c2-17d1-4d59-8775-54dbf0009938)
![Screenshot 2024-12-29 184614](https://github.com/user-attachments/assets/c713fa95-38f0-4aa3-b922-7da3de1c731f)

## License
This project is licensed under the [MIT License](LICENSE).

