# Kubernetes Commands Reference

This is my day-to-day `kubectl` reference for this project and for basic Kubernetes administration.

## Cluster and context

```bash
kubectl version --client
kubectl cluster-info
kubectl get nodes
kubectl config current-context
kubectl config get-contexts
```

Use these first when I am not sure which cluster/context I am connected to.

## Namespace

```bash
kubectl get ns
kubectl get all -n voting
kubectl get resourcequota -n voting
kubectl get limitrange -n voting
```

## Apply and inspect

```bash
kubectl apply -k k8s/
kubectl diff -k k8s/
kubectl get -f k8s/deployments/vote.yaml -n voting
kubectl describe deployment vote -n voting
```

## Pods and logs

```bash
kubectl get pods -n voting
kubectl get pods -n voting -o wide
kubectl get pods -n voting --show-labels
kubectl logs deployment/vote -n voting
kubectl logs <pod> -n voting --previous
kubectl logs <pod> -n voting -f
kubectl exec -it <pod> -n voting -- /bin/sh
```

For `CrashLoopBackOff`, I normally check `describe`, current logs and `--previous` logs.

## Events

```bash
kubectl get events -n voting --sort-by=.lastTimestamp
kubectl get events --all-namespaces --sort-by=.lastTimestamp
```

Events are especially useful for image pull, scheduling and volume failures.

## Deployments and rollouts

```bash
kubectl get deployments -n voting
kubectl rollout status deployment/vote -n voting
kubectl rollout history deployment/vote -n voting
kubectl rollout undo deployment/vote -n voting
kubectl scale deployment/vote --replicas=3 -n voting
```

## StatefulSet and storage

```bash
kubectl get statefulsets -n voting
kubectl get pvc -n voting
kubectl get pv
kubectl describe pvc postgres-data-postgres-0 -n voting
```

The PostgreSQL StatefulSet creates a PVC from `volumeClaimTemplates`.

## Services

```bash
kubectl get svc -n voting
kubectl describe svc vote -n voting
kubectl get endpoints -n voting
kubectl get endpointslices -n voting
```

If a Service has no endpoints, check its selector against Pod labels.

## ConfigMaps and Secrets

```bash
kubectl get configmaps -n voting
kubectl describe configmap voting-config -n voting
kubectl get secrets -n voting
kubectl describe secret postgres-secret -n voting
```

Avoid printing real secret values in a shared terminal or committing them to Git.

## Resources and OOMKilled

```bash
kubectl top pods -n voting
kubectl top nodes
kubectl describe pod <pod> -n voting
```

For `OOMKilled`, compare actual usage with the container memory limit and then review application behaviour before increasing limits.

## HPA

```bash
kubectl get hpa -n voting
kubectl describe hpa vote -n voting
kubectl get deployment vote -n voting
```

The HPA example needs a metrics provider such as Metrics Server.

## RBAC

```bash
kubectl get serviceaccounts -n voting
kubectl get roles -n voting
kubectl get rolebindings -n voting
kubectl auth can-i get pods --as=system:serviceaccount:voting:voting-app -n voting
kubectl auth can-i delete pods --as=system:serviceaccount:voting:voting-app -n voting
```

The project ServiceAccount should be able to read Pods/ConfigMaps but not delete Pods.

## Nodes and scheduling

```bash
kubectl get nodes --show-labels
kubectl describe node <node>
kubectl get pods -n voting -o wide
```

Use these when a Pod is `Pending` because of resources, affinity, taints or tolerations.

## Ingress

```bash
kubectl get ingress -n voting
kubectl describe ingress voting-ingress -n voting
kubectl get ingressclass
```

For an Ingress problem, check the Ingress controller, Service, endpoints and Pod health in that order.

## Troubleshooting workflow

### ImagePullBackOff

```bash
kubectl get pods -n voting
kubectl describe pod <pod> -n voting
kubectl get events -n voting --sort-by=.lastTimestamp
```

Check image name, tag and registry access.

### CrashLoopBackOff

```bash
kubectl logs <pod> -n voting
kubectl logs <pod> -n voting --previous
kubectl describe pod <pod> -n voting
```

### OOMKilled

```bash
kubectl describe pod <pod> -n voting
kubectl top pod <pod> -n voting
```

Check memory usage versus the configured limit.

### Pending Pod

```bash
kubectl describe pod <pod> -n voting
kubectl get nodes
kubectl describe node <node>
```

Look for insufficient resources, affinity rules, taints/tolerations or PVC problems.

### Service not reachable

```bash
kubectl get svc -n voting
kubectl describe svc vote -n voting
kubectl get endpoints -n voting
kubectl get pods -n voting --show-labels
```

### Useful generic commands

```bash
kubectl get all -n voting
kubectl get <resource> <name> -n voting -o yaml
kubectl explain deployment.spec
kubectl delete -f <file> -n voting
```
