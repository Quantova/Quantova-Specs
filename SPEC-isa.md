# The virtual machine instruction set

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The Quantova virtual machine is a register machine. It is not the Ethereum virtual machine and it is not WebAssembly. It executes the bytecode container produced by the language compiler. Every execution is deterministic, so the same code and the same inputs produce the same result and the same gas cost on every node.

## The machine model

The machine has a fixed set of general registers, each holding a 64 bit word. It has a program counter, a bounded operand and call stack, a linear scratch memory that is zeroed at entry, and access to contract state through the declared reads and writes of the entry. There is no floating point anywhere. Arithmetic is checked, so an overflow is a fault and not a wrap, unless the instruction is an explicit wrapping instruction.

## Instruction groups

Instructions fall into groups. There are arithmetic and logic instructions over 64 bit words. There are control instructions for branches, calls, and returns within a contract. There are storage instructions that read and write the declared state. There are message instructions that enqueue an asynchronous message to another contract, since there is no synchronous call that returns control in the middle of execution. And there are the native post quantum cryptographic instructions described below.

## The cryptographic instructions

The machine exposes the post quantum operations as native instructions so a contract never rolls its own cryptography. There is a verification instruction for machine lattice signatures. There is a verification instruction for hash based signatures. There is a hashing instruction over SHA3. There is a Merkle proof verification instruction. There is a random function verification instruction that checks a verifiable random output and its proof. And there is a key encapsulation instruction. There is no instruction that verifies a classical signature, and none can be added, because no classical primitive exists in the machine.

## Gas

Every instruction has a fixed gas cost. The cost of a cryptographic instruction is set from the measured cost of the primitive in the cryptography benchmarks, not guessed, and the gas schedule records the source of each number. Gas is exact and deterministic. An execution that runs out of gas faults and rolls back its state changes.

## The container

A contract is a bytecode container. The container holds the code, a constant pool, the state access manifest that lists the reads and writes of each entry, and the interface descriptor with its selectors. Every digest the machine surfaces is rendered in the identifier format, never in Ethereum hex.

## Signature verification dispatches on the scheme

The machine has a verification instruction for each account signature scheme. There is one for ML DSA and one for SLH DSA, and a third for FN DSA that sits behind the same feature flag the crypto crate uses for fn dsa and is off in every default build until the standard is final. When the machine checks an account signature it reads the one byte scheme identifier from the envelope and dispatches to the matching verify instruction. The gas cost of each verify instruction is taken from the benchmark of that scheme in the crypto crate, not guessed.
