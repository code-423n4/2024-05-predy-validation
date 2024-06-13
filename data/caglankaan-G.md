### Prevent re-setting a state variable with the same value
Not only is wasteful in terms of gas, but this is especially problematic when an event is emitted and the old and new values set are the same, as listeners might not expect this kind of scenario.

```solidity
Path: ./src/PredyPool.sol

303:        allowedTraders[trader][pairId] = enabled;	// @audit-issue
```
[303](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L303-L303), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue

93:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue

99:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L93-L93), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L99-L99), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

44:        whitelistFiller = _whitelistFiller;	// @audit-issue

46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue

141:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue

147:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L46-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L133-L133), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L141-L141), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L147-L147), 


#### Recommendation

Implement checks in your Solidity contracts to avoid re-setting state variables to their existing values. Prior to updating a state variable, compare the new value with the current value and proceed with the assignment only if they differ. Additionally, ensure that events related to state variable updates are emitted only when actual changes occur. This approach not only saves gas but also prevents confusion and unnecessary triggers in event listeners. Regularly reviewing and optimizing your contract for such redundancies can significantly enhance efficiency and clarity in contract operations.

### Cache Local Variable Array Length In For Loop
If not cached, the solidity compiler will always read the length of the array during each iteration. If it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

23:        for (uint256 i = 0; i < items.length; i++) {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L23-L23), 


#### Recommendation

To optimize gas consumption, cache the length of memory arrays before for loop iterations in Solidity. This reduces redundant mload operations, saving 3 gas per iteration after the first and improving overall contract efficiency.

### Another Constant With Same Value Is Already Defined
Defining multiple constants with the same value in a Solidity contract introduces unnecessary redundancy and can lead to confusion and potential errors in contract maintenance. Constants are used to define values that remain unchanged throughout the contract's lifecycle, enhancing readability and efficiency. However, when multiple constants representing the same value are defined, it not only clutters the code but also complicates the process of updating values and understanding the contract's logic. Consolidating these constants into a single definition improves code clarity and maintainability.

```solidity
Path: ./src/libraries/Constants.sol

8:    uint256 internal constant MAX_PAIRS = 18446744073709551616;	// @audit-issue: Same value `18446744073709551616` is already defined on line `7` as variable: `MAX_VAULTS`

29:    uint256 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;	// @audit-issue: Same value `12422` is already defined on line `25` as variable: `BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE`
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L8-L8), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L29-L29), 


#### Recommendation

Identify and consolidate duplicate constants in your Solidity contracts. Examine your contract's constants to find any with identical values and refactor your code to use a single constant definition for each unique value. This may involve choosing the most descriptive and appropriate name for the consolidated constant and updating all references accordingly. Such consolidation not only makes your contract more concise and easier to read but also simplifies future updates to constant values. Document the purpose and usage of each constant clearly, ensuring that the chosen names accurately reflect their role within the contract.

### Same cast is done multiple times
It's cheaper to do it once, and store the result to a variable.

```solidity
Path: ./src/PriceFeed.sol

21:        emit PriceFeedCreated(quotePrice, priceId, decimalsDiff, address(priceFeed));	// @audit-issue: Same casting also done in line(s): `[23]`.
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L21-L21), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

120:                cost = uint256(marginAmountUpdate);	// @audit-issue: Same casting also done in line(s): `[126]`.
```
[120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L120-L120), 


```solidity
Path: ./src/libraries/UniHelper.sol

58:            secondsAgos[0] = uint32(ago);	// @audit-issue: Same casting also done in line(s): `[38]`.

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue: Same casting also done in line(s): `[60]`.
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L58-L58), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), 


```solidity
Path: ./src/libraries/Perp.sol

168:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);	// @audit-issue: Same casting also done in line(s): `[172]`.

554:            if (getAvailableSqrtAmount(_assetStatus, true) < uint256(-closeAmount)) {	// @audit-issue: Same casting also done in line(s): `[557]`.

570:            _assetStatus.borrowedAmount += uint256(-openAmount);	// @audit-issue: Same casting also done in line(s): `[566]`.
```
[168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L168-L168), [554](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L554-L554), [570](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L570-L570), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

108:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");	// @audit-issue: Same casting also done in line(s): `[110]`.

120:            tokenStatus.totalNormalBorrowed += uint256(-openAmount);	// @audit-issue: Same casting also done in line(s): `[118]`.
```
[108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L108-L108), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L120-L120), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue: Same casting also done in line(s): `[63]`.
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

105:            ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);	// @audit-issue: Same casting also done in line(s): `[123, 133]`.

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue: Same casting also done in line(s): `[170, 180, 166]`.
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L105-L105), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), 


#### Recommendation

Identify instances in your Solidity contracts where the same value is cast to another type multiple times. Refactor these operations by performing the cast once and storing the result in a local variable. Use this variable for all subsequent operations requiring the cast value.

### `address(this)` should be cached
Cacheing saves gas when compared to repeating the calculation at each point it is used in the contract.

```solidity
Path: ./src/libraries/Perp.sol

273:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, type(uint128).max, type(uint128).max	// @audit-issue: `adress(this)` also used on line(s): [280]
```
[273](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L273-L273), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue: `adress(this)` also used on line(s): [119, 110]

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue: `adress(this)` also used on line(s): [157]
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), 


```solidity
Path: ./src/types/GlobalData.sol

40:        globalData.lockData.quoteReserve = ERC20(globalData.pairs[pairId].quotePool.token).balanceOf(address(this));	// @audit-issue: `adress(this)` also used on line(s): [41]
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L40-L40), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### `event` Declared But Not Emitted
An event within the contract is declared but not utilized in any of the contract's functions or operations. Having unused event declarations can consume unnecessary space and may lead to misunderstandings for developers or users expecting this event as part of the contract's functionality.


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

70:    event PerpTraded(	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L70-L70), 


#### Recommendation


Consider removing the unused event declaration to optimize the contract and enhance clarity. If there is an intent for this event to be part of certain operations, ensure it is emitted appropriately. Otherwise, for the sake of clean and efficient code, it is advisable to remove any unused declarations.


### Custom Errors in Solidity for Gas Efficiency
Starting from Solidity version 0.8.4, the language introduced a feature known as "custom errors". These custom errors provide a way for developers to define more descriptive and semantically meaningful error conditions without relying on string messages. Prior to this version, developers often used the `require` statement with string error messages to handle specific conditions or validations. However, every unique string used as a revert reason consumes gas, making transactions more expensive.

Custom errors, on the other hand, are identified by their name and the types of their parameters only, and they do not have the overhead of string storage. This means that, when using custom errors instead of `require` statements with string messages, the gas consumption can be significantly reduced, leading to more gas-efficient contracts.

```solidity
Path: ./src/PredyPool.sol

80:        require(allowedUniswapPools[msg.sender]);	// @audit-issue

98:        require(newOperator != address(0));	// @audit-issue

182:        require(amount > 0, "AZ");	// @audit-issue

204:        require(amount > 0, "AZ");	// @audit-issue

301:        require(globalData.pairs[pairId].allowlistEnabled);	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L80-L80), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L98-L98), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L204-L204), [301](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L301-L301), 


```solidity
Path: ./src/PriceFeed.sol

50:        require(basePrice.expo == -8, "INVALID_EXP");	// @audit-issue

52:        require(quoteAnswer > 0 && basePrice.price > 0);	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L50-L50), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L52-L52), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

201:        require(modifyInfo.maxSlippageTolerance >= modifyInfo.minSlippageTolerance);	// @audit-issue

202:        require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);	// @audit-issue

203:        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);	// @audit-issue
```
[201](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L201-L201), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L202-L202), [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L203-L203), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

266:        require(perpOrder.slippageTolerance <= Bps.ONE);	// @audit-issue
```
[266](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L266-L266), 


```solidity
Path: ./src/libraries/UniHelper.sol

84:        revert("e/empty-error");	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L84-L84), 


```solidity
Path: ./src/libraries/Reallocation.sol

110:        require(tickSpacing > 0);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L110-L110), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

55:        require(_supplyTokenAmount > 0, "S3");	// @audit-issue

67:        require(getAvailableCollateralValue(tokenState) >= finalWithdrawAmount, "S0");	// @audit-issue

85:            require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");

87:            require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");

108:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");

118:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-openAmount), "S0");

160:        require(accountState.positionAmount >= 0, "S1");	// @audit-issue

175:        require(accountState.positionAmount <= 0, "S1");	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L67-L67), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L85-L85), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L87-L87), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L108-L108), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L118-L118), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L160-L160), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L175-L175), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

60:        require(pairId < Constants.MAX_PAIRS, "MAXP");	// @audit-issue

76:        require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");	// @audit-issue

153:        require(_pairs[_pairId].id == 0, "AAA");	// @audit-issue

206:        require(_fee <= 20, "FEE");	// @audit-issue

210:        require(_poolOwner != address(0), "ADDZ");	// @audit-issue

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue

216:        require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");	// @audit-issue

220:        require(	// @audit-issue
221:            _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18
222:                && _irmParams.slope2 <= 10 * 1e18,
223:            "C4"
224:        );
```
[60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L60-L60), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L76-L76), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L153-L153), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L206-L206), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L210-L210), [214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L216-L216), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L220-L224), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

64:        require(_amount > 0, "AZ");	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

45:        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L45-L45), 


```solidity
Path: ./src/base/BaseMarket.sol

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

11:        require(_controller == msg.sender, "ST0");	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L11-L11), 


#### Recommendation


It is recommended to use custom errors instead of revert strings to reduce gas costs, especially during contract deployment. Custom errors can be defined using the error keyword and can include dynamic information.

### Avoid repeating computations
In Solidity development, repeating the same computations within a contract can lead to unnecessary gas consumption and reduce the contract's efficiency. This is particularly relevant in functions that are called frequently or involve complex calculations. Repeating computations not only wastes computational resources but also increases the cost of executing transactions. By identifying and eliminating redundant calculations, developers can optimize contract performance, reduce gas costs, and improve overall execution speed.

```solidity
Path: ./src/PredyPool.sol

182:        require(amount > 0, "AZ");	// @audit-issue: Same binary operation statement in line(s) between: ['186:186']

204:        require(amount > 0, "AZ");	// @audit-issue: Same binary operation statement in line(s) between: ['208:208']
```
[182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L204-L204), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

186:            tradeResult.vaultId, gammaOrder.positionId == 0, gammaOrder.info.trader, gammaOrder.pairId	// @audit-issue: Same binary operation statement in line(s) between: ['197:197']
```
[186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L186-L186), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

66:                && userPosition.lastHedgedTime + userPosition.hedgeInterval <= block.timestamp	// @audit-issue: Same binary operation statement in line(s) between: ['71:71']
```
[66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L66-L66), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

119:            if (marginAmountUpdate > 0) {	// @audit-issue: Same binary operation statement in line(s) between: ['125:125']
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L119-L119), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

97:        } else if (limitPrice > 0 && stopPrice > 0) {	// @audit-issue: Same binary operation statement in line(s) between: ['105:105']

97:        } else if (limitPrice > 0 && stopPrice > 0) {	// @audit-issue: Same binary operation statement in line(s) between: ['110:110']
```
[97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L97-L97), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L97-L97), 


```solidity
Path: ./src/libraries/Reallocation.sol

67:                    upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue: Same binary operation statement in line(s) between: ['80:80']

139:        if (minLowerTick > currentLowerTick - tickSpacing) {	// @audit-issue: Same binary operation statement in line(s) between: ['140:140']

160:        if (maxUpperTick < currentUpperTick + tickSpacing) {	// @audit-issue: Same binary operation statement in line(s) between: ['161:161']
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L67-L67), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L139-L139), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L160-L160), 


```solidity
Path: ./src/libraries/Perp.sol

399:            f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue: Same binary operation statement in line(s) between: ['402:402']

405:        _assetStatus.borrowPremium0Growth += FullMath.mulDiv(f0, 1000 + spreadParam, 1000);	// @audit-issue: Same binary operation statement in line(s) between: ['406:406']

646:            _userStatus.sqrtPerp.amount < 0	// @audit-issue: Same binary operation statement in line(s) between: ['653:653', '659:659']

692:                payoff = _valueUpdate - closeStableAmount;	// @audit-issue: Same binary operation statement in line(s) between: ['700:700']
```
[399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L399-L399), [405](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L405-L405), [646](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L646-L646), [692](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L692-L692), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

76:        require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");	// @audit-issue: Same binary operation statement in line(s) between: ['78:78']
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L76-L76), 


```solidity
Path: ./src/libraries/math/LPMath.sol

40:        bool swaped = sqrtRatioA > sqrtRatioB;	// @audit-issue: Same binary operation statement in line(s) between: ['42:42']

53:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue: Same binary operation statement in line(s) between: ['58:58']

78:        bool swaped = sqrtRatioA < sqrtRatioB;	// @audit-issue: Same binary operation statement in line(s) between: ['80:80']

90:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue: Same binary operation statement in line(s) between: ['95:95']
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L40-L40), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L53-L53), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L78-L78), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L90-L90), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

101:            uint256 quoteAmount = sellAmount * price / Constants.Q96;	// @audit-issue: Same binary operation statement in line(s) between: ['125:125']

148:            uint256 quoteAmount = buyAmount * price / Constants.Q96;	// @audit-issue: Same binary operation statement in line(s) between: ['172:172']
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L101-L101), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L148-L148), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Unused State Variables
There are state variables which are declared but not used in any function. This might increase the contract's gas usage unnecessarily.

```solidity
Path: ./src/libraries/Constants.sol

7:    uint256 internal constant MAX_VAULTS = 18446744073709551616;	// @audit-issue

15:    uint256 internal constant MIN_SQRT_PRICE = 79228162514264337593;	// @audit-issue

16:    uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;	// @audit-issue

25:    uint256 internal constant BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE = 12422;	// @audit-issue

27:    uint256 internal constant MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE = 24710;	// @audit-issue

29:    uint256 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L7-L7), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L16-L16), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L25-L25), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L27-L27), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L29-L29), 


#### Recommendation

To optimize your contract's gas usage, remove any state variables that are declared but not used in any function. Unnecessary state variables can increase gas costs and should be eliminated during code review.

### Unused `struct`s
Note that there may be cases where a struct superficially appears to be used, but this is only because there are multiple definitions of the struct in different files. In such cases, the struct definition should be moved into a separate file. The instances below are the unused definitions.

```solidity
Path: ./src/types/LockData.sol

7:    struct LockData {	// @audit-issue
8:        address locker;
9:        uint256 quoteReserve;
10:        uint256 baseReserve;
11:        uint256 pairId;
12:    }
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/LockData.sol#L7-L12), 


```solidity
Path: ./src/libraries/Perp.sol

54:    struct PositionStatus {	// @audit-issue
55:        int256 amount;
56:        int256 entryValue;
57:    }
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L54-L57), 


#### Recommendation

Identify and remove any unused `struct` definitions from your Solidity contracts. If a `struct` is defined multiple times across different files, centralize its definition in a single file and import it wherever required. This approach reduces code redundancy, ensures consistency in data structures, and helps maintain a clean and efficient codebase. Regularly auditing your contracts for unused or duplicated code elements like `structs` is a good practice for optimal contract maintenance and development.

### Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

36:    error TPSLConditionDoesNotMatch();	// @audit-issue

38:    error UpdateMarginMustNotBePositive();	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L36-L36), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L38-L38), 


#### Recommendation

Unused `error` definitions should be removed from the contract, and if needed, consolidated into a separate file to avoid duplication.

### Using Prefix Operators Costs Less Gas Than Postfix Operators in Loops
Conditions can be optimized issues in Solidity refer to situations where smart contract developers write conditional statements that can be simplified or optimized for better gas efficiency, readability, and maintainability. Optimizing conditions can lead to more cost-effective and secure smart contracts.

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

23:        for (uint256 i = 0; i < items.length; i++) {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L23-L23), 


```solidity
Path: ./src/libraries/VaultLib.sol

42:            globalData.vaultCount++;	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L42-L42), 


```solidity
Path: ./src/libraries/Perp.sol

818:        sqrtPerpStatus.numRebalance++;	// @audit-issue
```
[818](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L818-L818), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

91:        _global.pairsCount++;	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L91-L91), 


#### Recommendation

To improve gas efficiency in your Solidity code, prefer using prefix operators (e.g., `++i` or `--i`) instead of postfix operators (e.g., `i++` or `i--`) within loops. Prefix operators typically result in lower gas costs and can contribute to more efficient contract execution.

### Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
Path: ./src/PriceFeed.sol

35:    uint256 private constant VALID_TIME_PERIOD = 5 * 60;	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L35-L35), 


```solidity
Path: ./src/libraries/Constants.sol

32:    uint256 internal constant SQUART_KINK_UR = 10 * 1e16;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L32-L32), 


#### Recommendation

Precompute and assign literal values to constant variables in your Solidity contracts. Avoid defining constants with expressions or calculations. By providing direct values, you ensure that the compiler replaces these constants efficiently in the bytecode, maximizing gas savings and contract performance. Review your contracts to identify any constants defined with expressions and refactor them to use precomputed literal values instead.

### Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

```solidity
Path: ./src/PredyPool.sol

346:            UniHelper.getSqrtPrice(globalData.pairs[pairId].sqrtAssetStatus.uniswapPool),	// @audit-issue: Same index access of variable `globalData` is used also on line(s): ['347'].

386:            return globalData.pairs[pairId].quotePool;	// @audit-issue: Same index access of variable `globalData` is used also on line(s): ['388'].
```
[346](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L346-L346), [386](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L386-L386), 


```solidity
Path: ./src/base/BaseMarket.sol

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue: Same index access of variable `_quoteTokenMap` is used also on line(s): ['105'].
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue: Same index access of variable `_quoteTokenMap` is used also on line(s): ['153'].
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


#### Recommendation

When a mapping or array value is accessed multiple times within a function, cache it in a local `storage` or `calldata` variable. This approach minimizes the gas cost by reducing the number of hash computations for mappings and offset calculations for arrays. Carefully review your functions to identify opportunities for such optimizations, especially in functions with frequent or repeated accesses to the same mapping or array element, to enhance gas efficiency in your contracts.

### State Variables That Are Used Multiple Times In a Function Should Be Cached In Stack Variables
When performing multiple operations on a state variable in a function, it is recommended to cache it first. Either multiple reads or multiple writes to a state variable can save gas by caching it on the stack. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. Saves 100 gas per instance.


```solidity
Path: ./src/PredyPool.sol

346:            UniHelper.getSqrtPrice(globalData.pairs[pairId].sqrtAssetStatus.uniswapPool),	// @audit-issue: State variable `globalData` is used also on line(s): ['347'].

386:            return globalData.pairs[pairId].quotePool;	// @audit-issue: State variable `globalData` is used also on line(s): ['388'].
```
[346](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L346-L346), [386](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L386-L386), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

250:        DataType.Vault memory vault = _predyPool.getVault(userPosition.vaultId);	// @audit-issue: State variable `userPosition` is used also on line(s): ['233', '262', '233'].

237:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);	// @audit-issue: State variable `userPosition` is used also on line(s): ['261'].
```
[250](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L250-L250), [237](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L237-L237), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

188:                userPosition.vaultId,	// @audit-issue: State variable `userPosition` is used also on line(s): ['174', '207'].
```
[188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L188-L188), 


```solidity
Path: ./src/libraries/Trade.sol

46:            pairStatus.sqrtAssetStatus, pairStatus.isQuoteZero, openPosition, tradeParams.tradeAmountSqrt	// @audit-issue: State variable `pairStatus` is used also on line(s): ['49'].

49:        tradeResult.sqrtPrice = getSqrtPrice(pairStatus.sqrtAssetStatus.uniswapPool, pairStatus.isQuoteZero);	// @audit-issue: State variable `pairStatus` is used also on line(s): ['46'].
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L46-L46), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L49-L49), 


```solidity
Path: ./src/libraries/VaultLib.sol

44:            emit VaultCreated(vault.id, vault.owner, quoteToken, pairId);	// @audit-issue: State variable `vault` is used also on line(s): ['63', '46'].

44:            emit VaultCreated(vault.id, vault.owner, quoteToken, pairId);	// @audit-issue: State variable `vault` is used also on line(s): ['51'].
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L44-L44), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L44-L44), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

40:            applyInterestForPoolStatus(pairStatus.basePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);	// @audit-issue: State variable `pairStatus` is used also on line(s): ['32', '37'].

37:            applyInterestForPoolStatus(pairStatus.quotePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);	// @audit-issue: State variable `pairStatus` is used also on line(s): ['40'].
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L40-L40), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L37-L37), 


```solidity
Path: ./src/libraries/Perp.sol

799:                sqrtAsset.rebalancePositionQuote, _updateAmount0, _pairStatus.id, true	// @audit-issue: State variable `sqrtAsset` is used also on line(s): ['809'].

802:                sqrtAsset.rebalancePositionBase, _updateAmount1, _pairStatus.id, false	// @audit-issue: State variable `sqrtAsset` is used also on line(s): ['806'].
```
[799](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L799-L799), [802](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L802-L802), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

88:                pairStatus.sqrtAssetStatus.rebalanceInterestGrowthBase	// @audit-issue: State variable `pairStatus` is used also on line(s): ['40', '87', '91', '77', '85', '49', '76'].
```
[88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L88-L88), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

47:        DataType.PairStatus storage pairStatus = globalData.pairs[vault.openPosition.pairId];	// @audit-issue: State variable `vault` is used also on line(s): ['60', '50'].

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue: State variable `vault` is used also on line(s): ['50', '60', '47', '63'].

93:                if (vault.recipient != address(0)) {	// @audit-issue: State variable `vault` is used also on line(s): ['99'].
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L47-L47), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L93-L93), 


```solidity
Path: ./src/base/BaseMarket.sol

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue: State variable `_quoteTokenMap` is used also on line(s): ['105'].
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue: State variable `_quoteTokenMap` is used also on line(s): ['153'].
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


#### Recommendation

Cache state variables in stack or local memory variables within functions when they are used multiple times. This approach replaces costlier Gwarmaccess operations with cheaper stack reads, saving approximately 100 gas per instance and optimizing overall contract performance.

### Missing Constant Modifier For State Variables
State variables that are never re-assigned after their initial assignment should typically be declared with the `constant` modifier. This ensures that these variables remain unmodified and communicates their unchanging nature. Using the `constant` modifier also offers potential gas savings, as accessing these variables does not require any storage operations.

```solidity
Path: ./src/PredyPool.sol

36:    GlobalDataLibrary.GlobalData public globalData;	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L36-L36), 


#### Recommendation

Declare state variables that are not reassigned after initialization with the 'constant' modifier in Solidity. This clarifies their unchanging nature, improves code readability, and saves gas by avoiding storage operations when accessing these variables.

### Increments can be `unchecked` in for-loops
Newer versions of the Solidity compiler will check for integer overflows and underflows automatically. This provides safety but increases gas costs.
When an unsigned integer is guaranteed to never overflow, the unchecked feature of Solidity can be used to save gas costs.A common case for this is for-loops using a strictly-less-than comparision in their conditional statement.

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

23:        for (uint256 i = 0; i < items.length; i++) {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L23-L23), 


#### Recommendation

Use unchecked math to block overflow / underflow check to save Gas.

### Divisions can be unchecked to save gas
The expression type(int).min/(-1) is the only case where division causes an overflow. Therefore, uncheck can be used to save gas in scenarios where it is certain that such an overflow will not occur.

```solidity
Path: ./src/PriceFeed.sol

54:        uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);	// @audit-issue

55:        price = price * Constants.Q96 / _decimalsDiff;	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L55-L55), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

53:        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;	// @audit-issue

56:        return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;	// @audit-issue

84:        uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;	// @audit-issue

85:        uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;	// @audit-issue

150:        uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;	// @audit-issue

158:                + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE	// @audit-issue

176:        uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);	// @audit-issue

184:                + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L184-L184), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

233:        return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);	// @audit-issue

239:        return Math.abs(positionAmount) * price / Constants.Q96;	// @audit-issue
```
[233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L239-L239), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

87:        uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)	// @audit-issue

182:            return (price1 - price2) * Bps.ONE / price2;	// @audit-issue

184:            return (price2 - price1) * Bps.ONE / price2;	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L87-L87), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L184-L184), 


```solidity
Path: ./src/libraries/UniHelper.sol

29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


```solidity
Path: ./src/libraries/Reallocation.sol

51:        int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;	// @audit-issue

120:        result = (result / tickSpacing) * tickSpacing;	// @audit-issue

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue

184:        uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;	// @audit-issue

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L51-L51), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

93:            (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;	// @audit-issue

193:        uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;	// @audit-issue

194:        uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;	// @audit-issue

213:                (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L93-L93), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L213-L213), 


```solidity
Path: ./src/libraries/Trade.sol

128:        swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;	// @audit-issue

129:        swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;	// @audit-issue

132:        swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();	// @audit-issue
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L132-L132), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

66:        interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)	// @audit-issue

71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;	// @audit-issue

72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L66-L66), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L72-L72), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

21:        return (1600 * b * b / Constants.ONE) / Constants.ONE;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L21-L21), 


```solidity
Path: ./src/libraries/Perp.sol

166:            _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(	// @audit-issue

170:            _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(	// @audit-issue

399:            f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

402:            f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

590:        uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);	// @audit-issue

609:        uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;	// @audit-issue

689:                int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;	// @audit-issue

697:                int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;	// @audit-issue

785:            offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue

786:            offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue
```
[166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L166-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L170-L170), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L402-L402), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L590-L590), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L609-L609), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L697-L697), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L786-L786), 


```solidity
Path: ./src/libraries/SlippageLib.sol

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue

49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L49-L49), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

26:            ir += (utilizationRatio * irmParams.slope1) / _ONE;	// @audit-issue

28:            ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;	// @audit-issue

29:            ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L29-L29), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

32:                decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;	// @audit-issue

34:                decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L34-L34), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue

63:            -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,	// @audit-issue

168:        uint256 ratio = uint256(vaultValue * 1e4 / minMargin);	// @audit-issue

174:        return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L63-L63), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L168-L168), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L174-L174), 


```solidity
Path: ./src/libraries/math/Bps.sol

8:        return price * bps / ONE;	// @audit-issue

12:        return price * ONE / bps;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L12-L12), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

70:        uint256 fee = settlementParamsV3.feePrice * tradeAmountAbs / Constants.Q96;	// @audit-issue

76:        uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue

77:        uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L77-L77), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

101:            uint256 quoteAmount = sellAmount * price / Constants.Q96;	// @audit-issue

125:            uint256 quoteAmount = sellAmount * price / Constants.Q96;	// @audit-issue

148:            uint256 quoteAmount = buyAmount * price / Constants.Q96;	// @audit-issue

172:            uint256 quoteAmount = buyAmount * price / Constants.Q96;	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L101-L101), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L125-L125), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L148-L148), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L172-L172), 


#### Recommendation

Utilize 'unchecked' blocks in Solidity for divisions where overflow is impossible, such as when 'type(int).min/(-1)' is not a concern. This can save gas by bypassing overflow checks in these specific cases.

### Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
Unchecked keyword can be added to such scenerios: 
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

150:        uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;	// @audit-issue
```
[150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

182:            return (price1 - price2) * Bps.ONE / price2;	// @audit-issue

184:            return (price2 - price1) * Bps.ONE / price2;	// @audit-issue
```
[182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L184-L184), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L54-L54), 


```solidity
Path: ./src/libraries/Reallocation.sol

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

67:            * (block.timestamp - lastUpdateTimestamp) / 365 days;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L67-L67), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

