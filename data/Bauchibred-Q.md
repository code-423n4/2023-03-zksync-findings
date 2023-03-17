Taxi driver

# L-01 Fix typos in code/Incorrect natspec

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

# L-02 Open Todo related to unused code

## Impact

Unused code can hint at programming or architectural errors.

## Proof of Concept

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L99

The `__DEPRECATED_l2Bridge` variable is being declared as 0 and is not implemented through out the code, advisable to remove unwanted and unused code to save on gas and contract size


## Tool used

Manual Review

## Recommendation

Use it or remove it.
# L-03 Unapplied modifier

## Impact

Untrusted entities can access functions they are not meant to be able to call.

## Proof of Concept

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L158

NonceHolder.validateNonceUsage is vulnerable to non vetted use, according to the natspec comment of the function it is only supposed to be used by the Bootloader, but the `onlyBootloader` modifier was not applied and neither was any other method of access control applied to ensure that no untrusted contracts or contracts that are not bootloader engage this

## Tool used

Manual Review

## Recommendation

Add an effective access control to the function if it's to be called only by the Bootloader and if otherwise, correct the natspec comment

# L-04 Code blocks that are used for testing should be removed before final production. 

## Impact

Potențial undesired behaviour if non-practical code gets passed on to production.

## Proof of Concept

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L131

The `SystemContext.unsafeOverrideBlock()` function is used to set the current block properties such as block.number and block.timestamp, it's well understood that this is used just during testing, but if forgotten and used in final production, bootloaders now have access to set block properties, which is completely non practical behaviour.


## Tool used

Manual Review

## Recommendation

Advisably, emove all code meant for testing before final production.