### Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
libraries/TransactionHelper.sol::381 => IERC20(token).safeApprove(paymaster, 0);
libraries/TransactionHelper.sol::382 => IERC20(token).safeApprove(paymaster, minAllowance);
openzeppelin/token/ERC20/utils/SafeERC20.sol::52 => function safeApprove(
../contracts/libraries/TransactionHelper.sol::381 => IERC20(token).safeApprove(paymaster, 0);
../contracts/libraries/TransactionHelper.sol::382 => IERC20(token).safeApprove(paymaster, minAllowance);
../contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol::52 => function safeApprove(
../era-compiler-tester/tests/solidity/complex/defi/Mooniswap/MooniswapPool/ERC20/SafeERC20.sol::37 => function safeApprove(IERC20 token, address spender, uint256 value) internal {
../era-compiler-tester/tests/solidity/complex/defi/UniswapV2Router01/libraries/TransferHelper.sol::7 => function safeApprove(
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)