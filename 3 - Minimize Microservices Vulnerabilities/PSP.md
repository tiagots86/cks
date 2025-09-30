# Pod Security Policies

Ele foi depreciado em versões 1.21 e removido em versões 1.25, para habilitá-lo precisamos adicionar a seguinte flag no api server:

```yaml
- --enable-admission-plugins=PodSecurityPolicy
```

Na sequência você cria um recurso do tipo PodSecurityPolicy e em Spec você configura as regras permitidas, por exemplo privileged: false, runAsUser: rule: 'MustRunAsNonRoot'.

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted-psp
spec:
  privileged: false
  hostNetwork: false
  hostPID: false
  hostIPC: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'RunAsAny'
  fsGroup:
    rule: 'RunAsAny'
  readOnlyRootFilesystem: false
```

Observação: Para funcionar é preciso criar uma role e rolebiding para o service do PSP, o que gera complexidade.