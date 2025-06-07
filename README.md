# GitOps

First, you'll need to bootstrap the install:

```
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm template --namespace "argo-cd" "argo-cd" argo/argo-cd > bootstrap.yaml
kubectl apply -f bootstrap.yaml
rm bootstrap.yaml
```

Now, you can set up the initial application with the username `admin` and the
following password from https//localhost:8080:

```
kubectl get secret argocd-initial-admin-secret -n argo-cd -o jsonpath={.data.password} | base64 -d
kubectl port-forward svc/argo-cd-argocd-server -n argo-cd 8080:443
```
