### 1) PairLib::getRebalanceCacheId() can revert due to overflow at some point in future due to how the return value is computed.

```
   function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {
        return pairId * type(uint64).max + rebalanceId;
    }
```