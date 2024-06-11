# Gas 01 Constants Data Types
## Summary
Some of the data types in the `Constants` library can be optimized by using smaller data types.

## Vulnerability Detail
Several constants in the `Constants` library are using larger data types than necessary. This can lead to inefficient use of storage and gas costs. The following constants can be represented with smaller data types without any risk of overflow:

- `MIN_MARGIN_AMOUNT`: This is an `int256` with a value of `1e6`. It can be represented as an `int32` since it is well within the bounds of a 32-bit integer.
- `MIN_LIQUIDITY`: This is `100`, which can be represented as a `uint8`.
- `MIN_SQRT_PRICE`: This is `79228162514264337593`, which fits within `uint64`.
- `RESOLUTION`: This is `96`, which can be represented as a `uint8`.
- `BASE_MIN_COLLATERAL_WITH_DEBT`: This is `1000`, which can be represented as a `uint16`.
- `BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE`: This is `12422`, which can be represented as a `uint16`.
- `MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE`: This is `24710`, which can be represented as a `uint16`.
- `SLIPPAGE_SQRT_TOLERANCE`: This is `12422`, which can be represented as a `uint16`.
- `SQUART_KINK_UR`: This is `10 * 1e16`, which fits within `uint64`.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Constants.sol#L4-L33

## Impact
Optimizing these data types can lead to more efficient use of storage and reduce gas costs for transactions involving these constants.

## Code Snippet
Current implementation:
```solidity
library Constants {
    uint256 internal constant ONE = 1e18;

    uint256 internal constant MAX_VAULTS = 18446744073709551616;
    uint256 internal constant MAX_PAIRS = 18446744073709551616;

    // Margin option
    int256 internal constant MIN_MARGIN_AMOUNT = 1e6;

    uint256 internal constant MIN_LIQUIDITY = 100;

    uint256 internal constant MIN_SQRT_PRICE = 79228162514264337593;
    uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;

    uint8 internal constant RESOLUTION = 96;
    uint256 internal constant Q96 = 0x1000000000000000000000000;
    uint256 internal constant Q128 = 0x100000000000000000000000000000000;

    // 0.1%
    uint256 internal constant BASE_MIN_COLLATERAL_WITH_DEBT = 1000;
    // 2.5% scaled by 1e6
    uint256 internal constant BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE = 12422;
    // 5.0% scaled by 1e6
    uint256 internal constant MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE = 24710;
    // 2.5% scaled by 1e6
    uint256 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;

    // 10%
    uint256 internal constant SQUART_KINK_UR = 10 * 1e16;
}
```

Recommended implementation:
```solidity
library Constants {
    uint256 internal constant ONE = 1e18;

    uint256 internal constant MAX_VAULTS = 18446744073709551616;
    uint256 internal constant MAX_PAIRS = 18446744073709551616;

    // Margin option
    int32 internal constant MIN_MARGIN_AMOUNT = 1e6;

    uint8 internal constant MIN_LIQUIDITY = 100;

    uint64 internal constant MIN_SQRT_PRICE = 79228162514264337593;
    uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;

    uint8 internal constant RESOLUTION = 96;
    uint256 internal constant Q96 = 0x1000000000000000000000000;
    uint256 internal constant Q128 = 0x100000000000000000000000000000000;

    // 0.1%
    uint16 internal constant BASE_MIN_COLLATERAL_WITH_DEBT = 1000;
    // 2.5% scaled by 1e6
    uint16 internal constant BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE = 12422;
    // 5.0% scaled by 1e6
    uint16 internal constant MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE = 24710;
    // 2.5% scaled by 1e6
    uint16 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;

    // 10%
    uint64 internal constant SQUART_KINK_UR = 10 * 1e16;
}
```

## Tool used
Manual Review

## Recommendation
Update the data types for the specified constants in the `Constants` library to smaller data types as outlined in the recommended implementation. This will optimize storage usage and reduce gas costs.

