# Instalar e configurar o ArgoCD

Agora vamos abrir outra aba duplicada. Crie um novo namespace chamado argocd no seu cluster Kubernetes.

```bash
kubectl create namespace argocd
```

Instale o ArgoCD no namespace argocd aplicando o arquivo YAML do URL fornecido.

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Saber a versão do Argo instalada:

```bash
kubectl -n argocd get deployment argocd-server -o=jsonpath='{.spec.template.spec.containers[:1].image}'
```

Lista todos os recursos no namespace argocd . Ele fornece uma visão geral da configuração do ArgoCD, incluindo Pods, Services, Deployments, ReplicaSets e muito mais.

```bash
kubectl -n argocd get all
```

Edite a configuração do serviço de argocd-server.

```bash
kubectl -n argocd edit service argocd-server
```

Irá abir o VI, editr de texto

Altere o type de ClusterIP para NodePort . 

type: ClusterIP

type: NodePort

ClusterIP expõe o serviço somente dentro do cluster. 

NodePort torna o serviço acessível externamente por meio de uma porta específica nos nós.


Execute o comando kubectl -n argocd get all novamente para verificar se o tipo de serviço foi alterado ou não.

```bash
kubectl -n argocd get all
```

Execute o comando kubectl port-forward . 
Ele encaminha o tráfego da sua máquina local (porta 8080) para a argocd-serverporta do serviço 80no cluster Kubernetes. –address 0.0.0.0 

Garante que o serviço seja acessível de qualquer endereço IP, não apenas localhost.

```bash
kubectl port-forward -n argocd --address 0.0.0.0 svc/argocd-server 8080:80
```

Agora abra seu navegador e execute localhost:8080

Clique em Prosseguir para ….. para ir para a página inicial do ArgoCD.


## Recuperar a senha inicial 

Para recuperar a senha inicial do usuário admin no ArgoCD, você precisa saber o nome do deployment ou do pod que está executando o ArgoCD no seu cluster Kubernetes. A senha inicial é gerada automaticamente e armazenada no Kubernetes.

Aqui estão os passos para descobrir o pod correto e recuperar a senha inicial do ArgoCD:

1. Listar os pods no namespace argocd
Primeiro, liste os pods no namespace argocd para encontrar o pod que está executando o servidor ArgoCD:

```bash
kubectl get pods -n argocd

NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          31m
argocd-applicationset-controller-684cd5f5cc-ncjgv   1/1     Running   0          31m
argocd-dex-server-77c55fb54f-gjzd4                  1/1     Running   0          31m
argocd-notifications-controller-69cd888b56-vlv2c    1/1     Running   0          31m
argocd-redis-55c76cb574-w6qlw                       1/1     Running   0          31m
argocd-repo-server-584d45d88f-qkfq4                 1/1     Running   0          31m
argocd-server-8667f8577-mtjlq                       1/1     Running   0          31m
```

Você verá uma lista de pods. O nome do pod que você está procurando provavelmente será algo como argocd-server-xxxxxxx-xxxxx. 

O que você precisa é do pod que está executando o argocd-server.

2. Recuperar a senha inicial do ArgoCD

Uma vez que você encontrou o nome do pod correto, você pode recuperar a senha inicial do usuário admin a partir do secret gerado durante a instalação do ArgoCD. 

Execute o seguinte comando para obter a senha:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 --decode
```

0nhEE31KtmExQOUP