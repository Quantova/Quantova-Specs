# The Q Bridge gateway

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. The bridge is built after the public testnet, it never gates launch, and it never touches consensus.

## The idea

The bridge moves value between Quantova and a foreign chain. The whole of it lives off chain, inside the Q Oracle, outside our stack. The oracle watches the foreign chains, runs all of their verification, reaches its own agreement, and only then translates the result into a post quantum artifact and sends that one artifact to the chain. Nothing foreign ever enters the chain. The chain sees a post quantum attestation and nothing else, so no foreign cryptography, no foreign key, and no foreign data ever reaches the virtual machine or the consensus.

## The three parts

The Q Oracle is the bridge. It is a bonded set of off chain operators that carry the entire bridge operating system. Each operator watches a foreign chain and runs that chain's own verification in full, whether that is a proof of work header check for Bitcoin, a light client for a chain that has one, or a direct observation for a chain that has neither. Where a corridor calls for it the operators also run an internal proof of correct verification among themselves, so that no operator can attest to a fact it did not truly see. The operators reach agreement and produce one post quantum attestation of the fact. Every proof the oracle runs is its own, over our hashing, and it stays inside the oracle.

The Airlock is the ingress grammar of the chain. It admits exactly one shape of foreign originated message, a post quantum attestation in canonical form signed by the registered oracle operators. Anything else, a foreign signature, a foreign key, a raw foreign header, has no valid encoding at the boundary and cannot be parsed. The Airlock is the point where the communications layer switches from the foreign world to the post quantum one.

The gateway is a small contract on the chain. It holds the registered oracle operator set and the asset registry. When it receives an attestation it checks that enough registered operators signed it, checks the stated fact against its replay record, and then mints or burns the bridged asset. The gateway never verifies a foreign proof and never runs a light client, because all of that already happened off chain in the oracle. The gateway only checks a post quantum signature, which the machine already knows how to do.

## A deposit

A holder locks value on the foreign chain. The oracle operators each see the lock, verify it by that chain's own rules off chain, and agree on it. They sign one post quantum attestation that names the foreign chain, the foreign asset, the amount, the recipient on Quantova, and a unique reference for that one deposit. The attestation crosses the Airlock. The gateway checks that a sufficient number of registered operators signed it, checks that the deposit reference has never been used, and mints the origin tagged asset to the recipient. The reference is recorded so the same deposit can never mint twice.

## An exit

A holder burns the bridged asset in the gateway and names a destination on the foreign chain. The burn is final on Quantova. On the foreign side the value is released by an over collateralized vault, the model carried over from the earlier stack. A vault posts collateral worth more than the value it holds. The assigned vault must pay the holder on the foreign chain and prove the payout, and if it fails within the deadline anyone may cancel and the vault collateral is paid to the holder at a premium. There is no trusted signer and no threshold key. An exit is trust minimized and it is never trustless, on every corridor including Bitcoin.

## How this does not break the PQR stack

This is the heart of the design, so it is stated plainly and in full.

The chain runs no foreign cryptography, ever. Classical public key cryptography cannot be expressed anywhere the chain parses. There is no way to write an elliptic curve, a pairing, or a foreign signature check into a container, because the type system and the instruction set have no encoding for one. A foreign artifact presented at the gateway is not turned away by a check that could be forgotten, it simply has no valid form, and the conformance vectors prove that on every change.

The only thing that ever crosses into the chain is a post quantum attestation. It is signed with ML DSA, the same signature the chain verifies everywhere else, so the gateway checks it with the machine's own verify instruction and needs nothing new. The foreign chain's verification, its classical signatures, its headers, and its raw data all stay off chain in the oracle and are translated away before anything is sent. What arrives is a fact stated in our own post quantum terms, not a foreign object to be trusted on sight.

The bridge lives at the execution layer, as a contract, and it never touches consensus. QORUS does not read the bridge, the bridge sends no message back to consensus, and the bonded oracle set is not the validator set and shares no key with it. Because of this the worst a broken corridor can do is bounded by that corridor grade and by its rate limits, and it is contained to the bridged asset. A fault on a foreign chain, or a failure of an oracle quorum, can cost the bridged asset of that corridor and nothing more. It cannot reach the native asset, it cannot reach the validators, and it cannot reach finality.

Every bridged asset is second class by protocol law. It carries a visible risk label, it is never valid as validator stake, and only the native asset secures the chain. So even in the worst case the security of Quantova rests only on its own post quantum cryptography and its own validators, and the bridge sits beside that security and never inside it.

