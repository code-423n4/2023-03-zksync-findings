# 1: FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGE

Vulnerability details

## Context:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces to allow us to apply this rule better.

## Proof of Concept

#### Contracts 

> ***contracts/ImmutableSimulator.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ImmutableSimulator.sol#L5

> ***contracts/L1Messenger.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L5-L7

> ***contracts/MsgValueSimulator.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/MsgValueSimulator.sol#L5

> ***contracts/BytecodeCompressor.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L5-L8

> ***contracts/AccountCodeStorage.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/AccountCodeStorage.sol#L5-L6

> ***contracts/KnownCodesStorage.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L5-L7

> ***contracts/NonceHolder.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/NonceHolder.sol#L5-L6

> ***contracts/DefaultAccount.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L5-L8

> ***contracts/ContractDeployer.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L6

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L9-L10

> ***contracts/BootloaderUtilities.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BootloaderUtilities.sol#L5-L8

#### Abstracts 

> ***contracts/libraries/SystemContractHelper.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractHelper.sol#L7-L8

#### Libraries

> ***contracts/libraries/Utils.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/Utils.sol#L4

> ***contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L6-L8

> ***contracts/libraries/SystemContractsCaller.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/SystemContractsCaller.sol#L6

> ***contracts/libraries/EfficientCall.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/EfficientCall.sol#L5-L6

> ***contracts/libraries/TransactionHelper.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L5-L6

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L8-L9

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L11-L12

#### Interfaces 

> ***contracts/interfaces/IBootloaderUtilities.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IBootloaderUtilities.sol#L5

> ***contracts/interfaces/IPaymaster.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IPaymaster.sol#L5


> ***contracts/interfaces/IAccount.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/interfaces/IAccount.sol#L5

#### Constants

> ***contracts/Constants.sol***
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/Constants.sol#L5-L14

## Tools Used

Manual Analysis

## Recommended Mitigation Steps

import {contract1 , contract2} from "filename.sol";

#### Example:

import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";

# 2: FLOATING PRAGMA

Vulnerability details

## Context:

All the contracts in scope are floating the pragma version.


## Proof of Concept

All contracts in scope

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case of contracts in a library or a package.

# 3: ADD A LIMIT FOR THE MAXIMUM NUMBER OF CHARACTERS PER LINE

Vulnerability details

## Context:

The solidity documentation recommends a maximum of 120 characters.

For reference, see https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length

## Proof of Concept

> ***2 Files, 2 Instances*** 

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/ContractDeployer.sol#L7

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/libraries/TransactionHelper.sol#L85

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Consider adding a limit of 120 characters or less to prevent large lines.

# 4: USE UNDERSCORES FOR NUMBER LITERALS

Vulnerability details

## Impact:

There are occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

## Proof of Concept

> ***File: contracts/SystemContext.sol*** 

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/SystemContext.sol#L40

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Consider using underscores for number literals to improve their readability.

# 5: GENERATE PERFECT CODE HEADERS EVERY TIME

Vulnerability details

## Impact:

Generate perfect code headers every time

For reference, see https://github.com/transmissions11/headers

## Proof of Concept

/*//////////////////////////////////////////////////////////////
                            TESTING 123
//////////////////////////////////////////////////////////////*/

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

We recommend using headers for Solidity code layout and readability

# 6: USE CONSTANTS FOR NUMBERS

Vulnerability details

## Context:

In several locations in the code, numbers like 0x00, 0x20, 0x40, and 0x41 are used. The same goes for values like type(uint256).max It is pretty easy to make a mistake somewhere, also when comparing values.

## Tools Used

Manual Analysis

## Recommended Mitigation Steps

We recommend defining constants for the numbers used throughout the code.

# 7: FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE

Vulnerability details

## Context:

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

For reference, see https://docs.soliditylang.org/en/v0.8.17/style-guide.html

## Proof of Concept

> ***Functions should be grouped according to their visibility and ordered:***

-constructor
-receive function (if exists)
-fallback function (if exists)
-external
-public
-internal
-private
-within a grouping, place the view and pure functions last

### Tools Used

Manual Analysis.

# 8: IMPORTS CAN BE GROUPED TOGETHER

Vulnerability details

## Context:

Imports can be grouped together.

## Proof of Concept

> ***File: L2EthToken.sol*** 

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L5-L8

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Consider importing OZ first, then all interfaces, then all utils.

# 9: MOVE REQUIRE/VALIDATION STATEMENTS TO THE TOP OF THE FUNCTION WHEN VALIDATING INPUT PARAMETERS

Vulnerability details

## Context:

Move require/validation statements to the top of the function when validating input parameters.

## Proof of Concept

> ***File: L1Messenger.sol*** 

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L47

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L23


## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Consider moving the validation on L47 above the conditional on L23 for sendToL1().

#### The below-listed contracts have the same issue.

> ***contracts/L2EthToken.sol*** 

> ***contracts/BytecodeCompressor.sol*** 

> ***contracts/AccountCodeStorage.sol*** 

> ***contracts/SystemContext.sol*** 

> ***contracts/KnownCodesStorage.sol*** 

> ***contracts/NonceHolder.sol*** 

> ***contracts/DefaultAccount.sol*** 

> ***contracts/ContractDeployer.sol*** 

> ***contracts/BootloaderUtilities.sol*** 

> ***libraries/SystemContractHelper.sol*** 

> ***libraries/Utils.sol*** 

> ***utils/SafeERC20.sol*** 

> ***libraries/EfficientCall.sol*** 

> ***utils/Address.sol*** 

> ***libraries/TransactionHelper.sol*** 
