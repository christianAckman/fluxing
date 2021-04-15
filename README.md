# infra

### Directories:
- `/clusters/${cluster}/flux-system`
    - Flux configurations, generated from `flux bootstrap`
- `/clusters/${cluster}/apps.yaml`
    - Configuration to watch `apps/${cluster}/` directory
- `/clusters/${cluster}/uat.yaml`
    - Configuration to watch `apps/` directory on `uat` branch
- `/apps/`
    - Helm releases & repos for each cluster

---

### Setup:
```
kind create cluster --name kind
kind create cluster --name kind2

export GITHUB_TOKEN=xxx
export GITHUB_USER=xxx


## set context
kubectx kind-kind

## install flux & setup repo
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=infra \
  --branch=master \
  --path=/clusters/kind \
  --personal \
  --private


## set context
kubectx kind-kind2

## install flux & setup repo
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=infra \
  --branch=master \
  --path=/clusters/kind2 \
  --personal \
  --private
```

## Commands:

### Manually sync git & cluster
```
flux reconcile source git flux-system

// cluster1
flux reconcile kustomization kind
flux reconcile helmrelease kind

// cluster2
flux reconcile kustomization kind2
flux reconcile helmrelease kind2
```

### Other
```
flux check --pre
flux install
flux check
flux uninstall --namespace=flux-system
```
