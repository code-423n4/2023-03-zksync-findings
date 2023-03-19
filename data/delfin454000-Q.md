# Summary	
### Low risk findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Use `require` rather than `assert` where appropriate|2|
|2| Upgrade Open Zeppelin contract dependency|3|
|| Total|5|

### Non-critical findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Constant value definitions that include call to `keccak256` should use `immutable`|2|
|2| Avoid redundant `return` statement when `function` defines named return variable|1|
|3| Avoid extra-long single-line comments|14|
|4| Improve unclear comments|2|
|5| Typos|17|
|6| Update sensitive terms in both comments and code|20|
|7| Upgrade `pragma solidity` version to latest version before finalization|3|
|8-1| Natspec is partially missing for some `functions`|57|
|8-2| Natspec is wholly missing for some `functions`|35|
|| Total|151|

### Low risk findings

| No. | Description | 
|--| ----------- | 
|1|**Use `require` rather than `assert` where appropriate**|
|  |On failure, the `assert` function causes a `Panic` error and, unlike `require`, does not generate an error string. According to Solidity v0.8.19, "properly functioning code should never create a Panic." Therefore, an `assert` should be used only if, based on the relevant code, it is not expected to throw an exception.|

The following `assert` functions do not appear to meet the criteria and should be replaced by `require` functions:

___
[DefaultAccount.sol: L225](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L225)		
```solidity		
        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```
