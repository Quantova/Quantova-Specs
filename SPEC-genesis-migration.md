# Snapshot genesis migration

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

Mainnet launches from a snapshot of the legacy chain, not from a code merge and not from a bridge to the legacy chain. The legacy chain is never merged.

## The snapshot

At an announced height on the legacy chain, the balances and the stakes are snapshotted. The snapshot is a fixed record, published so anyone can check it, and it is the sole input to the genesis state of the new chain.

## The claim

A holder claims the balance snapshotted for a legacy account by binding the old account to a new module lattice key. The claim is a message that proves control of the old account and names the new post quantum key, and once it is accepted the balance is credited to the new account. The proof of control is checked once at the Airlock and never again, so no classical verification enters ongoing operation.

## No legacy code and no legacy bridge

There is no code merge of legacy code into the new stack, and there is no standing bridge to the legacy chain. The migration is a one time snapshot and claim. After the claim window the legacy chain is done, and the new chain carries the balances forward under post quantum keys only.

## The window

The claim window is stated at genesis. Balances not claimed within the window follow the policy set at genesis, which is decided by governance.
