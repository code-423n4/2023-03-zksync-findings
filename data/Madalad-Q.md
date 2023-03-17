# Low Risk Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[L-01]|Solidity's `ecrecover` is vulnerable to signature malleability|1|
|[L-02]|Downcasting `uint` or `int` may result in overflow|4|

Total issues: 2

Total instances: 5

&nbsp;
# Non-Critical Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[N-01]|Ether may become trapped in the contract|5|
|[N-02]|Use `indexed` for event parameters|6|
|[N-03]|Use fixed compiler version|13|
|[N-04]|Use appropriate function naming convention|3|
|[N-05]|Lines too long|3|
|[N-06]|Update import usages|50|
|[N-07]|Use scientific notation/underscores for large values|1|
|[N-08]|Remove/Resolve open TODOs|1|
|[N-09]|Remove unused imports|1|

Total issues: 9

Total instances: 83

&nbsp;
# Low Risk Issues
## [L-01] Solidity's `ecrecover` is vulnerable to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121, and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57

While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

Consider using [OpenZeppelin's ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/cryptography/ECDSA.sol) (which prevents this malleability) instead of the built-in function.

Instances: 1
```solidity
File: contracts/DefaultAccount.sol

189:         address recoveredAddress = ecrecover(_hash, v, r, s);

```
- [contracts/DefaultAccount.sol#L189](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L189)

&nbsp;
## [L-02] Downcasting `uint` or `int` may result in overflow

Consider using OpenZeppelin's `SafeCast` library to prevent unexpected overflows.

Instances: 15
```solidity
File: contracts/ContractDeployer.sol

320:             SystemContractHelper.setValueForNextFarCall(uint128(value));

```
- [contracts/ContractDeployer.sol#L320](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L320)

```solidity
File: contracts/libraries/RLPEncoder.sol

29:                 encoded[0] = bytes1(uint8(hbs + 0x81));

59:                 encoded[0] = bytes1(uint8(_len + _offset));

64:                 encoded[0] = bytes1(uint8(_offset + hbs + 56));

```
- [contracts/libraries/RLPEncoder.sol#L29](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L29)
- [contracts/libraries/RLPEncoder.sol#L59](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L59)
- [contracts/libraries/RLPEncoder.sol#L64](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/RLPEncoder.sol#L64)

&nbsp;

&nbsp;

# Non-Critical Issues
## [N-01] Ether may become trapped in the contract

Consider rethinking `payable` functions, or implementing a sweep functionality.

Instances: 5
```solidity
File: contracts/BytecodeCompressor.sol

35:     function publishCompressedBytecode(

```
- [contracts/BytecodeCompressor.sol#L35](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L35)

```solidity
File: contracts/L2EthToken.sol

80:     function withdraw(address _l1Receiver) external payable override {

```
- [contracts/L2EthToken.sol#L80](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L80)

```solidity
File: contracts/ContractDeployer.sol

128:     function create2(

144:     function create(

232:     function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {

```
- [contracts/ContractDeployer.sol#L128](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L128)
- [contracts/ContractDeployer.sol#L144](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L144)
- [contracts/ContractDeployer.sol#L232](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L232)

&nbsp;
## [N-02] Use `indexed` for event parameters

Index event fields make the field more quickly accessible to off-chain tools that parse events. 

If the variable is value type (uint, address, bool), then using `indexed` saves gas. Otherwise, each index field costs extra gas during emission.

Instances: 6
```solidity
File: contracts/interfaces/IL2StandardToken.sol

6:     event BridgeMint(address indexed _account, uint256 _amount);

8:     event BridgeBurn(address indexed _account, uint256 _amount);

```
- [contracts/interfaces/IL2StandardToken.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL2StandardToken.sol#L6)
- [contracts/interfaces/IL2StandardToken.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL2StandardToken.sol#L8)

```solidity
File: contracts/interfaces/IEthToken.sol

22:     event Mint(address indexed account, uint256 amount);

24:     event Transfer(address indexed from, address indexed to, uint256 value);

26:     event Withdrawal(address indexed _l2Sender, address indexed _l1Receiver, uint256 _amount);

```
- [contracts/interfaces/IEthToken.sol#L22](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L22)
- [contracts/interfaces/IEthToken.sol#L24](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L24)
- [contracts/interfaces/IEthToken.sol#L26](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol#L26)

```solidity
File: contracts/interfaces/INonceHolder.sol

14:     event ValueSetUnderNonce(address indexed accountAddress, uint256 indexed key, uint256 value);

```
- [contracts/interfaces/INonceHolder.sol#L14](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol#L14)

&nbsp;
## [N-03] Use fixed compiler version

A floating pragma risks a different compiler version being used in production vs testing, which poses security risks.

Instances: 13
```solidity
File: contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```
- [contracts/EmptyContract.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/EmptyContract.sol#L3)

```solidity
File: contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```
- [contracts/ImmutableSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L3)

```solidity
File: contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```
- [contracts/L1Messenger.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L3)

```solidity
File: contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```
- [contracts/MsgValueSimulator.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L3)

```solidity
File: contracts/BytecodeCompressor.sol

3: pragma solidity ^0.8.0;

```
- [contracts/BytecodeCompressor.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L3)

```solidity
File: contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```
- [contracts/AccountCodeStorage.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L3)

```solidity
File: contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```
- [contracts/L2EthToken.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L3)

```solidity
File: contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```
- [contracts/SystemContext.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L3)

```solidity
File: contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```
- [contracts/KnownCodesStorage.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L3)

```solidity
File: contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```
- [contracts/NonceHolder.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L3)

```solidity
File: contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```
- [contracts/DefaultAccount.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L3)

```solidity
File: contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```
- [contracts/ContractDeployer.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L3)

```solidity
File: contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```
- [contracts/BootloaderUtilities.sol#L3](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L3)

&nbsp;
## [N-04] Use appropriate function naming convention

According to Solidity's style guide and popular convention, function names should be prefixed with an underscore if and only if they are `private` or `internal`.

See [here](https://primitivefi.notion.site/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe) for more details.


Instances: 3
```solidity
File: contracts/BootloaderUtilities.sol

43:     function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {

138:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

228:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

```
- [contracts/BootloaderUtilities.sol#L43](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L43)
- [contracts/BootloaderUtilities.sol#L138](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L138)
- [contracts/BootloaderUtilities.sol#L228](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L228)

&nbsp;
## [N-05] Lines too long

[Solidity's style guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length) states lines should be kept within 79 characters.
 
Also, if lines exceed 164 characters then a horizontal scroll bar will be required when viewing the file on Github.

Extensions such as prettier are a simple solution.

Instances: 3
```solidity
File: contracts/ContractDeployer.sol

7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, ETH_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT} from "./Constants.sol";

```
- [contracts/ContractDeployer.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L7)

```solidity
File: contracts/libraries/EfficientCall.sol

24:  * 3. `ptr.pack` - Do the concatenation between the lowest 128 bits of the pointer itself and the highest 128 bits of `_value`. It is typically used to prepare the ABI for external calls.

```
- [contracts/libraries/EfficientCall.sol#L24](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L24)

```solidity
File: contracts/libraries/TransactionHelper.sol

85:             "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"

```
- [contracts/libraries/TransactionHelper.sol#L85](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L85)

&nbsp;
## [N-06] Update import usages

To improve readability and adhere to the rule of modularity and modular programming: only import what you need. Specific imports with curly braces allow us to apply this rule better.

For example:
```
import {ERC721} from "solmate/tokens/ERC721.sol";
```

Instances: 50
```solidity
File: contracts/ImmutableSimulator.sol

5: import "./interfaces/IImmutableSimulator.sol";

```
- [contracts/ImmutableSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol#L5)

```solidity
File: contracts/L1Messenger.sol

5: import "./interfaces/IL1Messenger.sol";

6: import "./libraries/SystemContractHelper.sol";

7: import "./libraries/EfficientCall.sol";

```
- [contracts/L1Messenger.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L5)
- [contracts/L1Messenger.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L6)
- [contracts/L1Messenger.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L1Messenger.sol#L7)

```solidity
File: contracts/MsgValueSimulator.sol

5: import "./libraries/EfficientCall.sol";

```
- [contracts/MsgValueSimulator.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/MsgValueSimulator.sol#L5)

```solidity
File: contracts/BytecodeCompressor.sol

5: import "./interfaces/IBytecodeCompressor.sol";

6: import "./Constants.sol";

7: import "./libraries/Utils.sol";

8: import "./libraries/UnsafeBytesCalldata.sol";

```
- [contracts/BytecodeCompressor.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L5)
- [contracts/BytecodeCompressor.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L6)
- [contracts/BytecodeCompressor.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L7)
- [contracts/BytecodeCompressor.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BytecodeCompressor.sol#L8)

```solidity
File: contracts/AccountCodeStorage.sol

5: import "./interfaces/IAccountCodeStorage.sol";

6: import "./libraries/Utils.sol";

```
- [contracts/AccountCodeStorage.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L5)
- [contracts/AccountCodeStorage.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L6)

```solidity
File: contracts/KnownCodesStorage.sol

5: import "./interfaces/IKnownCodesStorage.sol";

6: import "./libraries/Utils.sol";

7: import "./libraries/SystemContractHelper.sol";

```
- [contracts/KnownCodesStorage.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L5)
- [contracts/KnownCodesStorage.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L6)
- [contracts/KnownCodesStorage.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/KnownCodesStorage.sol#L7)

```solidity
File: contracts/NonceHolder.sol

5: import "./interfaces/INonceHolder.sol";

6: import "./interfaces/IContractDeployer.sol";

```
- [contracts/NonceHolder.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L5)
- [contracts/NonceHolder.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L6)

```solidity
File: contracts/DefaultAccount.sol

5: import "./interfaces/IAccount.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/SystemContractHelper.sol";

8: import "./libraries/EfficientCall.sol";

```
- [contracts/DefaultAccount.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L5)
- [contracts/DefaultAccount.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L6)
- [contracts/DefaultAccount.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L7)
- [contracts/DefaultAccount.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L8)

```solidity
File: contracts/ContractDeployer.sol

6: import "./interfaces/IContractDeployer.sol";

9: import "./libraries/Utils.sol";

10: import "./libraries/EfficientCall.sol";

```
- [contracts/ContractDeployer.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L6)
- [contracts/ContractDeployer.sol#L9](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L9)
- [contracts/ContractDeployer.sol#L10](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L10)

```solidity
File: contracts/BootloaderUtilities.sol

5: import "./interfaces/IBootloaderUtilities.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/RLPEncoder.sol";

8: import "./libraries/EfficientCall.sol";

```
- [contracts/BootloaderUtilities.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L5)
- [contracts/BootloaderUtilities.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L6)
- [contracts/BootloaderUtilities.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L7)
- [contracts/BootloaderUtilities.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/BootloaderUtilities.sol#L8)

```solidity
File: contracts/libraries/Utils.sol

4: import "./EfficientCall.sol";

```
- [contracts/libraries/Utils.sol#L4](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/Utils.sol#L4)

```solidity
File: contracts/libraries/SystemContractsCaller.sol

6: import "./Utils.sol";

```
- [contracts/libraries/SystemContractsCaller.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L6)

```solidity
File: contracts/libraries/EfficientCall.sol

5: import "./SystemContractHelper.sol";

6: import "./Utils.sol";

```
- [contracts/libraries/EfficientCall.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L5)
- [contracts/libraries/EfficientCall.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/EfficientCall.sol#L6)

```solidity
File: contracts/libraries/TransactionHelper.sol

5: import "../openzeppelin/token/ERC20/IERC20.sol";

6: import "../openzeppelin/token/ERC20/utils/SafeERC20.sol";

8: import "../interfaces/IPaymasterFlow.sol";

9: import "../interfaces/IContractDeployer.sol";

11: import "./RLPEncoder.sol";

12: import "./EfficientCall.sol";

```
- [contracts/libraries/TransactionHelper.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L5)
- [contracts/libraries/TransactionHelper.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L6)
- [contracts/libraries/TransactionHelper.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L8)
- [contracts/libraries/TransactionHelper.sol#L9](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L9)
- [contracts/libraries/TransactionHelper.sol#L11](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L11)
- [contracts/libraries/TransactionHelper.sol#L12](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L12)

```solidity
File: contracts/interfaces/IBootloaderUtilities.sol

5: import "../libraries/TransactionHelper.sol";

```
- [contracts/interfaces/IBootloaderUtilities.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IBootloaderUtilities.sol#L5)

```solidity
File: contracts/interfaces/IPaymaster.sol

5: import "../libraries/TransactionHelper.sol";

```
- [contracts/interfaces/IPaymaster.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IPaymaster.sol#L5)

```solidity
File: contracts/interfaces/IAccount.sol

5: import "../libraries/TransactionHelper.sol";

```
- [contracts/interfaces/IAccount.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IAccount.sol#L5)

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
- [contracts/Constants.sol#L5](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L5)
- [contracts/Constants.sol#L6](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L6)
- [contracts/Constants.sol#L7](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L7)
- [contracts/Constants.sol#L8](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L8)
- [contracts/Constants.sol#L9](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L9)
- [contracts/Constants.sol#L10](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L10)
- [contracts/Constants.sol#L11](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L11)
- [contracts/Constants.sol#L12](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L12)
- [contracts/Constants.sol#L13](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L13)
- [contracts/Constants.sol#L14](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/Constants.sol#L14)

&nbsp;
## [N-07] Use scientific notation/underscores for large values

e.g. `1e6` or `1_000_000` instead of `1000000`.

Instances: 1
```solidity
File: contracts/SystemContext.sol

40:     uint256 public difficulty = 2500000000000000;

```
- [contracts/SystemContext.sol#L40](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/SystemContext.sol#L40)

&nbsp;
## [N-08] Remove/Resolve open TODOs

Instances: 1
```solidity
File: contracts/L2EthToken.sol

25:     // TODO: Remove this variable with the new upgrade.

```
- [contracts/L2EthToken.sol#L25](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L25)

&nbsp;
## [N-09] Remove unused imports

Instances: 1
```solidity
File: contracts/libraries/TransactionHelper.sol

9: import "../interfaces/IContractDeployer.sol";

```
- [contracts/libraries/TransactionHelper.sol#L9](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L9)

&nbsp;
