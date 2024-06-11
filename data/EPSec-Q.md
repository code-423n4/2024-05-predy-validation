# INFORMATIONAL

# Info 01 AddDelta Revert
## Summary
The `addDelta` function will revert if the absolute value of `b` is greater than `a` and `b` is less than 0.

## Vulnerability Detail
The function `addDelta` takes two parameters, `a` (an unsigned integer) and `b` (a signed integer). When `b` is negative and its absolute value exceeds `a`, the subtraction operation `a - uint256(-b)` causes an underflow, leading to a revert.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/Math.sol#L54-L60

## Impact
If `b` is negative and its absolute value is greater than `a`, the contract will revert, potentially causing disruptions in contract functionality and leading to failed transactions.

## Code Snippet
```solidity
function addDelta(uint256 a, int256 b) internal pure returns (uint256) {
    if (b >= 0) {
        return a + uint256(b);
    } else {
        return a - uint256(-b);
    }
}
```

## Tool used
Manual Review

## Recommendation
Add a check to ensure that `a` is greater than or equal to the absolute value of `b` when `b` is negative. This prevents the underflow condition and ensures the function executes correctly.

# Info 02 Unused Constructors
## Summary
The constructors in `GammaTradeMarket`, `PredyPool`, `BaseMarketUpgradeable`, `BaseHookCallbackUpgradable`, and `PerpMarketV1` are empty and therefore do not serve any purpose.

## Vulnerability Detail
In the mentioned contracts, the constructors are defined but contain no logic or initialization code, rendering them useless.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/GammaTradeMarket.sol#L67

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L68

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L36

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseHookCallbackUpgradable.sol#L13

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L90
## Impact
Having empty constructors in these contracts can lead to confusion and misunderstanding of the contract's initialization process. It may also be indicative of incomplete or improperly designed contract architecture.

## Code Snippet
None

## Tool used
Manual Review

## Recommendation
Remove the empty constructors from `GammaTradeMarket`, `PredyPool`, `BaseMarketUpgradeable`, `BaseHookCallbackUpgradable`, and `PerpMarketV1` to improve code clarity and maintainability.

# Info 03 Redundant Check
## Summary
In the `PredyPool` contract, there are redundant checks in the `withdrawProtocolRevenue` and `withdrawCreatorRevenue` functions.

## Vulnerability Detail
Within the `withdrawProtocolRevenue` and `withdrawCreatorRevenue` functions of the `PredyPool` contract, a check is performed to ensure the `amount` is greater than 0 before executing a transfer:

```solidity
if (amount > 0) {
    ERC20(pool.token).safeTransfer(msg.sender, amount);
}
```

This check is redundant because the function already includes a `require` statement that ensures the `amount` is greater than 0:

```solidity
require(amount > 0, "AZ");
```

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L186-L188

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L208-L210

## Impact
The redundant check unnecessarily complicates the code, making it less efficient and harder to read without providing any additional safety or functionality.

## Code Snippet
```solidity
require(amount > 0, "AZ");

if (amount > 0) {
    ERC20(pool.token).safeTransfer(msg.sender, amount);
}
```

## Tool used
Manual Review

## Recommendation
Remove the redundant check for `amount > 0` in the `withdrawProtocolRevenue` and `withdrawCreatorRevenue`functions, as the `require` statement already ensures this condition is met.

# Info 04 Ternary Operator
## Summary
The code in `L2Decoder::decodeSpotOrderParams` and `L2GammaDecoder::decodeGammaModifyParam` contains an unnecessary if-else statement to check the value of `isLimitUint` and `isEnabledUint` respectively. This can be simplified using a ternary operator.

## Vulnerability Detail
In both `decodeSpotOrderParams` and `decodeGammaModifyParam`, the boolean values `isLimit` and `isEnabled` are determined through an if-else statement based on the values of `isLimitUint` and `isEnabledUint`. This is a less concise approach and can be replaced with a ternary operator to make the code more readable and maintainable.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L26-L30

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L78-L82

## Impact
While this issue does not introduce a security vulnerability, it affects the code's readability and maintainability. Simplifying the code can make it easier for developers to understand and reduce the likelihood of errors in future modifications.

