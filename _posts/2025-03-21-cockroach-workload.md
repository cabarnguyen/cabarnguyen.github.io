---
title: "Cockroach workload"
date: 2025-03-21 00:00:00 +0800
categories: [Cockroach]
tags: [cockroach, workload]
---


CockroachDB comes with built-in load generators for simulating different types of client workloads, printing per-operation statistics and totals after a specific duration or max number of operations. To run one of these load generators, use the cockroach workload command as described below.
### Step 1: Start CockroachDB in Standalone Mode
Run the following command to start a single-node CockroachDB instance:
```bash
cockroach.exe start-single-node --insecure --listen-addr=localhost:26257 --http-addr=localhost:8080
```

### Step 2: Initialize the Workload
Use the `cockroach workload` command to initialize the `movr` workload:
```bash
cockroach workload init movr "postgresql://root@localhost:26257?sslmode=disable"
```

### Step 3: Connect via Terminal
To connect to the CockroachDB instance, use:
```bash
cockroach sql --insecure --host=127.0.0.1
```

For more details, refer to the [link reference](https://www.cockroachlabs.com/docs/v21.1/cockroach-workload).

## Cockroach on windown and connect via WLS

```bash
cockroach.exe start-single-node --insecure --listen-addr=172.26.176.1:26257 --http-addr=172.26.176.1:8080
```