+++
categories = ["technology"]
date = 2023-08-26T18:41:00-07:00
description = "A tour of motivations, techniques, strengths, and weaknesses."
link = "https://latacora.micro.blog/2018/06/12/a-childs-garden.html"
tags = ["authentication", "encryption", "macaroons", "mtls", "servers"]
title = "Server-To-Server Authentication Schemes"
+++

Latacora:

>Modern applications tend to be composed from relationships between smaller applications. Secure modern applications thus need a way to express and enforce security policies that span multiple services. This is the "server-to-server" (S2S) authentication and authorization problem (for simplicity, I'll mash both concepts into the term "auth" for most of this post).
>
>Designers today have a lot of options for S2S auth, but there isn't much clarity about what the options are or why you'd select any of them. Bad decisions sometimes result. What follows is a stab at clearing the question up.
>
>[...]
>
>Do the simplest thing that makes sense for your application right now. A true fact we can relate from something like a decade of consulting work on these problems: intricate S2S auth schemes are not the norm; if there's a norm, it's "nothing at all except for ELBs". If you need something, but you have to ask whether that something oughtn't just be bearer tokens, then just use bearer tokens.
>
>Unfortunately, if there's a second norm, it's adopting complicated auth mechanisms independently or, worse, in combination, and then succumbing to vulnerabilities.

It's a great list. They conclude that MTLS is the best option for containers talking to each other:

>If your inter-service auth problem really decomposes to inter-container (or, without containers, inter-instance) auth, MTLS starts to make sense. The container-container MTLS story usually involves containers including a proxy, like Envoy, that mediates access. If you're not connecting containers, or have ad-hoc components, MTLS can really start to take on a CORBA feel: random sidecar processes (here stunnel, there Envoy, and this one app that tries to do everything itself). It can be a pain to configure properly, and this is a place you need to get configurations right.
>
>If you can do MTLS in such a way that there is exactly one way all your applications use it (probably: a single proxy that all your applications install), consider MTLS. Otherwise, be cautious about it.

They recommend to never use JWTs:

>JWT is a standard that tries to do too much and ends up doing everything haphazardly. Our loathing of JWT motivated this post, but this post isn't about JWT; we'll write more about it in the future.

As a bonus, they recommend macaroons for client-to-server authentication and authorization:

>Macaroons are inexplicably underused. They're the Velvet Underground of authentication mechanisms, hugely influential but with little radio airplay. Unlike the Velvets, Macaroons aren't overrated. They work well for client-server auth and for s2s auth. They're very flexible but have reassuring format rigidity, and they elegantly take advantage of just a couple simple crypto operations. There are libraries for all the mainstream languages. You will have a hard time coming up with a scenario where we'd try to talk you out of using them.
