QA1. 
The mint() function fails to perform a zero address check for ``_account`` and a zero amount check for ``_amount``:

[https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76](https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L2EthToken.sol#L72-L76)

Mitigation: both checks should be performed.
