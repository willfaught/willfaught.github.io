---
date: "2016-03-30T18:45:42+00:00"
draft: false
tags: ["cockroach", "cockroachdb", "sql", "beta", "newsql"]
title: "CockroachDB Skitters into Beta | Blog | Cockroach Labs"
---
From https://www.cockroachlabs.com/blog/cockroachdb-skitters-beta/:

I've been looking forward to this. NewSQL seems like the future. NoSQL has impressive strengths, but managing denormalization is a pain in the ass.

>One of the most notable features of CockroachDB is just how simple it is to deploy. CockroachDB is a single binary which requires only the location of one or more storage devices to manage. Starting a multi-node cluster is as simple as starting the first node on its own and then pointing each additional node at the first node or any other node which has already joined the cluster. There are no external dependencies required. No global configuration, no distributed file system, no bundle of resources or install scripts. There are no config files and we’ve strictly limited the available command line flags to those which are useful and not just tunable knobs for the sake of having knobs. Securing a cluster is similarly straightforward.

Amen! Music to my ears.