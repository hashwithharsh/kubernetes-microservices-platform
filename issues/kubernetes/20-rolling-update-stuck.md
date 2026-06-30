# Rolling update stuck

## What happened

I tested a Deployment update with a deliberately unhealthy new image. The rollout could not make the new Pod ready.

## Checks

```bash
kubectl rollout status deployment/vote -n voting
kubectl get pods -n voting
kubectl describe pod <pod> -n voting
kubectl rollout history deployment/vote -n voting
```

## Fix

I rolled back to the previous revision after confirming the new Pod was not becoming ready.

```bash
kubectl rollout undo deployment/vote -n voting
```

## Lesson

Readiness protects a rolling update, and rollout history gives me a straightforward recovery path.
