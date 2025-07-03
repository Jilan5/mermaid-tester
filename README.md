```mermaid
architecture-beta
    group aws_network(cloud)[AWS Network Infrastructure]
    
    service client1(internet)[Client 1 Browser] 
    service client2(internet)[Client 2 Browser]
    service client3(internet)[Client 3 Browser]
    service alb(load-balancer)[Application Load Balancer] in aws_network
    service app1(server)[App Server 1 - Local Todos] in aws_network
    service app2(server)[App Server 2 - Local Todos] in aws_network
    service redis(disk)[Redis Shared State Store] in aws_network
    
    client1:R --> L:alb
    client2:R --> L:alb
    client3:R --> L:alb
    alb:R --> L:app1
    alb:R --> L:app2
    app1:B --> T:redis
    app2:B --> T:redis
    redis:T --> B:app1
    redis:T --> B:app2
```

**🔄 Redis Shared State Architecture:**

**Shared via Redis:**
- Chat messages broadcast to all connected clients
- System notifications across all servers
- Real-time communication channels
- Cross-server data synchronization

**Local per Server:**
- Todo lists stored in memory
- Client session data
- WebSocket connection state
- User-specific application state

**How it works:**
1. Clients connect via load balancer with session affinity
2. Chat messages are published to Redis shared state store
3. Redis broadcasts to all app servers maintaining shared state
4. Todos remain local to each server for data consistency
5. Both servers subscribe to Redis for real-time updates