19:        uint256 b = (utilization - Constants.SQUART_KINK_UR);	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L19-L19), 


```solidity
Path: ./src/libraries/Perp.sol

598:            return available - buffer;	// @audit-issue
```
[598](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L598-L598), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

29:            ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L29-L29), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

28:            uint256 elapsed = value - decayStartTime;	// @audit-issue

29:            uint256 duration = decayEndTime - decayStartTime;	// @audit-issue

32:                decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;	// @audit-issue

34:                decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L29-L29), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L34-L34), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue

130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);	// @audit-issue

175:                ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);	// @audit-issue

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L130-L130), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L175-L175), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), 


#### Recommendation

In scenarios where subtraction cannot result in underflow due to prior `require()` or `if`-statements, wrap these operations in an `unchecked` block to save gas. This optimization should only be applied when the safety of the operation is assured. Carefully analyze each case to confirm that underflow is impossible before implementing `unchecked` blocks, as incorrect usage can lead to vulnerabilities in the contract.

### Stack variable is only used once
If the variable is only accessed once, it's cheaper to use the assigned value directly that one time, and save the 3 gas the extra stack assignment would spend

```solidity
Path: ./src/PredyPool.sol

287:        DataType.Vault storage vault = globalData.vaults[vaultId];	// @audit-issue: vault used only on line: 289
```
[287](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L287-L287), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

47:        GammaModifyInfo memory modifyInfo = L2GammaDecoder.decodeGammaModifyInfo(	// @audit-issue: modifyInfo used only on line: 65
48:            order.modifyParam, order.lowerLimit, order.upperLimit, order.maximaDeviation
49:        );

50:        (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =	// @audit-issue: deadline used only on line: 55
51:            L2GammaDecoder.decodeGammaParam(order.param);

50:        (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =	// @audit-issue: slippageTolerance used only on line: 63
51:            L2GammaDecoder.decodeGammaParam(order.param);

50:        (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =	// @audit-issue: leverage used only on line: 64
51:            L2GammaDecoder.decodeGammaParam(order.param);

74:        GammaModifyInfo memory modifyInfo =	// @audit-issue: modifyInfo used only on line: 91
75:            L2GammaDecoder.decodeGammaModifyInfo(order.param, order.lowerLimit, order.upperLimit, order.maximaDeviation);
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L47-L49), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L50-L51), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L50-L51), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L50-L51), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L74-L75), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

12:        (	// @audit-issue: isEnabled used only on line: 24
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: expiration used only on line: 25
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: hedgeInterval used only on line: 29
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: sqrtPriceTrigger used only on line: 30
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: minSlippageTolerance used only on line: 31
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: maxSlippageTolerance used only on line: 32
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: auctionPeriod used only on line: 33
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);

12:        (	// @audit-issue: auctionRange used only on line: 34
13:            bool isEnabled,
14:            uint64 expiration,
15:            uint32 hedgeInterval,
16:            uint32 sqrtPriceTrigger,
17:            uint32 minSlippageTolerance,
18:            uint32 maxSlippageTolerance,
19:            uint16 auctionPeriod,
20:            uint32 auctionRange
21:        ) = decodeGammaModifyParam(args);
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L12-L21), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

84:        ERC20 quoteToken = ERC20(_getQuoteTokenAddress(tradeParams.pairId));	// @audit-issue: quoteToken used only on line: 118

156:        ResolvedOrder memory resolvedOrder = GammaOrderLib.resolve(gammaOrder, sig);	// @audit-issue: resolvedOrder used only on line: 160

216:        ResolvedOrder memory resolvedOrder = GammaOrderLib.resolve(gammaOrder, sig);	// @audit-issue: resolvedOrder used only on line: 218

221:        GammaTradeMarketLib.UserPosition storage userPosition =	// @audit-issue: userPosition used only on line: 224
222:            _getOrInitPosition(gammaOrder.positionId, false, gammaOrder.info.trader, gammaOrder.pairId);

240:        (bool hedgeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: hedgeRequired used only on line: 243
241:            GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);

240:        (bool hedgeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: slippageTorelance used only on line: 271
241:            GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);

240:        (bool hedgeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: triggerType used only on line: 265
241:            GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);

260:        IPredyPool.TradeParams memory tradeParams = IPredyPool.TradeParams(	// @audit-issue: tradeParams used only on line: 269
261:            userPosition.pairId,
262:            userPosition.vaultId,
263:            -delta,
264:            0,
265:            abi.encode(CallbackData(triggerType, userPosition.owner, 0))
266:        );

288:        (bool closeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: closeRequired used only on line: 291
289:            GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);

288:        (bool closeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: slippageTorelance used only on line: 312
289:            GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);

288:        (bool closeRequired, uint256 slippageTorelance, GammaTradeMarketLib.CallbackType triggerType) =	// @audit-issue: triggerType used only on line: 303
289:            GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);

403:        address trader = userPositions[positionId].owner;	// @audit-issue: trader used only on line: 405
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L84-L84), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L156-L156), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L216-L216), [221](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L221-L222), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L240-L241), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L240-L241), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L240-L241), [260](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L260-L266), [288](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L288-L289), [288](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L288-L289), [288](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L288-L289), [403](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L403-L403), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

10:        uint256 index = getItemIndex(items, item);	// @audit-issue: index used only on line: 12
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L10-L10), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

135:        uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;	// @audit-issue: amount used only on line: 137
```
[135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L135-L135), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

53:        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;	// @audit-issue: sqrtPrice used only on line: 56
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

74:        uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;	// @audit-issue: amount used only on line: 76
```
[74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L74-L74), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

107:        ERC20 quoteToken = ERC20(_getQuoteTokenAddress(tradeParams.pairId));	// @audit-issue: quoteToken used only on line: 126

154:        PerpOrderV3 memory perpOrder = abi.decode(order.order, (PerpOrderV3));	// @audit-issue: perpOrder used only on line: 156

165:        ResolvedOrder memory resolvedOrder = PerpOrderV3Lib.resolve(perpOrder, sig);	// @audit-issue: resolvedOrder used only on line: 193

229:        uint256 price = Math.calSqrtPriceToPrice(sqrtPrice);	// @audit-issue: price used only on line: 231

231:        uint256 netValue = _calculateNetValue(vault, price);	// @audit-issue: netValue used only on line: 233

237:        int256 positionAmount = vault.openPosition.perp.amount;	// @audit-issue: positionAmount used only on line: 239
```
[107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L107-L107), [154](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L154-L154), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L165-L165), [229](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L229-L229), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L231-L231), [237](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L237-L237), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue: deadline used only on line: 38
35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue: leverage used only on line: 46
35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue: reduceOnly used only on line: 47
35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue: closePosition used only on line: 48
35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue: side used only on line: 41
35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);

37:        PerpOrderV3 memory order = PerpOrderV3({	// @audit-issue: order used only on line: 52
38:            info: OrderInfo(address(this), compressedOrder.trader, compressedOrder.nonce, deadline),
39:            pairId: pairId,
40:            entryTokenAddress: _quoteTokenMap[pairId],
41:            side: side ? "Buy" : "Sell",
42:            quantity: compressedOrder.quantity,
43:            marginAmount: compressedOrder.marginAmount,
44:            limitPrice: compressedOrder.limitPrice,
45:            stopPrice: compressedOrder.stopPrice,
46:            leverage: leverage,
47:            reduceOnly: reduceOnly,
48:            closePosition: closePosition,
49:            auctionData: compressedOrder.auctionData
50:        });
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L35), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L35), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L35), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L35), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L35), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L37-L50), 


```solidity
Path: ./src/libraries/PerpFee.sol

25:            (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) = computeRebalanceInterest(	// @audit-issue: rebalanceInterestBase used only on line: 28
26:                assetStatus.id, assetStatus.sqrtAssetStatus, rebalanceFeeGrowthCache, userStatus
27:            );

25:            (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) = computeRebalanceInterest(	// @audit-issue: rebalanceInterestQuote used only on line: 29
26:                assetStatus.id, assetStatus.sqrtAssetStatus, rebalanceFeeGrowthCache, userStatus
27:            );

33:            (int256 feeUnderlying, int256 feeStable) = computePremium(assetStatus, userStatus.sqrtPerp);	// @audit-issue: feeUnderlying used only on line: 34

33:            (int256 feeUnderlying, int256 feeStable) = computePremium(assetStatus, userStatus.sqrtPerp);	// @audit-issue: feeStable used only on line: 35

51:        (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) =	// @audit-issue: rebalanceInterestBase used only on line: 58
52:            settleRebalanceInterest(assetStatus.id, assetStatus.sqrtAssetStatus, rebalanceFeeGrowthCache, userStatus);

51:        (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) =	// @audit-issue: rebalanceInterestQuote used only on line: 57
52:            settleRebalanceInterest(assetStatus.id, assetStatus.sqrtAssetStatus, rebalanceFeeGrowthCache, userStatus);

55:        (int256 feeUnderlying, int256 feeStable) = settlePremium(assetStatus, userStatus.sqrtPerp);	// @audit-issue: feeUnderlying used only on line: 58

55:        (int256 feeUnderlying, int256 feeStable) = settlePremium(assetStatus, userStatus.sqrtPerp);	// @audit-issue: feeStable used only on line: 57

146:            uint256 rebalanceAmount = Math.abs(userStatus.sqrtPerp.amount);	// @audit-issue: rebalanceAmount used only on line: 148
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L25-L27), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L25-L27), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L33-L33), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L33-L33), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L51-L52), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L51-L52), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L55-L55), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L55-L55), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L146-L146), 


```solidity
Path: ./src/libraries/UniHelper.sol

49:            (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();	// @audit-issue: index used only on line: 51

49:            (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();	// @audit-issue: cardinality used only on line: 51

51:            (uint32 oldestAvailableAge,,, bool initialized) = uniswapPool.observations((index + 1) % cardinality);	// @audit-issue: initialized used only on line: 53

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue: tick used only on line: 72

72:        uint160 sqrtPriceX96 = TickMath.getSqrtRatioAtTick(tick);	// @audit-issue: sqrtPriceX96 used only on line: 74

92:        bytes32 positionKey = PositionKey.compute(address(this), tickLower, tickUpper);	// @audit-issue: positionKey used only on line: 96
```
[49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L49-L49), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L51-L51), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L72-L72), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L92-L92), 


```solidity
Path: ./src/libraries/Reallocation.sol

23:        int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();	// @audit-issue: tickSpacing used only on line: 36

51:        int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;	// @audit-issue: previousCenterTick used only on line: 56

93:        (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue: currentTick used only on line: 95

132:        uint160 sqrtPrice =	// @audit-issue: sqrtPrice used only on line: 135
133:            calculateAmount1ForLiquidity(TickMath.getSqrtRatioAtTick(currentLowerTick), available, liquidityAmount);

153:        uint160 sqrtPrice =	// @audit-issue: sqrtPrice used only on line: 156
154:            calculateAmount0ForLiquidity(TickMath.getSqrtRatioAtTick(currentUpperTick), available, liquidityAmount);
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L23-L23), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L51-L51), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L93-L93), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L132-L133), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L153-L154), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

43:        bool isSafe = vaultValue >= minMargin && _vault.margin >= 0;	// @audit-issue: isSafe used only on line: 45

92:        int256 minMinValue =	// @audit-issue: minMinValue used only on line: 95
93:            (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;

136:        Perp.UserStatus memory userStatus = _vault.openPosition;	// @audit-issue: userStatus used only on line: 138

232:        uint256 price = Math.calSqrtPriceToPrice(_sqrtPrice);	// @audit-issue: price used only on line: 234
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L43-L43), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L92-L93), [136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L136-L136), [232](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L232-L232), 


```solidity
Path: ./src/libraries/Trade.sol

45:        (int256 underlyingAmountForSqrt, int256 stableAmountForSqrt) = Perp.computeRequiredAmounts(	// @audit-issue: underlyingAmountForSqrt used only on line: 56
46:            pairStatus.sqrtAssetStatus, pairStatus.isQuoteZero, openPosition, tradeParams.tradeAmountSqrt
47:        );

45:        (int256 underlyingAmountForSqrt, int256 stableAmountForSqrt) = Perp.computeRequiredAmounts(	// @audit-issue: stableAmountForSqrt used only on line: 69
46:            pairStatus.sqrtAssetStatus, pairStatus.isQuoteZero, openPosition, tradeParams.tradeAmountSqrt
47:        );

87:            int256 amountQuote = calculateStableAmount(sqrtPrice, 1e18).toInt256();	// @audit-issue: amountQuote used only on line: 89

117:        uint256 quoteAmount = (currentSqrtPrice * baseAmount) >> Constants.RESOLUTION;	// @audit-issue: quoteAmount used only on line: 119
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L45-L47), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L45-L47), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L87-L87), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L117-L117), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

36:        (uint256 interestRateQuote, uint256 protocolFeeQuote) =	// @audit-issue: protocolFeeQuote used only on line: 46
37:            applyInterestForPoolStatus(pairStatus.quotePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);

39:        (uint256 interestRateBase, uint256 protocolFeeBase) =	// @audit-issue: protocolFeeBase used only on line: 46
40:            applyInterestForPoolStatus(pairStatus.basePool, pairStatus.lastUpdateTimestamp, pairStatus.feeRatio);
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L36-L37), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L39-L40), 


```solidity
Path: ./src/libraries/Perp.sol

206:        (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue: currentSqrtPrice used only on line: 256

268:        (uint256 receivedAmount0, uint256 receivedAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).burn(	// @audit-issue: receivedAmount0 used only on line: 286
269:            _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount
270:        );

268:        (uint256 receivedAmount0, uint256 receivedAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).burn(	// @audit-issue: receivedAmount1 used only on line: 287
269:            _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount
270:        );

279:        (uint256 requiredAmount0, uint256 requiredAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).mint(	// @audit-issue: requiredAmount0 used only on line: 286
280:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""
281:        );

279:        (uint256 requiredAmount0, uint256 requiredAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).mint(	// @audit-issue: requiredAmount1 used only on line: 287
280:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""
281:        );

338:        bytes32 positionKey = PositionKey.compute(_controllerAddress, _tickLower, _tickUpper);	// @audit-issue: positionKey used only on line: 340

340:        (uint128 liquidity,,,,) = IUniswapV3Pool(_uniswapPool).positions(positionKey);	// @audit-issue: liquidity used only on line: 342

394:        uint256 utilization = getUtilizationRatio(_assetStatus);	// @audit-issue: utilization used only on line: 396

415:        (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128) =	// @audit-issue: feeGrowthInside0X128 used only on line: 418
416:            UniHelper.getFeeGrowthInside(_assetStatus.uniswapPool, _assetStatus.tickLower, _assetStatus.tickUpper);

415:        (uint256 feeGrowthInside0X128, uint256 feeGrowthInside1X128) =	// @audit-issue: feeGrowthInside1X128 used only on line: 419
416:            UniHelper.getFeeGrowthInside(_assetStatus.uniswapPool, _assetStatus.tickLower, _assetStatus.tickUpper);

462:        (int256 offsetUnderlying, int256 offsetStable) = calculateSqrtPerpOffset(	// @audit-issue: offsetUnderlying used only on line: 466
463:            _userStatus, _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _tradeSqrtAmount, _isQuoteZero
464:        );

462:        (int256 offsetUnderlying, int256 offsetStable) = calculateSqrtPerpOffset(	// @audit-issue: offsetStable used only on line: 467
463:            _userStatus, _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _tradeSqrtAmount, _isQuoteZero
464:        );

711:        (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).mint(	// @audit-issue: amount0 used only on line: 715
712:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""
713:        );

711:        (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).mint(	// @audit-issue: amount1 used only on line: 716
712:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""
713:        );

727:        (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).burn(	// @audit-issue: amount0 used only on line: 736
728:            _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128()
729:        );

727:        (uint256 amount0, uint256 amount1) = IUniswapV3Pool(_assetStatus.uniswapPool).burn(	// @audit-issue: amount1 used only on line: 737
728:            _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128()
729:        );
```
[206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L206-L206), [268](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L268-L270), [268](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L268-L270), [279](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L279-L281), [279](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L279-L281), [338](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L338-L338), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L340-L340), [394](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L394-L394), [415](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L415-L416), [415](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L415-L416), [462](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L462-L464), [462](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L462-L464), [711](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L711-L713), [711](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L711-L713), [727](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L727-L729), [727](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L727-L729), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

194:        uint256 protocolFee = FixedPointMathLib.mulDivDown(	// @audit-issue: protocolFee used only on line: 214
195:            FixedPointMathLib.mulDivDown(_interestRate, getTotalDebtValue(tokenState), Constants.ONE),
196:            _reserveFactor,
197:            100
198:        );
```
[194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L194-L198), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

19:        DataType.PairStatus memory pairStatus = globalData.pairs[pairId];	// @audit-issue: pairStatus used only on line: 21

35:        (int256 minMargin, int256 vaultValue,, uint256 oraclePice) =	// @audit-issue: minMargin used only on line: 39
36:            PositionCalculator.calculateMinMargin(pairStatus, vault, feeAmount);

35:        (int256 minMargin, int256 vaultValue,, uint256 oraclePice) =	// @audit-issue: vaultValue used only on line: 39
36:            PositionCalculator.calculateMinMargin(pairStatus, vault, feeAmount);

35:        (int256 minMargin, int256 vaultValue,, uint256 oraclePice) =	// @audit-issue: oraclePice used only on line: 39
36:            PositionCalculator.calculateMinMargin(pairStatus, vault, feeAmount);
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L19-L19), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L35-L36), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L35-L36), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L35-L36), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

66:        IUniswapV3Factory uniswapV3Factory = IUniswapV3Factory(_global.uniswapFactory);	// @audit-issue: uniswapV3Factory used only on line: 70
```
[66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L66-L66), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

56:                (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue: settledQuoteAmount used only on line: 58

56:                (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue: settledBaseAmount used only on line: 64
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L56-L56), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L56-L56), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

56:        (uint256 sqrtOraclePrice, uint256 slippageTolerance) =	// @audit-issue: slippageTolerance used only on line: 83
57:            checkVaultIsDanger(pairStatus, vault, globalData.rebalanceFeeGrowthCache);

138:        DataType.FeeAmount memory FeeAmount =	// @audit-issue: FeeAmount used only on line: 142
139:            PerpFee.computeUserFee(pairStatus, rebalanceFeeGrowthCache, vault.openPosition);
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L56-L57), [138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L138-L139), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

77:        (int256 marginAmountUpdate, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue: settledBaseAmount used only on line: 79
```
[77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L77-L77), 


```solidity
Path: ./src/libraries/math/LPMath.sol

46:        bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;	// @audit-issue: _isRoundUp used only on line: 49

84:        bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;	// @audit-issue: _isRoundUp used only on line: 86
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L46-L46), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L84-L84), 


```solidity
Path: ./src/base/BaseMarket.sol

109:        DataType.PairStatus memory pairStatus = _predyPool.getPairStatus(pairId);	// @audit-issue: pairStatus used only on line: 111
```
[109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L109-L109), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

76:        uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue: maxQuoteAmount used only on line: 83

77:        uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue: minQuoteAmount used only on line: 83

157:        DataType.PairStatus memory pairStatus = _predyPool.getPairStatus(pairId);	// @audit-issue: pairStatus used only on line: 159
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L77-L77), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L157-L157), 


#### Recommendation

Eliminate single-use stack variables in Solidity to optimize gas consumption. Directly use the assigned value in the place of the variable. This approach saves the 3 gas typically used for the extra stack assignment, streamlining the function's execution and enhancing overall gas efficiency.

### Storage Layout Optimization
Storage Layout Optimization in Solidity involves arranging state variables to minimize gas costs. Since storage is expensive, combining variables into as few slots as possible and deleting unneeded variables can significantly reduce the gas needed for contract operations.

```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

10:struct GammaOrderL2 {	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['nonce', 'positionId', 'quantity', 'quantitySqrt', 'marginAmount', 'baseSqrtPrice', 'param', 'modifyParam', 'lowerLimit', 'upperLimit', 'trader', 'maximaDeviation']`"]
11:    address trader;
12:    uint256 nonce;
13:    uint256 positionId;
14:    int256 quantity;
15:    int256 quantitySqrt;
16:    int256 marginAmount;
17:    uint256 baseSqrtPrice;
18:    bytes32 param;
19:    bytes32 modifyParam;
20:    uint256 lowerLimit;
21:    uint256 upperLimit;
22:    int64 maximaDeviation;
23:}

25:struct GammaModifyOrderL2 {	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['nonce', 'deadline', 'positionId', 'param', 'lowerLimit', 'upperLimit', 'trader', 'maximaDeviation']`"]
26:    address trader;
27:    uint256 nonce;
28:    uint256 deadline;
29:    uint256 positionId;
30:    bytes32 param;
31:    uint256 lowerLimit;
32:    uint256 upperLimit;
33:    int64 maximaDeviation;
34:}
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L10-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L25-L34), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

63:struct GammaOrder {	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['OrderInfo', 'GammaModifyInfo', 'positionId', 'quantity', 'quantitySqrt', 'marginAmount', 'baseSqrtPrice', 'entryTokenAddress', 'pairId', 'slippageTolerance', 'leverage']`"]
64:    OrderInfo info;
65:    uint64 pairId;
66:    uint256 positionId;
67:    address entryTokenAddress;
68:    int256 quantity;
69:    int256 quantitySqrt;
70:    int256 marginAmount;
71:    uint256 baseSqrtPrice;
72:    uint32 slippageTolerance;
73:    uint8 leverage;
74:    GammaModifyInfo modifyInfo;
75:}
```
[63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L63-L75), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

9:struct PerpOrderV3 {	// @audit-issue: ['Current storage layout is like this: ', "However it can be optimized by 1 storage by storing variables like in order: \n\t`['OrderInfo', 'side', 'quantity', 'marginAmount', 'limitPrice', 'stopPrice', 'auctionData', 'entryTokenAddress', 'pairId', 'leverage', 'reduceOnly', 'closePosition']`"]
10:    OrderInfo info;
11:    uint64 pairId;
12:    address entryTokenAddress;
13:    string side;
14:    uint256 quantity;
15:    uint256 marginAmount;
16:    uint256 limitPrice;
17:    uint256 stopPrice;
18:    uint8 leverage;
19:    bool reduceOnly;
20:    bool closePosition;
21:    bytes auctionData;
22:}
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L9-L22), 


#### Recommendation

To optimize storage and reduce gas costs, rearrange the storage variables in a way that makes the most of each 32-byte storage slot.

### `Internal` functions only called once can be inlined to save gas
If an internal function is only used once, there is no need to modularize it, unless the function calling it would otherwise be too long and complex. Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

```
[136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L136-L136), 


```solidity
Path: ./src/markets/L2Decoder.sol

50:    function decodePerpOrderV3Params(bytes32 args)	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L50-L50), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

211:    function _modifyAutoHedgeAndClose(GammaOrder memory gammaOrder, bytes memory sig) internal {	// @audit-issue

375:    function _getUserPosition(uint256 positionId) internal returns (UserPositionResult memory result) {	// @audit-issue

388:    function _addPositionIndex(address trader, uint256 newPositionId) internal {	// @audit-issue
```
[211](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L211-L211), [375](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L375-L375), [388](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L388-L388), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

38:    function decodeGammaParam(bytes32 args)	// @audit-issue

51:    function decodeGammaModifyParam(bytes32 args)	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L51-L51), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

61:    function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)	// @audit-issue
```
[61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L61-L61), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

15:    function removeItemByIndex(uint256[] storage items, uint256 index) internal {	// @audit-issue

20:    function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L15-L15), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L20-L20), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

43:    function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {	// @audit-issue

115:    function hash(GammaOrder memory order) internal pure returns (bytes32) {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L43-L43), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L115-L115), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

48:    function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L48-L48), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

54:    function hash(PerpOrder memory order) internal pure returns (bytes32) {	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L54-L54), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

220:    function _calculateInitialMargin(uint256 vaultId, uint256 pairId, uint256 leverage)	// @audit-issue

236:    function _calculateNetValue(DataType.Vault memory vault, uint256 price) internal pure returns (uint256) {	// @audit-issue

242:    function _calculatePositionValue(DataType.Vault memory vault, uint256 sqrtPrice) internal pure returns (int256) {	// @audit-issue

327:    function _verifyOrderV3(ResolvedOrder memory order, uint256 amount) internal {	// @audit-issue
```
[220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L220-L220), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L236-L236), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L242-L242), [327](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L327-L327), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

58:    function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L58-L58), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

80:    function validateTrade(	// @audit-issue

178:    function ratio(uint256 price1, uint256 price2) internal pure returns (uint256) {	// @audit-issue

188:    function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L80-L80), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L178-L178), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L188-L188), 


```solidity
Path: ./src/libraries/PerpFee.sol

41:    function settleUserFee(	// @audit-issue

95:    function settlePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus storage sqrtPerp)	// @audit-issue

136:    function settleRebalanceInterest(	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L41-L41), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L95-L95), [136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L136-L136), 


```solidity
Path: ./src/libraries/UniHelper.sol

20:    function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {	// @audit-issue

35:    function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L20-L20), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L35-L35), 


```solidity
Path: ./src/libraries/Reallocation.sol

39:    function _getNewRange(	// @audit-issue

92:    function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {	// @audit-issue

98:    function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)	// @audit-issue

126:    function calculateMinLowerTick(	// @audit-issue

147:    function calculateMaxUpperTick(	// @audit-issue

165:    function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)	// @audit-issue

179:    function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L39-L39), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L92-L92), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L98-L98), [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L126-L126), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L147-L147), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L165-L165), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L179-L179), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

34:    function isLiquidatable(	// @audit-issue

49:    function checkSafe(	// @audit-issue

63:    function getIsSafe(	// @audit-issue

102:    function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {	// @audit-issue

117:    function calculateMinValue(	// @audit-issue

238:    function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L34-L34), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L49-L49), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L63-L63), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L102-L102), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L117-L117), [238](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L238-L238), 


```solidity
Path: ./src/libraries/Trade.sol

