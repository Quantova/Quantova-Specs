# QORUS consensus

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by QRC CONSENSUS and it depends on the prover for the certificate proofs, the crypto crate for the signatures, and the random function for the sampling.

QORUS is the consensus of Quantova. It is a post quantum byzantine fault tolerant protocol that reaches sub second deterministic finality and produces one certificate rather than a list of votes, so consensus votes never consume block space. It activates in three stages so that the novel parts turn on only after external cryptanalysis.

## The three stages

Stage one is the byzantine fault tolerant core at genesis. It has 150 millisecond slots, deterministic leader rotation, attestations signed with the machine lattice signature, and certificate aggregation done with a STARK rather than a pairing aggregate, since the pairing aggregate is broken by a quantum computer. Execution runs in parallel.

Stage two adds recursive succinctness to the certificate and opens the one tap validator in the application to the public.

Stage three adds the novel mechanisms, and each turns on only by a governance proposal after external cryptanalysis. These are the presence incentive, the verification receipt that binds every attestation to the transcript of the proof it checked so that rubber stamping is not possible, and the attestation ledger of chain managed one time hash keys.

## Validators and provers

A validator is phone class. It signs up, stakes 2 thousand QTOV in the application, and does verification only work. A committee of about 500 is sampled per round by a hash based function seeded from the previous block beacon. A validator is slashed only for equivocation, meaning signing two different blocks at one height. An offline validator is skipped and is never slashed. Unbonding takes 14 days. There is an honestly labeled delegation tier.

All heavy work, meaning execution, signature verification, and proving, is done by permissionless provers who earn fees and hold liveness bonds. A prover holds no vote and has no consensus power.

## A round

A round has a deterministic leader drawn from the committee. The leader proposes a block. The committee members verify it and attest with the machine lattice signature. When a supermajority of two thirds plus one has attested, the attestations aggregate into a single certificate and the block is final. The certificate travels in the certificate slot of the block header, and its digest feeds the beacon for the next block. Finality is deterministic and sub second for the global validator set. The beacon derives from the aggregated certificate, not from any single validator, so no participant can bias leader election without controlling the supermajority.

## The resource budget

The maximum proof size and the maximum verify time on a reference phone from the year 2020 are consensus parameters. Exceeding either needs a governance proposal, and the benchmark repository enforces them per release. This keeps a validator phone class forever.

## Safety and liveness

The protocol is safe as long as fewer than one third of the committee is byzantine, meaning it never finalizes two conflicting blocks at one height. It is live as long as a supermajority is online, meaning it keeps finalizing. A deterministic simulator models these properties under fault injection before the protocol is built, and a formal model in a specification language checks safety and liveness before stage one activates.

## Consensus uses one signature scheme

Validator attestations and the aggregated finality certificate use ML DSA only. A user account may sign with any registered scheme, but that choice never reaches consensus. SLH DSA and FN DSA are never wired into a consensus attestation or the certificate. Mixing schemes in the hot path would complicate the prover and the certificate for no benefit and would put finality and throughput at risk. This law is fixed and is written in both this specification and the accounts specification.
