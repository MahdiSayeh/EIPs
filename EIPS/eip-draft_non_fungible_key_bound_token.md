---
eip: .nan
title: Non-Fungible Key Bound Token
description: An interface for Non-Fungible Key Bound Tokens, also known as a  NFKBT.
author: Mihai Onila (@MihaiORO), Nick Zeman (@NickZCZ), Narcis Cotaie (@NarcisCRO)
discussions-to: https://ethereum-magicians.org/t/non-fungible-key-bound-token-kbt/13625
status: Draft
type: Standards Track
category: ERC
created: 2023-03-31
requires: 721
---

## Abstract

A standard interface for Non-Fungible Key Bound Tokens (NFKBT), a subset of the more general Key Bound Tokens (KBT).

The following standardizes an API for tokens within smart contracts and provides basic functionality to the `addBinding`[^2] function. This function designates **Key Wallets**[^3], which are responsible for conducting a **Safe Transfer**[^6]. During this process, **NFKBTs** are safely approved so they can be spent by the user or an on-chain third-party entity.

The premise of **NFKBTs** is to provide fully optional security features built directly into the non-fungible asset, via the concept of _allow_ found in the `allowTransfer`[^4] and `allowApproval`[^5] functions. These functions are called by one of the **Key Wallets**[^3] and _allow_ the **Holding Wallet** to either call the already familier `transfer` and `approve` function found in the [_ERC-721_](./eip-721.md). Responsibility for the **NFKBT** is therefore split. The **Holding Wallet** contains the asset and **Key Wallets** have authority over how the assets can be spent or approved. **Default Behaviors**[^22] of a traditional non-fungible _ERC-721_ can be achieved by simply never using the `addBinding`[^2] function.

We considered **NFKBTs** being used by every individual who wishes to add additional security to their non-fungible assets, as well as consignment to third-party wallets/brokers/banks/insurers/museums. **NFKBTs** allow tokens to be more resilient to attacks/thefts, by providing additional protection to the asset itself on a self-custodial level.

## Motivation

TIn this fast-paced technologically advancing world, people learn and mature at different speeds. The goal of global adoption must take into consideration the target demographic is of all ages and backgrounds. Unfortunately for self-custodial assets, one of the greatest pros is also one of its greatest cons. The individual is solely responsible for their actions and adequately securing their assets. If a mistake is made leading to a loss of funds, no one is able to guarantee their return.

In 2021, the United States Federal Trade Commission received more than 46,000 filed reports of crypto theft valued at over $1 Billion[^19]. Theft and malicious scams are an issue in any financial sector and oftentimes lead to stricter regulation. However, government-imposed regulation goes against one of this space’s core values. Efforts have been made to increase security within the space through centralized and decentralized means. Up until now, no one has offered a solution that holds onto the advantages of both whilst eliminating their disadvantages.

We asked ourselves the same question as many have in the past, “How does one protect the wallet?”. After a while, realizing the question that should be asked is “How does one protect the asset?”. Creating the wallet is free, the asset is what has value and is worth protecting. This question led to the development of **KBTs**. A solution that is fully optional and can be tailored so far as the user is concerned. Individual assets remain protected even if the seed phrase or private key is publicly released, as long as the security feature was activated.

**NFKBTs** saw the need to improve on the widely used non-fungible _ERC-721_ token standard. The security of non-fungible assets is a topic that concerns every entity in the crypto space, as their current and future use cases continue to be explored. **NFKBTs** provide a scalable decentralized security solution that takes security one step beyond wallet security e.g. _BIP-39_, focusing on the token's ability to remain secure. The security is on the blockchain itself, which allows every demographic that has access to the internet to secure their assets without the need for current hardware or centralized solutions. Made to be a promising alternative, **NFKBTs** inherit all the characteristics of an _ERC-721_. This was done so **NFKBTs** could be used on every dApp that is configured to use traditional non-fungible tokens.

During the development process, the potential advantages **KBTs** explored were the main motivation factors leading to their creation;

