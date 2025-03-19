+++
title = "Distributed lock with Redis"
description = ""
tags = [
    "Performance",
    "Consistency",
    "BackEnd",
    "Redis",
]
date = "2025-03-18"
categories = [
    "Review Popular Software",
    "Parallel Programming",
]
menu = "main"
+++

Some times we need to synrhonize between different server or different thread inside a service in a distributed System. **Redis** is a single-thread application and garantee atomicity for each operation when we do not consider **Cluster** Mode, and it also provide high performance. In this article, we will disccuss the implementation a distributed lock with **Redis**

### Single Redis 

### Redis Cluster
