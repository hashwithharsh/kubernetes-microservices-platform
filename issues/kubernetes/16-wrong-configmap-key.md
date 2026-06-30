# Wrong ConfigMap key

## What happened

A test Pod referenced a ConfigMap key that did not exist, so the container could not build its environment correctly.

## Checks

```bash
kubectl describe pod <pod> -n voting
kubectl get configmap voting-config -n voting -o yaml
```

## Lesson

I compare the exact key names in the manifest and ConfigMap instead of assuming the configuration object is correct.
