## [L] Remove redundant `amount` check from `withdrawProtocolRevenue(...)` function

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L186-L188

Modify the `withdrawProtocolRevenue(...)` function as shown below as the amount has been checked initially to be greater than zero
```solidity
File: PredyPool.sol
177:     function withdrawProtocolRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {
178:         Perp.AssetPoolStatus storage pool = _getAssetStatusPool(pairId, isQuoteToken);
179: 
180:         uint256 amount = pool.accumulatedProtocolRevenue;
181: 
182:         require(amount > 0, "AZ");
183: 
184:         pool.accumulatedProtocolRevenue = 0;
185: 
186:  -      if (amount > 0) {
187:             ERC20(pool.token).safeTransfer(msg.sender, amount);
188:  -      }
189: 
190:         emit ProtocolRevenueWithdrawn(pairId, isQuoteToken, amount);
191:     }

```


## [L] Remove redundant `amount` check from `withdrawCreatorRevenue(...)` function

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L199-L210

Modify the `withdrawCreatorRevenue(...)` function as shown below as the amount has been checked initially to be greater than zero
```solidity
File: PredyPool.sol
199:     function withdrawCreatorRevenue(uint256 pairId, bool isQuoteToken) external onlyOperator {
200:         Perp.AssetPoolStatus storage pool = _getAssetStatusPool(pairId, isQuoteToken);
201: 
202:         uint256 amount = pool.accumulatedCreatorRevenue;
203: 
204:         require(amount > 0, "AZ");
205: 
206:         pool.accumulatedCreatorRevenue = 0;
207: 
208:  -      if (amount > 0) {
209:             ERC20(pool.token).safeTransfer(msg.sender, amount);
210:  -      }
211: 
212:         emit CreatorRevenueWithdrawn(pairId, isQuoteToken, amount);
213:     }

```