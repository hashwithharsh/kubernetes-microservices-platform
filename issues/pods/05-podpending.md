# Pod stuck in Pending

## What happened

I tested a workload with resource requirements that the available node could not satisfy.

## Checks

```bash
kubectl describe pod <pod> -n voting
kubectl get nodes
kubectl describe node <node>
```

## Fix

I adjusted the test workload's resource request to fit the lab cluster and checked scheduling again.

## Lesson

A Pending Pod is a scheduling problem until proven otherwise. Events in `describe` are a good first place to look.
