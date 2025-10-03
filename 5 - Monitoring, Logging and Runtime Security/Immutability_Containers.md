# Immutability of Containers at Runtime

```yaml
Spec:
    containers:
        securityContext:
            readOnlyRootFilesystem: true
            Priviledged: false
            runAsUser: 0
```

Para evitar erros, podemos montar volumes para as áreas necessárias do POD

