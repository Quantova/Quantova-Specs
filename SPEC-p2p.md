# Peer transport

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The transport is our own protocol over QUIC. It uses ML KEM for the key exchange and the module lattice signature for the identity handshake. It uses no external peer library and no classical key exchange, including on development networks.

## The handshake

Two peers open a QUIC connection and run a post quantum handshake. Each peer has a long term module lattice identity key whose public part is its peer identity. The peers exchange ML KEM encapsulations to establish a shared secret, and each signs the transcript with its identity key so the other knows who it is talking to. There is no elliptic curve key exchange anywhere. A peer identity is rendered in the identifier format, never in hex.

## Encryption

Once the shared secret is set, the channel is encrypted with a 256 bit symmetric cipher and every record is authenticated. The keys are rotated on a schedule stated here.

## Gossip

Blocks, transactions, and certificates spread by gossip. A peer forwards a message it has not seen to a bounded set of neighbors, so a message reaches the network in a small number of hops without flooding. A message that fails to parse by the canonical codec is dropped at the edge and never forwarded, which is where a classical artifact is refused.

## Sync

A peer that is behind catches up by asking neighbors for headers from its last known height, verifying each header and its certificate, and then fetching the bodies. A light client syncs headers alone. Sync never trusts a peer. It verifies every header and every proof.

## No inherited stack

There is no external peer library, no inherited networking, and no classical transport security. The protocol is built from QUIC, ML KEM, the module lattice signature, and SHA3, and nothing else.
