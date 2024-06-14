https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/PredyPool.sol#L78-L90

Authorization in uniswapV3MintCallback:

Vulnerability: The allowedUniswapPools check does not revert if the pool is not allowed.
Mitigation: Ensure proper revert messages and validation.


https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L7-L28

The SupplyToken contract allows the controller to mint and burn tokens, which can significantly affect the token's supply and value. If the controller address is compromised, it can lead to unauthorized minting or burning of tokens, resulting in potential loss of value and trust in the token.

Recommended Mitigation Steps

Secure the Controller Address:

1- Use a multisig wallet for the controller address to ensure that multiple parties must approve critical actions.
2- Consider implementing time-locks for minting and burning operations to allow for community oversight and intervention.