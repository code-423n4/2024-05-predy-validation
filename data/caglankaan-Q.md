
### Contracts are vulnerable to fee-on-transfer accounting-related issues
The function(s) below transfer funds from the caller to the receiver via `transferFrom()`, but do not ensure that the actual number of tokens received is the same as the input amount to the transfer. If the token is a fee-on-transfer token, the balance after the transfer will be smaller than expected, leading to accounting issues. Even if there are checks later, related to a secondary transfer, an attacker may be able to use latent funds (e.g. mistakenly sent by another user) in order to get a free credit. One way to address this problem is to measure the balance before and after the transfer, and use the difference as the amount, rather than the stated amount.

```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

46:        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L46-L46), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

48:            ERC20(quoteToken).safeTransferFrom(	// @audit-issue

105:            ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);	// @audit-issue

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L48-L48), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L105-L105), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), 


#### Recommendation

To mitigate potential vulnerabilities and ensure accurate accounting with fee-on-transfer tokens, modify your contract's token transfer logic to measure the recipient's balance before and after the transfer. Use this observed difference as the actual transferred amount for any further logic or calculations. For example:
```solidity
function transferTokenAndPerformAction(address token, address from, address to, uint256 amount) public {
    uint256 balanceBefore = IERC20(token).balanceOf(to);

    // Perform the token transfer
    IERC20(token).transferFrom(from, to, amount);

    uint256 balanceAfter = IERC20(token).balanceOf(to);
    uint256 actualReceived = balanceAfter - balanceBefore;

    // Proceed with logic using actualReceived instead of the initial amount
    require(actualReceived >= minimumRequiredAmount, "Received amount is less than required");

    // Further logic here
}
```

### Missing L2 sequencer checks for Chainlink oracle
Using Chainlink in L2 chains such as Arbitrum [requires](https://docs.chain.link/data-feeds/l2-sequencer-feeds) to check if the sequencer is down to avoid prices from looking like they are fresh although they are not.

The bug could be leveraged by malicious actors to take advantage of the sequencer downtime.

```solidity
Path: ./src/PriceFeed.sol

46:        (, int256 quoteAnswer,,,) = AggregatorV3Interface(_quotePriceFeed).latestRoundData();	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L46-L46), 


#### Recommendation

Example how to fix:
```solidity
(
    /*uint80 roundID*/,
    int256 answer,
    uint256 startedAt,
    /*uint256 updatedAt*/,
    /*uint80 answeredInRound*/
) = sequencerUptimeFeed.latestRoundData();

// Answer == 0: Sequencer is up
// Answer == 1: Sequencer is down
bool isSequencerUp = answer == 0;
if (!isSequencerUp) {
    revert SequencerDown();
}
```


### Consider implementing two-step procedure for updating protocol addresses
Implementing a two-step procedure for updating protocol addresses adds an extra layer of security. In such a system, the first step initiates the change, and the second step, after a predefined delay, confirms and finalizes it. This delay allows stakeholders or monitoring tools to observe and react to unintended or malicious changes. If an unauthorized change is detected, corrective actions can be taken before the change is finalized. To achieve this, introduce a "proposed address" state variable and a "delay period". Upon an update request, set the "proposed address". After the delay, if not contested, the main protocol address can be updated.

```solidity
Path: ./src/PredyPool.sol

99:        operator = newOperator;	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L99-L99), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

44:        whitelistFiller = _whitelistFiller;	// @audit-issue

46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L46-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L133-L133), 


#### Recommendation

Introduce two state variables in your contract: one to hold the proposed new address (`address public proposedAddress`) and another to timestamp the proposal (`uint public addressChangeInitiated`). Implement two functions: one to propose a new address, recording the current timestamp, and another to finalize the address change after the delay period has elapsed.

### Centralization risk for privileged functions
Contracts with privileged functions need owner/admin to be trusted not to perform malicious updates or drain funds. This may also cause a single point failure.


```solidity
Path: ./src/PredyPool.sol

