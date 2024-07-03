#### Step 1: Deploy the `Multicall` Contract

**Objective**: Have a deployed `Multicall` contract that can aggregate multiple function calls.

- The `Multicall` contract is deployed to the Ethereum blockchain. It includes functions like `aggregate` and `tryAggregate` to batch and execute multiple calls.
- This contract is designed to reduce gas costs and improve the efficiency of data retrieval by combining multiple function calls into a single transaction.

#### Step 2: Prepare the Call Data

**Objective**: Encode the `balanceOf` function calls for each ERC-20 token contract.

- Identify the wallet address for which you want to check the balances.
- Identify the addresses of the ERC-20 token contracts.
- For each token contract, prepare the data for the `balanceOf` function call. This includes encoding the function signature (`balanceOf(address)`) and the wallet address into the required ABI format.
- Structurally, each call will consist of the target contract address and the encoded function call data.

#### Step 3: Aggregate the Calls Using `Multicall`

**Objective**: Use the `Multicall` contract to execute the encoded function calls in a single transaction.

- Pass the array of prepared call data to the `Multicall` contract's `aggregate` function.
- The `aggregate` function in the `Multicall` contract processes each call:
  - It executes each call on the specified target contract.
  - It collects the results of each call, ensuring all calls succeed (if using the `aggregate` function that requires success).

#### Step 4: Decode the Results

**Objective**: Decode the returned data to retrieve the token balances.

- The `aggregate` function returns an array of results, where each result corresponds to the return value of a `balanceOf` call.
- Decode each result from the ABI format to get the actual balance for each token.
- Compile the balances into a list or array, each entry corresponding to the balance of a token in the same order as the calls.

### Example Scenario: Fetching Balances for Four Tokens

1. **Wallet Address**: `0xWalletAddress`
2. **Token Contracts**:
   - Token 1: `0xTokenAddress1`
   - Token 2: `0xTokenAddress2`
   - Token 3: `0xTokenAddress3`
   - Token 4: `0xTokenAddress4`

**Steps**:

1. **Deploy the `Multicall` contract**: Ensure the `Multicall` contract is deployed on the Ethereum network.
2. **Prepare the call data**:
   - Encode the `balanceOf` call for each token contract with the wallet address.
   - Create an array of call data, each element containing the target token contract address and the encoded call data.
3. **Aggregate the calls**:
   - Pass the array of call data to the `aggregate` function of the `Multicall` contract.
   - The contract executes each call and collects the results.
4. **Decode the results**:
   - The `aggregate` function returns the encoded results of each call.
   - Decode each result to get the token balance in a human-readable format.
   - Store the balances in an array corresponding to each token contract.

### Key Points

- **Efficiency**: Using `Multicall` significantly reduces the number of transactions and, consequently, the gas costs.
- **Atomicity**: All calls succeed or fail together, ensuring data consistency.
- **Flexibility**: The `Multicall` contract can be used for various read-only function calls beyond `balanceOf`, such as retrieving token allowances, prices, or other on-chain data.

### Practical Application

This process is often used in decentralized finance (DeFi) platforms, such as Uniswap, to fetch multiple pieces of on-chain data efficiently. By combining multiple calls into a single transaction, DeFi applications can provide users with up-to-date information while optimizing for gas costs.

### Forming the Structure of Calls

To form the structure of calls using the provided data, we need to create an array of `Call` structs where each struct contains the target token contract address and the ABI-encoded `balanceOf` function call data for a given wallet address.

#### Example Data

- **Wallet Address**: `0xWalletAddress`
- **Token Addresses**:
  - Token 1: `0xTokenAddress1`
  - Token 2: `0xTokenAddress2`
  - Token 3: `0xTokenAddress3`
  - Token 4: `0xTokenAddress4`

### Steps to Form the Structure of Calls

1. **Prepare the Call Data**:
   - For each token contract, encode the `balanceOf` function call with the wallet address.

2. **Create the Array of Calls**:
   - Each element in the array will be a `Call` struct containing the target address (token contract) and the call data (encoded `balanceOf` function).

#### Detailed Steps

1. **Encode the `balanceOf` Function Call**:
   - The function signature for `balanceOf` is `balanceOf(address)`.
   - The encoded function call for a specific wallet address can be generated using Solidity’s ABI encoding functions.

2. **Structure the Calls**:
   - Each call struct will contain:
     - `target`: The address of the ERC-20 token contract.
     - `callData`: The ABI-encoded call data for `balanceOf` with the wallet address.

### Example in Solidity

#### Define the Struct for Call

```solidity
struct Call {
    address target;
    bytes callData;
}
```

#### Prepare the Call Data for Each Token

Using the example data, let’s encode the `balanceOf` calls:

```solidity
address walletAddress = 0xWalletAddress;
address[] memory tokenAddresses = new address[](4);
tokenAddresses[0] = 0xTokenAddress1;
tokenAddresses[1] = 0xTokenAddress2;
tokenAddresses[2] = 0xTokenAddress3;
tokenAddresses[3] = 0xTokenAddress4;

Call[] memory calls = new Call[](tokenAddresses.length);

for (uint256 i = 0; i < tokenAddresses.length; i++) {
    calls[i] = Call({
        target: tokenAddresses[i],
        callData: abi.encodeWithSignature("balanceOf(address)", walletAddress)
    });
}
```

### Structure of Calls

Here is how the structured calls will look:

1. **Call for Token 1**:
   - `target`: `0xTokenAddress1`
   - `callData`: `abi.encodeWithSignature("balanceOf(address)", 0xWalletAddress)`

2. **Call for Token 2**:
   - `target`: `0xTokenAddress2`
   - `callData`: `abi.encodeWithSignature("balanceOf(address)", 0xWalletAddress)`

