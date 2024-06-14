## Low Risk Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-01](#L-01) | Missing checks for `address(0)` in constructor | 4 | 
| [L-02](#L-02) | Tokens may be minted to the zero address | 1 | 
| [L-03](#L-03) | Constructor / initialization function lacks parameter validation | 1 | 

## Low Risk Issues

### <a name="L-01"></a>[L-01] Missing checks for `address(0)` in constructor

*Instances (4)*:

```solidity
File: src/PriceFeed.sol

15:         _pyth = pyth;

38:         _quotePriceFeed = quotePrice;

39:         _pyth = pyth;

```

[15](https://github.com/code-423n4/2024-05-predy/tree/main/src/PriceFeed.sol#L15), [38](https://github.com/code-423n4/2024-05-predy/tree/main/src/PriceFeed.sol#L38), [39](https://github.com/code-423n4/2024-05-predy/tree/main/src/PriceFeed.sol#L39)

```solidity
File: src/tokenization/SupplyToken.sol

18:         _controller = controller;

```

[18](https://github.com/code-423n4/2024-05-predy/tree/main/src/tokenization/SupplyToken.sol#L18)

### <a name="L-02"></a>[L-02] Tokens may be minted to the zero address
The target address is not checked in the mint function.

*Instances (1)*:

```solidity
File: src/tokenization/SupplyToken.sol

21:     function mint(address account, uint256 amount) external virtual override onlyController {
22:            _mint(account, amount);
23:        }

```

[21-23](https://github.com/code-423n4/2024-05-predy/tree/main/src/tokenization/SupplyToken.sol#L21-L23)

### <a name="L-03"></a>[L-03] Constructor / initialization function lacks parameter validation
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

*Instances (1)*:

```solidity
File: src/PriceFeed.sol

/// @audit  priceId, decimalsDiff
37:     constructor(address quotePrice, address pyth, bytes32 priceId, uint256 decimalsDiff) {
38:            _quotePriceFeed = quotePrice;
39:            _pyth = pyth;
40:            _priceId = priceId;
41:            _decimalsDiff = decimalsDiff;
42:        }

```

[37-42](https://github.com/code-423n4/2024-05-predy/tree/main/src/PriceFeed.sol#L37-L42)
