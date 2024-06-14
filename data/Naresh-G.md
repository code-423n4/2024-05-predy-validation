## Summary

| |Issue|Instances| Gas Savings
|-|:-|:-:|:-:|
| [[G-01](#g-01)] | `abi.encode()` is less efficient than `abi.encodepacked()` for non-address arguments | 13| 0|
| [[G-02](#g-02)] | Argument validation should be at the top of the function/block | 135| 0|
| [[G-03](#g-03)] | Use `Array.unsafeAccess()` to avoid repeated array length checks | 2| 4200|
| [[G-04](#g-04)] | Avoid updating storage when the value hasn't changed | 2| 1600|
| [[G-05](#g-05)] | Avoid zero transfer to save gas | 24| 2400|
| [[G-06](#g-06)] | It is more efficient to use `block.timestamp` directly rather than calling a function to return it | 2| 0|
| [[G-07](#g-07)] | Cache array length outside of loop | 2| 194|
| [[G-08](#g-08)] | Constructors can be marked as `payable` to save deployment gas | 11| 231|
| [[G-09](#g-09)] | Avoid contract existence checks by using low level calls | 4| 400|
| [[G-10](#g-10)] | Use custom errors rather than `revert()/require()` strings to save gas | 23| 1150|
| [[G-11](#g-11)] | Division by powers of two should use bit shifting | 3| 60|
| [[G-12](#g-12)] | Do not calculate constants | 2| 0|
| [[G-13](#g-13)] | `do-while` is cheaper than `for`-loops when the initial check can be skipped | 2| 0|
| [[G-14](#g-14)] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function to save deployment gas | 2| 0|
| [[G-15](#g-15)] | Counting down in `for` statements is more gas efficient | 2| 16|
| [[G-16](#g-16)] | Function names can be optimized | 57| 7296|
| [[G-17](#g-17)] | Don't initialize variables with default value | 5| 0|
| [[G-18](#g-18)] | Optimize Deployment Size by Fine-tuning IPFS Hash | 55| 83270|
| [[G-19](#g-19)] | Use assembly hashing | 5| 400|
| [[G-20](#g-20)] | Use `s.x = s.x + y` instead of `s.x += y` for memory structs (same for `-=` etc) | 18| 1800|
| [[G-21](#g-21)] | Merge events to save gas | 2| 750|
| [[G-22](#g-22)] | Inline modifiers used only once | 2| 0|
| [[G-23](#g-23)] | Multiple accesses of the same mapping/array key/index should be cached | 5| 210|
| [[G-24](#g-24)] | Multiple mappings can be replaced with a single struct mapping | 9| 378|
| [[G-25](#g-25)] | State variables should be cached in stack variables rather than re-reading them from storage | 9| 873|
| [[G-26](#g-26)] | Use Only Named Returns | 103| 4532|
| [[G-27](#g-27)] | Nesting `if`-statements is cheaper than using `&&` | 31| 124|
| [[G-28](#g-28)] | Consider using OpenZeppelin's `EnumerateSet` instead of nested mappings | 2| 2000|
| [[G-29](#g-29)] | Shorten the array rather than copying to a new one | 2| 0|
| [[G-30](#g-30)] | Only emit event in setter function if the state variable was changed | 1| 0|
| [[G-31](#g-31)] | Operator `>=`/`<=` costs less gas than operator `>`/`<` | 138| 414|
| [[G-32](#g-32)] | Functions that revert when called by normal users can be marked as `payable` | 26| 650|
| [[G-33](#g-33)] | Using `++i/--i` instead of `i++/i--` can save gas | 5| 25|
| [[G-34](#g-34)] | Using `private` for constants saves gas | 39| 132834|
| [[G-35](#g-35)] | Refactor modifiers to call a local function | 8| 8000|
| [[G-36](#g-36)] | Default bool values are manually reset | 3| 0|
| [[G-37](#g-37)] | Use assembly scratch space for building calldata for external calls | 183| 40260|
| [[G-38](#g-38)] | Use shift right/left instead of division if possible | 63| 0|
| [[G-39](#g-39)] | Usage of smaller `uint`/`int` types causes overhead | 124| 6820|
| [[G-40](#g-40)] | Consider using solady's 'FixedPointMathLib' | 76| 0|
| [[G-41](#g-41)] | Newer versions of solidity are more gas efficient | 55| 0|
| [[G-42](#g-42)] | Splitting `require()` statements that use `&&` saves gas | 8| 24|
| [[G-43](#g-43)] | `address(this)` can be stored in state variable that will ultimately cost less, than every time calculating this, specifically when it used multiple times in a contract | 26| 0|
| [[G-44](#g-44)] | Gas savings can be achieved by changing the model for assigning value to the structure | 2| 260|
| [[G-45](#g-45)] | Structs can be packed into fewer storage slots by editing time variables | 4| 0|
| [[G-46](#g-46)] | Optimize Storage with Byte Truncation for Time Related State Variables | 18| 36000|
| [[G-47](#g-47)] | Trade-offs Between Modifiers and Internal Functions | 190| 1995000|
| [[G-48](#g-48)] | Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas for changes | 4| 68400|
| [[G-49](#g-49)] | Divisions can be `unchecked` to save gas | 63| 1260|
| [[G-50](#g-50)] | Avoid unnecessary public variables | 9| 198000|
| [[G-51](#g-51)] | Use `!= 0` instead of `> 0` for unsigned integer comparison | 53| 212|
| [[G-52](#g-52)] | Using assembly to check for `address(0)` can save gas | 10| 60|
| [[G-53](#g-53)] | Use assembly to `emit` events | 29| 1102|
| [[G-54](#g-54)] | Use assembly to validate `msg.sender` | 12| 144|
| [[G-55](#g-55)] | Simple checks for zero can be done using assembly to save gas | 164| 164|
| [[G-56](#g-56)] | Use assembly in place of `abi.decode` to extract calldata values more efficiently | 8| 0|
| [[G-57](#g-57)] | Use `byte32` in place of `string` | 12| 0|
| [[G-58](#g-58)] | `internal` functions not called by the contract should be removed to save deployment gas | 88| 0|
| [[G-59](#g-59)] | Use `x = x + y` instead of `x += y` for state variables | 3| 30|
| [[G-60](#g-60)] | Use solady library where possible to save gas | 18| 18000|
| [[G-61](#g-61)] | Variable declared within iteration | 1| 0|
| [[G-62](#g-62)] | Enable IR-based code generation | 57| 0|
| [[G-63](#g-63)] | Using XOR (^) and AND (&) bitwise equivalents for gas optimizations | 72| 0|

Total: 2106 instances over 63 issues with an estimate of 2619543 gas saved.

### Gas Risk Issues

### [G-01]<a name="g-01"></a> `abi.encode()` is less efficient than `abi.encodepacked()` for non-address arguments



*There are 13 instance(s) of this issue:*

```solidity
📁 File: src/base/BaseMarket.sol

68:         return abi.encode(
69:             SettlementCallbackLib.SettlementParams(
70:                 filler,
71:                 settlementParams.contractAddress,
72:                 settlementParams.encodedData,
73:                 settlementParams.maxQuoteAmount,
74:                 settlementParams.price,
75:                 settlementParams.fee
76:             )
77:         );

115:         bytes memory data = abi.encode(tradeResult);

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L68-L77), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L115-L115)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

110:         return abi.encode(
111:             SettlementParamsV3Internal(
112:                 filler,
113:                 settlementParams.contractAddress,
114:                 settlementParams.encodedData,
115:                 settlementParams.maxQuoteAmountPrice,
116:                 settlementParams.minQuoteAmountPrice,
117:                 settlementParams.price,
118:                 settlementParams.feePrice,
119:                 settlementParams.minFee
120:             )
121:         );

163:         bytes memory data = abi.encode(tradeResult);

```


*GitHub* : [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L110-L121), [163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L163-L163)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

57:         bytes memory data = abi.encode(pairStatus);

65:         bytes memory data = abi.encode(vaultStatus);

```


*GitHub* : [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L57-L57), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L65-L65)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

45:             abi.encode(
46:                 GAMMA_MODIFY_INFO_TYPE_HASH,
47:                 info.isEnabled,
48:                 info.expiration,
49:                 info.maximaDeviation,
50:                 info.lowerLimit,
51:                 info.upperLimit,
52:                 info.hedgeInterval,
53:                 info.sqrtPriceTrigger,
54:                 info.minSlippageTolerance,
55:                 info.maxSlippageTolerance,
56:                 info.auctionPeriod,
57:                 info.auctionRange
58:             )

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L45-L58)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

169:                 abi.encode(
170:                     CallbackData(
171:                         GammaTradeMarketLib.CallbackType.TRADE, gammaOrder.info.trader, gammaOrder.marginAmount
172:                     )
173:                 )

265:             abi.encode(CallbackData(triggerType, userPosition.owner, 0))

303:             abi.encode(CallbackData(triggerType, userPosition.owner, 0))

323:                 abi.encode(
324:                     CallbackData(
325:                         GammaTradeMarketLib.CallbackType.QUOTE, gammaOrder.info.trader, gammaOrder.marginAmount
326:                     )
327:                 )

```


*GitHub* : [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L169-L173), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L265-L265), [303](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L303-L303), [323](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L323-L327)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

191:                 abi.encode(
192:                     CallbackData(
193:                         CallbackSource.TRADE3, perpOrder.info.trader, 0, perpOrder.leverage, resolvedOrder, orderId
194:                     )
195:                 )

299:                 abi.encode(
300:                     CallbackData(
301:                         CallbackSource.QUOTE3,
302:                         perpOrder.info.trader,
303:                         0,
304:                         perpOrder.leverage,
305:                         PerpOrderV3Lib.resolve(perpOrder, bytes("")),
306:                         0
307:                     )
308:                 )

```


*GitHub* : [191](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L191-L195), [299](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L299-L308)

### [G-02]<a name="g-02"></a> Argument validation should be at the top of the function/block

Checks that involve function arguments should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

*There are 135 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

84:         if (amount0 > 0) {

87:         if (amount1 > 0) {

272:         if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L87-L87), [272](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L272-L272)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

55:         if (settlementParams.fee > 0) {

77:         } else if (baseAmountDelta < 0) {

122:         if (price == 0) {

169:         if (price == 0) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L55-L55), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L77-L77), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L122-L122), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L169-L169)

```solidity
📁 File: src/libraries/InterestRateModel.sol

25:         if (utilizationRatio <= irmParams.kinkRate) {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L25-L25)

```solidity
📁 File: src/libraries/Perp.sol

210:             _sqrtAssetStatus.tickLower + _assetStatusUnderlying.riskParams.rebalanceThreshold < currentTick
211:                 && currentTick < _sqrtAssetStatus.tickUpper - _assetStatusUnderlying.riskParams.rebalanceThreshold

236:         if (currentTick < _sqrtAssetStatus.tickLower) {

240:         } else if (currentTick < _sqrtAssetStatus.tickUpper) {

321:         if (pairStatus.isQuoteZero) {

436:         if (!Reallocation.isInRange(_sqrtAssetStatus)) {

443:         if (_tradeSqrtAmount > 0) {

446:             if (_sqrtAssetStatus.totalAmount == _sqrtAssetStatus.borrowedAmount) {

450:         } else if (_tradeSqrtAmount < 0) {

454:         if (_isQuoteZero) {

535:         if (_userStatus.sqrtPerp.amount * _amount >= 0) {

538:             if (_userStatus.sqrtPerp.amount.abs() >= _amount.abs()) {

546:         if (_assetStatus.totalAmount == _assetStatus.borrowedAmount) {

554:             if (getAvailableSqrtAmount(_assetStatus, true) < uint256(-closeAmount)) {

566:             if (getAvailableSqrtAmount(_assetStatus, false) < uint256(-openAmount)) {

593:         if (_isWithdraw && _assetStatus.borrowedAmount == 0) {

633:         if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {

659:         if (_userStatus.sqrtPerp.amount < 0) {

664:         if (_isQuoteZero) {

682:         if (_positionAmount * _tradeAmount >= 0) {

686:             if (_positionAmount.abs() >= _tradeAmount.abs()) {

755:         if (_userStatus.sqrtPerp.amount * _tradeSqrtAmount >= 0) {

758:             if (_userStatus.sqrtPerp.amount.abs() >= _tradeSqrtAmount.abs()) {

778:             if (_isQuoteZero) {

797:         if (_pairStatus.isQuoteZero) {

```


*GitHub* : [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L210-L211), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L236-L236), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L240-L240), [321](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L321-L321), [436](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L436-L436), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L443-L443), [446](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L446-L446), [450](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L450-L450), [454](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L454-L454), [535](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L535-L535), [538](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L538-L538), [546](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L546-L546), [554](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L554-L554), [566](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L566-L566), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L593-L593), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L633-L633), [659](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L659-L659), [664](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L664-L664), [682](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L682-L682), [686](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L686-L686), [755](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L755-L755), [758](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L758-L758), [778](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L778-L778), [797](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L797-L797)

```solidity
📁 File: src/libraries/PerpFee.sol

73:         if (sqrtPerp.amount > 0) {

76:         } else if (sqrtPerp.amount < 0) {

86:         if (baseAssetStatus.isQuoteZero) {

101:         if (sqrtPerp.amount > 0) {

104:         } else if (sqrtPerp.amount < 0) {

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L73-L73), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L76-L76), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L86-L86), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L101-L101), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L104-L104)

```solidity
📁 File: src/libraries/PositionCalculator.sol

210:         if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {

```


*GitHub* : [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L210-L210)

```solidity
📁 File: src/libraries/Reallocation.sol

28:         if (_assetStatusUnderlying.isQuoteZero) {

56:             if (currentTick < previousCenterTick) {

65:                 if (lower < minLowerTick && minLowerTick < currentTick) {

78:                 if (upper > maxUpperTick && maxUpperTick >= currentTick) {

139:         if (minLowerTick > currentLowerTick - tickSpacing) {

160:         if (maxUpperTick < currentUpperTick + tickSpacing) {

172:         if (sqrtRatioA <= sqrtPrice + TickMath.MIN_SQRT_RATIO) {

186:         if (liquidityAmount <= denominator1) {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L28-L28), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L56-L56), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L65-L65), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L78-L78), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L139-L139), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L160-L160), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L172-L172), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L186-L186)

```solidity
📁 File: src/libraries/ScaledAsset.sol

55:         require(_supplyTokenAmount > 0, "S3");

59:         if (_supplyTokenAmount < burnAmount) {

67:         require(getAvailableCollateralValue(tokenState) >= finalWithdrawAmount, "S0");

85:             require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");

86:         } else if (userStatus.positionAmount < 0) {

87:             require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");

93:         if (isSameSign(userStatus.positionAmount, _amount)) {

96:             if (userStatus.positionAmount.abs() >= _amount.abs()) {

108:             require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");

118:             require(getAvailableCollateralValue(tokenStatus) >= uint256(-openAmount), "S0");

148:         if (_userStatus.positionAmount > 0) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L55-L55), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L59-L59), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L67-L67), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L85-L85), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L86-L86), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L87-L87), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L93-L93), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L96-L96), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L108-L108), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L118-L118), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L148-L148)

```solidity
📁 File: src/libraries/SlippageLib.sol

29:         if (tradeResult.averagePrice == 0) {

33:         if (tradeResult.averagePrice > 0) {

35:             if (basePrice.lower(slippageTolerance) > uint256(tradeResult.averagePrice)) {

38:         } else if (tradeResult.averagePrice < 0) {

40:             if (basePrice.upper(slippageTolerance) < uint256(-tradeResult.averagePrice)) {

46:             maxAcceptableSqrtPriceRange > 0
47:                 && (
48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange
49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
50:                 )

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L35-L35), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L40-L40), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L46-L50)

```solidity
📁 File: src/libraries/UniHelper.sol

118:                 if (tickCurrent >= tickLower) {

135:                 if (tickCurrent < tickUpper) {

```


*GitHub* : [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L118-L118), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L135-L135)

```solidity
📁 File: src/libraries/VaultLib.sol

30:         if (vaultId == 0) {

59:             if (vault.openPosition.pairId != pairId) {

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L30-L30), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L59-L59)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

70:             uniswapV3Factory.getPool(uniswapPool.token0(), uniswapPool.token1(), uniswapPool.fee())
71:                 != _addPairParam.uniswapPool

153:         require(_pairs[_pairId].id == 0, "AAA");

216:         require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");

```


*GitHub* : [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L70-L71), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L153-L153), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L216-L216)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

29:         if (_amount <= 0) {

37:         if (_isStable) {

64:         require(_amount > 0, "AZ");

70:         if (_isStable) {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L37-L37), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L64-L64), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L70-L70)

```solidity
📁 File: src/libraries/math/LPMath.sol

42:         if (sqrtRatioA > sqrtRatioB) (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

80:         if (sqrtRatioA < sqrtRatioB) (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

```


*GitHub* : [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L42-L42), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L80-L80)

```solidity
📁 File: src/libraries/math/Math.sol

27:         } else if (x > 0) {

37:         } else if (x > 0) {

47:         } else if (x > 0) {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L27-L27), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L47-L47)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

23:         } else if (decayEndTime <= value) {

25:         } else if (decayStartTime >= value) {

31:             if (endPrice < startPrice) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L23-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L25-L25), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L31-L31)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

28:         if (block.timestamp > resolvedOrder.info.deadline) {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L28-L28)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

24:             if (items[i] == item) {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L24-L24)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

89:             if (tradeResult.minMargin == 0) {

146:         if (closeRatio == 1e18) {

197:         if (gammaOrder.positionId == 0) {

233:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

281:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

414:         if (isCreatedNew) {

421:         if (positionId == 0 || userPosition.vaultId == 0) {

425:         if (userPosition.owner != trader) {

429:         if (userPosition.pairId != pairId) {

```


*GitHub* : [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L89-L89), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L146-L146), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L197-L197), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L233-L233), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L281-L281), [414](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L414-L414), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L421-L421), [425](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L425-L425), [429](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L429-L429)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

80:         if (userPosition.sqrtPriceTrigger == 0) {

87:         if (lowerThreshold >= sqrtIndexPrice) {

95:         if (upperThreshold <= sqrtIndexPrice) {

122:         if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {

130:         if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {

178:         if (ratio > auctionParams.auctionRange) {

197:         if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {

201:         require(modifyInfo.maxSlippageTolerance >= modifyInfo.minSlippageTolerance);

202:         require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);

203:         require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);

```


*GitHub* : [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L80-L80), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L87-L87), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L95-L95), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L178-L178), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L197-L197), [201](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L201-L201), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L202-L202), [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L203-L203)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

37:         if (closePosition) {

38:             if (isLong && currentPositionAmount >= 0) {

42:             if (!isLong && currentPositionAmount <= 0) {

49:         if (reduceOnly) {

50:             if (currentPositionAmount == 0) {

54:             if (currentPositionAmount > 0) {

56:                     if (currentPositionAmount > -tradeAmount) {

66:                     if (-currentPositionAmount > tradeAmount) {

92:         if (limitPrice == 0 && stopPrice == 0) {

94:             if (!validateMarketOrder(tradePrice, tradeAmount, auctionData)) {

97:         } else if (limitPrice > 0 && stopPrice > 0) {

100:                 !validateLimitPrice(tradePrice, tradeAmount, limitPrice)
101:                     && !validateStopPrice(oraclePrice, tradePrice, tradeAmount, stopPrice, auctionData)

105:         } else if (limitPrice > 0) {

107:             if (!validateLimitPrice(tradePrice, tradeAmount, limitPrice)) {

110:         } else if (stopPrice > 0) {

112:             if (!validateStopPrice(oraclePrice, tradePrice, tradeAmount, stopPrice, auctionData)) {

127:         if (tradeAmount > 0 && limitPrice < tradePrice) {

131:         if (tradeAmount < 0 && limitPrice > tradePrice) {

155:         if (tradeAmount > 0) {

157:             if (stopPrice > oraclePrice) {

161:             if (tradePrice > Bps.upper(oraclePrice, decayedSlippageTorelance)) {

164:         } else if (tradeAmount < 0) {

166:             if (stopPrice < oraclePrice) {

170:             if (tradePrice < Bps.lower(oraclePrice, decayedSlippageTorelance)) {

181:         } else if (price1 > price2) {

199:         if (tradeAmount != 0) {

200:             if (tradeAmount > 0 && decayedPrice < tradePrice) {

204:             if (tradeAmount < 0 && decayedPrice > tradePrice) {

```


*GitHub* : [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L37-L37), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L38-L38), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L42-L42), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L49-L49), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L50-L50), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L54-L54), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L56-L56), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L66-L66), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L92-L92), [94](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L94-L94), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L97-L97), [100](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L100-L101), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L105-L105), [107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L107-L107), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L110-L110), [112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L112-L112), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L131-L131), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L155-L155), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L157-L157), [161](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L161-L161), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L164-L164), [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L166-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L170-L170), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L181-L181), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L199-L199), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L204-L204)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

53:         if (amountInMaximum > amountIn) {

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L53-L53)

```solidity
📁 File: src/types/GlobalData.sol

79:         if (isQuoteAsset) {

95:         if (isQuoteAsset) {

105:         if (isQuoteAsset) {

```


*GitHub* : [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L79-L79), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L95-L95), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L105-L105)

### [G-03]<a name="g-03"></a> Use `Array.unsafeAccess()` to avoid repeated array length checks

When using storage arrays, solidity adds an internal lookup of the array's length (a Gcoldsload **2100 gas**) to ensure you don't read past the array's end. You can avoid this lookup by using [Array.unsafeAccess()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/94697be8a3f0dfcd95dfb13ffbd39b5973f5c65d/contracts/utils/Arrays.sol#L57) in cases where the length has already been checked, as is the case with the instances below

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

### [G-04]<a name="g-04"></a> Avoid updating storage when the value hasn't changed

If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (**2900 gas**), potentially at the expense of a Gcoldsload (**2100 gas**) or a Gwarmaccess (**100 gas**)

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/base/BaseMarket.sol

23:         whitelistFiller = _whitelistFiller;

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L23-L23)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

44:         whitelistFiller = _whitelistFiller;

```


*GitHub* : [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L44-L44)

### [G-05]<a name="g-05"></a> Avoid zero transfer to save gas

In Solidity, unnecessary operations can waste gas. For example, a transfer function without a zero amount check uses gas even if called with a zero amount, since the contract state remains unchanged. Implementing a zero amount check avoids these unnecessary function calls, saving gas and improving efficiency.

*There are 24 instance(s) of this issue:*

```solidity
📁 File: src/base/SettlementCallbackLib.sol

48:             ERC20(quoteToken).safeTransferFrom(
49:                 settlementParams.sender, address(predyPool), uint256(-settlementParams.fee)
50:             );

105:             ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);

123:             ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);

128:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);

130:                 ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);

133:             ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);

152:             ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);

170:             ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);

175:                 ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);

177:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);

180:             ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L48-L50), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L105-L105), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L123-L123), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L128-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L133-L133), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L152-L152), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L175-L175), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L177-L177), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L180-L180)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

99:                     ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);

106:                 ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));

```


*GitHub* : [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L99-L99), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L106-L106)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

69:                     ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));

```


*GitHub* : [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L69-L69)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

52:         ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);

90:         ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);

```


*GitHub* : [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L52-L52), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L90-L90)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

118:                     quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));

```


*GitHub* : [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L118-L118)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

126:                 quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));

317:         _permit2.permitWitnessTransferFrom(
318:             order.toPermit(),
319:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),
320:             order.info.trader,
321:             order.hash,
322:             PerpOrderLib.PERMIT2_ORDER_TYPE,
323:             order.sig
324:         );

330:         _permit2.permitWitnessTransferFrom(
331:             order.toPermit(),
332:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),
333:             order.info.trader,
334:             order.hash,
335:             PerpOrderV3Lib.PERMIT2_ORDER_TYPE,
336:             order.sig
337:         );

```


*GitHub* : [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L126-L126), [317](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L317-L324), [330](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L330-L337)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

30:         ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);

46:         ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);

54:             ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L46-L46), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L54-L54)

```solidity
📁 File: src/types/GlobalData.sol

85:         ERC20(currency).safeTransfer(to, amount);

```


*GitHub* : [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L85-L85)

### [G-06]<a name="g-06"></a> It is more efficient to use `block.timestamp` directly rather than calling a function to return it

Creating a function to return block.timestamp is gas inefficient due to the overhead of function storage and execution, while directly accessing block.timestamp in your contract code or as a function argument is a more gas-efficient alternative.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

68:             return (
69:                 true,
70:                 calculateSlippageTolerance(
71:                     userPosition.lastHedgedTime + userPosition.hedgeInterval,
72:                     block.timestamp,
73:                     userPosition.auctionParams
74:                     ),
75:                 GammaTradeMarketLib.CallbackType.HEDGE_BY_TIME
76:             );

112:             return (
113:                 true,
114:                 calculateSlippageTolerance(userPosition.expiration, block.timestamp, userPosition.auctionParams),
115:                 CallbackType.CLOSE_BY_TIME
116:             );

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L68-L76), [112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L112-L116)

### [G-07]<a name="g-07"></a> Cache array length outside of loop

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

### [G-08]<a name="g-08"></a> Constructors can be marked as `payable` to save deployment gas

`payable` functions cost less gas to execute, because the compiler does not have to add extra checks to ensure that no payment is provided. A constructor can be safely marked as payable, because only the deployer would be able to pass funds, and the project itself would not pass any funds.

*There are 11 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

68:     constructor() {}

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L68-L68)

```solidity
📁 File: src/PriceFeed.sol

14:     constructor(address pyth) {

37:     constructor(address quotePrice, address pyth, bytes32 priceId, uint256 decimalsDiff) {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L14-L14), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L37-L37)

```solidity
📁 File: src/base/BaseHookCallback.sol

12:     constructor(IPredyPool predyPool) {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L12-L12)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

13:     constructor() {}

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L13-L13)

```solidity
📁 File: src/base/BaseMarket.sol

19:     constructor(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
20:         BaseHookCallback(predyPool)
21:         Owned(msg.sender)
22:     {

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L19-L22)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

36:     constructor() {}

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L36-L36)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

67:     constructor() {}

```


*GitHub* : [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L67-L67)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

90:     constructor() {}

```


*GitHub* : [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L90-L90)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

16:     constructor(address swapRouterAddress, address quoterAddress) {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L16-L16)

```solidity
📁 File: src/tokenization/SupplyToken.sol

15:     constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)
16:         ERC20(_name, _symbol, __decimals)
17:     {

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L15-L17)

### [G-09]<a name="g-09"></a> Avoid contract existence checks by using low level calls

Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 4 instance(s) of this issue:*

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

86:             _pool.tokenStatus.removeAsset(ERC20(supplyTokenAddress).balanceOf(msg.sender), _amount);

```


*GitHub* : [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L86-L86)

```solidity
📁 File: src/types/GlobalData.sol

40:         globalData.lockData.quoteReserve = ERC20(globalData.pairs[pairId].quotePool.token).balanceOf(address(this));

41:         globalData.lockData.baseReserve = ERC20(globalData.pairs[pairId].basePool.token).balanceOf(address(this));

103:         uint256 reserveAfter = ERC20(currency).balanceOf(address(this));

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L40-L40), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L41-L41), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L103-L103)

### [G-10]<a name="g-10"></a> Use custom errors rather than `revert()/require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save [~50 gas](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://soliditylang.org/blog/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas.

*There are 23 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

182:         require(amount > 0, "AZ");

204:         require(amount > 0, "AZ");

```


*GitHub* : [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L182-L182), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L204-L204)

```solidity
📁 File: src/PriceFeed.sol

50:         require(basePrice.expo == -8, "INVALID_EXP");

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L50-L50)

```solidity
📁 File: src/libraries/ScaledAsset.sol

55:         require(_supplyTokenAmount > 0, "S3");

67:         require(getAvailableCollateralValue(tokenState) >= finalWithdrawAmount, "S0");

85:             require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");

87:             require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");

108:             require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");

118:             require(getAvailableCollateralValue(tokenStatus) >= uint256(-openAmount), "S0");

160:         require(accountState.positionAmount >= 0, "S1");

175:         require(accountState.positionAmount <= 0, "S1");

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L67-L67), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L85-L85), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L87-L87), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L108-L108), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L118-L118), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L160-L160), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L175-L175)

```solidity
📁 File: src/libraries/UniHelper.sol

84:         revert("e/empty-error");

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L84-L84)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

60:         require(pairId < Constants.MAX_PAIRS, "MAXP");

76:         require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");

153:         require(_pairs[_pairId].id == 0, "AAA");

206:         require(_fee <= 20, "FEE");

210:         require(_poolOwner != address(0), "ADDZ");

214:         require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");

216:         require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");

220:         require(
221:             _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18
222:                 && _irmParams.slope2 <= 10 * 1e18,
223:             "C4"
224:         );

```


*GitHub* : [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L60-L60), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L76-L76), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L153-L153), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L206-L206), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L210-L210), [214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L214-L214), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L216-L216), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L220-L224)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

45:         require(closeRatio > 0 && closeRatio <= 1e18, "ICR");

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L45-L45)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

64:         require(_amount > 0, "AZ");

```


*GitHub* : [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L64-L64)

```solidity
📁 File: src/tokenization/SupplyToken.sol

11:         require(_controller == msg.sender, "ST0");

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L11-L11)

### [G-11]<a name="g-11"></a> Division by powers of two should use bit shifting

`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [20 gas](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

71:         poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;

72:         poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;

```


*GitHub* : [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L72-L72)

```solidity
📁 File: src/libraries/Reallocation.sol

51:         int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;

```


*GitHub* : [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L51-L51)

### [G-12]<a name="g-12"></a> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

35:     uint256 private constant VALID_TIME_PERIOD = 5 * 60;

```


*GitHub* : [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L35-L35)

```solidity
📁 File: src/libraries/Constants.sol

32:     uint256 internal constant SQUART_KINK_UR = 10 * 1e16;

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L32-L32)

### [G-13]<a name="g-13"></a> `do-while` is cheaper than `for`-loops when the initial check can be skipped

`for (uint256 i; i < len; ++i){ ... }` -> `do { ...; ++i } while (i < len);`

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

### [G-14]<a name="g-14"></a> Duplicated `require()`/`revert()` checks should be refactored to a modifier or function to save deployment gas

This will cost more runtime gas, but will reduce deployment gas when the function is (optionally called via a modifier) called more than once as is the case for the examples below. Most projects do not make this trade-off, but it's available nonetheless.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

182:         require(amount > 0, "AZ");

204:         require(amount > 0, "AZ");

```


*GitHub* : [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L182-L182), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L204-L204)

### [G-15]<a name="g-15"></a> Counting down in `for` statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable. [More info](https://solodit.xyz/issues/g-02-counting-down-in-for-statements-is-more-gas-efficient-code4rena-pooltogether-pooltogether-git)

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

### [G-16]<a name="g-16"></a> Function names can be optimized

Function that are `public`/`external` and public state variable names can be optimized to save gas.

Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, per sorted position shifted. [Reference](https://blog.emn178.cc/en/post/solidity-gas-optimization-function-name/)

*There are 57 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

28: contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L28-L28)

```solidity
📁 File: src/PriceFeed.sol

9: contract PriceFeedFactory {

29: contract PriceFeed {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L9-L9), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L29-L29)

```solidity
📁 File: src/base/BaseHookCallback.sol

7: abstract contract BaseHookCallback is IHooks {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L7-L7)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

8: abstract contract BaseHookCallbackUpgradable is Initializable, IHooks {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L8-L8)

```solidity
📁 File: src/base/BaseMarket.sol

10: abstract contract BaseMarket is IFillerMarket, BaseHookCallback, Owned {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L10-L10)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

11: abstract contract BaseMarketUpgradable is IFillerMarket, BaseHookCallbackUpgradable {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L11-L11)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

12: library SettlementCallbackLib {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L12-L12)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

8: library ApplyInterestLib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L8-L8)

```solidity
📁 File: src/libraries/Constants.sol

4: library Constants {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L4-L4)

```solidity
📁 File: src/libraries/DataType.sol

6: library DataType {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L6-L6)

```solidity
📁 File: src/libraries/InterestRateModel.sol

8: library InterestRateModel {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L8-L8)

```solidity
📁 File: src/libraries/PairLib.sol

4: library PairLib {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L4-L4)

```solidity
📁 File: src/libraries/Perp.sol

22: library Perp {

```


*GitHub* : [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L22-L22)

```solidity
📁 File: src/libraries/PerpFee.sol

12: library PerpFee {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L12-L12)

```solidity
📁 File: src/libraries/PositionCalculator.sol

16: library PositionCalculator {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L16-L16)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

6: library PremiumCurveModel {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L6-L6)

```solidity
📁 File: src/libraries/Reallocation.sol

12: library Reallocation {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L12-L12)

```solidity
📁 File: src/libraries/ScaledAsset.sol

9: library ScaledAsset {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L9-L9)

```solidity
📁 File: src/libraries/SlippageLib.sol

9: library SlippageLib {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L9-L9)

```solidity
📁 File: src/libraries/Trade.sol

19: library Trade {

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L19-L19)

```solidity
📁 File: src/libraries/UniHelper.sol

10: library UniHelper {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L10-L10)

```solidity
📁 File: src/libraries/VaultLib.sol

8: library VaultLib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L8-L8)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

16: library AddPairLogic {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L16-L16)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

21: library LiquidationLogic {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L21-L21)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

13: library ReaderLogic {

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L13-L13)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

14: library ReallocationLogic {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L14-L14)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

14: library SupplyLogic {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L14-L14)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

15: library TradeLogic {

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L15-L15)

```solidity
📁 File: src/libraries/math/Bps.sol

4: library Bps {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L4-L4)

```solidity
📁 File: src/libraries/math/LPMath.sol

9: library LPMath {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L9-L9)

```solidity
📁 File: src/libraries/math/Math.sol

9: library Math {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L9-L9)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

5: library DecayLib {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L5-L5)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

12: library OrderInfoLib {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L12-L12)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

8: library Permit2Lib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L8-L8)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

14: library ResolvedOrderLib {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L14-L14)

```solidity
📁 File: src/markets/L2Decoder.sol

4: library L2Decoder {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L4-L4)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

4: library ArrayLib {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L4-L4)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

23: library GammaModifyInfoLib {

78: library GammaOrderLib {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L23-L23), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L78-L78)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

24: contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L24-L24)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

40: contract GammaTradeMarketL2 is GammaTradeMarket {

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L40-L40)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

11: library GammaTradeMarketLib {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L11-L11)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketWrapper.sol

10: contract GammaTradeMarketWrapper is GammaTradeMarketL2 {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketWrapper.sol#L10-L10)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

6: library L2GammaDecoder {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L6-L6)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

27: contract PerpMarket is PerpMarketV1 {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L27-L27)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

10: library PerpMarketLib {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L10-L10)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

29: contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L29-L29)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

24: library PerpOrderLib {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L24-L24)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

25: library PerpOrderV3Lib {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L25-L25)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

10: contract UniswapSettlement is ISettlement {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L10-L10)

```solidity
📁 File: src/tokenization/SupplyToken.sol

7: contract SupplyToken is ERC20, ISupplyToken {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L7-L7)

```solidity
📁 File: src/types/GlobalData.sol

12: library GlobalDataLibrary {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L12-L12)

```solidity
📁 File: src/types/LockData.sol

6: library LockDataLibrary {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/LockData.sol#L6-L6)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

4: interface AggregatorV3Interface {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L4-L4)

```solidity
📁 File: src/vendors/IPyth.sol

4: interface IPyth {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L4-L4)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

4: interface IUniswapV3PoolOracle {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L4-L4)

### [G-17]<a name="g-17"></a> Don't initialize variables with default value



*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

87:         uint256 sentMarginAmount = 0;

```


*GitHub* : [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L87-L87)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

65:         uint32 isEnabledUint = 0;

```


*GitHub* : [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L65-L65)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

117:             uint256 cost = 0;

```


*GitHub* : [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L117-L117)

### [G-18]<a name="g-18"></a> Optimize Deployment Size by Fine-tuning IPFS Hash

The Solidity compiler appends 53 bytes of metadata to the smart contract code, incurring an extra cost of 10,600 gas. This additional expense arises from 200 gas per bytecode, plus calldata cost, which amounts to 16 gas for non-zero bytes and 4 gas for zero bytes. This results in a maximum of 848 extra gas in calldata cost.

Reducing this cost is crucial for the following reasons:

The metadata's 53-byte addition leads to a deployment cost increase of 10,600 gas. It can also result in an additional calldata cost of up to 848 gas. Ways to Minimize Gas Consumption:

Employ the `--no-cbor-metadata` compiler option to exclude metadata. Be cautious as this might impact contract verification. Search for code comments that yield an IPFS hash with more zeros, thereby reducing calldata costs.

*There are 55 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L1-L1)

```solidity
📁 File: src/PriceFeed.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L1-L1)

```solidity
📁 File: src/base/BaseHookCallback.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L1-L1)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L1-L1)

```solidity
📁 File: src/base/BaseMarket.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L1-L1)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L1-L1)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L1-L1)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L1-L1)

```solidity
📁 File: src/libraries/Constants.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L1-L1)

```solidity
📁 File: src/libraries/DataType.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L1-L1)

```solidity
📁 File: src/libraries/InterestRateModel.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L1-L1)

```solidity
📁 File: src/libraries/PairLib.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L1-L1)

```solidity
📁 File: src/libraries/Perp.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L1-L1)

```solidity
📁 File: src/libraries/PerpFee.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L1-L1)

```solidity
📁 File: src/libraries/PositionCalculator.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L1-L1)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L1-L1)

```solidity
📁 File: src/libraries/Reallocation.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L1-L1)

```solidity
📁 File: src/libraries/ScaledAsset.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L1-L1)

```solidity
📁 File: src/libraries/SlippageLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L1-L1)

```solidity
📁 File: src/libraries/Trade.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L1-L1)

```solidity
📁 File: src/libraries/UniHelper.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L1-L1)

```solidity
📁 File: src/libraries/VaultLib.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L1-L1)

```solidity
📁 File: src/libraries/math/Bps.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L1-L1)

```solidity
📁 File: src/libraries/math/LPMath.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L1-L1)

```solidity
📁 File: src/libraries/math/Math.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L1-L1)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L1-L1)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L1-L1)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L1-L1)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L1-L1)

```solidity
📁 File: src/markets/L2Decoder.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketWrapper.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketWrapper.sol#L1-L1)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L1-L1)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L1-L1)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L1-L1)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L1-L1)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L1-L1)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L1-L1)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L1-L1)

```solidity
📁 File: src/tokenization/SupplyToken.sol

1: // SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L1-L1)

```solidity
📁 File: src/types/GlobalData.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L1-L1)

```solidity
📁 File: src/types/LockData.sol

1: // SPDX-License-Identifier: UNLICENSED

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/LockData.sol#L1-L1)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

1: // SPDX-License-Identifier: MIT

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L1-L1)

```solidity
📁 File: src/vendors/IPyth.sol

1: // SPDX-License-Identifier: Apache-2.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L1-L1)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

1: //SPDX-License-Identifier: agpl-3.0

```


*GitHub* : [1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L1-L1)

### [G-19]<a name="g-19"></a> Use assembly hashing

From a gas standpoint, the assembly version of the `keccak256` hashing function can be more efficient than the high-level Solidity version. This is because Solidity has additional overhead when handling function calls and memory management, which can increase the gas cost.

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

14:     bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L14-L14)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

39:     bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);

99:     bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);

```


*GitHub* : [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L39-L39), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L99-L99)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

44:     bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);

```


*GitHub* : [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L44-L44)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

46:     bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);

```


*GitHub* : [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L46-L46)

### [G-20]<a name="g-20"></a> Use `s.x = s.x + y` instead of `s.x += y` for memory structs (same for `-=` etc)

In Solidity, optimizing gas usage is crucial, particularly for frequently executed operations. For memory structs, using explicit assignment (e.g., `s.x = s.x + y`) instead of shorthand operations (e.g., `s.x += y`) can result in a minor gas saving, around 100 gas. This difference arises from the way the Solidity compiler optimizes bytecode. While such savings might seem small, they can add up in contracts with high transaction volume. This optimization applies to other compound assignment operators like `-=` and `*=` as well. It's a subtle efficiency gain that developers can leverage, especially in complex contracts where every gas unit counts

*There are 18 instance(s) of this issue:*

```solidity
📁 File: src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L29-L29)

```solidity
📁 File: src/libraries/Perp.sol

166:             _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(
167:                 _sqrtAssetStatus.rebalancePositionBase
168:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

170:             _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(
171:                 _sqrtAssetStatus.rebalancePositionQuote
172:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

498:         _userStatus.perp.amount += _updatePerpParams.tradeAmount;

501:         _userStatus.perp.entryValue += payoff.perpEntryUpdate;

502:         _userStatus.sqrtPerp.entryValue += payoff.sqrtEntryUpdate;

503:         _userStatus.sqrtPerp.quoteRebalanceEntryValue += payoff.sqrtRebalanceEntryUpdateStable;

504:         _userStatus.sqrtPerp.baseRebalanceEntryValue += payoff.sqrtRebalanceEntryUpdateUnderlying;

785:             offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;

786:             offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;

```


*GitHub* : [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L166-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L170-L172), [498](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L498-L498), [501](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L501-L501), [502](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L502-L502), [503](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L503-L503), [504](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L504-L504), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L786-L786)

```solidity
📁 File: src/libraries/PositionCalculator.sol

123:         minValue += calculateMinValue(sqrtPrice, positionParams, riskRatio);

125:         vaultValue += calculateValue(sqrtPrice, positionParams);

127:         debtValue += calculateSquartDebtValue(sqrtPrice, positionParams);

```


*GitHub* : [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L123-L123), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L127-L127)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

69:         vault.margin += tradeResult.fee + tradeResult.payoff.perpPayoff + tradeResult.payoff.sqrtPayoff;

```


*GitHub* : [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L69-L69)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

44:         globalData.vaults[tradeParams.vaultId].margin +=
45:             tradeResult.fee + tradeResult.payoff.perpPayoff + tradeResult.payoff.sqrtPayoff;

83:         globalData.vaults[tradeParams.vaultId].margin += marginAmountUpdate;

```


*GitHub* : [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L44-L45), [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L83-L83)

### [G-21]<a name="g-21"></a> Merge events to save gas

Consolidating multiple event emissions into a single event in Solidity can result in significant gas savings. Each event emission in Ethereum involves a gas cost, specifically for the topics logged with the event. By merging sequential events into a singular event, you can save on the Glogtopic cost, which is incurred for each topic of each event. This approach can save around 375 gas per additional topic. This strategy is particularly beneficial in functions where multiple related events are emitted in sequence. However, it's crucial to balance gas optimization with the clarity and utility of the event data for off-chain consumers

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

188:         emit AssetRiskParamsUpdated(_pairId, _addPairParam.assetRiskParams);

189:         emit IRMParamsUpdated(_pairId, _addPairParam.quoteIrmParams, _addPairParam.baseIrmParams);

```


*GitHub* : [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L188-L188), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L189-L189)

### [G-22]<a name="g-22"></a> Inline modifiers used only once



*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

52:     modifier onlyByLocker() {
53:         address locker = globalData.lockData.locker;
54:         if (msg.sender != locker) revert LockedBy(locker);
55:         _;
56:     }

63:     modifier onlyVaultOwner(uint256 vaultId) {
64:         if (globalData.vaults[vaultId].owner != msg.sender) revert CallerIsNotVaultOwner();
65:         _;
66:     }

```


*GitHub* : [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L52-L56), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L63-L66)

### [G-23]<a name="g-23"></a> Multiple accesses of the same mapping/array key/index should be cached

Access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's `keccak256` hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory`/`calldata`

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/base/BaseMarket.sol

// @audit '_quoteTokenMap[pairId]' also accessed on line 98
99:             _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);

```


*GitHub* : [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L99-L99)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

// @audit '_quoteTokenMap[pairId]' also accessed on line 146
147:             _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);

```


*GitHub* : [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L147-L147)

```solidity
📁 File: src/libraries/PerpFee.sol

// @audit 'rebalanceFeeGrowthCache[rebalanceId]' also accessed on line 123
128:                 assetStatus.rebalanceInterestGrowthQuote - rebalanceFeeGrowthCache[rebalanceId].stableGrowth,

```


*GitHub* : [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L128-L128)

```solidity
📁 File: src/libraries/UniHelper.sol

// @audit 'secondsAgos[0]' also accessed on line 38
58:             secondsAgos[0] = uint32(ago);

```


*GitHub* : [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L58-L58)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

155:         _pairs[_pairId] = DataType.PairStatus(

```


*GitHub* : [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L155-L155)

### [G-24]<a name="g-24"></a> Multiple mappings can be replaced with a single struct mapping

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 9 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

38:     mapping(address => bool) public allowedUniswapPools;

40:     mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L40-L40)

```solidity
📁 File: src/libraries/PerpFee.sol

18:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,

43:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,

114:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,

139:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L18-L18), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L43-L43), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L114-L114), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L139-L139)

```solidity
📁 File: src/types/GlobalData.sol

20:         mapping(uint256 => DataType.PairStatus) pairs;

21:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) rebalanceFeeGrowthCache;

22:         mapping(uint256 => DataType.Vault) vaults;

```


*GitHub* : [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L20-L20), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L21-L21), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L22-L22)

### [G-25]<a name="g-25"></a> State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the **second+** access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 9 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

// @audit 'globalData' also accessed on line 270, 276, 278
272:         if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {

// @audit 'globalData' also accessed on line 338
340:         return globalData.createOrGetVault(0, pairId);

// @audit 'globalData' also accessed on line 346
347:             globalData.pairs[pairId].isQuoteZero

// @audit 'globalData' also accessed on line 358
360:         return globalData.pairs[pairId];

// @audit 'globalData' also accessed on line 386
388:             return globalData.pairs[pairId].basePool;

```


*GitHub* : [272](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L272-L272), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L340-L340), [347](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L347-L347), [360](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L360-L360), [388](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L388-L388)

```solidity
📁 File: src/libraries/InterestRateModel.sol

// @audit '_ONE' also accessed on line 26, 29
28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28)

```solidity
📁 File: src/libraries/PositionCalculator.sol

// @audit 'RISK_RATIO_ONE' also accessed on line 193
194:         uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;

```


*GitHub* : [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L194-L194)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

// @audit '_swapRouter' also accessed on line 31
33:         amountOut = _swapRouter.exactInput(

// @audit '_swapRouter' also accessed on line 47
49:         amountIn = _swapRouter.exactOutput(

```


*GitHub* : [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L33-L33), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L49-L49)

### [G-26]<a name="g-26"></a> Use Only Named Returns

The Solidity compiler can generate more efficient bytecode when using named returns. It's recommended to replace anonymous returns with named returns for potential gas savings.

*There are 103 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

109:     function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {

337:     function createVault(uint256 pairId) external returns (uint256) {

344:     function getSqrtPrice(uint256 pairId) external view returns (uint160) {

352:     function getSqrtIndexPrice(uint256 pairId) external view returns (uint256) {

357:     function getPairStatus(uint256 pairId) external view returns (DataType.PairStatus memory) {

364:     function getVault(uint256 vaultId) external view returns (DataType.Vault memory) {

380:     function _getAssetStatusPool(uint256 pairId, bool isQuoteToken)
381:         internal
382:         view
383:         returns (Perp.AssetPoolStatus storage)
384:     {

```


*GitHub* : [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L109-L109), [337](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L337-L337), [344](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L344-L344), [352](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L352-L352), [357](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L357-L357), [364](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L364-L364), [380](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L380-L384)

```solidity
📁 File: src/PriceFeed.sol

18:     function createPriceFeed(address quotePrice, bytes32 priceId, uint256 decimalsDiff) external returns (address) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L18-L18)

```solidity
📁 File: src/base/BaseMarket.sol

47:     function execLiquidationCall(
48:         uint256 vaultId,
49:         uint256 closeRatio,
50:         IFillerMarket.SettlementParams memory settlementParams
51:     ) external returns (IPredyPool.TradeResult memory) {

55:     function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams)
56:         internal
57:         view
58:         returns (bytes memory)
59:     {

63:     function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)
64:         internal
65:         pure
66:         returns (bytes memory)
67:     {

108:     function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {

```


*GitHub* : [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L47-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L55-L59), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L63-L67), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L108-L108)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

61:     function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)
62:         internal
63:         pure
64:         returns (SettlementCallbackLib.SettlementParams memory)
65:     {

96:     function execLiquidationCall(
97:         uint256 vaultId,
98:         uint256 closeRatio,
99:         IFillerMarket.SettlementParamsV3 memory settlementParams
100:     ) external virtual returns (IPredyPool.TradeResult memory) {

105:     function _getSettlementDataFromV3(IFillerMarket.SettlementParamsV3 memory settlementParams, address filler)
106:         internal
107:         pure
108:         returns (bytes memory)
109:     {

156:     function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {

```


*GitHub* : [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L61-L65), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L96-L100), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L105-L109), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L156-L156)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

25:     function decodeParams(bytes memory settlementData) internal pure returns (SettlementParams memory) {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L25-L25)

```solidity
📁 File: src/libraries/InterestRateModel.sol

18:     function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)
19:         internal
20:         pure
21:         returns (uint256)
22:     {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L18-L22)

```solidity
📁 File: src/libraries/PairLib.sol

5:     function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L5-L5)

```solidity
📁 File: src/libraries/Perp.sol

119:     function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)
120:         internal
121:         pure
122:         returns (SqrtPerpAssetStatus memory)
123:     {

145:     function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {

202:     function reallocate(
203:         DataType.PairStatus storage _assetStatusUnderlying,
204:         SqrtPerpAssetStatus storage _sqrtAssetStatus
205:     ) internal returns (bool, bool, int256 deltaPositionBase, int256 deltaPositionQuote) {

332:     function getAvailableLiquidityAmount(
333:         address _controllerAddress,
334:         address _uniswapPool,
335:         int24 _tickLower,
336:         int24 _tickUpper
337:     ) internal view returns (uint128) {

585:     function getAvailableSqrtAmount(SqrtPerpAssetStatus memory _assetStatus, bool _isWithdraw)
586:         internal
587:         pure
588:         returns (uint256)
589:     {

604:     function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L119-L123), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L145-L145), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L202-L205), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L332-L337), [585](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L585-L589), [604](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L604-L604)

```solidity
📁 File: src/libraries/PerpFee.sol

16:     function computeUserFee(
17:         DataType.PairStatus memory assetStatus,
18:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:         Perp.UserStatus memory userStatus
20:     ) internal view returns (DataType.FeeAmount memory) {

41:     function settleUserFee(
42:         DataType.PairStatus storage assetStatus,
43:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
44:         Perp.UserStatus storage userStatus
45:     ) internal returns (DataType.FeeAmount memory) {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L16-L20), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L41-L45)

```solidity
📁 File: src/libraries/PositionCalculator.sol

102:     function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {

175:     function getHasPositionFlag(PositionParams memory _positionParams) internal pure returns (bool) {

231:     function calculateValue(uint256 _sqrtPrice, PositionParams memory _positionParams) internal pure returns (int256) {

238:     function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)
239:         internal
240:         pure
241:         returns (uint256)
242:     {

```


*GitHub* : [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L102-L102), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L175-L175), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L231-L231), [238](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L238-L242)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

14:     function calculatePremiumCurve(uint256 utilization) internal pure returns (uint256) {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L14-L14)

```solidity
📁 File: src/libraries/Reallocation.sol

92:     function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {

98:     function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)
99:         internal
100:         pure
101:         returns (bool)
102:     {

165:     function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)
166:         internal
167:         pure
168:         returns (uint160)
169:     {

179:     function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)
180:         internal
181:         pure
182:         returns (uint160)
183:     {

```


*GitHub* : [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L92-L92), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L98-L102), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L165-L169), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L179-L183)

```solidity
📁 File: src/libraries/ScaledAsset.sol

29:     function createAssetStatus() internal pure returns (AssetStatus memory) {

33:     function createUserStatus() internal pure returns (UserStatus memory) {

72:     function isSameSign(int256 a, int256 b) internal pure returns (bool) {

155:     function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)
156:         internal
157:         pure
158:         returns (uint256)
159:     {

170:     function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)
171:         internal
172:         pure
173:         returns (uint256)
174:     {

186:     function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)
187:         internal
188:         returns (uint256)
189:     {

217:     function getTotalCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {

222:     function getTotalDebtValue(AssetStatus memory tokenState) internal pure returns (uint256) {

226:     function getAvailableCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {

230:     function getUtilizationRatio(AssetStatus memory tokenState) internal pure returns (uint256) {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L33-L33), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L72-L72), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L155-L159), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L170-L174), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L186-L189), [217](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L217-L217), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L222-L222), [226](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L226-L226), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L230-L230)

```solidity
📁 File: src/libraries/Trade.sol

76:     function swap(
77:         GlobalDataLibrary.GlobalData storage globalData,
78:         uint256 pairId,
79:         SwapStableResult memory swapParams,
80:         bytes memory settlementData,
81:         uint256 sqrtPrice,
82:         uint256 vaultId
83:     ) internal returns (SwapStableResult memory) {

116:     function calculateStableAmount(uint256 currentSqrtPrice, uint256 baseAmount) internal pure returns (uint256) {

```


*GitHub* : [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L76-L83), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L116-L116)

```solidity
📁 File: src/libraries/UniHelper.sol

27:     function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {

35:     function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L35-L35)

```solidity
📁 File: src/libraries/VaultLib.sol

24:     function createOrGetVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId, uint256 pairId)
25:         internal
26:         returns (uint256)
27:     {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L24-L27)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

192:     function deploySupplyToken(address _tokenAddress) internal returns (address) {

```


*GitHub* : [192](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L192-L192)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

159:     function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)
160:         internal
161:         pure
162:         returns (uint256)
163:     {

```


*GitHub* : [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L159-L163)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

43:     function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)
44:         internal
45:         pure
46:         returns (IPredyPool.Position memory)
47:     {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L43-L47)

```solidity
📁 File: src/libraries/math/Bps.sol

7:     function upper(uint256 price, uint256 bps) internal pure returns (uint256) {

11:     function lower(uint256 price, uint256 bps) internal pure returns (uint256) {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L7-L7), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L11-L11)

```solidity
📁 File: src/libraries/math/LPMath.sol

10:     function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
11:         internal
12:         pure
13:         returns (int256)
14:     {

20:     function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
21:         internal
22:         pure
23:         returns (int256)
24:     {

30:     function calculateAmount0ForLiquidity(
31:         uint160 sqrtRatioA,
32:         uint160 sqrtRatioB,
33:         uint256 liquidityAmount,
34:         bool isRoundUp
35:     ) internal pure returns (int256) {

68:     function calculateAmount1ForLiquidity(
69:         uint160 sqrtRatioA,
70:         uint160 sqrtRatioB,
71:         uint256 liquidityAmount,
72:         bool isRoundUp
73:     ) internal pure returns (int256) {

108:     function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)
109:         internal
110:         pure
111:         returns (int256)
112:     {

119:     function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
120:         internal
121:         pure
122:         returns (uint256)
123:     {

134:     function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)
135:         internal
136:         pure
137:         returns (int256)
138:     {

145:     function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
146:         internal
147:         pure
148:         returns (uint256)
149:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L10-L14), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L20-L24), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L30-L35), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L68-L73), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L108-L112), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L119-L123), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L134-L138), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L145-L149)

```solidity
📁 File: src/libraries/math/Math.sol

12:     function abs(int256 x) internal pure returns (uint256) {

16:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

20:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

24:     function fullMulDivInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

34:     function fullMulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

44:     function mulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

54:     function addDelta(uint256 a, int256 b) internal pure returns (uint256) {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L16-L16), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L20-L20), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L24-L24), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L34-L34), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L44-L44), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L54-L54)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

18:     function hash(OrderInfo memory info) internal pure returns (bytes32) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L18-L18)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

10:     function toPermit(ResolvedOrder memory order)
11:         internal
12:         pure
13:         returns (ISignatureTransfer.PermitTransferFrom memory)
14:     {

23:     function transferDetails(ResolvedOrder memory order, address to)
24:         internal
25:         pure
26:         returns (ISignatureTransfer.SignatureTransferDetails memory)
27:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L10-L14), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L23-L27)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

20:     function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {

```


*GitHub* : [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L20-L20)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

43:     function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {

115:     function hash(GammaOrder memory order) internal pure returns (bytes32) {

134:     function resolve(GammaOrder memory gammaOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L43-L43), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L115-L115), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L134-L134)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

361:     function getUserPositions(address owner) external returns (UserPositionResult[] memory) {

```


*GitHub* : [361](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L361-L361)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

48:     function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)
49:         internal
50:         pure
51:         returns (int256)
52:     {

59:     function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)
60:         external
61:         view
62:         returns (bool, uint256 slippageTolerance, GammaTradeMarketLib.CallbackType)
63:     {

106:     function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)
107:         external
108:         view
109:         returns (bool, uint256 slippageTolerance, CallbackType)
110:     {

141:     function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)
142:         internal
143:         pure
144:         returns (uint256)
145:     {

167:     function calculateSlippageToleranceByPrice(uint256 price1, uint256 price2, AuctionParams memory auctionParams)
168:         internal
169:         pure
170:         returns (uint256)
171:     {

189:     function saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)
190:         external
191:         returns (bool)
192:     {

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L48-L52), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L59-L63), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L106-L110), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L141-L145), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L167-L171), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L189-L192)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

7:     function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)
8:         internal
9:         pure
10:         returns (GammaModifyInfo memory)
11:     {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L7-L11)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

28:     function executeOrderV3L2(
29:         PerpOrderV3L2 memory compressedOrder,
30:         bytes memory sig,
31:         SettlementParamsV3 memory settlementParams,
32:         uint64 orderId
33:     ) external nonReentrant returns (IPredyPool.TradeResult memory) {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L28-L33)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

118:     function validateLimitPrice(uint256 tradePrice, int256 tradeAmount, uint256 limitPrice)
119:         internal
120:         pure
121:         returns (bool)
122:     {

138:     function validateStopPrice(
139:         uint256 oraclePrice,
140:         uint256 tradePrice,
141:         int256 tradeAmount,
142:         uint256 stopPrice,
143:         bytes memory auctionData
144:     ) internal pure returns (bool) {

178:     function ratio(uint256 price1, uint256 price2) internal pure returns (uint256) {

188:     function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)
189:         internal
190:         view
191:         returns (bool)
192:     {

```


*GitHub* : [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L118-L122), [138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L138-L144), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L178-L178), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L188-L192)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

149:     function executeOrderV3(SignedOrder memory order, SettlementParamsV3 memory settlementParams)
150:         external
151:         nonReentrant
152:         returns (IPredyPool.TradeResult memory)
153:     {

220:     function _calculateInitialMargin(uint256 vaultId, uint256 pairId, uint256 leverage)
221:         internal
222:         view
223:         returns (int256)
224:     {

236:     function _calculateNetValue(DataType.Vault memory vault, uint256 price) internal pure returns (uint256) {

242:     function _calculatePositionValue(DataType.Vault memory vault, uint256 sqrtPrice) internal pure returns (int256) {

```


*GitHub* : [149](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L149-L153), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L220-L224), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L236-L236), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L242-L242)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

54:     function hash(PerpOrder memory order) internal pure returns (bytes32) {

73:     function resolve(PerpOrder memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L54-L54), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L73-L73)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

58:     function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {

78:     function resolve(PerpOrderV3 memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L58-L58), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L78-L78)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

5:     function decimals() external view returns (uint8);

6:     function description() external view returns (string memory);

7:     function version() external view returns (uint256);

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L7-L7)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

18:     function liquidity() external view returns (uint128);

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L18-L18)

### [G-27]<a name="g-27"></a> Nesting `if`-statements is cheaper than using `&&`

Using a double if statement instead of logical AND (&&) can provide similar short-circuiting behavior whereas double if is slightly more efficient.

*There are 31 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

272:         if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {

```


*GitHub* : [272](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L272-L272)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

33:         if (
34:             settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]

```


*GitHub* : [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L33-L34)

```solidity
📁 File: src/libraries/Perp.sol

209:         if (
210:             _sqrtAssetStatus.tickLower + _assetStatusUnderlying.riskParams.rebalanceThreshold < currentTick
211:                 && currentTick < _sqrtAssetStatus.tickUpper - _assetStatusUnderlying.riskParams.rebalanceThreshold

349:         if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {

390:         if (f0 == 0 && f1 == 0) {

593:         if (_isWithdraw && _assetStatus.borrowedAmount == 0) {

```


*GitHub* : [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L209-L211), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L349-L349), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L390-L390), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L593-L593)

```solidity
📁 File: src/libraries/PerpFee.sol

117:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

142:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

```


*GitHub* : [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L142-L142)

```solidity
📁 File: src/libraries/PositionCalculator.sol

97:         if (hasPosition && minMargin < Constants.MIN_MARGIN_AMOUNT) {

210:         if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {

215:             if (lowerPrice < minSqrtPrice && minSqrtPrice < upperPrice) {

```


*GitHub* : [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L97-L97), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L210-L210), [215](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L215-L215)

```solidity
📁 File: src/libraries/Reallocation.sol

65:                 if (lower < minLowerTick && minLowerTick < currentTick) {

78:                 if (upper > maxUpperTick && maxUpperTick >= currentTick) {

```


*GitHub* : [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L65-L65), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L78-L78)

```solidity
📁 File: src/libraries/ScaledAsset.sol

190:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

231:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

```


*GitHub* : [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L231-L231)

```solidity
📁 File: src/libraries/SlippageLib.sol

45:         if (
46:             maxAcceptableSqrtPriceRange > 0
47:                 && (
48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange
49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
50:                 )

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L45-L50)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

306:         if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {

342:         if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {

```


*GitHub* : [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L342-L342)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

64:         if (
65:             userPosition.hedgeInterval > 0
66:                 && userPosition.lastHedgedTime + userPosition.hedgeInterval <= block.timestamp

122:         if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {

130:         if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {

197:         if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {

```


*GitHub* : [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L64-L66), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L197-L197)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

38:             if (isLong && currentPositionAmount >= 0) {

42:             if (!isLong && currentPositionAmount <= 0) {

92:         if (limitPrice == 0 && stopPrice == 0) {

97:         } else if (limitPrice > 0 && stopPrice > 0) {

99:             if (
100:                 !validateLimitPrice(tradePrice, tradeAmount, limitPrice)
101:                     && !validateStopPrice(oraclePrice, tradePrice, tradeAmount, stopPrice, auctionData)

127:         if (tradeAmount > 0 && limitPrice < tradePrice) {

131:         if (tradeAmount < 0 && limitPrice > tradePrice) {

200:             if (tradeAmount > 0 && decayedPrice < tradePrice) {

204:             if (tradeAmount < 0 && decayedPrice > tradePrice) {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L38-L38), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L42-L42), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L97-L97), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L99-L101), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L131-L131), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L204-L204)

### [G-28]<a name="g-28"></a> Consider using OpenZeppelin's `EnumerateSet` instead of nested mappings

Nested mappings and multi-dimensional arrays in Solidity operate through a process of double hashing, wherein the original storage slot and the first key are concatenated and hashed, and then this hash is again concatenated with the second key and hashed. This process can be quite gas expensive due to the double-hashing operation and subsequent storage operation (sstore).
OpenZeppelin's `EnumerableSet` provides a potential solution to this problem. It creates a data structure that combines the benefits of set operations with the ability to enumerate stored elements, which is not natively available in Solidity. EnumerableSet handles the element uniqueness internally and can therefore provide a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays in certain scenarios.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

40:     mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L40-L40)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

68:     mapping(address owner => mapping(uint256 pairId => UserPosition)) public userPositions;

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L68-L68)

### [G-29]<a name="g-29"></a> Shorten the array rather than copying to a new one

Leveraging inline assembly in Solidity provides the ability to directly manipulate the length slot of an array, thereby altering its length without the need to copy the elements to a new array of the desired size. This technique is more gas-efficient as it avoids the computational expense associated with array duplication. However, this method circumvents the type-checking and safety mechanisms of high-level Solidity and should be used judiciously. Always ensure that the array doesn't contain vital data beyond the revised length, as this data will become unreachable yet still consume storage space.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/libraries/UniHelper.sol

36:         uint32[] memory secondsAgos = new uint32[](2);

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L36-L36)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

364:         UserPositionResult[] memory results = new UserPositionResult[](userPositionIDs.length);

```


*GitHub* : [364](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L364-L364)

### [G-30]<a name="g-30"></a> Only emit event in setter function if the state variable was changed

Emitting events in setter functions of smart contracts only when state variables change saves gas. This is because emitting events consumes gas, and unnecessary events, where no actual state change occurs, lead to wasteful consumption.

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

125:         emit AssetRiskParamsUpdated(_pairStatus.id, _riskParams);

```


*GitHub* : [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L125-L125)

### [G-31]<a name="g-31"></a> Operator `>=`/`<=` costs less gas than operator `>`/`<`

The compiler uses opcodes `GT` and `ISZERO` for code that uses `>`, but only requires `LT` for `>=`. A similar behavior applies for `>`, which uses opcodes `LT` and `ISZERO`, but only requires `GT` for `<=`. It can [save 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde) for each. It should be converted to the `<=`/`>=` equivalent when comparing against integer literals.

*There are 138 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

84:         if (amount0 > 0) {

87:         if (amount1 > 0) {

182:         require(amount > 0, "AZ");

186:         if (amount > 0) {

204:         require(amount > 0, "AZ");

208:         if (amount > 0) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L87-L87), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L182-L182), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L186-L186), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L204-L204), [208](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L208-L208)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

72:         if (fee < settlementParamsV3.minFee) {

83:             baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,

```


*GitHub* : [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L72-L72), [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L83-L83)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

47:         if (settlementParams.fee < 0) {

55:         if (settlementParams.fee > 0) {

67:         if (baseAmountDelta > 0) {

77:         } else if (baseAmountDelta < 0) {

127:             if (quoteAmount > quoteAmountFromUni) {

129:             } else if (quoteAmountFromUni > quoteAmount) {

174:             if (quoteAmount > quoteAmountToUni) {

176:             } else if (quoteAmountToUni > quoteAmount) {

```


*GitHub* : [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L47-L47), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L67-L67), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L77-L77), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L127-L127), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L129-L129), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L174-L174), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L176-L176)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

45:         if (interestRateQuote > 0 || interestRateBase > 0) {

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L45-L45)

```solidity
📁 File: src/libraries/Perp.sol

165:         if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {

210:             _sqrtAssetStatus.tickLower + _assetStatusUnderlying.riskParams.rebalanceThreshold < currentTick

211:                 && currentTick < _sqrtAssetStatus.tickUpper - _assetStatusUnderlying.riskParams.rebalanceThreshold

236:         if (currentTick < _sqrtAssetStatus.tickLower) {

240:         } else if (currentTick < _sqrtAssetStatus.tickUpper) {

443:         if (_tradeSqrtAmount > 0) {

450:         } else if (_tradeSqrtAmount < 0) {

551:         if (closeAmount > 0) {

553:         } else if (closeAmount < 0) {

554:             if (getAvailableSqrtAmount(_assetStatus, true) < uint256(-closeAmount)) {

560:         if (openAmount > 0) {

565:         } else if (openAmount < 0) {

566:             if (getAvailableSqrtAmount(_assetStatus, false) < uint256(-openAmount)) {

611:         if (utilization > 1e18) {

646:             _userStatus.sqrtPerp.amount < 0

653:             _userStatus.sqrtPerp.amount < 0

659:         if (_userStatus.sqrtPerp.amount < 0) {

723:         if (_assetStatus.totalAmount - _assetStatus.borrowedAmount < _liquidityAmount) {

768:             offsetUnderlying = LPMath.calculateAmount0OffsetWithTick(_tickUpper, openAmount.abs(), openAmount < 0);

771:             offsetStable = LPMath.calculateAmount1OffsetWithTick(_tickLower, openAmount.abs(), openAmount < 0);

773:             if (openAmount < 0) {

```


*GitHub* : [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L165-L165), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L210-L210), [211](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L211-L211), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L236-L236), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L240-L240), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L443-L443), [450](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L450-L450), [551](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L551-L551), [553](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L553-L553), [554](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L554-L554), [560](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L560-L560), [565](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L565-L565), [566](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L566-L566), [611](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L611-L611), [646](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L646-L646), [653](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L653-L653), [659](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L659-L659), [723](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L723-L723), [768](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L768-L768), [771](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L771-L771), [773](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L773-L773)

```solidity
📁 File: src/libraries/PerpFee.sol

73:         if (sqrtPerp.amount > 0) {

76:         } else if (sqrtPerp.amount < 0) {

101:         if (sqrtPerp.amount > 0) {

104:         } else if (sqrtPerp.amount < 0) {

117:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

142:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L73-L73), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L76-L76), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L101-L101), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L104-L104), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L142-L142)

```solidity
📁 File: src/libraries/PositionCalculator.sol

97:         if (hasPosition && minMargin < Constants.MIN_MARGIN_AMOUNT) {

198:             if (v < minValue) {

205:             if (v < minValue) {

210:         if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {

215:             if (lowerPrice < minSqrtPrice && minSqrtPrice < upperPrice) {

218:                 if (v < minValue) {

245:         if (squartPosition > 0) {

```


*GitHub* : [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L97-L97), [198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L198-L198), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L205-L205), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L210-L210), [215](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L215-L215), [218](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L218-L218), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L245-L245)

```solidity
📁 File: src/libraries/Reallocation.sol

55:         if (availableAmount > 0) {

56:             if (currentTick < previousCenterTick) {

65:                 if (lower < minLowerTick && minLowerTick < currentTick) {

78:                 if (upper > maxUpperTick && maxUpperTick >= currentTick) {

103:         return (sqrtAssetStatus.tickLower <= currentTick && currentTick < sqrtAssetStatus.tickUpper);

110:         require(tickSpacing > 0);

114:         if (result < TickMath.MIN_TICK) {

116:         } else if (result > TickMath.MAX_TICK) {

139:         if (minLowerTick > currentLowerTick - tickSpacing) {

160:         if (maxUpperTick < currentUpperTick + tickSpacing) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L55-L55), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L56-L56), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L65-L65), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L78-L78), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L103-L103), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L110-L110), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L114-L114), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L116-L116), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L139-L139), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L160-L160)

```solidity
📁 File: src/libraries/ScaledAsset.sol

55:         require(_supplyTokenAmount > 0, "S3");

59:         if (_supplyTokenAmount < burnAmount) {

73:         return (a >= 0 && b >= 0) || (a < 0 && b < 0);

84:         if (userStatus.positionAmount > 0) {

86:         } else if (userStatus.positionAmount < 0) {

104:         if (closeAmount > 0) {

106:         } else if (closeAmount < 0) {

113:         if (openAmount > 0) {

117:         } else if (openAmount < 0) {

135:         if (_userStatus.positionAmount > 0) {

148:         if (_userStatus.positionAmount > 0) {

239:         if (utilization > 1e18) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L55-L55), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L59-L59), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L73-L73), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L86-L86), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L106-L106), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L113-L113), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L117-L117), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L135-L135), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L148-L148), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L239-L239)

```solidity
📁 File: src/libraries/SlippageLib.sol

33:         if (tradeResult.averagePrice > 0) {

35:             if (basePrice.lower(slippageTolerance) > uint256(tradeResult.averagePrice)) {

38:         } else if (tradeResult.averagePrice < 0) {

40:             if (basePrice.upper(slippageTolerance) < uint256(-tradeResult.averagePrice)) {

46:             maxAcceptableSqrtPriceRange > 0

48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange

49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice

```


*GitHub* : [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L35-L35), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L40-L40), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L49-L49)

```solidity
📁 File: src/libraries/UniHelper.sol

78:         if (errMsg.length > 0) {

135:                 if (tickCurrent < tickUpper) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L78-L78), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L135-L135)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

60:         require(pairId < Constants.MAX_PAIRS, "MAXP");

```


*GitHub* : [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L60-L60)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

92:             if (remainingMargin > 0) {

101:             } else if (remainingMargin < 0) {

170:         if (ratio > 1e4) {

```


*GitHub* : [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L92-L92), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L101-L101), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L170-L170)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

60:                 if (exceedsQuote < 0) {

68:                 if (exceedsQuote > 0) {

```


*GitHub* : [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L60-L60), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L68-L68)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

64:         require(_amount > 0, "AZ");

```


*GitHub* : [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L64-L64)

```solidity
📁 File: src/libraries/math/LPMath.sol

40:         bool swaped = sqrtRatioA > sqrtRatioB;

42:         if (sqrtRatioA > sqrtRatioB) (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

78:         bool swaped = sqrtRatioA < sqrtRatioB;

80:         if (sqrtRatioA < sqrtRatioB) (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L40-L40), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L42-L42), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L80-L80)

```solidity
📁 File: src/libraries/math/Math.sol

17:         return a > b ? a : b;

21:         return a > b ? b : a;

27:         } else if (x > 0) {

37:         } else if (x > 0) {

47:         } else if (x > 0) {

```


*GitHub* : [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L17-L17), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L21-L21), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L27-L27), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L47-L47)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

21:         if (decayEndTime < decayStartTime) {

31:             if (endPrice < startPrice) {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L21-L21), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L31-L31)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

28:         if (block.timestamp > resolvedOrder.info.deadline) {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L28-L28)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

135:         uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;

```


*GitHub* : [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L135-L135)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

117:                 if (marginAmountUpdate > 0) {

119:                 } else if (marginAmountUpdate < 0) {

178:         if (tradeResult.minMargin > 0) {

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L117-L117), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L119-L119), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L178-L178), [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

65:             userPosition.hedgeInterval > 0

122:         if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {

130:         if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {

152:         if (elapsed > Bps.ONE) {

178:         if (ratio > auctionParams.auctionRange) {

197:         if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {

```


*GitHub* : [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L65-L65), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L152-L152), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L178-L178), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L197-L197)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

54:             if (currentPositionAmount > 0) {

55:                 if (tradeAmount < 0) {

56:                     if (currentPositionAmount > -tradeAmount) {

65:                 if (tradeAmount > 0) {

66:                     if (-currentPositionAmount > tradeAmount) {

97:         } else if (limitPrice > 0 && stopPrice > 0) {

105:         } else if (limitPrice > 0) {

110:         } else if (stopPrice > 0) {

127:         if (tradeAmount > 0 && limitPrice < tradePrice) {

131:         if (tradeAmount < 0 && limitPrice > tradePrice) {

155:         if (tradeAmount > 0) {

157:             if (stopPrice > oraclePrice) {

161:             if (tradePrice > Bps.upper(oraclePrice, decayedSlippageTorelance)) {

164:         } else if (tradeAmount < 0) {

166:             if (stopPrice < oraclePrice) {

170:             if (tradePrice < Bps.lower(oraclePrice, decayedSlippageTorelance)) {

181:         } else if (price1 > price2) {

200:             if (tradeAmount > 0 && decayedPrice < tradePrice) {

204:             if (tradeAmount < 0 && decayedPrice > tradePrice) {

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L55-L55), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L56-L56), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L65-L65), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L66-L66), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L97-L97), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L105-L105), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L110-L110), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L131-L131), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L155-L155), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L157-L157), [161](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L161-L161), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L164-L164), [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L166-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L170-L170), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L181-L181), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L204-L204)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

119:             if (marginAmountUpdate > 0) {

125:             if (marginAmountUpdate > 0) {

127:             } else if (marginAmountUpdate < 0) {

200:         if (tradeResult.minMargin > 0) {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L119-L119), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L127-L127), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L200-L200)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

74:         uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;

```


*GitHub* : [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L74-L74)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

53:         if (amountInMaximum > amountIn) {

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L53-L53)

### [G-32]<a name="g-32"></a> Functions that revert when called by normal users can be marked as `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are: `CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2)` which cost an average of about 21 gas per call to the function, in addition to the extra deployment cost.

*There are 26 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

97:     function setOperator(address newOperator) external onlyOperator {

109:     function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {

119:     function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)
120:         external
121:         onlyPoolOwner(pairId)

133:     function updateIRMParams(
134:         uint256 pairId,
135:         InterestRateModel.IRMParams memory quoteIrmParams,
136:         InterestRateModel.IRMParams memory baseIrmParams
137:     ) external onlyPoolOwner(pairId) {

147:     function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {

157:     function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {

167:     function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {

177:     function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {

199:     function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {

286:     function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {

300:     function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {

329:     function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {

```


*GitHub* : [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L97-L97), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L109-L109), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L119-L121), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L133-L137), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L147-L147), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L157-L157), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L167-L167), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L177-L177), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L199-L199), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L286-L286), [300](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L300-L300), [329](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L329-L329)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

20:     function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {

```


*GitHub* : [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L20-L20)

```solidity
📁 File: src/base/BaseMarket.sol

28:     function predySettlementCallback(
29:         address quoteToken,
30:         address baseToken,
31:         bytes memory settlementData,
32:         int256 baseAmountDelta
33:     ) external onlyPredyPool {

84:     function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {

92:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L28-L33), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L92-L92)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

38:     function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
39:         internal
40:         onlyInitializing

49:     function predySettlementCallback(
50:         address quoteToken,
51:         address baseToken,
52:         bytes memory settlementData,
53:         int256 baseAmountDelta
54:     ) external onlyPredyPool {

128:     function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {

132:     function updateQuoter(address newQuoter) external onlyFiller {

140:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L38-L40), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L49-L54), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L128-L128), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L132-L132), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L140-L140)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

79:     function predyTradeAfterCallback(
80:         IPredyPool.TradeParams memory tradeParams,
81:         IPredyPool.TradeResult memory tradeResult
82:     ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

392:     function removePosition(uint256 positionId) external onlyFiller {

```


*GitHub* : [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L79-L82), [392](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L392-L392)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

102:     function predyTradeAfterCallback(
103:         IPredyPool.TradeParams memory tradeParams,
104:         IPredyPool.TradeResult memory tradeResult
105:     ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

```


*GitHub* : [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L102-L105)

```solidity
📁 File: src/tokenization/SupplyToken.sol

21:     function mint(address account, uint256 amount) external virtual override onlyController {

25:     function burn(address account, uint256 amount) external virtual override onlyController {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L25-L25)

### [G-33]<a name="g-33"></a> Using `++i/--i` instead of `i++/i--` can save gas

*Saves 5 gas per instance*

*There are 5 instance(s) of this issue:*

```solidity
📁 File: src/libraries/Perp.sol

818:         sqrtPerpStatus.numRebalance++;

```


*GitHub* : [818](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L818-L818)

```solidity
📁 File: src/libraries/VaultLib.sol

42:             globalData.vaultCount++;

```


*GitHub* : [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L42-L42)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

91:         _global.pairsCount++;

```


*GitHub* : [91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L91-L91)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

23:         for (uint256 i = 0; i < items.length; i++) {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L23-L23)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366)

### [G-34]<a name="g-34"></a> Using `private` for constants saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 39 instance(s) of this issue:*

```solidity
📁 File: src/libraries/Constants.sol

5:     uint256 internal constant ONE = 1e18;

7:     uint256 internal constant MAX_VAULTS = 18446744073709551616;

8:     uint256 internal constant MAX_PAIRS = 18446744073709551616;

11:     int256 internal constant MIN_MARGIN_AMOUNT = 1e6;

13:     uint256 internal constant MIN_LIQUIDITY = 100;

15:     uint256 internal constant MIN_SQRT_PRICE = 79228162514264337593;

16:     uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;

18:     uint8 internal constant RESOLUTION = 96;

19:     uint256 internal constant Q96 = 0x1000000000000000000000000;

20:     uint256 internal constant Q128 = 0x100000000000000000000000000000000;

23:     uint256 internal constant BASE_MIN_COLLATERAL_WITH_DEBT = 1000;

25:     uint256 internal constant BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE = 12422;

27:     uint256 internal constant MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE = 24710;

29:     uint256 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;

32:     uint256 internal constant SQUART_KINK_UR = 10 * 1e16;

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L5-L5), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L8-L8), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L11-L11), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L16-L16), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L20-L20), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L23-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L25-L25), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L27-L27), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L29-L29), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L32-L32)

```solidity
📁 File: src/libraries/PositionCalculator.sol

22:     uint256 internal constant RISK_RATIO_ONE = 1e8;

```


*GitHub* : [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L22-L22)

```solidity
📁 File: src/libraries/SlippageLib.sol

13:     uint256 public constant MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 100747209;

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L13-L13)

```solidity
📁 File: src/libraries/UniHelper.sol

11:     uint256 internal constant _ORACLE_PERIOD = 30 minutes;

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L11-L11)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

27:     uint256 constant _MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 101488915;

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L27-L27)

```solidity
📁 File: src/libraries/math/Bps.sol

5:     uint32 public constant ONE = 1e6;

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L5-L5)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

13:     bytes internal constant ORDER_INFO_TYPE = "OrderInfo(address market,address trader,uint256 nonce,uint256 deadline)";

14:     bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L14-L14)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

24:     bytes internal constant GAMMA_MODIFY_INFO_TYPE = abi.encodePacked(
25:         "GammaModifyInfo(",
26:         "bool isEnabled,",
27:         "uint64 expiration,",
28:         "int64 maximaDeviation,",
29:         "uint256 lowerLimit,",
30:         "uint256 upperLimit,",
31:         "uint32 hedgeInterval,",
32:         "uint32 sqrtPriceTrigger,",
33:         "uint32 minSlippageTolerance,",
34:         "uint32 maxSlippageTolerance,",
35:         "uint16 auctionPeriod,",
36:         "uint32 auctionRange)"
37:     );

39:     bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);

81:     bytes internal constant GAMMA_ORDER_TYPE = abi.encodePacked(
82:         "GammaOrder(",
83:         "OrderInfo info,",
84:         "uint64 pairId,",
85:         "uint256 positionId,",
86:         "address entryTokenAddress,",
87:         "int256 quantity,",
88:         "int256 quantitySqrt,",
89:         "int256 marginAmount,",
90:         "uint256 baseSqrtPrice,",
91:         "uint32 slippageTolerance,",
92:         "uint8 leverage,",
93:         "GammaModifyInfo modifyInfo)"
94:     );

97:     bytes internal constant ORDER_TYPE =
98:         abi.encodePacked(GAMMA_ORDER_TYPE, GammaModifyInfoLib.GAMMA_MODIFY_INFO_TYPE, OrderInfoLib.ORDER_INFO_TYPE);

99:     bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);

101:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

102:     string internal constant PERMIT2_ORDER_TYPE = string(
103:         abi.encodePacked(
104:             "GammaOrder witness)",
105:             GammaModifyInfoLib.GAMMA_MODIFY_INFO_TYPE,
106:             GAMMA_ORDER_TYPE,
107:             OrderInfoLib.ORDER_INFO_TYPE,
108:             TOKEN_PERMISSIONS_TYPE
109:         )
110:     );

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L24-L37), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L39-L39), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L81-L94), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L97-L98), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L99-L99), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L101-L101), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L102-L110)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

27:     bytes internal constant PERP_ORDER_TYPE = abi.encodePacked(
28:         "PerpOrder(",
29:         "OrderInfo info,",
30:         "uint64 pairId,",
31:         "address entryTokenAddress,",
32:         "int256 tradeAmount,",
33:         "int256 marginAmount,",
34:         "uint256 takeProfitPrice,",
35:         "uint256 stopLossPrice,",
36:         "uint64 slippageTolerance,",
37:         "uint8 leverage,",
38:         "address validatorAddress,",
39:         "bytes validationData)"
40:     );

43:     bytes internal constant ORDER_TYPE = abi.encodePacked(PERP_ORDER_TYPE, OrderInfoLib.ORDER_INFO_TYPE);

44:     bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);

46:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

47:     string internal constant PERMIT2_ORDER_TYPE = string(
48:         abi.encodePacked("PerpOrder witness)", OrderInfoLib.ORDER_INFO_TYPE, PERP_ORDER_TYPE, TOKEN_PERMISSIONS_TYPE)
49:     );

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L27-L40), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L43-L43), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L47-L49)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

28:     bytes internal constant PERP_ORDER_V3_TYPE = abi.encodePacked(
29:         "PerpOrderV3(",
30:         "OrderInfo info,",
31:         "uint64 pairId,",
32:         "address entryTokenAddress,",
33:         "string side,",
34:         "uint256 quantity,",
35:         "uint256 marginAmount,",
36:         "uint256 limitPrice,",
37:         "uint256 stopPrice,",
38:         "uint8 leverage,",
39:         "bool reduceOnly,",
40:         "bool closePosition,",
41:         "bytes auctionData)"
42:     );

45:     bytes internal constant ORDER_V3_TYPE = abi.encodePacked(PERP_ORDER_V3_TYPE, OrderInfoLib.ORDER_INFO_TYPE);

46:     bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);

48:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

49:     string internal constant PERMIT2_ORDER_TYPE = string(
50:         abi.encodePacked(
51:             "PerpOrderV3 witness)", OrderInfoLib.ORDER_INFO_TYPE, PERP_ORDER_V3_TYPE, TOKEN_PERMISSIONS_TYPE
52:         )
53:     );

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L28-L42), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L45-L45), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L49-L53)

### [G-35]<a name="g-35"></a> Refactor modifiers to call a local function

Modifiers code is copied in all instances where it's used, increasing bytecode size. By doing a refractor to the internal function, one can reduce bytecode size significantly at the cost of one JUMP.

*There are 8 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

47:     modifier onlyOperator() {
48:         if (operator != msg.sender) revert CallerIsNotOperator();
49:         _;
50:     }

52:     modifier onlyByLocker() {
53:         address locker = globalData.lockData.locker;
54:         if (msg.sender != locker) revert LockedBy(locker);
55:         _;
56:     }

58:     modifier onlyPoolOwner(uint256 pairId) {
59:         if (globalData.pairs[pairId].poolOwner != msg.sender) revert CallerIsNotPoolCreator();
60:         _;
61:     }

63:     modifier onlyVaultOwner(uint256 vaultId) {
64:         if (globalData.vaults[vaultId].owner != msg.sender) revert CallerIsNotVaultOwner();
65:         _;
66:     }

```


*GitHub* : [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L47-L50), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L52-L56), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L58-L61), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L63-L66)

```solidity
📁 File: src/base/BaseHookCallback.sol

16:     modifier onlyPredyPool() {
17:         if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();
18:         _;
19:     }

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L16-L19)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

15:     modifier onlyPredyPool() {
16:         if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();
17:         _;
18:     }

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L15-L18)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

31:     modifier onlyFiller() {
32:         if (msg.sender != whitelistFiller) revert CallerIsNotFiller();
33:         _;
34:     }

```


*GitHub* : [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L31-L34)

```solidity
📁 File: src/tokenization/SupplyToken.sol

10:     modifier onlyController() {
11:         require(_controller == msg.sender, "ST0");
12:         _;
13:     }

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L10-L13)

### [G-36]<a name="g-36"></a> Default bool values are manually reset

Using .delete is better than resetting a Solidity variable to its default value manually because it frees up storage space on the Ethereum blockchain, resulting in gas cost savings.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/libraries/Perp.sol

242:             isOutOfRange = false;

```


*GitHub* : [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L242-L242)

```solidity
📁 File: src/markets/L2Decoder.sol

29:             isLimit = false;

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L29-L29)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

81:             isEnabled = false;

```


*GitHub* : [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L81-L81)

### [G-37]<a name="g-37"></a> Use assembly scratch space for building calldata for external calls

If an external call's calldata can fit into two or fewer words, use the scratch space to build the calldata, rather than allowing Solidity to do a memory expansion.

*There are 183 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

72:         AddPairLogic.initializeGlobalData(globalData, uniswapFactory);

85:             ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);

88:             ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);

123:         AddPairLogic.updateAssetRiskParams(globalData.pairs[pairId], riskParams);

138:         AddPairLogic.updateIRMParams(globalData.pairs[pairId], quoteIrmParams, baseIrmParams);

148:         AddPairLogic.updateFeeRatio(globalData.pairs[pairId], feeRatio);

158:         AddPairLogic.updatePoolOwner(globalData.pairs[pairId], poolOwner);

168:         AddPairLogic.updatePriceOracle(globalData.pairs[pairId], priceFeed);

187:             ERC20(pool.token).safeTransfer(msg.sender, amount);

209:             ERC20(pool.token).safeTransfer(msg.sender, amount);

270:         globalData.validate(tradeParams.pairId);

330:         globalData.take(isQuoteAsset, to, amount);

338:         globalData.validate(pairId);

358:         globalData.validate(pairId);

371:         ReaderLogic.getPairStatus(globalData, pairId);

377:         ReaderLogic.getVaultStatus(globalData, vaultId);

```


*GitHub* : [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L72-L72), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L85-L85), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L88-L88), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L123-L123), [138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L138-L138), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L148-L148), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L158-L158), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L168-L168), [187](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L187-L187), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L209-L209), [270](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L270-L270), [330](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L330-L330), [338](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L338-L338), [358](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L358-L358), [371](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L371-L371), [377](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L377-L377)

```solidity
📁 File: src/PriceFeed.sol

46:         (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

```


*GitHub* : [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L46-L46)

```solidity
📁 File: src/base/BaseMarket.sol

36:         SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);

37:         SettlementCallbackLib.execSettlement(_predyPool, quoteToken, baseToken, settlementParams, baseAmountDelta);

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L36-L36), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L37-L37)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

57:         SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);

58:         SettlementCallbackLib.execSettlement(_predyPool, quoteToken, baseToken, settlementParams, baseAmountDelta);

68:         uint256 tradeAmountAbs = Math.abs(baseAmountDelta);

```


*GitHub* : [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L57-L57), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L58-L58), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L68-L68)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

48:             ERC20(quoteToken).safeTransferFrom(
49:                 settlementParams.sender, address(predyPool), uint256(-settlementParams.fee)
50:             );

56:             predyPool.take(true, settlementParams.sender, uint256(settlementParams.fee));

103:             predyPool.take(false, sender, sellAmount);

105:             ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);

110:         predyPool.take(false, address(this), sellAmount);

111:         ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);

113:         uint256 quoteAmountFromUni = ISettlement(settlementParams.contractAddress).swapExactIn(
114:             quoteToken,
115:             baseToken,
116:             settlementParams.encodedData,
117:             sellAmount,
118:             settlementParams.maxQuoteAmount,
119:             address(this)
120:         );

123:             ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);

128:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);

130:                 ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);

133:             ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);

150:             predyPool.take(true, sender, quoteAmount);

152:             ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);

157:         predyPool.take(true, address(this), settlementParams.maxQuoteAmount);

158:         ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);

160:         uint256 quoteAmountToUni = ISettlement(settlementParams.contractAddress).swapExactOut(
161:             quoteToken,
162:             baseToken,
163:             settlementParams.encodedData,
164:             buyAmount,
165:             settlementParams.maxQuoteAmount,
166:             address(predyPool)
167:         );

170:             ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);

175:                 ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);

177:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);

180:             ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L48-L50), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L56-L56), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L103-L103), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L105-L105), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L110-L110), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L111-L111), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L113-L120), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L123-L123), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L128-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L133-L133), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L150-L150), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L152-L152), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L157-L157), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L158-L158), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L160-L167), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L175-L175), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L177-L177), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L180-L180)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

29:         Perp.updateFeeAndPremiumGrowth(pairId, pairStatus.sqrtAssetStatus);

58:         uint256 utilizationRatio = poolStatus.tokenStatus.getUtilizationRatio();

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L29-L29), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L58-L58)

```solidity
📁 File: src/libraries/Perp.sol

206:         (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();

268:         (uint256 receivedAmount0, uint256 receivedAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).burn(
269:             _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount
270:         );

272:         IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).collect(
273:             address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, type(uint128).max, type(uint128).max
274:         );

279:         (uint256 requiredAmount0, uint256 requiredAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).mint(
280:             address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""
281:         );

311:         uint160 tickSqrtPrice = TickMath.getSqrtRatioAtTick(_tick);

314:         int256 deltaPosition0 =
315:             LPMath.calculateAmount0ForLiquidity(_currentSqrtPrice, tickSqrtPrice, _totalLiquidityAmount, true);

318:         int256 deltaPosition1 =
319:             LPMath.calculateAmount1ForLiquidity(_currentSqrtPrice, tickSqrtPrice, _totalLiquidityAmount, true);

338:         bytes32 positionKey = PositionKey.compute(_controllerAddress, _tickLower, _tickUpper);

340:         (uint128 liquidity,,,,) = IUniswapV3Pool(_uniswapPool).positions(positionKey);

358:         _pairStatus.basePool.tokenStatus.updatePosition(
359:             _pairStatus.sqrtAssetStatus.rebalancePositionBase, -deltaPositionUnderlying, _pairStatus.id, false
360:         );

361:         _pairStatus.quotePool.tokenStatus.updatePosition(
362:             _pairStatus.sqrtAssetStatus.rebalancePositionQuote, -deltaPositionStable, _pairStatus.id, true
363:         );

365:         _pairStatus.basePool.tokenStatus.updatePosition(
366:             _userStatus.basePosition, deltaPositionUnderlying, _pairStatus.id, false
367:         );

368:         _pairStatus.quotePool.tokenStatus.updatePosition(
369:             _userStatus.stablePosition, deltaPositionStable, _pairStatus.id, true
370:         );

378:         (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128) =
379:             UniHelper.getFeeGrowthInside(_assetStatus.uniswapPool, _assetStatus.tickLower, _assetStatus.tickUpper);

396:         uint256 spreadParam = PremiumCurveModel.calculatePremiumCurve(utilization);

415:         (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128) =
416:             UniHelper.getFeeGrowthInside(_assetStatus.uniswapPool, _assetStatus.tickLower, _assetStatus.tickUpper);

511:         _pairStatus.basePool.tokenStatus.updatePosition(
512:             _userStatus.basePosition,
513:             _updatePerpParams.tradeAmount + payoff.sqrtRebalanceEntryUpdateUnderlying,
514:             _pairStatus.id,
515:             false
516:         );

518:         _pairStatus.quotePool.tokenStatus.updatePosition(
519:             _userStatus.stablePosition,
520:             payoff.perpEntryUpdate + payoff.sqrtEntryUpdate + payoff.sqrtRebalanceEntryUpdateStable,
521:             _pairStatus.id,
522:             true
523:         );

642:         int256 deltaPosition0 = LPMath.calculateAmount0ForLiquidityWithTicks(
643:             _assetStatus.tickUpper,
644:             _userStatus.rebalanceLastTickUpper,
645:             _userStatus.sqrtPerp.amount.abs(),
646:             _userStatus.sqrtPerp.amount < 0
647:         );

649:         int256 deltaPosition1 = LPMath.calculateAmount1ForLiquidityWithTicks(
650:             _assetStatus.tickLower,
651:             _userStatus.rebalanceLastTickLower,
652:             _userStatus.sqrtPerp.amount.abs(),
653:             _userStatus.sqrtPerp.amount < 0
654:         );

711:         (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).mint(
712:             address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""
713:         );

727:         (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).burn(
728:             _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128()
729:         );

732:         IUniswapV3Pool(_assetStatus.uniswapPool).collect(
733:             address(this), _assetStatus.tickLower, _assetStatus.tickUpper, type(uint128).max, type(uint128).max
734:         );

798:             _pairStatus.quotePool.tokenStatus.updatePosition(
799:                 sqrtAsset.rebalancePositionQuote, _updateAmount0, _pairStatus.id, true
800:             );

801:             _pairStatus.basePool.tokenStatus.updatePosition(
802:                 sqrtAsset.rebalancePositionBase, _updateAmount1, _pairStatus.id, false
803:             );

805:             _pairStatus.basePool.tokenStatus.updatePosition(
806:                 sqrtAsset.rebalancePositionBase, _updateAmount0, _pairStatus.id, false
807:             );

808:             _pairStatus.quotePool.tokenStatus.updatePosition(
809:                 sqrtAsset.rebalancePositionQuote, _updateAmount1, _pairStatus.id, true
810:             );

```


*GitHub* : [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L206-L206), [268](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L268-L270), [272](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L272-L274), [279](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L279-L281), [311](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L311-L311), [314](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L314-L315), [318](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L318-L319), [338](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L338-L338), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L340-L340), [358](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L358-L360), [361](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L361-L363), [365](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L365-L367), [368](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L368-L370), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L378-L379), [396](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L396-L396), [415](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L415-L416), [511](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L511-L516), [518](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L518-L523), [642](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L642-L647), [649](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L649-L654), [711](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L711-L713), [727](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L727-L729), [732](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L732-L734), [798](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L798-L800), [801](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L801-L803), [805](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L805-L807), [808](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L808-L810)

```solidity
📁 File: src/libraries/PerpFee.sol

21:         int256 FeeAmountUnderlying = assetStatus.basePool.tokenStatus.computeUserFee(userStatus.basePosition);

22:         int256 FeeAmountStable = assetStatus.quotePool.tokenStatus.computeUserFee(userStatus.stablePosition);

47:         int256 totalFeeUnderlying = assetStatus.basePool.tokenStatus.settleUserFee(userStatus.basePosition);

48:         int256 totalFeeStable = assetStatus.quotePool.tokenStatus.settleUserFee(userStatus.stablePosition);

83:         int256 fee0 = Math.fullMulDivDownInt256(sqrtPerp.amount, growthDiff0, Constants.Q128);

84:         int256 fee1 = Math.fullMulDivDownInt256(sqrtPerp.amount, growthDiff1, Constants.Q128);

118:             uint256 rebalanceId = PairLib.getRebalanceCacheId(pairId, userStatus.lastNumRebalance);

120:             uint256 rebalanceAmount = Math.abs(userStatus.sqrtPerp.amount);

146:             uint256 rebalanceAmount = Math.abs(userStatus.sqrtPerp.amount);

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L21-L21), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L22-L22), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L47-L47), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L48-L48), [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L83-L83), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L84-L84), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L118-L118), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L120-L120), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L146-L146)

```solidity
📁 File: src/libraries/PositionCalculator.sol

232:         uint256 price = Math.calSqrtPriceToPrice(_sqrtPrice);

```


*GitHub* : [232](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L232-L232)

```solidity
📁 File: src/libraries/Reallocation.sol

23:         int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();

93:         (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L23-L23), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L93-L93)

```solidity
📁 File: src/libraries/ScaledAsset.sol

57:         uint256 burnAmount = FixedPointMathLib.mulDivDown(_amount, Constants.ONE, tokenState.assetScaler);

194:         uint256 protocolFee = FixedPointMathLib.mulDivDown(
195:             FixedPointMathLib.mulDivDown(_interestRate, getTotalDebtValue(tokenState), Constants.ONE),
196:             _reserveFactor,
197:             100
198:         );

201:         uint256 supplyInterestRate = FixedPointMathLib.mulDivDown(
202:             FixedPointMathLib.mulDivDown(
203:                 _interestRate, getTotalDebtValue(tokenState), getTotalCollateralValue(tokenState)
204:             ),
205:             100 - _reserveFactor,
206:             100
207:         );

235:         uint256 utilization = FixedPointMathLib.mulDivDown(
236:             getTotalDebtValue(tokenState), Constants.ONE, getTotalCollateralValue(tokenState)
237:         );

```


*GitHub* : [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L57-L57), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L194-L198), [201](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L201-L207), [235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L235-L237)

```solidity
📁 File: src/libraries/SlippageLib.sol

27:         uint256 basePrice = Math.calSqrtPriceToPrice(sqrtBasePrice);

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L27-L27)

```solidity
📁 File: src/libraries/Trade.sol

45:         (int256 underlyingAmountForSqrt, int256 stableAmountForSqrt) = Perp.computeRequiredAmounts(
46:             pairStatus.sqrtAssetStatus, pairStatus.isQuoteZero, openPosition, tradeParams.tradeAmountSqrt
47:         );

87:             int256 amountQuote = calculateStableAmount(sqrtPrice, 1e18).toInt256();

92:         globalData.initializeLock(pairId);

94:         globalData.callSettlementCallback(settlementData, totalBaseAmount);

96:         (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();

142:         Perp.settleUserBalance(_pairStatus, _userStatus);

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L45-L47), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L87-L87), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L92-L92), [94](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L94-L94), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L96-L96), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L142-L142)

```solidity
📁 File: src/libraries/UniHelper.sol

41:         (bool success, bytes memory data) =
42:             address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));

49:             (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();

51:             (uint32 oldestAvailableAge,,, bool initialized) = uniswapPool.observations((index + 1) % cardinality);

72:         uint160 sqrtPriceX96 = TickMath.getSqrtRatioAtTick(tick);

92:         bytes32 positionKey = PositionKey.compute(address(this), tickLower, tickUpper);

104:         (, int24 tickCurrent,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();

106:         uint256 feeGrowthGlobal0X128 = IUniswapV3Pool(uniswapPoolAddress).feeGrowthGlobal0X128();

107:         uint256 feeGrowthGlobal1X128 = IUniswapV3Pool(uniswapPoolAddress).feeGrowthGlobal1X128();

115:                 (,, uint256 lowerFeeGrowthOutside0X128, uint256 lowerFeeGrowthOutside1X128,,,,) =
116:                     IUniswapV3Pool(uniswapPoolAddress).ticks(tickLower);

132:                 (,, uint256 upperFeeGrowthOutside0X128, uint256 upperFeeGrowthOutside1X128,,,,) =
133:                     IUniswapV3Pool(uniswapPoolAddress).ticks(tickUpper);

```


*GitHub* : [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L41-L42), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L51-L51), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L72-L72), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L92-L92), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L106-L106), [107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L107-L107), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L115-L116), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L132-L133)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

50:         ApplyInterestLib.applyInterestForToken(globalData.pairs, vault.openPosition.pairId);

53:         Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);

80:         SlippageLib.checkPrice(
81:             sqrtOraclePrice,
82:             tradeResult,
83:             slippageTolerance,
84:             tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE
85:         );

99:                     ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);

106:                 ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L50-L50), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L53-L53), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L80-L85), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L99-L99), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L106-L106)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

17:         ApplyInterestLib.applyInterestForToken(globalData.pairs, pairId);

28:         ApplyInterestLib.applyInterestForToken(globalData.pairs, vault.openPosition.pairId);

30:         Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);

35:         (int256 minMargin, int256 vaultValue,, uint256 oraclePice) =
36:             PositionCalculator.calculateMinMargin(pairStatus, vault, feeAmount);

```


*GitHub* : [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L17-L17), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L30-L30), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L35-L36)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

32:         globalData.validate(pairId);

35:         ApplyInterestLib.applyInterestForToken(globalData.pairs, pairId);

40:         Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);

52:                 globalData.initializeLock(pairId);

54:                 globalData.callSettlementCallback(settlementData, deltaPositionBase);

56:                 (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();

69:                     ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));

91:             Perp.finalizeReallocation(pairStatus.sqrtAssetStatus);

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L32-L32), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L35-L35), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L40-L40), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L52-L52), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L54-L54), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L56-L56), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L69-L69), [91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L91-L91)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

27:         globalData.validate(_pairId);

33:         ApplyInterestLib.applyInterestForToken(globalData.pairs, _pairId);

52:         ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);

54:         ISupplyToken(_pool.supplyTokenAddress).mint(msg.sender, mintAmount);

62:         globalData.validate(_pairId);

66:         ApplyInterestLib.applyInterestForToken(globalData.pairs, _pairId);

88:         ISupplyToken(supplyTokenAddress).burn(msg.sender, finalburntAmount);

90:         ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L27-L27), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L33-L33), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L52-L52), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L54-L54), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L62-L62), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L66-L66), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L88-L88), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L90-L90)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

