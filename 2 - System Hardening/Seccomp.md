# Seccomp

```shell
strace -c ls /root
```

default Seccomp profile: /var/lib/kubelet/seccomp

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: audit-nginx
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/audit.json
  containers:
  - image: nginx
    name: nginx
```