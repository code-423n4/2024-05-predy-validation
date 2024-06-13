
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

### `approve` can revert if the current approval is not zero
Some tokens like USDT check for the current approval, and they revert if it's not zero. While Tether is known to do this, it applies to other tokens as well, which are trying to protect against [this](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) attack vector.

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

When working with tokens that may revert when calling the `approve` function, be cautious and ensure that the current approval is set to zero before attempting to set a new approval. Tokens like USDT and others may follow this behavior to protect against potential attack vectors. Always check the token's documentation and handle approvals accordingly to avoid unexpected reverts.

### `approve` will always revert as the `IERC20` interface mismatch
Some tokens, such as [USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#code#L199), have a different implementation for the approve function: when the address is cast to a compliant `IERC20` interface and the approve function is used, it will always revert due to the interface mismatch.

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

When interacting with tokens like USDT that have a different implementation for the `approve` function, ensure that you use the correct interface and method to avoid reverts due to interface mismatches. Instead of casting to `IERC20`, use the specific interface that matches the token's implementation. Always review the token's documentation or contract to determine the correct interface and method to use when working with such tokens.

### Non-compliant `IERC20` tokens may revert with `transfer`
Some `IERC20` tokens (e.g. `BNB`, `OMG`, `USDT`) do not implement the standard properly, but they are still accepted by most code that accepts `ERC20` tokens.

For example, `USDT` transfer functions on L1 do not return booleans: when casted to `IERC20`, their function signatures do not match, and therefore the calls made will revert.

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

When dealing with tokens that may not fully comply with the `IERC20` standard, it's important to exercise caution and carefully verify the behavior of the specific token in question. In cases where tokens do not return booleans for transfer functions, consider implementing additional error-handling mechanisms to account for potential failures. This may include checking the token balance before and after the transfer to detect any discrepancies or using try-catch blocks to handle potential exceptions. Ensuring robust error handling can help your smart contract interact more gracefully with non-compliant tokens while maintaining security and reliability.

### Floating Pragma
A "floating pragma" in Solidity refers to the practice of using a pragma statement that does not specify a fixed compiler version but instead allows the contract to be compiled with any compatible compiler version. This issue arises when pragma statements like `pragma solidity ^0.8.0;` are used without a specific version number, allowing the contract to be compiled with the latest available compiler version. This can lead to various compatibility and stability issues.


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

Consider locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. [Consider known bugs](https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### Unneeded initializations of integer variable to `0`.
In Solidity, it is common practice to initialize variables with default values when declaring them. However, initializing integer variables to `0` when they are not subsequently used in the code can lead to unnecessary gas consumption and code clutter. This issue points out instances where such initializations are present but serve no functional purpose.

```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

65:        uint32 isEnabledUint = 0;	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L65-L65), 


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
Path: ./src/markets/perp/PerpMarketV1.sol

117:            uint256 cost = 0;	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L117-L117), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

87:        uint256 sentMarginAmount = 0;	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L87-L87), 


#### Recommendation


It is recommended not to initialize integer variables to `0` to save some Gas.


### Unused arguments should be removed or implemented
In Solidity, functions often have arguments that are intended for specific operations or logic within the function. However, sometimes these arguments remain unused in the function body, either due to changes during development or oversight. Unused arguments in functions can lead to confusion, making the code less readable and potentially misleading for other developers who might use or audit the contract. Moreover, they can create a false impression of the function's purpose and behavior. It's crucial to either implement these arguments in the function's logic as originally intended or remove them to ensure clarity and efficiency of the code.

```solidity
Path: ./src/PredyPool.sol

78:    function uniswapV3MintCallback(uint256 amount0, uint256 amount1, bytes calldata) external override {	// @audit-issue: None
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L78-L78), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

23:        address,	// @audit-issue: None

40:        address,	// @audit-issue: None
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L23-L23), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L40-L40), 


#### Recommendation

Eliminate unused arguments in Solidity function definitions to enhance code clarity and efficiency. If an argument is currently not utilized in the function's logic, assess its potential future utility. If it serves no purpose, removing it simplifies the function signature and aligns the code more closely with its actual operation, contributing to a cleaner and more understandable contract structure.

### `else`-block not required
One level of nesting can be removed by not having an `else` block when the `if`-block returns, and `if (foo) { return 1; } else { return 2; }` becomes `if (foo) { return 1; } return 2;`


```solidity
Path: ./src/PredyPool.sol

385:        if (isQuoteToken) {
386:            return globalData.pairs[pairId].quotePool;
387:        } else {	// @audit-issue
388:            return globalData.pairs[pairId].basePool;
389:        }
```
[387](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L385-L389), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

56:                    if (currentPositionAmount > -tradeAmount) {
57:                        return tradeAmount;
58:                    } else {	// @audit-issue
59:                        return -currentPositionAmount;
60:                    }

66:                    if (-currentPositionAmount > tradeAmount) {
67:                        return tradeAmount;
68:                    } else {	// @audit-issue
69:                        return -currentPositionAmount;
70:                    }

181:        } else if (price1 > price2) {
182:            return (price1 - price2) * Bps.ONE / price2;
183:        } else {	// @audit-issue
184:            return (price2 - price1) * Bps.ONE / price2;
185:        }
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L56-L60), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L66-L70), [183](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L181-L185), 


```solidity
Path: ./src/libraries/UniHelper.sol

28:        if (isQuoteZero) {
29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);
30:        } else {	// @audit-issue
31:            return sqrtPriceX96;
32:        }
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L28-L32), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

103:        if (debtRiskRatio == 0) {
104:            return Constants.BASE_MIN_COLLATERAL_WITH_DEBT;
105:        } else {	// @audit-issue
106:            return debtRiskRatio;
107:        }

142:        if (pairStatus.priceFeed != address(0)) {
143:            return PriceFeed(pairStatus.priceFeed).getSqrtPrice();
144:        } else {	// @audit-issue
145:            return UniHelper.convertSqrtPrice(
146:                UniHelper.getSqrtTWAP(pairStatus.sqrtAssetStatus.uniswapPool), pairStatus.isQuoteZero
147:            );
148:        }
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L103-L107), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L142-L148), 


```solidity
Path: ./src/libraries/VaultLib.sol

30:        if (vaultId == 0) {
31:            uint256 finalVaultId = globalData.vaultCount;
32:
33:            // Initialize a new vault
34:            DataType.Vault storage vault = globalData.vaults[finalVaultId];
35:
36:            vault.id = finalVaultId;
37:            vault.owner = msg.sender;
38:            vault.recipient = msg.sender;
39:            vault.openPosition.pairId = pairId;
40:            vault.quoteToken = quoteToken;
41:
42:            globalData.vaultCount++;
43:
44:            emit VaultCreated(vault.id, vault.owner, quoteToken, pairId);
45:
46:            return vault.id;
47:        } else {	// @audit-issue
48:            DataType.Vault memory vault = globalData.vaults[vaultId];
49:
50:            // Ensure the caller is the owner of the existing vault
51:            if (vault.owner != msg.sender) {
52:                revert IPredyPool.CallerIsNotVaultOwner();
53:            }
54:
55:            if (vault.quoteToken != quoteToken) {
56:                revert IPredyPool.VaultAlreadyHasAnotherMarginId();
57:            }
58:
59:            if (vault.openPosition.pairId != pairId) {
60:                revert IPredyPool.VaultAlreadyHasAnotherPair();
61:            }
62:
63:            return vault.id;
64:        }
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L30-L64), 


```solidity
Path: ./src/libraries/Perp.sol

597:        if (available >= buffer) {
598:            return available - buffer;
599:        } else {	// @audit-issue
600:            return 0;
601:        }
```
[599](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L597-L601), 


```solidity
Path: ./src/libraries/math/LPMath.sol

61:        if (swaped) {
62:            return -r;
63:        } else {	// @audit-issue
64:            return r;
65:        }

98:        if (swaped) {
99:            return -r;
100:        } else {	// @audit-issue
101:            return r;
102:        }

124:        if (isRoundUp) {
125:            return FullMath.mulDivRoundingUp(liquidityAmount, FixedPoint96.Q96, sqrtRatio);
126:        } else {	// @audit-issue
127:            return FullMath.mulDiv(liquidityAmount, FixedPoint96.Q96, sqrtRatio);
128:        }

150:        if (isRoundUp) {
151:            return FullMath.mulDivRoundingUp(liquidityAmount, sqrtRatio, FixedPoint96.Q96);
152:        } else {	// @audit-issue
153:            return FullMath.mulDiv(liquidityAmount, sqrtRatio, FixedPoint96.Q96);
154:        }
```
[63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L61-L65), [100](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L98-L102), [126](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L124-L128), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L150-L154), 


```solidity
Path: ./src/libraries/math/Math.sol

27:        } else if (x > 0) {
28:            return FullMath.mulDiv(uint256(x), y, z).toInt256();
29:        } else {	// @audit-issue
30:            return -FullMath.mulDiv(uint256(-x), y, z).toInt256();
31:        }

37:        } else if (x > 0) {
38:            return FullMath.mulDiv(uint256(x), y, z).toInt256();
39:        } else {	// @audit-issue
40:            return -FullMath.mulDivRoundingUp(uint256(-x), y, z).toInt256();
41:        }

47:        } else if (x > 0) {
48:            return FixedPointMathLib.mulDivDown(uint256(x), y, z).toInt256();
49:        } else {	// @audit-issue
50:            return -FixedPointMathLib.mulDivUp(uint256(-x), y, z).toInt256();
51:        }

55:        if (b >= 0) {
56:            return a + uint256(b);
57:        } else {	// @audit-issue
58:            return a - uint256(-b);
59:        }
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L27-L31), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L37-L41), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L47-L51), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L55-L59), 


#### Recommendation

Consider simplifying code by removing unnecessary `else` blocks when the `if` block returns. You can achieve cleaner and more concise code by directly returning a value after the `if` block instead of using an `else` block for a subsequent return statement.

### Unused import
The identifier is imported but never used within the file.

```solidity
Path: ./src/PredyPool.sol

11:import {IHooks} from "./interfaces/IHooks.sol";	// @audit-issue: IHooks not used in the contract.

12:import {ISettlement} from "./interfaces/ISettlement.sol";	// @audit-issue: ISettlement not used in the contract.
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L12-L12), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

6:import {GammaOrder, GammaOrderLib, GammaModifyInfo} from "./GammaOrder.sol";	// @audit-issue: GammaOrderLib not used in the contract.
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L6-L6), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

15:import {Bps} from "../../libraries/math/Bps.sol";	// @audit-issue: Bps not used in the contract.
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L15-L15), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

6:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: IFillerMarket not used in the contract.
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L6-L6), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

5:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: IFillerMarket not used in the contract.

6:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: IPredyPool not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L6-L6), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

6:import {DataType} from "../../libraries/DataType.sol";	// @audit-issue: DataType not used in the contract.

7:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: IPredyPool not used in the contract.

8:import {SlippageLib} from "../../libraries/SlippageLib.sol";	// @audit-issue: SlippageLib not used in the contract.
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L8-L8), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

5:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: IFillerMarket not used in the contract.

6:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: IPredyPool not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L6-L6), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

14:import {SlippageLib} from "../../libraries/SlippageLib.sol";	// @audit-issue: SlippageLib not used in the contract.

17:import {Perp} from "../../libraries/Perp.sol";	// @audit-issue: Perp not used in the contract.

22:import {PredyPoolQuoter} from "../../lens/PredyPoolQuoter.sol";	// @audit-issue: PredyPoolQuoter not used in the contract.

23:import {SettlementCallbackLib} from "../../base/SettlementCallbackLib.sol";	// @audit-issue: SettlementCallbackLib not used in the contract.
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L14-L14), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L17-L17), [22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L22-L22), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L23-L23), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

5:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: IFillerMarket not used in the contract.

6:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: IPredyPool not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L6-L6), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

9:import {Bps} from "../../libraries/math/Bps.sol";	// @audit-issue: Bps not used in the contract.

10:import {DataType} from "../../libraries/DataType.sol";	// @audit-issue: DataType not used in the contract.
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L10-L10), 


```solidity
Path: ./src/libraries/Trade.sol

4:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: IERC20 not used in the contract.

7:import {IHooks} from "../interfaces/IHooks.sol";	// @audit-issue: IHooks not used in the contract.

8:import {ISettlement} from "../interfaces/ISettlement.sol";	// @audit-issue: ISettlement not used in the contract.

14:import {LockDataLibrary} from "../types/LockData.sol";	// @audit-issue: LockDataLibrary not used in the contract.

15:import {PositionCalculator} from "./PositionCalculator.sol";	// @audit-issue: PositionCalculator not used in the contract.
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L4-L4), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L8-L8), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L14-L14), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L15-L15), 


```solidity
Path: ./src/libraries/SlippageLib.sol

5:import {Constants} from "./Constants.sol";	// @audit-issue: Constants not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L5-L5), 


```solidity
Path: ./src/libraries/DataType.sol

4:import {Perp} from "./Perp.sol";	// @audit-issue: Perp not used in the contract.
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/DataType.sol#L4-L4), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

5:import {Constants} from "../Constants.sol";	// @audit-issue: Constants not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L5-L5), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

5:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue: IERC20 not used in the contract.
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L5-L5), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

6:import {ISettlement} from "../../interfaces/ISettlement.sol";	// @audit-issue: ISettlement not used in the contract.
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L6-L6), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

7:import {IHooks} from "../../interfaces/IHooks.sol";	// @audit-issue: IHooks not used in the contract.

8:import {ISettlement} from "../../interfaces/ISettlement.sol";	// @audit-issue: ISettlement not used in the contract.

10:import {Constants} from "../Constants.sol";	// @audit-issue: Constants not used in the contract.

18:import {ScaledAsset} from "../ScaledAsset.sol";	// @audit-issue: ScaledAsset not used in the contract.
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L8-L8), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L10-L10), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L18-L18), 


```solidity
Path: ./src/libraries/logic/TradeLogic.sol

6:import {ISettlement} from "../../interfaces/ISettlement.sol";	// @audit-issue: ISettlement not used in the contract.

13:import {ScaledAsset} from "../ScaledAsset.sol";	// @audit-issue: ScaledAsset not used in the contract.
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L6-L6), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/TradeLogic.sol#L13-L13), 


#### Recommendation

Regularly review your Solidity code to remove unused imports. This practice declutters the codebase, making it easier to understand and maintain. In addition, consider using tools or IDE features that can automatically detect and highlight unused imports for cleanup. Keeping imports limited to only what is necessary helps maintain a clear understanding of the contract's dependencies and reduces potential confusion for developers working on or reviewing the code.

### Excessive Authorization in Contract Functions
A contract where a high percentage of functions require authorization (e.g., restricted to the contract owner or specific roles) may indicate over-centralization or excessive control. This could limit the contract's flexibility, reduce trust among users, and potentially create bottleneck points that could be exploited or become failure points. While some level of control is necessary for administrative purposes, overly restrictive access can detract from the decentralized nature of blockchain applications and concentrate too much power in the hands of a few.

```solidity
Path: ./src/tokenization/SupplyToken.sol

7:contract SupplyToken is ERC20, ISupplyToken {	// @audit-issue: %100.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `mint`, `burn`
	List of functions that require authorization: `mint`, `burn`
	List of functions that doesn't require authorization: None
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L7-L7), 


#### Recommendation

Make contract more decentralized.

### Missing Functionality
That function(s) lacks any implemented logic and merely emits events. If emitting those values is crucial for off-chain processes, then those events should be emitted in the function where the actual addition operation occurs.

```solidity
Path: ./src/libraries/ApplyInterestLib.sol

75:    function emitInterestGrowthEvent(	// @audit-issue
76:        DataType.PairStatus memory assetStatus,
77:        uint256 interestRateQuote,
78:        uint256 interestRateBase,
79:        uint256 totalProtocolFeeQuote,
80:        uint256 totalProtocolFeeBase
81:    ) internal {
82:        emit InterestGrowthUpdated(
83:            assetStatus.id,
84:            assetStatus.quotePool.tokenStatus,
85:            assetStatus.basePool.tokenStatus,
86:            interestRateQuote,
87:            interestRateBase,
88:            totalProtocolFeeQuote,
89:            totalProtocolFeeBase
90:        );
91:    }
```
[75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L75-L91), 


#### Recommendation

Either implement a functionality to that function(s) or remove it from the contract.

### `Internal` Functions Not Called by The Contract Should Be Removed
In Solidity, functions labeled as `internal` are meant to be used only within the contract in which they're defined or in derived contracts. They cannot be accessed from external transactions. While `internal` functions offer modularity and can be leveraged to break down complex logic, it's essential to monitor their relevance during the contract's lifecycle.

A common oversight during smart contract development is the presence of `internal` functions that remain within the contract code but are never called by any other function or part of the contract. These redundant functions occupy space, make the contract's bytecode longer, and, as a consequence, increase the contract deployment cost. Moreover, they can add unnecessary complexity to the code, making it harder to read, maintain, and audit.


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol



10:    function toPermit(ResolvedOrder memory order)	// @audit-issue

23:    function transferDetails(ResolvedOrder memory order, address to)	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L34-L34), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L71-L71), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L10-L10), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L23-L23), 


```solidity
Path: ./src/markets/L2Decoder.sol

5:    function decodeSpotOrderParams(bytes32 args1, bytes32 args2)	// @audit-issue

38:    function decodePerpOrderParams(bytes32 args)	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L5-L5), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L38-L38), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

5:    function addItem(uint256[] storage items, uint256 item) internal {	// @audit-issue

9:    function removeItem(uint256[] storage items, uint256 item) internal {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L5-L5), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L9-L9), 


```solidity
Path: ./src/libraries/UniHelper.sol

87:    function getFeeGrowthInsideLast(address uniswapPoolAddress, int24 tickLower, int24 tickUpper)	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L87-L87), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

135:    function getHasPosition(DataType.Vault memory _vault) internal pure returns (bool hasPosition) {	// @audit-issue
```
[135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L135-L135), 


```solidity
Path: ./src/libraries/VaultLib.sol

11:    function getVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId)	// @audit-issue

24:    function createOrGetVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId, uint256 pairId)	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L11-L11), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L24-L24), 


```solidity
Path: ./src/libraries/Perp.sol

145:    function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {	// @audit-issue
```
[145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L145-L145), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

37:    function addAsset(AssetStatus storage tokenState, uint256 _amount) internal returns (uint256 claimAmount) {	// @audit-issue

47:    function removeAsset(AssetStatus storage tokenState, uint256 _supplyTokenAmount, uint256 _amount)	// @audit-issue

186:    function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L37-L37), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L47-L47), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L186-L186), 


```solidity
Path: ./src/libraries/math/Math.sol

20:    function min(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue

54:    function addDelta(uint256 a, int256 b) internal pure returns (uint256) {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L20-L20), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L54-L54), 


```solidity
Path: ./src/types/GlobalData.sol

26:    function validateVaultId(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId) internal view {	// @audit-issue

35:    function initializeLock(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal {	// @audit-issue

46:    function callSettlementCallback(	// @audit-issue

62:    function finalizeLock(GlobalDataLibrary.GlobalData storage globalData)	// @audit-issue

72:    function take(GlobalDataLibrary.GlobalData storage globalData, bool isQuoteAsset, address to, uint256 amount)	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L26-L26), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L35-L35), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L46-L46), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L62-L62), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L72-L72), 


#### Recommendation

To maintain clean and efficient code, review the contract's `internal` functions to identify and remove any that are no longer in use or serve no purpose. Removing unused `internal` functions can improve code readability, reduce deployment costs, and enhance the overall quality of the smart contract.

### Functions contain the same code
The functions below have the same implementation as is seen in other files. The functions should be refactored into functions of a common base contract.

```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

92:    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)	// @audit-issue: Seen on line 69 of ./src/markets/gamma/GammaTradeMarket.sol
```
[92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L92-L92), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

145:    function updateQuoteTokenMap(uint256 pairId) external {	// @audit-issue: Seen on line 97 of ./src/base/BaseMarket.sol

152:    function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {	// @audit-issue: Seen on line 104 of ./src/base/BaseMarket.sol

156:    function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {	// @audit-issue: Seen on line 108 of ./src/base/BaseMarket.sol

162:    function _revertTradeResult(IPredyPool.TradeResult memory tradeResult) internal pure {	// @audit-issue: Seen on line 114 of ./src/base/BaseMarket.sol
```
[145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L145-L145), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L152-L152), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L156-L156), [162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L162-L162), 


```solidity
Path: ./src/base/BaseHookCallback.sol

16:    modifier onlyPredyPool() {	// @audit-issue: Seen on line 15 of ./src/base/BaseHookCallbackUpgradable.sol
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L16-L16), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### `if`-statement can be converted to a ternary
The code can be made more compact while also increasing readability by converting the following `if`-statements to ternaries (e.g. `foo += (x > y) ? a : b`)

```solidity
Path: ./src/PredyPool.sol

84:        if (amount0 > 0) {	// @audit-issue
85:            ERC20(uniswapPool.token0()).safeTransfer(msg.sender, amount0);
86:        }

87:        if (amount1 > 0) {	// @audit-issue
88:            ERC20(uniswapPool.token1()).safeTransfer(msg.sender, amount1);
89:        }

186:        if (amount > 0) {	// @audit-issue
187:            ERC20(pool.token).safeTransfer(msg.sender, amount);
188:        }

208:        if (amount > 0) {	// @audit-issue
209:            ERC20(pool.token).safeTransfer(msg.sender, amount);
210:        }

385:        if (isQuoteToken) {	// @audit-issue
386:            return globalData.pairs[pairId].quotePool;
387:        } else {
388:            return globalData.pairs[pairId].basePool;
389:        }
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L84-L86), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L87-L89), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L186-L188), [208](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L208-L210), [385](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L385-L389), 


```solidity
Path: ./src/markets/L2Decoder.sol

26:        if (isLimitUint == 1) {	// @audit-issue
27:            isLimit = true;
28:        } else {
29:            isLimit = false;
30:        }
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L26-L30), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

78:        if (isEnabledUint == 1) {	// @audit-issue
79:            isEnabled = true;
80:        } else {
81:            isEnabled = false;
82:        }
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L78-L82), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

146:        if (closeRatio == 1e18) {	// @audit-issue
147:            _removePosition(vaultId);
148:        }

342:        if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {	// @audit-issue
343:            return (false, false, positionId);
344:        }

378:        if (userPosition.vaultId == 0) {	// @audit-issue
379:            // if user has no position, return empty vault status and vault
380:            return result;
381:        }
```
[146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L146-L148), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L342-L344), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L378-L381), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

64:        if (	// @audit-issue
65:            userPosition.hedgeInterval > 0
66:                && userPosition.lastHedgedTime + userPosition.hedgeInterval <= block.timestamp
67:        ) {
68:            return (
69:                true,
70:                calculateSlippageTolerance(
71:                    userPosition.lastHedgedTime + userPosition.hedgeInterval,
72:                    block.timestamp,
73:                    userPosition.auctionParams
74:                    ),
75:                GammaTradeMarketLib.CallbackType.HEDGE_BY_TIME
76:            );
77:        }

80:        if (userPosition.sqrtPriceTrigger == 0) {	// @audit-issue
81:            return (false, 0, GammaTradeMarketLib.CallbackType.NONE);
82:        }

87:        if (lowerThreshold >= sqrtIndexPrice) {	// @audit-issue
88:            return (
89:                true,
90:                calculateSlippageToleranceByPrice(sqrtIndexPrice, lowerThreshold, userPosition.auctionParams),
91:                GammaTradeMarketLib.CallbackType.HEDGE_BY_PRICE
92:            );
93:        }

95:        if (upperThreshold <= sqrtIndexPrice) {	// @audit-issue
96:            return (
97:                true,
98:                calculateSlippageToleranceByPrice(upperThreshold, sqrtIndexPrice, userPosition.auctionParams),
99:                GammaTradeMarketLib.CallbackType.HEDGE_BY_PRICE
100:            );
101:        }

111:        if (userPosition.expiration <= block.timestamp) {	// @audit-issue
112:            return (
113:                true,
114:                calculateSlippageTolerance(userPosition.expiration, block.timestamp, userPosition.auctionParams),
115:                CallbackType.CLOSE_BY_TIME
116:            );
117:        }

122:        if (lowerThreshold > 0 && lowerThreshold >= sqrtIndexPrice) {	// @audit-issue
123:            return (
124:                true,
125:                calculateSlippageToleranceByPrice(sqrtIndexPrice, lowerThreshold, userPosition.auctionParams),
126:                CallbackType.CLOSE_BY_PRICE
127:            );
128:        }

130:        if (upperThreshold > 0 && upperThreshold <= sqrtIndexPrice) {	// @audit-issue
131:            return (
132:                true,
133:                calculateSlippageToleranceByPrice(upperThreshold, sqrtIndexPrice, userPosition.auctionParams),
134:                CallbackType.CLOSE_BY_PRICE
135:            );
136:        }

146:        if (currentTime <= startTime) {	// @audit-issue
147:            return auctionParams.minSlippageTolerance;
148:        }

152:        if (elapsed > Bps.ONE) {	// @audit-issue
153:            return auctionParams.maxSlippageTolerance;
154:        }

172:        if (price2 <= price1) {	// @audit-issue
173:            return auctionParams.minSlippageTolerance;
174:        }

178:        if (ratio > auctionParams.auctionRange) {	// @audit-issue
179:            return auctionParams.maxSlippageTolerance;
180:        }

193:        if (!modifyInfo.isEnabled) {	// @audit-issue
194:            return false;
195:        }
```
[64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L64-L77), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L80-L82), [87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L87-L93), [95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L95-L101), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L111-L117), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L122-L128), [130](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L130-L136), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L146-L148), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L152-L154), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L172-L174), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L178-L180), [193](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L193-L195), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

257:        if (userPosition.vaultId == 0) {	// @audit-issue
258:            // if user has no position, return empty vault status and vault
259:            return (userPosition, vaultStatus, vault);
260:        }
```
[257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L257-L260), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

123:        if (tradeAmount == 0) {	// @audit-issue
124:            return false;
125:        }

127:        if (tradeAmount > 0 && limitPrice < tradePrice) {	// @audit-issue
128:            return false;
129:        }

131:        if (tradeAmount < 0 && limitPrice > tradePrice) {	// @audit-issue
132:            return false;
133:        }

181:        } else if (price1 > price2) {	// @audit-issue
182:            return (price1 - price2) * Bps.ONE / price2;
183:        } else {
184:            return (price2 - price1) * Bps.ONE / price2;
185:        }
```
[123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L123-L125), [127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L127-L129), [131](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L131-L133), [181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L181-L185), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

53:        if (amountInMaximum > amountIn) {	// @audit-issue
54:            ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);
55:        }
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L53-L55), 


```solidity
Path: ./src/libraries/UniHelper.sol