1. Completely Decentralized: The security features are fully decentralized meaning no third-party will have access to user funds when activated. This was done in order to truly stay in line with the premise of self-custodial assets, responsibility and values.

2. Limitless Scalability: Centralized solutions require the creation of an account and their availability may be restricted based on location. **NFKBTs** do not face regional restrictions or account creation. Decentralized security solutions such as hardware options face scalability issues requiring transport logistics, secure shipping, and vendor. **NFKBTs** can be used anywhere around the world by anyone who so wishes, provided they have access to the internet.

3. Fully Optional Security: Security features are optional, customizable and removable. It’s completely up to the user to decide the level of security they would like when using **NFKBTs**.

4. Default Functionality: If the user would like to use **NFKBTs** as a traditional _ERC-721_, the security features do not have to be activated. As the token inherits all of the same characteristics, it results in the token acting with traditional non-fungible **Default Behaviours**[^22]. However, even when the security features are activated, the user will still have the ability to customize the functionality of the various features based on their desired outcome. The user can pass a set of custom and or **Default Values**[^23] manually or through a dApp.

5. Unmatched Security: By calling the `addBinding`[^2] function a **Key Wallet**[^3] is now required for the `allowTransfer`[^4] or `allowApproval`[^5] function. The `allowTransfer`[^4] function requires 4 parameters, `_tokenId`[^14], `_time`[^15], `_address`[^16], and `_allFunds`[^29], where as the `allowApproval`[^5] function has 2 parameters, `_time`[^30] and `_numberOfTransfers`[^31]. In addition to this, **NFKBTs** have a `safeFallback`[^12] and `resetBindings`[^13] function. The combination of all these prevent and virtually cover every single point of failure that is present with a traditional _ERC-721_[^9], when properly used.

6. Security Fail-Safes: With **NFKBTs**, users can be confident that their tokens are safe and secure, even if the **Holding Wallet**[^11] or one of the **Key Wallets**[^3] has been compromised. If the owner suspects that the **Holding Wallet**[^11] has been compromised or lost access, they can call the `safeFallback`[^12] function from one of the **Key Wallets**[^3]. This moves the assets to the other **Key Wallet**[^3] preventing a single point of failure. If the owner suspects that one of the **Key Wallets**[^3] has been comprised or lost access, the owner can call the `resetBindings`[^13] function from `_keyWallet1`[^7] or `_keyWallet2`[^8]. This resets the **NFKBTs** security feature and allows the **Holding Wallet**[^11] to call the `addBinding`[^2] function again. New **Key Wallets**[^3] can therefore be added and a single point of failure can be prevented.

7. Anonymous Security: Frequently, centralized solutions ask for personal information that is stored and subject to prying eyes. Purchasing decentralized hardware solutions are susceptible to the same issues e.g. a shipping address, payment information or a camera recording during a physical cash pick-up. This may be considered by some as infringing on their privacy and asset anonymity. **NFKBTs**[^1] ensures user confidentially as everything can be done remotely under a pseudonym on the blockchain.

8. Low-Cost Security: The cost of using **NFKBTs**[^1] security features correlate to on-chain fees, the current _GWEI_ at the given time. As a standalone solution, they are a viable cost-effective security measure feasible to the majority of the population.

9. Environmentally Friendly: Since the security features are coded into the **NFKBT**, there is no need for centralized servers, shipping, or the production of physical object/s. Thus leading to a minimal carbon footprint by the use of **NFKBTs**, working hand in hand with Ethereum’s change to a _PoS_[^28] network.

10. User Experience: The security feature can be activated by a simple call to the `addBinding`[^2] function. The user will only need two other wallets, which will act as `_keyWallet1`[^7] and `_keyWallet2`[^8], to gain access to all of the benefits **NFKBTs** offer. The optional security features improve the overall user experience and Ethereum ecosystem by ensuring a safety net for those who decide to use it. Those that do not use the security features are not hindered in any way. This safety net can increase global adoption as people can remain confident in the security of their assets, even in the scenario of a compromised wallet.