## Code Snippet
```solidity
function decodeSpotOrderParams(bytes32 args1, bytes32 args2)
	internal
	pure
	returns (
		bool isLimit,
		uint64 startTime,
		uint64 endTime,
		uint64 deadline,
		uint128 startAmount,
		uint128 endAmount
	)
{
	uint32 isLimitUint;

	assembly {
		deadline := and(args1, 0xFFFFFFFFFFFFFFFF)
		startTime := and(shr(64, args1), 0xFFFFFFFFFFFFFFFF)
		endTime := and(shr(128, args1), 0xFFFFFFFFFFFFFFFF)
		isLimitUint := and(shr(192, args1), 0xFFFFFFFF)
	}
	
	if (isLimitUint == 1) {
		isLimit = true;
	} else {
		isLimit = false;
	}

	assembly {
		startAmount := and(args2, 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF)
		endAmount := and(shr(128, args2), 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF)
	}
}
```

And:

```solidity
function decodeGammaModifyParam(bytes32 args)
	internal
	pure
	returns (
		bool isEnabled,
		uint64 expiration,
		uint32 hedgeInterval,
		uint32 sqrtPriceTrigger,
		uint32 minSlippageTolerance,
		uint32 maxSlippageTolerance,
		uint16 auctionPeriod,
		uint32 auctionRange
	)
{
	uint32 isEnabledUint = 0;

	assembly {
		expiration := and(args, 0xFFFFFFFFFFFFFFFF)
		hedgeInterval := and(shr(64, args), 0xFFFFFFFF)
		sqrtPriceTrigger := and(shr(96, args), 0xFFFFFFFF)
		minSlippageTolerance := and(shr(128, args), 0xFFFFFFFF)
		maxSlippageTolerance := and(shr(160, args), 0xFFFFFFFF)
		auctionRange := and(shr(192, args), 0xFFFFFFFF)
		isEnabledUint := and(shr(224, args), 0xFFFF)
		auctionPeriod := and(shr(240, args), 0xFFFF)
	}

	if (isEnabledUint == 1) {
		isEnabled = true;
	} else {
		isEnabled = false;
	}
}
```

## Tool used
Manual Review

## Recommendation
Replace the if-else statements in `decodeSpotOrderParams` and `decodeGammaModifyParam` with a ternary operator to determine the boolean values more concisely.

# Info 05 Wrong Function Visibility
## Summary
The functions `updatePoolOwner`, `updateFeeRatio`, `updatePriceOracle`, `updateAssetRiskParams`, and `updateIRMParams` are marked as `external` but should be `internal`.

## Vulnerability Detail
The functions mentioned are declared as `external`, which means they can be called from outside the contract. However, these functions are meant to be used internally within the contract and should not be exposed to external callers. Exposing these functions externally can lead to unauthorized modifications to the pool's parameters, potentially compromising the integrity and security of the contract.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L104

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L96

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L112

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L118

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L128-L132

## Impact
Allowing these functions to be called externally can result in unauthorized updates to critical parameters such as pool owner, fee ratio, price oracle, asset risk parameters, and interest rate model parameters. This could lead to severe security vulnerabilities, including unauthorized access and manipulation of sensitive data, financial loss, and disruption of the contract's intended functionality.

## Code Snippet
The current implementation of the functions:

```solidity
function updatePoolOwner(DataType.PairStatus storage _pairStatus, address _poolOwner) external {
    validatePoolOwner(_poolOwner);

    _pairStatus.poolOwner = _poolOwner;

    emit PoolOwnerUpdated(_pairStatus.id, _poolOwner);
}

function updateFeeRatio(DataType.PairStatus storage _pairStatus, uint8 _feeRatio) external {
    validateFeeRatio(_feeRatio);

    _pairStatus.feeRatio = _feeRatio;

    emit FeeRatioUpdated(_pairStatus.id, _feeRatio);
}

function updatePriceOracle(DataType.PairStatus storage _pairStatus, address _priceOracle) external {
    _pairStatus.priceFeed = _priceOracle;

    emit PriceOracleUpdated(_pairStatus.id, _priceOracle);
}

function updateAssetRiskParams(DataType.PairStatus storage _pairStatus, Perp.AssetRiskParams memory _riskParams) external {
    validateRiskParams(_riskParams);

    _pairStatus.riskParams = _riskParams;

    emit AssetRiskParamsUpdated(_pairStatus.id, _riskParams);
}

function updateIRMParams(
    DataType.PairStatus storage _pairStatus,
    InterestRateModel.IRMParams memory _quoteIrmParams,
    InterestRateModel.IRMParams memory _baseIrmParams
) external {
    validateIRMParams(_quoteIrmParams);
    validateIRMParams(_baseIrmParams);

    _pairStatus.quotePool.irmParams = _quoteIrmParams;
    _pairStatus.basePool.irmParams = _baseIrmParams;

    emit IRMParamsUpdated(_pairStatus.id, _quoteIrmParams, _baseIrmParams);
}
```

