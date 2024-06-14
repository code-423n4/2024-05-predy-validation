https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L78-L90

Authorization in uniswapV3MintCallback:

Vulnerability: The allowedUniswapPools check does not revert if the pool is not allowed.
Mitigation: Ensure proper revert messages and validation.