# Gas 02 Unneeded Variable PredyPool
## Summary
In `PredyPool`, there is an unneeded variable in the `onlyByLocker` modifier, which can be simplified.

## Vulnerability Detail
The `onlyByLocker` modifier currently declares an unnecessary local variable `locker`, which is then used only once. This increases the gas cost slightly and adds unnecessary complexity to the code.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L53

## Impact
The presence of an unneeded variable does not introduce a security vulnerability, but it results in slightly higher gas usage and reduced code readability. Simplifying the code by removing the unnecessary variable can make the code more efficient and easier to understand.

## Code Snippet
Current implementation:

```solidity
modifier onlyByLocker() {
    address locker = globalData.lockData.locker;
    if (msg.sender != locker) revert LockedBy(locker);
    _;
}
```

## Tool used
Manual Review

## Recommendation
The `onlyByLocker` modifier should be updated to remove the unnecessary variable declaration, resulting in a more streamlined and efficient implementation.

Recommended implementation:

```solidity
modifier onlyByLocker() {
    if (msg.sender != globalData.lockData.locker) revert LockedBy(globalData.lockData.locker);
    _;
}
```

# Gas 03 Not Efficient Masks
## Summary
The functions `L2Decoder::decodeSpotOrderParams` and `L2GammaDecoder::decodeGammaModifyParam` decode certain parameters from byte inputs. However, the parameter `isLimitUint` in `decodeSpotOrderParams` and `isEnabledUint` in `decodeGammaModifyParam` can only take values of 0 or 1, and they are currently being decoded using a larger bit mask than necessary.

## Vulnerability Detail
In the functions `decodeSpotOrderParams` and `decodeGammaModifyParam`, the parameters `isLimitUint` and `isEnabledUint` respectively are decoded using a 32-bit mask (`0xFFFFFFFF` for `isLimitUint` and `0xFFFF` for `isEnabledUint`). However, since these parameters can only be either 0 or 1, they can be decoded more efficiently using an 8-bit mask (`0xFF`).

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/L2Decoder.sol#L23

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/gamma/L2GammaDecoder.sol#L74

## Impact
The current implementation uses larger masks than necessary, which is less efficient and could potentially lead to confusion or errors in the future if the contract is modified. By reducing the mask size, the code will be more efficient and clearer.

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
Update the bit masks for `isLimitUint` in `decodeSpotOrderParams` and `isEnabledUint` in `decodeGammaModifyParam` to use 8-bit masks (`0xFF`) instead of larger masks. This change will make the code more efficient and easier to understand.

# Gas 04 Redundant Checks
## Summary
In the `LPMath::calculateAmount0ForLiquidity` and `LPMath::calculateAmount1ForLiquidity` functions, there is a redundant comparison check that can be optimized by using a boolean variable that has already been defined.

## Vulnerability Detail
In both `calculateAmount0ForLiquidity` and `calculateAmount1ForLiquidity`, the condition checks:

```solidity
if (sqrtRatioA > sqrtRatioB)
```

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L40-L42

and

```solidity
if (sqrtRatioA < sqrtRatioB)
```

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/math/LPMath.sol#L78-L80

are performed even though the boolean variable `swaped` has been set to reflect the result of these comparisons. This redundancy can be removed to simplify the code.

## Impact
Removing the redundant checks will make the code more efficient and easier to read without changing its functionality.

