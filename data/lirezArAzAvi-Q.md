## Medium Issues


*Total <b>11</b> instances over <b>4</b> issues:*

|ID|Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Return values of `approve()` not checked | 4 |
| [M-2](#M-2) | Usage of `slot0` is extremely easy to manipulate | 5 |
| [M-3](#M-3) | No check if L2 sequencer is down | 1 |
| [M-4](#M-4) | latestRoundData() has no check for round completeness | 1 |

## Low Issues


*Total <b>247</b> instances over <b>27</b> issues:*

|ID|Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Missing zero address check in functions with address parameters | 40 |
| [L-2](#L-2) | Use increase/decrease allowance instead of `approve()`/`safeApprove()` | 4 |
| [L-3](#L-3) | Array is `push()`ed but not `pop()`ed | 1 |
| [L-4](#L-4) | Division by zero not prevented | 2 |
| [L-5](#L-5) | Consider implementing two-step procedure for updating protocol addresses | 4 |
| [L-6](#L-6) | Constructor / initialization function lacks parameter validation | 8 |
| [L-7](#L-7) | Code does not follow the best practice of check-effects-interaction | 1 |
| [L-8](#L-8) | Functions calling contracts/addresses with transfer hooks are missing reentrancy guards | 2 |
| [L-9](#L-9) | Governance functions should be controlled by time locks | 1 |
| [L-10](#L-10) | Lack of `_disableInitializers` call to prevent uninitialized contracts | 3 |
| [L-11](#L-11) | Large approvals may not work with some `ERC20` tokens | 4 |
| [L-12](#L-12) | Loss of precision in divisions | 12 |
| [L-13](#L-13) | Missing checks for state variable assignments | 15 |
| [L-14](#L-14) | Missing zero address check in constructor | 5 |
| [L-15](#L-15) | Consider some checks for `address(0)` when setting address state variables | 11 |
| [L-16](#L-16) | Check storage gap for upgradable contracts | 3 |
| [L-17](#L-17) | Multiplication on the result of a division | 1 |
| [L-18](#L-18) | Solidity version 0.8.20 or above may not work on other chains due to PUSH0 | 55 |
| [L-19](#L-19) | Tokens may be minted to `address(0)` | 1 |
| [L-20](#L-20) | Unsafe downcast | 6 |
| [L-21](#L-21) | Using zero as a parameter | 14 |
| [L-22](#L-22) | Missing zero address check in initializer | 4 |
| [L-23](#L-23) | Centralization Risk for trusted owners | 26 |
| [L-24](#L-24) | Initializers could be front-run | 3 |
| [L-25](#L-25) | Chainlink answer is not compared against min/max values | 1 |
| [L-26](#L-26) | Library has public/external functions | 18 |
| [L-27](#L-27) | The call `abi.encodeWithSelector` is not type safe | 2 |

## Medium Issues

<a name="M-1"></a> 
### [M-1] Return values of `approve()` not checked
Not all IERC20 implementations `revert()` when there's a failure in `approve()`. The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There are <b>4</b> instances of this issue:*
```solidity
File: ./src/base/SettlementCallbackLib.sol

111:         ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);

158:         ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);

```

```solidity
File: ./src/settlements/UniswapSettlement.sol

31:         ERC20(baseToken).approve(address(_swapRouter), amountIn);

47:         ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);

```


<a name="M-2"></a> 
### [M-2] Usage of `slot0` is extremely easy to manipulate

*There are <b>5</b> instances of this issue:*
```solidity
File: ./src/libraries/Perp.sol

206:         (uint160 currentSqrtPrice, int24 currentTick,,,,,) = IUniswapV3Pool(_sqrtAssetStatus.uniswapPool).slot0();

```

```solidity
File: ./src/libraries/Reallocation.sol

93:         (, int24 currentTick,,,,,) = IUniswapV3Pool(sqrtAssetStatus.uniswapPool).slot0();

```

```solidity
File: ./src/libraries/UniHelper.sol

14:         (sqrtPrice,,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();

49:             (,, uint16 index, uint16 cardinality,,,) = uniswapPool.slot0();

104:         (, int24 tickCurrent,,,,,) = IUniswapV3Pool(uniswapPoolAddress).slot0();

```


<a name="M-3"></a> 
### [M-3] No check if L2 sequencer is down
Using Chainlink in L2 chains such as Arbitrum/Optimism/Base/Metis requires to check if the sequencer is down to avoid prices from looking like they are fresh although they are not.

*There is one instance of this issue:*
```solidity
File: ./src/PriceFeed.sol

46:         (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

```


<a name="M-4"></a> 
### [M-4] latestRoundData() has no check for round completeness
No check for round completeness could lead to stale prices and wrong price return value, or outdated price. The functions rely on accurate price feed might not work as expected, sometimes can lead to fund loss.

*There is one instance of this issue:*
```solidity
File: ./src/PriceFeed.sol

46:         (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

```



## Low Issues

<a name="L-1"></a> 
### [L-1] Missing zero address check in functions with address parameters
Adding a zero address check for each address type parameter can prevent errors.

<details>
<summary>
There are <b>40</b> instances (click to show):
</summary>

```solidity
File: ./src/PredyPool.sol

/// @audit missing zero check for `uniswapFactory`
70:     function initialize(address uniswapFactory) public initializer {

/// @audit missing zero check for `poolOwner`
157:     function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {

/// @audit missing zero check for `priceFeed`
167:     function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {

/// @audit missing zero check for `recipient`
286:     function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {

/// @audit missing zero check for `trader`
300:     function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {

/// @audit missing zero check for `to`
329:     function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {

```

```solidity
File: ./src/PriceFeed.sol

/// @audit missing zero check for `quotePrice`
18:     function createPriceFeed(address quotePrice, bytes32 priceId, uint256 decimalsDiff) external returns (address) {

```

```solidity
File: ./src/base/BaseHookCallbackUpgradable.sol

/// @audit missing zero check for `predyPool`
20:     function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit missing zero check for `quoteToken`
/// @audit missing zero check for `baseToken`
28:     function predySettlementCallback(
            address quoteToken,
            address baseToken,
            bytes memory settlementData,
            int256 baseAmountDelta
        ) external onlyPredyPool {

/// @audit missing zero check for `filler`
63:     function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)
            internal
            pure
            returns (bytes memory)
        {

/// @audit missing zero check for `newWhitelistFiller`
84:     function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {

/// @audit missing zero check for `settlementContractAddress`
92:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {

/// @audit missing zero check for `entryTokenAddress`
104:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

/// @audit missing zero check for `_whitelistFiller`
38:     function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
            internal
            onlyInitializing
        {

/// @audit missing zero check for `quoteToken`
/// @audit missing zero check for `baseToken`
49:     function predySettlementCallback(
            address quoteToken,
            address baseToken,
            bytes memory settlementData,
            int256 baseAmountDelta
        ) external onlyPredyPool {

/// @audit missing zero check for `filler`
105:     function _getSettlementDataFromV3(IFillerMarket.SettlementParamsV3 memory settlementParams, address filler)
             internal
             pure
             returns (bytes memory)
         {

/// @audit missing zero check for `newWhitelistFiller`
128:     function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {

/// @audit missing zero check for `settlementContractAddress`
140:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {

/// @audit missing zero check for `entryTokenAddress`
152:     function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {

```

```solidity
File: ./src/base/SettlementCallbackLib.sol

/// @audit missing zero check for `baseToken`
40:     function execSettlement(
            IPredyPool predyPool,
            address quoteToken,
            address baseToken,
            SettlementParams memory settlementParams,
            int256 baseAmountDelta
        ) internal {

/// @audit missing zero check for `predyPool`
/// @audit missing zero check for `quoteToken`
/// @audit missing zero check for `baseToken`
60:     function execSettlementInternal(
            IPredyPool predyPool,
            address quoteToken,
            address baseToken,
            SettlementParams memory settlementParams,
            int256 baseAmountDelta
        ) internal {

/// @audit missing zero check for `sender`
90:     function sell(
            IPredyPool predyPool,
            address quoteToken,
            address baseToken,
            SettlementParams memory settlementParams,
            address sender,
            uint256 price,
            uint256 sellAmount
        ) internal {

/// @audit missing zero check for `sender`
137:     function buy(
             IPredyPool predyPool,
             address quoteToken,
             address baseToken,
             SettlementParams memory settlementParams,
             address sender,
             uint256 price,
             uint256 buyAmount
         ) internal {

```

```solidity
File: ./src/libraries/Perp.sol

/// @audit missing zero check for `uniswapPool`
119:     function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)
             internal
             pure
             returns (SqrtPerpAssetStatus memory)
         {

/// @audit missing zero check for `_controllerAddress`
332:     function getAvailableLiquidityAmount(
             address _controllerAddress,
             address _uniswapPool,
             int24 _tickLower,
             int24 _tickUpper
         ) internal view returns (uint128) {
             bytes32 positionKey = PositionKey.compute(_controllerAddress, _tickLower, _tickUpper);

```

```solidity
File: ./src/libraries/logic/AddPairLogic.sol

/// @audit missing zero check for `uniswapFactory`
44:     function initializeGlobalData(GlobalDataLibrary.GlobalData storage global, address uniswapFactory) external {

/// @audit missing zero check for `_priceOracle`
112:     function updatePriceOracle(DataType.PairStatus storage _pairStatus, address _priceOracle) external {

```

```solidity
File: ./src/libraries/orders/Permit2Lib.sol

/// @audit missing zero check for `to`
23:     function transferDetails(ResolvedOrder memory order, address to)
            internal
            pure
            returns (ISignatureTransfer.SignatureTransferDetails memory)
        {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

/// @audit missing zero check for `predyPool`
/// @audit missing zero check for `whitelistFiller`
/// @audit missing zero check for `quoterAddress`
69:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
            public
            initializer
        {

/// @audit missing zero check for `owner`
361:     function getUserPositions(address owner) external returns (UserPositionResult[] memory) {

/// @audit missing zero check for `trader`
388:     function _addPositionIndex(address trader, uint256 newPositionId) internal {

/// @audit missing zero check for `trader`
408:     function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)
             internal
             returns (GammaTradeMarketLib.UserPosition storage userPosition)
         {

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

/// @audit missing zero check for `predyPool`
/// @audit missing zero check for `whitelistFiller`
/// @audit missing zero check for `quoterAddress`
92:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
            public
            initializer
        {

/// @audit missing zero check for `owner`
247:     function getUserPosition(address owner, uint256 pairId)
             external
             returns (
                 UserPosition memory userPosition,
                 IPredyPool.VaultStatus memory vaultStatus,
                 DataType.Vault memory vault
             )
         {

/// @audit missing zero check for `filler`
275:     function quoteExecuteOrderV3(
             PerpOrderV3 memory perpOrder,
             SettlementParamsV3 memory settlementParams,
             address filler
         ) external {

```

```solidity
File: ./src/settlements/UniswapSettlement.sol

/// @audit missing zero check for ``
/// @audit missing zero check for `recipient`
22:     function swapExactIn(
            address,
            address baseToken,
            bytes memory data,
            uint256 amountIn,
            uint256 amountOutMinimum,
            address recipient
        ) external override returns (uint256 amountOut) {

/// @audit missing zero check for ``
/// @audit missing zero check for `recipient`
38:     function swapExactOut(
            address quoteToken,
            address,
            bytes memory data,
            uint256 amountOut,
            uint256 amountInMaximum,
            address recipient
        ) external override returns (uint256 amountIn) {

```

```solidity
File: ./src/tokenization/SupplyToken.sol

/// @audit missing zero check for `account`
21:     function mint(address account, uint256 amount) external virtual override onlyController {

/// @audit missing zero check for `account`
25:     function burn(address account, uint256 amount) external virtual override onlyController {

```

```solidity
File: ./src/types/GlobalData.sol

/// @audit missing zero check for `to`
72:     function take(GlobalDataLibrary.GlobalData storage globalData, bool isQuoteAsset, address to, uint256 amount)
            internal
        {

```

</details>


<a name="L-2"></a> 
### [L-2] Use increase/decrease allowance instead of `approve()`/`safeApprove()`
Changing an allowance with approve() brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. Refer to [ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM). It is recommended to use the `increaseAllowance()`/`decreaseAllowance()` to avoid this problem.

*There are <b>4</b> instances of this issue:*
```solidity
File: ./src/base/SettlementCallbackLib.sol

111:         ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);

158:         ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);

```

```solidity
File: ./src/settlements/UniswapSettlement.sol

31:         ERC20(baseToken).approve(address(_swapRouter), amountIn);

47:         ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);

```


<a name="L-3"></a> 
### [L-3] Array is `push()`ed but not `pop()`ed
There is no limit specified on the amount of gas used, so the recipient can use up all of the remaining gas (`gasleft()`), causing it to revert. Therefore, when calling an external contract, it is necessary to specify a limited amount of gas to forward.

*There is one instance of this issue:*
```solidity
File: ./src/markets/gamma/ArrayLib.sol

6:         items.push(item);

```


<a name="L-4"></a> 
### [L-4] Division by zero not prevented
The divisions below take an input parameter that has no zero-value checks, which can cause the functions reverting if zero is passed.

*There are <b>2</b> instances of this issue:*
```solidity
File: ./src/PriceFeed.sol

/// @audit `_decimalsDiff`
55:         price = price * Constants.Q96 / _decimalsDiff;

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

/// @audit `leverage`
233:         return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);

```


<a name="L-5"></a> 
### [L-5] Consider implementing two-step procedure for updating protocol addresses
A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must "accept" the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement [EIP-165](https://eips.ethereum.org/EIPS/eip-165), and to have the "set" functions ensure that the recipient is of the right interface type.

*There are <b>4</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

/// @audit `operator`
97:     function setOperator(address newOperator) external onlyOperator {

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit `whitelistFiller`
84:     function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

/// @audit `whitelistFiller`
128:     function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {

/// @audit `_quoter`
132:     function updateQuoter(address newQuoter) external onlyFiller {

```


<a name="L-6"></a> 
### [L-6] Constructor / initialization function lacks parameter validation
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

*There are <b>8</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

/// @audit `uniswapFactory` not validated
70:     function initialize(address uniswapFactory) public initializer {

```

```solidity
File: ./src/PriceFeed.sol

/// @audit `pyth` not validated
14:     constructor(address pyth) {

/// @audit `quotePrice` not validated
/// @audit `pyth` not validated
/// @audit `priceId` not validated
/// @audit `decimalsDiff` not validated
37:     constructor(address quotePrice, address pyth, bytes32 priceId, uint256 decimalsDiff) {

```

```solidity
File: ./src/base/BaseHookCallback.sol

/// @audit `predyPool` not validated
12:     constructor(IPredyPool predyPool) {

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit `_whitelistFiller` not validated
19:     constructor(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

/// @audit `predyPool` not validated
/// @audit `whitelistFiller` not validated
/// @audit `quoterAddress` not validated
69:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

/// @audit `predyPool` not validated
/// @audit `whitelistFiller` not validated
/// @audit `quoterAddress` not validated
92:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```

```solidity
File: ./src/tokenization/SupplyToken.sol

/// @audit `controller` not validated
/// @audit `_name` not validated
/// @audit `_symbol` not validated
/// @audit `__decimals` not validated
15:     constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)

```


<a name="L-7"></a> 
### [L-7] Code does not follow the best practice of check-effects-interaction
Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

*There is one instance of this issue:*
```solidity
File: ./src/libraries/logic/AddPairLogic.sol

/// @audit `.token0(...)` is called on line 70
/// @audit `.token1(...)` is called on line 70
/// @audit `.fee(...)` is called on line 70
/// @audit `.getPool(...)` is called on line 70
/// @audit `.token0(...)` is called on line 76
/// @audit `.token1(...)` is called on line 76
/// @audit `.token0(...)` is called on line 78
/// @audit `.token0(...)` is called on line 84
/// @audit `.token1(...)` is called on line 84
89:         allowedUniswapPools[_addPairParam.uniswapPool] = true;

```


<a name="L-8"></a> 
### [L-8] Functions calling contracts/addresses with transfer hooks are missing reentrancy guards
While adherence to the check-effects-interaction pattern is commendable, the absence of a reentrancy guard in functions, especially where transfer hooks might be present, can expose the protocol users to risks of read-only reentrancies. Such reentrancy vulnerabilities can be exploited to execute malicious actions even without altering the contract state. Without a reentrancy guard, the only potential mitigation would be to blocklist the entire protocol - an extreme and disruptive measure. Therefore, incorporating a reentrancy guard into these functions is vital to bolster security, as it helps protect against both traditional reentrancy attacks and read-only reentrancies, ensuring robust and safe protocol operations.

*There are <b>2</b> instances of this issue:*
```solidity
File: ./src/settlements/UniswapSettlement.sol

22:     function swapExactIn(
            address,
            address baseToken,
            bytes memory data,
            uint256 amountIn,
            uint256 amountOutMinimum,
            address recipient
        ) external override returns (uint256 amountOut) {
            ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);

38:     function swapExactOut(
            address quoteToken,
            address,
            bytes memory data,
            uint256 amountOut,
            uint256 amountInMaximum,
            address recipient
        ) external override returns (uint256 amountIn) {
            ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);

```


<a name="L-9"></a> 
### [L-9] Governance functions should be controlled by time locks
Governance functions (such as upgrading contracts, setting critical parameters) should be controlled using time locks to introduce a delay between a proposal and its execution. This gives users time to exit before a potentially dangerous or malicious operation is applied.

*There is one instance of this issue:*
```solidity
File: ./src/PredyPool.sol

97:     function setOperator(address newOperator) external onlyOperator {

```


<a name="L-10"></a> 
### [L-10] Lack of `_disableInitializers` call to prevent uninitialized contracts
Multiple contracts are using the Initializable module from OpenZeppelin. For this reason and in order to prevent leaving that contract uninitialized OpenZeppelin's documentation recommends adding the `_disableInitializers` function in the constructor to automatically lock the contracts when they are deployed. this will protect the contract that holds the logic business from beeing initialized by an attack.

*There are <b>3</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

70:     function initialize(address uniswapFactory) public initializer {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

69:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

92:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```


<a name="L-11"></a> 
### [L-11] Large approvals may not work with some `ERC20` tokens
Not all `IERC20` implementations are totally compliant, and some (e.g `UNI`, `COMP`) may fail if the valued passed to `approve` is larger than `uint96`. If the approval amount is `type(uint256).max`, which may cause issues with systems that expect the value passed to approve to be reflected in the allowances mapping.

*There are <b>4</b> instances of this issue:*
```solidity
File: ./src/base/SettlementCallbackLib.sol

111:         ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);

158:         ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);

```

```solidity
File: ./src/settlements/UniswapSettlement.sol

31:         ERC20(baseToken).approve(address(_swapRouter), amountIn);

47:         ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);

```


<a name="L-12"></a> 
### [L-12] Loss of precision in divisions
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

<details>
<summary>
There are <b>12</b> instances (click to show):
</summary>

```solidity
File: ./src/libraries/InterestRateModel.sol

26:             ir += (utilizationRatio * irmParams.slope1) / _ONE;

28:             ir += (irmParams.kinkRate * irmParams.slope1) / _ONE;

29:             ir += (irmParams.slope2 * (utilizationRatio - irmParams.kinkRate)) / _ONE;

```

```solidity
File: ./src/libraries/Perp.sol

697:                 int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;

```

```solidity
File: ./src/libraries/PositionCalculator.sol

235:             + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;

249:         return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);

```

```solidity
File: ./src/libraries/PremiumCurveModel.sol

21:         return (1600 * b * b / Constants.ONE) / Constants.ONE;

```

```solidity
File: ./src/libraries/Reallocation.sol

120:         result = (result / tickSpacing) * tickSpacing;

```

```solidity
File: ./src/libraries/orders/DecayLib.sol

32:                 decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;

34:                 decayedPrice = startPrice + (endPrice - startPrice) * elapsed / duration;

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketLib.sol

150:         uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;

158:                 + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE

```

</details>


<a name="L-13"></a> 
### [L-13] Missing checks for state variable assignments
There are some missing checks in these functions, and this could lead to unexpected scenarios. Consider always adding a sanity check for state variables.

<details>
<summary>
There are <b>15</b> instances (click to show):
</summary>

```solidity
File: ./src/PredyPool.sol

/// @audit `newOperator`
99:         operator = newOperator;

```

```solidity
File: ./src/PriceFeed.sol

/// @audit `pyth`
15:         _pyth = pyth;

/// @audit `quotePrice`
38:         _quotePriceFeed = quotePrice;

/// @audit `pyth`
39:         _pyth = pyth;

/// @audit `priceId`
40:         _priceId = priceId;

/// @audit `decimalsDiff`
41:         _decimalsDiff = decimalsDiff;

```

```solidity
File: ./src/base/BaseHookCallback.sol

/// @audit `predyPool`
13:         _predyPool = predyPool;

```

```solidity
File: ./src/base/BaseHookCallbackUpgradable.sol

/// @audit `predyPool`
21:         _predyPool = predyPool;

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit `_whitelistFiller`
23:         whitelistFiller = _whitelistFiller;

/// @audit `newWhitelistFiller`
85:         whitelistFiller = newWhitelistFiller;

/// @audit `isEnabled`
93:         _whiteListedSettlements[settlementContractAddress] = isEnabled;

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

/// @audit `_whitelistFiller`
44:         whitelistFiller = _whitelistFiller;

/// @audit `newWhitelistFiller`
129:         whitelistFiller = newWhitelistFiller;

/// @audit `isEnabled`
141:         _whiteListedSettlements[settlementContractAddress] = isEnabled;

```

```solidity
File: ./src/tokenization/SupplyToken.sol

/// @audit `controller`
18:         _controller = controller;

```

</details>


<a name="L-14"></a> 
### [L-14] Missing zero address check in constructor
Constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a checking, there's a risk that an address parameter could be mistakenly set to the zero address (0x0). This could be due to an error or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it will be irretrievable. It's therefore crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

*There are <b>5</b> instances of this issue:*
```solidity
File: ./src/PriceFeed.sol

/// @audit missing zero check for `pyth`
14:     constructor(address pyth) {

/// @audit missing zero check for `quotePrice`
/// @audit missing zero check for `pyth`
37:     constructor(address quotePrice, address pyth, bytes32 priceId, uint256 decimalsDiff) {

```

```solidity
File: ./src/base/BaseHookCallback.sol

/// @audit missing zero check for `predyPool`
12:     constructor(IPredyPool predyPool) {

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit missing zero check for `_whitelistFiller`
19:     constructor(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
            BaseHookCallback(predyPool)
            Owned(msg.sender)
        {

```

```solidity
File: ./src/tokenization/SupplyToken.sol

/// @audit missing zero check for `controller`
15:     constructor(address controller, string memory _name, string memory _symbol, uint8 __decimals)
            ERC20(_name, _symbol, __decimals)
        {

```


<a name="L-15"></a> 
### [L-15] Consider some checks for `address(0)` when setting address state variables
Check for zero-address to avoid the risk of setting `address(0)` for state variables after an update.

<details>
<summary>
There are <b>11</b> instances (click to show):
</summary>

```solidity
File: ./src/PredyPool.sol

/// @audit `newOperator`
99:         operator = newOperator;

```

```solidity
File: ./src/PriceFeed.sol

/// @audit `pyth`
15:         _pyth = pyth;

/// @audit `quotePrice`
38:         _quotePriceFeed = quotePrice;

/// @audit `pyth`
39:         _pyth = pyth;

```

```solidity
File: ./src/base/BaseHookCallback.sol

/// @audit `predyPool`
13:         _predyPool = predyPool;

```

```solidity
File: ./src/base/BaseHookCallbackUpgradable.sol

/// @audit `predyPool`
21:         _predyPool = predyPool;

```

```solidity
File: ./src/base/BaseMarket.sol

/// @audit `_whitelistFiller`
23:         whitelistFiller = _whitelistFiller;

/// @audit `newWhitelistFiller`
85:         whitelistFiller = newWhitelistFiller;

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

/// @audit `_whitelistFiller`
44:         whitelistFiller = _whitelistFiller;

/// @audit `newWhitelistFiller`
129:         whitelistFiller = newWhitelistFiller;

```

```solidity
File: ./src/tokenization/SupplyToken.sol

/// @audit `controller`
18:         _controller = controller;

```

</details>


<a name="L-16"></a> 
### [L-16] Check storage gap for upgradable contracts
Each upgradable contract should include a state variable (usually named `__gap`) to provide reserved space in storage. This allows the team to freely add new state variables in the future upgrades without compromising the storage compatibility with existing deployments.

*There are <b>3</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

28: contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

24: contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

29: contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {

```


<a name="L-17"></a> 
### [L-17] Multiplication on the result of a division
Dividing an integer by another integer will often result in loss of precision. When the result is multiplied by another number, the loss of precision is magnified, often to material magnitudes. `(X / Z) * Y` should be re-written as `(X * Y) / Z`

*There is one instance of this issue:*
```solidity
File: ./src/libraries/Reallocation.sol

120:         result = (result / tickSpacing) * tickSpacing;

```


<a name="L-18"></a> 
### [L-18] Solidity version 0.8.20 or above may not work on other chains due to PUSH0
Solidity version 0.8.20 or above uses the new [Shanghai EVM](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note) which introduces the PUSH0 opcode. This op code may not yet be implemented on all evm-chains or Layer2s, so deployment on these chains will fail. Consider using an earlier solidity version.

<details>
<summary>
There are <b>55</b> instances (click to show):
</summary>

```solidity
File: ./src/PredyPool.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/PriceFeed.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/base/BaseHookCallback.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/base/BaseHookCallbackUpgradable.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/base/BaseMarket.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/base/SettlementCallbackLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/ApplyInterestLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/Constants.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/DataType.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/InterestRateModel.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/PairLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/Perp.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/PerpFee.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/PositionCalculator.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/PremiumCurveModel.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/Reallocation.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/ScaledAsset.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/SlippageLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/Trade.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/UniHelper.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/VaultLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/AddPairLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/LiquidationLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/ReaderLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/ReallocationLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/SupplyLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/logic/TradeLogic.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/math/Bps.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/math/LPMath.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/math/Math.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/orders/DecayLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/orders/OrderInfoLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/orders/Permit2Lib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/libraries/orders/ResolvedOrder.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/L2Decoder.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/ArrayLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/GammaOrder.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketL2.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketWrapper.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/gamma/L2GammaDecoder.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/perp/PerpMarket.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/perp/PerpMarketLib.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/perp/PerpOrder.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/markets/perp/PerpOrderV3.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/settlements/UniswapSettlement.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/tokenization/SupplyToken.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/types/GlobalData.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/types/LockData.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/vendors/AggregatorV3Interface.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/vendors/IPyth.sol

2: pragma solidity ^0.8.26;

```

```solidity
File: ./src/vendors/IUniswapV3PoolOracle.sol

2: pragma solidity ^0.8.26;

```

</details>


<a name="L-19"></a> 
### [L-19] Tokens may be minted to `address(0)`

*There is one instance of this issue:*
```solidity
File: ./src/tokenization/SupplyToken.sol

21:     function mint(address account, uint256 amount) external virtual override onlyController {
            _mint(account, amount);
        }

```


<a name="L-20"></a> 
### [L-20] Unsafe downcast
When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behavior and bugs.

*There are <b>6</b> instances of this issue:*
```solidity
File: ./src/libraries/Reallocation.sol

/// @audit uint256 -> uint160
190:         uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));

```

```solidity
File: ./src/libraries/UniHelper.sol

/// @audit uint256 -> uint160
29:             return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);

/// @audit uint256 -> uint32
38:         secondsAgos[0] = uint32(ago);

/// @audit uint256 -> uint32
58:             secondsAgos[0] = uint32(ago);

/// @audit int56 -> int24
70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

/// @audit int256 -> int56
70:         int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));

```


<a name="L-21"></a> 
### [L-21] Using zero as a parameter
Taking `0` as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous `0x0` address bug where numerous tokens were lost. This happens because 0 can be interpreted as an uninitialized `address`, leading to transfers to the 0x0 address, effectively burning tokens. Moreover, `0` as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like `0` appropriately. Use `require()` statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.

<details>
<summary>
There are <b>14</b> instances (click to show):
</summary>

```solidity
File: ./src/libraries/Perp.sol

124:         return SqrtPerpAssetStatus(

146:         return UserStatus(

151:             PositionStatus(0, 0),

152:             SqrtPositionStatus(0, 0, 0, 0, 0, 0),

```

```solidity
File: ./src/libraries/ScaledAsset.sol

30:         return AssetStatus(0, 0, 0, Constants.ONE, 0, 0);

34:         return UserStatus(0, 0);

```

```solidity
File: ./src/libraries/Trade.sol

56:             SwapStableResult(-tradeParams.tradeAmount, underlyingAmountForSqrt, realizedFee.feeAmountBase, 0),

89:             return divToStable(swapParams, int256(1e18), amountQuote, 0);

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

265:             abi.encode(CallbackData(triggerType, userPosition.owner, 0))

303:             abi.encode(CallbackData(triggerType, userPosition.owner, 0))

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketL2.sol

80:             GammaOrder(

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

156:         return _executeOrderV3(perpOrder, order.sig, settlementParams, 0);

192:                     CallbackData(

300:                     CallbackData(

```

</details>


<a name="L-22"></a> 
### [L-22] Missing zero address check in initializer

*There are <b>4</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

/// @audit missing zero check for `uniswapFactory`
70:     function initialize(address uniswapFactory) public initializer {

```

```solidity
File: ./src/libraries/logic/AddPairLogic.sol

/// @audit missing zero check for `uniswapFactory`
44:     function initializeGlobalData(GlobalDataLibrary.GlobalData storage global, address uniswapFactory) external {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

/// @audit missing zero check for `predyPool`
/// @audit missing zero check for `whitelistFiller`
/// @audit missing zero check for `quoterAddress`
69:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
            public
            initializer
        {

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

/// @audit missing zero check for `predyPool`
/// @audit missing zero check for `whitelistFiller`
/// @audit missing zero check for `quoterAddress`
92:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)
            public
            initializer
        {

```


<a name="L-23"></a> 
### [L-23] Centralization Risk for trusted owners
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

<details>
<summary>
There are <b>26</b> instances (click to show):
</summary>

```solidity
File: ./src/PredyPool.sol

97:     function setOperator(address newOperator) external onlyOperator {

109:     function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {

119:     function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)
             external
             onlyPoolOwner(pairId)

133:     function updateIRMParams(
             uint256 pairId,
             InterestRateModel.IRMParams memory quoteIrmParams,
             InterestRateModel.IRMParams memory baseIrmParams
         ) external onlyPoolOwner(pairId) {

147:     function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {

157:     function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {

167:     function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {

177:     function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {

199:     function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {

286:     function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {

300:     function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {

329:     function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {

```

```solidity
File: ./src/base/BaseHookCallbackUpgradable.sol

20:     function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {

```

```solidity
File: ./src/base/BaseMarket.sol

28:     function predySettlementCallback(
            address quoteToken,
            address baseToken,
            bytes memory settlementData,
            int256 baseAmountDelta
        ) external onlyPredyPool {

84:     function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {

92:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {

```

```solidity
File: ./src/base/BaseMarketUpgradable.sol

38:     function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
            internal
            onlyInitializing

49:     function predySettlementCallback(
            address quoteToken,
            address baseToken,
            bytes memory settlementData,
            int256 baseAmountDelta
        ) external onlyPredyPool {

128:     function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {

132:     function updateQuoter(address newQuoter) external onlyFiller {

140:     function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

79:     function predyTradeAfterCallback(
            IPredyPool.TradeParams memory tradeParams,
            IPredyPool.TradeResult memory tradeResult
        ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

392:     function removePosition(uint256 positionId) external onlyFiller {

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

102:     function predyTradeAfterCallback(
             IPredyPool.TradeParams memory tradeParams,
             IPredyPool.TradeResult memory tradeResult
         ) external override(BaseHookCallbackUpgradable) onlyPredyPool {

```

```solidity
File: ./src/tokenization/SupplyToken.sol

21:     function mint(address account, uint256 amount) external virtual override onlyController {

25:     function burn(address account, uint256 amount) external virtual override onlyController {

```

</details>


<a name="L-24"></a> 
### [L-24] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*There are <b>3</b> instances of this issue:*
```solidity
File: ./src/PredyPool.sol

70:     function initialize(address uniswapFactory) public initializer {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarket.sol

69:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```

```solidity
File: ./src/markets/perp/PerpMarketV1.sol

92:     function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)

```


<a name="L-25"></a> 
### [L-25] Chainlink answer is not compared against min/max values
Some check like this can be added to avoid returning of the min price or the max price in case of the price crashes:
```solidity
	require(answer < _maxPrice, "Upper price bound breached");
	require(answer > _minPrice, "Lower price bound breached");
```

*There is one instance of this issue:*
```solidity
File: ./src/PriceFeed.sol

46:         (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();

```


<a name="L-26"></a> 
### [L-26] Library has public/external functions
Libraries in Solidity are meant to be static collections of functions. They are deployed once and their code is reused by contracts through DELEGATECALL, which executes the library's code in the context of the calling contract. Public or external functions in libraries can be misleading because libraries are not supposed to maintain state or have external interactions in the way contracts do. Designing libraries with these kinds of functions goes against their intended purpose and can lead to confusion or misuse. For state management or external interactions, using contracts instead of libraries is more appropriate. Libraries should focus on utility functions that can operate on the data passed to them without side effects, enhancing code reuse and gas efficiency.

<details>
<summary>
There are <b>18</b> instances (click to show):
</summary>

```solidity
File: ./src/libraries/Trade.sol

32:     function trade(
            GlobalDataLibrary.GlobalData storage globalData,
            IPredyPool.TradeParams memory tradeParams,
            bytes memory settlementData
        ) external returns (IPredyPool.TradeResult memory tradeResult) {

```

```solidity
File: ./src/libraries/logic/AddPairLogic.sol

44:     function initializeGlobalData(GlobalDataLibrary.GlobalData storage global, address uniswapFactory) external {

53:     function addPair(
            GlobalDataLibrary.GlobalData storage _global,
            mapping(address => bool) storage allowedUniswapPools,
            AddPairParams memory _addPairParam
        ) external returns (uint256 pairId) {

96:     function updateFeeRatio(DataType.PairStatus storage _pairStatus, uint8 _feeRatio) external {

104:     function updatePoolOwner(DataType.PairStatus storage _pairStatus, address _poolOwner) external {

112:     function updatePriceOracle(DataType.PairStatus storage _pairStatus, address _priceOracle) external {

118:     function updateAssetRiskParams(DataType.PairStatus storage _pairStatus, Perp.AssetRiskParams memory _riskParams)
             external
         {

128:     function updateIRMParams(
             DataType.PairStatus storage _pairStatus,
             InterestRateModel.IRMParams memory _quoteIrmParams,
             InterestRateModel.IRMParams memory _baseIrmParams
         ) external {

```

```solidity
File: ./src/libraries/logic/LiquidationLogic.sol

39:     function liquidate(
            uint256 vaultId,
            uint256 closeRatio,
            GlobalDataLibrary.GlobalData storage globalData,
            bytes memory settlementData
        ) external returns (IPredyPool.TradeResult memory tradeResult) {

```

```solidity
File: ./src/libraries/logic/ReaderLogic.sol

16:     function getPairStatus(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) external {

24:     function getVaultStatus(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId) external {

```

```solidity
File: ./src/libraries/logic/ReallocationLogic.sol

27:     function reallocate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId, bytes memory settlementData)
            external
            returns (bool isRangeChanged)
        {

```

```solidity
File: ./src/libraries/logic/SupplyLogic.sol

22:     function supply(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)
            external
            returns (uint256 mintAmount)
        {

57:     function withdraw(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)
            external
            returns (uint256 finalburntAmount, uint256 finalWithdrawalAmount)
        {

```

```solidity
File: ./src/libraries/logic/TradeLogic.sol

29:     function trade(
            GlobalDataLibrary.GlobalData storage globalData,
            IPredyPool.TradeParams memory tradeParams,
            bytes memory settlementData
        ) external returns (IPredyPool.TradeResult memory tradeResult) {

```

```solidity
File: ./src/markets/gamma/GammaTradeMarketLib.sol

59:     function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)
            external
            view
            returns (bool, uint256 slippageTolerance, GammaTradeMarketLib.CallbackType)
        {

106:     function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)
             external
             view
             returns (bool, uint256 slippageTolerance, CallbackType)
         {

189:     function saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)
             external
             returns (bool)
         {

```

</details>


<a name="L-27"></a> 
### [L-27] The call `abi.encodeWithSelector` is not type safe
In Solidity, `abi.encodeWithSelector` is a function used for encoding data along with a function selector, but it is not type-safe. This means it does not enforce type checking at compile time, potentially leading to errors if arguments do not match the expected types. Starting from version 0.8.13, Solidity introduced `abi.encodeCall`, which offers a safer alternative. `abi.encodeCall` ensures type safety by performing a full type check, aligning the types of the arguments with the function signature. This reduces the risk of bugs caused by typographical errors or mismatched types. Using `abi.encodeCall` enhances the reliability and security of the code by ensuring that the encoded data strictly conforms to the specified types, making it a preferable choice in Solidity versions 0.8.13 and above.

*There are <b>2</b> instances of this issue:*
```solidity
File: ./src/libraries/UniHelper.sol

42:             address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));

61:                 abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos)

```


