# Quantova

Quantova is a sovereign post quantum Layer 1, built from scratch. It shares no code, no wire format, no address, no unit, and no trust assumption with any other chain. It is post quantum from the metal up, not a classical chain with a post quantum signature bolted on, and every layer is its own.

This repository, Quantova-Specs, holds the normative specifications and the crypto policy that the rest of the stack is built against. Under the disclosure policy the specifications go public first, so the fixed target can be read and checked before the implementations are.

## The one of one

The point that makes Quantova a one of one is not that it uses post quantum signatures. Many chains have added one. The point is that every layer is post quantum, on NIST standardized schemes alone, with no classical escape hatch anywhere. Classical public key cryptography is not deprecated and not merely unused. It is unrepresentable. Wire formats, opcodes, the type system, and every interface are shaped so a classical artifact has no valid encoding, and a parser that meets a classical signature, a classical handshake, or a pairing proof returns unparseable rather than falling back.

There is no elliptic curve anywhere in the stack. No ECDSA, no secp256k1, no Ed25519, no X25519, no RSA, no Diffie Hellman, no pairings, no BLS, no KZG, no ecrecover, and none can be built. Performance never buys a classical shortcut, because a pairing used only for aggregation or a small proof system used only to shrink proofs is exactly how other chains defeated their own quantum resistance claims. Certificate aggregation is a set of post quantum signatures a light client checks directly, and hashing carries a 256 bit width against Grover, so every security property in the stack reduces to a NIST post quantum algorithm and to nothing else.

## The crypto policy is the supreme law

POLICY-crypto is the highest authority in the stack and outranks every other document. If any instruction, specification, issue, or convenience conflicts with it, the work stops. It states the one rule that shapes everything else, which is that classical cryptography cannot be expressed, and it names the exact family that may secure anything. ML-KEM from FIPS 203 for key encapsulation, ML-DSA from FIPS 204 for signatures, SLH-DSA from FIPS 205 for hash based signatures, SHA-3 and SHAKE from FIPS 202 for hashing, and the 256 bit symmetric primitives for the network channel only. FN-DSA is admitted only once FIPS 206 is final and only through the transition track, and it stays behind a feature flag until then. Nothing else, ever.

The policy is enforced in four layers, and the enforcement runs on every change rather than at audit time.

- The dependency layer. A machine readable deny list bans the classical crypto crates from every repository, transitive and development dependencies included, and cargo deny fails the build on any of them.
- The symbol layer. The release gate scans compiled binaries for classical signatures such as curve constants, field primes, and modular exponentiation patterns, which catches vendored or hand written classical code that no dependency scan can see.
- The protocol layer. The reduction record pairs every cryptographic operation in the stack with its exact FIPS reduction, naming the operation, where it lives, the algorithm, the standard, the parameter set, and the source of its test vectors. An operation without a reduction blocks release.
- The negative test layer. Hostile conformance vectors, a classical signature at ingress, a classical handshake, a pairing proof at the gateway, must all come back unparseable, and this is asserted on every commit.

The single exemption is the Airlock, an off chain boundary that watches foreign chains, runs their classical verification, and translates the result into a post quantum attestation with a proof of correct verification. Classical cryptography stops there. Nothing in the stack imports it, the chain parses exactly two foreign artifacts, a post quantum attestation in canonical form and a proof, and anything else has no valid form.

## The stack, layer by layer

Every layer below is its own design, its own code, and its own vocabulary. Each names the specification it implements and pins its cross repository dependencies by git tag.

### Cryptography, Q-Crypto

Q-Crypto is the cryptographic floor and the only cryptography in the organization. Every signature the chain checks, every key exchange the transport runs, every committee draw the consensus samples, and every crypto opcode the machine executes calls into this one crate. It is a from scratch reference implementation of the NIST post quantum standards, written against the published FIPS documents and built on the Rust standard library alone. It pulls in no third party dependency, no RustCrypto, no OpenSSL, nothing, so what checks a signature on Quantova is code that can be read end to end, from the Keccak permutation up.

Each primitive is implemented in dependency order, and each one gates the next.

