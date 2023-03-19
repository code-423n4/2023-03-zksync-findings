

### [L-01] Hardcode the address causes no future updates
When an address is hardcoded as a constant, there is no option to update the address in the future without modifying the source code. Therefore, it is recommended to add an update option with the "onlyOwner" modifier. This way, only the contract owner can update the address if necessary, and changes to the address can be easily made without having to manually modify the source code.

``` solidity
Constants.sol
24: address constant ECRECOVER_SYSTEM_CONTRACT = address(0x01);
25: address constant SHA256_SYSTEM_CONTRACT = address(0x02);

Constants.sol
45: // A contract that is allowed to deploy any codehash
46: // on any address. To be used only during an upgrade.
47: address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);
48: IL1Messenger constant L1_MESSENGER_CONTRACT = IL1Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));
49: address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);
50: 
51: IEthToken constant ETH_TOKEN_SYSTEM_CONTRACT = IEthToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));
52: 
53: address constant KECCAK256_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x10);
54: 
55: ISystemContext constant SYSTEM_CONTEXT_CONTRACT = ISystemContext(payable(address(SYSTEM_CONTRACTS_OFFSET + 0x0b)));
56: 
57: BootloaderUtilities constant BOOTLOADER_UTILITIES = BootloaderUtilities(address(SYSTEM_CONTRACTS_OFFSET + 0x0c));
58: 
59: address constant EVENT_WRITER_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x0d);

SystemContractsCaller.sol
12: address constant TO_L1_CALL_ADDRESS = address((1 << 16) - 1);
13: address constant CODE_ADDRESS_CALL_ADDRESS = address((1 << 16) - 2);
14: address constant PRECOMPILE_CALL_ADDRESS = address((1 << 16) - 3);
15: address constant META_CALL_ADDRESS = address((1 << 16) - 4);
16: address constant MIMIC_CALL_CALL_ADDRESS = address((1 << 16) - 5);
17: address constant SYSTEM_MIMIC_CALL_CALL_ADDRESS = address((1 << 16) - 6);
18: address constant MIMIC_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 7);
19: address constant SYSTEM_MIMIC_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 8);
20: address constant RAW_FAR_CALL_CALL_ADDRESS = address((1 << 16) - 9);
21: address constant RAW_FAR_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 10);
22: address constant SYSTEM_CALL_CALL_ADDRESS = address((1 << 16) - 11);
23: address constant SYSTEM_CALL_BY_REF_CALL_ADDRESS = address((1 << 16) - 12);
24: address constant SET_CONTEXT_VALUE_CALL_ADDRESS = address((1 << 16) - 13);
25: address constant SET_PUBDATA_PRICE_CALL_ADDRESS = address((1 << 16) - 14);
26: address constant INCREMENT_TX_COUNTER_CALL_ADDRESS = address((1 << 16) - 15);
27: address constant PTR_CALLDATA_CALL_ADDRESS = address((1 << 16) - 16);
28: address constant CALLFLAGS_CALL_ADDRESS = address((1 << 16) - 17);
29: address constant PTR_RETURNDATA_CALL_ADDRESS = address((1 << 16) - 18);
30: address constant EVENT_INITIALIZE_ADDRESS = address((1 << 16) - 19);
31: address constant EVENT_WRITE_ADDRESS = address((1 << 16) - 20);
32: address constant LOAD_CALLDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 21);
33: address constant LOAD_LATEST_RETURNDATA_INTO_ACTIVE_PTR_CALL_ADDRESS = address((1 << 16) - 22);
34: address constant PTR_ADD_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 23);
35: address constant PTR_SHRINK_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 24);
36: address constant PTR_PACK_INTO_ACTIVE_CALL_ADDRESS = address((1 << 16) - 25);
37: address constant MULTIPLICATION_HIGH_ADDRESS = address((1 << 16) - 26);
38: address constant GET_EXTRA_ABI_DATA_ADDRESS = address((1 << 16) - 27);


```

## Recommended Mitigation Steps
It is important to consider the possibility of address changes in the future and make the source code flexible by adding the appropriate update option to avoid potential problems that may arise in the future.



### [L-02]  unsafe , do not use unchecked balance
unchecked produces smaller bytecode compared to regular arthimetic operations, as it doesn't contain the underflow/overflow validation.

So if you want to have a custom error message in case overflow would occur, this code costs less gas to run

