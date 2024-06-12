# [L-01] Uniswap TWAP hardcoded
Although having 30min if twap interval is a fine duration, since the cost of twap manipulation will also increase with time. But with known duration a manipulation attack can be carried out without having any way to change the duration. So is a general good practice not to hardcode the twap duration.
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L11
## Recommendation
Dont hardcode it and have a function for changing the duration

# [L-02] Blacklisted users can't withdraw their tokens
If a user get's blacklisted they may not be able to withdraw their collateral
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L90
## Recommendation
Have a claiming mechanism for the users instead of pushing the funds

# [C-01] Admin related functions missing events
Centralised/Admin related functions should always emit events for transparency purposes through out the protocol
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L128
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L132
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L140
## Recommendation
Add a admin related events for these functions