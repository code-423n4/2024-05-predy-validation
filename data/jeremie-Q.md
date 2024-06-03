Title:
Missing event

Impact:
Events for critical state changes (e.g. owner and other critical parameters) should be emitted for tracking this off-chain. (see here)

Code:
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L300-L304
https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/gamma/GammaTradeMarket.sol#L388-L390

Recommendation:
Emit an event !