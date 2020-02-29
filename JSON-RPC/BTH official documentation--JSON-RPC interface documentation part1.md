# BTH official documentation--JSON-RPC interface documentation part1

## version: V1.2_20200221

This document describes how the user communicates with the BTH network via HTTP RPC API to obtain network information. The json-rpc API service runs on port 8090 of the BTH representative node, and the API calls send POST and GET requests through the HTTP protocol.All requests follow the json-rpc standard format; see the documentation for code examples.

The BTH network ran two representative nodes (52.8.120.32, 47.111.255.193) to provide HTTP RPC service. In the example, the command was sent to the 47.111.255.193 node and the correct reply was received.


----------
## API List
### Addresses & Accounts

generateaddress

createaddress

validateaddress

createaccount

getaccount

getaccountresource

getaccountnet

updateaccount

createtransaction

gettransactionsign

broadcasttransaction

easytransfer

easytransferbyprivate

### Query Network

getblockbynum

getblockbyid

getblockbylimitnext

getblockbylatestnum

getnowblock

listnodes

getnodeinfo

gettransactioninfobyid

gettransactionbyid

----------
## generateaddress ##
Generates a random private key and address pair. Returns a private key, the corresponding address in hex and base58.

###Important Note:

 - This API call returns the randomly generated privateKey value in the JSON output. Please ensure this API call is executed in a secure environment.
 
POST http://47.111.255.193:8090/wallet/generateaddress

    curl --location --request POST 'http://47.111.255.193:8090/wallet/generateaddress' --header 'Content-Type: application/x-www-form-urlencoded'
    response:
    {"privateKey":"ce17cab6af3a52b7549ffa521001bf3ceca8a76abc89e82910afd116a2419d0a","address":"BGL3egq5w6p5DRXgYt1xXPaeV9uDBAC9VN","hexAddress":"19824cf4dfbcf24066a4554e2852929515bda6a948"}

----------

## createaddress ##
Create address from a specified password string (NOT PRIVATE KEY)

params|type|note
:|:|:
value|string|value is the password, converted from ascii to hex
Return value：broadcast success or failure
POST http://47.111.255.193:8090/wallet/createaddress

    curl --location --request POST 'http://47.111.255.193:8090/wallet/createaddress' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"value":"313233343536"}

    response:
    {"base58checkAddress":"BGCge2rtvVRyoe5YGwzWcC8WbrwGyMQswG","value":"1980e8816651790d4d6c187eef09f90b7a19408bb8"}

----------

## validateaddress ##
Validates address, returns either true or false.

params|type|note
:|:|:
Address|string|Address should be in base58checksum, hexString, or base64 format.

Return value: true or false
POST http://47.111.255.193:8090/wallet/validateaddress

    curl --location --request POST 'http://47.111.255.193:8090/wallet/validateaddress' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"address":"1980e8816651790d4d6c187eef09f90b7a19408bb8"}

    response:
    {"result":true,"message":"Hex string format"}

----------

## createaccount ##
Create an account. Uses an already activated account to create a new account。

params|type|note
:|:|:
owner_address|string|Owner_address is an activated account，converted to a hex String
account_address|string|account_address is the address of the new account, converted to a hex string, this address needs to be calculated in advance
visible|boolean|Optional,whether the address is in base58 format
permission_id|int32|Optional,for multi-signature use

Return: Transaction object
POST http://47.111.255.193:8090/wallet/createaccount

    curl --location --request POST 'http://47.111.255.193:8090/wallet/createaccount' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","account_address":"19e002f6487585c2b58bc2c9bb4492bc1f17132cd0"}

    response:
    {"txID":"96d3f6bba67cba773685801f60f5eb1cb672a4b74cae6620299e6b43d911f9b8","raw_data":{"contract":[{"parameter":{"value":{"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","account_address":"19e002f6487585c2b58bc2c9bb4492bc1f17132cd0"},"type_url":"type.googleapis.com/protocol.AccountCreateContract"},"type":"AccountCreateContract"}],"ref_block_bytes":"e0b8","ref_block_hash":"354277591d1544d7","expiration":1582127133000,"timestamp":1582127480634},"raw_data_hex":"0a02e0b82208354277591d1544d740c8e280f1852e5a6612640a32747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e4163636f756e74437265617465436f6e7472616374122e0a15190c22d79c37176df5cff31600facac21d5022faf1121519e002f6487585c2b58bc2c9bb4492bc1f17132cd070bafe95f1852e"}

