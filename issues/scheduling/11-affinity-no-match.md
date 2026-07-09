# Node affinity prevented scheduling

## What happened

A test Deployment had a required node label that did not exist on the available nodes.

## Checks

```bash
kubectl get nodes --show-labels
kubectl describe pod <pod> -n voting
```

## Fix

I changed the test to use a label that existed in the lab or removed the strict requirement.

## Lesson

Affinity rules are scheduling constraints, so I check node labels before assuming the cluster is broken.
