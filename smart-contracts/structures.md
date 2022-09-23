# Structures

## Rental offer

```solidity
struct RentalOffer {
    /// address of the user renting his NFTs
    address maker;
    /// address of the allowed tenant if private rental or `0x0` if public rental
    address taker;
    /// NFTs included in this rental offer
    NFT[] nfts;
    /// address of the ERC20 token for rental fees
    address feeToken;
    /// amount of the rental fee
    uint256 feeAmount;
    /// nonce
    uint256 nonce;
    /// until when the rental offer is valid
    uint256 deadline;
}

struct NFT {
    /// address of the contract of the NFT to rent
    address token;
    /// specific NFT to be rented
    uint256 tokenId;
    /// how long the rent should be
    uint64 duration;
    /// percentage of rewards for the lender, in basis points format
    uint16 basisPoints;
}
```

{% hint style="info" %}
Basis points are values between 0 and 10000 used as percentages.

For example the value 4219 means 42.19%
{% endhint %}

## Signature type

{% code overflow="wrap" %}
```solidity
enum SignatureType {
  PRE_SIGNED,
  EIP_712,
  EIP_1271
}
```
{% endcode %}

The kind of signatures used when advertising rental offers.

At the moment only `PRE_SIGNED` and `EIP_712` are supported.
