During the helm deployment (before it failed !)

Add this to the 3 containers from the `hook-image-puller` ds:

```
securityContext:
            runAsUser: 1000
```

Delete the `hook-image-awaiter` job:
```
kubectl delete -n svc-jhub job hook-image-awaiter
```

Add the securityContext to the containers inside the 2 new deployments `hub` and `proxy`.

Get the fake LB IP and connect:
```
kubectl --namespace=svc-jhub get svc proxy-public
```

**TODO**:   
trying to redeploy the `hook-image-awaiter` deleted job and associated RBAC stuffs because
```
client.go:420: [debug] Watching for changes to Job hook-image-awaiter with timeout of 5m0s
client.go:445: [debug] Add/Modify event for hook-image-awaiter: MODIFIED
client.go:484: [debug] hook-image-awaiter: Jobs active: 1, jobs failed: 0, jobs succeeded: 0
client.go:454: [debug] Deleted event for hook-image-awaiter
client.go:220: [debug] Starting delete for "hook-image-puller" DaemonSet
client.go:220: [debug] Starting delete for "hook-image-awaiter" Role
client.go:220: [debug] Starting delete for "hook-image-awaiter" RoleBinding
client.go:220: [debug] Starting delete for "hook-image-awaiter" ServiceAccount
client.go:220: [debug] Starting delete for "hook-image-awaiter" Job
client.go:245: [debug] jobs.batch "hook-image-awaiter" not found
```

by  

```
kubectl delete -n svc-jhub job hook-image-awaiter && \
kubectl apply -f job-sa.yaml && \
kubectl apply -f job.yaml
```
