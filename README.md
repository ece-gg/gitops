# GitOps


## Bootstrapping

First, you'll need to bootstrap the install:

```
helm dependency build argo-cd
helm template argo-cd argo-cd --namespace argo-cd > bootstrap.yaml
kubectl apply -f bootstrap.yaml
rm bootstrap.yaml
```

Now, you can set up the initial application with the username `admin` and the
following password from https//localhost:8080:

```
kubectl get secret argocd-initial-admin-secret -n argo-cd -o jsonpath={.data.password} | base64 -d
kubectl port-forward svc/argo-cd-argocd-server -n argo-cd 8080:443
```

## Examples

### `cert-manager`

```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-production
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-production
    solvers:
    - dns01:
        cloudflare:
          apiTokenSecretRef:
            name: cloudflare
            key: api-token
```

```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - dns01:
        cloudflare:
          apiTokenSecretRef:
            name: cloudflare
            key: api-token
```

```
apiVersion: v1
kind: Secret
metadata:
  name: cloudflare
  namespace: cert-manager
type: Opaque
stringData:
  api-token: <API_TOKEN>
```

### `cnpg`

```
apiVersion: v1
kind: Secret
metadata:
  name: cnpg-<USERNAME>
  namespace: cnpg
  labels:
    cnpg.io/reload: "true"
type: kubernetes.io/basic-auth
stringData:
  username: <USERNAME>
  password: <PASSWORD>
```

### `compeng`

```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-production
  namespace: compeng
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-production
    solvers:
    - dns01:
        cloudflare:
          apiTokenSecretRef:
            name: cloudflare
            key: api-token
```

```
apiVersion: v1
kind: Secret
metadata:
  name: cloudflare
  namespace: compeng
type: Opaque
stringData:
  api-token: <API_TOKEN>
```
