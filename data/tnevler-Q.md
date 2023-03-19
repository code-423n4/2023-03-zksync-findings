# Report
## Low Risk ##
### [L-1]: Unsafe casting may overflow
**Context:**

1. ```address account = address(uint160(_input));``` [L77](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L77) 
1. ```address account = address(uint160(_input));``` [L105](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L105) 

**Description:**
While Solidity 0.8.x checks for overflows on arithmetic operations, it does not do so for casting.

**Recommendation:**
Use OpenZeppelin’s SafeCast library to prevent unexpected overflows.

## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return deploymentNonce;``` [L128](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L128) 
1. ```return _accountInfo[_address];``` [L33](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ContractDeployer.sol#L33) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Use of immutable instead of constant keccak expression
**Context:**

1. ```bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;``` [L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L22) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#constant-and-immutable-state-variables) for a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. It is recommended to use immutable instead. 

### [N-3]: Follow Solidity standard naming conventions
**Context:**

1. ```mapping(uint256 => mapping(uint256 => bytes32)) internal immutableDataStorage;``` [L20](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol#L20) (immutableDataStorage should start with _)
1. ```bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;``` [L22](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L22) (EMPTY_STRING_KECCAK should start with _)
1. ```uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;``` [L27](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L27) (DEPLOY_NONCE_MULTIPLIER should start with _)
1. ```uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;``` [L30](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L30) (MAXIMAL_MIN_NONCE_INCREMENT should star with _)
1. ```mapping(uint256 => uint256) internal rawNonces;``` [L35](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L35) (rawNonces should start with _)
1. ```mapping(uint256 => mapping(uint256 => uint256)) internal nonceValues;``` [L40](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/NonceHolder.sol#L40) (nonceValues should start with _)
1. ```function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {``` [L43](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L43) (encodeLegacyTransactionHash should start with _)
1. ```function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {``` [L138](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L138) (encodeEIP2930TransactionHash should start with _)
1. ```function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {``` [L228](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/BootloaderUtilities.sol#L228) (encodeEIP1559TransactionHash should start with _)
1. ```bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");``` [L81](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/TransactionHelper.sol#L81) (EIP712_DOMAIN_TYPEHASH should start with _)

**Description:**

The above codes don't follow [Solidity's standard naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).  
- Internal and private functions should use mixedCase format starting with an underscore.
- Structs should be named using the CapWords style. Examples: MyCoin, Position, PositionXY.
- Events should be named using the CapWords style. Examples: Deposit, Transfer, Approval, BeforeTransfer, AfterTransfer.
- Functions should use mixedCase. Examples: getBalance, transfer, verifyOwner, addMember, changeOwner.
- Function arguments should use mixedCase. Examples: initialSupply, account, recipientAddress, senderAddress, newOwner.
- Local and State Variable should use mixedCase. Examples: totalSupply, remainingSupply, balancesOf, creatorAddress, isPreSale, tokenExchangeRate.
- Constants should be named with all capital letters with underscores separating words. Examples: MAX_BLOCKS, TOKEN_NAME, TOKEN_TICKER, CONTRACT_VERSION.
- Modifier should use mixedCase. Examples: onlyBy, onlyAfter, onlyDuringThePreSale.
- Enums, in the style of simple type declarations, should be named using the CapWords style. Examples: TokenGroup, Frame, HashStyle, CharacterLocation.

### [N-4]: Wrong order of functions
**Context:**

1. ```receive() external payable {}``` [L13](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/EmptyContract.sol#L13) (receive() function can not go after fallback() function)
1. ```function getCodeHash(uint256 _input) external view override returns (bytes32) {``` [L74](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L74) (external function can not go after public function)
1. ```mapping(address => uint256) balance;``` [L19](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol#L19) (balance should start with _)
1. ```function getMarker(bytes32 _hash) public view override returns (uint256 marker) {``` [L117](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/KnownCodesStorage.sol#L117) (public function can not go after internal function)
1. ```function executeTransaction(``` [L117](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol#L117) (external function can not go after internal function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.
