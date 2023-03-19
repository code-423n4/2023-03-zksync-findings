## LOW-1: State variable visibility not set

### Code

```
uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;
uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;
uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;
```

### Affected file

* NonceHolder.sol (Line: 27, 30)
* SystemContext.sol (Line: 48)

## LOW-2: Floating Pragma

### Code

```
pragma solidity ^0.8.0;
```

### Affected file

* BootloaderUtilities.sol Line(3)
* ContractDeployer.sol Line(3)
* DefaultAccount.sol Line(3)
* KnownCodesStorage.sol Line(3)
* NonceHolder.sol Line(3)
* SystemContext.sol Line(3)
