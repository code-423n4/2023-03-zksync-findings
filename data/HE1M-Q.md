### L1
In the document, it is noted that the `gasLimit` is equal to `2^32-1`, but in the `SystemContext` the value is hardcoded to `1<<30`.
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L33
https://era.zksync.io/docs/dev/developer-guides/transactions/blocks.html#block-properties