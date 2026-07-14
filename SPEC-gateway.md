# The Q Bridge gateway

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. This is built after the public testnet, it never gates launch, and it never touches consensus.

The bridge lets value move between Quantova and a foreign chain without letting a classical primitive into the stack. It runs through an off chain translation layer and the Airlock.

## The parts

The gateway is an on chain contract that mints and burns bridged assets. The translation layer is a set of off chain nodes that watch a foreign chain, run its classical verification, and produce a post quantum attestation with a proof of correct verification. The Airlock is the ingress grammar, so the chain parses exactly two foreign artifacts, a post quantum attestation in canonical form and a proof, and anything else has no valid form.

## Deposits and exits

A deposit locks value on the foreign chain, the translation layer attests to it, and the gateway mints the bridged asset once it checks the attestation and the proof against its registered attestor set. An exit burns the bridged asset and releases the value on the foreign chain through a threshold custody. The Bitcoin corridor comes first and is near trustless, since its verification is a proof of a foreign header chain inside a STARK. The Ethereum corridor is second and is trust minimized.

## Bridged assets

A bridged asset is origin tagged, so it records the foreign chain it came from, and it is risk labeled in the wallet. It is not valid as validator stake, so a fault on a foreign chain can never harm consensus.

## Containment

Classical cryptography stops at the Airlock. The translation layer is the only place in the whole organization where classical verification runs, nothing in the organization imports it, and the gateway enforces rate limits, exit delays, and a watchdog pause. A breach on the foreign side is contained to the bridged asset and never reaches consensus or the native asset.

## Claim discipline

Say near trustless Bitcoin deposits and trust minimized exits. Never say fully trustless bridge.
