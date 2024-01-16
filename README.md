# Fluxy

This repo contains code for resources in kubernetes repo.

## Installation 

Flux is a tool to maintain Kubernetes cluster through a GitOps approach. So to being it needs two component 
1. Github (can be GitLab etc)
    1. Repo
    2. Github token which flux uses to pull and push artifacts from github
2. Kubernetes cluster: For this example we will be using a local Kubernetes Kind cluster


### Repo

This fluxy will be the github repo that will be used for GitOps deployment. We will [Bootstrap](https://fluxcd.io/flux/installation/bootstrap/) flux with this repo which basically means that flux will maintain itself using this repo. The next thing required is a token which we can get under Account > Settings > Developer Settings > Personal Access token > Fine-grained tokens. When creating a new token make sure to give Read and Write permission for contents under Repository Permission. Then export the token as local environment variable so that flux bootstrap cli command can pick it up 
```bash
export GITHUB_TOKEN={created in the above step}
export GITHUB_USER=<your-username>
```


### Kind cluster
For more info check [Kind Cluster](https://github.com/anityam/cluster)
```bash
kind create cluster --config Kind/Config.yaml
```

### Cli
Install the fluxcd cli to interact with the flux components in the cluster and also to bootstrap flux with the git repo created above

```bash
brew install fluxcd/tap/flux
# do a flight check for flux before installation 
flux check --pre
## bootstrapping flux components to the cluster using this repo
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=fluxy \
  --branch=main \
  --path=./clusters/my-cluster \
  --personal
```

Flux bootstrapping will install the flux component and sync them with this git repo so it can be managed in GitOps approach. To do the sync it will create a directory in the repo called [flux-system](./clusters/my-cluster/flux-system/) where a kustomization and GitRepository source are created for flux resources.
