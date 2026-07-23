# The governance specification

This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the QONCORD protocol crates and the QONCORD system contracts written in Quanta. It depends on the consensus specification for the certificate wrapper and on the prover for the STARK circuits.

The guiding rule for every governance surface is that no vote is above the law.

QONCORD is the governance protocol of Quantova. It runs parallel referendum tracks with stake weighted voting and delegation, and it rebuilds all of that on post quantum foundations. Every ballot is a lattice signature. Every tally is a STARK certificate that travels the same wrapper as consensus. Governance is bounded by a constitution that no track can cross.

## 1. The five tracks

Governance runs as five parallel tracks. Each track has its own deposit and its voting period, and every track passes on one and the same threshold. A proposal passes only when at least 40 percent of the total staked QTOV votes yes, which is 4000 basis points of the staked electorate, and the same 40 percent applies to every track including recovery. The genesis values are frozen starting points and change later only through the Chain upgrades track.

1.1 Chain upgrades. The most powerful track. It carries runtime upgrades and every root gated action, the fees, the governance configuration, and sensitive maintenance. It also carries every parameter change, a QIP, which is proposed and enacted here and on no other track. Its deposit is 2,250,000 QTOV, the highest on the chain. It runs fourteen days on a strong and deliberate schedule. The root authority of The LA super user exists only to bootstrap the testnet, it is handed to this track at mainnet so from then on no single key can act, and that key is never included when the stack is open sourced.

1.2 Mint QTOV. The only path that creates new QTOV after genesis. Its deposit is 4,000,000 QTOV. It resolves in three days, so newly raised capital or network need can be met quickly. No single key can mint.

1.3 Bridge pool migration. Moves the bridge pool to a new vault, a high value custody action. Its deposit is 1,500,000 QTOV. It runs five days. In an emergency the bridge is frozen first and the pool migrates under this vote inside the freeze window.

1.4 Freeze and asset recovery. Emergency consumer protection. Its deposit is 292,500 QTOV. It ratifies in about six hours. The freeze itself is instant and handled by the guardian caucus in section 4. This vote ratifies the clawback fast enough to catch a thief. The full amount returns to the address it was stolen from, and ordinary users are never affected.

1.5 Blacklist and kill address. Retires a compromised or hostile address. Its deposit is 390,000 QTOV. It runs about two days.

## 2. Voting

A voter's weight is their staked QTOV, with no multiplier, so one staked unit is one unit of weight and no lock can make a small stake vote larger than its size. Staked QTOV returns automatically to the same address when it is unbonded. Delegation is chosen per track, so a holder can delegate one track and vote directly on another.

A node validator's consensus bond is a separate pool and carries no vote. Governance weight comes only from stake locked for governance, never from a validator bond, and the two pools are tracked separately at the ledger level so one can never be counted as the other. A validator who wishes to vote does so as an ordinary staker with a separate governance lock.

## 3. Minting

Minting the native asset exists only through the Mint QTOV track. It is uncapped. There is no supply ceiling and no per year limit, and the amount minted is whatever the referendum enacts, so newly raised capital or network need can be met on demand through a high threshold public vote. No single key can mint. Every mint permanently records the referendum identifier and the STARK tally certificate.

## 4. Freeze and asset recovery

Recovery has two stages, and both are bound to a declared scope of exact addresses and amounts, so an action can never reach anything outside its scope.

4.1 The scope. A report names the asset, the address the funds were stolen from, and the addresses and amounts in play, committed as a hash with SHA 3, the Qudros digest. The scope hash locks the action, so enactment can never widen beyond it.

4.2 The instant freeze. A guardian caucus, a multisig and never one person, freezes exactly the listed addresses on the next block, within the hour, without waiting on a vote. A continuous on chain tracer follows the funds across every address they are moved or split into, however small, and freezes them within the same scope, so the thief cannot spend or escape. The freeze is locked to the scope and cannot widen, and it expires automatically unless the recovery referendum opens.

4.3 Ratify and return. The Freeze and asset recovery referendum of section 1.4 ratifies the clawback in about six hours. On enactment the full amount returns to the address it was stolen from. Ordinary users are never touched.

4.4 Protected accounts. Treasury and foundation addresses are protected and can never be frozen or clawed, so the power can never be turned on the chain's own funds.

## 5. The constitution, five invariants

Five invariants are enforced by the protocol, and no track can cross them, not even Chain upgrades.

First, no track may introduce classical or non approved cryptography, because the crypto policy outranks governance itself.

Second, recovery can never reach validator stake, consensus parameters, or governance locks.

Third, an emergency freeze pauses and never moves value on its own, and every freeze expires unless a referendum confirms it.

Fourth, freeze expiry, appeal windows, and scope locks are protocol invariants, so any referendum that violates them is unenactable, refused the way a malformed transaction is refused. Minting carries no ceiling invariant, because the mint is uncapped by decision.

Fifth, every enacted referendum permanently stores the proposal hash, the scope hash where the action is a recovery, the STARK tally certificate, and the enactment receipt.

## 6. The tally pipeline

Each ballot is a lattice signature over the referendum identifier and the choice. Ballots are aggregated once for each epoch. Each referendum then produces exactly one STARK certificate that proves the tally, built from the prover circuits and carried on the same certificate wrapper as consensus. No classical aggregation appears anywhere in this pipeline.

## 7. The enactment record

For every enacted referendum the protocol stores the proposal hash, the scope hash when the action is a recovery, the STARK tally certificate, and the enactment receipt. The record is permanent and committed with the rest of state.

## 8. Amendment

Frozen genesis parameters and the five invariants change only through the Chain upgrades track, and never in a way that violates the five invariants. All other tracks operate within these bounds.

## 9. Claims discipline

Say STARK proven tallies, scope bound recovery, machine enforced constitution, and no vote above the law. Never say capture proof, censorship proof, or unhackable.

## 10. Conformance

Hostile vectors are frozen in the Quantova Conformance repository. A recovery action that reaches validator stake, and a fund moving emergency freeze, must each be unenactable. The QONCORD constitution crate carries the matching negative tests, written before any feature they gate.
