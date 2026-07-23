# The privileged authority inventory

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It names every address and every power in the chain that is not an ordinary account, and it states plainly, for each one, whether it is a key someone holds or a keyless address that moves only through a passed governance vote. The guiding rule for every governance surface is that no vote is above the law, and this document exists so that anyone can check who or what actually holds power over the chain, not who is said to.

## The root authority named in the governance specification

The governance specification names a root authority, called the LA super user, whose stated purpose is to bootstrap the testnet before governance exists to do the job itself. As specified, that authority is scoped to testnet bootstrap only, it is handed to the Chain upgrades governance track at mainnet so that from then on no single key can act in its place, and it is never included when the stack is open sourced. As built today, no such key exists anywhere in the running chain. There is no hardcoded root address, no sudo path, and no admin key in the node's code, so the honest present state is that the chain runs today with no privileged single signer of any kind, testnet bootstrap included. If and when this authority is built for testnet bootstrap, its scope and its handover to the vote gated track at mainnet are the design to hold it to, and its absence from any open sourced release is a condition of building it at all.

## The keyless pots

Every reserved system account in the running chain, the grants account, the staking treasury, and the other reserved pots that receive slashed stake, forfeited bonds, or unclaimed allocations, is a keyless address. Each is derived as a hash of a fixed label rather than from any keypair, so there is no private key anywhere that can sign from one of them. Treasury and foundation addresses are additionally protected from an emergency freeze or a clawback, so the recovery power described below can never be turned on the chain's own funds.

Funds leave a keyless pot only through a governance action, and today that action rides the Mint QTOV track regardless of whether it mints new supply or spends from an existing pot, so moving grants or treasury funds needs the same high threshold public vote as minting, a substantial deposit, a strong approval share, and a meaningful turnout of staked value, resolved over a period measured in days rather than hours. No operator, no validator, and no single key can move a keyless pot on its own.

## Slashed and forfeited stake

Slashed stake and forfeited bonds go to the governance controlled treasury. They are never taken by the organization and never redistributed to other validators, since redistribution would pay the set for a peer's fault and invite the coordination the slashing exists to deter.

## The guardian caucus and the instant freeze

The governance specification describes a guardian caucus, a multisig of several people rather than one, empowered to freeze exactly the addresses named in a declared theft scope within the hour and without waiting on a vote, so a thief cannot move stolen funds while a recovery referendum is pending. The freeze is bound to its declared scope, it can never widen beyond the addresses and amounts named, and it expires automatically unless a recovery referendum confirms it within its window. The multisig mechanism itself exists in the governance code today, requiring at least two guardians to act together, but it is not seeded with any guardians in the genesis construction that produces the chain running today, so on the current devnet and testnet this path is empty and inert. It is a built but unused emergency power, not a live one, until a founder decision seeds a guardian set.

## The seven governance tracks

All chain level power beyond the two mechanisms above runs through one of seven parallel governance tracks, each with its own deposit, its own approval and support thresholds, and its own voting period, and each gated so that no track can cross the five constitutional invariants regardless of its own threshold. Chain upgrades is the most powerful track, carrying runtime upgrades and every root gated action including the parameters and the governance configuration itself. Mint QTOV is the only path that creates new QTOV after genesis and also the path that spends a keyless pot, and it is uncapped by decision, with no supply ceiling and no per year limit, so the amount any single referendum mints or spends is whatever that referendum enacts. Bridge pool migration moves the bridge pool to a new vault. Freeze and asset recovery ratifies a clawback already begun by the guardian caucus. Blacklist and kill address retires a compromised or hostile address. Add asset to bridge lists a new asset. Parameter change is the everyday tuning track for an ordinary chain parameter. Every referendum's tally is a lattice signed ballot aggregated into a single proof carried on the same certificate wrapper as consensus, so the record of who voted for what is permanent and machine checked rather than merely reported.

## Validator keys are not chain authority

A validator's own signing key, sortition key, and bond and reward account are generated and held by the validator's own operator, on the operator's own machine, and Quantova never holds or sees any of them. This is real key custody and it matters, but it is not privileged chain authority in the sense this document tracks, since a validator's key only ever acts for that validator's own bond and its own attestations, never for the chain as a whole or for another party's funds. A prover, similarly, holds a liveness bond and earns fees for work completed, but holds no vote and no authority over anything beyond the work it was paid to do.

## The bridge boundary

The Airlock, the boundary where the chain reads a foreign chain's own classical verification, is a place classical cryptography is tolerated, not a place privileged authority lives. It translates a foreign chain's result into a post quantum attestation and a proof, and the chain parses nothing else from across that boundary. It holds no power over Quantova's own consensus, its own accounts, or its own native asset, and a fault on a foreign chain is contained to the bridged asset it concerns.

## The honest summary

Today, with no LA super user built and the guardian caucus unseeded, every form of privileged power actually reachable in the running chain is either a keyless address spendable only by a passed public vote, or the emergency freeze design that exists in code but is not yet switched on. Nothing in the chain today can move another party's funds, mint new supply, upgrade the chain, or freeze an address by a single signature. Where that changes, whether by seeding a guardian set or by building the testnet bootstrap authority, the change itself is a founder decision, and this document is the place that decision gets recorded honestly once it is made.