97:    function setOperator(address newOperator) external onlyOperator {	// @audit-issue

109:    function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {	// @audit-issue

119:    function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)
120:        external
121:        onlyPoolOwner(pairId)	// @audit-issue

133:    function updateIRMParams(
134:        uint256 pairId,
135:        InterestRateModel.IRMParams memory quoteIrmParams,
136:        InterestRateModel.IRMParams memory baseIrmParams
137:    ) external onlyPoolOwner(pairId) {	// @audit-issue

147:    function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {	// @audit-issue

157:    function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {	// @audit-issue

167:    function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {	// @audit-issue

177:    function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {	// @audit-issue

199:    function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {	// @audit-issue

286:    function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {	// @audit-issue

300:    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {	// @audit-issue

329:    function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {	// @audit-issue
```
[97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L97-L97), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L109-L109), [121](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L119-L121), [137](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L133-L137), [147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L147-L147), [157](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L157-L157), [167](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L167-L167), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L177-L177), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L199-L199), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L286-L286), [300](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L300-L300), [329](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L329-L329), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(
80:        IPredyPool.TradeParams memory tradeParams,
81:        IPredyPool.TradeResult memory tradeResult
82:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {	// @audit-issue

392:    function removePosition(uint256 positionId) external onlyFiller {	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L82), [392](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L392-L392), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

102:    function predyTradeAfterCallback(
103:        IPredyPool.TradeParams memory tradeParams,
104:        IPredyPool.TradeResult memory tradeResult
105:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L105), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

20:    function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L20-L20), 


```solidity
Path: ./src/base/BaseMarket.sol

28:    function predySettlementCallback(
29:        address quoteToken,
30:        address baseToken,
31:        bytes memory settlementData,
32:        int256 baseAmountDelta
33:    ) external onlyPredyPool {	// @audit-issue

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {	// @audit-issue

92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L28-L33), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L92-L92), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

38:    function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
39:        internal
40:        onlyInitializing	// @audit-issue

49:    function predySettlementCallback(
50:        address quoteToken,
51:        address baseToken,
52:        bytes memory settlementData,
53:        int256 baseAmountDelta
54:    ) external onlyPredyPool {	// @audit-issue

128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {	// @audit-issue

132:    function updateQuoter(address newQuoter) external onlyFiller {	// @audit-issue

140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L38-L40), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L49-L54), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L128-L128), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L132-L132), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L140-L140), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

21:    function mint(address account, uint256 amount) external virtual override onlyController {	// @audit-issue

25:    function burn(address account, uint256 amount) external virtual override onlyController {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L25-L25), 


#### Recommendation

To mitigate centralization risks associated with privileged functions, consider implementing a multi-signature or decentralized governance mechanism. Instead of relying solely on a single owner/administrator, distribute control and decision-making authority among multiple parties or stakeholders. This approach enhances security, reduces the risk of malicious actions by a single entity, and prevents single points of failure. Explore governance solutions and smart contract frameworks that support decentralized control and decision-making to enhance the trustworthiness and resilience of your contract.

### Missing checks for `address(0)` in constructor/initializers
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

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
Path: ./src/base/BaseMarket.sol

23:        whitelistFiller = _whitelistFiller;	// @audit-issue

25:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L23-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L25-L25), 


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

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### Missing checks for `address(0)`  when updating state variables
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

44:        return _predyPool.reallocate(pairId, _getSettlementData(settlementParams));	// @audit-issue

52:        return _predyPool.execLiquidationCall(vaultId, closeRatio, _getSettlementData(settlementParams));	// @audit-issue

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L44-L44), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L52-L52), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

44:        whitelistFiller = _whitelistFiller;	// @audit-issue

46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L46-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L133-L133), 


#### Recommendation

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### The `constructor`/`initialize` function lacks parameter validation.
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

```solidity
Path: ./src/PriceFeed.sol

40:        _priceId = priceId;	// @audit-issue

41:        _decimalsDiff = decimalsDiff;	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L40-L40), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L41-L41), 


```solidity
Path: ./src/base/BaseHookCallback.sol

13:        _predyPool = predyPool;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L13-L13), 


#### Recommendation

Incorporate comprehensive parameter validation in your contract's constructor and initialization functions. This should include checks for value ranges, address validity, and any other condition that defines a parameter as valid. Use `require` statements for validation, providing clear error messages for each condition. If any validation fails, the `require` statement will revert the transaction, preventing the contract from being deployed or initialized with invalid parameters.
Example:
```solidity
contract MyContract {
    address public owner;
    uint256 public someValue;

    constructor(address _owner, uint256 _someValue) {
        require(_owner != address(0), "Owner cannot be the zero address");
        require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

        owner = _owner;
        someValue = _someValue;
    }
}

// For contracts using the proxy pattern and requiring initialization functions:
function initialize(address _owner, uint256 _someValue) public {
    require(_owner != address(0), "Owner cannot be the zero address");
    require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

    owner = _owner;
    someValue = _someValue;
}
```


### Division before multiplication
When dealing with arithmetic operations, it's crucial to prioritize multiplication before division. This practice helps to reduce the risk of rounding errors and increases the precision of the calculations. By rearranging your mathematical operations to perform multiplications first, you can enhance the accuracy and efficiency of your contract's computational processes.

```solidity
Path: ./src/libraries/Reallocation.sol

120:        result = (result / tickSpacing) * tickSpacing;	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L120-L120), 


#### Recommendation

Review your smart contract code to identify instances where division precedes multiplication. Refactor these calculations by rearranging the order, ensuring multiplication is performed first. This adjustment can significantly improve the precision of your results, especially in environments where every decimal point matters, like financial or token-related contracts.

### Unsafe downcast may overflow
When a type is downcast to a smaller type, the higher order bits are discarded, resulting in the application of a modulo operation to the original value.

If the downcasted value is large enough, this may result in an overflow that will not revert.


```solidity
Path: ./src/libraries/Reallocation.sol

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue: Variable `liquidityAmount` is type `uint256` and it is downcasted to `uint160`

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue: Variable `denominator1` is type `uint256` and it is downcasted to `uint160`
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


#### Recommendation

When downcasting numbers to `addresses` in Solidity, be cautious of potential collisions. Downcasting truncates the upper bytes of the number, which can lead to multiple values mapping to the same `address`. To avoid collisions, consider using a more robust mapping strategy or additional checks to ensure unique mappings.

### `decimals()` is not a part of the `ERC-20` standard
The `decimals()` function is not a part of the [ERC-20](https://eips.ethereum.org/EIPS/eip-20) standard, and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid `ERC20` tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

200:                erc20.decimals()	// @audit-issue
```
[200](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L200-L200), 


#### Recommendation

When working with ERC-20 tokens in your Solidity code, be aware that the `decimals()` function is not a part of the ERC-20 standard and is considered an optional extension. Not all valid ERC-20 tokens implement this interface, so avoid blindly casting all tokens to this interface and calling the `decimals()` function. Instead, check the token's documentation or contract to determine whether it supports this extension before using it.

### The Contract Should `approve(0)` First
Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), 


#### Recommendation

When developing functions that update allowances for ERC20 tokens, especially when interacting with tokens known to require this pattern, implement a two-step approval process. This process first sets the allowance for a spender to zero, and then sets it to the desired new value. For example:
```solidity
function resetAndApprove(address token, address spender, uint256 amount) public {
    IERC20(token).approve(spender, 0);
    IERC20(token).approve(spender, amount);
}
```


### `symbol()` is not a part of the `ERC-20` standard
The `symbol()` function is not a part of the [ERC-20](https://eips.ethereum.org/EIPS/eip-20) standard, and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid `ERC20` tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

199:                string.concat("p", erc20.symbol()),	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L199-L199), 


#### Recommendation

When working with ERC-20 tokens in your Solidity code, be aware that the `symbol()` function is not a part of the ERC-20 standard and is considered an optional extension. Not all valid ERC-20 tokens implement this interface, so avoid blindly casting all tokens to this interface and calling the `symbol()` function. Instead, check the token's documentation or contract to determine whether it supports this extension before using it.

### Avoid Double `casting`
Consider refactoring the following code, as double casting may introduce unexpected truncations and/or rounding issues.

Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging.


```solidity
Path: ./src/libraries/UniHelper.sol

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


#### Recommendation

To enhance code readability, maintainability, and avoid potential truncation or rounding issues, refactor code that involves double type casting. Minimize the use of double casting to improve code quality and reduce the risk of errors during development and debugging.

### Unsafe `uint` to `int` conversion
The `int` type in Solidity uses the [two's complement system](https://en.wikipedia.org/wiki/Two%27s_complement), so it is possible to accidentally overflow a very large `uint` to an `int`, even if they share the same number of bytes (e.g. a `uint256 number > type(uint128).max` will overflow a `int256` cast).

Consider using the [SafeCast](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol) library to prevent any overflows.


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

53:        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;	// @audit-issue: Variable `_sqrtPrice` is type `uint256` and it is casted to `int256`
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

35:        int256 tradeAmount = isLong ? int256(quantity) : -int256(quantity);	// @audit-issue: Variable `quantity` is type `uint256` and it is casted to `int256`
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L35-L35), 


```solidity
Path: ./src/libraries/UniHelper.sol

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue: Variable `ago` is type `uint256` and it is casted to `int256`
```
[70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


```solidity
Path: ./src/libraries/Perp.sol

286:            int256(receivedAmount0) - int256(requiredAmount0),	// @audit-issue: Variable `receivedAmount0` is type `uint256` and it is casted to `int256`

286:            int256(receivedAmount0) - int256(requiredAmount0),	// @audit-issue: Variable `requiredAmount0` is type `uint256` and it is casted to `int256`

287:            int256(receivedAmount1) - int256(requiredAmount1)	// @audit-issue: Variable `receivedAmount1` is type `uint256` and it is casted to `int256`

287:            int256(receivedAmount1) - int256(requiredAmount1)	// @audit-issue: Variable `requiredAmount1` is type `uint256` and it is casted to `int256`
```
[286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L286-L286), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L286-L286), [287](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L287-L287), [287](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L287-L287), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue: Variable `closeRatio` is type `uint256` and it is casted to `int256`

63:            -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,	// @audit-issue: Variable `closeRatio` is type `uint256` and it is casted to `int256`
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L63-L63), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

85:            int256(fee)	// @audit-issue: Variable `fee` is type `uint256` and it is casted to `int256`
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L85-L85), 


#### Recommendation

To prevent potential overflow issues when converting from `uint` to `int` in Solidity, consider using the SafeCast library. This library helps ensure safe type conversions and avoids unexpected behavior, especially when dealing with very large `uint` values that could overflow when cast to `int`.

### Functions calling contracts/addresses with transfer hooks are missing reentrancy guards
Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.

```solidity
Path: ./src/PredyPool.sol

85:            ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);	// @audit-issue

88:            ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);	// @audit-issue

187:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue

209:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L85-L85), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L88-L88), [187](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L187-L187), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L209-L209), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

118:                    quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L118-L118), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

126:                quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L126-L126), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

46:        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);	// @audit-issue

54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L46-L46), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L54-L54), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

52:        ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);	// @audit-issue

90:        ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L52-L52), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L90-L90), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

69:                    ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L69-L69), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

99:                    ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);	// @audit-issue

106:                ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L99-L99), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L106-L106), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

48:            ERC20(quoteToken).safeTransferFrom(	// @audit-issue

105:            ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);	// @audit-issue

123:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);	// @audit-issue

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue

130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);	// @audit-issue

133:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);	// @audit-issue

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue

170:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);	// @audit-issue

175:                ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);	// @audit-issue

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue

180:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L48-L48), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L105-L105), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L123-L123), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L133-L133), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L175-L175), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L180-L180), 


```solidity
Path: ./src/types/GlobalData.sol

85:        ERC20(currency).safeTransfer(to, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L85-L85), 


#### Recommendation

To ensure the security of your protocol and protect users from potential vulnerabilities, consider implementing reentrancy guards in functions that call contracts or addresses with transfer hooks. Even if your function follows the check-effects-interaction pattern, using reentrancy guards is essential to prevent read-only reentrancy attacks, as demonstrated in incidents like the [Curve LP Oracle Manipulation](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/). Implementing reentrancy guards adds an extra layer of protection and safeguards your protocol against such attacks.

### Consider using OpenZeppelin’s `SafeCast` library to prevent unexpected overflows when casting from various type int/uint values
In Solidity, when casting from `int` to `uint` or vice versa, there is a risk of unexpected overflow or underflow, especially when dealing with large values. To mitigate this risk and ensure safe type conversions, you can consider using OpenZeppelin’s `SafeCast` library. This library provides functions that check for overflow/underflow conditions before performing the cast, helping you prevent potential issues related to type conversion in your smart contracts.

```solidity
Path: ./src/PriceFeed.sol

54:        uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L54-L54), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

96:                _predyPool.take(true, callbackData.trader, uint256(vault.margin));	// @audit-issue

118:                    quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue

120:                    _predyPool.take(true, callbackData.trader, uint256(-marginAmountUpdate));	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L96-L96), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L118-L118), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L120-L120), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

135:        uint256 amount = gammaOrder.marginAmount > 0 ? uint256(gammaOrder.marginAmount) : 0;	// @audit-issue
```
[135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L135-L135), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

53:        int256 sqrtPrice = int256(_sqrtPrice) * (1e6 + maximaDeviation) / 1e6;	// @audit-issue

56:        return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L53-L53), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

74:        uint256 amount = perpOrder.marginAmount > 0 ? uint256(perpOrder.marginAmount) : 0;	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L74-L74), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

120:                cost = uint256(marginAmountUpdate);	// @audit-issue

126:                quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue

128:                _predyPool.take(true, callbackData.trader, uint256(-marginAmountUpdate));	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L120-L120), [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L126-L126), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L128-L128), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

