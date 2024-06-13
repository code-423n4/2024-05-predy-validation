# [L-1] Constants.MIN_MARGIN_AMOUNT should not be a constant
## Impact  
- Inconsistencies in risk management across different pools/pairs. (`Constants.MIN_MARGIN_AMOUNT` doesn't serve its purpose for token with different decimals)  
## Proof-of-Concept  
Every open positions must maintain their margin above minimum margin calculated from a formula.  
However, if the calculated min. margin is less than `Constants.MIN_MARGIN_AMOUNT`, it will be set at `Constants.MIN_MARGIN_AMOUNT`.   
```solidity
if (hasPosition && minMargin < Constants.MIN_MARGIN_AMOUNT) {
    minMargin = Constants.MIN_MARGIN_AMOUNT;
}
```
See: https://github.com/code-423n4/2024-05-predy/blob/main/src/libraries/PositionCalculator.sol#L97-L99  

However, margin are quoted in quote token amount, which can be different for each pool/pair.  
In this current version of the codebase, `Constants.MIN_MARGIN_AMOUNT` is set at `1e6`. For a pair with `USDC` (6 decimals) as a quote token, the minimum margin would be interpreted as `1e6 USDC` which is roungly `1 USD`. In contrast, for a pair with `DAI` (18 decimals) as a quote token, the minimum margin would be interpreted as `1e6 DAI` which is roughly `1e-12 USD`.  

This creates inconsistencies in risk management across different pools/pairs.  
## Recommendation  
- Allow each pool/pair to have its own setting of minimum margin