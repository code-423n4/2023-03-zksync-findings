https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/NonceHolder.sol#L135

```require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");```
exhaustive check of authority, because incrementDeploymentNonce() has onlySystemCall modifier
```
modifier onlySystemCall() {
        require(
            SystemContractHelper.isSystemCall() || SystemContractHelper.isSystemContract(msg.sender),
            "This method require system call flag"
        );
        _;
    }
```
DeployerSystemContract passes the second check in modifier, so there is no need in it

#### Proposal

remove onlySystemCall modifier, and use single style guide as in https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/AccountCodeStorage.sol#L24

