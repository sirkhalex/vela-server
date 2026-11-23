# XLM reserve policy

Vela uses XLM as the native fee and reserve asset on Stellar. A wallet needs
enough XLM to satisfy the network's minimum-balance requirement and pay the
transaction fee before it can send XLM or an issued asset such as USDC.

## Assumptions

- The server and development seed target Stellar testnet unless configuration
  explicitly selects another network.
- The current network base reserve and transaction fee are protocol values, not
  application constants. They can change through Stellar protocol upgrades.
- A wallet's minimum balance depends on its base reserve and the number of
  ledger entries/subentries owned by the account. Trustlines and other entries
  therefore increase the required reserve.
- A payment amount is not a reserve calculation. The client must keep the
  reserve and fee buffer visible when presenting a spendable balance.

## Runtime behavior

The server loads the source account from the configured Horizon network before
building a transaction. It does not claim that a balance is spendable merely
because the account exists, and it must not use a testnet balance or issuer on
mainnet. The configured network passphrase and USDC issuer are validated at
startup, while Horizon remains authoritative for the account's current
balances and ledger state.

Before a live transfer, clients should:

1. Query the wallet on the configured network.
2. Confirm the native XLM balance covers the current minimum reserve, the fee,
   and the intended payment amount.
3. Confirm an issued-asset trustline exists when sending USDC.
4. Re-check the account immediately before signing because another operation
   can change the balance or reserve requirement.

Do not hard-code a reserve amount in the API contract or test fixtures. Use
testnet accounts and fake identifiers in local/CI checks, and independently
verify a live testnet transaction in Horizon. This document is an operational
assumption guide, not a live-balance guarantee.
