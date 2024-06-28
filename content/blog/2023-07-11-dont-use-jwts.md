+++
categories = ["technology"]
date = 2023-07-11T20:08:58-07:00
description = "Use session tokens instead."
link = "https://redis.com/blog/json-web-tokens-jwt-are-dangerous-for-user-sessions/"
tags = ["json", "jwt", "redis", "sessions"]
title = "Don't Use JWTs"
+++

Raja Rao, writing for Redis:

>There are many in-depth articles and videos from SMEs of companies like Okta talking about the potential dangers and inefficiencies of using JWT tokens[1]. Yet, these warnings are overshadowed by marketers, YouTubers, bloggers, course creators, and others who knowingly or unknowingly promote it.
>
>If you look at many of these videos and articles, they all just talk about the perceived benefits of JWT and ignore the deficiency. More specifically, they just show how to use it but don’t talk about revocations and additional complexities that JWT adds in a real production environment. They also never compare it with the existing battle-tested approaches deep enough to really weigh the pros and cons.
>
>[...]
>
>The biggest problem with JWT is the token revoke problem. Since it continues to work until it expires, the server has no easy way to revoke it.

A nice summary of the issues. You don't tend to see many JWT critiques.
