# L1 BlockGasLimit value is wrong .
As mentioned in the Zksync docs , `blockGasLimit` is 2 **32 -1 . But in the code below , it is a different value . 
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L33

Recommended Steps :
Change `blockGasLimit` to 2**32 -1 

# L2 Timestamp is not incremental 
When new block is set , the old block timestamp can be equal to the new block timestamp.

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L116

Recommendation:
Remove the `=` operator.

```solidity
-        require(_newTimestamp >= currentBlockTimestamp, "Timestamps should be incremental");
+        require(_newTimestamp > currentBlockTimestamp, "Timestamps should be incremental");

```