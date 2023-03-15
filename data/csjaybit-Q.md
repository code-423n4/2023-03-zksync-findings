solidity version not properly specified in 3rd line of contract SystemContractsCaller.sol and SystemContractHelper.sol <br>
``` pragma solidity ^0.8; ```<br>
which should actually be ``` pragma solidity ^0.8.0; ``` <br>
not specifying proper solidity version can make compiler behave abnormally