----------

## getaccount ##
Query information about an account,Including balances, freezes, votes and time, etc.

params|type|note
:|:|:
Address|string|address should be converted to a hex string
visible|boolean|Optional,whether the address is in base58 format

Return value: Account Object
POST http://47.111.255.193:8090/wallet/getaccount

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getaccount' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"address":"190c22d79c37176df5cff31600facac21d5022faf1"}

    response:
    {"account_name": "5a696f6e30","address": "190c22d79c37176df5cff31600facac21d5022faf1","balance": 98999999118800000,"latest_opration_time": 1582125516000,"allowance": 658518001443,"is_witness": true,"latest_consume_free_time": 1581656616000,"account_resource": {}}

----------

## getaccountresource ##
Query the resource information of an account(Enforcement Point（EP）,Contract Point（CP）,etc)

params|type|note
:|:|:
Address|string|address should be converted to a hex string
visible|boolean|Optional,whether the address is in base58 format

Return:
1. If the parameter format is correct, the resource information of the account is returned
2. If the format of the parameter is incorrect, it returns null.
3. If you do not fill in the parameters, a null pointer error is returned.

POST http://47.111.255.193:8090/wallet/getaccountresource

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getaccountresource' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"address":"190c22d79c37176df5cff31600facac21d5022faf1"}

    response:
    {"freeNetLimit": 5000,"TotalNetLimit": 43200000000,"TotalEnergyLimit": 50000000000000}

----------
## getaccountnet ##
Query the resource information of an account(Enforcement Point（EP）)

params|type|note
:|:|:
Address|string|address should be converted to a hex string
visible|boolean|Optional,whether the address is in base58 format

Return value：Enforcement Point information for the account.

POST http://47.111.255.193:8090/wallet/getaccountnet

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getaccountnet' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"address":"190c22d79c37176df5cff31600facac21d5022faf1"}

    response:
    {"freeNetLimit": 5000,"TotalNetLimit": 43200000000}

----------
## updateaccount ##
Modify account name

params|type|note
:|:|:
account_name|string|Account_name is the name of the account, converted to a hex string
owner_address|string|Owner_address is the account address to be modified, converted to a hex string
visible|boolean|Optional,whether the address is in base58 format
permission_id|int32|Optional,for multi-signature use

Return value：Transaction object

