# ResourceQuota blocked a Pod

## What happened

I tested an additional workload after the namespace quota was already close to its configured limits. The API server rejected the request because it exceeded the namespace quota.

## Checks

```bash
kubectl get resourcequota -n voting
kubectl describe resourcequota voting-quota -n voting
```

## Lesson

ResourceQuota controls total namespace consumption. A valid Pod can still be rejected when the namespace has no remaining quota.
