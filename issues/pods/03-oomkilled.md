# OOMKilled

## What happened

I tested a small memory limit on a workload and the container was killed after it exceeded the configured limit.

## Checks

```bash
kubectl describe pod <pod> -n voting
kubectl top pod <pod> -n voting
```

## Fix

I reviewed the request/limit values and increased the limit only after checking actual usage.

## Lesson

A limit is an upper bound for the container. Increasing it blindly can hide an application problem, so I check usage first.
