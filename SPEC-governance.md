# SPEC-governance — QONCORD Constitutional Governance

**Status:** Normative (Draft v0.1). **Precedence:** subordinate to POLICY-crypto.md; any conflict →
STOP AND REPORT. **Implemented by:** `QONCORD` (runtime crates) and its Quanta system contracts.
**Depends on:** SPEC-consensus-qorus (certificate wrapper), q-prover (STARK circuits), SPEC-primitives.

Tagline for all governance surfaces: **no vote above the law.**

QONCORD is Quantova's governance protocol. It inherits the OpenGov layout (parallel referendum
tracks, conviction voting, per-track delegation) and rebuilds it post-quantum from first principles.
Every ballot is an ML-DSA signature; every tally is a STARK certificate traveling the same wrapper as
consensus. Governance is bounded by a machine-enforced constitution that no track can cross.

## 1. Referendum tracks

Seven tracks run in parallel. Genesis parameters are FROZEN starting values; they are amendable later
only via the Constitution track (§1.1) itself. Deposit is the proposal bond; Decision is the voting
period; Enact is the delay from passing to execution.

| # | Track | Powers | Deposit | Decision | Enact | Approval | Support |
|---|-------|--------|---------|----------|-------|----------|---------|
| 1 | Constitution | amend constitution, track params, runtime upgrades | 100k QTOV | 28d | 14d | ≥60% | ≥25% of all staked |
| 2 | Crypto Transition | add/retire NIST schemes, key-rotation windows | 50k | 28d | 30d | ≥66% | ≥20% |
| 3 | Monetary | mint native QTOV, fee burn/split | 50k | 21d | 7d | ≥66% | ≥20% |
| 4 | Treasury | spend across small/medium/large lanes | 1k–25k | 7–21d | 2–7d | sliding per lane | sliding per lane |
| 5 | Justice | freeze extensions and clawback, evidence-bound only | 25k | 14d | 7d + appeal | ≥75% | ≥25% |
| 6 | Emergency | pause a contract, corridor, or module; NEVER moves funds | guardian caucus 7-of-11 | hours | immediate | caucus | auto-expires 72h unless confirmed |
| 7 | Standards (QSP) | accept/deprecate QSPs, signaling | 500 | 14d | — | simple majority | — |

### 1.1 Track 1 — Constitution
Amends the constitution, track parameters, and runtime upgrades. Highest deposit and the only track
that may change frozen parameters or the invariants of §6, and even it is bounded by §6 (the five
invariants hold against Track 1).

### 1.2 Track 2 — Crypto Transition
Adds or retires NIST schemes and sets key-rotation windows. A proposal here MUST include an external
cryptanalysis report; without it the proposal is invalid. This is the only path to change the
algorithm set and it can never introduce a non-NIST primitive (§6.1).

### 1.3 Track 3 — Monetary
The ONLY mint path (§4). Governs native QTOV minting and the fee burn/split. Bounded by the hard
supply invariants of §4.

### 1.4 Track 4 — Treasury
Spends from the treasury across small, medium, and large lanes with a sliding deposit, decision, and
enactment curve per lane. Cannot mint (minting is Track 3 only).

### 1.5 Track 5 — Justice
Evidence-bound freeze extensions and clawback only (§5). Bounded by the governance and consensus
firewall (§6.2): Justice can never touch validator stake, consensus parameters, or governance locks.

### 1.6 Track 6 — Emergency
A guardian caucus of 7-of-11 may pause a contract, corridor, or module. It NEVER moves value (§6.3).
Any pause auto-expires after 72 hours unless confirmed by a referendum.

### 1.7 Track 7 — Standards (QSP)
Accepts or deprecates QSPs and carries signaling votes. Simple majority; no enactment delay.

## 2. Voting mechanics

### 2.1 Conviction voting
A voter multiplies vote weight by committing to a lock: 1× with no lock up to 6× with a 32-week lock.
Weight equals stake multiplied by the conviction factor.

### 2.2 Delegation
Delegation is per-track: a voter may delegate voting on one track and vote directly on another.

### 2.3 Validator neutrality
Validators vote as ordinary stakers with zero extra weight. There is no validator voting bonus.

