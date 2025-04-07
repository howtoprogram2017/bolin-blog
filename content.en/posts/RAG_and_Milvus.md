+++
title = "Python RAG with Vector Database Milvus"
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

Some times we need to synchronize between different server or different thread inside a service in a distributed System. **Redis** can guarantee atomicity for each operation when we do not consider **Cluster** Mode, and it also provide high performance. In this article, we will discuss the implementation a distributed lock with **Redis**


Part of the contents in this blog page are written based on personal thought which might not be accurate.  
For more detailed discussion on this issue, check the official document on it:  [Distributed Locks with Redis](https://redis.io/docs/latest/develop/use/patterns/distributed-locks/)

### Major Concern
The minimum guarantees needed for an effective distributed lock are:
1. Mutual Exclusion: At any given moment, only one client can hold a lock
2. Deadlock free. It is always possible to acquire a lock Eventually
3. Fault tolerance. when minority of node failed, clients are still able to acquire and release locks.


Also we do want a high performance lock on Redis so the locking time is not the bottleneck of our service. So concurrent locking requests should be properly covered in the implantation. 

As we will see in the following article, the property of Mutual Exclusion is not guarantee in Redis Lock.

### Single Instance
The implementation for single redis lock is very short but valuable for a high performance lock. 

#### Acquire Lock
To acquire a lock, we can send this command to redis
```
SET lock_key random_value NX PX 50000
```
This command set the *lock_key* with a random value when the lock key is not already exist (NX). And redis will hold the key for 50000 mill seconds (PX 50000). The functionality of command can be illustrated as a transaction in a relational database: 
```
start transaction；
select lock_table  where key = lock_key and value is NULL  for update；
update lock_table set value = random_value,  expiration_time = current_timestamp() + 50000；
commit；
```
In this operation we only have two command and we do not need to wait other keys but for current requesting key. Since redis execute this in memory which is pretty fast compare to disk IO and the waiting time for acquiring lock is still acceptable for high concurrency.

#### Release Lock
To release a lock, we accomplished it using Lua script:
```
if redis.call("get",lock_key) == random_value then
    return redis.call("del",lock_key)
else
    return 0
end
```

Here we need to delete the lock if the lock value is exactly the same as the value we have set in the same thread. The reason for double checking the lock_value is that their still possible for other client to acquire this lock with different lock value when the current client need to unlock. The Mutual Exclusive property is invalid now, and it is because we need to remove the lock when it expires (timeout) and another client can acquire the lock again. The current client cannot set the unmatched lock_value to empty since it is used by other client right now.

If we remove the timeout settings, there might be in some cases when clients cannot send the unlock command back, and other client cannot acquire the lock forever (DeadLock). Therefore setting an appropriated timeout value is essential for locking performance. If the application do have an uncertain processing time, we can use a *Watch Dog* program to probe the Liveness of our client and extend the holding lock.

Another thing need to mention is the generation for lock_value. This random value should be generated and stored at client side, and it need to be complex enough  to prevent conflicts on lock_value. And we need an idempotent action clients in the locking period, since the mutual exclusive property cannot be guaranteed 




### Redis Cluster
If single instance of **Redis** cannot provide enough storage or availability we can switch to Redis Cluster which need at least 3 master nodes (Read and Write). There is a Red Lock Algorithm of N Redis master  and you may get the implementation in a package of your preferred language.


In order to acquire the lock, the client performs the following operations:

t gets the current time in milliseconds.
It tries to acquire the lock in all the N instances sequentially, using the same key name and random value in all the instances. During step 2, when setting the lock in each instance, the client uses a timeout which is small compared to the total lock auto-release time in order to acquire it. For example if the auto-release time is 10 seconds, the timeout could be in the ~ 5-50 milliseconds range. This prevents the client from remaining blocked for a long time trying to talk with a Redis node which is down: if an instance is not available, we should try to talk with the next instance ASAP.
The client computes how much time elapsed in order to acquire the lock, by subtracting from the current time the timestamp obtained in step 1. If and only if the client was able to acquire the lock in the majority of the instances (at least 3), and the total time elapsed to acquire the lock is less than lock validity time, the lock is considered to be acquired.
If the lock was acquired, its validity time is considered to be the initial validity time minus the time elapsed, as computed in step 3.
If the client failed to acquire the lock for some reason (either it was not able to lock N/2+1 instances or the validity time is negative), it will try to unlock all the instances (even the instances it believed it was not able to lock).