## Supported networks and assets

The bridge targets 39 origin chains and more than seventy origin assets. A corridor activates one at a time through a standards proposal, and it is graded before it opens. The registry below is the seed set. It grows only through a standards proposal and never through an operator action.

The seed registry is as follows. Bitcoin carries BTC. Ethereum carries ETH, USDT, USDC, LINK, SHIB, and UNI. BNB Smart Chain carries USDT, BNB, LINK, FDUSD, and TUSD. Solana carries USDT, SOL, USDC, RENDER, BONK, and WIF. TRON carries USDT, TRX, TUSD, and XAUT. Polygon carries USDT, USDC, LINK, UNI, AAVE, and POL. Arbitrum carries ETH, USDT, USDC, LINK, UNI, and AAVE. Base carries ETH, USDC, LINK, AERO, VIRTUAL, and wstETH. Cosmos Hub carries ATOM. Monero carries XMR, Litecoin carries LTC, Dogecoin carries DOGE, Zcash carries ZEC, and the Circle CCTP corridor carries USDC, each a federated relayer corridor graded and labeled as such. The remaining chains toward the target join the registry as their corridors are graded and activated. Every registry row carries an origin tag and a per corridor trust grade.

## The origin tag rule

Every bridged asset is identified by the pair of origin chain and origin asset, never by symbol alone. The same symbol on two chains is two distinct assets, each with its own reserve, its own risk grade, and its own origin tag, and they are never commingled and never fungible. USDT bridged from Ethereum, BNB Smart Chain, Solana, TRON, and Polygon becomes five distinct assets on Quantova, written qUSDT.eth, qUSDT.bsc, qUSDT.sol, qUSDT.tron, and qUSDT.poly. The origin tagged asset kinds reserved in the primitives specification enforce this, and collapsing same symbol assets across chains has no valid form.

## Trust grading

Every corridor carries a grade that says how strongly the oracle verifies the foreign chain, and the grade is a required and visible field in the registry and in the wallet, never a footnote. At the strongest grade the oracle runs the foreign chain full light client or payment verification and backs its attestation with an internal proof of correct verification, so an operator cannot attest to a fact it did not verify. At a weaker grade the oracle has no light client to run, because the foreign chain does not expose what one would need, so the operators observe and attest by agreement alone. From the chain side every corridor rests on the bonded oracle set, so the honest description of the whole bridge is trust minimized through the oracle, and the grade tells a holder how far the oracle own verification reduces that trust for a given corridor. Bitcoin, whose deposits the oracle backs with a proof of the foreign header chain, is graded near trustless. A corridor with no light client is graded a relayer corridor and is labeled as such so a holder sees the added trust before using it.

## Asset controls

A stablecoin bridged into Quantova carries the reality that its backing on the origin chain can be frozen or seized by its issuer, independent of Quantova. Each stablecoin asset is labeled with that issuer freeze reality, and where the issuer standard applies the bridged representation respects the origin side controls. Every bridged asset is second class by protocol law, a distinct origin tagged kind with a visible risk label, and never valid as validator stake, so only native QTOV secures consensus and a fault on a foreign chain can never reach it. A wrapped or long tail asset carries the long tail protocol fee tier of one tenth of one percent. A native or major asset carries its tier from the fee table in the economics specification.

## Fees

The bridge charges a single flat fee of one tenth of one percent on the way out, taken from the amount so the holder receives the amount less the fee, and the fee goes to the treasury. It is set once and changed only by governance. There is no fee on the way in beyond the ordinary cost of the minting transaction.

## Containment

Classical cryptography stops at the oracle and never reaches the Airlock. The oracle is the attestor, and its foreign verification code for every one of the origin chains lives only in the q-attestor repository, the one component exempt from the deny list, and nothing in the organization imports it. No corridor, whatever its grade, may introduce a classical signature or a foreign key into anything the chain parses, and the gateway enforces rate limits, exit delays, and a watchdog pause. A breach on the foreign side is contained to the bridged asset and never reaches consensus or the native asset.

## Claim discipline

Say trust minimized through the oracle, near trustless Bitcoin deposits, and trust minimized exits. Grade every corridor by the strength of the oracle own verification and label a relayer corridor as one. Never say fully trustless bridge, and never say the chain itself verifies a foreign proof, because the chain verifies only the post quantum attestation.
