## Title

Fix typos in code/Incorrect natspec

## Impact

Use of accurate and error-free comments helps read, audit and maintain code. Otherwise, it can be misleading and miss the flagging of or cause the introduction of vulnerabilities.

## Proof of Concept

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L99

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L16

In the natspec of  `KnownCodesStorage.markBytecodeAsPublished()` it's indicated hat only trusted contracts can call this function, but only the byte code compressor contract can call this due to the `onlyBytecodeCompressor` modifier, meaning this  function is no longer accessible to other trusted contracts to mark hashes of bytecode as known.

## Tool used

Manual Review

## Recommendation

Keep only `is` and go away with `are` for the first case.
For the second case, recommend changing the statement to "This system contract accepts `an` arbitrary length message and sends a fixed length message with..."