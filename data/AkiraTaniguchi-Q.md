# summary
You should fix the version of Solidity pragma.

# problem
Currently, each Solidity file contains a

```
pragma solidity ^0.8.17;.
```

and pragma is set.

With this configuration,

・　When there are multiple developers and deployers
・　When a new compiler version is released

the bytecode compiled in each environment will be different.


# Why it's a problem
Operation may or may not change depending on the environment, and defects may or may not occur, resulting in inconsistent quality.


# Examples of specific responses

```
pragma solidity 0.8.17;.
```

and fix all Solidity versions.
The version to be fixed should be the latest possible (0.8.26 as of 2024/5) for defects and gas costs, though,
The default EVM has been Cancun since 0.8.24, so if the product is released outside the Ethereum mainnet, consider changing the default EVM, or 0.8.23 to avoid, whichever you prefer.

# supplement
in foundry's configuration file.
```
solc_version = "0.8.19"
```
so I don't think it's really a problem, but well, I think it's better for quality if we keep it consistent throughout.