## Specification

### ERC-N (Token Contract)

**NOTES**:

- The following specifications use syntax from Solidity `0.8.17` (or above)
- Callers MUST handle `false` from `returns (bool success)`. Callers MUST NOT assume that `false` is never returned!

```solidity
interface IKBT721 {
    event AccountSecured(address indexed _account, uint256 _noOfTokens);
    event AccountResetBinding(address indexed _account);
    event SafeFallbackActivated(address indexed _account);
    event AccountEnabledTransfer(
        address _account,
        uint256 _tokenId,
        uint256 _time,
        address _to,
        bool _anyToken
    );
    event AccountEnabledApproval(
        address _account,
        uint256 _time,
        uint256 _numberOfTransfers
    );
    event Ingress(address _account, uint256 _tokenId);
    event Egress(address _account, uint256 _tokenId);

    struct AccountHolderBindings {
        address firstWallet;
        address secondWallet;
    }

    struct FirstAccountBindings {
        address accountHolderWallet;
        address secondWallet;
    }

    struct SecondAccountBindings {
        address accountHolderWallet;
        address firstWallet;
    }

    struct TransferConditions {
        uint256 tokenId;
        uint256 time;
        address to;
        bool anyToken;
    }

    struct ApprovalConditions {
        uint256 time;
        uint256 numberOfTransfers;
    }

    function addBindings(
        address _keyWallet1,
        address _keyWallet2
    ) external returns (bool);

    function getBindings(
        address _account
    ) external view returns (AccountHolderBindings memory);

    function resetBindings() external returns (bool);

    function safeFallback() external returns (bool);

    function allowTransfer(
        uint256 _tokenId,
        uint256 _time,
        address _to,
        bool _allTokens
    ) external returns (bool);

    function getTransferableFunds(
        address _account
    ) external view returns (TransferConditions memory);

    function allowApproval(
        uint256 _time,
        uint256 _numberOfTransfers
    ) external returns (bool);

    function getApprovalConditions(
        address account
    ) external view returns (ApprovalConditions memory);

    function getNumberOfTransfersAllowed(
        address _account,
        address _spender
    ) external view returns (uint256);

    function isSecureWallet(address _account) external returns (bool);

    function isSecureToken(uint256 _tokenId) external returns (bool);
}
```

---

### Events

#### `AccountSecured` event

Emitted when the `_account` is securing his account by calling the `addBindings` function.

`_amount` is the current balance of the `_account`.

```solidity
event AccountSecured(address _account, uint256 _amount)
```

#### `AccountResetBinding` event

Emitted when the holder is resetting his `keyWallets` by calling the `resetBindings` function.

```solidity
event AccountResetBinding(address _account)
```

#### `SafeFallbackActivated` event

Emitted when the holder is choosing to move all the funds to one of the `keyWallets` by calling the `safeFallback` function.

```solidity
event SafeFallbackActivated(address _account)
```

#### `AccountEnabledTransfer` event

Emitted when the `_account` has allowed for transfer `_amount` of tokens for the `_time` amount of `block` seconds for `_to` address (or if
the `_account` has allowed for transfer all funds though `_allFunds` set to `true`) by calling the `allowTransfer` function.

```solidity
event AccountEnabledTransfer(address _account, uint256 _amount, uint256 _time, address _to, bool _allFunds)
```

#### `AccountEnabledApproval` event

Emitted when `_account` has allowed approval for the `_time` amount of `block` seconds by calling the `allowApproval` function.

```solidity
event AccountEnabledApproval(address _account, uint256 _time)
```

#### `Ingress` event

Emitted when `_account` becomes a holder. `_amount` is the current balance of the `_account`.

```solidity
event Ingress(address _account, uint256 _amount)
```

#### `Egress` event

Emitted when `_account` transfers all his tokens and is no longer a holder. `_amount` is the previous balance of the `_account`.

