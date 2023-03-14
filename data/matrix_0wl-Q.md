## Non Critical Issues

|       | Issue                                                                                    |
| ----- | :--------------------------------------------------------------------------------------- |
| NC-1  | ADD TO INDEXED PARAMETER FOR COUNTABLE EVENTS                                            |
| NC-2  | ADD A TIMELOCK TO CRITICAL FUNCTIONS                                                     |
| NC-3  | USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)                                     |
| NC-4  | GENERATE PERFECT CODE HEADERS EVERY TIME                                                 |
| NC-5  | SOLIDITY COMPILER VERSIONS MISMATCH                                                      |
| NC-6  | SIGNATURE MALLEABILITY OF EVM’S `ECRECOVER()`                                            |
| NC-7  | FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES                                  |
| NC-8  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |
| NC-9  | LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION                                    |
| NC-10 | FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION                            |
| NC-11 | NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS                                        |
| NC-12 | NO SAME VALUE INPUT CONTROL                                                              |
| NC-13 | FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE                      |
| NC-14 | USE A MORE RECENT VERSION OF SOLIDITY                                                    |
| NC-15 | FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS                  |
| NC-16 | STOP USING V != 27 && V != 28 OR V == 27                                                 |
| NC-17 | LINES ARE TOO LONG                                                                       |
| NC-18 | USE UNDERSCORES FOR NUMBER LITERALS                                                      |

### [NC-1] ADD TO INDEXED PARAMETER FOR COUNTABLE EVENTS

#### Description:

Add to indexed parameter for countable Events

#### **Proof Of Concept**

```solidity
File: contracts/interfaces/IL2StandardToken.sol

6:     event BridgeMint(address indexed _account, uint256 _amount);

8:     event BridgeBurn(address indexed _account, uint256 _amount);

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

22:     event Approval(address indexed owner, address indexed spender, uint256 value);

```

#### Recommendation:

Add Event-Emit

### [NC-2] ADD A TIMELOCK TO CRITICAL FUNCTIONS

#### Description:

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

#### **Proof Of Concept**

```solidity
File: contracts/ImmutableSimulator.sol

33:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {

```

```solidity
File: contracts/NonceHolder.sol

81:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

```

```solidity
File: contracts/SystemContext.sol

60:     function setTxOrigin(address _newOrigin) external onlyBootloader {

66:     function setGasPrice(uint256 _gasPrice) external onlyBootloader {

109:     function setNewBlock(

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

13:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external;

```

```solidity
File: contracts/interfaces/INonceHolder.sol

27:     function setValueUnderNonce(uint256 _key, uint256 _value) external;

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

164:     function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

```

### [NC-3] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)

Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, `bytes.concat()` is enabled.

Since version 0.8.4 for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked(,)`.

#### **Proof Of Concept**

```solidity
File: contracts/L2EthToken.sol

98:         return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);

```

```solidity
File: contracts/libraries/TransactionHelper.sol

132:                 keccak256(abi.encodePacked(_transaction.factoryDeps)),

141:         return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

### [NC-4] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-5] SOLIDITY COMPILER VERSIONS MISMATCH

The project is compiled with different versions of Solidity, which is not recommended because it can lead to undefined behaviors.

It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks.

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/Constants.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```

### [NC-6] SIGNATURE MALLEABILITY OF EVM’S `ECRECOVER()`

#### Description:

The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

References: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.

While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### **Proof Of Concept**

```solidity
File: contracts/DefaultAccount.sol

189:         address recoveredAddress = ecrecover(_hash, v, r, s);

```

#### Recommended Mitigation Steps:

Use the `ecrecover` function from OpenZeppelin’s ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [NC-7] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

#### Description:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

5: import "./interfaces/IAccountCodeStorage.sol";

6: import "./libraries/Utils.sol";

```

```solidity
File: contracts/BootloaderUtilities.sol

5: import "./interfaces/IBootloaderUtilities.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/RLPEncoder.sol";

8: import "./libraries/EfficientCall.sol";

```

```solidity
File: contracts/BytecodeCompressor.sol

5: import "./interfaces/IBytecodeCompressor.sol";

6: import "./Constants.sol";

7: import "./libraries/Utils.sol";

8: import "./libraries/UnsafeBytesCalldata.sol";

```

```solidity
File: contracts/Constants.sol

5: import "./interfaces/IAccountCodeStorage.sol";

6: import "./interfaces/INonceHolder.sol";

7: import "./interfaces/IContractDeployer.sol";

8: import "./interfaces/IKnownCodesStorage.sol";

9: import "./interfaces/IImmutableSimulator.sol";

10: import "./interfaces/IEthToken.sol";

11: import "./interfaces/IL1Messenger.sol";

12: import "./interfaces/ISystemContext.sol";

13: import "./interfaces/IBytecodeCompressor.sol";

14: import "./BootloaderUtilities.sol";

```

```solidity
File: contracts/ContractDeployer.sol

6: import "./interfaces/IContractDeployer.sol";

9: import "./libraries/Utils.sol";

10: import "./libraries/EfficientCall.sol";

```

```solidity
File: contracts/DefaultAccount.sol

5: import "./interfaces/IAccount.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/SystemContractHelper.sol";

8: import "./libraries/EfficientCall.sol";

```

```solidity
File: contracts/ImmutableSimulator.sol

5: import "./interfaces/IImmutableSimulator.sol";

```

```solidity
File: contracts/KnownCodesStorage.sol

5: import "./interfaces/IKnownCodesStorage.sol";

6: import "./libraries/Utils.sol";

7: import "./libraries/SystemContractHelper.sol";

```

```solidity
File: contracts/L1Messenger.sol

5: import "./interfaces/IL1Messenger.sol";

6: import "./libraries/SystemContractHelper.sol";

7: import "./libraries/EfficientCall.sol";

```

```solidity
File: contracts/MsgValueSimulator.sol

5: import "./libraries/EfficientCall.sol";

```

```solidity
File: contracts/NonceHolder.sol

5: import "./interfaces/INonceHolder.sol";

6: import "./interfaces/IContractDeployer.sol";

```

```solidity
File: contracts/interfaces/IAccount.sol

5: import "../libraries/TransactionHelper.sol";

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

5: import "../libraries/TransactionHelper.sol";

```

```solidity
File: contracts/interfaces/IPaymaster.sol

5: import "../libraries/TransactionHelper.sol";

```

```solidity
File: contracts/libraries/EfficientCall.sol

5: import "./SystemContractHelper.sol";

6: import "./Utils.sol";

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

7: import "./SystemContractsCaller.sol";

8: import "./Utils.sol";

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

6: import "./Utils.sol";

```

```solidity
File: contracts/libraries/TransactionHelper.sol

5: import "../openzeppelin/token/ERC20/IERC20.sol";

6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";

8: import "../interfaces/IPaymasterFlow.sol";

9: import "../interfaces/IContractDeployer.sol";

11: import "./RLPEncoder.sol";

12: import "./EfficientCall.sol";

```

```solidity
File: contracts/libraries/Utils.sol

4: import "./EfficientCall.sol";

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

6: import "../IERC20.sol";

7: import "../extensions/IERC20Permit.sol";

8: import "../../../utils/Address.sol";

```

#### Recommended Mitigation Steps:

`import {contract1 , contract2} from "filename.sol";` OR Use specific imports syntax per solidity docs recommendation.

### [NC-8] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Description:

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

#### **Proof Of Concept**

```solidity
File: contracts/libraries/TransactionHelper.sol

81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

83:     bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
        keccak256(

```

