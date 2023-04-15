---
eip: .nan
title: Sharable interface for Borrowing
description: Defines interface for borrowing from a defi pool
author: 0xbakuchi (@massun-onibakuchi), Takeru Natsui(@cotoneum), Brian Ko (@briankostar)
discussions-to: https://ethereum-magicians.org/t/eip-5313-light-contract-ownership/10052
status: Draft
type: Standards Track
category: ERC
created: 2023-08-15
---

## Abstract

This EIP proposes a standard API for borrowing tokens from lending protocols and introduces a borrowing aggregator that provides users with optimal borrowing paths across multiple lending protocols.

## Motivation

Many lending protocols in the DeFi ecosystem share common features, and creating a standard API will simplify the process of building applications that interact with these protocols. Borrowing aggregators can provide users with lower borrow APRs and higher supply APRs, improving the overall user experience.

## Specification

The key word “MUST” in this document is to be interpreted as described in RFC 2119.

Every contract compliant with this EIP MUST implement the `EIP6882` interface.

```solidity
interface ILendingMarket {
    /**
     * @notice Supplies an `amount` of underlying asset into the reserve, receiving in return overlying interest-bearing tokens.
     * - E.g. User supplies 100 USDC and gets in return 100 / price interest-bearing USDC
     * @param asset The address of the underlying asset to supply
     * @param amount The amount to be supplied
     * @param receiver The address that will receive the interest-bearing tokens, same as msg.sender if the user
     *   wants to receive them on his own wallet, or a different address if the beneficiary of interest-bearing tokens
     *   is a different wallet
     */
    function supply(address asset, uint256 amount, address receiver) external returns (uint256);

    /**
     * @notice Withdraws an `amount` of underlying asset from the reserve, burning the equivalent interest-bearing tokens owned
     * E.g. User has 100 interest-bearing USDC, calls withdraw() and receives 100 * price USDC, burning the 100 interest-bearing USDC
     * @param asset The address of the underlying asset to withdraw
     * @param amount The underlying amount to be withdrawn
     * @param receiver The address that will receive the underlying, same as msg.sender if the user
     *   wants to receive it on his own wallet, or a different address if the beneficiary is a
     *   different wallet
     * @return The final amount withdrawn
     */
    function withdraw(address asset, uint256 amount, address receiver) external returns (uint256);

    /**
     * @notice Allows users to borrow a specific `amount` of the reserve underlying asset, provided that the borrower
     * already supplied enough collateral, or he was given enough allowance by a credit delegator on the
     * corresponding debt token
     * - E.g. User borrows 100 USDC passing as `receiver` his own address, receiving the 100 USDC in his wallet
     *   and 100 debt tokens
     * @param asset The address of the underlying asset to borrow
     * @param amount The amount to be borrowed
     * @param receiver The address of the user who will receive the debt. Should be the address of the borrower itself
     * calling the function if he wants to borrow against his own collateral, or the address of the credit delegator
     * if he has been given credit delegation allowance
     */
    function borrow(address asset, uint256 amount, address receiver, address owner) external returns (uint256);

    /**
     * @notice Repays a borrowed `amount` on a specific reserve, burning the equivalent debt tokens owned
     * @param asset The address of the borrowed underlying asset previously borrowed
     * @param amount The amount to repay
     * @param onBehalfOf The address of the user who will get his debt reduced/removed. Should be the address of the
     * user calling the function if he wants to reduce/remove his own debt, or the address of any other
     * other borrower whose debt should be removed
     * @return The final amount repaid
     */
    function repay(address asset, uint256 amount, address onBehalfOf) external returns (uint256);

    /*
     * Allow third party accounts to borrow tokens on behalf of the owner.
     * @param delegatee The address of the third party account to allow
     * @param allowed True if the delegatee is allowed to borrow tokens on behalf of the owner, false otherwise
     */
    function allow(address delegatee, bool allowed) external;

    /*
     * Returns true if the delegatee is allowed to borrow tokens on behalf of the owner.
     * @param owner The address of the owner
     * @param account The address of the delegatee
     */
    function isAllowed(address owner, address account) external view returns (bool);
}

```

## Rationale

The rationale behind creating a standard API for borrowing tokens is to improve the user and developer experience when interacting with lending protocols. By providing a borrowing aggregator, users can access aggregated data from multiple lending protocols in a single place and easily move funds between them to achieve the best borrowing rates.

## Backwards Compatibility

This proposal is designed to be compatible with major lending protocols, such as Aave V2/V3 and Compound V3. However, it may not support some protocols like Compound V2 and Morpho, due to significant differences in their interfaces and the lack of support for 'credit delegation' parameters.

## Test Cases

Test cases for the proposed EIP will be provided in a well-written test template for contracts that comply with the standard.

## Security Considerations

By providing a well-written test template for contracts that comply with the standard, the security of the contracts can be improved, leading to better user experience and higher overall security.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).