| Primitive | Standard | Category | Notes |
| --- | --- | --- | --- |
| SHA-3, SHAKE | FIPS 202 | | The Keccak sponge, implemented first because everything else builds on it |
| ML-DSA-65 | FIPS 204 | 3 | Primary signature, 1952 byte public key, 3309 byte signature |
| ML-KEM-768 | FIPS 203 | 3 | Transport key encapsulation, 1184 byte encapsulation key, 1088 byte ciphertext |
| SLH-DSA-SHAKE-192s | FIPS 205 | 3 | Conservative hash based signature, 48 byte public key, 16224 byte signature |
| Two post quantum VRFs | FIPS 205, FIPS 202 | 3 | One hash based on SLH-DSA, one lattice based on ML-DSA, on a shared interface |
| ChaCha20-Poly1305 | RFC 8439 | | The single 256 bit symmetric AEAD, transport only, never consensus or an account |

The signature and KEM schemes are checked end to end against the official NIST known answer tests committed in the repository, keygen and sign and verify for ML-DSA, keygen and encapsulation and decapsulation for ML-KEM, the SLH-DSA vectors, the SHA-3 and SHAKE known answers, and the RFC 8439 vectors for the AEAD, alongside rejection tests that a tampered ciphertext, tag, nonce, or key fails to open. The per operation benchmarks calibrate the gas schedule for the native crypto opcodes and the validator resource budget, so a gas number is measured rather than guessed. Q-Crypto is a reference implementation validated against the NIST test vectors. It has not been independently audited.

### Consensus, QORUS

QORUS is the consensus of Quantova. It is a committee based byzantine fault tolerant protocol that reaches sub second deterministic finality and produces one certificate rather than a list of votes, so consensus votes never consume block space. A block at a height is final when more than two thirds of the sampled committee attests to it, and once final it never reverts. Every attestation is a real ML-DSA-65 signature, and every hash is SHA-3 or SHAKE from the single crypto crate.

The committee is drawn by a one time key sortition that is grinding resistant by construction. Each bonded account commits, at registration and bound to its stake, the Merkle root of a tree of one time preimages, one leaf per slot. The draw for a slot is a deterministic SHAKE256 hash of the committed preimage, the beacon seed, and the slot, with no randomizer, so there is exactly one valid output per account per slot and there is nothing to re roll. A second draw costs a second full stake bond at slashing risk. Selection is stake weighted against a committee budget, so the work to finalize a block is bounded by the budget rather than growing with the validator set. The sampler tests assert this directly. A set grown a thousandfold, from five hundred to five hundred thousand accounts, finalizes with the same size committee. The beacon is a hash of the previous block's aggregated certificate, so no single validator can bias the draw without controlling the supermajority. Leadership is a stake neutral exponential race, proven neutral under stake splitting rather than sampled, so a validator cannot buy a better leadership chance by splitting one bond into many.

The finality certificate carries the ML-DSA attestations directly, and a light client verifies it from public inputs alone through a closed set of typed rejection reasons. This trades bandwidth for full soundness on purpose. A hash based folding certificate that would shrink the certificate is implemented and tested in the repository, but it is not on the finality path, and moving finality onto it is a deliberate protocol decision that has not been taken.

The stage one core has a machine checked TLA+ specification, and the state machine follows it transition for transition. TLC checks agreement, that no two conflicting blocks finalize at one height, along with validity, chain descent, that only byzantine validators are slashed, that offline validators are never slashed, and a temporal liveness property that a stabilized network finalizes. The recorded safety run explored 419,840 distinct states to depth 20 under an equivocating byzantine leader with every invariant holding, and the liveness run explored 3,368 states with the temporal property holding. Validation is permissionless, with stake as the only barrier and a deterministic stake ranked active set, and every validator re executes every block, so one honest re executor protects validity absolutely. A fully corrupt committee can halt the chain but it cannot lie to it.

### The virtual machine, the QVM

The QVM is the execution layer. It is a register machine that runs a compiled container, the machine's own unit of code. It is not the Ethereum virtual machine and it is not a WebAssembly runtime. The instruction set, the container format, the gas model, and the crypto opcodes are all its own.

The machine has sixteen general registers holding 64 bit words, a program counter, a bounded operand and call stack, and a linear scratch memory of 64 KiB that is zeroed at entry and sized to hold a full post quantum key, signature, or proof. There is no floating point anywhere. Arithmetic is checked, so an overflow is a fault and not a wrap unless the instruction is an explicit wrapping one, and a widening high word multiply forms a 128 bit product from two 64 bit words with no wider machine word. Every instruction has a fixed byte encoding and one deterministic gas cost, and on any fault, an overflow, a division by zero, a bad jump, a bad memory access, or running out of gas, the machine rolls back and records nothing. Only a clean halt surfaces state changes and effects.