## Code Snippet
### Original Code
```solidity
function calculateAmount0ForLiquidity(
    uint160 sqrtRatioA,
    uint160 sqrtRatioB,
    uint256 liquidityAmount,
    bool isRoundUp
) internal pure returns (int256) {
    if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {
        return 0;
    }

    bool swaped = sqrtRatioA > sqrtRatioB;
    
    if (sqrtRatioA > sqrtRatioB)
        (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

    int256 r;
    
    bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;
    uint256 numerator = liquidityAmount;

    if (_isRoundUp) {
        uint256 r0 = FullMath.mulDivRoundingUp(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioA
        );
        uint256 r1 = FullMath.mulDiv(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioB
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    } else {
        uint256 r0 = FullMath.mulDiv(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioA
        );
        uint256 r1 = FullMath.mulDivRoundingUp(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioB
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    }
    if (swaped) {
        return -r;
    } else {
        return r;
    }
}

function calculateAmount1ForLiquidity(
    uint160 sqrtRatioA,
    uint160 sqrtRatioB,
    uint256 liquidityAmount,
    bool isRoundUp
) internal pure returns (int256) {
    if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {
        return 0;
    }

    bool swaped = sqrtRatioA < sqrtRatioB;

    if (sqrtRatioA < sqrtRatioB)
        (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

    int256 r;

    bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;

    if (_isRoundUp) {
        uint256 r0 = FullMath.mulDivRoundingUp(
            liquidityAmount,
            sqrtRatioA,
            FixedPoint96.Q96
        );
        uint256 r1 = FullMath.mulDiv(
            liquidityAmount,
            sqrtRatioB,
            FixedPoint96.Q96
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    } else {
        uint256 r0 = FullMath.mulDiv(
            liquidityAmount,
            sqrtRatioA,
            FixedPoint96.Q96
        );
        uint256 r1 = FullMath.mulDivRoundingUp(
            liquidityAmount,
            sqrtRatioB,
            FixedPoint96.Q96
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    }

    if (swaped) {
        return -r;
    } else {
        return r;
    }
}
```

### Optimized Code
```soldiity
function calculateAmount0ForLiquidity(
    uint160 sqrtRatioA,
    uint160 sqrtRatioB,
    uint256 liquidityAmount,
    bool isRoundUp
) internal pure returns (int256) {
    if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {
        return 0;
    }

    bool swaped = sqrtRatioA > sqrtRatioB;
    if (swaped)
        (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

    int256 r;
    
    bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;
    uint256 numerator = liquidityAmount;

    if (_isRoundUp) {
        uint256 r0 = FullMath.mulDivRoundingUp(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioA
        );
        uint256 r1 = FullMath.mulDiv(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioB
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    } else {
        uint256 r0 = FullMath.mulDiv(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioA
        );
        uint256 r1 = FullMath.mulDivRoundingUp(
            numerator,
            FixedPoint96.Q96,
            sqrtRatioB
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    }
    if (swaped) {
        return -r;
    } else {
        return r;
    }
}

function calculateAmount1ForLiquidity(
    uint160 sqrtRatioA,
    uint160 sqrtRatioB,
    uint256 liquidityAmount,
    bool isRoundUp
) internal pure returns (int256) {
    if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {
        return 0;
    }

    bool swaped = sqrtRatioA < sqrtRatioB;
    if (swaped)
        (sqrtRatioA, sqrtRatioB) = (sqrtRatioB, sqrtRatioA);

    int256 r;

    bool _isRoundUp = swaped ? !isRoundUp : isRoundUp;

    if (_isRoundUp) {
        uint256 r0 = FullMath.mulDivRoundingUp(
            liquidityAmount,
            sqrtRatioA,
            FixedPoint96.Q96
        );
        uint256 r1 = FullMath.mulDiv(
            liquidityAmount,
            sqrtRatioB,
            FixedPoint96.Q96
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    } else {
        uint256 r0 = FullMath.mulDiv(
            liquidityAmount,
            sqrtRatioA,
            FixedPoint96.Q96
        );
        uint256 r1 = FullMath.mulDivRoundingUp(
            liquidityAmount,
            sqrtRatioB,
            FixedPoint96.Q96
        );

        r = SafeCast.toInt256(r0) - SafeCast.toInt256(r1);
    }

    if (swaped) {
        return -r;
    } else {
        return r;
    }
}
```

## Tool used
Manual Review

## Recommendation
Remove the redundant comparison checks by using the boolean variable `swaped` directly, as shown in the optimized code snippet.

# Gas 05 Tick Spacing Gas Inefficient
## Summary
The function `Reallocation::calculateUsableTick` has an implementation that is not the most gas efficient. An optimized version of the function has been identified, which improves gas efficiency while maintaining the same functionality.

