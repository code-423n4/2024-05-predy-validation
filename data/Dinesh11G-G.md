## Use != 0 instead of > 0 for Unsigned Integer Comparison
Description
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

```
2024-05-predy/src/lens/GammaTradeMarketQuoter.sol::33 => if (reason.length < 192) {
2024-05-predy/src/lens/PerpMarketQuoter.sol::35 => if (reason.length < 192) {
2024-05-predy/src/lens/PredyPoolQuoter.sol::19 => if (data.length > 0) {
2024-05-predy/src/lens/PredyPoolQuoter.sol::147 => if (reason.length < 320) {
2024-05-predy/src/lens/PredyPoolQuoter.sol::19 => if (data.length > 0) {
```









## Use immutable for OpenZeppelin AccessControl's Roles Declarations
Description
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

```
2024-05-predy/src/libraries/orders/OrderInfoLib.sol::14 => bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);
2024-05-predy/src/markets/gamma/GammaOrder.sol::39 => bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);
2024-05-predy/src/markets/gamma/GammaOrder.sol::44 => return keccak256(
2024-05-predy/src/markets/gamma/GammaOrder.sol::99 => bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);
2024-05-predy/src/markets/gamma/GammaOrder.sol::116 => return keccak256(
2024-05-predy/src/markets/perp/PerpOrder.sol::44 => bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);
2024-05-predy/src/markets/perp/PerpOrderV3.sol::46 => bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);
```

## Use Shift Right/Left instead of Division/Multiplication if possible
Description
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

```
2024-05-predy/src/libraries/ApplyInterestLib.sol::71 => poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;
2024-05-predy/src/libraries/ApplyInterestLib.sol::72 => poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;
2024-05-predy/src/libraries/Reallocation.sol::51 => int24 previousCenterTick = (sqrtAssetStatus.tickLower + sqrtAssetStatus.tickUpper) / 2;
2024-05-predy/src/libraries/Reallocation.sol::67 => upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;
2024-05-predy/src/libraries/Reallocation.sol::80 => lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;
```