```solidity
event Egress(address _account, uint256 _amount)
```

---

### **Interface functions**

The functions detailed below MUST be implemented.

#### `addBindings` function

Secures the sender account with other two wallets called `_keyWallet1` and `_keyWallet2` and MUST fire the `AccountSecured` event.

The function SHOULD `throw` if:

- the sender account is not a holder
- or the sender is already secured
- or the keyWallets are the same
- or one of the keyWallets is the same as the sender
- or one or both keyWallets are zero address (`0x0`)
- or one or both keyWallets are already keyWallets to another holder account

```solidity
function addBindings (address _keyWallet1, address _keyWallet2) external returns (bool)
```

#### `getBindings` function

The function returns the `keyWallets` for the `_account` in a `struct` format.

```solidity
struct AccountHolderBindings {
    address firstWallet;
    address secondWallet;
}
```

```solidity
function getBindings(address _account) external view returns (AccountHolderBindings memory)
```

#### `resetBindings` function

**Note:** This function is helpful when one of the two `keyWallets` is compromised.

Called from a `keyWallet`, the function resets the `keyWallets` for the `holder` account. MUST fire the `AccountResetBinding` event.

The function SHOULD `throw` if the sender is not a `keyWallet`.

```solidity
function resetBindings() external returns (bool)
```

#### `safeFallback` function

**Note:** This function is helpful when the `holder` account is compromised.

Called from a `keyWallet`, this function transfers all the tokens from the `holder` account to the other `keyWallet` and MUST fire the `SafeFallbackActivated` event.

The function SHOULD `throw` if the sender is not a `keyWallet`.

```solidity
function safeFallback() external returns (bool);
```

#### `allowTransfer` function

Called from a `keyWallet`, this function is called before a `transferFrom` or `safeTransferFrom` functions are called.

It allows to transfer a tokenId, for a specific time frame, to a specific address.

If the tokenId is 0 then there will be no restriction on the tokenId.
If the time is 0 then there will be no restriction on the time.
If the to address is zero address then there will be no restriction on the to address.
Or if `_anyToken` is `true`, regardless of the other params, it allows any token, whenever, to anyone to be transferred of the holder.

The function MUST fire `AccountEnabledTransfer` event.

The function SHOULD `throw` if the sender is not a `keyWallet` for a holder or if the owner of the `_tokenId` is different than the `holder`.

```solidity
function allowTransfer(uint256 _tokenId, uint256 _time, address _to, bool _anyToken) external returns (bool);
```

#### `getTransferableFunds` function

The function returns the transfer conditions for the `_account` in a `struct` format.

```solidity
struct TransferConditions {
    uint256 tokenId;
    uint256 time;
    address to;
    bool anyToken;
}
```

```solidity
function getTransferableFunds(address _account) external view returns (TransferConditions memory);
```

#### `allowApproval` function

Called from a `keyWallet`, this function is called before `approve` or `setApprovalForAll` functions are called.

It allows the `holder` for a specific amount of `_time` to do an `approve` or `setApprovalForAll` and limit the number of transfers the spender is allowed to do through `_numberOfTransfers` (0 - unlimited number of transfers in the allowance limit).

The function MUST fire `AccountEnabledApproval` event.

The function SHOULD `throw` if the sender is not a `keyWallet`.

```solidity
function allowApproval(uint256 _time) external returns (bool)
```

#### `getApprovalConditions` function

The function returns the approval conditions in a struct format. Where `time` is the `block.timestamp` until the `approve` or `setApprovalForAll` functions can be called, and `numberOfTransfers` is the number of transfers the spender will be allowed.

```solidity
struct ApprovalConditions {
    uint256 time;
    uint256 numberOfTransfers;
}
```

```solidity
function getApprovalConditions(address _account) external view returns (ApprovalConditions memory);
```

#### `transferFrom` function

The function transfers from `_from` address to `_to` address the `_tokenId` token.

