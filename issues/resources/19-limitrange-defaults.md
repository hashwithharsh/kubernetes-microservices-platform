# LimitRange defaults were unexpected

## What happened

I created a test container without explicit resources and noticed that the namespace LimitRange supplied defaults.

## Checks

```bash
kubectl describe limitrange voting-defaults -n voting
kubectl get pod <pod> -n voting -o yaml
```

## Lesson

LimitRange can apply default requests and limits. I check the resulting Pod spec when resource values do not look like the original manifest.
