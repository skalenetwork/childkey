# ChildWallet

## Problem
Metamask requires users to manually confirm each network change. This significantly impairs UIs of games and other dynamic dApps.


## Solution

A child wallet ```ChildWallet``` is derived from an existing Metamask account ```ParentAccount```.  ChildWallet exists inside the browser and may be used for multiple networks, without requiring user confirmation.

Note that ```ChildWallet``` can at any time be derived from ```ParentAccount```. The derivation procedure requires a single user confirmation.


##  User session initiation (login).

User session initiation requires two user confirmations:

- User connects the ```ParentAccount``` in Metamask

- The private key ```CHILD_WALLET_PRIVATE_KEY`` of ```ChildWallet``` is derived from ```ParentAccount``` by signing the web page URL using the ```ParentAccount``` as provided by Metamask sign data v4 API  https://docs.metamask.io/guide/signing-data.html#sign-typed-data-v4. This procedure requires a single user confirmation.   


- The ```CHILD_WALLET_PRIVATE_KEY``` instance will stay in browser memory and will be destroyed on web page exit.   


##  ChildWallet use.

Once ```CHILD_WALLET_PRIVATE_KEY``` is established inside the browser, it can be used to send Ethereum transactions and read balance information for ```ChildWallet``` utilizing any network ID.  

The operation will not require any further confirmations from Metamask.

Note: the web application code can define its own warnings and confirmations as needed.



## Sample code 

To be completed ...

```
// Derive CHILD_WALLET_PRIVATE_KEY 
TO_BE_COMPLETED
```


const ethNetwork = 'wss://rinkeby.infura.io/ws/v3/PROJECT-ID'
const web3 = await new Web3(new Web3.providers.WebsocketProvider(ethNetwork))

// abi and address defined here
const contract = await new web3.eth.Contract(abi, address);

const account = web3.eth.accounts.privateKeyToAccount(CHILD_WALLET_PRIVATE_KEY)

// define METHOD_NAME, ARG1, ARG2 here
const transaction = contract.methods.METHOD_NAME(ARG1, ARG2);

// define CONTRACT_ADDRESS
const options = {
        to: CONTRACT_ADDRESS,
        data: transaction.encodeABI(),
        gas: await transaction.estimateGas({from: account.address}),
        gasPrice: await web3.eth.getGasPrice()
    };

const signed  = await web3.eth.accounts.signTransaction(options, PRIVATE_KEY);
const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction);

```
