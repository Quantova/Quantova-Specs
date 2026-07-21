# Quantova Specs

Quantova-Specs holds the specifications and the crypto policy for Quantova. It is the fixed target the rest of the stack implements against, and under the disclosure policy it is the first part of Quantova to go public, so the design can be read and checked before the implementations are.

Quantova is a sovereign post quantum Layer 1 built from scratch, sharing no code, no wire, and no trust assumption with any other chain. It is post quantum end to end and not a classical chain with a post quantum signature bolted on, built on NIST standardized schemes alone with no classical escape hatch anywhere. Consensus is QORUS, the virtual machine is the QVM running compiled containers, addresses are q1 bech32m, and the asset is QTOV with its base unit the Quon and TQTOV on the testnet.

## The crypto policy is the supreme law

POLICY-crypto is the highest authority in the stack and outranks every other document. It states the one rule that shapes everything else, which is that classical cryptography is not deprecated but impossible to express. Only ML KEM from FIPS 203, ML DSA from FIPS 204, SLH DSA from FIPS 205, and SHA3 and SHAKE from FIPS 202 secure anything, with the symmetric primitives and hash based STARKs alongside them, and no elliptic curve, pairing, or classical recovery exists anywhere. The policy sets out the four layers that enforce this, the dependency ban, the compiled symbol scan, the reduction table, and the hostile negative tests, and it names the single exemption, the Airlock boundary that nothing else imports. POLICY-disclosure governs the order in which repositories go public.

## The reduction table

The reduction record pairs every cryptographic operation in the stack with its exact reduction to a NIST standard, naming the operation, where it lives, the algorithm, the standard that defines it, the parameter set, and the source of its test vectors. An operation without a reduction blocks release. This is the protocol layer of the crypto policy's enforcement.

## The specifications

The specifications are normative and each one sits under the crypto policy. Together they cover the stack end to end.

- Foundations. SPEC-accounts defines the q1 address, the 256 bit security floor, and the key pipeline. SPEC-codec defines the canonical encoding. SPEC-transactions, SPEC-blocks, and SPEC-state define the ledger. SPEC-invariants carries the threat model and scheme agility.
- Execution. SPEC-isa defines the QVM instruction set. SPEC-container defines the bytecode container, SPEC-cid the interface descriptor, and SPEC-lowering how Quanta lowers to bytecode. SPEC-quanta-language and SPEC-primitives define the language and the shared primitive types. SPEC-token-standard defines the token.
- Consensus and randomness. SPEC-consensus-qorus defines QORUS. SPEC-vrf and SPEC-sortition-onetime define the verifiable random function and the committee sortition. Two draft documents carry the post quantum consensus and VRF work that turns on only after external cryptanalysis.
- Network and services. SPEC-p2p defines the peer transport, SPEC-mempool the mempool and data availability, SPEC-rpc the access interface, SPEC-gateway the Q Bridge gateway, and SPEC-qns the name service.
- Economics and governance. SPEC-economics defines the asset, the supply, the fee, and the staking rewards. SPEC-governance defines QONCORD. SPEC-genesis-migration and SPEC-recovery define snapshot genesis and key recovery.

## Reading order

Start with POLICY-crypto, then read the specification for the area you are working in. Every other repository in the stack names the specification section it implements and pins its cross repository dependencies by tag.

## Status and honesty

Quantova is at the testnet stage. The specifications describe a from scratch design whose cryptography is a reference implementation validated against the NIST test vectors and not independently audited. The documents state what each part is and does and hold back any figure that is not measured. Nothing here is audited, unbreakable, or production secure.

## License

Dual licensed under Apache 2.0 and MIT.
