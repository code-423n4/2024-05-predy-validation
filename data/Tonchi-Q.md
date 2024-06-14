## [info] Title A Range of compiler version used, leads to inconsistent behaviour

**Description:** Using a range of compiler version can leads to compiler bugs, inconsistent behaviour, allows the use of deprecated features of older versions. This practice is used in entire protocol.

**Impact:** It can leads to Deployment issues, inconsistent contract behaviour, maintenance complexity.

**Recommendation:** Specify an exact compiler version for predictable behaviour, security, consistency, and maintainability. 


## [Info] Imported Libraries not used, unnecessarily increased bytecode size

**Description:** In `Trade.sol` ```import {LockDataLibrary} from "../types/LockData.sol";``` this library not used and in `LockData.sol` ```import ../interfaces/IPredyPool.sol:``` not used. It increased bytecode size and increases execution cost.

**Impact:** It unnecessarily increased bytecode size.

**Recommendation:** Remove these lines from contract.

## [info] Missing Natspec, leads to confusion to auditors

**Description:** In this protocol many important and critical function doesn't have natspec, so security researcher may get confused and misunderstood the function. Also the parameter are not documented which passed to the function and the auditor itself find the parameter meaning.

**Impact:** Leads to confusion to contract readers

**Recommended Mitigation:** Specify natspec/documentation for each critical functions

## [Low] Event emitted after external interaction, lack of information got to user because of this.

**Description:** In `SupplyLogic.sol::supply`, if user accidently called supply on recieving mintAmount itself than the contract emit event only once. Exact information not reached if supply called mint and mint called to msg.sender but while recieving mintAmount the contract calls supply then it emits event once. It should emit twice as expected.

**Impact:** Lack of information reached to user.

**Recommended Mitigation:** Follows CEI(checks, effects, interactions):
Do following changes in `SupplyLogic.sol::supply`:
```diff
function supply(GlobalDataLibrary.GlobalData storage globalData, uint256 _pairId, uint256 _amount, bool _isStable)
        external
        returns (uint256 mintAmount)
    {
        // Checks pair exists
        globalData.validate(_pairId);
        // Checks amount is not 0
        if (_amount <= 0) {
            revert IPredyPool.InvalidAmount();
        }
        // Updates interest rate related to the pair
        ApplyInterestLib.applyInterestForToken(globalData.pairs, _pairId);

        DataType.PairStatus storage pair = globalData.pairs[_pairId];
+       emit TokenSupplied(msg.sender, pair.id, _isStable, _amount);
        if (_isStable) {
            mintAmount = receiveTokenAndMintBond(pair.quotePool, _amount);
        } else {
            mintAmount = receiveTokenAndMintBond(pair.basePool, _amount);
        }
-       emit TokenSupplied(msg.sender, pair.id, _isStable, _amount);
    }
```


## [Low/Gas] Removes item accidently and missing checks in `ArrayLib.sol::removeItemByIndex` for items array if index not found, disruption of functionality of protocol and it becomes gas expensive also.

**Description:** In `ArrayLib.sol::removeItemByIndex`, If the item not found in the Items storage array, than  `ArrayLib.sol::removeItemByIndex` called and removes the element at last index. And mark element of last index at type(uint256).max. This bug can have higher severity but it already checked from where it is called, So it is specified in a low severity because it should have checks so that it can not unneccessarily execute the operation

**Impact:** disruption of functionality which is not intended and it becomes more gas expensive to execute an unnecessary operations.

**Proof of Concept:**
1. The `ArrayLib.sol::removeItem` called.
2. If index not found than `index = type(uint256).max`
3. than It calls `ArrayLib.sol::removeItemByIndex`
4. It removes the last element of the array `Items` Which is in storage.
   
**Recommended Mitigation:** Add checks if index is type(uint256).max than it should not execute. Than it won't accidently removes last element from array and also will not unneccessary storage operations 


