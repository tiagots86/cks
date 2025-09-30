# Pod Security Admission

Existem 03 perfils:

- Privileged: unrestricted policy
- Baseline: minimal restrictive policy
- Restricted: heavily restricted policy

Existem 03 modos:

- enforce: reject pod
- audit: record in the audit logs
- warn: trigger user-facing warning

Para habilitar, é preciso inserir uma label no namespace:

```shell
kubectl label ns payroll pod-security.kubernetes.io/enforce=restricted
```

