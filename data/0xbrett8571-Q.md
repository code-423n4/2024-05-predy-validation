## 1. computePremium Security

[Link to the Function](https://github.com/code-423n4/2024-05-predy/blob/e96f378007e3dc56b184079f0c0e4fe48a72efaa/src/libraries/PerpFee.sol#L65-L81)

`settlePremium` function modifies the `sqrtPerp` storage variable directly. It might be safer to create a new `SqrtPositionStatus` instance with the updated values and return it, allowing the caller to decide whether to update the storage variable or not.

This would follow the principle of minimizing the number of storage writes and reduce the risk of unintended modifications.

## 2. Redundant Calculations and Conditional Complexity

[Link here](https://github.com/code-423n4/2024-05-predy/blob/e96f378007e3dc56b184079f0c0e4fe48a72efaa/src/libraries/PerpFee.sol#L111-L132)
[Link here](https://github.com/code-423n4/2024-05-predy/blob/e96f378007e3dc56b184079f0c0e4fe48a72efaa/src/libraries/PerpFee.sol#L136-L153)

The `computeRebalanceInterest` function calculates `rebalanceAmount` as `Math.abs(userStatus.sqrtPerp.amount`). However, this calculation is repeated in the `settleRebalanceInterest` function. It might be more efficient to calculate `rebalanceAmount` once and pass it as a parameter to `computeRebalanceInterest`.

The if condition in both `computeRebalanceInterest` and `settleRebalanceInterest` functions is relatively complex, involving multiple checks. Consider breaking it down into smaller, more readable conditions or extracting it into a separate function for better readability and maintainability.

## 3. Redundant Variable Assignment

[Link Here](https://github.com/code-423n4/2024-05-predy/blob/e96f378007e3dc56b184079f0c0e4fe48a72efaa/src/libraries/Reallocation.sol#L18-L37)
The code assigns values to `token0Status` and `token1Status` based on the `_assetStatusUnderlying.isQuoteZero` flag. These assignments could be simplified by directly accessing the appropriate properties from `_assetStatusUnderlying`.
```solidity
ScaledAsset.AssetStatus memory token0Status = _assetStatusUnderlying.isQuoteZero
    ? _assetStatusUnderlying.quotePool.tokenStatus
    : _assetStatusUnderlying.basePool.tokenStatus;

ScaledAsset.AssetStatus memory token1Status = _assetStatusUnderlying.isQuoteZero
    ? _assetStatusUnderlying.basePool.tokenStatus
    : _assetStatusUnderlying.quotePool.tokenStatus;
```
### Unnecessary Memory Allocation
The function creates two temporary memory variables (`token0Status` and `token1Status`) that are only used to pass them to the `_getNewRange` function. Instead, you could directly pass the appropriate properties from `_assetStatusUnderlying` to `_getNewRange`, avoiding the need for these temporary variables.

### Inline Variable Declaration
The `tickSpacing` variable could be declared and assigned inline within the function call to `_getNewRange`, eliminating the need for a separate variable declaration.
```solidity
return _getNewRange(
    _assetStatusUnderlying,
    _assetStatusUnderlying.isQuoteZero ? _assetStatusUnderlying.quotePool.tokenStatus : _assetStatusUnderlying.basePool.tokenStatus,
    _assetStatusUnderlying.isQuoteZero ? _assetStatusUnderlying.basePool.tokenStatus : _assetStatusUnderlying.quotePool.tokenStatus,
    currentTick,
    IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing()
);
```
https://github.com/code-423n4/2024-05-predy/blob/e96f378007e3dc56b184079f0c0e4fe48a72efaa/src/libraries/Reallocation.sol#L18-L37

The function retrieves the `tickSpacing` value from the Uniswap V3 pool contract using the `IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing()` call. This assumes that the `_assetStatusUnderlying.sqrtAssetStatus.uniswapPool` address points to a valid Uniswap V3 pool contract.

If the `_assetStatusUnderlying.sqrtAssetStatus.uniswapPool` address is not a valid Uniswap V3 pool contract, or if the contract at that address does not implement the `tickSpacing` function correctly, the `getNewRange` function could potentially return an incorrect or unexpected range.

**To mitigate this issue**, consider adding input validation or error handling to ensure that the `_assetStatusUnderlying.sqrtAssetStatus.uniswapPool` address is a valid Uniswap V3 pool contract before calling the `tickSpacing` function. This could involve checking if the contract at the given address implements the expected interface or checking if the returned `tickSpacing` value is within an expected range.