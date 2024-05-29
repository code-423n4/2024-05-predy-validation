# Low-1 Block timestamps have been found in the following contracts, consider block number instead.

# ./src/libraries/ApplyInterestLib.sol
# Lines 32 & 43 & 54 & 67

# ./src/libraries/UniHelper.sol
# Line 57

# ./src/libraries/logic/AddPairLogic.sol
# Line 185

# ./src/libraries/orders/DecayLib.sol
# Line 13

# ./src/libraries/orders/ResolvedOrder.sol
# Line 28

# ./src/markets/gamma/GammaTradeMarket.sol
# Lines 195 & 248

# ./src/markets/gamma/GammaTradeMarketLib.sol
# Lines 66 & 72 & 111 & 114

# ./src/settlements/UniswapSettlement.sol
# Lines 34 & 50 

# Impact
ApplyInterestLib.sol (Lines 32, 43, 54, 67): Interest calculations based on block.timestamp can be manipulated by miners, potentially leading to incorrect interest accrual.

UniHelper.sol (Line 57): Utilizing block.timestamp for time-based events could result in inconsistent behavior across network upgrades.

AddPairLogic.sol (Line 185), DecayLib.sol (Line 13), ResolvedOrder.sol (Line 28): Timestamp dependencies in order resolution and decay mechanisms can be exploited, leading to unfair trading advantages.

GammaTradeMarket.sol (Lines 195, 248), GammaTradeMarketLib.sol (Lines 66, 72, 111, 114): Market settlement times reliant on block.timestamp may be inconsistent, affecting trade execution and settlement.

UniswapSettlement.sol (Lines 34, 50): Settlement processes tied to block.timestamp could be vulnerable to timing attacks, compromising the integrity of trades.

# Recommendations
I have identified the use of block.timestamp in several contracts within the codebase. It is recommended to consider the use of block.number as an alternative due to the following reasons:

Reasons for Recommendation
Determinism: block.number provides a deterministic measure of time passage based on the number of blocks mined, which is not subject to manipulation by miners to the same extent as block.timestamp.

Consistency Across Networks: Different Ethereum networks (Mainnet, testnets) may have slight variations in average block time, but the increment of block.number is consistent across all networks after every mined block.

Upgrade Resilience: Hard forks and upgrades to the Ethereum protocol are less likely to affect the behavior of block.number, ensuring long-term stability of contract functions that rely on block numbers.