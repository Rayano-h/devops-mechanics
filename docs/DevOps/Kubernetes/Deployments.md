# Deployments

!!! note "Définition"
    Un Deploymet permet de déclarer l'état souhaité detes Pods Kubernetes.


## Créer un Deployment

!!! tip "Bonne patrique"
    Utilise toujours 'kubectl apply -f' plutôt que 'kubectl create' pour pouvoir mettre à jour facilement.

!!! warning "Attention"
    Ne modifie jamais directementles Pods créés par un Deployment, ils seront recréés.


## Architecture Kubernetes

```mermaid
flowchart LR
    A[Utilisateur] --> B[Ingress]
    B --> C[Service]
    C --> D[Pod 1]
    C --> E[Pod 2]
    C --> F[Pod 3]
```