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