76:    function swap(	// @audit-issue

112:    function getSqrtPrice(address uniswapPoolAddress, bool isQuoteZero) internal view returns (uint256 sqrtPriceX96) {	// @audit-issue

116:    function calculateStableAmount(uint256 currentSqrtPrice, uint256 baseAmount) internal pure returns (uint256) {	// @audit-issue

135:    function settleUserBalanceAndFee(	// @audit-issue
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L76-L76), [112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L112-L112), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L116-L116), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L135-L135), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

75:    function emitInterestGrowthEvent(	// @audit-issue
```
[75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L75-L75), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

14:    function calculatePremiumCurve(uint256 utilization) internal pure returns (uint256) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L14-L14), 


```solidity
Path: ./src/libraries/Perp.sol

119:    function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)	// @audit-issue

202:    function reallocate(	// @audit-issue

262:    function rebalanceForInRange(	// @audit-issue

305:    function swapForOutOfRange(	// @audit-issue

332:    function getAvailableLiquidityAmount(	// @audit-issue

345:    function settleUserBalance(DataType.PairStatus storage _pairStatus, UserStatus storage _userStatus) internal {	// @audit-issue

373:    function updateFeeAndPremiumGrowth(uint256 _pairId, SqrtPerpAssetStatus storage _assetStatus) internal {	// @audit-issue

426:    function computeRequiredAmounts(	// @audit-issue

526:    function updateSqrtPosition(	// @audit-issue

604:    function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {	// @audit-issue

618:    function updateRebalanceEntry(	// @audit-issue

707:    function increase(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)	// @audit-issue

719:    function decrease(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)	// @audit-issue

815:    function finalizeReallocation(SqrtPerpAssetStatus storage sqrtPerpStatus) internal {	// @audit-issue
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L119-L119), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L202-L202), [262](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L262-L262), [305](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L305-L305), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L332-L332), [345](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L345-L345), [373](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L373-L373), [426](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L426-L426), [526](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L526-L526), [604](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L604-L604), [618](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L618-L618), [707](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L707-L707), [719](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L719-L719), [815](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L815-L815), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

72:    function isSameSign(int256 a, int256 b) internal pure returns (bool) {	// @audit-issue

130:    function computeUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus memory _userStatus)	// @audit-issue

155:    function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)	// @audit-issue

170:    function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L72-L72), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L130-L130), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L155-L155), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L170-L170), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

18:    function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L18-L18), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

43:    function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)	// @audit-issue

56:    function revertPairStatus(DataType.PairStatus memory pairStatus) internal pure {	// @audit-issue

64:    function revertVaultStatus(IPredyPool.VaultStatus memory vaultStatus) internal pure {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L43-L43), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L56-L56), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

142:    function _storePairStatus(	// @audit-issue

209:    function validatePoolOwner(address _poolOwner) internal pure {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L142-L142), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L209-L209), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

129:    function checkVaultIsDanger(	// @audit-issue

159:    function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)	// @audit-issue
```
[129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L129-L129), [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L159-L159), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

68:    function callTradeAfterCallback(	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L68-L68), 


```solidity
Path: ./src/libraries/math/Bps.sol

7:    function upper(uint256 price, uint256 bps) internal pure returns (uint256) {	// @audit-issue

11:    function lower(uint256 price, uint256 bps) internal pure returns (uint256) {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L7-L7), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L11-L11), 


```solidity
Path: ./src/libraries/math/LPMath.sol

10:    function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

20:    function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

108:    function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

119:    function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

134:    function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

145:    function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L10-L10), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L20-L20), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L108-L108), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L119-L119), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L134-L134), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L145-L145), 


```solidity
Path: ./src/libraries/math/Math.sol

16:    function max(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L16-L16), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

20:    function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L20-L20), 


```solidity
Path: ./src/base/BaseMarket.sol

108:    function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {	// @audit-issue
```
[108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L108-L108), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

60:    function execSettlementInternal(	// @audit-issue

90:    function sell(	// @audit-issue

137:    function buy(	// @audit-issue
```
[60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L60-L60), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L90-L90), [137](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L137-L137), 


#### Recommendation

Inline 'internal' functions in Solidity that are called only once to save gas. This avoids the additional gas cost of 20 to 40 units associated with extra JUMP instructions and stack operations required for separate function calls, unless the calling function becomes too complex.

### Inline modifiers used only once
In Solidity, modifiers provide a way to add reusable conditions or logic to functions. However, if a modifier is used only once, the modularization may not be beneficial in terms of gas efficiency. The overhead associated with a modifier – including two extra JUMP instructions and additional stack operations for the function call – results in an extra cost of about 20 to 40 gas. In cases where a modifier is unique to a single function and the function’s complexity does not necessitate breaking out logic, inlining the modifier's code directly within the function can be more gas-efficient. This approach avoids the overhead of a separate modifier call while maintaining code clarity.

```solidity
Path: ./src/PredyPool.sol

52:    modifier onlyByLocker() {	// @audit-issue

63:    modifier onlyVaultOwner(uint256 vaultId) {	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L52-L52), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L63-L63), 


#### Recommendation

Evaluate the use of modifiers in your Solidity contracts, particularly those applied to only one function. If a modifier is unique to a single function, consider inlining its logic within the function itself to save gas. This is especially advantageous for simpler functions where the additional clarity provided by a separate modifier does not outweigh the gas cost of its use. Ensure that the function remains clear and maintainable after inlining the modifier's logic, and document the rationale for inlining to maintain code readability and facilitate future maintenance.

### Optimizing Arithmetic with Shift Operators for Division and Multiplication by Powers of Two
In computational operations, especially within contexts where efficiency matters, certain arithmetic operations can be optimized. One such optimization is leveraging shift operators for division and multiplication by powers of two. This stems from the binary nature of numbers in computing, where shifting bits to the left (using `<<`) effectively multiplies a number by 2 for each position shifted, and shifting bits to the right (using `>>`) divides the number by 2 for each position shifted.

In Solidity, and many other programming languages, using shift operators can be more gas-efficient than traditional arithmetic operations for these specific cases. For instance, instead of performing `value * 4`, one can use `value << 2`, and instead of `value / 4`, one can use `value >> 2`. These optimizations can result in reduced gas costs and faster execution times.


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

202:        require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);	// @audit-issue
```
[202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L202-L202), 


```solidity
Path: ./src/libraries/Reallocation.sol

51:        int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;	// @audit-issue

67:                    upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue

80:                    lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L51-L51), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L67-L67), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L80-L80), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

235:            + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;	// @audit-issue

249:        return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);	// @audit-issue
```
[235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L235-L235), [249](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L249-L249), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;	// @audit-issue

72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;	// @audit-issue
```
[71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L72-L72), 


#### Recommendation

When performing division or multiplication by powers of two in Solidity, consider using shift operators (`<<` for multiplication and `>>` for division) for improved gas efficiency. This optimization takes advantage of the binary nature of computing and can lead to reduced gas costs and faster execution times.

### Multiple Pragma Definition
In Solidity, pragma statements are used to specify the compiler version and settings for a smart contract. This issue occurs when multiple pragma statements are defined within the same contract or file. Each pragma statement should be unique within a contract or file, as it sets the compiler version and potentially other compiler-specific configurations.


```solidity
Path: ./src/PredyPool.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L2-L2), 


```solidity
Path: ./src/PriceFeed.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L2-L2), 


```solidity
Path: ./src/markets/L2Decoder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L2-L2), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L2-L2), 


```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/IUniswapV3PoolOracle.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


```solidity
Path: ./src/libraries/PerpFee.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L2-L2), 


```solidity
Path: ./src/libraries/UniHelper.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L2-L2), 


```solidity
Path: ./src/libraries/Reallocation.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L2-L2), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L2-L2), 


```solidity
Path: ./src/libraries/Trade.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L2-L2), 


```solidity
Path: ./src/libraries/VaultLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/Constants.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L2-L2), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L2-L2), 


```solidity
Path: ./src/libraries/Perp.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L2-L2), 


```solidity
Path: ./src/libraries/PairLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PairLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L2-L2), 


```solidity
Path: ./src/libraries/SlippageLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/DataType.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/DataType.sol#L2-L2), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/Bps.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/LPMath.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/Math.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L2-L2), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L2-L2), 


```solidity
Path: ./src/base/BaseMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L2-L2), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L2-L2), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L2-L2), 


```solidity
Path: ./src/base/BaseHookCallback.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L2-L2), 


```solidity
Path: ./src/types/GlobalData.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L2-L2), 


```solidity
Path: ./src/types/LockData.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/LockData.sol#L2-L2), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L2-L2), 


#### Recommendation

Ensure each Solidity contract or file contains a single, unique pragma statement to clearly specify the intended compiler version and settings. Avoid multiple pragma definitions to prevent confusion and potential compilation issues.

### Use `private` Rather than `public` for Constants 
In Solidity, constants represent immutable values that cannot be changed after they are set at compile-time. By default, constants have internal visibility, meaning they can be accessed within the contract they are declared in and in derived contracts. If a constant is explicitly declared as `public`, Solidity automatically generates a getter function for it. While this might seem harmless, it actually incurs a gas overhead, especially when the contract is deployed, as the EVM needs to generate bytecode for that getter. Conversely, declaring constants as `private` ensures that no additional getter is generated, optimizing gas usage.

```solidity
Path: ./src/libraries/SlippageLib.sol

13:    uint256 public constant MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 100747209;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L13-L13), 


```solidity
Path: ./src/libraries/math/Bps.sol

5:    uint32 public constant ONE = 1e6;	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L5-L5), 


#### Recommendation

To optimize gas usage in your Solidity contracts, declare constants with `private` visibility rather than `public` when possible. Using `private` prevents the automatic generation of a getter function, reducing gas overhead, especially during contract deployment.

### Consider pre-calculating the address of `address(this)` to save gas
Use `foundry`'s [`script.sol`](https://book.getfoundry.sh/reference/forge-std/compute-create-address) or `solady`'s [`LibRlp.sol`](https://github.com/Vectorized/solady/blob/main/src/utils/LibRLP.sol) to save the value in a constant, which will avoid having to spend gas to push the value on the stack every time it's used.

```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

55:                OrderInfo(address(this), order.trader, order.nonce, deadline),	// @audit-issue

81:                OrderInfo(address(this), order.trader, order.nonce, order.deadline),	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L55-L55), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L81-L81), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

449:            order.transferDetails(address(this)),	// @audit-issue
```
[449](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L449-L449), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

319:            ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),	// @audit-issue

332:            ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),	// @audit-issue
```
[319](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L319-L319), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L332-L332), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

38:            info: OrderInfo(address(this), compressedOrder.trader, compressedOrder.nonce, deadline),	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L38-L38), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

46:        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L46-L46), 


```solidity
Path: ./src/libraries/UniHelper.sol

92:        bytes32 positionKey = PositionKey.compute(address(this), tickLower, tickUpper);	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L92-L92), 


```solidity
Path: ./src/libraries/Perp.sol

220:            address(this), _sqrtAssetStatus.uniswapPool, _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper	// @audit-issue

273:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, type(uint128).max, type(uint128).max	// @audit-issue

280:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""	// @audit-issue

712:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""	// @audit-issue

733:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, type(uint128).max, type(uint128).max	// @audit-issue
```
[220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L220-L220), [273](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L273-L273), [280](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L280-L280), [712](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L712-L712), [733](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L733-L733), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

24:        if (address(this) != address(resolvedOrder.info.market)) {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L24-L24), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

197:                address(this),	// @audit-issue
```
[197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L197-L197), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

52:        ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L52-L52), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

106:                ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));	// @audit-issue
```
[106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L106-L106), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

110:        predyPool.take(false, address(this), sellAmount);	// @audit-issue

119:            address(this)	// @audit-issue

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue

157:        predyPool.take(true, address(this), settlementParams.maxQuoteAmount);	// @audit-issue

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L110-L110), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L119-L119), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L157-L157), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), 


```solidity
Path: ./src/types/GlobalData.sol

40:        globalData.lockData.quoteReserve = ERC20(globalData.pairs[pairId].quotePool.token).balanceOf(address(this));	// @audit-issue

41:        globalData.lockData.baseReserve = ERC20(globalData.pairs[pairId].basePool.token).balanceOf(address(this));	// @audit-issue

103:        uint256 reserveAfter = ERC20(currency).balanceOf(address(this));	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L40-L40), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L41-L41), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L103-L103), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### All variables can be packed into fewer storage slots by truncating timestamp bytes
By using a `uint32` rather than a larger type for variables that track timestamps, one can save gas by using fewer storage slots per struct, at the expense of the protocol breaking after the year 2106 (when `uint32` wraps). If this is an acceptable tradeoff, if variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

141:    function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L141-L141), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

28:    uint256 deadline;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L28-L28), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

8:    uint256 deadline;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L8-L8), 


#### Recommendation

Evaluate the use of `uint32` for timestamp variables in your Solidity contracts, especially within structs that are frequently written to or read from storage. Ensure that the reduced size will not impact the functionality of your contract, particularly considering the 2106 overflow limitation. When implementing this optimization, aim to group such variables together in the same storage slot to maximize gas savings. This strategy is most effective when these variables are updated together, as in a constructor or specific functions, allowing for a single storage operation (`Gsset`) instead of multiple. Always balance the benefits of gas savings against the longevity and future-proofing of your contract.

### Counting down in for statements is more gas efficient
Looping downwards in Solidity is more gas efficient due to how the EVM compares variables. In a 'for' loop that counts down, the end condition is usually to compare with zero, which is cheaper than comparing with another number. As such, restructure your loops to count downwards where possible.

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

23:        for (uint256 i = 0; i < items.length; i++) {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L23-L23), 


#### Recommendation

Where feasible, refactor `for` loops in your Solidity contracts to count downwards. Adjust the loop initialization, condition, and iteration statements to decrement the loop variable and terminate the loop when it reaches zero. This approach can lead to gas savings, making your contract more efficient in terms of execution costs. Ensure that this refactoring aligns with the logic and requirements of your contract, and thoroughly test to confirm that the revised loop behavior matches the intended functionality.

### Use solady library where possible to save gas
The following OpenZeppelin imports have a Solady equivalent, as such they can be used to save GAS as Solady modules have been specifically designed to be as GAS efficient as possible

```solidity
Path: ./src/PredyPool.sol

8:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L8-L8), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

6:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L6-L6), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

8:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L8-L8), 


```solidity
Path: ./src/libraries/Trade.sol

4:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L4-L4), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

4:import {IERC20Metadata} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L5-L5), 


#### Recommendation

Evaluate and, where appropriate, integrate Solady modules in your Solidity contracts as alternatives to similar OpenZeppelin imports. Focus on areas where gas efficiency can be significantly improved. Ensure that any replacement with Solady's modules does not compromise the security or functionality of your contracts. Conduct thorough testing and code reviews when making such substitutions to confirm compatibility and maintain the integrity of your application. Stay informed about updates and community feedback on both libraries to make informed decisions about their use in your projects.

### Consider using solady's 'FixedPointMathLib'
Using Solady's "FixedPointMathLib" for multiplication or division operations in Solidity can lead to significant gas savings. This library is designed to optimize fixed-point arithmetic operations, which are common in financial calculations involving tokens or currencies. By implementing more efficient algorithms and assembly optimizations, "FixedPointMathLib" minimizes the computational resources required for these operations. For contracts that frequently perform such calculations, integrating this library can reduce transaction costs, thereby enhancing overall performance and cost-effectiveness. However, developers must ensure compatibility with their existing codebase and thoroughly test for accuracy and expected behavior to avoid any unintended consequences.

```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

53:        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;	// @audit-issue

56:        return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;	// @audit-issue

84:        uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;	// @audit-issue

85:        uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;	// @audit-issue

150:        uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;	// @audit-issue

158:                + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE	// @audit-issue

176:        uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);	// @audit-issue

184:                + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)	// @audit-issue

202:        require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L184-L184), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L202-L202), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

233:        return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);	// @audit-issue

239:        return Math.abs(positionAmount) * price / Constants.Q96;	// @audit-issue
```
[233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L239-L239), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

87:        uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)	// @audit-issue

182:            return (price1 - price2) * Bps.ONE / price2;	// @audit-issue

184:            return (price2 - price1) * Bps.ONE / price2;	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L87-L87), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L184-L184), 


```solidity
Path: ./src/libraries/UniHelper.sol

29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


```solidity
Path: ./src/libraries/Reallocation.sol

51:        int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;	// @audit-issue

67:                    upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue

80:                    lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue

120:        result = (result / tickSpacing) * tickSpacing;	// @audit-issue

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue

184:        uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;	// @audit-issue

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L51-L51), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L67-L67), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L80-L80), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

93:            (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;	// @audit-issue

193:        uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;	// @audit-issue

194:        uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;	// @audit-issue

213:                (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);	// @audit-issue

235:            + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;	// @audit-issue

249:        return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L93-L93), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L213-L213), [235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L235-L235), [249](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L249-L249), 


```solidity
Path: ./src/libraries/Trade.sol

103:        if (settledQuoteAmount * totalBaseAmount <= 0) {	// @audit-issue

117:        uint256 quoteAmount = (currentSqrtPrice * baseAmount) >> Constants.RESOLUTION;	// @audit-issue

119:        return (quoteAmount * currentSqrtPrice) >> Constants.RESOLUTION;	// @audit-issue

128:        swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;	// @audit-issue

129:        swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;	// @audit-issue

132:        swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();	// @audit-issue
```
[103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L103-L103), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L117-L117), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L119-L119), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L132-L132), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

66:        interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)	// @audit-issue

71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;	// @audit-issue

72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L66-L66), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L72-L72), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

21:        return (1600 * b * b / Constants.ONE) / Constants.ONE;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L21-L21), 


```solidity
Path: ./src/libraries/Perp.sol

166:            _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(	// @audit-issue

170:            _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(	// @audit-issue

399:            f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

402:            f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

535:        if (_userStatus.sqrtPerp.amount * _amount >= 0) {	// @audit-issue

590:        uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);	// @audit-issue

609:        uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;	// @audit-issue

682:        if (_positionAmount * _tradeAmount >= 0) {	// @audit-issue

689:                int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;	// @audit-issue

697:                int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;	// @audit-issue

755:        if (_userStatus.sqrtPerp.amount * _tradeSqrtAmount >= 0) {	// @audit-issue

785:            offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue

786:            offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue
```
[166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L166-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L170-L170), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L402-L402), [535](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L535-L535), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L590-L590), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L609-L609), [682](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L682-L682), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L697-L697), [755](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L755-L755), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L786-L786), 


```solidity
Path: ./src/libraries/PairLib.sol

6:        return pairId * type(uint64).max + rebalanceId;	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PairLib.sol#L6-L6), 


```solidity
Path: ./src/libraries/SlippageLib.sol

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue

49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L49-L49), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

26:            ir += (utilizationRatio * irmParams.slope1) / _ONE;	// @audit-issue

28:            ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;	// @audit-issue

29:            ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L29-L29), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

32:                decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;	// @audit-issue

34:                decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L34-L34), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue

222:                && _irmParams.slope2 <= 10 * 1e18,	// @audit-issue
```
[214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L222-L222), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue

63:            -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,	// @audit-issue

168:        uint256 ratio = uint256(vaultValue * 1e4 / minMargin);	// @audit-issue

174:        return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L63-L63), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L168-L168), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L174-L174), 


```solidity
Path: ./src/libraries/math/Bps.sol

8:        return price * bps / ONE;	// @audit-issue

12:        return price * ONE / bps;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L12-L12), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

70:        uint256 fee = settlementParamsV3.feePrice * tradeAmountAbs / Constants.Q96;	// @audit-issue

76:        uint256 maxQuoteAmount = settlementParamsV3.maxQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue

77:        uint256 minQuoteAmount = settlementParamsV3.minQuoteAmountPrice * tradeAmountAbs / Constants.Q96;	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L77-L77), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

101:            uint256 quoteAmount = sellAmount * price / Constants.Q96;	// @audit-issue

125:            uint256 quoteAmount = sellAmount * price / Constants.Q96;	// @audit-issue

148:            uint256 quoteAmount = buyAmount * price / Constants.Q96;	// @audit-issue

172:            uint256 quoteAmount = buyAmount * price / Constants.Q96;	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L101-L101), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L125-L125), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L148-L148), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L172-L172), 


#### Recommendation

Consider integrating Solady's 'FixedPointMathLib' into your Solidity contracts for optimized fixed-point arithmetic operations. This library can provide substantial gas savings and enhance the performance of your contract. Before integration, evaluate how 'FixedPointMathLib' aligns with your contract’s requirements. Ensure thorough testing for accuracy and compatibility with your existing contract logic. Carefully document any changes and keep track of how these optimizations affect your contract's operations to maintain transparency and reliability in your application. Adopting 'FixedPointMathLib' should be a considered decision, balancing the benefits of gas efficiency with the need for maintaining code clarity and functionality.

### Consider using OZ EnumerateSet in place of nested mappings
Nested mappings and multi-dimensional arrays in Solidity operate through a process of double hashing, wherein the original storage slot and the first key are concatenated and hashed, and then this hash is again concatenated with the second key and hashed. This process can be quite gas expensive due to the double-hashing operation and subsequent storage operation (sstore).

A possible optimization involves manually concatenating the keys followed by a single hash operation and an sstore. However, this technique introduces the risk of storage collision, especially when there are other nested hash maps in the contract that use the same key types. Because Solidity is unaware of the number and structure of nested hash maps in a contract, it follows a conservative approach in computing the storage slot to avoid possible collisions.

OpenZeppelin's EnumerableSet provides a potential solution to this problem. It creates a data structure that combines the benefits of set operations with the ability to enumerate stored elements, which is not natively available in Solidity. EnumerableSet handles the element uniqueness internally and can therefore provide a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays in certain scenarios.


```solidity
Path: ./src/PredyPool.sol

40:    mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L40-L40), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

68:    mapping(address owner => mapping(uint256 pairId => UserPosition)) public userPositions;	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L68-L68), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L68-L68), 


#### Recommendation

Consider using OpenZeppelin's EnumerableSet library as a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays, especially when managing unique elements. This library simplifies the handling of unique data sets and allows for the enumeration of elements, a feature not natively supported in Solidity. When refactoring your contract, replace nested mappings or arrays with EnumerableSet where appropriate, ensuring both gas efficiency and enhanced functionality. Be sure to understand the use cases and limitations of EnumerableSet to apply it effectively in your contract's design and implementation.

### Reduce Gas Usage by Moving to Solidity 0.8.19 or Later
This issue highlights the opportunity to reduce gas consumption in a smart contract by upgrading the Solidity compiler version to 0.8.19 or a later release. Gas usage is a critical consideration in Ethereum smart contracts, as it directly affects transaction costs and contract execution efficiency. Newer compiler versions often come with optimizations and improvements that can lead to reduced gas costs for certain operations and contract structures.


```solidity
Path: ./src/PredyPool.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L2-L2), 


```solidity
Path: ./src/PriceFeed.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L2-L2), 


```solidity
Path: ./src/markets/L2Decoder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L2-L2), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L2-L2), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L2-L2), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L2-L2), 


```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/IUniswapV3PoolOracle.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


```solidity
Path: ./src/libraries/PerpFee.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L2-L2), 


```solidity
Path: ./src/libraries/UniHelper.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L2-L2), 


```solidity
Path: ./src/libraries/Reallocation.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L2-L2), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L2-L2), 


```solidity
Path: ./src/libraries/Trade.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L2-L2), 


```solidity
Path: ./src/libraries/VaultLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/Constants.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L2-L2), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L2-L2), 


```solidity
Path: ./src/libraries/Perp.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L2-L2), 


```solidity
Path: ./src/libraries/PairLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PairLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L2-L2), 


```solidity
Path: ./src/libraries/SlippageLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/DataType.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/DataType.sol#L2-L2), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L2-L2), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/Bps.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/LPMath.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L2-L2), 


```solidity
Path: ./src/libraries/math/Math.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L2-L2), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L2-L2), 


```solidity
Path: ./src/base/BaseMarket.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L2-L2), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L2-L2), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L2-L2), 


```solidity
Path: ./src/base/BaseHookCallback.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L2-L2), 


```solidity
Path: ./src/types/GlobalData.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L2-L2), 


```solidity
Path: ./src/types/LockData.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/LockData.sol#L2-L2), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

2:pragma solidity ^0.8.17;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L2-L2), 


#### Recommendation

Consider upgrading to Solidity version 0.8.19 or later to leverage compiler optimizations for reduced gas consumption. Newer versions often include improvements that enhance transaction cost-efficiency and overall contract execution.

### Constant Keccak Variables Are Treated As Expressions, Not Constants
In Solidity, state variables or local variables declared with the `constant` keyword are expected to represent constant values that are determined at compile time. These constants typically do not incur any gas costs when accessed since their values are hard-coded into the contract bytecode.

However, this expected behavior deviates when using the `keccak256()` function. When a variable is initialized with a `keccak256()` hash computation, even if declared as `constant`, it isn't truly treated as a constant in the resulting bytecode. Instead, every time this "constant" is referenced, the EVM recalculates the hash. This behavior contrasts with other true constants, which are embedded directly into the bytecode and accessed without additional computations.


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

39:    bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);	// @audit-issue

99:    bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L39-L39), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L99-L99), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

44:    bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L44-L44), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

46:    bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L46-L46), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

14:    bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L14-L14), 


#### Recommendation

Avoid using 'keccak256()' to initialize 'constant' variables in Solidity. Instead, precompute the hash value outside of the contract and assign this precomputed value to the constant. This ensures the variable is a true constant, eliminating redundant hash computations and saving gas.

### Use bitmap to save gas
Bitmaps in Solidity are essentially a way of representing a set of boolean values within an integer type variable such as `uint256`. Each bit in the integer represents a true or false value (1 or 0), thus allowing efficient storage of multiple boolean values.

Bitmaps can save gas in the Ethereum network because they condense a lot of information into a small amount of storage. In Ethereum, storage is one of the most significant costs in terms of gas usage. By reducing the amount of storage space needed, you can potentially save on gas fees.

Here's a quick comparison:

If you were to represent 256 different boolean values in the traditional way, you would have to declare 256 different `bool` variables. Given that each `bool` occupies a storage slot and each storage slot costs 20,000 gas to initialize, you would end up paying a considerable amount of gas.

On the other hand, if you were to use a bitmap, you could store these 256 boolean values within a single `uint256` variable. In other words, you'd only pay for a single storage slot, resulting in significant gas savings.

However, it's important to note that while bitmaps can provide gas efficiencies, they do add complexity to the code, making it harder to read and maintain. Also, using bitmaps is efficient only when dealing with a large number of boolean variables that are frequently changed or accessed together. 

In contrast, the straightforward counterpart to bitmaps would be using arrays or mappings to store boolean values, with each `bool` value occupying its own storage slot. This approach is simpler and more readable but could potentially be more expensive in terms of gas usage.

```solidity
Path: ./src/markets/L2Decoder.sol

27:            isLimit = true;	// @audit-issue

29:            isLimit = false;	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L27-L27), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L29-L29), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

79:            isEnabled = true;	// @audit-issue

81:            isEnabled = false;	// @audit-issue
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L79-L79), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L81-L81), 


```solidity
Path: ./src/libraries/Perp.sol

238:            isOutOfRange = true;	// @audit-issue

242:            isOutOfRange = false;	// @audit-issue

245:            isOutOfRange = true;	// @audit-issue
```
[238](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L238-L238), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L242-L242), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L245-L245), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

89:        allowedUniswapPools[_addPairParam.uniswapPool] = true;	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L89-L89), 


#### Recommendation

Consider using bitmaps in your Solidity contracts when you need to store and manipulate a large set of boolean values. This approach is particularly advantageous in terms of gas efficiency for scenarios involving frequent changes or accesses to these values. However, balance this efficiency with code readability and maintainability. Ensure that the use of bitmaps is well-documented, and consider the complexity it introduces into the code. Employ bitmaps judiciously, especially when their use results in significant gas savings, and the logic they represent is a core aspect of the contract's functionality.

### Using `bool`s for storage incurs overhead
[Booleans are more expensive than uint256](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use `uint256(0)` and `uint256(1)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD.


```solidity
Path: ./src/PredyPool.sol

38:    mapping(address => bool) public allowedUniswapPools;	// @audit-issue

40:    mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;	// @audit-issue

44:    event ProtocolRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);	// @audit-issue

45:    event CreatorRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L40-L40), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L45-L45), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

10:    bool isEnabled;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L10-L10), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

19:    bool reduceOnly;	// @audit-issue

20:    bool closePosition;	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L20-L20), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

27:    event ScaledAssetPositionUpdated(uint256 indexed pairId, bool isStable, int256 open, int256 close);	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L27-L27), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

19:    event TokenSupplied(address indexed account, uint256 pairId, bool isStable, uint256 suppliedAmount);	// @audit-issue