Each time a spender calls the function the contract subtracts and checks if the number of allowed transfers of that spender has reached 0,
and when that happens, the approval is revoked using a set approval for all to `false`.

The function MUST fire the `Transfer` event.

The function SHOULD `throw` if:

- the sender is not the owner or is not approved to transfer the `_tokenId`
- or if the `_from` address is not the owner of the `_tokenId`
- or if the sender is a secure account and it has not allowed for transfer this `_tokenId` through `allowTransfer` function.

```solidity
function transferFrom(address _from, address _to, uint256 _tokenId) external returns (bool)
```

#### `safeTransferFrom` function

The function transfers from `_from` address to `_to` address the `_tokenId` token.

The function MUST fire the `Transfer` event.

The function SHOULD `throw` if:

- the sender is not the owner or is not approved to transfer the `_tokenId`
- or if the `_from` address is not the owner of the `_tokenId`
- or if the sender is a secure account and it has not allowed for transfer this `_tokenId` through `allowTransfer` function.

```solidity
function safeTransferFrom(address _from, address _to, uint256 _tokenId, bytes memory data) external returns (bool)
```

#### `safeTransferFrom` function, with data parameter

This works identically to the other function with an extra data parameter, except this function just sets data to "".

```solidity
function safeTransferFrom(address _from, address _to, uint256 _tokenId) external returns (bool)
```

#### `approve` function

The function allows `_to` account to transfer the `_tokenId` from the sender account.

The function also limits the `_to` account to the specific number of transfers set in the `ApprovalConditions` for that `holder` account. If the value is `0` then the `_spender` can transfer multiple times.

The function MUST fire an `Approval` event.

If the function is called again it overrides the number of transfers allowed with `_numberOfTransfers`, set in `allowApproval` function.

The function SHOULD `throw` if:

- the sender is not the current NFT owner, or an authorized operator of the current owner
- the NFT owner is secured and has not called `allowApproval` function
- or if the `_time`, set in the `allowApproval` function, has elapsed.

```solidity
function approve(address _to, uint256 _tokenId) public virtual override(ERC721, IERC721)
```

#### `setApprovalForAll` function

The function enables or disables approval for another account `_operator` to manage all of sender assets.

The function also limits the `_to` account to the specific number of transfers set in the `ApprovalConditions` for that `holder` account. If the value is `0` then the `_spender` can transfer multiple times.

The function Emits an `Approval` event indicating the updated allowance.

If the function is called again it overrides the number of transfers allowed with `_numberOfTransfers`, set in `allowApproval` function.

The function SHOULD `throw` if:

- the sender account is secured and has not called `allowApproval` function
- or if the `_spender` is a zero address (`0x0`)
- or if the `_time`, set in the `allowApproval` function, has elapsed.

```solidity
function setApprovalForAll(address _operator, bool _approved) public virtual override(ERC721, IERC721)
```

---

### **Internal functions**

#### `_hasAllowedTransfer` function

The function returns `true` if:

- the `_account` has allowed for transfer any token through `_anyToken` parameter
- or the `_account` has allowed for transfer a `_tokenId`, for `_to` address and if the `time` has not elapsed

In all other cases the function will return `false`.

```solidity
function _hasAllowedTransfer(
        address _account,
        uint256 _tokenId,
        address _to
    ) internal view returns (bool) {
        TransferConditions memory conditions = _transferConditions[_account];

        if (conditions.anyToken) {
            return true;
        }

        if (
            (conditions.tokenId == 0 &&
                conditions.time == 0 &&
                conditions.to == address(0)) ||
            (conditions.tokenId > 0 && conditions.tokenId != _tokenId) ||
            (conditions.time > 0 && conditions.time < block.timestamp) ||
            (conditions.to != address(0) && conditions.to != _to)
        ) {
            return false;
        }

        return true;
    }
```

#### `_getAccountHolder` function

This function identifies and returns the `_holder` account starting from the `sender` address.

If the `sender` is not a `keyWallet` zero address is returned (`0x0`).

