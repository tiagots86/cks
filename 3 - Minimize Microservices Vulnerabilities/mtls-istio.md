# Configuring Pod to Pod Encryption with istio

```yaml
apiVersion: security.istio.io/v1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
```

```shell
kubectl label ns test istio-injection=enabled
```

"isto automatically configures workload sidecars to use mutual TLS when calling other workloads. By default, Istio configures the destination workloads using PERMISSIVE mode. When PERMISSIVE mode is enabled, a service can accept both plaintext and mutual TLS traffic. In order to only allow mutual TLS traffic, the configuration needs to be changed to STRICT mode"