28:        if (isQuoteZero) {	// @audit-issue
29:            return uint160((Constants.Q96 << Constants.RESOLUTION) / sqrtPriceX96);
30:        } else {
31:            return sqrtPriceX96;
32:        }
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L28-L32), 


```solidity
Path: ./src/libraries/Reallocation.sol

116:        } else if (result > TickMath.MAX_TICK) {	// @audit-issue
117:            result = TickMath.MAX_TICK;
118:        }

139:        if (minLowerTick > currentLowerTick - tickSpacing) {	// @audit-issue
140:            minLowerTick = currentLowerTick - tickSpacing;
141:        }

160:        if (maxUpperTick < currentUpperTick + tickSpacing) {	// @audit-issue
161:            maxUpperTick = currentUpperTick + tickSpacing;
162:        }

172:        if (sqrtRatioA <= sqrtPrice + TickMath.MIN_SQRT_RATIO) {	// @audit-issue
173:            return TickMath.MIN_SQRT_RATIO + 1;
174:        }

186:        if (liquidityAmount <= denominator1) {	// @audit-issue
187:            return TickMath.MAX_SQRT_RATIO - 1;
188:        }

192:        if (sqrtPrice <= TickMath.MIN_SQRT_RATIO) {	// @audit-issue
193:            return TickMath.MIN_SQRT_RATIO + 1;
194:        }
```
[116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L116-L118), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L139-L141), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L160-L162), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L172-L174), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L186-L188), [192](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L192-L194), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

97:        if (hasPosition && minMargin < Constants.MIN_MARGIN_AMOUNT) {	// @audit-issue
98:            minMargin = Constants.MIN_MARGIN_AMOUNT;
99:        }

103:        if (debtRiskRatio == 0) {	// @audit-issue
104:            return Constants.BASE_MIN_COLLATERAL_WITH_DEBT;
105:        } else {
106:            return debtRiskRatio;
107:        }

142:        if (pairStatus.priceFeed != address(0)) {	// @audit-issue
143:            return PriceFeed(pairStatus.priceFeed).getSqrtPrice();
144:        } else {
145:            return UniHelper.convertSqrtPrice(
146:                UniHelper.getSqrtTWAP(pairStatus.sqrtAssetStatus.uniswapPool), pairStatus.isQuoteZero
147:            );
148:        }

198:            if (v < minValue) {	// @audit-issue
199:                minValue = v;
200:            }

205:            if (v < minValue) {	// @audit-issue
206:                minValue = v;
207:            }

245:        if (squartPosition > 0) {	// @audit-issue
246:            return 0;
247:        }
```
[97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L97-L99), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L103-L107), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L142-L148), [198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L198-L200), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L205-L207), [245](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L245-L247), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

32:        if (pairStatus.lastUpdateTimestamp >= block.timestamp) {	// @audit-issue
33:            return;
34:        }

45:        if (interestRateQuote > 0 || interestRateBase > 0) {	// @audit-issue
46:            emitInterestGrowthEvent(pairStatus, interestRateQuote, interestRateBase, protocolFeeQuote, protocolFeeBase);
47:        }

54:        if (block.timestamp <= lastUpdateTimestamp) {	// @audit-issue
55:            return (0, 0);
56:        }

61:        if (utilizationRatio == 0) {	// @audit-issue
62:            return (0, 0);
63:        }
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L32-L34), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L45-L47), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L54-L56), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L61-L63), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

15:        if (utilization <= Constants.SQUART_KINK_UR) {	// @audit-issue
16:            return 0;
17:        }
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L15-L17), 


```solidity
Path: ./src/libraries/Perp.sol

254:        if (isOutOfRange) {	// @audit-issue
255:            (deltaPositionBase, deltaPositionQuote) =
256:                swapForOutOfRange(_assetStatusUnderlying, currentSqrtPrice, tick, totalLiquidityAmount);
257:        }

349:        if (deltaPositionUnderlying == 0 && deltaPositionStable == 0) {	// @audit-issue
350:            return;
351:        }

374:        if (_assetStatus.totalAmount == 0) {	// @audit-issue
375:            return;
376:        }

390:        if (f0 == 0 && f1 == 0) {	// @audit-issue
391:            return;
392:        }

432:        if (_tradeSqrtAmount == 0) {	// @audit-issue
433:            return (0, 0);
434:        }

546:        if (_assetStatus.totalAmount == _assetStatus.borrowedAmount) {	// @audit-issue
547:            // if available liquidity was 0 and added first liquidity then update last fee growth
548:            saveLastFeeGrowth(_assetStatus);
549:        }

593:        if (_isWithdraw && _assetStatus.borrowedAmount == 0) {	// @audit-issue
594:            return available;
595:        }

597:        if (available >= buffer) {	// @audit-issue
598:            return available - buffer;
599:        } else {
600:            return 0;
601:        }

605:        if (_assetStatus.totalAmount == 0) {	// @audit-issue
606:            return 0;
607:        }

611:        if (utilization > 1e18) {	// @audit-issue
612:            return 1e18;
613:        }

678:        if (_tradeAmount == 0) {	// @audit-issue
679:            return (0, 0);
680:        }
```
[254](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L254-L257), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L349-L351), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L374-L376), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L390-L392), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L432-L434), [546](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L546-L549), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L593-L595), [597](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L597-L601), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L605-L607), [611](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L611-L613), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L678-L680), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

38:        if (_amount == 0) {	// @audit-issue
39:            return 0;
40:        }

51:        if (_amount == 0) {	// @audit-issue
52:            return (0, 0);
53:        }

59:        if (_supplyTokenAmount < burnAmount) {	// @audit-issue
60:            finalBurnAmount = _supplyTokenAmount;
61:        } else {
62:            finalBurnAmount = burnAmount;
63:        }

86:        } else if (userStatus.positionAmount < 0) {	// @audit-issue
87:            require(userStatus.lastFeeGrowth == tokenStatus.debtGrowth, "S2");
88:        }

135:        if (_userStatus.positionAmount > 0) {	// @audit-issue
136:            interestFee = (getAssetFee(_assetStatus, _userStatus)).toInt256();
137:        } else {
138:            interestFee = -(getDebtFee(_assetStatus, _userStatus)).toInt256();
139:        }

148:        if (_userStatus.positionAmount > 0) {	// @audit-issue
149:            _userStatus.lastFeeGrowth = _assetStatus.assetGrowth;
150:        } else {
151:            _userStatus.lastFeeGrowth = _assetStatus.debtGrowth;
152:        }

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
191:            return 0;
192:        }

231:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
232:            return 0;
233:        }

239:        if (utilization > 1e18) {	// @audit-issue
240:            return 1e18;
241:        }
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L38-L40), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L51-L53), [59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L59-L63), [86](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L86-L88), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L135-L139), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L148-L152), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L192), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L231-L233), [239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L239-L241), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

37:        if (_isStable) {	// @audit-issue
38:            mintAmount = receiveTokenAndMintBond(pair.quotePool, _amount);
39:        } else {
40:            mintAmount = receiveTokenAndMintBond(pair.basePool, _amount);
41:        }

70:        if (_isStable) {	// @audit-issue
71:            (finalburntAmount, finalWithdrawalAmount) = burnBondAndTransferToken(pair.quotePool, _amount);
72:        } else {
73:            (finalburntAmount, finalWithdrawalAmount) = burnBondAndTransferToken(pair.basePool, _amount);
74:        }
```
[37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L37-L41), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L70-L74), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

164:        if (vaultValue <= 0 || minMargin == 0) {	// @audit-issue
165:            return riskParams.maxSlippage;
166:        }

170:        if (ratio > 1e4) {	// @audit-issue
171:            return riskParams.minSlippage;
172:        }
```
[164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L164-L166), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L170-L172), 


```solidity
Path: ./src/libraries/math/LPMath.sol

36:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue
37:            return 0;
38:        }

61:        if (swaped) {	// @audit-issue
62:            return -r;
63:        } else {
64:            return r;
65:        }

74:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue
75:            return 0;
76:        }

98:        if (swaped) {	// @audit-issue
99:            return -r;
100:        } else {
101:            return r;
102:        }

124:        if (isRoundUp) {	// @audit-issue
125:            return FullMath.mulDivRoundingUp(liquidityAmount, FixedPoint96.Q96, sqrtRatio);
126:        } else {
127:            return FullMath.mulDiv(liquidityAmount, FixedPoint96.Q96, sqrtRatio);
128:        }

150:        if (isRoundUp) {	// @audit-issue
151:            return FullMath.mulDivRoundingUp(liquidityAmount, sqrtRatio, FixedPoint96.Q96);
152:        } else {
153:            return FullMath.mulDiv(liquidityAmount, sqrtRatio, FixedPoint96.Q96);
154:        }
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L36-L38), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L61-L65), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L74-L76), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L98-L102), [124](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L124-L128), [150](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L150-L154), 


```solidity
Path: ./src/libraries/math/Math.sol

27:        } else if (x > 0) {	// @audit-issue
28:            return FullMath.mulDiv(uint256(x), y, z).toInt256();
29:        } else {
30:            return -FullMath.mulDiv(uint256(-x), y, z).toInt256();
31:        }

37:        } else if (x > 0) {	// @audit-issue
38:            return FullMath.mulDiv(uint256(x), y, z).toInt256();
39:        } else {
40:            return -FullMath.mulDivRoundingUp(uint256(-x), y, z).toInt256();
41:        }

47:        } else if (x > 0) {	// @audit-issue
48:            return FixedPointMathLib.mulDivDown(uint256(x), y, z).toInt256();
49:        } else {
50:            return -FixedPointMathLib.mulDivUp(uint256(-x), y, z).toInt256();
51:        }

55:        if (b >= 0) {	// @audit-issue
56:            return a + uint256(b);
57:        } else {
58:            return a - uint256(-b);
59:        }
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L27-L31), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L37-L41), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L47-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L55-L59), 


```solidity
Path: ./src/base/BaseMarket.sol

98:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue
99:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);
100:        }
```
[98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L98-L100), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

72:        if (fee < settlementParamsV3.minFee) {	// @audit-issue
73:            fee = settlementParamsV3.minFee;
74:        }

146:        if (_quoteTokenMap[pairId] == address(0)) {	// @audit-issue
147:            _quoteTokenMap[pairId] = _getQuoteTokenAddress(pairId);
148:        }
```
[72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L72-L74), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L146-L148), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

47:        if (settlementParams.fee < 0) {	// @audit-issue
48:            ERC20(quoteToken).safeTransferFrom(
49:                settlementParams.sender, address(predyPool), uint256(-settlementParams.fee)
50:            );
51:        }

55:        if (settlementParams.fee > 0) {	// @audit-issue
56:            predyPool.take(true, settlementParams.sender, uint256(settlementParams.fee));
57:        }

77:        } else if (baseAmountDelta < 0) {	// @audit-issue
78:            buy(
79:                predyPool,
80:                quoteToken,
81:                baseToken,
82:                settlementParams,
83:                settlementParams.sender,
84:                settlementParams.price,
85:                uint256(-baseAmountDelta)
86:            );
87:        }

129:            } else if (quoteAmountFromUni > quoteAmount) {	// @audit-issue
130:                ERC20(quoteToken).safeTransfer(sender, quoteAmountFromUni - quoteAmount);
131:            }

176:            } else if (quoteAmountToUni > quoteAmount) {	// @audit-issue
177:                ERC20(quoteToken).safeTransferFrom(sender, address(this), quoteAmountToUni - quoteAmount);
178:            }
```
[47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L47-L51), [55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L55-L57), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L77-L87), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L129-L131), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L176-L178), 


```solidity
Path: ./src/types/GlobalData.sol

79:        if (isQuoteAsset) {	// @audit-issue
80:            currency = pairStatus.quotePool.token;
81:        } else {
82:            currency = pairStatus.basePool.token;
83:        }

105:        if (isQuoteAsset) {	// @audit-issue
106:            globalData.lockData.quoteReserve = reserveAfter;
107:        } else {
108:            globalData.lockData.baseReserve = reserveAfter;
109:        }
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L79-L83), [105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L105-L109), 


#### Recommendation

Consider using single line if statements as ternary.

### Missing Revert Messages in `require`/`revert` Statements
In Solidity, the `require` function is commonly used to enforce certain conditions or invariants in the code. If the condition inside `require` evaluates to `false`, the function will throw an exception and revert all changes made during the transaction. Along with this condition check, `require` can also accept a second argument — a string that provides a descriptive error message to convey the reason for the transaction's failure.

When `require` is used without this descriptive error message, as in `require(someBoolean)`, it results in less informative feedback to the users or interacting contracts. This lack of clarity can pose challenges in debugging failed transactions or understanding why an operation was not allowed.

On the other hand, a well-structured error message, like `require(someBoolean, "this is an error msg")`, offers insight into the failure's cause, making it much easier for developers, users, and auditors to identify and address issues.


```solidity
Path: ./src/PredyPool.sol

80:        require(allowedUniswapPools[msg.sender]);	// @audit-issue

98:        require(newOperator != address(0));	// @audit-issue

301:        require(globalData.pairs[pairId].allowlistEnabled);	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L80-L80), [98](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L98-L98), [301](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L301-L301), 


```solidity
Path: ./src/PriceFeed.sol

52:        require(quoteAnswer > 0 && basePrice.price > 0);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L52-L52), 


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
Path: ./src/libraries/Reallocation.sol

110:        require(tickSpacing > 0);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L110-L110), 


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

Consider adding an error message to require statements.

### Contract uses both `require()`/`revert()` as well as custom errors
Consider using just one method in a single file

```solidity
Path: ./src/PredyPool.sol

28:contract PredyPool is IPredyPool, IUniswapV3MintCallback, Initializable, ReentrancyGuardUpgradeable {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L28-L28), 


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
Path: ./src/libraries/logic/LiquidationLogic.sol

21:library LiquidationLogic {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L21-L21), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

11:abstract contract BaseMarketUpgradable is IFillerMarket, BaseHookCallbackUpgradable {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L11-L11), 


#### Recommendation

Consistently use either `require()`/`revert()` or custom errors for error handling in your contract to maintain code consistency and readability. Using both methods in the same contract can lead to confusion and make the code harder to understand.

### Custom error has no error details
Consider adding parameters to the error to indicate which user or values caused the failure

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

31:    error PositionNotFound();	// @audit-issue

32:    error PositionHasDifferentPairId();	// @audit-issue

33:    error PositionIsNotClosed();	// @audit-issue

34:    error InvalidOrder();	// @audit-issue

35:    error SignerIsNotPositionOwner();	// @audit-issue

36:    error HedgeTriggerNotMatched();	// @audit-issue

37:    error DeltaIsZero();	// @audit-issue

38:    error AlreadyClosed();	// @audit-issue

39:    error AutoCloseTriggerNotMatched();	// @audit-issue

40:    error MarginUpdateMustBeZero();	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L31-L31), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L32-L32), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L33-L33), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L34-L34), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L35-L35), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L36-L36), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L37-L37), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L38-L38), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L39-L39), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L40-L40), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

12:    error TooShortHedgeInterval();	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L12-L12), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

36:    error TPSLConditionDoesNotMatch();	// @audit-issue

38:    error UpdateMarginMustNotBePositive();	// @audit-issue

40:    error AmountIsZero();	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L36-L36), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L38-L38), [40](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L40-L40), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

11:    error LimitPriceDoesNotMatch();	// @audit-issue

13:    error StopPriceDoesNotMatch();	// @audit-issue

15:    error LimitStopOrderDoesNotMatch();	// @audit-issue

17:    error MarketOrderDoesNotMatch();	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L11-L11), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L15-L15), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L17-L17), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

20:    error NotSafe();	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L20-L20), 


```solidity
Path: ./src/libraries/Perp.sol

28:    error SqrtAssetCanNotCoverBorrow();	// @audit-issue

31:    error NoCFMMLiquidityError();	// @audit-issue

34:    error OutOfRangeError();	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L28-L28), [31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L31-L31), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L34-L34), 


```solidity
Path: ./src/libraries/SlippageLib.sol

15:    error InvalidAveragePrice();	// @audit-issue

17:    error SlippageTooLarge();	// @audit-issue

19:    error OutOfAcceptablePriceRange();	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L15-L15), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L17-L17), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L19-L19), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

6:    error EndTimeBeforeStartTime();	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L6-L6), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

16:    error InvalidMarket();	// @audit-issue

19:    error DeadlinePassed();	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L16-L16), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L19-L19), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

29:    error InvalidUniswapPool();	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L29-L29), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

11:    error CallerIsNotPredyPool();	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L11-L11), 


```solidity
Path: ./src/base/BaseHookCallback.sol

10:    error CallerIsNotPredyPool();	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L10-L10), 


#### Recommendation

When defining custom errors, consider adding parameters or error details that provide information about the specific conditions or inputs that caused the error. Including error details can make debugging and troubleshooting easier by providing context on the cause of the failure.

### Use a `modifier` instead of `require` to check for `msg.sender`
If some functions are only allowed to be called by some specific users, consider using a `modifier` instead of checking with a `require` statement, especially if this check is done in multiple functions.

```solidity
Path: ./src/PredyPool.sol

80:        require(allowedUniswapPools[msg.sender]);	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L80-L80), 


#### Recommendation

To improve code readability and maintainability, consider using a `modifier` to check for `msg.sender` in functions that should only be called by specific users. This not only simplifies the code but also makes it more consistent and easier to audit.

### Consider moving `msg.sender` checks to `modifier`s
If some functions are only allowed to be called by some specific users, consider using a modifier instead of checking with a require statement, especially if this check is done in multiple functions.

```solidity
Path: ./src/PredyPool.sol

80:        require(allowedUniswapPools[msg.sender]);	// @audit-issue

272:        if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {
273:            revert TraderNotAllowed();	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L80-L80), [273](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L272-L273), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

