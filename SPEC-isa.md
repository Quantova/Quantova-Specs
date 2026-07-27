# The virtual machine instruction set

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The Quantova virtual machine is a register machine. It is not the Ethereum virtual machine. It executes the bytecode container produced by the language compiler. Every execution is deterministic, so the same code and the same inputs produce the same result and the same meter cost on every node.

## The machine model

The machine has a fixed set of general registers, each holding a 64 bit word. It has a program counter, a bounded operand and call stack, a linear scratch memory that is zeroed at entry, and access to contract state through the declared reads and writes of the entry. There is no floating point anywhere. Arithmetic is checked, so an overflow is a fault and not a wrap, unless the instruction is an explicit wrapping instruction.

## Instruction groups

Instructions fall into groups. There are arithmetic and logic instructions over 64 bit words. The arithmetic includes checked forms that fault on overflow, wrapping forms that take the modular result, and a widening high word multiply that returns the high 64 bits of a 64 by 64 product, so a 128 bit product is formed from two 64 bit words without any wider machine word. There are control instructions for branches, calls, and returns within a contract. There are storage instructions that read and write the declared state. There are message instructions that enqueue an asynchronous message to another contract, since there is no synchronous call that returns control in the middle of execution. There is an event instruction that records a typed event. And there are the native post quantum cryptographic instructions described below.

## Events

A contract records a typed event with the event instruction. The instruction names a payload region in scratch memory by its offset and byte length and carries the event's interface selector, and the machine records the event as an effect the host appends to the block event trie after a clean halt. Like a native transfer the event is a recorded effect and never a direct state change, so a fault records nothing and only a clean halt surfaces the events, in emission order. The event root over the recorded events is committed in the block header, so a light client verifies an event against the header without holding the full state, as the block specification describes.

## The cryptographic instructions

The machine exposes the post quantum operations as native instructions so a contract never rolls its own cryptography. There is a verification instruction for module lattice signatures. There is a verification instruction for hash based signatures. There is a hashing instruction over SHA3. There is a Merkle proof verification instruction. There is a random function verification instruction that checks a verifiable random output and its proof. And there is a key encapsulation instruction. There is no instruction that verifies a classical signature, and none can be added, because no classical primitive exists in the machine.

The key encapsulation instruction seals a value at submission. Opening a sealed value at execution needs two further instructions the machine does not yet expose, a key decapsulation that recovers the shared secret and an authenticated symmetric decryption under a 256 bit symmetric primitive. These instructions, together with the model for which key opens a sealed value and at what point in sequencing it opens, are specified and added in the sequencing wave, because the opening is coupled to how the mempool orders and reveals a block. Until then the compiler refuses to lower a sealed opening rather than read sealed bytes in the clear, and the front running guarantee stays a compile time fact enforced by the type checker.

## Metering

Every instruction has a fixed meter cost. The cost of a cryptographic instruction is set from the measured cost of the primitive in the cryptography benchmarks, not guessed, and the meter schedule records the source of each number. The meter is exact and deterministic. An execution that runs out of meter faults and rolls back its state changes.

## The container

A contract is a bytecode container. The container holds the code, a constant pool, the state access manifest that lists the reads and writes of each entry, and the interface descriptor with its selectors. Every digest the machine surfaces is rendered in the identifier format, never in Ethereum hex.

## Signature verification dispatches on the scheme

The machine has a verification instruction for each account signature scheme. There is one for ML DSA and one for SLH DSA, and a third for FN DSA that sits behind the same feature flag the crypto crate uses for fn dsa and is off in every default build until the standard is final. When the machine checks an account signature it reads the one byte scheme identifier from the envelope and dispatches to the matching verify instruction. The meter cost of each verify instruction is taken from the benchmark of that scheme in the crypto crate, not guessed.