3. **Call for Token 3**:
   - `target`: `0xTokenAddress3`
   - `callData`: `abi.encodeWithSignature("balanceOf(address)", 0xWalletAddress)`

4. **Call for Token 4**:
   - `target`: `0xTokenAddress4`
   - `callData`: `abi.encodeWithSignature("balanceOf(address)", 0xWalletAddress)`

### Summary

- **Wallet Address**: `0xWalletAddress`
- **Token Addresses**: An array containing the addresses of the four ERC-20 token contracts.
- **Calls Structure**:
  - Each call is represented by a `Call` struct containing the target token contract address and the ABI-encoded call data for the `balanceOf` function.

### Example Summary

Given the data:

```solidity
address walletAddress = 0xWalletAddress;
address[] memory tokenAddresses = new address[](4);
tokenAddresses[0] = 0xTokenAddress1;
tokenAddresses[1] = 0xTokenAddress2;
tokenAddresses[2] = 0xTokenAddress3;
tokenAddresses[3] = 0xTokenAddress4;
```

The array of `Call` structs will look like this:

```solidity
Call[] memory calls = new Call[](4);

calls[0] = Call({
    target: 0xTokenAddress1,
    callData: abi.encodeWithSignature("balanceOf(address)", walletAddress)
});
calls[1] = Call({
    target: 0xTokenAddress2,
    callData: abi.encodeWithSignature("balanceOf(address)", walletAddress)
});
calls[2] = Call({
    target: 0xTokenAddress3,
    callData: abi.encodeWithSignature("balanceOf(address)", walletAddress)
});
calls[3] = Call({
    target: 0xTokenAddress4,
    callData: abi.encodeWithSignature("balanceOf(address)", walletAddress)
});
```

This array can now be passed to the `aggregate` function of the `Multicall` contract to fetch the balances in a single transaction.


# Fetching ERC-20 Token Balances Using Multicall with Web3.js

## Overview

This guide explains how to use a deployed `Multicall` contract to fetch ERC-20 token balances for a given wallet address using Web3.js. The steps involve preparing call data, using the `Multicall` contract to batch the calls, and decoding the results.

## Requirements

- Node.js
- Web3.js
- Infura project ID or another Ethereum node provider

## Step-by-Step Guide

### 1. Initialize Web3

Initialize Web3 with your Ethereum node provider (e.g., Infura).

```javascript
const Web3 = require('web3');
const web3 = new Web3('https://mainnet.infura.io/v3/YOUR_INFURA_PROJECT_ID'); // Replace with your Infura project ID or other Ethereum node provider
```

### 2. Define Contract Details

Provide the address and ABI for the `Multicall` contract and the simplified ABI for ERC-20 token contracts.

```javascript
// Multicall contract address and ABI (example, you need to use the actual deployed address and ABI)
const multicallAddress = '0xYourMulticallContractAddress'; // Replace with your deployed Multicall contract address
const multicallABI = [ /* Multicall ABI */ ]; // Replace with Multicall contract ABI

// ERC-20 token contract ABI (simplified)
const erc20ABI = [
  {
    "constant": true,
    "inputs": [{ "name": "_owner", "type": "address" }],
    "name": "balanceOf",
    "outputs": [{ "name": "balance", "type": "uint256" }],
    "type": "function"
  }
];
```

### 3. Set Wallet and Token Addresses

Define the wallet address and an array of token addresses for which you want to check balances.

```javascript
// Wallet address to check balances
const walletAddress = '0xWalletAddress'; // Replace with the actual wallet address

// Token addresses
const tokenAddresses = [
  '0xTokenAddress1', // Replace with actual token contract addresses
  '0xTokenAddress2',
  '0xTokenAddress3',
  '0xTokenAddress4'
];
```

### 4. Prepare the Call Data

Create an array of call data for the `balanceOf` function for each token.

```javascript
// Prepare the call data for the balanceOf function for each token
const calls = tokenAddresses.map(tokenAddress => {
  const contract = new web3.eth.Contract(erc20ABI, tokenAddress);
  return {
    target: tokenAddress,
    callData: contract.methods.balanceOf(walletAddress).encodeABI()
  };
});
```

### 5. Create Multicall Contract Instance

Instantiate the `Multicall` contract.

```javascript
// Multicall contract instance
const multicallContract = new web3.eth.Contract(multicallABI, multicallAddress);
```

### 6. Aggregate Function Call

Use the `aggregate` method of the `Multicall` contract to batch the calls and decode the results.

```javascript
// Call the aggregate function
async function fetchBalances() {
  try {
    const aggregateResult = await multicallContract.methods.aggregate(
      calls.map(call => [call.target, call.callData])
    ).call();

    const decodedBalances = aggregateResult.returnData.map(data =>
      web3.eth.abi.decodeParameter('uint256', data)
    );

    return decodedBalances;
  } catch (error) {
    console.error('Error fetching balances:', error);
    return [];
  }
}
```

### 7. Execute and Log Balances

Fetch and log the token balances.

```javascript
// Fetch and log balances
fetchBalances().then(balances => {
  console.log('Balances:', balances);
}).catch(error => {
  console.error('Error fetching balances:', error);
});
```

## Summary

1. **Deploy**: Ensure the `Multicall` contract is deployed.
2. **Prepare Call Data**: Encode the `balanceOf` function calls for the wallet address and token contracts.
3. **Aggregate Calls**: Use the `Multicall` contract's `aggregate` function to batch and execute the calls.
4. **Decode Results**: Decode the returned data to get the token balances.

This approach efficiently retrieves multiple token balances with a single contract call, reducing gas costs and improving performance.