## Tool used
Manual Review

## Recommendation
Change the visibility of these functions from `external` to `internal` to ensure that they are only callable from within the contract, thereby preventing unauthorized access.

# Info 06 Missing Address(0) Checks
## Summary
The `UniswapSettlement` contract lacks checks for zero addresses in its constructor, which can lead to potential vulnerabilities if uninitialized addresses are used. This issue needs to be addressed to ensure the contract's security and proper functioning.

## Vulnerability Detail
The constructor of the `UniswapSettlement` contract initializes the `_swapRouter` and `_quoterV2` variables with the addresses passed as parameters. However, there are no checks to ensure that these addresses are not zero addresses (`address(0)`). If zero addresses are passed, it can lead to critical failures when the contract tries to interact with these addresses, causing the functions to fail or behave unexpectedly.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L10

## Impact
If zero addresses are used for `_swapRouter` or `_quoterV2`, any function call that interacts with these addresses will fail. This can cause the `UniswapSettlement` contract to malfunction, leading to potential loss of funds or denial of service.

## Code Snippet
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.17;

import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";
import {ERC20} from "@solmate/src/tokens/ERC20.sol";
import {ISwapRouter} from "@uniswap/v3-periphery/contracts/interfaces/ISwapRouter.sol";
import {IQuoterV2} from "@uniswap/v3-periphery/contracts/interfaces/IQuoterV2.sol";
import "../interfaces/ISettlement.sol";

