# Flux Workshop - Nerdearla 2023

Este repo es complementario al workshop "Introducción a GitOps con Flux", dado en la Nerdearla 2023.

## Requisitos

-   Kubernetes - Con [colima](https://github.com/abiosoft/colima#installation) o [kind](https://kind.sigs.k8s.io/docs/user/quick-start/) está bien
-   [kubectl](https://kubernetes.io/docs/tasks/tools/)
-   [flux](https://fluxcd.io/docs/installation/#install-the-flux-cli)
-   [gitops](https://docs.gitops.weave.works/docs/installation/weave-gitops/) CLI - (Opcional)
-   Github personal access token (PAT) - https://github.com/settings/tokens

### Mi setup

-   colima 0.5.5
-   kubectl 1.28.2
-   flux 2.1.1

## Crear cluster

```bash
colima start --kubernetes --cpu 4 --memory 8 --profile flux-demo

# kind
kind create cluster --name flux-demo
```

Check la creación del cluster

```bash
kubectl get nodes --watch
```

## Instalar Flux en el cluster

Primero vas a necesitar un token personal de Github con todos los permisos del scope `repo` - https://github.com/settings/tokens.

<img src="github-token.png" height="400px">

```bash
export GITHUB_USER=<github-user>
export GITHUB_TOKEN=<github-token>
```

### Chequear compatibilidad de flux con nuestro cluster

```bash
flux check --pre

► checking prerequisites
✔ Kubernetes 1.28.1+k3s1 >=1.25.0-0
✔ prerequisites checks passed
```

### Flux bootstrap

```bash
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=flux-workshop-nerdearla \
  --branch=main \
  --path=./clusters/dev \
  --personal
```

<details>
<summary>Output</summary>

```bash
► connecting to github.com
► cloning branch "main" from Git repository "https://github.com/marcorichetta/flux-workshop-nerdearla.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed sync manifests to "main" ("128f2feb9053ed1863b3e5a8c14cf65488512b12")
► pushing component manifests to "https://github.com/marcorichetta/flux-workshop-nerdearla.git"
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
► generating source secret
✔ public key: ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBNf54MZoM/zhM6DdYxJwyHf8PY2B6+VVobMzy+2kmpP0jSfFe4alVUbW0t/ljzeJyss5zj75Raw31z7LC7dH8LJZBzamVCmvX4g80S1EUzkbBLkhj0y4++YlLhcE51/19g==
✔ configured deploy key "flux-system-main-flux-system-./clusters/dev" for "https://github.com/marcorichetta/flux-workshop-nerdearla"
► applying source secret "flux-system/flux-system"
✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ committed sync manifests to "main" ("0426a12bd7de1dc4186311a6bd5f8835d1340212")
```

</details>

Ver como se levanta flux

```bash
kubectl get pods -n flux-system -w
```

Traerse los manifiestos commiteados por flux

```bash
git pull

ls --tree clusters
 clusters
└──  dev
    └──  flux-system
        ├──  gotk-components.yaml
        ├──  gotk-sync.yaml
        └──  kustomization.yaml
```