35:        int256 tradeAmount = isLong ? int256(quantity) : -int256(quantity);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L35-L35), 


```solidity
Path: ./src/libraries/UniHelper.sol

29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);	// @audit-issue

38:        secondsAgos[0] = uint32(ago);	// @audit-issue

58:            secondsAgos[0] = uint32(ago);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L29-L29), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L38-L38), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L58-L58), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


```solidity
Path: ./src/libraries/Reallocation.sol

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

213:                (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);	// @audit-issue

249:        return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);	// @audit-issue
```
[213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L213-L213), [249](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L249-L249), 


```solidity
Path: ./src/libraries/Trade.sol

89:            return divToStable(swapParams, int256(1e18), amountQuote, 0);	// @audit-issue

132:        swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L89-L89), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L132-L132), 


```solidity
Path: ./src/libraries/Perp.sol

168:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);	// @audit-issue

172:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);	// @audit-issue

286:            int256(receivedAmount0) - int256(requiredAmount0),	// @audit-issue

287:            int256(receivedAmount1) - int256(requiredAmount1)	// @audit-issue

444:            (requiredAmount0, requiredAmount1) = increase(_sqrtAssetStatus, uint256(_tradeSqrtAmount));	// @audit-issue

451:            (requiredAmount0, requiredAmount1) = decrease(_sqrtAssetStatus, uint256(-_tradeSqrtAmount));	// @audit-issue

552:            _assetStatus.borrowedAmount -= uint256(closeAmount);	// @audit-issue

554:            if (getAvailableSqrtAmount(_assetStatus, true) < uint256(-closeAmount)) {	// @audit-issue

557:            _assetStatus.totalAmount -= uint256(-closeAmount);	// @audit-issue

561:            _assetStatus.totalAmount += uint256(openAmount);	// @audit-issue

566:            if (getAvailableSqrtAmount(_assetStatus, false) < uint256(-openAmount)) {	// @audit-issue

570:            _assetStatus.borrowedAmount += uint256(-openAmount);	// @audit-issue
```
[168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L168-L168), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L172-L172), [286](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L286-L286), [287](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L287-L287), [444](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L444-L444), [451](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L451-L451), [552](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L552-L552), [554](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L554-L554), [557](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L557-L557), [561](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L561-L561), [566](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L566-L566), [570](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L570-L570), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