``` solidity
L2EthToken.sol
84:         unchecked {
85:             balance[address(this)] -= amount;
86:             totalSupply -= amount;
87:         }
88: 
L2EthToken.sol
50:         unchecked {
51:             balance[_from] = fromBalance - _amount;
52:             // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
53:             // decrementing then incrementing.
54:             balance[_to] += _amount;
55:         }
```

## Recommended Mitigation Steps
this would be the cheapest, but still safe, option:
EG.
unchecked {
balance[_from] = fromBalance - _amount;
}



### [Q&N]Lack of Input Validation
Lack of input validation is the process of checking input data to ensure that it is valid and safe to process. Therefore, it is important for contracts to consider the lack of input validation to ensure that what is produced is secure and protected from potential security vulnerabilities. This can be achieved by ensuring that every user input is thoroughly checked and validated at every level of data processing, from user interface to database. By doing so, the application can be reliable and protected from security threats that may arise due to lack of input validation.

-The transferFromTo function does not validate whether _to is a valid address, so a transfer error may occur if _to is not valid.
-The mint function does not validate whether _account is a valid address or not, so a printing error may occur if _account is not valid.
-The withdraw function does not validate whether _l1Receiver is a valid address or not, so a withdraw error may occur if _l1Receiver is not valid.

``` solidity
L2EthToken.sol
40:     function transferFromTo(address _from, address _to, uint256 _amount) external override {
41:         require(
42:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
43:                 msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
44:                 msg.sender == BOOTLOADER_FORMAL_ADDRESS,
45:             "Only system contracts with special access can call this method"
46:         );
47: 
48:         uint256 fromBalance = balance[_from];
49:         require(fromBalance >= _amount, "Transfer amount exceeds balance");
50:         unchecked {
51:             balance[_from] = fromBalance - _amount;
52:             // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
53:             // decrementing then incrementing.
54:             balance[_to] += _amount;
55:         }
56: 
57:         emit Transfer(_from, _to, _amount);
58:     }
59: 

L2EthToken.sol
72:     function mint(address _account, uint256 _amount) external override onlyBootloader {
73:         totalSupply += _amount;
74:         balance[_account] += _amount;
75:         emit Mint(_account, _amount);
76:     }


L2EthToken.sol
80:     function withdraw(address _l1Receiver) external payable override {
81:         uint256 amount = msg.value;
82: 
83:         // Silent burning of the ether
84:         unchecked {
85:             balance[address(this)] -= amount;
86:             totalSupply -= amount;
87:         }
88: 
89:         // Send the L2 log, a user could use it as proof of the withdrawal
90:         bytes memory message = _getL1WithdrawMessage(_l1Receiver, amount);
91:         L1_MESSENGER_CONTRACT.sendToL1(message);
92: 
93:         emit Withdrawal(msg.sender, _l1Receiver, amount);
94:     }
```

``` solidity
EfficientCall.sol
209: 
210:     function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) {
211:         if (_success) {
212:             uint256 size;
213:             assembly {
214:                 size := returndatasize()
215:             }
216: 
217:             returnData = new bytes(size);   

218:             assembly {
219:                 returndatacopy(add(returnData, 0x20), 0, size)
220:             }
@>        require(returnData.length > 0, "Call returned empty data");
221:         } else {
222:             propagateRevert();
223:         }
224:     }

```


### [L-03] Bad Logical Execution
Some logical control flow code that can trigger race conditions, such as using block.chainid and require() in the encodeLegacyTransactionHash() function. Race attacks such as timestamp attacks and hash attacks can manipulate these values ​​to affect the result of the function.

``` solidity
BootloaderUtilities.sol
88:         bytes memory vEncoded;
89:         {
90:             uint256 vInt = uint256(uint8(_transaction.signature[64]));
91:             require(vInt == 27 || vInt == 28, "Invalid v value");
92: 
93:             // If the `chainId` is specified in the transaction, then the `v` value is encoded as
94:             // `35 + y + 2 * chainId == vInt + 8 + 2 * chainId`, where y - parity bit (see EIP-155).
95:             if (_transaction.reserved[0] != 0) {
96:                 vInt += 8 + block.chainid * 2;
97:             }
98: 
99:             vEncoded = RLPEncoder.encodeUint256(vInt);
100:         }
```

### [L-04] bytes.concat use abi.encodePacked or abi.encode instead to ensure that the resulting bytes are of the expected length
bytes.concat is used to concatenate data throughout the contract. Concatenating data using this method can result in unexpected results if the size of the concatenated data is not properly accounted for, which could lead to out-of-bounds memory accesses or other unintended consequences. Developers should use abi.encodePacked or abi.encode instead to ensure that the resulting bytes are of the expected length.

