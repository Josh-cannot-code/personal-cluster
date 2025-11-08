# personal-cluster

## Notes for minikube
```
minikube addons enable storage-provisioner
minikube addons enable default-storageclass
```

## From scratch

1. Install ArgoCD
    ```
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
2. Login to argo
    ```
    # Forward the API locally
    kubectl -n argocd port-forward svc/argocd-server 8080:443

    # Get initial admin password (first-time installs)
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

    # Log in through the forwarded port
    argocd login localhost:8080 --username admin --password "<the-password>" --insecure --grpc-web

    # Now list clusters
    argocd cluster list
    ```
3. Create infiscal secret so secrets can sync properly
    ```
    kubectl create secret generic service-token --from-literal=infisicalToken="<your-service-token-here>"
    ```
4. Install open iscsi on the host
    for minikube:
    ```
    minikube ssh "sudo apt-get update;sudo apt-get install -y open-iscsi"
    ```
5. Create application
    ```
    argocd app create apps \
          --dest-namespace argocd \
          --dest-server https://kubernetes.default.svc \
          --repo https://github.com/Josh-cannot-code/personal-cluster.git \
          --path .
    argocd app sync apps
    ```

## Node Affinity for jellyfin
Add `kubectl label nodes <your-node-name> compute=true` to prefer jellyfin scheduling node
