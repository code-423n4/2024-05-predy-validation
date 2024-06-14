

# L-01: **Approval Reset Failure in `swapExactOut` Function Leads to Potential Security Risk**

---

## Impact

The `swapExactOut` function in the smart contract does not reset the token approval to zero after completing the swap operation. This oversight leaves the contract approved for an indefinite amount, exposing it to potential security risks. Specifically, malicious actors could exploit this by executing unauthorized transactions using the previously approved amount.

## Link to Github

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/settlements/UniswapSettlement.sol#L52-L54 

### Scenario: Exploitation of Unreset Approval

1. **Setup**: The `swapExactOut` function is called with a maximum input amount and executes a swap.
2. **Approval and Swap Execution**: The function transfers the maximum input amount and sets approval for the `_swapRouter`.

   ```solidity
   ERC20(quoteToken).safeTransferFrom(msg.sender, address(this), amountInMaximum);
   ERC20(quoteToken).approve(address(_swapRouter), amountInMaximum);
   ```

3. **Completion**: The swap executes, and any excess tokens are returned to the sender.

   ```solidity
   amountIn = _swapRouter.exactOutput(
       ISwapRouter.ExactOutputParams(data, recipient, block.timestamp, amountOut, amountInMaximum)
   );

   if (amountInMaximum > amountIn) {
       ERC20(quoteToken).safeTransfer(msg.sender, amountInMaximum - amountIn);
   }
   ```

4. **Flaw**: The approval is not reset to zero after the swap, leaving the contract vulnerable to unauthorized transfers using the previously approved amount.

## Recommended Mitigation Steps

To prevent unauthorized use of the approved amount, the approval for `_swapRouter` should be reset to zero after the swap operation completes. This practice aligns with the best practices for handling ERC20 approvals and mitigates the security risk.



# L-02: **Controller Assignment Issue in `SupplyToken` Contract: Need to Update to `iPredyPool`**

---

## Impact

The `SupplyToken` contract uses an outdated controller address state. The `_controller` was initially set to an address intended for a previous contract, but now it should be updated to the `iPredyPool` contract, which is the new main controller. This misconfiguration can lead to control issues, where the intended new controller (`iPredyPool`) does not have the expected authority over the `SupplyToken`.

https://github.com/code-423n4/2024-05-predy/blob/a9246db5f874a91fb71c296aac6a66902289306a/src/tokenization/SupplyToken.sol#L8



## Tools Used

- **Manual Code Review**

## Recommended Mitigation Steps

To ensure that `iPredyPool` has the proper control over the `SupplyToken`, update the controller assignment to use the `iPredyPool` address instead of the outdated controller. This change will align the contract with the current governance structure.




