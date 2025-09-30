# Security Contexts

```yaml
securityContext:
    runAsUser: 1000
    capabilities:
        add: ["MAC_ADMIN"]
```

- Usuários é suportado tanto a nível de POD como Container, então Container sobresai.
- Capabilities só é suportado a nível de container.