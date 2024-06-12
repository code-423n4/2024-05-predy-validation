
# [L-01] Lack of double-step transfer ownership pattern in PredyPool.sol::setOperator


## Description

The current operator ownership transfer process for PredyPool contract does not implement 2 step transfer ownership pattern.

If the nominated EOA account is not a valid account, it is entirely possible that the operator may accidentally transfer ownership to an uncontrolled account, losing the access to all functions with the `onlyOperator` modifier.


Similar issue is found in `BaseMarketUpgradable.sol::updateWhitelistFiller`.


## Recommendation

It is recommended to implement a two-step process where the operator nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of the ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

## Reference

OpenZeppelin’s Ownable2Step contract: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol


# [L-02] OpenZeppelin’s Ownable2Step should be used instead of Solmate's Owned


## Description

Solmate's Owned contract does not implement 2 step transfer ownership pattern.

If the nominated EOA account is not a valid account, it is entirely possible that the admin may accidentally transfer ownership to an uncontrolled account, losing the access to all functions with the `onlyOwner` modifier.

## Recommendation

Use OpenZeppelin’s Ownable2Step contract: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol in `BaseMarket.sol` & `SpotMarket.sol`.

## Reference


# [NC-01] Typo in `PredyPool.sol::updateRecepient`

## Description

There is a typo in `PredyPool.sol::updateRecepient` function.

## Recommendation

Change it to `PredyPool.sol::updateRecipient`