180:            if (msg.sender != whitelistFiller) {
181:                revert CallerIsNotFiller();	// @audit-issue
```
[181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L180-L181), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

202:            if (msg.sender != whitelistFiller) {
203:                revert CallerIsNotFiller();	// @audit-issue
```
[203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L202-L203), 


```solidity
Path: ./src/libraries/VaultLib.sol

19:        if (vault.owner != msg.sender) {
20:            revert IPredyPool.CallerIsNotVaultOwner();	// @audit-issue

51:            if (vault.owner != msg.sender) {
52:                revert IPredyPool.CallerIsNotVaultOwner();	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L19-L20), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L51-L52), 


#### Recommendation

Consider refactoring your code by moving `msg.sender` checks to modifiers when certain functions are only allowed to be called by specific users. This approach can enhance code readability, reduce redundancy, and make it easier to maintain access control logic.

### Constants in comparisons should appear on the left side
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

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

89:            if (tradeResult.minMargin == 0) {	// @audit-issue

90:                if (callbackData.marginAmountUpdate != 0) {	// @audit-issue

146:        if (closeRatio == 1e18) {	// @audit-issue

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
[89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L89-L89), [90](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L90-L90), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L146-L146), [186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L186-L186), [197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L197-L197), [212](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L212-L212), [233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L233-L233), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L256-L256), [281](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L281-L281), [306](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L306-L306), [342](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L342-L342), [378](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L378-L378), [395](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L395-L395), [421](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L421-L421), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

80:        if (userPosition.sqrtPriceTrigger == 0) {	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L80-L80), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

181:        if (tradeAmount == 0) {	// @audit-issue

207:        if (userPosition.vaultId == 0) {	// @audit-issue

257:        if (userPosition.vaultId == 0) {	// @audit-issue

290:        if (tradeAmount == 0) {	// @audit-issue
```
[181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L181-L181), [207](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L207-L207), [257](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L257-L257), [290](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L290-L290), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

50:            if (currentPositionAmount == 0) {	// @audit-issue

92:        if (limitPrice == 0 && stopPrice == 0) {	// @audit-issue

123:        if (tradeAmount == 0) {	// @audit-issue

199:        if (tradeAmount != 0) {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L50-L50), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L92-L92), [123](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L123-L123), [199](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L199-L199), 


```solidity
Path: ./src/libraries/PerpFee.sol

117:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue

142:        if (userStatus.sqrtPerp.amount != 0 && userStatus.lastNumRebalance < assetStatus.numRebalance) {	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L117-L117), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L142-L142), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

103:        if (debtRiskRatio == 0) {	// @audit-issue

176:        return _positionParams.amountSqrt != 0 || _positionParams.amountBase != 0;	// @audit-issue
```
[103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L103-L103), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L176-L176), 


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

593:        if (_isWithdraw && _assetStatus.borrowedAmount == 0) {	// @audit-issue

605:        if (_assetStatus.totalAmount == 0) {	// @audit-issue

626:        if (_userStatus.sqrtPerp.amount == 0) {	// @audit-issue

633:        if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {	// @audit-issue

678:        if (_tradeAmount == 0) {	// @audit-issue

766:        if (openAmount != 0) {	// @audit-issue

784:        if (closeAmount != 0) {	// @audit-issue
```
[223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L223-L223), [349](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L349-L349), [374](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L374-L374), [390](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L390-L390), [432](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L432-L432), [593](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L593-L593), [605](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L605-L605), [626](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L626-L626), [633](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L633-L633), [678](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L678-L678), [766](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L766-L766), [784](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L784-L784), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

38:        if (_amount == 0) {	// @audit-issue

51:        if (_amount == 0) {	// @audit-issue

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue

231:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L38-L38), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L51-L51), [190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L190), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L231-L231), 


```solidity
Path: ./src/libraries/SlippageLib.sol

29:        if (tradeResult.averagePrice == 0) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L29-L29), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

153:        require(_pairs[_pairId].id == 0, "AAA");	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L153-L153), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

51:            if (deltaPositionBase != 0) {	// @audit-issue

64:                if (settledBaseAmount + deltaPositionBase != 0) {	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L51-L51), [64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L64-L64), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

84:            tradeParams.tradeAmountSqrt == 0 ? 0 : _MAX_ACCEPTABLE_SQRT_PRICE_RANGE	// @audit-issue

164:        if (vaultValue <= 0 || minMargin == 0) {	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L84-L84), [164](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L164-L164), 


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

25:        if (x == 0) {	// @audit-issue

35:        if (x == 0) {	// @audit-issue

45:        if (x == 0) {	// @audit-issue
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L25-L25), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L35-L35), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L45-L45), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

122:        if (price == 0) {	// @audit-issue

169:        if (price == 0) {	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L122-L122), [169](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L169-L169), 


#### Recommendation

To prevent typo bugs and improve code readability, it's advisable to place constants on the left side of comparisons. This coding practice helps catch accidental assignment (=) instead of comparison (==) and enhances code quality.

### Convert duplicated codes to modifier/functions
Duplicated code in Solidity contracts, especially when repeated across multiple functions, can lead to inefficiencies and increased maintenance challenges. It not only bloats the contract but also makes it harder to implement changes consistently across the codebase. By identifying common patterns or checks that are repeated and abstracting them into modifiers or separate functions, the code can be made more concise, readable, and maintainable. This practice not only reduces the overall bytecode size, potentially lowering deployment and execution costs, but also enhances the contract's reliability by ensuring consistency in how these common checks or operations are executed.

```solidity
Path: ./src/PredyPool.sol

186:        if (amount > 0) {	// @audit-issue: Same if statement in line(s): ['208']
```
[186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L186-L186), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

198:            if (v < minValue) {	// @audit-issue: Same if statement in line(s): ['205', '218']
```
[198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L198-L198), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

190:        if (tokenState.totalCompoundDeposited == 0 && tokenState.totalNormalDeposited == 0) {	// @audit-issue: Same if statement in line(s): ['231']
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L190-L190), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

59:        assembly {	// @audit-issue: Same inline assembly statement in line(s): ['67']
```
[59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L59-L59), 


```solidity
Path: ./src/libraries/math/LPMath.sol

36:        if (liquidityAmount == 0 || sqrtRatioA == sqrtRatioB) {	// @audit-issue: Same if statement in line(s): ['74']

61:        if (swaped) {	// @audit-issue: Same if statement in line(s): ['98']
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L36-L36), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L61-L61), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### Style guide: Function ordering does not follow the Solidity style guide
According to the Solidity [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

```solidity
Path: ./src/PredyPool.sol

78:    function uniswapV3MintCallback(uint256 amount0, uint256 amount1, bytes calldata) external override {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L78-L78), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(	// @audit-issue
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L79), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

59:    function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L59-L59), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

102:    function predyTradeAfterCallback(	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L102), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

57:    function withdraw(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L57-L57), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

24:    function predyTradeAfterCallback(	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L24-L24), 


```solidity
Path: ./src/base/BaseMarket.sol

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {	// @audit-issue: updateWhitelistFiller should come before than _getSettlementData
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L84), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

49:    function predySettlementCallback(	// @audit-issue
```
[49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L49-L49), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html).

### Style guide: Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/latest/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Errors, 5) Modifiers, and 6) Functions, but the contract(s) below do not follow this ordering


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

24:contract GammaTradeMarket is IFillerMarket, BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using ResolvedOrderLib
	2 -> using ArrayLib
	3 -> using GammaOrderLib
	4 -> using Permit2Lib
	5 -> using SafeTransferLib
	6 -> _permit2
	7 -> positionIDs
	8 -> userPositions
	9 -> GammaPositionTraded
	10 -> GammaPositionModified
	11 -> PositionNotFound
	12 -> PositionHasDifferentPairId
	13 -> PositionIsNotClosed
	14 -> InvalidOrder
	15 -> SignerIsNotPositionOwner
	16 -> HedgeTriggerNotMatched
	17 -> DeltaIsZero
	18 -> AlreadyClosed
	19 -> AutoCloseTriggerNotMatched
	20 -> MarginUpdateMustBeZero
	21 -> constructor
	22 -> initialize
	23 -> predyTradeAfterCallback
	24 -> execLiquidationCall
	25 -> _executeTrade
	26 -> _modifyAutoHedgeAndClose
	27 -> autoHedge
	28 -> autoClose
	29 -> quoteTrade
	30 -> checkAutoHedgeAndClose
	31 -> getUserPositions
	32 -> _getUserPosition
	33 -> _addPositionIndex
	34 -> removePosition
	35 -> _removePosition
	36 -> _getOrInitPosition
	37 -> _saveUserPosition
	38 -> _verifyOrder

```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L24-L24), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

29:contract PerpMarketV1 is BaseMarketUpgradable, ReentrancyGuardUpgradeable {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using ResolvedOrderLib
	2 -> using PerpOrderLib
	3 -> using Permit2Lib
	4 -> using SafeTransferLib
	5 -> using SafeCast
	6 -> _permit2
	7 -> userPositions
	8 -> PerpTraded
	9 -> PerpTraded2
	10 -> TPSLConditionDoesNotMatch
	11 -> UpdateMarginMustNotBePositive
	12 -> AmountIsZero
	13 -> constructor
	14 -> initialize
	15 -> predyTradeAfterCallback
	16 -> executeOrderV3
	17 -> _executeOrderV3
	18 -> _calculateInitialMargin
	19 -> _calculateNetValue
	20 -> _calculatePositionValue
	21 -> getUserPosition
	22 -> _saveUserPosition
	23 -> quoteExecuteOrderV3
	24 -> _verifyOrder
	25 -> _verifyOrderV3

```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L29-L29), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

16:library PositionCalculator {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using ScaledAsset
	2 -> using SafeCast
	3 -> RISK_RATIO_ONE
	4 -> NotSafe
	5 -> isLiquidatable
	6 -> checkSafe
	7 -> getIsSafe
	8 -> calculateMinMargin
	9 -> calculateRequiredCollateralWithDebt
	10 -> calculateMinValue
	11 -> getHasPosition
	12 -> getSqrtIndexPrice
	13 -> getPositionWithFeeAmount
	14 -> getPosition
	15 -> getHasPositionFlag
	16 -> calculateMinValue
	17 -> calculateValue
	18 -> calculateSquartDebtValue

```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L16-L16), 


```solidity
Path: ./src/libraries/Perp.sol

22:library Perp {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> using ScaledAsset
	2 -> using SafeCastLib
	3 -> using Math
	4 -> PremiumGrowthUpdated
	5 -> SqrtPositionUpdated
	6 -> SqrtAssetCanNotCoverBorrow
	7 -> NoCFMMLiquidityError
	8 -> OutOfRangeError
	9 -> createAssetStatus
	10 -> createPerpUserStatus
	11 -> updateRebalanceInterestGrowth
	12 -> reallocate
	13 -> rebalanceForInRange
	14 -> swapForOutOfRange
	15 -> getAvailableLiquidityAmount
	16 -> settleUserBalance
	17 -> updateFeeAndPremiumGrowth
	18 -> saveLastFeeGrowth
	19 -> computeRequiredAmounts
	20 -> updatePosition
	21 -> updateSqrtPosition
	22 -> getAvailableSqrtAmount
	23 -> getUtilizationRatio
	24 -> updateRebalanceEntry
	25 -> calculateEntry
	26 -> increase
	27 -> decrease
	28 -> calculateSqrtPerpOffset
	29 -> updateRebalancePosition
	30 -> finalizeReallocation

```
[22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L22-L22), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

16:library AddPairLogic {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> PairAdded
	2 -> AssetRiskParamsUpdated
	3 -> IRMParamsUpdated
	4 -> FeeRatioUpdated
	5 -> PoolOwnerUpdated
	6 -> PriceOracleUpdated
	7 -> InvalidUniswapPool
	8 -> initializeGlobalData
	9 -> addPair
	10 -> updateFeeRatio
	11 -> updatePoolOwner
	12 -> updatePriceOracle
	13 -> updateAssetRiskParams
	14 -> updateIRMParams
	15 -> _storePairStatus
	16 -> deploySupplyToken
	17 -> validateFeeRatio
	18 -> validatePoolOwner
	19 -> validateRiskParams
	20 -> validateIRMParams

```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L16-L16), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

8:abstract contract BaseHookCallbackUpgradable is Initializable, IHooks {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> _predyPool
	2 -> CallerIsNotPredyPool
	3 -> onlyPredyPool
	4 -> constructor
	5 -> __BaseHookCallback_init
	6 -> predyTradeAfterCallback

```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L8-L8), 


```solidity
Path: ./src/base/BaseHookCallback.sol

7:abstract contract BaseHookCallback is IHooks {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> _predyPool
	2 -> CallerIsNotPredyPool
	3 -> onlyPredyPool
	4 -> constructor
	5 -> predyTradeAfterCallback

```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L7-L7), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
In Solidity contracts, it's common to encounter duplicated `require()` or `revert()` statements across multiple functions. These statements are crucial for validating conditions and ensuring contract integrity. However, repeated checks can lead to code redundancy, making the contract larger and more difficult to maintain. This redundancy can be streamlined by encapsulating the repeated logic in a modifier or a dedicated function. By doing so, the code becomes more concise, easier to audit, and more gas-efficient. Furthermore, centralizing the validation logic in a single location makes the codebase more adaptable to changes and reduces the risk of inconsistencies or errors in future updates.

```solidity
Path: ./src/PredyPool.sol

182:        require(amount > 0, "AZ");	// @audit-issue: Same require statement in line(s): ['204']
```
[182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

233:        if (userPosition.vaultId == 0 || positionId != userPosition.vaultId) {	// @audit-issue: Same if statement in line(s): ['281']
```
[233](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L233-L233), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

181:        if (tradeAmount == 0) {	// @audit-issue: Same if statement in line(s): ['290']
```
[181](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L181-L181), 


```solidity
Path: ./src/libraries/VaultLib.sol

19:        if (vault.owner != msg.sender) {	// @audit-issue: Same if statement in line(s): ['51']
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L19-L19), 


#### Recommendation

Consolidate repeated `require()` or `revert()` checks in Solidity by creating a modifier or a separate function. Apply this modifier to all functions requiring the same validation, or call the function at the beginning of each relevant function. This refactoring enhances contract efficiency, maintainability, and consistency, while potentially reducing gas costs associated with deploying and executing redundant code.

### Duplicate import statements
Contracts often depend on libraries and other contracts to modularize code and reuse functionalities. However, redundant imports occur when a contract imports a library or another contract that is already imported by one of its dependencies. This redundancy does not impact the compiled bytecode but can clutter the codebase, making it harder to understand the direct dependencies of each contract. Simplifying imports by removing these redundancies enhances code readability and maintainability.

```solidity
Path: ./src/PredyPool.sol

4:import {IUniswapV3Pool} from "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue: Same library is also imported on: `['Perp', 'PositionCalculator', 'UniHelper', 'AddPairLogic']`, at lines: `[4, 4, 4, 7]`

7:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue: Same library is also imported on: `['LiquidationLogic', 'ReallocationLogic', 'SupplyLogic', 'GlobalDataLibrary']`, at lines: `[4, 4, 4, 4]`

9:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue: Same library is also imported on: `['LiquidationLogic', 'ReallocationLogic', 'SupplyLogic', 'GlobalDataLibrary']`, at lines: `[5, 5, 5, 6]`
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L4-L4), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L7-L7), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L9-L9), 


```solidity
Path: ./src/PriceFeed.sol

4:import {FixedPointMathLib} from "@solmate/src/utils/FixedPointMathLib.sol";	// @audit-issue: Same library is also imported on: `['PriceFeedFactory']`, at lines: `[4]`

5:import {AggregatorV3Interface} from "./vendors/AggregatorV3Interface.sol";	// @audit-issue: Same library is also imported on: `['PriceFeedFactory']`, at lines: `[5]`

6:import {IPyth} from "./vendors/IPyth.sol";	// @audit-issue: Same library is also imported on: `['PriceFeedFactory']`, at lines: `[6]`

7:import {Constants} from "./libraries/Constants.sol";	// @audit-issue: Same library is also imported on: `['PriceFeedFactory']`, at lines: `[7]`
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L7-L7), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

5:import {OrderInfo} from "../../libraries/orders/OrderInfoLib.sol";	// @audit-issue: Same library is also imported on: `['GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[4, 4]`

6:import {GammaOrder, GammaOrderLib, GammaModifyInfo} from "./GammaOrder.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarket', 'L2GammaDecoder']`, at lines: `[17, 4]`

8:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarket', 'GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[8, 6, 6]`
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L6-L6), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L8-L8), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

8:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: Same library is also imported on: `['GammaModifyInfoLib', 'GammaOrderLib', 'GammaTradeMarketLib']`, at lines: `[6, 6, 7]`

9:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: Same library is also imported on: `['GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[5, 5]`

13:import {ResolvedOrder, ResolvedOrderLib} from "../../libraries/orders/ResolvedOrder.sol";	// @audit-issue: Same library is also imported on: `['GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[7, 7]`

14:import {SlippageLib} from "../../libraries/SlippageLib.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketLib']`, at lines: `[8]`

15:import {Bps} from "../../libraries/math/Bps.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketLib']`, at lines: `[4]`

16:import {DataType} from "../../libraries/DataType.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketLib']`, at lines: `[6]`

17:import {GammaOrder, GammaOrderLib, GammaModifyInfo} from "./GammaOrder.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketLib']`, at lines: `[9]`
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L9-L9), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L14-L14), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L16-L16), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L17-L17), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

5:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketL2', 'GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[8, 6, 6]`

6:import {IFillerMarket} from "../../interfaces/IFillerMarket.sol";	// @audit-issue: Same library is also imported on: `['GammaModifyInfoLib', 'GammaOrderLib']`, at lines: `[5, 5]`

7:import {GammaOrder} from "./GammaOrder.sol";	// @audit-issue: Same library is also imported on: `['GammaTradeMarketL2']`, at lines: `[6]`
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L7-L7), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

9:import "../../interfaces/IPredyPool.sol";	// @audit-issue: Same library is also imported on: `['PerpOrderLib', 'PerpOrderV3Lib', 'PerpMarketLib']`, at lines: `[6, 6, 4]`

13:import "../../libraries/orders/ResolvedOrder.sol";	// @audit-issue: Same library is also imported on: `['PerpOrderLib', 'PerpOrderV3Lib']`, at lines: `[7, 7]`

16:import {Constants} from "../../libraries/Constants.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketLib']`, at lines: `[5]`

18:import {Bps} from "../../libraries/math/Bps.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketLib']`, at lines: `[7]`

19:import {Math} from "../../libraries/math/Math.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketLib']`, at lines: `[8]`
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L9-L9), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L13-L13), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L16-L16), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L19-L19), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

5:import {IPredyPool} from "../../interfaces/IPredyPool.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketV1', 'PerpOrderV3Lib']`, at lines: `[9, 6]`

6:import {PerpOrderV3} from "./PerpOrderV3.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketV1']`, at lines: `[21]`

7:import {OrderInfo} from "../../libraries/orders/OrderInfoLib.sol";	// @audit-issue: Same library is also imported on: `['PerpOrderV3Lib']`, at lines: `[4]`

9:import {Bps} from "../../libraries/math/Bps.sol";	// @audit-issue: Same library is also imported on: `['PerpMarketV1']`, at lines: `[18]`
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L7-L7), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L9-L9), 


#### Recommendation

Review your Solidity contracts and eliminate duplicate import statements. Ensure each file is imported only once where it's needed. If you find the same import statement across multiple files, consider whether you can restructure your code to centralize common logic or dependencies, potentially through base contracts or libraries. Regularly conduct code audits or utilize linters and other static analysis tools to identify and resolve duplicate imports, thereby enhancing the clarity, structure, and security of your Solidity codebase.

### Adding a return statement when the function defines a named return variable, is redundant
Once the return variable has been assigned (or has its default value), there is no need to explicitly return it at the end of the function, since it's returned automatically.

```solidity
Path: ./src/PredyPool.sol

222:    function supply(uint256 pairId, bool isQuoteAsset, uint256 supplyAmount)
223:        external
224:        nonReentrant
225:        returns (uint256 finalSuppliedAmount)
226:    {
227:        return SupplyLogic.supply(globalData, pairId, supplyAmount, isQuoteAsset);	// @audit-issue
228:    }

237:    function withdraw(uint256 pairId, bool isQuoteAsset, uint256 withdrawAmount)
238:        external
239:        nonReentrant
240:        returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)
241:    {
242:        return SupplyLogic.withdraw(globalData, pairId, withdrawAmount, isQuoteAsset);	// @audit-issue
243:    }

251:    function reallocate(uint256 pairId, bytes memory settlementData)
252:        external
253:        nonReentrant
254:        returns (bool relocationOccurred)
255:    {
256:        return ReallocationLogic.reallocate(globalData, pairId, settlementData);	// @audit-issue
257:    }

265:    function trade(TradeParams memory tradeParams, bytes memory settlementData)
266:        external
267:        nonReentrant
268:        returns (TradeResult memory tradeResult)
269:    {
270:        globalData.validate(tradeParams.pairId);
271:
272:        if (globalData.pairs[tradeParams.pairId].allowlistEnabled && !allowedTraders[msg.sender][tradeParams.pairId]) {
273:            revert TraderNotAllowed();
274:        }
275:
276:        tradeParams.vaultId = globalData.createOrGetVault(tradeParams.vaultId, tradeParams.pairId);
277:
278:        return TradeLogic.trade(globalData, tradeParams, settlementData);	// @audit-issue
279:    }

313:    function execLiquidationCall(uint256 vaultId, uint256 closeRatio, bytes memory settlementData)
314:        external
315:        nonReentrant
316:        returns (TradeResult memory tradeResult)
317:    {
318:        return LiquidationLogic.liquidate(vaultId, closeRatio, globalData, settlementData);	// @audit-issue
319:    }
```
[227](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L222-L228), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L237-L243), [256](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L251-L257), [278](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L265-L279), [318](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L313-L319), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

42:    function executeTradeL2(GammaOrderL2 memory order, bytes memory sig, SettlementParamsV3 memory settlementParams)
43:        external
44:        nonReentrant
45:        returns (IPredyPool.TradeResult memory tradeResult)
46:    {
47:        GammaModifyInfo memory modifyInfo = L2GammaDecoder.decodeGammaModifyInfo(
48:            order.modifyParam, order.lowerLimit, order.upperLimit, order.maximaDeviation
49:        );
50:        (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage) =
51:            L2GammaDecoder.decodeGammaParam(order.param);
52:
53:        return _executeTrade(	// @audit-issue
54:            GammaOrder(
55:                OrderInfo(address(this), order.trader, order.nonce, deadline),
56:                pairId,
57:                order.positionId,
58:                _quoteTokenMap[pairId],
59:                order.quantity,
60:                order.quantitySqrt,
61:                order.marginAmount,
62:                order.baseSqrtPrice,
63:                slippageTolerance,
64:                leverage,
65:                modifyInfo
66:            ),
67:            sig,
68:            settlementParams
69:        );
70:    }
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L42-L70), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

333:    function checkAutoHedgeAndClose(uint256 positionId)
334:        external
335:        view
336:        returns (bool hedgeRequired, bool closeRequired, uint256 resultPositionId)
337:    {
338:        GammaTradeMarketLib.UserPosition memory userPosition = userPositions[positionId];
339:
340:        DataType.Vault memory vault = _predyPool.getVault(userPosition.vaultId);
341:
342:        if (vault.openPosition.perp.amount == 0 && vault.openPosition.sqrtPerp.amount == 0) {
343:            return (false, false, positionId);	// @audit-issue
344:        }
345:
346:        uint256 sqrtPrice = _predyPool.getSqrtIndexPrice(userPosition.pairId);
347:
348:        (hedgeRequired,,) = GammaTradeMarketLib.validateHedgeCondition(userPosition, sqrtPrice);
349:
350:        (closeRequired,,) = GammaTradeMarketLib.validateCloseCondition(userPosition, sqrtPrice);
351:
352:        resultPositionId = positionId;
353:    }

375:    function _getUserPosition(uint256 positionId) internal returns (UserPositionResult memory result) {
376:        GammaTradeMarketLib.UserPosition memory userPosition = userPositions[positionId];
377:
378:        if (userPosition.vaultId == 0) {
379:            // if user has no position, return empty vault status and vault
380:            return result;	// @audit-issue
381:        }
382:
383:        return UserPositionResult(
384:            userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId)
385:        );
386:    }

375:    function _getUserPosition(uint256 positionId) internal returns (UserPositionResult memory result) {
376:        GammaTradeMarketLib.UserPosition memory userPosition = userPositions[positionId];
377:
378:        if (userPosition.vaultId == 0) {
379:            // if user has no position, return empty vault status and vault
380:            return result;
381:        }
382:
383:        return UserPositionResult(	// @audit-issue
384:            userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId)
385:        );
386:    }

408:    function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)
409:        internal
410:        returns (GammaTradeMarketLib.UserPosition storage userPosition)
411:    {
412:        userPosition = userPositions[positionId];
413:
414:        if (isCreatedNew) {
415:            userPosition.vaultId = positionId;
416:            userPosition.owner = trader;
417:            userPosition.pairId = pairId;
418:            return userPosition;	// @audit-issue
419:        }
420:
421:        if (positionId == 0 || userPosition.vaultId == 0) {
422:            revert PositionNotFound();
423:        }
424:
425:        if (userPosition.owner != trader) {
426:            revert SignerIsNotPositionOwner();
427:        }
428:
429:        if (userPosition.pairId != pairId) {
430:            revert PositionHasDifferentPairId();
431:        }
432:
433:        return userPosition;
434:    }

408:    function _getOrInitPosition(uint256 positionId, bool isCreatedNew, address trader, uint64 pairId)
409:        internal
410:        returns (GammaTradeMarketLib.UserPosition storage userPosition)
411:    {
412:        userPosition = userPositions[positionId];
413:
414:        if (isCreatedNew) {
415:            userPosition.vaultId = positionId;
416:            userPosition.owner = trader;
417:            userPosition.pairId = pairId;
418:            return userPosition;
419:        }
420:
421:        if (positionId == 0 || userPosition.vaultId == 0) {
422:            revert PositionNotFound();
423:        }
424:
425:        if (userPosition.owner != trader) {
426:            revert SignerIsNotPositionOwner();
427:        }
428:
429:        if (userPosition.pairId != pairId) {
430:            revert PositionHasDifferentPairId();
431:        }
432:
433:        return userPosition;	// @audit-issue
434:    }
```
[343](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L333-L353), [380](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L375-L386), [383](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L375-L386), [418](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L408-L434), [433](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L408-L434), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

12:    function executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)
13:        external
14:        returns (IPredyPool.TradeResult memory tradeResult)
15:    {
16:        return _executeTrade(gammaOrder, sig, settlementParams);	// @audit-issue
17:    }
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L12-L17), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

159:    function _executeOrderV3(
160:        PerpOrderV3 memory perpOrder,
161:        bytes memory sig,
162:        SettlementParamsV3 memory settlementParams,
163:        uint64 orderId
164:    ) internal returns (IPredyPool.TradeResult memory tradeResult) {
165:        ResolvedOrder memory resolvedOrder = PerpOrderV3Lib.resolve(perpOrder, sig);
166:
167:        _validateQuoteTokenAddress(perpOrder.pairId, perpOrder.entryTokenAddress);
168:
169:        UserPosition storage userPosition = userPositions[perpOrder.info.trader][perpOrder.pairId];
170:
171:        userPosition.lastLeverage = perpOrder.leverage;
172:
173:        int256 tradeAmount = PerpMarketLib.getFinalTradeAmount(
174:            _predyPool.getVault(userPosition.vaultId).openPosition.perp.amount,
175:            perpOrder.side,
176:            perpOrder.quantity,
177:            perpOrder.reduceOnly,
178:            perpOrder.closePosition
179:        );
180:
181:        if (tradeAmount == 0) {
182:            revert AmountIsZero();
183:        }
184:
185:        tradeResult = _predyPool.trade(
186:            IPredyPool.TradeParams(
187:                perpOrder.pairId,
188:                userPosition.vaultId,
189:                tradeAmount,
190:                0,
191:                abi.encode(
192:                    CallbackData(
193:                        CallbackSource.TRADE3, perpOrder.info.trader, 0, perpOrder.leverage, resolvedOrder, orderId
194:                    )
195:                )
196:            ),
197:            _getSettlementDataFromV3(settlementParams, msg.sender)
198:        );
199:
200:        if (tradeResult.minMargin > 0) {
201:            // only whitelisted filler can open position
202:            if (msg.sender != whitelistFiller) {
203:                revert CallerIsNotFiller();
204:            }
205:        }
206:
207:        if (userPosition.vaultId == 0) {
208:            userPosition.vaultId = tradeResult.vaultId;
209:
210:            _predyPool.updateRecepient(tradeResult.vaultId, perpOrder.info.trader);
211:        }
212:
213:        PerpMarketLib.validateTrade(
214:            tradeResult, tradeAmount, perpOrder.limitPrice, perpOrder.stopPrice, perpOrder.auctionData
215:        );
216:
217:        return tradeResult;	// @audit-issue
218:    }

247:    function getUserPosition(address owner, uint256 pairId)
248:        external
249:        returns (
250:            UserPosition memory userPosition,
251:            IPredyPool.VaultStatus memory vaultStatus,
252:            DataType.Vault memory vault
253:        )
254:    {
255:        userPosition = userPositions[owner][pairId];
256:
257:        if (userPosition.vaultId == 0) {
258:            // if user has no position, return empty vault status and vault
259:            return (userPosition, vaultStatus, vault);	// @audit-issue
260:        }
261:
262:        return (userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId));
263:    }

247:    function getUserPosition(address owner, uint256 pairId)
248:        external
249:        returns (
250:            UserPosition memory userPosition,
251:            IPredyPool.VaultStatus memory vaultStatus,
252:            DataType.Vault memory vault
253:        )
254:    {
255:        userPosition = userPositions[owner][pairId];
256:
257:        if (userPosition.vaultId == 0) {
258:            // if user has no position, return empty vault status and vault
259:            return (userPosition, vaultStatus, vault);
260:        }
261:
262:        return (userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId));	// @audit-issue
263:    }
```
[217](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L159-L218), [259](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L247-L263), [262](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L247-L263), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

26:    function getFinalTradeAmount(
27:        int256 currentPositionAmount,
28:        string memory side,
29:        uint256 quantity,
30:        bool reduceOnly,
31:        bool closePosition
32:    ) internal pure returns (int256 finalTradeAmount) {
33:        bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));
34:
35:        int256 tradeAmount = isLong ? int256(quantity) : -int256(quantity);
36:
37:        if (closePosition) {
38:            if (isLong && currentPositionAmount >= 0) {
39:                return 0;	// @audit-issue
40:            }
41:
42:            if (!isLong && currentPositionAmount <= 0) {
43:                return 0;
44:            }
45:
46:            return -currentPositionAmount;
47:        }
48:
49:        if (reduceOnly) {
50:            if (currentPositionAmount == 0) {
51:                return 0;
52:            }
53:
54:            if (currentPositionAmount > 0) {
55:                if (tradeAmount < 0) {
56:                    if (currentPositionAmount > -tradeAmount) {
57:                        return tradeAmount;
58:                    } else {
59:                        return -currentPositionAmount;
60:                    }
61:                } else {
62:                    return 0;
63:                }
64:            } else {
65:                if (tradeAmount > 0) {
66:                    if (-currentPositionAmount > tradeAmount) {
67:                        return tradeAmount;
68:                    } else {
69:                        return -currentPositionAmount;
70:                    }
71:                } else {
72:                    return 0;
73:                }
74:            }
75:        }
76:
77:        return tradeAmount;
78:    }

26:    function getFinalTradeAmount(
27:        int256 currentPositionAmount,
28:        string memory side,
29:        uint256 quantity,
30:        bool reduceOnly,
31:        bool closePosition
32:    ) internal pure returns (int256 finalTradeAmount) {
33:        bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));
34:
35:        int256 tradeAmount = isLong ? int256(quantity) : -int256(quantity);
36:
37:        if (closePosition) {
38:            if (isLong && currentPositionAmount >= 0) {
39:                return 0;
40:            }
41:
42:            if (!isLong && currentPositionAmount <= 0) {
43:                return 0;
44:            }
45:
46:            return -currentPositionAmount;
47:        }
48:
49:        if (reduceOnly) {
50:            if (currentPositionAmount == 0) {
51:                return 0;	// @audit-issue
52:            }
53:
54:            if (currentPositionAmount > 0) {
55:                if (tradeAmount < 0) {
56:                    if (currentPositionAmount > -tradeAmount) {
57:                        return tradeAmount;
58:                    } else {
59:                        return -currentPositionAmount;
60:                    }
61:                } else {
62:                    return 0;
63:                }
64:            } else {
65:                if (tradeAmount > 0) {
66:                    if (-currentPositionAmount > tradeAmount) {
67:                        return tradeAmount;
68:                    } else {
69:                        return -currentPositionAmount;
70:                    }
71:                } else {
72:                    return 0;
73:                }
74:            }
75:        }
76:
77:        return tradeAmount;
78:    }

26:    function getFinalTradeAmount(
27:        int256 currentPositionAmount,
28:        string memory side,
29:        uint256 quantity,
30:        bool reduceOnly,
31:        bool closePosition
32:    ) internal pure returns (int256 finalTradeAmount) {
33:        bool isLong = (keccak256(bytes(side))) == keccak256(bytes("Buy"));
34:
35:        int256 tradeAmount = isLong ? int256(quantity) : -int256(quantity);
36:
37:        if (closePosition) {
38:            if (isLong && currentPositionAmount >= 0) {
39:                return 0;
40:            }
41:
42:            if (!isLong && currentPositionAmount <= 0) {
43:                return 0;
44:            }
45:
46:            return -currentPositionAmount;
47:        }
48:
49:        if (reduceOnly) {
50:            if (currentPositionAmount == 0) {
51:                return 0;
52:            }
53:
54:            if (currentPositionAmount > 0) {
55:                if (tradeAmount < 0) {
56:                    if (currentPositionAmount > -tradeAmount) {
57:                        return tradeAmount;
58:                    } else {
59:                        return -currentPositionAmount;
60:                    }
61:                } else {
62:                    return 0;
63:                }
64:            } else {
65:                if (tradeAmount > 0) {
66:                    if (-currentPositionAmount > tradeAmount) {
67:                        return tradeAmount;
68:                    } else {
69:                        return -currentPositionAmount;
70:                    }
71:                } else {
72:                    return 0;
73:                }
74:            }
75:        }
76:
77:        return tradeAmount;	// @audit-issue
78:    }
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L78), [51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L78), [77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L78), 


```solidity
Path: ./src/libraries/PerpFee.sol

65:    function computePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus memory sqrtPerp)
66:        internal
67:        pure
68:        returns (int256 feeUnderlying, int256 feeStable)
69:    {
70:        uint256 growthDiff0;
71:        uint256 growthDiff1;
72:
73:        if (sqrtPerp.amount > 0) {
74:            growthDiff0 = baseAssetStatus.sqrtAssetStatus.fee0Growth - sqrtPerp.entryTradeFee0;
75:            growthDiff1 = baseAssetStatus.sqrtAssetStatus.fee1Growth - sqrtPerp.entryTradeFee1;
76:        } else if (sqrtPerp.amount < 0) {
77:            growthDiff0 = baseAssetStatus.sqrtAssetStatus.borrowPremium0Growth - sqrtPerp.entryTradeFee0;
78:            growthDiff1 = baseAssetStatus.sqrtAssetStatus.borrowPremium1Growth - sqrtPerp.entryTradeFee1;
79:        } else {
80:            return (feeUnderlying, feeStable);	// @audit-issue
81:        }
82:
83:        int256 fee0 = Math.fullMulDivDownInt256(sqrtPerp.amount, growthDiff0, Constants.Q128);
84:        int256 fee1 = Math.fullMulDivDownInt256(sqrtPerp.amount, growthDiff1, Constants.Q128);
85:
86:        if (baseAssetStatus.isQuoteZero) {
87:            feeStable = fee0;
88:            feeUnderlying = fee1;
89:        } else {
90:            feeUnderlying = fee0;
91:            feeStable = fee1;
92:        }
93:    }
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L65-L93), 


```solidity
Path: ./src/libraries/Reallocation.sol

