# Configurar um pipeline CICD no Gitlab (atualização do gráfico do Helm)

Agora, vá para o repositório do aplicativo web, abra o arquivo `.gitlab-ci.yml` e edite-o.

Adicione a parte referente à atualização do gráfico do Helm no arquivo `.gitlab-ci.yml` e clique em **Commit changes**. Após o commit, clique na marca de seleção verde para ver o pipeline.

```yaml
stages:
  - build
  - update_helm_chart

variables:
  APP_NAME: login_demo
  IMAGE_TAG: registry.gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-app/$APP_NAME:$CI_COMMIT_SHORT_SHA

# Job para construção da imagem Docker
build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  script:
    # Login no Docker registry
    - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
    # Construção da imagem Docker
    - docker build -t $IMAGE_TAG .
    # Push da imagem para o GitLab Container Registry
    - docker push $IMAGE_TAG

# Job para atualização do gráfico do Helm
update_helm_chart:
  stage: update_helm_chart
  image: ubuntu:24.04
  before_script:
    # Instalar dependências necessárias (ssh-agent, openssh-client, git)
    - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client git -y )'
    # Criar diretório para chaves SSH
    - mkdir -p /root/.ssh
    # Adicionar chave privada SSH
    - echo "$SSH_PRIVATE_KEY" > /root/.ssh/id_rsa
    - chmod 600 /root/.ssh/id_rsa
    # Adicionar o host do GitLab ao arquivo known_hosts para evitar o aviso de segurança
    - ssh-keyscan -H gitlab.com >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    # Iniciar o ssh-agent
    - eval $(ssh-agent -s)
    # Adicionar chave SSH armazenada na variável SSH_PRIVATE_KEY ao agente
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    # Configurações de Git
    - git config --global user.email "rfahham@gmail.com"
    - git config --global user.name "rfahham"
    # Clonar repositório que contém os manifests do Helm
    - git clone git@gitlab.com:add-k8s-helm-gitlab-argocd/nodejs-manifest.git
    - cd nodejs-manifest
    - ls -latr
  script:
    # Atualizar a tag da imagem no arquivo values.yaml do Helm
    - sed -i "s/login_demo:.*/login_demo:${CI_COMMIT_SHORT_SHA}/g" login-app/values.yaml
    # Adicionar a alteração no arquivo values.yaml ao controle de versão
    - git add login-app/values.yaml
    # Comitar a alteração com uma mensagem
    - git commit -am "Update Image"
    # Enviar a alteração para o repositório GitLab
    - git push
```

### Passos seguintes:

- Após editar o arquivo `.gitlab-ci.yml`, o pipeline será iniciado automaticamente no GitLab.
- Clique em **update_helm_chart** para ver o andamento do pipeline.
- O pipeline consistirá em duas etapas: **build** e **update_helm_chart**. A primeira etapa irá construir e enviar a imagem Docker para o GitLab Container Registry, enquanto a segunda etapa irá atualizar o gráfico Helm (no arquivo `values.yaml`) com a tag correta da imagem.

### Considerações importantes:

- **Variáveis de Ambiente**: Certifique-se de que as variáveis `CI_REGISTRY_USER`, `CI_REGISTRY_PASSWORD`, e `SSH_PRIVATE_KEY` estejam configuradas corretamente no GitLab CI/CD para garantir que a autenticação funcione corretamente.
- **Permissões SSH**: Assegure-se de que a chave privada SSH (`SSH_PRIVATE_KEY`) tenha as permissões corretas e que o GitLab esteja configurado para permitir o acesso SSH para o repositório onde você está fazendo o clone.
- **GitLab Container Registry**: A imagem Docker será enviada para o registro do GitLab (com a tag gerada usando `CI_COMMIT_SHORT_SHA`), garantindo que a imagem esteja associada à versão do commit.

Isso garante que, sempre que um commit for feito, o pipeline será executado, construindo a imagem Docker e, em seguida, atualizando o gráfico do Helm para refletir a nova tag da imagem.

---

Esse manual agora está mais claro e corrigido para facilitar a implementação do pipeline CI/CD com a atualização do gráfico do Helm.