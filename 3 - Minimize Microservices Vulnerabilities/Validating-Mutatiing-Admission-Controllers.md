# Validation and Mutating Admission Controllers

- Denies all requests for pods to run as root in a container if no securityContext is provided.
- Defaults: If runAsNonRoot is not set, the webhook automatically adds runAsNonRoot: true and sets the user ID to 1234.
- Explicit root access: The webhook allows containers to run as root only if you explicitly set runAsNonRoot: false in the pod's securityContext.

```shell
kubectl create ns webhook-demo
```

```shell
kubectl -n webhook-demo create secret tls webhook-server-tls \
    --cert "/root/keys/webhook-server-tls.crt" \
    --key "/root/keys/webhook-server-tls.key"
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webhook-server
  namespace: webhook-demo
  labels:
    app: webhook-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webhook-server
  template:
    metadata:
      labels:
        app: webhook-server
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1234
      containers:
      - name: server
        image: stackrox/admission-controller-webhook-demo:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 8443
          name: webhook-api
        volumeMounts:
        - name: webhook-tls-certs
          mountPath: /run/secrets/tls
          readOnly: true
      volumes:
      - name: webhook-tls-certs
        secret:
          secretName: webhook-server-tls
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webhook-server
  namespace: webhook-demo
spec:
  selector:
    app: webhook-server
  ports:
    - port: 443
      targetPort: webhook-api
```

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: demo-webhook
webhooks:
  - name: webhook-server.webhook-demo.svc
    clientConfig:
      service:
        name: webhook-server
        namespace: webhook-demo
        path: "/mutate"
      caBundle: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURQekNDQWllZ0F3SUJBZ0lVSVFoU0o1bXg3bFdXQmg1UzNGRjB4dmt2Yk8wd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0x6RXRNQ3NHQTFVRUF3d2tRV1J0YVhOemFXOXVJRU52Ym5SeWIyeHNaWElnVjJWaWFHOXZheUJFWlcxdgpJRU5CTUI0WERUSTFNRGt6TURFME1qUXdObG9YRFRJMU1UQXpNREUwTWpRd05sb3dMekV0TUNzR0ExVUVBd3drClFXUnRhWE56YVc5dUlFTnZiblJ5YjJ4c1pYSWdWMlZpYUc5dmF5QkVaVzF2SUVOQk1JSUJJakFOQmdrcWhraUcKOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQXVJUVFEZnB4OWcyQWVkaGJCM1VuYVY1cnIvaGJHdUwra3BvSAoyYlNuVGxFZVRUUFB1VTZQdk9NbWJDU2FPak1Mc3ZnVEZwK05yMWd3aS95UkJlWG9tY2Q5c0ZDMHhnckRlUDAzCkxRVmJHSVNtbEFxK3Ira1FYZWtBL3ZLWUpDQVZJZGoxK0dGeGoxWW1ySTBUbmFwWUpjZE1Cb3A3TUpjbkdLZDAKd0gyY0JRbzVzZUZrNzZFVmJ2b0J3aE9mS1I5NXVhWkpiVkhwNUJwdS9BOVhCUHJCOFo1MHQzMlpibUtMU3VJLwpMZG9oYzhGM2ZYbHhFTHRFbkVtS0RnZTcrdklKbmg5Sk5TdVMvODhyeEdyVlB2VThoOWgvU29pS29WenZRanlkClZHWE5iUytnS2VXTGRGZUVvaEI0SHBVZGs1b1prcDh6djRzSXpyN1RkdDNReFpkVXJ3SURBUUFCbzFNd1VUQWQKQmdOVkhRNEVGZ1FVbVJBQW5za1FSL3kzRUd4UkRTN0Rpclgxb3Rnd0h3WURWUjBqQkJnd0ZvQVVtUkFBbnNrUQpSL3kzRUd4UkRTN0Rpclgxb3Rnd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBTkJna3Foa2lHOXcwQkFRc0ZBQU9DCkFRRUF0RkFNZENTZ1NXVGtKQ3NBOEw1VU41eVhtYWNta2hkeC83a0s0dUU4bVBDdHZOcHNrZTJTVFdLeHh3Qk0KOTNFeUVNaWk2dmQyMzBBTWFST2pHVnhDb2FFbVhTR2VKblFxRXZnYXpuZmhSRVdIM0RHRE5pOEtwaE9rREpBNQpoOEd0SUl4U0gvb3o4ajhJZzdDaWdxdFRqTEs3V2hrOG8yRDdrN3hyK2ZFM2czeGZRa3hLTlhUTHNXU216Nys1CkQ4b1lWaGlHRHhXRnhrUTY0Q01TRE1wekU2bDQyWjJsanozc3FrQ3NXa3lKaVRoeGc4SkxZSjBPVzdYdUNQVGMKeWJmS01QbzUyOVhoZ21aUC9ZTkZ1SFZZUFp4YWdOWHR2T3dTYVd3QWhSU3ZNWlhNN2pKNkVXUmEwSHFocEJDeApIYUQ3djZ4OVdlTWpDRHpqVjljVStIZEZhUT09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    rules:
      - operations: [ "CREATE" ]
        apiGroups: [""]
        apiVersions: ["v1"]
        resources: ["pods"]
    admissionReviewVersions: ["v1beta1"]
    sideEffects: None
```