37:         ApplyInterestLib.applyInterestForToken(globalData.pairs, tradeParams.pairId);

40:         Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);

73:         globalData.initializeLock(tradeParams.pairId);

75:         IHooks(msg.sender).predyTradeAfterCallback(tradeParams, tradeResult);

77:         (int256 marginAmountUpdate, int256 settledBaseAmount) = globalData.finalizeLock();

```


*GitHub* : [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L37-L37), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L40-L40), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L73-L73), [75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L75-L75), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L77-L77)

```solidity
📁 File: src/libraries/math/LPMath.sol

50:             uint256 r0 = FullMath.mulDivRoundingUp(numerator, FixedPoint96.Q96, sqrtRatioA);

56:             uint256 r1 = FullMath.mulDivRoundingUp(numerator, FixedPoint96.Q96, sqrtRatioB);

87:             uint256 r0 = FullMath.mulDivRoundingUp(liquidityAmount, sqrtRatioA, FixedPoint96.Q96);

93:             uint256 r1 = FullMath.mulDivRoundingUp(liquidityAmount, sqrtRatioB, FixedPoint96.Q96);

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L50-L50), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L56-L56), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L87-L87), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L93-L93)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

96:                 _predyPool.take(true, callbackData.trader, uint256(vault.margin));

118:                     quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));

