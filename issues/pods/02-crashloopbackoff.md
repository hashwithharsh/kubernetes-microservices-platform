# CrashLoopBackOff

## What happened

A test container started and then exited because its startup command was wrong. Kubernetes kept restarting it and the Pod entered `CrashLoopBackOff`.

## Troubleshooting

```bash
kubectl logs <pod> -n voting
kubectl logs <pod> -n voting --previous
kubectl describe pod <pod> -n voting
```

## Fix

I corrected the container command and rolled out the Deployment again.

## Lesson

`CrashLoopBackOff` tells me the container is repeatedly failing; the logs normally give the application-level reason.