105:            tokenStatus.totalNormalBorrowed -= uint256(closeAmount);	// @audit-issue

108:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-closeAmount), "S0");	// @audit-issue

110:            tokenStatus.totalNormalDeposited -= uint256(-closeAmount);	// @audit-issue

114:            tokenStatus.totalNormalDeposited += uint256(openAmount);	// @audit-issue

118:            require(getAvailableCollateralValue(tokenStatus) >= uint256(-openAmount), "S0");	// @audit-issue

120:            tokenStatus.totalNormalBorrowed += uint256(-openAmount);	// @audit-issue

165:            uint256(accountState.positionAmount),	// @audit-issue

180:            uint256(-accountState.positionAmount),	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L105-L105), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L108-L108), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L110-L110), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L114-L114), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L118-L118), [120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L120-L120), [165](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L165-L165), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L180-L180), 


```solidity
Path: ./src/libraries/SlippageLib.sol

35:            if (basePrice.lower(slippageTolerance) > uint256(tradeResult.averagePrice)) {	// @audit-issue

40:            if (basePrice.upper(slippageTolerance) < uint256(-tradeResult.averagePrice)) {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L35-L35), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L40-L40), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

69:                    ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L69-L69), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

62:            -vault.openPosition.perp.amount * int256(closeRatio) / 1e18,	// @audit-issue

63:            -vault.openPosition.sqrtPerp.amount * int256(closeRatio) / 1e18,	// @audit-issue

97:                    sentMarginAmount = uint256(remainingMargin);	// @audit-issue

106:                ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));	// @audit-issue

168:        uint256 ratio = uint256(vaultValue * 1e4 / minMargin);	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L62-L62), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L63-L63), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L97-L97), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L106-L106), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L168-L168), 


```solidity
Path: ./src/libraries/math/Math.sol

13:        return uint256(x >= 0 ? x : -x);	// @audit-issue

28:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

30:            return -FullMath.mulDiv(uint256(-x), y, z).toInt256();	// @audit-issue

38:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

40:            return -FullMath.mulDivRoundingUp(uint256(-x), y, z).toInt256();	// @audit-issue

48:            return FixedPointMathLib.mulDivDown(uint256(x), y, z).toInt256();	// @audit-issue

50:            return -FixedPointMathLib.mulDivUp(uint256(-x), y, z).toInt256();	// @audit-issue

56:            return a + uint256(b);	// @audit-issue

58:            return a - uint256(-b);	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L13-L13), [28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L30-L30), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L40-L40), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L48-L48), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L50-L50), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L56-L56), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L58-L58), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

85:            int256(fee)	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L85-L85), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

49:                settlementParams.sender, address(predyPool), uint256(-settlementParams.fee)	// @audit-issue

56:            predyPool.take(true, settlementParams.sender, uint256(settlementParams.fee));	// @audit-issue

75:                uint256(baseAmountDelta)	// @audit-issue

85:                uint256(-baseAmountDelta)	// @audit-issue
```
[49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L49-L49), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L56-L56), [75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L75-L75), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L85-L85), 


#### Recommendation

To enhance the safety and reliability of your Solidity smart contracts, it is advisable to utilize OpenZeppelin’s `SafeCast` library when casting between `int` and `uint` types. Incorporating this library into your codebase will help prevent unexpected overflows and underflows during type conversion, reducing the risk of vulnerabilities and ensuring secure contract execution.

### Initializers can be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment.


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

To mitigate the risk of initializers being front-run, ensure that they are only callable by a trusted entity, such as the deployer, or through a secure, multi-step initialization process. One approach is to use a constructor for initial setup, which is inherently protected against front-running. If a separate initializer is necessary, consider using a modifier that restricts the initializer function to be called only once by a predefined address, typically the deployer's address. Additionally, implement robust checks within the initializer to validate inputs and reject suspicious or unauthorized initialization attempts.

### Revert on transfer to the zero address
It's good practice to revert a token transfer transaction if the recipient's address is the zero address. This can prevent unintentional transfers to the zero address due to accidental operations or programming errors. Many token contracts implement such a safeguard, such as [OpenZeppelin - ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC20/ERC20.sol#L232), [OpenZeppelin - ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L142).

```solidity
Path: ./src/PredyPool.sol

85:            ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);	// @audit-issue

88:            ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);	// @audit-issue

187:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue

209:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L85-L85), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L88-L88), [187](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L187-L187), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L209-L209), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

118:                    quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L118-L118), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

126:                quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L126-L126), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L54-L54), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

90:        ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);	// @audit-issue
```
[90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L90-L90), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

69:                    ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L69-L69), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

49:                settlementParams.sender, address(predyPool), uint256(-settlementParams.fee)	// @audit-issue

105:            ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);	// @audit-issue

123:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);	// @audit-issue

130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);	// @audit-issue

133:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);	// @audit-issue

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue

170:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);	// @audit-issue

175:                ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);	// @audit-issue

180:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);	// @audit-issue
```
[49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L49-L49), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L105-L105), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L123-L123), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L133-L133), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L175-L175), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L180-L180), 


```solidity
Path: ./src/types/GlobalData.sol

85:        ERC20(currency).safeTransfer(to, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L85-L85), 


#### Recommendation

To enhance the security and reliability of your token contracts, it's advisable to implement a safeguard that reverts token transfer transactions if the recipient's address is the zero address. This practice helps prevent unintentional transfers to the zero address, reducing the risk of fund loss due to accidental operations or programming errors. Many token contracts, including [OpenZeppelin's ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC20/ERC20.sol#L232) and [ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L142), incorporate this safeguard for added security.

### State variables not limited to reasonable values
Consider adding appropriate minimum/maximum value checks to ensure that the following state variables can never be used to excessively harm users, including via griefing.

```solidity
Path: ./src/PriceFeed.sol

41:        _decimalsDiff = decimalsDiff;	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L41-L41), 


```solidity
Path: ./src/base/BaseMarket.sol

99:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L99-L99), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

147:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);	// @audit-issue
```
[147](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L147-L147), 


#### Recommendation