20:    event TokenWithdrawn(address indexed account, uint256 pairId, bool isStable, uint256 finalWithdrawnAmount);	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L20-L20), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

20:        bool relocationOccurred,	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L20-L20), 


```solidity
Path: ./src/base/BaseMarket.sol

17:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L17-L17), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

29:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L29-L29), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra [22-28](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed


```solidity
Path: ./src/PredyPool.sol

147:    function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {	// @audit-issue

344:    function getSqrtPrice(uint256 pairId) external view returns (uint160) {	// @audit-issue
```
[147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L147-L147), [344](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L344-L344), 


```solidity
Path: ./src/markets/L2Decoder.sol

10:            uint64 startTime,	// @audit-issue

11:            uint64 endTime,	// @audit-issue

12:            uint64 deadline,	// @audit-issue

13:            uint128 startAmount,	// @audit-issue

14:            uint128 endAmount	// @audit-issue

17:        uint32 isLimitUint;	// @audit-issue

41:        returns (uint64 deadline, uint64 pairId, uint8 leverage)	// @audit-issue

53:        returns (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side)	// @audit-issue

55:        uint8 reduceOnlyUint;	// @audit-issue

56:        uint8 closePositionUint;	// @audit-issue

57:        uint8 sideUint;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L14-L14), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L17-L17), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L41-L41), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L53-L53), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L55-L55), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L56-L56), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L57-L57), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

50:        (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =	// @audit-issue

77:        uint64 pairId = userPositions[order.positionId].pairId;	// @audit-issue

22:    int64 maximaDeviation;	// @audit-issue

33:    int64 maximaDeviation;	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L50-L50), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L77-L77), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L22-L22), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L33-L33), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

7:    function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)	// @audit-issue

13:            bool isEnabled,	// @audit-issue

41:        returns (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage)	// @audit-issue

56:            uint64 expiration,	// @audit-issue

57:            uint32 hedgeInterval,	// @audit-issue

58:            uint32 sqrtPriceTrigger,	// @audit-issue

59:            uint32 minSlippageTolerance,	// @audit-issue

60:            uint32 maxSlippageTolerance,	// @audit-issue

61:            uint16 auctionPeriod,	// @audit-issue

62:            uint32 auctionRange	// @audit-issue

65:        uint32 isEnabledUint = 0;	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L7-L7), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L13-L13), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L41-L41), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L56-L56), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L57-L57), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L58-L58), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L59-L59), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L60-L60), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L61-L61), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L62-L62), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L65-L65), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue

408:    function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), [408](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L408-L408), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

48:    function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L48-L48), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

163:        uint64 orderId	// @audit-issue
```
[163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L163-L163), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

32:        uint64 orderId	// @audit-issue

34:        (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side) =	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L32-L32), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L34-L34), 


```solidity
Path: ./src/vendors/IUniswapV3PoolOracle.sol

9:            uint160 sqrtPriceX96,	// @audit-issue

10:            int24 tick,	// @audit-issue

11:            uint16 observationIndex,	// @audit-issue

12:            uint16 observationCardinality,	// @audit-issue

13:            uint16 observationCardinalityNext,	// @audit-issue

14:            uint8 feeProtocol,	// @audit-issue

18:    function liquidity() external view returns (uint128);	// @audit-issue

28:        returns (uint32 blockTimestamp, int56 tickCumulative, uint160 liquidityCumulative, bool initialized);	// @audit-issue

30:    function increaseObservationCardinalityNext(uint16 observationCardinalityNext) external;	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L14-L14), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L18-L18), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L30-L30), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

5:    function decimals() external view returns (uint8);	// @audit-issue

8:    function getRoundData(uint80 _roundId)	// @audit-issue

11:        returns (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound);	// @audit-issue

15:        returns (uint80 roundId, int256 answer, uint256 startedAt, uint256 updatedAt, uint80 answeredInRound);	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L5-L5), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L8-L8), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L11-L11), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L15-L15), 


```solidity
Path: ./src/libraries/UniHelper.sol

13:    function getSqrtPrice(address uniswapPoolAddress) internal view returns (uint160 sqrtPrice) {	// @audit-issue

20:    function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {	// @audit-issue

27:    function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {	// @audit-issue

35:    function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {	// @audit-issue

49:            (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();	// @audit-issue

51:            (uint32 oldestAvailableAge,,, bool initialized) = uniswapPool.observations((index + 1) % cardinality);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue

72:        uint160 sqrtPriceX96 = TickMath.getSqrtRatioAtTick(tick);	// @audit-issue

87:    function getFeeGrowthInsideLast(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)	// @audit-issue

99:    function getFeeGrowthInside(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)	// @audit-issue

104:        (, int24 tickCurrent,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L13-L13), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L20-L20), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L35-L35), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L51-L51), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L72-L72), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L87-L87), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L99-L99), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L104-L104), 


```solidity
Path: ./src/libraries/Reallocation.sol

18:    function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)	// @audit-issue

21:        returns (int24 lower, int24 upper)	// @audit-issue

23:        int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();	// @audit-issue

43:        int24 currentTick,	// @audit-issue

44:        int24 tickSpacing	// @audit-issue

45:    ) internal pure returns (int24 lower, int24 upper) {	// @audit-issue

51:        int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;	// @audit-issue

58:                int24 minLowerTick = calculateMinLowerTick(	// @audit-issue

71:                int24 maxUpperTick = calculateMaxUpperTick(	// @audit-issue

93:        (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue

98:    function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)	// @audit-issue

109:    function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {	// @audit-issue

127:        int24 currentLowerTick,	// @audit-issue

130:        int24 tickSpacing	// @audit-issue

131:    ) internal pure returns (int24 minLowerTick) {	// @audit-issue

132:        uint160 sqrtPrice =	// @audit-issue

148:        int24 currentUpperTick,	// @audit-issue

151:        int24 tickSpacing	// @audit-issue

152:    ) internal pure returns (int24 maxUpperTick) {	// @audit-issue

153:        uint160 sqrtPrice =	// @audit-issue

165:    function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)	// @audit-issue

168:        returns (uint160)	// @audit-issue

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue

179:    function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)	// @audit-issue

182:        returns (uint160)	// @audit-issue

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L18-L18), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L21-L21), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L23-L23), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L43-L43), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L45-L45), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L51-L51), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L58-L58), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L71-L71), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L93-L93), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L98-L98), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L109-L109), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L127-L127), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L130-L130), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L131-L131), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L132-L132), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L148-L148), [151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L151-L151), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L152-L152), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L153-L153), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L165-L165), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L168-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L179-L179), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L182-L182), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

102:    function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L102-L102), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

50:    function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L50-L50), 


```solidity
Path: ./src/libraries/Constants.sol

18:    uint8 internal constant RESOLUTION = 96;	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L18-L18), 


```solidity
Path: ./src/libraries/Perp.sol

119:    function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)	// @audit-issue

145:    function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {	// @audit-issue

206:        (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue

219:        uint128 totalLiquidityAmount = getAvailableLiquidityAmount(	// @audit-issue

233:        int24 tick;	// @audit-issue

265:        int24 _currentTick,	// @audit-issue

266:        uint128 _totalLiquidityAmount	// @audit-issue

307:        uint160 _currentSqrtPrice,	// @audit-issue

308:        int24 _tick,	// @audit-issue

309:        uint128 _totalLiquidityAmount	// @audit-issue

311:        uint160 tickSqrtPrice = TickMath.getSqrtRatioAtTick(_tick);	// @audit-issue

335:        int24 _tickLower,	// @audit-issue

336:        int24 _tickUpper	// @audit-issue

337:    ) internal view returns (uint128) {	// @audit-issue

340:        (uint128 liquidity,,,,) = IUniswapV3Pool(_uniswapPool).positions(positionKey);	// @audit-issue

747:        int24 _tickLower,	// @audit-issue

748:        int24 _tickUpper,	// @audit-issue
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L119-L119), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L145-L145), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L206-L206), [219](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L219-L219), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L233-L233), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L265-L265), [266](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L266-L266), [307](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L307-L307), [308](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L308-L308), [309](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L309-L309), [311](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L311-L311), [335](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L335-L335), [336](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L336-L336), [337](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L337-L337), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L340-L340), [747](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L747-L747), [748](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L748-L748), 


```solidity
Path: ./src/libraries/PairLib.sol

5:    function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PairLib.sol#L5-L5), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

186:    function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)	// @audit-issue
```
[186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L186-L186), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

96:    function updateFeeRatio(DataType.PairStatus storage _pairStatus, uint8 _feeRatio) external {	// @audit-issue

205:    function validateFeeRatio(uint8 _fee) internal pure {	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L96-L96), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L205-L205), 


```solidity
Path: ./src/libraries/math/Bps.sol

5:    uint32 public constant ONE = 1e6;	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L5-L5), 


```solidity
Path: ./src/libraries/math/LPMath.sol

10:    function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

20:    function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

31:        uint160 sqrtRatioA,	// @audit-issue

32:        uint160 sqrtRatioB,	// @audit-issue

69:        uint160 sqrtRatioA,	// @audit-issue

70:        uint160 sqrtRatioB,	// @audit-issue

108:    function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

119:    function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

134:    function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue

145:    function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L10-L10), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L20-L20), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L31-L31), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L32-L32), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L69-L69), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L70-L70), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L108-L108), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L119-L119), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L134-L134), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L145-L145), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

15:    constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L15-L15), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

11:    uint64 expiration;	// @audit-issue

12:    int64 maximaDeviation;	// @audit-issue

15:    uint32 hedgeInterval;	// @audit-issue

16:    uint32 sqrtPriceTrigger;	// @audit-issue

17:    uint32 minSlippageTolerance;	// @audit-issue

18:    uint32 maxSlippageTolerance;	// @audit-issue

19:    uint16 auctionPeriod;	// @audit-issue

20:    uint32 auctionRange;	// @audit-issue

65:    uint64 pairId;	// @audit-issue

72:    uint32 slippageTolerance;	// @audit-issue

73:    uint8 leverage;	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L12-L12), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L16-L16), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L17-L17), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L20-L20), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L65-L65), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L72-L72), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L73-L73), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

11:    uint64 pairId;	// @audit-issue

17:    uint64 slippageTolerance;	// @audit-issue

18:    uint8 leverage;	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L11-L11), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L17-L17), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L18-L18), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

11:    uint64 pairId;	// @audit-issue

18:    uint8 leverage;	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L11-L11), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L18-L18), 


#### Recommendation

Minimize gas overhead by using 'uint256' or 'int256' instead of smaller integer types in Solidity contracts. The EVM operates more efficiently with 32-byte sizes. Downcast to smaller types only when necessary, as operations with smaller types like 'uint8' incur extra gas due to additional EVM operations for size adjustment.

### Refactor modifiers to call a local function
Modifiers code is copied in all instances where it's used, increasing bytecode size. If deployment gas costs are a concern for this contract, refactoring modifiers into functions can reduce bytecode size significantly at the cost of one JUMP.

```solidity
Path: ./src/PredyPool.sol

47:    modifier onlyOperator() {	// @audit-issue

52:    modifier onlyByLocker() {	// @audit-issue

58:    modifier onlyPoolOwner(uint256 pairId) {	// @audit-issue

63:    modifier onlyVaultOwner(uint256 vaultId) {	// @audit-issue
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L47-L47), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L52-L52), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L58-L58), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L63-L63), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

15:    modifier onlyPredyPool() {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L15-L15), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

31:    modifier onlyFiller() {	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L31-L31), 


```solidity
Path: ./src/base/BaseHookCallback.sol

16:    modifier onlyPredyPool() {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L16-L16), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

10:    modifier onlyController() {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L10-L10), 


#### Recommendation

Evaluate your contract's use of modifiers, particularly those applied across multiple functions, to identify candidates for refactoring into functions. Convert these modifiers into external or public functions that perform the same checks or actions. Then, replace the modifier usage in function declarations with calls to these functions at the start of your functions

### Avoid Unnecessary Public Variables
Public state variables in Solidity automatically generate getter functions, increasing contract size and potentially leading to higher deployment and interaction costs. To optimize gas usage and contract efficiency, minimize the use of public variables unless external access is necessary. Instead, use internal or private visibility combined with explicit getter functions when required. This practice not only reduces contract size but also provides better control over data access and manipulation, enhancing security and readability. Prioritize lean, efficient contracts to ensure cost-effectiveness and better performance on the blockchain.

```solidity
Path: ./src/PredyPool.sol

34:    address public operator;	// @audit-issue

36:    GlobalDataLibrary.GlobalData public globalData;	// @audit-issue

38:    mapping(address => bool) public allowedUniswapPools;	// @audit-issue

40:    mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L34-L34), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L36-L36), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L40-L40), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

23:}	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L23-L23), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

23: */	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L23-L23), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L23-L23), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

23:import {SettlementCallbackLib} from "../../base/SettlementCallbackLib.sol";	// @audit-issue

68:    mapping(address owner => mapping(uint256 pairId => UserPosition)) public userPositions;	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L23-L23), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L68-L68), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

23:/**	// @audit-issue

```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L23-L23), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L68-L68), 


```solidity
Path: ./src/libraries/SlippageLib.sol

13:    uint256 public constant MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 100747209;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L13-L13), 


```solidity
Path: ./src/libraries/math/Bps.sol

5:    uint32 public constant ONE = 1e6;	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L5-L5), 


```solidity
Path: ./src/base/BaseMarket.sol

11:    address public whitelistFiller;	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L11-L11), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

23:    address public whitelistFiller;	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L23-L23), 


#### Recommendation

Avoid creating explicit getter functions for 'public' state variables in Solidity. The compiler automatically generates getters for such variables, making additional functions redundant. This practice helps reduce contract size, lowers deployment costs, and simplifies maintenance and understanding of the contract.

### Use `do while` loops intead of for loops
A `do while` loop will cost less gas since the condition is not being checked for the first iteration.
```solidity
uint256 i = 1;
do {                   
    param2 += i;
    i++;
}
while (i < 50);
``` 
is better than
```solidity
for(uint256 i = 1; i< 50; i++){
    param1 += i;
}
```


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

366:        for (uint64 i = 0; i < userPositionIDs.length; i++) {	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L366-L366), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

23:        for (uint256 i = 0; i < items.length; i++) {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L23-L23), 


#### Recommendation

Where appropriate, consider using a `do while` loop instead of a `for` loop in your Solidity contracts. This is especially beneficial when the first iteration of the loop does not require a condition check. Refactor your loop logic to fit the `do while` structure for more gas-efficient execution. However, ensure that the loop's logic and termination conditions are correctly implemented to avoid infinite loops or other logical errors. Always balance gas efficiency with code readability and the specific requirements of your contract's logic.

### Using XOR (^) and AND (&) bitwise equivalents for gas optimizations
Given 4 variables a, b, c and d represented as such:
```
0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d
```
To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.


```solidity
Path: ./src/PriceFeed.sol

50:        require(basePrice.expo == -8, "INVALID_EXP");	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L50-L50), 


```solidity
Path: ./src/markets/L2Decoder.sol

26:        if (isLimitUint == 1) {	// @audit-issue

68:        reduceOnly = reduceOnlyUint == 1;	// @audit-issue

69:        closePosition = closePositionUint == 1;	// @audit-issue

70:        side = sideUint == 1;	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L26-L26), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L68-L68), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L69-L69), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L70-L70), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

78:        if (isEnabledUint == 1) {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L78-L78), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

86:        if (callbackData.callbackType == GammaTradeMarketLib.CallbackType.QUOTE) {	// @audit-issue

89:            if (tradeResult.minMargin == 0) {	// @audit-issue

110:                    callbackData.callbackType == GammaTradeMarketLib.CallbackType.TRADE	// @audit-issue

146:        if (closeRatio == 1e18) {	// @audit-issue

186:            tradeResult.vaultId, gammaOrder.positionId == 0, gammaOrder.info.trader, gammaOrder.pairId	// @audit-issue

197:        if (gammaOrder.positionId == 0) {	// @audit-issue

233:        if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {	// @audit-issue

256:        if (delta == 0) {	// @audit-issue

281:        if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {	// @audit-issue

306:        if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {	// @audit-issue

342:        if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {	// @audit-issue

378:        if (userPosition.vaultId == 0) {	// @audit-issue

421:        if (positionId == 0 || userPosition.vaultId == 0) {	// @audit-issue
```
[86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L86-L86), [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L89-L89), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L110-L110), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L146-L146), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L186-L186), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L197-L197), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L233-L233), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L256-L256), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L281-L281), [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L342-L342), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L378-L378), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L421-L421), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

24:            if (items[i] == item) {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L24-L24), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

80:        if (userPosition.sqrtPriceTrigger == 0) {	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L80-L80), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

109:        if (callbackData.callbackSource == CallbackSource.QUOTE) {	// @audit-issue

111:        } else if (callbackData.callbackSource == CallbackSource.QUOTE3) {	// @audit-issue

113:        } else if (callbackData.callbackSource == CallbackSource.TRADE3) {	// @audit-issue

181:        if (tradeAmount == 0) {	// @audit-issue

207:        if (userPosition.vaultId == 0) {	// @audit-issue

257:        if (userPosition.vaultId == 0) {	// @audit-issue

290:        if (tradeAmount == 0) {	// @audit-issue
```
[109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L109-L109), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L111-L111), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L113-L113), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L181-L181), [207](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L207-L207), [257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L257-L257), [290](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L290-L290), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

33:        bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));	// @audit-issue

50:            if (currentPositionAmount == 0) {	// @audit-issue

92:        if (limitPrice == 0 && stopPrice == 0) {	// @audit-issue

123:        if (tradeAmount == 0) {	// @audit-issue

179:        if (price1 == price2) {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L33-L33), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L50-L50), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L92-L92), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L123-L123), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L179-L179), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

103:        if (debtRiskRatio == 0) {	// @audit-issue
```
[103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L103-L103), 


```solidity
Path: ./src/libraries/Trade.sol

86:        if (totalBaseAmount == 0) {	// @audit-issue
```
[86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L86-L86), 


```solidity
Path: ./src/libraries/VaultLib.sol

30:        if (vaultId == 0) {	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L30-L30), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

61:        if (utilizationRatio == 0) {	// @audit-issue
```
[61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L61-L61), 


```solidity
Path: ./src/libraries/Perp.sol

223:        if (totalLiquidityAmount == 0) {	// @audit-issue

349:        if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {	// @audit-issue

374:        if (_assetStatus.totalAmount == 0) {	// @audit-issue

390:        if (f0 == 0 && f1 == 0) {	// @audit-issue

432:        if (_tradeSqrtAmount == 0) {	// @audit-issue

446:            if (_sqrtAssetStatus.totalAmount == _sqrtAssetStatus.borrowedAmount) {	// @audit-issue

546:        if (_assetStatus.totalAmount == _assetStatus.borrowedAmount) {	// @audit-issue

593:        if (_isWithdraw && _assetStatus.borrowedAmount == 0) {	// @audit-issue

605:        if (_assetStatus.totalAmount == 0) {	// @audit-issue

626:        if (_userStatus.sqrtPerp.amount == 0) {	// @audit-issue

633:        if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {	// @audit-issue

678:        if (_tradeAmount == 0) {	// @audit-issue
```
[223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L223-L223), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L349-L349), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L374-L374), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L390-L390), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L432-L432), [446](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L446-L446), [546](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L546-L546), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L593-L593), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L605-L605), [626](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L626-L626), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L633-L633), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L678-L678), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

38:        if (_amount == 0) {	// @audit-issue

51:        if (_amount == 0) {	// @audit-issue

85:            require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");	// @audit-issue

87:            require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");	// @audit-issue

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue

231:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L51-L51), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L85-L85), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L87-L87), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L231-L231), 


```solidity
Path: ./src/libraries/SlippageLib.sol

29:        if (tradeResult.averagePrice == 0) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L29-L29), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

76:        require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");	// @audit-issue

78:        bool isQuoteZero = uniswapPool.token0() == stableTokenAddress;	// @audit-issue

153:        require(_pairs[_pairId].id == 0, "AAA");	// @audit-issue
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L78-L78), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L153-L153), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

84:            tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE	// @audit-issue

164:        if (vaultValue <= 0 || minMargin == 0) {	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L84-L84), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L164-L164), 


```solidity
Path: ./src/libraries/math/LPMath.sol

36:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue

74:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L36-L36), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L74-L74), 


```solidity
Path: ./src/libraries/math/Math.sol

25:        if (x == 0) {	// @audit-issue

35:        if (x == 0) {	// @audit-issue

45:        if (x == 0) {	// @audit-issue
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L25-L25), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L35-L35), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L45-L45), 


```solidity
Path: ./src/base/BaseMarket.sol

98:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L98-L98), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

146:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L146-L146), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

99:        if (settlementParams.contractAddress == address(0)) {	// @audit-issue

122:        if (price == 0) {	// @audit-issue

146:        if (settlementParams.contractAddress == address(0)) {	// @audit-issue

169:        if (price == 0) {	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L99-L99), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L122-L122), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L146-L146), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L169-L169), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

11:        require(_controller == msg.sender, "ST0");	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L11-L11), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Consider using `bytes32` rather than a `string`
Using the bytes types for fixed-length strings is more efficient than having the EVM have to incur the overhead of string processing. Consider whether the value needs to be a string. A good reason to keep it as a string would be if the variable is defined in an interface that this project does not own.


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

101:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

102:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L101-L101), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L102-L102), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

46:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

47:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L47-L47), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

13:    string side;	// @audit-issue

48:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

49:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L13-L13), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L49-L49), 


#### Recommendation

For fixed-length strings, prefer using 'bytes32' over 'string' to leverage EVM efficiency and reduce overhead. Evaluate the necessity of using a string, especially if the variable isn't part of an externally defined interface.

### The result of a function call should be cached rather than re-calling the function
The function calls in solidity are expensive. If the same result of the same function calls are to be used several times, the result should be cached to reduce the gas consumption of repeated calls.        

```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

110:            _revertTradeResult(tradeResult);	// @audit-issue: Function call `_revertTradeResult` is called multiple times at lines [112].
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L110-L110), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

107:            if (!validateLimitPrice(tradePrice, tradeAmount, limitPrice)) {	// @audit-issue: Function call `validateLimitPrice` is called multiple times at lines [100].

101:                    && !validateStopPrice(oraclePrice, tradePrice, tradeAmount, stopPrice, auctionData)	// @audit-issue: Function call `validateStopPrice` is called multiple times at lines [112].
```
[107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L107-L107), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L101-L101), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue: Function call `ERC20` is called multiple times at lines [31].

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue: Function call `ERC20` is called multiple times at lines [46, 54].
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/libraries/UniHelper.sol

60:            (success, data) = address(uniswapPool).staticcall(	// @audit-issue: Function call `staticcall` is called multiple times at lines [42].

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue: Function call `encodeWithSelector` is called multiple times at lines [61].

46:                revertBytes(data);	// @audit-issue: Function call `revertBytes` is called multiple times at lines [64].

133:                    IUniswapV3Pool(uniswapPoolAddress).ticks(tickUpper);	// @audit-issue: Function call `IUniswapV3Pool` is called multiple times at lines [106, 107, 104, 116].
```
[60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L60-L60), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L46-L46), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L133-L133), 


```solidity
Path: ./src/libraries/Perp.sol

139:            ScaledAsset.createUserStatus(),	// @audit-issue: Function call `createUserStatus` is called multiple times at lines [138].

153:            ScaledAsset.createUserStatus(),	// @audit-issue: Function call `createUserStatus` is called multiple times at lines [154].

213:            saveLastFeeGrowth(_sqrtAssetStatus);	// @audit-issue: Function call `saveLastFeeGrowth` is called multiple times at lines [227, 251].

268:        (uint256 receivedAmount0, uint256 receivedAmount1) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).burn(	// @audit-issue: Function call `IUniswapV3Pool` is called multiple times at lines [272, 279].

273:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, type(uint128).max, type(uint128).max	// @audit-issue: Function call is called multiple times at line(s) [273].

567:                revert SqrtAssetCanNotCoverBorrow();	// @audit-issue: Function call `SqrtAssetCanNotCoverBorrow` is called multiple times at lines [555].

652:            _userStatus.sqrtPerp.amount.abs(),	// @audit-issue: Function call `abs` is called multiple times at lines [645].

732:        IUniswapV3Pool(_assetStatus.uniswapPool).collect(	// @audit-issue: Function call `IUniswapV3Pool` is called multiple times at lines [727].

733:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, type(uint128).max, type(uint128).max	// @audit-issue: Function call is called multiple times at line(s) [733].

771:            offsetStable = LPMath.calculateAmount1OffsetWithTick(_tickLower, openAmount.abs(), openAmount < 0);	// @audit-issue: Function call `abs` is called multiple times at lines [768].
```
[139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L139-L139), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L153-L153), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L213-L213), [268](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L268-L268), [273](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L273-L273), [567](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L567-L567), [652](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L652-L652), [732](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L732-L732), [733](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L733-L733), [771](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L771-L771), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

108:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");	// @audit-issue: Function call `getAvailableCollateralValue` is called multiple times at lines [118].

195:            FixedPointMathLib.mulDivDown(_interestRate, getTotalDebtValue(tokenState), Constants.ONE),	// @audit-issue: Function call `getTotalDebtValue` is called multiple times at lines [203].
```
[108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L108-L108), [195](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L195-L195), 


```solidity
Path: ./src/libraries/SlippageLib.sol

36:                revert SlippageTooLarge();	// @audit-issue: Function call `SlippageTooLarge` is called multiple times at lines [41].
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L36-L36), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

76:        require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");	// @audit-issue: Function call `token0` is called multiple times at lines [70, 78, 84].

84:            isQuoteZero ? uniswapPool.token1() : uniswapPool.token0(),	// @audit-issue: Function call `token1` is called multiple times at lines [76, 70].

170:                ScaledAsset.createAssetStatus(),	// @audit-issue: Function call `createAssetStatus` is called multiple times at lines [162].
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L76-L76), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L84-L84), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L170-L170), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

99:                    ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);	// @audit-issue: Function call `ERC20` is called multiple times at lines [106].
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L99-L99), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

56:            PositionCalculator.checkSafe(pairStatus, globalData.vaults[tradeParams.vaultId], DataType.FeeAmount(0, 0));	// @audit-issue: Function call `FeeAmount` is called multiple times at lines [48].
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L56-L56), 


```solidity
Path: ./src/libraries/math/LPMath.sol

53:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue: Function call `toInt256` is called multiple times at lines [58].

95:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue: Function call `toInt256` is called multiple times at lines [90].
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L53-L53), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L95-L95), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue: Function call `ERC20` is called multiple times at lines [105, 133, 123, 130].

170:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);	// @audit-issue: Function call `ERC20` is called multiple times at lines [158, 180, 175, 177].
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L170-L170), 


#### Recommendation

Cache the result of function calls in Solidity instead of making repeated calls to the same function. This practice significantly reduces gas consumption by minimizing costly function call operations.

### Avoid updating storage when the value hasn't changed
Manipulating storage in solidity is gas-intensive. It can be optimized by avoiding unnecessary storage updates when the new value equals the existing value. If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload (2100 gas) or a Gwarmaccess (100 gas).

```solidity
Path: ./src/PredyPool.sol

303:        allowedTraders[trader][pairId] = enabled;	// @audit-issue
```
[303](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L303-L303), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue

93:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue

99:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L93-L93), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L99-L99), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

44:        whitelistFiller = _whitelistFiller;	// @audit-issue

46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue

141:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue

147:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L46-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L133-L133), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L141-L141), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L147-L147), 


#### Recommendation

Optimize gas usage by avoiding storage updates in Solidity when the new value is the same as the existing one. This prevents unnecessary gas expenditure from storage resets, balancing the cost against cold or warm storage access as needed.

### Avoid zero transfers calls
In Solidity, unnecessary operations can waste gas. For example, a transfer function without a zero amount check uses gas even if called with a zero amount, since the contract state remains unchanged. Implementing a zero amount check avoids these unnecessary function calls, saving gas and improving efficiency.

```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

46:        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L46-L46), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

52:        ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L52-L52), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue
```
[152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), 


```solidity
Path: ./src/types/GlobalData.sol

85:        ERC20(currency).safeTransfer(to, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L85-L85), 


#### Recommendation

Include a condition in your transfer functions to check for and prevent zero-value transfers. Implement a `require(amount > 0, "Transfer amount must be greater than zero");` statement at the beginning of the function. This preemptive check ensures that the function only proceeds with non-zero transfer amounts, avoiding wasteful operations and saving gas. Apply this optimization across all functions involving token transfers or similar operations to improve your contract's gas efficiency and operational effectiveness.

### Functions guaranteed to `revert` when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

```solidity
Path: ./src/PredyPool.sol

97:    function setOperator(address newOperator) external onlyOperator {	// @audit-issue

109:    function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {	// @audit-issue

119:    function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)	// @audit-issue

133:    function updateIRMParams(	// @audit-issue

147:    function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {	// @audit-issue

157:    function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {	// @audit-issue

167:    function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {	// @audit-issue

177:    function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {	// @audit-issue

199:    function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {	// @audit-issue

286:    function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {	// @audit-issue

300:    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {	// @audit-issue

329:    function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {	// @audit-issue
```
[97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L97-L97), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L109-L109), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L119-L119), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L133-L133), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L147-L147), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L157-L157), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L167-L167), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L177-L177), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L199-L199), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L286-L286), [300](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L300-L300), [329](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L329-L329), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(	// @audit-issue

392:    function removePosition(uint256 positionId) external onlyFiller {	// @audit-issue
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L79), [392](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L392-L392), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

102:    function predyTradeAfterCallback(	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L102), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

20:    function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L20-L20), 


```solidity
Path: ./src/base/BaseMarket.sol

28:    function predySettlementCallback(	// @audit-issue

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {	// @audit-issue

92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L28-L28), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L92-L92), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

38:    function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)	// @audit-issue

49:    function predySettlementCallback(	// @audit-issue

128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {	// @audit-issue

132:    function updateQuoter(address newQuoter) external onlyFiller {	// @audit-issue

140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L38-L38), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L49-L49), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L128-L128), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L132-L132), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L140-L140), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

21:    function mint(address account, uint256 amount) external virtual override onlyController {	// @audit-issue

25:    function burn(address account, uint256 amount) external virtual override onlyController {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L25-L25), 


#### Recommendation

Mark functions with access restrictions like 'onlyOwner' as 'payable' in Solidity. This reduces gas costs for legitimate callers by removing the compiler's checks for incoming payments, as the function will revert for unauthorized users anyway.

### `abi.encode()` is less efficient than `abi.encodePacked()`
When working with Solidity, it is important to pay attention to gas efficiency in order to optimize smart contracts. In this blog post, we will compare the gas costs of `abi.encode()` and `abi.encodepacked()` and explore why the latter is more efficient.Source: [reference](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

169:                abi.encode(	// @audit-issue
170:                    CallbackData(
171:                        GammaTradeMarketLib.CallbackType.TRADE, gammaOrder.info.trader, gammaOrder.marginAmount
172:                    )
173:                )

265:            abi.encode(CallbackData(triggerType, userPosition.owner, 0))	// @audit-issue

303:            abi.encode(CallbackData(triggerType, userPosition.owner, 0))	// @audit-issue

323:                abi.encode(	// @audit-issue
324:                    CallbackData(
325:                        GammaTradeMarketLib.CallbackType.QUOTE, gammaOrder.info.trader, gammaOrder.marginAmount
326:                    )
327:                )
```
[169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L169-L173), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L265-L265), [303](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L303-L303), [323](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L323-L327), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

45:            abi.encode(	// @audit-issue
46:                GAMMA_MODIFY_INFO_TYPE_HASH,
47:                info.isEnabled,
48:                info.expiration,
49:                info.maximaDeviation,
50:                info.lowerLimit,
51:                info.upperLimit,
52:                info.hedgeInterval,
53:                info.sqrtPriceTrigger,
54:                info.minSlippageTolerance,
55:                info.maxSlippageTolerance,
56:                info.auctionPeriod,
57:                info.auctionRange
58:            )

117:            abi.encode(	// @audit-issue
118:                GAMMA_ORDER_TYPE_HASH,
119:                order.info.hash(),
120:                order.pairId,
121:                order.positionId,
122:                order.entryTokenAddress,
123:                order.quantity,
124:                order.quantitySqrt,
125:                order.marginAmount,
126:                order.baseSqrtPrice,
127:                order.slippageTolerance,
128:                order.leverage,
129:                GammaModifyInfoLib.hash(order.modifyInfo)
130:            )
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L45-L58), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L117-L130), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

56:            abi.encode(	// @audit-issue
57:                PERP_ORDER_TYPE_HASH,
58:                order.info.hash(),
59:                order.pairId,
60:                order.entryTokenAddress,
61:                order.tradeAmount,
62:                order.marginAmount,
63:                order.takeProfitPrice,
64:                order.stopLossPrice,
65:                order.slippageTolerance,
66:                order.leverage,
67:                order.validatorAddress,
68:                keccak256(order.validationData)
69:            )
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L56-L69), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

191:                abi.encode(	// @audit-issue
192:                    CallbackData(
193:                        CallbackSource.TRADE3, perpOrder.info.trader, 0, perpOrder.leverage, resolvedOrder, orderId
194:                    )
195:                )

299:                abi.encode(	// @audit-issue
300:                    CallbackData(
301:                        CallbackSource.QUOTE3,
302:                        perpOrder.info.trader,
303:                        0,
304:                        perpOrder.leverage,
305:                        PerpOrderV3Lib.resolve(perpOrder, bytes("")),
306:                        0
307:                    )
308:                )
```
[191](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L191-L195), [299](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L299-L308), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

60:            abi.encode(	// @audit-issue
61:                PERP_ORDER_V3_TYPE_HASH,
62:                order.info.hash(),
63:                order.pairId,
64:                order.entryTokenAddress,
65:                keccak256(bytes(order.side)),
66:                order.quantity,
67:                order.marginAmount,
68:                order.limitPrice,
69:                order.stopPrice,
70:                order.leverage,
71:                order.reduceOnly,
72:                order.closePosition,
73:                keccak256(order.auctionData)
74:            )
```
[60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L60-L74), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

19:        return keccak256(abi.encode(ORDER_INFO_TYPE_HASH, info.market, info.trader, info.nonce, info.deadline));	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L19-L19), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

57:        bytes memory data = abi.encode(pairStatus);	// @audit-issue

65:        bytes memory data = abi.encode(vaultStatus);	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L57-L57), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L65-L65), 


```solidity
Path: ./src/base/BaseMarket.sol

68:        return abi.encode(	// @audit-issue
69:            SettlementCallbackLib.SettlementParams(
70:                filler,
71:                settlementParams.contractAddress,
72:                settlementParams.encodedData,
73:                settlementParams.maxQuoteAmount,
74:                settlementParams.price,
75:                settlementParams.fee
76:            )
77:        );

115:        bytes memory data = abi.encode(tradeResult);	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L68-L77), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L115-L115), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

110:        return abi.encode(	// @audit-issue
111:            SettlementParamsV3Internal(
112:                filler,
113:                settlementParams.contractAddress,
114:                settlementParams.encodedData,
115:                settlementParams.maxQuoteAmountPrice,
116:                settlementParams.minQuoteAmountPrice,
117:                settlementParams.price,
118:                settlementParams.feePrice,
119:                settlementParams.minFee
120:            )
121:        );

163:        bytes memory data = abi.encode(tradeResult);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L110-L121), [163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L163-L163), 


#### Recommendation

Optimize gas usage by preferring 'abi.encodePacked()' over 'abi.encode()' where appropriate, as 'abi.encodePacked()' is generally more gas-efficient. However, ensure its suitability based on the data type and structure requirements, as 'abi.encodePacked()' can lead to ambiguity in certain cases.

### `x += y` costs more gas than `x = x + y` for stack variables
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./src/libraries/PositionCalculator.sol

123:        minValue += calculateMinValue(sqrtPrice, positionParams, riskRatio);	// @audit-issue

125:        vaultValue += calculateValue(sqrtPrice, positionParams);	// @audit-issue

127:        debtValue += calculateSquartDebtValue(sqrtPrice, positionParams);	// @audit-issue

131:        minValue += marginAmount;	// @audit-issue

132:        vaultValue += marginAmount;	// @audit-issue
```
[123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L123-L123), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L131-L131), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L132-L132), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

26:            ir += (utilizationRatio * irmParams.slope1) / _ONE;	// @audit-issue

28:            ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;	// @audit-issue

29:            ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L26-L26), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L28-L28), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L29-L29), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

69:        vault.margin += tradeResult.fee + tradeResult.payoff.perpPayoff + tradeResult.payoff.sqrtPayoff;	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L69-L69), 


#### Recommendation

Prefer using 'x = x + y' over 'x += y' for state variable assignments in Solidity to save gas. The latter incurs extra costs due to additional JUMP instructions and stack operations associated with non-inlined function calls.

### Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

```solidity
Path: ./src/PredyPool.sol

109:    function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {	// @audit-issue

119:    function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)	// @audit-issue

133:    function updateIRMParams(
134:        uint256 pairId,
135:        InterestRateModel.IRMParams memory quoteIrmParams,	// @audit-issue
136:        InterestRateModel.IRMParams memory baseIrmParams
137:    ) external onlyPoolOwner(pairId) {

133:    function updateIRMParams(
134:        uint256 pairId,
135:        InterestRateModel.IRMParams memory quoteIrmParams,
136:        InterestRateModel.IRMParams memory baseIrmParams	// @audit-issue
137:    ) external onlyPoolOwner(pairId) {

251:    function reallocate(uint256 pairId, bytes memory settlementData)	// @audit-issue

265:    function trade(TradeParams memory tradeParams, bytes memory settlementData)	// @audit-issue

313:    function execLiquidationCall(uint256 vaultId, uint256 closeRatio, bytes memory settlementData)	// @audit-issue
```
[109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L109-L109), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L119-L119), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L133-L137), [136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L133-L137), [251](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L251-L251), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L265-L265), [313](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L313-L313), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

42:    function executeTradeL2(GammaOrderL2 memory order, bytes memory sig, SettlementParamsV3 memory settlementParams)	// @audit-issue

73:    function modifyAutoHedgeAndClose(GammaModifyOrderL2 memory order, bytes memory sig) external {	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L42-L42), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L73-L73), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(
80:        IPredyPool.TradeParams memory tradeParams,	// @audit-issue
81:        IPredyPool.TradeResult memory tradeResult
82:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

79:    function predyTradeAfterCallback(
80:        IPredyPool.TradeParams memory tradeParams,
81:        IPredyPool.TradeResult memory tradeResult	// @audit-issue
82:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

138:    function execLiquidationCall(
139:        uint256 vaultId,
140:        uint256 closeRatio,
141:        IFillerMarket.SettlementParamsV3 memory settlementParams	// @audit-issue
142:    ) external override returns (IPredyPool.TradeResult memory tradeResult) {

152:    function _executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)	// @audit-issue

211:    function _modifyAutoHedgeAndClose(GammaOrder memory gammaOrder, bytes memory sig) internal {	// @audit-issue

227:    function autoHedge(uint256 positionId, SettlementParamsV3 memory settlementParams)	// @audit-issue

274:    function autoClose(uint256 positionId, SettlementParamsV3 memory settlementParams)	// @audit-issue

315:    function quoteTrade(GammaOrder memory gammaOrder, SettlementParamsV3 memory settlementParams) external {	// @audit-issue

436:    function _saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)	// @audit-issue

444:    function _verifyOrder(ResolvedOrder memory order) internal {	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L82), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L82), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L138-L142), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L152-L152), [211](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L211-L211), [227](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L227-L227), [274](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L274-L274), [315](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L315-L315), [436](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L436-L436), [444](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L444-L444), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

12:    function executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L12-L12), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

20:    function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L20-L20), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

43:    function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {	// @audit-issue

115:    function hash(GammaOrder memory order) internal pure returns (bytes32) {	// @audit-issue

134:    function resolve(GammaOrder memory gammaOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L43-L43), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L115-L115), [134](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L134-L134), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

59:    function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)	// @audit-issue

106:    function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)	// @audit-issue

141:    function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)	// @audit-issue

167:    function calculateSlippageToleranceByPrice(uint256 price1, uint256 price2, AuctionParams memory auctionParams)	// @audit-issue

189:    function saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L59-L59), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L106-L106), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L141-L141), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L167-L167), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L189-L189), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

54:    function hash(PerpOrder memory order) internal pure returns (bytes32) {	// @audit-issue

73:    function resolve(PerpOrder memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L54-L54), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L73-L73), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

102:    function predyTradeAfterCallback(
103:        IPredyPool.TradeParams memory tradeParams,	// @audit-issue
104:        IPredyPool.TradeResult memory tradeResult
105:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

102:    function predyTradeAfterCallback(
103:        IPredyPool.TradeParams memory tradeParams,
104:        IPredyPool.TradeResult memory tradeResult	// @audit-issue
105:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

149:    function executeOrderV3(SignedOrder memory order, SettlementParamsV3 memory settlementParams)	// @audit-issue

159:    function _executeOrderV3(
160:        PerpOrderV3 memory perpOrder,	// @audit-issue
161:        bytes memory sig,
162:        SettlementParamsV3 memory settlementParams,
163:        uint64 orderId
164:    ) internal returns (IPredyPool.TradeResult memory tradeResult) {

159:    function _executeOrderV3(
160:        PerpOrderV3 memory perpOrder,
161:        bytes memory sig,	// @audit-issue
162:        SettlementParamsV3 memory settlementParams,
163:        uint64 orderId
164:    ) internal returns (IPredyPool.TradeResult memory tradeResult) {

159:    function _executeOrderV3(
160:        PerpOrderV3 memory perpOrder,
161:        bytes memory sig,
162:        SettlementParamsV3 memory settlementParams,	// @audit-issue
163:        uint64 orderId
164:    ) internal returns (IPredyPool.TradeResult memory tradeResult) {

236:    function _calculateNetValue(DataType.Vault memory vault, uint256 price) internal pure returns (uint256) {	// @audit-issue

242:    function _calculatePositionValue(DataType.Vault memory vault, uint256 sqrtPrice) internal pure returns (int256) {	// @audit-issue

265:    function _saveUserPosition(UserPosition storage userPosition, PerpOrder memory perpOrder) internal {	// @audit-issue

275:    function quoteExecuteOrderV3(
276:        PerpOrderV3 memory perpOrder,	// @audit-issue
277:        SettlementParamsV3 memory settlementParams,
278:        address filler
279:    ) external {

275:    function quoteExecuteOrderV3(
276:        PerpOrderV3 memory perpOrder,
277:        SettlementParamsV3 memory settlementParams,	// @audit-issue
278:        address filler
279:    ) external {

314:    function _verifyOrder(ResolvedOrder memory order, uint256 amount) internal {	// @audit-issue

327:    function _verifyOrderV3(ResolvedOrder memory order, uint256 amount) internal {	// @audit-issue
```
[103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L105), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L105), [149](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L149-L149), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L159-L164), [161](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L159-L164), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L159-L164), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L236-L236), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L242-L242), [265](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L265-L265), [276](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L275-L279), [277](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L275-L279), [314](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L314-L314), [327](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L327-L327), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

58:    function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {	// @audit-issue

78:    function resolve(PerpOrderV3 memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L58-L58), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L78-L78), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

28:    function executeOrderV3L2(
29:        PerpOrderV3L2 memory compressedOrder,	// @audit-issue
30:        bytes memory sig,
31:        SettlementParamsV3 memory settlementParams,
32:        uint64 orderId
33:    ) external nonReentrant returns (IPredyPool.TradeResult memory) {

28:    function executeOrderV3L2(
29:        PerpOrderV3L2 memory compressedOrder,
30:        bytes memory sig,	// @audit-issue
31:        SettlementParamsV3 memory settlementParams,
32:        uint64 orderId
33:    ) external nonReentrant returns (IPredyPool.TradeResult memory) {

28:    function executeOrderV3L2(
29:        PerpOrderV3L2 memory compressedOrder,
30:        bytes memory sig,
31:        SettlementParamsV3 memory settlementParams,	// @audit-issue
32:        uint64 orderId
33:    ) external nonReentrant returns (IPredyPool.TradeResult memory) {
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L28-L33), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L28-L33), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L28-L33), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

26:    function getFinalTradeAmount(
27:        int256 currentPositionAmount,
28:        string memory side,	// @audit-issue
29:        uint256 quantity,
30:        bool reduceOnly,
31:        bool closePosition
32:    ) internal pure returns (int256 finalTradeAmount) {

80:    function validateTrade(
81:        IPredyPool.TradeResult memory tradeResult,	// @audit-issue
82:        int256 tradeAmount,
83:        uint256 limitPrice,
84:        uint256 stopPrice,
85:        bytes memory auctionData
86:    ) internal view {

80:    function validateTrade(
81:        IPredyPool.TradeResult memory tradeResult,
82:        int256 tradeAmount,
83:        uint256 limitPrice,
84:        uint256 stopPrice,
85:        bytes memory auctionData	// @audit-issue
86:    ) internal view {

138:    function validateStopPrice(
139:        uint256 oraclePrice,
140:        uint256 tradePrice,
141:        int256 tradeAmount,
142:        uint256 stopPrice,
143:        bytes memory auctionData	// @audit-issue
144:    ) internal pure returns (bool) {

188:    function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L32), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L80-L86), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L80-L86), [143](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L138-L144), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L188-L188), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

22:    function swapExactIn(
23:        address,
24:        address baseToken,
25:        bytes memory data,	// @audit-issue
26:        uint256 amountIn,
27:        uint256 amountOutMinimum,
28:        address recipient
29:    ) external override returns (uint256 amountOut) {

38:    function swapExactOut(
39:        address quoteToken,
40:        address,
41:        bytes memory data,	// @audit-issue
42:        uint256 amountOut,
43:        uint256 amountInMaximum,
44:        address recipient
45:    ) external override returns (uint256 amountIn) {

58:    function quoteSwapExactIn(bytes memory data, uint256 amountIn) external override returns (uint256 amountOut) {	// @audit-issue

62:    function quoteSwapExactOut(bytes memory data, uint256 amountOut) external override returns (uint256 amountIn) {	// @audit-issue
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L22-L29), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L38-L45), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L58-L58), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L62-L62), 


```solidity
Path: ./src/libraries/PerpFee.sol

16:    function computeUserFee(
17:        DataType.PairStatus memory assetStatus,	// @audit-issue
18:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:        Perp.UserStatus memory userStatus
20:    ) internal view returns (DataType.FeeAmount memory) {

16:    function computeUserFee(
17:        DataType.PairStatus memory assetStatus,
18:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:        Perp.UserStatus memory userStatus	// @audit-issue
20:    ) internal view returns (DataType.FeeAmount memory) {

65:    function computePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus memory sqrtPerp)	// @audit-issue

95:    function settlePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus storage sqrtPerp)	// @audit-issue

111:    function computeRebalanceInterest(
112:        uint256 pairId,
113:        Perp.SqrtPerpAssetStatus memory assetStatus,	// @audit-issue
114:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
115:        Perp.UserStatus memory userStatus
116:    ) internal view returns (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) {

111:    function computeRebalanceInterest(
112:        uint256 pairId,
113:        Perp.SqrtPerpAssetStatus memory assetStatus,
114:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
115:        Perp.UserStatus memory userStatus	// @audit-issue
116:    ) internal view returns (int256 rebalanceInterestBase, int256 rebalanceInterestQuote) {
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L16-L20), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L16-L20), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L65-L65), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L95-L95), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L111-L116), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L111-L116), 


```solidity
Path: ./src/libraries/UniHelper.sol

77:    function revertBytes(bytes memory errMsg) internal pure {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L77-L77), 


```solidity
Path: ./src/libraries/Reallocation.sol

18:    function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)	// @audit-issue

39:    function _getNewRange(
40:        DataType.PairStatus memory _assetStatusUnderlying,	// @audit-issue
41:        ScaledAsset.AssetStatus memory _token0Status,
42:        ScaledAsset.AssetStatus memory _token1Status,
43:        int24 currentTick,
44:        int24 tickSpacing
45:    ) internal pure returns (int24 lower, int24 upper) {

39:    function _getNewRange(
40:        DataType.PairStatus memory _assetStatusUnderlying,
41:        ScaledAsset.AssetStatus memory _token0Status,	// @audit-issue
42:        ScaledAsset.AssetStatus memory _token1Status,
43:        int24 currentTick,
44:        int24 tickSpacing
45:    ) internal pure returns (int24 lower, int24 upper) {

39:    function _getNewRange(
40:        DataType.PairStatus memory _assetStatusUnderlying,
41:        ScaledAsset.AssetStatus memory _token0Status,
42:        ScaledAsset.AssetStatus memory _token1Status,	// @audit-issue
43:        int24 currentTick,
44:        int24 tickSpacing
45:    ) internal pure returns (int24 lower, int24 upper) {

92:    function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {	// @audit-issue

98:    function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L18-L18), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L39-L45), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L39-L45), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L39-L45), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L92-L92), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L98-L98), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

34:    function isLiquidatable(
35:        DataType.PairStatus memory pairStatus,	// @audit-issue
36:        DataType.Vault memory _vault,
37:        DataType.FeeAmount memory feeAmount
38:    ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {

34:    function isLiquidatable(
35:        DataType.PairStatus memory pairStatus,
36:        DataType.Vault memory _vault,	// @audit-issue
37:        DataType.FeeAmount memory feeAmount
38:    ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {

34:    function isLiquidatable(
35:        DataType.PairStatus memory pairStatus,
36:        DataType.Vault memory _vault,
37:        DataType.FeeAmount memory feeAmount	// @audit-issue
38:    ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {

49:    function checkSafe(
50:        DataType.PairStatus memory pairStatus,	// @audit-issue
51:        DataType.Vault memory _vault,
52:        DataType.FeeAmount memory feeAmount
53:    ) internal view returns (int256 minMargin) {

49:    function checkSafe(
50:        DataType.PairStatus memory pairStatus,
51:        DataType.Vault memory _vault,	// @audit-issue
52:        DataType.FeeAmount memory feeAmount
53:    ) internal view returns (int256 minMargin) {

49:    function checkSafe(
50:        DataType.PairStatus memory pairStatus,
51:        DataType.Vault memory _vault,
52:        DataType.FeeAmount memory feeAmount	// @audit-issue
53:    ) internal view returns (int256 minMargin) {

63:    function getIsSafe(
64:        DataType.PairStatus memory pairStatus,	// @audit-issue
65:        DataType.Vault memory _vault,
66:        DataType.FeeAmount memory feeAmount
67:    ) internal view returns (int256 minMargin, bool isSafe, bool hasPosition) {

63:    function getIsSafe(
64:        DataType.PairStatus memory pairStatus,
65:        DataType.Vault memory _vault,	// @audit-issue
66:        DataType.FeeAmount memory feeAmount
67:    ) internal view returns (int256 minMargin, bool isSafe, bool hasPosition) {

63:    function getIsSafe(
64:        DataType.PairStatus memory pairStatus,
65:        DataType.Vault memory _vault,
66:        DataType.FeeAmount memory feeAmount	// @audit-issue
67:    ) internal view returns (int256 minMargin, bool isSafe, bool hasPosition) {

75:    function calculateMinMargin(
76:        DataType.PairStatus memory pairStatus,	// @audit-issue
77:        DataType.Vault memory vault,
78:        DataType.FeeAmount memory feeAmount
79:    ) internal view returns (int256 minMargin, int256 vaultValue, bool hasPosition, uint256 sqrtOraclePrice) {

75:    function calculateMinMargin(
76:        DataType.PairStatus memory pairStatus,
77:        DataType.Vault memory vault,	// @audit-issue
78:        DataType.FeeAmount memory feeAmount
79:    ) internal view returns (int256 minMargin, int256 vaultValue, bool hasPosition, uint256 sqrtOraclePrice) {

75:    function calculateMinMargin(
76:        DataType.PairStatus memory pairStatus,
77:        DataType.Vault memory vault,
78:        DataType.FeeAmount memory feeAmount	// @audit-issue
79:    ) internal view returns (int256 minMargin, int256 vaultValue, bool hasPosition, uint256 sqrtOraclePrice) {

117:    function calculateMinValue(
118:        int256 marginAmount,
119:        PositionParams memory positionParams,	// @audit-issue
120:        uint256 sqrtPrice,
121:        uint256 riskRatio
122:    ) internal pure returns (int256 minValue, int256 vaultValue, uint256 debtValue, bool hasPosition) {

135:    function getHasPosition(DataType.Vault memory _vault) internal pure returns (bool hasPosition) {	// @audit-issue

141:    function getSqrtIndexPrice(DataType.PairStatus memory pairStatus) internal view returns (uint256 sqrtPriceX96) {	// @audit-issue

151:    function getPositionWithFeeAmount(Perp.UserStatus memory perpUserStatus, DataType.FeeAmount memory feeAmount)	// @audit-issue

163:    function getPosition(Perp.UserStatus memory _perpUserStatus)	// @audit-issue

175:    function getHasPositionFlag(PositionParams memory _positionParams) internal pure returns (bool) {	// @audit-issue

186:    function calculateMinValue(uint256 _sqrtPrice, PositionParams memory _positionParams, uint256 _riskRatio)	// @audit-issue

231:    function calculateValue(uint256 _sqrtPrice, PositionParams memory _positionParams) internal pure returns (int256) {	// @audit-issue

238:    function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L34-L38), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L34-L38), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L34-L38), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L49-L53), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L49-L53), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L49-L53), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L63-L67), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L63-L67), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L63-L67), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L75-L79), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L75-L79), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L75-L79), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L117-L122), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L135-L135), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L141-L141), [151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L151-L151), [163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L163-L163), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L175-L175), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L186-L186), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L231-L231), [238](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L238-L238), 


```solidity
Path: ./src/libraries/Trade.sol

32:    function trade(
33:        GlobalDataLibrary.GlobalData storage globalData,
34:        IPredyPool.TradeParams memory tradeParams,	// @audit-issue
35:        bytes memory settlementData
36:    ) external returns (IPredyPool.TradeResult memory tradeResult) {

32:    function trade(
33:        GlobalDataLibrary.GlobalData storage globalData,
34:        IPredyPool.TradeParams memory tradeParams,
35:        bytes memory settlementData	// @audit-issue
36:    ) external returns (IPredyPool.TradeResult memory tradeResult) {

76:    function swap(
77:        GlobalDataLibrary.GlobalData storage globalData,
78:        uint256 pairId,
79:        SwapStableResult memory swapParams,	// @audit-issue
80:        bytes memory settlementData,
81:        uint256 sqrtPrice,
82:        uint256 vaultId
83:    ) internal returns (SwapStableResult memory) {

76:    function swap(
77:        GlobalDataLibrary.GlobalData storage globalData,
78:        uint256 pairId,
79:        SwapStableResult memory swapParams,
80:        bytes memory settlementData,	// @audit-issue
81:        uint256 sqrtPrice,
82:        uint256 vaultId
83:    ) internal returns (SwapStableResult memory) {

122:    function divToStable(
123:        SwapStableResult memory swapParams,	// @audit-issue
124:        int256 amountBase,
125:        int256 amountQuote,
126:        int256 totalAmountStable
127:    ) internal pure returns (SwapStableResult memory swapResult) {
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L32-L36), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L32-L36), [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L76-L83), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L76-L83), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L122-L127), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

75:    function emitInterestGrowthEvent(
76:        DataType.PairStatus memory assetStatus,	// @audit-issue
77:        uint256 interestRateQuote,
78:        uint256 interestRateBase,
79:        uint256 totalProtocolFeeQuote,
80:        uint256 totalProtocolFeeBase
81:    ) internal {
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L75-L81), 


```solidity
Path: ./src/libraries/Perp.sol

159:    function updateRebalanceInterestGrowth(
160:        DataType.PairStatus memory _pairStatus,	// @audit-issue
161:        SqrtPerpAssetStatus storage _sqrtAssetStatus
162:    ) internal {

426:    function computeRequiredAmounts(
427:        SqrtPerpAssetStatus storage _sqrtAssetStatus,
428:        bool _isQuoteZero,
429:        UserStatus memory _userStatus,	// @audit-issue
430:        int256 _tradeSqrtAmount
431:    ) internal returns (int256 requiredAmountUnderlying, int256 requiredAmountStable) {

470:    function updatePosition(
471:        DataType.PairStatus storage _pairStatus,
472:        UserStatus storage _userStatus,
473:        UpdatePerpParams memory _updatePerpParams,	// @audit-issue
474:        UpdateSqrtPerpParams memory _updateSqrtPerpParams
475:    ) internal returns (IPredyPool.Payoff memory payoff) {

470:    function updatePosition(
471:        DataType.PairStatus storage _pairStatus,
472:        UserStatus storage _userStatus,
473:        UpdatePerpParams memory _updatePerpParams,
474:        UpdateSqrtPerpParams memory _updateSqrtPerpParams	// @audit-issue
475:    ) internal returns (IPredyPool.Payoff memory payoff) {

585:    function getAvailableSqrtAmount(SqrtPerpAssetStatus memory _assetStatus, bool _isWithdraw)	// @audit-issue

604:    function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {	// @audit-issue

707:    function increase(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)	// @audit-issue

719:    function decrease(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)	// @audit-issue

745:    function calculateSqrtPerpOffset(
746:        UserStatus memory _userStatus,	// @audit-issue
747:        int24 _tickLower,
748:        int24 _tickUpper,
749:        int256 _tradeSqrtAmount,
750:        bool _isQuoteZero
751:    ) internal pure returns (int256 offsetUnderlying, int256 offsetStable) {
```
[160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L159-L162), [429](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L426-L431), [473](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L470-L475), [474](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L470-L475), [585](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L585-L585), [604](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L604-L604), [707](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L707-L707), [719](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L719-L719), [746](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L745-L751), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

130:    function computeUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus memory _userStatus)	// @audit-issue

142:    function settleUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus storage _userStatus)	// @audit-issue

155:    function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)	// @audit-issue

170:    function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)	// @audit-issue

