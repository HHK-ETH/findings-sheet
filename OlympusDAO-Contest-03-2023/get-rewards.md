## Summary

auraRewardPool().getReward doesn't use the right parameters.

## Vulnerability Detail

auraRewardPool().getReward in claimRewards uses the owner as account to check rewards for instead of the vault.
Thus this will never claim rewards for the vault.

Rewards can still be claimed by inputing claim = true when calling withdraw.

## Impact

Low

## Code Snippet

Vault
```
 /// @inheritdoc IBLVaultLido
    function claimRewards() external override onlyWhileActive onlyOwner nonReentrant {
        // Claim rewards from Aura
        auraRewardPool().getReward(owner(), true);

        // Send rewards to owner
        _sendRewards();
    }
```

AuraRewardPool
```
/**
     * @dev Gives a staker their rewards, with the option of claiming extra rewards
     * @param _account     Account for which to claim
     * @param _claimExtras Get the child rewards too?
     */
    function getReward(address _account, bool _claimExtras) public updateReward(_account) returns(bool){
        uint256 reward = earned(_account);
        if (reward > 0) {
            rewards[_account] = 0;
            rewardToken.safeTransfer(_account, reward);
            IDeposit(operator).rewardClaimed(pid, _account, reward);
            emit RewardPaid(_account, reward);
        }

        //also get rewards from linked rewards
        if(_claimExtras){
            for(uint i=0; i < extraRewards.length; i++){
                IRewards(extraRewards[i]).getReward(_account);
            }
        }
        return true;
    }
```

## Tool used

Manual Review

## Recommendation

Change owner() with address(this)

```
 auraRewardPool().getReward(address(this), true);
```
