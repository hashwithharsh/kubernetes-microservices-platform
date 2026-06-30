# Service targetPort was wrong

## What happened

I tested a Service with a targetPort that did not match the container port. The Service existed but requests did not reach the application.

## Checks

```bash
kubectl describe svc <service> -n voting
kubectl get endpoints <service> -n voting
kubectl get pods -n voting
```

## Lesson

A Service port and the Pod's listening port are separate values. I check both when debugging connection failures.
