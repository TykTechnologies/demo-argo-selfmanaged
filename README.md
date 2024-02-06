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

### Log in to ArgoCD

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

### Create ArgoCD applications and deploy Tyk Self-Managed

* Clone this repository locally
* Replace ```YOUR-LICENSE-GOES-HERE``` with your Tyk self-managed license in ./staging/argo-applications-tyk/tyk-stack.yml. You can register for a free trial: https://tyk.io/sign-up/#self. 
* Start by deploying the dependencies (configuration, Redis, PostgreSQL):

```
cd ./staging/argo-applications-tyk
kubectl apply -f tyk-config-secrets.yml
kubectl apply -f tyk-redis.yml
kubectl apply -f tyk-postgres.yml
```

Wait for all the applications to be Healthy and Synched, then deploy Tyk Self-Managed:

```
kubectl apply -f tyk-stack.yml
```

![Argo admin UI (Tyk Self-Managed deployed)](https://github.com/TykTechnologies/demo-argo-selfmanaged/blob/main/img/argo_staging_tyk_stack.png)

### Try it out

Port forward Tyk Gateway: 

```
kubectl port-forward svc/gateway-svc-tyk-stack-tyk-gateway 8080:8080 -n tyk
```

Check that it is healthy by sending a request to the health endpoint: http://localhost:8080/hello.

Port forward Tyk Dashboard:

```
kubectl port-forward svc/dashboard-svc-tyk-stack-tyk-dashboard 3000:3000 -n tyk
```

Log into Tyk Dashboard: http://localhost:3000 (default@example.com / 123456 if you haven't changed the default from the Helm chart).


## Deploy production

### Switch to the staging cluster

```
kubectx production
```

### Install ArgoCD

Here are the commands needed to Install ArgoCD on your cluster. Refer to [ArgoCD documentation](https://argo-cd.readthedocs.io/en/stable/getting_started/) for more details. 

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Log in to ArgoCD

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

### Create ArgoCD applications and deploy Tyk Self-Managed

* Clone this repository locally
* Replace ```YOUR-LICENSE-GOES-HERE``` with your Tyk self-managed license in ./staging/argo-applications-tyk/tyk-stack.yml. You can register for a free trial: https://tyk.io/sign-up/#self. 
* Start by deploying the dependencies (configuration, Redis, PostgreSQL):

```
cd ./production/argo-applications-tyk
kubectl apply -f tyk-config-secrets.yml
kubectl apply -f tyk-redis.yml
kubectl apply -f tyk-postgres.yml
```

Wait for all the applications to be Healthy and Synched, then deploy Tyk Self-Managed:

```
kubectl apply -f tyk-stack.yml
```

![Argo admin UI (Tyk Self-Managed deployed)](https://github.com/TykTechnologies/demo-argo-selfmanaged/blob/main/img/argo_staging_tyk_stack.png)

### Try it out

Port forward Tyk Gateway: 

```
kubectl port-forward svc/gateway-svc-tyk-stack-tyk-gateway 8080:8080 -n tyk
```

Check that it is healthy by sending a request to the health endpoint: http://localhost:8080/hello.

Port forward Tyk Dashboard:

```
kubectl port-forward svc/dashboard-svc-tyk-stack-tyk-dashboard 3000:3000 -n tyk
```

Log into Tyk Dashboard: http://localhost:3000 (default@example.com / 123456 if you haven't changed the default from the Helm chart).


## Contributions welcome 

1. [Contribution guidelines](./CONTRIBUTING.md) 
2. [PR template](./.github/pull_request_template.md)
3. [Bug report template](./.github/ISSUE_TEMPLATE/bug_report.md)
4. [Feature request template](./.github/ISSUE_TEMPLATE/feature_request.md) 
5. [Contributor License Agreement](https://github.com/TykTechnologies/tyk/blob/master/CLA.md) - This will enforce your contributors to sign the CLA on the first time they submit a PR.
6. [License](./LICENSE)  *from tyk-gateway*
7. [Default Repo README](./.github/README-template.md), which resides in `/.github`
