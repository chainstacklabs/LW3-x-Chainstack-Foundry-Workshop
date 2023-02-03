<img width="1200" alt="Labs" src="https://user-images.githubusercontent.com/99700157/213291931-5a822628-5b8a-4768-980d-65f324985d32.png">

<p>
 <h3 align="center">Chainstack is the leading suite of services connecting developers with Web3 infrastructure</h3>
</p>

<p align="center">
  <a target="_blank" href="https://chainstack.com/build-better-with-ethereum/"><img src="https://github.com/soos3d/blockchain-badges/blob/main/protocols_badges/Ethereum.svg" /></a>&nbsp;  
  <a target="_blank" href="https://chainstack.com/build-better-with-bnb-smart-chain/"><img src="https://github.com/soos3d/blockchain-badges/blob/main/protocols_badges/BNB.svg" /></a>&nbsp;
  <a target="_blank" href="https://chainstack.com/build-better-with-polygon/"><img src="https://github.com/soos3d/blockchain-badges/blob/main/protocols_badges/Polygon.svg" /></a>&nbsp;
  <a target="_blank" href="https://chainstack.com/build-better-with-avalanche/"><img src="https://github.com/soos3d/blockchain-badges/blob/main/protocols_badges/Avalanche.svg" /></a>&nbsp;
  <a target="_blank" href="https://chainstack.com/build-better-with-solana/"><img src="https://github.com/soos3d/blockchain-badges/blob/main/protocols_badges/Solana.svg" /></a>&nbsp;
</p>

<p align="center">
  <a target="_blank" href="https://chainstack.com/protocols/">Supported protocols</a> •
  <a target="_blank" href="https://chainstack.com/blog/">Chainstack blog</a> •
  <a target="_blank" href="https://docs.chainstack.com/quickstart/">Chainstack docs</a> •
  <a target="_blank" href="https://docs.chainstack.com/quickstart/">Blockchain API reference</a> •
  <a target="_blank" href="https://console.chainstack.com/user/account/create">Start for free</a>
</p>

# Introduction to Foundry Learn Web3 DAO & Chainstack workshop

This repository holds the code to be followed along with the Foundry workshop conducted by Chainstack on LWB3.
Feel free to follow this README at your own pace.

