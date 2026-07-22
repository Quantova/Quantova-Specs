# The contract interface descriptor

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The contract interface descriptor is the interface of a contract. It plays the role that an application binary interface plays elsewhere, and it is embedded in every compiled contract and fetchable from the chain, so no application ever depends on a file kept outside the chain.

## Selectors

Every entry and every event has a selector. A selector is the first bytes of the SHA3 hash of the canonical signature string of the entry or event, where the signature string is the name followed by the parenthesized parameter types in order, written with the language type names. A caller names an entry by its selector.

## Embedding

The compiler places the descriptor inside the bytecode container. A node that holds the contract can return its descriptor, so a client learns how to call a contract from the chain itself. The descriptor is part of what the contract address commits to, so it cannot be swapped without changing the contract.

## Rendering

Any digest in the descriptor, including a selector shown to a person, is rendered in the identifier format, never in Ethereum hex.