Cryptography is a first class opcode group, and every primitive comes from Q-Crypto. HASH computes SHA3-256 over a memory region, VERIFY_ML verifies an ML-DSA-65 signature, VERIFY_SLH verifies an SLH-DSA signature, VRF_VERIFY checks a verifiable random output and its proof, KEM runs ML-KEM-768 encapsulation, and MERKLE_VERIFY checks a SHA3-256 authentication path. There is no classical verify opcode and none can be added, because no classical primitive exists in the machine. Signature verification reads the one byte scheme identifier from the envelope and dispatches to the matching verify instruction, and the gas for each is taken from the measured cost of that scheme.

The contract address model is the full thirty two byte address, and storage is keyed by a full slot, so an owner check binds all thirty two bytes and a token balance is full strength rather than truncated. A contract is a container that holds the code, a constant pool, a state access manifest that lists the reads and writes of each entry, and the interface descriptor of entries and events, each named by a four byte selector that is the leading bytes of the SHA3 hash of its canonical signature string. The container identifier is the SHA3-256 hash of a canonical length prefixed serialization of the whole container, so two distinct containers never share an identifier, and the declared read and write sets are what let the node schedule non overlapping contract calls in parallel. Every digest the machine surfaces is rendered in the Q identifier format, never in Ethereum hex.

### The language, Quanta

Quanta is the smart contract language. A contract is written in a source file with the extension qs and compiles to a QVM container. It is familiar to a developer who has written Solidity and shares no lineage with it. The vocabulary is its own, entry not function, guard not require, genesis not constructor, caller not the sender field, and the lexer refuses foreign identifiers such as function, require, msg, mapping, uint256, ecrecover, pragma, wei, and ether outright, so source shaped like another chain's language does not even tokenize.

The defining property of Quanta is that whole classes of exploit are compile errors rather than runtime hazards guarded by a check. A contract that could carry one does not produce a container, because the shape that carries the exploit is not a valid program. Six classes fail to compile, each for its own reason.

- Reentrancy has no expression. The language has no synchronous external call, so control cannot leave an entry and reenter it. The only outward transfer is send, which is terminal and returns nothing.
- Unchecked overflow is rejected at the type level. Checked arithmetic is the default, and on top of that a bare addition of unbounded external input into a stored integer does not compile. The author bounds the result with a limits clause or writes the arithmetic as checked or wrapping.
- Forged authorization cannot be assembled. Authority over an entry comes only from a value written signed by a party, produced by a real machine verification, so gating on a self declared parameter field does not compile.
- Infinite mint is refused by conservation. Only an entry that declares mints or burns may change supply, and the conserves clause runs a linear flow analysis that proves asset in equals asset out plus explicit fees on every path.
- Dropped and double spent value are ruled out by linearity. An asset value must be used exactly once. Consuming it twice copies it and leaving it unused drops it, and both are compile errors.
- Front running is closed by the sealed rule. A competitive order pooled for later settlement must be declared sealed, so it travels under ML-KEM encapsulation and cannot be read in the mempool and outbid. An unsealed competitive order that gates on its own amount is rejected.

The static checker runs a fixed sequence of passes, resolve, types, linear, signature, conserve, access, and sealed, and returns the first violation with its line and column. The repository carries a corpus of six exploit contracts, one per class, each valid syntax and each rejected for its own reason, with a test asserting that not one compiles clean. The compiler is a Rust workspace of a lexer, a syntax tree, a parser, the type checker and its passes, a code generator that lowers to QVM bytecode, an emit crate that produces one JSON document carrying the container bytes and the interface, a command line front end, and a browser compiler crate that builds to WebAssembly and runs the whole compiler in the user's page with no backend, so the container the editor shows is byte for byte the container the command line produces and the machine runs. The code generator has no path to a classical opcode, and a test classifies every machine opcode with an exhaustive match that has no classical arm.

The compiler is the sole path from source to bytecode, and that is a hard precondition rather than a note. Any deploy path added to the chain must refuse bytecode that did not come from the compiler, or add the missing checks to the machine, because the machine itself has no concept of an asset or of linearity and would run a value minting program without complaint.

