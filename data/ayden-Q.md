1.Ownable2Step is preferred to change the owner of pool 
https://github.com/code-423n4/2024-05-predy/blob/main/src/PredyPool.sol#L97-L102
```solidity
    function setOperator(address newOperator) external onlyOperator {
        require(newOperator != address(0));
        operator = newOperator; //@audit it's better 2-step to change owner.

        emit OperatorUpdated(newOperator);
    }
```