# QA Report for zkSync Era System Contracts contest
## Overview
During the audit, 1 low and 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | ```verifyingContract``` is not used | Low | 1
NC-1 | Order of Functions | Non-Critical | 7
NC-2 | Order of Layout | Non-Critical | 4
NC-3 | Missing leading underscores | Non-Critical | 10
NC-4 | Scientific notation may be used | Non-Critical | 1
NC-5 | Unused named return variables | Non-Critical | 2
NC-6 |  Visibility is not set | Non-Critical | 4
NC-7 | Maximum line length exceeded | Non-Critical | 5

## Low Risk Findings(1)
### L-1. ```verifyingContract``` is not used
##### Description
[EIP712_DOMAIN_TYPEHASH](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L81) does not include the address of the contract that will verify the signature. The user-agent may do contract specific phishing prevention.
##### Instances
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L81
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L137-L139

##### Recommendation
Consider using ```bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");```
#
## Non-Critical Risk Findings(7)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
receive() function should be placed before fallback() function:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L13

receive() and fallback() functions should be placed before other functions:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L223
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L230

External functions should be placed before public:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L74-L123 

External functions should be placed before internal:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L117-L136 
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L199-L221

Public function should be placed before internal:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L117

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
Modifier should be placed between state variables and functions:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L15

Events should be placed before functions:
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IEthToken.sol#L22
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IEthToken.sol#L24
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IEthToken.sol#L26

#
### NC-3. Missing leading underscores 
##### Description
Internal and private constants, immutables, state variables, and functions should have a leading underscore.
##### Instances
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L20
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L19
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L48
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L27
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L30
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L35
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L40
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L43
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L138
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L228

##### Recommendation
Add leading underscores where needed.
#
### NC-4. Scientific notation may be used
##### Description
For readability and to avoid misprints, it is better to use scientific notation.
##### Instances
- [```uint256 public difficulty = 2500000000000000;```](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L40) 

##### Recommendation
Replace with ```25e14```.
#
### NC-5. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L128
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L33

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-6.  Visibility is not set
##### Instances
- [```mapping(address => uint256) balance;```](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L19) 
- [```uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;```](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L48) 
- [```uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;```](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L27) 
- [```uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;```](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L30) 

##### Recommendation
It is better to specify visibility explicitly.
#
### NC-7. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  Longer lines make the code harder to read.
##### Instances
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L23
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L49
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L15
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L24
- https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/EfficientCall.sol#L248

##### Recommendation
Make the lines shorter.