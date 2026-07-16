# Plan for the genuinely distributed run

Status. This is a plan for the founder, not normative and not an implementation. It sets out the run that produces the first real network throughput and finality figure, the hosts it needs, what it measures and isolates, and the prerequisites that must be resolved first. It builds nothing. The parallelism variable is already measured and committed in the loopback results file, so this run adds one variable on top of a number that is understood.

## What it measures, and the one variable it adds

The loopback run isolated parallelism, real parallel per process compute over localhost sockets with near zero propagation, and it moved finality about two and a half times against the serial baseline. This run adds propagation, real geographic network latency between validators, on top of that parallelism. Because parallelism is already understood, the difference between this run and the loopback run is the propagation cost, so the two runs together separate the two variables that a single distributed run would have bundled. This run produces the first real network sustained finalised throughput and the first real finality distribution with a tail that includes the wire.

## The hosts it needs

The value of this run is real propagation, so the hosts must be geographically spread, not in one datacentre where inter host latency is about a millisecond and barely above loopback.

The validator hosts run one process per validator, each on its own machine, at least the committee size, four to match the loopback comparison, and more for a realistic set, seven or the active set size. They are server class to match the validator budget and the one gigabit floor, on the order of four to eight cores, sixteen gigabytes, a one gigabit link.

They are spread across regions, three to five continents, so inter host round trips are on the order of a hundred to three hundred milliseconds, the real intercontinental range the finality number must include.

One coordinator host rendezvous the validators, drives the workload, and collects the committed results.

Cloud virtual machines are the practical form. Provisioning them, the count, the regions, and the cost, is an operations decision and a spend, and it is the founder's to authorise.

## The method

Deploy the validator binary on each host, adapted so each takes its peers' real addresses rather than localhost. Run the real consensus, the one time key sortition with the floor on, real module lattice attestations, a real aggregated certificate every host verifies, over the real post quantum channel across the real wide area sockets. Drive a sustained back to back workload of real signed transactions for at least a minute of wall clock, count only finalised transactions, and record the finality as a distribution with its tail. Commit a results file in the q-prover form, the host regions and the measured inter host round trips, the profile, the process count, the committee size, the block width, the transaction mix, the duration, the method, then the figures, and never a figure without the file. Compare it to the loopback number at the same committee and width so the propagation delta is explicit.

## Prerequisites, named so they are not discovered on the day

The transport is not QUIC. The channel is the real post quantum secure channel over a reliable byte stream, which in the loopback run is real localhost TCP. Over a wide area, the congestion control and the head of line blocking of a byte stream are real costs, and this run measures them honestly because they are what runs. The fixed decision names QUIC and the implementation is a channel over TCP, so the run measures the transport that exists, and the QUIC against reality gap in the record is separate and flagged.

The width paths are half coded. Over a wide area, nodes lag on real propagation, so catch up sync triggers, and catch up sync and view change records still serve a whole block per record and are open, recorded in the p2p spec. So this run stays at block widths below the record, where sync and view change are unaffected, or those two records are coded first. A sub record distributed run is unblocked. A wide width one is not.

The key tree bounds a long run. The pinned sortition serves sixty four slots, one per height, so a sustained run finalises at most sixty four heights before the tree is exhausted. The distributed run is slower per height than loopback because propagation adds latency, so it exhausts the tree in wall clock more slowly, but a minute at the distributed rate must be checked against the sixty four height bound, and a longer run needs a larger slot count in the pinned consensus, a cross repo change and a founder decision.

Keys, addresses, and reachability. Each validator needs its identity key and its peers' addresses and reachable ports across the internet, and the hosts must reach each other on the transport port. This is ordinary deployment setup, not code.

## What it does not settle

It does not settle scale beyond the committee size run, and it does not settle the wide block width until the sync and view change records are coded. It settles the one thing it is for, the real propagation cost on top of a parallelism number already understood, and it produces the first honest whole stack network figure, committed and outside verifiable.

## The decision

The run is ready to build once the hosts exist. What is the founder's is the spend and the shape, how many hosts, which regions, and the block width, sub record now or wide after the two records are coded. Name those and the run is built, its result committed as a file, and verified from the outside.
