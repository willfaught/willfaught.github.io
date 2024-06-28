+++
categories = ["life"]
date = 2008-10-12T16:34:00-07:00
tags = ["artificial intelligence", "computation", "computers", "concurrency", "ideas", "reflections", "turing machines"]
title = "Artificial Intelligence"
+++

For a while I've thought that creating artificial intelligence would be straightforward. Regardless of whether the nature of human intelligence will ever be understood, eventually every detail of the physiology of the human brain will be discovered and known. Computers can know the current state of every atom in a brain and compute its next state, thereby simulating the brain. Computers don't need to understand human intelligence to simulate it, they need only to simulate a brain, and intelligence will arise from that.

Could that work? Contemporary computers are manifestations of a model of computation called a register machine, which is equivalent in computational capability to a Turing machine. Turing machines are well understood to be incapable of several important tasks, such as reliably determining whether another Turing machine will in fact do what it was meant to do, or if it would ever finish doing it. Human intelligence can compute those things; it is capable of computing things that Turing machines cannot. If Turing machines (computers) could simulate a brain, and thus simulate human intelligence, then that human intelligence, and thus Turing machines, could compute things that Turing machines cannot, which is a contradiction. Therefore Turing machines, and thus computers, cannot simulate human intelligence by simulating brains.

Turing machines can simulate brains, but not human intelligence. What's the difference between how Turing machines simulate brains and how real brains work? One difference is that the state of real brains changes concurrently, whereas Turing machines must compute brain states consecutively: one atom at a time. Turing machines have no sense of time. Perhaps human intelligence requires brain states to be computed concurrently. Perhaps human intelligence arises because signals that are determined concurrently cross between the brain hemispheres and feed into each other's next state like a feedback loop.

Computers can simulate concurrency through hardware or do concurrency with multiple cores. Perhaps their concurrency capability renders them more powerful than Turing machines.
