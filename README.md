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
6. Verify the contracts on an Etherscan flavored block explorer by running the following command. Ensure that `<ETHERSCAN_KEY>` is filled.
```
$ forge script script/deploy/deploy-fiat-token.s.sol -vv --gas-estimate-multiplier 110 --rpc-url testnet --verify --resume --etherscan-api-key <ETHERSCAN_KEY> --zksync
```

### Problem: LLVM IR generator definition pass error
I found [USDC](https://explorer.zksync.io/address/0xF4d6912ED152600356ea991F027046F6d1740b8d#contract) ([Proxy contract](https://explorer.zksync.io/address/0x1d17CBcF0D6D143135aE902365D2E5e2A16538D4#contract)) deploys with this similar code successfully on zkSync Era, but I still can't figure out how to solve this problem.

- Reproduce:
```
$ forge build --zksync
>
[⠒] Compiling...
No files changed, compilation skipped

Error:
Failed to compile with zksolc: Compilation failed with "Contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` compiling error: 

The contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` LLVM IR generator definition pass error: 

Library `contracts/util/SignatureChecker.sol:SignatureChecker` not found in the project

The contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` LLVM IR generator definition pass error: 

Library `contracts/util/SignatureChecker.sol:SignatureChecker` not found in the project\n\n\nStack backtrace:
    0: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm
    1: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm
    2: __mh_execute_header
    3: __mh_execute_header
    4: __mh_execute_header
    5: __mh_execute_header
    6: __mh_execute_header
    7: __mh_execute_header
    8: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm
    9: __mh_execute_header
    10: __mh_execute_header
    11: __mh_execute_header
    12: __mh_execute_header
    13: __mh_execute_header
    14: __mh_execute_header
    15: __mh_execute_header
    16: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm
    17: __mh_execute_header
    18: __mh_execute_header
    19: __mh_execute_header
    20: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm
    21: __pthread_deallocate\n". 

Using compiler: "/Users/chihaolu/.zksync/zksolc-macosx-arm64-v1.4.0", with args "/Users/chihaolu/Desktop/project/stablecoin-zkSync/contracts/v2/FiatTokenV2.sol" ["--standard-json", "--solc", "/Users/chihaolu/.svm/0.6.12/solc-0.6.12"]
```

- Solution: ❌
- Some related issues:
    - `type(*).runtimeCode`, zkSync do not support it: [link](https://github.com/matter-labs/hardhat-zksync/issues/99)
    - `EXTCODECOPY`: [link](https://github.com/zkSync-Community-Hub/zksync-developers/discussions/86)
    - `EXTCODECOPY`: [link](https://github.com/zkSync-Community-Hub/zksync-developers/discussions/101)
    - `solidity-coverage`: [link](https://github.com/zkSync-Community-Hub/zksync-developers/discussions/152)