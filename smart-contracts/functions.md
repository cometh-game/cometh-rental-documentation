# Functions

## Create rental offer

{% code overflow="wrap" %}
```solidity
/**
 * @notice Create a new on-chain rental offer.
 * @notice In order to create a private offer, specify the `taker` address, otherwise use the `0x0` address
 * @dev When using pre-signed order, pass `SignatureType.PRE_SIGNED` as the `signatureType` for `rent`
 * @param offer the rental offer to store on-chain
 */
function preSignRentalOffer(RentalOffer calldata offer) external;
```
{% endcode %}

This function advertises new rental offers.

{% hint style="warning" %}
At this stage, only onchain offers are supported by the protocol but offchain alternatives are planned for the future.
{% endhint %}

## Cancel rental offer

```solidity
/**
 * @notice Cancel an on-chain rental offer.
 * @param nonce the nonce of the rental offer to cancel
 */
function cancelRentalOffer(uint256 nonce) external;
```

A previously advertised offer can be cancelled by summited its `nonce` to this function.

## Begin rental(s)

```solidity
/**
 * @notice Start a rental between the `offer.maker` and `offer.taker`.
 * @param offer the rental offer
 * @param signatureType the signature type
 * @param signature optional signature when using `SignatureType.EIP_712` or `SignatureType.EIP_1271`
 * @dev `SignatureType.EIP_1271` is not yet supported, call will revert
 */
function rent(
    RentalOffer calldata offer,
    SignatureType signatureType,
    bytes calldata signature
) external;
```

In order to accept a rental offer, this function has to be called with the details of the offer and the `signatureType` (only `PRE_SIGNED` and `EIP_712` signature types are supported at this stage).

`signature` is only needed when using the signature type `EIP_712`.

{% hint style="info" %}
A rental offer can be a bundle of more than 1 NFT.

Each NFT in the rental offer is a rental by itself when the offer is accepted.

Said differently a rental offer of 3 spaceships will start 3 rentals when the offer is accepted through this function.
{% endhint %}

## End rental

```solidity
/**
 * @notice End a rental when its duration is over.
 * @dev A rental can only be ended by the lender or the tenant.
 *      If there is a sublet it will be automatically ended.
 * @param token address of the contract of the NFT rented
 * @param tokenId tokenId of the rented NFT
 */
function endRental(address token, uint256 tokenId) external;
```

A rental can only be ended when its duration is complete.

{% hint style="info" %}
If a rental has ended but still has ongoing sublet, the lender can still terminate the rent and automatically end the sublet through this function.
{% endhint %}

## End rental prematurely

```solidity
/**
 * @notice End a rental *before* its duration is over.
 *         Doing so need both the lender and the tenant to call this function.
 * @dev If there is an ongoing sublet the call will revert.
 * @param token address of the contract of the NFT rented
 * @param tokenId tokenId of the rented NFT
 */
function endRentalPrematurely(address token, uint256 tokenId) external;
```

When the first party (either lender or tenant) makes this call, a blockchain event is emitted and indexed in order to send notifications to the other involved party.

The other party can then either ignore it, or accept it by making the same call.

{% hint style="info" %}
Agreement to end rental prematurely has to be done in a P2P manner.
{% endhint %}

## Sublet

<pre class="language-solidity"><code class="lang-solidity">/**
 * @notice Sublet a rental.
 * @dev Only a single sublet depth is allowed.
<strong> * @param token address of the contract of the NFT rented
</strong> * @param tokenId tokenId of the rented NFT
 * @param subtenant address of whom the sublet is done for
 * @param basisPoints how many basis points the tenant keeps
 */
 function sublet(
     address token,
     uint256 tokenId,
     address subtenant,
     uint16 basisPoints
 ) external;</code></pre>

Sublet a rental to a designated party (`subtenant`).

A sublet is completely controlled by the tenant.

## End sublet

```solidity
/**
 * @notice End a sublet. Can be called by the tenant / sublender at any time.
 * @param token address of the contract of the NFT rented
 * @param tokenId tokenId of the rented NFT
 */
function endSublet(address token, uint256 tokenId) external;
```

## Fees table

```solidity
/**
 * Fees table for a given `token` and `tokenId`.
 *
 * `pencentage` is not based on the rewards to be distributed, but the what these
 * specific users keeps for themselves.
 * If lender keeps 30% and tenant keeps 20%, the 20% are 20% of the remaining 70%.
 * This is stored as `3000` and `2000` and maths should be done accordingly at
 * rewarding time.
 *
 * @param token address of the contract of the NFT rented
 * @param tokenId tokenId of the rented NFT
 * @return fees table
 */
function getFeesTable(address token, uint256 tokenId) external view
  returns (Fee[] memory);
```

Fees tables are used in order to expose how many rewards (percentages in basis points) are kept by each involved party.

In practice, Cometh relies on two levels: one for the lender, one for the tenant (if there is a sublet).\
The last party involved (either the tenant if no sublet or the subtenant if sublet) keeps the rest of the rewards.
