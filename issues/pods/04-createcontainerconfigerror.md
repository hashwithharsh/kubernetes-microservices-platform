# CreateContainerConfigError

## What happened

A test Deployment referenced a configuration key that did not exist in the ConfigMap.

## Checks

```bash
kubectl describe pod <pod> -n voting
kubectl get configmap voting-config -n voting -o yaml
```

## Fix

I corrected the ConfigMap key reference and reapplied the manifest.

## Lesson

For configuration errors, I compare the Pod's `env`/volume references with the actual ConfigMap or Secret keys.
