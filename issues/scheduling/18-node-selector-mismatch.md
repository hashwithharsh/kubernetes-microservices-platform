# Node selector mismatch

## What happened

A test workload used a node selector that no available node had.

## Checks

```bash
kubectl get nodes --show-labels
kubectl describe pod <pod> -n voting
```

## Lesson

Scheduling constraints are only useful when the cluster has matching node labels.
