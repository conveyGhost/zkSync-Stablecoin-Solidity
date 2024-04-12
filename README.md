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

```
$ forge build --zksync
>
[⠒] Compiling...
No files changed, compilation skipped

┌──────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Warning: Your code or one of its dependencies uses the 'extcodesize' instruction, which is       │
│ usually needed in the following cases:                                                           │
│   1. To detect whether an address belongs to a smart contract.                                   │
│   2. To detect whether the deploy code execution has finished.                                   │
│ zkSync Era comes with native account abstraction support (so accounts are smart contracts,       │
│ including private-key controlled EOAs), and you should avoid differentiating between contracts   │
│ and non-contract addresses.                                                                      │
└──────────────────────────────────────────────────────────────────────────────────────────────────┘
--> src/openzeppelin/contracts/utils/Address.sol

src/upgradeability/AdminUpgradeabilityProxy.sol:31:1: Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
contract AdminUpgradeabilityProxy is UpgradeabilityProxy {
^ (Relevant source part starts here and spans across multiple lines).
src/upgradeability/Proxy.sol:35:5: The payable fallback function is defined here.
    fallback() external payable {
    ^ (Relevant source part starts here and spans across multiple lines).

src/v1/FiatTokenProxy.sol:29:1: Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
contract FiatTokenProxy is AdminUpgradeabilityProxy {
^ (Relevant source part starts here and spans across multiple lines).
src/upgradeability/Proxy.sol:35:5: The payable fallback function is defined here.
    fallback() external payable {
    ^ (Relevant source part starts here and spans across multiple lines).

Error:
Failed to compile with zksolc: Compilation failed with "Contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` compiling error: The contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` LLVM IR generator definition pass error: Library `contracts/util/SignatureChecker.sol:SignatureChecker` not found in the projectThe contract `contracts/v2/FiatTokenV2.sol:FiatTokenV2` LLVM IR generator definition pass error: Library `contracts/util/SignatureChecker.sol:SignatureChecker` not found in the project\n\n\nStack backtrace:\n   0: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm\n   1: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm\n   2: __mh_execute_header\n   3: __mh_execute_header\n   4: __mh_execute_header\n   5: __mh_execute_header\n   6: __mh_execute_header\n   7: __mh_execute_header\n   8: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm\n   9: __mh_execute_header\n  10: __mh_execute_header\n  11: __mh_execute_header\n  12: __mh_execute_header\n  13: __mh_execute_header\n  14: __mh_execute_header\n  15: __mh_execute_header\n  16: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm\n  17: __mh_execute_header\n  18: __mh_execute_header\n  19: __mh_execute_header\n  20: __ZN4llvm15SmallVectorBaseIyE8grow_podEPvmm\n  21: __pthread_deallocate\n". Using compiler: "/Users/chihaolu/.zksync/zksolc-macosx-arm64-v1.4.0", with args "/Users/chihaolu/Desktop/project/stablecoin-zkSync/contracts/v2/FiatTokenV2.sol" ["--standard-json", "--solc", "/Users/chihaolu/.svm/0.6.12/solc-0.6.12"]
```

- Solution: ❌

I found a [similar issue in foundry-hardhat](https://github.com/matter-labs/hardhat-zksync/issues/99), and [USDC](https://explorer.zksync.io/address/0xF4d6912ED152600356ea991F027046F6d1740b8d#contract) deploys with this similar code successfully, but I still can't figure out how to solve this problem.