Implement validation checks for state variables to enforce minimum and maximum value limits. This can be achieved by adding modifiers or require statements in Solidity functions that modify these state variables. Ensure that these limits are reasonable and reflect the intended use of the contract. Additionally, consider implementing a mechanism to update these limits through a governance process or a trusted role, if applicable, to maintain flexibility and adaptability of the contract over time.

### Solidity version 0.8.20 might not work on all chains due to `PUSH0`
The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

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

The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

It is recommended to verify whether solution can be deployed on particular blockchain with the Solidity version 0.8.20 >=. Whenever such deployment is not possible due to lack of PUSH0 opcode support and lowering the Solidity version is a must, it is strongly advised to review all feature changes and bugfixes in [Solidity releases](https://soliditylang.org/blog/category/releases/). Some changes may have impact on current implementation and may impose a necessity of maintaining another version of solution.

### Upgradeable contract is missing `__gap[..]` storage variable
Storage gaps are needed to not break storage layouts when adding new variables to base contracts. Listed contracts may not be inherited from but it is good practice to add it now rather than forgetting later.


```solidity
Path: ./src/PredyPool.sol

28:contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L28-L28), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

24:contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L24-L24), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

29:contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L29-L29), 


#### Recommendation

When designing upgradeable contracts, it's important to include `__gap[..]` storage variables as storage gaps to prevent issues with storage layout changes when adding new variables to base contracts in the future. While the listed contracts may not be directly inherited from, it is a good practice to add `__gap[..]` storage variables now to ensure a robust and future-proof upgradeable contract design. This proactive approach helps avoid potential complications and storage layout conflicts in later stages of contract development.

### Use `increaseAllowance()`/`decreaseAllowance()` instead of `approve()`/`safeApprove()`
Changing an allowance with `approve()` brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. [Refer to ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.m9fhqynw2xvt). It is recommended to use the `increaseAllowance()`/`decreaseAllowance()` to avoid ths problem.

```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), 


#### Recommendation

To enhance the security of your Solidity smart contracts and avoid potential issues related to transaction ordering, it is advisable to use the `increaseAllowance()` and `decreaseAllowance()` functions instead of `approve()` or `safeApprove()`. These functions provide a safer and more atomic way to modify allowances and mitigate the risk associated with potential attack vectors like those described in the [ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.m9fhqynw2xvt) document.

### `unchecked` blocks with subtractions may underflow
There aren't any checks to avoid an underflow which can happen inside an `unchecked` block, so the following subtractions may underflow silently.

```solidity
Path: ./src/libraries/UniHelper.sol

122:                    feeGrowthBelow0X128 = feeGrowthGlobal0X128 - lowerFeeGrowthOutside0X128;	// @audit-issue

123:                    feeGrowthBelow1X128 = feeGrowthGlobal1X128 - lowerFeeGrowthOutside1X128;	// @audit-issue

139:                    feeGrowthAbove0X128 = feeGrowthGlobal0X128 - upperFeeGrowthOutside0X128;	// @audit-issue

140:                    feeGrowthAbove1X128 = feeGrowthGlobal1X128 - upperFeeGrowthOutside1X128;	// @audit-issue

144:            feeGrowthInside0X128 = feeGrowthGlobal0X128 - feeGrowthBelow0X128 - feeGrowthAbove0X128;	// @audit-issue

145:            feeGrowthInside1X128 = feeGrowthGlobal1X128 - feeGrowthBelow1X128 - feeGrowthAbove1X128;	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L122-L122), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L123-L123), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L139-L139), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L140-L140), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L144-L144), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L145-L145), 


```solidity
Path: ./src/libraries/Perp.sol

386:            f0 = feeGrowthInside0X128 - _assetStatus.lastFee0Growth;	// @audit-issue

387:            f1 = feeGrowthInside1X128 - _assetStatus.lastFee1Growth;	// @audit-issue
```
[386](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L386-L386), [387](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L387-L387), 


#### Recommendation

Exercise caution when using `unchecked` blocks with subtractions in Solidity, as there are no built-in checks to prevent underflows. It's essential to implement your own checks and validations to ensure that subtractions within `unchecked` blocks do not result in silent underflows. Proper input validation and error handling can help prevent unexpected issues related to underflows.

### Non Disabled Implementation Contract
The upgradeable contracts do not disable initializers in the constructor, as recommended by the imported Initializable contract. This means that anyone can call the initializer on the implementation contract to set the contract variables and assign the roles. To reduce the potential attack surface, `_disableInitializers` in the constructor needs to be called.

```solidity
Path: ./src/PredyPool.sol

68:    constructor() {}	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L68-L68), 


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


#### Recommendation


Build a constructor function in the upgradeable contracts that calls the disableInitializers() function.

### Potential division by zero should have zero checks in place
In Solidity, division by zero is a critical issue that can lead to exceptions and disrupt the normal flow of a contract. It's essential to ensure that the divisor in any division operation is not zero before performing the operation. Failing to check for zero can result in transaction reversion or other unintended consequences. This is especially important in financial contracts or any contract where division operations are used to determine distributions, rewards, or similar calculations. Implementing checks to ensure the divisor is not zero before performing division enhances the robustness and reliability of the contract.

```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

233:        return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);	// @audit-issue: Variable `leverage` not checked.
```
[233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L233-L233), 


```solidity
Path: ./src/libraries/UniHelper.sol

29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);	// @audit-issue: Variable `sqrtPriceX96` not checked.
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L29-L29), 


```solidity
Path: ./src/libraries/Reallocation.sol

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue: Variable `liquidityAmount` not checked.
```
[170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

194:        uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;	// @audit-issue: Variable `_riskRatio` not checked.
```
[194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L194-L194), 


```solidity
Path: ./src/libraries/Trade.sol

128:        swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;	// @audit-issue: Variable `amountBase` not checked.

129:        swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;	// @audit-issue: Variable `amountBase` not checked.
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L129-L129), 


```solidity
Path: ./src/libraries/Perp.sol

689:                int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;	// @audit-issue: Variable `_positionAmount` not checked.
```
[689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L689-L689), 


```solidity
Path: ./src/libraries/math/Bps.sol

12:        return price * ONE / bps;	// @audit-issue: Variable `bps` not checked.
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L12-L12), 


#### Recommendation

Before performing any division operation in Solidity, implement a check to ensure that the divisor is not zero. Use a `require()` statement or a similar assertion to validate the divisor. For example, use `require(divisor != 0, "Divisor cannot be zero");` before executing the division. This precaution prevents exceptions due to division by zero and ensures that your contract remains stable and predictable under all operational conditions. Regularly review your contract's logic to identify and safeguard against potential division by zero occurrences.

