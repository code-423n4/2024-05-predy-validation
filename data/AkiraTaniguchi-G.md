# summary
Using Transient Storage

# problem
Currently, ReentrancyGuardUpgradeable is used to prevent reentrancy attacks.

# Why it's a problem
Extra gas is required because the flags are written to storage.

# Examples of specific responses

Gas costs can be reduced by using the Transient Storage feature, which can be used temporarily during transaction execution.

see
https://soliditylang.org/blog/2024/01/26/transient-storage/

# supplement
This is a feature from Solidity 0.8.24, so if you want to use it, you will need to check the network support and change the default EVM if necessary.