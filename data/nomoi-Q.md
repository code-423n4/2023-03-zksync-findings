### Typos

#### **File:** [DefaultAccount.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/DefaultAccount.sol#L99)
   - **Issue:** "is are" should be "is"
   - **Line:** 99

#### **File:** [NonceHolder.sol](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L17)
   - **Issue:** "marked" should be "mark"
   - **Line:** 17

### Incorrect Comments

#### **File:** [TransactionHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L19)
   - **Issue:** The comment refers to a different constant
   - **Line:** 19

#### **File:** [MsgValueSimulator.sol](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L49-L53)
   - **Issue:** The comment mentions that the function reverts, but instead it just returns
   - **Lines:** 49-53

### Inconsistent Usage of Leading Underscore for Internal Functions

#### Examples
##### **File:** [BootloaderUtilities.sol](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BootloaderUtilities.sol#L43)
   - **Issue:** The function doesn't use a leading underscore
   - **Line:** 43

##### **File:** [BytecodeCompressor.sol](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L77)
   - **Issue:** The function uses a leading underscore
   - **Line:** 77

### Improve Variable Cleanup Documentation

#### **File:** [SystemContractHelper.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol)
   - **Issue:** Comments like "// Clearing input params as they are not cleaned by Solidity by default" should provide more context and a link to the Solidity documentation on [variable cleanup](https://docs.soliditylang.org/en/v0.8.16/internals/variable_cleanup.html), similar to the comments in `UnsafeBytesCalldata.sol`.

### Confusing Function Name

#### **File:** [L2EthToken.sol](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L40)
   - **Issue:** The function `transferFromTo` simulates ETH token transfers from one address to another, and can only be invoked by some system contracts. The naming can be confusing as its behavior is quite different to ERC20's `transferFrom`, and the addition of `To` at the end doesn't really add much additional information. Consider renaming to `simulateTransfer`, `privilegedTransfer`, or something similar.

