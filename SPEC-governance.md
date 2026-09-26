# The governance specification

This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the QONCORD protocol crates and the QONCORD system contracts written in Quanta. It depends on the consensus specification for the certificate wrapper.

The guiding rule for every governance surface is that no vote is above the law.

QONCORD is the governance protocol of Quantova. It runs parallel referendum tracks with stake weighted conviction voting, and it rebuilds all of that on post quantum foundations. Every ballot is a lattice signature. Every tally is committed on chain from the counted lattice ballots. Governance is bounded by a constitution that no track can cross.

## 1. The five tracks

Governance runs as five parallel tracks. Each track has its own deposit, voting period, enactment delay, and pass threshold. A proposal passes only when its aye weight reaches the track threshold of the whole staked electorate, 66.67 percent for Chain upgrades, Mint QTOV, and Bridge pool migration and 75 percent for Freeze and asset recovery and Blacklist and kill address, when turnout reaches at least 25 percent of that electorate, and when aye outweighs nay. The genesis values are frozen starting points and change later only through the Chain upgrades track.

1.1 Chain upgrades. The most powerful track. It carries runtime upgrades and every root gated action, the fees, the governance configuration, and sensitive maintenance. It also carries every parameter change, a QIP, which is proposed and enacted here and on no other track. Its deposit is 225,000 QTOV. It runs fourteen days on a strong and deliberate schedule, then waits a seven day enactment delay. The root authority of The LA super user exists only to bootstrap the testnet, it is handed to this track at mainnet so from then on no single key can act, and that key is never included when the stack is open sourced.

1.2 Mint QTOV. The only path that creates new QTOV after genesis. Its deposit is 400,000 QTOV, the highest on the chain. It resolves in three days and enacts after a seven day delay, so newly raised capital or network need can be met quickly. No single key can mint.

1.3 Bridge pool migration. Moves the bridge pool to a new vault, a high value custody action. Its deposit is 150,000 QTOV. It runs five days, then waits a seven day enactment delay. It also carries bridge asset registration, epoch advance, operator revocation, and committee rotation. In an emergency the bridge is frozen first and the pool migrates under this vote inside the freeze window.

1.4 Freeze and asset recovery. Emergency consumer protection. Its deposit is 29,250 QTOV. It ratifies in six hours, then waits a one hour enactment delay. The freeze itself is instant and handled by the guardian caucus in section 4. This vote ratifies the clawback fast enough to catch a thief. The full amount returns to the address it was stolen from, and ordinary users are never affected.

1.5 Blacklist and kill address. Retires a compromised or hostile address. Its deposit is 39,000 QTOV. It runs two days, then waits a one day enactment delay. Account freezes and unfreezes and the governance lift of a bridge freeze ride this track as well.

## 2. Voting

A voter's weight is the QTOV they lock for the vote, up to the size of their bonded stake, multiplied by a conviction factor, one times for a one month lock, one and a half times for a one year lock, and two and a half times for a two year lock, so no stake can ever weigh more than two and a half times its size. Locked QTOV returns to the same address when the vote lock ends. There is no vote delegation.

A node validator's consensus bond and a governance lock are separate pools, tracked separately at the ledger level so one can never be counted as the other. The lock is what votes and the bond sets its ceiling, so no voter can lock more for a vote than the stake they have bonded.

## 3. Minting

Minting the native asset exists only through the Mint QTOV track. It is capped by a yearly ceiling of two percent of the total supply, and never less than 100,000 QTOV, and a referendum that would mint above it is unenactable, so newly raised capital or network need can be met through a high threshold public vote without unbounded dilution. No single key can mint. Every mint permanently records the referendum identifier and the committed tally.

## 4. Freeze and asset recovery

Recovery has two stages, and both are bound to a declared scope of exact addresses and amounts, so an action can never reach anything outside its scope.

4.1 The scope. A report names the asset, the address the funds were stolen from, and the addresses and amounts in play, committed as a hash with SHA 3, the Qudros digest. The scope hash locks the action, so enactment can never widen beyond it.

4.2 The instant freeze. A guardian caucus, a multisig and never one person, freezes exactly the listed addresses on the next block, within the hour, without waiting on a vote. A continuous on chain tracer follows the funds across every address they are moved or split into, however small, and freezes them within the same scope, so the thief cannot spend or escape. The freeze is locked to the scope and cannot widen, and it expires automatically unless the recovery referendum opens.

4.3 Ratify and return. The Freeze and asset recovery referendum of section 1.4 ratifies the clawback in about six hours. On enactment the full amount returns to the victim address named in the scope. The clawback takes from a frozen holder's free balance first, then from its validator bond, then from its governance vote lock, so stolen funds moved into staking are pulled back the same way. A frozen validator also drops out of the consensus roster the block its freeze lands, so stolen stake can never produce or finalize blocks, while the holder can still vote so a freeze can never silence the electorate. Ordinary users are never touched.

4.4 Protected accounts. Treasury and foundation addresses are protected and can never be frozen or clawed, so the power can never be turned on the chain's own funds.

## 5. The constitution, five invariants

Five invariants are enforced by the protocol, and no track can cross them, not even Chain upgrades.

First, no track may introduce classical or non approved cryptography, because the crypto policy outranks governance itself.

Second, recovery can reach validator stake and governance locks only when their holder is frozen, stays inside its scope, and can never touch a protected network pot or consensus parameters.

Third, an emergency freeze pauses and never moves value on its own, and every freeze expires unless a referendum confirms it.

Fourth, the yearly mint ceiling, freeze expiry, appeal windows, and scope locks are protocol invariants, so any referendum that violates them is unenactable, refused the way a malformed transaction is refused.

Fifth, every enacted referendum permanently stores the proposal hash, the scope hash where the action is a recovery, the committed tally, and the enactment receipt.

## 6. The tally pipeline

Each ballot is a lattice signature over the referendum identifier and the choice. Ballots are aggregated once for each epoch. Each referendum then commits its tally on chain from the counted lattice ballots, and that committed tally is the permanent record. No classical aggregation appears anywhere in this pipeline.

## 7. The enactment record

For every enacted referendum the protocol stores the proposal hash, the scope hash when the action is a recovery, the committed tally, and the enactment receipt. The record is permanent and committed with the rest of state.

## 8. Amendment

Frozen genesis parameters and the five invariants change only through the Chain upgrades track, and never in a way that violates the five invariants. All other tracks operate within these bounds.

## 9. Claims discipline

Say lattice signed ballots and an on chain committed tally, scope bound recovery, machine enforced constitution, and no vote above the law. Never say capture proof, censorship proof, or unhackable.

## 10. Conformance

Hostile vectors are frozen in the Quantova Conformance repository. A recovery action that reaches the stake of a holder that was never frozen, and a fund moving emergency freeze, must each be unenactable. The QONCORD constitution crate carries the matching negative tests, written before any feature they gate.
