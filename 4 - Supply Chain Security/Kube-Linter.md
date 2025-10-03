# KubeLinter

```shell
curl -LO https://github.com/stackrox/kube-linter/releases/latest/download/kube-linter-linux.tar.gz

tar -xvf kube-linter-linux.tar.gz

mv kube-linter /usr/local/bin/

kube-linter lint nginx.yml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      affinity:                                               #added
        podAntiAffinity:                                      #added
          requiredDuringSchedulingIgnoredDuringExecution:     #added
          - labelSelector:                                    #added
              matchExpressions:                               #added
              - key: app                                      #added
                operator: In                                  #added
                values:                                       #added
                - nginx                                       #added
            topologyKey: "kubernetes.io/hostname"             #added
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        securityContext:
          readOnlyRootFilesystem: true
          runAsUser: 1000
          runAsNonRoot: true
```