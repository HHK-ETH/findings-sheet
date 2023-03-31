# findings sheet

## ERC20

- Check if works with fee/rebase token

- Check if uses safeTransfer (and if token without return true should be compatible)

- Check if token can exist or not before calling function

## ERC721

- Check if reetrancy

- Check if using safeTransfer

### Math

- Check precision loss, always multiply before dividing

- Check overflow/underflow
