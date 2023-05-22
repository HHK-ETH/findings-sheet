# findings sheet

### ERC20

- Check if works with fee/rebase token
- Check if uses safeTransfer (and if token without return true should be compatible, see (https://github.com/zeroknots/brokentoken)
- Check if token can exist or not before calling function
- Check if transfer always from msg.sender or could transfer from someone else that approved
- Check if different token decimals are possible and handled
- Can contract end up requiring a 0 amount of ERC20 transfer which would revert for some ERC20
- safeApprove allows only to approve from zero

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
- Read-only reentrancy for curve virtual price (on ETH pools)

### Swap

- Check minOut is valid and doesn't open MEV/arbitrage opportuniy
- Check for dust on zap (https://blog.alphaventuredao.io/onesideduniswap/)
- https://dacian.me/defi-slippage-attacks

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
- An address can create multiple different signatures for the same message (https://github.com/0xbok/ecdsa-vuln-poc/tree/master/ch2_not_unique)
- https://github.com/0xbok/ecdsa-vuln-poc

### Onchain Governance

- Time between proposed and execution
- Flashloan attack governance

### Dos

- Loop if iterate too many times
- address.call that check for success on a contract that can consume more gas than expected

### Other

- Can a reorg (happens often on polygon or bnb) result in loss of funds
- If fork of a famous protocol check this list: https://github.com/YAcademy-Residents/defi-fork-bugs

### Multichain deployment

- Token order might change for Uniswap Lps (token0 & token1) and all other contracts that determine variable order by address

### Chain specific

- ZKsync era doesn't support address.transfer

### Lending

- https://dacian.me/lending-borrowing-defi-attacks

## Extra links

- https://github.com/immunefi-team/Web3-Security-Library#vulnerabilities
- https://github.com/OpenCoreCH/smart-contract-auditing-heuristics
- https://github.com/YAcademy-Residents/CommonWeb3SecurityIssues
- https://proxies.yacademy.dev/