contract UniswapSettlement is ISettlement {
    using SafeTransferLib for ERC20;

    ISwapRouter private immutable _swapRouter;
    IQuoterV2 private immutable _quoterV2;

    constructor(address swapRouterAddress, address quoterAddress) {
        require(swapRouterAddress != address(0), "Invalid swap router address");
        require(quoterAddress != address(0), "Invalid quoter address");
        _swapRouter = ISwapRouter(swapRouterAddress);
        _quoterV2 = IQuoterV2(quoterAddress);
    }

    function swapExactIn(
        address,
        address baseToken,
        bytes memory data,
        uint256 amountIn,
        uint256 amountOutMinimum,
        address recipient
    ) external override returns (uint256 amountOut) {
        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);
        ERC20(baseToken).approve(address(_swapRouter), amountIn);

        amountOut = _swapRouter.exactInput(
            ISwapRouter.ExactInputParams(data, recipient, block.timestamp, amountIn, amountOutMinimum)
        );
    }

    function swapExactOut(
        address quoteToken,
        address,
        bytes memory data,
        uint256 amountOut,
        uint256 amountInMaximum,
        address recipient
    ) external override returns (uint256 amountIn) {
        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);
        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);

        amountIn = _swapRouter.exactOutput(
            ISwapRouter.ExactOutputParams(data, recipient, block.timestamp, amountOut, amountInMaximum)
        );

        if (amountInMaximum > amountIn) {
            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);
        }
    }

    function quoteSwapExactIn(bytes memory data, uint256 amountIn) external override returns (uint256 amountOut) {
        (amountOut,,,) = _quoterV2.quoteExactInput(data, amountIn);
    }

    function quoteSwapExactOut(bytes memory data, uint256 amountOut) external override returns (uint256 amountIn) {
        (amountIn,,,) = _quoterV2.quoteExactOutput(data, amountOut);
    }
}
```

## Tool used
Manual Review

## Recommendation
Add checks for zero addresses in the constructor to ensure that the `_swapRouter` and `_quoterV2` variables are initialized with valid addresses. This can be done using `require` statements as shown in the updated code snippet.

# Info 07 Redundant Variables
## Summary
In the `Perp` contract's `updateRebalanceInterestGrowth` function and the `InterestRateModel` library, constants are defined and used inconsistently. Specifically, `1e18` is used directly instead of a predefined constant, and there is a redundant constant `_ONE` that should be replaced with `Constants.ONE`.

## Vulnerability Detail
1. **In `Perp`**:
   - In the `updateRebalanceInterestGrowth` function, the constant `1e18` is used directly to scale fee growths. This practice is prone to errors and inconsistencies, especially if the value of the constant needs to be changed or reused elsewhere.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L165-L172

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Perp.sol#L611-L613
   
2. **In `InterestRateModel`**:
   - The `InterestRateModel` library defines a private constant `_ONE` with a value of `1e18`. However, there is already a `Constants.ONE` that serves the same purpose. Using `Constants.ONE` instead of `_ONE` would reduce redundancy and improve code maintainability.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/InterestRateModel.sol#L16

## Impact
Using `1e18` directly in the `updateRebalanceInterestGrowth` function and having a redundant constant in the `InterestRateModel` library could lead to inconsistencies and maintenance challenges. Centralizing the constant definition in `Constants.ONE` improves code clarity and reduces the risk of errors.

## Code Snippet
```solidity
/// @notice Settle the interest on rebalance positions up to this block and update the rebalance fee growth value
function updateRebalanceInterestGrowth(
    DataType.PairStatus memory _pairStatus,
    SqrtPerpAssetStatus storage _sqrtAssetStatus
) internal {
    // settle the interest on rebalance position
    // fee growths are scaled by Constants.ONE
    if (_sqrtAssetStatus.lastRebalanceTotalSquartAmount > 0) {
        _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(
            _sqrtAssetStatus.rebalancePositionBase
        ) * Constants.ONE / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

        _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(
            _sqrtAssetStatus.rebalancePositionQuote
        ) * Constants.ONE / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);
    }
}
```

And:

```solidity
library InterestRateModel {
    struct IRMParams {
        uint256 baseRate;
        uint256 kinkRate;
        uint256 slope1;
        uint256 slope2;
    }
    
    // Removed redundant constant _ONE
    // uint256 private constant _ONE = 1e18;

    function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)
        internal
        pure
        returns (uint256)
    {
        uint256 ir = irmParams.baseRate;

        if (utilizationRatio <= irmParams.kinkRate) {
            ir += (utilizationRatio * irmParams.slope1) / Constants.ONE;
        } else {
            ir += (irmParams.kinkRate * irmParams.slope1) / Constants.ONE;
            ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / Constants.ONE;
        }

        return ir;
    }
}
```

## Tool used
Manual Review

## Recommendation
- Update the `updateRebalanceInterestGrowth` function to use `Constants.ONE` instead of `1e18`.
- Remove the redundant `_ONE` constant from the `InterestRateModel` library and replace its usage with `Constants.ONE`.

# Info 08 Unused Enum Value
## Summary
Unused `TRADE` enum value in `CallbackSource`.

## Vulnerability Detail
In the `PerpMarketV1` contract, the `CallbackSource.TRADE` enum value is defined but never used in the code. This indicates that the `TRADE` value is redundant and can be removed to simplify the code.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketV1.sol#L51

## Impact
Leaving unused code in the contract can lead to confusion and maintainability issues. Removing such code improves clarity and reduces potential points of failure or misunderstanding.

## Code Snippet
```solidity
enum CallbackSource {
	TRADE,
	TRADE3,
	QUOTE,
	QUOTE3
}
```

## Tool used
Manual Review

## Recommendation
Remove the unused `TRADE` value from the `CallbackSource` enum to clean up the code and improve maintainability.

# Info 09 Logical Simplification
## Summary
The audit identified potential improvements in the `PerpMarketLib` library by simplifying conditional statements in three functions: `validateMarketOrder`, `validateStopPrice`, and `validateLimitPrice`. These simplifications enhance code readability and maintainability without changing the logic or functionality.

## Vulnerability Detail
In `PerpMarketLib`, three functions contain if statements that can be simplified:

1. `validateMarketOrder`:
   The current implementation uses nested conditions to check if the trade order is valid. This can be condensed into a single line for better clarity.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L199-L207

2. `validateStopPrice`:
   Similar to `validateMarketOrder`, the conditions in this function can be simplified to improve readability while preserving the original checks.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L155-L173

3. `validateLimitPrice`:
   The conditional logic can also be streamlined in this function to make the code more straightforward.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L123-L133

## Impact
The suggested changes do not affect the functionality or security of the code. The primary benefit is improved code readability and maintainability, making it easier for developers to understand and manage the codebase.

## Code Snippet
### Original Code
#### `validateMarketOrder`
```solidity
if (tradeAmount != 0) {
	if (tradeAmount > 0 && decayedPrice < tradePrice) {
		return false;
	}

	if (tradeAmount < 0 && decayedPrice > tradePrice) {
		return false;
	}
}
```
#### `validateStopPrice`
```solidity
if (tradeAmount > 0) {
	// buy
	if (stopPrice > oraclePrice) {
		return false;
	}

	if (tradePrice > Bps.upper(oraclePrice, decayedSlippageTorelance)) {
		return false;
	}
} else if (tradeAmount < 0) {
	// sell
	if (stopPrice < oraclePrice) {
		return false;
	}

	if (tradePrice < Bps.lower(oraclePrice, decayedSlippageTorelance)) {
		return false;
	}
}
```

#### `validateLimitPrice`
```solidity
if (tradeAmount == 0) {
	return false;
}