___
[RLPEncoder.sol: L45](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/RLPEncoder.sol#L45)
```solidity
        assert(_len != 1);
```
___

| No. | Description | 
|--| ----------- | 
|2|**Upgrade Open Zeppelin contract dependency**|
|  |Outdated Open Zeppelin versions are used (v4.4.10, v4.6.0 and v4.8.0).|

Versions previous to v4.7.3 have known vulnerabilties, including three with high severity. Version v4.8.0 also has two medium severity vulnerabilities, which have been patched by v4.8.2. See [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

___
___


### Non-critical findings


| No. | Description |
|--| ----------- | 
|1|**Constant value definitions that include call to `keccak256` should use `immutable`**|
|  |While it does not save gas to use `immutable` instead of `constant` (since the compiler recognizes and makes up for the error), it is better form to use the correct variable type. Constant variables are intended to be used for literal values written into the code, whereas immutable variables are for expressions.|

___
[TransactionHelper.sol: L81](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L81)
```solidity
    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```
___
[TransactionHelper.sol: L83-86](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L83-L86)
```solidity
    bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
        keccak256(
            "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
        );
```
___


| No. | Description |
|--| ----------- | 
|2|**Avoid redundant return statement when function defines named return variable**|
|  |Adding a return statement when a function defines a named return variable is superfluous.|

Below, function `getDeploymentNonce` defines named return variable `deploymentNonce`.  An unnecesssary `return` statement for `deploymentNonce` is given at the end of the function:

[NonceHolder.sol: L124-129](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L124-L129)
```solidity
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);

        return deploymentNonce;
    }
```
___


| No. | Description |
|--| ----------- | 
|3|**Avoid extra-long single-line comments**|
|  |In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are becoming acceptable and a scroll bar is provided, very long comments can interfere with readability.|

Most of the long comments in the contest do wrap. However, below are instances various types of extra-long comments (over 120 characters) whose readability could be improved by wrapping, as shown. Note that a small indentation is used in each continuation line (which flags for the reader that the comment is ongoing), along with a period at the close (to signal the end of the comment).

___
[EfficientCall.sol: L24](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/EfficientCall.sol#L24)
```solidity
 * 3. `ptr.pack` - Do the concatenation between the lowest 128 bits of the pointer itself and the highest 128 bits of `_value`. It is typically used to prepare the ABI for external calls.
```
Suggestion:
```solidity
 * 3. `ptr.pack` - Do the concatenation between the lowest 128 bits of the pointer itself and the highest 128 bits
 *    of `_value`. It is typically used to prepare the ABI for external calls.
```
___
[NonceHolder.sol: L23-24](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L23-L24)
```solidity
 * @dev The behavior of some of the methods depends on the nonce ordering of the account. Nonce ordering is a mere suggestion and all the checks that are present
 * here serve more as a help to users to prevent from doing mistakes, rather than any invariants.
```
Suggestion:
```solidity
 * @dev The behavior of some of the methods depends on the nonce ordering of the account. Nonce ordering is a mere
 *   suggestion and all the checks that are present here serve more as a help to users to prevent from doing mistakes,
 *   rather than any invariants.
```
___
[EfficientCall.sol: L248](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/EfficientCall.sol#L248)
```solidity
        // Currently, zkEVM considers the pointer valid if(ptr.offset < ptr.length || (ptr.length == 0 && ptr.offset == 0)), otherwise panics.
```
Suggestion:
```solidity
        // Currently, zkEVM considers the pointer valid 
        //   if(ptr.offset < ptr.length || (ptr.length == 0 && ptr.offset == 0)), otherwise panics.
```
___
[BootloaderUtilities.sol: L20-21](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BootloaderUtilities.sol#L20-L21)
```solidity
    /// @return txHash and signedTxHash of the transaction, i.e. the transaction hash to be used in the explorer and commits to all
    /// the fields of the transaction and the recommended hash to be signed for this transaction.
```
Suggestion:
```solidity
    /// @return txHash and signedTxHash of the transaction, i.e. the transaction hash to be used in the explorer and
    ///   commits to all the fields of the transaction and the recommended hash to be signed for this transaction.
```
___
Suggestion: make similar changes to the following extra-long comment lines:

[AccountCodeStorage.sol: L12-14](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L12-L14)

[AccountCodeStorage.sol: L16](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L16)

[L2EthToken.sol: L13-15](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L13-L15)

[KnownCodesStorage.sol: L13-15](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L13-L15)

[KnownCodesStorage.sol: L88-89](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L88-L89)

[SystemContractHelper.sol: L93](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L93)

[SystemContractHelper.sol: L105](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L105)

[UnsafeBytesCalldata.sol: L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/UnsafeBytesCalldata.sol#L8)

[Utils.sol: L49](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/Utils.sol#L49)

[EfficientCall.sol: L15](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/EfficientCall.sol#L15)
___


| No. | Description |
|--| ----------- | 
|4|**Improve unclear comments**|
|  |Comments should be readable. In other words, they should communicate clearly, immediately and without ambiguity. The readability of the set of comments below could be improved:|

___
[MsgValueSimulator.sol: L17-18](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L17-L18)
```solidity
    /// @notice Extract value, isSystemCall and to from the extraAbi params.
    /// @dev The contract accepts value, the callee and whether the call should a system one via its ABI params.
```
___


| No. | Description |
|--| ----------- | 
|5|**Typos**|

___
[MsgValueSimulator.sol: L49](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L49)
```solidity
            // The if above should never be true, since noone should be able to have
```
Change `noone` to `no one`
___
[SystemContext.sol: L36](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L36)
```solidity
    /// @dev For the support of coinbase, we will the bootloader formal address for now
```
Change `will the` to `will use the`
___
[SystemContext.sol: L106](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L106)
```solidity
    /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still
```
Change `Whie` to `While`
___
[DefaultAccount.sol: L64](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L64)
```solidity
    /// @dev Besides the params above, it also accepts unused first paramter "_txHash", which
```
Change `paramter` to `parameter`
___
[DefaultAccount.sol: L99](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L99)
```solidity
        // The fact there is are enough balance for the account
```
Remove unneeded word `are`
___
[DefaultAccount.sol: L161](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L161)
```solidity
    /// @return EIP1271_SUCCESS_RETURN_VALUE if the signaure is correct. It reverts otherwise.
```
Change `signaure` to `signature`
___
[SystemContractHelper.sol: L151](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L151)
```solidity
        // thats why it should be checked before the call.
```
Change `thats` to `that's`
___
The same typo (`auxilary`) occurs in all three lines below:

[SystemContractHelper.sol: L236](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L236)

[SystemContractHelper.sol: L238](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L238)

[SystemContractHelper.sol: L239](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L239)
```solidity
    /// of the auxilary heap in bytes.
```
Change `auxilary` to `auxiliary` in each case
___
[SystemContractHelper.sol: L318](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L318)
```solidity
    /// @notice Retuns whether the current call is a system call.
```
Change `Retuns` to `Returns`
___
The same typo (`since as`) occurs in both lines below:

[RLPEncoder.sol: L42](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/RLPEncoder.sol#L42)

[RLPEncoder.sol: L50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/RLPEncoder.sol#L50)
```solidity
    /// @param _len The length of the bytes to encode. It has a `uint64` type since as larger values are not supported.
```
Remove unneeded word `as` in both cases
___
[TransactionHelper.sol: L378](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L378)
```solidity
                // Some tokens, e.g. USDT require that the allowance is firsty set to zero
```
Change `firsty` to `first`
___
[ISystemContext.sol: L31](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L31)
```solidity
    // Note, that for now, the implementation of the bootloader allows this variables to
```
Change `variables` to `variable`
___
[IPaymasterFlow.sol: L9](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymasterFlow.sol#L9)
```solidity
 * @notice This is NOT an interface to be implementated
```
Change `implementated` to `implemented`
___
[IPaymaster.sol: L40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L40)
```solidity
    /// @param _maxRefundedGas, the upper bound on the amout of gas that could be refunded to the paymaster.
```
Change `amout` to `amount`
___


| No. | Description |
|--| ----------- | 
|6|**Update sensitive terms in both comments and code**|
|  |Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).|

___
[DefaultAccount.sol: L209](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L209)
```solidity
    /// paid for by a paymaster.
```
Suggestion: Replace `paymaster` with `payAdmin`
___
[DefaultAccount.sol: L215](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L215)
```solidity
    function prepareForPaymaster(
```
Suggestion: Replace `prepareForPaymaster` with `prepareForPayAdmin`
___
Similarly for the following instances of `paymaster`:

[DefaultAccount.sol: L220](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L220)

[TransactionHelper.sol: L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L8)

[TransactionHelper.sol: L43-44](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L43-L44)

[TransactionHelper.sol: L66-67](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L66-L67)

[TransactionHelper.sol: L85](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L85)

[TransactionHelper.sol: L128](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L128)

[TransactionHelper.sol: L133](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L133)

[TransactionHelper.sol: L358-368](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L358-L368)

[TransactionHelper.sol: L372-376](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L372-L376)

[TransactionHelper.sol: L381-387](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L381-L387)

[TransactionHelper.sol: L405-406](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L405-L406)

[IPaymasterFlow.sol: L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymasterFlow.sol#L8)

[IPaymasterFlow.sol: L12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymasterFlow.sol#L12)

[IPaymaster.sol: L12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L12)

[IPaymaster.sol: L14-28](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L14-L28)

[IPaymaster.sol: L37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L37)

[IPaymaster.sol: L40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L40)

[IAccount.sol: L42](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L42)
___


| No. | Description |
|--| ----------- | 
|7|**Upgrade `pragma solidity` version to latest version before finalization**|
|  |The solidity version used in almost all of the in-scope contracts is `^0.8.0`, compared to the latest version of `0.8.19`. There are upgrades that have been applied since `0.8.0`.|

For example, a version of at least 0.8.10 is required to have external calls skip contract existence checks if the external call has a return value, 0.8.12 is necessary for `string.concat` to be used instead of `abi.encodePacked`, and 0.8.13 or later is needed for the ability to use `using for` with a list of free functions. In addition, only the latest version receives security fixes. 
___


| No. | Description |
|--| ----------- | 
|8-1|**Natspec is partially missing for some `functions`**|
|  |Natspec is partially missing for some `public` or `external` `functions`, for which Natspec is recommended. Note that, while NatSpec may be informative for `internal` or `private` functions, it is not required.|

___
[L1Messenger.sol: L9-22](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L9-L22)
```solidity
/**
 * @author Matter Labs
 * @notice Smart contract for sending arbitrary length messages to L1
 * @dev by default ZkSync can send fixed length messages on L1.
 * A fixed length message has 4 parameters `senderAddress` `isService`, `key`, `value`,
 * the first one is taken from the context, the other three are chosen by the sender.
 * @dev To send a variable length message we use this trick:
 * - This system contract accepts a arbitrary length message and sends a fixed length message with
 * parameters `senderAddress == this`, `marker == true`, `key == msg.sender`, `value == keccak256(message)`.
 * - The contract on L1 accepts all sent messages and if the message came from this system contract
 * it requires that the preimage of `value` be provided.
 */
contract L1Messenger is IL1Messenger {
    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
```
Missing: `@param _message`, `@return`
___
[BytecodeCompressor.sol: L20-35](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L20-L35)
```solidity
    /// @notice Verify the compressed bytecode and publish it on the L1.
    /// @param _bytecode The original bytecode to be verified against.
    /// @param _rawCompressedData The compressed bytecode in a format of:
    ///    - 2 bytes: the length of the dictionary
    ///    - N bytes: the dictionary
    ///    - M bytes: the encoded data
    /// @dev The dictionary is a sequence of 8-byte chunks, each of them has the associated index.
    /// @dev The encoded data is a sequence of 2-byte chunks, each of them is an index of the dictionary.
    /// @dev The compression algorithm works as follows:
    ///     1. The original bytecode is split into 8-byte chunks.
    ///     Since the bytecode size is always a multiple of 32, this is always possible.
    ///     2. For each 8-byte chunk in the original bytecode:
    ///         * If the chunk is not already in the dictionary, it is added to the dictionary array.
    ///         * If the dictionary becomes overcrowded (2^16 + 1 elements), the compression process will fail.
    ///         * The 2-byte index of the chunk in the dictionary is added to the encoded data.
    function publishCompressedBytecode(
```
Missing: `@return`
___
[L2EthToken.sol: L60-65](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L60-L65)
```solidity
    /// @notice Returns ETH balance of an account
    /// @dev It takes `uint256` as an argument to be able to properly simulate the behaviour of the
    /// Ethereum's `BALANCE` opcode that accepts uint256 as an argument and truncates any upper bits
    /// @param _account The address of the account to return the balance of.
    function balanceOf(uint256 _account) external view override returns (uint256) {
        return balance[address(uint160(_account))];
```
Missing: `@return`
___
[L2EthToken.sol: L101-103](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L101-L103)
```solidity
    /// @dev This method has not been stabilized and might be
    /// removed later on.
    function name() external pure override returns (string memory) {
```
Missing: `@return`
___
[L2EthToken.sol: L107-109](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L107-L109)
```solidity
    /// @dev This method has not been stabilized and might be
    /// removed later on.
    function symbol() external pure override returns (string memory) {
```
Missing: `@return`
___
[L2EthToken.sol: L113-115](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L113-L115)
```solidity
    /// @dev This method has not been stabilized and might be
    /// removed later on.
    function decimals() external pure override returns (uint8) {
```
Missing: `@return`
___
[SystemContext.sol: L70-73](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L70-L73)
```solidity
    /// @notice The method that emulates `blockhash` opcode in EVM.
    /// @dev Just like the blockhash in the EVM, it returns bytes32(0), when
    /// when queried about hashes that are older than 256 blocks ago.
    function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {
```
Missing: `@param _block`, `@return`
___
[SystemContext.sol: L101-114](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L101-L114)
```solidity
    /// @notice Increments the current block number and sets the new timestamp
    /// @dev Called by the bootloader at the start of the block.
    /// @param _prevBlockHash The hash of the previous block.
    /// @param _newTimestamp The timestamp of the new block.
    /// @param _expectedNewNumber The new block's number
    /// @dev Whie _expectedNewNumber can be derived as prevBlockNumber + 1, we still
    /// manually supply it here for consistency checks.
    /// @dev The correctness of the _prevBlockHash and _newTimestamp should be enforced on L1.
    function setNewBlock(
        bytes32 _prevBlockHash,
        uint256 _newTimestamp,
        uint256 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyBootloader {
```
Missing: `@param _baseFee`
___
[SystemContext.sol: L130-132](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L130-L132)
```solidity
    /// @notice A testing method that manually sets the current blocks' number and timestamp.
    /// @dev Should be used only for testing / ethCalls and should never be used in production.
    function unsafeOverrideBlock(uint256 _newTimestamp, uint256 number, uint256 _baseFee) external onlyBootloader {
```
Missing: `@param _newTimestamp`, `@param number`, `@param _baseFee`
___
[KnownCodesStorage.sol: L115-117](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L115-L117)
```solidity
    /// @notice Returns the marker stored for a bytecode hash. 1 means that the bytecode hash is known
    /// and can be used for deploying contracts. 0 otherwise.
    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
```
Missing: `@param _hash`, `@return`
___
[DefaultAccount.sol: L57-70](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L57-L70)
```solidity
    /// @notice Validates the transaction & increments nonce.
    /// @dev The transaction is considered accepted by the account if
    /// the call to this function by the bootloader does not revert
    /// and the nonce has been set as used.
    /// @param _suggestedSignedHash The suggested hash of the transaction to be signed by the user.
    /// This is the hash that is signed by the EOA by default.
    /// @param _transaction The transaction structure itself.
    /// @dev Besides the params above, it also accepts unused first paramter "_txHash", which
    /// is the unique (canonical) hash of the transaction.
    function validateTransaction(
        bytes32, // _txHash
        bytes32 _suggestedSignedHash,
        Transaction calldata _transaction
    ) external payable override ignoreNonBootloader ignoreInDelegateCall returns (bytes4 magic) {
```
Missing: `@return`
___
[ContractDeployer.sol: L31-32](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L31-L32)
```solidity
    /// @notice Returns information about a certain account.
    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
```
Missing: `@param _address`, `@return`
___
[ContractDeployer.sol: L36-38](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L61-L38)
```solidity
    /// @notice Returns the account abstraction version if `_address` is a deployed contract.
    /// Returns the latest supported account abstraction version if `_address` is an EOA.
    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
```
Missing: `@param _address`
___
[ContractDeployer.sol: L84-95](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L84-L95)
```solidity
    /// @notice Calculates the address of a deployed contract via create2
    /// @param _sender The account that deploys the contract.
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    /// @param _salt The create2 salt.
    /// @param _input The constructor data.
    /// @return newAddress The derived address of the account.
    function getNewAddressCreate2(
        address _sender,
        bytes32 _bytecodeHash,
        bytes32 _salt,
        bytes calldata _input
    ) public view override returns (address newAddress) {
```
Missing: `@return`
___
[ContractDeployer.sol: L123-132](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L123-L132)
```solidity
    /// @notice Deploys a contract with similar address derivation rules to the EVM's `CREATE2` opcode.
    /// @param _salt The CREATE2 salt
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    /// @param _input The constructor calldata
    /// @dev In case of a revert, the zero address should be returned.
    function create2(
        bytes32 _salt,
        bytes32 _bytecodeHash,
        bytes calldata _input
    ) external payable override returns (address) {
```
Missing: `@return`
___
[ContractDeployer.sol: L136-148](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L136-L148)
```solidity
    /// @notice Deploys a contract with similar address derivation rules to the EVM's `CREATE` opcode.
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    /// @param _input The constructor calldata
    /// @dev This method also accepts nonce as one of its parameters.
    /// It is not used anywhere and it needed simply for the consistency for the compiler
    /// @dev In case of a revert, the zero address should be returned.
    /// Note: this method may be callable only in system mode,
    /// that is checked in the `createAccount` by `onlySystemCall` modifier.
    function create(
        bytes32 _salt,
        bytes32 _bytecodeHash,
        bytes calldata _input
    ) external payable override returns (address) {
```
Missing: `@param _salt`, `@return`
___
[ContractDeployer.sol: L152-165](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L152-L165)
```solidity
    /// @notice Deploys a contract account with similar address derivation rules to the EVM's `CREATE2` opcode.
    /// @param _salt The CREATE2 salt
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    /// @param _input The constructor calldata.
    /// @param _aaVersion The account abstraction version to use.
    /// @dev In case of a revert, the zero address should be returned.
    /// Note: this method may be callable only in system mode,
    /// that is checked in the `createAccount` by `onlySystemCall` modifier.
    function create2Account(
        bytes32 _salt,
        bytes32 _bytecodeHash,
        bytes calldata _input,
        AccountAbstractionVersion _aaVersion
    ) public payable override onlySystemCall returns (address) {
```
Missing: `@return`
___
[ContractDeployer.sol: L174-186](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L174-L186)
```solidity
    /// @notice Deploys a contract account with similar address derivation rules to the EVM's `CREATE` opcode.
    /// @param _bytecodeHash The correctly formatted hash of the bytecode.
    /// @param _input The constructor calldata.
    /// @param _aaVersion The account abstraction version to use.
    /// @dev This method also accepts salt as one of its parameters.
    /// It is not used anywhere and it needed simply for the consistency for the compiler
    /// @dev In case of a revert, the zero address should be returned.
    function createAccount(
        bytes32, // salt
        bytes32 _bytecodeHash,
        bytes calldata _input,
        AccountAbstractionVersion _aaVersion
    ) public payable override onlySystemCall returns (address) {
```
Missing: `@return`
___
[ContractDeployer.sol: L229-232](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L229-L232)
```solidity
    /// @notice This method is to be used only during an upgrade to set bytecodes on specific addresses.
    /// @dev We do not require `onlySystemCall` here, since the method is accessible only
    /// by `FORCE_DEPLOYER`.
    function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {
```
Missing: `@param _deployments`
___
[IPaymasterFlow.sol: L5-13](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymasterFlow.sol#L5-L13)
```solidity
/**
 * @author Matter Labs
 * @dev The interface that is used for encoding/decoding of
 * different types of paymaster flows.
 * @notice This is NOT an interface to be implementated
 * by contracts. It is just used for encoding.
 */
interface IPaymasterFlow {
    function general(bytes calldata input) external;
```
Missing: `@param input`
___
[INonceHolder.sol: L16-17](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L16-L17)
```solidity
    /// @dev Returns the current minimal nonce for account.
    function getMinNonce(address _address) external view returns (uint256);
```
Missing: `@param _address`, `@return`
___
[INonceHolder.sol: L19-21](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L19-L21)
```solidity
    /// @dev Returns the raw version of the current minimal nonce
    /// (equal to minNonce + 2^128 * deployment nonce).
    function getRawNonce(address _address) external view returns (uint256);
```
Missing: `@param _address`, `@return`
___
[INonceHolder.sol: L23-24](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L23-L24)
```solidity
    /// @dev Increases the minimal nonce for the msg.sender.
    function increaseMinNonce(uint256 _value) external returns (uint256);
```
Missing: `@param _value`, `@return`
___
[INonceHolder.sol: L26-27](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L26-L27)
```solidity
    /// @dev Sets the nonce value `key` as used.
    function setValueUnderNonce(uint256 _key, uint256 _value) external;
```
Missing: `@param _key`, `@param _value`
___
[INonceHolder.sol: L29-30](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L29-L30)
```solidity
    /// @dev Gets the value stored inside a custom nonce.
    function getValueUnderNonce(uint256 _key) external view returns (uint256);
```
Missing: `@param _key`, `@return`
___
[INonceHolder.sol: L32-34](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L32-L34)
```solidity
    /// @dev A convenience method to increment the minimal nonce if it is equal
    /// to the `_expectedNonce`.
    function incrementMinNonceIfEquals(uint256 _expectedNonce) external;
```
Missing: `@param _expectedNonce`
___
[INonceHolder.sol: L36-37](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L36-L37)
```solidity
    /// @dev Returns the deployment nonce for the accounts used for CREATE opcode.
    function getDeploymentNonce(address _address) external view returns (uint256);
```
Missing: `@param _address`, `@return`
___
[INonceHolder.sol: L39-40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L39-L40)
```solidity
    /// @dev Increments the deployment nonce for the account and returns the previous one.
    function incrementDeploymentNonce(address _address) external returns (uint256);
```
Missing: `@param _address`, `@return`
___
[INonceHolder.sol: L42-43](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L42-L43)
```solidity
    /// @dev Determines whether a certain nonce has been already used for an account.
    function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view;
```
Missing: `@param _address`, `@param _key`, `@param _shouldBeUsed`
___
[INonceHolder.sol: L45-46](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/INonceHolder.sol#L45-L46)
```solidity
    /// @dev Returns whether a nonce has been used for an account.
    function isNonceUsed(address _address, uint256 _nonce) external view returns (bool);
```
Missing: `@param _address`, `@param _nonce`, `@return`
___
[IERC20Permit.sol: L16-44](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L16-L44)
```solidity
     * @dev Sets `value` as the allowance of `spender` over ``owner``'s tokens,
     * given ``owner``'s signed approval.
     *
     * IMPORTANT: The same issues {IERC20-approve} has related to transaction
     * ordering also apply here.
     *
     * Emits an {Approval} event.
     *
     * Requirements:
     *
     * - `spender` cannot be the zero address.
     * - `deadline` must be a timestamp in the future.
     * - `v`, `r` and `s` must be a valid `secp256k1` signature from `owner`
     * over the EIP712-formatted function arguments.
     * - the signature must use ``owner``'s current nonce (see {nonces}).
     *
     * For more information on the signature format, see the
     * https://eips.ethereum.org/EIPS/eip-2612#specification[relevant EIP
     * section].
     */
    function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external;
```
Missing: Multiple `@param' statements
___
[IERC20Permit.sol: L46-53](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L46-L53)
```solidity
    /**
     * @dev Returns the current nonce for `owner`. This value must be
     * included whenever a signature is generated for {permit}.
     *
     * Every successful call to {permit} increases ``owner``'s nonce by one. This
     * prevents a signature from being used multiple times.
     */
    function nonces(address owner) external view returns (uint256);
```
Missing: `@param owner)`, `@return`
___
[IERC20Permit.sol: L55-59](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/extensions/IERC20Permit.sol#L55-L59)
```solidity
    /**
     * @dev Returns the domain separator used in the encoding of the signature for {permit}, as defined by {EIP712}.
     */
    // solhint-disable-next-line func-name-mixedcase
    function DOMAIN_SEPARATOR() external view returns (bytes32);
```
Missing: `@return`
___
[ISystemContext.sol: L11](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L11)
```solidity
    function chainId() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L13](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L13)
```solidity
    function origin() external view returns (address);
```
Missing: `@return`
___
[ISystemContext.sol: L15](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L15)
```solidity
    function gasPrice() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L17](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L17)
```solidity
    function blockGasLimit() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L19](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L19)
```solidity
    function coinbase() external view returns (address);
```
Missing: `@return`
___
[ISystemContext.sol: L21](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L21)
```solidity
    function difficulty() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L23](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L23)
```solidity
    function baseFee() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L25](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L25)
```solidity
    function blockHash(uint256 _block) external view returns (bytes32);
```
Missing: `@param _block`, `@return`
___
[ISystemContext.sol: L27](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L27)
```solidity
    function getBlockHashEVM(uint256 _block) external view returns (bytes32);
```
Missing: `@param _block`, `@return`
___
[ISystemContext.sol: L29](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L29)
```solidity
    function getBlockNumberAndTimestamp() external view returns (uint256 blockNumber, uint256 blockTimestamp);
```
Missing: `@return`, `@return`
___
[ISystemContext.sol: L33](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L33)
```solidity
    function getBlockNumber() external view returns (uint256);
```
Missing: `@return`
___
[ISystemContext.sol: L35](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/ISystemContext.sol#L35)
```solidity
    function getBlockTimestamp() external view returns (uint256);
```
Missing: `@return`
___
[IERC20.sol: L24-27](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L24-L27)
```solidity
    /**
     * @dev Returns the amount of tokens in existence.
     */
    function totalSupply() external view returns (uint256);
```
Missing: `@return`
___
[IERC20.sol: L29-32](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L29-L32)
```solidity
    /**
     * @dev Returns the amount of tokens owned by `account`.
     */
    function balanceOf(address account) external view returns (uint256);
```
Missing: `@return`
___
[IERC20.sol: L34-41](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L34-L41)
```solidity
    /**
     * @dev Moves `amount` tokens from the caller's account to `to`.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * Emits a {Transfer} event.
     */
    function transfer(address to, uint256 amount) external returns (bool);
```
Missing: `@param to`, `@param amount`, `@return`
___
[IERC20.sol: L43-50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L43-L50)
```solidity
    /**
     * @dev Returns the remaining number of tokens that `spender` will be
     * allowed to spend on behalf of `owner` through {transferFrom}. This is
     * zero by default.
     *
     * This value changes when {approve} or {transferFrom} are called.
     */
    function allowance(address owner, address spender) external view returns (uint256);
```
Missing: `@param owner`, `@param spender`, `@return`
___
[IERC20.sol: L52-66](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L52-L66)
```solidity
    /**
     * @dev Sets `amount` as the allowance of `spender` over the caller's tokens.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * IMPORTANT: Beware that changing an allowance with this method brings the risk
     * that someone may use both the old and the new allowance by unfortunate
     * transaction ordering. One possible solution to mitigate this race
     * condition is to first reduce the spender's allowance to 0 and set the
     * desired value afterwards:
     * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
     *
     * Emits an {Approval} event.
     */
    function approve(address spender, uint256 amount) external returns (bool);
```
Missing: `@param spender`, `@param spender`, `@return`
___
[IERC20.sol: L68-81](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/IERC20.sol#L68-L81)
```solidity
    /**
     * @dev Moves `amount` tokens from `from` to `to` using the
     * allowance mechanism. `amount` is then deducted from the caller's
     * allowance.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * Emits a {Transfer} event.
     */
    function transferFrom(
        address from,
        address to,
        uint256 amount
    ) external returns (bool);
```
Missing: `@param from`, `@param to`, `@param amount`, `@return`
___
[IPaymaster.sol: L34-50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L34-L50)
```solidity
    /// @dev Called by the bootloader after the execution of the transaction. Please note that
    /// there is no guarantee that this method will be called at all. Unlike the original EIP4337,
    /// this method won't be called if the transaction execution results in out-of-gas.
    /// @param _context, the context of the execution, returned by the "validateAndPayForPaymasterTransaction" method.
    /// @param  _transaction, the users' transaction.
    /// @param _txResult, the result of the transaction execution (success or failure).
    /// @param _maxRefundedGas, the upper bound on the amout of gas that could be refunded to the paymaster.
    /// @dev The exact amount refunded depends on the gas spent by the "postOp" itself and so the developers should
    /// take that into account.
    function postTransaction(
        bytes calldata _context,
        Transaction calldata _transaction,
        bytes32 _txHash,
        bytes32 _suggestedSignedHash,
        ExecutionResult _txResult,
        uint256 _maxRefundedGas
    ) external payable;
```
Missing: `@param _txHash`, `@param _suggestedSignedHash`
___
[IContractDeployer: L65-72](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L65-72)
```solidity
    /// @dev While the `_salt` parameter is not used anywhere here,
    /// it is still needed for consistency between `create` and
    /// `create2` functions (required by the compiler).
    function create(
        bytes32 _salt,
        bytes32 _bytecodeHash,
        bytes calldata _input
    ) external payable returns (address newAddress);
```
Missing: `@param _salt`, `@param _bytecodeHash`, `@param _input`, `@return`
___
[IContractDeployer: L74-81](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L74-81)
```solidity
    /// @dev While `_salt` is never used here, we leave it here as a parameter
    /// for the consistency with the `create` function.
    function createAccount(
        bytes32 _salt,
        bytes32 _bytecodeHash,
        bytes calldata _input,
        AccountAbstractionVersion _aaVersion
    ) external payable returns (address newAddress);
```
Missing: Multiple `@param` statements, `@return`
___
[IContractDeployer: L83-84](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L83-84)
```solidity
    /// @notice Returns the information about a certain AA.
    function getAccountInfo(address _address) external view returns (AccountInfo memory info);
```
Missing: `@param _address`, `@return`
___
[IContractDeployer: L86-87](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L86-87)
```solidity
    /// @notice Can be called by an account to update its account version
    function updateAccountVersion(AccountAbstractionVersion _version) external;
```
Missing: `@param  _version`
___
[IContractDeployer: L89-90](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L89-90)
```solidity
    /// @notice Can be called by an account to update its nonce ordering
    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external;
```
Missing: `@param  _nonceOrdering`
___



| No. | Description |
|--| ----------- | 
|8-2|**Natspec is wholly missing for some `functions`**|
|  |Natspec is completely missing for the functions referenced below. Note that while many of the in-scope interface files do not have Natspec, some do. The treatment should be consistent.|


__IBootloaderUtilities.sol__

[L8-10](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IBootloaderUtilities.sol#L8-L10)

__IBytecodeCompressor.sol__

[L6-9](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IBytecodeCompressor.sol#L6-L9)

__IAccountCodeStorage.sol__

[L6](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccountCodeStorage.sol#L6), [L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccountCodeStorage.sol#L8), [L10](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccountCodeStorage.sol#L10), [L12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccountCodeStorage.sol#L12), [L14](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccountCodeStorage.sol#L14)

__IImmutableSimulator.sol__

[L11](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IImmutableSimulator.sol#L11), [L13](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IImmutableSimulator.sol#L13)

__IL2StandardToken.sol__

[L10](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IL2StandardToken.sol#L10), [L12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IL2StandardToken.sol#L12), [L16](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IL2StandardToken.sol#L16)

__IMailbox.sol__

[L6-12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IMailbox.sol#L6-L12)

__IKnownCodesStorage.sol__

[L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IKnownCodesStorage.sol#L8), [L10-14](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IKnownCodesStorage.sol#L10-L14), [L16](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IKnownCodesStorage.sol#L16)

__IEthToken.sol__

[L6](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L6), [L8](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L8), [L10](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L10), [L12](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L12)

[L14](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L14), [L16](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L16), [L18](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L18), [L20](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IEthToken.sol#L20)

__IAccount.sol__

[L26-30](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L26-L30), [L32-34](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L32-L34), [L36-40](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L36-L40), [L42-46](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L42-L46)

__IContractDeployer.sol__

[L43-48](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L43-L48), [L50](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L50), [L52-56](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L52-56), [L58-63](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IContractDeployer.sol#L58-63)

__NonceHolder.sol__

[L146](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L146)

__IL1Messenger.sol__

[L10](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IL1Messenger.sol#L10)

__IPaymasterFlow.sol__

[L15](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymasterFlow.sol#L15)
___
___
