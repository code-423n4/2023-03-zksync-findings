# L-01 Might be possible to set/have hidden immutables on re-deployed contracts

## Impact
If I'm not mistaken, one could deploy a contract A at a specific address via create2 with an arbitrary number of immutables, which would get saved by contract address in [_constructContract](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L325) and stored in [ImmutableSimulator](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/ImmutableSimulator.sol). Now let contract A selfdestruct and then re-deploy contract B under the same address with less immutables than contract A. The excess immutables from A should still be visible, because ImmutableSimulator simply [iterates the number of immutables](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L36-L41) in the deployed contract.
 
Duplicate deployment to an address [is prevented](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L259-L262), though, so this should only be able to happen when using [forceDeployOnAddress](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L212).

## Proof of Concept
Couldn't finish the PoC in time.

## Tools Used
Manual review

## Recommended Mitigation Steps
When leftover immutables are not acceptable, there should be clearance of the address in ImmutableSimulator on selfdestruct, which would probably require another SystemContract. 