### Critical functions should have a timelock
Critical functions, especially those affecting protocol parameters or user funds, are potential points of failure or exploitation. To mitigate risks, incorporating a timelock on such functions can be beneficial. A timelock requires a waiting period between the time an action is initiated and when it's executed, giving stakeholders time to react, potentially vetoing malicious or erroneous changes. To implement, integrate a smart contract like OpenZeppelin's `TimelockController` or build a custom mechanism. This ensures governance decisions or administrative changes are transparent and allows for community or multi-signature interventions, enhancing protocol security and trustworthiness.

```solidity
Path: ./src/PredyPool.sol

300:    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {
301:        require(globalData.pairs[pairId].allowlistEnabled);
302:
303:        allowedTraders[trader][pairId] = enabled;	// @audit-issue
```
[303](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L300-L303), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

20:    function __BaseHookCallback_init(IPredyPool predyPool) internal onlyInitializing {
21:        _predyPool = predyPool;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L20-L21), 


```solidity
Path: ./src/base/BaseMarket.sol

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {
85:        whitelistFiller = newWhitelistFiller;	// @audit-issue

92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {
93:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L85), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L92-L93), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

38:    function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
39:        internal
40:        onlyInitializing
41:    {
42:        __BaseHookCallback_init(predyPool);
43:
44:        whitelistFiller = _whitelistFiller;	// @audit-issue

38:    function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)
39:        internal
40:        onlyInitializing
41:    {
42:        __BaseHookCallback_init(predyPool);
43:
44:        whitelistFiller = _whitelistFiller;
45:
46:        _quoter = PredyPoolQuoter(quoterAddress);	// @audit-issue

128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {
129:        whitelistFiller = newWhitelistFiller;	// @audit-issue

132:    function updateQuoter(address newQuoter) external onlyFiller {
133:        _quoter = PredyPoolQuoter(newQuoter);	// @audit-issue

140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {
141:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L38-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L38-L46), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L128-L129), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L132-L133), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L140-L141), 


#### Recommendation

Integrate a timelock mechanism into your Solidity contracts for critical functions, especially those controlling protocol parameters or managing user funds. Consider using established solutions like OpenZeppelin's `TimelockController` for robustness and reliability. Alternatively, develop a custom timelock mechanism tailored to your specific requirements. Ensure that the timelock duration is appropriate for your contract's use case and stakeholder needs, providing sufficient time for review and intervention. Clearly document and communicate the presence and workings of the timelock mechanism to stakeholders to maintain transparency and trust in your contract's operations.

### Consider bounding input array length
If the number of for loop iterations is unbounded, then it may lead to the transaction to run out of gas. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

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

Implement a check at the beginning of your Solidity functions to enforce a maximum length on input arrays. Use a `require()` statement to validate that the length of any input array does not exceed a predetermined limit, which should be chosen based on the function's complexity and typical gas usage. This ensures that the function will not attempt to process more data than it can handle within reasonable gas limits, thereby preventing out-of-gas errors and improving the overall user experience. Clearly document this behavior and the rationale behind the chosen array size limit to inform users and developers interacting with your contract.

### Return values of `approve` not checked.
Not all `IERC20` implementations `revert` when there's a failure in `approve`. The function signature has a boolean return value and they indicate errors that way instead.

By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything. 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), 


#### Recommendation

To ensure the accuracy of token approvals and prevent potential issues, it's important to check the return values when calling the `approve` function of an `IERC20` contract. While some implementations use a boolean return value to indicate errors, not checking this return value may allow operations to proceed without the intended approval. Always verify the return value to confirm the success of the approval operation.

### Large transfers may not work with some `ERC20` tokens
Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed is larger than uint96. [Source](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers)


```solidity
Path: ./src/PredyPool.sol

85:            ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);	// @audit-issue

88:            ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);	// @audit-issue

187:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue

209:            ERC20(pool.token).safeTransfer(msg.sender, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L85-L85), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L88-L88), [187](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L187-L187), [209](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L209-L209), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

118:                    quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L118-L118), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

126:                quoteToken.safeTransfer(address(_predyPool), uint256(marginAmountUpdate));	// @audit-issue
```
[126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L126-L126), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

30:        ERC20(baseToken).safeTransferFrom(msg.sender, address(this), amountIn);	// @audit-issue

46:        ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);	// @audit-issue

54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L46-L46), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L54-L54), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

52:        ERC20(_pool.token).safeTransferFrom(msg.sender, address(this), _amount);	// @audit-issue

90:        ERC20(_pool.token).safeTransfer(msg.sender, finalWithdrawalAmount);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L52-L52), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L90-L90), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

69:                    ERC20(pairStatus.quotePool.token).safeTransfer(msg.sender, uint256(exceedsQuote));	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L69-L69), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

99:                    ERC20(pairStatus.quotePool.token).safeTransfer(vault.recipient, sentMarginAmount);	// @audit-issue

106:                ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L99-L99), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L106-L106), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

48:            ERC20(quoteToken).safeTransferFrom(	// @audit-issue

105:            ERC20(quoteToken).safeTransferFrom(sender, address(predyPool), quoteAmount);	// @audit-issue

123:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmountFromUni);	// @audit-issue

128:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmount - quoteAmountFromUni);	// @audit-issue

130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);	// @audit-issue

133:            ERC20(quoteToken).safeTransfer(address(predyPool), quoteAmount);	// @audit-issue

152:            ERC20(baseToken).safeTransferFrom(sender, address(predyPool), buyAmount);	// @audit-issue

170:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmountToUni);	// @audit-issue

175:                ERC20(quoteToken).safeTransfer(sender, quoteAmount - quoteAmountToUni);	// @audit-issue

177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);	// @audit-issue

180:            ERC20(quoteToken).safeTransfer(address(predyPool), settlementParams.maxQuoteAmount - quoteAmount);	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L48-L48), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L105-L105), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L123-L123), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L128-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L130-L130), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L133-L133), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L152-L152), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L175-L175), [177](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L177-L177), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L180-L180), 


```solidity
Path: ./src/types/GlobalData.sol

85:        ERC20(currency).safeTransfer(to, amount);	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L85-L85), 


#### Recommendation

When transferring tokens using ERC-20 contracts, such as UNI or COMP, it's important to be aware that not all IERC20 implementations are fully compliant, and they may not support large transfer values exceeding uint96. To avoid potential issues, it's essential to check the documentation or source code of the specific token you're using and ensure that your transfers adhere to the token's limitations.