18:    function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)
19:        internal
20:        view
21:        returns (int24 lower, int24 upper)
22:    {
23:        int24 tickSpacing = IUniswapV3Pool(_assetStatusUnderlying.sqrtAssetStatus.uniswapPool).tickSpacing();
24:
25:        ScaledAsset.AssetStatus memory token0Status;
26:        ScaledAsset.AssetStatus memory token1Status;
27:
28:        if (_assetStatusUnderlying.isQuoteZero) {
29:            token0Status = _assetStatusUnderlying.quotePool.tokenStatus;
30:            token1Status = _assetStatusUnderlying.basePool.tokenStatus;
31:        } else {
32:            token0Status = _assetStatusUnderlying.basePool.tokenStatus;
33:            token1Status = _assetStatusUnderlying.quotePool.tokenStatus;
34:        }
35:
36:        return _getNewRange(_assetStatusUnderlying, token0Status, token1Status, currentTick, tickSpacing);	// @audit-issue
37:    }
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L18-L37), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

141:    function getSqrtIndexPrice(DataType.PairStatus memory pairStatus) internal view returns (uint256 sqrtPriceX96) {
142:        if (pairStatus.priceFeed != address(0)) {
143:            return PriceFeed(pairStatus.priceFeed).getSqrtPrice();	// @audit-issue
144:        } else {
145:            return UniHelper.convertSqrtPrice(
146:                UniHelper.getSqrtTWAP(pairStatus.sqrtAssetStatus.uniswapPool), pairStatus.isQuoteZero
147:            );
148:        }
149:    }

151:    function getPositionWithFeeAmount(Perp.UserStatus memory perpUserStatus, DataType.FeeAmount memory feeAmount)
152:        internal
153:        pure
154:        returns (PositionParams memory positionParams)
155:    {
156:        return PositionParams(	// @audit-issue
157:            perpUserStatus.perp.entryValue + perpUserStatus.sqrtPerp.entryValue + feeAmount.feeAmountQuote,
158:            perpUserStatus.sqrtPerp.amount,
159:            perpUserStatus.perp.amount + feeAmount.feeAmountBase
160:        );
161:    }

163:    function getPosition(Perp.UserStatus memory _perpUserStatus)
164:        internal
165:        pure
166:        returns (PositionParams memory positionParams)
167:    {
168:        return PositionParams(	// @audit-issue
169:            _perpUserStatus.perp.entryValue + _perpUserStatus.sqrtPerp.entryValue,
170:            _perpUserStatus.sqrtPerp.amount,
171:            _perpUserStatus.perp.amount
172:        );
173:    }
```
[143](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L141-L149), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L151-L161), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L163-L173), 


```solidity
Path: ./src/libraries/Trade.sol

112:    function getSqrtPrice(address uniswapPoolAddress, bool isQuoteZero) internal view returns (uint256 sqrtPriceX96) {
113:        return UniHelper.convertSqrtPrice(UniHelper.getSqrtPrice(uniswapPoolAddress), isQuoteZero);	// @audit-issue
114:    }
```
[113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L112-L114), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

50:    function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)
51:        internal
52:        returns (uint256 interestRate, uint256 totalProtocolFee)
53:    {
54:        if (block.timestamp <= lastUpdateTimestamp) {
55:            return (0, 0);	// @audit-issue
56:        }
57:
58:        uint256 utilizationRatio = poolStatus.tokenStatus.getUtilizationRatio();
59:
60:        // Skip calculating interest if utilization ratio is 0
61:        if (utilizationRatio == 0) {
62:            return (0, 0);
63:        }
64:
65:        // Calculates interest rate
66:        interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)
67:            * (block.timestamp - lastUpdateTimestamp) / 365 days;
68:
69:        totalProtocolFee = poolStatus.tokenStatus.updateScaler(interestRate, fee);
70:
71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;
72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;
73:    }

50:    function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)
51:        internal
52:        returns (uint256 interestRate, uint256 totalProtocolFee)
53:    {
54:        if (block.timestamp <= lastUpdateTimestamp) {
55:            return (0, 0);
56:        }
57:
58:        uint256 utilizationRatio = poolStatus.tokenStatus.getUtilizationRatio();
59:
60:        // Skip calculating interest if utilization ratio is 0
61:        if (utilizationRatio == 0) {
62:            return (0, 0);	// @audit-issue
63:        }
64:
65:        // Calculates interest rate
66:        interestRate = InterestRateModel.calculateInterestRate(poolStatus.irmParams, utilizationRatio)
67:            * (block.timestamp - lastUpdateTimestamp) / 365 days;
68:
69:        totalProtocolFee = poolStatus.tokenStatus.updateScaler(interestRate, fee);
70:
71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;
72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;
73:    }
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L50-L73), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L50-L73), 


```solidity
Path: ./src/libraries/Perp.sol

426:    function computeRequiredAmounts(
427:        SqrtPerpAssetStatus storage _sqrtAssetStatus,
428:        bool _isQuoteZero,
429:        UserStatus memory _userStatus,
430:        int256 _tradeSqrtAmount
431:    ) internal returns (int256 requiredAmountUnderlying, int256 requiredAmountStable) {
432:        if (_tradeSqrtAmount == 0) {
433:            return (0, 0);	// @audit-issue
434:        }
435:
436:        if (!Reallocation.isInRange(_sqrtAssetStatus)) {
437:            revert OutOfRangeError();
438:        }
439:
440:        int256 requiredAmount0;
441:        int256 requiredAmount1;
442:
443:        if (_tradeSqrtAmount > 0) {
444:            (requiredAmount0, requiredAmount1) = increase(_sqrtAssetStatus, uint256(_tradeSqrtAmount));
445:
446:            if (_sqrtAssetStatus.totalAmount == _sqrtAssetStatus.borrowedAmount) {
447:                // if available liquidity was 0 and added first liquidity then update last fee growth
448:                saveLastFeeGrowth(_sqrtAssetStatus);
449:            }
450:        } else if (_tradeSqrtAmount < 0) {
451:            (requiredAmount0, requiredAmount1) = decrease(_sqrtAssetStatus, uint256(-_tradeSqrtAmount));
452:        }
453:
454:        if (_isQuoteZero) {
455:            requiredAmountStable = requiredAmount0;
456:            requiredAmountUnderlying = requiredAmount1;
457:        } else {
458:            requiredAmountStable = requiredAmount1;
459:            requiredAmountUnderlying = requiredAmount0;
460:        }
461:
462:        (int256 offsetUnderlying, int256 offsetStable) = calculateSqrtPerpOffset(
463:            _userStatus, _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _tradeSqrtAmount, _isQuoteZero
464:        );
465:
466:        requiredAmountUnderlying -= offsetUnderlying;
467:        requiredAmountStable -= offsetStable;
468:    }

618:    function updateRebalanceEntry(
619:        SqrtPerpAssetStatus storage _assetStatus,
620:        UserStatus storage _userStatus,
621:        bool _isQuoteZero
622:    ) internal returns (int256 rebalancePositionUpdateUnderlying, int256 rebalancePositionUpdateStable) {
623:        // Rebalance position should be over repayed or deposited.
624:        // rebalancePositionUpdate values must be rounded down to a smaller value.
625:
626:        if (_userStatus.sqrtPerp.amount == 0) {
627:            _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
628:            _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
629:
630:            return (0, 0);	// @audit-issue
631:        }
632:
633:        if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {
634:            // last user who settles rebalance position
635:            _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
636:            _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
637:
638:            return
639:                (_assetStatus.rebalancePositionBase.positionAmount, _assetStatus.rebalancePositionQuote.positionAmount);
640:        }
641:
642:        int256 deltaPosition0 = LPMath.calculateAmount0ForLiquidityWithTicks(
643:            _assetStatus.tickUpper,
644:            _userStatus.rebalanceLastTickUpper,
645:            _userStatus.sqrtPerp.amount.abs(),
646:            _userStatus.sqrtPerp.amount < 0
647:        );
648:
649:        int256 deltaPosition1 = LPMath.calculateAmount1ForLiquidityWithTicks(
650:            _assetStatus.tickLower,
651:            _userStatus.rebalanceLastTickLower,
652:            _userStatus.sqrtPerp.amount.abs(),
653:            _userStatus.sqrtPerp.amount < 0
654:        );
655:
656:        _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
657:        _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
658:
659:        if (_userStatus.sqrtPerp.amount < 0) {
660:            deltaPosition0 = -deltaPosition0;
661:            deltaPosition1 = -deltaPosition1;
662:        }
663:
664:        if (_isQuoteZero) {
665:            rebalancePositionUpdateUnderlying = deltaPosition1;
666:            rebalancePositionUpdateStable = deltaPosition0;
667:        } else {
668:            rebalancePositionUpdateUnderlying = deltaPosition0;
669:            rebalancePositionUpdateStable = deltaPosition1;
670:        }
671:    }

618:    function updateRebalanceEntry(
619:        SqrtPerpAssetStatus storage _assetStatus,
620:        UserStatus storage _userStatus,
621:        bool _isQuoteZero
622:    ) internal returns (int256 rebalancePositionUpdateUnderlying, int256 rebalancePositionUpdateStable) {
623:        // Rebalance position should be over repayed or deposited.
624:        // rebalancePositionUpdate values must be rounded down to a smaller value.
625:
626:        if (_userStatus.sqrtPerp.amount == 0) {
627:            _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
628:            _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
629:
630:            return (0, 0);
631:        }
632:
633:        if (_assetStatus.lastRebalanceTotalSquartAmount == 0) {
634:            // last user who settles rebalance position
635:            _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
636:            _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
637:
638:            return	// @audit-issue
639:                (_assetStatus.rebalancePositionBase.positionAmount, _assetStatus.rebalancePositionQuote.positionAmount);
640:        }
641:
642:        int256 deltaPosition0 = LPMath.calculateAmount0ForLiquidityWithTicks(
643:            _assetStatus.tickUpper,
644:            _userStatus.rebalanceLastTickUpper,
645:            _userStatus.sqrtPerp.amount.abs(),
646:            _userStatus.sqrtPerp.amount < 0
647:        );
648:
649:        int256 deltaPosition1 = LPMath.calculateAmount1ForLiquidityWithTicks(
650:            _assetStatus.tickLower,
651:            _userStatus.rebalanceLastTickLower,
652:            _userStatus.sqrtPerp.amount.abs(),
653:            _userStatus.sqrtPerp.amount < 0
654:        );
655:
656:        _userStatus.rebalanceLastTickLower = _assetStatus.tickLower;
657:        _userStatus.rebalanceLastTickUpper = _assetStatus.tickUpper;
658:
659:        if (_userStatus.sqrtPerp.amount < 0) {
660:            deltaPosition0 = -deltaPosition0;
661:            deltaPosition1 = -deltaPosition1;
662:        }
663:
664:        if (_isQuoteZero) {
665:            rebalancePositionUpdateUnderlying = deltaPosition1;
666:            rebalancePositionUpdateStable = deltaPosition0;
667:        } else {
668:            rebalancePositionUpdateUnderlying = deltaPosition0;
669:            rebalancePositionUpdateStable = deltaPosition1;
670:        }
671:    }

673:    function calculateEntry(int256 _positionAmount, int256 _entryValue, int256 _tradeAmount, int256 _valueUpdate)
674:        internal
675:        pure
676:        returns (int256 deltaEntry, int256 payoff)
677:    {
678:        if (_tradeAmount == 0) {
679:            return (0, 0);	// @audit-issue
680:        }
681:
682:        if (_positionAmount * _tradeAmount >= 0) {
683:            // open position
684:            deltaEntry = _valueUpdate;
685:        } else {
686:            if (_positionAmount.abs() >= _tradeAmount.abs()) {
687:                // close position
688:
689:                int256 closeStableAmount = _entryValue * _tradeAmount / _positionAmount;
690:
691:                deltaEntry = closeStableAmount;
692:                payoff = _valueUpdate - closeStableAmount;
693:            } else {
694:                // close full and open position
695:
696:                int256 closeStableAmount = -_entryValue;
697:                int256 openStableAmount = _valueUpdate * (_positionAmount + _tradeAmount) / _tradeAmount;
698:
699:                deltaEntry = closeStableAmount + openStableAmount;
700:                payoff = _valueUpdate - closeStableAmount - openStableAmount;
701:            }
702:        }
703:    }
```
[433](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L426-L468), [630](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L618-L671), [638](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L618-L671), [679](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L673-L703), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

37:    function addAsset(AssetStatus storage tokenState, uint256 _amount) internal returns (uint256 claimAmount) {
38:        if (_amount == 0) {
39:            return 0;	// @audit-issue
40:        }
41:
42:        claimAmount = FixedPointMathLib.mulDivDown(_amount, Constants.ONE, tokenState.assetScaler);
43:
44:        tokenState.totalCompoundDeposited += claimAmount;
45:    }

47:    function removeAsset(AssetStatus storage tokenState, uint256 _supplyTokenAmount, uint256 _amount)
48:        internal
49:        returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)
50:    {
51:        if (_amount == 0) {
52:            return (0, 0);	// @audit-issue
53:        }
54:
55:        require(_supplyTokenAmount > 0, "S3");
56:
57:        uint256 burnAmount = FixedPointMathLib.mulDivDown(_amount, Constants.ONE, tokenState.assetScaler);
58:
59:        if (_supplyTokenAmount < burnAmount) {
60:            finalBurnAmount = _supplyTokenAmount;
61:        } else {
62:            finalBurnAmount = burnAmount;
63:        }
64:
65:        finalWithdrawAmount = FixedPointMathLib.mulDivDown(finalBurnAmount, tokenState.assetScaler, Constants.ONE);
66:
67:        require(getAvailableCollateralValue(tokenState) >= finalWithdrawAmount, "S0");
68:
69:        tokenState.totalCompoundDeposited -= finalBurnAmount;
70:    }
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L37-L45), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L47-L70), 


```solidity
Path: ./src/base/BaseMarket.sol

40:    function reallocate(uint256 pairId, IFillerMarket.SettlementParams memory settlementParams)
41:        external
42:        returns (bool relocationOccurred)
43:    {
44:        return _predyPool.reallocate(pairId, _getSettlementData(settlementParams));	// @audit-issue
45:    }
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L40-L45), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

89:    function reallocate(uint256 pairId, IFillerMarket.SettlementParamsV3 memory settlementParams)
90:        external
91:        returns (bool relocationOccurred)
92:    {
93:        return _predyPool.reallocate(pairId, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue
94:    }
```
[93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L89-L94), 


#### Recommendation

When a function defines a named return variable and assigns a value to it, there is no need to add an explicit return statement at the end of the function. The named return variable will be automatically returned with its assigned value. Removing the redundant return statement can lead to cleaner and more concise code, improving readability and reducing the risk of introducing unnecessary errors.

### Consider using `delete` rather than assigning values to `false`
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.


```solidity
Path: ./src/markets/L2Decoder.sol

29:            isLimit = false;	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L29-L29), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

81:            isEnabled = false;	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L81-L81), 


```solidity
Path: ./src/libraries/Perp.sol

242:            isOutOfRange = false;	// @audit-issue
```
[242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L242-L242), 


#### Recommendation

Prefer using the `delete` keyword to reset state variables to their initial values instead of assigning `false` or other values, as it makes the intent clearer and helps with auditing.

### Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

```solidity
Path: ./src/PredyPool.sol

184:        pool.accumulatedProtocolRevenue = 0;	// @audit-issue

206:        pool.accumulatedCreatorRevenue = 0;	// @audit-issue
```
[184](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L184-L184), [206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L206-L206), 


```solidity
Path: ./src/libraries/UniHelper.sol

39:        secondsAgos[1] = 0;	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L39-L39), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

95:                    vault.margin = 0;	// @audit-issue

102:                vault.margin = 0;	// @audit-issue
```
[95](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L95-L95), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L102-L102), 


#### Recommendation

When you need to clear or reset values in storage variables, consider using the `delete` keyword instead of manually assigning zero or other values. Using `delete` provides a more explicit and efficient way to clear storage variables. For example, instead of `myVariable = 0;`, you can use `delete myVariable;` to clear the value.

### Defined named returns not used within function
Such instances can be replaced with unnamed returns

```solidity
Path: ./src/PredyPool.sol

225:        returns (uint256 finalSuppliedAmount)	// @audit-issue: Return param `finalSuppliedAmount` is defined but not used in function.

240:        returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)	// @audit-issue: Return param `finalBurnAmount` is defined but not used in function.

240:        returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)	// @audit-issue: Return param `finalWithdrawAmount` is defined but not used in function.

254:        returns (bool relocationOccurred)	// @audit-issue: Return param `relocationOccurred` is defined but not used in function.

268:        returns (TradeResult memory tradeResult)	// @audit-issue: Return param `tradeResult` is defined but not used in function.

316:        returns (TradeResult memory tradeResult)	// @audit-issue: Return param `tradeResult` is defined but not used in function.
```
[225](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L225-L225), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L240-L240), [240](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L240-L240), [254](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L254-L254), [268](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L268-L268), [316](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L316-L316), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

45:        returns (IPredyPool.TradeResult memory tradeResult)	// @audit-issue: Return param `tradeResult` is defined but not used in function.
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L45-L45), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketWrapper.sol

14:        returns (IPredyPool.TradeResult memory tradeResult)	// @audit-issue: Return param `tradeResult` is defined but not used in function.
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketWrapper.sol#L14-L14), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

32:    ) internal pure returns (int256 finalTradeAmount) {	// @audit-issue: Return param `finalTradeAmount` is defined but not used in function.
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L32-L32), 


```solidity
Path: ./src/libraries/Reallocation.sol

21:        returns (int24 lower, int24 upper)	// @audit-issue: Return param `lower` is defined but not used in function.

21:        returns (int24 lower, int24 upper)	// @audit-issue: Return param `upper` is defined but not used in function.
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L21-L21), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L21-L21), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

141:    function getSqrtIndexPrice(DataType.PairStatus memory pairStatus) internal view returns (uint256 sqrtPriceX96) {	// @audit-issue: Return param `sqrtPriceX96` is defined but not used in function.

154:        returns (PositionParams memory positionParams)	// @audit-issue: Return param `positionParams` is defined but not used in function.

166:        returns (PositionParams memory positionParams)	// @audit-issue: Return param `positionParams` is defined but not used in function.
```
[141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L141-L141), [154](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L154-L154), [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L166-L166), 


```solidity
Path: ./src/libraries/Trade.sol

112:    function getSqrtPrice(address uniswapPoolAddress, bool isQuoteZero) internal view returns (uint256 sqrtPriceX96) {	// @audit-issue: Return param `sqrtPriceX96` is defined but not used in function.
```
[112](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L112-L112), 


```solidity
Path: ./src/base/BaseMarket.sol

42:        returns (bool relocationOccurred)	// @audit-issue: Return param `relocationOccurred` is defined but not used in function.
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L42-L42), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

91:        returns (bool relocationOccurred)	// @audit-issue: Return param `relocationOccurred` is defined but not used in function.
```
[91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L91-L91), 


#### Recommendation

Inspect your Solidity functions for unused named return variables. If a named return is not actively used in the function, switch to using unnamed return types. This change simplifies the function signature and avoids potential confusion over the function's return behavior. Ensure that the function's documentation and comments clearly describe the return type and purpose, maintaining clarity even with unnamed return variables. This approach contributes to cleaner, more concise function declarations in your Solidity codebase.

### Too long functions should be refactored
Functions with too many lines are difficult to understand. It is recommended to refactor complex functions into multiple shorter and easier to understand functions.


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(	// @audit-issue 57 lines

152:    function _executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)	// @audit-issue 57 lines
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L79), [152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L152-L152), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

159:    function _executeOrderV3(	// @audit-issue 59 lines
```
[159](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L159-L159), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

26:    function getFinalTradeAmount(	// @audit-issue 52 lines
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L26), 


```solidity
Path: ./src/libraries/Perp.sol

202:    function reallocate(	// @audit-issue 58 lines

470:    function updatePosition(	// @audit-issue 54 lines

526:    function updateSqrtPosition(	// @audit-issue 53 lines

618:    function updateRebalanceEntry(	// @audit-issue 53 lines
```
[202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L202-L202), [470](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L470-L470), [526](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L526-L526), [618](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L618-L618), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

76:    function updatePosition(	// @audit-issue 52 lines
```
[76](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L76-L76), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

27:    function reallocate(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId, bytes memory settlementData)	// @audit-issue 66 lines
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L27-L27), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

39:    function liquidate(	// @audit-issue 80 lines
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L39-L39), 


#### Recommendation

To address this issue, refactor long and complex functions into multiple shorter and more manageable functions. This will improve code readability and maintainability, making it easier to understand and maintain your smart contract.

### Expressions for `constant` values should use `immutable` rather than `constant`
While it does not save gas for some simple binary expressions because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constant`s should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.

```solidity
Path: ./src/markets/gamma/GammaOrder.sol

24:    bytes internal constant GAMMA_MODIFY_INFO_TYPE = abi.encodePacked(	// @audit-issue
25:        "GammaModifyInfo(",
26:        "bool isEnabled,",
27:        "uint64 expiration,",
28:        "int64 maximaDeviation,",
29:        "uint256 lowerLimit,",
30:        "uint256 upperLimit,",
31:        "uint32 hedgeInterval,",
32:        "uint32 sqrtPriceTrigger,",
33:        "uint32 minSlippageTolerance,",
34:        "uint32 maxSlippageTolerance,",
35:        "uint16 auctionPeriod,",
36:        "uint32 auctionRange)"
37:    );

39:    bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);	// @audit-issue

81:    bytes internal constant GAMMA_ORDER_TYPE = abi.encodePacked(	// @audit-issue
82:        "GammaOrder(",
83:        "OrderInfo info,",
84:        "uint64 pairId,",
85:        "uint256 positionId,",
86:        "address entryTokenAddress,",
87:        "int256 quantity,",
88:        "int256 quantitySqrt,",
89:        "int256 marginAmount,",
90:        "uint256 baseSqrtPrice,",
91:        "uint32 slippageTolerance,",
92:        "uint8 leverage,",
93:        "GammaModifyInfo modifyInfo)"
94:    );

97:    bytes internal constant ORDER_TYPE =	// @audit-issue
98:        abi.encodePacked(GAMMA_ORDER_TYPE, GammaModifyInfoLib.GAMMA_MODIFY_INFO_TYPE, OrderInfoLib.ORDER_INFO_TYPE);

99:    bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue

102:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
103:        abi.encodePacked(
104:            "GammaOrder witness)",
105:            GammaModifyInfoLib.GAMMA_MODIFY_INFO_TYPE,
106:            GAMMA_ORDER_TYPE,
107:            OrderInfoLib.ORDER_INFO_TYPE,
108:            TOKEN_PERMISSIONS_TYPE
109:        )
110:    );
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L24-L37), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L39-L39), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L81-L94), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L97-L98), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L99-L99), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L102-L110), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

27:    bytes internal constant PERP_ORDER_TYPE = abi.encodePacked(	// @audit-issue
28:        "PerpOrder(",
29:        "OrderInfo info,",
30:        "uint64 pairId,",
31:        "address entryTokenAddress,",
32:        "int256 tradeAmount,",
33:        "int256 marginAmount,",
34:        "uint256 takeProfitPrice,",
35:        "uint256 stopLossPrice,",
36:        "uint64 slippageTolerance,",
37:        "uint8 leverage,",
38:        "address validatorAddress,",
39:        "bytes validationData)"
40:    );

43:    bytes internal constant ORDER_TYPE = abi.encodePacked(PERP_ORDER_TYPE, OrderInfoLib.ORDER_INFO_TYPE);	// @audit-issue

44:    bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue

47:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
48:        abi.encodePacked("PerpOrder witness)", OrderInfoLib.ORDER_INFO_TYPE, PERP_ORDER_TYPE, TOKEN_PERMISSIONS_TYPE)
49:    );
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L27-L40), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L43-L43), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L44-L44), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L47-L49), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

28:    bytes internal constant PERP_ORDER_V3_TYPE = abi.encodePacked(	// @audit-issue
29:        "PerpOrderV3(",
30:        "OrderInfo info,",
31:        "uint64 pairId,",
32:        "address entryTokenAddress,",
33:        "string side,",
34:        "uint256 quantity,",
35:        "uint256 marginAmount,",
36:        "uint256 limitPrice,",
37:        "uint256 stopPrice,",
38:        "uint8 leverage,",
39:        "bool reduceOnly,",
40:        "bool closePosition,",
41:        "bytes auctionData)"
42:    );

45:    bytes internal constant ORDER_V3_TYPE = abi.encodePacked(PERP_ORDER_V3_TYPE, OrderInfoLib.ORDER_INFO_TYPE);	// @audit-issue

46:    bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);	// @audit-issue

49:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
50:        abi.encodePacked(
51:            "PerpOrderV3 witness)", OrderInfoLib.ORDER_INFO_TYPE, PERP_ORDER_V3_TYPE, TOKEN_PERMISSIONS_TYPE
52:        )
53:    );
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L28-L42), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L45-L45), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L46-L46), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L49-L53), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

26:        returns (ISignatureTransfer.SignatureTransferDetails memory)	// @audit-issue
27:    {
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L26-L27), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

14:    bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L14-L14), 


#### Recommendation

To improve code readability and adhere to best practices, consider using `immutable` variables instead of `constant` for expressions or values calculated in, or passed into the constructor. While the compiler may handle this issue for simple binary expressions, it's essential to use the right tool for the task at hand. `constant` should be reserved for literal values written directly into the code, while `immutable` is better suited for dynamic values and expressions.

### Event is not properly `indexed`
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be `indexed`.

```solidity
Path: ./src/PredyPool.sol

42:    event OperatorUpdated(address operator);	// @audit-issue

43:    event RecepientUpdated(uint256 vaultId, address recipient);	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L42-L42), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L43-L43), 


```solidity
Path: ./src/PriceFeed.sol

12:    event PriceFeedCreated(address quotePrice, bytes32 priceId, uint256 decimalsDiff, address priceFeed);	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L12-L12), 


```solidity
Path: ./src/libraries/Trade.sol

30:    event Swapped(uint256 pairId, uint256 vaultId, address owner, int256 settledQuoteAmount, int256 settledBaseAmount);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L30-L30), 


```solidity
Path: ./src/libraries/VaultLib.sol