### Contract security

Authority in a contract is not a field a contract fills in. It is a value the machine alone produces by verifying a signature, and the binding is done in the lowering so it is enforced by the machine rather than trusted.

An owner gated entry, a parameter written signed by an owner, is bound three ways before the body runs. The signer address derived from the presented public key must equal the stored owner, so a stranger's own valid signature is refused. The signed message commits to this contract and this entry, so an order cannot be lifted onto another action. And the message carries a per signer nonce that the entry reads and increments, so a captured order cannot be replayed.

A quorum, a parameter of type Quorum of M of N over a stored guardian set, is constructed by the machine only from M valid signatures, each by a distinct guardian of the set. Each member's address is derived from its presented public key, each signed message commits to the contract, the entry, the member, a per member nonce, and the order fields, and the derived member addresses must be strictly increasing by guardian index, so the M members are M distinct guardians and a captured quorum cannot be replayed. A member may sign with ML-DSA or SLH-DSA, and the machine reads the scheme identifier and dispatches accordingly.

### The primitive catalog

Q-Primitives is the small set of Q types that the language and the machine share. Each is defined so that a value of it can only be produced in the one sanctioned way, which is what turns a class of bugs into something that cannot be written. Q_Address names an account or a contract and renders in the Q1 format. Q_Sig binds a signed message to its signer and exists only through a real verification. Q_Asset carries an amount of a declared kind and is linear, and an origin tagged asset bridged from a foreign chain is never valid as validator stake. Q_Commit binds a hidden value to a public SHA3 digest. Q_Rand is a verified output of the verifiable random function. Q_Sealed is confidential in the mempool under ML-KEM and opens only at execution. Q_Key holds a public key with its scheme identifier byte, and the secret half is never a value inside a contract.

### Identifiers, money, and the wire

Identity has three layers. A name in the Quantova Name Service is the short human readable top, ending in the suffix q, and the whole resolution path is post quantum. The name resolves to a Q1 address, which is the on chain identifier. The address is derived from a post quantum keypair that stays hidden beneath it.

A Q1 address is the Bech32m encoding of a 256 bit payload under the human readable part q, and because the character after the human readable part is the natural separator, every address reads as Q1 by construction. The payload is the SHA3-256 hash of the scheme identifier followed by the full post quantum public key, so the address binds a real key at full width. It is about sixty characters, checksummed, and error detecting. There is one width and no shorter tier, because a short key is a broken key, and the floor is permanent. A Q1 address is never a twenty byte hex string and never an SS58 string.

A secret export uses the human readable part Q2 and encodes the 32 byte seed as Bech32m, also expressible as a 24 word mnemonic from a Quantova derivation over SHAKE256, not BIP39 and not BIP32, with no secp256k1 derivation path anywhere. The Q2 part is the secret namespace and never labels anything safe to publish, so a block hash, a transaction hash, and a state root read qbk1, qtx1, and qst1, never Q2. A string that reads Q2 is always a secret.

The asset is QTOV. It pays fees, it is staked by validators, and it is the unit of the treasury. Its base unit is the Quon, and one QTOV is one million Quon. The testnet asset is TQTOV. The genesis supply is fixed at 4,571,429 QTOV, and issuance runs only through a governed mint path. A fee is targeted in United States dollars, in a band from five hundredths to one tenth of a cent, and charged in QTOV, but the hard runtime cap is a native ceiling in base units set at genesis and independent of any price, because a live price feed in the transaction hot path is a manipulation and a liveness risk and is prohibited. Part of every fee is burned and the rest funds the validator reward pool and the grants pool.

The wire is the stack's own. The transaction encoding is a canonical codec in which every value has exactly one valid form and the decoder rejects anything else, no trailing bytes, no overlong length, no unknown tag. It is not RLP and it is not SCALE. A transaction is a wrapper around a body that holds the sender, a nonce that orders a sender and stops replay, the gas limit, the fee, and the call, with the scheme identifier and the signature over the domain tagged body hash carried outside the body. The peer transport is a post quantum authenticated channel over a reliable byte stream. The handshake exchanges ML-DSA identities, encapsulates an ephemeral ML-KEM key, and signs the transcript with ML-DSA, then a SHAKE256 schedule derives the directional ChaCha20-Poly1305 record keys, with no X25519 and no classical key exchange even on a development network.

