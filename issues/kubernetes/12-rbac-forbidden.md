# RBAC returned Forbidden

## What happened

I tested the voting ServiceAccount with a command that was intentionally outside its Role permissions.

## Check

```bash
kubectl auth can-i delete pods --as=system:serviceaccount:voting:voting-app -n voting
```

The result was `no`.

## Lesson

The Forbidden response was expected. The Role only grants read access to Pods and ConfigMaps, which is preferable to giving the application broad permissions.
