# The token standard

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is the standard a fungible token follows on Quantova, including a regulated stablecoin, and its reference implementation lives in the standard contract library.

A regulated issuer chooses a chain first on control and compliance, then on demand and liquidity, then on audits and security, and only then on fees. This standard gives the issuer, at the contract level, the controls the law requires, expressed with the native language primitives so they are proven by the compiler rather than hand rolled.

## The issuer authority

The issuer holds a quorum authority, a set of guardian keys with a threshold, using the module lattice or the hash based signature. Every issuer power is gated by this quorum, and every use of a power emits a permanent event, so the control is auditable on chain forever. The issuer authority is separate from chain governance. Chain governance does not run the token, and the token never touches consensus.

## The issuer powers

The standard defines these powers, each gated by the issuer quorum. Freeze and unfreeze of a named address, so the issuer can stop a specific account to meet law and sanctions. Clawback of the balance of a frozen address to a recovery destination, bound to evidence the same way as the justice protocol, so a seizure is scoped and recorded. Mint and burn of the token, so the issuer can manage supply, each with a permanent event. Pause of all transfers, so the issuer can stop the whole token in an emergency, with the pause recorded and reversible only by the quorum.

## Compliance hooks

The standard offers optional hooks that an issuer turns on, and the chain never imposes them. An allow list or a deny list gates who may hold or receive the token, for know your customer rules. A transfer restriction limits how the token moves. A sanctions freeze path ties a freeze to a named list. Every hook is checked with a post quantum signature, and an issuer that does not need a hook does not enable it.

## Reconciliation with governance and the mint ceiling

The issuer powers are contract level and act only on the issuer token. They are not chain governance and they never reach consensus or a consensus attestation, which stay module lattice only. The mint and burn here are of the issuer token and are not an issuance of the native asset, so the native mint ceiling in the constitution does not apply to them and is not touched by them. The issuer controls its own token supply under its own quorum, and the native asset supply stays under the monetary track and its ceiling.

## The reference contract

The standard contract library holds a reference stablecoin contract that an issuer adopts as a canonical template. It uses the language primitives directly, a quorum over the guardian set for every power, a signed by check for a claim, conserves on every balance flow, and the evidence pattern for a clawback. The reference contract is written once the language compiler reaches its milestone, and it is stubbed until then.
