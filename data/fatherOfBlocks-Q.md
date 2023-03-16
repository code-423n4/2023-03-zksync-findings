**all**
- L3 - Pragma float This contracts in scope are floating the pragma version.


**MsgValueSimulator.sol**
- L48 - There is dead code that will never be executed, since when it is validated (value > MAX_MSG_VALUE) it is actually validating this: (value > 2 ** 128 - 1).
The problem with this is that in L35 it is validated (value != 0), therefore regardless of whether value > MAX_MSG_value or not, it will always be executed and will depend on the first validation.
A possible solution to this is to do this validation first: "if (value > MAX_MSG_VALUE)" and then "if(value != 0)".


**L2EthToken.sol**
- L7 - The SystemContractHelper library is imported, but it is never used, therefore it should be removed.


**KnownCodesStorage.sol**
- L59 - The _markBytecodeAsPublished() function has only one if inside: getMarker(_bytecodeHash) == 0, which if it is not executed does nothing and in turn the function does not return any value.
Because of the name of the function it is very confusing that it says that it is going to mark the bytecode as published without really knowing if it did or not. Therefore, the correct thing would be to return a bool.


**NonceHolder.sol**
- L45/56/101/124 - The getter functions are all separated in the code mixed with the setters and other functionality. The most comfortable thing for reading the code would be for the getters, setters and other functionalities to be all together.

- L169/171/172 - says that _rawMinNonce is minNonce + deploymentNonce* 2**128, and to get deploymentNonce it does this operation _rawMinNonce / DEPLOY_NONCE_MULTIPLIER, but what you get with this operation is (minNonce + deploymentNonce).

- L146 - A simpler and easier to understand way to implement isNonceUsed is:
function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
        if(_nonce < getMinNonce(_address)) return true;
        return nonceValues[uint256(uint160(_address))][_nonce] > 0;
    }

- L135 - require() / revert() statements should have descriptive reason strings


**libraries/SystemContractHelper.sol*
- L5/8 - MSG_VALUE_SYSTEM_CONTRACT and Utils are imported, but they are never used, so they should be removed.


**libraries/SystemContractsCaller.sol**
- L16/17/20/25/26/29/33/37 - Multiple constants are created that are never used anywhere in the entire project, such as:
MIMIC_CALL_CALL_ADDRESS, SYSTEM_MIMIC_CALL_CALL_ADDRESS, RAW_FAR_CALL_CALL_ADDRESS, SET_PUBDATA_PRICE_CALL_ADDRESS,
INCREMENT_TX_COUNTER_CALL_ADDRESS, PTR_RETURNDATA_CALL_ADDRESS, LOAD_LATEST_RETURNDATA_INTO_ACTIVE_PTR_CALL_ADDRESS, and MULTIPLICATION_HIGH_ADDRESS.
These should be removed.


**interfaces/IL2StandardToken**
- L5 - The IL2StandardToken interface was created but it is never used, therefore it should be removed.


**interfaces/IPaymaster**
- L5 - The IPaymaster interface, the PAYMASTER_VALIDATION_SUCCESS_MAGIC constant and the ExecutionResult enum were created but they are never used, therefore they should be removed.


**Constants.sol**
- L24/55/57/59 - Multiple constants are created that are never used anywhere in the entire project, such as:
ECRECOVER_SYSTEM_CONTRACT, SYSTEM_CONTEXT_CONTRACT, BOOTLOADER_UTILITIES, EVENT_WRITER_CONTRACT
These should be removed.
