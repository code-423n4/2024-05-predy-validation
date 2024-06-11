## Title
Missing event


## Impact
Events for critical state changes (e.g. owner and other critical parameters) and token transfer should be emitted for tracking this off-chain.


## Proof-of-code
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L300-L304

    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {
        require(globalData.pairs[pairId].allowlistEnabled);

        allowedTraders[trader][pairId] = enabled;
    }

https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L329-L331

    function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {
        globalData.take(isQuoteAsset, to, amount);
    }

https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/spot/SpotMarket.sol#L76-L78

    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {
        _whiteListedSettlements[settlementContractAddress] = isEnabled;
    }


## Recommendation
You should emit an event when these state variables change.