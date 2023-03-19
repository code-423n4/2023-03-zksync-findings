## [L-01] `isEthToken` should return false when `_addr == 0`

[TransactionHelper.sol#L93-L95](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L93-L95)

```solidity
    function isEthToken(uint256 _addr) internal pure returns (bool) {
        return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
    }
```

A contract using this library uses this check to verify if contract to be called is the ETH_TOKEN_SYSTEM_CONTRACT. If it passes, it proceeds to call `withdraw`. Since 0 will be an "EmptyContract", it will behave like a regular EOA and succeed even though no `withdraw` occurred. Since `withdraw` also expects a value to be passed, the ETH will be sent to address 0 and effectively be burned. This issue is likely to happen since the zero-value of uint256 is 0. 


## [L-02]  Disallow sending ETH to System Contracts

[L2EthToken.sol#L40-L58](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L40-L58)
[L2EthToken.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L72-L76)

L2EthToken is able to directly mint and directly transfer ETH to system contracts. For example, a user can bridge ETH from L1 to L2 and choose a system contract address as recipient. That action triggers the bootloader to call `L2EthToken.mint` which directly mints ETH to any account even if that address has a contract with no `payable receive` or `fallback` callback.

Since there is no value in having system contracts be recipients of ETH via transfers or mints, we could add a check to those functions to require that the recipient of ETH is not a system contract except maybe for the zero address. The zero address is typically used as the recipient of burned tokens and we want to be able to allow that same behavior in zkSync. 


## [L-03] `SystemContractHelper.getZkSyncMeta` does not include heapSize and auxHeapSize
[SystemContractHelper.sol#L272-L278](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L272-L278)

The `ZkSyncMeta` struct includes the following fields:
```solidity
struct ZkSyncMeta {
    uint32 gasPerPubdataByte;
    uint32 heapSize;
    uint32 auxHeapSize;
    uint8 shardId;
    uint8 callerShardId;
    uint8 codeShardId;
}
```

The `getZkSyncMeta` function that is supposed to retrieve ZkSyncMeta structure does not include heapSize and auxHeapSize fields as can be seen here:

```solidity
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
    }
```

The following lines can be added to the last lines of the `getZkSyncMeta` function:

```solidity
   meta.heapSize = getHeapSizeFromMeta(metaPacked);
   meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);
```


## [N-01] More detail in `L2EthToken.mint` natspec

[L2EthToken.sol#L69](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L69)

Since `L2EthToken.mint` is such a significant functionality because it can create ETH out of nothing and add it to an account's balance, some more detail on the situations when this `mint` function is only used would be helpful for any developer reading the docs. Mention in its docs that it is used only when ETH is bridged from L1 to L2 and how that process works and that `mint` is not used by bootloader anywhere else.


## [N-02] Mention `L2EthToken.transferFromTo` does not trigger callbacks in docs

[L2EthToken.sol#L33-L40](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L33-L40)

`L2EthToken.transferFromTo` allows some system contracts to do a direct ETH transfer which only updates balances and does not trigger `receive` callbacks of any contract recipient. Mention in the documentation that it is a "direct ETH transfer and does not trigger any `receive` callbacks of a contract recipient". Making that behavior explicit in the docs, saves developers/auditors time. 


## [N-03] No documentation for how ZkSyncMeta is packed
[SystemContractsCaller.sol#L41-L46](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractsCaller.sol#L41-L46)
[meta.rs#L14](https://github.com/matter-labs/era-zkevm_opcode_defs/blob/main/src/definitions/abi/meta.rs#L14)

There is no description of how the `ZKSyncMeta` struct is packed and encoded. There is a link in the README to the Rust code that shows the packing of `ZKSyncMeta`. This is not helpful when the reader can not parse Rust. Comments right above the offset constants explaining how ZkSyncMeta is packed and encoded for easy reference would help all developers/auditors wanting to verify if the functions for getting the different fields of `ZkSyncMeta` are working as intended.


## [N-04] Incomplete NatSpec Comments
NatSpec comments provide rich code documentation. The following functions are some examples that miss the @param comments.

Please consider completing the NatSpec comments for functions like these:
[TransactionHelper.encodeHash](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L97-L99)
[TransactionHelper._encodeHashEIP712Transaction](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L115-L117)
[TransactionHelper._encodeHashLegacyTransaction](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L144-L146)
[TransactionHelper._encodeHashEIP2930Transaction](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L216-L218)
[TransactionHelper._encodeHashEIP1559Transaction](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L286-L288)
[TransactionHelper.processPaymasterInput](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L358-L361)
[TransactionHelper.payToTheBootloader](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L391-L395)


## [N-05] Missing NatSpec Comments

NatSpec comments provide rich code documentation. The following functions are some examples that miss NatSpec comments. Please consider adding NatSpec comments for functions like these.

[TransactionHelper.totalRequiredBalance](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/TransactionHelper.sol#L403-L404)



## [R-01] `L2EthToken.withdraw` can accept `amount` instead of `msg.value`

[L2EthToken.sol#L80-L94](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/L2EthToken.sol#L80-L94)

```solidity
    function withdraw(address _l1Receiver) external payable override {
        uint256 amount = msg.value;

        // Silent burning of the ether
        unchecked {
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }
        ... // irrelevant code snipped
    }
```

`withdraw` needs `msg.value` to be sent by the account that wants to withdraw ETH to L1. This triggers the `MsgValueSimulator` which transfers the ETH to the `L2EthToken` contract before ultimately burning that amount of ETH from `L2EthToken`'s balance and from the `totalSupply`. The function can be refactored to:

```solidity
    function withdraw(address _l1Receiver, uint256 amount) external override {
        require(balance[msg.sender] >= amount, "not enough balance");
        // Silent burning of the ether
        unchecked {
            balance[msg.sender] -= amount;
            totalSupply -= amount;
        }
        ... // irrelevant code snipped
    }
```

The refactored version reads clearer to me since the balance that is subtracted from is the balance of the sender and not of `L2EthToken`. Also, it does not transfer ETH to L2EthToken before burning, which is unnecessary. 


## [R-02] `NonceHolder.incrementDeploymentNonce` has redundant access control check

[NonceHolder.sol#L134-L135](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L134-L135)

```solidity
    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```

The `onlySystemCall` modifier can be removed since only the `DEPLOYER_SYSTEM_CONTRACT` can call this function anyway. It is an unnecessary redundant check. It would also be good to add an error message like "only Deployer system contract can increment deployment nonce". 


## [R-03] `ContractDeployer.updateNonceOrdering` can only change to Arbitrary
[ContractDeployer.sol#L66-L82](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ContractDeployer.sol#L66-L82)


```solidity
    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
        AccountInfo memory currentInfo = _accountInfo[msg.sender];

        require(
            _nonceOrdering == AccountNonceOrdering.Arbitrary &&
                currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
            "It is only possible to change from sequential to arbitrary ordering"
        );
    ...
```

The only valid `_nonceOrdering` parameter that can be used is `AccountNonceOrdering.Arbitrary`. Since there is only ever one valid parameter that can be used, the function can be renamed to be much clearer and can be refactored to the following:

```solidity
    function setToArbitraryNonceOrdering() external onlySystemCall {
        _accountInfo[msg.sender].nonceOrdering = AccountNonceOrdering.Arbitrary;
        emit AccountNonceOrderingUpdated(msg.sender, _nonceOrdering);
    }
```

The function is now clearer in intent and much simpler.