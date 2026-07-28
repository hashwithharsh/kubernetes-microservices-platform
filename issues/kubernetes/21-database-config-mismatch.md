# Database configuration mismatch

## What happened

The first Kubernetes version used one PostgreSQL password in the Secret while the `result` and `worker` application code still had the sample password hardcoded. The worker also had a `DB_HOST` value that did not match the Kubernetes Service name.

## How I checked it

```bash
kubectl get secret postgres-secret -n voting
kubectl describe pod <result-pod> -n voting
kubectl logs <result-pod> -n voting
kubectl logs <worker-pod> -n voting
kubectl get svc -n voting
```

I then compared the Kubernetes configuration with the connection settings in `result/server.js` and `worker/Program.cs`.

## Root cause

The application was still using hardcoded local-development database values instead of the Kubernetes ConfigMap and Secret.

## Fix

The application now reads the database host, database name, user, and password from environment variables. The worker and result Deployments get those values from the ConfigMap and PostgreSQL Secret. The worker uses the `db` Service name.

## Verification

```bash
kubectl apply -k k8s/
kubectl get pods -n voting
kubectl logs deployment/worker -n voting
kubectl logs deployment/result -n voting
```

## What I learned

Kubernetes configuration should have one clear source of truth. If a Secret or ConfigMap is changed, application connection settings also need to consume those values instead of keeping another hardcoded copy.
