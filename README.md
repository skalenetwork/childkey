# ChildWallet

## Problem
Metamask requires users to manually confirm each network change. This significantly impairs UIs of games and other dynamic dApps.


## Solution

A child wallet ```CHILD_WALLET``` is derived from an existing Metamask account ```PARENT_ACCOUNT```.  CHILD_WALLET exists inside the browser and may be used for multiple networks and multiple transactions, without requiring Metamask confirmation.

Note that ```CHILD_WALLET``` can at any time be derived from ```PARENT_ACCOUNT```. The derivation procedure requires a single user confirmation in Metamask.


## Diagram

![ChildWallet diagram](https://github.com/skalenetwork/childwallet/blob/a2fc3aa4cc928f7f64c43f3f2896dad51fb7cd13/Screenshot%20from%202022-05-30%2017-30-27.png)


## Example: a sample Game user flow.

1. User accesses the web app for the first time.

2. ```CHILD_WALLET``` is derived and funds are transferred to it from ```PARENT_ACCOUNT```. YThis requires Metamasks confirmations.

3. ```CHILD_WALLET``` is used to purchase game tokens on decentralized exchange.

4.  Game tokens are transferred to Dapp chain where the game is played.

5. The game is played, which includes multiple transactions using ```CHILD_WALLET```.


Note that steps 2. - 5. do not require any futher Metamask interactions.




##  User session initiation (login).

User session initiation requires **two user confirmations**:

- User connects the ```PARENT_ACCOUNT``` in Metamask and switches to the ```CHAIN_ID``` of the Dapp. **Requires user confirmation**.

- The private key ```CHILD_WALLET_PRIVATE_KEY`` of ```CHILD_WALLET``` is derived from ```PARENT_ACCOUNT``` by signing the web page URL using the ```ParentAccount```.
  **Requires user confirmation**.   


- The ```CHILD_WALLET_PRIVATE_KEY``` instance will stay in browser memory and will be destroyed on web page exit.  


##  ChildWallet use.

Once ```CHILD_WALLET_PRIVATE_KEY``` is established inside the browser, it can be used to send Ethereum transactions and read balance information for ```ChildWallet``` utilizing any network ID. 

The operation ** will not require any further confirmations ** in Metamask.

Note: the web application code can define its own warnings and confirmations as needed.


## Sample code 

```
// Derive CHILD_WALLET_PRIVATE_KEY 


const ethNetwork = 'wss://rinkeby.infura.io/ws/v3/PROJECT-ID'
const web3 = await new Web3(new Web3.providers.WebsocketProvider(ethNetwork)


const CHILD_WALLET_PRIVATE_KEY = await skale.childwallet.derivePrivateKeyForMetamaskAccount(); // will trigger Metamask sign confirmation


// abi and address defined here
const contract = await new web3.eth.Contract(abi, address);


web3.eth.accounts.wallet.add(PRIVATE_KEY)
const account = web3.eth.accounts.wallet[0].address


// define METHOD_NAME, ARG1, ARG2 here
const transaction = contract.methods.METHOD_NAME(ARG1, ARG2);

// define CONTRACT_ADDRESS
const options = {
        to: CONTRACT_ADDRESS,
        data: transaction.encodeABI(),
        gas: await transaction.estimateGas({from: account.address}),
        gasPrice: await web3.eth.getGasPrice(),
        customChain: {
        name: 'custom-chain',
        chainId: 1,
        networkId: 1
      }        
    };



const signed  = await web3.eth.accounts.signTransaction(options, PRIVATE_KEY); // wont trigger Metamask confirmation
const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction); // wont trigger Metemask confirmation

```


## ChildWallet account balances


Since Childwallet exists outside Metamask, the Metamask UI will not display the corresponding balance.

The balance will need to be displayed inside the webpage UI by using the Web3.js ``` web3.eth.getBalance``` call.


## ChildWallet key derivation spec.

```CHILD_WALLET_PRIVATE_KEY``` is derived in two steps:

1. Create ```SEED_SIGNATURE``` byte sequence by signing the the current domain name string and using ```PARENT_ACCOUNT``` and utilizing Metamask sign data v4 API  https://docs.metamask.io/guide/signing-data.html#sign-typed-data-v4. 

2.  Derive ```CHILD_WALLET_PRIVATE_KEY``` by from ```SEED_SIGNATURE``` by utilizing BIP-39 key derivation standard, and utilizing ```SEED_SIGNATURE``` as entropy.

## Web hijack attack protection.


Since Metamask requires a domain name match to initiate signing, a web page from a malicious domain will not be able to initiate ```CHILD_WALLET_KEY``` derivation.


