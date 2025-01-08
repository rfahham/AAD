# Implantando o aplicativo com o ArgoCD

1. **Acesse o ArgoCD**: 
   - Abra o painel do ArgoCD.

2. **Conectar Repositório**:
   - No menu lateral, clique em **Configurações**.
   - Em seguida, clique em **Repositórios**.
   - Clique em **CONECTAR REPOSITÓRIO**.

3. **Adicionar o Repositório GitLab**:
   - Copie o link HTTPS do repositório `nodejs-manifest` do GitLab:
     ```
     https://gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-manifest.git
     ```
   - No campo **URL do Repositório**, cole a URL copiada.
   - Selecione o **Método de Conexão VIA HTTPS**.
   - Para autenticação, forneça:
     - **Usuário**: `git`
     - **Nome de usuário**: Seu nome de usuário GitLab.
     - **Senha**: Seu token de acesso pessoal do GitLab (GitLab Access Token).
   
   - Após inserir os detalhes, clique em **Testar Conexão**. O status de conexão deve ser **Bem-sucedido**.

4. **Criar Novo Aplicativo**:
   - No menu lateral, clique em **Aplicativos**.
   - Clique em **NOVO APLICATIVO**.

5. **Preencher Detalhes do Aplicativo**:
   - **Nome do Aplicativo**: `loginapp`
   - **Nome do Projeto**: `default` (ou o nome do projeto que você está utilizando).
   - **Política de Sincronização**: Selecione **Automático**.
   - **Repository URL**: Selecione o repositório que você conectou anteriormente. Os outros detalhes serão preenchidos automaticamente.
   - **Cluster URL**: Selecione o cluster desejado. O campo será preenchido automaticamente.
   - **Namespace**: Selecione **default** (ou o namespace desejado).

6. **Visualizar o Aplicativo**:
   - Após preencher as informações, clique em **Criar**. O ArgoCD irá criar e implantar o aplicativo.
   - Após a criação, você pode visualizar o status do aplicativo:
     - **Status**: **Healthy** e **Synced**, indicando que o aplicativo está em funcionamento e sincronizado com o repositório.
   - Clique no nome do aplicativo para ver mais detalhes, incluindo o status e o pipeline de implantação.

---

Essa versão corrigida segue uma sequência mais clara e compreensível, explicando cada etapa detalhadamente para a configuração do ArgoCD, desde a conexão do repositório até a criação e visualização do aplicativo.