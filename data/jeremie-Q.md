Title:
Missing event

Impact:
Events for critical state changes (e.g. owner and other critical parameters) and token transfer should be emitted for tracking this off-chain.

Code:
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L300-L304
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L329-L331
https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/spot/SpotMarket.sol#L76-L78

Recommendation:
Emit an event !