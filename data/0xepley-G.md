In contract AccountCodeStorage 

In the storeAccountConstructingCodeHash and markAccountCodeHashAsConstructed functions, you could save some gas by caching the value of uint256(uint160(_address)) instead of computing it twice.


In the getCodeHash function, you could save some gas by replacing if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) with require(uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS, EMPTY_STRING_KECCAK). This ensures that the input is a valid address before continuing, which prevents any unnecessary gas consumption.