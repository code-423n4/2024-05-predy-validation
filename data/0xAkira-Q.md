## [L-01] Lack of indexed parameters in events
Some events are defined with no indexed parameters. For example, `PriceFeedCreated`,`OperatorUpdated`,`RecepientUpdated`,

## Recommended Mitigation
Consider indexing event parameters to avoid hindering the task of off-chain services searching and filtering for specific events.

---

## [L-02] Missing address(0) checks

Consider adding an address(0) check here:

https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseMarketUpgradable.sol#L132-L134

```solidity
function updateQuoter(address newQuoter) external onlyFiller {
        _quoter = PredyPoolQuoter(newQuoter);
    }
```
---
https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseMarketUpgradable.sol#L140-L142

```solidity
function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {
        _whiteListedSettlements[settlementContractAddress] = isEnabled;
    }
```
---
https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseMarketUpgradable.sol#L128-L130

```solidity
function updateWhitelistFiller(address newWhitelistFiller) external onlyOwner {
        whitelistFiller = newWhitelistFiller;
    }
```
---

## [L-03] Single-step ownership change introduces risks

### Description:
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L97-L102

Single-step ownership transfers add the risk of setting an unwanted owner by accident if the ownership transfer is not done with excessive care.

```solidity 
function setOperator(address newOperator) external onlyOperator {
        require(newOperator != address(0));
        operator = newOperator;

        emit OperatorUpdated(newOperator);
    }
```
### Recommendation:
Consider employing 2 step ownership transfer mechanisms for this critical ownership, such as
Open Zeppelin's [Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

---

## [L-04] Remove unnecessary code
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L177-L191
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L199-L213

functions `withdrawProtocolRevenue()` and `withdrawCreatorRevenue()` have unnecessary code 

```solidity 
function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {
        Perp.AssetPoolStatus storage pool = _getAssetStatusPool(pairId, isQuoteToken);

        uint256 amount = pool.accumulatedProtocolRevenue;

->      require(amount > 0, "AZ");

        pool.accumulatedProtocolRevenue = 0;

->      if (amount > 0) {
            ERC20(pool.token).safeTransfer(msg.sender, amount);
        }

        emit ProtocolRevenueWithdrawn(pairId, isQuoteToken, amount);
    }
```

```solidity 
 function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyPoolOwner(pairId) {
        Perp.AssetPoolStatus storage pool = _getAssetStatusPool(pairId, isQuoteToken);

        uint256 amount = pool.accumulatedCreatorRevenue;

->      require(amount > 0, "AZ");

        pool.accumulatedCreatorRevenue = 0;

->      if (amount > 0) {
            ERC20(pool.token).safeTransfer(msg.sender, amount);
        }

        emit CreatorRevenueWithdrawn(pairId, isQuoteToken, amount);
    }

```

### Recommendation:
Remove one of the checks 

---

## [L-05] Unnecessary check

https://github.com/code-423n4/2024-05-predy/blob/main/src/libraries/logic/SupplyLogic.sol#L22-L44

The `supply` function has an unnecessary comparison condition, this function accepts `uint256 _amount` and then compares that `_amount <=0`, but the fact that the function accepts **uint** is already a check that `_amount` **cannot be less than 0**

```solidity 
function supply(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)
        external
        returns (uint256 mintAmount)
    {
        // Checks pair exists
        globalData.validate(_pairId);
        // Checks amount is not 0
->      if (_amount <= 0) {
            revert IPredyPool.InvalidAmount();
        }
```

### Recommendation:
Leave the check only that `_amount != 0`

```diff
function supply(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)
        external
        returns (uint256 mintAmount)
    {
        // Checks pair exists
        globalData.validate(_pairId);
        // Checks amount is not 0
-      if (_amount <= 0) {
            revert IPredyPool.InvalidAmount();
        }
+       if (_amount == 0) {
            revert IPredyPool.InvalidAmount();
        }
```
---

## [L-06] Error in NatSpec

https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseMarketUpgradable.sol#L125-L142

In `NatSpec` comments it is stated that only the **owner** can call these functions, but the `onlyFiller` modifier is present

```solidity 
   /**
     * @notice Updates the whitelist filler address
     * @dev only owner can call this function
     */

    function updateQuoter(address newQuoter) external onlyFiller {
        _quoter = PredyPoolQuoter(newQuoter);
    }
```

```solidity
/**
     * @notice Updates the whitelist settlement address
     * @dev only owner can call this function
     */
    function updateWhitelistSettlement(address settlementContractAddress, bool isEnabled) external onlyFiller {
        _whiteListedSettlements[settlementContractAddress] = isEnabled; 
    }
```

### Recommendation

replace `only owner` in `NatSpec` with `only filler`.

---

## [NC-01] Unused imports

https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L9-L10

There are a few unused imports on the code base. These imports should be cleaned up from the code if they have no purpose. Clearing these imports will increase the readability of contracts

```solidity 
import {IHooks} from "./interfaces/IHooks.sol";
import {ISettlement} from "./interfaces/ISettlement.sol";
```

## Recommendation:

Consider removing imports from the code.
---

## [NC-02] Visibility variables 

https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseHookCallbackUpgradable.sol#L9

It is best practice to set the visibility of state variables explicitly.

```solidty
abstract contract BaseHookCallbackUpgradable is Initializable, IHooks {
->    IPredyPool _predyPool; 
    
    error CallerIsNotPredyPool();

    constructor() {}
```

## [NC-03] UNNECESSARY IMPORTS

https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseHookCallback.sol#L4
https://github.com/code-423n4/2024-05-predy/blob/main/src/base/BaseHookCallbackUpgradable.sol#L5

The following interfaces imports can be removed because they are redundant

- `import "../interfaces/IPredyPool.sol";`

    - It is included in the `"../interfaces/IHooks.sol"` import in the contract `BaseHookCallback.sol`
    - It is included in the `"../interfaces/IHooks.sol"` import in the contract `BaseHookCallbackUpgradable.sol`

```solidity
import "../interfaces/IPredyPool.sol";
import "../interfaces/IHooks.sol";

abstract contract BaseHookCallback is IHooks {
    IPredyPool immutable _predyPool;
```

```solidity
import "./IPredyPool.sol";

interface IHooks {
```
