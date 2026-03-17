# Vault on OCP

## Prerequisities

1. OCP Cluster
2. [helm](https://helm.sh/docs/intro/install/)
3. [virtctl](https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/virtualization/getting-started#installing-virtctl_virt-using-the-cli-tools)

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

### Bare Metal Machine

1. Make a copy of existing MachineSet configuration:

```bash
MACHINESET=$(oc get machineset -n openshift-machine-api -o jsonpath='{.items[0].metadata.name}')
oc get machineset $MACHINESET -n openshift-machine-api -o yaml > scratch/baremetal-machineset.yaml
```

2. Edit `scratch/baremetal-machineset.yaml`

  - [ ] Delete `creationTimestamp`, `generation`, `resourceVersion`, `uid`
  - [ ] Set `.metadata.name` to `baremetal-machineset`
  - [ ] Set `.spec.replicas` to `1`
  - [ ] Set `.spec.selector.matchLabels["machine.openshift.io/cluster-api-machineset"]` to `baremetal-machineset`
  - [ ] Set `.spec.template.metadata.labels["machine.openshift.io/cluster-api-machineset"]` to `baremetal-machineset`
  - [ ] Set `.spec.template.spec.providerSpec.value.instanceType` to `m5zn.metal`

3. Create bare metal machine

```bash
oc create -f scratch/baremetal-machineset.yaml
```

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


