# findings sheet

## ERC20

- Check if works with fee/rebase token
- Check if uses safeTransfer (and if token without return true should be compatible)
- Check if token can exist or not before calling function
- Check if transfer always from msg.sender or could transfer from someone else that approved

## ERC721

- Check if reetrancy
- Check if using safeTransfer

### Math

- Check precision loss, always multiply before dividing
- Check overflow/underflow

### ETH

- Check if reetrancy
- Check if msg.value is used in a loop
- 
