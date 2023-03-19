(1) Timestamps should be incremental.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L116
Right now the block timestamp is checked like this _newTimestamp >= currentBlockTimestamp
I'd say this isn't correct, especially because even the require statement error message says that the timestamps should be incremental. Only allow the new timestamp to be bigger than the current one.
Some contracts could break or certain bad actors and attackers could try and freeze the timestamp for as long as possible.
Fix: _newTimestamp > currentBlockTimestamp

(2) Missing soft check when calling the DEPLOYER_SYSTEM_CONTRACT address.
Here there's a check for tx.length > 4 https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L95-L96

But here such check is omitted: https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L146-L149

(3) When force deploying bytecode on a existing contract, any previous immutables aren't deleted.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L33-L44
Lets say the previous contract has 4 immutables. Then a different bytecode is deployed on this address, this time there are only 3 immutables. So, the first 3 immutables are overwritten, but there's 1 left. This could lead to certain bugs, because the immutable is of the previous contract.
Fix:
When force deploying, make sure that the immutable count is the same.
You could also just delete all the previous immutables before assigning anything, or revert if the immutable count doesn't match.