```solidity
function _getAccountHolder() internal view returns (address) {
    address sender = _msgSender();
    return
        _firstAccounts[sender].accountHolderWallet != address(0)
            ? _firstAccounts[sender].accountHolderWallet
            : (
                _secondAccounts[sender].accountHolderWallet != address(0)
                    ? _secondAccounts[sender].accountHolderWallet
                    : address(0)
            );
}
```

#### `_getOtherSecureWallet` function

This function identifies and returns the other `keyWallet` starting from the `sender` address.

If the `sender` is not a `keyWallet` zero address is returned (`0x0`).

```solidity
function _getOtherSecureWallet() internal view returns (address) {
    address sender = _msgSender();
    address accountHolder = _getAccountHolder();

    return
        _holderAccounts[accountHolder].firstWallet == sender
            ? _holderAccounts[accountHolder].secondWallet
            : _holderAccounts[accountHolder].firstWallet;
}
```

#### `_isApprovalAllowed` function

This function returns `true` if the `_time`, set in `allowApproval` function, has not elapsed yet.

```solidity
function _isApprovalAllowed(address account) internal view returns (bool) {
    return _allowApproval[account] >= block.timestamp;
}
```

#### `_afterTokenTransfer` function

This function is called after a successful `transfer`, `transferFrom`, `mint` and `burn`.

This function fires `Egress` event when loosing a holder and `Ingress` event when gaining a holder.

```solidity
function _afterTokenTransfer(
    address _from,
    address _to,
    uint256 _firstTokenId,
    uint256 _batchSize
) internal virtual override {
    if (_from != address(0)) {
        // region update secureAccounts
        if (isSecureWallet(_from) && balanceOf(_from) == 0) {
            delete _firstAccounts[_holderAccounts[_from].firstWallet];
            delete _secondAccounts[_holderAccounts[_from].secondWallet];
            delete _holderAccounts[_from];
        }
        // endregion
        if (balanceOf(_from) == 0) {
            emit Egress(_from, _firstTokenId);
        }
    }

    if (_to != address(0) && balanceOf(_to) == _batchSize) {
        emit Ingress(_to, _firstTokenId);
    }
}
```

## Rationale

The intent from individual technical decisions made during the development of **NFKBTs** focused on maintaining consistency and backward compatibility with _ERC-721s_, all the while offering self-custodial security features to the user. It was important that **NFKBTs** inherited all of the _ERC-721s_ characteristics to comply with requirements found in dApps which use non-fungible tokens on their platform. In doing so, it allowed for flawless backward compatibility to take place and gave the user the choice to decide if they want their **NFKBTs** to act with **Default Behaviours**[^22]. We wanted to ensure that wide-scale implementation and adoption of **NFKBTs** could take place immediately, without the greater collective needing to adapt and make changes to the already flourishing decentralized ecosystem.

For developers and users alike, the `allowTransfer`[^4] and `allowApproval`[^5] functions both return bools rather than reverting on failure. This decision was done purposefully, to keep consistency with the already familiar _ERC-721_. Additional technical decisions related to self-custodial security features are broken down and located within the security considerations section.

## Backwards Compatibility

**KBTs** are designed to be backward-compatible with existing token standards and wallets. Existing tokens and wallets will continue to function as normal, and will not be affected by the implementation of **NFKBTs**.

## Test Cases

The [KBTstandard repository](../assets/eip-6809/README.md) has all the [tests](../assets/eip-6809/test/kbt721.js).

Average Gas used (_GWEI_[^17] ):

- `addBindings` - 155,096
- `resetBindings` - 30,588
- `safeFallback` - 72,221 (depending on how many NFTs the holder has)
- `allowTransfer` - 50,025
- `allowApproval` - 44,983

## Reference Implementation

The GitHub repository [KBTstandard repository](../assets/eip-6809/README.md) contains the implementation. There's also a [diagram](../assets/eip-6809/Contract%20interactions%20diagram.png) with the contract interactions.

## Security Considerations

