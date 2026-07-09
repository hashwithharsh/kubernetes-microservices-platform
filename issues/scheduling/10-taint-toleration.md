# Pod not scheduling because of a taint

## What happened

A test node had a `NoSchedule` taint and the worker Pod did not have a matching toleration.

## Checks

```bash
kubectl describe node <node>
kubectl describe pod <pod> -n voting
```

## Fix

I added the matching toleration to the test worker workload.

## Lesson

Taints keep workloads away from a node; tolerations allow a matching Pod to be considered for that node. A toleration does not force scheduling by itself.