### [NC-9] LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

#### Description:

Use (e.g. 1e5) rather than decimal literals (e.g. 10000), for better code readability.

#### **Proof Of Concept**

```solidity
File: contracts/SystemContext.sol

40:     uint256 public difficulty = 2500000000000000;

```

### [NC-10] FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION

#### Description:

https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/Constants.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```

#### Recommended Mitigation Steps:

Use solidity pragma version min. 0.8.13

### [NC-11] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS

#### Description:

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.

In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html.

#### Recommended Mitigation Steps:

NatSpec comments should be increased in contracts

### [NC-12] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: contracts/SystemContext.sol

67:         gasPrice = _gasPrice;

124:         baseFee = _baseFee;

134:         baseFee = _baseFee;

```

### [NC-13] FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE

#### Context:

Files: contracts/AccountCodeStorage.sol, contracts/ContractDeployer.sol, contracts/DefaultAccount.sol, contracts/KnownCodesStorage.sol, contracts/L2EthToken.sol, contracts/NonceHolder.sol, contracts/SystemContext.sol, contracts/libraries/EfficientCall.sol, contracts/libraries/TransactionHelper.sol

#### Description:

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private, within a grouping, place the view and pure functions last

### [NC-14] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

For security, it is best practice to use the latest Solidity version (0.8.19). For the security fix list in the versions; https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/Constants.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```

### [NC-15] FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

#### Description:

Solidity’s standard naming convention for internal and private functions and variables (apart from constants): the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

Solidity’s standard naming convention for internal and private constants variables: the snake_case format starting with an underscore (\_mixedCase starting with an underscore) and use ALL_CAPS for naming them.

#### **Proof Of Concept**

```solidity
File: contracts/BootloaderUtilities.sol

