# The bytecode container and the interface descriptor

Status. This document is normative. It sits under the crypto policy and the language specification. If anything conflicts with the crypto policy, stop and report. The authoritative companions are SPEC-isa.md, SPEC-cid.md, SPEC-quanta-language.md, SPEC-lowering.md, and SPEC-blocks.md.

A contract is a bytecode container. The container holds the code image, a constant pool, a state access manifest that lists the reads and writes of each entry, and the interface descriptor of entry and event selectors. The interpreter loads the container and enters an entry by its selector.

## The canonical bytes and the identifier

The container has one canonical serialization, so no two distinct containers share an encoding. The bytes begin with a four byte format tag that binds the layout version, then the length prefixed code image, then the length prefixed constant pool of 64 bit words, then the length prefixed entry table. Each entry carries its selector, the code offset it begins at, and its declared reads and writes as length prefixed slot lists. The container identifier is the SHA3 256 hash of the canonical bytes, surfaced in the identifier format and never in hexadecimal.

## Selectors

The selector of an entry or an event is the leading four bytes of the SHA3 hash of the canonical signature string, which is the name followed by the parenthesized parameter types in order, written with the language type names. A call names an entry by its selector and the interpreter resolves it to the entry's code offset, reverting when no entry matches. The selector width and the hash are the machine's, so a selector a compiler emits matches the one the machine surfaces.

## The interface descriptor

The interface descriptor names every callable entry and every event, so a caller, a wallet, or an explorer reads a contract's full interface from the chain without an out of band file. Each entry records its name, its canonical signature, its selector, and the scratch memory offsets its argument words load from. Each event records its name, its canonical signature, and its selector.

## The event payload

An emit records a typed event through the event instruction. The event carries its four byte selector and a payload of the operand values, marshalled in emission order, a u128 operand as its low then high 64 bit word and any other operand as a single 64 bit word. The machine records the event as an effect, and the host appends it to the block event trie whose root is committed in the block header, so a light client verifies an event against the header without holding the full state. A fault records nothing, so an event lands only on a clean halt.
