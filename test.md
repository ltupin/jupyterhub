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
