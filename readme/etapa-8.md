Aqui está o manual corrigido:

---

# Testando o Aplicativo

1. **Verificando o Status dos Pods**:
   - Abra uma nova aba e execute o seguinte comando para verificar o status dos pods em seu cluster:

     ```bash
     kubectl -n argocd get pods
     ```

   - Isso irá mostrar todos os pods em execução no namespace `argocd`. O output deve ser algo como:

     ```
     NAME                                                READY   STATUS    RESTARTS   AGE
     argocd-application-controller-0                     1/1     Running   0          3h33m
     argocd-applicationset-controller-684cd5f5cc-ncjgv   1/1     Running   0          3h33m
     argocd-dex-server-77c55fb54f-gjzd4                  1/1     Running   0          3h33m
     argocd-notifications-controller-69cd888b56-vlv2c    1/1     Running   0          3h33m
     argocd-redis-55c76cb574-w6qlw                       1/1     Running   0          3h33m
     argocd-repo-server-584d45d88f-qkfq4                 1/1     Running   0          3h33m
     argocd-server-8667f8577-mtjlq                       1/1     Running   0          3h33m
     ```

2. **Verificando as Implantações**:
   - Para ver as implantações (deployments) no namespace `argocd`, execute o seguinte comando:

     ```bash
     kubectl -n argocd get deployments
     ```

   - O resultado deve ser similar a:

     ```
     NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
     argocd-applicationset-controller   1/1     1            1           3h33m
     argocd-dex-server                  1/1     1            1           3h33m
     argocd-notifications-controller    1/1     1            1           3h33m
     argocd-redis                       1/1     1            1           3h33m
     argocd-repo-server                 1/1     1            1           3h33m
     argocd-server                      1/1     1            1           3h33m
     ```

3. **Verificando os Serviços Disponíveis**:
   - Para listar todos os serviços disponíveis no namespace `argocd`, execute o comando:

     ```bash
     kubectl -n argocd get services
     ```

   - O resultado deve ser similar a:

     ```
     NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
     argocd-applicationset-controller          ClusterIP   10.43.82.84     <none>        7000/TCP,8080/TCP            3h34m
     argocd-dex-server                         ClusterIP   10.43.89.238    <none>        5556/TCP,5557/TCP,5558/TCP   3h34m
     argocd-metrics                            ClusterIP   10.43.18.52     <none>        8082/TCP                     3h34m
     argocd-notifications-controller-metrics   ClusterIP   10.43.100.251   <none>        9001/TCP                     3h34m
     argocd-redis                              ClusterIP   10.43.127.232   <none>        6379/TCP                     3h34m
     argocd-repo-server                        ClusterIP   10.43.42.133    <none>        8081/TCP,8084/TCP            3h34m
     argocd-server                             NodePort    10.43.60.31     <none>        80:31341/TCP,443:32284/TCP   3h34m
     argocd-server-metrics                     ClusterIP   10.43.139.60    <none>        8083/TCP                     3h34m
     ```

4. **Encaminhando o Serviço para Acesso via Navegador**:
   - Para acessar o aplicativo pelo navegador, você precisa encaminhar a porta do serviço do `loginapp-webapp-svc` para o seu navegador local.
   - Execute o seguinte comando para encaminhar a porta:

     ```bash
     kubectl port-forward --address 0.0.0.0 svc/loginapp-webapp-svc 8081:3000
     ```

   - Este comando fará o **port forwarding** da porta 3000 (onde o aplicativo está sendo executado) para a porta 8081 no seu host local.

5. **Acessando o Aplicativo**:
   - Agora, você pode acessar o aplicativo em seu navegador. Use o **endereço IP público** do seu servidor (ou localhost se estiver usando o minikube ou ambiente local) com a **porta 8081**.
   
   - O URL será algo como:
     ```
     http://<ENDEREÇO_IP_PUBLICO>:8081
     ```
     Ao acessar esse endereço, você verá a página de login do aplicativo.

---

Esse manual está corrigido e agora descreve de forma clara e detalhada as etapas para testar e acessar o aplicativo implantado no Kubernetes com o ArgoCD.