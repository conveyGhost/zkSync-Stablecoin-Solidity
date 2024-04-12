## Minimal Circle's Stablecoin Smart Contracts on zkSync Era

Modified from [Circle's Stablecoin repo](https://github.com/circlefin/stablecoin-evm).

DON'T USE THIS REPO IN YOUR PRODUCT!!! IT'S JUST FOR DEVELOPMENT ON TESTNET!

## Usage

### Build

```shell
$ forge install
$ forge build
```

### Compile

```shell
$ forge build --zksync
```

### Test

```shell
$ forge test --zksync
```

### Deployment

1. Create a copy of the file `.env.example`, and name it `.env`. Fill in appropriate values in the `.env` file. This file must not be checked into the repository.
```
$ cp .env.example .env

```
2. Create a `blacklist.remote.json` file and populate it with a list of addresses to be blacklisted. This file must not be checked into the repository.
```
$ echo "[]" > blacklist.remote.json
```
3. Simulate a deployment by running the following command
```
$ forge script script/deploy/deploy-fiat-token.s.sol:DeployFiatToken -vv --gas-estimate-multiplier 110  --rpc-url testnet --zksync
```
4. Validate that all transactions to be broadcasted are filled in with the correct values
5. Deploy the contracts by running the following command
```
$ forge script script/deploy/deploy-fiat-token.s.sol -vv --gas-estimate-multiplier 110 --rpc-url testnet --broadcast --zksync
```
6. Verify the contracts on an Etherscan flavored block explorer by running the following command. Ensure that `ETHERSCAN_KEY` is set in the `.env` file.
```
$ forge script script/deploy/deploy-fiat-token.s.sol -vv --gas-estimate-multiplier 110 --rpc-url testnet --verify --resume --etherscan-api-key <ETHERSCAN_KEY> --zksync
```