# Vault on OCP

## Prerequisities

1. OCP Cluster
2. helm

## Install

1. Create namespace

```bash
oc create project vault
```

2. Install Vault

```bash
helm install vault hashicorp/vault \
    --namespace vault \
    --set "global.openshift=true" \
    --set "injector.image.tag=latest" \
    --set "injector.agentImage.tag=latest" \
    --set "server.image.tag=latest"
```

## Virt PKI

### Configure PKI for edge terminated ingress

### Configure PKI for passthrough ingress
