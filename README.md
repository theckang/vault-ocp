# Vault on OCP

## Prerequisities

1. OCP Cluster with Bare Metal Node (see [here](https://github.com/theckang/mssql-ocp/blob/main/virt/README.md#bare-metal-machine) for AWS example)
2. [helm](https://helm.sh/docs/intro/install/)
3. [virtctl](https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/virtualization/getting-started#installing-virtctl_virt-using-the-cli-tools)

## Install Vault

Adapted from [official documentation](https://developer.hashicorp.com/vault/tutorials/kubernetes-platforms/kubernetes-openshift)

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
    --set "server.dev.enabled=true" \
    --set "injector.image.tag=latest" \
    --set "injector.agentImage.tag=latest" \
    --set "server.image.tag=latest"
```

## Configure Vault

> Note: Vault recommends \
[JWT authentication](https://developer.hashicorp.com/validated-patterns/vault/vault-pki-with-vso-and-openshift#choosing-which-method)\
 for centralized Vault deployments

> This shows Kubernetes authentication for demo purposes since Vault is running on the cluster

1. Exec into Vault pod

```bash
oc exec -it vault-0 -- /bin/sh
```

2. Enable authentication

```bash
$ vault auth enable kubernetes
```

```bash
$ vault write auth/kubernetes/config \
    kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443"
```

3. Exit

```bash
$ exit
```

## Install Vault Secrets Operator

1. Install subscription

```bash
oc apply -f - <<EOF
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: vault-secrets-operator
  namespace: openshift-operators
spec:
  channel: stable
  name: vault-secrets-operator
  source: certified-operators
  sourceNamespace: openshift-marketplace
  installPlanApproval: Automatic
EOF
```

2. Verify

> Note: Wait a few seconds to see output

```bash
oc get csv -n openshift-operators
oc get pods -n openshift-operators
```

## Virt PKI

### OpenShift Virtualization

1. Install [OpenShift Virtualization](https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/virtualization/installing)

2. Create HyperConvered Resource (use default)

3. Create a project

```bash
oc new-project virtapps
```

4. Create database VM and expose it

```bash
oc create -n virtapps -f virt/database.yaml
oc create -n virtapps -f virt/database-svc.yaml
```

5. Create windows app VM and expose it

```bash
oc create -n virtapps -f virt/winweb.yaml
oc create -n virtapps -f virt/winweb-svc.yaml
oc create -n virtapps -f virt/winweb-route.yaml
```

### Configure PKI for edge terminated ingress

### Configure PKI for passthrough ingress


