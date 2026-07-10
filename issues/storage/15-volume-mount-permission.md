# Volume mount permission problem

## What happened

I tested a workload that expected to write to a mounted directory but the container user did not have the required permission.

## Checks

```bash
kubectl describe pod <pod> -n voting
kubectl logs <pod> -n voting
```

## Lesson

Persistent storage being mounted does not automatically mean the application user can write to it. I check the container user and mounted path permissions.
