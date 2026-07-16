# Peer transport

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The transport is our own protocol over QUIC. It uses ML KEM for the key exchange and the module lattice signature for the identity handshake. It uses no external peer library and no classical key exchange, including on development networks.

## The handshake

Two peers open a QUIC connection and run a post quantum handshake. Each peer has a long term module lattice identity key whose public part is its peer identity. The peers exchange ML KEM encapsulations to establish a shared secret, and each signs the transcript with its identity key so the other knows who it is talking to. There is no elliptic curve key exchange anywhere. A peer identity is rendered in the identifier format, never in hex.

## Encryption

Once the shared secret is set, the channel is encrypted with a 256 bit symmetric cipher and every record is authenticated. The keys are rotated on a schedule stated here.

## Gossip

Blocks, transactions, and certificates spread by gossip. A peer forwards a message it has not seen to a bounded set of neighbors, so a message reaches the network in a small number of hops without flooding. A message that fails to parse by the canonical codec is dropped at the edge and never forwarded, which is where a classical artifact is refused.

## Block dissemination and availability

A block may be dispersed by an erasure code rather than sent whole. The block is coded into k data shards and n total shards, any k of which reconstruct it byte for byte, with the n shard hashes committed under a SHA3 Merkle root the header carries, so a shard is verified against the header before use. This is recorded here with its purpose stated exactly, so it is not later mistaken for something it is not.

What erasure coding does. It relieves the block producer of sending the whole block to many peers, which was a real per slot bottleneck at the leader. It buys loss tolerance, since the block survives losing up to n minus k shards. And it gives a genuine saving for a node performing the custody and availability duty, holding and verifying one shard rather than the whole block.

What it does not do. It does not reduce what a node that must reconstruct the block downloads. Reconstructing the whole block gathers k shards, which is a full block worth by information theory, so a committee member that executes and attests to the block saves no bytes from coding alone. The saving is for the availability layer, not a per validator bandwidth cut, and it is never to be reported as one.

Why it is here anyway. Erasure coding is the prerequisite for data availability sampling, which is the only safe way to let a node stop downloading data it does not itself execute. A node can verify that a coded block is available by sampling a few shards against the committed root, and if the samples are present the whole is reconstructable with high probability, so availability becomes a property a node checks rather than a thing it must download in full to know. Without the coding there is no way to prove availability short of downloading, and a chain where nobody can prove the data is held is not safe to lighten. So this lays the foundation for the availability architecture, and the analysis of that architecture is in SCOPE-transaction-availability.md.

## What is coded and what is not yet, open

The proposal path is coded. A block wider than the transport record is dispersed as erasure coded shards and reconstructed against the header, so proposal width is no longer capped by the record size. Two other paths are not coded and are open, recorded here so they are not lost in a report. Catch up sync still serves a whole finalised block in one record, so a node fetching a block wider than the record cannot get it that way. And a view change record carries the locked block whole, so an oversize locked block does not fit a view change. Both were pre existing to the proposal path and are outside it, and until they are coded the record size still caps block width on the sync and the view change, not on the proposal.

Neither blocks a run that stays in lockstep or below the record size. They would bite a genuinely distributed run only at a block width above the record under lag or a view change, so a distributed run at sub record widths is unaffected, and a wide width distributed run needs the same erasure mechanism applied to these two records first. Coding them is that mechanism on those two records, and it is not done.

## Sync

A peer that is behind catches up by asking neighbors for headers from its last known height, verifying each header and its certificate, and then fetching the bodies. A light client syncs headers alone. Sync never trusts a peer. It verifies every header and every proof.

## No inherited stack

There is no external peer library, no inherited networking, and no classical transport security. The protocol is built from QUIC, ML KEM, the module lattice signature, and SHA3, and nothing else.
