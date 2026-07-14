# The name service

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The Quantova Name Service is the human layer of identity. A name is short and readable, and it resolves to a Q1 address. A name is a pointer in a registry, not a key, so a short name is safe here even though a short key is never safe. The three layers of identity are the name on top, the Q1 address beneath it, and the quantum keypair beneath that, as set out in the accounts specification.

## Names

A name uses the suffix q. A name reads as a label, then a dot, then q. A name resolves forward to a Q1 address, and an address resolves back to its primary name, which is reverse resolution. Every name has an owner, an expiry, and a renewal.

## The registry

The registry lives on chain. Every record is signed with ML DSA, and the whole resolution path, meaning the registry, the signatures, and the governance, is post quantum. This is the first name service whose entire path is post quantum. Do not claim that a name is unsquattable or unhackable. State only what is true.

## Pricing

The registration fee scales inversely with the length of the name, so a short name costs more. The pricing parameters are set by the standards process and can be changed through it.

## Disputes

A dispute over a name routes through the justice track of the governance protocol. A registration that impersonates or that is malicious is subject to evidence bound action, using the evidence bundle and the freeze and clawback powers described in the governance specification.

## Randomness

Where the name service needs fair allocation, meaning a lottery or an ordering for contested names, it draws randomness from the per user verifiable random function described in the verifiable random function specification. The name service imports that function by tag and does not reimplement it. This is specification only at this stage. The registry contract is written after the Quanta compiler reaches its parser milestone, and the randomness is wired in then.
