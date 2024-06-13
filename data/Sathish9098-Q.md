 ## QA Reports

##

## [L-] Revenue Discrepancies in ``poolStatus.accumulatedProtocolRevenue`` and ``poolStatus.accumulatedCreatorRevenue`` Due to Ineffective Fee Division 

### Impact 
As the transaction volume increases, the cumulative loss due to ineffective fee division becomes significantly larger.

Solidity uses integer division, which discards any fractional part. For example, if totalProtocolFee is 5, dividing it by 2 results in 2, with a remainder of 1.
This remainder is lost if not handled properly.

Each time the division happens, the remainder is discarded. Over many transactions, these small remainders add up, leading to a significant loss in total accumulated revenue.

### POC

In the original fee division approach, integer division causes loss of precision. For example, dividing 1001 by 2 results in 500 each for protocol and creator revenues, with a remainder of 1 being lost.

### Original Method:
Calculation: totalProtocolFee = 1001
Division: halfFee = 1001 / 2 = 500, remainder = 1001 % 2 = 1
Revenue: protocolRevenue += 500, creatorRevenue += 500 (1 unit lost)
Over 1000 transactions, this results in a loss of 1000 units.

### Improved Method:
Calculation: totalProtocolFee = 1001
Division: halfFee = 1001 / 2 = 500, remainder = 1001 % 2 = 1
Revenue: protocolRevenue += 500 + 1, creatorRevenue += 500 (no units lost)

RemainingFee += remainder ;

By adding the remainder to one of the accounts, no units are lost, ensuring accurate revenue distribution. The remaining Fee can be accumulated the this fee can be shared to  protocolRevenue and creatorRevenue instead of losing 1000 uints.


```solidity
FILE:2024-05-predy/src/libraries/ApplyInterestLib.sol

poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;
poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;

```
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L71-L72

### Recommended Mitigation
As per POC ensures that all calculated fees are accurately accounted for, preventing any loss due to integer division. 

##

## [L-] Chainlink's latestRoundData return stale or incorrect result

### Impact

On PriceFeed.sol, you are using latestRoundData, but there is no check if the return value indicates stale data. The current check ``quoteAnswer > 0`` not enough to ensure the staleness . 

This could lead to stale prices according to the Chainlink documentation: https://docs.chain.link/data-feeds/price-feeds/historical-data Related report: https://github.com/code-423n4/2021-05-fairside-findings/issues/70

```solidity
FILE:2024-05-predy/src/PriceFeed.sol

    (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

        IPyth.Price memory basePrice = IPyth(_pyth).getPriceNoOlderThan(_priceId, VALID_TIME_PERIOD);

        require(basePrice.expo == -8, "INVALID_EXP");

        require(quoteAnswer > 0 && basePrice.price > 0);

        

```
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L46-L52

### Recommended Mitigation
Incorporate the required checks to ensure the staleness 

```diff

(, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

+    (uint80 roundID,int256 quoteAnswer,uint256 timestamp,uint256 updatedAt,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

        IPyth.Price memory basePrice = IPyth(_pyth).getPriceNoOlderThan(_priceId, VALID_TIME_PERIOD);

+ require(updatedAt >= roundID, "Stale price");
+ require(timestamp != 0,"Round not complete");

+ if (updatedAt < block.timestamp - maxDelayTime) //maxDelayTime minimum allowed delay 
+            revert PRICE_OUTDATED();
        
        require(basePrice.expo == -8, "INVALID_EXP");

        require(quoteAnswer > 0 && basePrice.price > 0);

```

##

## [L-] Unable to remove pairs once added in the predy system

 In automated market makers (AMMs), liquidity pools represent the order book. Non-performing pairs can clutter the system, making it harder for users to find active trading pairs. Without the ability to prune inactive pairs, governance mechanisms become overwhelmed with managing an ever-growing list of pairs, complicating decision-making processes.


```solidity
FILE: 2024-05-predy/src/PredyPool.sol 

function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {
        return AddPairLogic.addPair(globalData, allowedUniswapPools, addPairParam);
    }

``` 

##

## [L-] Risk of Operator Self-Assignment Hindering Protocol Updates and Security 

The function setOperator(address newOperator) external onlyOperator allows the current operator to change the operator address to a new one. 

- If the protocol requires updates or changes that the current operator does not agree with or that do not align with their interests, they can block these changes by refusing to set a new, neutral operator or owner. 

- In the worst-case scenario, a malicious operator can use this privilege to set the operator address to a contract or address that acts in bad faith, potentially leading to exploits, fund mismanagement, or other malicious activities.


```solidity
FILE: 2024-05-predy/src/PredyPool.sol

 function setOperator(address newOperator) external onlyOperator {
        require(newOperator != address(0));
        operator = newOperator;

        emit OperatorUpdated(newOperator);
    }

```
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L97-L102

### Recommended Mitigation
- Restrict the ability to change the operator to the contract owner by using the onlyOwner modifier. This ensures that only a trusted and secure entity can make such critical changes.

- Use a multi-signature wallet to manage ownership and critical changes.

##

## [L-]  Redundant Timestamp Comparison in pairStatus.lastUpdateTimestamp >= block.timestamp

The condition if (pairStatus.lastUpdateTimestamp >= block.timestamp) is used to determine if the lastUpdateTimestamp is greater than or equal to the current block's timestamp. While the equality check (==) is valid and necessary, the greater-than check (>) is redundant and practically impossible because block.timestamp represents the current block's timestamp and cannot be in the future.

```solidity
FILE: 2024-05-predy/src/libraries/ApplyInterestLib.sol

 // avoid applying interest rate multiple times in the same block
        if (pairStatus.lastUpdateTimestamp >= block.timestamp) {
            return;
        }

 // Update last update timestamp
        pairStatus.lastUpdateTimestamp = block.timestamp;


```
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L31-L34

### Recommended Mitigation

```solidity

if (pairStatus.lastUpdateTimestamp == block.timestamp) {
    return; // Skip the update if it's within the same block
}

```

##

## [L-] 