120:                     _predyPool.take(true, callbackData.trader, uint256(-marginAmountUpdate));

200:             _predyPool.updateRecepient(tradeResult.vaultId, gammaOrder.info.trader);

203:         SlippageLib.checkPrice(
204:             gammaOrder.baseSqrtPrice,
205:             tradeResult,
206:             gammaOrder.slippageTolerance,
207:             SlippageLib.MAX_ACCEPTABLE_SQRT_PRICE_RANGE
208:         );

237:         uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);

240:         (bool hedgeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =
241:             GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);

252:         int256 delta = GammaTradeMarketLib.calculateDelta(
253:             sqrtPrice, userPosition.maximaDeviation, vault.openPosition.sqrtPerp.amount, vault.openPosition.perp.amount
254:         );

271:         SlippageLib.checkPrice(sqrtPrice, tradeResult, slippageTorelance, SlippageLib.MAX_ACCEPTABLE_SQRT_PRICE_RANGE);

286:         uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);

288:         (bool closeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =
289:             GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);

312:         SlippageLib.checkPrice(sqrtPrice, tradeResult, slippageTorelance, SlippageLib.MAX_ACCEPTABLE_SQRT_PRICE_RANGE);

317:         _predyPool.trade(
318:             IPredyPool.TradeParams(
319:                 gammaOrder.pairId,
320:                 gammaOrder.positionId,
321:                 gammaOrder.quantity,
322:                 gammaOrder.quantitySqrt,
323:                 abi.encode(
324:                     CallbackData(
325:                         GammaTradeMarketLib.CallbackType.QUOTE, gammaOrder.info.trader, gammaOrder.marginAmount
326:                     )
327:                 )
328:             ),
329:             _getSettlementDataFromV3(settlementParams, msg.sender)
330:         );

