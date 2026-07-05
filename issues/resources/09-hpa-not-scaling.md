# HPA not scaling

## What happened

The HPA existed, but it did not get the CPU metrics needed to calculate scaling.

## Checks

```bash
kubectl get hpa -n voting
kubectl describe hpa vote -n voting
kubectl top pods -n voting
```

## Fix

I checked the Metrics Server in the lab cluster and made sure the workload had CPU requests configured.

## Lesson

Creating an HPA object alone is not enough. Metrics must be available and the workload needs sensible resource configuration.