if (tradeAmount > 0 && limitPrice < tradePrice) {
	return false;
}

if (tradeAmount < 0 && limitPrice > tradePrice) {
	return false;
}
```

### Simplified Code
#### `validateMarketOrder`
```solidity
if ((tradeAmount > 0 && decayedPrice < tradePrice) || (tradeAmount < 0 && decayedPrice > tradePrice)) {
    return false;
}
```
#### `validateStopPrice`
```solidity
if (tradeAmount > 0 && (stopPrice > oraclePrice || tradePrice > Bps.upper(oraclePrice, decayedSlippageTorelance))) {
    return false;
} else if (tradeAmount < 0 && (stopPrice < oraclePrice || tradePrice < Bps.lower(oraclePrice, decayedSlippageTorelance))) {
    return false;
}
```

#### `validateLimitPrice`
```solidity
if (tradeAmount == 0 || (tradeAmount > 0 && limitPrice < tradePrice) || (tradeAmount < 0 && limitPrice > tradePrice)) {
	return false;
}
```

## Tool used
Manual Review

## Recommendation
Refactor the conditional statements in the `validateMarketOrder`, `validateStopPrice`, and `validateLimitPrice`functions as shown in the simplified code snippets. This will enhance code readability and maintainability without affecting functionality.

# LOW

# Low 01 Wrong Max Check
## Summary
The function `AddPairLogic::addPair` contains a logic error that could result in the contract having one less pair than intended.

## Vulnerability Detail
The issue lies in the requirement check within the `addPair` function. The current check uses the less than (`<`) operator, which fails to account for the maximum allowed pairs correctly. This causes the function to prevent the creation of the maximum allowable pairs, effectively resulting in one less pair being added to the contract.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L60

## Impact
This vulnerability limits the number of pairs that can be added to the contract to one less than the intended maximum, potentially affecting the functionality and scalability of the contract.

## Code Snippet
```solidity
require(pairId < Constants.MAX_PAIRS, "MAXP");
```

## Tool used
Manual Review

## Recommendation
Update the requirement check in the `addPair` function to use the less than or equal to (`<=`) operator instead of the less than (`<`) operator:

```solidity
require(pairId <= Constants.MAX_PAIRS, "MAXP");
```

# Low 02 Missing Validation
## Summary
The `AddPairLogic` contract has several missing validation checks in its functions, which could lead to improper handling and potential vulnerabilities.

## Vulnerability Detail
1. **Missing Validation in _storePairStatus**: The function `_storePairStatus` lacks a check for `poolOwner`. This check is necessary to ensure the integrity of the `poolOwner` variable.
2. **Incomplete Validation for IRM Parameters**: There are missing checks for `IrmParms` within the function. The parameters `quoteIrmParams` and `baseIrmParams` should be validated.
3. **Missing Validation in updatePriceOracle**: The parameter `_priceOracle` in the `AddPairLogic::updatePriceOracle` function is not checked, which could lead to the use of an invalid oracle address.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L142-L149

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/AddPairLogic.sol#L112

## Impact
1. **Unchecked poolOwner**: Without validation, `poolOwner` could be improperly set or altered, leading to unauthorized control over the pool.
2. **Unchecked IRM Parameters**: Failing to validate IRM parameters could result in incorrect or harmful configurations being applied.
3. **Unchecked Price Oracle**: An invalid `_priceOracle` address could result in incorrect price data being used, affecting the accuracy and reliability of the system.

## Code Snippet
```solidity
function _storePairStatus(
	address quoteToken,
	mapping(uint256 => DataType.PairStatus) storage _pairs,
	uint256 _pairId,
	address _tokenAddress,
	bool _isQuoteZero,
	AddPairParams memory _addPairParam
) internal {
	validateRiskParams(_addPairParam.assetRiskParams);
	validateFeeRatio(_addPairParam.fee);

	require(_pairs[_pairId].id == 0, "AAA");

	_pairs[_pairId] = DataType.PairStatus(
		_pairId,
		quoteToken,
		_addPairParam.poolOwner,
		Perp.AssetPoolStatus(
			quoteToken,
			deploySupplyToken(quoteToken),
			ScaledAsset.createAssetStatus(),
			_addPairParam.quoteIrmParams,
			0,
			0
		),
		Perp.AssetPoolStatus(
			_tokenAddress,
			deploySupplyToken(_tokenAddress),
			ScaledAsset.createAssetStatus(),
			_addPairParam.baseIrmParams,
			0,
			0
		),
		_addPairParam.assetRiskParams,
		Perp.createAssetStatus(
			_addPairParam.uniswapPool,
			-_addPairParam.assetRiskParams.rangeSize,
			_addPairParam.assetRiskParams.rangeSize
		),
		_addPairParam.priceFeed,
		_isQuoteZero,
		_addPairParam.allowlistEnabled,
		_addPairParam.fee,
		block.timestamp
	);

	emit AssetRiskParamsUpdated(_pairId, _addPairParam.assetRiskParams);
	emit IRMParamsUpdated(_pairId, _addPairParam.quoteIrmParams, _addPairParam.baseIrmParams);
}

