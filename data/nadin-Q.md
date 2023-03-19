## Non-Critical Issues List
### Issue 
## [N-01] Redundant named returns
## [N-02] Use of bytes.concat() instead of abi.encodePacked(,)
## [N-03] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
## [N-04] Function writing that does not comply with the Solidity Style Guide
## [N-05] USE UNDERSCORES FOR NUMBER LITERALS
## [N-06] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
## [N-07] USE REQUIRE INSTEAD OF ASSERT
## [N-08] Using ecrecover is against best practice
## [N-09] Use a more recent pragma version 
## [N-10] Constants should be defined rather than using magic numbers
### Total:  10 issues

## [N-01] Redundant named returns
When a function has unused named returns and used return statements, these named returns become redundant. To improve readability and maintainability, these variables for the named returns can be removed while keeping the return statements for the functions associated with the following lines.
```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol
22:    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol
63:    function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {
102:    function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol
117:    function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
70:    ) external payable override ignoreNonBootloader ignoreInDelegateCall returns (bytes4 magic) {
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol
95:    ) public view override returns (address newAddress) {
113:    ) public pure override returns (address newAddress) {
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol
25:    ) external view override returns (bytes32 txHash, bytes32 signedTxHash) {
```

## [N-02] Use of bytes.concat() instead of abi.encodePacked(,)
Rather than using abi.encodePacked for appending bytes, since version 0.8.4, bytes.concat() is enabled
Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,).

```
File: contracts/libraries/TransactionHelper.sol
132:                 keccak256(abi.encodePacked(_transaction.factoryDeps)),
141:         return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));s
```

## [N-03] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
### Contexts: All contract
### Description:
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103
### Recommendation:
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

## [N-04] Function writing that does not comply with the Solidity Style Guide
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor

receive function (if exists)

fallback function (if exists)

external

public

internal

private

within a grouping, place the view and pure functions last

## [N-05] USE UNDERSCORES FOR NUMBER LITERALS
### Description:
There are occasions where certain numbers have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.
### Recommendation:
Consider using underscores for number literals to improve its readability.
```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol
40:    uint256 public difficulty = 2500000000000000;
```

## [N-06] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.
While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.
```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol
81:    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
82:

83:    bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
84:        keccak256(
85:            "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
86:        );
```

## [N-07] USE REQUIRE INSTEAD OF ASSERT
Assert should not be used except for tests, require should be used
Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.
assert() and reqire();
The big difference between the two is that the assert()function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.This is the most common Solidity function used by developers for debugging and error handling.
Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
225:        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/RLPEncoder.sol
45:        assert(_len != 1);
```

## [N-08] Using ecrecover is against best practice
Using ecrecover is against best practice. Preferably use ECDSA.recover instead. EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature unique. However it should be impossible to be a threat by now.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.4.0/contracts/cryptography/ECDSA.sol

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
162:    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
163:        require(_signature.length == 65, "Signature length is incorrect");
164:        uint8 v;
165:        bytes32 r;
166:        bytes32 s;
167:        // Signature loading code
168:        // we jump 32 (0x20) as the first slot of bytes contains the length
169:        // we jump 65 (0x41) per signature
170:        // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
171:        assembly {
172:            r := mload(add(_signature, 0x20))
173:            s := mload(add(_signature, 0x40))
174:            v := and(mload(add(_signature, 0x41)), 0xff)
175:        }
176:        require(v == 27 || v == 28, "v is neither 27 nor 28");
177:

178:        // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
179:        // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
180:        // the valid range for s in (301): 0 < s < secp256k1n ÷ 2 + 1, and for v in (302): v ∈ {27, 28}. Most
181:        // signatures from current libraries generate a unique signature with an s-value in the lower half order.
182:        //
183:        // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
184:        // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
185:        // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
186:        // these malleable signatures as well.
187:        require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");
188:

189:        address recoveredAddress = ecrecover(_hash, v, r, s);
190:

191:        return recoveredAddress == address(this) && recoveredAddress != address(0);
192:    }
```

## [N-09] Use a more recent pragma version 
Old version of solidity is used, consider using the new one 
https://github.com/ethereum/solidity/blob/develop/Changelog.md

## [N-10] Constants should be defined rather than using magic numbers
```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol
81:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words
```

```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BytecodeCompressor.sol
43:             require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
```


## Suggestion List
### Issue 
## [S-01] GENERATE PERFECT CODE HEADERS EVERY TIME
## [S-02] Use nested if and, avoid multiple check combinations
## [S-03] Use descriptive names for Contracts and Libraries
### Total:  03 issues

## [S-01] GENERATE PERFECT CODE HEADERS EVERY TIME
Description:
I recommend using header for Solidity code layout and readability
https://github.com/transmissions11/headers
```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

## [S-02] Use nested if and, avoid multiple check combinations
### Description:
Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
```
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol
File: https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol
```

## [S-03] Use descriptive names for Contracts and Libraries
This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_
We recommend that you implement this or a similar agreement.