``` solidity
BootloaderUtilities.sol
28:   txHash = keccak256(bytes.concat(signedTxHash, EfficientCall.keccak(_transaction.signature)));
BootloaderUtilities.sol
57:         encodedGasParam = bytes.concat(encodedGasPrice, encodedGasLimit);
BootloaderUtilities.sol
118:         return
119:             keccak256(
120:                 bytes.concat(
121:                     encodedListLength,
122:                     encodedNonce,
123:                     encodedGasParam,
124:                     encodedTo,
125:                     encodedValue,
126:                     encodedDataLength,
127:                     _transaction.data,
128:                     vEncoded,
129:                     rEncoded,
130:                     sEncoded
131:                 )
132:             );
BootloaderUtilities.sol
148:             encodedFixedLengthParams = bytes.concat(
149:                 encodedChainId,
150:                 encodedNonce,
151:                 encodedGasPrice,
152:                 encodedGasLimit,
153:                 encodedTo,
154:                 encodedValue
155:             );
BootloaderUtilities.sol
209:         return
210:             keccak256(
211:                 bytes.concat(
212:                     "\x01",
213:                     encodedListLength,
214:                     encodedFixedLengthParams,
215:                     encodedDataLength,
216:                     _transaction.data,
217:                     encodedAccessListLength,
218:                     vEncoded,
219:                     rEncoded,
220:                     sEncoded
221:                 )
222:             );
BootloaderUtilities.sol
242:             encodedFixedLengthParams = bytes.concat(
243:                 encodedChainId,
244:                 encodedNonce,
245:                 encodedMaxPriorityFeePerGas,
246:                 encodedMaxFeePerGas,
247:                 encodedGasLimit,
248:                 encodedTo,
249:                 encodedValue
250:             );
BootloaderUtilities.sol
303: 
304:         return
305:             keccak256(
306:                 bytes.concat(
307:                     "\x02",
308:                     encodedListLength,
309:                     encodedFixedLengthParams,
310:                     encodedDataLength,
311:                     _transaction.data,
312:                     encodedAccessListLength,
313:                     vEncoded,
314:                     rEncoded,
315:                     sEncoded
316:                 )
317:             );
TransactionHelper.sol
160:             encodedGasParam = bytes.concat(encodedGasPrice, encodedGasLimit);
ContractDeployer.sol
100:         bytes32 hash = keccak256(
101:             bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)
102:         );
ContractDeployer.sol
116:         bytes32 hash = keccak256(
117:             bytes.concat(CREATE_PREFIX, bytes32(uint256(uint160(_sender))), bytes32(_senderNonce))
118:         );
TransactionHelper.sol
160:   encodedGasParam = bytes.concat(encodedGasPrice, encodedGasLimit);
161:  
TransactionHelper.sol
184:        encodedChainId = bytes.concat(RLPEncoder.encodeUint256(block.chainid), hex"80_80");
TransactionHelper.sol
201:         return
202:             keccak256(
203:                 bytes.concat(
204:                     encodedListLength,
205:                     encodedNonce,
206:                     encodedGasParam,
207:                     encodedTo,
208:                     encodedValue,
209:                     encodedDataLength,
210:                     _transaction.data,
211:                     encodedChainId
212:                 )
213:             );
TransactionHelper.sol
233:             encodedFixedLengthParams = bytes.concat(
234:                 encodedChainId,
235:                 encodedNonce,
236:                 encodedGasPrice,
237:                 encodedGasLimit,
238:                 encodedTo,
239:                 encodedValue
240:             );
TransactionHelper.sol
273:         return
274:             keccak256(
275:                 bytes.concat(
276:                     "\x01",
277:                     encodedListLength,
278:                     encodedFixedLengthParams,
279:                     encodedDataLength,
280:                     _transaction.data,
281:                     encodedAccessListLength
282:                 )
283:             );
TransactionHelper.sol
304:             encodedFixedLengthParams = bytes.concat(
305:                 encodedChainId,
306:                 encodedNonce,
307:                 encodedMaxPriorityFeePerGas,
308:                 encodedMaxFeePerGas,
309:                 encodedGasLimit,
310:                 encodedTo,
311:                 encodedValue
312:             );
TransactionHelper.sol
345:         return
346:             keccak256(
347:                 bytes.concat(
348:                     "\x02",
349:                     encodedListLength,
350:                     encodedFixedLengthParams,
351:                     encodedDataLength,
352:                     _transaction.data,
353:                     encodedAccessListLength
354:                 )
355:             );
```

