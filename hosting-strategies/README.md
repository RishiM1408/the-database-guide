# Hosting Strategies: Economics of Scale

A great database architecture can fail if it bankrupts the company. This section analyzes the "Build vs. Buy" decision.

## 💰 Tiers

### 1. [The Bootstrapper](./cost-comparison.md#1-bootstrapping-free--managed)

- **Goal**: $0/month to start.
- **Strategy**: Leverage generous free tiers from managed cloud providers.

### 2. [The Self-Hoster](./cost-comparison.md#2-self-hosting-lowest-cost)

- **Goal**: Maximum compute/storage per dollar.
- **Strategy**: Renting "bare metal" or VPS (Hetzner, DigitalOcean) and managing Docker containers yourself.

### 3. [The Enterprise](./cost-comparison.md#3-the-enterprise-no-ops)

- **Goal**: Reliability, Compliance, and "No-Ops".
- **Strategy**: Paying a premium for AWS RDS, Google Spanner, or other fully managed services where you pay for _sleep_.
