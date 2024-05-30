AN ARRAY’S LENGTH SHOULD BE CACHED TO SAVE GAS IN FOR-LOOPS
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Here, I suggest storing the array’s length in a variable before the for-loop.

```
        for (uint256 i = 0; i < items.length; i++) {
            if (items[i] == item) {
                index = i;
                break;
            }
        }
```

Link to the issue: https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23

