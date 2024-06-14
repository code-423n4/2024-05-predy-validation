# QA Report

# [L-01] Unnecessary revert in `TradeLogic`

## Summary

Within the function [_executeTrade](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/gamma/GammaTradeMarket.sol#L152-L209), there’s a call to [checkSafe](https://github.com/code-423n4/2024-05-predy/blob/main/src/libraries/PositionCalculator.sol#L49-L61), which internally calls [getIsSafe](https://github.com/code-423n4/2024-05-predy/blob/main/src/libraries/PositionCalculator.sol#L63-L73), from which it gets and returns the `minMargin` of a vault and updates the value of the `bool isSafe`. Even though it verifies if the position has enough value to remain open, it should not revert, based on the fact that even if it’s not safe, all of the trading logic and updates to the state have already been executed. The only remaining piece of code that hasn’t executed is the event emission of [PositionUpdated](https://github.com/code-423n4/2024-05-predy/blob/main/src/libraries/logic/TradeLogic.sol#L58-L65), which can be confirmed by running the test [testExecuteOrderSucceedsForOpen](https://github.com/code-423n4/2024-05-predy/blob/main/test/market/gamma/ExecuteOrder.t.sol#L45-L56) with increased verbosity, allowing us to see that position has been traded and updated, before calling `checkSafe`.

The following command can be used to run the test without making any changes:
`forge test —mt testExecuteOrderSucceedsForOpen -vvvv`

## Recommendations

One way to mitigate this issue is by checking whether the position is safe earlier on in the function, by moving the call at the very top of the function. This would allow for a revert to take place at an appropriate time, before any state updates.

# [L-02] `executeOrderOrderV3` is always called with `orderId` == 0

## Summary

The [PerpMarketV1::executeOrderV3()](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/perp/PerpMarketV1.sol#L149C4-L157C6) function returns the result from the internal [PerpMarketV1::_executeOrderV3()](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/perp/PerpMarketV1.sol#L159) function. This result represents the trade outcome from the predy pool. However, there's an issue: the `orderId` when [PerpMarketV1::_executeOrderV3()](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/perp/PerpMarketV1.sol#L159) is called is hardcoded to 0.

```solidity
function executeOrderV3(SignedOrder memory order, SettlementParamsV3 memory settlementParams)
        external
        nonReentrant
        returns (IPredyPool.TradeResult memory)
    {
        PerpOrderV3 memory perpOrder = abi.decode(order.order, (PerpOrderV3));
        return _executeOrderV3(perpOrder, order.sig, settlementParams, 0); //@audit 4th parameter is orderId which is 0
    }
```

This `orderId` serves as metadata used off-chain, but it's never incremented, potentially causing problems off-chain.

To verify this fix, we can add a logging statement within  the [PerpMarketV1::_executeOrderV3()](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/perp/PerpMarketV1.sol#L159):
`console2.log('OrderId:', orderId)`

Then, run the following command to execute the test without any modifications:
`forge test --match-test testExecuteOrderV3SucceedsForOpen -vvvv`

## Recommendations

To address this issue, you can move away from hardcoding `orderId` to 0. Instead, you should follow the approach you implemented in [PerpMarket::executeOrderV3L2()](https://github.com/code-423n4/2024-05-predy/blob/main/src/markets/perp/PerpMarket.sol#L28):

```solidity
function executeOrderV3L2(
        PerpOrderV3L2 memory compressedOrder,
        bytes memory sig,
        SettlementParamsV3 memory settlementParams,
        uint64 orderId //@audit-info not harcoded orderId
    ) external nonReentrant returns (IPredyPool.TradeResult memory) {

//some code...

return _executeOrderV3(order, sig, settlementParams, orderId);

```

# [C-01] Functions only accessible by certain roles can be frozen

## Summary

When a new market is created, whether it inherits from the [BaseMarket](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol) or the [BaseMarketUpgradeable](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol) contract, it has functions to update the [filler](https://docs.predy.finance/predy-v6/dev/architecture#filler) and the settlement address, with the only difference being that the `upgradeable` version offers a function to update the quoter. Even though they are access-controlled, they can be used to leave a certain role unassigned to anyone, leading to any functionality dependent on it being suspended up until a new address is set.

## Recommendations

Add a check that confirms the new address that is being set is not equal to address(0), and make sure the length of the [_whiteListedSettlements](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L29C65-L29C88) is greater than 0.