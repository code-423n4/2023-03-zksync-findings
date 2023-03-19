## 1. Non-library/interface files should use fixed compiler versions, not floating ones
Contracts should be deployed with the same compiler version and flags that they
have been tested with thoroughly. Locking the pragma helps to ensure that 
contracts do not accidentally get deployed using, for example, an outdated compiler
 version that might introduce bugs that affect the contract system negatively.
 ```
contracts/EmptyContract.sol 
contracts/ImmutableSimulator.sol
contracts/L1Messenger.sol
contracts/MsgValueSimulator.sol 
contracts/BytecodeCompressor.sol
contracts/AccountCodeStorage.sol
contracts/L2EthToken.sol
contracts/SystemContext.sol
contracts/KnownCodesStorage.sol
contracts/NonceHolder.sol
contracts/DefaultAccount.sol 
contracts/ContractDeployer.sol
contracts/BootloaderUtilities.sol
```

##  2. Order of Layout
https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout

Inside each contract, library or interface, use the following order:
1. Type declarations
2. State variables
3. Events
4. Modifiers
5. Functions

```
contracts/SystemContext.sol#L15
contracts/ContractDeployer.sol#L195
```

## 3. Function order.

Functions should be ordered following the [Solidity conventions](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-functions): 
receive() function should be placed after the constructor and before every 
other function.

```
contracts/DefaultAccount.sol#L222
contracts/MsgValueSimulator.sol
```

MITIGATION
Place the receive() and fallback() functions after the constructor, before all the 
other functions.

## 4. Use require instead of assert
Properly functioning code should never reach a failing assert statement. If it 
happened, it would indicate the presence of a bug in the contract. A failing assert 
uses all the remaining gas, which can be financially painful for a user.

```
contracts/DefaultAccount.sol#L224
```
MITIGATION
Replace the assert statements with a require statement or a custom error
## 5.it's better to specify visibility
```
contracts/L2EthToken.sol#L19
"balance"
contracts/NonceHolder.sol#L27
"DEPLOY_NONCE_MULTIPLIER"
contracts/NonceHolder.sol#L30
"MAXIMAL_MIN_NONCE_INCREMENT"
```
## 6. Require statements should have descriptive strings.
```
contracts/SystemContext.sol#L16
contracts/L1Messenger.sol#L46
```
## 7.unnecessary setting value
```
contracts/ContractDeployer.sol#L237
"sumOfValues = 0"
```
```
contracts/KnownCodesStorage.sol#L34
contracts/ImmutableSimulator.sol#36
contracts/ContractDeployer.sol#L243
contracts/ContractDeployer.sol#L238
"uint256 i = 0"
```