https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/markets/perp/PerpMarketLib.sol#L33

Using keccak256 for string comparison is gas-expensive. Consider using enums instead of strings for the side parameter.