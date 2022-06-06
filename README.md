# ArgoCD on k3d

```bash
# k3d-create:
k3d cluster create $(CLUSTER) --servers 1 --agents 2 --port "80:80@loadbalancer" \
--port "443:443@loadbalancer" --api-port 6443 --k3s-arg "--disable=traefik@server:0" --port "30000-30010:30000-30010@server:0"

# istio-asm-install
cd istio-1.10.2-asm.3
bin/istioctl x precheck
bin/istioctl install --set profile=default -y
kubectl label namespace default istio-injection=enabled

# argocd-install:
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# argocd-fwd:
nohup kubectl port-forward svc/argocd-server -n argocd 8443:443 &

# argocd-pass:
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d | pbcopy

# argocd-add-repo
kubectl apply -f application.yaml
```
