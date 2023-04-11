# findings sheet

### ERC20

- Check if works with fee/rebase token
- Check if uses safeTransfer (and if token without return true should be compatible, see (https://github.com/zeroknots/brokentoken)
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

- Check if reetrancy (not only modifier but also balances update order and view function result change)
- Check if msg.value is used in a loop
- Use Call to transfer ETH

### Oracle

- Check if oracle heartbeat allow arbitrage/mev
- Check if oracle can be changed atomically
- Check that doesn't rely on spot price and uses at least twap or chainlink oracle
- Check if decimal price is correct (100$ could become 100k$)
- Check if oracle include a vault that can have value per share changed

### Swap

- Check minOut is valid and doesn't open MEV/arbitrage opportuniy
- Check for dust on zap (https://blog.alphaventuredao.io/onesideduniswap/)

### Proxy

- Initialized and can't be initiliazed again (implementation & proxy)
- Check variable order and state shadowing (storage collision & function clashing (check signatures))
- DelegateCall to selfDestruct/unexpected code
- DelegateCall returns true if target does not exist
- Many OpenZeppelin upgradeable contracts need to be initialized in the importing contract's constructor.

### Ownership

- admin functions protected
- 2 step ownership change
- Multisig over EOA

### Vault

- Protection against first deposit shares/donation attack (https://twitter.com/naddison/status/1640549868442902528)
- Doesn't assume share value being constant
- Pull Over Push -> user should be able to withdraw for themself

### External contracts

- Check return value (especially if address.call /!\ as could silently revert or not fully execute)
- Phantom function that might not revert
- Doesn't allow arbitrary call or if does make sure to not be msg.sender in these calls
- Assembly check on calldata/target could be bypassed (see abi-smuggling on damnVulnerableDefi)

### Signature

- Time or nonce limit
- erecover can return 0 if invalid signature

### Onchain Governance

- Time between proposed and execution
- Flashloan attack governance

### Dos

- Loop if iterate to many times
- address.call that check for success on a contract that can consumme more gas than expected

## Extra links

- https://github.com/immunefi-team/Web3-Security-Library#vulnerabilities
- https://github.com/OpenCoreCH/smart-contract-auditing-heuristics
- https://github.com/YAcademy-Residents/CommonWeb3SecurityIssues
