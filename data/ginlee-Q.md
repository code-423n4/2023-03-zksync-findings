[N1] use solidity latest version of named params for mapping to increase readability
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/ImmutableSimulator.sol

mapping(uint256 => mapping(uint256 => bytes32)) internal immutableDataStorage;

[N2] only import what you need, specific imports with curly braces
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol

import "./interfaces/IL1Messenger.sol";
import "./libraries/SystemContractHelper.sol";
import "./libraries/EfficientCall.sol";

[L1] Division before multiplication incurs unnecessary precision loss
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L1Messenger.sol
 unchecked {
            pubdataLen = ((_message.length + 31) / 32) * 32 + 64;
        }

[L2] Use selfbalance() instead of address(this).balance
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/DefaultAccount.sol

equire(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");