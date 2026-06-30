# Service had no endpoints

## What happened

A test Service had a selector that did not match the labels on the vote Pods.

## Checks

```bash
kubectl get svc vote -n voting
kubectl get endpoints vote -n voting
kubectl get pods -n voting --show-labels
```

## Fix

I corrected the selector so it matched `app: vote`.

## Lesson

When a Service exists but traffic does not reach Pods, I check selectors and endpoints before changing networking configuration.
