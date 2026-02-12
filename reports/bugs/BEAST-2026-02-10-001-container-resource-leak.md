---
title: "Container Resource Leak"
date: 2026-02-10
category: bugs
tags: [docker, infrastructure, beast]
author: Beast Infrastructure
status: resolved
---

# Bug Report: Testing Container Resource Leak

**Report ID:** BEAST-2026-02-10-001
**Severity:** High
**Environment:** Beast (91GB RAM, 24-core AMD Ryzen AI 9 HX 370, Ubuntu 24.04)
**Date:** 2026-02-10
**Reporter:** Beast Infrastructure

---

## Summary

Testing containers from Zephyr E2E and distributed chaos testing accumulated without cleanup, causing system resource exhaustion and hung containers requiring Docker daemon restart.

---

## Affected Components

| Component | Image | Issue |
|-----------|-------|-------|
| Zephyr E2E | zephyr-e2e-linux:latest | Container stuck, unresponsive to stop/kill |
| Distributed Chaos | dist-chaos-* | 4 containers running 37+ hours |
| Chaos Test Images | chaos-* | 108 orphaned images (~13GB) |

---

## Symptoms

1. **Hung container:** `modest_ellis` (Zephyr E2E) running 18 hours, would not respond to:
   - `docker stop`
   - `docker kill`
   - `docker rm -f`
   - `docker kill --signal=SIGKILL`

2. **Image accumulation:** 108 chaos test images from ~36 test runs:
   - Each run creates 3 images: client-a, client-b, relay (~120MB each)
   - Total bloat: ~13GB
   - Images named: `chaos-{uuid}-{role}:latest`

3. **Long-running test containers:**
   - `dist-chaos-client-beast-1` - 37 hours
   - `dist-chaos-relay-{1,2,3}-1` - 37 hours

---

## Root Cause Analysis

1. **No cleanup hooks:** Test frameworks not cleaning up containers/images on completion or failure
2. **No TTL:** Containers lack `--rm` flag or max runtime limits
3. **Stuck process:** Zephyr container's entrypoint (`/entrypoint.sh regr…`) hung in uninterruptible state (likely FUSE mount)

---

## Impact

- Docker daemon required restart to recover
- 36 containers total before cleanup (should be ~14)
- ~15GB disk consumed by test artifacts
- System performance degradation

---

## Resolution Applied

```bash
sudo systemctl restart docker
docker container prune -f
docker images | grep "^chaos-" | xargs docker rmi -f
docker rmi zephyr-e2e-linux:latest dist-chaos-*:latest
docker system prune -f
```

---

## Recommendations

### 1. Zephyr E2E

- Add `--rm` flag to container runs
- Add timeout/watchdog to entrypoint script
- Investigate why container becomes unresponsive (FUSE mount issue)

### 2. Chaos Testing (0k-sync)

- Add post-test cleanup script: `docker rmi $(docker images -q 'chaos-*')`
- Use `--rm` for all test containers
- Consider using a dedicated test image tag that gets pruned daily

### 3. Infrastructure

- Add cron job: `0 4 * * * docker system prune -f --filter "until=24h"`
- Monitor container count with Prometheus alert (threshold: >20)
- Add disk usage alert for Docker storage

---

## Files for Reference

- Chaos compose: `~/0k-sync/tests/chaos/docker-compose.chaos.yml`
- Distributed compose: `~/0k-sync/tests/chaos/docker-compose.distributed.yml`
- Zephyr entrypoint: `automation/beast/entrypoint.sh`
- Zephyr run script: `automation/beast/run-tests.sh`
- Zephyr image: `zephyr-e2e-linux:latest` (1.37GB)

---

## Status

**Resolved** (manual cleanup)

**Follow-up Required:** Implement automated cleanup in test frameworks
