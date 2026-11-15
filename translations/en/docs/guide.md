Linearizability is easily confused with serializability, because both names seem to imply something like "things can be arranged in some order." But they are two completely different guarantees, and it is important to distinguish them:

Serializability
Serializability is an isolation property of transactions, where each transaction may read and write multiple objects (rows, documents, or records). It guarantees that the behavior of transactions is the same as if they had executed in some serial order: one transaction executed completely first, then the next transaction, with no interleaving. This serial order can be different from the order in which transactions were actually run 9.
Linearizability
Linearizability is a guarantee about reads and writes of a register (i.e., a single object). It does not group operations together into transactions, so it does not prevent problems such as write skew and phantom reads, which involve multiple objects. But linearizability is a recency guarantee: if an operation finishes before another operation begins, then the later operation must observe a state that is at least as recent as the state observed by the earlier operation. Serializability has no such requirement; for example, it allows stale reads 10.

```
upstream upserver_weighted {
    server 192.168.1.10:8080 weight=3;  # 高性能服务器
    server 192.168.1.11:8080 weight=2;  # 中等性能服务器
    server 192.168.1.12:8080 weight=1;  # 普通性能服务器
}

server {
    listen 80;
    server_name ms.com;
    
    location /api/questions/ {
        proxy_pass http://upserver_weighted;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Locking and Leader Election
Systems using single-leader replication must ensure that there is indeed only one leader, and not several leaders at the same time (split brain). One way of implementing leader election is to use a lease: each node that starts up tries to acquire the lease, and the winner becomes the leader 17. Whatever the underlying mechanism, it must satisfy linearizability; it must never happen that two different nodes are granted the same lease at the same time.

Coordination services such as Apache ZooKeeper 18 and etcd are often used to implement distributed leases and leader election. They use consensus algorithms to provide linearizable operations in a fault-tolerant way (these algorithms are discussed later in this chapter). There are many subtle details to get right in implementing leases and leader election correctly, such as the fencing problem described in "Distributed Locks and Leases." Libraries such as Apache Curator provide higher-level recipes on top of ZooKeeper that can help with this work; but the foundation of all these coordination tasks is still a linearizable storage service.