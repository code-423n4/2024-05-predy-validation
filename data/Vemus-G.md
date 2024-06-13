### Gas Optimization by Removing Redundant Require Check

#### Impact
Both `withdrawProtocolRevenue` and `withdrawCreatorRevenue` functions contain redundant checks with the condition `if (amount > 0)` after the `require(amount > 0, "AZ");` statement. Since the `require` statement ensures that `amount` is greater than zero, the subsequent `if` condition is unnecessary.


- `withdrawProtocolRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L182-L188)
- `withdrawCreatorRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L204-L210)



#### Recommended Mitigation Steps
Remove the redundant `if (amount > 0)` check to reduce gas usage and simplify the code.

### Event Emission Order

#### Impact
In both the `withdrawProtocolRevenue` and `withdrawCreatorRevenue` functions, events (`ProtocolRevenueWithdrawn` and `CreatorRevenueWithdrawn` respectively) are emitted after the token transfer is executed. Changing the order so that the events are emitted before the transfer does not directly involve security risks but can offer informational benefits.

- `withdrawProtocolRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L186-L190)
- `withdrawCreatorRevenue`: [View Code on GitHub](https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L208-L212)

Informational Benefits:

* Better Event Logging: Emitting the event before executing the transfer ensures that the event is logged even if the token transfer fails. This can help track attempts to withdraw funds.

* Transparency and Debugging: In case of a transfer failure, having the event logged can aid in debugging and analyzing issues.

#### Recommended Mitigation Steps
Emit the events before transferring tokens. Implementing this change can improve transparency and facilitate better tracking of contract operations, even though it is not directly related to security risks.
