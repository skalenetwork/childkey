# ChildWallet

## Problem
Metamask requires users to manually confirm each network change. This significantly impairs UIs of games and other dynamic dApps.


## Solution

A child wallet ```ChildWallet``` is derived from an existing Metamask account ```ParentAccount```.  ```ChildWallet``` exists inside the browser and may be used for multiple networks and multiple transactions, without requiring Metamask confirmation.

Note that ```ChildWallet``` can at any time be derived from ```ParentAccount```. The derivation procedure requires a single user confirmation in Metamask.


##  User session initiation (login).

User session initiation requires **two user confirmations**:

- User connects the ```ParentAccount``` in Metamask and switches to the ```ChainID``` of the Dapp. **Requires user confirmation**.

- The private key ```CHILD_WALLET_PRIVATE_KEY`` of ```ChildWallet``` is derived from ```ParentAccount``` by signing the web page URL using the ```ParentAccount``` as provided by Metamask sign data v4 API  https://docs.metamask.io/guide/signing-data.html#sign-typed-data-v4. 
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

The balance will need to be displayed inside the webpage by using the following sample code








