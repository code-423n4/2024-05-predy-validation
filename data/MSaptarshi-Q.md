# [L-01] Uniswap TWAP hardcoded
Although having 30min if twap interval is a fine duration, since the cost of twap manipulation will also increase with time. But with known duration a manipulation attack can be carried out without having any way to change the duration. So is a general good practice not to hardcode the twap duration.
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L11
## Recommendation
Dont hardcode it and have a function for changing the duration

# [L-02] Blacklisted users can't withdraw their tokens
Tokens like USDT has a blacklisting feature. So, if a user get's blacklisted they may not be able to withdraw their collateral
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L90
## Recommendation
Have a claiming mechanism for the users instead of pushing the funds

# [L-03] Pyth Oracle Stale Price
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L48
It is a known issue from [PYTH](https://docs.pyth.network/price-feeds/best-practices#price-availability) that having a stale price will revert by default in solidity
> Integrators should be careful to avoid accidentally using a stale price. The Pyth SDKs guard against this failure mode by incorporating a staleness check by default. Querying the current price will fail if too much time has elapsed since the last update. The SDKs expose this failure condition in an idiomatic way: for example, the Rust SDK may return None, and our Solidity SDK may revert the transaction.

Since Preddy uses getPriceNoOlderThan which automatically reverts when not updated within the stale duration that is 300 sec. 
But 300 sec is enough for a arbitrager or a bot to manipulate the price (which was seen in recent Levana Finance)
And as pointed out by PYTH in [here](https://docs.pyth.network/price-feeds/api-reference/evm/get-price-no-older-than) calling `updatePriceFeeds` will solve this problem.
## Recommendation
Add `updatePricefeeds` from PYTH

# [L-04] Empty Constructor
Constructors should not be left empty or uninvoked .
To prevent the implementation contract from being used, we invoke the _disableInitializers
 function in the constructor to automatically lock it when it is deployed.
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L68
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L90
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L67
## Recommendation
Call `_disableInitializers` in constructor

# [C-01] Admin related functions missing events
Centralised/Admin related functions should always emit events for transparency purposes through out the protocol
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L128
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L132
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L140
## Recommendation
Add a admin related events for these functions