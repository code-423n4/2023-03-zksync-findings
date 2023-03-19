## QA-1

As per EIP-712, the encoded EIP-712 hash of a struct must include every member field, but this implementation of the hash does not include `transaction.signature`:
https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L118

## QA-2

In `NonceHolder.sol`, the `getRawNonce` getter could be used in more positions. I.e, here: https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L68.

This could cost more gas and it also leads to the duplication of a line, so there is a tradeoff.

## QA-3

`Ecrecover.yul` returns an empty bytes array upon failure, instead of the 0 address like the original Ethereum `ecrecover`. The return call occurs at:
https://github.com/code-423n4/2023-03-zksync/blob/01379615bc20c2926d81c0a182f1abb6e922b93a/contracts/precompiles/Ecrecover.yul#L90