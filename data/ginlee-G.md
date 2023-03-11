[G1]With assembly, .call (bool success) transfer can be done gas-optimized

“(bool success, bytes memory returnData) = target.call()" automatically copies the return data to memory even if you omit the returnData variable, if a relayer executes transactions with such calls it can lead to a gas-griefing attack.

The proper way to handle this is to do a low-level Yul "call" instead, with "out" and "outsize" argument values are zero. It looks like "success := call(gas, target, value, add(calldata, 0x20), mload(calldata), 0, 0)" where the last 2 args are "out" & "outsize" and are both 0

https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol

if (value != 0) {
            (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
                abi.encodeCall(ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo, (msg.sender, to, value))
            );

[G2]Use nested if and, avoid multiple check combinations
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol

if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {
            codeHash = EMPTY_STRING_KECCAK;

if (
            uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
            codeHash != 0x00 &&
            !Utils.isContractConstructing(codeHash)
        ) {
            codeSize = Utils.bytecodeLenInBytes(codeHash);
        }


        }

[G3] use x = x + y instead of x += y
https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/L2EthToken.sol

 unchecked {
            balance[_from] = fromBalance - _amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            balance[_to] += _amount;
        }

unchecked {
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }