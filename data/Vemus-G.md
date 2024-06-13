### Gas Optimization by Removing Redundant Require Check

#### Impact
Both `withdrawProtocolRevenue` and `withdrawCreatorRevenue` functions contain redundant checks with the condition `if (amount > 0)` after the `require(amount > 0, "AZ");` statement. Since the `require` statement ensures that `amount` is greater than zero, the subsequent `if` condition is unnecessary.


- `withdrawProtocolRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L182-L188)
- `withdrawCreatorRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L204-L210)



#### Recommended Mitigation Steps
Remove the redundant `if (amount > 0)` check to reduce gas usage and simplify the code.
