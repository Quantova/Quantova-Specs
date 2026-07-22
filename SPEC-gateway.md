# The Q Bridge gateway

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. This is built after the public testnet, it never gates launch, and it never touches consensus.

The bridge lets value move between Quantova and a foreign chain without letting a classical primitive into the stack. It runs through an off chain translation layer and the Airlock.

## The parts

The gateway is an on chain contract that mints and burns bridged assets. The translation layer is a set of off chain nodes that watch a foreign chain, run its classical verification, and produce a post quantum attestation with a proof of correct verification. The Airlock is the ingress grammar, so the chain parses exactly two foreign artifacts, a post quantum attestation in canonical form and a proof, and anything else has no valid form.

## Deposits and exits

A deposit locks value on the foreign chain, the translation layer attests to it, and the gateway mints the bridged asset once it checks the attestation and the proof against its registered attestor set. An exit burns the bridged asset and releases the value on the foreign chain through a threshold custody. The Bitcoin corridor comes first and is near trustless, since its verification is a proof of a foreign header chain inside a STARK. The Ethereum corridor is second and is trust minimized.

## Supported networks and assets

The bridge targets 37 origin chains and 68 origin assets. A corridor activates one at a time through a standards proposal, and it is graded before it opens. The registry below is the seed set. It grows only through a standards proposal and never through an operator action.

The seed registry is as follows. Bitcoin carries BTC. Ethereum carries ETH, USDT, USDC, LINK, SHIB, and UNI. BNB Smart Chain carries USDT, BNB, LINK, FDUSD, and TUSD. Solana carries USDT, SOL, USDC, RENDER, BONK, and WIF. TRON carries USDT, TRX, TUSD, and XAUT. Polygon carries USDT, USDC, LINK, UNI, AAVE, and POL. Arbitrum carries ETH, USDT, USDC, LINK, UNI, and AAVE. Base carries ETH, USDC, LINK, AERO, VIRTUAL, and wstETH. Cosmos Hub carries ATOM. The remaining chains toward the target join the registry as their corridors are graded and activated. Every registry row carries an origin tag and a per corridor trust grade.

## The origin tag rule

Every bridged asset is identified by the pair of origin chain and origin asset, never by symbol alone. The same symbol on two chains is two distinct assets, each with its own reserve, its own risk grade, and its own origin tag, and they are never commingled and never fungible. USDT bridged from Ethereum, BNB Smart Chain, Solana, TRON, and Polygon becomes five distinct assets on Quantova, written qUSDT.eth, qUSDT.bsc, qUSDT.sol, qUSDT.tron, and qUSDT.poly. The origin tagged asset kinds reserved in the primitives specification enforce this, and collapsing same symbol assets across chains has no valid form.

## Trust grading

Every corridor records how its foreign verification is done and how far it is trust minimized, and the grade is a required and visible field in the registry and in the wallet, never a footnote. A trust minimized corridor verifies the foreign chain inside the runtime through a light client, a simple payment verification, or a proof, so it rests on the foreign chain's own consensus and needs no external quorum. Bitcoin is graded trust minimized through a SHA 256 simple payment verification carried in a proof of the foreign header chain, and Ethereum is graded trust minimized. A trusted relayer corridor depends on an attestor quorum or a multisig, because the foreign chain does not expose the commitments a trust minimized proof would need. TRON is graded trusted relayer. No corridor ships without a grade, and a trusted relayer corridor is labeled as such in the wallet so a holder sees the added trust before using it.

## Asset controls

A stablecoin bridged into Quantova carries the reality that its backing on the origin chain can be frozen or seized by its issuer, independent of Quantova. Each stablecoin asset is labeled with that issuer freeze reality, and where the issuer standard applies the bridged representation respects the origin side controls. Every bridged asset is second class by protocol law, a distinct origin tagged kind with a visible risk label, and never valid as validator stake, so only native QTOV secures consensus and a fault on a foreign chain can never reach it. A wrapped or long tail asset carries the long tail protocol fee tier of one tenth of one percent. A native or major asset carries its tier from the fee table in the economics specification.

## Containment

Classical cryptography stops at the Airlock. The translation layer is the attestor, and its classical verification code for every one of the origin chains lives only in the q-attestor repository, the sole component exempt from the deny list. Nothing in the organization imports it. No corridor, whatever its grade, may introduce a classical signature or a foreign key into anything the chain parses, and the gateway enforces rate limits, exit delays, and a watchdog pause. A breach on the foreign side is contained to the bridged asset and never reaches consensus or the native asset.

## Claim discipline

Say near trustless Bitcoin deposits and trust minimized exits. Never say fully trustless bridge.
