# Demo: deploying Tyk self-managed platform with ArgoCD

Warning: this is a demo project - not production ready. 

Follow along to deploy 2 environments with ArgoCD and Tyk Self-Managed.
You will need a license for Tyk Self-Managed. You can register for a free trial: https://tyk.io/sign-up/#self. 

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

### Switch to the staging cluster

```
kubectx staging
```

### Install ArgoCD

Here are the commands needed to Install ArgoCD on your cluster. Refer to [ArgoCD documentation](https://argo-cd.readthedocs.io/en/stable/getting_started/) for more details. 

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Log in to ArgoCD

Forward the local port 9080 to ArgoCD's secure port (443) to access ArgoCD UI interface:

```
kubectl port-forward svc/argocd-server -n argocd 9080:443
```

[Download and install Argo CD CLI](https://argo-cd.readthedocs.io/en/stable/getting_started/#2-download-argo-cd-cli) and retrieve default admin password:

```
argocd admin initial-password -n argocd
```

* Argo admin UI: https://localhost:9080/applications (user: admin)

![Argo admin UI (empty)](https://github.com/TykTechnologies/demo-argo-selfmanaged/blob/main/img/argo_staging_empty.png)



## Create ArgoCD applications

Replace ```YOUR-LICENSE-GOES-HERE``` with your Tyk self-managed license in ./staging/argo-applications-tyk/tyk-stack.yml. You can register for a free trial: https://tyk.io/sign-up/#self. 

```
cd ./staging/argo-applications-tyk
kubectl apply -f tyk-config-secrets.yml
kubectl apply -f tyk-redis.yml
kubectl apply -f tyk-postgres.yml
kubectl apply -f tyk-stack.yml
```


## Try it out

### Tyk

Forward the port 8080:

```
kubectl port-forward svc/gateway-svc-tyk-gateway-application -n tyk 8080:8080
```

* Tyk health endpoint: http://localhost:8080/hello
* go-httpbin: http://localhost:8080/httpbin/get



## Deploy production



## Contributions welcome 

1. [Contribution guidelines](./CONTRIBUTING.md) 
2. [PR template](./.github/pull_request_template.md)
3. [Bug report template](./.github/ISSUE_TEMPLATE/bug_report.md)
4. [Feature request template](./.github/ISSUE_TEMPLATE/feature_request.md) 
5. [Contributor License Agreement](https://github.com/TykTechnologies/tyk/blob/master/CLA.md) - This will enforce your contributors to sign the CLA on the first time they submit a PR.
6. [License](./LICENSE)  *from tyk-gateway*
7. [Default Repo README](./.github/README-template.md), which resides in `/.github`