346:         uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);

389:         positionIDs[trader].addItem(newPositionId);

405:         positionIDs[trader].removeItem(positionId);

439:         if (GammaTradeMarketLib.saveUserPosition(userPosition, modifyInfo)) {

445:         order.validate();

447:         _permit2.permitWitnessTransferFrom(
448:             order.toPermit(),
449:             order.transferDetails(address(this)),
450:             order.info.trader,
451:             order.hash,
452:             GammaOrderLib.PERMIT2_ORDER_TYPE,
453:             order.sig
454:         );

```


*GitHub* : [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L96-L96), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L118-L118), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L120-L120), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L200-L200), [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L203-L208), [237](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L237-L237), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L240-L241), [252](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L252-L254), [271](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L271-L271), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L286-L286), [288](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L288-L289), [312](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L312-L312), [317](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L317-L330), [346](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L346-L346), [389](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L389-L389), [405](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L405-L405), [439](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L439-L439), [445](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L445-L445), [447](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L447-L454)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

50:         (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =
51:             L2GammaDecoder.decodeGammaParam(order.param);

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L50-L51)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

34:         (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =
35:             L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L34-L35)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

90:         uint256 oraclePrice = Math.calSqrtPriceToPrice(tradeResult.sqrtTwap);

147:         uint256 decayedSlippageTorelance = DecayLib.decay2(
148:             auctionParams.startPrice,
149:             auctionParams.endPrice,
150:             auctionParams.startTime,
151:             auctionParams.endTime,
152:             ratio(oraclePrice, stopPrice)
153:         );

195:         uint256 decayedPrice = DecayLib.decay(
196:             auctionParams.startPrice, auctionParams.endPrice, auctionParams.startTime, auctionParams.endTime
197:         );

```


*GitHub* : [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L90-L90), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L147-L153), [195](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L195-L197)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

126:                 quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));

128:                 _predyPool.take(true, callbackData.trader, uint256(-marginAmountUpdate));

173:         int256 tradeAmount = PerpMarketLib.getFinalTradeAmount(
174:             _predyPool.getVault(userPosition.vaultId).openPosition.perp.amount,
175:             perpOrder.side,
176:             perpOrder.quantity,
177:             perpOrder.reduceOnly,
178:             perpOrder.closePosition
179:         );

210:             _predyPool.updateRecepient(tradeResult.vaultId, perpOrder.info.trader);

213:         PerpMarketLib.validateTrade(
214:             tradeResult, tradeAmount, perpOrder.limitPrice, perpOrder.stopPrice, perpOrder.auctionData
215:         );

227:         uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(pairId);

229:         uint256 price = Math.calSqrtPriceToPrice(sqrtPrice);

282:         int256 tradeAmount = PerpMarketLib.getFinalTradeAmount(
283:             _predyPool.getVault(userPosition.vaultId).openPosition.perp.amount,
284:             perpOrder.side,
285:             perpOrder.quantity,
286:             perpOrder.reduceOnly,
287:             perpOrder.closePosition
288:         );

293:         _predyPool.trade(
294:             IPredyPool.TradeParams(
295:                 perpOrder.pairId,
296:                 userPosition.vaultId,
297:                 tradeAmount,
298:                 0,
299:                 abi.encode(
300:                     CallbackData(
301:                         CallbackSource.QUOTE3,
302:                         perpOrder.info.trader,
303:                         0,
304:                         perpOrder.leverage,
305:                         PerpOrderV3Lib.resolve(perpOrder, bytes("")),
306:                         0
307:                     )
308:                 )
309:             ),
310:             _getSettlementDataFromV3(settlementParams, filler)
311:         );

315:         order.validate();

317:         _permit2.permitWitnessTransferFrom(
318:             order.toPermit(),
319:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),
320:             order.info.trader,
321:             order.hash,
322:             PerpOrderLib.PERMIT2_ORDER_TYPE,
323:             order.sig
324:         );

328:         order.validate();

330:         _permit2.permitWitnessTransferFrom(
331:             order.toPermit(),
332:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),
333:             order.info.trader,
334:             order.hash,
335:             PerpOrderV3Lib.PERMIT2_ORDER_TYPE,
336:             order.sig
337:         );

```


*GitHub* : [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L126-L126), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L128-L128), [173](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L173-L179), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L210-L210), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L213-L215), [227](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L227-L227), [229](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L229-L229), [282](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L282-L288), [293](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L293-L311), [315](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L315-L315), [317](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L317-L324), [328](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L328-L328), [330](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L330-L337)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

30:         ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);

31:         ERC20(baseToken).approve(address(_swapRouter), amountIn);

46:         ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);

47:         ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);

54:             ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L30-L30), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L31-L31), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L47-L47), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L54-L54)

```solidity
📁 File: src/types/GlobalData.sol

53:         IHooks(msg.sender).predySettlementCallback(
54:             globalData.pairs[pairId].quotePool.token,
55:             globalData.pairs[pairId].basePool.token,
56:             settlementData,
57:             deltaBaseAmount
58:         );

85:         ERC20(currency).safeTransfer(to, amount);

103:         uint256 reserveAfter = ERC20(currency).balanceOf(address(this));

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L53-L58), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L85-L85), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L103-L103)

### [G-38]<a name="g-38"></a> Use shift right/left instead of division if possible

While the `DIV` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*There are 63 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

54:         uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);

55:         price = price * Constants.Q96 / _decimalsDiff;

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L55-L55)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

70:         uint256 fee = settlementParamsV3.feePrice * tradeAmountAbs / Constants.Q96;

76:         uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

77:         uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

```


*GitHub* : [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L77-L77)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

101:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

125:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

148:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

172:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L101-L101), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L125-L125), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L148-L148), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L172-L172)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

66:         interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)
67:             * (block.timestamp - lastUpdateTimestamp) / 365 days;

71:         poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;

72:         poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;

```


*GitHub* : [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L66-L67), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L72-L72)

```solidity
📁 File: src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L29-L29)

```solidity
📁 File: src/libraries/Perp.sol

166:             _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(
167:                 _sqrtAssetStatus.rebalancePositionBase
168:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

170:             _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(
171:                 _sqrtAssetStatus.rebalancePositionQuote
172:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

399:             f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

402:             f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

590:         uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);

609:         uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;

689:                 int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;

697:                 int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;

785:             offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;

786:             offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;

```


*GitHub* : [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L166-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L170-L172), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L402-L402), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L590-L590), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L609-L609), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L697-L697), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L786-L786)

```solidity
📁 File: src/libraries/PositionCalculator.sol

93:             (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;

193:         uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;

194:         uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;

213:                 (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);

```


*GitHub* : [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L93-L93), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L213-L213)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

21:         return (1600 * b * b / Constants.ONE) / Constants.ONE;

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L21-L21)

```solidity
📁 File: src/libraries/Reallocation.sol

51:         int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;

120:         result = (result / tickSpacing) * tickSpacing;

170:         uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();

184:         uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;

190:         uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));

```


*GitHub* : [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L51-L51), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L190-L190)

```solidity
📁 File: src/libraries/SlippageLib.sol

48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange

49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L49-L49)

```solidity
📁 File: src/libraries/Trade.sol

128:         swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;

129:         swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;

132:         swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();

```


*GitHub* : [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L132-L132)

```solidity
📁 File: src/libraries/UniHelper.sol

29:             return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);

70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L70-L70)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

62:             -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,

63:             -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,

168:         uint256 ratio = uint256(vaultValue * 1e4 / minMargin);

174:         return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);

```


*GitHub* : [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L63-L63), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L168-L168), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L174-L174)

```solidity
📁 File: src/libraries/math/Bps.sol

8:         return price * bps / ONE;

12:         return price * ONE / bps;

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L12-L12)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

32:                 decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;

34:                 decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L34-L34)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

53:         int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;

56:         return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;

84:         uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;

85:         uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;

150:         uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;

158:                 + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE

176:         uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);

184:                 + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)
185:                     / auctionParams.auctionRange

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L184-L185)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

87:         uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)
88:             * Constants.Q96 / Math.abs(tradeAmount);

182:             return (price1 - price2) * Bps.ONE / price2;

184:             return (price2 - price1) * Bps.ONE / price2;

```


*GitHub* : [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L87-L88), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L184-L184)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

233:         return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);

239:         return Math.abs(positionAmount) * price / Constants.Q96;

```


*GitHub* : [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L239-L239)

### [G-39]<a name="g-39"></a> Usage of smaller `uint`/`int` types causes overhead

When using a smaller int/uint type it first needs to be converted to it's 258 bit counterpart to be operated, this increases the gass cost and thus should be avoided. However it does make sense to use smaller int/uint values within structs provided you pack the struct properly.

*There are 124 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

147:     function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {

344:     function getSqrtPrice(uint256 pairId) external view returns (uint160) {

```


*GitHub* : [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L147-L147), [344](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L344-L344)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

50:     function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L50-L50)

```solidity
📁 File: src/libraries/Constants.sol

18:     uint8 internal constant RESOLUTION = 96;

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L18-L18)

```solidity
📁 File: src/libraries/PairLib.sol

