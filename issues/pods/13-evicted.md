# Pod Evicted

## What happened

I tested a workload under a constrained lab node and Kubernetes evicted a Pod when node pressure became high.

## Checks

```bash
kubectl get pods -n voting
kubectl describe pod <pod> -n voting
kubectl describe node <node>
```

## Lesson

Eviction is different from an application crash. I check node conditions and resource pressure before changing the container command.
