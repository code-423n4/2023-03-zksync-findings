### Reduce nestedness and complexity
Reducing the nestedness and complexity of the code will make it easier to read and understand.
In `knownCodesStorage.sol`, in function `_markBytecodeAsPublished` we can change the body of the function to the following:

```solidity
if (getMarker(_bytecodeHash) != 0) {
    return;
}

_validateBytecode(_bytecodeHash);

if (_shouldSendToL1) {
    _sendBytecodeToL1(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen);
}

// Save as known, to not resend the log to L1
assembly {
    sstore(_bytecodeHash, 1)
}

emit MarkedAsKnown(_bytecodeHash, _shouldSendToL1);
```

### Use `revert` instead of `return`
In the `fallback` function of `MsgValueSimulator`, there is a condition that we know it should not happen but it's there for safety. However if this happens and user sends a amount more than `MAX_MSG_VALUE` the function will return and the user will not get any refund. We should use `revert` instead of `return` on line 55 or move the condition to the beginning of the function.

This is the condition:

```solidity
if (value > MAX_MSG_VALUE) {
    // The if above should never be true, since noone should be able to have
    // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
    // we will revert(0,0).
    // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
    // the EVM's one, i.e. returndata should be empty.
    assembly {
        return(0, 0)
    }
}
```


### Test function not removed from the code
In the file `SystemContext.sol`, the function `unsafeOverrideBlock` was meant only for testing purposes, but it was not removed from the code.


### Mistakes in the comments
- In `knownCodesStorage.sol` on line 14, the word `words` at the end of the line should be removed
- In `NonceHolder.sol` on line 17, the word `marked` should be replaced with `mark`
- In `SystemContext.sol` on line 36, the word `will` should be changed to `set it to`
- In `SystemContext.sol` on line 64, The comment was copied from the above function and is not correct
- In `SystemContext.sol` on line 71, the word `when` should be removed