5:     function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L5-L5)

```solidity
📁 File: src/libraries/Perp.sol

145:     function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {

206:         (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();

219:         uint128 totalLiquidityAmount = getAvailableLiquidityAmount(

233:         int24 tick;

265:         int24 _currentTick,

266:         uint128 _totalLiquidityAmount

307:         uint160 _currentSqrtPrice,

308:         int24 _tick,

309:         uint128 _totalLiquidityAmount

311:         uint160 tickSqrtPrice = TickMath.getSqrtRatioAtTick(_tick);

335:         int24 _tickLower,

336:         int24 _tickUpper

337:     ) internal view returns (uint128) {

340:         (uint128 liquidity,,,,) = IUniswapV3Pool(_uniswapPool).positions(positionKey);

747:         int24 _tickLower,

748:         int24 _tickUpper,

```


*GitHub* : [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L145-L145), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L206-L206), [219](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L219-L219), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L233-L233), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L265-L265), [266](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L266-L266), [307](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L307-L307), [308](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L308-L308), [309](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L309-L309), [311](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L311-L311), [335](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L335-L335), [336](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L336-L336), [337](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L337-L337), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L340-L340), [747](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L747-L747), [748](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L748-L748)

```solidity
📁 File: src/libraries/PositionCalculator.sol

102:     function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {

```


*GitHub* : [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L102-L102)

```solidity
📁 File: src/libraries/Reallocation.sol

18:     function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)

21:         returns (int24 lower, int24 upper)

23:         int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();

43:         int24 currentTick,

44:         int24 tickSpacing

45:     ) internal pure returns (int24 lower, int24 upper) {

51:         int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;

58:                 int24 minLowerTick = calculateMinLowerTick(

71:                 int24 maxUpperTick = calculateMaxUpperTick(

93:         (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();

98:     function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)

109:     function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {

127:         int24 currentLowerTick,

130:         int24 tickSpacing

131:     ) internal pure returns (int24 minLowerTick) {

132:         uint160 sqrtPrice =

148:         int24 currentUpperTick,

151:         int24 tickSpacing

152:     ) internal pure returns (int24 maxUpperTick) {

153:         uint160 sqrtPrice =

165:     function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)

168:         returns (uint160)

170:         uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();

179:     function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)

182:         returns (uint160)

190:         uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L18-L18), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L21-L21), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L23-L23), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L43-L43), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L45-L45), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L51-L51), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L58-L58), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L71-L71), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L93-L93), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L98-L98), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L109-L109), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L127-L127), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L130-L130), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L131-L131), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L132-L132), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L148-L148), [151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L151-L151), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L152-L152), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L153-L153), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L165-L165), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L168-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L170-L170), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L179-L179), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L182-L182), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L190-L190)

```solidity
📁 File: src/libraries/ScaledAsset.sol

186:     function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)

```


*GitHub* : [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L186-L186)

```solidity
📁 File: src/libraries/UniHelper.sol

13:     function getSqrtPrice(address uniswapPoolAddress) internal view returns (uint160 sqrtPrice) {

20:     function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {

27:     function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {

35:     function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {

49:             (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();

51:             (uint32 oldestAvailableAge,,, bool initialized) = uniswapPool.observations((index + 1) % cardinality);

70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

72:         uint160 sqrtPriceX96 = TickMath.getSqrtRatioAtTick(tick);

87:     function getFeeGrowthInsideLast(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)

99:     function getFeeGrowthInside(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)

104:         (, int24 tickCurrent,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L13-L13), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L20-L20), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L35-L35), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L51-L51), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L72-L72), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L87-L87), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L99-L99), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L104-L104)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

36:     event FeeRatioUpdated(uint256 pairId, uint8 feeRatio);

96:     function updateFeeRatio(DataType.PairStatus storage _pairStatus, uint8 _feeRatio) external {

205:     function validateFeeRatio(uint8 _fee) internal pure {

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L36-L36), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L96-L96), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L205-L205)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

21:         int24 tickLower,

22:         int24 tickUpper,

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L21-L21), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L22-L22)

```solidity
📁 File: src/libraries/math/Bps.sol

5:     uint32 public constant ONE = 1e6;

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L5-L5)

```solidity
📁 File: src/libraries/math/LPMath.sol

10:     function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)

20:     function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)

31:         uint160 sqrtRatioA,

32:         uint160 sqrtRatioB,

69:         uint160 sqrtRatioA,

70:         uint160 sqrtRatioB,

108:     function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)

119:     function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)

134:     function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)

145:     function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L10-L10), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L20-L20), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L31-L31), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L32-L32), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L69-L69), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L70-L70), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L108-L108), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L119-L119), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L134-L134), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L145-L145)

```solidity
📁 File: src/markets/L2Decoder.sol

10:             uint64 startTime,

11:             uint64 endTime,

12:             uint64 deadline,

13:             uint128 startAmount,

14:             uint128 endAmount

17:         uint32 isLimitUint;

41:         returns (uint64 deadline, uint64 pairId, uint8 leverage)

53:         returns (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side)

55:         uint8 reduceOnlyUint;

56:         uint8 closePositionUint;

57:         uint8 sideUint;

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L14-L14), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L17-L17), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L41-L41), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L53-L53), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L55-L55), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L56-L56), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L57-L57)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

366:         for (uint64 i = 0; i < userPositionIDs.length; i++) {

408:     function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)

```


*GitHub* : [366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L366-L366), [408](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L408-L408)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

50:         (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =

77:         uint64 pairId = userPositions[order.positionId].pairId;

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L50-L50), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L77-L77)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

7:     function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)

14:             uint64 expiration,

15:             uint32 hedgeInterval,

16:             uint32 sqrtPriceTrigger,

17:             uint32 minSlippageTolerance,

18:             uint32 maxSlippageTolerance,

19:             uint16 auctionPeriod,

20:             uint32 auctionRange

41:         returns (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage)

56:             uint64 expiration,

57:             uint32 hedgeInterval,

58:             uint32 sqrtPriceTrigger,

59:             uint32 minSlippageTolerance,

60:             uint32 maxSlippageTolerance,

61:             uint16 auctionPeriod,

62:             uint32 auctionRange

65:         uint32 isEnabledUint = 0;

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L14-L14), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L16-L16), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L17-L17), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L20-L20), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L41-L41), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L56-L56), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L57-L57), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L58-L58), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L59-L59), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L60-L60), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L61-L61), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L62-L62), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L65-L65)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

32:         uint64 orderId

34:         (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L34-L34)

```solidity
📁 File: src/tokenization/SupplyToken.sol

15:     constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L15-L15)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

5:     function decimals() external view returns (uint8);

8:     function getRoundData(uint80 _roundId)

11:         returns (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound);

15:         returns (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound);

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L5-L5), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L8-L8), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L11-L11), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L15-L15)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

9:             uint160 sqrtPriceX96,

10:             int24 tick,

11:             uint16 observationIndex,

12:             uint16 observationCardinality,

13:             uint16 observationCardinalityNext,

14:             uint8 feeProtocol,

18:     function liquidity() external view returns (uint128);

28:         returns (uint32 blockTimestamp, int56 tickCumulative, uint160 liquidityCumulative, bool initialized);

30:     function increaseObservationCardinalityNext(uint16 observationCardinalityNext) external;

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L14-L14), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L18-L18), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L30-L30)

### [G-40]<a name="g-40"></a> Consider using solady's 'FixedPointMathLib'

Using Solady's 'FixedPointMathLib' for multiplication or division operations in Solidity can lead to significant gas savings. This library is designed to optimize fixed-point arithmetic operations, which are common in financial calculations involving tokens or currencies. By implementing more efficient algorithms and assembly optimizations, 'FixedPointMathLib' minimizes the computational resources required for these operations. For contracts that frequently perform such calculations, integrating this library can reduce transaction costs, thereby enhancing overall performance and cost-effectiveness. However, developers must ensure compatibility with their existing codebase and thoroughly test for accuracy and expected behavior to avoid any unintended consequences.

*There are 76 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

35:     uint256 private constant VALID_TIME_PERIOD = 5 * 60;

54:         uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);

55:         price = price * Constants.Q96 / _decimalsDiff;

```


*GitHub* : [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L35-L35), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L55-L55)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

70:         uint256 fee = settlementParamsV3.feePrice * tradeAmountAbs / Constants.Q96;

76:         uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

77:         uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

```


*GitHub* : [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L77-L77)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

101:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

125:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

148:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

172:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L101-L101), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L125-L125), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L148-L148), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L172-L172)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

66:         interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)
67:             * (block.timestamp - lastUpdateTimestamp) / 365 days;

71:         poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;

72:         poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;

```


*GitHub* : [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L66-L67), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L72-L72)

```solidity
📁 File: src/libraries/Constants.sol

32:     uint256 internal constant SQUART_KINK_UR = 10 * 1e16;

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L32-L32)

```solidity
📁 File: src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L29-L29)

```solidity
📁 File: src/libraries/PairLib.sol

6:         return pairId * type(uint64).max + rebalanceId;

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L6-L6)

```solidity
📁 File: src/libraries/Perp.sol

166:             _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(
167:                 _sqrtAssetStatus.rebalancePositionBase
168:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

170:             _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(
171:                 _sqrtAssetStatus.rebalancePositionQuote
172:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

399:             f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

402:             f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

535:         if (_userStatus.sqrtPerp.amount * _amount >= 0) {

590:         uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);

609:         uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;

682:         if (_positionAmount * _tradeAmount >= 0) {

689:                 int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;

697:                 int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;

755:         if (_userStatus.sqrtPerp.amount * _tradeSqrtAmount >= 0) {

785:             offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;

786:             offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;

```


*GitHub* : [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L166-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L170-L172), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L402-L402), [535](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L535-L535), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L590-L590), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L609-L609), [682](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L682-L682), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L697-L697), [755](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L755-L755), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L786-L786)

```solidity
📁 File: src/libraries/PositionCalculator.sol

93:             (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;

193:         uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;

194:         uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;

213:                 (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);

235:             + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;

249:         return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);

```


*GitHub* : [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L93-L93), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L213-L213), [235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L235-L235), [249](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L249-L249)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

21:         return (1600 * b * b / Constants.ONE) / Constants.ONE;

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L21-L21)

```solidity
📁 File: src/libraries/Reallocation.sol

51:         int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;

67:                     upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;

80:                     lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;

120:         result = (result / tickSpacing) * tickSpacing;

170:         uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();

184:         uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;

190:         uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));

```


*GitHub* : [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L51-L51), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L67-L67), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L80-L80), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L190-L190)

```solidity
📁 File: src/libraries/SlippageLib.sol

48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange

49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L49-L49)

```solidity
📁 File: src/libraries/Trade.sol

103:         if (settledQuoteAmount * totalBaseAmount <= 0) {

117:         uint256 quoteAmount = (currentSqrtPrice * baseAmount) >> Constants.RESOLUTION;

119:         return (quoteAmount * currentSqrtPrice) >> Constants.RESOLUTION;

128:         swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;

129:         swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;

132:         swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();

```


*GitHub* : [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L103-L103), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L117-L117), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L119-L119), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L132-L132)

```solidity
📁 File: src/libraries/UniHelper.sol

29:             return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);

70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L70-L70)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

62:             -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,

63:             -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,

168:         uint256 ratio = uint256(vaultValue * 1e4 / minMargin);

174:         return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);

```


*GitHub* : [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L63-L63), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L168-L168), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L174-L174)

```solidity
📁 File: src/libraries/math/Bps.sol

8:         return price * bps / ONE;

12:         return price * ONE / bps;

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L12-L12)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

32:                 decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;

34:                 decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L34-L34)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

53:         int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;

56:         return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;

84:         uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;

85:         uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;

150:         uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;

158:                 + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE

176:         uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);

184:                 + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)
185:                     / auctionParams.auctionRange

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L184-L185)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

87:         uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)
88:             * Constants.Q96 / Math.abs(tradeAmount);

182:             return (price1 - price2) * Bps.ONE / price2;

184:             return (price2 - price1) * Bps.ONE / price2;

```


*GitHub* : [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L87-L88), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L184-L184)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

233:         return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);

239:         return Math.abs(positionAmount) * price / Constants.Q96;

```


*GitHub* : [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L239-L239)

### [G-41]<a name="g-41"></a> Newer versions of solidity are more gas efficient

he solidity language continues to pursue more efficient gas optimization schemes. Adopting a [newer version of solidity](https://github.com/ethereum/solc-js/tags) can be more gas efficient.

*There are 55 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L2-L2)

```solidity
📁 File: src/PriceFeed.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L2-L2)

```solidity
📁 File: src/base/BaseHookCallback.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L2-L2)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L2-L2)

```solidity
📁 File: src/base/BaseMarket.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L2-L2)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L2-L2)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L2-L2)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L2-L2)

```solidity
📁 File: src/libraries/Constants.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L2-L2)

```solidity
📁 File: src/libraries/DataType.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L2-L2)

```solidity
📁 File: src/libraries/InterestRateModel.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L2-L2)

```solidity
📁 File: src/libraries/PairLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L2-L2)

```solidity
📁 File: src/libraries/Perp.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L2-L2)

```solidity
📁 File: src/libraries/PerpFee.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L2-L2)

```solidity
📁 File: src/libraries/PositionCalculator.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L2-L2)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L2-L2)

```solidity
📁 File: src/libraries/Reallocation.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L2-L2)

```solidity
📁 File: src/libraries/ScaledAsset.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L2-L2)

```solidity
📁 File: src/libraries/SlippageLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L2-L2)

```solidity
📁 File: src/libraries/Trade.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L2-L2)

```solidity
📁 File: src/libraries/UniHelper.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L2-L2)

```solidity
📁 File: src/libraries/VaultLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L2-L2)

```solidity
📁 File: src/libraries/math/Bps.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L2-L2)

```solidity
📁 File: src/libraries/math/LPMath.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L2-L2)

```solidity
📁 File: src/libraries/math/Math.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L2-L2)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L2-L2)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L2-L2)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L2-L2)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L2-L2)

```solidity
📁 File: src/markets/L2Decoder.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketWrapper.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketWrapper.sol#L2-L2)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L2-L2)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L2-L2)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L2-L2)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L2-L2)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L2-L2)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L2-L2)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L2-L2)

```solidity
📁 File: src/tokenization/SupplyToken.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L2-L2)

```solidity
📁 File: src/types/GlobalData.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L2-L2)

```solidity
📁 File: src/types/LockData.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/LockData.sol#L2-L2)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

2: pragma solidity ^0.8.0;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L2-L2)

```solidity
📁 File: src/vendors/IPyth.sol

2: pragma solidity ^0.8.0;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L2-L2)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

2: pragma solidity ^0.8.17;

```


*GitHub* : [2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L2-L2)

### [G-42]<a name="g-42"></a> Splitting `require()` statements that use `&&` saves gas



*There are 8 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

52:         require(quoteAnswer > 0 && basePrice.price > 0);

```


*GitHub* : [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L52-L52)

```solidity
📁 File: src/base/BaseMarket.sol

105:         require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);

```


*GitHub* : [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L105-L105)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

153:         require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);

```


*GitHub* : [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L153-L153)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

214:         require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");

216:         require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");

220:         require(
221:             _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18
222:                 && _irmParams.slope2 <= 10 * 1e18,
223:             "C4"
224:         );

```


*GitHub* : [214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L214-L214), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L216-L216), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L220-L224)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

45:         require(closeRatio > 0 && closeRatio <= 1e18, "ICR");

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L45-L45)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

203:         require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);

```


*GitHub* : [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L203-L203)

### [G-43]<a name="g-43"></a> `address(this)` can be stored in state variable that will ultimately cost less, than every time calculating this, specifically when it used multiple times in a contract



*There are 26 instance(s) of this issue:*

```solidity
📁 File: src/base/SettlementCallbackLib.sol

110:         predyPool.take(false, address(this), sellAmount);

119:             address(this)

128:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);

157:         predyPool.take(true, address(this), settlementParams.maxQuoteAmount);

177:                 ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);

```


*GitHub* : [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L110-L110), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L119-L119), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L128-L128), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L157-L157), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L177-L177)

```solidity
📁 File: src/libraries/Perp.sol

220:             address(this), _sqrtAssetStatus.uniswapPool, _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper

273:             address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, type(uint128).max, type(uint128).max

280:             address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""

712:             address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""

733:             address(this), _assetStatus.tickLower, _assetStatus.tickUpper, type(uint128).max, type(uint128).max

```


*GitHub* : [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L220-L220), [273](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L273-L273), [280](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L280-L280), [712](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L712-L712), [733](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L733-L733)

```solidity
📁 File: src/libraries/UniHelper.sol

92:         bytes32 positionKey = PositionKey.compute(address(this), tickLower, tickUpper);

```


*GitHub* : [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L92-L92)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

197:                 address(this),

```


*GitHub* : [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L197-L197)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

106:                 ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));

```


*GitHub* : [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L106-L106)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

52:         ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);

```


*GitHub* : [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L52-L52)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

24:         if (address(this) != address(resolvedOrder.info.market)) {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L24-L24)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

449:             order.transferDetails(address(this)),

```


*GitHub* : [449](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L449-L449)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

55:                 OrderInfo(address(this), order.trader, order.nonce, deadline),

81:                 OrderInfo(address(this), order.trader, order.nonce, order.deadline),

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L55-L55), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L81-L81)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

38:             info: OrderInfo(address(this), compressedOrder.trader, compressedOrder.nonce, deadline),

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L38-L38)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

319:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),

332:             ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),

```


*GitHub* : [319](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L319-L319), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L332-L332)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

30:         ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);

46:         ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L46-L46)

```solidity
📁 File: src/types/GlobalData.sol

40:         globalData.lockData.quoteReserve = ERC20(globalData.pairs[pairId].quotePool.token).balanceOf(address(this));

41:         globalData.lockData.baseReserve = ERC20(globalData.pairs[pairId].basePool.token).balanceOf(address(this));

103:         uint256 reserveAfter = ERC20(currency).balanceOf(address(this));

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L40-L40), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L41-L41), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L103-L103)

### [G-44]<a name="g-44"></a> Gas savings can be achieved by changing the model for assigning value to the structure

Here's an example to illustrate this:

```solidity
struct MyStruct {
  uint256 a;
  uint256 b;
  uint256 c;
}

function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
  MyStruct memory myStruct = MyStruct(_a, _b, _c);
  // Do something with myStruct
}
```

In this example, we have a simple MyStruct data structure with three uint256 fields. The assignValuesToStruct function takes three input parameters _a, _b, and _c, and assigns them to the corresponding fields in a new myStruct variable. This is done using the struct constructor syntax, which creates a new instance of the MyStruct struct with the specified field values.

The following approach can be more efficient than assigning values to the struct fields:

```solidity
function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
  MyStruct memory myStruct;
  myStruct.a = _a;
  myStruct.b = _b;
  myStruct.c = _c;
  // Do something with myStruct
}
```

By changing the pattern of assigning value to the structure, gas savings of ~130 per instance are achieved. In addition, this use will provide significant savings in distribution costs.

*There are 2 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

170:                     CallbackData(
171:                         GammaTradeMarketLib.CallbackType.TRADE, gammaOrder.info.trader, gammaOrder.marginAmount
172:                     )

```


*GitHub* : [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L170-L172)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

192:                     CallbackData(
193:                         CallbackSource.TRADE3, perpOrder.info.trader, 0, perpOrder.leverage, resolvedOrder, orderId
194:                     )

```


*GitHub* : [192](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L192-L194)

### [G-45]<a name="g-45"></a> Structs can be packed into fewer storage slots by editing time variables

The following structs contain time variables that are unlikely to ever reach max `uint256` then it is possible to set them as `uint32`, saving gas slots. Max value of `uint32` is equal to Year 2016, which is more than enough.

*There are 4 instance(s) of this issue:*

```solidity
📁 File: src/libraries/DataType.sol

// @audit  lastUpdateTimestamp
7:     struct PairStatus {
8:         uint256 id;
9:         address quoteToken;
10:         address poolOwner;
11:         Perp.AssetPoolStatus quotePool;
12:         Perp.AssetPoolStatus basePool;
13:         Perp.AssetRiskParams riskParams;
14:         Perp.SqrtPerpAssetStatus sqrtAssetStatus;
15:         address priceFeed;
16:         bool isQuoteZero;
17:         bool allowlistEnabled;
18:         uint8 feeRatio;
19:         uint256 lastUpdateTimestamp;
20:     }

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L7-L20)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

// @audit  lastHedgedTime
14:     struct UserPosition {
15:         uint256 vaultId;
16:         address owner;
17:         uint64 pairId;
18:         uint8 leverage;
19:         int64 maximaDeviation;
20:         uint256 expiration;
21:         uint256 lowerLimit;
22:         uint256 upperLimit;
23:         uint256 lastHedgedTime;
24:         uint256 hedgeInterval;
25:         uint256 lastHedgedSqrtPrice;
26:         uint256 sqrtPriceTrigger;
27:         GammaTradeMarketLib.AuctionParams auctionParams;
28:     }

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L14-L28)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

// @audit  startTime, endTime
19:     struct AuctionParams {
20:         uint256 startPrice;
21:         uint256 endPrice;
22:         uint256 startTime;
23:         uint256 endTime;
24:     }

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L19-L24)

```solidity
📁 File: src/vendors/IPyth.sol

// @audit  publishTime
5:     struct Price {
6:         // Price
7:         int64 price;
8:         // Confidence interval around the price
9:         uint64 conf;
10:         // Price exponent
11:         int32 expo;
12:         // Unix timestamp describing when the price was published
13:         uint256 publishTime;
14:     }

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L5-L14)

### [G-46]<a name="g-46"></a> Optimize Storage with Byte Truncation for Time Related State Variables

Storage optimization in Solidity contracts is vital for reducing gas costs, especially when storing time-related state variables. Using `uint32` for storing time values like timestamps is often sufficient, given it can represent dates up to the year 2106. By truncating larger default integer types to `uint32`, you significantly save on storage space and consequently on gas costs for deployment and state modifications. However, ensure that the truncation does not lead to overflow issues and that the variable's size is adequate for the application's expected lifespan and precision requirements. Adopting this optimization practice contributes to more efficient and cost-effective smart contract development.

*There are 18 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

35:     uint256 private constant VALID_TIME_PERIOD = 5 * 60;

```


*GitHub* : [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L35-L35)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

50:     function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L50-L50)

```solidity
📁 File: src/libraries/DataType.sol

19:         uint256 lastUpdateTimestamp;

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L19-L19)

```solidity
📁 File: src/libraries/UniHelper.sol

11:     uint256 internal constant _ORACLE_PERIOD = 30 minutes;

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L11-L11)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

8:     function decay(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime)

16:     function decay2(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime, uint256 value)

29:             uint256 duration = decayEndTime - decayStartTime;

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L8-L8), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L16-L16), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L29-L29)

```solidity
📁 File: src/markets/L2Decoder.sol

10:             uint64 startTime,

11:             uint64 endTime,

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L11-L11)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

19:     uint16 auctionPeriod;

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L19-L19)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

23:         uint256 lastHedgedTime;

33:         uint16 auctionPeriod;

141:     function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L23-L23), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L33-L33), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L141-L141)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

19:             uint16 auctionPeriod,

61:             uint16 auctionPeriod,

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L19-L19), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L61-L61)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

22:         uint256 startTime;

23:         uint256 endTime;

```


*GitHub* : [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L22-L22), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L23-L23)

```solidity
📁 File: src/vendors/IPyth.sol

13:         uint256 publishTime;

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L13-L13)

### [G-47]<a name="g-47"></a> Trade-offs Between Modifiers and Internal Functions

In Solidity, both modifiers and internal functions can be used to modularize and reuse code, but they have different trade-offs.

Modifiers are primarily used to augment the behavior of functions, often for checks or validations. They can access parameters of the function they modify and are integrated into the function’s code at compile time. This makes them syntactically cleaner for repetitive precondition checks. However, modifiers can sometimes lead to less readable code, especially when the logic is complex or when multiple modifiers are used on a single function.

Internal functions, on the other hand, offer more flexibility. They can contain complex logic, return values, and be called from other functions. This makes them more suitable for reusable chunks of business logic. Since internal functions are separate entities, they can be more readable and easier to test in isolation compared to modifiers.

Using internal functions can result in slightly more gas consumption, as it involves an internal function call. However, this cost is usually minimal and can be a worthwhile trade-off for increased code clarity and maintainability.

In summary, while modifiers offer a concise way to include checks and simple logic across multiple functions, internal functions provide more flexibility and are better suited for complex and reusable code. The choice between the two should be based on the specific use case, considering factors like code complexity, readability, and gas efficiency.

*There are 190 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

380:     function _getAssetStatusPool(uint256 pairId, bool isQuoteToken)
381:         internal
382:         view
383:         returns (Perp.AssetPoolStatus storage)
384:     {

```


*GitHub* : [380](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L380-L384)

```solidity
📁 File: src/base/BaseMarket.sol

55:     function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams)
56:         internal
57:         view
58:         returns (bytes memory)
59:     {

63:     function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)
64:         internal
65:         pure
66:         returns (bytes memory)
67:     {

104:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

108:     function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {

114:     function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L55-L59), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L63-L67), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L104-L104), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L108-L108), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L114-L114)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