### Division in comparison
To ensure accuracy in comparisons within programming, especially when dealing with integers, it's often more efficient to use multiplication rather than division. This approach stems from the fact that division operations are generally slower and more complex than multiplication. And in the context of solidity they can cause precision errors.

Suppose you want to compare if a/b is greater than c/d (where a, b, c, and d are integers). Instead of performing division, which is prone to precision errors, you can cross-multiply to avoid division. The comparison a/b > c/d is equivalent to ad > bc. This way, you only use multiplication, which is faster and avoids potential inaccuracies or complexities associated with division.

```solidity
Path: ./src/libraries/SlippageLib.sol

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L48), 


#### Recommendation

When comparing ratios or fractions in Solidity, avoid direct division operations within the comparison. Instead, cross-multiply to maintain precision and reduce computational complexity. For example, to compare if `a/b` is greater than `c/d`, where `a`, `b`, `c`, and `d` are integers, reframe the comparison as `a * d > b * c`. This approach avoids the division entirely, eliminating the risk of precision loss due to integer division truncation. Additionally, this method is more gas-efficient, as multiplication operations are cheaper than division. Apply this strategy in all relevant comparisons to enhance the accuracy and performance of your smart contracts. Always test thoroughly to ensure that the logic remains correct after the transformation.

### Large approvals may not work with some `ERC20` tokens
Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed is larger than uint96. [Source](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers)

```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), 


#### Recommendation

When working with ERC-20 tokens, particularly tokens like UNI or COMP, be aware that not all IERC20 implementations are fully compliant, and they may not support large approval values exceeding uint96. To avoid potential issues, it's essential to check the documentation or source code of the specific token you're interacting with and ensure that your approvals conform to the token's limitations.

### Unsafe ERC20 operation `approve()`
Approve call do not handle non-standard erc20 behavior. Use `safeApprove` instead of `approve`.
- Some token contracts do not return any value.
- Some token contracts revert the transaction when the allowance is not zero.

```solidity
Path: ./src/settlements/UniswapSettlement.sol

31:        ERC20(baseToken).approve(address(_swapRouter), amountIn);	// @audit-issue

47:        ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L31-L31), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L47-L47), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

111:        ERC20(baseToken).approve(address(settlementParams.contractAddress), sellAmount);	// @audit-issue

158:        ERC20(quoteToken).approve(address(settlementParams.contractAddress), settlementParams.maxQuoteAmount);	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L111-L111), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L158-L158), 


#### Recommendation

Use `safeApprove` instead of `approve`

### File allows a version of solidity that is susceptible to `.selector`-related optimizer bug
In solidity versions prior to 0.8.21, there is a legacy code generation [bug](https://soliditylang.org/blog/2023/07/19/missing-side-effects-on-selector-access-bug/) where if `foo().selector` is called, `foo()` doesn't actually get evaluated. It is listed as low-severity, because projects usually use the contract name rather than a function call to look up the selector. I've flagged all files using `.selector` where the version is vulnerable.

```solidity
Path: ./src/libraries/UniHelper.sol

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue

61:                abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos)	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L61-L61), 


#### Recommendation

Review your Solidity contracts for usage of the `.selector` property in combination with function calls, particularly if your project is compiled with a Solidity version prior to 0.8.21. To mitigate the risk associated with this optimizer bug.

### Possible loss of precision
Division by large numbers may result in precision loss due to rounding down, or even the result being erroneously equal to zero. Consider adding checks on the numerator to ensure precision loss is handled appropriately.


```solidity
Path: ./src/PriceFeed.sol

54:        uint256 price = uint256(int256(basePrice.price)) * Constants.Q96 / uint256(quoteAnswer);	// @audit-issue

55:        price = price * Constants.Q96 / _decimalsDiff;	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L54-L54), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L55-L55), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

56:        return perpAmount + _sqrtAmount * int256(Constants.Q96) / sqrtPrice;	// @audit-issue

84:        uint256 upperThreshold = userPosition.lastHedgedSqrtPrice * userPosition.sqrtPriceTrigger / Bps.ONE;	// @audit-issue

85:        uint256 lowerThreshold = userPosition.lastHedgedSqrtPrice * Bps.ONE / userPosition.sqrtPriceTrigger;	// @audit-issue

150:        uint256 elapsed = (currentTime - startTime) * Bps.ONE / auctionParams.auctionPeriod;	// @audit-issue

158:                + elapsed * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance) / Bps.ONE	// @audit-issue

176:        uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);	// @audit-issue

184:                + ratio * (auctionParams.maxSlippageTolerance - auctionParams.minSlippageTolerance)	// @audit-issue
185:                    / auctionParams.auctionRange
```
[56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L56-L56), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L85-L85), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L150-L150), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L158-L158), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L184-L185), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

233:        return (netValue / leverage).toInt256() - _calculatePositionValue(vault, sqrtPrice);	// @audit-issue

239:        return Math.abs(positionAmount) * price / Constants.Q96;	// @audit-issue
```
[233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L233-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L239-L239), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

87:        uint256 tradePrice = Math.abs(tradeResult.payoff.perpEntryUpdate + tradeResult.payoff.perpPayoff)	// @audit-issue
88:            * Constants.Q96 / Math.abs(tradeAmount);

182:            return (price1 - price2) * Bps.ONE / price2;	// @audit-issue

184:            return (price2 - price1) * Bps.ONE / price2;	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L87-L88), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L182-L182), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L184-L184), 


```solidity
Path: ./src/libraries/UniHelper.sol

29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L29-L29), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


```solidity
Path: ./src/libraries/Reallocation.sol

120:        result = (result / tickSpacing) * tickSpacing;	// @audit-issue

170:        uint160 sqrtPrice = (available * FixedPoint96.Q96 / liquidityAmount).toUint160();	// @audit-issue

184:        uint256 denominator1 = available * sqrtRatioB / FixedPoint96.Q96;	// @audit-issue

190:        uint160 sqrtPrice = uint160(liquidityAmount * sqrtRatioB / (liquidityAmount - denominator1));	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L120-L120), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L170-L170), [184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L184-L184), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L190-L190), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

193:        uint256 upperPrice = _sqrtPrice * _riskRatio / RISK_RATIO_ONE;	// @audit-issue

194:        uint256 lowerPrice = _sqrtPrice * RISK_RATIO_ONE / _riskRatio;	// @audit-issue

213:                (uint256(-_positionParams.amountSqrt) * Constants.Q96) / uint256(_positionParams.amountBase);	// @audit-issue
```
[193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L193-L193), [194](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L194-L194), [213](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L213-L213), 


