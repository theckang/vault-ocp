### Bare Metal Machine in AWS

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
