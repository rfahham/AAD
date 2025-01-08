# Configurar o SETUP

## LOCAL

1 - Instalar o Docker Desktop

2 - Instalar o k3d - https://k3d.io/stable/

3 - Criar o Cluster

```bash
k3d cluster create login
```

Listar os clusters

kubectx
docker-desktop
k3d-login

Listar os Nodes

```bash
kubectl get nodes
```

4 - Deletar o cluster

```bash
k3d cluster delete login
```


## CLOUD

Criar o Cluster