```solidity
Path: ./src/libraries/Trade.sol

128:        swapResult.amountPerp = amountQuote * swapParams.amountPerp / amountBase;	// @audit-issue

129:        swapResult.amountSqrtPerp = amountQuote * swapParams.amountSqrtPerp / amountBase;	// @audit-issue

132:        swapResult.averagePrice = amountQuote * int256(Constants.Q96) / Math.abs(amountBase).toInt256();	// @audit-issue
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L129-L129), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L132-L132), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

21:        return (1600 * b * b / Constants.ONE) / Constants.ONE;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L21-L21), 


```solidity
Path: ./src/libraries/Perp.sol

166:            _sqrtAssetStatus.rebalanceInterestGrowthBase += _pairStatus.basePool.tokenStatus.settleUserFee(	// @audit-issue
167:                _sqrtAssetStatus.rebalancePositionBase
168:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

170:            _sqrtAssetStatus.rebalanceInterestGrowthQuote += _pairStatus.quotePool.tokenStatus.settleUserFee(	// @audit-issue
171:                _sqrtAssetStatus.rebalancePositionQuote
172:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);

398:        _assetStatus.fee0Growth += FullMath.mulDiv(	// @audit-issue

401:        _assetStatus.fee1Growth += FullMath.mulDiv(	// @audit-issue

405:        _assetStatus.borrowPremium0Growth += FullMath.mulDiv(f0, 1000 + spreadParam, 1000);	// @audit-issue

406:        _assetStatus.borrowPremium1Growth += FullMath.mulDiv(f1, 1000 + spreadParam, 1000);	// @audit-issue

609:        uint256 utilization = _assetStatus.borrowedAmount * Constants.ONE / _assetStatus.totalAmount;	// @audit-issue

689:                int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;	// @audit-issue

697:                int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;	// @audit-issue

785:            offsetStable += closeAmount * _userStatus.sqrtPerp.quoteRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue

786:            offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue
```
[166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L166-L168), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L170-L172), [398](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L398-L398), [401](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L401-L401), [405](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L405-L405), [406](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L406-L406), [609](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L609-L609), [689](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L689-L689), [697](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L697-L697), [785](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L785-L785), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L786-L786), 


```solidity
Path: ./src/libraries/SlippageLib.sol

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L48), 


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

168:        uint256 ratio = uint256(vaultValue * 1e4 / minMargin);	// @audit-issue
```
[168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L168-L168), 


```solidity
Path: ./src/libraries/math/Bps.sol

8:        return price * bps / ONE;	// @audit-issue

12:        return price * ONE / bps;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L12-L12), 


```solidity
Path: ./src/libraries/math/LPMath.sol

51:            uint256 r1 = FullMath.mulDiv(numerator, FixedPoint96.Q96, sqrtRatioB);	// @audit-issue

55:            uint256 r0 = FullMath.mulDiv(numerator, FixedPoint96.Q96, sqrtRatioA);	// @audit-issue

88:            uint256 r1 = FullMath.mulDiv(liquidityAmount, sqrtRatioB, FixedPoint96.Q96);	// @audit-issue

92:            uint256 r0 = FullMath.mulDiv(liquidityAmount, sqrtRatioA, FixedPoint96.Q96);	// @audit-issue

127:            return FullMath.mulDiv(liquidityAmount, FixedPoint96.Q96, sqrtRatio);	// @audit-issue

153:            return FullMath.mulDiv(liquidityAmount, sqrtRatio, FixedPoint96.Q96);	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L51-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L55-L55), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L88-L88), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L92-L92), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L127-L127), [153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L153-L153), 


```solidity
Path: ./src/libraries/math/Math.sol

28:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

30:            return -FullMath.mulDiv(uint256(-x), y, z).toInt256();	// @audit-issue

38:            return FullMath.mulDiv(uint256(x), y, z).toInt256();	// @audit-issue

63:        price = FullMath.mulDiv(sqrtPrice, sqrtPrice, Constants.Q96);	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L30-L30), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L38-L38), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L63-L63), 


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

Incorporate strategies in your Solidity contracts to mitigate precision loss in division operations. This can include:
1. Performing checks on the numerator and denominator to ensure they are within a reasonable range to avoid significant rounding errors.
2. Considering the use of fixed-point arithmetic libraries or scaling factors to handle divisions with higher precision.
3. Clearly documenting any inherent limitations of your division logic and providing guidelines for inputs to minimize unexpected behavior.
Always thoroughly test division operations under various scenarios to ensure that the outcomes are consistent with your contract's intended logic and accuracy requirements.

### Library function isn't `internal` or `private`
In a library, using an external or public visibility means that we won't be going through the library with a DELEGATECALL but with a CALL. This changes the context and should be done carefully.

```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

59:    function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)	// @audit-issue

106:    function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)	// @audit-issue

189:    function saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L59-L59), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L106-L106), [189](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L189-L189), 


```solidity
Path: ./src/libraries/Trade.sol

32:    function trade(	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L32-L32), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

16:    function getPairStatus(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) external {	// @audit-issue

24:    function getVaultStatus(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId) external {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L16-L16), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L24-L24), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

44:    function initializeGlobalData(GlobalDataLibrary.GlobalData storage global, address uniswapFactory) external {	// @audit-issue

53:    function addPair(	// @audit-issue

96:    function updateFeeRatio(DataType.PairStatus storage _pairStatus, uint8 _feeRatio) external {	// @audit-issue

104:    function updatePoolOwner(DataType.PairStatus storage _pairStatus, address _poolOwner) external {	// @audit-issue

112:    function updatePriceOracle(DataType.PairStatus storage _pairStatus, address _priceOracle) external {	// @audit-issue

118:    function updateAssetRiskParams(DataType.PairStatus storage _pairStatus, Perp.AssetRiskParams memory _riskParams)	// @audit-issue

128:    function updateIRMParams(	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L44-L44), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L53-L53), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L96-L96), [104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L104-L104), [112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L112-L112), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L118-L118), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L128-L128), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

22:    function supply(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)	// @audit-issue

57:    function withdraw(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L22-L22), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L57-L57), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

27:    function reallocate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId, bytes memory settlementData)	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L27-L27), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

39:    function liquidate(	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L39-L39), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

29:    function trade(	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L29-L29), 


#### Recommendation

Carefully assess the intended use of functions within your libraries and opt for `internal` or `private` visibility unless there's a specific reason to expose them via `external` or `public`. This practice encourages the use of `DELEGATECALL` where appropriate, maintaining the execution context of the calling contract and ensuring consistent access to storage variables.

