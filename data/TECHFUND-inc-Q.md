### 1) PairLib::getRebalanceCacheId() can revert due to overflow at some point in future due to how the return value is computed.

```
   function getRebalanceCacheId(uint256 pairId, uint64 rebalanceId) internal pure returns (uint256) {
        return pairId * type(uint64).max + rebalanceId;
    }
```

### 2) ApplyInterestLib::applyInterestForPoolStatus() implementation has a chance of losing a small fragment of the totalProtocolFee when sharing between protocol and creator.

```
    poolStatus.accumulatedProtocolRevenue += totalProtocolFee / 2;
     poolStatus.accumulatedCreatorRevenue += totalProtocolFee / 2;
```

The recommendation is to implement as below so that one of the party gets the fractional amount that could have been lost.

```
    uint256 protocolShare = totalProtocolFee / 2;
    poolStatus.accumulatedProtocolRevenue += protocolShare;
    poolStatus.accumulatedCreatorRevenue += totalProtocolFee - protocolShare;
```