9:    event VaultCreated(uint256 vaultId, address owner, address quoteToken, uint256 pairId);	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L9-L9), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

31:    event PairAdded(uint256 pairId, address quoteToken, address uniswapPool);	// @audit-issue

37:    event PoolOwnerUpdated(uint256 pairId, address poolOwner);	// @audit-issue

38:    event PriceOracleUpdated(uint256 pairId, address priceOracle);	// @audit-issue
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L31-L31), [37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L37-L37), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L38-L38), 


#### Recommendation

Enhance smart contract efficiency post-deployment by utilizing indexed events. This approach aids in efficiently tracking contract activities, significantly contributing to the reduction of gas costs.

### Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. The solidity style guide recommends a maximumum line length of [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length), so the lines below should be split when they reach that length.        self.impact_details = 


```solidity
Path: ./src/PredyPool.sol

295:     * @notice Sets the authorized traders. When allowlistEnabled is true, only authorized traders are allowed to trade.	// @audit-issue: Length of the line is: 121
```
[295](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L295-L295), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

75:            L2GammaDecoder.decodeGammaModifyInfo(order.param, order.lowerLimit, order.upperLimit, order.maximaDeviation);	// @audit-issue: Length of the line is: 122
```
[75](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L75-L75), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

65:    event GammaPositionModified(address indexed trader, uint256 pairId, uint256 positionId, GammaModifyInfo modifyInfo);	// @audit-issue: Length of the line is: 121

144:            _predyPool.execLiquidationCall(vaultId, closeRatio, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue: Length of the line is: 121

436:    function _saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)	// @audit-issue: Length of the line is: 121
```
[65](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L65-L65), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L144-L144), [436](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L436-L436), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

262:        return (userPosition, _quoter.quoteVaultStatus(userPosition.vaultId), _predyPool.getVault(userPosition.vaultId));	// @audit-issue: Length of the line is: 122
```
[262](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L262-L262), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

79:        return ResolvedOrder(perpOrder.info, perpOrder.entryTokenAddress, perpOrder.marginAmount, hash(perpOrder), sig);	// @audit-issue: Length of the line is: 121
```
[79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L79-L79), 


```solidity
Path: ./src/libraries/UniHelper.sol

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue: Length of the line is: 121
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

235:            + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;	// @audit-issue: Length of the line is: 126
```
[235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L235-L235), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

23:     * @notice Each time a user interacts with the contract, interest and premium from the previous interaction to the current one are applied.	// @audit-issue: Length of the line is: 144

46:            emitInterestGrowthEvent(pairStatus, interestRateQuote, interestRateBase, protocolFeeQuote, protocolFeeBase);	// @audit-issue: Length of the line is: 121

50:    function applyInterestForPoolStatus(Perp.AssetPoolStatus storage poolStatus, uint256 lastUpdateTimestamp, uint8 fee)	// @audit-issue: Length of the line is: 121
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L23-L23), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L46-L46), [50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L50-L50), 


```solidity
Path: ./src/libraries/Perp.sol

639:                (_assetStatus.rebalancePositionBase.positionAmount, _assetStatus.rebalancePositionQuote.positionAmount);	// @audit-issue: Length of the line is: 121

786:            offsetUnderlying += closeAmount * _userStatus.sqrtPerp.baseRebalanceEntryValue / _userStatus.sqrtPerp.amount;	// @audit-issue: Length of the line is: 122

816:        // LastRebalanceTotalSquartAmount is the total amount of positions that will have to pay rebalancing interest in the future	// @audit-issue: Length of the line is: 132
```
[639](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L639-L639), [786](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L786-L786), [816](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L816-L816), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

6: * @notice This library is used to define the interest rate curve, which determines the interest rate based on utilization.	// @audit-issue: Length of the line is: 124
```
[6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L6-L6), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

13:    bytes internal constant ORDER_INFO_TYPE = "OrderInfo(address market,address trader,uint256 nonce,uint256 deadline)";	// @audit-issue: Length of the line is: 121
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L13-L13), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

39:            IPredyPool.VaultStatus(vaultId, vaultValue, minMargin, oraclePice, feeAmount, getPosition(vault, feeAmount))	// @audit-issue: Length of the line is: 121
```
[39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L39-L39), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

57:    function withdraw(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)	// @audit-issue: Length of the line is: 121
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L57-L57), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

104:                // To prevent the liquidator from unfairly profiting through arbitrage trades in the AMM and passing losses onto the protocol,	// @audit-issue: Length of the line is: 143

106:                ERC20(pairStatus.quotePool.token).safeTransferFrom(msg.sender, address(this), uint256(-remainingMargin));	// @audit-issue: Length of the line is: 122
```
[104](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L104-L104), [106](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L106-L106), 


```solidity
Path: ./src/libraries/math/LPMath.sol

113:        return SafeCast.toInt256(calculateAmount0Offset(TickMath.getSqrtRatioAtTick(upper), liquidityAmount, isRoundUp));	// @audit-issue: Length of the line is: 122

139:        return SafeCast.toInt256(calculateAmount1Offset(TickMath.getSqrtRatioAtTick(lower), liquidityAmount, isRoundUp));	// @audit-issue: Length of the line is: 122
```
[113](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L113-L113), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L139-L139), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

55:        SettlementCallbackLib.SettlementParams memory settlementParams = decodeParamsV3(settlementData, baseAmountDelta);	// @audit-issue: Length of the line is: 122

66:        SettlementParamsV3Internal memory settlementParamsV3 = abi.decode(settlementData, (SettlementParamsV3Internal));	// @audit-issue: Length of the line is: 121

102:            _predyPool.execLiquidationCall(vaultId, closeRatio, _getSettlementDataFromV3(settlementParams, msg.sender));	// @audit-issue: Length of the line is: 121
```
[55](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L55-L55), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L66-L66), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L102-L102), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

34:            settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]	// @audit-issue: Length of the line is: 121
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L34-L34), 


#### Recommendation

To adhere to coding standards and enhance code readability, consider splitting long lines of code when they approach the recommended maximum line length. In Solidity, a common guideline is to limit lines to a maximum of 120 characters. Splitting long lines can improve code maintainability and make it easier to understand.

### Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist.

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

69:    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)	// @audit-issue: Replace `whitelist` with `allowlist`

74:        __BaseMarket_init(predyPool, whitelistFiller, quoterAddress);	// @audit-issue: Replace `whitelist` with `allowlist`

179:            // only whitelisted filler can open position	// @audit-issue: Replace `whitelist` with `allowlist`

180:            if (msg.sender != whitelistFiller) {	// @audit-issue: Replace `whitelist` with `allowlist`
```
[69](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L69-L69), [74](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L74-L74), [179](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L179-L179), [180](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L180-L180), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

92:    function initialize(IPredyPool predyPool, address permit2Address, address whitelistFiller, address quoterAddress)	// @audit-issue: Replace `whitelist` with `allowlist`

97:        __BaseMarket_init(predyPool, whitelistFiller, quoterAddress);	// @audit-issue: Replace `whitelist` with `allowlist`

201:            // only whitelisted filler can open position	// @audit-issue: Replace `whitelist` with `allowlist`

202:            if (msg.sender != whitelistFiller) {	// @audit-issue: Replace `whitelist` with `allowlist`
```
[92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L97-L97), [201](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L201-L201), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L202-L202), 


```solidity
Path: ./src/vendors/IPyth.sol

19:    /// @dev This function is a sanity-checked version of `getPriceUnsafe` which is useful in	// @audit-issue: Replace `sanity` with `quick`
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L19-L19), 


```solidity
Path: ./src/base/BaseMarket.sol

11:    address public whitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

17:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue: Replace `whitelist` with `allowlist`

19:    constructor(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)	// @audit-issue: Replace `whitelist` with `allowlist`

23:        whitelistFiller = _whitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

36:        SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);	// @audit-issue: Replace `whitelist` with `allowlist`

81:     * @notice Updates the whitelist filler address	// @audit-issue: Replace `whitelist` with `allowlist`

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {	// @audit-issue: Replace `whitelist` with `allowlist`

85:        whitelistFiller = newWhitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

89:     * @notice Updates the whitelist settlement address	// @audit-issue: Replace `whitelist` with `allowlist`

92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {	// @audit-issue: Replace `whitelist` with `allowlist`

93:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue: Replace `whitelist` with `allowlist`
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L11-L11), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L17-L17), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L19-L19), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L23-L23), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L36-L36), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L81-L81), [84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L84), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L85-L85), [89](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L89-L89), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L92-L92), [93](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L93-L93), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

23:    address public whitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

29:    mapping(address settlementContractAddress => bool) internal _whiteListedSettlements;	// @audit-issue: Replace `whitelist` with `allowlist`

32:        if (msg.sender != whitelistFiller) revert CallerIsNotFiller();	// @audit-issue: Replace `whitelist` with `allowlist`

38:    function __BaseMarket_init(IPredyPool predyPool, address _whitelistFiller, address quoterAddress)	// @audit-issue: Replace `whitelist` with `allowlist`

44:        whitelistFiller = _whitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

57:        SettlementCallbackLib.validate(_whiteListedSettlements, settlementParams);	// @audit-issue: Replace `whitelist` with `allowlist`

125:     * @notice Updates the whitelist filler address	// @audit-issue: Replace `whitelist` with `allowlist`

128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {	// @audit-issue: Replace `whitelist` with `allowlist`

129:        whitelistFiller = newWhitelistFiller;	// @audit-issue: Replace `whitelist` with `allowlist`

137:     * @notice Updates the whitelist settlement address	// @audit-issue: Replace `whitelist` with `allowlist`

140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {	// @audit-issue: Replace `whitelist` with `allowlist`

141:        _whiteListedSettlements[settlementContractAddress] = isEnabled;	// @audit-issue: Replace `whitelist` with `allowlist`
```
[23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L23-L23), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L29-L29), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L32-L32), [38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L38-L38), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L44-L44), [57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L57-L57), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L125-L125), [128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L128-L128), [129](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L129-L129), [137](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L137-L137), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L140-L140), [141](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L141-L141), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

30:        mapping(address settlementContractAddress => bool) storage _whiteListedSettlements,	// @audit-issue: Replace `whitelist` with `allowlist`

34:            settlementParams.contractAddress != address(0) && !_whiteListedSettlements[settlementParams.contractAddress]	// @audit-issue: Replace `whitelist` with `allowlist`

36:            revert IFillerMarket.SettlementContractIsNotWhitelisted();	// @audit-issue: Replace `whitelist` with `allowlist`
```
[30](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L30-L30), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L34-L34), [36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L36-L36), 


#### Recommendation

To promote inclusive language and avoid insensitive terminology, consider using alternative terms such as 'allowlist' instead of 'whitelist' and 'denylist' instead of 'blacklist.' These alternatives help create a more welcoming and inclusive environment in code and documentation.

### Dependence on external protocols
External protocols should be monitored as such dependencies may introduce vulnerabilities if a vulnerability is found /introduced in the external protocol

```solidity
Path: ./src/PredyPool.sol

4:import {IUniswapV3Pool} from "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import {IUniswapV3MintCallback} from "@uniswap/v3-core/contracts/interfaces/callback/IUniswapV3MintCallback.sol";	// @audit-issue

6:import {Initializable} from "@openzeppelin-upgradeable/contracts/proxy/utils/Initializable.sol";	// @audit-issue

7:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

8:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue

9:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L9-L9), 


```solidity
Path: ./src/PriceFeed.sol

4:import {FixedPointMathLib} from "@solmate/src/utils/FixedPointMathLib.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L4-L4), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue

6:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue

7:import {IPermit2} from "@uniswap/permit2/src/interfaces/IPermit2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L7-L7), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue

6:import {IPermit2} from "@uniswap/permit2/src/interfaces/IPermit2.sol";	// @audit-issue

7:import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

8:import {ReentrancyGuardUpgradeable} from "@openzeppelin-upgradeable/contracts/security/ReentrancyGuardUpgradeable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L8-L8), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue

6:import {ISwapRouter} from "@uniswap/v3-periphery/contracts/interfaces/ISwapRouter.sol";	// @audit-issue

7:import {IQuoterV2} from "@uniswap/v3-periphery/contracts/interfaces/IQuoterV2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L7-L7), 


```solidity
Path: ./src/libraries/PerpFee.sol

4:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L4-L4), 


```solidity
Path: ./src/libraries/UniHelper.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-periphery/contracts/libraries/PositionKey.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L6-L6), 


```solidity
Path: ./src/libraries/Reallocation.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L7-L7), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L6-L6), 


```solidity
Path: ./src/libraries/Trade.sol

4:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

5:import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L5-L5), 


```solidity
Path: ./src/libraries/Perp.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-periphery/contracts/libraries/PositionKey.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

8:import "@solmate/src/utils/SafeCastLib.sol";	// @audit-issue

9:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L9-L9), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

4:import {FixedPointMathLib} from "@solmate/src/utils/FixedPointMathLib.sol";	// @audit-issue

5:import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L5-L5), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

4:import {ISignatureTransfer} from "@uniswap/permit2/src/interfaces/ISignatureTransfer.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L4-L4), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

4:import {IERC20Metadata} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";	// @audit-issue

5:import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";	// @audit-issue

6:import {IUniswapV3Factory} from "@uniswap/v3-core/contracts/interfaces/IUniswapV3Factory.sol";	// @audit-issue

7:import {IUniswapV3Pool} from "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L7-L7), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L5-L5), 


```solidity
Path: ./src/libraries/logic/ReallocationLogic.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReallocationLogic.sol#L5-L5), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L5-L5), 


```solidity
Path: ./src/libraries/math/LPMath.sol

4:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L7-L7), 


```solidity
Path: ./src/libraries/math/Math.sol

4:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue

5:import {FixedPointMathLib} from "@solmate/src/utils/FixedPointMathLib.sol";	// @audit-issue

6:import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L6-L6), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

4:import {Initializable} from "@openzeppelin-upgradeable/contracts/proxy/utils/Initializable.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L4-L4), 


```solidity
Path: ./src/base/BaseMarket.sol

4:import {Owned} from "@solmate/src/auth/Owned.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L4-L4), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L5-L5), 


```solidity
Path: ./src/types/GlobalData.sol

4:import {SafeTransferLib} from "@solmate/src/utils/SafeTransferLib.sol";	// @audit-issue

5:import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

6:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L6-L6), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

4:import {ERC20} from "@solmate/src/tokens/ERC20.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L4-L4), 


#### Recommendation

Regularly monitor and review the external protocols your Solidity contracts depend on for any updates or identified vulnerabilities. Consider implementing fallback mechanisms or contingency plans in your contracts to handle potential failures or compromises in these external protocols. Additionally, thoroughly audit and test the integration points with external protocols to ensure they adhere to your contract's security standards. Where possible, design your contract architecture to minimize reliance on external protocols or allow for upgradability in response to changes in these dependencies. Stay informed about developments in the protocols you depend on and actively participate in their community for early awareness of potential issues.

### Incorrect withdraw declaration
In Solidity, it's essential for clarity and interoperability to correctly specify return types in function declarations. If the `withdraw` function is expected to return a `bool` to indicate success or failure, its omission could lead to ambiguity or unexpected behavior when interacting with or calling this function from other contracts or off-chain systems. Missing return values can mislead developers and potentially lead to contract integrations built on incorrect assumptions. To resolve this, the function declaration for `withdraw` should be modified to explicitly include the `bool` return type, ensuring clarity and correctness in contract interactions.

```solidity
Path: ./src/PredyPool.sol

237:    function withdraw(uint256 pairId, bool isQuoteAsset, uint256 withdrawAmount)	// @audit-issue
238:        external
239:        nonReentrant
240:        returns (uint256 finalBurnAmount, uint256 finalWithdrawAmount)
```
[237](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L237-L240), 


```solidity
Path: ./src/libraries/logic/SupplyLogic.sol

57:    function withdraw(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)	// @audit-issue
58:        external
59:        returns (uint256 finalburntAmount, uint256 finalWithdrawalAmount)
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/SupplyLogic.sol#L57-L59), 


#### Recommendation

Review the function declarations in your Solidity contracts, particularly for critical operations like `withdraw`, to ensure that they correctly specify return types. If a function is intended to indicate success or failure, it should explicitly declare a `bool` return type in its signature. For example, modify the `withdraw` function declaration from:
```solidity
function withdraw(uint256 amount) public {
    // Implementation
}

// to

function withdraw(uint256 amount) public returns (bool) {
    // Implementation
    return true; // Indicate success
}
```


### Subtraction may underflow if result of multiplication is too large
The following expressions may underflow, as the subtrahend could be greater than the minuend in case the former is multiplied by a large number.

```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

176:        uint256 ratio = (price2 * Bps.ONE / price1 - Bps.ONE);	// @audit-issue
```
[176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L176-L176), 


```solidity
Path: ./src/libraries/Reallocation.sol

80:                    lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L80-L80), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

32:                decayedPrice = startPrice - (startPrice - endPrice) * elapsed / duration;	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L32-L32), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

174:        return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);	// @audit-issue
```
[174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L174-L174), 


#### Recommendation

Implement checks to guard against underflow in subtraction operations that follow multiplications in your Solidity contracts. Use SafeMath library or Solidity 0.8.x or higher, which has built-in overflow/underflow checks, to safely perform these arithmetic operations. Before performing the subtraction, ensure that the minuend is greater than or equal to the subtrahend. If using a version of Solidity that does not include automatic checks, consider adding explicit require statements to validate this condition, or use a library like OpenZeppelin's SafeMath to provide these safeguards. Careful handling of arithmetic operations, especially in critical functions involving financial calculations, is crucial for the security and correctness of your contract.

### User Defined Value Types Bug
The current Solidity version has the [User Defined Value Types Bug]() issue.

```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2021/09/29/user-defined-value-types-bug/).

### Bug in Deduplication of Verbatim Blocks
The current Solidity version has the [Bug in Deduplication of Verbatim Blocks](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/) issue.

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

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/).

### Storage Write Removal Bug On Conditional Early Termination
The current Solidity version has the [Storage Write Removal Bug On Conditional Early Termination](https://soliditylang.org/blog/2022/09/08/storage-write-removal-before-conditional-termination/) issue.

```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2022/09/08/storage-write-removal-before-conditional-termination/).

### Optimizer Bug Regarding Memory Side Effects of Inline Assembly
The current Solidity version has the [Optimizer Bug Regarding Memory Side Effects of Inline Assembly](https://soliditylang.org/blog/2022/06/15/inline-assembly-memory-side-effects-bug/) issue.

```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2022/06/15/inline-assembly-memory-side-effects-bug/).

### `abi.encodeCall` Literals Bug
The current Solidity version has the [abi.encodeCall Literals Bug](https://soliditylang.org/blog/2022/03/16/encodecall-bug/) issue.

```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2022/03/16/encodecall-bug/).

### Hardcoded string that is repeatedly used can be replaced with a constant
For better maintainability, please consider creating and using a constant for those strings instead of hardcoding them.

```solidity
Path: ./src/PredyPool.sol

182:        require(amount > 0, "AZ");	// @audit-issue: This string `AZ` is used also on line(s): `[204]`
```
[182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L182-L182), 


```solidity
Path: ./src/libraries/Perp.sol

280:            address(this), _sqrtAssetStatus.tickLower, _sqrtAssetStatus.tickUpper, _totalLiquidityAmount, ""	// @audit-issue: This string `` is used also on line(s): `[712]`
```
[280](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L280-L280), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

67:        require(getAvailableCollateralValue(tokenState) >= finalWithdrawAmount, "S0");	// @audit-issue: This string `S0` is used also on line(s): `[108, 118]`

85:            require(userStatus.lastFeeGrowth == tokenStatus.assetGrowth, "S2");	// @audit-issue: This string `S2` is used also on line(s): `[87]`

160:        require(accountState.positionAmount >= 0, "S1");	// @audit-issue: This string `S1` is used also on line(s): `[175]`
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L67-L67), [85](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L85-L85), [160](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L160-L160), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue: This string `C0` is used also on line(s): `[216]`
```
[214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), 


#### Recommendation

Identify hardcoded strings that are used repeatedly in your Solidity contract and replace them with constants. Define these constants at the beginning of your contract or in a separate dedicated contract or library if they are shared across multiple contracts. This practice centralizes the management of these values, making your code more maintainable and reducing the risk of inconsistencies or errors when changes are required. Refactoring to use constants not only simplifies updates but also enhances the readability and clarity of your code.

### Style Guide: Surround top level declarations in Solidity source with two blank lines.
1- Surround top level declarations in Solidity source with two blank lines.
2- Within a contract surround function declarations with a single blank line.


```solidity
Path: ./src/PredyPool.sol

75:    }
76:
77:    /// @dev Callback for Uniswap V3 pool.	// @audit-issue: There should be single blank line between function declarations.
78:    function uniswapV3MintCallback(uint256 amount0, uint256 amount1, bytes calldata) external override {

90:    }
91:
92:    /**
93:     * @notice Sets new operator
94:     * @dev Only operator can call this function.
95:     * @param newOperator The address of new operator
96:     */	// @audit-issue: There should be single blank line between function declarations.
97:    function setOperator(address newOperator) external onlyOperator {

102:    }
103:
104:    /**
105:     * @notice Adds a new trading pair.
106:     * @param addPairParam AddPairParams struct containing pair information.
107:     * @return pairId The id of the pair.
108:     */	// @audit-issue: There should be single blank line between function declarations.
109:    function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {

111:    }
112:
113:    /**
114:     * @notice Updates asset risk parameters.
115:     * @dev The function can be called by pool owner.
116:     * @param pairId The id of asset to update params.
117:     * @param riskParams Asset risk parameters.
118:     */	// @audit-issue: There should be single blank line between function declarations.
119:    function updateAssetRiskParams(uint256 pairId, Perp.AssetRiskParams memory riskParams)

124:    }
125:
126:    /**
127:     * @notice Updates interest rate model parameters.
128:     * @dev The function can be called by pool owner.
129:     * @param pairId The id of pair to update params.
130:     * @param quoteIrmParams Asset interest-rate parameters for quote token.
131:     * @param baseIrmParams Asset interest-rate parameters for base token.
132:     */	// @audit-issue: There should be single blank line between function declarations.
133:    function updateIRMParams(

139:    }
140:
141:    /**
142:     * @notice Updates fee ratio
143:     * @dev The function can be called by pool owner.
144:     * @param pairId The id of pair to update params.
145:     * @param feeRatio The ratio of fee
146:     */	// @audit-issue: There should be single blank line between function declarations.
147:    function updateFeeRatio(uint256 pairId, uint8 feeRatio) external onlyPoolOwner(pairId) {

149:    }
150:
151:    /**
152:     * @notice Updates pool owner
153:     * @dev The function can be called by pool owner.
154:     * @param pairId The id of pair to update owner.
155:     * @param poolOwner The address of pool owner
156:     */	// @audit-issue: There should be single blank line between function declarations.
157:    function updatePoolOwner(uint256 pairId, address poolOwner) external onlyPoolOwner(pairId) {

159:    }
160:
161:    /**
162:     * @notice Updates price oracle
163:     * @dev The function can be called by pool owner.
164:     * @param pairId The id of pair to update oracle.
165:     * @param priceFeed The address of price feed
166:     */	// @audit-issue: There should be single blank line between function declarations.
167:    function updatePriceOracle(uint256 pairId, address priceFeed) external onlyPoolOwner(pairId) {

169:    }
170:
171:    /**
172:     * @notice Withdraws accumulated protocol revenue.
173:     * @dev Only operator can call this function.
174:     * @param pairId The id of pair
175:     * @param isQuoteToken Is quote or base
176:     */	// @audit-issue: There should be single blank line between function declarations.
177:    function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {

191:    }
192:
193:    /**
194:     * @notice Withdraws accumulated creator revenue.
195:     * @dev Only pool owner can call this function.
196:     * @param pairId The id of pair
197:     * @param isQuoteToken Is quote or base
198:     */	// @audit-issue: There should be single blank line between function declarations.
199:    function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {

213:    }
214:
215:    /**
216:     * @notice Supplies either the quoteToken or the baseToken to the lending pool.
217:     * In return, the lender receives bond tokens.
218:     * @param pairId The id of pair to supply liquidity
219:     * @param isQuoteAsset Whether the token is quote or base
220:     * @param supplyAmount The amount of tokens to supply
221:     */	// @audit-issue: There should be single blank line between function declarations.
222:    function supply(uint256 pairId, bool isQuoteAsset, uint256 supplyAmount)

228:    }
229:
230:    /**
231:     * @notice Withdraws either the quoteToken or the baseToken from the lending pool.
232:     * In return, the lender burns the bond tokens.
233:     * @param pairId The id of pair to withdraw liquidity
234:     * @param isQuoteAsset Whether the token is quote or base
235:     * @param withdrawAmount The amount of tokens to withdraw
236:     */	// @audit-issue: There should be single blank line between function declarations.
237:    function withdraw(uint256 pairId, bool isQuoteAsset, uint256 withdrawAmount)

243:    }
244:
245:    /**
246:     * @notice Reallocates the range of concentrated liquidity provider position to be in-range.
247:     * @param pairId The id of pair to reallocate the range.
248:     * @param settlementData byte data for settlement contract.
249:     * @return relocationOccurred Whether relocation occurred.
250:     */	// @audit-issue: There should be single blank line between function declarations.
251:    function reallocate(uint256 pairId, bytes memory settlementData)

257:    }
258:
259:    /**
260:     * @notice Trades perps and squarts. If vaultId is 0, it creates a new vault.
261:     * @param tradeParams trade details
262:     * @param settlementData byte data for settlement contract.
263:     * @return tradeResult The result of the trade.
264:     */	// @audit-issue: There should be single blank line between function declarations.
265:    function trade(TradeParams memory tradeParams, bytes memory settlementData)

279:    }
280:
281:    /**
282:     * @notice Updates the recipient. If the position is liquidated, the remaining margin is sent to the recipient.
283:     * @param vaultId The id of the vault.
284:     * @param recipient if recipient is zero address, protocol never transfers margin.
285:     */	// @audit-issue: There should be single blank line between function declarations.
286:    function updateRecepient(uint256 vaultId, address recipient) external onlyVaultOwner(vaultId) {

292:    }
293:
294:    /**
295:     * @notice Sets the authorized traders. When allowlistEnabled is true, only authorized traders are allowed to trade.
296:     * @param pairId The id of pair
297:     * @param trader The address of allowed trader
298:     * @param enabled Whether the trader is allowed to trade
299:     */	// @audit-issue: There should be single blank line between function declarations.
300:    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {

304:    }
305:
306:    /**
307:     * @notice Executes a liquidation call to close an unsafe vault.
308:     * @param vaultId The identifier of the vault to be liquidated.
309:     * @param closeRatio The ratio at which the position will be closed.
310:     * @param settlementData SettlementData struct for trade settlement.
311:     * @return tradeResult TradeResult struct with the result of the liquidation.
312:     */	// @audit-issue: There should be single blank line between function declarations.
313:    function execLiquidationCall(uint256 vaultId, uint256 closeRatio, bytes memory settlementData)

319:    }
320:
321:    /**
322:     * @notice Transfers tokens. It can only be called from within the `predySettlementCallback` and
323:     * `predyTradeAfterCallback` of the contract that invoked the trade function.
324:     * @dev Only the current locker can call this function
325:     * @param isQuoteAsset Whether the token is quote or base
326:     * @param to The address to transfer the tokens to
327:     * @param amount The amount of tokens to transfer
328:     */	// @audit-issue: There should be single blank line between function declarations.
329:    function take(bool isQuoteAsset, address to, uint256 amount) external onlyByLocker {

331:    }
332:
333:    /**
334:     * @notice Creates a new vault
335:     * @param pairId The id of pair to create vault
336:     */	// @audit-issue: There should be single blank line between function declarations.
337:    function createVault(uint256 pairId) external returns (uint256) {

341:    }
342:
343:    /// @notice Gets the square root of the AMM price	// @audit-issue: There should be single blank line between function declarations.
344:    function getSqrtPrice(uint256 pairId) external view returns (uint160) {

349:    }
350:
351:    /// @notice Gets the square root of the index price	// @audit-issue: There should be single blank line between function declarations.
352:    function getSqrtIndexPrice(uint256 pairId) external view returns (uint256) {

354:    }
355:
356:    /// @notice Gets the status of pair	// @audit-issue: There should be single blank line between function declarations.
357:    function getPairStatus(uint256 pairId) external view returns (DataType.PairStatus memory) {

361:    }
362:
363:    /// @notice Gets the vault	// @audit-issue: There should be single blank line between function declarations.
364:    function getVault(uint256 vaultId) external view returns (DataType.Vault memory) {

366:    }
367:
368:    /// @notice Gets the status of pair
369:    /// @dev This function always reverts	// @audit-issue: There should be single blank line between function declarations.
370:    function revertPairStatus(uint256 pairId) external {

372:    }
373:
374:    /// @notice Gets the status of the vault
375:    /// @dev This function always reverts	// @audit-issue: There should be single blank line between function declarations.
376:    function revertVaultStatus(uint256 vaultId) external {
```
[77](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L75-L78), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L90-L97), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L102-L109), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L111-L119), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L124-L133), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L139-L147), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L149-L157), [166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L159-L167), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L169-L177), [198](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L191-L199), [221](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L213-L222), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L228-L237), [250](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L243-L251), [264](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L257-L265), [285](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L279-L286), [299](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L292-L300), [312](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L304-L313), [328](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L319-L329), [336](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L331-L337), [343](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L341-L344), [351](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L349-L352), [356](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L354-L357), [363](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L361-L364), [369](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L366-L370), [375](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L372-L376), 