## 3. Monetary invariants

1. **Single mint path.** Minting native QTOV exists ONLY via Track 3.
2. **Per-epoch ceiling.** Minting is capped by a hard per-epoch ceiling. Genesis value: cumulative
   minting ≤2% per year.
3. **Unenactable over-ceiling.** A referendum that would mint above the ceiling is not outvoted — it
   is UNENACTABLE. The runtime refuses it the way it refuses a malformed transaction.
4. **Mint record.** Every mint emits an event carrying the referendum identifier and the tally
   certificate.

## 4. Justice Protocol

Two asymmetric powers, both cryptographically scoped to an Evidence Bundle.

### 4.1 Evidence Bundle
Canonical-codec, hash-committed. Contents: hash-committed exploit traces, ML-DSA-signed victim
attestations, and an explicit list of addresses and amounts. The bundle hash is SHA3-256 over the
canonical encoding and is the scope-lock for any enactment that cites it. Enactment can never reach an
address or amount outside the bundle.

### 4.2 FREEZE (fast, reversible)
- **F1.** A bonded reporter (1k QTOV, slashed if frivolous) files an Evidence Bundle.
- **F2.** The guardian caucus 7-of-11 enacts a TEMPORARY freeze on exactly the listed addresses.
- **F3.** The freeze is scope-locked to the bundle hash and cannot widen.
- **F4.** It auto-expires after 72 hours unless a Justice referendum opens.
- **F5.** Counter-bundles are allowed at any time.

### 4.3 CLAWBACK (slow, evidentiary)
- **C1.** A Justice referendum opens citing the bundle.
- **C2.** A 14-day decision period at ≥75% approval and ≥25% support.
- **C3.** A 7-day appeal window; a counter-bundle triggers exactly one re-vote.
- **C4.** On enactment, assets move to the Recovery Escrow contract, scoped strictly to the bundle
  addresses and amounts; anything outside the bundle is untouchable.
- **C5.** Victims claim with ML-DSA-signed proofs. Unclaimed residue moves to the insurance fund after
  2 years.

## 5. The Constitution — five machine-enforced invariants

No track may cross these, not even Track 1.

1. **No classical cryptography.** No track may introduce non-NIST or classical cryptography.
   POLICY-crypto.md outranks governance itself.
2. **Governance and consensus firewall.** Justice can NEVER touch validator stake, consensus
   parameters, or governance locks.
3. **Emergency never moves value.** Emergency pauses, never moves value; every pause auto-expires.
4. **Runtime invariants are unenactable if violated.** Mint ceilings, freeze expiry, appeal windows,
   and scope-locks are runtime invariants; a violating referendum is unenactable.
5. **Permanent record.** Every enacted referendum permanently stores the proposal hash, the evidence
   hash (if judicial), the STARK tally certificate, and the enactment receipt.

## 6. Tally pipeline

Each ballot is an ML-DSA signature over the referendum identifier, the choice, and the conviction.
Ballots are aggregated per epoch. Each referendum produces exactly one STARK certificate proving the
tally, built from the q-prover circuits and traveling the same certificate wrapper as consensus
(SPEC-consensus-qorus, SPEC-blocks). No classical aggregation (no BLS) appears anywhere in the tally.

## 7. Enactment record schema

For every enacted referendum the runtime stores: proposal hash, evidence hash (present only for
judicial actions), STARK tally certificate, and enactment receipt. The record is permanent and
Merkle-committed with the rest of state.

## 8. Amendment

Frozen genesis parameters and the invariants of §5 change only through the Constitution track (§1.1),
and never in a way that violates §5. All other tracks operate within these bounds.

## 9. Claims discipline

Exact: "STARK-proven tallies", "evidence-bound enactment", "machine-enforced constitution",
"no vote above the law". Never: "capture-proof", "censorship-proof", "unhackable".

## 10. Conformance

Hostile vectors are frozen in Quantova-Conformance: an over-ceiling mint referendum, a Justice action
reaching validator stake, and a fund-moving Emergency call MUST each be unenactable. The QONCORD
`qtv-constitution` crate carries the matching negative tests, written before any feature they gate.