function updatePriceOracle(DataType.PairStatus storage _pairStatus, address _priceOracle) external {
	_pairStatus.priceFeed = _priceOracle;

	emit PriceOracleUpdated(_pairStatus.id, _priceOracle);
}
```

## Tool used
Manual Review

## Recommendation
1. Add a validation check for `poolOwner` in the `_storePairStatus` function;
2. Ensure that IRM parameters are validated;
3. Add a validation check for `_priceOracle` in the `updatePriceOracle` function.

# Low 03 DoS By Casting
## Summary
A potential denial of service (DoS) vulnerability was identified in the `BaseMarketUpgradeable::decodeParamsV3` and `LiquidationLogic::liquidate` functions. This vulnerability arises when certain integer casting operations result in overflows, causing the functions to revert.

## Vulnerability Detail
The `decodeParamsV3` function can cause a DoS when the `fee` value is large enough to overflow during casting from `uint256` to `int256`. This is demonstrated by the following code, which shows that casting the maximum `uint256` value to `int256` results in a revert:

```solidity
function revertsDueToOverflow() external pure returns (int256) {
    return int256(type(uint256).max);
}
```

Similarly, in the `liquidate` function, a DoS can occur due to an overflow when `remainingMargin` is cast from `uint256`to `int256`.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/base/BaseMarketUpgradable.sol#L85

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/LiquidationLogic.sol#L62-L63

## Impact
The vulnerability can lead to a denial of service, making the contract unusable under specific conditions where large values are involved in the calculations, particularly with the `fee` and `remainingMargin` values. This can disrupt the normal operation of the contract and affect all users.

## Code Snippet
### decodeParamsV3 Function
```solidity
function decodeParamsV3(
	bytes memory settlementData,
	int256 baseAmountDelta
) internal pure returns (SettlementCallbackLib.SettlementParams memory) {
	SettlementParamsV3Internal memory settlementParamsV3 = abi.decode(
		settlementData,
		(SettlementParamsV3Internal)
	);

	uint256 tradeAmountAbs = Math.abs(baseAmountDelta);

	uint256 fee = (settlementParamsV3.feePrice * tradeAmountAbs) /
		Constants.Q96;

	if (fee < settlementParamsV3.minFee) {
		fee = settlementParamsV3.minFee;
	}

	uint256 maxQuoteAmount = (settlementParamsV3.maxQuoteAmountPrice *
		tradeAmountAbs) / Constants.Q96;
	uint256 minQuoteAmount = (settlementParamsV3.minQuoteAmountPrice *
		tradeAmountAbs) / Constants.Q96;

	return
		SettlementCallbackLib.SettlementParams(
			settlementParamsV3.filler,
			settlementParamsV3.contractAddress,
			settlementParamsV3.encodedData,
			baseAmountDelta > 0 ? minQuoteAmount : maxQuoteAmount,
			settlementParamsV3.price,
			int256(fee)
		);
}
```

### liquidate Function
```solidity
 function liquidate(
	uint256 vaultId,
	uint256 closeRatio,
	GlobalDataLibrary.GlobalData storage globalData,
	bytes memory settlementData
) external returns (IPredyPool.TradeResult memory tradeResult) {
	require(closeRatio > 0 && closeRatio <= 1e18, "ICR");
	DataType.Vault storage vault = globalData.vaults[vaultId];
	DataType.PairStatus storage pairStatus = globalData.pairs[vault.openPosition.pairId];

	// update interest growth
	ApplyInterestLib.applyInterestForToken(globalData.pairs, vault.openPosition.pairId);

	// update rebalance interest growth
	Perp.updateRebalanceInterestGrowth(pairStatus, pairStatus.sqrtAssetStatus);

	// Checks the vault is danger
	(uint256 sqrtOraclePrice, uint256 slippageTolerance) =
		checkVaultIsDanger(pairStatus, vault, globalData.rebalanceFeeGrowthCache);

	IPredyPool.TradeParams memory tradeParams = IPredyPool.TradeParams(
		vault.openPosition.pairId,
		vaultId,
		-vault.openPosition.perp.amount * int256(closeRatio) / 1e18,
		-vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,
		""
	);

	tradeResult = Trade.trade(globalData, tradeParams, settlementData);

	vault.margin += tradeResult.fee + tradeResult.payoff.perpPayoff + tradeResult.payoff.sqrtPayoff;

	tradeResult.sqrtTwap = sqrtOraclePrice;

	bool hasPosition;

	(tradeResult.minMargin,, hasPosition,) =
		PositionCalculator.calculateMinMargin(pairStatus, vault, DataType.FeeAmount(0, 0));

	// Check if the price is within the slippage tolerance range to ensure that the price does not become
	// excessively favorable to the liquidator.
	SlippageLib.checkPrice(
		sqrtOraclePrice,
		tradeResult,
		slippageTolerance,
		tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE
	);

	uint256 sentMarginAmount = 0;

	if (!hasPosition) {
		int256 remainingMargin = vault.margin;

		if (remainingMargin > 0) {
			if (vault.recipient != address(0)) {
				// Send the remaining margin to the recipient.
				vault.margin = 0;

				sentMarginAmount = uint256(remainingMargin);

				ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);
			}
		} else if (remainingMargin < 0) {
			vault.margin = 0;

			// To prevent the liquidator from unfairly profiting through arbitrage trades in the AMM and passing losses onto the protocol,
			// any losses that cannot be covered by the vault must be compensated by the liquidator
			ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));
		}
	}

	emit PositionLiquidated(
		tradeParams.vaultId,
		tradeParams.pairId,
		tradeParams.tradeAmount,
		tradeParams.tradeAmountSqrt,
		tradeResult.payoff,
		tradeResult.fee,
		sentMarginAmount
	);
}
```

## Tool used
Manual Review

## Recommendation
To mitigate this issue, ensure that any `uint256` to `int256` casting is preceded by a check to confirm that the `uint256`value is within the range that can be safely cast to `int256`. This will prevent overflows and the subsequent DoS vulnerability.

# Low 04 Poor NatSpec
## Summary
The protocol lacks adequate NatSpec documentation, making it difficult for developers and auditors to understand and improve the code.

## Vulnerability Detail
The absence of comprehensive NatSpec documentation for the functions in the files under review hinders the ability of new developers and auditors to effectively work with the code. NatSpec comments provide essential explanations and context, which are crucial for understanding the purpose and functionality of each function.

## Impact
Poor NatSpec documentation can lead to misunderstandings, errors, and inefficiencies in the development and auditing process. This increases the likelihood of bugs and vulnerabilities being introduced or overlooked, thereby compromising the security and maintainability of the protocol.

## Code Snippet
None

## Tool used
Manual Review

## Recommendation
Consider adding detailed NatSpec documentation for every function in the files in scope to enhance clarity and facilitate future improvements and audits.

# Low 05 Wrong Require Statement
## Summary
The `require` statement in the `ScaledAsset::getAssetFee` function uses `>=` to check `accountState.positionAmount`, but the only time this function is called is when `positionAmount` is greater than 0. So it cannot be called with `positionAmount` equal to 0.

## Vulnerability Detail
In the `ScaledAsset::getAssetFee` function, the `require` statement checks if `accountState.positionAmount` is greater than or equal to zero. However, this is redundant because `positionAmount` is always greater than zero when this function is called, as evidenced by the condition in `ScaledAsset::computeUserFee`.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L135-L136

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/ScaledAsset.sol#L160

## Impact
The redundant check in the `getAssetFee` function can lead to unnecessary gas consumption. While this might seem minor, optimizing smart contract code to avoid redundant checks can contribute to more efficient and cost-effective contract execution.

## Code Snippet
```solidity
function computeUserFee(
    ScaledAsset.AssetStatus memory _assetStatus,
    ScaledAsset.UserStatus memory _userStatus
) internal pure returns (int256 interestFee) {
    if (_userStatus.positionAmount > 0) {
        interestFee = (getAssetFee(_assetStatus, _userStatus)).toInt256();
    } else {
        interestFee = -(getDebtFee(_assetStatus, _userStatus)).toInt256();
    }
}

