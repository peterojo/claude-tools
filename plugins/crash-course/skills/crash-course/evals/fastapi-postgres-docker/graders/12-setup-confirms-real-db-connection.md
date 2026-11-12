---
type: llm
focus: trace
weight: 1
---

setup.sh reports success only after a real client connection to Postgres (for example SELECT 1 over TCP) succeeds, not just a container healthcheck
