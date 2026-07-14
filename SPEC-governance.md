# The governance specification

This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the QONCORD protocol crates and the QONCORD system contracts written in Quanta. It depends on the consensus specification for the certificate wrapper, on the prover for the STARK circuits, and on the primitive catalog.

The guiding rule for every governance surface is that no vote is above the law.

QONCORD is the governance protocol of Quantova. It follows the familiar open governance layout of parallel referendum tracks, conviction voting, and delegation, and it rebuilds all of that on post quantum foundations. Every ballot is a lattice signature. Every tally is a STARK certificate that travels the same wrapper as consensus. Governance is bounded by a constitution that no track can cross.

## 1. The seven tracks

Governance runs as seven parallel tracks. Each track has its own deposit, decision period, enactment delay, and thresholds. The genesis values are frozen starting points and can be changed later only through the Constitution track.

1.1 Constitution. This track amends the constitution, the track parameters, and the node logic. Its deposit is 100 thousand QTOV, its decision period is 28 days, and its enactment delay is 14 days. It passes at 60 percent approval with support of at least 25 percent of all staked value. Even this track is bounded by the five invariants.

1.2 Crypto transition. This track adds or retires approved schemes and sets key rotation windows. Its deposit is 50 thousand QTOV, its decision period is 28 days, and its enactment delay is 30 days. It passes at 66 percent approval with 20 percent support. A proposal on this track must include an external cryptanalysis report or it is invalid. This is the only path that can change the algorithm set, and it can never introduce a classical primitive.

1.3 Monetary. This track is the only path that can mint the native asset, and it governs the fee burn and split. Its deposit is 50 thousand QTOV, its decision period is 21 days, and its enactment delay is 7 days. It passes at 66 percent approval with 20 percent support. Minting is capped by a hard ceiling described in section 3.

1.4 Treasury. This track spends from the treasury across small, medium, and large lanes with a sliding scale of deposit, decision, and enactment. Deposits range from 1 thousand to 25 thousand QTOV. This track cannot mint.

1.5 Justice. This track handles freeze extensions and clawback, and only on the basis of signed evidence. Its deposit is 25 thousand QTOV, its decision period is 14 days, and its enactment delay is 7 days followed by an appeal window. It passes at 75 percent approval with 25 percent support. Justice can never reach validator stake, consensus parameters, or governance locks.

1.6 Emergency. A guardian caucus of 7 of 11 can pause a contract, a corridor, or a module within hours. It never moves funds. Any pause expires automatically after 72 hours unless a referendum confirms it.

1.7 Standards. This track accepts or deprecates standards proposals and carries signaling votes. Its deposit is 500 QTOV, its decision period is 14 days, and it passes by simple majority.

## 2. Voting

Voters strengthen a vote by locking stake. The multiplier runs from one time with no lock up to six times with a lock of 32 weeks, where weight is stake times the conviction factor. Delegation is chosen for each track, so a holder can delegate one track and vote directly on another. Validators vote as ordinary stakers with no extra weight.

## 3. Monetary invariants

Minting the native asset exists only through the Monetary track. It is capped by a hard ceiling for each epoch, set at genesis so that cumulative minting stays at or below 2 percent per year. A referendum that would mint above the ceiling is not outvoted. It is unenactable, and the protocol refuses it the way it refuses a malformed transaction. Every mint records the referendum identifier and the tally certificate.

## 4. The Justice Protocol

Justice has two powers, and both are bound to an evidence bundle.

4.1 The evidence bundle. It is a hash committed record in canonical form. It holds exploit traces, signed victim attestations, and an explicit list of addresses and amounts. The bundle hash, taken with SHA 3, locks the scope of any action that cites it, so enactment can never reach an address or an amount outside the bundle.

4.2 Freeze, which is fast and reversible. A reporter posts a bond of 1 thousand QTOV, slashed if the report is frivolous, and files an evidence bundle. The guardian caucus of 7 of 11 enacts a temporary freeze on exactly the listed addresses. The freeze is locked to the bundle and cannot widen. It expires automatically after 72 hours unless a Justice referendum opens. Counter evidence can be filed at any time.

4.3 Clawback, which is slow and evidentiary. A Justice referendum opens by citing the bundle, runs for 14 days, and then holds a 7 day appeal window in which counter evidence triggers one further vote. On enactment the assets move to the recovery escrow contract, scoped strictly to the bundle, so anything outside the bundle stays untouched. Victims claim with signed proofs, and any unclaimed remainder moves to the insurance fund after 2 years.

## 5. The constitution

Five invariants are enforced by the protocol, and no track can cross them, not even the Constitution track. First, no track may introduce classical or non approved cryptography, because the crypto policy outranks governance itself. Second, Justice can never touch validator stake, consensus parameters, or governance locks. Third, Emergency pauses and never moves value, and every pause expires. Fourth, mint ceilings, freeze expiry, appeal windows, and scope locks are protocol invariants, so any referendum that violates them is unenactable. Fifth, every enacted referendum permanently stores the proposal hash, the evidence hash where the action is judicial, the STARK tally certificate, and the enactment receipt.

## 6. The tally pipeline

Each ballot is a lattice signature over the referendum identifier, the choice, and the conviction. Ballots are aggregated once for each epoch. Each referendum then produces exactly one STARK certificate that proves the tally, built from the prover circuits and carried on the same certificate wrapper as consensus. No classical aggregation appears anywhere in this pipeline.

## 7. The enactment record

For every enacted referendum the protocol stores the proposal hash, the evidence hash when the action is judicial, the STARK tally certificate, and the enactment receipt. The record is permanent and committed with the rest of state.

## 8. Amendment

Frozen genesis parameters and the five invariants change only through the Constitution track, and never in a way that violates the five invariants. All other tracks operate within these bounds.

## 9. Claims discipline

Say STARK proven tallies, evidence bound enactment, machine enforced constitution, and no vote above the law. Never say capture proof, censorship proof, or unhackable.

## 10. Conformance

Hostile vectors are frozen in the Quantova Conformance repository. An over ceiling mint, a Justice action that reaches validator stake, and a fund moving Emergency call must each be unenactable. The QONCORD constitution crate carries the matching negative tests, written before any feature they gate.
