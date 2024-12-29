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

Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.

Account #0: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d

Account #2: 0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC (10000 ETH)
Private Key: 0x5de4111afa1a4b94908f83103eb1f1706367c2e68ca870fc3fb9a804cdab365a

Account #3: 0x90F79bf6EB2c4f870365E785982E1f101E93b906 (10000 ETH)
Private Key: 0x7c852118294e51e653712a81e05800f419141751be58f605c371e15141b007a6

Account #4: 0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65 (10000 ETH)
Private Key: 0x47e179ec197488593b187f80a00eb0da91f1b9d0b13f8733639f19c30a34926a

Account #5: 0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc (10000 ETH)
Private Key: 0x8b3a350cf5c34c9194ca85829a2df0ec3153be0318b5e2d3348e872092edffba

Account #6: 0x976EA74026E726554dB657fA54763abd0C3a0aa9 (10000 ETH)
Private Key: 0x92db14e403b83dfe3df233f83dfa3a0d7096f21ca9b0d6d6b8d88b2b4ec1564e

Account #7: 0x14dC79964da2C08b23698B3D3cc7Ca32193d9955 (10000 ETH)
Private Key: 0x4bbbf85ce3377467afe5d46f804f221813b2bb87f24d81f60f1fcdbf7cbf4356

Account #8: 0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f (10000 ETH)
Private Key: 0xdbda1821b80551c9d65939329250298aa3472ba22feea921c0cf5d620ea67b97

Account #9: 0xa0Ee7A142d267C1f36714E4a8F75612F20a79720 (10000 ETH)
Private Key: 0x2a871d0798f97d79848a013d4936a73bf4cc922c825d33c1cf7073dff6d409c6

Account #10: 0xBcd4042DE499D14e55001CcbB24a551F3b954096 (10000 ETH)
Private Key: 0xf214f2b2cd398c806f84e317254e0f0b801d0643303237d97a22a48e01628897

Account #11: 0x71bE63f3384f5fb98995898A86B02Fb2426c5788 (10000 ETH)
Private Key: 0x701b615bbdfb9de65240bc28bd21bbc0d996645a3dd57e7b12bc2bdf6f192c82

Account #12: 0xFABB0ac9d68B0B445fB7357272Ff202C5651694a (10000 ETH)
Private Key: 0xa267530f49f8280200edf313ee7af6b827f2a8bce2897751d06a843f644967b1

Account #13: 0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec (10000 ETH)
Private Key: 0x47c99abed3324a2707c28affff1267e45918ec8c3f20b8aa892e8b065d2942dd

Account #14: 0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097 (10000 ETH)
Private Key: 0xc526ee95bf44d8fc405a158bb884d9d1238d99f0612e9f33d006bb0789009aaa

Account #15: 0xcd3B766CCDd6AE721141F452C550Ca635964ce71 (10000 ETH)
Private Key: 0x8166f546bab6da521a8369cab06c5d2b9e46670292d85c875ee9ec20e84ffb61

Account #16: 0x2546BcD3c84621e976D8185a91A922aE77ECEc30 (10000 ETH)
Private Key: 0xea6c44ac03bff858b476bba40716402b03e41b8e97e276d1baec7c37d42484a0

Account #17: 0xbDA5747bFD65F08deb54cb465eB87D40e51B197E (10000 ETH)
Private Key: 0x689af8efa8c651a91ad287602527f3af2fe9f6501a7ac4b061667b5a93e037fd

Account #18: 0xdD2FD4581271e230360230F9337D5c0430Bf44C0 (10000 ETH)
Private Key: 0xde9be858da4a475276426320d5e9262ecfc3ba460bfac56360bfa6c4c28b4ee0

Account #19: 0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199 (10000 ETH)
Private Key: 0xdf57089febbacf7ba0bc227dafbffa9fc08a93fdc68e1e42411a14efcf23656e
![Screenshot 2024-12-29 183716](https://github.com/user-attachments/assets/cf5b6c2c-e0da-49c9-9ae4-ce081bb74f70)


The Hardhat development network needs to remain running in the terminal that was used to start it. Open a new terminal instance in the project directory to execute the remaining commands in this tutorial.

Next, create a new file called index.js in your project directory and add the following code to it:

	const { Web3 } = require('web3');

	const web3 = new Web3('http://127.0.0.1:8545/');

	// Log the chain ID to the console
	web3.eth
		.getChainId()
		.then(result => {
			console.log('Chain ID: ' + result);
		})
		.catch(error => {
			console.error(error);
		});
  ![Screenshot 2024-12-29 183926](https://github.com/user-attachments/assets/99ee4230-8909-4754-ac36-8742aa433eeb)

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

Step-by-Step Breakdown
Setup Web3 Instance:


	const web3 = new Web3('http://127.0.0.1:8545/');
Connects to the blockchain network (Ganache in this case).
Define Contract Address and ABI:


	const deployedAddress = '0xYourContractAddressHere';
	const abi = [ /* ABI Array */ ];
Use the address of the deployed contract and its ABI (Application Binary Interface) generated during compilation.
Initialize Contract Instance:

	const myContract = new web3.eth.Contract(abi, deployedAddress);
This creates an instance of the contract, allowing you to call its functions.
Get Accounts:


	const accounts = await web3.eth.getAccounts();
	const user = accounts[0];
Fetches accounts from the connected blockchain and selects the first account for interaction.
Get Contract Balance:


	const balance = await myContract.methods.getBalance().call();
	console.log('Contract balance:', web3.utils.fromWei(balance, 'ether'), 'ETH');
Calls the getBalance function of the contract to check the current Ether balance.
Send Ether:


	const sendTx = await web3.eth.sendTransaction({
	  from: user,
	  to: deployedAddress,
	  value: web3.utils.toWei('0.1', 'ether'),
	  gas: 2000000,
	});
	console.log('Transaction Hash (Send):', sendTx.transactionHash);
	Sends 0.1 Ether to the contract.
	Update Contract State:


	   const updateTx = await myContract.methods.setMyNumber(42).send({ from: user });
	   console.log('Transaction Hash (Update):', updateTx.transactionHash);
Calls the setMyNumber function to update the state variable myNumber to 42.
Withdraw Ether (Owner Only):


	const withdrawTx = await myContract.methods.withdraw().send({ from: user });
	console.log('Transaction Hash (Withdraw):', withdrawTx.transactionHash);
Calls the withdraw function, allowing the owner to transfer all Ether in the contract back to their account.

Prerequisites for Execution
The contract must already be deployed, and its address must be known.
Replace the placeholder 0xYourContractAddressHere with the actual contract address.
Ensure the ABI matches the deployed contract.

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

