# What is ArgoCD
Argo CD is a Kubernetes-native continuous deployment (CD) tool. Unlike external CD tools that only enable push-based deployments, Argo CD can pull updated code from Git repositories and deploy it directly to Kubernetes resources.

# Install and Setup ArgoCD
You must have a running kubernetes cluster minikube.

## Create the namespace for argoCD
```kubectl create namespace argocd```

## Install ArgoCD using the below command
```bash 
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

After installing the ArgoCD, you can run the below command to check what resources it has created.
```bash 
kubectl get all -n argocd
```

## Access the Argo CD API server: 

By default, the Argo CD server is exposed as a ClusterIP service. To access the UI or CLI from outside the cluster, you might need to change the service type or use port forwarding.
- Port Forwarding (for quick access and testing):
```bash 
kubectl port-forward svc/argocd-server -n argocd 8088:443
```

This will forward local port 8088 to the Argo CD server's HTTPS port (443), allowing you to access the UI in your browser at `https://localhost:8088`.

## Retrieve the initial admin password: 
The default username is admin. The initial password is stored in a Kubernetes secret.

```bash 
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode ; echo
```

OR

```bash
 kubectl get secret argocd-initial-admin-secret -n argocd -o yaml

 echo "secret value" | base64 --decode
```

- After login I have changed password to `admin@123`

## View configuration if needed
```bash 
kubectl edit svc argocd-server -n argocd  #enable Node Port
```

# Apply the Argo CD Application

Once Argo CD is installed in your cluster (usually in argocd namespace):

```bash 
kubectl apply -f flask-redis-argocd-app.yaml
```

Argo CD will:

- Read your repo
- Apply `redis-deploy.yaml`, `hellodocker-deploy.yaml`, `hellodocker-service.yaml`
- Keep them synced with Git (auto-heal & prune if enabled)

You can then open the Argo CD UI (port-forward example):

```bash 
kubectl port-forward svc/argocd-server -n argocd 8089:443
```

Then go to: <https://localhost:8088> in your browser


# References
- https://medium.com/@veerababu.narni232/a-complete-overview-of-argocd-with-a-practical-example-f4a9a8488cf9
- https://medium.com/@muppedaanvesh/a-hands-on-guide-to-argocd-on-kubernetes-part-1-%EF%B8%8F-7a80c1b0ac98