[Workshop video](https://www.youtube.com/watch?v=FGl2gk3G71U).

## Installing Foundry

> This installation method has been tested on Linux and MacOS. Windows users will need to build from Foundry's Github repo. Refer to Foundry docs for [detailed instructions](https://book.getfoundry.sh/getting-started/installation#on-windows-build-from-source "detailed instructions").

To get started open your terminal, and run the following command-

```sh
curl --version
```

This command will return the version  of the [curl CLI](https://curl.se/ "curl CLI") installed in your system. You need it installed before moving on to install Foundry.

Install Foundry in your system using the curl command-

```sh
curl -L https://foundry.paradigm.xyz | bash
```

> Note that you might need to restart the terminal in case the `foundryup` command is not recognized.

This will install *foundryup* into your system. Install Foundry by running-

```sh
foundryup
```

And that's it. You now have Foundry installed into your system.
If you face any errors while installing Foundry, refer to the [docs](https://book.getfoundry.sh/ "docs").

This installs 3 seperate CLI tools to into your system.

1. #### Forge
   **Forge** is used to initilaize, build, test and deploy a Foundry project.

2. #### Cast
   **Cast** is the CLI tool that allows us to perfrom RPC calls to the EVM. We can interact with already deployed smart contracts right from our terminals, to both read and sign transactions.

3. #### Anvil
   **Anvil** is the local testnet node that is shipped with Foundry. You can use Anvil to simulate an EVM enviornment without having to deploy to a live testnet. Truffle and Hardhat also ship similar local testnets.

## Getting Started

To set up a Foundry project, create a new folder and open the directory in your terminal. In the terminal, run-

```sh
forge init
```

This will set up a basic Foundry project complete with a test and a deployment script. 
You can compile contracts and generate ABIs for them using-

```sh
forge build
```

## Simple ERC20 contract

We will use Foundry to compile, test and deploy an ERC20 token to the Goerli testnet.

### Installing Opzeppelin contracts library

By default, Forge manages dependencies as Git Submodules. 
To install Openzeppelin contracts as a dependency, run this command in your terminal-

```sh
forge install OpenZeppelin/openzeppelin-contracts
```

This will install the [Openzepplin contracts library](https://github.com/OpenZeppelin/openzeppelin-contracts "Openzepplin contracts library") directly from the Github repo into oyur 'lib' folder. 
By default, forge installs the code from the latest version of the master branch from the referenced repository.

### Configuring remappings

Inside the src folder, create a file named `ERC20.sol`.  Paste the following code inside the file-

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.17;
    
    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
    import "@openzeppelin/contracts/access/Ownable.sol";
    contract MyToken is ERC20, ERC20Burnable, Ownable {
        constructor() ERC20("FoundryToken", "FTK") {
            _mint(msg.sender, 1000 * 10 ** decimals());
        }
    
        function mint(address to, uint256 amount) public onlyOwner {
            _mint(to, amount);
        }
    }

If you try to compile this code with `forge build`, you will notice that the code doesn't compile because Forge cannot locate the files we are trying to import into our smart contract.
This brings us to remappings.

We need to define the exact path of our dependencies to help forge find them.
In your terminal, run-

```sh
forge remappings
```

This will return some remappings that are generated by default. To create a *remappings.txt* from the pre-existing remappings, run-

```sh
forge remappings > remappings.txt
```

Add this line to the remappings file-

```sh
openzeppelin/=lib/openzeppelin-contracts/contracts/
```

This does two things for us.
1. We create a new remapping named *openzeppelin* that points forge to our contracts library.
2. This remapping points directly to the contracts folder inside our library, since all of the required imports are located inside the contracts folder.

Edit all of your imports inside `ERC20.sol` to look like this-

    import "openzeppelin/token/ERC20/ERC20.sol";
    import "openzeppelin/token/ERC20/extensions/ERC20Burnable.sol";
    import "openzeppelin/access/Ownable.sol";

The errors are now resolved. Run `forge build` again to finally compile our ERC20 smart contract.

### foundry.toml

Foundry allows users a high degree of customization.
A `foundry.toml` file is created at the root of every Foundry project. We can use it to customize almost every Forge configuration.
We can also create multiple profiles if all of our smart contracts can't be compiled with a uniform configuration.
For now, add this line to foundry.toml-

```sh
solc = "0.8.17"
```

This will ensure our ERC20 contract is compiled with Solidity version 0.8.17. You can see a foundry.toml file with all the configurations set [here](https://github.com/foundry-rs/foundry/blob/master/config/README.md#configuration "here"). 
This [Gist](https://gist.github.com/Genesis3800/bba57037e2b7180f0fda7bc252ddee3b "Gist") contains a list of default toml configurations for Foundry.

### Testing in Foundry

Foundry tests are written in Solidity.

The preffered way of writing Foundry tests is to import the `Test.sol` smart contract provided to us by Forge. The [Test.sol](https://github.com/foundry-rs/forge-std/blob/master/src/Test.sol "Test.sol") contract is itself a superset of many different smart contracts that allow us to make assertions and log values to the terminal.
Forge also gives us a variety of utils and cheatcodes designed to make testing easier.

To test the ERC20 contract-
1. Create a new file nameds `ERC20.t.sol` inside the test folder. Copy all of the code from the equivalent file in this repo, and paste it inside your project.
2. Run 

```sh
forge test
```

For a simple forge test command, Forge will scan the entire source directory for functions starting with the string "***test***". This means a function named "testTransfer" will be tested by Foundry, while a function named "Transfer" won't be.
By convention, all the test contracts are placed under the test folder, and are suffixed with '**.*t.sol***'.

What if you only want to run a specific test file?
To run only the ERC20.t.sol file, run this command in your terminal-

```sh
forge test --match-path test/ERC20.t.sol
```

You can find a complete reference for the Forge test command in [Foundry's docs](https://book.getfoundry.sh/reference/forge/forge-test "Foundry's docs").

### Setting up dotenv file

Before moving on to deploying and verifying our ERC20 contract, we need to set up a dotenv file. It is not recommended to directly pass sensitive values to Foundry in the command line or foundry.toml, since you may end up pushing them to a remote repository.

Create a `.env` file at the root of your project.
Fill it like this-

    RPC_URL="https://mc-12.p123yippy.com/12ase525c5012"
    PRIVATE_KEY=dlhj12342kjh4eslkh1pq4h13sd13vb14kqwrekhwe
    ETHERSCAN_API_KEY=SDJKASL232ADS7262AS

Replace all the dummy data with real values. You can get an RPC endpoint from [Chainstack](https://chainstack.com/ "Chainstack"), and an Etherscan API key from [Etherscan](https://etherscan.io/myapikey "Etherscan").

Now save the `.env` file and in your terminal, run-

```sh
source .env
```

This command will load up the env variables.

### Deploying and verifying using Foundry

Create a file named `ERC20.s.sol` inside the script folder. Copy all of the code from the equivalent file in this repo, and paste it inside your project.

Foundry allows us to deploy contracts either by using a deploy script (which is what we will be doing), or by using the `forge create` command. You can however only deploy one smart contract at a time using the forge create command.

To deploy the ERC20 contract using our script, run-

```sh
forge script script/ERC20.s.sol:ERC20Deploy --rpc-url $RPC_URL --private-key $PRIVATE_KEY --broadcast --verify --etherscan-api-key $ETHERSCAN_API_KEY -vvvv
```

To deploy using the forge create command-

    forge create --rpc-url $RPC_URL \                                                                                  --private-key $PRIVATE_KEY src/ERC20.sol/MyToken \
    --etherscan-api-key $ETHERSCAN_API_KEY \
    --verify

Ideally Forge should be able to verify your smart contract right after deploying it. But if that doesn't work, we can also verify pre-existing smart contracts by running this command-

    forge verify-contract --chain-id 5 --num-of-optimizations 200 --watch --compiler-version v0.8.17+commit.8df45f5f <YOUR_SMART_CONTRACT_ADDRESS> src/ERC20.sol:MyToken $ETHERSCAN_API_KEY
 

> Note- Foundry allows you to set the number of optimizations in `foundry.toml`. The default value is 200. This value needs to be passed as a parameter to the forge verify command. Also be careful to specify the exact compiler version.

## Cast

Cast is shipped with Foundry as a separate CLI. Can be used to make calls, send transactions or retrieve a wide range of data. Can be used to interact with contracts we deployed.
Basically it is a tool to interact with the blockchain form the terminal.

*cast chain* can be used to fetch blockchain data. Here are two example-

```bash
cast chain-id --rpc-url $RPC_URL
cast chain --rpc-url $RPC_URL
```

*cast rpc* can be used to send any JSON-RPC post request through your blockchain node. 
Example-

```bash
cast rpc eth_getTransactionByHash <Txn_Hash> --rpc-url $RPC_URL
```
You can read about more about Ethereums's JSON_RPC implementation from [here](https://docs.chainstack.com/api/ethereum/eth_gettransactionbyhash "here").

You can use *cast call* to read data from the blockchain. The following command reads the balance of a particular address from this [contract address](https://goerli.etherscan.io/address/0x806DaB016369A2e74140e34de28ea78e472a1Ab4 "contract address").
This function takes an address as a parameter, and returns the account balance in the form of a uint256 return value.

```bash
cast call 0x806DaB016369A2e74140e34de28ea78e472a1Ab4 "balanceOf(address)(uint256)" <Account_Address> --rpc-url $RPC_URL
```

Finally, you can use *cast send* to sign and send transactions. Please note that you will need a private key to use this command.
The following command will invoke the transfer function on our ERC20 contract to transfer tokens from one account to another.

```bash
cast send --private-key $PRIVATE_KEY 0x806DaB016369A2e74140e34de28ea78e472a1Ab4 "function transfer(address,uint256)" "<Recipient_Address>" "<Amount_To_Transfer>" --rpc-url $RPC_URL
```
Cast is an incredibly powerful tool, and supports many more functionalities. You can read the detailed reference to Cast from [Foundry docs](https://book.getfoundry.sh/reference/cast/ "the docs").

## Anvil

Anvil is the local testnet node that is shipped with Foundry. You can use Anvil to simulate an EVM enviornment without having to deploy to a live testnet. Truffle and Hardhat also ship similar local testnets.

To start using anvil, open a new terminal, and run:

```bash
anvil
```
By default, anvil produces a new block only when a new transaction is submitted. You can change configure this behaviour such that it generates a new block after a fixed period of time.

```bash
anvil --block-time 12
```

To deploy a contract on anvil:

```bash
forge script script/ERC20.s.sol:ERC20Deploy --rpc-url http://localhost:8545 --private-key <Anvil_Private_Key> --broadcast
```

You can now use cast to interact with your smart contract as usual.
You can check out the detaild reference to anvil [here](https://book.getfoundry.sh/anvil/ "here").

