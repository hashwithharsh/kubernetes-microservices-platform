# Kubernetes Troubleshooting Notes

These are lab/build troubleshooting notes for the Kubernetes voting app. They are examples reproduced while learning and testing the manifests, not claims of production incidents.

The normal flow I use is:

1. `kubectl get pods`
2. `kubectl describe pod`
3. `kubectl logs`
4. `kubectl logs --previous`
5. `kubectl get events`
6. Check Services/endpoints
7. Check resources and scheduling
8. Fix and verify the rollout

The issue folders cover Pods, networking, storage, resources and scheduling.