43:     function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {

138:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

228:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

```

```solidity
File: contracts/libraries/EfficientCall.sol

35:     function keccak(bytes calldata _data) internal view returns (bytes32) {

44:     function sha(bytes calldata _data) internal view returns (bytes32) {

56:     function call(

71:     function staticCall(

85:     function delegateCall(

102:     function mimicCall(

120:     function rawCall(

154:     function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success) {

168:     function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) {

186:     function rawMimicCall(

227:     function propagateRevert() internal pure {

```

```solidity
File: contracts/libraries/RLPEncoder.sol

6:     function encodeAddress(address _val) internal pure returns (bytes memory encoded) {

19:     function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {

44:     function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {

51:     function encodeListLen(uint64 _len) internal pure returns (bytes memory) {

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

48:     function toL1(bool _isService, bytes32 _key, bytes32 _value) internal {

63:     function getCodeAddress() internal view returns (address addr) {

74:     function loadCalldataIntoActivePtr() internal view {

85:     function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view {

94:     function ptrAddIntoActive(uint32 _value) internal view {

106:     function ptrShrinkIntoActive(uint32 _shrink) internal view {

124:     function packPrecompileParams(

147:     function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {

164:     function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

177:     function eventInitialize(uint256 initializer, uint256 value1) internal {

187:     function eventWrite(uint256 value1, uint256 value2) internal {

198:     function getZkSyncMetaBytes() internal view returns (uint256 meta) {

210:     function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) {

221:     function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) {

231:     function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {

240:     function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {

248:     function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) {

257:     function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) {

266:     function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) {

272:     function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {

287:     function getCallFlags() internal view returns (uint256 callFlags) {

298:     function getCalldataPtr() internal view returns (uint256 ptr) {

309:     function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {

320:     function isSystemCall() internal view returns (bool) {

329:     function isSystemContract(address _address) internal pure returns (bool) {

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

75:     function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {

122:     function systemCallWithReturndata(

149:     function systemCallWithPropagatedRevert(

213:     function getFarCallABI(

249:     function getFarCallABIWithEmptyFatPointer(

```

```solidity
File: contracts/libraries/TransactionHelper.sol

93:     function isEthToken(uint256 _addr) internal pure returns (bool) {

99:     function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {

361:     function processPaymasterInput(Transaction calldata _transaction) internal {

394:     function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {

404:     function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

18:     function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {

25:     function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {

```

```solidity
File: contracts/libraries/Utils.sol

19:     function safeCastToU128(uint256 _x) internal pure returns (uint128) {

25:     function safeCastToU32(uint256 _x) internal pure returns (uint32) {

31:     function safeCastToU24(uint256 _x) internal pure returns (uint24) {

38:     function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {

43:     function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {

50:     function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {

57:     function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

65:     function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

76:     function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

22:     function safeTransfer(

33:     function safeTransferFrom(

52:     function safeApprove(

70:     function safeIncreaseAllowance(

86:     function safeDecreaseAllowance(

109:     function safePermit(

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

36:     function isContract(address account) internal view returns (bool) {

60:     function sendValue(address payable recipient, uint256 amount) internal {

91:      function functionCall(address target, bytes memory data)

110:     function functionCall(

129:     function functionCallWithValue(

149:     function functionCallWithValue(

177:      function functionStaticCall(address target, bytes memory data)

196:     function functionStaticCall(

217:      function functionDelegateCall(address target, bytes memory data)

235:     function functionDelegateCall(

256:     function verifyCallResultFromTarget(

280:     function verifyCallResult(

```

### [NC-16] STOP USING V != 27 && V != 28 OR V == 27 || V == 28

#### Description:

See this for reference:
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg

#### **Proof Of Concept**

```solidity
File: contracts/BootloaderUtilities.sol

91:             require(vInt == 27 || vInt == 28, "Invalid v value");

190:             require(vInt == 27 || vInt == 28, "Invalid v value");

285:             require(vInt == 27 || vInt == 28, "Invalid v value");

```

```solidity
File: contracts/DefaultAccount.sol

176:         require(v == 27 || v == 28, "v is neither 27 nor 28");

```

### [NC-17] LINES ARE TOO LONG

#### Description:

Usually lines in source code are limited to 80 characters.

[Reference](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length)

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

7: import {DEPLOYER_SYSTEM_CONTRACT, NONCE_HOLDER_SYSTEM_CONTRACT, CURRENT_MAX_PRECOMPILE_ADDRESS} from "./Constants.sol";

22:     bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

34:     function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

36:         require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");

46:     function markAccountCodeHashAsConstructed(address _address) external override onlyDeployer {

50:         require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");

52:         bytes32 constructedBytecodeHash = Utils.constructedBytecodeHash(codeHash);

63:     function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {

74:     function getCodeHash(uint256 _input) external view override returns (bytes32) {

87:         if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

102:     function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {

```

```solidity
File: contracts/BootloaderUtilities.sol

28:             txHash = keccak256(bytes.concat(signedTxHash, EfficientCall.keccak(_transaction.signature)));

43:     function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {

51:         bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

55:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

56:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

60:         bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

61:         bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

70:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

138:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

142:             bytes memory encodedChainId = RLPEncoder.encodeUint256(block.chainid);

143:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

144:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

145:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

146:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

147:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

166:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

228:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

235:             bytes memory encodedChainId = RLPEncoder.encodeUint256(block.chainid);

236:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

237:             bytes memory encodedMaxPriorityFeePerGas = RLPEncoder.encodeUint256(_transaction.maxPriorityFeePerGas);

238:             bytes memory encodedMaxFeePerGas = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

239:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

240:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

241:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

261:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

```

```solidity
File: contracts/BytecodeCompressor.sol

40:             (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);

42:             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");

46:                 "Encoded data length should be 4 times shorter than the original bytecode"

49:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

50:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

51:                 require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

53:                 uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);

54:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

56:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

62:         bytes32 rawCompressedDataHash = L1_MESSENGER_CONTRACT.sendToL1(_rawCompressedData);

77:     ) internal pure returns (bytes calldata dictionary, bytes calldata encodedData) {

```

```solidity
File: contracts/Constants.sol

32: uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));

34: address payable constant BOOTLOADER_FORMAL_ADDRESS = payable(address(SYSTEM_CONTRACTS_OFFSET + 0x01));

35: IAccountCodeStorage constant ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT = IAccountCodeStorage(

38: INonceHolder constant NONCE_HOLDER_SYSTEM_CONTRACT = INonceHolder(address(SYSTEM_CONTRACTS_OFFSET + 0x03));

39: IKnownCodesStorage constant KNOWN_CODE_STORAGE_CONTRACT = IKnownCodesStorage(address(SYSTEM_CONTRACTS_OFFSET + 0x04));

40: IImmutableSimulator constant IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT = IImmutableSimulator(

43: IContractDeployer constant DEPLOYER_SYSTEM_CONTRACT = IContractDeployer(address(SYSTEM_CONTRACTS_OFFSET + 0x06));

48: IL1Messenger constant L1_MESSENGER_CONTRACT = IL1Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

49: address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

51: IEthToken constant ETH_TOKEN_SYSTEM_CONTRACT = IEthToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

53: address constant KECCAK256_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x10);

55: ISystemContext constant SYSTEM_CONTEXT_CONTRACT = ISystemContext(payable(address(SYSTEM_CONTRACTS_OFFSET + 0x0b)));

57: BootloaderUtilities constant BOOTLOADER_UTILITIES = BootloaderUtilities(address(SYSTEM_CONTRACTS_OFFSET + 0x0c));

59: address constant EVENT_WRITER_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x0d);

61: IBytecodeCompressor constant BYTECODE_COMPRESSOR_CONTRACT = IBytecodeCompressor(

78: bytes32 constant CREATE2_PREFIX = 0x2020dba91b30cc0006188af794c2fb30dd8520db7e2c088b7fc7c103c00ca494;

81: bytes32 constant CREATE_PREFIX = 0x63bae3a9951d38e8a3fbb7b70909afc1200610fc5bc55ade242f815974674f23;

```

```solidity
File: contracts/ContractDeployer.sol

7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";

11: import {SystemContractHelper, ISystemContract} from "./libraries/SystemContractHelper.sol";

32:     function getAccountInfo(address _address) external view returns (AccountInfo memory info) {

38:     function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {

45:         if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {

53:     function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal {

60:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {

69:     function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {

75:             "It is only possible to change from sequential to arbitrary ordering"

101:             bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)

117:             bytes.concat(CREATE_PREFIX, bytes32(uint256(uint160(_sender))), bytes32(_senderNonce))

133:         return create2Account(_salt, _bytecodeHash, _input, AccountAbstractionVersion.None);

149:         return createAccount(_salt, _bytecodeHash, _input, AccountAbstractionVersion.None);

167:         address newAddress = getNewAddressCreate2(msg.sender, _bytecodeHash, _salt, _input);

169:         _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input);

187:         uint256 senderNonce = NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender);

190:         _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input);

212:     function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {

214:         _storeConstructingByteCodeHashOnAddress(_deployment.newAddress, _deployment.bytecodeHash);

223:             _constructContract(_sender, _deployment.newAddress, _deployment.input, false);

226:         emit ContractDeployed(_sender, _deployment.bytecodeHash, _deployment.newAddress);

232:     function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {

233:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");

242:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

245:             this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);

256:         require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");

260:             ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,

264:         require(NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(_newAddress) == 0x00, "Account is occupied");

266:         _performDeployOnAddress(_bytecodeHash, _newAddress, _aaVersion, _input);

295:         uint256 knownCodeMarker = KNOWN_CODE_STORAGE_CONTRACT.getMarker(_bytecodeHash);

302:     function _storeConstructingByteCodeHashOnAddress(address _newAddress, bytes32 _bytecodeHash) internal {

304:         bytes32 constructingBytecodeHash = Utils.constructingBytecodeHash(_bytecodeHash);

305:         ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash);

314:     function _constructContract(address _sender, address _newAddress, bytes calldata _input, bool _isSystem) internal {

318:             ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value);

323:         bytes memory returnData = EfficientCall.mimicCall(gasleft(), _newAddress, _input, _sender, true, _isSystem);

324:         ImmutableData[] memory immutables = abi.decode(returnData, (ImmutableData[]));

325:         IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables);

326:         ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress);

```

```solidity
File: contracts/DefaultAccount.sol

9: import {BOOTLOADER_FORMAL_ADDRESS, NONCE_HOLDER_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, INonceHolder} from "./Constants.sol";

70:     ) external payable override ignoreNonBootloader ignoreInDelegateCall returns (bytes4 magic) {

86:             abi.encodeCall(INonceHolder.incrementMinNonceIfEquals, (_transaction.nonce))

93:         bytes32 txHash = _suggestedSignedHash != bytes32(0) ? _suggestedSignedHash : _transaction.encodeHash();

95:         if (_transaction.to == uint256(uint160(address(DEPLOYER_SYSTEM_CONTRACT)))) {

96:             require(_transaction.data.length >= 4, "Invalid call to ContractDeployer");

103:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

149:             SystemContractsCaller.systemCallWithPropagatedRevert(gas, to, value, data);

162:     function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {

187:         require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

191:         return recoveredAddress == address(this) && recoveredAddress != address(0);

```

```solidity
File: contracts/ImmutableSimulator.sol

20:     mapping(uint256 => mapping(uint256 => bytes32)) internal immutableDataStorage;

26:     function getImmutable(address _dest, uint256 _index) external view override returns (bytes32) {

33:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {

34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```

```solidity
File: contracts/KnownCodesStorage.sol

8: import {BOOTLOADER_FORMAL_ADDRESS, BYTECODE_PUBLISHING_OVERHEAD, BYTECODE_COMPRESSOR_CONTRACT} from "./Constants.sol";

19:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

24:         require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");

31:     function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyBootloader {

35:                 uint256 codeLengthInBytes = Utils.bytecodeLenInBytes(_hashes[i]);

36:                 _markBytecodeAsPublished(_hashes[i], 0, codeLengthInBytes, _shouldSendToL1);

51:         _markBytecodeAsPublished(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen, false);

69:                 _sendBytecodeToL1(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen);

90:     function _sendBytecodeToL1(bytes32 _bytecodeHash, bytes32 _l1PreimageHash, uint256 _l1PreimageBytesLen) internal {

95:         uint256 pricePerPubdataByteInGas = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

97:         uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;

109:             0, // The precompile parameters are formal ones. We only need the precompile call to burn gas.

117:     function getMarker(bytes32 _hash) public view override returns (uint256 marker) {

128:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

130:         require(Utils.bytecodeLenInWords(_bytecodeHash) % 2 == 1, "Code length in words must be odd");

```

```solidity
File: contracts/L1Messenger.sol

22:     function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

27:         uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

42:         uint256 precompileParams = SystemContractHelper.packPrecompileParams(0, 0, 0, 0, 0);

49:         SystemContractHelper.toL1(true, bytes32(uint256(uint160(msg.sender))), hash);

```

```solidity
File: contracts/L2EthToken.sol

6: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, L1_MESSENGER_CONTRACT} from "./Constants.sol";

29:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

40:     function transferFromTo(address _from, address _to, uint256 _amount) external override {

64:     function balanceOf(uint256 _account) external view override returns (uint256) {

72:     function mint(address _account, uint256 _amount) external override onlyBootloader {

97:     function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

98:         return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);

```

```solidity
File: contracts/MsgValueSimulator.sol

6: import {SystemContractHelper, ISystemContract} from "./libraries/SystemContractHelper.sol";

7: import {MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT, ETH_TOKEN_SYSTEM_CONTRACT, MAX_MSG_VALUE} from "./Constants.sol";

22:     function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

32:     fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {

37:                 abi.encodeCall(ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo, (msg.sender, to, value))

62:         return EfficientCall.mimicCall(gasleft(), to, _data, msg.sender, false, isSystemCall);

```

```solidity
File: contracts/NonceHolder.sol

64:     function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {

65:         require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

81:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

82:         IContractDeployer.AccountInfo memory accountInfo = DEPLOYER_SYSTEM_CONTRACT.getAccountInfo(msg.sender);

87:         if (accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0) {

88:             require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");

109:     function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {

124:     function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {

134:     function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {

146:     function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {

148:         return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0);

158:     function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view {

171:     function _splitRawNonce(uint256 _rawMinNonce) internal pure returns (uint256 deploymentNonce, uint256 minNonce) {

```

```solidity
File: contracts/SystemContext.sol

73:     function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {

83:     function getBlockNumberAndTimestamp() public view returns (uint256 blockNumber, uint256 blockTimestamp) {

115:         (uint256 currentBlockNumber, uint256 currentBlockTimestamp) = getBlockNumberAndTimestamp();

116:         require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");

117:         require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

122:         currentBlockInfo = (currentBlockNumber + 1) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;

127:         SystemContractHelper.toL1(false, bytes32(_newTimestamp), _prevBlockHash);

132:     function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader {

133:         currentBlockInfo = (number) * BLOCK_INFO_BLOCK_NUMBER_PART + _newTimestamp;

```

```solidity
File: contracts/interfaces/IAccount.sol

7: bytes4 constant ACCOUNT_VALIDATION_SUCCESS_MAGIC = IAccount.validateTransaction.selector;

34:     function executeTransactionFromOutside(Transaction calldata _transaction) external payable;

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

6:     function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external;

10:     function getRawCodeHash(address _address) external view returns (bytes32 codeHash);

12:     function getCodeHash(uint256 _input) external view returns (bytes32 codeHash);

14:     function getCodeSize(uint256 _input) external view returns (uint256 codeSize);

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

39:     event AccountNonceOrderingUpdated(address indexed accountAddress, AccountNonceOrdering nonceOrdering);

41:     event AccountVersionUpdated(address indexed accountAddress, AccountAbstractionVersion aaVersion);

50:     function getNewAddressCreate(address _sender, uint256 _senderNonce) external pure returns (address newAddress);

84:     function getAccountInfo(address _address) external view returns (AccountInfo memory info);

```

```solidity
File: contracts/interfaces/IEthToken.sol

8:     function transferFromTo(address _from, address _to, uint256 _amount) external;

26:     event Withdrawal(address indexed _l2Sender, address indexed _l1Receiver, uint256 _amount);

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

11:     function getImmutable(address _dest, uint256 _index) external view returns (bytes32);

13:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external;

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

6:     event MarkedAsKnown(bytes32 indexed bytecodeHash, bool indexed sendBytecodeToL1);

8:     function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external;

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

8:     event L1MessageSent(address indexed _sender, bytes32 indexed _hash, bytes _message);

```

```solidity
File: contracts/interfaces/INonceHolder.sol

14:     event ValueSetUnderNonce(address indexed accountAddress, uint256 indexed key, uint256 value);

37:     function getDeploymentNonce(address _address) external view returns (uint256);

40:     function incrementDeploymentNonce(address _address) external returns (uint256);

43:     function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view;

46:     function isNonceUsed(address _address, uint256 _nonce) external view returns (bool);

```

```solidity
File: contracts/interfaces/IPaymaster.sol

12: bytes4 constant PAYMASTER_VALIDATION_SUCCESS_MAGIC = IPaymaster.validateAndPayForPaymasterTransaction.selector;

```

```solidity
File: contracts/interfaces/IPaymasterFlow.sol

15:     function approvalBased(address _token, uint256 _minAllowance, bytes calldata _innerInput) external;

```

```solidity
File: contracts/interfaces/ISystemContext.sol

29:     function getBlockNumberAndTimestamp() external view returns (uint256 blockNumber, uint256 blockTimestamp);

```

```solidity
File: contracts/libraries/EfficientCall.sol

7: import {SHA256_SYSTEM_CONTRACT, KECCAK256_SYSTEM_CONTRACT} from "../Constants.sol";

36:         bytes memory returnData = staticCall(gasleft(), KECCAK256_SYSTEM_CONTRACT, _data);

45:         bytes memory returnData = staticCall(gasleft(), SHA256_SYSTEM_CONTRACT, _data);

110:         bool success = rawMimicCall(_gas, _address, _data, _whoToMimic, _isConstructor, _isSystem);

144:                 success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)

154:     function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success) {

168:     function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) {

210:     function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) {

262:             uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset));

267:         uint256 farCallAbi = SystemContractsCaller.getFarCallABIWithEmptyFatPointer(

```

```solidity
File: contracts/libraries/RLPEncoder.sol

6:     function encodeAddress(address _val) internal pure returns (bytes memory encoded) {

13:             mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)

19:     function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {

24:                 encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));

44:     function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {

55:     function _encodeLength(uint64 _len, uint256 _offset) private pure returns (bytes memory encoded) {

79:     function _highestByteSet(uint256 _number) private pure returns (uint256 hbs) {

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

5: import {MAX_SYSTEM_CONTRACT_ADDRESS, MSG_VALUE_SYSTEM_CONTRACT} from "../Constants.sol";

55:             let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)

147:     function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {

157:             success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)

164:     function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

210:     function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) {

221:     function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) {

222:         gasPerPubdataByte = uint32(extractNumberFromMeta(meta, META_GAS_PER_PUBDATA_BYTE_OFFSET, 32));

231:     function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {

232:         heapSize = uint32(extractNumberFromMeta(meta, META_HEAP_SIZE_OFFSET, 32));

240:     function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {

241:         auxHeapSize = uint32(extractNumberFromMeta(meta, META_AUX_HEAP_SIZE_OFFSET, 32));

248:     function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) {

257:     function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) {

258:         callerShardId = uint8(extractNumberFromMeta(meta, META_CALLER_SHARD_ID_OFFSET, 8));

266:     function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) {

267:         codeShardId = uint8(extractNumberFromMeta(meta, META_CODE_SHARD_ID_OFFSET, 8));

309:     function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {

341:             SystemContractHelper.isSystemCall() || SystemContractHelper.isSystemContract(msg.sender),

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

5: import {MSG_VALUE_SYSTEM_CONTRACT, MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT} from "../Constants.sol";

19: address constant SYSTEM_MIMIC_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 8);

32: address constant LOAD_CALLDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 21);

33: address constant LOAD_LATEST_RETURNDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 22);

75:     function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {

109:                 success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)

156:         (success, returnData) = systemCallWithReturndata(gasLimit, to, value, data);

```

```solidity
File: contracts/libraries/TransactionHelper.sol

10: import {ETH_TOKEN_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS} from "../Constants.sol";

81:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

85:             "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"

94:         return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;

99:     function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {

117:     function _encodeHashEIP712Transaction(Transaction calldata _transaction) private view returns (bytes32) {

138:             abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)

141:         return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

146:     function _encodeHashLegacyTransaction(Transaction calldata _transaction) private view returns (bytes32) {

154:         bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

158:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

159:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

163:         bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

164:         bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

173:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

184:             encodedChainId = bytes.concat(RLPEncoder.encodeUint256(block.chainid), hex"80_80");

218:     function _encodeHashEIP2930Transaction(Transaction calldata _transaction) private view returns (bytes32) {

227:             bytes memory encodedChainId = RLPEncoder.encodeUint256(block.chainid);

228:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

229:             bytes memory encodedGasPrice = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

230:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

231:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

232:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

251:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

288:     function _encodeHashEIP1559Transaction(Transaction calldata _transaction) private view returns (bytes32) {

297:             bytes memory encodedChainId = RLPEncoder.encodeUint256(block.chainid);

298:             bytes memory encodedNonce = RLPEncoder.encodeUint256(_transaction.nonce);

299:             bytes memory encodedMaxPriorityFeePerGas = RLPEncoder.encodeUint256(_transaction.maxPriorityFeePerGas);

300:             bytes memory encodedMaxFeePerGas = RLPEncoder.encodeUint256(_transaction.maxFeePerGas);

301:             bytes memory encodedGasLimit = RLPEncoder.encodeUint256(_transaction.gasLimit);

302:             bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));

303:             bytes memory encodedValue = RLPEncoder.encodeUint256(_transaction.value);

323:                 encodedDataLength = RLPEncoder.encodeNonSingleBytesLen(txDataLen);

361:     function processPaymasterInput(Transaction calldata _transaction) internal {

362:         require(_transaction.paymasterInput.length >= 4, "The standard paymaster input must be at least 4 bytes long");

364:         bytes4 paymasterInputSelector = bytes4(_transaction.paymasterInput[0:4]);

368:                 "The approvalBased paymaster input must be at least 68 bytes long"

373:             (address token, uint256 minAllowance) = abi.decode(_transaction.paymasterInput[4:68], (address, uint256));

376:             uint256 currentAllowance = IERC20(token).allowance(address(this), paymaster);

394:     function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {

404:     function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {

410:             requiredBalance = _transaction.maxFeePerGas * _transaction.gasLimit + _transaction.value;

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

18:     function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {

25:     function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {

```

```solidity
File: contracts/libraries/Utils.sol

38:     function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {

39:         codeLength = bytecodeLenInWords(_bytecodeHash) << 5; // _bytecodeHash * 32

43:     function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {

45:             codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

50:     function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {

57:     function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

59:         return constructedBytecodeHash(_bytecodeHash) | SET_IS_CONSTRUCTOR_MARKER_BIT_MASK;

65:     function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

76:     function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {

81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

82:         require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

22:     event Approval(address indexed owner, address indexed spender, uint256 value);

50:     function allowance(address owner, address spender) external view returns (uint256);

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

41:             abi.encodeWithSelector(token.transferFrom.selector, from, to, value)

```

### [NC-18] USE UNDERSCORES FOR NUMBER LITERALS

#### Description:

Consider using underscores for number literals to improve its readability.

#### **Proof Of Concept**

```solidity
File: contracts/Constants.sol

67: uint256 constant BYTECODE_PUBLISHING_OVERHEAD = 100;

```

```solidity
File: contracts/SystemContext.sol

40:     uint256 public difficulty = 2500000000000000;

```

## Low Issues

|      | Issue                                                                                           |
| ---- | :---------------------------------------------------------------------------------------------- |
| L-1  | ARBITRARY JUMP WITH FUNCTION TYPE VARIABLE                                                      |
| L-2  | DOS WITH BLOCK GAS LIMIT                                                                        |
| L-3  | AVOID `TRANSFER()` AS REENTRANCY MITIGATIONS                                                    |
| L-4  | AVOID USING LOW CALL FUNCTION ECRECOVER                                                         |
| L-5  | CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE                                                  |
| L-6  | SAFEAPPROVE OF OPENZEPPELIN IS DEPRECATED                                                       |
| L-7  | `ECRECOVER` MAY RETURN EMPTY ADDRESS                                                            |
| L-8  | LACK OF INPUT VALIDATION                                                                        |
| L-9  | UNIFY RETURN CRITERIA                                                                           |
| L-10 | REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR                                                      |
| L-11 | REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS                                  |
| L-12 | IN SafeERC20.sol THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS |
| L-13 | ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS                                                     |
| L-14 | UNSAFE CAST                                                                                     |
| L-15 | UNSPECIFIC COMPILER VERSION PRAGMA                                                              |
| L-16 | UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT                                         |
| L-17 | USE `SAFETRANSFER` INSTEAD OF `TRANSFER`                                                        |

### [L-1] ARBITRARY JUMP WITH FUNCTION TYPE VARIABLE

#### Description:

Function types are supported in Solidity. This means that a variable of type function can be assigned to a function with a matching signature. The function can then be called from the variable just like any other function. Users should not be able to change the function variable, but in some cases this is possible.

If the smart contract uses certain assembly instructions, mstore for example, an attacker may be able to point the function variable to any other function. This may give the attacker the ability to break the functionality of the contract, and perhaps even drain the contract funds.

Since inline assembly is a way to access the EVM at a low level, it bypasses many important safety features. So it's important to only use assembly if it is necessary and properly understood.

[Source](https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/arbitrary-jump-function-type.md)

[SWC Registry](https://swcregistry.io/docs/SWC-127)

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

39:         assembly {
                    sstore(addressAsKey, _hash)
        }

55:         assembly {
                    sstore(addressAsKey, constructedBytecodeHash)
        }

66:         assembly {
                    codeHash := sload(addressAsKey)
        }

```

```solidity
File: contracts/DefaultAccount.sol

171:         assembly {
                r := mload(add(_signature, 0x20))
                s := mload(add(_signature, 0x40))
                v := and(mload(add(_signature, 0x41)), 0xff)
        }

```

```solidity
File: contracts/KnownCodesStorage.sol

73:             assembly {
                        sstore(_bytecodeHash, 1)
            }

118:         assembly {
                    marker := sload(_hash)
        }

```

```solidity
File: contracts/libraries/EfficientCall.sol

130:             assembly {
                        success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }

143:             assembly {
                        success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }

158:         assembly {
                    success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }

172:         assembly {
                success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)
        }

198:         assembly {
                // Clearing values before usage in assembly, since Solidity
                // doesn't do it by default
                _whoToMimic := and(_whoToMimic, cleanupMask)

                success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)
        }

```

```solidity
File: contracts/libraries/RLPEncoder.sol

11:         assembly {
                // In the first byte we write the encoded length as 0x80 + 0x14 == 0x94.
                mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
                // Write address data without stripping zeros.
                mstore(add(encoded, 0x21), shiftedVal)
        }

34:                 assembly {
                           mstore(add(encoded, 0x21), shiftedVal)
                }

69:                 assembly {
                            mstore(add(encoded, 0x21), shiftedVal)
                }

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

50:         assembly {
                // Ensuring that the type is bool
                _isService := and(_isService, 1)
                // This `success` is always 0, but the method always succeeds
                // (except for the cases when there is not enough gas)
                let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)
        }

65:         assembly {
                    addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }

76:         assembly {
                  pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))
        }

87:         assembly {
                    pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))
        }

97:         assembly {
                // Clearing input params as they are not cleaned by Solidity by default
                _value := and(_value, cleanupMask)
                pop(staticcall(_value, callAddr, 0, 0xFFFF, 0, 0))
        }

109:         assembly {
                // Clearing input params as they are not cleaned by Solidity by default
                _shrink := and(_shrink, cleanupMask)
                pop(staticcall(_shrink, callAddr, 0, 0xFFFF, 0, 0))
        }

154:         assembly {
                // Clearing input params as they are not cleaned by Solidity by default
                _gasToBurn := and(_gasToBurn, cleanupMask)
                success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)
        }

167:         assembly {
                // Clearing input params as they are not cleaned by Solidity by default
                _value := and(_value, cleanupMask)
                success := call(0, callAddr, _value, 0, 0xFFFF, 0, 0)
        }

179:         assembly {
                    pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))
        }

189:         assembly {
                    pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))
        }

200:         assembly {
                    meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }

289:         assembly {
                    callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }

300:         assembly {
                   ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)
        }

313:         assembly {
                    extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)
        }

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

99:             assembly {
                        success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)
            }

108:             assembly {
                        success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
            }

159:             assembly {
                        let size := mload(returnData)
                        revert(add(returnData, 0x20), size)
            }

```

```solidity
File: contracts/libraries/TransactionHelper.sol

398:         assembly {
                   success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)
        }

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

19:         assembly {
                let offset := sub(_bytes.offset, 30)
                result := calldataload(add(offset, _start))
        }

26:         assembly {
                let offset := sub(_bytes.offset, 24)
                result := calldataload(add(offset, _start))
        }

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

300:             assembly {
                        let returndata_size := mload(returndata)
                        revert(add(32, returndata), returndata_size)
            }

```

### [L-2] DOS WITH BLOCK GAS LIMIT

#### Description:

Programming patterns such as looping over arrays of unknown size may lead to DoS when the gas cost of execution exceeds the block gas limit.

https://swcregistry.io/docs/SWC-128

This loop could drain all user gas and revert;

#### **Proof Of Concept**

```solidity
File: contracts/ContractDeployer.sol

239:     for (uint256 i = 0; i < deploymentsLength; ++i) {

244:     for (uint256 i = 0; i < deploymentsLength; ++i) {

```

```solidity
File: contracts/ImmutableSimulator.sol

37:      for (uint256 i = 0; i < immutablesLength; ++i) {

```

```solidity
File: contracts/KnownCodesStorage.sol

34:     for (uint256 i = 0; i < hashesLen; ++i) {

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

37:     for (uint256 i = 0; i < immutablesLength; ++i) {

```

### [L-3] AVOID `TRANSFER()` AS REENTRANCY MITIGATIONS

#### Description:

Although `transfer()` has been recommended as a security best-practice to prevent reentrancy attacks because they only forward 2300 gas, the gas repricing of opcodes may break deployed contracts. Use `call()` instead, without hardcoded gas limits along with checks-effects-interactions pattern or reentrancy guards for reentrancy protection.

https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

https://swcregistry.io/docs/SWC-134

#### **Proof Of Concept**

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

41:     function transfer(address to, uint256 amount) external returns (bool);

```

#### Recommended Mitigation Steps:

Using low-level `call.value(amount)` with the corresponding result check or using the OpenZeppelin `Address.sendValue` is advised:https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L60

### [L-4] AVOID USING LOW CALL FUNCTION ECRECOVER

#### Description:

Use OZ library ECDSA that its battle tested to avoid classic errors.

https://docs.openzeppelin.com/contracts/4.x/api/utils#ECDSA

#### **Proof Of Concept**

```solidity
File: contracts/DefaultAccount.sol

189:         address recoveredAddress = ecrecover(_hash, v, r, s);

```

### [L-5] CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

#### Description:

The critical procedures should be two step process.

#### **Proof Of Concept**

```solidity
File: contracts/ImmutableSimulator.sol

33:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {

```

```solidity
File: contracts/NonceHolder.sol

81:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

```

```solidity
File: contracts/SystemContext.sol

60:     function setTxOrigin(address _newOrigin) external onlyBootloader {

66:     function setGasPrice(uint256 _gasPrice) external onlyBootloader {

109:     function setNewBlock(

```

#### Recommended Mitigation Steps:

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-6] SAFEAPPROVE OF OPENZEPPELIN IS DEPRECATED

#### Description:

You use safeApprove of openZeppelin although it’s deprecated. (see [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/566a774222707e424896c0c390a84dc3c13bdcb2/contracts/token/ERC20/utils/SafeERC20.sol#L38%3E)).

You should change it to increase/decrease Allowance as OpenZeppilin says.

#### **Proof Of Concept**

```solidity
File: contracts/libraries/TransactionHelper.sol

381:                 IERC20(token).safeApprove(paymaster, 0);

382:                 IERC20(token).safeApprove(paymaster, minAllowance);

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

52:     function safeApprove(

```

### [L-7] `ECRECOVER` MAY RETURN EMPTY ADDRESS

#### Description:

There is a common issue that ecrecover returns empty (0x0) address when the signature is invalid. function `_verifySigner` should check that before returning the result of ecrecover.

#### **Proof Of Concept**

```solidity
File: contracts/DefaultAccount.sol

189:         address recoveredAddress = ecrecover(_hash, v, r, s);

```

#### Recommended Mitigation Steps:

See the solution here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.4.0/contracts/cryptography/ECDSA.sol#L68

### [L-8] LACK OF INPUT VALIDATION

#### Description:

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.
https://code4rena.com/reports/2022-11-redactedcartel#l-11-lack-of-input-validation

#### **Proof Of Concept**

```solidity
File: contracts/libraries/TransactionHelper.sol

396:         uint256 amount = _transaction.maxFeePerGas * _transaction.gasLimit;

```

### [L-9] UNIFY RETURN CRITERIA

In contracts, sometimes the name of the return variable is not defined and sometimes is, unifying the way of writing the code makes the code more uniform and readable.

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

63:     function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {

74:     function getCodeHash(uint256 _input) external view override returns (bytes32) {

102:     function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {

```

```solidity
File: contracts/BootloaderUtilities.sol

25:     ) external view override returns (bytes32 txHash, bytes32 signedTxHash) {

43:     function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {

138:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

228:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

```

```solidity
File: contracts/BytecodeCompressor.sol

38:     ) external payable returns (bytes32 bytecodeHash) {

77:     ) internal pure returns (bytes calldata dictionary, bytes calldata encodedData) {

```

```solidity
File: contracts/ContractDeployer.sol

32:     function getAccountInfo(address _address) external view returns (AccountInfo memory info) {

38:     function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {

95:     ) public view override returns (address newAddress) {

113:     ) public pure override returns (address newAddress) {

132:     ) external payable override returns (address) {

148:     ) external payable override returns (address) {

165:     ) public payable override onlySystemCall returns (address) {

186:     ) public payable override onlySystemCall returns (address) {

```

```solidity
File: contracts/DefaultAccount.sol

70:     ) external payable override ignoreNonBootloader ignoreInDelegateCall returns (bytes4 magic) {

80:     ) internal returns (bytes4 magic) {

162:     function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {

```

```solidity
File: contracts/ImmutableSimulator.sol

26:     function getImmutable(address _dest, uint256 _index) external view override returns (bytes32) {

```

```solidity
File: contracts/KnownCodesStorage.sol

117:     function getMarker(bytes32 _hash) public view override returns (uint256 marker) {

```

```solidity
File: contracts/L1Messenger.sol

22:     function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

```

```solidity
File: contracts/L2EthToken.sol

64:     function balanceOf(uint256 _account) external view override returns (uint256) {

97:     function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

103:     function name() external pure override returns (string memory) {

109:     function symbol() external pure override returns (string memory) {

115:     function decimals() external pure override returns (uint8) {

```

```solidity
File: contracts/MsgValueSimulator.sol

22:     function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

32:     fallback(bytes calldata _data) external payable onlySystemCall returns (bytes memory) {

```

```solidity
File: contracts/NonceHolder.sol

45:     function getMinNonce(address _address) public view returns (uint256) {

56:     function getRawNonce(address _address) public view returns (uint256) {

64:     function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {

101:     function getValueUnderNonce(uint256 _key) public view returns (uint256) {

124:     function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {

134:     function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {

146:     function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {

171:     function _splitRawNonce(uint256 _rawMinNonce) internal pure returns (uint256 deploymentNonce, uint256 minNonce) {

```

```solidity
File: contracts/SystemContext.sol

73:     function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {

83:     function getBlockNumberAndTimestamp() public view returns (uint256 blockNumber, uint256 blockTimestamp) {

91:     function getBlockNumber() public view returns (uint256 blockNumber) {

97:     function getBlockTimestamp() public view returns (uint256 timestamp) {

```

```solidity
File: contracts/interfaces/IAccount.sol

24:     ) external payable returns (bytes4 magic);

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

10:     function getRawCodeHash(address _address) external view returns (bytes32 codeHash);

12:     function getCodeHash(uint256 _input) external view returns (bytes32 codeHash);

14:     function getCodeSize(uint256 _input) external view returns (uint256 codeSize);

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

10:     ) external view returns (bytes32 txHash, bytes32 signedTxHash);

```

```solidity
File: contracts/interfaces/IBytecodeCompressor.sol

9:     ) external payable returns (bytes32 bytecodeHash);

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

48:     ) external view returns (address newAddress);

50:     function getNewAddressCreate(address _sender, uint256 _senderNonce) external pure returns (address newAddress);

56:     ) external payable returns (address newAddress);

63:     ) external payable returns (address newAddress);

72:     ) external payable returns (address newAddress);

81:     ) external payable returns (address newAddress);

84:     function getAccountInfo(address _address) external view returns (AccountInfo memory info);

```

```solidity
File: contracts/interfaces/IEthToken.sol

6:     function balanceOf(uint256) external view returns (uint256);

10:     function totalSupply() external view returns (uint256);

12:     function name() external pure returns (string memory);

14:     function symbol() external pure returns (string memory);

16:     function decimals() external pure returns (uint8);

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

11:     function getImmutable(address _dest, uint256 _index) external view returns (bytes32);

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

16:     function getMarker(bytes32 _hash) external view returns (uint256);

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

10:     function sendToL1(bytes memory _message) external returns (bytes32);

```

```solidity
File: contracts/interfaces/IL2StandardToken.sol

14:     function l1Address() external view returns (address);

16:     function l2Bridge() external view returns (address);

```

```solidity
File: contracts/interfaces/INonceHolder.sol

17:     function getMinNonce(address _address) external view returns (uint256);

21:     function getRawNonce(address _address) external view returns (uint256);

24:     function increaseMinNonce(uint256 _value) external returns (uint256);

30:     function getValueUnderNonce(uint256 _key) external view returns (uint256);

37:     function getDeploymentNonce(address _address) external view returns (uint256);

40:     function incrementDeploymentNonce(address _address) external returns (uint256);

46:     function isNonceUsed(address _address, uint256 _nonce) external view returns (bool);

```

```solidity
File: contracts/interfaces/IPaymaster.sol

32:     ) external payable returns (bytes4 magic, bytes memory context);

```

```solidity
File: contracts/interfaces/ISystemContext.sol

11:     function chainId() external view returns (uint256);

13:     function origin() external view returns (address);

15:     function gasPrice() external view returns (uint256);

17:     function blockGasLimit() external view returns (uint256);

19:     function coinbase() external view returns (address);

21:     function difficulty() external view returns (uint256);

23:     function baseFee() external view returns (uint256);

25:     function blockHash(uint256 _block) external view returns (bytes32);

27:     function getBlockHashEVM(uint256 _block) external view returns (bytes32);

29:     function getBlockNumberAndTimestamp() external view returns (uint256 blockNumber, uint256 blockTimestamp);

33:     function getBlockNumber() external view returns (uint256);

35:     function getBlockTimestamp() external view returns (uint256);

```

```solidity
File: contracts/libraries/EfficientCall.sol

35:     function keccak(bytes calldata _data) internal view returns (bytes32) {

44:     function sha(bytes calldata _data) internal view returns (bytes32) {

61:     ) internal returns (bytes memory returnData) {

75:     ) internal view returns (bytes memory returnData) {

89:     ) internal returns (bytes memory returnData) {

109:     ) internal returns (bytes memory returnData) {

125:     ) internal returns (bool success) {

154:     function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success) {

168:     function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) {

193:     ) internal returns (bool success) {

210:     function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) {

```

```solidity
File: contracts/libraries/RLPEncoder.sol

6:     function encodeAddress(address _val) internal pure returns (bytes memory encoded) {

19:     function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) {

44:     function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) {

51:     function encodeListLen(uint64 _len) internal pure returns (bytes memory) {

55:     function _encodeLength(uint64 _len, uint256 _offset) private pure returns (bytes memory encoded) {

79:     function _highestByteSet(uint256 _number) private pure returns (uint256 hbs) {

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

63:     function getCodeAddress() internal view returns (address addr) {

130:     ) internal pure returns (uint256 rawParams) {

147:     function precompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) {

164:     function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

198:     function getZkSyncMetaBytes() internal view returns (uint256 meta) {

210:     function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) {

221:     function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) {

231:     function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) {

240:     function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) {

248:     function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) {

257:     function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) {

266:     function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) {

272:     function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {

287:     function getCallFlags() internal view returns (uint256 callFlags) {

298:     function getCalldataPtr() internal view returns (uint256 ptr) {

309:     function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) {

320:     function isSystemCall() internal view returns (bool) {

329:     function isSystemContract(address _address) internal pure returns (bool) {

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

75:     function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) {

127:     ) internal returns (bool success, bytes memory returnData) {

154:     ) internal returns (bytes memory returnData) {

223:     ) internal pure returns (uint256 farCallAbi) {

255:     ) internal pure returns (uint256 farCallAbiWithEmptyFatPtr) {

```

```solidity
File: contracts/libraries/TransactionHelper.sol

93:     function isEthToken(uint256 _addr) internal pure returns (bool) {

99:     function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) {

117:     function _encodeHashEIP712Transaction(Transaction calldata _transaction) private view returns (bytes32) {

146:     function _encodeHashLegacyTransaction(Transaction calldata _transaction) private view returns (bytes32) {

218:     function _encodeHashEIP2930Transaction(Transaction calldata _transaction) private view returns (bytes32) {

288:     function _encodeHashEIP1559Transaction(Transaction calldata _transaction) private view returns (bytes32) {

394:     function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) {

404:     function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

18:     function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {

25:     function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {

```

```solidity
File: contracts/libraries/Utils.sol

19:     function safeCastToU128(uint256 _x) internal pure returns (uint128) {

25:     function safeCastToU32(uint256 _x) internal pure returns (uint32) {

31:     function safeCastToU24(uint256 _x) internal pure returns (uint24) {

38:     function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {

43:     function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {

50:     function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {

57:     function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

65:     function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) {

76:     function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

27:     function totalSupply() external view returns (uint256);

32:     function balanceOf(address account) external view returns (uint256);

41:     function transfer(address to, uint256 amount) external returns (bool);

50:     function allowance(address owner, address spender) external view returns (uint256);

66:     function approve(address spender, uint256 amount) external returns (bool);

81:     ) external returns (bool);

```

```solidity
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

53:     function nonces(address owner) external view returns (uint256);

59:     function DOMAIN_SEPARATOR() external view returns (bytes32);

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

36:     function isContract(address account) internal view returns (bool) {

93:         returns (bytes memory)

114:     ) internal returns (bytes memory) {

133:     ) internal returns (bytes memory) {

154:     ) internal returns (bytes memory) {

180:         returns (bytes memory)

200:     ) internal view returns (bytes memory) {

219:         returns (bytes memory)

239:     ) internal returns (bytes memory) {

261:     ) internal view returns (bytes memory) {

284:     ) internal pure returns (bytes memory) {

```

#### Recommended Mitigation Steps:

add `{return x}` if you want to return the updated value or else remove `returns(uint)` from the `function(){}` if no value you wanted to return

### [L-10] REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory.

#### **Proof Of Concept**

```solidity
File: contracts/NonceHolder.sol

135:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");

```

```solidity
File: contracts/SystemContext.sol

16:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS);

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

152:         require(gasleft() >= _gasToBurn);

```

```solidity
File: contracts/libraries/Utils.sol

78:         require(_bytecode.length % 32 == 0, "po");

81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

82:         require(bytecodeLenInWords % 2 == 1, "pr"); // bytecode length in words must be odd

```

### [L-11] REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

#### Description:

Error definitions should be added to the require block, not exceeding 32 bytes.

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Error definitions should be added to the require block, not exceeding 32 bytes or we should use custom errors

```solidity
File: contracts/AccountCodeStorage.sol

25:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

36:         require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");

50:         require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");

```

```solidity
File: contracts/BytecodeCompressor.sol

42:             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");

56:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

```

```solidity
File: contracts/ContractDeployer.sol

233:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER_CONTRACT");

242:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

256:         require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");

```

```solidity
File: contracts/DefaultAccount.sol

205:         require(success, "Failed to pay the fee to the operator");

```

```solidity
File: contracts/ImmutableSimulator.sol

34:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```

```solidity
File: contracts/KnownCodesStorage.sol

24:         require(msg.sender == address(BYTECODE_COMPRESSOR_CONTRACT), "Callable only by the bytecode compressor");

```

```solidity
File: contracts/NonceHolder.sol

65:         require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

```

```solidity
File: contracts/SystemContext.sol

117:         require(currentBlockNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

310:         require(index < 10, "There are only 10 accessible registers");

```

```solidity
File: contracts/libraries/TransactionHelper.sol

362:         require(_transaction.paymasterInput.length >= 4, "The standard paymaster input must be at least 4 bytes long");


```

### [L-12] IN SafeERC20.sol THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS

#### Description:

If a pair gets created and after a while one of the tokens gets self-destroyed (maybe because of a bug) then `safeTransfer` would still succeed. It’s probably a good idea to check if the contract still exists by checking the bytecode length.

#### **Proof Of Concept**

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

22:     function safeTransfer(

```

### [L-13] ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

#### **Proof Of Concept**

```solidity
File: contracts/MsgValueSimulator.sol

36:             (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(

```

```solidity
File: contracts/libraries/EfficientCall.sol

56:     function call(

131:                 success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)

144:                 success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)

159:             success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)

173:             success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)

203:             success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

55:             let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)

66:             addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

77:             pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))

88:             pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))

100:             pop(staticcall(_value, callAddr, 0, 0xFFFF, 0, 0))

112:             pop(staticcall(_shrink, callAddr, 0, 0xFFFF, 0, 0))

157:             success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)

170:             success := call(0, callAddr, _value, 0, 0xFFFF, 0, 0)

180:             pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))

190:             pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))

201:             meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

290:             callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

301:             ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

314:             extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

100:                 success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)

109:                 success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)

```

```solidity
File: contracts/libraries/TransactionHelper.sol

399:             success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

66:         (bool success, ) = recipient.call{value: amount}("");

159:         (bool success, bytes memory returndata) = target.call{value: value}(

201:         (bool success, bytes memory returndata) = target.staticcall(data);

240:         (bool success, bytes memory returndata) = target.delegatecall(data);

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-14] UNSAFE CAST

#### Description:

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

#### **Proof Of Concept**

```solidity
File: contracts/BootloaderUtilities.sol

90:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

189:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

284:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

```

```solidity
File: contracts/DefaultAccount.sol

83:             uint32(gasleft()),

```

```solidity
File: contracts/KnownCodesStorage.sol

127:         uint8 version = uint8(_bytecodeHash[0]);

```

```solidity
File: contracts/MsgValueSimulator.sol

60:         SystemContractHelper.setValueForNextFarCall(uint128(value));

```

```solidity
File: contracts/libraries/RLPEncoder.sol

24:                 encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));

29:                 encoded[0] = bytes1(uint8(hbs + 0x81));

59:                 encoded[0] = bytes1(uint8(_len + _offset));

64:                 encoded[0] = bytes1(uint8(_offset + hbs + 56));

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

222:         gasPerPubdataByte = uint32(extractNumberFromMeta(meta, META_GAS_PER_PUBDATA_BYTE_OFFSET, 32));

232:         heapSize = uint32(extractNumberFromMeta(meta, META_HEAP_SIZE_OFFSET, 32));

241:         auxHeapSize = uint32(extractNumberFromMeta(meta, META_AUX_HEAP_SIZE_OFFSET, 32));

249:         shardId = uint8(extractNumberFromMeta(meta, META_SHARD_ID_OFFSET, 8));

258:         callerShardId = uint8(extractNumberFromMeta(meta, META_CALLER_SHARD_ID_OFFSET, 8));

267:         codeShardId = uint8(extractNumberFromMeta(meta, META_CODE_SHARD_ID_OFFSET, 8));

```

```solidity
File: contracts/libraries/Utils.sol

45:             codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```

#### Recommended Mitigation Steps:

Use a safeCast from Open Zeppelin or increase the type length.

### [L-15] UNSPECIFIC COMPILER VERSION PRAGMA

#### Description:

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103

#### **Proof Of Concept**

```solidity
File: contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/Constants.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IBytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/SystemContractHelper.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```

```solidity
File: contracts/libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: contracts/openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```

#### Recommendation:

Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

### [L-16] UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT

#### Description:

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### **Proof Of Concept**

```solidity
File: contracts/DefaultAccount.sol

230:     receive() external payable {
                // If the contract is called directly, behave like an EOA
    }

```

```solidity
File: contracts/EmptyContract.sol

13:     receive() external payable {}

```

#### Recommended Mitigation Steps:

The function should call another function, otherwise it should revert

### [L-17] USE `SAFETRANSFER` INSTEAD OF `TRANSFER`

#### Description:

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransfer`/`safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

For example, Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)‘s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

#### **Proof Of Concept**

```solidity
File: contracts/openzeppelin/token/ERC20/IERC20.sol

41:     function transfer(address to, uint256 amount) external returns (bool);

```

#### Recommended Mitigation Steps:

Consider using `safeTransfer`/`safeTransferFrom` or `require()` consistently.
