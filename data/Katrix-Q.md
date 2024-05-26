# [L-1] Missing checks for address(0) when assigning values to address state variables  
-------  
## Description  
Checking for address(0) is crucial as one might lose tokens, assets or even ownership if address(0) is entered.  
  
## PoC  
src/PredyPool.sol#303 - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L303  
  
## Tools used  
Aderyn, Manual Review  
  
## Recommendation  
Add a check for the zero address as well.  
  
  
  
# [NC-1] Internal functions called only once can be inlined
-------  
## Description  
Instead of separating the logic into a separate function, consider inlining the logic into the calling function. This can reduce the number of function calls and improve readability.  
  
## PoC  
src/base/BaseMarketUpgradable.sol#61  
src/base/SettlementCallbackLib.sol#60  
src/base/SettlementCallbackLib.sol#90  
src/base/SettlementCallbackLib.sol#137  
src/libraries/ApplyInterestLib.sol#75  
src/libraries/Perp.sol#262  
src/libraries/Perp.sol#305  
src/libraries/Perp.sol#332  
src/libraries/Perp.sol#526  
src/libraries/Perp.sol#604  
src/libraries/Perp.sol#618  
src/libraries/Perp.sol#707  
src/libraries/Perp.sol#719  
src/libraries/PerpFee.sol#95  
src/libraries/PerpFee.sol#136  
src/libraries/PositionCalculator.sol#63  
src/libraries/PositionCalculator.sol#102  
src/libraries/PositionCalculator.sol#117  
src/libraries/PositionCalculator.sol#141  
src/libraries/PositionCalculator.sol#151  
src/libraries/PositionCalculator.sol#163  
src/libraries/PositionCalculator.sol#186  
src/libraries/PositionCalculator.sol#238  
src/libraries/Reallocation.sol#126  
src/libraries/Reallocation.sol#147  
src/libraries/Reallocation.sol#165  
src/libraries/Reallocation.sol#179  
src/markets/perp/PerpOrderV3.sol#58  
src/markets/perp/PerpOrder.sol#54  
src/markets/perp/PerpOrderLib.sol#178  
src/markets/perp/PerpOrderLib.sol#188  
src/markets/gamma/ArrayLib.sol#15  
src/markets/gamma/ArrayLib.sol#20  
...and several other instances  
  
## Tools used  
Aderyn, Manual review  
  
## Recommendation  
If possible, remove the functions and consider inlining the code to reduce function calls and to improve readability.  
  
  
  
# [NC-2] Dead code found, may take up runtime bytecode  
-------
## Description  
Several LoC were found that contained functions that were never used in any smart contract. If they are of no importance, please consider removing them.  
  
## Impact  
Dead codes are usually of no importance. They clutter up the code and also take up your runtime bytecode so it would be best to get rid of it.  
  
## PoC  
src/base/BaseMarket.sol - Entire contract has not been used or accessed by any other contract. In this contract, there are a couple of functions that haven't been used at all internally either.  
src/base/BaseMarket.sol#104 (_validateQuoteTokenAddress(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/base/BaseMarket.sol#L104  
src/base/BaseMarket.sol#114 (_revertTradeResult(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/base/BaseMarket.sol#L114  
  
src/types/GlobalData.sol#26 (validateVaultId(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/types/GlobalData.sol#L26  
  
src/markets/perp/PerpMarketV1.sol#265 (_saveUserPosition(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/markets/perp/PerpMarketV1.sol#L265  
  
src/markets/perp/PerpOrder.sol#54 (hash(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/markets/perp/PerpOrder.sol#L54  
src/markets/perp/PerpOrder.sol#73 (resolve(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/markets/perp/PerpOrder.sol#L73  
  
src/libraries/PositionCalculator.sol#135 (getHasPosition(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/PositionCalculator.sol#L135  
  
src/libraries/UniHelper.sol#87 (getFeeGrowthInsideLast(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/UniHelper.sol#L87  
  
src/libraries/VaultLib.sol#11 (getVault(...)) - https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/libraries/VaultLib.sol#L11  
  
## Tools Used  
Slither, Manual Review  
  
## Recommendation  
Please check if the above mentioned functions really are never used, and if so, consider removing them.  
  
  
  
# [NC-3] Unused Custom Errors  
-------  
## Description  
Custom errors are defined which have never been used.  
  
## PoC  
- src/markets/perp/PerpMarketV1.sol#36
```javascript
error TPSLConditionDoesNotMatch();
```  

- src/markets/perp/PerpMarketV1.sol#38
```javascript
error UpdateMarginMustNotBePositive();
```  
  
## Tools used  
Aderyn, Manual review  
  
## Recommendation  
It would be recommended to remove the definitions of these unused custom errors.