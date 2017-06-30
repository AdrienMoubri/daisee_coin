# Installation of Parity

Go to The [Download link](https://github.com/paritytech/parity/releases) 
Download the image corresponding to your OS.(this doc will use a linux libarairy)
Then create a file named demo-spec.json and paste the code below.

demo-spec.json
```javascript
{
    "name": "DemoPoA",
    "engine": {
        "authorityRound": {
            "params": {
                "gasLimitBoundDivisor": "0x400",
                "stepDuration": "5",
                "validators" : {
                    "list": []
                }
            }
        }
    },
    "params": {
        "maximumExtraDataSize": "0x20",
        "minGasLimit": "0x1388",
        "networkID" : "0x2323"
    },
    "genesis": {
        "seal": {
            "authorityRound": {
                "step": "0x0",
                "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "gasLimit": "0x5B8D80"
    },
    "accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } }
    }
}
```
Then create and paste the files :
node0.toml
```[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity0"
[network]
port = 30300
[rpc]
port = 8540
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[ui]
port = 8180
[dapps]
port = 8080
```

node1.toml
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity1"
[network]
port = 30301
[rpc]
port = 8541
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[ui]
port = 8181
[dapps]
port = 8081
[ipc]
disable = true
```

Start a terminal and launch ```parity --config node0.toml```

Start Web explorer and access the adress [127.0.0.1:8180](127.0.0.1:8180) 

follow the step of the account creation
go to the setting menu and check the status interface and the contract interface

go to the setting menu, and copy the enode value.

then open the file node1.toml and 
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity1"
[network]
port = 30301
bootnodes = ["enode://0xdeadbeef"]
[rpc]
port = 8541
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"\
]
[ui]
port = 8181
[dapps]
port = 8081
[ipc]
disable = true
```
replace _"enode://0xdeadbeef"_ by your enode value.
then you can launch the command ```parity --config node1.toml```
Access [127.0.0.1:8181](127.0.0.1:8181) 

then you can create a new account on the node1.
And copy the address of this account.
now you can stop node1 followed by node0 with ctrl+c or launch the commands : "ps aux | grep parity" and "kill -9 #num process parity#") on another terminal.


Now you can add money to an account by changing the demo-spec.json file by changing the accounts lines.
```
"accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
        "#numAccount#": { "balance": "10000000000000000000000" }
    }
```
deploy node0.toml and node1.toml with the command  ```parity --config node0.toml```
access [127.0.0.1:8181](127.0.0.1:8181)
and check the account balance.

#Create and deploy a contract and a certifier

To create a certifier, we need to launch the command ```parity account new --chain demo-spec.json --keys-path /tmp/parity0/keys```
enter and confirm the password of the mineer.
create a file node.pwds
and past the password of your mineer account.
access [127.0.0.1:8180](http://127.0.0.1:8180) and copy your mineer account ID it should be the unnamed account on your node0.
open the file node0.toml and add the lines 
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity0"
[network]
port = 30300
[rpc]
port = 8540
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"\
]
[account]
password = ["node.pwds"]
[ui]
port = 8180
[dapps]
port = 8080
[mining]
engine_signer = "#your mineer account ID#"
reseal_on_txs = "none"
```

once this is done open the file demo-spec.json
and add the mineer ID to the validators list
```
"engine": {
        "authorityRound": {
            "params": {
                "gasLimitBoundDivisor": "0x400",
                "stepDuration": "5",
                "validators" : {
                    "list": ["#your mineer ID#"]
                }
            }
        }
    },
```
stop and restart the two nodes.
Access one of your node account and go to the contract menu click on develop, paste your contract script compile, deploy and wait the mineer validation.
