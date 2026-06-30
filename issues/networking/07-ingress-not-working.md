# Ingress not working

## What happened

The Ingress resource existed, but the expected host did not return the application.

## Checks

```bash
kubectl get ingress -n voting
kubectl describe ingress voting-ingress -n voting
kubectl get svc -n voting
kubectl get endpoints -n voting
```

I also checked that an Ingress controller was installed and that the host resolved to its address.

## Lesson

Ingress is only one part of the path: controller → Ingress rule → Service → endpoints → Pod.