```solidity
Path: ./src/PriceFeed.sol

42:    }
43:
44:    /// @notice This function returns the square root of the baseToken price quoted in quoteToken.	// @audit-issue: There should be single blank line between function declarations.
45:    function getSqrtPrice() external view returns (uint256 sqrtPrice) {
```
[44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L42-L45), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketL2.sol

70:    }
71:
72:    // modify position (hedge or close)	// @audit-issue: There should be single blank line between function declarations.
73:    function modifyAutoHedgeAndClose(GammaModifyOrderL2 memory order, bytes memory sig) external {
```
[72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketL2.sol#L70-L73), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

149:    }
150:
151:    // open position	// @audit-issue: There should be single blank line between function declarations.
152:    function _executeTrade(GammaOrder memory gammaOrder, bytes memory sig, SettlementParamsV3 memory settlementParams)
```
[151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L149-L152), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

39:    bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);
40:
41:    /// @notice hash an GammaModifyInfo object
42:    /// @param info The GammaModifyInfo object to hash	// @audit-issue: There should be single blank line between function declarations.
43:    function hash(GammaModifyInfo memory info) internal pure returns (bytes32) {

110:    );
111:
112:    /// @notice hash the given order
113:    /// @param order the order to hash
114:    /// @return the eip-712 order hash	// @audit-issue: There should be single blank line between function declarations.
115:    function hash(GammaOrder memory order) internal pure returns (bytes32) {
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L39-L43), [114](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L110-L115), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

160:    }
161:
162:    /**
163:     * @notice Calculate slippage tolerance by price
164:     * trader want to trade in price1 >= price2,
165:     * slippage tolerance will be increased if price1 <= price2
166:     */	// @audit-issue: There should be single blank line between function declarations.
167:    function calculateSlippageToleranceByPrice(uint256 price1, uint256 price2, AuctionParams memory auctionParams)
```
[166](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L160-L167), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

49:    );
50:
51:    /// @notice hash the given order
52:    /// @param order the order to hash
53:    /// @return the eip-712 order hash	// @audit-issue: There should be single blank line between function declarations.
54:    function hash(PerpOrder memory order) internal pure returns (bytes32) {
```
[53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L49-L54), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

142:    }
143:
144:    /**
145:     * @notice Verifies signature of the order_v3 and executes trade
146:     * @param order The order signed by trader
147:     * @param settlementParams The route of settlement created by filler
148:     */	// @audit-issue: There should be single blank line between function declarations.
149:    function executeOrderV3(SignedOrder memory order, SettlementParamsV3 memory settlementParams)

272:    }
273:
274:    /// @notice Estimate transaction results and return with revert message	// @audit-issue: There should be single blank line between function declarations.
275:    function quoteExecuteOrderV3(
```
[148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L142-L149), [274](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L272-L275), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

53:    );
54:
55:    /// @notice hash the given order
56:    /// @param order the order to hash
57:    /// @return the eip-712 order hash	// @audit-issue: There should be single blank line between function declarations.
58:    function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {
```
[57](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L53-L58), 


```solidity
Path: ./src/libraries/PerpFee.sol

61:    }
62:
63:    // Trade fee and premium
64:	// @audit-issue: There should be single blank line between function declarations.
65:    function computePremium(DataType.PairStatus memory baseAssetStatus, Perp.SqrtPositionStatus memory sqrtPerp)

108:    }
109:
110:    /// @notice Computes the unrealized interest of rebalance position	// @audit-issue: There should be single blank line between function declarations.
111:    function computeRebalanceInterest(

133:    }
134:
135:    /// @notice Settles the unrealized interest of rebalance position	// @audit-issue: There should be single blank line between function declarations.
136:    function settleRebalanceInterest(
```
[64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L61-L65), [110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L108-L111), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L133-L136), 


```solidity
Path: ./src/libraries/UniHelper.sol

15:    }
16:
17:    /**
18:     * Gets square root of time weighted average price.
19:     */	// @audit-issue: There should be single blank line between function declarations.
20:    function getSqrtTWAP(address uniswapPoolAddress) internal view returns (uint160 sqrtTwapX96) {

22:    }
23:
24:    /**
25:     * sqrt price in stable token
26:     */	// @audit-issue: There should be single blank line between function declarations.
27:    function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {
```
[19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L15-L20), [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L22-L27), 


```solidity
Path: ./src/libraries/Reallocation.sol

13:    using SafeCast for uint256;
14:
15:    /**
16:     * @notice Gets new available range
17:     */	// @audit-issue: There should be single blank line between function declarations.
18:    function getNewRange(DataType.PairStatus memory _assetStatusUnderlying, int24 currentTick)

87:    }
88:
89:    /**
90:     * @notice Returns the flag that a tick is within a range or not
91:     */	// @audit-issue: There should be single blank line between function declarations.
92:    function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {

104:    }
105:
106:    /**
107:     * @notice Normalizes a tick by tick spacing
108:     */	// @audit-issue: There should be single blank line between function declarations.
109:    function calculateUsableTick(int24 _tick, int24 tickSpacing) internal pure returns (int24 result) {

121:    }
122:
123:    /**
124:     * @notice The minimum tick that can be moved from the currentLowerTick, calculated from token1 amount
125:     */	// @audit-issue: There should be single blank line between function declarations.
126:    function calculateMinLowerTick(

142:    }
143:
144:    /**
145:     * @notice The maximum tick that can be moved from the currentUpperTick, calculated from token0 amount
146:     */	// @audit-issue: There should be single blank line between function declarations.
147:    function calculateMaxUpperTick(
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L13-L18), [91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L87-L92), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L104-L109), [125](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L121-L126), [146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L142-L147), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

31:    }
32:
33:    /// @notice Returns whether a position is liquidatable.	// @audit-issue: There should be single blank line between function declarations.
34:    function isLiquidatable(

46:    }
47:
48:    /// @notice Checks if a position is safe. It reverts if the position is not safe.	// @audit-issue: There should be single blank line between function declarations.
49:    function checkSafe(

108:    }
109:
110:    /**
111:     * @notice Calculates min value of the vault.
112:     * @param marginAmount The target vault for calculation
113:     * @param positionParams The position parameters
114:     * @param sqrtPrice The square root of time-weighted average price
115:     * @param riskRatio risk ratio of price
116:     */	// @audit-issue: There should be single blank line between function declarations.
117:    function calculateMinValue(

177:    }
178:
179:    /**
180:     * @notice Calculates min position value in the range `p/r` to `rp`.
181:     * MinValue := Min(v(rp), v(p/r), v((b/a)^2))
182:     * where `a` is base asset amount, `b` is Sqrt perp amount
183:     * and `c` is quote asset amount.
184:     * r is risk parameter.
185:     */	// @audit-issue: There should be single blank line between function declarations.
186:    function calculateMinValue(uint256 _sqrtPrice, PositionParams memory _positionParams, uint256 _riskRatio)

223:    }
224:
225:    /**
226:     * @notice Calculates position value.
227:     * PositionValue = a * x+2 * b * sqrt(x) + c.
228:     * where `a` is base asset amount, `b` is liquidity amount of Uni LP Position
229:     * and `c` is quote asset amount
230:     */	// @audit-issue: There should be single blank line between function declarations.
231:    function calculateValue(uint256 _sqrtPrice, PositionParams memory _positionParams) internal pure returns (int256) {
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L31-L34), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L46-L49), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L108-L117), [185](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L177-L186), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L223-L231), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

20:    );
21:
22:    /**
23:     * @notice Each time a user interacts with the contract, interest and premium from the previous interaction to the current one are applied.
24:     * This increases the amount available for withdrawal by the lender and the premium income for Squart.
25:     */	// @audit-issue: There should be single blank line between function declarations.
26:    function applyInterestForToken(mapping(uint256 => DataType.PairStatus) storage pairs, uint256 pairId) internal {
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L20-L26), 


```solidity
Path: ./src/libraries/Perp.sol

156:    }
157:
158:    /// @notice Settle the interest on rebalance positions up to this block and update the rebalance fee growth value	// @audit-issue: There should be single blank line between function declarations.
159:    function updateRebalanceInterestGrowth(

174:    }
175:
176:    /**
177:     * @notice Reallocates LP position to be in range.
178:     * In case of in-range
179:     *   token0
180:     *     1/sqrt(x) - 1/sqrt(b1) -> 1/sqrt(x) - 1/sqrt(b2)
181:     *       1/sqrt(b2) - 1/sqrt(b1)
182:     *   token1
183:     *     sqrt(x) - sqrt(a1) -> sqrt(x) - sqrt(a2)
184:     *       sqrt(a2) - sqrt(a1)
185:     *
186:     * In case of out-of-range (tick high b1 < x)
187:     *   token0
188:     *     0 -> 1/sqrt(x) - 1/sqrt(b2)
189:     *       1/sqrt(b2) - 1/sqrt(x)
190:     *   token1
191:     *     sqrt(b1) - sqrt(a1) -> sqrt(x) - sqrt(a2)
192:     *       sqrt(b1) - sqrt(a1) - (sqrt(x) - sqrt(a2))
193:     *
194:     * In case of out-of-range (tick low x < a1)
195:     *   token0
196:     *     1/sqrt(a1) - 1/sqrt(b1) -> 1/sqrt(x) - 1/sqrt(b2)
197:     *       1/sqrt(a1) - 1/sqrt(b1) - (1/sqrt(x) - 1/sqrt(b2))
198:     *   token1
199:     *     0 -> sqrt(x) - sqrt(a2)
200:     *       sqrt(a2) - sqrt(x)
201:     */	// @audit-issue: There should be single blank line between function declarations.
202:    function reallocate(

289:    }
290:
291:    /**
292:     * @notice Swaps additional token amounts for rebalance.
293:     * In case of out-of-range (tick high b1 < x)
294:     *   token0
295:     *       1/sqrt(x)　- 1/sqrt(b1)
296:     *   token1
297:     *       sqrt(x) - sqrt(b1)
298:     *
299:     * In case of out-of-range (tick low x < a1)
300:     *   token0
301:     *       1/sqrt(x) - 1/sqrt(a1)
302:     *   token1
303:     *       sqrt(x) - sqrt(a1)
304:     */	// @audit-issue: There should be single blank line between function declarations.
305:    function swapForOutOfRange(

420:    }
421:
422:    /**
423:     * @notice Computes reuired amounts to increase or decrease sqrt positions.
424:     * (L/sqrt{x}, L * sqrt{x})
425:     */	// @audit-issue: There should be single blank line between function declarations.
426:    function computeRequiredAmounts(

579:    }
580:
581:    /**
582:     * @notice Gets available sqrt amount
583:     * max available amount is 98% of total amount
584:     */	// @audit-issue: There should be single blank line between function declarations.
585:    function getAvailableSqrtAmount(SqrtPerpAssetStatus memory _assetStatus, bool _isWithdraw)

703:    }
704:
705:    // private functions
706:	// @audit-issue: There should be single blank line between function declarations.
707:    function increase(SqrtPerpAssetStatus memory _assetStatus, uint256 _liquidityAmount)

738:    }
739:
740:    /**
741:     * @notice Calculates sqrt perp offset
742:     * open: (L/sqrt{b}, L * sqrt{a})
743:     * close: (-L * e0, -L * e1)
744:     */	// @audit-issue: There should be single blank line between function declarations.
745:    function calculateSqrtPerpOffset(

812:    }
813:
814:    /// @notice called after reallocation	// @audit-issue: There should be single blank line between function declarations.
815:    function finalizeReallocation(SqrtPerpAssetStatus storage sqrtPerpStatus) internal {
```
[158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L156-L159), [201](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L174-L202), [304](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L289-L305), [425](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L420-L426), [584](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L579-L585), [706](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L703-L707), [744](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L738-L745), [814](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L812-L815), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

183:    }
184:
185:    // update scaler	// @audit-issue: There should be single blank line between function declarations.
186:    function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)
```
[185](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L183-L186), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

20:    }
21:
22:    /// @notice returns a ResolvedOrder into a permit object	// @audit-issue: There should be single blank line between function declarations.
23:    function transferDetails(ResolvedOrder memory order, address to)
```
[22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L20-L23), 


```solidity
Path: ./src/libraries/orders/ResolvedOrder.sol

19:    error DeadlinePassed();
20:
21:    /// @notice Validates a resolved order, reverting if invalid
22:    /// @param resolvedOrder resovled order	// @audit-issue: There should be single blank line between function declarations.
23:    function validate(ResolvedOrder memory resolvedOrder) internal view {
```
[22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/ResolvedOrder.sol#L19-L23), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

14:    bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);
15:
16:    /// @notice hash an OrderInfo object
17:    /// @param info The OrderInfo object to hash	// @audit-issue: There should be single blank line between function declarations.
18:    function hash(OrderInfo memory info) internal pure returns (bytes32) {
```
[17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L14-L18), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

38:    event PriceOracleUpdated(uint256 pairId, address priceOracle);
39:
40:    /**
41:     * @notice Initialized global data counts
42:     * @param global Global data
43:     */	// @audit-issue: There should be single blank line between function declarations.
44:    function initializeGlobalData(GlobalDataLibrary.GlobalData storage global, address uniswapFactory) external {

48:    }
49:
50:    /**
51:     * @notice Adds token pair
52:     */	// @audit-issue: There should be single blank line between function declarations.
53:    function addPair(
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L38-L44), [52](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L48-L53), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

119:    }
120:
121:    /**
122:     * @notice Check vault safety and get slippage tolerance
123:     * @param pairStatus The pair status
124:     * @param vault The vault object
125:     * @param rebalanceFeeGrowthCache rebalance fee growth
126:     * @return sqrtOraclePrice The square root of oracle price used for value calculation
127:     * @return slippageTolerance slippage tolerance calculated by minMargin and vault value
128:     */	// @audit-issue: There should be single blank line between function declarations.
129:    function checkVaultIsDanger(

149:    }
150:
151:    /**
152:     * @notice Calculates slippage tolerance based on minMargin and vaultValue.
153:     * the smaller the vault value, the larger the slippage tolerance becomes like Dutch auction.
154:     * @param minMargin minMargin value
155:     * @param vaultValue vault value
156:     * @param riskParams risk parameters
157:     * @return slippageTolerance slippage tolerance calculated by minMargin and vault value
158:     */	// @audit-issue: There should be single blank line between function declarations.
159:    function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L119-L129), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L149-L159), 


```solidity
Path: ./src/libraries/math/LPMath.sol

103:    }
104:
105:    /**
106:     * @notice Calculates L / (1.0001)^(b/2)
107:     */	// @audit-issue: There should be single blank line between function declarations.
108:    function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)

114:    }
115:
116:    /**
117:     * @notice Calculates L / sqrt{p_b}
118:     */	// @audit-issue: There should be single blank line between function declarations.
119:    function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)

129:    }
130:
131:    /**
132:     * @notice Calculates L * (1.0001)^(a/2)
133:     */	// @audit-issue: There should be single blank line between function declarations.
134:    function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)

140:    }
141:
142:    /**
143:     * @notice Calculates L * sqrt{p_a}
144:     */	// @audit-issue: There should be single blank line between function declarations.
145:    function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
```
[107](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L103-L108), [118](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L114-L119), [133](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L129-L134), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L140-L145), 


```solidity
Path: ./src/base/BaseMarket.sol

78:    }
79:
80:    /**
81:     * @notice Updates the whitelist filler address
82:     * @dev only owner can call this function
83:     */	// @audit-issue: There should be single blank line between function declarations.
84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {

86:    }
87:
88:    /**
89:     * @notice Updates the whitelist settlement address
90:     * @dev only owner can call this function
91:     */	// @audit-issue: There should be single blank line between function declarations.
92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {

94:    }
95:
96:    /// @notice Registers quote token address for the pair	// @audit-issue: There should be single blank line between function declarations.
97:    function updateQuoteTokenMap(uint256 pairId) external {

101:    }
102:
103:    /// @notice Checks if entryTokenAddress is registerd for the pair	// @audit-issue: There should be single blank line between function declarations.
104:    function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {
```
[83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L78-L84), [91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L86-L92), [96](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L94-L97), [103](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L101-L104), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

122:    }
123:
124:    /**
125:     * @notice Updates the whitelist filler address
126:     * @dev only owner can call this function
127:     */	// @audit-issue: There should be single blank line between function declarations.
128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {

134:    }
135:
136:    /**
137:     * @notice Updates the whitelist settlement address
138:     * @dev only owner can call this function
139:     */	// @audit-issue: There should be single blank line between function declarations.
140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {

142:    }
143:
144:    /// @notice Registers quote token address for the pair	// @audit-issue: There should be single blank line between function declarations.
145:    function updateQuoteTokenMap(uint256 pairId) external {

149:    }
150:
151:    /// @notice Checks if entryTokenAddress is registerd for the pair	// @audit-issue: There should be single blank line between function declarations.
152:    function _validateQuoteTokenAddress(uint256 pairId, address entryTokenAddress) internal view {
```
[127](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L122-L128), [139](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L134-L140), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L142-L145), [151](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L149-L152), 


```solidity
Path: ./src/types/GlobalData.sol

32:    }
33:
34:    /// @notice Initializes lock for token settlement	// @audit-issue: There should be single blank line between function declarations.
35:    function initializeLock(GlobalDataLibrary.GlobalData storage globalData, uint256 pairId) internal {

59:    }
60:
61:    /// @notice Finalizes lock	// @audit-issue: There should be single blank line between function declarations.
62:    function finalizeLock(GlobalDataLibrary.GlobalData storage globalData)
```
[34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L32-L35), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L59-L62), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html#blank-lines).

### `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

```solidity
Path: ./src/PriceFeed.sol

50:        require(basePrice.expo == -8, "INVALID_EXP");	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L50-L50), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

146:        if (closeRatio == 1e18) {	// @audit-issue
```
[146](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L146-L146), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

197:        if (0 < modifyInfo.hedgeInterval && 10 minutes > modifyInfo.hedgeInterval) {	// @audit-issue

202:        require(modifyInfo.maxSlippageTolerance <= 2 * Bps.ONE);	// @audit-issue

203:        require(-1e6 < modifyInfo.maximaDeviation && modifyInfo.maximaDeviation < 1e6);	// @audit-issue
```
[197](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L197-L197), [202](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L202-L202), [203](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L203-L203), 


```solidity
Path: ./src/libraries/Reallocation.sol

67:                    upper = lower + _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue

80:                    lower = upper - _assetStatusUnderlying.riskParams.rangeSize * 2;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L67-L67), [80](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L80-L80), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

235:            + Math.fullMulDivInt256(2 * _positionParams.amountSqrt, _sqrtPrice, Constants.Q96) + _positionParams.amountQuote;	// @audit-issue

249:        return (2 * (uint256(-squartPosition) * _sqrtPrice) >> Constants.RESOLUTION);	// @audit-issue
```
[235](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L235-L235), [249](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L249-L249), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

67:            * (block.timestamp - lastUpdateTimestamp) / 365 days;	// @audit-issue

71:        poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;	// @audit-issue

72:        poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L67-L67), [71](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L71-L71), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L72-L72), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

21:        return (1600 * b * b / Constants.ONE) / Constants.ONE;	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L21-L21), 


```solidity
Path: ./src/libraries/Perp.sol

168:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);	// @audit-issue

172:            ) * 1e18 / int256(_sqrtAssetStatus.lastRebalanceTotalSquartAmount);	// @audit-issue

399:            f0, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

402:            f1, _assetStatus.totalAmount + _assetStatus.borrowedAmount * spreadParam / 1000, _assetStatus.totalAmount	// @audit-issue

405:        _assetStatus.borrowPremium0Growth += FullMath.mulDiv(f0, 1000 + spreadParam, 1000);	// @audit-issue

406:        _assetStatus.borrowPremium1Growth += FullMath.mulDiv(f1, 1000 + spreadParam, 1000);	// @audit-issue

611:        if (utilization > 1e18) {	// @audit-issue
```
[168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L168-L168), [172](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L172-L172), [399](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L399-L399), [402](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L402-L402), [405](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L405-L405), [406](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L406-L406), [611](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L611-L611), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

239:        if (utilization > 1e18) {	// @audit-issue
```
[239](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L239-L239), 


```solidity
Path: ./src/libraries/SlippageLib.sol

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue

49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L49-L49), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

206:        require(_fee <= 20, "FEE");	// @audit-issue

214:        require(1e8 < _assetRiskParams.riskRatio && _assetRiskParams.riskRatio <= 10 * 1e8, "C0");	// @audit-issue

221:            _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18	// @audit-issue

222:                && _irmParams.slope2 <= 10 * 1e18,	// @audit-issue
```
[206](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L206-L206), [214](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L214-L214), [221](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L221-L221), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L222-L222), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

45:        require(closeRatio > 0 && closeRatio <= 1e18, "ICR");	// @audit-issue

170:        if (ratio > 1e4) {	// @audit-issue

174:        return (riskParams.maxSlippage - ratio * (riskParams.maxSlippage - riskParams.minSlippage) / 1e4);	// @audit-issue
```
[45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L45-L45), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L170-L170), [174](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L174-L174), 


#### Recommendation

Consider defining constants with meaningful names for magic numbers and hexadecimal literals to improve code readability and maintainability.

### Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

```solidity
Path: ./src/markets/perp/PerpOrder.sol

43:    bytes internal constant ORDER_TYPE = abi.encodePacked(PERP_ORDER_TYPE, OrderInfoLib.ORDER_INFO_TYPE);	// @audit-issue seen in: ./src/markets/gamma/GammaOrder.sol

46:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue seen in: ./src/markets/gamma/GammaOrder.sol

47:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue seen in: ./src/markets/gamma/GammaOrder.sol
```
[43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L43-L43), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L47-L47), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

48:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue seen in: ./src/markets/perp/PerpOrder.sol

49:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue seen in: ./src/markets/perp/PerpOrder.sol
```
[48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L49-L49), 


```solidity
Path: ./src/libraries/math/Bps.sol

5:    uint32 public constant ONE = 1e6;	// @audit-issue seen in: ./src/libraries/Constants.sol
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L5-L5), 


#### Recommendation

To avoid constant values being redefined in multiple locations, consider defining constants in a single contract, such as a library, using internal constants. This approach ensures that values remain consistent and reduces the risk of synchronization issues. If a variable serves as a local cache of another contract's value, make the cache variable internal or private, requiring external users to query the contract with the source of truth to prevent synchronization problems.

### Immutable redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

```solidity
Path: ./src/PriceFeed.sol

31:    address private immutable _pyth;	// @audit-issue seen in: ./src/PriceFeed.sol
```
[31](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L31-L31), 


#### Recommendation

To avoid immutable values being redefined in multiple locations, consider defining immutables in a single contract.

### Lack of index element validation in function
There's no validation to check whether the index element provided as an argument actually exists in the call. This omission could lead to unintended behavior if an element that does not exist in the call is passed to the function. The function should validate that the provided index element exists in the call before proceeding.

```solidity
Path: ./src/markets/gamma/ArrayLib.sol

16:        items[index] = items[items.length - 1];	// @audit-issue

24:            if (items[i] == item) {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L16-L16), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L24-L24), 


#### Recommendation

Integrate explicit index validation checks at the beginning of functions that operate based on index elements. Use conditional statements to verify that the provided index falls within the valid range of existing elements. For array operations, ensure the index is less than the array's length. For mappings, consider additional logic to confirm the presence of a key. For example, in an array-based function:
```solidity
function getElementByIndex(uint256 index) public view returns (ElementType) {
    require(index < array.length, "Index out of bounds");
    return array[index];
}
```


### Contract should expose an `interface`
All `external`/`public` functions should extend an `interface`. This is useful to make sure that the whole API is extracted.


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


#### Recommendation

Consider defining an `interface` that includes all `external`/`public` functions of the contract. Exposing a well-defined interface helps ensure that the entire API is extracted and provides a clear and standardized way for other contracts or users to interact with your contract.

