## Low - 01# function applyInterestForToken
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/ApplyInterestLib.sol#L26-L48

ApplyInterestLib.applyInterestForToken the way the `protocolFeeQuote` and `protocolFeeBase` values are handled. These values are returned from the `applyInterestForPoolStatus` function, but they are not being used or stored anywhere within the `applyInterestForToken` function. [ApplyInterestLib.applyInterestForToken#L36-L46](https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/ApplyInterestLib.sol#L36-L47)
```solidity
(uint256 interestRateQuote, uint256 protocolFeeQuote) =
    applyInterestForPoolStatus(pairStatus.quotePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);

(uint256 interestRateBase, uint256 protocolFeeBase) =
    applyInterestForPoolStatus(pairStatus.basePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);

// ...

if (interestRateQuote > 0 || interestRateBase > 0) {
    emitInterestGrowthEvent(pairStatus, interestRateQuote, interestRateBase, protocolFeeQuote, protocolFeeBase);
}
```
The `protocolFeeQuote` and `protocolFeeBase` values are passed to the `emitInterestGrowthEvent` function, but they are not being used or stored anywhere else in the `ApplyInterestLib.applyInterestForToken`.

This could potentially lead to a bug if the `protocolFeeQuote` and `protocolFeeBase` values are expected to be used or stored elsewhere in the contract. If these values are not needed, it would be better to remove them from the function signature and the event emission to improve code clarity and gas efficiency.

Remove the `protocolFeeQuote` and `protocolFeeBase` values from the function signature and the event emission if they are not needed.
Store or use the `protocolFeeQuote` and `protocolFeeBase` values appropriately within the `applyInterestForToken` function if they are expected to be used or stored elsewhere in the contract.

## Low - 02# function uniswapV3MintCallback.
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L78-L90

**Add input validation:** The function does not validate the input parameters `amount0` and `amount1`. It would be a good practice to add checks to ensure that the values are non-zero before attempting to transfer tokens. This can prevent potential errors and improve the overall robustness of the code.

**Consider using a loop for token transfers:** Instead of having separate `if` statements for transferring `token0` and `token1`, you could use a loop to iterate over the non-zero amounts and transfer the tokens in a more concise and efficient manner.

## Low - 03# function updateIRMParams
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L133-L139

**Input Validation**: The function does not perform any validation on the input parameters `quoteIrmParams` and `baseIrmParams`. It might be beneficial to validate the input parameters to ensure they are within expected ranges or meet certain criteria before passing them to the `AddPairLogic.updateIRMParams` function.

**Pair Existence Check**: The function assumes that the `pairId` provided is a valid pair ID and that `globalData.pairs[pairId]` exists. It might be prudent to add a check to ensure that the `pairId` is valid and that the corresponding pair exists before attempting to update the interest rate model parameters.

## Low - 04# function withdrawProtocolRevenue
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L177-L191

**Redundant Check**: The check `if (amount > 0)` is redundant since it's already checked in the previous `require` statement. This line can be removed.
```solidity
if (amount > 0) {
    ERC20(pool.token).safeTransfer(msg.sender, amount);
}
```
**Revert Reason String**: The revert reason string "AZ" is not very descriptive and can be improved to provide more context about the error condition. For example, `"No protocol revenue to withdraw"` or something similar would be more informative.

**Function Visibility**: The function visibility is not explicitly specified. While the default visibility in Solidity is `public`, it's a good practice to explicitly specify the visibility for better readability and to prevent unintended visibility changes in future versions of Solidity.

**Event Emission Order**: The event `ProtocolRevenueWithdrawn` is emitted after the state update `(pool.accumulatedProtocolRevenue = 0;)`. It's generally recommended to emit events before making state changes to ensure that the event captures the pre-state values, which can be useful for off-chain monitoring and analysis.

## Low - 05# function withdrawCreatorRevenue
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L199-L213

**Redundant Check:** The line `if (amount > 0) { ... }` is redundant since the `require` statement above already ensures that `amount` is greater than zero. This redundant check can be removed.

**Revert Reason:** The revert reason `"AZ"` is not very informative. It would be better to use a more descriptive revert reason like `"No creator revenue to withdraw"` or something similar.

**Event Emission**: The event `CreatorRevenueWithdrawn` is emitted even if the `amount` is zero, which might be confusing. It would be better to emit the event only when the `amount` is greater than zero.

## Low - 06# function _getAssetStatusPool
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L380-L390

**Potential issue with storage layout:**
The function assumes that the `quotePool` and `basePool` fields of the `globalData.pairs[pairId]` struct are of the `Perp.AssetPoolStatus` type. If this assumption is incorrect or if the storage layout of the `Perp.AssetPoolStatus` struct changes in the future, it could lead to unexpected behavior or potential vulnerabilities.

Consider adding a type check or assertion to ensure that the returned value is of the expected type. For example:
```solidity
function _getAssetStatusPool(uint256 pairId, bool isQuoteToken)
    internal
    view
    returns (Perp.AssetPoolStatus storage)
{
    Perp.AssetPoolStatus storage pool = isQuoteToken
        ? globalData.pairs[pairId].quotePool
        : globalData.pairs[pairId].basePool;

    // Type check or assertion
    assert(pool.code == type(Perp.AssetPoolStatus).creationCode);

    return pool;
}
```
**Consider using a ternary operator:** Instead of using an if-else statement, you could use a ternary operator to make the code more concise and readable. For example:
```solidity
return isQuoteToken ? globalData.pairs[pairId].quotePool : globalData.pairs[pairId].basePool;
```
**Consider using a mapping for better performance**: If the `globalData.pairs` array is large and accessed frequently, it might be more efficient to use a mapping instead of an array to store the pair data. This could improve the performance of the function, especially if the `pairId` is not sequential.

## Low - 07# function predySettlementCallback
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/base/BaseMarket.sol#L28-L38

The function is using the `SettlementCallbackLib.execSettlement` function, which likely interacts with the PredyPool contract and potentially performs state changes or token transfers. If the `SettlementCallbackLib.execSettlement` function fails or reverts for any reason, the `predySettlementCallback` function will not handle the error or revert the transaction.

It might be a good practice to wrap the call to `SettlementCallbackLib.execSettlement` in a try/catch block or use a low-level call to handle potential failures gracefully. This way, if the `SettlementCallbackLib.execSettlement` function reverts or encounters an error, the `predySettlementCallback` function can handle the error appropriately, such as reverting the transaction with an appropriate error message or logging the error for debugging purposes.
```solidity
function predySettlementCallback(
    address quoteToken,
    address baseToken,
    bytes memory settlementData,
    int256 baseAmountDelta
) external onlyPredyPool {
    SettlementCallbackLib.SettlementParams memory settlementParams =
        SettlementCallbackLib.decodeParams(settlementData);
    SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);

    // Wrap the call to execSettlement in a try/catch block
    try SettlementCallbackLib.execSettlement(_predyPool, quoteToken, baseToken, settlementParams, baseAmountDelta) {
        // Handle success case if needed
    } catch (bytes memory reason) {
        // Handle failure case
        // You can revert with the reason or log the error for debugging
        revert(string(reason));
    }
}
```

## Low - 08# function buy
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/base/SettlementCallbackLib.sol#L137-L181

**Unnecessary Calculations:** In the case where `price` is 0, the code calculates `quoteAmount` but doesn't use it. Consider removing this unnecessary calculation to improve code efficiency and readability.

**Potential Overflow/Underflow:** The multiplication operation `buyAmount * price / Constants.Q96` could potentially overflow or underflow, depending on the values of `buyAmount` and `price`. Consider using SafeMath or other overflow/underflow prevention techniques.

## Low - 09# function applyInterestForToken
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/ApplyInterestLib.sol#L26-L48

**Use Modifiers or Guards:** The check for `pairStatus.lastUpdateTimestamp >= block.timestamp` could be extracted into a modifier or a guard clause at the beginning of the function. This would make the code more readable and easier to maintain.

## Low - 10# https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L58-L66

In the `onlyPoolOwner` and `onlyVaultOwner` modifiers, the check for ownership is based on the `msg.sender` address. This assumes that the caller is an externally-owned account (EOA) and not a contract. If a contract calls these functions, the `msg.sender` will be the contract address, not the address of the account that initiated the transaction.

This can lead to unintended behavior if a contract is allowed to create pools or vaults, as the ownership check will always fail. 

Consider using the `tx.origin` instead of `msg.sender` in the ownership checks. Using `tx.origin` has its own security implications, as it can make the contract vulnerable to phishing attacks.

A better approach would be to implement an access control mechanism that explicitly allows contracts to be owners, and to use the `msg.sender `for EOAs and the contract address for contract owners. This way, the ownership check would work correctly for both EOAs and contracts.
```solidity
modifier onlyPoolOwner(uint256 pairId) {
    address poolOwner = globalData.pairs[pairId].poolOwner;
    if (msg.sender != poolOwner && (tx.origin != poolOwner || !isContractOwner[poolOwner][msg.sender])) {
        revert CallerIsNotPoolCreator();
    }
    _;
}
```

## Low - 11# function applyInterestForPoolStatus
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/ApplyInterestLib.sol#L50-L73

**Consider Caching Calculations:** The function calculates the `utilizationRatio` every time it is called. If this value is expensive to calculate or does not change frequently, it might be worth caching the result to improve performance.

**Add Error Handling:** The function does not handle potential errors or edge cases, such as when the `InterestRateModel.calculateInterestRate` function fails or returns an invalid value.

## Low - 12# function updateRebalanceInterestGrowth
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/Perp.sol#L159-L174

The code uses the magic number `1e18` (`10^18`) multiple times. It would be better to define a constant with a descriptive name (e.g., `PRECISION_FACTOR`) and use that constant instead. This makes the code more readable and easier to maintain if the value needs to be changed in the future.

The code performs arithmetic operations without any overflow/underflow checks. It would be safer to use a library like OpenZeppelin's `SafeMath` to prevent potential integer overflows or underflows.

The function `updateRebalanceInterestGrowth` is responsible for updating two different values (`rebalanceInterestGrowthBase` and `rebalanceInterestGrowthQuote`). It might be better to split this into two separate functions, one for updating the base interest growth and another for updating the quote interest growth. This would improve code readability and maintainability.

## Low - 13# function swapForOutOfRange
https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/Perp.sol#L305-L330

Avoid Unnecessary Calculations In the case where `pairStatus.isQuoteZero` is `true`, the calculation of `deltaPosition1` is not needed. Similarly, when `pairStatus.isQuoteZero` is `false`, the calculation of `deltaPosition0` is unnecessary. Could conditionally skip these calculations to improve performance.

Utilize Existing Functions Instead of using the `LPMath.calculateAmount0ForLiquidity` and `LPMath.calculateAmount1ForLiquidity` functions directly, Could consider creating a helper function that encapsulates the logic for calculating delta positions based on the `isQuoteZero` flag. This would improve code reusability and readability.