POST http://47.111.255.193:8090/wallet/updateaccount

    curl --location --request POST 'http://47.111.255.193:8090/wallet/updateaccount' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"account_name":"76d652d426974636f696e486f7431323","owner_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"}

    response:
    {"txID":"bf567b39057bb28b44d03652f3f0a9b90227cf9f8206b8102702191357cbc6db","raw_data":{"contract":[{"parameter":{"value":{"account_name":"76d652d426974636f696e486f7431323","owner_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.AccountUpdateContract"},"type":"AccountUpdateContract"}],"ref_block_bytes":"e0b8","ref_block_hash":"354277591d1544d7","expiration":1582127133000,"timestamp":1582129107335},"raw_data_hex":"0a02e0b82208354277591d1544d740c8e280f1852e5a63080a125f0a32747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e4163636f756e74557064617465436f6e747261637412290a1076d652d426974636f696e486f74313231215190f7d686fc33641e98fe563bf99a16b1b39a3beeb7087a3f9f1852e"}

----------

## createtransaction #
Create a transfer transaction, if to address is not existed, then create the account on the blockchain
###Important Note:
 - The expiration time of the http api creation transaction is 1 minute, so to complete the on-chain, you need to complete gettransactionsign and broadcasttransaction within 1 minute after the creation.

params|type|note
:|:|:
to_address|string|To_address is the transfer address, converted to a hex string
owner_address|string|Owner_address is the transfer address, converted to a hex string
amount|int64|Amount is the transfer amount,the unit is sun
permission_id|int32|Optional, for multi-signature use
visible|boolean|Optional.Whehter the address is in base58 format.

Return value：a new Transaction contract data
POST http://47.111.255.193:8090/wallet/gettransactionsign

    curl --location --request POST 'http://47.111.255.193:8090/wallet/createtransaction' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"to_address": "190f7d686fc33641e98fe563bf99a16b1b39a3beeb", "owner_address":"190c22d79c37176df5cff31600facac21d5022faf1", "amount": 1000000 }'
    
    response:
    {"txID":"ab88933ef3897329e0ae5aafde5c0a867247185e87a23afbd9c7e8b568c2e5a0","raw_data":{"contract":[{"parameter":{"value":{"amount":1000000,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"c7f5","ref_block_hash":"0fb68612e19bea6a","expiration":1582108110000,"timestamp":1582108051677},"raw_data_hex":"0a02c7f522080fb68612e19bea6a40b0d9f7e7852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18c0843d70dd91f4e7852e"}

----------

## gettransactionsign ##
Sign the transaction, the api has the risk of leaking the private key, please make sure to call the api in a secure environment
###Important Note:

 - Please control risks when using this API. To ensure environmental security, please do not invoke APIs provided by other or invoke this very API on a public network.
params|type|note
:|:|:
transaction|json|Transaction is a contract created by http api
privateKey|string|privateKey is the user private key

Return value：Signed Transaction contract data
POST http://47.111.255.193:8090/wallet/gettransactionsign

    curl --location --request POST 'http://47.111.255.193:8090/wallet/gettransactionsign' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"transaction":{"txID":"ab88933ef3897329e0ae5aafde5c0a867247185e87a23afbd9c7e8b568c2e5a0","raw_data":{"contract":[{"parameter":{"value":{"amount":1000000,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"c7f5","ref_block_hash":"0fb68612e19bea6a","expiration":1582108110000,"timestamp":1582108051677},"raw_data_hex":"0a02c7f522080fb68612e19bea6a40b0d9f7e7852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18c0843d70dd91f4e7852e"},"privateKey":"3c03f27aa29cad79c436f32752f3e3d7d53a3c81f41d25085e6bb99db6ad75ed"}'

    response:
    {"signature":["dfa8173809231c8e626bb11fe97c022a5f052ac2ebe8e11e0065ffbd5d2b92f00ad455ddc16e0a27ad87edd088cbb5908dfd5af02cc6091d673ffbd91e0825d501"],"txID":"ab88933ef3897329e0ae5aafde5c0a867247185e87a23afbd9c7e8b568c2e5a0","raw_data":{"contract":[{"parameter":{"value":{"amount":1000000,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"c7f5","ref_block_hash":"0fb68612e19bea6a","expiration":1582108110000,"timestamp":1582108051677},"raw_data_hex":"0a02c7f522080fb68612e19bea6a40b0d9f7e7852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18c0843d70dd91f4e7852e"}

----------
## broadcasttransaction ##
Broadcast the signed transaction

params|type|note
:|:|:
visible|boolean|The JSON output of the signed transaction contract data API call.
signature|array of strings|
txID|string|
raw_data|json|
raw_data_hex|string|



Return value：broadcast success or failure
POST http://47.111.255.193:8090/wallet/broadcasttransaction

    curl --location --request POST 'http://47.111.255.193:8090/wallet/broadcasttransaction' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"visible":false,"signature":["05d8711b3e91511466e1213109d58dccde8d64efb8522203e62ccffe16237a631dfc144dea10340efc86f5e979c80e3ee730fd2acae573a28e313259c6db383500"],"txID":"8682c4e11862feb6ea8ed501988f8349fdd2af0b8a4199e7ec4a533a5b94a736","raw_data":{"contract":[{"parameter":{"value":{"amount":1000000,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"ddda","ref_block_hash":"3bec0fc0a9026fec","expiration":1582126245000,"timestamp":1582124872646},"raw_data_hex":"0a02ddda22083bec0fc0a9026fec4088c9caf0852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18c0843d70c6e7f6ef852e"}'

    response:
    {"result": true}

----------
## easytransfer ##
Easily transfer from an address using the password string. Only works with accounts created from createAddress,integrated getransactionsign and broadcasttransaction.
###Warning:
 - Please control risks when using this API. To ensure environmental security, please do not invoke APIs provided by other or invoke this very API on a public network.

params|type|note
:|:|:
passPhrase|string|Password converted from ascii to hex
toAddress|string|Recipient address converted into a hex string
Amount|int32|Amount of BTH to transfer expressed in YUN.

Return value： transaction, including execution results.
POST http://47.111.255.193:8090/wallet/easytransfer

    curl --location --request POST 'http://47.111.255.193:8090/wallet/easytransfer' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"passPhrase": "313233343536","toAddress": "190f7d686fc33641e98fe563bf99a16b1b39a3beeb", "amount":100}'

    response:
    {"result":{"result":true},"transaction":{"signature":["8457f80768af63eaf753926605c0d8caf973bf9f76237ae83f4dc8e596f00e733b1e179e3b8abc74bf595a93aefcdbfea175cfc43958b2e309fd4b387f1fd3b601"],"txID":"e66a890e3a79395080fd369dfdd84bc7aef0a49d969cff76fabb69399c9d1962","raw_data":{"contract":[{"parameter":{"value":{"amount":100,"owner_address":"1980e8816651790d4d6c187eef09f90b7a19408bb8","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"e879","ref_block_hash":"2a9a0347778f8166","expiration":1582133088000,"timestamp":1582133029345},"raw_data_hex":"0a02e87922082a9a0347778f816640809eecf3852e5a65080112610a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412300a151980e8816651790d4d6c187eef09f90b7a19408bb81215190f7d686fc33641e98fe563bf99a16b1b39a3beeb186470e1d3e8f3852e"}}

----------
## easytransferbyprivate ##
Easily transfer from an address using the private key.
###Warning:
 - Please control risks when using this API. To ensure environmental security, please do not invoke APIs provided by other or invoke this very API on a public network.

params|type|note
:|:|:
privateKey|string|passPhrase is the private key in hex string format
toAddress|string|Recipient address converted into a hex string
Amount|int32|Amount of BTH to transfer expressed in YUN.

Return value： transaction, including execution results.
POST http://47.111.255.193:8090/wallet/easytransferbyprivate

    curl --location --request POST 'http://47.111.255.193:8090/wallet/easytransferbyprivate' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"privateKey": "3c03f27aa29cad79c436f32752f3e3d7d53a3c81f41d25085e6bb99db6ad75ed","toAddress": "190f7d686fc33641e98fe563bf99a16b1b39a3beeb", "amount":1001039}'

    response:
    {"result":{"result":true},"transaction":{"signature":["c51330764cbe467372b271bd921bbe149a761dc8b8b8da6c0ae7d40e9fba112354543fe1b6e89cf968a31027102a8f70df03bcf2658a1a7bb98af2bafe4a47d901"],"txID":"277e1369c6d072f021bdc382eb4bd013edf0c81f12463c332a1520cd5c715d4c","raw_data":{"contract":[{"parameter":{"value":{"amount":1001039,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"e806","ref_block_hash":"3fe6ea12988b916e","expiration":1582132743000,"timestamp":1582132683896},"raw_data_hex":"0a02e80622083fe6ea12988b916e40d896d7f3852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18cf8c3d70f8c8d3f3852e"}}

----------
## getblockbynum ##
Returns the Block Object corresponding to the 'Block Height' specified (number of blocks preceding it).

params|type|note
:|:|:
num|int32|num is the block height

POST http://47.111.255.193:8090/wallet/getblockbynum

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getblockbynum' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"num":1}

    response:
    {"blockID":"0000000000000001443fddb5c430334d383a4ecf054a46beb12ed567b58164c0","block_header":{"raw_data":{"number":1,"txTrieRoot":"0000000000000000000000000000000000000000000000000000000000000000","witness_address":"190c22d79c37176df5cff31600facac21d5022faf1","parentHash":"0000000000000000f91b5b0d9133ee4f3337d25595f30cf961af93c096163be3","version":7,"timestamp":1581561099000},"witness_signature":"2050f76ffb49ffe5aadedba6b40da1d9a607e239ef6ca966f19d7f51d527d2284b9bec0252b556cd47b664a00b2031c3b67e0266568fb6950ee15ffbb8b875cb01"}}

----------
## getblockbyid ##
Query block by ID.

params|type|note
:|:|:
value|string|Block ID

POST http://47.111.255.193:8090/wallet/getblockbyid

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getblockbyid' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"value": "0000000000000001443fddb5c430334d383a4ecf054a46beb12ed567b58164c0"}'

    response:
    {"blockID":"0000000000000001443fddb5c430334d383a4ecf054a46beb12ed567b58164c0","block_header":{"raw_data":{"number":1,"txTrieRoot":"0000000000000000000000000000000000000000000000000000000000000000","witness_address":"190c22d79c37176df5cff31600facac21d5022faf1","parentHash":"0000000000000000f91b5b0d9133ee4f3337d25595f30cf961af93c096163be3","version":7,"timestamp":1581561099000},"witness_signature":"2050f76ffb49ffe5aadedba6b40da1d9a607e239ef6ca966f19d7f51d527d2284b9bec0252b556cd47b664a00b2031c3b67e0266568fb6950ee15ffbb8b875cb01"}}

----------
## getblockbylimitnext ##
Returns the list of Block Objects included in the 'Block Height' range specified.

params|type|note
:|:|:
startNum|int32|Starting block height, including this block.
endNum|int32|Ending block height, excluding that block.

POST http://47.111.255.193:8090/wallet/getblockbylimitnext

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getblockbylimitnext' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"startNum":1,"endNum":3}'

    response:
    {"block": [{"block_header": {"raw_data": {"timestamp": 1581561099000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "0000000000000000f91b5b0d9133ee4f3337d25595f30cf961af93c096163be3","number": 1,"witness_address": "190c22d79c37176df5cff31600facac21d5022faf1","version": 7},"witness_signature": "2050f76ffb49ffe5aadedba6b40da1d9a607e239ef6ca966f19d7f51d527d2284b9bec0252b556cd47b664a00b2031c3b67e0266568fb6950ee15ffbb8b875cb01"}},{"block_header": {"raw_data": {"timestamp": 1581561114000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "0000000000000001443fddb5c430334d383a4ecf054a46beb12ed567b58164c0","number": 2,"witness_address": "190c22d79c37176df5cff31600facac21d5022faf1","version": 7},"witness_signature": "2d4d9e3f7d4033314b84d343b76a602598f2f773e96d1ca71cbeb2947e0fbc9b2bafc99cc41d6104f2d61b06567c9bceb79d92b4644b99ff15ef4cc993c4de3101"}}]}

----------
## getblockbylatestnum ##
Returns a list of block objects.

params|type|note
:|:|:
num|int32|The number of blocks to query

POST http://47.111.255.193:8090/wallet/getblockbylatestnum

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getblockbylatestnum' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"num":5}' 

    response:
    {"block": [{"block_header": {"raw_data": {"timestamp": 1582164804000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "00000000000311d6209269375310fbb845bf7fc39dfbfb62c4ddc651057f2e79","number": 201175,"witness_address": "192b4523cbba5fbb9cc5654e7f23c83ed62903c645","version": 7},"witness_signature": "166c243043aa5d3cf5cad21871688529a113591fcd9a4ba4fb9434630fef003479cd1e7bed8181448746201321ea9eacecaea700c142cfdb41116dfdeb48d17b01"}},{"block_header": {"raw_data": {"timestamp": 1582164807000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "00000000000311d7da622c2c9b6518243c7a2b9d36d76cce7f7bfdd0fda927ac","number": 201176,"witness_address": "19656a076e914859441308e4e3a7b1c7f8765d07e7","version": 7},"witness_signature": "b79ccf1c35ca8ccdad4c0bc62b128c8fd3eedce4cb0655731c2061ecfbc4e782608106a503dd95189fe874ecf7c97854b9bcd75fba865ff0b15202cf09490c6901"}},{"block_header": {"raw_data": {"timestamp": 1582164810000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "00000000000311d8a7f5cdd4b3a49c720fc133867e0308ba13d062cd8b3f6624","number": 201177,"witness_address": "190c22d79c37176df5cff31600facac21d5022faf1","version": 7},"witness_signature": "3822681959fa64e4c7869b16eb4e6c1f54aa5261f0772148b02825e723096ad039d9626a3b2742bb860a4f6a6612295b98c623beed03bc2d81c7d35ec053330a01"}},{"block_header": {"raw_data": {"timestamp": 1582164813000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "00000000000311d9bda0f27b9ec16de74462e0b7c562ae9718e472396bc16ae2","number": 201178,"witness_address": "192b4523cbba5fbb9cc5654e7f23c83ed62903c645","version": 7},"witness_signature": "95aef61ee6acace2379e5d6633eb49dedbf5ede470c3e2b2f00232a0d68d511830d807fa8155345e94a793a6ed5bdddf39c430267eafbd40651b65a063f3738d00"}},{"block_header": {"raw_data": {"timestamp": 1582164816000,"txTrieRoot": "0000000000000000000000000000000000000000000000000000000000000000","parentHash": "00000000000311da743c5f767959ca13a635732870ddfec56ab7a90be44df928","number": 201179,"witness_address": "19656a076e914859441308e4e3a7b1c7f8765d07e7","version": 7},"witness_signature": "64b160a7e0427f18495544071102748d1f0557c146176bded59ddfc73cb4a4d15b11b7e794d131b40fa3ee55bbf8ef076d6aded76d3fb3a04015fc8cb9d4b94201"}}]}

----------
## getnowblock ##
Query the latest block information

POST http://47.111.255.193:8090/wallet/getnowblock

    curl --location --request POST 'http://47.111.255.193:8090/wallet/getnowblock' --header 'Content-Type: application/x-www-form-urlencoded' 

    response:
    {"blockID":"00000000000310dbfe368631a16d86d42c5142f828949bcf49d08c6874ad386d","block_header":{"raw_data":{"number":200923,"txTrieRoot":"0000000000000000000000000000000000000000000000000000000000000000","witness_address":"192b4523cbba5fbb9cc5654e7f23c83ed62903c645","parentHash":"00000000000310daee5a386e89a13a8d09aa49a42c2d931b7c67cbb694e848ba","version":7,"timestamp":1582164048000},"witness_signature":"fc4071d494e7d2863a6d8a1ac188798878b76269f0701f8df21f561cf8eb552b0884a1178c9a9b6919c5d13a45bbf8590cb146759f0372b75e789dc8eb625b6600"}}

----------
## listnodes ##
Query the list of nodes connected to the ip of the api

GET http://47.111.255.193:8090/wallet/listnodes

    curl --location --request GET 'http://47.111.255.193:8090/wallet/listnodes' --header 'Content-Type: application/x-www-form-urlencoded'

    response:
    {"nodes": [{"address": {"host": "33392e39382e39322e313039","port": 20000}},{"address": {"host": "34372e39382e3132352e3831","port": 20000}},{"address": {"host": "3139382e31312e3137342e323237","port": 18888}},{"address": {"host": "3131322e37342e3138372e3230","port": 20000}}]}

explain: 33392e39382e39322e313039  ---> ip 39.98.92.109

----------
## getnodeinfo ##
Query Node Information. Returns information about current state of node.

GET http://47.111.255.193:8090/wallet/getnodeinfo

    curl --location --request GET 'http://47.111.255.193:8090/wallet/getnodeinfo' --header 'Content-Type: application/x-www-form-urlencoded'

    response:
    {"activeConnectCount":2,"beginSyncNum":201358,"block":"Num:201363,ID:0000000000031293c03b4d41c8fe9e832ad9f7423157a9e64cb50b1c8dabb813","cheatWitnessInfoMap":{},"configNodeInfo":{"activeNodeSize":0,"allowAdaptiveEnergy":1,"allowCreationOfContracts":1,"backupListenPort":10001,"backupMemberSize":0,"backupPriority":8,"codeVersion":"3.5.1","dbVersion":2,"discoverEnable":true,"listenPort":18888,"maxConnectCount":30,"maxTimeRatio":5.0,"minParticipationRate":15,"minTimeRatio":0.0,"p2pVersion":"9981","passiveNodeSize":0,"sameIpMaxConnectCount":2,"sendNodeSize":0,"supportConstant":true,"versionName":"Odyssey-v3.5.0.1-318-gc6864f595","versionNum":"9911"},"currentConnectCount":2,"machineInfo":{"cpuCount":2,"cpuRate":0.012252525272200393,"deadLockThreadCount":0,"deadLockThreadInfoList":[],"freeMemory":5127331840,"javaVersion":"1.8.0_211","jvmFreeMemory":1780015952,"jvmTotalMemoery":1860698112,"memoryDescInfoList":[{"initSize":2555904,"maxSize":251658240,"name":"Code Cache","useRate":0.10646591186523438,"useSize":26793024},{"initSize":0,"maxSize":-1,"name":"Metaspace","useRate":0.9666910491864502,"useSize":49035272},{"initSize":0,"maxSize":1073741824,"name":"Compressed Class Space","useRate":0.0053452253341674805,"useSize":5739392},{"initSize":33554432,"maxSize":694681600,"name":"PS Eden Space","useRate":0.04648833652712264,"useSize":32294592},{"initSize":5242880,"maxSize":2097152,"name":"PS Survivor Space","useRate":0.828125,"useSize":1736704},{"initSize":88080384,"maxSize":1395654656,"name":"PS Old Gen","useRate":0.03343999161924481,"useSize":46670680}],"osName":"Linux 4.15.0-52-generic","processCpuRate":3.912392520844941E-5,"threadCount":82,"totalMemory":8364273664},"passiveConnectCount":0,"peerList":[{"active":true,"avgLatency":0.0,"blockInPorcSize":0,"connectTime":1582132205601,"disconnectTimes":0,"headBlockTimeWeBothHave":0,"headBlockWeBothHave":"Num:190317,ID:000000000002e76d87b8c76b0188f1828a3ba56a242a36684f0a670271f50f9e","host":"47.98.125.81","inFlow":6299,"lastBlockUpdateTime":1582132226063,"lastSyncBlock":"","localDisconnectReason":"","needSyncFromPeer":false,"needSyncFromUs":false,"nodeCount":3,"nodeId":"956363caba9036a1f434f3330ae7a20e37bbd0702a8f21bdc1daa27fe58e73cc454b88f64baa8569c80f922fc1cb09bf2ccf5a83bb82af2a40751b247714df7e","port":20000,"remainNum":0,"remoteDisconnectReason":"","score":145,"syncBlockRequestedSize":0,"syncFlag":false,"syncToFetchSize":0,"syncToFetchSizePeekNum":-1,"unFetchSynNum":0},{"active":true,"avgLatency":0.0,"blockInPorcSize":0,"connectTime":1582132206610,"disconnectTimes":0,"headBlockTimeWeBothHave":0,"headBlockWeBothHave":"Num:190317,ID:000000000002e76d87b8c76b0188f1828a3ba56a242a36684f0a670271f50f9e","host":"112.74.187.20","inFlow":10016,"lastBlockUpdateTime":1582132226063,"lastSyncBlock":"","localDisconnectReason":"","needSyncFromPeer":false,"needSyncFromUs":false,"nodeCount":3,"nodeId":"bfc1fef6b285c5127f8b5a45b9c5ed0592b21ac6285a2e46a4d7eaada7d32b76ea7f42aa7b54818f98e36065c83f178ce4f5006502e5b5fad2063d7e8208ecf8","port":20000,"remainNum":0,"remoteDisconnectReason":"","score":146,"syncBlockRequestedSize":0,"syncFlag":false,"syncToFetchSize":0,"syncToFetchSizePeekNum":-1,"unFetchSynNum":0}],"solidityBlock":"Num:201361,ID:0000000000031291c6985fb5c9660953b534149c82a65ab84a50b9bf3a532507","totalFlow":16315}

----------
## gettransactioninfobyid ##
Query the transaction fee, block height by transaction id

params|type|note
:|:|:
value|string|This can be the transaction ID.

POST http://47.111.255.193:8090/wallet/gettransactioninfobyid

    curl --location --request POST 'http://47.111.255.193:8090/wallet/gettransactioninfobyid' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-raw '{"value":"8682c4e11862feb6ea8ed501988f8349fdd2af0b8a4199e7ec4a533a5b94a736"}'

    response:
    {"id": "8682c4e11862feb6ea8ed501988f8349fdd2af0b8a4199e7ec4a533a5b94a736","fee": 100000,"blockNumber": 188082,"blockTimeStamp": 1582125519000,"contractResult": [""],"receipt": {"net_fee": 100000}}

----------
## gettransactionbyid ##
Query an transaction infromation by transaction id

params|type|note
:|:|:
value|string|This can be the transaction ID.

POST http://47.111.255.193:8090/wallet/gettransactionbyid

    curl --location --request POST 'http://47.111.255.193:8090/wallet/gettransactionbyid' --header 'Content-Type: application/x-www-form-urlencoded' --data-raw '{"value":"8682c4e11862feb6ea8ed501988f8349fdd2af0b8a4199e7ec4a533a5b94a736"}'

    response:
    {"ret":[{"contractRet":"SUCCESS"}],"signature":["05d8711b3e91511466e1213109d58dccde8d64efb8522203e62ccffe16237a631dfc144dea10340efc86f5e979c80e3ee730fd2acae573a28e313259c6db383500"],"txID":"8682c4e11862feb6ea8ed501988f8349fdd2af0b8a4199e7ec4a533a5b94a736","raw_data":{"contract":[{"parameter":{"value":{"amount":1000000,"owner_address":"190c22d79c37176df5cff31600facac21d5022faf1","to_address":"190f7d686fc33641e98fe563bf99a16b1b39a3beeb"},"type_url":"type.googleapis.com/protocol.TransferContract"},"type":"TransferContract"}],"ref_block_bytes":"ddda","ref_block_hash":"3bec0fc0a9026fec","expiration":1582126245000,"timestamp":1582124872646},"raw_data_hex":"0a02ddda22083bec0fc0a9026fec4088c9caf0852e5a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a15190c22d79c37176df5cff31600facac21d5022faf11215190f7d686fc33641e98fe563bf99a16b1b39a3beeb18c0843d70c6e7f6ef852e"}





