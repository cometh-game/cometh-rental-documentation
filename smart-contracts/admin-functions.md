# Admin Functions

## Whitelist NFT for the rental protocol

```solidity
function associateOriginalToLentAndBorrowedNFT(
  address originalNFT,
  address lentNFT,
  address borrowedNFT,
  address subLentNFT
) external onlyRole(TOKENS_MANAGER_ROLE);
```

The rental protocol works by having an original NFT associated with `LentNFT`, `SubLentNFT` and `BorrowedNFT` ones.

In order to avoid spam, only a Cometh admin can whitelist such associations at the moment.

## Protocol Fees collector

{% code overflow="wrap" %}
```solidity
function setFeesCollector(address _feesCollector) public override onlyRole(FEES_MANAGER_ROLE)
```
{% endcode %}

Sets the address of the wallets collecting the rental protocol fees.

## Protocol Fees percentage

{% code overflow="wrap" %}
```solidity
function setFeesBasisPoints(uint16 basisPoints) public override onlyRole(FEES_MANAGER_ROLE)
```
{% endcode %}

Sets the percentage (in basis points) the rental protocol collects.

Such protocol fee is a percentage of the rental offer entry fee.