**NFKBTs** were designed with security in mind every step of the way. Below are some design decisions that were rigorously discussed and thought through during the development process.

**Key Wallets**[^3]: These have been limited to two, in order to prevent a pitfall scenario of a user adding multiple wallets. For this reason, we have stuck to _BIP-39_ split across three wallets as opposed to creating a _SLIP-39_ security solution. Thus we can prevent a disastrous `safeFallback`[^12] scenario.

Typically if a wallet is compromised, the non-fungible assets within are at risk. With **NFKBTs** there are two different functions that can be called from a **Key Wallet**[^3] depending on which wallet has been compromised.

Scenario: **Holding Wallet**[^11] has been compromised, call `safeFallback`[^12].

`safeFallback`[^12]: This function was created in the event that the owner believes the **Holding Wallet**[^11] has been compromised. It can also be used if the owner losses access to the **Holding Wallet**[^11]. In this scenario, the user has the ability to call `safeFallback`[^12] from one of the **Key Wallets**[^3]. **NFKBTs** are then redirected from the **Holding Wallet**[^11] to the other **Key Wallet**[^3].

By redirecting the **NFKBTs** it prevents a single point of failure. If an attacker were to call `safeFallback`[^12] and the **NFKBTs** redirected to the **Key Wallet**[^3] that called the function, they would gain access to all the **NFKBTs**.

Scenario: **Key Wallet**[^3] has been compromised, call `resetBindings`[^13].

`resetBindings`[^13]: This function was created in the event that the owner believes `keyWallet1`[^7] or `keyWallet2`[^8] has been compromised. It can also be used if the owner losses access to one of the **Key Wallets**[^3]. In this instance, the user has the ability to call `resetBindings`[^13], removing the bound **Key Wallets**[^3] and resetting the security features. The **NFKBTs** will now function as a traditional _ERC-721_ until `addBindings`[^2] is called again and a new set of **Key Wallets**[^3] are added.

The reason why `keyWallet1`[^7] or `keyWallet2`[^8] are required to call the `resetBindings`[^13] function is because a **Holding Wallet**[^11] having the ability to call `resetBindings`[^13] could result in an immediate loss of **NFKBTs**. The attacker would only need to gain access to the **Holding Wallet**[^11] and call `resetBindings`[^13].

In the scenario that 2 of the 3 wallets have been compromised, there is nothing the owner of the **NFKBTs** can do if the attack is malicious. However, by allowing 1 wallet to be compromised, holders of non-fungible tokens built using the **NFKBT** standard are given a second chance, unlike other current standards.

The `allowTransfer`[^4] function is in place to guarantee a **Safe Transfer**[^6], but can also have **Default Values**[^22] set by a dApp to emulate **Default Bahviours**[^23] of a traditional _ERC-721_. It enables the user to highly specify the type of transfer they are about to conduct, whilst simultaneously allowing the user to unlock all the **NFKBTs** to anyone for an unlimited amount of time. The desired security is completely up to the user.

This function requires 4 parameters to be filled and different combinations of these result in different levels of security;

Parameter 1 `_tokenId`[^14]: This is the ID of the **NFKBT** that will be spent on a transfer.

Parameter 2 `_time`[^15]: The number of blocks the **NFKBT** can be transferred starting from the current block timestamp.

Parameter 3 `_address`[^16]: The destination the **NFKBT** will be sent to.

Parameter 4 `_allFunds`[^29]: This is a boolean value. When false, the `transfer` function takes into consideration Parameters 1, 2 and 3. If the value is true, the `transfer` function will revert to a **Default Behaviour**[^22], the same as a traditional _ERC-721_.

The `allowTransfer`[^4] function requires a **Key Wallet**[^2] and enables the **Holding Wallet**[^11] to conduct the transaction within the previously specified parameters. These parameters were added in order to provide additional security by limiting the **Holding Wallet**[^11] in the case it was compromised without the user's knowledge.

