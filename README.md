# personal-cluster

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