61:     function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)
62:         internal
63:         pure
64:         returns (SettlementCallbackLib.SettlementParams memory)
65:     {

105:     function _getSettlementDataFromV3(IFillerMarket.SettlementParamsV3 memory settlementParams, address filler)
106:         internal
107:         pure
108:         returns (bytes memory)
109:     {

152:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

156:     function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {

162:     function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {

```


*GitHub* : [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L61-L65), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L105-L109), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L152-L152), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L156-L156), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L162-L162)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

25:     function decodeParams(bytes memory settlementData) internal pure returns (SettlementParams memory) {

29:     function validate(
30:         mapping(address settlementContractAddress => bool) storage _whiteListedSettlements,
31:         SettlementParams memory settlementParams
32:     ) internal view {

40:     function execSettlement(
41:         IPredyPool predyPool,
42:         address quoteToken,
43:         address baseToken,
44:         SettlementParams memory settlementParams,
45:         int256 baseAmountDelta
46:     ) internal {

60:     function execSettlementInternal(
61:         IPredyPool predyPool,
62:         address quoteToken,
63:         address baseToken,
64:         SettlementParams memory settlementParams,
65:         int256 baseAmountDelta
66:     ) internal {

90:     function sell(
91:         IPredyPool predyPool,
92:         address quoteToken,
93:         address baseToken,
94:         SettlementParams memory settlementParams,
95:         address sender,
96:         uint256 price,
97:         uint256 sellAmount
98:     ) internal {

137:     function buy(
138:         IPredyPool predyPool,
139:         address quoteToken,
140:         address baseToken,
141:         SettlementParams memory settlementParams,
142:         address sender,
143:         uint256 price,
144:         uint256 buyAmount
145:     ) internal {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L25-L25), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L29-L32), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L40-L46), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L60-L66), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L90-L98), [137](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L137-L145)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

26:     function applyInterestForToken(mapping(uint256 => DataType.PairStatus) storage pairs, uint256 pairId) internal {

50:     function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)
51:         internal
52:         returns (uint256 interestRate, uint256 totalProtocolFee)
53:     {

75:     function emitInterestGrowthEvent(
76:         DataType.PairStatus memory assetStatus,
77:         uint256 interestRateQuote,
78:         uint256 interestRateBase,
79:         uint256 totalProtocolFeeQuote,
80:         uint256 totalProtocolFeeBase
81:     ) internal {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L26-L26), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L50-L53), [75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L75-L81)

```solidity
📁 File: src/libraries/InterestRateModel.sol

18:     function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)
19:         internal
20:         pure
21:         returns (uint256)
22:     {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L18-L22)

```solidity
📁 File: src/libraries/PairLib.sol

5:     function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L5-L5)

```solidity
📁 File: src/libraries/Perp.sol

119:     function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)
120:         internal
121:         pure
122:         returns (SqrtPerpAssetStatus memory)
123:     {

145:     function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {

159:     function updateRebalanceInterestGrowth(
160:         DataType.PairStatus memory _pairStatus,
161:         SqrtPerpAssetStatus storage _sqrtAssetStatus
162:     ) internal {

202:     function reallocate(
203:         DataType.PairStatus storage _assetStatusUnderlying,
204:         SqrtPerpAssetStatus storage _sqrtAssetStatus
205:     ) internal returns (bool, bool, int256 deltaPositionBase, int256 deltaPositionQuote) {

262:     function rebalanceForInRange(
263:         DataType.PairStatus storage _assetStatusUnderlying,
264:         SqrtPerpAssetStatus storage _sqrtAssetStatus,
265:         int24 _currentTick,
266:         uint128 _totalLiquidityAmount
267:     ) internal {

305:     function swapForOutOfRange(
306:         DataType.PairStatus storage pairStatus,
307:         uint160 _currentSqrtPrice,
308:         int24 _tick,
309:         uint128 _totalLiquidityAmount
310:     ) internal returns (int256 deltaPositionBase, int256 deltaPositionQuote) {

332:     function getAvailableLiquidityAmount(
333:         address _controllerAddress,
334:         address _uniswapPool,
335:         int24 _tickLower,
336:         int24 _tickUpper
337:     ) internal view returns (uint128) {

345:     function settleUserBalance(DataType.PairStatus storage _pairStatus, UserStatus storage _userStatus) internal {

373:     function updateFeeAndPremiumGrowth(uint256 _pairId, SqrtPerpAssetStatus storage _assetStatus) internal {

414:     function saveLastFeeGrowth(SqrtPerpAssetStatus storage _assetStatus) internal {

426:     function computeRequiredAmounts(
427:         SqrtPerpAssetStatus storage _sqrtAssetStatus,
428:         bool _isQuoteZero,
429:         UserStatus memory _userStatus,
430:         int256 _tradeSqrtAmount
431:     ) internal returns (int256 requiredAmountUnderlying, int256 requiredAmountStable) {

470:     function updatePosition(
471:         DataType.PairStatus storage _pairStatus,
472:         UserStatus storage _userStatus,
473:         UpdatePerpParams memory _updatePerpParams,
474:         UpdateSqrtPerpParams memory _updateSqrtPerpParams
475:     ) internal returns (IPredyPool.Payoff memory payoff) {

526:     function updateSqrtPosition(
527:         uint256 _pairId,
528:         SqrtPerpAssetStatus storage _assetStatus,
529:         UserStatus storage _userStatus,
530:         int256 _amount
531:     ) internal {

585:     function getAvailableSqrtAmount(SqrtPerpAssetStatus memory _assetStatus, bool _isWithdraw)
586:         internal
587:         pure
588:         returns (uint256)
589:     {

604:     function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {

618:     function updateRebalanceEntry(
619:         SqrtPerpAssetStatus storage _assetStatus,
620:         UserStatus storage _userStatus,
621:         bool _isQuoteZero
622:     ) internal returns (int256 rebalancePositionUpdateUnderlying, int256 rebalancePositionUpdateStable) {

673:     function calculateEntry(int256 _positionAmount, int256 _entryValue, int256 _tradeAmount, int256 _valueUpdate)
674:         internal
675:         pure
676:         returns (int256 deltaEntry, int256 payoff)
677:     {

707:     function increase(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)
708:         internal
709:         returns (int256 requiredAmount0, int256 requiredAmount1)
710:     {

719:     function decrease(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)
720:         internal
721:         returns (int256 receivedAmount0, int256 receivedAmount1)
722:     {

745:     function calculateSqrtPerpOffset(
746:         UserStatus memory _userStatus,
747:         int24 _tickLower,
748:         int24 _tickUpper,
749:         int256 _tradeSqrtAmount,
750:         bool _isQuoteZero
751:     ) internal pure returns (int256 offsetUnderlying, int256 offsetStable) {

790:     function updateRebalancePosition(
791:         DataType.PairStatus storage _pairStatus,
792:         int256 _updateAmount0,
793:         int256 _updateAmount1
794:     ) internal {

815:     function finalizeReallocation(SqrtPerpAssetStatus storage sqrtPerpStatus) internal {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L119-L123), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L145-L145), [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L159-L162), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L202-L205), [262](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L262-L267), [305](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L305-L310), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L332-L337), [345](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L345-L345), [373](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L373-L373), [414](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L414-L414), [426](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L426-L431), [470](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L470-L475), [526](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L526-L531), [585](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L585-L589), [604](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L604-L604), [618](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L618-L622), [673](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L673-L677), [707](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L707-L710), [719](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L719-L722), [745](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L745-L751), [790](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L790-L794), [815](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L815-L815)

```solidity
📁 File: src/libraries/PerpFee.sol

16:     function computeUserFee(
17:         DataType.PairStatus memory assetStatus,
18:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:         Perp.UserStatus memory userStatus
20:     ) internal view returns (DataType.FeeAmount memory) {

41:     function settleUserFee(
42:         DataType.PairStatus storage assetStatus,
43:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
44:         Perp.UserStatus storage userStatus
45:     ) internal returns (DataType.FeeAmount memory) {

65:     function computePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus memory sqrtPerp)
66:         internal
67:         pure
68:         returns (int256 feeUnderlying, int256 feeStable)
69:     {

95:     function settlePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus storage sqrtPerp)
96:         internal
97:         returns (int256 feeUnderlying, int256 feeStable)
98:     {

111:     function computeRebalanceInterest(
112:         uint256 pairId,
113:         Perp.SqrtPerpAssetStatus memory assetStatus,
114:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
115:         Perp.UserStatus memory userStatus
116:     ) internal view returns (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) {

136:     function settleRebalanceInterest(
137:         uint256 pairId,
138:         Perp.SqrtPerpAssetStatus storage assetStatus,
139:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
140:         Perp.UserStatus storage userStatus
141:     ) internal returns (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L16-L20), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L41-L45), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L65-L69), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L95-L98), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L111-L116), [136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L136-L141)

```solidity
📁 File: src/libraries/PositionCalculator.sol

34:     function isLiquidatable(
35:         DataType.PairStatus memory pairStatus,
36:         DataType.Vault memory _vault,
37:         DataType.FeeAmount memory feeAmount
38:     ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {

49:     function checkSafe(
50:         DataType.PairStatus memory pairStatus,
51:         DataType.Vault memory _vault,
52:         DataType.FeeAmount memory feeAmount
53:     ) internal view returns (int256 minMargin) {

63:     function getIsSafe(
64:         DataType.PairStatus memory pairStatus,
65:         DataType.Vault memory _vault,
66:         DataType.FeeAmount memory feeAmount
67:     ) internal view returns (int256 minMargin, bool isSafe, bool hasPosition) {

75:     function calculateMinMargin(
76:         DataType.PairStatus memory pairStatus,
77:         DataType.Vault memory vault,
78:         DataType.FeeAmount memory feeAmount
79:     ) internal view returns (int256 minMargin, int256 vaultValue, bool hasPosition, uint256 sqrtOraclePrice) {

102:     function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {

117:     function calculateMinValue(
118:         int256 marginAmount,
119:         PositionParams memory positionParams,
120:         uint256 sqrtPrice,
121:         uint256 riskRatio
122:     ) internal pure returns (int256 minValue, int256 vaultValue, uint256 debtValue, bool hasPosition) {

135:     function getHasPosition(DataType.Vault memory _vault) internal pure returns (bool hasPosition) {

141:     function getSqrtIndexPrice(DataType.PairStatus memory pairStatus) internal view returns (uint256 sqrtPriceX96) {

151:     function getPositionWithFeeAmount(Perp.UserStatus memory perpUserStatus, DataType.FeeAmount memory feeAmount)
152:         internal
153:         pure
154:         returns (PositionParams memory positionParams)
155:     {

163:     function getPosition(Perp.UserStatus memory _perpUserStatus)
164:         internal
165:         pure
166:         returns (PositionParams memory positionParams)
167:     {

175:     function getHasPositionFlag(PositionParams memory _positionParams) internal pure returns (bool) {

186:     function calculateMinValue(uint256 _sqrtPrice, PositionParams memory _positionParams, uint256 _riskRatio)
187:         internal
188:         pure
189:         returns (int256 minValue)
190:     {

231:     function calculateValue(uint256 _sqrtPrice, PositionParams memory _positionParams) internal pure returns (int256) {

238:     function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)
239:         internal
240:         pure
241:         returns (uint256)
242:     {

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L34-L38), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L49-L53), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L63-L67), [75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L75-L79), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L102-L102), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L117-L122), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L135-L135), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L141-L141), [151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L151-L155), [163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L163-L167), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L175-L175), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L186-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L231-L231), [238](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L238-L242)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

14:     function calculatePremiumCurve(uint256 utilization) internal pure returns (uint256) {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L14-L14)

```solidity
📁 File: src/libraries/Reallocation.sol

18:     function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)
19:         internal
20:         view
21:         returns (int24 lower, int24 upper)
22:     {

39:     function _getNewRange(
40:         DataType.PairStatus memory _assetStatusUnderlying,
41:         ScaledAsset.AssetStatus memory _token0Status,
42:         ScaledAsset.AssetStatus memory _token1Status,
43:         int24 currentTick,
44:         int24 tickSpacing
45:     ) internal pure returns (int24 lower, int24 upper) {

92:     function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {

98:     function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)
99:         internal
100:         pure
101:         returns (bool)
102:     {

109:     function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {

126:     function calculateMinLowerTick(
127:         int24 currentLowerTick,
128:         uint256 available,
129:         uint256 liquidityAmount,
130:         int24 tickSpacing
131:     ) internal pure returns (int24 minLowerTick) {

147:     function calculateMaxUpperTick(
148:         int24 currentUpperTick,
149:         uint256 available,
150:         uint256 liquidityAmount,
151:         int24 tickSpacing
152:     ) internal pure returns (int24 maxUpperTick) {

165:     function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)
166:         internal
167:         pure
168:         returns (uint160)
169:     {

179:     function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)
180:         internal
181:         pure
182:         returns (uint160)
183:     {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L18-L22), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L39-L45), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L92-L92), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L98-L102), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L109-L109), [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L126-L131), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L147-L152), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L165-L169), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L179-L183)

```solidity
📁 File: src/libraries/ScaledAsset.sol

29:     function createAssetStatus() internal pure returns (AssetStatus memory) {

33:     function createUserStatus() internal pure returns (UserStatus memory) {

37:     function addAsset(AssetStatus storage tokenState, uint256 _amount) internal returns (uint256 claimAmount) {

47:     function removeAsset(AssetStatus storage tokenState, uint256 _supplyTokenAmount, uint256 _amount)
48:         internal
49:         returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)
50:     {

72:     function isSameSign(int256 a, int256 b) internal pure returns (bool) {

76:     function updatePosition(
77:         ScaledAsset.AssetStatus storage tokenStatus,
78:         ScaledAsset.UserStatus storage userStatus,
79:         int256 _amount,
80:         uint256 _pairId,
81:         bool _isStable
82:     ) internal {

130:     function computeUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus memory _userStatus)
131:         internal
132:         pure
133:         returns (int256 interestFee)
134:     {

142:     function settleUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus storage _userStatus)
143:         internal
144:         returns (int256 interestFee)
145:     {

155:     function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)
156:         internal
157:         pure
158:         returns (uint256)
159:     {

170:     function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)
171:         internal
172:         pure
173:         returns (uint256)
174:     {

186:     function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)
187:         internal
188:         returns (uint256)
189:     {

217:     function getTotalCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {

222:     function getTotalDebtValue(AssetStatus memory tokenState) internal pure returns (uint256) {

226:     function getAvailableCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {

230:     function getUtilizationRatio(AssetStatus memory tokenState) internal pure returns (uint256) {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L33-L33), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L47-L50), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L72-L72), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L76-L82), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L130-L134), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L142-L145), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L155-L159), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L170-L174), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L186-L189), [217](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L217-L217), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L222-L222), [226](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L226-L226), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L230-L230)

```solidity
📁 File: src/libraries/SlippageLib.sol

21:     function checkPrice(
22:         uint256 sqrtBasePrice,
23:         IPredyPool.TradeResult memory tradeResult,
24:         uint256 slippageTolerance,
25:         uint256 maxAcceptableSqrtPriceRange
26:     ) internal pure {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L21-L26)

```solidity
📁 File: src/libraries/Trade.sol

76:     function swap(
77:         GlobalDataLibrary.GlobalData storage globalData,
78:         uint256 pairId,
79:         SwapStableResult memory swapParams,
80:         bytes memory settlementData,
81:         uint256 sqrtPrice,
82:         uint256 vaultId
83:     ) internal returns (SwapStableResult memory) {

112:     function getSqrtPrice(address uniswapPoolAddress, bool isQuoteZero) internal view returns (uint256 sqrtPriceX96) {

116:     function calculateStableAmount(uint256 currentSqrtPrice, uint256 baseAmount) internal pure returns (uint256) {

122:     function divToStable(
123:         SwapStableResult memory swapParams,
124:         int256 amountBase,
125:         int256 amountQuote,
126:         int256 totalAmountStable
127:     ) internal pure returns (SwapStableResult memory swapResult) {

135:     function settleUserBalanceAndFee(
136:         DataType.PairStatus storage _pairStatus,
137:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
138:         Perp.UserStatus storage _userStatus
139:     ) internal returns (DataType.FeeAmount memory realizedFee) {

```


*GitHub* : [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L76-L83), [112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L112-L112), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L116-L116), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L122-L127), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L135-L139)

```solidity
📁 File: src/libraries/UniHelper.sol

13:     function getSqrtPrice(address uniswapPoolAddress) internal view returns (uint160 sqrtPrice) {

20:     function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {

27:     function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {

35:     function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {

77:     function revertBytes(bytes memory errMsg) internal pure {

87:     function getFeeGrowthInsideLast(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)
88:         internal
89:         view
90:         returns (uint256 feeGrowthInside0LastX128, uint256 feeGrowthInside1LastX128)
91:     {

99:     function getFeeGrowthInside(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)
100:         internal
101:         view
102:         returns (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128)
103:     {

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L13-L13), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L20-L20), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L35-L35), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L77-L77), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L87-L91), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L99-L103)

```solidity
📁 File: src/libraries/VaultLib.sol

11:     function getVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId)
12:         internal
13:         view
14:         returns (DataType.Vault storage vault)
15:     {

24:     function createOrGetVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId, uint256 pairId)
25:         internal
26:         returns (uint256)
27:     {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L11-L15), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L24-L27)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

142:     function _storePairStatus(
143:         address quoteToken,
144:         mapping(uint256 => DataType.PairStatus) storage _pairs,
145:         uint256 _pairId,
146:         address _tokenAddress,
147:         bool _isQuoteZero,
148:         AddPairParams memory _addPairParam
149:     ) internal {

192:     function deploySupplyToken(address _tokenAddress) internal returns (address) {

205:     function validateFeeRatio(uint8 _fee) internal pure {

209:     function validatePoolOwner(address _poolOwner) internal pure {

213:     function validateRiskParams(Perp.AssetRiskParams memory _assetRiskParams) internal pure {

219:     function validateIRMParams(InterestRateModel.IRMParams memory _irmParams) internal pure {

```


*GitHub* : [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L142-L149), [192](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L192-L192), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L205-L205), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L209-L209), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L213-L213), [219](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L219-L219)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

129:     function checkVaultIsDanger(
130:         DataType.PairStatus memory pairStatus,
131:         DataType.Vault memory vault,
132:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache
133:     ) internal view returns (uint256 sqrtOraclePrice, uint256 slippageTolerance) {

159:     function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)
160:         internal
161:         pure
162:         returns (uint256)
163:     {

```


*GitHub* : [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L129-L133), [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L159-L163)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

43:     function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)
44:         internal
45:         pure
46:         returns (IPredyPool.Position memory)
47:     {

56:     function revertPairStatus(DataType.PairStatus memory pairStatus) internal pure {

64:     function revertVaultStatus(IPredyPool.VaultStatus memory vaultStatus) internal pure {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L43-L47), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L56-L56), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L64-L64)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

46:     function receiveTokenAndMintBond(Perp.AssetPoolStatus storage _pool, uint256 _amount)
47:         internal
48:         returns (uint256 mintAmount)
49:     {

79:     function burnBondAndTransferToken(Perp.AssetPoolStatus storage _pool, uint256 _amount)
80:         internal
81:         returns (uint256 finalburntAmount, uint256 finalWithdrawalAmount)
82:     {

```


*GitHub* : [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L46-L49), [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L79-L82)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

68:     function callTradeAfterCallback(
69:         GlobalDataLibrary.GlobalData storage globalData,
70:         IPredyPool.TradeParams memory tradeParams,
71:         IPredyPool.TradeResult memory tradeResult
72:     ) internal {

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L68-L72)

```solidity
📁 File: src/libraries/math/Bps.sol

7:     function upper(uint256 price, uint256 bps) internal pure returns (uint256) {

11:     function lower(uint256 price, uint256 bps) internal pure returns (uint256) {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L7-L7), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L11-L11)

```solidity
📁 File: src/libraries/math/LPMath.sol

10:     function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
11:         internal
12:         pure
13:         returns (int256)
14:     {

20:     function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
21:         internal
22:         pure
23:         returns (int256)
24:     {

30:     function calculateAmount0ForLiquidity(
31:         uint160 sqrtRatioA,
32:         uint160 sqrtRatioB,
33:         uint256 liquidityAmount,
34:         bool isRoundUp
35:     ) internal pure returns (int256) {

68:     function calculateAmount1ForLiquidity(
69:         uint160 sqrtRatioA,
70:         uint160 sqrtRatioB,
71:         uint256 liquidityAmount,
72:         bool isRoundUp
73:     ) internal pure returns (int256) {

108:     function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)
109:         internal
110:         pure
111:         returns (int256)
112:     {

119:     function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
120:         internal
121:         pure
122:         returns (uint256)
123:     {

134:     function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)
135:         internal
136:         pure
137:         returns (int256)
138:     {

145:     function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
146:         internal
147:         pure
148:         returns (uint256)
149:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L10-L14), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L20-L24), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L30-L35), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L68-L73), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L108-L112), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L119-L123), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L134-L138), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L145-L149)

```solidity
📁 File: src/libraries/math/Math.sol

12:     function abs(int256 x) internal pure returns (uint256) {

16:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

20:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

24:     function fullMulDivInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

34:     function fullMulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

44:     function mulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

54:     function addDelta(uint256 a, int256 b) internal pure returns (uint256) {

62:     function calSqrtPriceToPrice(uint256 sqrtPrice) internal pure returns (uint256 price) {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L16-L16), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L20-L20), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L24-L24), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L34-L34), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L44-L44), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L54-L54), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L62-L62)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

8:     function decay(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime)
9:         internal
10:         view
11:         returns (uint256 decayedPrice)
12:     {

16:     function decay2(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime, uint256 value)
17:         internal
18:         pure
19:         returns (uint256 decayedPrice)
20:     {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L8-L12), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L16-L20)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

18:     function hash(OrderInfo memory info) internal pure returns (bytes32) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L18-L18)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

10:     function toPermit(ResolvedOrder memory order)
11:         internal
12:         pure
13:         returns (ISignatureTransfer.PermitTransferFrom memory)
14:     {

23:     function transferDetails(ResolvedOrder memory order, address to)
24:         internal
25:         pure
26:         returns (ISignatureTransfer.SignatureTransferDetails memory)
27:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L10-L14), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L23-L27)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

23:     function validate(ResolvedOrder memory resolvedOrder) internal view {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L23-L23)

```solidity
📁 File: src/markets/L2Decoder.sol

5:     function decodeSpotOrderParams(bytes32 args1, bytes32 args2)
6:         internal
7:         pure
8:         returns (
9:             bool isLimit,
10:             uint64 startTime,
11:             uint64 endTime,
12:             uint64 deadline,
13:             uint128 startAmount,
14:             uint128 endAmount
15:         )
16:     {

38:     function decodePerpOrderParams(bytes32 args)
39:         internal
40:         pure
41:         returns (uint64 deadline, uint64 pairId, uint8 leverage)
42:     {

50:     function decodePerpOrderV3Params(bytes32 args)
51:         internal
52:         pure
53:         returns (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side)
54:     {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L5-L16), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L38-L42), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L50-L54)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

5:     function addItem(uint256[] storage items, uint256 item) internal {

9:     function removeItem(uint256[] storage items, uint256 item) internal {

15:     function removeItemByIndex(uint256[] storage items, uint256 index) internal {

20:     function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L5-L5), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L9-L9), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L15-L15), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L20-L20)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

43:     function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {

115:     function hash(GammaOrder memory order) internal pure returns (bytes32) {

134:     function resolve(GammaOrder memory gammaOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L43-L43), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L115-L115), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L134-L134)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

152:     function _executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)
153:         internal
154:         returns (IPredyPool.TradeResult memory tradeResult)
155:     {

211:     function _modifyAutoHedgeAndClose(GammaOrder memory gammaOrder, bytes memory sig) internal {

375:     function _getUserPosition(uint256 positionId) internal returns (UserPositionResult memory result) {

388:     function _addPositionIndex(address trader, uint256 newPositionId) internal {

402:     function _removePosition(uint256 positionId) internal {

408:     function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)
409:         internal
410:         returns (GammaTradeMarketLib.UserPosition storage userPosition)
411:     {

436:     function _saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)
437:         internal
438:     {

444:     function _verifyOrder(ResolvedOrder memory order) internal {

```


*GitHub* : [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L152-L155), [211](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L211-L211), [375](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L375-L375), [388](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L388-L388), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L402-L402), [408](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L408-L411), [436](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L436-L438), [444](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L444-L444)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

48:     function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)
49:         internal
50:         pure
51:         returns (int256)
52:     {

141:     function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)
142:         internal
143:         pure
144:         returns (uint256)
145:     {

167:     function calculateSlippageToleranceByPrice(uint256 price1, uint256 price2, AuctionParams memory auctionParams)
168:         internal
169:         pure
170:         returns (uint256)
171:     {

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L48-L52), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L141-L145), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L167-L171)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

7:     function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)
8:         internal
9:         pure
10:         returns (GammaModifyInfo memory)
11:     {

38:     function decodeGammaParam(bytes32 args)
39:         internal
40:         pure
41:         returns (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage)
42:     {

51:     function decodeGammaModifyParam(bytes32 args)
52:         internal
53:         pure
54:         returns (
55:             bool isEnabled,
56:             uint64 expiration,
57:             uint32 hedgeInterval,
58:             uint32 sqrtPriceTrigger,
59:             uint32 minSlippageTolerance,
60:             uint32 maxSlippageTolerance,
61:             uint16 auctionPeriod,
62:             uint32 auctionRange
63:         )
64:     {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L7-L11), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L38-L42), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L51-L64)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

26:     function getFinalTradeAmount(
27:         int256 currentPositionAmount,
28:         string memory side,
29:         uint256 quantity,
30:         bool reduceOnly,
31:         bool closePosition
32:     ) internal pure returns (int256 finalTradeAmount) {

80:     function validateTrade(
81:         IPredyPool.TradeResult memory tradeResult,
82:         int256 tradeAmount,
83:         uint256 limitPrice,
84:         uint256 stopPrice,
85:         bytes memory auctionData
86:     ) internal view {

118:     function validateLimitPrice(uint256 tradePrice, int256 tradeAmount, uint256 limitPrice)
119:         internal
120:         pure
121:         returns (bool)
122:     {

138:     function validateStopPrice(
139:         uint256 oraclePrice,
140:         uint256 tradePrice,
141:         int256 tradeAmount,
142:         uint256 stopPrice,
143:         bytes memory auctionData
144:     ) internal pure returns (bool) {

178:     function ratio(uint256 price1, uint256 price2) internal pure returns (uint256) {

188:     function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)
189:         internal
190:         view
191:         returns (bool)
192:     {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L26-L32), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L80-L86), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L118-L122), [138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L138-L144), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L178-L178), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L188-L192)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

159:     function _executeOrderV3(
160:         PerpOrderV3 memory perpOrder,
161:         bytes memory sig,
162:         SettlementParamsV3 memory settlementParams,
163:         uint64 orderId
164:     ) internal returns (IPredyPool.TradeResult memory tradeResult) {

220:     function _calculateInitialMargin(uint256 vaultId, uint256 pairId, uint256 leverage)
221:         internal
222:         view
223:         returns (int256)
224:     {

236:     function _calculateNetValue(DataType.Vault memory vault, uint256 price) internal pure returns (uint256) {

242:     function _calculatePositionValue(DataType.Vault memory vault, uint256 sqrtPrice) internal pure returns (int256) {

265:     function _saveUserPosition(UserPosition storage userPosition, PerpOrder memory perpOrder) internal {

314:     function _verifyOrder(ResolvedOrder memory order, uint256 amount) internal {

327:     function _verifyOrderV3(ResolvedOrder memory order, uint256 amount) internal {

```


*GitHub* : [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L159-L164), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L220-L224), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L236-L236), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L242-L242), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L265-L265), [314](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L314-L314), [327](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L327-L327)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

54:     function hash(PerpOrder memory order) internal pure returns (bytes32) {

73:     function resolve(PerpOrder memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L54-L54), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L73-L73)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

58:     function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {

78:     function resolve(PerpOrderV3 memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L58-L58), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L78-L78)

```solidity
📁 File: src/types/GlobalData.sol

26:     function validateVaultId(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId) internal view {

30:     function validate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal view {

35:     function initializeLock(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal {

46:     function callSettlementCallback(
47:         GlobalDataLibrary.GlobalData storage globalData,
48:         bytes memory settlementData,
49:         int256 deltaBaseAmount
50:     ) internal {

62:     function finalizeLock(GlobalDataLibrary.GlobalData storage globalData)
63:         internal
64:         returns (int256 paidQuote, int256 paidBase)
65:     {

72:     function take(GlobalDataLibrary.GlobalData storage globalData, bool isQuoteAsset, address to, uint256 amount)
73:         internal
74:     {

88:     function settle(GlobalDataLibrary.GlobalData storage globalData, bool isQuoteAsset)
89:         internal
90:         returns (int256 paid)
91:     {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L26-L26), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L30-L30), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L35-L35), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L46-L50), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L62-L65), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L72-L74), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L88-L91)

### [G-48]<a name="g-48"></a> Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas for changes

Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past. Refer to the [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/5ae630684a0f57de400ef69499addab4c32ac8fb/contracts/security/ReentrancyGuard.sol#L23-L27).

*There are 4 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

38:     mapping(address => bool) public allowedUniswapPools;

40:     mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L40-L40)

```solidity
📁 File: src/base/BaseMarket.sol

17:     mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;

```


*GitHub* : [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L17-L17)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

29:     mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L29-L29)

### [G-49]<a name="g-49"></a> Divisions can be `unchecked` to save gas

The expression `type(int).min/(-1)` is the only case where division causes an overflow. Therefore, uncheck can be used to [save gas](https://gist.github.com/DadeKuma/3bc597338ae774b8b3bd43280d55271f) in scenarios where it is certain that such an overflow will not occur.

*There are 63 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

54:         uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);

55:         price = price * Constants.Q96 / _decimalsDiff;

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L55-L55)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

70:         uint256 fee = settlementParamsV3.feePrice * tradeAmountAbs / Constants.Q96;

76:         uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

77:         uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;

```


*GitHub* : [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L77-L77)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

101:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

125:             uint256 quoteAmount = sellAmount * price / Constants.Q96;

148:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

172:             uint256 quoteAmount = buyAmount * price / Constants.Q96;

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L101-L101), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L125-L125), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L148-L148), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L172-L172)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

66:         interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)
67:             * (block.timestamp - lastUpdateTimestamp) / 365 days;

71:         poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;

72:         poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;

```


*GitHub* : [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L66-L67), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L72-L72)

```solidity
📁 File: src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L29-L29)

```solidity
📁 File: src/libraries/Perp.sol

166:             _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(
167:                 _sqrtAssetStatus.rebalancePositionBase
168:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

170:             _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(
171:                 _sqrtAssetStatus.rebalancePositionQuote
172:             ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

399:             f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

402:             f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount

590:         uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);

609:         uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;

689:                 int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;

697:                 int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;

785:             offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;

786:             offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;

```


*GitHub* : [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L166-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L170-L172), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L402-L402), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L590-L590), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L609-L609), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L697-L697), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L786-L786)

```solidity
📁 File: src/libraries/PositionCalculator.sol

93:             (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;

193:         uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;

194:         uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;

213:                 (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);

```


*GitHub* : [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L93-L93), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L213-L213)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

21:         return (1600 * b * b / Constants.ONE) / Constants.ONE;

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L21-L21)

```solidity
📁 File: src/libraries/Reallocation.sol

51:         int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;

120:         result = (result / tickSpacing) * tickSpacing;

170:         uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();

184:         uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;

190:         uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));

```


*GitHub* : [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L51-L51), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L190-L190)

```solidity
📁 File: src/libraries/SlippageLib.sol

48:                     tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange

49:                         || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L49-L49)

```solidity
📁 File: src/libraries/Trade.sol

128:         swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;

129:         swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;

132:         swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();

```


*GitHub* : [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L132-L132)

```solidity
📁 File: src/libraries/UniHelper.sol

29:             return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);

70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L70-L70)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

62:             -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,

63:             -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,

168:         uint256 ratio = uint256(vaultValue * 1e4 / minMargin);

174:         return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);

```


*GitHub* : [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L63-L63), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L168-L168), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L174-L174)

```solidity
📁 File: src/libraries/math/Bps.sol

8:         return price * bps / ONE;

12:         return price * ONE / bps;

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L12-L12)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

32:                 decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;

34:                 decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L34-L34)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

53:         int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;

56:         return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;

84:         uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;

85:         uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;

150:         uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;

158:                 + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE

176:         uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);

184:                 + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)
185:                     / auctionParams.auctionRange

```


*GitHub* : [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L184-L185)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

87:         uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)
88:             * Constants.Q96 / Math.abs(tradeAmount);

182:             return (price1 - price2) * Bps.ONE / price2;

184:             return (price2 - price1) * Bps.ONE / price2;

```


*GitHub* : [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L87-L88), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L184-L184)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

233:         return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);

239:         return Math.abs(positionAmount) * price / Constants.Q96;

```


*GitHub* : [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L239-L239)

### [G-50]<a name="g-50"></a> Avoid unnecessary public variables

Public state variables in Solidity automatically generate getter functions, increasing contract size and potentially leading to higher deployment and interaction costs. To optimize gas usage and contract efficiency, minimize the use of public variables unless external access is necessary. Instead, use internal or private visibility combined with explicit getter functions when required. This practice not only reduces contract size but also provides better control over data access and manipulation, enhancing security and readability. Prioritize lean, efficient contracts to ensure cost-effectiveness and better performance on the blockchain.

*There are 9 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

34:     address public operator;

36:     GlobalDataLibrary.GlobalData public globalData;

38:     mapping(address => bool) public allowedUniswapPools;

40:     mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L34-L34), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L36-L36), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L40-L40)

```solidity
📁 File: src/base/BaseMarket.sol

11:     address public whitelistFiller;

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L11-L11)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

