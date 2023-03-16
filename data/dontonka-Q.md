```diff
diff --git a/contracts/BytecodeCompressor.sol b/contracts/BytecodeCompressor.sol
index 467e56b..e2460fe 100644
--- a/contracts/BytecodeCompressor.sol
+++ b/contracts/BytecodeCompressor.sol
@@ -40,7 +40,7 @@ contract BytecodeCompressor is IBytecodeCompressor {
             (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);

             require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
-            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
+            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big"); //@audit (QA) should not this be < as otherwise it at 2**16 it would actually overflow 2 Bytes.
             require(
                 encodedData.length * 4 == _bytecode.length,
                 "Encoded data length should be 4 times shorter than the original bytecode"
@@ -78,8 +78,9 @@ contract BytecodeCompressor is IBytecodeCompressor {
         unchecked {
             // The dictionary length can't be more than 2^16, so it fits into 2 bytes.
             uint256 dictionaryLen = uint256(_rawCompressedData.readUint16(0));
-            dictionary = _rawCompressedData[2:2 + dictionaryLen * 8];
-            encodedData = _rawCompressedData[2 + dictionaryLen * 8:];
+            uint256 encodedDataOffset = 2 + dictionaryLen * 8; // @audit (QA) I would add this variable, code would be clearer, easier to understand and as a bonus probably cheaper
+            dictionary = _rawCompressedData[2:encodedDataOffset];
+            encodedData = _rawCompressedData[encodedDataOffset:];
         }
     }
 }
```

```diff
diff --git a/contracts/KnownCodesStorage.sol b/contracts/KnownCodesStorage.sol
index 8e2b1a1..1fd8cda 100644
--- a/contracts/KnownCodesStorage.sol
+++ b/contracts/KnownCodesStorage.sol
@@ -94,7 +94,7 @@ contract KnownCodesStorage is IKnownCodesStorage {
         uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
         uint256 pricePerPubdataByteInGas = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

-        uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;
+        uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas; //@audit (QA) why here there is an additional 100 Bytes added, while in L1Messenger.sol#L37 there is only 64 Bytes?
         _burnGas(Utils.safeCastToU32(gasToPay));

         // Send a log to L1 that bytecode should be known.
```



```diff
diff --git a/contracts/L1Messenger.sol b/contracts/L1Messenger.sol
index cab7a10..2ae454b 100644
--- a/contracts/L1Messenger.sol
+++ b/contracts/L1Messenger.sol
@@ -39,7 +39,7 @@ contract L1Messenger is IL1Messenger {
         uint256 gasToPay = pubdataLen * gasPerPubdataBytes;

         // Call precompile to burn gas to cover the cost of publishing pubdata to L1.
-        uint256 precompileParams = SystemContractHelper.packPrecompileParams(0, 0, 0, 0, 0);
+        uint256 precompileParams = SystemContractHelper.packPrecompileParams(0, 0, 0, 0, 0); //@audit (QA) this is not necessary as only for burning gas, should use the same patterns as KnownCodesStorage.sol#L109
         bool precompileCallSuccess = SystemContractHelper.precompileCall(
             precompileParams,
             Utils.safeCastToU32(gasToPay)
```

```diff
diff --git a/contracts/L2EthToken.sol b/contracts/L2EthToken.sol
index d01f5b6..7026eb3 100644
--- a/contracts/L2EthToken.sol
+++ b/contracts/L2EthToken.sol
@@ -45,6 +45,8 @@ contract L2EthToken is IEthToken {
             "Only system contracts with special access can call this method"
         );

+        //@audit (QA) would add require here to be more conservative : require(_amount != 0, "Nothing to tranfer");
+        //@audit (QA) would add require here also to ensure _from and _to are != address(0)           
+
         uint256 fromBalance = balance[_from];
         require(fromBalance >= _amount, "Transfer amount exceeds balance");
         unchecked {
@@ -75,13 +77,14 @@ contract L2EthToken is IEthToken {
         emit Mint(_account, _amount);
     }

-    /// @notice Initiate the ETH withdrawal, funds will be available to claim on L1 `finalizeEthWithdrawal` method.
+    /// @notice Initiate the ETH withdrawal, funds will be available to claim on L1 `finalizeEthWithdrawal` method. //@audit (LOW) Inacurate comment, the func is actually called finalizeWithdrawal, at least from latest version on github.
     /// @param _l1Receiver The address on L1 to receive the funds.
     function withdraw(address _l1Receiver) external payable override {
         uint256 amount = msg.value;
+        //@audit (QA) would add require here to be more conservative : require(amount != 0, "Nothing to withdraw");

         // Silent burning of the ether
-        unchecked {
+        unchecked { //@audit (QA) while in theory this is safe (compiler will have added code to call transferFromTo just before this, transfering the amount from the sendoer to the contract), I would probably remove the unchecked to be safe, otherwise if an underflow occur it would screw up the bridge, simulating a huge mint that nobody really owns. Not sure if its worth the risk VS the gas saved by being inside an unchecked.
             balance[address(this)] -= amount;
             totalSupply -= amount;
         }
```

```diff
diff --git a/contracts/ContractDeployer.sol b/contracts/ContractDeployer.sol
index 3a85a9d..a69c7b7 100644
--- a/contracts/ContractDeployer.sol
+++ b/contracts/ContractDeployer.sol
@@ -314,7 +314,7 @@ contract ContractDeployer is IContractDeployer, ISystemContract {
     function _constructContract(address _sender, address _newAddress, bytes calldata _input, bool _isSystem) internal {
         // Transfer the balance to the new address on the constructor call.
         uint256 value = msg.value;
-        if (value > 0) {
+        if (value > 0) { //@audit (LOW) since this is uint, != 0 seems more appropriate, as you do in MsgValueSimulator.sol#L35
             ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value);
             // Safe to cast value, because `msg.value` <= `uint128.max` due to `MessageValueSimulator` invariant
             SystemContractHelper.setValueForNextFarCall(uint128(value));
```