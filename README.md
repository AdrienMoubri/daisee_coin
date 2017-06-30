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

Start Web explorer and access the adress [127.0.0.1:8180](http://127.0.0.1:8180) 

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
Access [127.0.0.1:8181](http://127.0.0.1:8181) 

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
access [127.0.0.1:8181](http://127.0.0.1:8181)
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
# Deploying the smart contract
```
pragma solidity ^0.4.2;
/* from tutorial : https://www.ethereum.org/token */
contract owned {
    address public owner;

    function owned() {
        owner = msg.sender;
    }

    modifier onlyOwner {
        if (msg.sender != owner) throw;
        _;
    }

    function transferOwnership(address newOwner) onlyOwner {
        owner = newOwner;
    }
}

contract tokenRecipient { function receiveApproval(address _from, uint256 _value, address _token, bytes _extraData); }

contract token {
    /* Public variables of the token */
    string public standard = 'Token 0.1';
    string public name;
    string public symbol;
    uint8 public decimals;
    uint256 public totalSupply;

    /* This creates an array with all balances */
    mapping (address => uint256) public balanceOf;
    mapping (address => mapping (address => uint256)) public allowance;

    /* This generates a public event on the blockchain that will notify clients */
    event Transfer(address indexed from, address indexed to, uint256 value);

    /* Initializes contract with initial supply tokens to the creator of the contract */
    function token(
        uint256 initialSupply,
        string tokenName,
        uint8 decimalUnits,
        string tokenSymbol
        ) {
        balanceOf[msg.sender] = initialSupply;              // Give the creator all initial tokens
        totalSupply = initialSupply;                        // Update total supply
        name = tokenName;                                   // Set the name for display purposes
        symbol = tokenSymbol;                               // Set the symbol for display purposes
        decimals = decimalUnits;                            // Amount of decimals for display purposes
    }

    /* Send coins */
    function transfer(address _to, uint256 _value) {
        if (balanceOf[msg.sender] < _value) throw;           // Check if the sender has enough
        if (balanceOf[_to] + _value < balanceOf[_to]) throw; // Check for overflows
        balanceOf[msg.sender] -= _value;                     // Subtract from the sender
        balanceOf[_to] += _value;                            // Add the same to the recipient
        Transfer(msg.sender, _to, _value);                   // Notify anyone listening that this transfer took place
    }

    /* Allow another contract to spend some tokens in your behalf */
    function approve(address _spender, uint256 _value)
        returns (bool success) {
        allowance[msg.sender][_spender] = _value;
        tokenRecipient spender = tokenRecipient(_spender);
        return true;
    }

    /* Approve and then comunicate the approved contract in a single tx */
    function approveAndCall(address _spender, uint256 _value, bytes _extraData)
        returns (bool success) {
        tokenRecipient spender = tokenRecipient(_spender);
        if (approve(_spender, _value)) {
            spender.receiveApproval(msg.sender, _value, this, _extraData);
            return true;
        }
    }

    /* A contract attempts to get the coins */
    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
        if (balanceOf[_from] < _value) throw;                 // Check if the sender has enough
        if (balanceOf[_to] + _value < balanceOf[_to]) throw;  // Check for overflows
        if (_value > allowance[_from][msg.sender]) throw;   // Check allowance
        balanceOf[_from] -= _value;                          // Subtract from the sender
        balanceOf[_to] += _value;                            // Add the same to the recipient
        allowance[_from][msg.sender] -= _value;
        Transfer(_from, _to, _value);
        return true;
    }

    /* This unnamed function is called whenever someone tries to send ether to it */
    function () {
        throw;     // Prevents accidental sending of ether
    }
}

contract MyAdvancedToken is owned, token {

    uint256 public sellPrice;
    uint256 public buyPrice;

    mapping (address => bool) public frozenAccount;

    /* This generates a public event on the blockchain that will notify clients */
    event FrozenFunds(address target, bool frozen);

    /* Initializes contract with initial supply tokens to the creator of the contract */
    function MyAdvancedToken(
        uint256 initialSupply,
        string tokenName,
        uint8 decimalUnits,
        string tokenSymbol
    ) token (initialSupply, tokenName, decimalUnits, tokenSymbol) {}

    /* Send coins */
    function transfer(address _to, uint256 _value) {
        if (balanceOf[msg.sender] < _value) throw;           // Check if the sender has enough
        if (balanceOf[_to] + _value < balanceOf[_to]) throw; // Check for overflows
        if (frozenAccount[msg.sender]) throw;                // Check if frozen
        balanceOf[msg.sender] -= _value;                     // Subtract from the sender
        balanceOf[_to] += _value;                            // Add the same to the recipient
        Transfer(msg.sender, _to, _value);                   // Notify anyone listening that this transfer took place
    }


    /* A contract attempts to get the coins */
    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
        if (frozenAccount[_from]) throw;                        // Check if frozen
        if (balanceOf[_from] < _value) throw;                 // Check if the sender has enough
        if (balanceOf[_to] + _value < balanceOf[_to]) throw;  // Check for overflows
        if (_value > allowance[_from][msg.sender]) throw;   // Check allowance
        balanceOf[_from] -= _value;                          // Subtract from the sender
        balanceOf[_to] += _value;                            // Add the same to the recipient
        allowance[_from][msg.sender] -= _value;
        Transfer(_from, _to, _value);
        return true;
    }

    function mintToken(address target, uint256 mintedAmount) onlyOwner {
        balanceOf[target] += mintedAmount;
        totalSupply += mintedAmount;
        Transfer(0, this, mintedAmount);
        Transfer(this, target, mintedAmount);
    }

    function freezeAccount(address target, bool freeze) onlyOwner {
        frozenAccount[target] = freeze;
        FrozenFunds(target, freeze);
    }

    function setPrices(uint256 newSellPrice, uint256 newBuyPrice) onlyOwner {
        sellPrice = newSellPrice;
        buyPrice = newBuyPrice;
    }

    function buy() payable {
        uint amount = msg.value / buyPrice;                // calculates the amount
        if (balanceOf[this] < amount) throw;               // checks if it has enough to sell
        balanceOf[msg.sender] += amount;                   // adds the amount to buyer's balance
        balanceOf[this] -= amount;                         // subtracts amount from seller's balance
        Transfer(this, msg.sender, amount);                // execute an event reflecting the change
    }

    function sell(uint256 amount) {
        if (balanceOf[msg.sender] < amount ) throw;        // checks if the sender has enough to sell
        balanceOf[this] += amount;                         // adds the amount to owner's balance
        balanceOf[msg.sender] -= amount;                   // subtracts the amount from seller's balance
        if (!msg.sender.send(amount * sellPrice)) {        // sends ether to the seller. It's important
            throw;                                         // to do this last to avoid recursion attacks
        } else {
            Transfer(msg.sender, this, amount);            // executes an event reflecting on the change
        }
    }
}

contract Daisee {

    // variables
    //// tarif de l'énergie en DaiseeCoin
    uint public rate;
    bool private transactionOK;

    //// utilisateurs
    mapping (address => uint) public energyProduction;
    mapping (address => uint) public totalEnergyConsumption;
    ///// energyConsumption[msg.sender][origin]
    mapping (address => mapping (address => uint)) public energyConsumption;
    ///// allowance[seller][msg.sender]
    mapping (address => mapping (address => uint)) public allowance;

    //// list of sellers
    struct Seller {
        uint index;
      }
    mapping (address => Seller) private sellersList;
    address[] public sellerIndex;

    // constructeur

    function Daisee() {
        rate = 1; // (=> 1W = 1 DaiseeCoin)
    }

    // définition des events (pour affichage à partir de la lecture des logs)
    event Produce(address from, uint energy);
    event Consume(address from, address origin, uint energy);
    event Buy(address from, address to, uint energy);


    // fonction permettant de payer en DaiseeCoin
	function sendCoin(address coinContractAddress,
	                  address energyBuyer,
	                  address energySeller,
	                  uint amount)
	                  returns (bool success){
		token m = token(coinContractAddress);
		success = m.transferFrom(energyBuyer, energySeller, amount);
		return success;
	}

    function nbSellers() public constant returns (uint) {
        return sellerIndex.length;
    }


    function isSeller(address sellerAddress) public constant returns(bool isSeller) {
        if(sellerIndex.length == 0) return false;
        return (sellerIndex[sellersList[sellerAddress].index] == sellerAddress);
    }


    function addSeller(address sellerAddress) private returns (uint nbSellers) {
        if(!isSeller(sellerAddress)) {
            sellersList[sellerAddress].index = sellerIndex.push(sellerAddress)-1;
        }
    }


    // fonction permettant de mettre à jour l'énergie produite et
    // donc dispo à la vente
    // seul le propriétaire du compte peut mettre à jour sa prod
    function setProduction(uint energy) returns (uint EnergyProd) {
        energyProduction[msg.sender] += energy;

        //event
        Produce(msg.sender, energy);

        return energyProduction[msg.sender];
    }

    // fonction permettant de consommer de l'énergie
    // seul le propriétaire du compte peut mettre à jour sa prod
    function consumeEnergy (address origin, uint energy) returns (uint EnergyCons) {
        // dans le cas où on achète de l'énergie d'un autre noeud
        if ( origin != msg.sender &&
             energy > allowance[origin][msg.sender] ) throw;
        else allowance[origin][msg.sender]    -= energy;

        energyConsumption[msg.sender][origin] += energy;
        totalEnergyConsumption[msg.sender]    += energy;

        // event
        Consume(msg.sender, origin, energy);

        return totalEnergyConsumption[msg.sender];
    }


    // fonction permettant la vente d'énergie
    function buyEnergy(address coinContractAddress, address seller, uint energy) returns (bool transactionOK) {

        // on verifie d'abord que l'acheteur n'achète pas sa propre énergie
        if (msg.sender == seller) throw;

        // appel de la fonction de transfer de DaiseeCoin
        // 1W = 1DaiseeCoin, pas de besoin de conversion
        transactionOK = sendCoin(coinContractAddress, msg.sender, seller, energy);
        if (transactionOK != true) throw;

        // on met à jour :
        // - la liste des vendeurs
        addSeller(seller);
        // - la quantité d'énergie pouvant être consommée
        allowance[seller][msg.sender] += energy;

        //event
        Buy(msg.sender, seller, energy);

        return transactionOK;
    }

}
```
