## Summary

In vaultManager, mintOhmToVault() can mint more ohm than ohmLimit variable, which makes ohmLimit variable usage unclear.

## Vulnerability Detail

In the function mintOhmToVault() the circulatingOhmBurned is added to ohmLimit.

circulatingOhmBurned is updated in burnOhmFromVault() if the ohm burned is higher than ohm deployed. 

This means that if ohm price was to drop and users started withdrawing from the vault, the circulatingOhmBurned would increase and future users creating vaults and deploying ohm would be able to deploy more ohm than the fixed limit.

## Impact

Low/Informational

## Code Snippet

VaultManager
```solidity
 /// @inheritdoc IBLVaultManagerLido
    function mintOhmToVault(uint256 amount_) external override onlyWhileActive onlyVault {
        // Check that minting will not exceed limit
        if (deployedOhm + amount_ > ohmLimit + circulatingOhmBurned) //<-- HERE
            revert BLManagerLido_LimitViolation();

        deployedOhm += amount_;

        // Mint OHM
        MINTR.increaseMintApproval(address(this), amount_);
        MINTR.mintOhm(msg.sender, amount_);
    }

    /// @inheritdoc IBLVaultManagerLido
    function burnOhmFromVault(uint256 amount_) external override onlyWhileActive onlyVault {
        // Account for how much OHM has been deployed by the Vault system or burned from circulating supply.
        // If we are burning more OHM than has been deployed by the system we are removing previously
        // circulating OHM which should be tracked separately.
        if (amount_ > deployedOhm) {
            circulatingOhmBurned += amount_ - deployedOhm;
            deployedOhm = 0;
        } else {
            deployedOhm -= amount_;
        }

        // Burn OHM
        MINTR.burnOhm(msg.sender, amount_);
    }
```

## Tool used

Manual Review

## Recommendation

Remove circulatingOhmBurned from the check.

```solidity
        if (deployedOhm + amount_ > ohmLimit)
```
