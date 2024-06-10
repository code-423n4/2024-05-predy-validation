## L-1: Solmate's SafeTransferLib does not check for token contract's existence

There is a subtle difference between the implementation of solmate's SafeTransferLib and OZ's SafeERC20: OZ's SafeERC20 checks if the token is a contract or not, solmate's SafeTransferLib does not.
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9 
`@dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller`


<details><summary>10 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 7](src/PredyPool.sol#L7)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 4](src/base/SettlementCallbackLib.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 4](src/libraries/logic/LiquidationLogic.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/libraries/logic/ReallocationLogic.sol [Line: 4](src/libraries/logic/ReallocationLogic.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/libraries/logic/SupplyLogic.sol [Line: 4](src/libraries/logic/SupplyLogic.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 4](src/markets/gamma/GammaTradeMarket.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 4](src/markets/perp/PerpMarketV1.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/markets/spot/SpotMarket.sol [Line: 6](src/markets/spot/SpotMarket.sol#L6)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/settlements/UniswapSettlement.sol [Line: 4](src/settlements/UniswapSettlement.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

- Found in src/types/GlobalData.sol [Line: 4](src/types/GlobalData.sol#L4)

	```solidity
	import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
	```

</details>

## L-2: Solidity pragma should be specific, not wide

Consider using a specific version of Solidity in your contracts instead of a wide version. For example, instead of `pragma solidity ^0.8.0;`, use `pragma solidity 0.8.0;`

<details><summary>68 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 2](src/PredyPool.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/PriceFeed.sol [Line: 2](src/PriceFeed.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseHookCallback.sol [Line: 2](src/base/BaseHookCallback.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseHookCallbackUpgradable.sol [Line: 2](src/base/BaseHookCallbackUpgradable.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseMarket.sol [Line: 2](src/base/BaseMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 2](src/base/BaseMarketUpgradable.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 2](src/base/SettlementCallbackLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/interfaces/IFillerMarket.sol [Line: 2](src/interfaces/IFillerMarket.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/IHooks.sol [Line: 2](src/interfaces/IHooks.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/interfaces/IPredyPool.sol [Line: 2](src/interfaces/IPredyPool.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/ISettlement.sol [Line: 2](src/interfaces/ISettlement.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/interfaces/ISpotMarket.sol [Line: 2](src/interfaces/ISpotMarket.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/ISupplyToken.sol [Line: 2](src/interfaces/ISupplyToken.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/lens/GammaTradeMarketQuoter.sol [Line: 2](src/lens/GammaTradeMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/PerpMarketQuoter.sol [Line: 2](src/lens/PerpMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/PredyPoolQuoter.sol [Line: 2](src/lens/PredyPoolQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/SpotMarketQuoter.sol [Line: 2](src/lens/SpotMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/ApplyInterestLib.sol [Line: 2](src/libraries/ApplyInterestLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Constants.sol [Line: 2](src/libraries/Constants.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/DataType.sol [Line: 2](src/libraries/DataType.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/InterestRateModel.sol [Line: 2](src/libraries/InterestRateModel.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PairLib.sol [Line: 2](src/libraries/PairLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Perp.sol [Line: 2](src/libraries/Perp.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PerpFee.sol [Line: 2](src/libraries/PerpFee.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PositionCalculator.sol [Line: 2](src/libraries/PositionCalculator.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PremiumCurveModel.sol [Line: 2](src/libraries/PremiumCurveModel.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Reallocation.sol [Line: 2](src/libraries/Reallocation.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/ScaledAsset.sol [Line: 2](src/libraries/ScaledAsset.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/SlippageLib.sol [Line: 2](src/libraries/SlippageLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Trade.sol [Line: 2](src/libraries/Trade.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/UniHelper.sol [Line: 2](src/libraries/UniHelper.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/VaultLib.sol [Line: 2](src/libraries/VaultLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 2](src/libraries/logic/AddPairLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 2](src/libraries/logic/LiquidationLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/ReaderLogic.sol [Line: 2](src/libraries/logic/ReaderLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/ReallocationLogic.sol [Line: 2](src/libraries/logic/ReallocationLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/SupplyLogic.sol [Line: 2](src/libraries/logic/SupplyLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/TradeLogic.sol [Line: 2](src/libraries/logic/TradeLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/Bps.sol [Line: 2](src/libraries/math/Bps.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/LPMath.sol [Line: 2](src/libraries/math/LPMath.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/Math.sol [Line: 2](src/libraries/math/Math.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/DecayLib.sol [Line: 2](src/libraries/orders/DecayLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/OrderInfoLib.sol [Line: 2](src/libraries/orders/OrderInfoLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/Permit2Lib.sol [Line: 2](src/libraries/orders/Permit2Lib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/ResolvedOrder.sol [Line: 2](src/libraries/orders/ResolvedOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/L2Decoder.sol [Line: 2](src/markets/L2Decoder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/ArrayLib.sol [Line: 2](src/markets/gamma/ArrayLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaOrder.sol [Line: 2](src/markets/gamma/GammaOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 2](src/markets/gamma/GammaTradeMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketL2.sol [Line: 2](src/markets/gamma/GammaTradeMarketL2.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 2](src/markets/gamma/GammaTradeMarketLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketWrapper.sol [Line: 2](src/markets/gamma/GammaTradeMarketWrapper.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/L2GammaDecoder.sol [Line: 2](src/markets/gamma/L2GammaDecoder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarket.sol [Line: 2](src/markets/perp/PerpMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarketLib.sol [Line: 2](src/markets/perp/PerpMarketLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 2](src/markets/perp/PerpMarketV1.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpOrder.sol [Line: 2](src/markets/perp/PerpOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpOrderV3.sol [Line: 2](src/markets/perp/PerpOrderV3.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotMarket.sol [Line: 2](src/markets/spot/SpotMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotMarketL2.sol [Line: 2](src/markets/spot/SpotMarketL2.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotOrder.sol [Line: 2](src/markets/spot/SpotOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/settlements/UniswapSettlement.sol [Line: 2](src/settlements/UniswapSettlement.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/tokenization/SupplyToken.sol [Line: 2](src/tokenization/SupplyToken.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/types/GlobalData.sol [Line: 2](src/types/GlobalData.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/types/LockData.sol [Line: 2](src/types/LockData.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/vendors/AggregatorV3Interface.sol [Line: 2](src/vendors/AggregatorV3Interface.sol#L2)

	```solidity
	pragma solidity ^0.8.0;
	```

- Found in src/vendors/IPyth.sol [Line: 2](src/vendors/IPyth.sol#L2)

	```solidity
	pragma solidity ^0.8.0;
	```

- Found in src/vendors/IUniswapV3PoolOracle.sol [Line: 2](src/vendors/IUniswapV3PoolOracle.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

</details>



## L-3: Missing checks for `address(0)` when assigning values to address state variables

Check for `address(0)` when assigning values to address state variables.

<details><summary>16 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 303](src/PredyPool.sol#L303)

	```solidity
	        allowedTraders[trader][pairId] = enabled;
	```

- Found in src/base/BaseHookCallbackUpgradable.sol [Line: 21](src/base/BaseHookCallbackUpgradable.sol#L21)

	```solidity
	        _predyPool = predyPool;
	```

- Found in src/base/BaseMarket.sol [Line: 23](src/base/BaseMarket.sol#L23)

	```solidity
	        whitelistFiller = _whitelistFiller;
	```

- Found in src/base/BaseMarket.sol [Line: 85](src/base/BaseMarket.sol#L85)

	```solidity
	        whitelistFiller = newWhitelistFiller;
	```

- Found in src/base/BaseMarket.sol [Line: 93](src/base/BaseMarket.sol#L93)

	```solidity
	        _whiteListedSettlements[settlementContractAddress] = isEnabled;
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 44](src/base/BaseMarketUpgradable.sol#L44)

	```solidity
	        whitelistFiller = _whitelistFiller;
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 46](src/base/BaseMarketUpgradable.sol#L46)

	```solidity
	        _quoter = PredyPoolQuoter(quoterAddress);
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 129](src/base/BaseMarketUpgradable.sol#L129)

	```solidity
	        whitelistFiller = newWhitelistFiller;
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 133](src/base/BaseMarketUpgradable.sol#L133)

	```solidity
	        _quoter = PredyPoolQuoter(newQuoter);
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 141](src/base/BaseMarketUpgradable.sol#L141)

	```solidity
	        _whiteListedSettlements[settlementContractAddress] = isEnabled;
	```

- Found in src/lens/GammaTradeMarketQuoter.sol [Line: 17](src/lens/GammaTradeMarketQuoter.sol#L17)

	```solidity
	        gammaTradeMarket = _gammaTradeMarket;
	```

- Found in src/lens/PerpMarketQuoter.sol [Line: 18](src/lens/PerpMarketQuoter.sol#L18)

	```solidity
	        perpMarket = _perpMarket;
	```

- Found in src/lens/SpotMarketQuoter.sol [Line: 16](src/lens/SpotMarketQuoter.sol#L16)

	```solidity
	        spotMarket = _spotMarket;
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 76](src/markets/gamma/GammaTradeMarket.sol#L76)

	```solidity
	        _permit2 = IPermit2(permit2Address);
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 99](src/markets/perp/PerpMarketV1.sol#L99)

	```solidity
	        _permit2 = IPermit2(permit2Address);
	```

- Found in src/markets/spot/SpotMarket.sol [Line: 77](src/markets/spot/SpotMarket.sol#L77)

	```solidity
	        _whiteListedSettlements[settlementContractAddress] = isEnabled;
	```

</details>



## L-4: `public` functions not used internally could be marked `external`

Instead of marking a function as `public`, consider marking it as `external` if it is not used internally.

<details><summary>3 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 70](src/PredyPool.sol#L70)

	```solidity
	    function initialize(address uniswapFactory) public initializer {
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 69](src/markets/gamma/GammaTradeMarket.sol#L69)

	```solidity
	    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 92](src/markets/perp/PerpMarketV1.sol#L92)

	```solidity
	    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
	```

</details>



## L-5: Define and use `constant` variables instead of using literals

If the same constant literal value is used multiple times, create a constant state variable and reference it throughout the contract.

<details><summary>37 Found Instances</summary>


- Found in src/libraries/Perp.sol [Line: 168](src/libraries/Perp.sol#L168)

	```solidity
	            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);
	```

- Found in src/libraries/Perp.sol [Line: 172](src/libraries/Perp.sol#L172)

	```solidity
	            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);
	```

- Found in src/libraries/Perp.sol [Line: 399](src/libraries/Perp.sol#L399)

	```solidity
	            f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount
	```

- Found in src/libraries/Perp.sol [Line: 402](src/libraries/Perp.sol#L402)

	```solidity
	            f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount
	```

- Found in src/libraries/Perp.sol [Line: 405](src/libraries/Perp.sol#L405)

	```solidity
	        _assetStatus.borrowPremium0Growth += FullMath.mulDiv(f0, 1000 + spreadParam, 1000);
	```

- Found in src/libraries/Perp.sol [Line: 406](src/libraries/Perp.sol#L406)

	```solidity
	        _assetStatus.borrowPremium1Growth += FullMath.mulDiv(f1, 1000 + spreadParam, 1000);
	```

- Found in src/libraries/Perp.sol [Line: 611](src/libraries/Perp.sol#L611)

	```solidity
	        if (utilization > 1e18) {
	```

- Found in src/libraries/Perp.sol [Line: 612](src/libraries/Perp.sol#L612)

	```solidity
	            return 1e18;
	```

- Found in src/libraries/ScaledAsset.sol [Line: 197](src/libraries/ScaledAsset.sol#L197)

	```solidity
	            100
	```

- Found in src/libraries/ScaledAsset.sol [Line: 205](src/libraries/ScaledAsset.sol#L205)

	```solidity
	            100 - _reserveFactor,
	```

- Found in src/libraries/ScaledAsset.sol [Line: 206](src/libraries/ScaledAsset.sol#L206)

	```solidity
	            100
	```

- Found in src/libraries/ScaledAsset.sol [Line: 239](src/libraries/ScaledAsset.sol#L239)

	```solidity
	        if (utilization > 1e18) {
	```

- Found in src/libraries/ScaledAsset.sol [Line: 240](src/libraries/ScaledAsset.sol#L240)

	```solidity
	            return 1e18;
	```

- Found in src/libraries/SlippageLib.sol [Line: 48](src/libraries/SlippageLib.sol#L48)

	```solidity
	                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange
	```

- Found in src/libraries/SlippageLib.sol [Line: 49](src/libraries/SlippageLib.sol#L49)

	```solidity
	                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
	```

- Found in src/libraries/Trade.sol [Line: 87](src/libraries/Trade.sol#L87)

	```solidity
	            int256 amountQuote = calculateStableAmount(sqrtPrice, 1e18).toInt256();
	```

- Found in src/libraries/Trade.sol [Line: 89](src/libraries/Trade.sol#L89)

	```solidity
	            return divToStable(swapParams, int256(1e18), amountQuote, 0);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 214](src/libraries/logic/AddPairLogic.sol#L214)

	```solidity
	        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 221](src/libraries/logic/AddPairLogic.sol#L221)

	```solidity
	            _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 222](src/libraries/logic/AddPairLogic.sol#L222)

	```solidity
	                && _irmParams.slope2 <= 10 * 1e18,
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 45](src/libraries/logic/LiquidationLogic.sol#L45)

	```solidity
	        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 62](src/libraries/logic/LiquidationLogic.sol#L62)

	```solidity
	            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 63](src/libraries/logic/LiquidationLogic.sol#L63)

	```solidity
	            -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 168](src/libraries/logic/LiquidationLogic.sol#L168)

	```solidity
	        uint256 ratio = uint256(vaultValue * 1e4 / minMargin);
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 170](src/libraries/logic/LiquidationLogic.sol#L170)

	```solidity
	        if (ratio > 1e4) {
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 174](src/libraries/logic/LiquidationLogic.sol#L174)

	```solidity
	        return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 53](src/markets/gamma/GammaTradeMarketLib.sol#L53)

	```solidity
	        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 203](src/markets/gamma/GammaTradeMarketLib.sol#L203)

	```solidity
	        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);
	```

</details>



## L-6: Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

<details><summary>28 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 42](src/PredyPool.sol#L42)

	```solidity
	    event OperatorUpdated(address operator);
	```

- Found in src/PredyPool.sol [Line: 43](src/PredyPool.sol#L43)

	```solidity
	    event RecepientUpdated(uint256 vaultId, address recipient);
	```

- Found in src/PredyPool.sol [Line: 44](src/PredyPool.sol#L44)

	```solidity
	    event ProtocolRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);
	```

- Found in src/PredyPool.sol [Line: 45](src/PredyPool.sol#L45)

	```solidity
	    event CreatorRevenueWithdrawn(uint256 pairId, bool isStable, uint256 amount);
	```

- Found in src/PriceFeed.sol [Line: 12](src/PriceFeed.sol#L12)

	```solidity
	    event PriceFeedCreated(address quotePrice, bytes32 priceId, uint256 decimalsDiff, address priceFeed);
	```

- Found in src/libraries/ApplyInterestLib.sol [Line: 12](src/libraries/ApplyInterestLib.sol#L12)

	```solidity
	    event InterestGrowthUpdated(
	```

- Found in src/libraries/Perp.sol [Line: 109](src/libraries/Perp.sol#L109)

	```solidity
	    event PremiumGrowthUpdated(
	```

- Found in src/libraries/Perp.sol [Line: 117](src/libraries/Perp.sol#L117)

	```solidity
	    event SqrtPositionUpdated(uint256 indexed pairId, int256 open, int256 close);
	```

- Found in src/libraries/ScaledAsset.sol [Line: 27](src/libraries/ScaledAsset.sol#L27)

	```solidity
	    event ScaledAssetPositionUpdated(uint256 indexed pairId, bool isStable, int256 open, int256 close);
	```

- Found in src/libraries/Trade.sol [Line: 30](src/libraries/Trade.sol#L30)

	```solidity
	    event Swapped(uint256 pairId, uint256 vaultId, address owner, int256 settledQuoteAmount, int256 settledBaseAmount);
	```

- Found in src/libraries/VaultLib.sol [Line: 9](src/libraries/VaultLib.sol#L9)

	```solidity
	    event VaultCreated(uint256 vaultId, address owner, address quoteToken, uint256 pairId);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 31](src/libraries/logic/AddPairLogic.sol#L31)

	```solidity
	    event PairAdded(uint256 pairId, address quoteToken, address uniswapPool);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 32](src/libraries/logic/AddPairLogic.sol#L32)

	```solidity
	    event AssetRiskParamsUpdated(uint256 pairId, Perp.AssetRiskParams riskParams);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 33](src/libraries/logic/AddPairLogic.sol#L33)

	```solidity
	    event IRMParamsUpdated(
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 36](src/libraries/logic/AddPairLogic.sol#L36)

	```solidity
	    event FeeRatioUpdated(uint256 pairId, uint8 feeRatio);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 37](src/libraries/logic/AddPairLogic.sol#L37)

	```solidity
	    event PoolOwnerUpdated(uint256 pairId, address poolOwner);
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 38](src/libraries/logic/AddPairLogic.sol#L38)

	```solidity
	    event PriceOracleUpdated(uint256 pairId, address priceOracle);
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 29](src/libraries/logic/LiquidationLogic.sol#L29)

	```solidity
	    event PositionLiquidated(
	```

- Found in src/libraries/logic/ReallocationLogic.sol [Line: 18](src/libraries/logic/ReallocationLogic.sol#L18)

	```solidity
	    event Rebalanced(
	```

- Found in src/libraries/logic/SupplyLogic.sol [Line: 19](src/libraries/logic/SupplyLogic.sol#L19)

	```solidity
	    event TokenSupplied(address indexed account, uint256 pairId, bool isStable, uint256 suppliedAmount);
	```

- Found in src/libraries/logic/SupplyLogic.sol [Line: 20](src/libraries/logic/SupplyLogic.sol#L20)

	```solidity
	    event TokenWithdrawn(address indexed account, uint256 pairId, bool isStable, uint256 finalWithdrawnAmount);
	```

- Found in src/libraries/logic/TradeLogic.sol [Line: 18](src/libraries/logic/TradeLogic.sol#L18)

	```solidity
	    event MarginUpdated(uint256 indexed vaultId, int256 updatedMarginAmount);
	```

- Found in src/libraries/logic/TradeLogic.sol [Line: 20](src/libraries/logic/TradeLogic.sol#L20)

	```solidity
	    event PositionUpdated(
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 53](src/markets/gamma/GammaTradeMarket.sol#L53)

	```solidity
	    event GammaPositionTraded(
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 65](src/markets/gamma/GammaTradeMarket.sol#L65)

	```solidity
	    event GammaPositionModified(address indexed trader, uint256 pairId, uint256 positionId, GammaModifyInfo modifyInfo);
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 70](src/markets/perp/PerpMarketV1.sol#L70)

	```solidity
	    event PerpTraded(
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 79](src/markets/perp/PerpMarketV1.sol#L79)

	```solidity
	    event PerpTraded2(
	```

- Found in src/markets/spot/SpotMarket.sol [Line: 52](src/markets/spot/SpotMarket.sol#L52)

	```solidity
	    event SpotTraded(
	```

</details>



## L-7: Empty `require()` / `revert()` statements

Use descriptive reason strings or custom errors for revert paths.

<details><summary>11 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 80](src/PredyPool.sol#L80)

	```solidity
	        require(allowedUniswapPools[msg.sender]);
	```

- Found in src/PredyPool.sol [Line: 98](src/PredyPool.sol#L98)

	```solidity
	        require(newOperator != address(0));
	```

- Found in src/PredyPool.sol [Line: 301](src/PredyPool.sol#L301)

	```solidity
	        require(globalData.pairs[pairId].allowlistEnabled);
	```

- Found in src/PriceFeed.sol [Line: 52](src/PriceFeed.sol#L52)

	```solidity
	        require(quoteAnswer > 0 && basePrice.price > 0);
	```

- Found in src/base/BaseMarket.sol [Line: 105](src/base/BaseMarket.sol#L105)

	```solidity
	        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 153](src/base/BaseMarketUpgradable.sol#L153)

	```solidity
	        require(_quoteTokenMap[pairId] != address(0) && entryTokenAddress == _quoteTokenMap[pairId]);
	```

- Found in src/libraries/Reallocation.sol [Line: 110](src/libraries/Reallocation.sol#L110)

	```solidity
	        require(tickSpacing > 0);
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 201](src/markets/gamma/GammaTradeMarketLib.sol#L201)

	```solidity
	        require(modifyInfo.maxSlippageTolerance >= modifyInfo.minSlippageTolerance);
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 202](src/markets/gamma/GammaTradeMarketLib.sol#L202)

	```solidity
	        require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 203](src/markets/gamma/GammaTradeMarketLib.sol#L203)

	```solidity
	        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 266](src/markets/perp/PerpMarketV1.sol#L266)

	```solidity
	        require(perpOrder.slippageTolerance <= Bps.ONE);
	```

</details>



## L-8: PUSH0 is not supported by all chains

Solc compiler version 0.8.20 switches the default target EVM version to Shanghai, which means that the generated bytecode will include PUSH0 opcodes. Be sure to select the appropriate EVM version in case you intend to deploy on a chain other than mainnet like L2 chains that may not support PUSH0, otherwise deployment of your contracts will fail.

<details><summary>68 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 2](src/PredyPool.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/PriceFeed.sol [Line: 2](src/PriceFeed.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseHookCallback.sol [Line: 2](src/base/BaseHookCallback.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseHookCallbackUpgradable.sol [Line: 2](src/base/BaseHookCallbackUpgradable.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseMarket.sol [Line: 2](src/base/BaseMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/BaseMarketUpgradable.sol [Line: 2](src/base/BaseMarketUpgradable.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 2](src/base/SettlementCallbackLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/interfaces/IFillerMarket.sol [Line: 2](src/interfaces/IFillerMarket.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/IHooks.sol [Line: 2](src/interfaces/IHooks.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/interfaces/IPredyPool.sol [Line: 2](src/interfaces/IPredyPool.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/ISettlement.sol [Line: 2](src/interfaces/ISettlement.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/interfaces/ISpotMarket.sol [Line: 2](src/interfaces/ISpotMarket.sol#L2)

	```solidity
	pragma solidity >=0.8.0;
	```

- Found in src/interfaces/ISupplyToken.sol [Line: 2](src/interfaces/ISupplyToken.sol#L2)

	```solidity
	pragma solidity >=0.7.0;
	```

- Found in src/lens/GammaTradeMarketQuoter.sol [Line: 2](src/lens/GammaTradeMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/PerpMarketQuoter.sol [Line: 2](src/lens/PerpMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/PredyPoolQuoter.sol [Line: 2](src/lens/PredyPoolQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/lens/SpotMarketQuoter.sol [Line: 2](src/lens/SpotMarketQuoter.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/ApplyInterestLib.sol [Line: 2](src/libraries/ApplyInterestLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Constants.sol [Line: 2](src/libraries/Constants.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/DataType.sol [Line: 2](src/libraries/DataType.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/InterestRateModel.sol [Line: 2](src/libraries/InterestRateModel.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PairLib.sol [Line: 2](src/libraries/PairLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Perp.sol [Line: 2](src/libraries/Perp.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PerpFee.sol [Line: 2](src/libraries/PerpFee.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PositionCalculator.sol [Line: 2](src/libraries/PositionCalculator.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/PremiumCurveModel.sol [Line: 2](src/libraries/PremiumCurveModel.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Reallocation.sol [Line: 2](src/libraries/Reallocation.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/ScaledAsset.sol [Line: 2](src/libraries/ScaledAsset.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/SlippageLib.sol [Line: 2](src/libraries/SlippageLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/Trade.sol [Line: 2](src/libraries/Trade.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/UniHelper.sol [Line: 2](src/libraries/UniHelper.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/VaultLib.sol [Line: 2](src/libraries/VaultLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 2](src/libraries/logic/AddPairLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 2](src/libraries/logic/LiquidationLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/ReaderLogic.sol [Line: 2](src/libraries/logic/ReaderLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/ReallocationLogic.sol [Line: 2](src/libraries/logic/ReallocationLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/SupplyLogic.sol [Line: 2](src/libraries/logic/SupplyLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/logic/TradeLogic.sol [Line: 2](src/libraries/logic/TradeLogic.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/Bps.sol [Line: 2](src/libraries/math/Bps.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/LPMath.sol [Line: 2](src/libraries/math/LPMath.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/math/Math.sol [Line: 2](src/libraries/math/Math.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/DecayLib.sol [Line: 2](src/libraries/orders/DecayLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/OrderInfoLib.sol [Line: 2](src/libraries/orders/OrderInfoLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/Permit2Lib.sol [Line: 2](src/libraries/orders/Permit2Lib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/libraries/orders/ResolvedOrder.sol [Line: 2](src/libraries/orders/ResolvedOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/L2Decoder.sol [Line: 2](src/markets/L2Decoder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/ArrayLib.sol [Line: 2](src/markets/gamma/ArrayLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaOrder.sol [Line: 2](src/markets/gamma/GammaOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarket.sol [Line: 2](src/markets/gamma/GammaTradeMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketL2.sol [Line: 2](src/markets/gamma/GammaTradeMarketL2.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 2](src/markets/gamma/GammaTradeMarketLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/GammaTradeMarketWrapper.sol [Line: 2](src/markets/gamma/GammaTradeMarketWrapper.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/gamma/L2GammaDecoder.sol [Line: 2](src/markets/gamma/L2GammaDecoder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarket.sol [Line: 2](src/markets/perp/PerpMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarketLib.sol [Line: 2](src/markets/perp/PerpMarketLib.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 2](src/markets/perp/PerpMarketV1.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpOrder.sol [Line: 2](src/markets/perp/PerpOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/perp/PerpOrderV3.sol [Line: 2](src/markets/perp/PerpOrderV3.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotMarket.sol [Line: 2](src/markets/spot/SpotMarket.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotMarketL2.sol [Line: 2](src/markets/spot/SpotMarketL2.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/markets/spot/SpotOrder.sol [Line: 2](src/markets/spot/SpotOrder.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/settlements/UniswapSettlement.sol [Line: 2](src/settlements/UniswapSettlement.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/tokenization/SupplyToken.sol [Line: 2](src/tokenization/SupplyToken.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/types/GlobalData.sol [Line: 2](src/types/GlobalData.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/types/LockData.sol [Line: 2](src/types/LockData.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

- Found in src/vendors/AggregatorV3Interface.sol [Line: 2](src/vendors/AggregatorV3Interface.sol#L2)

	```solidity
	pragma solidity ^0.8.0;
	```

- Found in src/vendors/IPyth.sol [Line: 2](src/vendors/IPyth.sol#L2)

	```solidity
	pragma solidity ^0.8.0;
	```

- Found in src/vendors/IUniswapV3PoolOracle.sol [Line: 2](src/vendors/IUniswapV3PoolOracle.sol#L2)

	```solidity
	pragma solidity ^0.8.17;
	```

</details>



## L-9: Modifiers invoked only once can be shoe-horned into the function



<details><summary>2 Found Instances</summary>


- Found in src/PredyPool.sol [Line: 52](src/PredyPool.sol#L52)

	```solidity
	    modifier onlyByLocker() {
	```

- Found in src/PredyPool.sol [Line: 63](src/PredyPool.sol#L63)

	```solidity
	    modifier onlyVaultOwner(uint256 vaultId) {
	```

</details>



## L-10: Large literal values multiples of 10000 can be replaced with scientific notation

Use `e` notation, for example: `1e18`, instead of its full numeric value.

<details><summary>1 Found Instances</summary>


- Found in src/libraries/Constants.sol [Line: 16](src/libraries/Constants.sol#L16)

	```solidity
	    uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;
	```

</details>



## L-11: Internal functions called only once can be inlined

Instead of separating the logic into a separate function, consider inlining the logic into the calling function. This can reduce the number of function calls and improve readability.

<details><summary>57 Found Instances</summary>


- Found in src/base/BaseMarketUpgradable.sol [Line: 61](src/base/BaseMarketUpgradable.sol#L61)

	```solidity
	    function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 60](src/base/SettlementCallbackLib.sol#L60)

	```solidity
	    function execSettlementInternal(
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 90](src/base/SettlementCallbackLib.sol#L90)

	```solidity
	    function sell(
	```

- Found in src/base/SettlementCallbackLib.sol [Line: 137](src/base/SettlementCallbackLib.sol#L137)

	```solidity
	    function buy(
	```

- Found in src/libraries/ApplyInterestLib.sol [Line: 75](src/libraries/ApplyInterestLib.sol#L75)

	```solidity
	    function emitInterestGrowthEvent(
	```

- Found in src/libraries/Perp.sol [Line: 262](src/libraries/Perp.sol#L262)

	```solidity
	    function rebalanceForInRange(
	```

- Found in src/libraries/Perp.sol [Line: 305](src/libraries/Perp.sol#L305)

	```solidity
	    function swapForOutOfRange(
	```

- Found in src/libraries/Perp.sol [Line: 332](src/libraries/Perp.sol#L332)

	```solidity
	    function getAvailableLiquidityAmount(
	```

- Found in src/libraries/Perp.sol [Line: 526](src/libraries/Perp.sol#L526)

	```solidity
	    function updateSqrtPosition(
	```

- Found in src/libraries/Perp.sol [Line: 604](src/libraries/Perp.sol#L604)

	```solidity
	    function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {
	```

- Found in src/libraries/Perp.sol [Line: 618](src/libraries/Perp.sol#L618)

	```solidity
	    function updateRebalanceEntry(
	```

- Found in src/libraries/Perp.sol [Line: 707](src/libraries/Perp.sol#L707)

	```solidity
	    function increase(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)
	```

- Found in src/libraries/Perp.sol [Line: 719](src/libraries/Perp.sol#L719)

	```solidity
	    function decrease(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)
	```

- Found in src/libraries/PerpFee.sol [Line: 95](src/libraries/PerpFee.sol#L95)

	```solidity
	    function settlePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus storage sqrtPerp)
	```

- Found in src/libraries/PerpFee.sol [Line: 136](src/libraries/PerpFee.sol#L136)

	```solidity
	    function settleRebalanceInterest(
	```

- Found in src/libraries/PositionCalculator.sol [Line: 63](src/libraries/PositionCalculator.sol#L63)

	```solidity
	    function getIsSafe(
	```

- Found in src/libraries/PositionCalculator.sol [Line: 102](src/libraries/PositionCalculator.sol#L102)

	```solidity
	    function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {
	```

- Found in src/libraries/PositionCalculator.sol [Line: 117](src/libraries/PositionCalculator.sol#L117)

	```solidity
	    function calculateMinValue(
	```

- Found in src/libraries/PositionCalculator.sol [Line: 141](src/libraries/PositionCalculator.sol#L141)

	```solidity
	    function getSqrtIndexPrice(DataType.PairStatus memory pairStatus) internal view returns (uint256 sqrtPriceX96) {
	```

- Found in src/libraries/PositionCalculator.sol [Line: 151](src/libraries/PositionCalculator.sol#L151)

	```solidity
	    function getPositionWithFeeAmount(Perp.UserStatus memory perpUserStatus, DataType.FeeAmount memory feeAmount)
	```

- Found in src/libraries/PositionCalculator.sol [Line: 163](src/libraries/PositionCalculator.sol#L163)

	```solidity
	    function getPosition(Perp.UserStatus memory _perpUserStatus)
	```

- Found in src/libraries/PositionCalculator.sol [Line: 186](src/libraries/PositionCalculator.sol#L186)

	```solidity
	    function calculateMinValue(uint256 _sqrtPrice, PositionParams memory _positionParams, uint256 _riskRatio)
	```

- Found in src/libraries/PositionCalculator.sol [Line: 238](src/libraries/PositionCalculator.sol#L238)

	```solidity
	    function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)
	```

- Found in src/libraries/Reallocation.sol [Line: 126](src/libraries/Reallocation.sol#L126)

	```solidity
	    function calculateMinLowerTick(
	```

- Found in src/libraries/Reallocation.sol [Line: 147](src/libraries/Reallocation.sol#L147)

	```solidity
	    function calculateMaxUpperTick(
	```

- Found in src/libraries/Reallocation.sol [Line: 165](src/libraries/Reallocation.sol#L165)

	```solidity
	    function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)
	```

- Found in src/libraries/Reallocation.sol [Line: 179](src/libraries/Reallocation.sol#L179)

	```solidity
	    function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)
	```

- Found in src/libraries/ScaledAsset.sol [Line: 72](src/libraries/ScaledAsset.sol#L72)

	```solidity
	    function isSameSign(int256 a, int256 b) internal pure returns (bool) {
	```

- Found in src/libraries/ScaledAsset.sol [Line: 130](src/libraries/ScaledAsset.sol#L130)

	```solidity
	    function computeUserFee(ScaledAsset.AssetStatus memory _assetStatus, ScaledAsset.UserStatus memory _userStatus)
	```

- Found in src/libraries/ScaledAsset.sol [Line: 155](src/libraries/ScaledAsset.sol#L155)

	```solidity
	    function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)
	```

- Found in src/libraries/ScaledAsset.sol [Line: 170](src/libraries/ScaledAsset.sol#L170)

	```solidity
	    function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)
	```

- Found in src/libraries/Trade.sol [Line: 76](src/libraries/Trade.sol#L76)

	```solidity
	    function swap(
	```

- Found in src/libraries/Trade.sol [Line: 112](src/libraries/Trade.sol#L112)

	```solidity
	    function getSqrtPrice(address uniswapPoolAddress, bool isQuoteZero) internal view returns (uint256 sqrtPriceX96) {
	```

- Found in src/libraries/Trade.sol [Line: 116](src/libraries/Trade.sol#L116)

	```solidity
	    function calculateStableAmount(uint256 currentSqrtPrice, uint256 baseAmount) internal pure returns (uint256) {
	```

- Found in src/libraries/Trade.sol [Line: 135](src/libraries/Trade.sol#L135)

	```solidity
	    function settleUserBalanceAndFee(
	```

- Found in src/libraries/UniHelper.sol [Line: 35](src/libraries/UniHelper.sol#L35)

	```solidity
	    function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {
	```

- Found in src/libraries/logic/AddPairLogic.sol [Line: 209](src/libraries/logic/AddPairLogic.sol#L209)

	```solidity
	    function validatePoolOwner(address _poolOwner) internal pure {
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 129](src/libraries/logic/LiquidationLogic.sol#L129)

	```solidity
	    function checkVaultIsDanger(
	```

- Found in src/libraries/logic/LiquidationLogic.sol [Line: 159](src/libraries/logic/LiquidationLogic.sol#L159)

	```solidity
	    function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)
	```

- Found in src/libraries/logic/ReaderLogic.sol [Line: 43](src/libraries/logic/ReaderLogic.sol#L43)

	```solidity
	    function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)
	```

- Found in src/libraries/logic/ReaderLogic.sol [Line: 56](src/libraries/logic/ReaderLogic.sol#L56)

	```solidity
	    function revertPairStatus(DataType.PairStatus memory pairStatus) internal pure {
	```

- Found in src/libraries/logic/ReaderLogic.sol [Line: 64](src/libraries/logic/ReaderLogic.sol#L64)

	```solidity
	    function revertVaultStatus(IPredyPool.VaultStatus memory vaultStatus) internal pure {
	```

- Found in src/libraries/logic/TradeLogic.sol [Line: 68](src/libraries/logic/TradeLogic.sol#L68)

	```solidity
	    function callTradeAfterCallback(
	```

- Found in src/libraries/math/LPMath.sol [Line: 30](src/libraries/math/LPMath.sol#L30)

	```solidity
	    function calculateAmount0ForLiquidity(
	```

- Found in src/libraries/math/LPMath.sol [Line: 68](src/libraries/math/LPMath.sol#L68)

	```solidity
	    function calculateAmount1ForLiquidity(
	```

- Found in src/libraries/math/LPMath.sol [Line: 119](src/libraries/math/LPMath.sol#L119)

	```solidity
	    function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
	```

- Found in src/libraries/math/LPMath.sol [Line: 145](src/libraries/math/LPMath.sol#L145)

	```solidity
	    function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
	```

- Found in src/libraries/orders/DecayLib.sol [Line: 16](src/libraries/orders/DecayLib.sol#L16)

	```solidity
	    function decay2(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime, uint256 value)
	```

- Found in src/markets/gamma/ArrayLib.sol [Line: 15](src/markets/gamma/ArrayLib.sol#L15)

	```solidity
	    function removeItemByIndex(uint256[] storage items, uint256 index) internal {
	```

- Found in src/markets/gamma/ArrayLib.sol [Line: 20](src/markets/gamma/ArrayLib.sol#L20)

	```solidity
	    function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {
	```

- Found in src/markets/gamma/GammaOrder.sol [Line: 115](src/markets/gamma/GammaOrder.sol#L115)

	```solidity
	    function hash(GammaOrder memory order) internal pure returns (bytes32) {
	```

- Found in src/markets/gamma/L2GammaDecoder.sol [Line: 51](src/markets/gamma/L2GammaDecoder.sol#L51)

	```solidity
	    function decodeGammaModifyParam(bytes32 args)
	```

- Found in src/markets/perp/PerpMarketLib.sol [Line: 178](src/markets/perp/PerpMarketLib.sol#L178)

	```solidity
	    function ratio(uint256 price1, uint256 price2) internal pure returns (uint256) {
	```

- Found in src/markets/perp/PerpMarketLib.sol [Line: 188](src/markets/perp/PerpMarketLib.sol#L188)

	```solidity
	    function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)
	```

- Found in src/markets/perp/PerpOrder.sol [Line: 54](src/markets/perp/PerpOrder.sol#L54)

	```solidity
	    function hash(PerpOrder memory order) internal pure returns (bytes32) {
	```

- Found in src/markets/perp/PerpOrderV3.sol [Line: 58](src/markets/perp/PerpOrderV3.sol#L58)

	```solidity
	    function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {
	```

- Found in src/markets/spot/SpotOrder.sol [Line: 45](src/markets/spot/SpotOrder.sol#L45)

	```solidity
	    function hash(SpotOrder memory order) internal pure returns (bytes32) {
	```

</details>



## L-12: Unused Custom Error

it is recommended that the definition be removed when custom error is unused

<details><summary>3 Found Instances</summary>


- Found in src/interfaces/IFillerMarket.sol [Line: 5](src/interfaces/IFillerMarket.sol#L5)

	```solidity
	    error SignerIsNotVaultOwner();
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 36](src/markets/perp/PerpMarketV1.sol#L36)

	```solidity
	    error TPSLConditionDoesNotMatch();
	```

- Found in src/markets/perp/PerpMarketV1.sol [Line: 38](src/markets/perp/PerpMarketV1.sol#L38)

	```solidity
	    error UpdateMarginMustNotBePositive();
	```
Recommendation
Emit an event for critical parameter changes.

</details>

## L-13: Missing events access control
```solidity
updateWhitelistFiller
```
 has no event, so it is difficult to track off-chain owner changes.

<details><summary>3 Found Instances</summary>

- Found in src/interfaces/IFillerMarket.sol [Line: 129](src/base/BaseMarketUpgradable.sol#L129)

	```solidity
	    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {
        	whitelistFiller = newWhitelistFiller;
    	}
	```

</details>

## L-14: Missing events access control
```solidity
updateWhitelistFiller
```
 has no event, so it is difficult to track off-chain owner changes.

<details><summary>3 Found Instances</summary>

- Found in src/interfaces/IFillerMarket.sol [Line: 129](src/base/BaseMarketUpgradable.sol#L129)

	```solidity
	    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {
        	whitelistFiller = newWhitelistFiller;
    	}
	```

</details>

## L-15: Missing events access control
```solidity
updateWhitelistFiller
```
 Dangerous usage of block.timestamp. block.timestamp can be manipulated by miners.

<details><summary>3 Found Instances</summary>

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 111](src/markets/gamma/GammaTradeMarketLib.sol#L111)

	```solidity
	    userPosition.expiration <= block.timestamp
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 146](src/markets/gamma/GammaTradeMarketLib.sol#L146)

	```solidity
	    currentTime <= startTime
	```

- Found in src/markets/gamma/GammaTradeMarketLib.sol [Line: 65](src/markets/gamma/GammaTradeMarketLib.sol#L65)

	```solidity
	    userPosition.hedgeInterval > 0 && userPosition.lastHedgedTime + userPosition.hedgeInterval <= block.timestamp
	```

Recommendation
Avoid relying on block.timestamp.

</details>

## L-16: Local variable shadowing
```solidity
updateWhitelistFiller
```
 Detection of shadowing using local variables. This will not lead to any error but can be confusing. Rename the local variables that shadow another component.

<details><summary>2 Found Instances</summary>

- Found whitelistFiller in 

src/markets/gamma/GammaTradeMarket.sol#69 [Line: 69](src/markets/gamma/GammaTradeMarket.sol#L69)
	```solidity
	    GammaTradeMarket.initialize(IPredyPool,address,address,address).whitelistFiller
	```

src/base/BaseMarketUpgradable.sol#23 [Line: 92](src/base/BaseMarketUpgradable.sol#L23)
	```solidity
	    PerpMarketV1.initialize(IPredyPool,address,address,address).whitelistFiller 
	```

- Found whitelistFiller in 

src/markets/perp/PerpMarketV1.sol#92 [Line: 92](src/markets/perp/PerpMarketV1.sol#L92)
	```solidity
	    PerpMarketV1.initialize(IPredyPool,address,address,address).whitelistFiller
	```

src/base/BaseMarketUpgradable.sol#23 [Line: 23](src/base/BaseMarketUpgradable.sol#L23)
	```solidity
	    BaseMarketUpgradable.whitelistFiller
	```

</details>