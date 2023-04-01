# findings sheet

### ERC20

- Check if works with fee/rebase token
- Check if uses safeTransfer (and if token without return true should be compatible)
- Check if token can exist or not before calling function
- Check if transfer always from msg.sender or could transfer from someone else that approved
- Check if different token decimals are possible and handled

### ERC721

- Check if reetrancy
- Check if using safeTransfer

### Math

- Check precision loss, always multiply before dividing
- Check overflow/underflow
- Check uint used in the calcul and convertions (*1 day is uint24*)

### ETH

- Check if reetrancy
- Check if msg.value is used in a loop
- Use Call to transfer ETH

### Oracle

- Check if oracle heartbeat allow arbitrage/mev
- Check if oracle can be changed atomically
- Check that doesn't rely on spot price and uses at least twap or chainlink oracle

### Swap

- Check minOut is valid and doesn't open MEV/arbitrage opportuniy

### Init

- Initialized and can't be initiliazed again

### Ownership

- admin functions protected
- 2 step ownership change

## Extra links

- https://github.com/immunefi-team/Web3-Security-Library#vulnerabilities
- https://github.com/OpenCoreCH/smart-contract-auditing-heuristics