217:    function getTotalCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

222:    function getTotalDebtValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

226:    function getAvailableCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

230:    function getUtilizationRatio(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue
```
[130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L130-L130), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L142-L142), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L155-L155), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L170-L170), [217](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L217-L217), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L222-L222), [226](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L226-L226), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L230-L230), 


```solidity
Path: ./src/libraries/SlippageLib.sol

21:    function checkPrice(
22:        uint256 sqrtBasePrice,
23:        IPredyPool.TradeResult memory tradeResult,	// @audit-issue
24:        uint256 slippageTolerance,
25:        uint256 maxAcceptableSqrtPriceRange
26:    ) internal pure {
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L21-L26), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

18:    function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L18-L18), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

10:    function toPermit(ResolvedOrder memory order)	// @audit-issue

23:    function transferDetails(ResolvedOrder memory order, address to)	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L10-L10), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L23-L23), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

23:    function validate(ResolvedOrder memory resolvedOrder) internal view {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L23-L23), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

18:    function hash(OrderInfo memory info) internal pure returns (bytes32) {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L18-L18), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

43:    function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)	// @audit-issue

56:    function revertPairStatus(DataType.PairStatus memory pairStatus) internal pure {	// @audit-issue

64:    function revertVaultStatus(IPredyPool.VaultStatus memory vaultStatus) internal pure {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L43-L43), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L56-L56), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

53:    function addPair(
54:        GlobalDataLibrary.GlobalData storage _global,
55:        mapping(address => bool) storage allowedUniswapPools,
56:        AddPairParams memory _addPairParam	// @audit-issue
57:    ) external returns (uint256 pairId) {

118:    function updateAssetRiskParams(DataType.PairStatus storage _pairStatus, Perp.AssetRiskParams memory _riskParams)	// @audit-issue

128:    function updateIRMParams(
129:        DataType.PairStatus storage _pairStatus,
130:        InterestRateModel.IRMParams memory _quoteIrmParams,	// @audit-issue
131:        InterestRateModel.IRMParams memory _baseIrmParams
132:    ) external {

128:    function updateIRMParams(
129:        DataType.PairStatus storage _pairStatus,
130:        InterestRateModel.IRMParams memory _quoteIrmParams,
131:        InterestRateModel.IRMParams memory _baseIrmParams	// @audit-issue
132:    ) external {

142:    function _storePairStatus(
143:        address quoteToken,
144:        mapping(uint256 => DataType.PairStatus) storage _pairs,
145:        uint256 _pairId,
146:        address _tokenAddress,
147:        bool _isQuoteZero,
148:        AddPairParams memory _addPairParam	// @audit-issue
149:    ) internal {

213:    function validateRiskParams(Perp.AssetRiskParams memory _assetRiskParams) internal pure {	// @audit-issue

219:    function validateIRMParams(InterestRateModel.IRMParams memory _irmParams) internal pure {	// @audit-issue
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L53-L57), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L118-L118), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L128-L132), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L128-L132), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L142-L149), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L213-L213), [219](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L219-L219), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

27:    function reallocate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId, bytes memory settlementData)	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L27-L27), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

39:    function liquidate(
40:        uint256 vaultId,
41:        uint256 closeRatio,
42:        GlobalDataLibrary.GlobalData storage globalData,
43:        bytes memory settlementData	// @audit-issue
44:    ) external returns (IPredyPool.TradeResult memory tradeResult) {

129:    function checkVaultIsDanger(
130:        DataType.PairStatus memory pairStatus,	// @audit-issue
131:        DataType.Vault memory vault,
132:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache
133:    ) internal view returns (uint256 sqrtOraclePrice, uint256 slippageTolerance) {

129:    function checkVaultIsDanger(
130:        DataType.PairStatus memory pairStatus,
131:        DataType.Vault memory vault,	// @audit-issue
132:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache
133:    ) internal view returns (uint256 sqrtOraclePrice, uint256 slippageTolerance) {

159:    function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L39-L44), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L129-L133), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L129-L133), [159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L159-L159), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

29:    function trade(
30:        GlobalDataLibrary.GlobalData storage globalData,
31:        IPredyPool.TradeParams memory tradeParams,	// @audit-issue
32:        bytes memory settlementData
33:    ) external returns (IPredyPool.TradeResult memory tradeResult) {

29:    function trade(
30:        GlobalDataLibrary.GlobalData storage globalData,
31:        IPredyPool.TradeParams memory tradeParams,
32:        bytes memory settlementData	// @audit-issue
33:    ) external returns (IPredyPool.TradeResult memory tradeResult) {

68:    function callTradeAfterCallback(
69:        GlobalDataLibrary.GlobalData storage globalData,
70:        IPredyPool.TradeParams memory tradeParams,	// @audit-issue
71:        IPredyPool.TradeResult memory tradeResult
72:    ) internal {

68:    function callTradeAfterCallback(
69:        GlobalDataLibrary.GlobalData storage globalData,
70:        IPredyPool.TradeParams memory tradeParams,
71:        IPredyPool.TradeResult memory tradeResult	// @audit-issue
72:    ) internal {
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L29-L33), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L29-L33), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L68-L72), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L68-L72), 


```solidity
Path: ./src/base/BaseMarket.sol

28:    function predySettlementCallback(
29:        address quoteToken,
30:        address baseToken,
31:        bytes memory settlementData,	// @audit-issue
32:        int256 baseAmountDelta
33:    ) external onlyPredyPool {

40:    function reallocate(uint256 pairId, IFillerMarket.SettlementParams memory settlementParams)	// @audit-issue

47:    function execLiquidationCall(
48:        uint256 vaultId,
49:        uint256 closeRatio,
50:        IFillerMarket.SettlementParams memory settlementParams	// @audit-issue
51:    ) external returns (IPredyPool.TradeResult memory) {

55:    function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams)	// @audit-issue

63:    function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)	// @audit-issue

114:    function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L28-L33), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L40-L40), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L47-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L55-L55), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L63-L63), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L114-L114), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

49:    function predySettlementCallback(
50:        address quoteToken,
51:        address baseToken,
52:        bytes memory settlementData,	// @audit-issue
53:        int256 baseAmountDelta
54:    ) external onlyPredyPool {

61:    function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)	// @audit-issue

89:    function reallocate(uint256 pairId, IFillerMarket.SettlementParamsV3 memory settlementParams)	// @audit-issue

96:    function execLiquidationCall(
97:        uint256 vaultId,
98:        uint256 closeRatio,
99:        IFillerMarket.SettlementParamsV3 memory settlementParams	// @audit-issue
100:    ) external virtual returns (IPredyPool.TradeResult memory) {

105:    function _getSettlementDataFromV3(IFillerMarket.SettlementParamsV3 memory settlementParams, address filler)	// @audit-issue

162:    function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L49-L54), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L61-L61), [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L89-L89), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L96-L100), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L105-L105), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L162-L162), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

25:    function decodeParams(bytes memory settlementData) internal pure returns (SettlementParams memory) {	// @audit-issue

29:    function validate(
30:        mapping(address settlementContractAddress => bool) storage _whiteListedSettlements,
31:        SettlementParams memory settlementParams	// @audit-issue
32:    ) internal view {

40:    function execSettlement(
41:        IPredyPool predyPool,
42:        address quoteToken,
43:        address baseToken,
44:        SettlementParams memory settlementParams,	// @audit-issue
45:        int256 baseAmountDelta
46:    ) internal {

60:    function execSettlementInternal(
61:        IPredyPool predyPool,
62:        address quoteToken,
63:        address baseToken,
64:        SettlementParams memory settlementParams,	// @audit-issue
65:        int256 baseAmountDelta
66:    ) internal {

90:    function sell(
91:        IPredyPool predyPool,
92:        address quoteToken,
93:        address baseToken,
94:        SettlementParams memory settlementParams,	// @audit-issue
95:        address sender,
96:        uint256 price,
97:        uint256 sellAmount
98:    ) internal {

137:    function buy(
138:        IPredyPool predyPool,
139:        address quoteToken,
140:        address baseToken,
141:        SettlementParams memory settlementParams,	// @audit-issue
142:        address sender,
143:        uint256 price,
144:        uint256 buyAmount
145:    ) internal {
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L25-L25), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L29-L32), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L40-L46), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L60-L66), [94](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L90-L98), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L137-L145), 


```solidity
Path: ./src/types/GlobalData.sol

46:    function callSettlementCallback(
47:        GlobalDataLibrary.GlobalData storage globalData,
48:        bytes memory settlementData,	// @audit-issue
49:        int256 deltaBaseAmount
50:    ) internal {
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L46-L50), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

15:    constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L15-L15), 


#### Recommendation

To optimize gas usage in your Solidity functions, mark data types as `calldata` instead of `memory` wherever applicable. This prevents unnecessary data loading into memory. Use `calldata` for function arguments that do not require changes within the function, except when passing them into another function that explicitly requires `memory` storage.

### Splitting `require()` statements that use `&&` saves gas
Instead of using the `&&` operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`:

```solidity
Path: ./src/PriceFeed.sol

52:        require(quoteAnswer > 0 && basePrice.price > 0);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L52-L52), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

203:        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);	// @audit-issue
```
[203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L203-L203), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue

216:        require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");	// @audit-issue

220:        require(	// @audit-issue
221:            _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18
222:                && _irmParams.slope2 <= 10 * 1e18,
223:            "C4"
224:        );
```
[214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L216-L216), [220](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L220-L224), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

45:        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L45-L45), 


```solidity
Path: ./src/base/BaseMarket.sol

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


#### Recommendation

Split 'require()' statements in Solidity that use '&&' into multiple 'require()' statements, each with a single condition. This approach saves 3 gas per '&&', optimizing overall gas usage in the contract.

### Nesting `if` statements that uses `&&` saves gas
In Solidity, the way conditional checks are structured can impact the gas consumption of a transaction. When conditions are combined using `&&` within an `if` statement, Solidity short-circuits the evaluation, meaning that if the first condition is `false`, the subsequent conditions won't be evaluated. This behavior can lead to gas savings compared to using separate nested `if` statements because not all conditions might need to be checked every time. By efficiently structuring these conditional checks, contracts can optimize the gas required for execution, leading to reduced costs for users.


```solidity
Path: ./src/PredyPool.sol

272:        if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {	// @audit-issue
```
[272](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L272-L272), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

306:        if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {	// @audit-issue

342:        if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {	// @audit-issue
```
[306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L342-L342), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

65:            userPosition.hedgeInterval > 0	// @audit-issue
66:                && userPosition.lastHedgedTime + userPosition.hedgeInterval <= block.timestamp

122:        if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {	// @audit-issue

130:        if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {	// @audit-issue

197:        if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L65-L66), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L197-L197), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

38:            if (isLong && currentPositionAmount >= 0) {	// @audit-issue

42:            if (!isLong && currentPositionAmount <= 0) {	// @audit-issue

92:        if (limitPrice == 0 && stopPrice == 0) {	// @audit-issue

97:        } else if (limitPrice > 0 && stopPrice > 0) {	// @audit-issue

100:                !validateLimitPrice(tradePrice, tradeAmount, limitPrice)	// @audit-issue
101:                    && !validateStopPrice(oraclePrice, tradePrice, tradeAmount, stopPrice, auctionData)

127:        if (tradeAmount > 0 && limitPrice < tradePrice) {	// @audit-issue

131:        if (tradeAmount < 0 && limitPrice > tradePrice) {	// @audit-issue

200:            if (tradeAmount > 0 && decayedPrice < tradePrice) {	// @audit-issue

204:            if (tradeAmount < 0 && decayedPrice > tradePrice) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L38-L38), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L42-L42), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L97-L97), [100](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L100-L101), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L131-L131), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L204-L204), 


```solidity
Path: ./src/libraries/PerpFee.sol

117:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue

142:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L142-L142), 


```solidity
Path: ./src/libraries/Reallocation.sol

65:                if (lower < minLowerTick && minLowerTick < currentTick) {	// @audit-issue

78:                if (upper > maxUpperTick && maxUpperTick >= currentTick) {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L65-L65), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L78-L78), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

97:        if (hasPosition && minMargin < Constants.MIN_MARGIN_AMOUNT) {	// @audit-issue

210:        if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {	// @audit-issue

215:            if (lowerPrice < minSqrtPrice && minSqrtPrice < upperPrice) {	// @audit-issue
```
[97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L97-L97), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L210-L210), [215](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L215-L215), 


```solidity
Path: ./src/libraries/Perp.sol

210:            _sqrtAssetStatus.tickLower + _assetStatusUnderlying.riskParams.rebalanceThreshold < currentTick	// @audit-issue
211:                && currentTick < _sqrtAssetStatus.tickUpper - _assetStatusUnderlying.riskParams.rebalanceThreshold

349:        if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {	// @audit-issue

390:        if (f0 == 0 && f1 == 0) {	// @audit-issue

593:        if (_isWithdraw && _assetStatus.borrowedAmount == 0) {	// @audit-issue
```
[210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L210-L211), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L349-L349), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L390-L390), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L593-L593), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue

231:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L231-L231), 


```solidity
Path: ./src/libraries/SlippageLib.sol

46:            maxAcceptableSqrtPriceRange > 0	// @audit-issue
47:                && (
48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange
49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
50:                )
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L46-L50), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

34:            settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L34-L34), 


#### Recommendation


When multiple conditions need to be checked successively, try to combine them in a single `if` statement using `&&` instead of nesting separate `if` statements. This will leverage short-circuit evaluation for potential gas savings.


### Use `!= 0` Instead of `> 0` for Unsigned Integer Comparison
In Solidity, unsigned integers (e.g., `uint256`, `uint8`, etc.) represent non-negative whole numbers, ranging from 0 to a maximum value determined by their bit size. When performing comparisons on these numbers, especially to check if they are non-zero, developers have options. A common practice is to compare against zero using the `>` operator, as in `value > 0`. However, given the nature of unsigned integers, a more straightforward and slightly gas-efficient comparison is to use the `!=` operator, as in `value != 0`.

The primary rationale is that the `!=` comparison directly checks for non-equality, whereas the `>` comparison checks if one value is strictly greater than another. For unsigned integers, where negative values don't exist, the `!= 0` check is both semantically clearer and potentially more efficient at the EVM bytecode level.


```solidity
Path: ./src/PredyPool.sol

84:        if (amount0 > 0) {	// @audit-issue

182:        require(amount > 0, "AZ");	// @audit-issue

204:        require(amount > 0, "AZ");	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L84-L84), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L204-L204), 


```solidity
Path: ./src/PriceFeed.sol

52:        require(quoteAnswer > 0 && basePrice.price > 0);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L52-L52), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

117:                if (marginAmountUpdate > 0) {	// @audit-issue

178:        if (tradeResult.minMargin > 0) {	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L117-L117), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L178-L178), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

135:        uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;	// @audit-issue
```
[135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L135-L135), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

65:            userPosition.hedgeInterval > 0	// @audit-issue

122:        if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L65-L65), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

74:        uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L74-L74), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

119:            if (marginAmountUpdate > 0) {	// @audit-issue

200:        if (tradeResult.minMargin > 0) {	// @audit-issue
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L119-L119), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L200-L200), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

54:            if (currentPositionAmount > 0) {	// @audit-issue

65:                if (tradeAmount > 0) {	// @audit-issue

97:        } else if (limitPrice > 0 && stopPrice > 0) {	// @audit-issue

127:        if (tradeAmount > 0 && limitPrice < tradePrice) {	// @audit-issue

155:        if (tradeAmount > 0) {	// @audit-issue

200:            if (tradeAmount > 0 && decayedPrice < tradePrice) {	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L54-L54), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L65-L65), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L97-L97), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L127-L127), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L155-L155), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L200-L200), 


```solidity
Path: ./src/libraries/PerpFee.sol

73:        if (sqrtPerp.amount > 0) {	// @audit-issue

101:        if (sqrtPerp.amount > 0) {	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L73-L73), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L101-L101), 


```solidity
Path: ./src/libraries/UniHelper.sol

78:        if (errMsg.length > 0) {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L78-L78), 


```solidity
Path: ./src/libraries/Reallocation.sol

55:        if (availableAmount > 0) {	// @audit-issue

110:        require(tickSpacing > 0);	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L55-L55), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L110-L110), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

210:        if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {	// @audit-issue

245:        if (squartPosition > 0) {	// @audit-issue
```
[210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L210-L210), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L245-L245), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

45:        if (interestRateQuote > 0 || interestRateBase > 0) {	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L45-L45), 


```solidity
Path: ./src/libraries/Perp.sol

165:        if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {	// @audit-issue

443:        if (_tradeSqrtAmount > 0) {	// @audit-issue

551:        if (closeAmount > 0) {	// @audit-issue
```
[165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L165-L165), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L443-L443), [551](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L551-L551), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

55:        require(_supplyTokenAmount > 0, "S3");	// @audit-issue

84:        if (userStatus.positionAmount > 0) {	// @audit-issue

135:        if (_userStatus.positionAmount > 0) {	// @audit-issue

148:        if (_userStatus.positionAmount > 0) {	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L55-L55), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L84-L84), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L135-L135), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L148-L148), 


```solidity
Path: ./src/libraries/SlippageLib.sol

33:        if (tradeResult.averagePrice > 0) {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L33-L33), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

216:        require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L216-L216), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

64:        require(_amount > 0, "AZ");	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

68:                if (exceedsQuote > 0) {	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L68-L68), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

45:        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");	// @audit-issue

92:            if (remainingMargin > 0) {	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L45-L45), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L92-L92), 


```solidity
Path: ./src/libraries/math/Math.sol

27:        } else if (x > 0) {	// @audit-issue

37:        } else if (x > 0) {	// @audit-issue

47:        } else if (x > 0) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L27-L27), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L47-L47), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

83:            baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,	// @audit-issue
```
[83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L83-L83), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

55:        if (settlementParams.fee > 0) {	// @audit-issue

67:        if (baseAmountDelta > 0) {	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L67-L67), 


#### Recommendation

Use '!= 0' instead of '> 0' for comparing unsigned integers in Solidity. This is semantically clearer and slightly more gas-efficient, as it directly checks for non-zero values without considering unnecessary relational comparisons.

### Operator `>=`/`<=` costs less gas than operator `>`/`<`
The compiler uses opcodes `GT` and `ISZERO` for code that uses `>`, but only requires `LT` for `>=`. A similar behaviour applies for `>`, which uses opcodes `LT` and `ISZERO`, but only requires `GT` for `<=`.


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

197:        if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {	// @audit-issue

203:        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);	// @audit-issue
```
[197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L197-L197), [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L203-L203), 


```solidity
Path: ./src/libraries/Perp.sol

611:        if (utilization > 1e18) {	// @audit-issue
```
[611](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L611-L611), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

239:        if (utilization > 1e18) {	// @audit-issue
```
[239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L239-L239), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue
```
[214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

170:        if (ratio > 1e4) {	// @audit-issue
```
[170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L170-L170), 


#### Recommendation

Opt for '>=', '<=' operators over '>' and '<' in Solidity where logically appropriate, as they consume less gas. This is because '>=' and '<=' require only one opcode ('LT' or 'GT'), compared to the two opcodes ('GT'/'LT' and 'ISZERO') needed for '>' and '<'.

### Constructor Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `constructor` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



```solidity
Path: ./src/PredyPool.sol

68:    constructor() {}	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L68-L68), 


```solidity
Path: ./src/PriceFeed.sol

14:    constructor(address pyth) {	// @audit-issue

37:    constructor(address quotePrice, address pyth, bytes32 priceId, uint256 decimalsDiff) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L14-L14), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L37-L37), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

67:    constructor() {}	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L67-L67), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

90:    constructor() {}	// @audit-issue
```
[90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L90-L90), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

16:    constructor(address swapRouterAddress, address quoterAddress) {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L16-L16), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

13:    constructor() {}	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L13-L13), 


```solidity
Path: ./src/base/BaseMarket.sol

19:    constructor(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L19-L19), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

36:    constructor() {}	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L36-L36), 


```solidity
Path: ./src/base/BaseHookCallback.sol

12:    constructor(IPredyPool predyPool) {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L12-L12), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

15:    constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L15-L15), 


#### Recommendation

Mark constructors as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Initializers Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `initializer` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



```solidity
Path: ./src/PredyPool.sol

70:    function initialize(address uniswapFactory) public initializer {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L70-L70), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

69:    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L69-L69), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

92:    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L92-L92), 


#### Recommendation

Mark initializers as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).


```solidity
Path: ./src/PredyPool.sol

28:contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L28-L28), 


```solidity
Path: ./src/PriceFeed.sol

9:contract PriceFeedFactory {	// @audit-issue

29:contract PriceFeed {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L9-L9), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L29-L29), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

40:contract GammaTradeMarketL2 is GammaTradeMarket {	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L40-L40), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

24:contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L24-L24), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

10:contract GammaTradeMarketWrapper is GammaTradeMarketL2 {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L10-L10), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

11:library GammaTradeMarketLib {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L11-L11), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

29:contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L29-L29), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

27:contract PerpMarket is PerpMarketV1 {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L27-L27), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

10:contract UniswapSettlement is ISettlement {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L10-L10), 


```solidity
Path: ./src/vendors/IPyth.sol

4:interface IPyth {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L4-L4), 


```solidity
Path: ./src/vendors/IUniswapV3PoolOracle.sol

4:interface IUniswapV3PoolOracle {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L4-L4), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

4:interface AggregatorV3Interface {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L4-L4), 


```solidity
Path: ./src/libraries/Trade.sol

19:library Trade {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L19-L19), 


```solidity
Path: ./src/libraries/SlippageLib.sol

9:library SlippageLib {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L9-L9), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

13:library ReaderLogic {	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L13-L13), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

16:library AddPairLogic {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L16-L16), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

14:library SupplyLogic {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L14-L14), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

14:library ReallocationLogic {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L14-L14), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

21:library LiquidationLogic {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L21-L21), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

15:library TradeLogic {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L15-L15), 


```solidity
Path: ./src/libraries/math/Bps.sol

4:library Bps {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L4-L4), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

8:abstract contract BaseHookCallbackUpgradable is Initializable, IHooks {	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L8-L8), 


```solidity
Path: ./src/base/BaseMarket.sol

10:abstract contract BaseMarket is IFillerMarket, BaseHookCallback, Owned {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L10-L10), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

11:abstract contract BaseMarketUpgradable is IFillerMarket, BaseHookCallbackUpgradable {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L11-L11), 


```solidity
Path: ./src/base/BaseHookCallback.sol

7:abstract contract BaseHookCallback is IHooks {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L7-L7), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

7:contract SupplyToken is ERC20, ISupplyToken {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L7-L7), 


#### Recommendation

Optimize gas usage by renaming 'public'/'external' functions and 'public' member variables in Solidity. Aim for shorter and more efficient names, especially for frequently called functions. This can save gas during deployment and reduce gas costs per call due to lower method ID sorting positions.

### Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas for changes
Avoids a Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past. Since most of the bools aren't changed twice in one transaction, I've counted the amount of gas as half of the full amount, for each variable. Note that public state variables can be re-written to be `private` and use `uint256`, but have public getters returning `bool`s.

```solidity
Path: ./src/PredyPool.sol

38:    mapping(address => bool) public allowedUniswapPools;	// @audit-issue

40:    mapping(address trader => mapping(uint256 pairId => bool)) public allowedTraders;	// @audit-issue

44:    event ProtocolRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);	// @audit-issue

45:    event CreatorRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L40-L40), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L45-L45), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

10:    bool isEnabled;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L10-L10), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

19:    bool reduceOnly;	// @audit-issue

20:    bool closePosition;	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L20-L20), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

27:    event ScaledAssetPositionUpdated(uint256 indexed pairId, bool isStable, int256 open, int256 close);	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L27-L27), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

19:    event TokenSupplied(address indexed account, uint256 pairId, bool isStable, uint256 suppliedAmount);	// @audit-issue

20:    event TokenWithdrawn(address indexed account, uint256 pairId, bool isStable, uint256 finalWithdrawnAmount);	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L20-L20), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

20:        bool relocationOccurred,	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L20-L20), 


