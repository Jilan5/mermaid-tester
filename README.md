# mermaid-tester

```mermaid
architecture-beta
    group aws_cloud(cloud)[AWS Cloud Infrastructure]
    
    service client1(internet)[Client 1 (Browser)]
    service client2(internet)[Client 2 (Browser)]
    service alb(load-balancer)[Application Load Balancer\nWith Sticky Sessions]
    service app1(server)[App Server 1\nEC2 Instance] in aws_cloud
    service app2(server)[App Server 2\nEC2 Instance] in aws_cloud
    service redis(database)[Redis Server\nEC2 Instance] in aws_cloud
    
    client1:R --> L:alb
    client2:R --> L:alb
    alb:R --> L:app1
    alb:R --> L:app2
    app1:B --> T:redis
    app2:B --> T:redis
```

---

**With Sticky Sessions:**
- Client 1 always connects to App Server 1
- Client 2 always connects to App Server 2
- Todos persist in server memory
- Chat messages broadcast via Redis

---

```mermaid
architecture-beta
    group aws_cloud_unsticky(cloud)[AWS Cloud - No Sticky Sessions]
    
    service client(internet)[Same Client]
    service alb_round(load-balancer)[Application Load Balancer\nRound Robin]
    service app1_lost(server)[App Server 1\n❌ Lost Todo Data] in aws_cloud_unsticky
    service app2_new(server)[App Server 2\n⚠️ Empty Todo List] in aws_cloud_unsticky
    service redis_msg(database)[Redis Server\n✅ Messages OK] in aws_cloud_unsticky
    
    client:R --> L:alb_round
    alb_round:R --> L:app1_lost
    alb_round:R --> L:app2_new
    app1_lost:B --> T:redis_msg
    app2_new:B --> T:redis_msg
```

**Without Sticky Sessions:**
- Client randomly connects to different servers
- Todos are lost when switching servers
- Only chat messages work (via Redis)
- Poor user experience
