# PVC stuck in Pending

## What happened

The PostgreSQL StatefulSet created a PVC, but the lab cluster did not have a matching dynamic storage provisioner.

## Checks

```bash
kubectl get pvc -n voting
kubectl describe pvc postgres-data-postgres-0 -n voting
kubectl get storageclass
```

## Fix

I enabled/used the storage class available in the local cluster and recreated the test claim.

## Lesson

A PVC is only a request. The cluster still needs a suitable StorageClass/PV to satisfy it.