### Polymorphic functions make security audits more time-consuming and error-prone
The instances below point to one of two functions with the same name. Consider naming each function differently, in order to make code navigation and analysis easier.

```solidity
Path: ./src/libraries/PositionCalculator.sol

186:    function calculateMinValue(uint256 _sqrtPrice, PositionParams memory _positionParams, uint256 _riskRatio)	// @audit-issue same function also on line(s): 117
```
[186](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L186-L186), 


```solidity
Path: ./src/base/BaseMarket.sol

63:    function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)	// @audit-issue same function also on line(s): 55
```
[63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L63-L63), 


#### Recommendation

Rename one or both of the polymorphic functions to have distinct names, improving code readability and making security audits more efficient and less error-prone. Clear and unique function names help prevent confusion and ensure that the intended function is called.

### Consider using named returns
Using named returns makes the code more self-documenting, makes it easier to fill out NatSpec, and in some cases can save gas. The cases below are where there currently is at most one return statement, which is ideal for named returns.

```solidity
Path: ./src/PredyPool.sol

109:    function registerPair(AddPairLogic.AddPairParams memory addPairParam) external onlyOperator returns (uint256) {	// @audit-issue

337:    function createVault(uint256 pairId) external returns (uint256) {	// @audit-issue

344:    function getSqrtPrice(uint256 pairId) external view returns (uint160) {	// @audit-issue

352:    function getSqrtIndexPrice(uint256 pairId) external view returns (uint256) {	// @audit-issue

357:    function getPairStatus(uint256 pairId) external view returns (DataType.PairStatus memory) {	// @audit-issue

364:    function getVault(uint256 vaultId) external view returns (DataType.Vault memory) {	// @audit-issue

380:    function _getAssetStatusPool(uint256 pairId, bool isQuoteToken)
381:        internal
382:        view
383:        returns (Perp.AssetPoolStatus storage)	// @audit-issue
```
[109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L109-L109), [337](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L337-L337), [344](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L344-L344), [352](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L352-L352), [357](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L357-L357), [364](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L364-L364), [383](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L380-L383), 


```solidity
Path: ./src/PriceFeed.sol

18:    function createPriceFeed(address quotePrice, bytes32 priceId, uint256 decimalsDiff) external returns (address) {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L18-L18), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

7:    function decodeGammaModifyInfo(bytes32 args, uint256 lowerLimit, uint256 upperLimit, int64 maximaDeviation)
8:        internal
9:        pure
10:        returns (GammaModifyInfo memory)	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L7-L10), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

361:    function getUserPositions(address owner) external returns (UserPositionResult[] memory) {	// @audit-issue
```
[361](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L361-L361), 


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

48:    function calculateDelta(uint256 _sqrtPrice, int64 maximaDeviation, int256 _sqrtAmount, int256 perpAmount)
49:        internal
50:        pure
51:        returns (int256)	// @audit-issue

59:    function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)
60:        external
61:        view
62:        returns (bool, uint256 slippageTolerance, GammaTradeMarketLib.CallbackType)	// @audit-issue

106:    function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)
107:        external
108:        view
109:        returns (bool, uint256 slippageTolerance, CallbackType)	// @audit-issue

141:    function calculateSlippageTolerance(uint256 startTime, uint256 currentTime, AuctionParams memory auctionParams)
142:        internal
143:        pure
144:        returns (uint256)	// @audit-issue

167:    function calculateSlippageToleranceByPrice(uint256 price1, uint256 price2, AuctionParams memory auctionParams)
168:        internal
169:        pure
170:        returns (uint256)	// @audit-issue

189:    function saveUserPosition(GammaTradeMarketLib.UserPosition storage userPosition, GammaModifyInfo memory modifyInfo)
190:        external
191:        returns (bool)	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L48-L51), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L59-L62), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L106-L109), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L141-L144), [170](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L167-L170), [191](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L189-L191), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

54:    function hash(PerpOrder memory order) internal pure returns (bytes32) {	// @audit-issue

73:    function resolve(PerpOrder memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L54-L54), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L73-L73), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

149:    function executeOrderV3(SignedOrder memory order, SettlementParamsV3 memory settlementParams)
150:        external
151:        nonReentrant
152:        returns (IPredyPool.TradeResult memory)	// @audit-issue

220:    function _calculateInitialMargin(uint256 vaultId, uint256 pairId, uint256 leverage)
221:        internal
222:        view
223:        returns (int256)	// @audit-issue

236:    function _calculateNetValue(DataType.Vault memory vault, uint256 price) internal pure returns (uint256) {	// @audit-issue

242:    function _calculatePositionValue(DataType.Vault memory vault, uint256 sqrtPrice) internal pure returns (int256) {	// @audit-issue
```
[152](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L149-L152), [223](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L220-L223), [236](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L236-L236), [242](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L242-L242), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

58:    function hash(PerpOrderV3 memory order) internal pure returns (bytes32) {	// @audit-issue

78:    function resolve(PerpOrderV3 memory perpOrder, bytes memory sig) internal pure returns (ResolvedOrder memory) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L58-L58), [78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L78-L78), 


```solidity
Path: ./src/markets/perp/PerpMarket.sol

28:    function executeOrderV3L2(
29:        PerpOrderV3L2 memory compressedOrder,
30:        bytes memory sig,
31:        SettlementParamsV3 memory settlementParams,
32:        uint64 orderId
33:    ) external nonReentrant returns (IPredyPool.TradeResult memory) {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarket.sol#L28-L33), 


```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

118:    function validateLimitPrice(uint256 tradePrice, int256 tradeAmount, uint256 limitPrice)
119:        internal
120:        pure
121:        returns (bool)	// @audit-issue

138:    function validateStopPrice(
139:        uint256 oraclePrice,
140:        uint256 tradePrice,
141:        int256 tradeAmount,
142:        uint256 stopPrice,
143:        bytes memory auctionData
144:    ) internal pure returns (bool) {	// @audit-issue

178:    function ratio(uint256 price1, uint256 price2) internal pure returns (uint256) {	// @audit-issue

188:    function validateMarketOrder(uint256 tradePrice, int256 tradeAmount, bytes memory auctionData)
189:        internal
190:        view
191:        returns (bool)	// @audit-issue
```
[121](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L118-L121), [144](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L138-L144), [178](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L178-L178), [191](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L188-L191), 


```solidity
Path: ./src/vendors/IUniswapV3PoolOracle.sol

18:    function liquidity() external view returns (uint128);	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IUniswapV3PoolOracle.sol#L18-L18), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

5:    function decimals() external view returns (uint8);	// @audit-issue

6:    function description() external view returns (string memory);	// @audit-issue

7:    function version() external view returns (uint256);	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L7-L7), 


```solidity
Path: ./src/libraries/PerpFee.sol

16:    function computeUserFee(
17:        DataType.PairStatus memory assetStatus,
18:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
19:        Perp.UserStatus memory userStatus
20:    ) internal view returns (DataType.FeeAmount memory) {	// @audit-issue

41:    function settleUserFee(
42:        DataType.PairStatus storage assetStatus,
43:        mapping(uint256 => DataType.RebalanceFeeGrowthCache) storage rebalanceFeeGrowthCache,
44:        Perp.UserStatus storage userStatus
45:    ) internal returns (DataType.FeeAmount memory) {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L16-L20), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L41-L45), 


```solidity
Path: ./src/libraries/UniHelper.sol

27:    function convertSqrtPrice(uint160 sqrtPriceX96, bool isQuoteZero) internal pure returns (uint160) {	// @audit-issue

35:    function callUniswapObserve(IUniswapV3Pool uniswapPool, uint256 ago) internal view returns (uint160, uint256) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L35-L35), 


```solidity
Path: ./src/libraries/Reallocation.sol

92:    function isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus) internal view returns (bool) {	// @audit-issue

98:    function _isInRange(Perp.SqrtPerpAssetStatus memory sqrtAssetStatus, int24 currentTick)
99:        internal
100:        pure
101:        returns (bool)	// @audit-issue

165:    function calculateAmount1ForLiquidity(uint160 sqrtRatioA, uint256 available, uint256 liquidityAmount)
166:        internal
167:        pure
168:        returns (uint160)	// @audit-issue

179:    function calculateAmount0ForLiquidity(uint160 sqrtRatioB, uint256 available, uint256 liquidityAmount)
180:        internal
181:        pure
182:        returns (uint160)	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L92-L92), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L98-L101), [168](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L165-L168), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L179-L182), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

102:    function calculateRequiredCollateralWithDebt(uint128 debtRiskRatio) internal pure returns (uint256) {	// @audit-issue

175:    function getHasPositionFlag(PositionParams memory _positionParams) internal pure returns (bool) {	// @audit-issue

231:    function calculateValue(uint256 _sqrtPrice, PositionParams memory _positionParams) internal pure returns (int256) {	// @audit-issue

238:    function calculateSquartDebtValue(uint256 _sqrtPrice, PositionParams memory positionParams)
239:        internal
240:        pure
241:        returns (uint256)	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L102-L102), [175](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L175-L175), [231](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L231-L231), [241](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L238-L241), 


```solidity
Path: ./src/libraries/Trade.sol

76:    function swap(
77:        GlobalDataLibrary.GlobalData storage globalData,
78:        uint256 pairId,
79:        SwapStableResult memory swapParams,
80:        bytes memory settlementData,
81:        uint256 sqrtPrice,
82:        uint256 vaultId
83:    ) internal returns (SwapStableResult memory) {	// @audit-issue

116:    function calculateStableAmount(uint256 currentSqrtPrice, uint256 baseAmount) internal pure returns (uint256) {	// @audit-issue
```
[83](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L76-L83), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L116-L116), 


```solidity
Path: ./src/libraries/VaultLib.sol

24:    function createOrGetVault(GlobalDataLibrary.GlobalData storage globalData, uint256 vaultId, uint256 pairId)
25:        internal
26:        returns (uint256)	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/VaultLib.sol#L24-L26), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

14:    function calculatePremiumCurve(uint256 utilization) internal pure returns (uint256) {	// @audit-issue
```
[14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L14-L14), 


```solidity
Path: ./src/libraries/Perp.sol

119:    function createAssetStatus(address uniswapPool, int24 tickLower, int24 tickUpper)
120:        internal
121:        pure
122:        returns (SqrtPerpAssetStatus memory)	// @audit-issue

145:    function createPerpUserStatus(uint64 _pairId) internal pure returns (UserStatus memory) {	// @audit-issue

202:    function reallocate(
203:        DataType.PairStatus storage _assetStatusUnderlying,
204:        SqrtPerpAssetStatus storage _sqrtAssetStatus
205:    ) internal returns (bool, bool, int256 deltaPositionBase, int256 deltaPositionQuote) {	// @audit-issue

332:    function getAvailableLiquidityAmount(
333:        address _controllerAddress,
334:        address _uniswapPool,
335:        int24 _tickLower,
336:        int24 _tickUpper
337:    ) internal view returns (uint128) {	// @audit-issue

585:    function getAvailableSqrtAmount(SqrtPerpAssetStatus memory _assetStatus, bool _isWithdraw)
586:        internal
587:        pure
588:        returns (uint256)	// @audit-issue

604:    function getUtilizationRatio(SqrtPerpAssetStatus memory _assetStatus) internal pure returns (uint256) {	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L119-L122), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L145-L145), [205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L202-L205), [337](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L332-L337), [588](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L585-L588), [604](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L604-L604), 


```solidity
Path: ./src/libraries/PairLib.sol

5:    function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PairLib.sol#L5-L5), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

29:    function createAssetStatus() internal pure returns (AssetStatus memory) {	// @audit-issue

33:    function createUserStatus() internal pure returns (UserStatus memory) {	// @audit-issue

72:    function isSameSign(int256 a, int256 b) internal pure returns (bool) {	// @audit-issue

155:    function getAssetFee(AssetStatus memory tokenState, UserStatus memory accountState)
156:        internal
157:        pure
158:        returns (uint256)	// @audit-issue

170:    function getDebtFee(AssetStatus memory tokenState, UserStatus memory accountState)
171:        internal
172:        pure
173:        returns (uint256)	// @audit-issue

186:    function updateScaler(AssetStatus storage tokenState, uint256 _interestRate, uint8 _reserveFactor)
187:        internal
188:        returns (uint256)	// @audit-issue

217:    function getTotalCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

222:    function getTotalDebtValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

226:    function getAvailableCollateralValue(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue

230:    function getUtilizationRatio(AssetStatus memory tokenState) internal pure returns (uint256) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L29-L29), [33](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L33-L33), [72](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L72-L72), [158](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L155-L158), [173](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L170-L173), [188](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L186-L188), [217](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L217-L217), [222](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L222-L222), [226](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L226-L226), [230](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L230-L230), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

18:    function calculateInterestRate(IRMParams memory irmParams, uint256 utilizationRatio)
19:        internal
20:        pure
21:        returns (uint256)	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L18-L21), 


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

10:    function toPermit(ResolvedOrder memory order)
11:        internal
12:        pure
13:        returns (ISignatureTransfer.PermitTransferFrom memory)	// @audit-issue

23:    function transferDetails(ResolvedOrder memory order, address to)
24:        internal
25:        pure
26:        returns (ISignatureTransfer.SignatureTransferDetails memory)	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L10-L13), [26](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L23-L26), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

18:    function hash(OrderInfo memory info) internal pure returns (bytes32) {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L18-L18), 


```solidity
Path: ./src/libraries/logic/ReaderLogic.sol

43:    function getPosition(DataType.Vault memory vault, DataType.FeeAmount memory feeAmount)
44:        internal
45:        pure
46:        returns (IPredyPool.Position memory)	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/ReaderLogic.sol#L43-L46), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

192:    function deploySupplyToken(address _tokenAddress) internal returns (address) {	// @audit-issue
```
[192](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L192-L192), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

159:    function calculateSlippageTolerance(int256 minMargin, int256 vaultValue, Perp.AssetRiskParams memory riskParams)
160:        internal
161:        pure
162:        returns (uint256)	// @audit-issue
```
[162](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L159-L162), 


```solidity
Path: ./src/libraries/math/Bps.sol

7:    function upper(uint256 price, uint256 bps) internal pure returns (uint256) {	// @audit-issue

11:    function lower(uint256 price, uint256 bps) internal pure returns (uint256) {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L7-L7), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L11-L11), 


```solidity
Path: ./src/libraries/math/LPMath.sol

10:    function calculateAmount0ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
11:        internal
12:        pure
13:        returns (int256)	// @audit-issue

20:    function calculateAmount1ForLiquidityWithTicks(int24 tickA, int24 tickB, uint256 liquidityAmount, bool isRoundUp)
21:        internal
22:        pure
23:        returns (int256)	// @audit-issue

30:    function calculateAmount0ForLiquidity(
31:        uint160 sqrtRatioA,
32:        uint160 sqrtRatioB,
33:        uint256 liquidityAmount,
34:        bool isRoundUp
35:    ) internal pure returns (int256) {	// @audit-issue

68:    function calculateAmount1ForLiquidity(
69:        uint160 sqrtRatioA,
70:        uint160 sqrtRatioB,
71:        uint256 liquidityAmount,
72:        bool isRoundUp
73:    ) internal pure returns (int256) {	// @audit-issue

108:    function calculateAmount0OffsetWithTick(int24 upper, uint256 liquidityAmount, bool isRoundUp)
109:        internal
110:        pure
111:        returns (int256)	// @audit-issue

119:    function calculateAmount0Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
120:        internal
121:        pure
122:        returns (uint256)	// @audit-issue

134:    function calculateAmount1OffsetWithTick(int24 lower, uint256 liquidityAmount, bool isRoundUp)
135:        internal
136:        pure
137:        returns (int256)	// @audit-issue

145:    function calculateAmount1Offset(uint160 sqrtRatio, uint256 liquidityAmount, bool isRoundUp)
146:        internal
147:        pure
148:        returns (uint256)	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L10-L13), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L20-L23), [35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L30-L35), [73](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L68-L73), [111](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L108-L111), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L119-L122), [137](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L134-L137), [148](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L145-L148), 


```solidity
Path: ./src/libraries/math/Math.sol

12:    function abs(int256 x) internal pure returns (uint256) {	// @audit-issue

16:    function max(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue

20:    function min(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue

24:    function fullMulDivInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {	// @audit-issue

34:    function fullMulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {	// @audit-issue

44:    function mulDivDownInt256(int256 x, uint256 y, uint256 z) internal pure returns (int256) {	// @audit-issue

54:    function addDelta(uint256 a, int256 b) internal pure returns (uint256) {	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L16-L16), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L20-L20), [24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L24-L24), [34](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L34-L34), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L44-L44), [54](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L54-L54), 


```solidity
Path: ./src/base/BaseMarket.sol

47:    function execLiquidationCall(
48:        uint256 vaultId,
49:        uint256 closeRatio,
50:        IFillerMarket.SettlementParams memory settlementParams
51:    ) external returns (IPredyPool.TradeResult memory) {	// @audit-issue

55:    function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams)
56:        internal
57:        view
58:        returns (bytes memory)	// @audit-issue

63:    function _getSettlementData(IFillerMarket.SettlementParams memory settlementParams, address filler)
64:        internal
65:        pure
66:        returns (bytes memory)	// @audit-issue

108:    function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L47-L51), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L55-L58), [66](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L63-L66), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L108-L108), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

61:    function decodeParamsV3(bytes memory settlementData, int256 baseAmountDelta)
62:        internal
63:        pure
64:        returns (SettlementCallbackLib.SettlementParams memory)	// @audit-issue

96:    function execLiquidationCall(
97:        uint256 vaultId,
98:        uint256 closeRatio,
99:        IFillerMarket.SettlementParamsV3 memory settlementParams
100:    ) external virtual returns (IPredyPool.TradeResult memory) {	// @audit-issue

105:    function _getSettlementDataFromV3(IFillerMarket.SettlementParamsV3 memory settlementParams, address filler)
106:        internal
107:        pure
108:        returns (bytes memory)	// @audit-issue

156:    function _getQuoteTokenAddress(uint256 pairId) internal view returns (address) {	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L61-L64), [100](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L96-L100), [108](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L105-L108), [156](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L156-L156), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

25:    function decodeParams(bytes memory settlementData) internal pure returns (SettlementParams memory) {	// @audit-issue
```
[25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L25-L25), 


#### Recommendation

Adopt named returns in your Solidity functions, especially in cases where functions contain a single return statement. This approach enhances code readability and documentation by making the return values clear and explicit. When defining your function, specify the return types with names, and manipulate these named variables directly within your function logic. Additionally, leverage named returns to streamline your NatSpec documentation, providing clear descriptions for each return variable. Evaluate your current contracts for opportunities to refactor functions to use named returns, prioritizing those with simple return patterns for immediate benefits in gas efficiency and code clarity.

### Interfaces should be indicated with an `I` prefix in the contract name
In Solidity, as in many other programming languages, it's a common and recommended practice to use a specific naming convention for interfaces. This often involves prefixing the interface name with an `I`, such as `IERC20` for an ERC20 token interface. This convention helps in quickly differentiating interfaces from implementations and other contract types. It brings clarity and consistency to the codebase, making it easier for developers to understand the structure and relationships of various components in a contract system. While not enforced by Solidity syntax, adhering to this naming convention is considered a best practice in the development community.

```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

4:interface AggregatorV3Interface {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L4-L4), 


#### Recommendation

To improve code readability and follow common naming conventions, consider adding an `I` prefix to the names of your interface contracts. This helps distinguish interfaces from regular contracts and makes it clear that they define a set of functions and events to be implemented by other contracts.

### Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`
Starting with version 0.8.11, abi.encodeCall() has compiler [type safety](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693), whereas the other two functions do not.

```solidity
Path: ./src/libraries/UniHelper.sol

42:            address(uniswapPool).staticcall(abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos));	// @audit-issue

45:            if (keccak256(data) != keccak256(abi.encodeWithSignature("Error(string)", "OLD"))) {	// @audit-issue

61:                abi.encodeWithSelector(IUniswapV3PoolOracle.observe.selector, secondsAgos)	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L42-L42), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L45-L45), [61](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L61-L61), 


#### Recommendation

In Solidity version 0.8.11 and later, it's recommended to use `abi.encodeCall()` for creating function call data, as it provides better type safety compared to `abi.encodeWithSignature()` or `abi.encodeWithSelector()`. This helps prevent type-related errors and ensures more reliable contract interactions.

### Missing events in initializers/deploys
As a best practice, consider emitting an event when the contract is initialized. In this way, it's easy for the user to track the exact point in time when the contract was initialized, by filtering the emitted events.

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

To provide transparency and enable users to track the initialization of the contract, consider emitting an event within the contract's initializer function. Emitting an event during initialization can help users pinpoint the exact moment the contract was initialized by filtering and monitoring the emitted events.

### Hardcoded `address` should be avoided
It's better to declare the hardcoded addresses as `immutable` state variables, as this will facilitate deployment on other chains.


```solidity
Path: ./src/libraries/orders/Permit2Lib.sol

27:    {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/Permit2Lib.sol#L27-L27), 


#### Recommendation

To enhance the flexibility and portability of your smart contract, consider declaring hardcoded addresses as `immutable` state variables. This allows for easier deployment on various chains and ensures that addresses can be updated if necessary without modifying the contract's code.

### Inefficient Array Usage
Use mappings instead of arrays for managing lists of data in order to conserve gas. Mappings are less expensive and more efficient for accessing any value without having to iterate through an array.

```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

50:    mapping(address owner => uint256[]) internal positionIDs;	// @audit-issue

362:        uint256[] memory userPositionIDs = positionIDs[owner];	// @audit-issue

364:        UserPositionResult[] memory results = new UserPositionResult[](userPositionIDs.length);	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L50-L50), [362](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L362-L362), [364](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L364-L364), 


```solidity
Path: ./src/markets/gamma/ArrayLib.sol

5:    function addItem(uint256[] storage items, uint256 item) internal {	// @audit-issue

9:    function removeItem(uint256[] storage items, uint256 item) internal {	// @audit-issue

15:    function removeItemByIndex(uint256[] storage items, uint256 index) internal {	// @audit-issue

20:    function getItemIndex(uint256[] memory items, uint256 item) internal pure returns (uint256) {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L5-L5), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L9-L9), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L15-L15), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/ArrayLib.sol#L20-L20), 


```solidity
Path: ./src/libraries/UniHelper.sol

36:        uint32[] memory secondsAgos = new uint32[](2);	// @audit-issue

68:        int56[] memory tickCumulatives = abi.decode(data, (int56[]));	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L36-L36), [68](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L68-L68), 


#### Recommendation

In scenarios where data access efficiency is critical, prefer using mappings over arrays in Solidity contracts. Mappings offer more efficient and gas-effective data retrieval and updates, especially when dealing with large or frequently accessed datasets. Ensure to structure your data and choose keys thoughtfully to maximize the efficiency gains offered by mappings. While arrays might be suitable for ordered data or when the entire dataset needs to be iterated, for most other use cases, mappings are likely to be the more gas-efficient choice.

### Enum values should be used in place of constant array indexes
Consider using an `enum` instead of hardcoding an index access to make the code easier to understand.

```solidity
Path: ./src/libraries/UniHelper.sol

38:        secondsAgos[0] = uint32(ago);	// @audit-issue

39:        secondsAgos[1] = 0;	// @audit-issue

58:            secondsAgos[0] = uint32(ago);	// @audit-issue

70:        int24 tick = int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int256(ago)));	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L38-L38), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L39-L39), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L58-L58), [70](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L70-L70), 


#### Recommendation

To improve code readability and maintainability, replace hardcoded array indexes with corresponding enum values. Enum values provide descriptive names for array elements, making your code more self-explanatory and reducing the risk of errors when working with arrays. This enhances the overall clarity and robustness of your smart contract code.

### Complex math should be split into multiple steps
Consider splitting long arithmetic calculations (more than 4 operations) into multiple steps to improve the code readability.

```solidity
Path: ./src/libraries/SlippageLib.sol

46:            maxAcceptableSqrtPriceRange > 0	// @audit-issue
47:                && (
48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange
49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
50:                )

48:                    tradeResult.sqrtPrice < sqrtBasePrice * 1e8 / maxAcceptableSqrtPriceRange	// @audit-issue
49:                        || sqrtBasePrice * maxAcceptableSqrtPriceRange / 1e8 < tradeResult.sqrtPrice
```
[46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L46-L50), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L48-L49), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

221:            _irmParams.baseRate <= 1e18 && _irmParams.kinkRate <= 1e18 && _irmParams.slope1 <= 1e18	// @audit-issue
222:                && _irmParams.slope2 <= 10 * 1e18,
```
[221](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L221-L222), 


#### Recommendation

To enhance code readability and maintainability, it's advisable to break down complex arithmetic calculations into multiple steps. This not only makes the code more understandable but also helps in debugging and verifying the correctness of calculations.

### Lack of specific import identifier
It is better to use `import {<identifier>} from "<file.sol>"` instead of `import "<file.sol>"` to improve readability and speed up the compilation time.

```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

9:import "../../interfaces/IPredyPool.sol";	// @audit-issue

12:import "../../libraries/orders/Permit2Lib.sol";	// @audit-issue

13:import "../../libraries/orders/ResolvedOrder.sol";	// @audit-issue

20:import "./PerpOrder.sol";	// @audit-issue

21:import "./PerpOrderV3.sol";	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L9-L9), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L13-L13), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L20-L20), [21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L21-L21), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

8:import "../interfaces/ISettlement.sol";	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L8-L8), 


```solidity
Path: ./src/libraries/PerpFee.sol

4:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

5:import "./PairLib.sol";	// @audit-issue

6:import "./Perp.sol";	// @audit-issue

7:import "./DataType.sol";	// @audit-issue

8:import "./Constants.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PerpFee.sol#L8-L8), 


```solidity
Path: ./src/libraries/UniHelper.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-periphery/contracts/libraries/PositionKey.sol";	// @audit-issue

7:import "../vendors/IUniswapV3PoolOracle.sol";	// @audit-issue

8:import "./Constants.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L8-L8), 


```solidity
Path: ./src/libraries/Reallocation.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

9:import "./Perp.sol";	// @audit-issue

10:import "./ScaledAsset.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L7-L7), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L10-L10), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue

6:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

7:import "./UniHelper.sol";	// @audit-issue

8:import "./Perp.sol";	// @audit-issue

9:import "./DataType.sol";	// @audit-issue

10:import "./Constants.sol";	// @audit-issue

11:import "./math/Math.sol";	// @audit-issue

12:import "../PriceFeed.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L10-L10), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L12-L12), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

4:import "./Perp.sol";	// @audit-issue

5:import "./ScaledAsset.sol";	// @audit-issue

6:import "./DataType.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L6-L6), 


```solidity
Path: ./src/libraries/PremiumCurveModel.sol