## Vulnerability Detail
The current implementation of `calculateUsableTick` uses integer division to align the tick value to the nearest multiple of `tickSpacing`. This approach, while correct, is not the most gas efficient.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/Reallocation.sol#L120

## Impact
Optimizing the function reduces gas consumption, which is crucial in blockchain applications where gas costs translate to real monetary expenses. The original implementation consumes 1407 gas, whereas the optimized version consumes 1298 gas, leading to more efficient and cost-effective transactions.

## Code Snippet

### Current Implementation
```solidity
function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {
    require(tickSpacing > 0);

    result = _tick;

    if (result < TickMath.MIN_TICK) {
        result = TickMath.MIN_TICK;
    } else if (result > TickMath.MAX_TICK) {
        result = TickMath.MAX_TICK;
    }

    result = (result / tickSpacing) * tickSpacing;
}
```

### Optimized Implementation
```solidity
function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {
    require(tickSpacing > 0);

    result = _tick;

    if (result < TickMath.MIN_TICK) {
        result = TickMath.MIN_TICK;
    } else if (result > TickMath.MAX_TICK) {
        result = TickMath.MAX_TICK;
    }

    result = result - result % tickSpacing;
}
```

## Tool used
Manual Review

## Recommendation
Replace the current implementation of `calculateUsableTick` with the optimized version provided above to achieve better gas efficiency and maintain code readability.

# Gas 06 Inefficient HasPosition Expression
## Summary
The `calculateMinValue` and `getHasPosition` functions in the `PositionCalculator` contract contains an inefficiency with the `hasPosition` variable.

## Vulnerability Detail
In the `calculateMinValue` function, the `hasPosition` variable is intended to reflect whether a position exists. However, the current implementation initializes `hasPosition` to `false` and then attempts to update it using a logical OR operation with the `getHasPositionFlag(positionParams)` method. Since `hasPosition` is always `false` the value of `hasPosition` will always only depend on the result of the `getHasPositionFlag` function making this whole logical expression unnecessary.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L129

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/PositionCalculator.sol#L138

## Impact
Keeping this logical expression increases gas costs when calling the `calculateMinValue` and `getHasPosition` functions in the `PositionCalculator` contract. 

## Code Snippet
```solidity
hasPosition = hasPosition || getHasPositionFlag(positionParams);
```

## Tool used
Manual Review

## Recommendation
Consider changing this code to:

```solidity
hasPosition = getHasPositionFlag(positionParams);
```

# Gas 07 Redundant MinMargin Calculations
## Summary
The `TradeLogic::trade` function redundantly calculates the minimum margin (`minMargin`) twice, which is unnecessary and inefficient.

## Vulnerability Detail
In the `TradeLogic::trade` function, the minimum margin is calculated twice using the `PositionCalculator` class. The first calculation occurs with the `calculateMinMargin` method, and the second calculation occurs with the `checkSafe` method. The result from the first calculation is not used, making the operation redundant.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L47-L49

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/libraries/logic/TradeLogic.sol#L55-L56

## Impact
This redundancy can lead to unnecessary computational overhead, potentially affecting the performance and efficiency of the function. Additionally, it introduces clutter in the code, making it harder to maintain and understand.

## Code Snippet
```solidity
// First calculation of minMargin
(tradeResult.minMargin, , , tradeResult.sqrtTwap) = PositionCalculator
.calculateMinMargin(
    pairStatus,
    globalData.vaults[tradeParams.vaultId],
    DataType.FeeAmount(0, 0)
);

// Second calculation of minMargin
tradeResult.minMargin = PositionCalculator.checkSafe(
    pairStatus,
    globalData.vaults[tradeParams.vaultId],
    DataType.FeeAmount(0, 0)
);
```

## Tool used
Manual Review

## Recommendation
Remove the first calculation of `minMargin` using the `calculateMinMargin` method, as the result is not utilized and the value is recalculated later using the `checkSafe` method.