The access gateway is a plain HTTP endpoint with a custom JSON codec written on the standard library. A method is an HTTP POST to a path under a version prefix carrying a flat JSON body, and the node stays the single owner of state while the gateway forwards typed requests to it. Every method is Q native, among them node_info, head, validators, chain_params, get_account, get_transaction, submit_transaction, get_block, supply, get_container, get_storage, and get_events. There is no eth_ method anywhere, it is not Ethereum JSON-RPC, and it is not a Substrate WebSocket.

### The SDK, the QCore family

A client does four things it has to get exactly right. It derives a post quantum key, it builds a transaction, it signs that transaction, and it speaks the gateway wire. The QCore family does all four in one place. QCore.rs is a single Rust core, QCore.js is that core compiled to WebAssembly, and QCore.py is that core built as a native extension. The signing and the wire are written once in Rust and never rewritten by hand in another language, and all three derive the same address from the same seed and sign the same body to the same bytes, because they are the one core rather than three copies of it. A second implementation of the signing would be a second chance to be wrong with a user's money, so there is only ever the one.

## What this repository holds

The specifications are normative, and each one sits under the crypto policy. Alongside them this repository holds POLICY-crypto, the supreme law, POLICY-disclosure, which governs the order in which repositories go public, and the reduction record, which pairs every crypto operation with its FIPS reduction. Together the specifications cover the stack end to end.

- Foundations. SPEC-accounts defines the Q1 address, the Q2 secret, the 256 bit security floor, and the key pipeline. SPEC-codec defines the canonical encoding. SPEC-transactions, SPEC-blocks, and SPEC-state define the ledger and the sparse Merkle state. SPEC-invariants carries the threat model, the unrepresentable rule, and scheme agility.
- Execution. SPEC-isa defines the QVM instruction set, SPEC-container the bytecode container, SPEC-cid the interface descriptor, and SPEC-lowering how Quanta lowers to bytecode. SPEC-quanta-language defines the language and SPEC-primitives the shared Q types. SPEC-token-standard defines the token and its issuer controls.
- Consensus and randomness. SPEC-consensus-qorus defines QORUS. SPEC-sortition-onetime defines the one time key sortition and SPEC-vrf the verifiable random function. Two draft documents carry the post quantum consensus and VRF work that turns on only after external cryptanalysis.
- Network and services. SPEC-p2p defines the peer transport and its erasure coded availability layer, SPEC-mempool the mempool, SPEC-rpc the access interface, SPEC-gateway the Q Bridge and its Airlock, and SPEC-qns the name service.
- Economics and governance. SPEC-economics defines the asset, the supply, the fee flow, and the validator and staking rules. SPEC-governance defines QONCORD, its seven referendum tracks, the conviction voting, and the constitution that no vote can cross. SPEC-genesis-migration and SPEC-recovery define snapshot genesis and key recovery.

## Reading order

Start with POLICY-crypto, then read the specification for the area you are working in. Every other repository in the stack names the specification section it implements and pins its cross repository dependencies by tag.

## Status and honesty

Quantova is at the testnet stage. The quantovad daemon runs the chain, native transfers, staking, and governance are live, and contract execution is metered under a per block compute budget and an admission ceiling. Every signature and key exchange that secures the chain is NIST post quantum, ML-DSA-65 for accounts and finality, ML-KEM-768 for key exchange, SLH-DSA as the hash based alternative, and SHA-3 throughout, and there is no elliptic curve or other classical public key cryptography anywhere in the consensus, the accounts, or the virtual machine, so nothing that secures the chain is exposed to Shor's algorithm. The one place classical cryptography is touched at all is the bridge, which has to verify a foreign chain's own signatures to read it, and that verification is isolated in the Airlock and never secures Quantova itself. The cryptography is a from scratch reference implementation validated against the official NIST test vectors and not yet independently audited, and independent audits of the cryptography, the virtual machine, the language, and the consensus are planned before the public testnet and required before mainnet. The documents hold back any figure that is not measured, including any throughput number, and the cross chain bridge is trust minimized, near trustless on the Bitcoin corridor through a foreign header proof carried inside a STARK, rather than fully trustless.

## License

Dual licensed under Apache 2.0 and MIT. See LICENSE-APACHE and LICENSE-MIT.
