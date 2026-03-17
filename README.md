# Vault on OCP

## Prerequisities

1. OCP Cluster with Bare Metal Node (see [here](baremetal/aws.md) for AWS example)
2. [helm](https://helm.sh/docs/intro/install/)
3. [virtctl](https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/virtualization/getting-started#installing-virtctl_virt-using-the-cli-tools)

## Install

1. Create namespace

```bash
oc new-project vault
```

2. Install Vault

```bash
helm repo add hashicorp https://helm.releases.hashicorp.com
```

```bash
helm install vault hashicorp/vault \
    --namespace vault \
    --set "global.openshift=true" \
    --set "injector.image.tag=latest" \
    --set "injector.agentImage.tag=latest" \
    --set "server.image.tag=latest"
```

## Virt PKI

### OpenShift Virtualization

1. Install [OpenShift Virtualization](https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/virtualization/installing)

2. Create HyperConvered Resource (use default)

3. Create a project and ssh key

```bash
oc new-project virtapps
oc create secret generic authorized-keys --from-file=ssh-publickey=$HOME/.ssh/id_rsa.pub
```
4. Create a VM

> TODO: Windows

```bash
oc create -f virt/win-vm.yaml
```

5. Expose the VM

```bash
oc create -f virt/win-vm-svc.yaml
```

6. SSH into the VM

> TODO: Install a web server


### Configure PKI for edge terminated ingress

### Configure PKI for passthrough ingress