4:import "./Constants.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PremiumCurveModel.sol#L4-L4), 


```solidity
Path: ./src/libraries/Perp.sol

4:import "@uniswap/v3-core/contracts/interfaces/IUniswapV3Pool.sol";	// @audit-issue

5:import "@uniswap/v3-periphery/contracts/libraries/PositionKey.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

8:import "@solmate/src/utils/SafeCastLib.sol";	// @audit-issue

9:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue

11:import "./ScaledAsset.sol";	// @audit-issue

12:import "./InterestRateModel.sol";	// @audit-issue

13:import "./PremiumCurveModel.sol";	// @audit-issue

14:import "./Constants.sol";	// @audit-issue

16:import "./UniHelper.sol";	// @audit-issue

17:import "./math/LPMath.sol";	// @audit-issue

18:import "./math/Math.sol";	// @audit-issue

19:import "./Reallocation.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L9-L9), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L11-L11), [12](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L12-L12), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L14-L14), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L16-L16), [17](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L17-L17), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L19-L19), 


```solidity
Path: ./src/libraries/math/LPMath.sol

4:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue

5:import "@uniswap/v3-core/contracts/libraries/TickMath.sol";	// @audit-issue

6:import "@uniswap/v3-core/contracts/libraries/FixedPoint96.sol";	// @audit-issue

7:import "@openzeppelin/contracts/utils/math/SafeCast.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L6-L6), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/LPMath.sol#L7-L7), 


```solidity
Path: ./src/libraries/math/Math.sol

4:import "@uniswap/v3-core/contracts/libraries/FullMath.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Math.sol#L4-L4), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

5:import "../interfaces/IPredyPool.sol";	// @audit-issue

6:import "../interfaces/IHooks.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L6-L6), 


```solidity
Path: ./src/base/BaseMarket.sol

5:import "./BaseHookCallback.sol";	// @audit-issue

7:import "../interfaces/IFillerMarket.sol";	// @audit-issue

8:import "./SettlementCallbackLib.sol";	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L5-L5), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L8-L8), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

8:import "../interfaces/IFillerMarket.sol";	// @audit-issue

9:import "./SettlementCallbackLib.sol";	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L9-L9), 


```solidity
Path: ./src/base/BaseHookCallback.sol

4:import "../interfaces/IPredyPool.sol";	// @audit-issue

5:import "../interfaces/IHooks.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L5-L5), 


```solidity
Path: ./src/types/GlobalData.sol

7:import "../interfaces/IPredyPool.sol";	// @audit-issue

9:import "../libraries/DataType.sol";	// @audit-issue

10:import "./LockData.sol";	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L7-L7), [9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L9-L9), [10](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/GlobalData.sol#L10-L10), 


```solidity
Path: ./src/types/LockData.sol

4:import "../interfaces/IPredyPool.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/types/LockData.sol#L4-L4), 


#### Recommendation

To improve code clarity and avoid naming conflicts, it's recommended to use specific import identifiers when importing from other contracts or libraries. Instead of using `import "<file.sol>";`, specify the desired identifiers using `import { <identifier1>, <identifier2> } from "<file.sol>";`. This not only enhances readability but also can speed up compilation times by only importing the necessary symbols.

### Returning a struct instead of returning many variables is better
If a function returns [too many variables](https://docs.soliditylang.org/en/latest/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

```solidity
Path: ./src/markets/L2Decoder.sol

5:    function decodeSpotOrderParams(bytes32 args1, bytes32 args2)
6:        internal
7:        pure
8:        returns (
9:            bool isLimit,
10:            uint64 startTime,
11:            uint64 endTime,
12:            uint64 deadline,
13:            uint128 startAmount,
14:            uint128 endAmount
15:        )	// @audit-issue

38:    function decodePerpOrderParams(bytes32 args)
39:        internal
40:        pure
41:        returns (uint64 deadline, uint64 pairId, uint8 leverage)	// @audit-issue

50:    function decodePerpOrderV3Params(bytes32 args)
51:        internal
52:        pure
53:        returns (uint64 deadline, uint64 pairId, uint8 leverage, bool reduceOnly, bool closePosition, bool side)	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L5-L15), [41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L38-L41), [53](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/L2Decoder.sol#L50-L53), 


```solidity
Path: ./src/markets/gamma/L2GammaDecoder.sol

38:    function decodeGammaParam(bytes32 args)
39:        internal
40:        pure
41:        returns (uint64 deadline, uint64 pairId, uint32 slippageTolerance, uint8 leverage)	// @audit-issue

51:    function decodeGammaModifyParam(bytes32 args)
52:        internal
53:        pure
54:        returns (
55:            bool isEnabled,
56:            uint64 expiration,
57:            uint32 hedgeInterval,
58:            uint32 sqrtPriceTrigger,
59:            uint32 minSlippageTolerance,
60:            uint32 maxSlippageTolerance,
61:            uint16 auctionPeriod,
62:            uint32 auctionRange
63:        )	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L38-L41), [63](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/L2GammaDecoder.sol#L51-L63), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

333:    function checkAutoHedgeAndClose(uint256 positionId)
334:        external
335:        view
336:        returns (bool hedgeRequired, bool closeRequired, uint256 resultPositionId)	// @audit-issue
```
[336](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L333-L336), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarketLib.sol

59:    function validateHedgeCondition(GammaTradeMarketLib.UserPosition memory userPosition, uint256 sqrtIndexPrice)
60:        external
61:        view
62:        returns (bool, uint256 slippageTolerance, GammaTradeMarketLib.CallbackType)	// @audit-issue

106:    function validateCloseCondition(UserPosition memory userPosition, uint256 sqrtIndexPrice)
107:        external
108:        view
109:        returns (bool, uint256 slippageTolerance, CallbackType)	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L59-L62), [109](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarketLib.sol#L106-L109), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

247:    function getUserPosition(address owner, uint256 pairId)
248:        external
249:        returns (
250:            UserPosition memory userPosition,
251:            IPredyPool.VaultStatus memory vaultStatus,
252:            DataType.Vault memory vault
253:        )	// @audit-issue
```
[253](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L247-L253), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

34:    function isLiquidatable(
35:        DataType.PairStatus memory pairStatus,
36:        DataType.Vault memory _vault,
37:        DataType.FeeAmount memory feeAmount
38:    ) internal view returns (bool _isLiquidatable, int256 minMargin, int256 vaultValue, uint256 sqrtOraclePrice) {	// @audit-issue

63:    function getIsSafe(
64:        DataType.PairStatus memory pairStatus,
65:        DataType.Vault memory _vault,
66:        DataType.FeeAmount memory feeAmount
67:    ) internal view returns (int256 minMargin, bool isSafe, bool hasPosition) {	// @audit-issue

75:    function calculateMinMargin(
76:        DataType.PairStatus memory pairStatus,
77:        DataType.Vault memory vault,
78:        DataType.FeeAmount memory feeAmount
79:    ) internal view returns (int256 minMargin, int256 vaultValue, bool hasPosition, uint256 sqrtOraclePrice) {	// @audit-issue

117:    function calculateMinValue(
118:        int256 marginAmount,
119:        PositionParams memory positionParams,
120:        uint256 sqrtPrice,
121:        uint256 riskRatio
122:    ) internal pure returns (int256 minValue, int256 vaultValue, uint256 debtValue, bool hasPosition) {	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L34-L38), [67](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L63-L67), [79](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L75-L79), [122](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L117-L122), 


```solidity
Path: ./src/libraries/Perp.sol

202:    function reallocate(
203:        DataType.PairStatus storage _assetStatusUnderlying,
204:        SqrtPerpAssetStatus storage _sqrtAssetStatus
205:    ) internal returns (bool, bool, int256 deltaPositionBase, int256 deltaPositionQuote) {	// @audit-issue
```
[205](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L202-L205), 


#### Recommendation

Consider returning a struct instead of multiple variables when a function returns many variables. This can enhance code readability, maintainability, and reusability, as well as make the contract interface more user-friendly.

### Consider using a `struct` rather than having many function input parameters
In Solidity, functions with a large number of input parameters can become cumbersome to manage and call. This can lead to readability issues and increased likelihood of errors, especially if the order of parameters is complex or not intuitive. To streamline this, consider consolidating multiple parameters into a single `struct`. This approach not only simplifies function signatures but also enhances code clarity. Structs allow for grouping related data together, making it easier to understand the relationship between parameters and manage them as a single entity.

```solidity
Path: ./src/markets/perp/PerpMarketLib.sol

26:    function getFinalTradeAmount(
27:        int256 currentPositionAmount,
28:        string memory side,
29:        uint256 quantity,
30:        bool reduceOnly,
31:        bool closePosition
32:    ) internal pure returns (int256 finalTradeAmount) {

80:    function validateTrade(
81:        IPredyPool.TradeResult memory tradeResult,
82:        int256 tradeAmount,
83:        uint256 limitPrice,
84:        uint256 stopPrice,
85:        bytes memory auctionData
86:    ) internal view {

138:    function validateStopPrice(
139:        uint256 oraclePrice,
140:        uint256 tradePrice,
141:        int256 tradeAmount,
142:        uint256 stopPrice,
143:        bytes memory auctionData
144:    ) internal pure returns (bool) {
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L26-L32), [116](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L80-L86), [176](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketLib.sol#L138-L144), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

22:    function swapExactIn(
23:        address,
24:        address baseToken,
25:        bytes memory data,
26:        uint256 amountIn,
27:        uint256 amountOutMinimum,
28:        address recipient
29:    ) external override returns (uint256 amountOut) {

38:    function swapExactOut(
39:        address quoteToken,
40:        address,
41:        bytes memory data,
42:        uint256 amountOut,
43:        uint256 amountInMaximum,
44:        address recipient
45:    ) external override returns (uint256 amountIn) {
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L22-L29), [56](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L38-L45), 


```solidity
Path: ./src/libraries/Reallocation.sol

39:    function _getNewRange(
40:        DataType.PairStatus memory _assetStatusUnderlying,
41:        ScaledAsset.AssetStatus memory _token0Status,
42:        ScaledAsset.AssetStatus memory _token1Status,
43:        int24 currentTick,
44:        int24 tickSpacing
45:    ) internal pure returns (int24 lower, int24 upper) {
```
[87](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Reallocation.sol#L39-L45), 


```solidity
Path: ./src/libraries/Trade.sol

76:    function swap(
77:        GlobalDataLibrary.GlobalData storage globalData,
78:        uint256 pairId,
79:        SwapStableResult memory swapParams,
80:        bytes memory settlementData,
81:        uint256 sqrtPrice,
82:        uint256 vaultId
83:    ) internal returns (SwapStableResult memory) {
```
[110](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Trade.sol#L76-L83), 


```solidity
Path: ./src/libraries/ApplyInterestLib.sol

75:    function emitInterestGrowthEvent(
76:        DataType.PairStatus memory assetStatus,
77:        uint256 interestRateQuote,
78:        uint256 interestRateBase,
79:        uint256 totalProtocolFeeQuote,
80:        uint256 totalProtocolFeeBase
81:    ) internal {
```
[91](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ApplyInterestLib.sol#L75-L81), 


```solidity
Path: ./src/libraries/Perp.sol

745:    function calculateSqrtPerpOffset(
746:        UserStatus memory _userStatus,
747:        int24 _tickLower,
748:        int24 _tickUpper,
749:        int256 _tradeSqrtAmount,
750:        bool _isQuoteZero
751:    ) internal pure returns (int256 offsetUnderlying, int256 offsetStable) {
```
[788](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Perp.sol#L745-L751), 


```solidity
Path: ./src/libraries/ScaledAsset.sol

76:    function updatePosition(
77:        ScaledAsset.AssetStatus storage tokenStatus,
78:        ScaledAsset.UserStatus storage userStatus,
79:        int256 _amount,
80:        uint256 _pairId,
81:        bool _isStable
82:    ) internal {
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/ScaledAsset.sol#L76-L82), 


```solidity
Path: ./src/libraries/orders/DecayLib.sol

16:    function decay2(uint256 startPrice, uint256 endPrice, uint256 decayStartTime, uint256 decayEndTime, uint256 value)
```
[37](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/DecayLib.sol#L16-L16), 


```solidity
Path: ./src/libraries/logic/AddPairLogic.sol

142:    function _storePairStatus(
143:        address quoteToken,
144:        mapping(uint256 => DataType.PairStatus) storage _pairs,
145:        uint256 _pairId,
146:        address _tokenAddress,
147:        bool _isQuoteZero,
148:        AddPairParams memory _addPairParam
149:    ) internal {
```
[190](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/AddPairLogic.sol#L142-L149), 


```solidity
Path: ./src/base/SettlementCallbackLib.sol

40:    function execSettlement(
41:        IPredyPool predyPool,
42:        address quoteToken,
43:        address baseToken,
44:        SettlementParams memory settlementParams,
45:        int256 baseAmountDelta
46:    ) internal {

60:    function execSettlementInternal(
61:        IPredyPool predyPool,
62:        address quoteToken,
63:        address baseToken,
64:        SettlementParams memory settlementParams,
65:        int256 baseAmountDelta
66:    ) internal {

90:    function sell(
91:        IPredyPool predyPool,
92:        address quoteToken,
93:        address baseToken,
94:        SettlementParams memory settlementParams,
95:        address sender,
96:        uint256 price,
97:        uint256 sellAmount
98:    ) internal {

137:    function buy(
138:        IPredyPool predyPool,
139:        address quoteToken,
140:        address baseToken,
141:        SettlementParams memory settlementParams,
142:        address sender,
143:        uint256 price,
144:        uint256 buyAmount
145:    ) internal {
```
[58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L40-L46), [88](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L60-L66), [135](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L90-L98), [182](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/SettlementCallbackLib.sol#L137-L145), 


#### Recommendation

When faced with functions having numerous input parameters, refactor them to accept a `struct` instead. Define a `struct` that encapsulates all these parameters, thereby simplifying the function signature and improving code readability and maintainability. This method is particularly effective in complex functions or those with parameters that are logically related, making the code more intuitive and less error-prone.

### Some variables have a implicit default visibility
Consider always adding an explicit visibility modifier for variables, as the default is `internal`.

```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

27:    uint256 constant _MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 101488915;	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L27-L27), 


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

9:    IPredyPool _predyPool;	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L9-L9), 


```solidity
Path: ./src/base/BaseHookCallback.sol

8:    IPredyPool immutable _predyPool;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallback.sol#L8-L8), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

8:    address immutable _controller;	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L8-L8), 


#### Recommendation

Always add an explicit visibility modifier for variables to enhance code clarity and avoid potential issues. The default visibility for variables is `internal`, but specifying it explicitly makes your intentions clear.

### Outdated Solidity Version
The current Solidity version used in the contract is outdated. Consider using a more recent version for improved features and security.

0.8.4: bytes.concat() instead of abi.encodePacked(,)

0.8.12: string.concat() instead of abi.encodePacked(,)

0.8.13: Ability to use using for with a list of free functions

0.8.14: ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases. Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15: Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays. Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16: Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17: Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.


```solidity
Path: ./src/vendors/IPyth.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/IPyth.sol#L2-L2), 


```solidity
Path: ./src/vendors/AggregatorV3Interface.sol

2:pragma solidity ^0.8.0;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/vendors/AggregatorV3Interface.sol#L2-L2), 


#### Recommendation

Upgrade the contract to a more recent version of Solidity to take advantage of the latest features, improvements, and security enhancements. It's important to stay up-to-date with the Solidity releases to ensure the contract's robustness and compatibility with the Ethereum ecosystem.

### Unnecessary Use of override Keyword
In Solidity version 0.8.8 and later, the use of the override keyword becomes superfluous when a function is overriding solely from an interface and the function isn't present in multiple base contracts. Previously, the override keyword was required as an explicit indication to the compiler. However, this is no longer the case, and the extraneous use of the keyword can make the code less clean and more verbose.
Solidity documentation on [Function Overriding](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding).


```solidity
Path: ./src/PredyPool.sol

78:    function uniswapV3MintCallback(uint256 amount0, uint256 amount1, bytes calldata) external override {	// @audit-issue
```
[78](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L78-L78), 


```solidity
Path: ./src/markets/gamma/GammaTradeMarket.sol

79:    function predyTradeAfterCallback(
80:        IPredyPool.TradeParams memory tradeParams,
81:        IPredyPool.TradeResult memory tradeResult
82:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {	// @audit-issue

138:    function execLiquidationCall(
139:        uint256 vaultId,
140:        uint256 closeRatio,
141:        IFillerMarket.SettlementParamsV3 memory settlementParams
142:    ) external override returns (IPredyPool.TradeResult memory tradeResult) {	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L79-L82), [142](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaTradeMarket.sol#L138-L142), 


```solidity
Path: ./src/markets/perp/PerpMarketV1.sol

102:    function predyTradeAfterCallback(
103:        IPredyPool.TradeParams memory tradeParams,
104:        IPredyPool.TradeResult memory tradeResult
105:    ) external override(BaseHookCallbackUpgradable) onlyPredyPool {	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpMarketV1.sol#L102-L105), 


```solidity
Path: ./src/settlements/UniswapSettlement.sol

22:    function swapExactIn(
23:        address,
24:        address baseToken,
25:        bytes memory data,
26:        uint256 amountIn,
27:        uint256 amountOutMinimum,
28:        address recipient
29:    ) external override returns (uint256 amountOut) {	// @audit-issue

38:    function swapExactOut(
39:        address quoteToken,
40:        address,
41:        bytes memory data,
42:        uint256 amountOut,
43:        uint256 amountInMaximum,
44:        address recipient
45:    ) external override returns (uint256 amountIn) {	// @audit-issue

58:    function quoteSwapExactIn(bytes memory data, uint256 amountIn) external override returns (uint256 amountOut) {	// @audit-issue

62:    function quoteSwapExactOut(bytes memory data, uint256 amountOut) external override returns (uint256 amountIn) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L22-L29), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L38-L45), [58](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L58-L58), [62](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/settlements/UniswapSettlement.sol#L62-L62), 


```solidity
Path: ./src/tokenization/SupplyToken.sol

21:    function mint(address account, uint256 amount) external virtual override onlyController {	// @audit-issue

25:    function burn(address account, uint256 amount) external virtual override onlyController {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/tokenization/SupplyToken.sol#L25-L25), 


#### Recommendation

In Solidity versions 0.8.8 and later, the `override` keyword is no longer required for functions that are solely overriding from an interface and not present in multiple base contracts. Removing the unnecessary `override` keyword can make the code cleaner and less verbose.

### Use a single file for all system-wide constants
System-wide constants should be declared in a single file for better maintainability and readability. This contract seems to contain constants which could potentially be system-wide and could be better managed if they were centralized in a single location.

```solidity
Path: ./src/PriceFeed.sol

35:    uint256 private constant VALID_TIME_PERIOD = 5 * 60;	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PriceFeed.sol#L35-L35), 


```solidity
Path: ./src/markets/gamma/GammaOrder.sol

24:    bytes internal constant GAMMA_MODIFY_INFO_TYPE = abi.encodePacked(	// @audit-issue

39:    bytes32 internal constant GAMMA_MODIFY_INFO_TYPE_HASH = keccak256(GAMMA_MODIFY_INFO_TYPE);	// @audit-issue

81:    bytes internal constant GAMMA_ORDER_TYPE = abi.encodePacked(	// @audit-issue

97:    bytes internal constant ORDER_TYPE =	// @audit-issue

99:    bytes32 internal constant GAMMA_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue

101:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

102:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L24-L24), [39](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L39-L39), [81](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L81-L81), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L97-L97), [99](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L99-L99), [101](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L101-L101), [102](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/gamma/GammaOrder.sol#L102-L102), 


```solidity
Path: ./src/markets/perp/PerpOrder.sol

27:    bytes internal constant PERP_ORDER_TYPE = abi.encodePacked(	// @audit-issue

43:    bytes internal constant ORDER_TYPE = abi.encodePacked(PERP_ORDER_TYPE, OrderInfoLib.ORDER_INFO_TYPE);	// @audit-issue

44:    bytes32 internal constant PERP_ORDER_TYPE_HASH = keccak256(ORDER_TYPE);	// @audit-issue

46:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

47:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L27-L27), [43](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L43-L43), [44](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L44-L44), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L46-L46), [47](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrder.sol#L47-L47), 


```solidity
Path: ./src/markets/perp/PerpOrderV3.sol

28:    bytes internal constant PERP_ORDER_V3_TYPE = abi.encodePacked(	// @audit-issue

45:    bytes internal constant ORDER_V3_TYPE = abi.encodePacked(PERP_ORDER_V3_TYPE, OrderInfoLib.ORDER_INFO_TYPE);	// @audit-issue

46:    bytes32 internal constant PERP_ORDER_V3_TYPE_HASH = keccak256(ORDER_V3_TYPE);	// @audit-issue

48:    string internal constant TOKEN_PERMISSIONS_TYPE = "TokenPermissions(address token,uint256 amount)";	// @audit-issue

49:    string internal constant PERMIT2_ORDER_TYPE = string(	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L28-L28), [45](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L45-L45), [46](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L48-L48), [49](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/markets/perp/PerpOrderV3.sol#L49-L49), 


```solidity
Path: ./src/libraries/UniHelper.sol

11:    uint256 internal constant _ORACLE_PERIOD = 30 minutes;	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/UniHelper.sol#L11-L11), 


```solidity
Path: ./src/libraries/PositionCalculator.sol

22:    uint256 internal constant RISK_RATIO_ONE = 1e8;	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/PositionCalculator.sol#L22-L22), 


```solidity
Path: ./src/libraries/Constants.sol

5:    uint256 internal constant ONE = 1e18;	// @audit-issue

7:    uint256 internal constant MAX_VAULTS = 18446744073709551616;	// @audit-issue

8:    uint256 internal constant MAX_PAIRS = 18446744073709551616;	// @audit-issue

11:    int256 internal constant MIN_MARGIN_AMOUNT = 1e6;	// @audit-issue

13:    uint256 internal constant MIN_LIQUIDITY = 100;	// @audit-issue

15:    uint256 internal constant MIN_SQRT_PRICE = 79228162514264337593;	// @audit-issue

16:    uint256 internal constant MAX_SQRT_PRICE = 79228162514264337593543950336000000000;	// @audit-issue

18:    uint8 internal constant RESOLUTION = 96;	// @audit-issue

19:    uint256 internal constant Q96 = 0x1000000000000000000000000;	// @audit-issue

20:    uint256 internal constant Q128 = 0x100000000000000000000000000000000;	// @audit-issue

23:    uint256 internal constant BASE_MIN_COLLATERAL_WITH_DEBT = 1000;	// @audit-issue

25:    uint256 internal constant BASE_LIQ_SLIPPAGE_SQRT_TOLERANCE = 12422;	// @audit-issue

27:    uint256 internal constant MAX_LIQ_SLIPPAGE_SQRT_TOLERANCE = 24710;	// @audit-issue

29:    uint256 internal constant SLIPPAGE_SQRT_TOLERANCE = 12422;	// @audit-issue

32:    uint256 internal constant SQUART_KINK_UR = 10 * 1e16;	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L5-L5), [7](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L7-L7), [8](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L8-L8), [11](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L11-L11), [13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L15-L15), [16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L16-L16), [18](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L18-L18), [19](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L20-L20), [23](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L23-L23), [25](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L25-L25), [27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L27-L27), [29](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L29-L29), [32](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/Constants.sol#L32-L32), 


```solidity
Path: ./src/libraries/SlippageLib.sol

13:    uint256 public constant MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 100747209;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/SlippageLib.sol#L13-L13), 


```solidity
Path: ./src/libraries/InterestRateModel.sol

16:    uint256 private constant _ONE = 1e18;	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/InterestRateModel.sol#L16-L16), 


```solidity
Path: ./src/libraries/orders/OrderInfoLib.sol

13:    bytes internal constant ORDER_INFO_TYPE = "OrderInfo(address market,address trader,uint256 nonce,uint256 deadline)";	// @audit-issue

14:    bytes32 internal constant ORDER_INFO_TYPE_HASH = keccak256(ORDER_INFO_TYPE);	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L13-L13), [14](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/orders/OrderInfoLib.sol#L14-L14), 


```solidity
Path: ./src/libraries/logic/LiquidationLogic.sol

27:    uint256 constant _MAX_ACCEPTABLE_SQRT_PRICE_RANGE = 101488915;	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/logic/LiquidationLogic.sol#L27-L27), 


```solidity
Path: ./src/libraries/math/Bps.sol

5:    uint32 public constant ONE = 1e6;	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/libraries/math/Bps.sol#L5-L5), 


#### Recommendation

Consider centralizing system-wide constants in a single file for better maintainability and readability. This practice makes it easier to manage and update constants across the contract and promotes consistency in your codebase.

### Missing events in sensitive functions
Events should be emitted when sensitive changes are made to the contracts, but some functions lack them.

```solidity
Path: ./src/PredyPool.sol

300:    function allowTrader(uint256 pairId, address trader, bool enabled) external onlyPoolOwner(pairId) {	// @audit-issue
```
[300](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/PredyPool.sol#L300-L300), 


```solidity
Path: ./src/base/BaseMarket.sol

84:    function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {	// @audit-issue

92:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyOwner {	// @audit-issue

97:    function updateQuoteTokenMap(uint256 pairId) external {	// @audit-issue
```
[84](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L84-L84), [92](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L92-L92), [97](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarket.sol#L97-L97), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

128:    function updateWhitelistFiller(address newWhitelistFiller) external onlyFiller {	// @audit-issue

132:    function updateQuoter(address newQuoter) external onlyFiller {	// @audit-issue

140:    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {	// @audit-issue

145:    function updateQuoteTokenMap(uint256 pairId) external {	// @audit-issue
```
[128](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L128-L128), [132](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L132-L132), [140](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L140-L140), [145](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L145-L145), 


#### Recommendation

To enhance transparency and auditability, ensure that events are emitted when sensitive changes are made to the contracts. Review and update functions that lack event emissions, especially in cases where sensitive operations or state changes occur, to provide a clear record of such actions.

### Include sender information in events
When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

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

To improve the usability and analysis of your smart contract events, consider including the `msg.sender` address as part of the event data. This enables easier filtering and identification of the sender's actions within your contract, providing valuable insights for users and external tools.

### Empty constructor
A constructor is an optional function. If there is no constructor, the contract will assume the default constructor, which is equivalent to constructor() {}.

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


```solidity
Path: ./src/base/BaseHookCallbackUpgradable.sol

13:    constructor() {}	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseHookCallbackUpgradable.sol#L13-L13), 


```solidity
Path: ./src/base/BaseMarketUpgradable.sol

36:    constructor() {}	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/./src/base/BaseMarketUpgradable.sol#L36-L36), 


#### Recommendation

Remove redundant parts of code.