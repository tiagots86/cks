# OPA - Open Policy Agent

```shell
export VERSION=v0.38.1
curl -L -o opa https://github.com/open-policy-agent/opa/releases/download/${VERSION}/opa_linux_amd64

chmod 755 ./opa

./opa run -s &
```

O Opa roda na porta 8081 e utiliza a linguagem REGO

A configuração é feita através da criação de Configmaps

# OPA Gatekeeper in Kubernetes

```yaml
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate                    
metadata:
  name: k8srequiredlabels
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels                                 
      validation:
        openAPIV3Schema:
          type: object
          properties:
            labels:
              type: array
              items:
                type: string
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |                                     
        package k8srequiredlabels

        violation[{"msg": msg, "details": {"missing_labels": missing}}] {
          provided := {label | input.review.object.metadata.labels[label]}
          required := {label | label := input.parameters.labels[_]}
          missing := required - provided
          count(missing) > 0
          msg := sprintf("you must provide labels: %v", [missing])
        }
---
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: require-tech-label
spec:
  match:
    namespaces: ["engineering"]
  parameters:
    labels: ["tech"]
```

```yaml
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata:
  name: k8sreplicalimits
  annotations:
    metadata.gatekeeper.sh/title: "Replica Limits"
    metadata.gatekeeper.sh/version: 1.0.1
    description: >-
      Requires that objects with the field `spec.replicas` (Deployments,
      ReplicaSets, etc.) specify a number of replicas within defined ranges.
spec:
  crd:
    spec:
      names:
        kind: K8sReplicaLimits
      validation:
        # Schema for the `parameters` field
        openAPIV3Schema:
          type: object
          properties:
            ranges:
              type: array
              description: Allowed ranges for numbers of replicas.  Values are inclusive.
              items:
                type: object
                description: A range of allowed replicas.  Values are inclusive.
                properties:
                  min_replicas:
                    description: The minimum number of replicas allowed, inclusive.
                    type: integer
                  max_replicas:
                    description: The maximum number of replicas allowed, inclusive.
                    type: integer
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8sreplicalimits

        object_name = input.review.object.metadata.name
        object_kind = input.review.kind.kind

        violation[{"msg": msg}] {
            spec := input.review.object.spec
            not input_replica_limit(spec)
            msg := sprintf("The provided number of replicas is not allowed for %v: %v. Allowed ranges: %v", [object_kind, object_name, input.parameters])
        }

        input_replica_limit(spec) {
            provided := input.review.object.spec.replicas
            count(input.parameters.ranges) > 0
            range := input.parameters.ranges[_]
            value_within_range(range, provided)
        }

        value_within_range(range, value) {
            range.min_replicas <= value
            range.max_replicas >= value
        }
---
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sReplicaLimits
metadata:
  name: replica-limits
spec:
  match:
    kinds:
      - apiGroups: ["apps"]
        kinds: ["Deployment"]
  parameters:
    ranges:
    - min_replicas: 2
      max_replicas: 5
```