function getAssetFee(
    AssetStatus memory tokenState,
    UserStatus memory accountState
) internal pure returns (uint256) {
    require(accountState.positionAmount >= 0, "S1");

    return
        FixedPointMathLib.mulDivDown(
            tokenState.assetGrowth - accountState.lastFeeGrowth,
            // never overflow
            uint256(accountState.positionAmount),
            Constants.ONE
        );
}
```

## Tool used
Manual Review

## Recommendation
Change the redundant `require(accountState.positionAmount >= 0, "S1");` statement from the `getAssetFee`function to `require(accountState.positionAmount > 0, "S1");`. The `positionAmount` should be greater than 0 when calling `getAssetFee`.

# Low 06 Missing Address 0 Check
## Summary
The `SupplyToken` constructor lacks an `address(0)` check for the controller address. Additionally, there is no mechanism to change the controller, potentially bricking the protocol.

## Vulnerability Detail
In the `SupplyToken` constructor, there is no validation to ensure that the `controller` address is not set to the zero address. This can lead to assigning a null address to the controller, which is problematic. Furthermore, once set, the controller address cannot be changed, making the protocol inflexible and potentially inoperative if an incorrect address is assigned initially.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L15-L19

## Impact
If the `controller` address is set to the zero address upon deployment, the protocol could become inoperative. Since there is no way to update the controller address after deployment, this issue could permanently brick the entire protocol.

## Code Snippet
```solidity
constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)
    ERC20(_name, _symbol, __decimals)
{
    _controller = controller;
}
```

## Tool used
Manual Review

## Recommendation
1. Add a check in the constructor to ensure the `controller` address is not the zero address.
2. Implement a function to allow updating the `controller` address post-deployment, ensuring it includes appropriate access controls.