23:     address public whitelistFiller;

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L23-L23)

```solidity
📁 File: src/libraries/SlippageLib.sol

13:     uint256 public constant MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 100747209;

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L13-L13)

```solidity
📁 File: src/libraries/math/Bps.sol

5:     uint32 public constant ONE = 1e6;

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L5-L5)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

68:     mapping(address owner => mapping(uint256 pairId => UserPosition)) public userPositions;

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L68-L68)

### [G-51]<a name="g-51"></a> Use `!= 0` instead of `> 0` for unsigned integer comparison

`> 0` is less efficient than `!= 0` for unsigned integers.`!= 0` costs less gas compared to `> 0` for unsigned integers in `require` statements with the optimizer enabled (6 gas). While it may seem that `> 0` is cheaper than `!=`, this is only true without the optimizer enabled and outside a `require` statement. If you enable the optimizer at 10k AND you're in a `require` statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

*There are 53 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

84:         if (amount0 > 0) {

87:         if (amount1 > 0) {

182:         require(amount > 0, "AZ");

186:         if (amount > 0) {

204:         require(amount > 0, "AZ");

208:         if (amount > 0) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L87-L87), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L182-L182), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L186-L186), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L204-L204), [208](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L208-L208)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

83:             baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,

```


*GitHub* : [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L83-L83)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

55:         if (settlementParams.fee > 0) {

67:         if (baseAmountDelta > 0) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L67-L67)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

45:         if (interestRateQuote > 0 || interestRateBase > 0) {

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L45-L45)

```solidity
📁 File: src/libraries/Perp.sol

165:         if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {

443:         if (_tradeSqrtAmount > 0) {

551:         if (closeAmount > 0) {

560:         if (openAmount > 0) {

```


*GitHub* : [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L165-L165), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L443-L443), [551](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L551-L551), [560](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L560-L560)

```solidity
📁 File: src/libraries/PerpFee.sol

73:         if (sqrtPerp.amount > 0) {

101:         if (sqrtPerp.amount > 0) {

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L73-L73), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L101-L101)

```solidity
📁 File: src/libraries/PositionCalculator.sol

210:         if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {

245:         if (squartPosition > 0) {

```


*GitHub* : [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L210-L210), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L245-L245)

```solidity
📁 File: src/libraries/Reallocation.sol

55:         if (availableAmount > 0) {

110:         require(tickSpacing > 0);

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L55-L55), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L110-L110)

```solidity
📁 File: src/libraries/ScaledAsset.sol

55:         require(_supplyTokenAmount > 0, "S3");

84:         if (userStatus.positionAmount > 0) {

104:         if (closeAmount > 0) {

113:         if (openAmount > 0) {

135:         if (_userStatus.positionAmount > 0) {

148:         if (_userStatus.positionAmount > 0) {

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L55-L55), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L84-L84), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L104-L104), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L113-L113), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L135-L135), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L148-L148)

```solidity
📁 File: src/libraries/SlippageLib.sol

33:         if (tradeResult.averagePrice > 0) {

46:             maxAcceptableSqrtPriceRange > 0

```


*GitHub* : [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L33-L33), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L46-L46)

```solidity
📁 File: src/libraries/UniHelper.sol

78:         if (errMsg.length > 0) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L78-L78)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

92:             if (remainingMargin > 0) {

```


*GitHub* : [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L92-L92)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

68:                 if (exceedsQuote > 0) {

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L68-L68)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

64:         require(_amount > 0, "AZ");

```


*GitHub* : [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L64-L64)

```solidity
📁 File: src/libraries/math/Math.sol

27:         } else if (x > 0) {

37:         } else if (x > 0) {

47:         } else if (x > 0) {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L27-L27), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L47-L47)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

135:         uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;

```


*GitHub* : [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L135-L135)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

117:                 if (marginAmountUpdate > 0) {

178:         if (tradeResult.minMargin > 0) {

```


*GitHub* : [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L117-L117), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L178-L178)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

65:             userPosition.hedgeInterval > 0

122:         if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {

130:         if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {

```


*GitHub* : [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L65-L65), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L130-L130)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

54:             if (currentPositionAmount > 0) {

65:                 if (tradeAmount > 0) {

97:         } else if (limitPrice > 0 && stopPrice > 0) {

105:         } else if (limitPrice > 0) {

110:         } else if (stopPrice > 0) {

127:         if (tradeAmount > 0 && limitPrice < tradePrice) {

155:         if (tradeAmount > 0) {

200:             if (tradeAmount > 0 && decayedPrice < tradePrice) {

```


*GitHub* : [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L54-L54), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L65-L65), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L97-L97), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L105-L105), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L110-L110), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L127-L127), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L155-L155), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L200-L200)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

119:             if (marginAmountUpdate > 0) {

125:             if (marginAmountUpdate > 0) {

200:         if (tradeResult.minMargin > 0) {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L119-L119), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L125-L125), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L200-L200)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

74:         uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;

```


*GitHub* : [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L74-L74)

### [G-52]<a name="g-52"></a> Using assembly to check for `address(0)` can save gas

Using assembly to check for `address(0)` can save gas by allowing more direct access to the evm and reducing some of the overhead associated with high-level operations in solidity.

*There are 10 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

98:         require(newOperator != address(0));

```


*GitHub* : [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L98-L98)

```solidity
📁 File: src/base/BaseMarket.sol

98:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L98-L98)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

146:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L146-L146)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

34:             settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]

99:         if (settlementParams.contractAddress == address(0)) {

146:         if (settlementParams.contractAddress == address(0)) {

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L34-L34), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L99-L99), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L146-L146)

```solidity
📁 File: src/libraries/PositionCalculator.sol

142:         if (pairStatus.priceFeed != address(0)) {

```


*GitHub* : [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L142-L142)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

210:         require(_poolOwner != address(0), "ADDZ");

```


*GitHub* : [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L210-L210)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

93:                 if (vault.recipient != address(0)) {

```


*GitHub* : [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L93-L93)

```solidity
📁 File: src/types/GlobalData.sol

36:         if (globalData.lockData.locker != address(0)) {

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L36-L36)

### [G-53]<a name="g-53"></a> Use assembly to `emit` events

To efficiently `emit` events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.
However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.
A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.

*There are 29 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

101:         emit OperatorUpdated(newOperator);

190:         emit ProtocolRevenueWithdrawn(pairId, isQuoteToken, amount);

212:         emit CreatorRevenueWithdrawn(pairId, isQuoteToken, amount);

291:         emit RecepientUpdated(vaultId, recipient);

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L101-L101), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L190-L190), [212](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L212-L212), [291](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L291-L291)

```solidity
📁 File: src/PriceFeed.sol

21:         emit PriceFeedCreated(quotePrice, priceId, decimalsDiff, address(priceFeed));

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L21-L21)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

82:         emit InterestGrowthUpdated(
83:             assetStatus.id,
84:             assetStatus.quotePool.tokenStatus,
85:             assetStatus.basePool.tokenStatus,
86:             interestRateQuote,
87:             interestRateBase,
88:             totalProtocolFeeQuote,
89:             totalProtocolFeeBase
90:         );

```


*GitHub* : [82](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L82-L90)

```solidity
📁 File: src/libraries/Perp.sol

411:         emit PremiumGrowthUpdated(_pairId, _assetStatus.totalAmount, _assetStatus.borrowedAmount, f0, f1, spreadParam);

578:         emit SqrtPositionUpdated(_pairId, openAmount, closeAmount);

```


*GitHub* : [411](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L411-L411), [578](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L578-L578)

```solidity
📁 File: src/libraries/ScaledAsset.sol

127:         emit ScaledAssetPositionUpdated(_pairId, _isStable, openAmount, closeAmount);

```


*GitHub* : [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L127-L127)

```solidity
📁 File: src/libraries/Trade.sol

107:         emit Swapped(pairId, vaultId, msg.sender, settledQuoteAmount, settledBaseAmount);

```


*GitHub* : [107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L107-L107)

```solidity
📁 File: src/libraries/VaultLib.sol

44:             emit VaultCreated(vault.id, vault.owner, quoteToken, pairId);

```


*GitHub* : [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L44-L44)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

93:         emit PairAdded(pairId, _addPairParam.quoteToken, _addPairParam.uniswapPool);

101:         emit FeeRatioUpdated(_pairStatus.id, _feeRatio);

109:         emit PoolOwnerUpdated(_pairStatus.id, _poolOwner);

115:         emit PriceOracleUpdated(_pairStatus.id, _priceOracle);

125:         emit AssetRiskParamsUpdated(_pairStatus.id, _riskParams);

139:         emit IRMParamsUpdated(_pairStatus.id, _quoteIrmParams, _baseIrmParams);

188:         emit AssetRiskParamsUpdated(_pairId, _addPairParam.assetRiskParams);

189:         emit IRMParamsUpdated(_pairId, _addPairParam.quoteIrmParams, _addPairParam.baseIrmParams);

```


*GitHub* : [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L93-L93), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L101-L101), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L109-L109), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L115-L115), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L125-L125), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L139-L139), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L188-L188), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L189-L189)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

110:         emit PositionLiquidated(
111:             tradeParams.vaultId,
112:             tradeParams.pairId,
113:             tradeParams.tradeAmount,
114:             tradeParams.tradeAmountSqrt,
115:             tradeResult.payoff,
116:             tradeResult.fee,
117:             sentMarginAmount
118:         );

```


*GitHub* : [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L110-L118)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

73:             emit Rebalanced(
74:                 pairId,
75:                 relocationOccurred,
76:                 pairStatus.sqrtAssetStatus.tickLower,
77:                 pairStatus.sqrtAssetStatus.tickUpper,
78:                 deltaPositionBase,
79:                 deltaPositionQuote
80:             );

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L73-L80)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

43:         emit TokenSupplied(msg.sender, pair.id, _isStable, _amount);

76:         emit TokenWithdrawn(msg.sender, pair.id, _isStable, finalWithdrawalAmount);

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L43-L43), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L76-L76)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

58:         emit PositionUpdated(
59:             tradeParams.vaultId,
60:             tradeParams.pairId,
61:             tradeParams.tradeAmount,
62:             tradeParams.tradeAmountSqrt,
63:             tradeResult.payoff,
64:             tradeResult.fee
65:         );

85:         emit MarginUpdated(tradeParams.vaultId, marginAmountUpdate);

```


*GitHub* : [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L58-L65), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L85-L85)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

101:                 emit GammaPositionTraded(
102:                     callbackData.trader,
103:                     tradeParams.pairId,
104:                     tradeParams.vaultId,
105:                     tradeParams.tradeAmount,
106:                     tradeParams.tradeAmountSqrt,
107:                     tradeResult.payoff,
108:                     tradeResult.fee,
109:                     -vault.margin,
110:                     callbackData.callbackType == GammaTradeMarketLib.CallbackType.TRADE
111:                         ? GammaTradeMarketLib.CallbackType.CLOSE
112:                         : callbackData.callbackType
113:                 );

123:                 emit GammaPositionTraded(
124:                     callbackData.trader,
125:                     tradeParams.pairId,
126:                     tradeParams.vaultId,
127:                     tradeParams.tradeAmount,
128:                     tradeParams.tradeAmountSqrt,
129:                     tradeResult.payoff,
130:                     tradeResult.fee,
131:                     marginAmountUpdate,
132:                     callbackData.callbackType
133:                 );

440:             emit GammaPositionModified(userPosition.owner, userPosition.pairId, userPosition.vaultId, modifyInfo);

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L101-L113), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L123-L133), [440](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L440-L440)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

131:             emit PerpTraded2(
132:                 callbackData.trader,
133:                 tradeParams.pairId,
134:                 tradeResult.vaultId,
135:                 tradeParams.tradeAmount,
136:                 tradeResult.payoff,
137:                 tradeResult.fee,
138:                 marginAmountUpdate,
139:                 callbackData.orderId
140:             );

```


*GitHub* : [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L131-L140)

### [G-54]<a name="g-54"></a> Use assembly to validate `msg.sender`

We can use assembly to efficiently validate `msg.sender` with the least amount of opcodes necessary. For more details check the following report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender)

*There are 12 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

48:         if (operator != msg.sender) revert CallerIsNotOperator();

54:         if (msg.sender != locker) revert LockedBy(locker);

59:         if (globalData.pairs[pairId].poolOwner != msg.sender) revert CallerIsNotPoolCreator();

64:         if (globalData.vaults[vaultId].owner != msg.sender) revert CallerIsNotVaultOwner();

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L48-L48), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L54-L54), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L59-L59), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L64-L64)

```solidity
📁 File: src/base/BaseHookCallback.sol

17:         if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();

```


*GitHub* : [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L17-L17)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

16:         if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L16-L16)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

32:         if (msg.sender != whitelistFiller) revert CallerIsNotFiller();

```


*GitHub* : [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L32-L32)

```solidity
📁 File: src/libraries/VaultLib.sol

19:         if (vault.owner != msg.sender) {

51:             if (vault.owner != msg.sender) {

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L19-L19), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L51-L51)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

180:             if (msg.sender != whitelistFiller) {

```


*GitHub* : [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L180-L180)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

202:             if (msg.sender != whitelistFiller) {

```


*GitHub* : [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L202-L202)

```solidity
📁 File: src/tokenization/SupplyToken.sol

11:         require(_controller == msg.sender, "ST0");

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L11-L11)

### [G-55]<a name="g-55"></a> Simple checks for zero can be done using assembly to save gas



*There are 164 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

84:         if (amount0 > 0) {

87:         if (amount1 > 0) {

98:         require(newOperator != address(0));

182:         require(amount > 0, "AZ");

186:         if (amount > 0) {

204:         require(amount > 0, "AZ");

208:         if (amount > 0) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L87-L87), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L98-L98), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L182-L182), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L186-L186), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L204-L204), [208](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L208-L208)

```solidity
📁 File: src/base/BaseMarket.sol

98:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L98-L98)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

83:             baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,

146:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L83-L83), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L146-L146)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

34:             settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]

47:         if (settlementParams.fee < 0) {

55:         if (settlementParams.fee > 0) {

67:         if (baseAmountDelta > 0) {

77:         } else if (baseAmountDelta < 0) {

99:         if (settlementParams.contractAddress == address(0)) {

122:         if (price == 0) {

146:         if (settlementParams.contractAddress == address(0)) {

169:         if (price == 0) {

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L34-L34), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L47-L47), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L67-L67), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L77-L77), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L99-L99), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L122-L122), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L146-L146), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L169-L169)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

45:         if (interestRateQuote > 0 || interestRateBase > 0) {

61:         if (utilizationRatio == 0) {

```


*GitHub* : [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L45-L45), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L61-L61)

```solidity
📁 File: src/libraries/Perp.sol

165:         if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {

223:         if (totalLiquidityAmount == 0) {

349:         if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {

374:         if (_assetStatus.totalAmount == 0) {

390:         if (f0 == 0 && f1 == 0) {

432:         if (_tradeSqrtAmount == 0) {

443:         if (_tradeSqrtAmount > 0) {

450:         } else if (_tradeSqrtAmount < 0) {

535:         if (_userStatus.sqrtPerp.amount * _amount >= 0) {

551:         if (closeAmount > 0) {

553:         } else if (closeAmount < 0) {

560:         if (openAmount > 0) {

565:         } else if (openAmount < 0) {

593:         if (_isWithdraw && _assetStatus.borrowedAmount == 0) {

605:         if (_assetStatus.totalAmount == 0) {

626:         if (_userStatus.sqrtPerp.amount == 0) {

633:         if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {

646:             _userStatus.sqrtPerp.amount < 0

653:             _userStatus.sqrtPerp.amount < 0

659:         if (_userStatus.sqrtPerp.amount < 0) {

678:         if (_tradeAmount == 0) {

682:         if (_positionAmount * _tradeAmount >= 0) {

755:         if (_userStatus.sqrtPerp.amount * _tradeSqrtAmount >= 0) {

766:         if (openAmount != 0) {

768:             offsetUnderlying = LPMath.calculateAmount0OffsetWithTick(_tickUpper, openAmount.abs(), openAmount < 0);

771:             offsetStable = LPMath.calculateAmount1OffsetWithTick(_tickLower, openAmount.abs(), openAmount < 0);

773:             if (openAmount < 0) {

784:         if (closeAmount != 0) {

```


*GitHub* : [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L165-L165), [223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L223-L223), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L349-L349), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L374-L374), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L390-L390), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L432-L432), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L443-L443), [450](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L450-L450), [535](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L535-L535), [551](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L551-L551), [553](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L553-L553), [560](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L560-L560), [565](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L565-L565), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L593-L593), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L605-L605), [626](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L626-L626), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L633-L633), [646](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L646-L646), [653](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L653-L653), [659](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L659-L659), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L678-L678), [682](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L682-L682), [755](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L755-L755), [766](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L766-L766), [768](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L768-L768), [771](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L771-L771), [773](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L773-L773), [784](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L784-L784)

```solidity
📁 File: src/libraries/PerpFee.sol

73:         if (sqrtPerp.amount > 0) {

76:         } else if (sqrtPerp.amount < 0) {

101:         if (sqrtPerp.amount > 0) {

104:         } else if (sqrtPerp.amount < 0) {

117:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

142:         if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L73-L73), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L76-L76), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L101-L101), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L104-L104), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L142-L142)

```solidity
📁 File: src/libraries/PositionCalculator.sol

43:         bool isSafe = vaultValue >= minMargin && _vault.margin >= 0;

72:         isSafe = vaultValue >= minMargin && _vault.margin >= 0;

103:         if (debtRiskRatio == 0) {

142:         if (pairStatus.priceFeed != address(0)) {

176:         return _positionParams.amountSqrt != 0 || _positionParams.amountBase != 0;

210:         if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {

245:         if (squartPosition > 0) {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L43-L43), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L72-L72), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L103-L103), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L142-L142), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L176-L176), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L210-L210), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L245-L245)

```solidity
📁 File: src/libraries/Reallocation.sol

55:         if (availableAmount > 0) {

110:         require(tickSpacing > 0);

```


*GitHub* : [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L55-L55), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L110-L110)

```solidity
📁 File: src/libraries/ScaledAsset.sol

38:         if (_amount == 0) {

51:         if (_amount == 0) {

55:         require(_supplyTokenAmount > 0, "S3");

73:         return (a >= 0 && b >= 0) || (a < 0 && b < 0);

84:         if (userStatus.positionAmount > 0) {

86:         } else if (userStatus.positionAmount < 0) {

104:         if (closeAmount > 0) {

106:         } else if (closeAmount < 0) {

113:         if (openAmount > 0) {

117:         } else if (openAmount < 0) {

135:         if (_userStatus.positionAmount > 0) {

148:         if (_userStatus.positionAmount > 0) {

160:         require(accountState.positionAmount >= 0, "S1");

175:         require(accountState.positionAmount <= 0, "S1");

190:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

231:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L51-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L55-L55), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L73-L73), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L86-L86), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L106-L106), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L113-L113), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L117-L117), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L135-L135), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L148-L148), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L160-L160), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L175-L175), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L231-L231)

```solidity
📁 File: src/libraries/SlippageLib.sol

29:         if (tradeResult.averagePrice == 0) {

33:         if (tradeResult.averagePrice > 0) {

38:         } else if (tradeResult.averagePrice < 0) {

46:             maxAcceptableSqrtPriceRange > 0

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L33-L33), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L38-L38), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L46-L46)

```solidity
📁 File: src/libraries/Trade.sol

86:         if (totalBaseAmount == 0) {

103:         if (settledQuoteAmount * totalBaseAmount <= 0) {

```


*GitHub* : [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L86-L86), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L103-L103)

```solidity
📁 File: src/libraries/UniHelper.sol

78:         if (errMsg.length > 0) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L78-L78)

```solidity
📁 File: src/libraries/VaultLib.sol

30:         if (vaultId == 0) {

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L30-L30)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

153:         require(_pairs[_pairId].id == 0, "AAA");

210:         require(_poolOwner != address(0), "ADDZ");

```


*GitHub* : [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L153-L153), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L210-L210)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

84:             tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE

92:             if (remainingMargin > 0) {

93:                 if (vault.recipient != address(0)) {

101:             } else if (remainingMargin < 0) {

164:         if (vaultValue <= 0 || minMargin == 0) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L92-L92), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L93-L93), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L101-L101), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L164-L164)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

51:             if (deltaPositionBase != 0) {

60:                 if (exceedsQuote < 0) {

64:                 if (settledBaseAmount + deltaPositionBase != 0) {

68:                 if (exceedsQuote > 0) {

```


*GitHub* : [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L51-L51), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L60-L60), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L64-L64), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L68-L68)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

29:         if (_amount <= 0) {

64:         require(_amount > 0, "AZ");

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L29-L29), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L64-L64)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

79:         if (settledBaseAmount != 0) {

```


*GitHub* : [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L79-L79)

```solidity
📁 File: src/libraries/math/LPMath.sol

36:         if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {

74:         if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L36-L36), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L74-L74)

```solidity
📁 File: src/libraries/math/Math.sol

13:         return uint256(x >= 0 ? x : -x);

25:         if (x == 0) {

27:         } else if (x > 0) {

35:         if (x == 0) {

37:         } else if (x > 0) {

45:         if (x == 0) {

47:         } else if (x > 0) {

55:         if (b >= 0) {

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L13-L13), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L25-L25), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L37-L37), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L47-L47), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L55-L55)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

135:         uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;

```


*GitHub* : [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L135-L135)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

89:             if (tradeResult.minMargin == 0) {

90:                 if (callbackData.marginAmountUpdate != 0) {

117:                 if (marginAmountUpdate > 0) {

119:                 } else if (marginAmountUpdate < 0) {

178:         if (tradeResult.minMargin > 0) {

186:             tradeResult.vaultId, gammaOrder.positionId == 0, gammaOrder.info.trader, gammaOrder.pairId

197:         if (gammaOrder.positionId == 0) {

212:         if (gammaOrder.quantity != 0 || gammaOrder.quantitySqrt != 0 || gammaOrder.marginAmount != 0) {

233:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

256:         if (delta == 0) {

281:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

306:         if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {

342:         if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {

378:         if (userPosition.vaultId == 0) {

395:         if (vault.margin != 0 || vault.openPosition.perp.amount != 0 || vault.openPosition.sqrtPerp.amount != 0) {

421:         if (positionId == 0 || userPosition.vaultId == 0) {

```


*GitHub* : [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L89-L89), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L90-L90), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L117-L117), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L119-L119), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L178-L178), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L186-L186), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L197-L197), [212](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L212-L212), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L233-L233), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L256-L256), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L281-L281), [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L342-L342), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L378-L378), [395](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L395-L395), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L421-L421)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

65:             userPosition.hedgeInterval > 0

80:         if (userPosition.sqrtPriceTrigger == 0) {

122:         if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {

130:         if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {

197:         if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {

```


*GitHub* : [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L65-L65), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L80-L80), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L197-L197)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

38:             if (isLong && currentPositionAmount >= 0) {

42:             if (!isLong && currentPositionAmount <= 0) {

50:             if (currentPositionAmount == 0) {

54:             if (currentPositionAmount > 0) {

55:                 if (tradeAmount < 0) {

65:                 if (tradeAmount > 0) {

92:         if (limitPrice == 0 && stopPrice == 0) {

97:         } else if (limitPrice > 0 && stopPrice > 0) {

105:         } else if (limitPrice > 0) {

110:         } else if (stopPrice > 0) {

123:         if (tradeAmount == 0) {

127:         if (tradeAmount > 0 && limitPrice < tradePrice) {

131:         if (tradeAmount < 0 && limitPrice > tradePrice) {

155:         if (tradeAmount > 0) {

164:         } else if (tradeAmount < 0) {

199:         if (tradeAmount != 0) {

200:             if (tradeAmount > 0 && decayedPrice < tradePrice) {

204:             if (tradeAmount < 0 && decayedPrice > tradePrice) {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L38-L38), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L42-L42), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L50-L50), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L55-L55), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L65-L65), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L97-L97), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L105-L105), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L110-L110), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L123-L123), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L131-L131), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L155-L155), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L164-L164), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L199-L199), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L204-L204)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

119:             if (marginAmountUpdate > 0) {

125:             if (marginAmountUpdate > 0) {

127:             } else if (marginAmountUpdate < 0) {

181:         if (tradeAmount == 0) {

200:         if (tradeResult.minMargin > 0) {

207:         if (userPosition.vaultId == 0) {

257:         if (userPosition.vaultId == 0) {

290:         if (tradeAmount == 0) {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L119-L119), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L127-L127), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L181-L181), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L200-L200), [207](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L207-L207), [257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L257-L257), [290](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L290-L290)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

74:         uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;

```


*GitHub* : [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L74-L74)

```solidity
📁 File: src/types/GlobalData.sol

27:         if (vaultId <= 0 || globalData.vaultCount <= vaultId) revert IPredyPool.InvalidPairId();

31:         if (pairId <= 0 || globalData.pairsCount <= pairId) revert IPredyPool.InvalidPairId();

36:         if (globalData.lockData.locker != address(0)) {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L27-L27), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L31-L31), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L36-L36)

### [G-56]<a name="g-56"></a> Use assembly in place of `abi.decode` to extract calldata values more efficiently

Using inline assembly to extract calldata values can be more gas-efficient than using `abi.decode` in Solidity. Inline assembly gives more direct access to EVM operations, enabling optimized usage of calldata. However, assembly should be used judiciously as it's more prone to errors. Opt for this approach when performance is critical and the complexity it introduces is manageable

*There are 8 instance(s) of this issue:*

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

66:         SettlementParamsV3Internal memory settlementParamsV3 = abi.decode(settlementData, (SettlementParamsV3Internal));

```


*GitHub* : [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L66-L66)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

26:         return abi.decode(settlementData, (SettlementParams));

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L26-L26)

```solidity
📁 File: src/libraries/UniHelper.sol

68:         int56[] memory tickCumulatives = abi.decode(data, (int56[]));

```


*GitHub* : [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L68-L68)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

83:         CallbackData memory callbackData = abi.decode(tradeParams.extraData, (CallbackData));

```


*GitHub* : [83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L83-L83)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

145:         AuctionParams memory auctionParams = abi.decode(auctionData, (AuctionParams));

193:         AuctionParams memory auctionParams = abi.decode(auctionData, (AuctionParams));

```


*GitHub* : [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L145-L145), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L193-L193)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

106:         CallbackData memory callbackData = abi.decode(tradeParams.extraData, (CallbackData));

154:         PerpOrderV3 memory perpOrder = abi.decode(order.order, (PerpOrderV3));

```


*GitHub* : [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L106-L106), [154](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L154-L154)

### [G-57]<a name="g-57"></a> Use `byte32` in place of `string`

For strings of 32 char strings and below you can use bytes32 instead as it's more gas efficient

*There are 12 instance(s) of this issue:*

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

198:                 string.concat("Predy6-Supply-", erc20.name()),

199:                 string.concat("p", erc20.symbol()),

```


*GitHub* : [198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L198-L198), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L199-L199)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

101:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

102:     string internal constant PERMIT2_ORDER_TYPE = string(

```


*GitHub* : [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L101-L101), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L102-L102)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

28:         string memory side,

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L28-L28)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

46:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

47:     string internal constant PERMIT2_ORDER_TYPE = string(

```


*GitHub* : [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L47-L47)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

13:     string side;

48:     string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";

49:     string internal constant PERMIT2_ORDER_TYPE = string(

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L13-L13), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L49-L49)

```solidity
📁 File: src/tokenization/SupplyToken.sol

15:     constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L15-L15)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

6:     function description() external view returns (string memory);

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L6-L6)

### [G-58]<a name="g-58"></a> `internal` functions not called by the contract should be removed to save deployment gas



*There are 88 instance(s) of this issue:*

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

20:     function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {

```


*GitHub* : [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L20-L20)

```solidity
📁 File: src/base/BaseMarket.sol

104:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

114:     function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {

```


*GitHub* : [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L104-L104), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L114-L114)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

38:     function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
39:         internal
40:         onlyInitializing
41:     {

152:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

162:     function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L38-L41), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L152-L152), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L162-L162)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

25:     function decodeParams(bytes memory settlementData) internal pure returns (SettlementParams memory) {

29:     function validate(
30:         mapping(address settlementContractAddress => bool) storage _whiteListedSettlements,
31:         SettlementParams memory settlementParams
32:     ) internal view {

40:     function execSettlement(
41:         IPredyPool predyPool,
42:         address quoteToken,
43:         address baseToken,
44:         SettlementParams memory settlementParams,
45:         int256 baseAmountDelta
46:     ) internal {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L25-L25), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L29-L32), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L40-L46)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

26:     function applyInterestForToken(mapping(uint256 => DataType.PairStatus) storage pairs, uint256 pairId) internal {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L26-L26)

```solidity
📁 File: src/libraries/InterestRateModel.sol

18:     function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)
19:         internal
20:         pure
21:         returns (uint256)
22:     {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L18-L22)

```solidity
📁 File: src/libraries/PairLib.sol

5:     function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L5-L5)

```solidity
📁 File: src/libraries/Perp.sol

119:     function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)
120:         internal
121:         pure
122:         returns (SqrtPerpAssetStatus memory)
123:     {

145:     function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {

159:     function updateRebalanceInterestGrowth(
160:         DataType.PairStatus memory _pairStatus,
161:         SqrtPerpAssetStatus storage _sqrtAssetStatus
162:     ) internal {

202:     function reallocate(
203:         DataType.PairStatus storage _assetStatusUnderlying,
204:         SqrtPerpAssetStatus storage _sqrtAssetStatus
205:     ) internal returns (bool, bool, int256 deltaPositionBase, int256 deltaPositionQuote) {

345:     function settleUserBalance(DataType.PairStatus storage _pairStatus, UserStatus storage _userStatus) internal {

373:     function updateFeeAndPremiumGrowth(uint256 _pairId, SqrtPerpAssetStatus storage _assetStatus) internal {

426:     function computeRequiredAmounts(
427:         SqrtPerpAssetStatus storage _sqrtAssetStatus,
428:         bool _isQuoteZero,
429:         UserStatus memory _userStatus,
430:         int256 _tradeSqrtAmount
431:     ) internal returns (int256 requiredAmountUnderlying, int256 requiredAmountStable) {

470:     function updatePosition(
471:         DataType.PairStatus storage _pairStatus,
472:         UserStatus storage _userStatus,
473:         UpdatePerpParams memory _updatePerpParams,
474:         UpdateSqrtPerpParams memory _updateSqrtPerpParams
475:     ) internal returns (IPredyPool.Payoff memory payoff) {

815:     function finalizeReallocation(SqrtPerpAssetStatus storage sqrtPerpStatus) internal {

```


*GitHub* : [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L119-L123), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L145-L145), [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L159-L162), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L202-L205), [345](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L345-L345), [373](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L373-L373), [426](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L426-L431), [470](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L470-L475), [815](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L815-L815)

```solidity
📁 File: src/libraries/PerpFee.sol

16:     function computeUserFee(
17:         DataType.PairStatus memory assetStatus,
18:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:         Perp.UserStatus memory userStatus
20:     ) internal view returns (DataType.FeeAmount memory) {

41:     function settleUserFee(
42:         DataType.PairStatus storage assetStatus,
43:         mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
44:         Perp.UserStatus storage userStatus
45:     ) internal returns (DataType.FeeAmount memory) {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L16-L20), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L41-L45)

```solidity
📁 File: src/libraries/PositionCalculator.sol

34:     function isLiquidatable(
35:         DataType.PairStatus memory pairStatus,
36:         DataType.Vault memory _vault,
37:         DataType.FeeAmount memory feeAmount
38:     ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {

49:     function checkSafe(
50:         DataType.PairStatus memory pairStatus,
51:         DataType.Vault memory _vault,
52:         DataType.FeeAmount memory feeAmount
53:     ) internal view returns (int256 minMargin) {

135:     function getHasPosition(DataType.Vault memory _vault) internal pure returns (bool hasPosition) {

```


*GitHub* : [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L34-L38), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L49-L53), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L135-L135)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

14:     function calculatePremiumCurve(uint256 utilization) internal pure returns (uint256) {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L14-L14)

```solidity
📁 File: src/libraries/Reallocation.sol

18:     function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)
19:         internal
20:         view
21:         returns (int24 lower, int24 upper)
22:     {

92:     function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L18-L22), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L92-L92)

```solidity
📁 File: src/libraries/ScaledAsset.sol

29:     function createAssetStatus() internal pure returns (AssetStatus memory) {

33:     function createUserStatus() internal pure returns (UserStatus memory) {

37:     function addAsset(AssetStatus storage tokenState, uint256 _amount) internal returns (uint256 claimAmount) {

47:     function removeAsset(AssetStatus storage tokenState, uint256 _supplyTokenAmount, uint256 _amount)
48:         internal
49:         returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)
50:     {

76:     function updatePosition(
77:         ScaledAsset.AssetStatus storage tokenStatus,
78:         ScaledAsset.UserStatus storage userStatus,
79:         int256 _amount,
80:         uint256 _pairId,
81:         bool _isStable
82:     ) internal {

142:     function settleUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus storage _userStatus)
143:         internal
144:         returns (int256 interestFee)
145:     {

186:     function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)
187:         internal
188:         returns (uint256)
189:     {

230:     function getUtilizationRatio(AssetStatus memory tokenState) internal pure returns (uint256) {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L33-L33), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L47-L50), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L76-L82), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L142-L145), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L186-L189), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L230-L230)

```solidity
📁 File: src/libraries/SlippageLib.sol

21:     function checkPrice(
22:         uint256 sqrtBasePrice,
23:         IPredyPool.TradeResult memory tradeResult,
24:         uint256 slippageTolerance,
25:         uint256 maxAcceptableSqrtPriceRange
26:     ) internal pure {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L21-L26)

```solidity
📁 File: src/libraries/UniHelper.sol

13:     function getSqrtPrice(address uniswapPoolAddress) internal view returns (uint160 sqrtPrice) {

20:     function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {

27:     function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {

87:     function getFeeGrowthInsideLast(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)
88:         internal
89:         view
90:         returns (uint256 feeGrowthInside0LastX128, uint256 feeGrowthInside1LastX128)
91:     {

99:     function getFeeGrowthInside(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)
100:         internal
101:         view
102:         returns (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128)
103:     {

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L13-L13), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L20-L20), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L27-L27), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L87-L91), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L99-L103)

```solidity
📁 File: src/libraries/VaultLib.sol

11:     function getVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId)
12:         internal
13:         view
14:         returns (DataType.Vault storage vault)
15:     {

24:     function createOrGetVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId, uint256 pairId)
25:         internal
26:         returns (uint256)
27:     {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L11-L15), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L24-L27)

```solidity
📁 File: src/libraries/math/Bps.sol

7:     function upper(uint256 price, uint256 bps) internal pure returns (uint256) {

11:     function lower(uint256 price, uint256 bps) internal pure returns (uint256) {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L7-L7), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L11-L11)

```solidity
📁 File: src/libraries/math/LPMath.sol

10:     function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
11:         internal
12:         pure
13:         returns (int256)
14:     {

20:     function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
21:         internal
22:         pure
23:         returns (int256)
24:     {

108:     function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)
109:         internal
110:         pure
111:         returns (int256)
112:     {

134:     function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)
135:         internal
136:         pure
137:         returns (int256)
138:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L10-L14), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L20-L24), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L108-L112), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L134-L138)

