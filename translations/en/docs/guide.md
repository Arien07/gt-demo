Linearizability is easily confused with "serializability", because both names seem to suggest "can be arranged in some order". But they are two completely different guarantees and must be distinguished:

Serializability

Serializability is an isolation property of transactions; each transaction can read and write multiple objects (rows, documents, or records). It guarantees that the behavior of transactions is equivalent to executing them in some serial order: one transaction is executed completely first, then the next transaction is executed completely, without interleaving. This serial order can differ from the order in which the transactions actually ran 9.

Linearizability

Linearizability is a guarantee about reads and writes of a register (i.e., a single object). It does not group operations into transactions, so it cannot prevent problems involving multiple objects such as "write skew and phantom reads". But linearizability is a freshness guarantee: if one operation has finished before another operation begins, then the latter operation must observe a state that is at least as fresh as the former. Serializability has no such requirement; for example, it allows stale reads 10.

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

Locking and leader election

Systems that use single-leader replication must ensure that there is indeed only one leader, and not multiple leaders at the same time (split brain). One approach to leader election is to use a lease: each node that starts up tries to acquire the lease, and the winner becomes the leader 17. No matter how the underlying mechanism is implemented, it must satisfy linearizability, and it must never allow two different nodes to acquire the same lease at the same time.

Coordination services such as Apache ZooKeeper 18 and etcd are often used to implement distributed leases and leader election. They use consensus algorithms to provide linearizable operations in a fault-tolerant manner (these algorithms will be discussed later in this chapter). There are still many subtle details to correctly implement leases and leader election, such as the fencing problem described in "Distributed Locks and Leases". Libraries such as Apache Curator wrap higher-level idiomatic solutions on top of ZooKeeper, which can alleviate this work; but the foundation of all these coordination tasks is still a linearizable storage service.