---
date: '2015-10-17'
link: https://www.consul.io/intro/vs/zookeeper.html
tags:
- consul
- zookeeper
- etcd
- hashicorp
title: Consul vs. ZooKeeper, doozerd, etcd
---

HashiCorp on Consul versus ZooKeeper, doozerd, and etcd:

>ZooKeeper, doozerd, and etcd are all similar in their architecture. All three have server nodes that require a quorum of nodes to operate (usually a simple majority). They are strongly-consistent and expose various primitives that can be used through client libraries within applications to build complex distributed systems.
>
>[...]
>
>All of these systems have roughly the same semantics when providing key/value storage: reads are strongly consistent and availability is sacrificed for consistency in the face of a network partition. However, the differences become more apparent when these systems are used for advanced cases.

They conclude:

>Consul provides first-class support for service discovery, health checking, K/V storage, and multiple datacenters. To support anything more than simple K/V storage, all these other systems require additional tools and libraries to be built on top. By using client nodes, Consul provides a simple API that only requires thin clients. Additionally, the API can be avoided entirely by using configuration files and the DNS interface to have a complete service discovery solution with no development at all.

A pretty compelling story.
