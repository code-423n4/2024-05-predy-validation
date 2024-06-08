# L-01
Remove tautologies
## Proof of concept
The pair and vault id is units and a uint is always bigger than zero so you don't need to check if it is negative but it is checked here there are 2 instances:
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L27
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L31

# L-02
The wrong error is returned when the lock doesn't exist which might cause confusion to the user 

## Proof of concept
When the vault id is invalid InvalidPairId() error is returned consider creating and giving another error like for example InvalidVaultId()
https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/types/GlobalData.sol#L31