```solidity
Path: ./src/base/BaseMarket.sol

17:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L17-L17), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

29:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L29-L29), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Consider activating `via-ir` for deploying
The IR-based code generator was developed to make code generation more performant by enabling optimization passes that can be applied across functions.

It is possible to activate the IR-based code generator through the command line by using the flag `--via-ir`or by including the option `{"viaIR": true}`.

Keep in mind that compiling with this option may take longer. However, you can simply test it before deploying your code. If you find that it provides better performance, you can add the `--via-ir` flag to your deploy command.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L1), 


#### Recommendation

Consider activating `via-ir`.

### Optimize Deployment Size by Fine-tuning IPFS Hash
The Solidity compiler appends 53 bytes of metadata to the smart contract code, incurring an extra cost of 10,600 gas. This additional expense arises from 200 gas per bytecode, plus calldata cost, which amounts to 16 gas for non-zero bytes and 4 gas for zero bytes. This results in a maximum of 848 extra gas in calldata cost.

Reducing this cost is crucial for the following reasons:

The metadata's 53-byte addition leads to a deployment cost increase of 10,600 gas. It can also result in an additional calldata cost of up to 848 gas. Ways to Minimize Gas Consumption:

Employ the `--no-cbor-metadata` compiler option to exclude metadata. Be cautious as this might impact contract verification. Search for code comments that yield an IPFS hash with more zeros, thereby reducing calldata costs.

```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L1), 


#### Recommendation

To optimize deployment size and reduce associated costs, consider the following strategies:
1. **Exclude Metadata with Compiler Option**: Use the `solc` compiler’s `--metadata-hash none` or `--no-cbor-metadata` option to prevent the inclusion of metadata in the compiled bytecode. This action reduces the bytecode size, thus lowering deployment gas costs. However, exercise caution with this approach, as it might affect the ability to verify the contract on platforms like Etherscan.

2. **Optimize IPFS Hash for More Zeros**: If excluding metadata is not desirable, another approach involves optimizing code comments or elements that influence the metadata hash generation to achieve an IPFS hash with a higher proportion of zeros. Since calldata costs are lower for zero bytes, a metadata hash with more zeros can reduce the calldata costs associated with contract interactions.

Example for excluding metadata:
```bash
solc --metadata-hash none YourContract.sol
```


### Assembly: Use scratch space for building calldata
If an external call's calldata can fit into two or fewer words, use the scratch space to build the calldata, rather than allowing Solidity to do a memory expansion.

```solidity
Path: ./src/PredyPool.sol

72:        AddPairLogic.initializeGlobalData(globalData, uniswapFactory);	// @audit-issue

85:            ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);	// @audit-issue

88:            ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);	// @audit-issue

123:        AddPairLogic.updateAssetRiskParams(globalData.pairs[pairId], riskParams);	// @audit-issue

148:        AddPairLogic.updateFeeRatio(globalData.pairs[pairId], feeRatio);	// @audit-issue

158:        AddPairLogic.updatePoolOwner(globalData.pairs[pairId], poolOwner);	// @audit-issue

168:        AddPairLogic.updatePriceOracle(globalData.pairs[pairId], priceFeed);	// @audit-issue

187:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue

209:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue

270:        globalData.validate(tradeParams.pairId);	// @audit-issue

276:        tradeParams.vaultId = globalData.createOrGetVault(tradeParams.vaultId, tradeParams.pairId);	// @audit-issue

338:        globalData.validate(pairId);	// @audit-issue

340:        return globalData.createOrGetVault(0, pairId);	// @audit-issue

345:        return UniHelper.convertSqrtPrice(	// @audit-issue

346:            UniHelper.getSqrtPrice(globalData.pairs[pairId].sqrtAssetStatus.uniswapPool),	// @audit-issue

353:        return PositionCalculator.getSqrtIndexPrice(globalData.pairs[pairId]);	// @audit-issue

358:        globalData.validate(pairId);	// @audit-issue

371:        ReaderLogic.getPairStatus(globalData, pairId);	// @audit-issue

377:        ReaderLogic.getVaultStatus(globalData, vaultId);	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L72-L72), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L85-L85), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L88-L88), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L123-L123), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L148-L148), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L158-L158), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L168-L168), [187](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L187-L187), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L209-L209), [270](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L270-L270), [276](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L276-L276), [338](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L338-L338), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L340-L340), [345](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L345-L345), [346](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L346-L346), [353](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L353-L353), [358](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L358-L358), [371](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L371-L371), [377](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L377-L377), 


```solidity
Path: ./src/PriceFeed.sol

46:        (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();	// @audit-issue

48:        IPyth.Price memory basePrice = IPyth(_pyth).getPriceNoOlderThan(_priceId, VALID_TIME_PERIOD);	// @audit-issue

57:        sqrtPrice = FixedPointMathLib.sqrt(price);	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L48-L48), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L57-L57), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

51:            L2GammaDecoder.decodeGammaParam(order.param);	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L51-L51), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

94:                DataType.Vault memory vault = _predyPool.getVault(tradeParams.vaultId);	// @audit-issue

118:                    quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue

156:        ResolvedOrder memory resolvedOrder = GammaOrderLib.resolve(gammaOrder, sig);	// @audit-issue

163:        tradeResult = _predyPool.trade(	// @audit-issue

200:            _predyPool.updateRecepient(tradeResult.vaultId, gammaOrder.info.trader);	// @audit-issue

216:        ResolvedOrder memory resolvedOrder = GammaOrderLib.resolve(gammaOrder, sig);	// @audit-issue

237:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);	// @audit-issue

241:            GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);	// @audit-issue

250:        DataType.Vault memory vault = _predyPool.getVault(userPosition.vaultId);	// @audit-issue

269:        tradeResult = _predyPool.trade(tradeParams, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue

286:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);	// @audit-issue

289:            GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);	// @audit-issue

296:        DataType.Vault memory vault = _predyPool.getVault(userPosition.vaultId);	// @audit-issue

310:        tradeResult = _predyPool.trade(tradeParams, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue

317:        _predyPool.trade(	// @audit-issue

340:        DataType.Vault memory vault = _predyPool.getVault(userPosition.vaultId);	// @audit-issue

346:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);	// @audit-issue

348:        (hedgeRequired,,) = GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);	// @audit-issue

350:        (closeRequired,,) = GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);	// @audit-issue

384:            userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId)	// @audit-issue

389:        positionIDs[trader].addItem(newPositionId);	// @audit-issue

393:        DataType.Vault memory vault = _predyPool.getVault(userPositions[positionId].vaultId);	// @audit-issue

405:        positionIDs[trader].removeItem(positionId);	// @audit-issue

439:        if (GammaTradeMarketLib.saveUserPosition(userPosition, modifyInfo)) {	// @audit-issue

445:        order.validate();	// @audit-issue

448:            order.toPermit(),	// @audit-issue

449:            order.transferDetails(address(this)),	// @audit-issue
```
[94](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L94-L94), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L118-L118), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L156-L156), [163](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L163-L163), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L200-L200), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L216-L216), [237](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L237-L237), [241](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L241-L241), [250](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L250-L250), [269](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L269-L269), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L286-L286), [289](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L289-L289), [296](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L296-L296), [310](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L310-L310), [317](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L317-L317), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L340-L340), [346](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L346-L346), [348](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L348-L348), [350](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L350-L350), [384](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L384-L384), [389](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L389-L389), [393](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L393-L393), [405](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L405-L405), [439](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L439-L439), [445](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L445-L445), [448](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L448-L448), [449](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L449-L449), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

6:        items.push(item);	// @audit-issue

17:        items.pop();	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L6-L6), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L17-L17), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

119:                order.info.hash(),	// @audit-issue

129:                GammaModifyInfoLib.hash(order.modifyInfo)	// @audit-issue
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L119-L119), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L129-L129), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

58:                order.info.hash(),	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L58-L58), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

126:                quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue

165:        ResolvedOrder memory resolvedOrder = PerpOrderV3Lib.resolve(perpOrder, sig);	// @audit-issue

174:            _predyPool.getVault(userPosition.vaultId).openPosition.perp.amount,	// @audit-issue

185:        tradeResult = _predyPool.trade(	// @audit-issue

210:            _predyPool.updateRecepient(tradeResult.vaultId, perpOrder.info.trader);	// @audit-issue

225:        DataType.Vault memory vault = _predyPool.getVault(vaultId);	// @audit-issue

227:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(pairId);	// @audit-issue

229:        uint256 price = Math.calSqrtPriceToPrice(sqrtPrice);	// @audit-issue

233:        return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);	// @audit-issue

239:        return Math.abs(positionAmount) * price / Constants.Q96;	// @audit-issue

243:        return PositionCalculator.calculateValue(sqrtPrice, PositionCalculator.getPosition(vault.openPosition))	// @audit-issue

262:        return (userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId));	// @audit-issue

283:            _predyPool.getVault(userPosition.vaultId).openPosition.perp.amount,	// @audit-issue

293:        _predyPool.trade(	// @audit-issue

305:                        PerpOrderV3Lib.resolve(perpOrder, bytes("")),	// @audit-issue

315:        order.validate();	// @audit-issue

318:            order.toPermit(),	// @audit-issue

319:            ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),	// @audit-issue

328:        order.validate();	// @audit-issue

331:            order.toPermit(),	// @audit-issue

332:            ISignatureTransfer.SignatureTransferDetails({to: address(this), requestedAmount: amount}),	// @audit-issue
```
[126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L126-L126), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L165-L165), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L174-L174), [185](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L185-L185), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L210-L210), [225](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L225-L225), [227](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L227-L227), [229](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L229-L229), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L239-L239), [243](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L243-L243), [262](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L262-L262), [283](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L283-L283), [293](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L293-L293), [305](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L305-L305), [315](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L315-L315), [318](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L318-L318), [319](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L319-L319), [328](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L328-L328), [331](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L331-L331), [332](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L332-L332), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

62:                order.info.hash(),	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L62-L62), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

35:            L2Decoder.decodePerpOrderV3Params(compressedOrder.data1);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L35-L35), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

87:        uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)	// @audit-issue

88:            * Constants.Q96 / Math.abs(tradeAmount);	// @audit-issue

90:        uint256 oraclePrice = Math.calSqrtPriceToPrice(tradeResult.sqrtTwap);	// @audit-issue

161:            if (tradePrice > Bps.upper(oraclePrice, decayedSlippageTorelance)) {	// @audit-issue

170:            if (tradePrice < Bps.lower(oraclePrice, decayedSlippageTorelance)) {	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L87-L87), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L88-L88), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L90-L90), [161](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L161-L161), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L170-L170), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

33:        amountOut = _swapRouter.exactInput(	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue

49:        amountIn = _swapRouter.exactOutput(	// @audit-issue

54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);	// @audit-issue

59:        (amountOut,,,) = _quoterV2.quoteExactInput(data, amountIn);	// @audit-issue

63:        (amountIn,,,) = _quoterV2.quoteExactOutput(data, amountOut);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L33-L33), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L49-L49), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L54-L54), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L59-L59), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L63-L63), 


```solidity
Path: ./src/libraries/PerpFee.sol

21:        int256 FeeAmountUnderlying = assetStatus.basePool.tokenStatus.computeUserFee(userStatus.basePosition);	// @audit-issue

22:        int256 FeeAmountStable = assetStatus.quotePool.tokenStatus.computeUserFee(userStatus.stablePosition);	// @audit-issue

38:        return DataType.FeeAmount(FeeAmountUnderlying, FeeAmountStable);	// @audit-issue

47:        int256 totalFeeUnderlying = assetStatus.basePool.tokenStatus.settleUserFee(userStatus.basePosition);	// @audit-issue

48:        int256 totalFeeStable = assetStatus.quotePool.tokenStatus.settleUserFee(userStatus.stablePosition);	// @audit-issue

60:        return DataType.FeeAmount(totalFeeUnderlying, totalFeeStable);	// @audit-issue

118:            uint256 rebalanceId = PairLib.getRebalanceCacheId(pairId, userStatus.lastNumRebalance);	// @audit-issue

120:            uint256 rebalanceAmount = Math.abs(userStatus.sqrtPerp.amount);	// @audit-issue

146:            uint256 rebalanceAmount = Math.abs(userStatus.sqrtPerp.amount);	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L21-L21), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L22-L22), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L38-L38), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L47-L47), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L48-L48), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L60-L60), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L118-L118), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L120-L120), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L146-L146), 


```solidity
Path: ./src/libraries/UniHelper.sol

14:        (sqrtPrice,,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();	// @audit-issue

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue

49:            (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();	// @audit-issue

51:            (uint32 oldestAvailableAge,,, bool initialized) = uniswapPool.observations((index + 1) % cardinality);	// @audit-issue

54:                (oldestAvailableAge,,,) = uniswapPool.observations(0);	// @audit-issue

60:            (success, data) = address(uniswapPool).staticcall(	// @audit-issue

72:        uint160 sqrtPriceX96 = TickMath.getSqrtRatioAtTick(tick);	// @audit-issue

96:            IUniswapV3Pool(uniswapPoolAddress).positions(positionKey);	// @audit-issue

104:        (, int24 tickCurrent,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();	// @audit-issue

106:        uint256 feeGrowthGlobal0X128 = IUniswapV3Pool(uniswapPoolAddress).feeGrowthGlobal0X128();	// @audit-issue

107:        uint256 feeGrowthGlobal1X128 = IUniswapV3Pool(uniswapPoolAddress).feeGrowthGlobal1X128();	// @audit-issue

116:                    IUniswapV3Pool(uniswapPoolAddress).ticks(tickLower);	// @audit-issue

133:                    IUniswapV3Pool(uniswapPoolAddress).ticks(tickUpper);	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L14-L14), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L51-L51), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L54-L54), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L60-L60), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L72-L72), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L96-L96), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L106-L106), [107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L107-L107), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L116-L116), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L133-L133), 


```solidity
Path: ./src/libraries/Reallocation.sol

23:        int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();	// @audit-issue

60:                    ScaledAsset.getAvailableCollateralValue(_token1Status),	// @audit-issue

73:                    ScaledAsset.getAvailableCollateralValue(_token0Status),	// @audit-issue

93:        (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue

133:            calculateAmount1ForLiquidity(TickMath.getSqrtRatioAtTick(currentLowerTick), available, liquidityAmount);	// @audit-issue

135:        minLowerTick = TickMath.getTickAtSqrtRatio(sqrtPrice);	// @audit-issue

154:            calculateAmount0ForLiquidity(TickMath.getSqrtRatioAtTick(currentUpperTick), available, liquidityAmount);	// @audit-issue

156:        maxUpperTick = TickMath.getTickAtSqrtRatio(sqrtPrice);	// @audit-issue

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L23-L23), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L60-L60), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L73-L73), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L93-L93), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L133-L133), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L135-L135), [154](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L154-L154), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L156-L156), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

93:            (calculateRequiredCollateralWithDebt(pairStatus.riskParams.debtRiskRatio) * debtValue).toInt256() / 1e6;	// @audit-issue

143:            return PriceFeed(pairStatus.priceFeed).getSqrtPrice();	// @audit-issue

145:            return UniHelper.convertSqrtPrice(	// @audit-issue

146:                UniHelper.getSqrtTWAP(pairStatus.sqrtAssetStatus.uniswapPool), pairStatus.isQuoteZero	// @audit-issue

232:        uint256 price = Math.calSqrtPriceToPrice(_sqrtPrice);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L93-L93), [143](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L143-L143), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L145-L145), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L146-L146), [232](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L232-L232), 


```solidity
Path: ./src/libraries/Trade.sol

68:            Perp.UpdatePerpParams(tradeParams.tradeAmount, swapResult.amountPerp),	// @audit-issue

69:            Perp.UpdateSqrtPerpParams(tradeParams.tradeAmountSqrt, swapResult.amountSqrtPerp + stableAmountForSqrt)	// @audit-issue

87:            int256 amountQuote = calculateStableAmount(sqrtPrice, 1e18).toInt256();	// @audit-issue

92:        globalData.initializeLock(pairId);	// @audit-issue

94:        globalData.callSettlementCallback(settlementData, totalBaseAmount);	// @audit-issue

96:        (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue

99:            revert IPredyPool.BaseTokenNotSettled();	// @audit-issue

104:            revert IPredyPool.QuoteTokenNotSettled();	// @audit-issue

113:        return UniHelper.convertSqrtPrice(UniHelper.getSqrtPrice(uniswapPoolAddress), isQuoteZero);	// @audit-issue

132:        swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();	// @audit-issue

142:        Perp.settleUserBalance(_pairStatus, _userStatus);	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L68-L68), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L69-L69), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L87-L87), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L92-L92), [94](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L94-L94), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L96-L96), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L99-L99), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L104-L104), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L113-L113), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L132-L132), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L142-L142), 


```solidity
Path: ./src/libraries/VaultLib.sol

20:            revert IPredyPool.CallerIsNotVaultOwner();	// @audit-issue

52:                revert IPredyPool.CallerIsNotVaultOwner();	// @audit-issue

56:                revert IPredyPool.VaultAlreadyHasAnotherMarginId();	// @audit-issue

60:                revert IPredyPool.VaultAlreadyHasAnotherPair();	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L20-L20), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L52-L52), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L56-L56), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L60-L60), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

29:        Perp.updateFeeAndPremiumGrowth(pairId, pairStatus.sqrtAssetStatus);	// @audit-issue

58:        uint256 utilizationRatio = poolStatus.tokenStatus.getUtilizationRatio();	// @audit-issue

66:        interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)	// @audit-issue

69:        totalProtocolFee = poolStatus.tokenStatus.updateScaler(interestRate, fee);	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L29-L29), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L58-L58), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L66-L66), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L69-L69), 


```solidity
Path: ./src/libraries/Perp.sol

138:            ScaledAsset.createUserStatus(),	// @audit-issue

139:            ScaledAsset.createUserStatus(),	// @audit-issue

153:            ScaledAsset.createUserStatus(),	// @audit-issue

154:            ScaledAsset.createUserStatus()	// @audit-issue

166:            _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(	// @audit-issue

170:            _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(	// @audit-issue

206:        (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();	// @audit-issue

225:                Reallocation.getNewRange(_assetStatusUnderlying, currentTick);	// @audit-issue

277:            Reallocation.getNewRange(_assetStatusUnderlying, _currentTick);	// @audit-issue

311:        uint160 tickSqrtPrice = TickMath.getSqrtRatioAtTick(_tick);	// @audit-issue

340:        (uint128 liquidity,,,,) = IUniswapV3Pool(_uniswapPool).positions(positionKey);	// @audit-issue

396:        uint256 spreadParam = PremiumCurveModel.calculatePremiumCurve(utilization);	// @audit-issue

436:        if (!Reallocation.isInRange(_sqrtAssetStatus)) {	// @audit-issue

538:            if (_userStatus.sqrtPerp.amount.abs() >= _amount.abs()) {	// @audit-issue

590:        uint256 buffer = Math.max(_assetStatus.totalAmount / 50, Constants.MIN_LIQUIDITY);	// @audit-issue

645:            _userStatus.sqrtPerp.amount.abs(),	// @audit-issue

652:            _userStatus.sqrtPerp.amount.abs(),	// @audit-issue

686:            if (_positionAmount.abs() >= _tradeAmount.abs()) {	// @audit-issue

712:            address(this), _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128(), ""	// @audit-issue

715:        requiredAmount0 = -SafeCast.toInt256(amount0);	// @audit-issue

716:        requiredAmount1 = -SafeCast.toInt256(amount1);	// @audit-issue

728:            _assetStatus.tickLower, _assetStatus.tickUpper, _liquidityAmount.safeCastTo128()	// @audit-issue

736:        receivedAmount0 = SafeCast.toInt256(amount0);	// @audit-issue

737:        receivedAmount1 = SafeCast.toInt256(amount1);	// @audit-issue

758:            if (_userStatus.sqrtPerp.amount.abs() >= _tradeSqrtAmount.abs()) {	// @audit-issue

768:            offsetUnderlying = LPMath.calculateAmount0OffsetWithTick(_tickUpper, openAmount.abs(), openAmount < 0);	// @audit-issue

771:            offsetStable = LPMath.calculateAmount1OffsetWithTick(_tickLower, openAmount.abs(), openAmount < 0);	// @audit-issue
```
[138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L138-L138), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L139-L139), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L153-L153), [154](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L154-L154), [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L166-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L170-L170), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L206-L206), [225](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L225-L225), [277](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L277-L277), [311](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L311-L311), [340](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L340-L340), [396](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L396-L396), [436](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L436-L436), [538](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L538-L538), [590](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L590-L590), [645](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L645-L645), [652](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L652-L652), [686](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L686-L686), [712](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L712-L712), [715](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L715-L715), [716](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L716-L716), [728](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L728-L728), [736](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L736-L736), [737](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L737-L737), [758](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L758-L758), [768](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L768-L768), [771](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L771-L771), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

96:            if (userStatus.positionAmount.abs() >= _amount.abs()) {	// @audit-issue

136:            interestFee = (getAssetFee(_assetStatus, _userStatus)).toInt256();	// @audit-issue

138:            interestFee = -(getDebtFee(_assetStatus, _userStatus)).toInt256();	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L96-L96), [136](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L136-L136), [138](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L138-L138), 


```solidity
Path: ./src/libraries/SlippageLib.sol

27:        uint256 basePrice = Math.calSqrtPriceToPrice(sqrtBasePrice);	// @audit-issue

35:            if (basePrice.lower(slippageTolerance) > uint256(tradeResult.averagePrice)) {	// @audit-issue

40:            if (basePrice.upper(slippageTolerance) < uint256(-tradeResult.averagePrice)) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L35-L35), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L40-L40), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

16:            permitted: ISignatureTransfer.TokenPermissions({token: order.token, amount: order.amount}),	// @audit-issue

28:        return ISignatureTransfer.SignatureTransferDetails({to: to, requestedAmount: order.amount});	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L16-L16), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L28-L28), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

17:        ApplyInterestLib.applyInterestForToken(globalData.pairs, pairId);	// @audit-issue

28:        ApplyInterestLib.applyInterestForToken(globalData.pairs, vault.openPosition.pairId);	// @audit-issue

30:        Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);	// @audit-issue

49:            PositionCalculator.getPositionWithFeeAmount(vault.openPosition, feeAmount);	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L17-L17), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L30-L30), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L49-L49), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

70:            uniswapV3Factory.getPool(uniswapPool.token0(), uniswapPool.token1(), uniswapPool.fee())	// @audit-issue

76:        require(uniswapPool.token0() == stableTokenAddress || uniswapPool.token1() == stableTokenAddress, "C3");	// @audit-issue

78:        bool isQuoteZero = uniswapPool.token0() == stableTokenAddress;	// @audit-issue

84:            isQuoteZero ? uniswapPool.token1() : uniswapPool.token0(),	// @audit-issue

162:                ScaledAsset.createAssetStatus(),	// @audit-issue

170:                ScaledAsset.createAssetStatus(),	// @audit-issue

198:                string.concat("Predy6-Supply-", erc20.name()),	// @audit-issue

199:                string.concat("p", erc20.symbol()),	// @audit-issue

200:                erc20.decimals()	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L70-L70), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L78-L78), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L84-L84), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L162-L162), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L170-L170), [198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L198-L198), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L199-L199), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L200-L200), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

27:        globalData.validate(_pairId);	// @audit-issue

30:            revert IPredyPool.InvalidAmount();	// @audit-issue

33:        ApplyInterestLib.applyInterestForToken(globalData.pairs, _pairId);	// @audit-issue

50:        mintAmount = _pool.tokenStatus.addAsset(_amount);	// @audit-issue

54:        ISupplyToken(_pool.supplyTokenAddress).mint(msg.sender, mintAmount);	// @audit-issue

62:        globalData.validate(_pairId);	// @audit-issue

66:        ApplyInterestLib.applyInterestForToken(globalData.pairs, _pairId);	// @audit-issue

86:            _pool.tokenStatus.removeAsset(ERC20(supplyTokenAddress).balanceOf(msg.sender), _amount);	// @audit-issue

88:        ISupplyToken(supplyTokenAddress).burn(msg.sender, finalburntAmount);	// @audit-issue

90:        ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L27-L27), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L33-L33), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L50-L50), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L54-L54), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L62-L62), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L66-L66), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L88-L88), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L90-L90), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

32:        globalData.validate(pairId);	// @audit-issue

35:        ApplyInterestLib.applyInterestForToken(globalData.pairs, pairId);	// @audit-issue

40:        Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);	// @audit-issue

49:                Perp.reallocate(pairStatus, pairStatus.sqrtAssetStatus);	// @audit-issue

52:                globalData.initializeLock(pairId);	// @audit-issue

54:                globalData.callSettlementCallback(settlementData, deltaPositionBase);	// @audit-issue

56:                (int256 settledQuoteAmount, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue

61:                    revert IPredyPool.QuoteTokenNotSettled();	// @audit-issue

65:                    revert IPredyPool.BaseTokenNotSettled();	// @audit-issue

69:                    ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));	// @audit-issue

84:            globalData.rebalanceFeeGrowthCache[PairLib.getRebalanceCacheId(	// @audit-issue

86:            )] = DataType.RebalanceFeeGrowthCache(	// @audit-issue

91:            Perp.finalizeReallocation(pairStatus.sqrtAssetStatus);	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L32-L32), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L35-L35), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L40-L40), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L49-L49), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L52-L52), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L54-L54), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L56-L56), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L61-L61), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L65-L65), [69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L69-L69), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L86-L86), [91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L91-L91), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

50:        ApplyInterestLib.applyInterestForToken(globalData.pairs, vault.openPosition.pairId);	// @audit-issue

53:        Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);	// @audit-issue

76:            PositionCalculator.calculateMinMargin(pairStatus, vault, DataType.FeeAmount(0, 0));	// @audit-issue

99:                    ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);	// @audit-issue

145:            revert IPredyPool.VaultIsNotDanger(vaultValue, minMargin);	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L50-L50), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L53-L53), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L76-L76), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L99-L99), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L145-L145), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

37:        ApplyInterestLib.applyInterestForToken(globalData.pairs, tradeParams.pairId);	// @audit-issue

40:        Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);	// @audit-issue

48:            pairStatus, globalData.vaults[tradeParams.vaultId], DataType.FeeAmount(0, 0)	// @audit-issue

56:            PositionCalculator.checkSafe(pairStatus, globalData.vaults[tradeParams.vaultId], DataType.FeeAmount(0, 0));	// @audit-issue

73:        globalData.initializeLock(tradeParams.pairId);	// @audit-issue

75:        IHooks(msg.sender).predyTradeAfterCallback(tradeParams, tradeResult);	// @audit-issue

77:        (int256 marginAmountUpdate, int256 settledBaseAmount) = globalData.finalizeLock();	// @audit-issue

80:            revert IPredyPool.BaseTokenNotSettled();	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L37-L37), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L40-L40), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L48-L48), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L56-L56), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L73-L73), [75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L75-L75), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L77-L77), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L80-L80), 


```solidity
Path: ./src/libraries/math/LPMath.sol

16:            TickMath.getSqrtRatioAtTick(tickA), TickMath.getSqrtRatioAtTick(tickB), liquidityAmount, isRoundUp	// @audit-issue

26:            TickMath.getSqrtRatioAtTick(tickA), TickMath.getSqrtRatioAtTick(tickB), liquidityAmount, isRoundUp	// @audit-issue

53:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue

58:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue

90:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue

95:            r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);	// @audit-issue

113:        return SafeCast.toInt256(calculateAmount0Offset(TickMath.getSqrtRatioAtTick(upper), liquidityAmount, isRoundUp));	// @audit-issue

139:        return SafeCast.toInt256(calculateAmount1Offset(TickMath.getSqrtRatioAtTick(lower), liquidityAmount, isRoundUp));	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L16-L16), [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L26-L26), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L53-L53), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L58-L58), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L90-L90), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L95-L95), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L113-L113), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L139-L139), 


```solidity
Path: ./src/libraries/math/Math.sol

28:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

30:            return -FullMath.mulDiv(uint256(-x), y, z).toInt256();	// @audit-issue

38:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

40:            return -FullMath.mulDivRoundingUp(uint256(-x), y, z).toInt256();	// @audit-issue

48:            return FixedPointMathLib.mulDivDown(uint256(x), y, z).toInt256();	// @audit-issue

50:            return -FixedPointMathLib.mulDivUp(uint256(-x), y, z).toInt256();	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L30-L30), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L40-L40), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L48-L48), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L50-L50), 


```solidity
Path: ./src/base/BaseMarket.sol

35:            SettlementCallbackLib.decodeParams(settlementData);	// @audit-issue

36:        SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);	// @audit-issue

44:        return _predyPool.reallocate(pairId, _getSettlementData(settlementParams));	// @audit-issue

109:        DataType.PairStatus memory pairStatus = _predyPool.getPairStatus(pairId);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L35-L35), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L36-L36), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L44-L44), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L109-L109), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

57:        SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);	// @audit-issue

68:        uint256 tradeAmountAbs = Math.abs(baseAmountDelta);	// @audit-issue

93:        return _predyPool.reallocate(pairId, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue

157:        DataType.PairStatus memory pairStatus = _predyPool.getPairStatus(pairId);	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L57-L57), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L68-L68), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L93-L93), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L157-L157), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

36:            revert IFillerMarket.SettlementContractIsNotWhitelisted();	// @audit-issue

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

123:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);	// @audit-issue

130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);	// @audit-issue

133:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue

170:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);	// @audit-issue

175:                ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);	// @audit-issue

180:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L36-L36), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L123-L123), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L133-L133), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L175-L175), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L180-L180), 


```solidity
Path: ./src/types/GlobalData.sol

27:        if (vaultId <= 0 || globalData.vaultCount <= vaultId) revert IPredyPool.InvalidPairId();	// @audit-issue

31:        if (pairId <= 0 || globalData.pairsCount <= pairId) revert IPredyPool.InvalidPairId();	// @audit-issue

37:            revert IPredyPool.LockedBy(globalData.lockData.locker);	// @audit-issue

40:        globalData.lockData.quoteReserve = ERC20(globalData.pairs[pairId].quotePool.token).balanceOf(address(this));	// @audit-issue

41:        globalData.lockData.baseReserve = ERC20(globalData.pairs[pairId].basePool.token).balanceOf(address(this));	// @audit-issue

85:        ERC20(currency).safeTransfer(to, amount);	// @audit-issue

103:        uint256 reserveAfter = ERC20(currency).balanceOf(address(this));	// @audit-issue

111:        paid = reserveAfter.toInt256() - reservesBefore.toInt256();	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L27-L27), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L31-L31), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L37-L37), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L40-L40), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L41-L41), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L85-L85), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L103-L103), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L111-L111), 


#### Recommendation

Review your smart contracts to identify and remove `block.number` and `block.timestamp` from the parameters of emitted events. Instead of manually adding these fields, rely on the Ethereum blockchain's inherent inclusion of this information within the block context. Simplify your event definitions to include only the essential data specific to the event's purpose, excluding universally available block metadata.

### Use assembly to check for `address(0)`
In Solidity, it's a common practice to check whether an Ethereum address variable is set to the zero address (`address(0)`) to handle various scenarios, such as token transfers or contract interactions. Typically, this check is performed using a conditional statement like `if (addressVariable == address(0))`.

However, using this approach in high-frequency or gas-sensitive operations can lead to unnecessary gas costs. A more gas-efficient alternative is to use inline assembly to perform the zero address check, which can significantly reduce gas consumption, especially in loops or complex contract logic.

By utilizing inline assembly for this specific check, you can optimize gas usage and make your Solidity code more efficient.

```solidity
Path: ./src/PredyPool.sol

98:        require(newOperator != address(0));	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L98-L98), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

142:        if (pairStatus.priceFeed != address(0)) {	// @audit-issue
```
[142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L142-L142), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

210:        require(_poolOwner != address(0), "ADDZ");	// @audit-issue
```
[210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L210-L210), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

93:                if (vault.recipient != address(0)) {	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L93-L93), 


```solidity
Path: ./src/base/BaseMarket.sol

98:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue

105:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L98-L98), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L105-L105), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

146:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue

153:        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);	// @audit-issue
```
[146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L146-L146), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L153-L153), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

34:            settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]	// @audit-issue

99:        if (settlementParams.contractAddress == address(0)) {	// @audit-issue

146:        if (settlementParams.contractAddress == address(0)) {	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L34-L34), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L99-L99), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L146-L146), 


```solidity
Path: ./src/types/GlobalData.sol

36:        if (globalData.lockData.locker != address(0)) {	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L36-L36), 


#### Recommendation

To optimize gas usage in your Solidity code, consider using inline assembly for checking `address(0)`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to check for `0`
Using assembly to check for zero can save gas by allowing more direct access to the evm and reducing some of the overhead associated with high-level operations in solidity.

```solidity
Path: ./src/PredyPool.sol

84:        if (amount0 > 0) {	// @audit-issue

87:        if (amount1 > 0) {	// @audit-issue

182:        require(amount > 0, "AZ");	// @audit-issue

186:        if (amount > 0) {	// @audit-issue

204:        require(amount > 0, "AZ");	// @audit-issue

208:        if (amount > 0) {	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L87-L87), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L186-L186), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L204-L204), [208](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L208-L208), 


```solidity
Path: ./src/PriceFeed.sol

52:        require(quoteAnswer > 0 && basePrice.price > 0);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L52-L52), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

89:            if (tradeResult.minMargin == 0) {	// @audit-issue

90:                if (callbackData.marginAmountUpdate != 0) {	// @audit-issue

117:                if (marginAmountUpdate > 0) {	// @audit-issue

119:                } else if (marginAmountUpdate < 0) {	// @audit-issue

178:        if (tradeResult.minMargin > 0) {	// @audit-issue

186:            tradeResult.vaultId, gammaOrder.positionId == 0, gammaOrder.info.trader, gammaOrder.pairId	// @audit-issue

197:        if (gammaOrder.positionId == 0) {	// @audit-issue

212:        if (gammaOrder.quantity != 0 || gammaOrder.quantitySqrt != 0 || gammaOrder.marginAmount != 0) {	// @audit-issue

233:        if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {	// @audit-issue

256:        if (delta == 0) {	// @audit-issue

281:        if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {	// @audit-issue

306:        if (tradeParams.tradeAmount == 0 && tradeParams.tradeAmountSqrt == 0) {	// @audit-issue

342:        if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {	// @audit-issue

378:        if (userPosition.vaultId == 0) {	// @audit-issue

395:        if (vault.margin != 0 || vault.openPosition.perp.amount != 0 || vault.openPosition.sqrtPerp.amount != 0) {	// @audit-issue

421:        if (positionId == 0 || userPosition.vaultId == 0) {	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L89-L89), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L90-L90), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L117-L117), [119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L119-L119), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L178-L178), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L186-L186), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L197-L197), [212](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L212-L212), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L233-L233), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L256-L256), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L281-L281), [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L342-L342), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L378-L378), [395](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L395-L395), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L421-L421), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

135:        uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;	// @audit-issue
```
[135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L135-L135), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

65:            userPosition.hedgeInterval > 0	// @audit-issue

80:        if (userPosition.sqrtPriceTrigger == 0) {	// @audit-issue

122:        if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {	// @audit-issue

130:        if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {	// @audit-issue

197:        if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L65-L65), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L80-L80), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L122-L122), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L130-L130), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L197-L197), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

74:        uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L74-L74), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

119:            if (marginAmountUpdate > 0) {	// @audit-issue

125:            if (marginAmountUpdate > 0) {	// @audit-issue

127:            } else if (marginAmountUpdate < 0) {	// @audit-issue

181:        if (tradeAmount == 0) {	// @audit-issue

200:        if (tradeResult.minMargin > 0) {	// @audit-issue

207:        if (userPosition.vaultId == 0) {	// @audit-issue

257:        if (userPosition.vaultId == 0) {	// @audit-issue

290:        if (tradeAmount == 0) {	// @audit-issue
```
[119](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L119-L119), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L127-L127), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L181-L181), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L200-L200), [207](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L207-L207), [257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L257-L257), [290](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L290-L290), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

38:            if (isLong && currentPositionAmount >= 0) {	// @audit-issue

42:            if (!isLong && currentPositionAmount <= 0) {	// @audit-issue

50:            if (currentPositionAmount == 0) {	// @audit-issue

54:            if (currentPositionAmount > 0) {	// @audit-issue

55:                if (tradeAmount < 0) {	// @audit-issue

65:                if (tradeAmount > 0) {	// @audit-issue

92:        if (limitPrice == 0 && stopPrice == 0) {	// @audit-issue

97:        } else if (limitPrice > 0 && stopPrice > 0) {	// @audit-issue

105:        } else if (limitPrice > 0) {	// @audit-issue

110:        } else if (stopPrice > 0) {	// @audit-issue

123:        if (tradeAmount == 0) {	// @audit-issue

127:        if (tradeAmount > 0 && limitPrice < tradePrice) {	// @audit-issue

131:        if (tradeAmount < 0 && limitPrice > tradePrice) {	// @audit-issue

155:        if (tradeAmount > 0) {	// @audit-issue

164:        } else if (tradeAmount < 0) {	// @audit-issue

199:        if (tradeAmount != 0) {	// @audit-issue

200:            if (tradeAmount > 0 && decayedPrice < tradePrice) {	// @audit-issue

204:            if (tradeAmount < 0 && decayedPrice > tradePrice) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L38-L38), [42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L42-L42), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L50-L50), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L55-L55), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L65-L65), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L97-L97), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L105-L105), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L110-L110), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L123-L123), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L127-L127), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L131-L131), [155](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L155-L155), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L164-L164), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L199-L199), [200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L200-L200), [204](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L204-L204), 


```solidity
Path: ./src/libraries/PerpFee.sol

73:        if (sqrtPerp.amount > 0) {	// @audit-issue

76:        } else if (sqrtPerp.amount < 0) {	// @audit-issue

101:        if (sqrtPerp.amount > 0) {	// @audit-issue

104:        } else if (sqrtPerp.amount < 0) {	// @audit-issue

117:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue

142:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L73-L73), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L76-L76), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L101-L101), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L104-L104), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L142-L142), 


```solidity
Path: ./src/libraries/UniHelper.sol

78:        if (errMsg.length > 0) {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L78-L78), 


```solidity
Path: ./src/libraries/Reallocation.sol

55:        if (availableAmount > 0) {	// @audit-issue

110:        require(tickSpacing > 0);	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L55-L55), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L110-L110), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

43:        bool isSafe = vaultValue >= minMargin && _vault.margin >= 0;	// @audit-issue

72:        isSafe = vaultValue >= minMargin && _vault.margin >= 0;	// @audit-issue

103:        if (debtRiskRatio == 0) {	// @audit-issue

176:        return _positionParams.amountSqrt != 0 || _positionParams.amountBase != 0;	// @audit-issue

210:        if (_positionParams.amountSqrt < 0 && _positionParams.amountBase > 0) {	// @audit-issue

245:        if (squartPosition > 0) {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L43-L43), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L72-L72), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L103-L103), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L176-L176), [210](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L210-L210), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L245-L245), 


```solidity
Path: ./src/libraries/Trade.sol

86:        if (totalBaseAmount == 0) {	// @audit-issue

103:        if (settledQuoteAmount * totalBaseAmount <= 0) {	// @audit-issue
```
[86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L86-L86), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L103-L103), 


```solidity
Path: ./src/libraries/VaultLib.sol

30:        if (vaultId == 0) {	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L30-L30), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

45:        if (interestRateQuote > 0 || interestRateBase > 0) {	// @audit-issue

61:        if (utilizationRatio == 0) {	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L45-L45), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L61-L61), 


```solidity
Path: ./src/libraries/Perp.sol

165:        if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {	// @audit-issue

223:        if (totalLiquidityAmount == 0) {	// @audit-issue

349:        if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {	// @audit-issue

374:        if (_assetStatus.totalAmount == 0) {	// @audit-issue

390:        if (f0 == 0 && f1 == 0) {	// @audit-issue

432:        if (_tradeSqrtAmount == 0) {	// @audit-issue

443:        if (_tradeSqrtAmount > 0) {	// @audit-issue

450:        } else if (_tradeSqrtAmount < 0) {	// @audit-issue

535:        if (_userStatus.sqrtPerp.amount * _amount >= 0) {	// @audit-issue

551:        if (closeAmount > 0) {	// @audit-issue

553:        } else if (closeAmount < 0) {	// @audit-issue

560:        if (openAmount > 0) {	// @audit-issue

565:        } else if (openAmount < 0) {	// @audit-issue

593:        if (_isWithdraw && _assetStatus.borrowedAmount == 0) {	// @audit-issue

605:        if (_assetStatus.totalAmount == 0) {	// @audit-issue

626:        if (_userStatus.sqrtPerp.amount == 0) {	// @audit-issue

633:        if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {	// @audit-issue

646:            _userStatus.sqrtPerp.amount < 0	// @audit-issue

653:            _userStatus.sqrtPerp.amount < 0	// @audit-issue

659:        if (_userStatus.sqrtPerp.amount < 0) {	// @audit-issue

678:        if (_tradeAmount == 0) {	// @audit-issue

682:        if (_positionAmount * _tradeAmount >= 0) {	// @audit-issue

755:        if (_userStatus.sqrtPerp.amount * _tradeSqrtAmount >= 0) {	// @audit-issue

766:        if (openAmount != 0) {	// @audit-issue

768:            offsetUnderlying = LPMath.calculateAmount0OffsetWithTick(_tickUpper, openAmount.abs(), openAmount < 0);	// @audit-issue

771:            offsetStable = LPMath.calculateAmount1OffsetWithTick(_tickLower, openAmount.abs(), openAmount < 0);	// @audit-issue

773:            if (openAmount < 0) {	// @audit-issue

784:        if (closeAmount != 0) {	// @audit-issue
```
[165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L165-L165), [223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L223-L223), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L349-L349), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L374-L374), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L390-L390), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L432-L432), [443](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L443-L443), [450](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L450-L450), [535](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L535-L535), [551](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L551-L551), [553](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L553-L553), [560](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L560-L560), [565](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L565-L565), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L593-L593), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L605-L605), [626](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L626-L626), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L633-L633), [646](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L646-L646), [653](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L653-L653), [659](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L659-L659), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L678-L678), [682](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L682-L682), [755](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L755-L755), [766](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L766-L766), [768](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L768-L768), [771](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L771-L771), [773](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L773-L773), [784](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L784-L784), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

38:        if (_amount == 0) {	// @audit-issue

51:        if (_amount == 0) {	// @audit-issue

55:        require(_supplyTokenAmount > 0, "S3");	// @audit-issue

73:        return (a >= 0 && b >= 0) || (a < 0 && b < 0);	// @audit-issue

84:        if (userStatus.positionAmount > 0) {	// @audit-issue

86:        } else if (userStatus.positionAmount < 0) {	// @audit-issue

104:        if (closeAmount > 0) {	// @audit-issue

106:        } else if (closeAmount < 0) {	// @audit-issue

113:        if (openAmount > 0) {	// @audit-issue

117:        } else if (openAmount < 0) {	// @audit-issue

135:        if (_userStatus.positionAmount > 0) {	// @audit-issue

148:        if (_userStatus.positionAmount > 0) {	// @audit-issue

160:        require(accountState.positionAmount >= 0, "S1");	// @audit-issue

175:        require(accountState.positionAmount <= 0, "S1");	// @audit-issue

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue

231:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L51-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L55-L55), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L73-L73), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L86-L86), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L106-L106), [113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L113-L113), [117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L117-L117), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L135-L135), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L148-L148), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L160-L160), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L175-L175), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L231-L231), 


```solidity
Path: ./src/libraries/SlippageLib.sol

29:        if (tradeResult.averagePrice == 0) {	// @audit-issue

33:        if (tradeResult.averagePrice > 0) {	// @audit-issue

38:        } else if (tradeResult.averagePrice < 0) {	// @audit-issue

46:            maxAcceptableSqrtPriceRange > 0	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L33-L33), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L38-L38), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L46-L46), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

153:        require(_pairs[_pairId].id == 0, "AAA");	// @audit-issue

216:        require(_assetRiskParams.rangeSize > 0 && _assetRiskParams.rebalanceThreshold > 0, "C0");	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L153-L153), [216](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L216-L216), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

29:        if (_amount <= 0) {	// @audit-issue

64:        require(_amount > 0, "AZ");	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L29-L29), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

51:            if (deltaPositionBase != 0) {	// @audit-issue

60:                if (exceedsQuote < 0) {	// @audit-issue

64:                if (settledBaseAmount + deltaPositionBase != 0) {	// @audit-issue

68:                if (exceedsQuote > 0) {	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L51-L51), [60](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L60-L60), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L64-L64), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L68-L68), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

45:        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");	// @audit-issue

84:            tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE	// @audit-issue

92:            if (remainingMargin > 0) {	// @audit-issue

101:            } else if (remainingMargin < 0) {	// @audit-issue

164:        if (vaultValue <= 0 || minMargin == 0) {	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L45-L45), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L92-L92), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L101-L101), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L164-L164), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

79:        if (settledBaseAmount != 0) {	// @audit-issue
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L79-L79), 


```solidity
Path: ./src/libraries/math/LPMath.sol

36:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue

74:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L36-L36), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L74-L74), 


```solidity
Path: ./src/libraries/math/Math.sol

13:        return uint256(x >= 0 ? x : -x);	// @audit-issue

25:        if (x == 0) {	// @audit-issue

27:        } else if (x > 0) {	// @audit-issue

35:        if (x == 0) {	// @audit-issue

37:        } else if (x > 0) {	// @audit-issue

45:        if (x == 0) {	// @audit-issue

47:        } else if (x > 0) {	// @audit-issue

55:        if (b >= 0) {	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L13-L13), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L25-L25), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L37-L37), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L47-L47), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L55-L55), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

83:            baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,	// @audit-issue
```
[83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L83-L83), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

47:        if (settlementParams.fee < 0) {	// @audit-issue

55:        if (settlementParams.fee > 0) {	// @audit-issue

67:        if (baseAmountDelta > 0) {	// @audit-issue

77:        } else if (baseAmountDelta < 0) {	// @audit-issue

122:        if (price == 0) {	// @audit-issue

169:        if (price == 0) {	// @audit-issue
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L47-L47), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L55-L55), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L67-L67), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L77-L77), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L122-L122), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L169-L169), 


```solidity
Path: ./src/types/GlobalData.sol

27:        if (vaultId <= 0 || globalData.vaultCount <= vaultId) revert IPredyPool.InvalidPairId();	// @audit-issue

31:        if (pairId <= 0 || globalData.pairsCount <= pairId) revert IPredyPool.InvalidPairId();	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L27-L27), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L31-L31), 


#### Recommendation

To optimize gas usage in your Solidity code, consider using inline assembly for checking `0`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to write `address` storage values
Using assembly `{ sstore(state.slot, addr)}` instead of `state = addr` can save gas.


```solidity
Path: ./src/PredyPool.sol

99:        operator = newOperator;	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L99-L99), 


```solidity
Path: ./src/PriceFeed.sol

15:        _pyth = pyth;	// @audit-issue

38:        _quotePriceFeed = quotePrice;	// @audit-issue

39:        _pyth = pyth;	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L15-L15), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L38-L38), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L39-L39), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

76:        _permit2 = IPermit2(permit2Address);	// @audit-issue
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L76-L76), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

99:        _permit2 = IPermit2(permit2Address);	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L99-L99), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

17:        _swapRouter = ISwapRouter(swapRouterAddress);	// @audit-issue

19:        _quoterV2 = IQuoterV2(quoterAddress);	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L17-L17), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L19-L19), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

23:        whitelistFiller = _whitelistFiller;	// @audit-issue

25:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L23-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L25-L25), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

44:        whitelistFiller = _whitelistFiller;	// @audit-issue

46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L46-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L133-L133), 


```solidity
Path: ./src/base/BaseHookCallback.sol

13:        _predyPool = predyPool;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L13-L13), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

18:        _controller = controller;	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L18-L18), 


#### Recommendation

To reduce gas costs in your Solidity code, consider using assembly with `{ sstore(state.slot, addr) }` for writing `address` storage values instead of `state = addr`. This approach can result in significant gas savings.

### Use assembly to emit an `event`
To efficiently emit events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.

However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.

A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.


```solidity
Path: ./src/PredyPool.sol

101:        emit OperatorUpdated(newOperator);	// @audit-issue

190:        emit ProtocolRevenueWithdrawn(pairId, isQuoteToken, amount);	// @audit-issue

212:        emit CreatorRevenueWithdrawn(pairId, isQuoteToken, amount);	// @audit-issue

291:        emit RecepientUpdated(vaultId, recipient);	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L101-L101), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L190-L190), [212](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L212-L212), [291](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L291-L291), 


```solidity
Path: ./src/PriceFeed.sol

21:        emit PriceFeedCreated(quotePrice, priceId, decimalsDiff, address(priceFeed));	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L21-L21), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

101:                emit GammaPositionTraded(	// @audit-issue

123:                emit GammaPositionTraded(	// @audit-issue

440:            emit GammaPositionModified(userPosition.owner, userPosition.pairId, userPosition.vaultId, modifyInfo);	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L101-L101), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L123-L123), [440](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L440-L440), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

131:            emit PerpTraded2(	// @audit-issue
```
[131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L131-L131), 


```solidity
Path: ./src/libraries/Trade.sol

107:        emit Swapped(pairId, vaultId, msg.sender, settledQuoteAmount, settledBaseAmount);	// @audit-issue
```
[107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L107-L107), 


```solidity
Path: ./src/libraries/VaultLib.sol

44:            emit VaultCreated(vault.id, vault.owner, quoteToken, pairId);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L44-L44), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

82:        emit InterestGrowthUpdated(	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L82-L82), 


```solidity
Path: ./src/libraries/Perp.sol

411:        emit PremiumGrowthUpdated(_pairId, _assetStatus.totalAmount, _assetStatus.borrowedAmount, f0, f1, spreadParam);	// @audit-issue

578:        emit SqrtPositionUpdated(_pairId, openAmount, closeAmount);	// @audit-issue
```
[411](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L411-L411), [578](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L578-L578), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

127:        emit ScaledAssetPositionUpdated(_pairId, _isStable, openAmount, closeAmount);	// @audit-issue
```
[127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L127-L127), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

93:        emit PairAdded(pairId, _addPairParam.quoteToken, _addPairParam.uniswapPool);	// @audit-issue

101:        emit FeeRatioUpdated(_pairStatus.id, _feeRatio);	// @audit-issue

109:        emit PoolOwnerUpdated(_pairStatus.id, _poolOwner);	// @audit-issue

115:        emit PriceOracleUpdated(_pairStatus.id, _priceOracle);	// @audit-issue

125:        emit AssetRiskParamsUpdated(_pairStatus.id, _riskParams);	// @audit-issue

139:        emit IRMParamsUpdated(_pairStatus.id, _quoteIrmParams, _baseIrmParams);	// @audit-issue

188:        emit AssetRiskParamsUpdated(_pairId, _addPairParam.assetRiskParams);	// @audit-issue

189:        emit IRMParamsUpdated(_pairId, _addPairParam.quoteIrmParams, _addPairParam.baseIrmParams);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L93-L93), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L101-L101), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L109-L109), [115](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L115-L115), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L125-L125), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L139-L139), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L188-L188), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L189-L189), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

43:        emit TokenSupplied(msg.sender, pair.id, _isStable, _amount);	// @audit-issue

76:        emit TokenWithdrawn(msg.sender, pair.id, _isStable, finalWithdrawalAmount);	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L43-L43), [76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L76-L76), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

73:            emit Rebalanced(	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L73-L73), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

110:        emit PositionLiquidated(	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L110-L110), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

58:        emit PositionUpdated(	// @audit-issue

85:        emit MarginUpdated(tradeParams.vaultId, marginAmountUpdate);	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L58-L58), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L85-L85), 


#### Recommendation

To optimize event emission in your Solidity code, consider using assembly with scratch space and the free memory pointer. This approach can help reduce gas costs by avoiding memory expansion expenses. However, it's crucial to ensure safe optimization by caching and restoring the free memory pointer, as demonstrated in examples like Solady's codebase.

### Use assembly to validate `msg.sender`
We can use assembly to efficiently validate `msg.sender` with the least amount of opcodes necessary. For more details check the following report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender)


```solidity
Path: ./src/PredyPool.sol

48:        if (operator != msg.sender) revert CallerIsNotOperator();	// @audit-issue

54:        if (msg.sender != locker) revert LockedBy(locker);	// @audit-issue

59:        if (globalData.pairs[pairId].poolOwner != msg.sender) revert CallerIsNotPoolCreator();	// @audit-issue

64:        if (globalData.vaults[vaultId].owner != msg.sender) revert CallerIsNotVaultOwner();	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L48-L48), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L54-L54), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L59-L59), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L64-L64), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

180:            if (msg.sender != whitelistFiller) {	// @audit-issue
```
[180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L180-L180), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

202:            if (msg.sender != whitelistFiller) {	// @audit-issue
```
[202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L202-L202), 


```solidity
Path: ./src/libraries/VaultLib.sol

19:        if (vault.owner != msg.sender) {	// @audit-issue

51:            if (vault.owner != msg.sender) {	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L19-L19), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L51-L51), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

16:        if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L16-L16), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

32:        if (msg.sender != whitelistFiller) revert CallerIsNotFiller();	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L32-L32), 


```solidity
Path: ./src/base/BaseHookCallback.sol

17:        if (msg.sender != address(_predyPool)) revert CallerIsNotPredyPool();	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L17-L17), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

11:        require(_controller == msg.sender, "ST0");	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L11-L11), 


#### Recommendation

To optimize the validation of `msg.sender` in your Solidity code, consider using assembly to achieve this with the minimum number of opcodes required. You can refer to the detailed report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender) for more insights and examples on efficient implementation.

### Use assembly to write hashes
Considering using [assembly](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) to write hashes, as it's possible to save a considerable amount of gas.


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

44:        return keccak256(	// @audit-issue

116:        return keccak256(	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L44-L44), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L116-L116), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

55:        return keccak256(	// @audit-issue

68:                keccak256(order.validationData)	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L55-L55), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L68-L68), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

59:        return keccak256(	// @audit-issue

65:                keccak256(bytes(order.side)),	// @audit-issue

73:                keccak256(order.auctionData)	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L59-L59), [65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L65-L65), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L73-L73), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

33:        bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L33-L33), 


```solidity
Path: ./src/libraries/UniHelper.sol

45:            if (keccak256(data) != keccak256(abi.encodeWithSignature("Error(string)", "OLD"))) {	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L45-L45), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

19:        return keccak256(abi.encode(ORDER_INFO_TYPE_HASH, info.market, info.trader, info.nonce, info.deadline));	// @audit-issue
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L19-L19), 


#### Recommendation

To achieve gas savings in your Solidity code when writing hashes, consider using assembly, as it can significantly reduce gas consumption. You can refer to this [article](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) for insights on how to efficiently implement hash operations.