# findings sheet

### ERC20

- Check if works with fee/rebase token
- Check if uses safeTransfer (and if token without return true should be compatible, see (https://github.com/zeroknots/brokentoken)
- Check if token can exist or not before calling function (especially with solmate safetransfer)
- Check if transfer always from msg.sender or could transfer from someone else that approved
- Check if different token decimals are possible and handled
- Can contract end up requiring a 0 amount of ERC20 transfer which would revert for some ERC20 (see: https://github.com/d-xo/weird-erc20)
- safeApprove allows only to approve from zero
- Main tokens like weth or btc on xdai have a callback
- erc777 reentrancy
- Weth has a phantom permit (or any other function call wouldn't revert because of the fallback, see: https://media.dedaub.com/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f)

### ERC721

- Check if reetrancy
- Check if using safeTransfer
- Check if compatible to receive an NFT

### Math

- Check precision loss, always multiply before dividing
- Check overflow/underflow
- Check uint used in the calcul and convertions (*1 day is uint24*)

### ETH

- Check if reetrancy (not only modifier but also balances update order and view function result change)
- Check if msg.value is used in a loop
- Use Call to transfer ETH
- `msg.value` will be passed on in a `delegateCall`, extra careful if in a loop (https://matink.me/solidity/2023/10/01/multicall-in-solidity/)

### Oracle

- Check if oracle heartbeat allow arbitrage/mev
- Check if oracle can be changed atomically
- Check that doesn't rely on spot price and uses at least twap or chainlink oracle
- Check if decimal price is correct (100$ could become 100k$)
- Check if oracle include a vault that can have value per share changed
- Read-only reentrancy for curve virtual price (on ETH pools)
- additional checks: https://medium.com/cyfrin/chainlink-oracle-defi-attacks-93b6cb6541bf#99af

### AMMs

- Check fees calculation (fees can vary between AMMs and pools)
- Check minOut is valid and doesn't open MEV/arbitrage opportuniy
- Check for dust on zap (https://blog.alphaventuredao.io/onesideduniswap/)
- https://dacian.me/defi-slippage-attacks
- block.timestamp as expiry not recommended (ex: https://github.com/code-423n4/2023-08-dopex/blob/main/bot-report.md#m02-using-blocktimestamp-as-the-deadlineexpiry-invites-mev) more of a low/info

### Proxy

- Initialized and can't be initiliazed again (implementation & proxy)
- Check variable order and state shadowing (storage collision & function clashing (check signatures))
- DelegateCall to selfDestruct/unexpected code
- DelegateCall returns true if target does not exist
- Many OpenZeppelin upgradeable contracts need to be initialized in the importing contract's constructor.
- Solady create3 deploys 2 contracts, msg.sender during deployment is the middle man contract and not the initial caller of the deploy3 function

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
- Try/catch won't work if the method doesn't exist on the call receiver, the whole call will revert. [More here](https://ethereum.stackexchange.com/questions/129150/solidity-try-catch-call-to-external-non-existent-address-method)

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
- One elements of the loop fail and makes the whole transaction fail ([Asymmetry safeth](https://github.com/code-423n4/2023-03-asymmetry-findings/issues/703))
- address.call that check for success on a contract that can consume more gas than expected
- Gas griefing in general, can one make this function consumes excessive amount of gas ?

### Other

- Can a reorg (happens often on polygon or bnb) result in loss of funds (invalid on sherlock)
- If sequencer is down how can it affect the protocol ? (invalid in contests unless chainlink price feeds used)
- If fork of a famous protocol check this list: https://github.com/YAcademy-Residents/defi-fork-bugs
- Minting amount can depends of external factors that requires it to be treated like a swap (deadline & slippage check)
- frontrunning and time sensitive function. anything can potentially be frontrun. Can one submit a TX or change Data right before a different TX and get more out of it ? can someone submit data and then change it by submitting a tx right before the admin tx get accepted making the admin validate invalid data? Can the admin submit a tx believing the contract is in state X but when his transaction get accepted it's in state Y, thus affecting his tx or someone else tx that wasn't supposed to be mined before his.
- Double check bitmaps, error in indexing can result in med or high vulnerabilites.
- Similar to above can an array or mapping be altered or broken with invalid entries

### Multichain deployment

- Token order might change for Uniswap Lps (token0 & token1) and all other contracts that determine variable order by address
- https://github.com/0xJuancito/multichain-auditor

### Chain specific

- ZKsync era doesn't support address.transfer
- https://era.zksync.io/docs/dev/building-on-zksync/contracts/differences-with-ethereum.html#evm-instructions

### Lending

- https://dacian.me/lending-borrowing-defi-attacks

## Extra links

- https://github.com/immunefi-team/Web3-Security-Library#vulnerabilities
- https://github.com/OpenCoreCH/smart-contract-auditing-heuristics
- https://github.com/YAcademy-Residents/CommonWeb3SecurityIssues
- https://proxies.yacademy.dev/
- https://github.com/crytic/not-so-smart-contracts