The `allowApproval`[^5] function provides extra security when allowing on-chain third parties to use your **NFKBTs** on your behalf. This is especially useful when a user is met with common malicious attacks e.g. draining dApp.

This function requires 2 parameters to be filled and different combinations of these result in different levels of security;

Parameter 1 `_time`[^30]: The number of blocks that the approval of a third-party service can take place, starting from the current block timestamp.

Parameter 2 `_numberOfTransfers_`[^31]: The number of transactions a third-party service can conduct on the user's behalf.

The `allowApproval`[^5] function requires a **Key Wallet**[^2] which enables the **Holding Wallet**[^11] to allow a third-party service by using the `approve` function. These parameters were added to provide extra security when granting permission to a third-party that uses assets on the user's behalf. Parameter 1, `_time`[^30], is a limitation to when the **Holding Wallet**[^11] can `approve` a third-party service. Parameter 2 ,`_numberOfTranfers`[^31], is a limitation to the number of transactions the approved third-party service can conduct on the user's behalf before revoking approval.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

---

[^2]: The `addBinding` function is used to add **Key Wallets**.
[^3]: The **Key Wallet/s** refer to `_keyWallet1` or `_keyWallet2` which can call the `safeFallback`, `resetBindings`, `allowTransfer` and `allowApproval` functions.
[^4]: The `allowTransfer` function allows requires a set of 4 parameters to be conducted. A specific `_tokenId`, `_time`, `_address`, and `_allFunds`.
[^5]: The `allowApproval` function requires a set of 2 parameters to be conducted. A specific amount of `_time` the **Holding Wallet** can `approve` and `_numberOfTransfers` a third-party can conduct on the user's behalf.
[^6]: A **Safe Transfer** is when 1 of the **Key Wallets** safely approved the use of the **NFKBTs**.
[^7]: The `keyWallet1` is 1 of the 2 **Key Wallets** set when calling the `addBinding` function.
[^8]: The `keyWallet2` is 1 of the 2 **Key Wallets** set when calling the `addBinding` function.
[^9]: The [_ERC-721_](./eip-721.md) is the token standard for creating smart contract-enabled non-fungible tokens to be used in the Ethereum ecosystem.
[^10]: Security known as _BIP-39_, defines how wallets create seed phrases and generate encryption keys.
[^11]: The **Holding Wallet** refers to the wallet containing the **NFKBTs**.
[^12]: The `safeFallback` function moves **NFKBTs** from the **Holding Wallet** to the **Key Wallet** that didn't call the `safeFallback` function.
[^13]: The `resetBindings` function resets the **Key Wallets** allowing the **Holding Wallet** to add new ones.
[^14]: The `_tokenId` represents the ID of the **NFKBT** intended to be spent.
[^15]: The `_time` in `allowTransfer` represents the number of blocks a `transferFrom` can take place in.
[^16]: The `_address` represents tha address that the **NFKBT** will be sent too.
[^18]: Security known as _Slip-39_, describes a way to securely back up a secret value using Shamir's Secret Sharing scheme. The secret value, called a Master Secret (MS) in SLIP-39 terminology, is first encrypted by a passphrase, producing an Encrypted Master Secret (EMS).
[^19]: The amount stolen in the USA provided by FTC reports.
[^22]: A **Default Behaviour/s** refers to bahaviour/s present in the preexisting non-fungible _ERC-721_ standard.
[^23]: A **Default Value/s** refer to a value/s that emulates the non-fungible _ERC-721_ **Default Behaviour/s**.
[^28]: A _PoS_ protocol, Proof-of-Stake protocol, is a cryptocurrency consensus mechanism for processing transactions and creating new blocks in a blockchain. Source - investopedia.com/terms/p/proof-stake-pos.asp website.
[^29]: The `_allFunds` is a bool that can be set to true or false.
[^30]: The `_time` in `allowApproval` represents the number of blocks an `approve` can take place in.
[^31]: The `_numberOfTransfers` is the number of transfers a third-party entity can conduct via `transferFrom` on the user's behalf.
