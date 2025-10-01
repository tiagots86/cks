# Configuring Pod to Pod Encryption with Cilium

## Add the Cilium Helm repository

```shell
helm repo add cilium https://helm.cilium.io/
```

## Install Cilium with encryption enabled

```shell
helm install cilium cilium/cilium --version v1.18.0-pre.0 \
  --namespace kube-system \
  --set encryption.enabled=true \
  --set encryption.type=wireguard
```
