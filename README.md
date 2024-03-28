# Demo: deploying Tyk self-managed platform with ArgoCD

Warning: this is a demo project - not production ready. 


Follow along to deploy 2 environments with ArgoCD and Tyk Self-Managed.
You will need a license for Tyk Self-Managed. You can register for a free trial: https://tyk.io/sign-up/#self. 

This demo is based on the blog post: 
* [Deploying an API gateway to Kubernetes with ArgoCD](https://tyk.io/blog/deploying-api-gateway-kubernetes-with-argocd/)

## Create local Kubernetes cluster for staging and production

In this demo, we will assume 2 environments (staging and prod) running in minikube:

```
minikube start -p staging
minikube start -p production
```

Later, to list the clusters:

```
minikube profile list
```

Then to switch cluster use kubectx:

```
kubectx staging
kubectx production
```

## Deploy staging

Follow the steps from [./staging/README.md](./staging/README.md)

## Deploy production

Follow the steps from [./production/README.md](./production/README.md)


## Contributions welcome 

1. [Contribution guidelines](./CONTRIBUTING.md) 
2. [PR template](./.github/pull_request_template.md)
3. [Bug report template](./.github/ISSUE_TEMPLATE/bug_report.md)
4. [Feature request template](./.github/ISSUE_TEMPLATE/feature_request.md) 
5. [Contributor License Agreement](https://github.com/TykTechnologies/tyk/blob/master/CLA.md) - This will enforce your contributors to sign the CLA on the first time they submit a PR.
6. [License](./LICENSE)  *from tyk-gateway*
7. [Default Repo README](./.github/README-template.md), which resides in `/.github`
