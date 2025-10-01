# Container Sandboxing

## gVisor

Handler: runsc

## Kata Containers

Handler: kata

## Runtime Classes

```yaml
apiVersion: node.k8s.io/v1beta1
kind: RuntimeClass
metadata:
  name: gvisor
handler: runsc
```

No Pod então definimos o atributo runtimeClassName, que pode ser gvisor ou kata.

Observação: Containerd usa o runc como runtime

Maiores informações:

https://kubernetes.io/docs/concepts/containers/runtime-class/