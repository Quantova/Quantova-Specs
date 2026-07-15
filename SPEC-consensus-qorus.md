# QORUS consensus

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by QRC CONSENSUS and it depends on the prover for the certificate proofs, the crypto crate for the signatures, and the random function for the sampling.

QORUS is the consensus of Quantova. It is a post quantum byzantine fault tolerant protocol that reaches sub second deterministic finality and produces one certificate rather than a list of votes, so consensus votes never consume block space. It activates in three stages so that the novel parts turn on only after external cryptanalysis.

## The three stages

Stage one is the byzantine fault tolerant core at genesis. It has 150 millisecond slots, deterministic leader rotation, attestations signed with the module lattice signature, and certificate aggregation done with a STARK rather than a pairing aggregate, since the pairing aggregate is broken by a quantum computer. Execution runs in parallel.

Stage two adds recursive succinctness to the certificate and opens the one tap validator in the application to the public.

Stage three adds the novel mechanisms, and each turns on only by a governance proposal after external cryptanalysis. These are the presence incentive, the verification receipt that binds every attestation to the transcript of the proof it checked so that rubber stamping is not possible, and the attestation ledger of chain managed one time hash keys.

## Validators and provers

A validator runs real server class hardware and the set is globally distributed. Validation is permissionless. Any operator anywhere may join the validator set without approval, there is no allowlist, no vetting, and no admission committee. Because identity carries no weight in a permissionless set, Sybil resistance comes from stake alone. Eligibility requires a minimum self stake, and the active set is capped. When more eligible stake seeks a slot than the cap allows, the active set is exactly the highest staked eligible operators ranked by stake, with ties broken deterministically by the operator identifier. There is no discretionary selection at any point, the rule alone decides who is active, and the marginal stake to enter is the lowest active stake, which floats with participation. The minimum self stake and the active set cap are economic parameters set alongside the yield in the economics specification.

A committee of about 500 is sampled per round from the active set by the verifiable random function seeded from the previous block beacon. Unbonding takes 14 days.

The slashing model is reopened by this amendment and is held for the founder decision, so it is not yet fixed. Under the earlier phone class design a validator was slashed only for equivocation, meaning signing two different blocks at one height, and an offline validator was skipped and never slashed, because a phone drops offline unpredictably. A permissionless server class set has no lever to eject a bad operator by revoking approval, so an economic penalty is the only enforcement, and unpunished unreliability becomes a liveness attack surface. A slashing model covering equivocation and sustained downtime is proposed in the economics specification and held, and neither the old rule nor a new one is implemented until the founder decides.

Users stake through the application without running hardware, which is how a holder participates with 2 thousand QTOV. Whether that stake delegates to a validator the holder chooses, which disperses power, or is pooled behind operators, which concentrates it, is a founder decision that materially changes the staking and the rewards, and it is held. A holder may run their own hardware, but the application path never requires it.

Heavy work, meaning execution, signature verification, and proving, is done by permissionless provers who earn fees and hold liveness bonds. A prover holds no vote and has no consensus power.

## A round

A round has a deterministic leader drawn from the committee. The leader proposes a block. The committee members verify it and attest with the module lattice signature. When a supermajority of two thirds plus one has attested, the attestations aggregate into a single certificate and the block is final. The certificate travels in the certificate slot of the block header, and its digest feeds the beacon for the next block. Finality is deterministic and sub second for the global validator set. The beacon derives from the aggregated certificate, not from any single validator, so no participant can bias leader election without controlling the supermajority.

## The resource budget

The maximum certificate proof size and the maximum certificate verify time are consensus parameters. Exceeding either needs a governance proposal, and the benchmark repository enforces them per release. They are set for a validator on real server class hardware, globally distributed, since the validator floor is server class and no longer a phone. The budget keeps the certificate cheap enough that a server class validator anywhere verifies it well inside a slot, while the heavy proving stays with the permissionless provers.

## Safety and liveness

The protocol is safe as long as fewer than one third of the committee is byzantine, meaning it never finalizes two conflicting blocks at one height. It is live as long as a supermajority is online, meaning it keeps finalizing. A deterministic simulator models these properties under fault injection before the protocol is built, and a formal model in a specification language checks safety and liveness before stage one activates.

## Consensus uses one signature scheme

Validator attestations and the aggregated finality certificate use ML DSA only. A user account may sign with any registered scheme, but that choice never reaches consensus. SLH DSA and FN DSA are never wired into a consensus attestation or the certificate. Mixing schemes in the hot path would complicate the prover and the certificate for no benefit and would put finality and throughput at risk. This law is fixed and is written in both this specification and the accounts specification.
