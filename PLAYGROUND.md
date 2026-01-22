# Infrastructure Playground

This folder allows you to spin up the entire database landscape with a single command.

## 🚀 One-Click Start

Ensure you have **Docker Desktop** installed.

```bash
# Start all databases
docker-compose up -d

# Stop all databases
docker-compose down
```

## 🔌 Connection Details

| Database       | Port    | Username | Password   |
| :------------- | :------ | :------- | :--------- |
| **PostgreSQL** | `5432`  | `admin`  | `password` |
| **MySQL**      | `3306`  | `root`   | `password` |
| **Redis**      | `6379`  | _(none)_ | _(none)_   |
| **ScyllaDB**   | `9042`  | _(none)_ | _(none)_   |
| **MongoDB**    | `27017` | `admin`  | `password` |
