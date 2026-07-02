# ImagePullBackOff

## What happened

I changed the vote image tag to a tag that was not available to my local Kubernetes cluster. The Pod stayed in `ImagePullBackOff`.

## What I checked

```bash
kubectl describe pod <pod> -n voting
kubectl get events -n voting --sort-by=.lastTimestamp
```

The events showed that the kubelet could not pull the requested image.

## Fix

I corrected the image name/tag and applied the Deployment again.

## What I learned

`ImagePullBackOff` is normally an image/registry/pull-credential problem, so I check `describe` and Events before changing the application.