```solidity
📁 File: src/libraries/math/Math.sol

12:     function abs(int256 x) internal pure returns (uint256) {

16:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

20:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

24:     function fullMulDivInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

34:     function fullMulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

44:     function mulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {

54:     function addDelta(uint256 a, int256 b) internal pure returns (uint256) {

62:     function calSqrtPriceToPrice(uint256 sqrtPrice) internal pure returns (uint256 price) {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L16-L16), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L20-L20), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L24-L24), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L34-L34), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L44-L44), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L54-L54), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L62-L62)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

8:     function decay(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime)
9:         internal
10:         view
11:         returns (uint256 decayedPrice)
12:     {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L8-L12)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

18:     function hash(OrderInfo memory info) internal pure returns (bytes32) {

```


*GitHub* : [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L18-L18)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

10:     function toPermit(ResolvedOrder memory order)
11:         internal
12:         pure
13:         returns (ISignatureTransfer.PermitTransferFrom memory)
14:     {

23:     function transferDetails(ResolvedOrder memory order, address to)
24:         internal
25:         pure
26:         returns (ISignatureTransfer.SignatureTransferDetails memory)
27:     {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L10-L14), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L23-L27)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

23:     function validate(ResolvedOrder memory resolvedOrder) internal view {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L23-L23)

```solidity
📁 File: src/markets/L2Decoder.sol

5:     function decodeSpotOrderParams(bytes32 args1, bytes32 args2)
6:         internal
7:         pure
8:         returns (
9:             bool isLimit,
10:             uint64 startTime,
11:             uint64 endTime,
12:             uint64 deadline,
13:             uint128 startAmount,
14:             uint128 endAmount
15:         )
16:     {

38:     function decodePerpOrderParams(bytes32 args)
39:         internal
40:         pure
41:         returns (uint64 deadline, uint64 pairId, uint8 leverage)
42:     {

50:     function decodePerpOrderV3Params(bytes32 args)
51:         internal
52:         pure
53:         returns (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side)
54:     {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L5-L16), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L38-L42), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L50-L54)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

5:     function addItem(uint256[] storage items, uint256 item) internal {

9:     function removeItem(uint256[] storage items, uint256 item) internal {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L5-L5), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L9-L9)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

43:     function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {

134:     function resolve(GammaOrder memory gammaOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L43-L43), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L134-L134)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

152:     function _executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)
153:         internal
154:         returns (IPredyPool.TradeResult memory tradeResult)
155:     {

211:     function _modifyAutoHedgeAndClose(GammaOrder memory gammaOrder, bytes memory sig) internal {

```


*GitHub* : [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L152-L155), [211](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L211-L211)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

48:     function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)
49:         internal
50:         pure
51:         returns (int256)
52:     {

```


*GitHub* : [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L48-L52)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

7:     function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)
8:         internal
9:         pure
10:         returns (GammaModifyInfo memory)
11:     {

38:     function decodeGammaParam(bytes32 args)
39:         internal
40:         pure
41:         returns (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage)
42:     {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L7-L11), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L38-L42)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

26:     function getFinalTradeAmount(
27:         int256 currentPositionAmount,
28:         string memory side,
29:         uint256 quantity,
30:         bool reduceOnly,
31:         bool closePosition
32:     ) internal pure returns (int256 finalTradeAmount) {

80:     function validateTrade(
81:         IPredyPool.TradeResult memory tradeResult,
82:         int256 tradeAmount,
83:         uint256 limitPrice,
84:         uint256 stopPrice,
85:         bytes memory auctionData
86:     ) internal view {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L26-L32), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L80-L86)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

265:     function _saveUserPosition(UserPosition storage userPosition, PerpOrder memory perpOrder) internal {

314:     function _verifyOrder(ResolvedOrder memory order, uint256 amount) internal {

```


*GitHub* : [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L265-L265), [314](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L314-L314)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

73:     function resolve(PerpOrder memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L73-L73)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

78:     function resolve(PerpOrderV3 memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L78-L78)

```solidity
📁 File: src/types/GlobalData.sol

26:     function validateVaultId(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId) internal view {

30:     function validate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal view {

35:     function initializeLock(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal {

46:     function callSettlementCallback(
47:         GlobalDataLibrary.GlobalData storage globalData,
48:         bytes memory settlementData,
49:         int256 deltaBaseAmount
50:     ) internal {

62:     function finalizeLock(GlobalDataLibrary.GlobalData storage globalData)
63:         internal
64:         returns (int256 paidQuote, int256 paidBase)
65:     {

72:     function take(GlobalDataLibrary.GlobalData storage globalData, bool isQuoteAsset, address to, uint256 amount)
73:         internal
74:     {

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L26-L26), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L30-L30), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L35-L35), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L46-L50), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L62-L65), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L72-L74)

### [G-59]<a name="g-59"></a> Use `x = x + y` instead of `x += y` for state variables

Using `x = x + y` instead of `x += y` for simple state variables can save 10 gas. The same applies to `-=` , `*=` , etc.

*There are 3 instance(s) of this issue:*

```solidity
📁 File: src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L29-L29)

### [G-60]<a name="g-60"></a> Use solady library where possible to save gas

The following OpenZeppelin imports have a Solady equivalent, as such they can be used to save GAS as Solady modules have been specifically designed to be as GAS efficient as possible

*There are 18 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

6: import {Initializable} from "@openzeppelin-upgradeable/contracts/proxy/utils/Initializable.sol";

8: import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L6-L6), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L8-L8)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

4: import {Initializable} from "@openzeppelin-upgradeable/contracts/proxy/utils/Initializable.sol";

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L4-L4)

```solidity
📁 File: src/libraries/Perp.sol

9: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L9-L9)

```solidity
📁 File: src/libraries/PerpFee.sol

4: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L4-L4)

```solidity
📁 File: src/libraries/PositionCalculator.sol

6: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L6-L6)

```solidity
📁 File: src/libraries/Reallocation.sol

7: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L7-L7)

```solidity
📁 File: src/libraries/ScaledAsset.sol

5: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L5-L5)

```solidity
📁 File: src/libraries/Trade.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L5-L5)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

4: import {IERC20Metadata} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L5-L5)

```solidity
📁 File: src/libraries/math/LPMath.sol

7: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L7-L7)

```solidity
📁 File: src/libraries/math/Math.sol

6: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L6-L6)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

6: import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L6-L6)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

7: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

8: import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L8-L8)

```solidity
📁 File: src/types/GlobalData.sol

5: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L5-L5)

### [G-61]<a name="g-61"></a> Variable declared within iteration

Please elaborate and generalise the following with detail and feel free to use your own knowledge and lmit ur words to 100 words please:

*There are 1 instance(s) of this issue:*

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

367:             uint256 positionId = userPositionIDs[i];

```


*GitHub* : [367](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L367-L367)

### [G-62]<a name="g-62"></a> Enable IR-based code generation

Enabling Intermediate Representation (IR)-based code generation in Solidity, via the --via-ir compiler flag or setting {'viaIR': true} in the configuration, activates an advanced optimization phase. This approach allows the compiler to perform more sophisticated optimizations across multiple functions, potentially leading to significant gas savings. IR-based compilation can optimize contract bytecode more efficiently, making smart contracts cheaper to deploy and execute by reducing the gas required for transactions

*There are 57 instance(s) of this issue:*

```solidity
📁 File: src/PredyPool.sol

28: contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {

```


*GitHub* : [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L28-L28)

```solidity
📁 File: src/PriceFeed.sol

9: contract PriceFeedFactory {

29: contract PriceFeed {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L9-L9), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L29-L29)

```solidity
📁 File: src/base/BaseHookCallback.sol

7: abstract contract BaseHookCallback is IHooks {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallback.sol#L7-L7)

```solidity
📁 File: src/base/BaseHookCallbackUpgradable.sol

8: abstract contract BaseHookCallbackUpgradable is Initializable, IHooks {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L8-L8)

```solidity
📁 File: src/base/BaseMarket.sol

10: abstract contract BaseMarket is IFillerMarket, BaseHookCallback, Owned {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L10-L10)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

11: abstract contract BaseMarketUpgradable is IFillerMarket, BaseHookCallbackUpgradable {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L11-L11)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

12: library SettlementCallbackLib {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L12-L12)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

8: library ApplyInterestLib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L8-L8)

```solidity
📁 File: src/libraries/Constants.sol

4: library Constants {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L4-L4)

```solidity
📁 File: src/libraries/DataType.sol

6: library DataType {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/DataType.sol#L6-L6)

```solidity
📁 File: src/libraries/InterestRateModel.sol

8: library InterestRateModel {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L8-L8)

```solidity
📁 File: src/libraries/PairLib.sol

4: library PairLib {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PairLib.sol#L4-L4)

```solidity
📁 File: src/libraries/Perp.sol

22: library Perp {

```


*GitHub* : [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L22-L22)

```solidity
📁 File: src/libraries/PerpFee.sol

12: library PerpFee {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PerpFee.sol#L12-L12)

```solidity
📁 File: src/libraries/PositionCalculator.sol

16: library PositionCalculator {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L16-L16)

```solidity
📁 File: src/libraries/PremiumCurveModel.sol

6: library PremiumCurveModel {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PremiumCurveModel.sol#L6-L6)

```solidity
📁 File: src/libraries/Reallocation.sol

12: library Reallocation {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L12-L12)

```solidity
📁 File: src/libraries/ScaledAsset.sol

9: library ScaledAsset {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L9-L9)

```solidity
📁 File: src/libraries/SlippageLib.sol

9: library SlippageLib {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L9-L9)

```solidity
📁 File: src/libraries/Trade.sol

19: library Trade {

```


*GitHub* : [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L19-L19)

```solidity
📁 File: src/libraries/UniHelper.sol

10: library UniHelper {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/UniHelper.sol#L10-L10)

```solidity
📁 File: src/libraries/VaultLib.sol

8: library VaultLib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L8-L8)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

16: library AddPairLogic {

```


*GitHub* : [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L16-L16)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

21: library LiquidationLogic {

```


*GitHub* : [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L21-L21)

```solidity
📁 File: src/libraries/logic/ReaderLogic.sol

13: library ReaderLogic {

```


*GitHub* : [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReaderLogic.sol#L13-L13)

```solidity
📁 File: src/libraries/logic/ReallocationLogic.sol

14: library ReallocationLogic {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/ReallocationLogic.sol#L14-L14)

```solidity
📁 File: src/libraries/logic/SupplyLogic.sol

14: library SupplyLogic {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/SupplyLogic.sol#L14-L14)

```solidity
📁 File: src/libraries/logic/TradeLogic.sol

15: library TradeLogic {

```


*GitHub* : [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L15-L15)

```solidity
📁 File: src/libraries/math/Bps.sol

4: library Bps {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Bps.sol#L4-L4)

```solidity
📁 File: src/libraries/math/LPMath.sol

9: library LPMath {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L9-L9)

```solidity
📁 File: src/libraries/math/Math.sol

9: library Math {

```


*GitHub* : [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L9-L9)

```solidity
📁 File: src/libraries/orders/DecayLib.sol

5: library DecayLib {

```


*GitHub* : [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/DecayLib.sol#L5-L5)

```solidity
📁 File: src/libraries/orders/OrderInfoLib.sol

12: library OrderInfoLib {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/OrderInfoLib.sol#L12-L12)

```solidity
📁 File: src/libraries/orders/Permit2Lib.sol

8: library Permit2Lib {

```


*GitHub* : [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/Permit2Lib.sol#L8-L8)

```solidity
📁 File: src/libraries/orders/ResolvedOrder.sol

14: library ResolvedOrderLib {

```


*GitHub* : [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/orders/ResolvedOrder.sol#L14-L14)

```solidity
📁 File: src/markets/L2Decoder.sol

4: library L2Decoder {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L4-L4)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

4: library ArrayLib {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L4-L4)

```solidity
📁 File: src/markets/gamma/GammaOrder.sol

23: library GammaModifyInfoLib {

78: library GammaOrderLib {

```


*GitHub* : [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L23-L23), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaOrder.sol#L78-L78)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

24: contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L24-L24)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketL2.sol

40: contract GammaTradeMarketL2 is GammaTradeMarket {

```


*GitHub* : [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketL2.sol#L40-L40)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

11: library GammaTradeMarketLib {

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L11-L11)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketWrapper.sol

10: contract GammaTradeMarketWrapper is GammaTradeMarketL2 {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketWrapper.sol#L10-L10)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

6: library L2GammaDecoder {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L6-L6)

```solidity
📁 File: src/markets/perp/PerpMarket.sol

27: contract PerpMarket is PerpMarketV1 {

```


*GitHub* : [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarket.sol#L27-L27)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

10: library PerpMarketLib {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L10-L10)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

29: contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L29-L29)

```solidity
📁 File: src/markets/perp/PerpOrder.sol

24: library PerpOrderLib {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrder.sol#L24-L24)

```solidity
📁 File: src/markets/perp/PerpOrderV3.sol

25: library PerpOrderV3Lib {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpOrderV3.sol#L25-L25)

```solidity
📁 File: src/settlements/UniswapSettlement.sol

10: contract UniswapSettlement is ISettlement {

```


*GitHub* : [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L10-L10)

```solidity
📁 File: src/tokenization/SupplyToken.sol

7: contract SupplyToken is ERC20, ISupplyToken {

```


*GitHub* : [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L7-L7)

```solidity
📁 File: src/types/GlobalData.sol

12: library GlobalDataLibrary {

```


*GitHub* : [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L12-L12)

```solidity
📁 File: src/types/LockData.sol

6: library LockDataLibrary {

```


*GitHub* : [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/LockData.sol#L6-L6)

```solidity
📁 File: src/vendors/AggregatorV3Interface.sol

4: interface AggregatorV3Interface {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/AggregatorV3Interface.sol#L4-L4)

```solidity
📁 File: src/vendors/IPyth.sol

4: interface IPyth {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IPyth.sol#L4-L4)

```solidity
📁 File: src/vendors/IUniswapV3PoolOracle.sol

4: interface IUniswapV3PoolOracle {

```


*GitHub* : [4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/vendors/IUniswapV3PoolOracle.sol#L4-L4)

### [G-63]<a name="g-63"></a> Using XOR (^) and AND (&) bitwise equivalents for gas optimizations

Given 4 variables a, b, c and d represented as such:

```

0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d

```

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

*There are 72 instance(s) of this issue:*

```solidity
📁 File: src/PriceFeed.sol

50:         require(basePrice.expo == -8, "INVALID_EXP");

```


*GitHub* : [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PriceFeed.sol#L50-L50)

```solidity
📁 File: src/base/BaseMarket.sol

98:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarket.sol#L98-L98)

```solidity
📁 File: src/base/BaseMarketUpgradable.sol

146:         if (_quoteTokenMap[pairId] == address(0)) {

```


*GitHub* : [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L146-L146)

```solidity
📁 File: src/base/SettlementCallbackLib.sol

99:         if (settlementParams.contractAddress == address(0)) {

122:         if (price == 0) {

146:         if (settlementParams.contractAddress == address(0)) {

169:         if (price == 0) {

```


*GitHub* : [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L99-L99), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L122-L122), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L146-L146), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/SettlementCallbackLib.sol#L169-L169)

```solidity
📁 File: src/libraries/ApplyInterestLib.sol

61:         if (utilizationRatio == 0) {

```


*GitHub* : [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ApplyInterestLib.sol#L61-L61)

```solidity
📁 File: src/libraries/Perp.sol

223:         if (totalLiquidityAmount == 0) {

349:         if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {

374:         if (_assetStatus.totalAmount == 0) {

390:         if (f0 == 0 && f1 == 0) {

432:         if (_tradeSqrtAmount == 0) {

446:             if (_sqrtAssetStatus.totalAmount == _sqrtAssetStatus.borrowedAmount) {

546:         if (_assetStatus.totalAmount == _assetStatus.borrowedAmount) {

593:         if (_isWithdraw && _assetStatus.borrowedAmount == 0) {

605:         if (_assetStatus.totalAmount == 0) {

626:         if (_userStatus.sqrtPerp.amount == 0) {

633:         if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {

678:         if (_tradeAmount == 0) {

```


*GitHub* : [223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L223-L223), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L349-L349), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L374-L374), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L390-L390), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L432-L432), [446](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L446-L446), [546](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L546-L546), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L593-L593), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L605-L605), [626](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L626-L626), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L633-L633), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L678-L678)

```solidity
📁 File: src/libraries/PositionCalculator.sol

103:         if (debtRiskRatio == 0) {

```


*GitHub* : [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L103-L103)

```solidity
📁 File: src/libraries/ScaledAsset.sol

38:         if (_amount == 0) {

51:         if (_amount == 0) {

85:             require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");

87:             require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");

190:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

231:         if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {

```


*GitHub* : [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L51-L51), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L85-L85), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L87-L87), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L231-L231)

```solidity
📁 File: src/libraries/SlippageLib.sol

29:         if (tradeResult.averagePrice == 0) {

```


*GitHub* : [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/SlippageLib.sol#L29-L29)

```solidity
📁 File: src/libraries/Trade.sol

86:         if (totalBaseAmount == 0) {

```


*GitHub* : [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Trade.sol#L86-L86)

```solidity
📁 File: src/libraries/VaultLib.sol

30:         if (vaultId == 0) {

```


*GitHub* : [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/VaultLib.sol#L30-L30)

```solidity
📁 File: src/libraries/logic/AddPairLogic.sol

78:         bool isQuoteZero = uniswapPool.token0() == stableTokenAddress;

153:         require(_pairs[_pairId].id == 0, "AAA");

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L78-L78), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L153-L153)

```solidity
📁 File: src/libraries/logic/LiquidationLogic.sol

84:             tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE

164:         if (vaultValue <= 0 || minMargin == 0) {

```


*GitHub* : [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L84-L84), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L164-L164)

```solidity
📁 File: src/libraries/math/LPMath.sol

36:         if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {

74:         if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {

```


*GitHub* : [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L36-L36), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L74-L74)

```solidity
📁 File: src/libraries/math/Math.sol

25:         if (x == 0) {

35:         if (x == 0) {

45:         if (x == 0) {

```


*GitHub* : [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L25-L25), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L35-L35), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L45-L45)

```solidity
📁 File: src/markets/L2Decoder.sol

26:         if (isLimitUint == 1) {

68:         reduceOnly = reduceOnlyUint == 1;

69:         closePosition = closePositionUint == 1;

70:         side = sideUint == 1;

```


*GitHub* : [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L26-L26), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L68-L68), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L69-L69), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L70-L70)

```solidity
📁 File: src/markets/gamma/ArrayLib.sol

24:             if (items[i] == item) {

```


*GitHub* : [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/ArrayLib.sol#L24-L24)

```solidity
📁 File: src/markets/gamma/GammaTradeMarket.sol

86:         if (callbackData.callbackType == GammaTradeMarketLib.CallbackType.QUOTE) {

89:             if (tradeResult.minMargin == 0) {

110:                     callbackData.callbackType == GammaTradeMarketLib.CallbackType.TRADE

146:         if (closeRatio == 1e18) {

186:             tradeResult.vaultId, gammaOrder.positionId == 0, gammaOrder.info.trader, gammaOrder.pairId

197:         if (gammaOrder.positionId == 0) {

233:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

256:         if (delta == 0) {

281:         if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {

306:         if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {

342:         if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {

378:         if (userPosition.vaultId == 0) {

421:         if (positionId == 0 || userPosition.vaultId == 0) {

```


*GitHub* : [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L86-L86), [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L89-L89), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L110-L110), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L146-L146), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L186-L186), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L197-L197), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L233-L233), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L256-L256), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L281-L281), [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L342-L342), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L378-L378), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L421-L421)

```solidity
📁 File: src/markets/gamma/GammaTradeMarketLib.sol

80:         if (userPosition.sqrtPriceTrigger == 0) {

```


*GitHub* : [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarketLib.sol#L80-L80)

```solidity
📁 File: src/markets/gamma/L2GammaDecoder.sol

78:         if (isEnabledUint == 1) {

```


*GitHub* : [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L78-L78)

```solidity
📁 File: src/markets/perp/PerpMarketLib.sol

33:         bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));

50:             if (currentPositionAmount == 0) {

92:         if (limitPrice == 0 && stopPrice == 0) {

123:         if (tradeAmount == 0) {

179:         if (price1 == price2) {

```


*GitHub* : [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L33-L33), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L50-L50), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L92-L92), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L123-L123), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L179-L179)

```solidity
📁 File: src/markets/perp/PerpMarketV1.sol

109:         if (callbackData.callbackSource == CallbackSource.QUOTE) {

111:         } else if (callbackData.callbackSource == CallbackSource.QUOTE3) {

113:         } else if (callbackData.callbackSource == CallbackSource.TRADE3) {

181:         if (tradeAmount == 0) {

207:         if (userPosition.vaultId == 0) {

257:         if (userPosition.vaultId == 0) {

290:         if (tradeAmount == 0) {

```


*GitHub* : [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L109-L109), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L111-L111), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L113-L113), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L181-L181), [207](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L207-L207), [257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L257-L257), [290](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L290-L290)

```solidity
📁 File: src/tokenization/SupplyToken.sol

11:         require(_controller == msg.sender, "ST0");

```


*GitHub* : [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L11-L11)

