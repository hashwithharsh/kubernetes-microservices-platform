# Kubernetes Voting App

This project deploys the Docker Example Voting App on Kubernetes. The application source comes from the Docker Samples Example Voting App; my work here is focused on Kubernetes deployment, configuration, scheduling, resources, access control and troubleshooting.

## What I practiced

- Deployments and rolling updates
- StatefulSet and persistent storage for PostgreSQL
- Services and service discovery
- Ingress
- ConfigMaps and Secrets
- CPU/memory requests and limits
- Readiness and liveness probes
- Namespace ResourceQuota and LimitRange
- RBAC and ServiceAccounts
- HPA
- Node affinity
- Taints and tolerations
- kubectl troubleshooting

## Project layout

`k8s/` contains the manifests. `issues/` contains troubleshooting notes. `k8s_commands.md` is my command reference.

## Important project notes

The original voting application has some fixed connection settings. In this Kubernetes project the internal PostgreSQL Service is named `db` and Redis is named `redis` so the sample worker and result code can resolve their existing connection names.

## Before deploying

Build the Project 3 images and make them available to the Kubernetes cluster using the image names used in the manifests, or change the image values for your registry.

An Ingress controller and Metrics Server are required for the Ingress and HPA examples.

## Deploy

```bash
kubectl apply -f k8s/namespace/namespace.yaml
kubectl apply -k k8s/
kubectl get all -n voting
```

For a local cluster, add `vote.local` and `result.local` to your hosts file according to the cluster/Ingress address.

## Useful checks

```bash
kubectl get pods -n voting -o wide
kubectl get svc -n voting
kubectl get ingress -n voting
kubectl get pvc -n voting
kubectl get hpa -n voting
kubectl get events -n voting --sort-by=.lastTimestamp
```

## Cleanup

```bash
kubectl delete -k k8s/
kubectl delete namespace voting
```

The secret in this repository contains only a demo password. Never commit real credentials.
