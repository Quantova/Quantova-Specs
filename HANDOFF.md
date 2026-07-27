# The handoff

This is the master briefing for the Quantova build. Read it and the crypto policy at the start of every session. The order of authority is the crypto policy first, then this handoff, then the session instructions. If anything conflicts with the crypto policy, stop and report.

## What we are building

Quantova is a sovereign layer one blockchain in which post quantum security is a structural property, built entirely from scratch in Rust. We own the wire encoding, the peer protocol, the consensus named QORUS, the virtual machine named QVM, the contract language named Quanta, the interface descriptor named CID, the software development kits named QCore, the governance named QONCORD, the standards process named QSP, and the bridge named Q Bridge which runs through Q Oracle and the Airlock. There is no Substrate, no Ethereum virtual machine, no external peer library, and no inherited code. Classical cryptography is not rejected. It cannot be expressed. Only the NIST algorithms secure anything, as set out in the crypto policy.

Throughput is not published, in any form, anywhere, including here. What was once several separate internal working numbers, a measured single node figure, a modelled networked projection, and a design target, is retired from this document along with the rest, because a number kept only for internal use still leaks into judgement and still gets relayed. No performance figure appears in Quantova documentation until the finished stack is benchmarked end to end and the result is committed as a results file beside the code that produced it. Finality is deterministic and targeted at sub second for the global validator set, which is a property of the protocol design rather than a measured figure, and consensus votes never consume block space. The chain currently produces a block roughly every second in the running node, separately from the shorter internal slot granularity the consensus round uses inside that second. Validators run real server class hardware and the set is permissionless, with stake the only barrier and a deterministic stake ranked active set. A holder can stake 2 thousand QTOV in the application to participate without running hardware. The slashing model is decided, so an offline validator is no longer simply skipped, liveness is graduated and correlation scaled while attributable faults cost the whole bond. All heavy work, meaning execution, signature verification, and proving, is done by permissionless provers who earn fees and hold no votes.

## Decisions already made

Rust is used for all framework code, and software development kits in other languages are bindings built from the Rust core, never rewrites.

Consensus is QORUS, staged. Stage one is a post quantum byzantine fault tolerant core at genesis, with 150 millisecond slots, deterministic leader rotation, lattice attestations, STARK certificate aggregation in place of the quantum broken pairing aggregate, and parallel execution. Stage two adds recursive STARK succinctness and public one tap validators in the application. Stage three adds novel mechanisms that are activated only by governance after external cryptanalysis.

Validator economics are frozen in the economics specification. The minimum self stake is 2 thousand QTOV, a committee of about 500 is sampled per round by the one time key sortition, the bond locks for ninety days then unbonds over twenty one slashable days, rewards accrue per epoch for work and unlock on a rolling twelve month schedule, each validator's own session payout is held under a dollar denominated cap, the network level yield is otherwise emergent as the reward pool is drawn against total staked, and slashing is maximum for attributable faults and graduated for liveness. The delegation tier is honestly labeled and its model is a held founder decision. The prover market is permissionless with liveness bonds.

The validator resource budget is protocol law. The maximum certificate proof size and verify time are consensus parameters set for real server class hardware, and the validator bandwidth floor is one gigabit a second. Exceeding them needs a governance proposal, and the benchmarks repository enforces them per release.

The contract language is Quanta. It is familiar to Solidity developers but shares no lineage. Source files carry the Quanta source extension and compile to a bytecode container. Safety lives in the type system, with linear assets where copy or drop is a compile error, unforgeable signatures that only the machine can construct, declared reads and writes per entry, and message passing between contracts so the reentrancy class does not exist.

The interface descriptor is the CID, with SHA 3 selectors, embedded in every deployed container and fetchable on chain.

The virtual machine is the QVM, a custom register machine with 64 bit words, deterministic meter, and native post quantum opcodes. It is not the Ethereum virtual machine.

The peer layer is our own QUIC protocol with ML KEM key exchange and an ML DSA identity handshake, with no external peer library and no classical key exchange, including on development networks.

The bridge is Q Bridge, running through Q Oracle and the Airlock. Classical verification happens only off chain in the oracle. The chain parses exactly two foreign artifacts, a post quantum attestation in canonical form and a proof, and anything else is unparseable by construction. The Bitcoin corridor comes first and is near trustless. The Ethereum corridor is second and is trust minimized. Bridged assets are origin tagged, risk labeled in the wallet, and invalid as validator stake. The bridge is specified now but built after the public testnet. It never gates launch and never touches consensus.

Repositories start private and disclose in waves, as set out in the disclosure policy. Every repository carries the dual license. No repository merges while its checks are red. The legacy chain is never merged. Mainnet launches through a snapshot genesis migration, where balances and stakes are snapshotted at an announced height and claimed by binding old keys to new post quantum keys.

## Conventions

Crate names use the qtv prefix. Module and file names are lower case with underscores. Type names use upper camel case, and constants use upper case with underscores. All code is Rust in a workspace layout. Specifications and policies live only in the Quantova Specs repository, and code repositories link to them rather than copy them. Every repository root carries a readme, the two license files, a contributing guide, the deny file, and a continuous integration file. Test vectors are JSON under a vectors folder, named by area and case, and frozen in the Quantova Conformance repository.

Work goes to the repository that owns it and is never centralized into the chain repository. One session works on one repository and one step. No one pushes straight to main. Work goes on a feature branch, then a pull request, and merges only when the checks are green. Cross repository dependencies pin git tags, never branches. Every pull request names the specification section it implements. A missing specification is a gap. File it. Do not improvise.

## Build order

First, the specifications. Author the crypto policy, then the invariants, the codec, and the accounts, which unblock code, then the rest.

Second, the shared continuous integration and the deny list, proven by a scratch project that adds a banned crate and fails the build. Nothing in the crypto wave starts before that proof.

Third, the cryptography crate, in the order SHA 3, then ML DSA, then ML KEM, then SLH DSA, then the randomness function, with the NIST vectors green per module, the benchmarks committed, and the first release tagged.

Fourth, the chain library crates, each mirroring one specification, then the conformance freeze.

Fifth, the virtual machine and the primitive catalog, with fuzzing from the first week.

Sixth, the language, with the exploit suite that must fail to compile.

Seventh, the consensus, the prover, and the benchmarks in parallel, where the prover data comes before the consensus design is frozen, the simulator comes before the protocol, and the formal model comes before stage one.

Eighth, node integration and the development network. Ninth, the access layer. Tenth, the public testnet. Eleventh, the bridge, after the testnet. Twelfth, the mainnet genesis through the snapshot migration.

## Independence in four layers

The dependency layer fails on any banned crate anywhere in the tree. The symbol layer scans compiled binaries for classical signatures. The protocol layer keeps a reduction table pairing every crypto operation with its exact FIPS reduction. The negative test layer proves that classical artifacts at ingress come back as unparseable. Performance never justifies a classical shortcut. The only classical cryptography in the organization lives in Q Oracle, quarantined behind the Airlock, and nothing imports it.

## Escalation

Stop and report if an instruction conflicts with the crypto policy, if a task appears to need a banned dependency, if work belongs to a different repository than assigned, or if anything touches Q Oracle before the bridge wave. Flag to the founder any naming or trademark question, the legal review of staking and delegation and migration before the public testnet, and the external audit booking. The formal model of the consensus is scheduled work, not optional.
