# web3 development

## 1. issues resolving

### "Returned error: Cannot run transaction: EIP 1559 is not activated."

#### solution:

add gas-related params to call(), for example: 

```
  console.log(await contract.factory.methods.allPairsLength().call({                                                     
            gasLimit: 30000,                                                                                                
            gasPrice: await web3.eth.getGasPrice(),                                                                             		})
   );
```

#### background:

##### environment:

```
# fork.sh
# fork EVM mainnet at block number 12489619
# which is generated at May-23-2021 08:54:16 AM +UTC

#!/bin/bash
                                                                                                                                                                                                                                                 
npx hardhat node \                                                                                                          
--fork https://eth-mainnet.g.alchemy.com/v2/${api_key} \                                              
--fork-block-number 12489619 \                                                                                              
--hostname 0.0.0.0 \                                                                                                        
--port 8545 \                                                                                                               
>testnet.log 2>&1 &
```

##### interact:

```
/*
 * test.js
 * fetch all swapping pairs of UniswapV2 on EVM
 */
const { Web3 } = require('web3');
const web3 = new Web3('http://127.0.0.1:8545');
let abi = ...;
let contract = new web3.eth.Contract(abi, '0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f');
let pairsLen = await contract.factory.methods.allPairsLength().call();
...
```

#### key path:

```
# checking `@options` of `_contractMethodCall`

vim ${project root}/node_modules/web3-eth-contract/lib/commonjs/contract.js +769
```

At first I followed the document of web3.js, and it indicated that **@options** should be passed with **type = 0** field, to make my call to be **Legacy** <br>

But its not work at all, then I checked **@options**, and fields of it are all **undefined**, maybe this is why EVM thought it's a EIP-1559 transaction<br>

Then I filled other fields of **@options** one by one, when I filled both **gas** and **gasPrice** or **gasLimit** and **gasPrice**, the call successed, eventhough I left **type** be te **undefined**. However gas is not cost any, becaucse it's not a transaction that modifies any EVM states.

#### Root cause

TODO
