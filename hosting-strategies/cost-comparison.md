# Cost & Strategy Comparison

## 1. Bootstrapping (Free / Managed)

Perfect for MVP, Side Projects, and Pre-Revenue startups.

| Provider          | Database       | Free Tier Limits                                            |
| :---------------- | :------------- | :---------------------------------------------------------- |
| **Supabase**      | PostgreSQL     | 500MB DB, 2GB Bandwidth                                     |
| **Upstash**       | Redis/Kafka    | 10k requests/day                                            |
| **PlanetScale**   | MySQL (Vitess) | (Note: Their free tier has changed recently, check pricing) |
| **MongoDB Atlas** | MongoDB        | M0 Cluster (512MB Storage)                                  |

> **Pros**: Setup time is minutes. No maintenance.
> **Cons**: "Cliff" pricing. Once you scale, it gets expensive fast.

---

## 2. Self-Hosting (Lowest Cost)

Perfect for Technical Founders, Bootstrapped SaaS with moderate traffic.

**The Stack**:

- **Hardware**: Hetzner (Germany/Finland) or DigitalOcean Droplets.
- **Orchestration**: Docker Compose or Coolify (PaaS in a box).
- **Database**: Official Docker Images.

**Cost Example**:

- **Managed AWS RDS (db.t3.medium, 2vCPU, 4GB RAM)**: ~$60/month + Storage cost.
- **Hetzner VPS (CX31, 2vCPU, 8GB RAM)**: ~$5 - $10/month.
- **Savings**: ~85% cheaper.

> **Pros**: Insane value. Full control.
> **Cons**: You are the DBA. Backups, updates, and security patches are YOUR job.

---

## 3. The Enterprise (No-Ops)

Perfect for funded scale-ups where Engineering Salary > AWS Bill.

**The Logic**:
A Senior DevOps Engineer costs $150k+/year. If AWS RDS costs an extra $2k/month ($24k/year) but saves 20% of that engineer's time, **you buy AWS RDS.**

**Top Tier Services**:

- **AWS Aurora**: Serverless scaling, crash recovery.
- **Google Spanner**: Global consistency (True External Consistency).
- **DynamoDB**: Infinite throughput (pay per request).

> **Pros**: 99.999% SLA. Point-in-time recovery. Compliance (SOC2/HIPAA).
> **Cons**: The bill can be shocking if not monitored (Egress fees!).
