# [GAS-1] `public` functions not used internally could be marked as `external`  
------  
### **Description**  
`public` function are preferred to be marked `external` if not used internally.  
  
### **Impact**  
It is preferred to mark such functions as `external` because it would be gas efficient.  
  
### **PoC**  
- src/PredyPool.sol#70 https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L70  
- src/markets/gamma/GammaTradeMarket.sol#69 https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/markets/gamma/GammaTradeMarket.sol#L69  
- src/markets/perp/PerpMarketV1.sol#92 https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/markets/perp/PerpMarketV1.sol#L92  
  
### **Tools Used**  
Aderyn, Manual review  
  
  
  
# [GAS-2] Modifiers invoked only once can be shoe-horned into the function  
------  
### **Description**  
If a modifier is being used only once, one can directly integrate it into the function that is using it.  
  
### **Impact**  
It would save gas and provide cleaner code.  
  
### **PoC**  
- src/PredyPool.sol#52 https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L52  
- src/PredyPool.sol#63 https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src/PredyPool.sol#L63  
  
### **Tools Used**  
Aderyn, Manual review  
  