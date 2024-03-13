# Deploy production

## Switch to the staging cluster

```
kubectx production
```

## Install ArgoCD

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

## Create ArgoCD applications and deploy Tyk Self-Managed

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

## Deploy Tyk Operator 

Configure an Argo CD application to deploy Tyk Operator and Cert Manager. Tyk Operator enables the management of Tyk API Gateway within Kubernetes, and Cert Manager handles SSL/TLS for secure communication. 

```
kubectl apply -f application-cert-manager.yaml
kubectl apply -f application-tyk-operator.yaml
```

## Sync API Definitions

API Definitions are going to be stored into the direction ./api-definitions. Let's create an Argo app to synced the API Definitions automatically from GitHub:

```
kubectl apply -f application-api-definitions.yaml
```

### Try it out

```
http://localhost:8080/httpbin/get
```