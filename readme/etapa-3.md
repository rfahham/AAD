# Configurar um Pipeline CI/CD no GitLab (Estágio de Construção)

### Passo 1: Criar o arquivo `.gitlab-ci.yml`

1. Acesse o repositório `nodejs-app` no GitLab.
2. Crie um novo arquivo no repositório, chamado `.gitlab-ci.yml`. 
3. Adicione o seguinte conteúdo ao arquivo `.gitlab-ci.yml`:

```yaml
stages:
  - build

variables:
  APP_NAME: login_demo
  IMAGE_TAG: registry.gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-app/$APP_NAME:$CI_COMMIT_SHORT_SHA

build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  script:
    # Login no Docker registry
    - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
    # Construção da imagem
    - docker build -t $IMAGE_TAG .
    # Push da imagem
    - docker push $IMAGE_TAG
```

### Explicação do arquivo `.gitlab-ci.yml`:

- **stages**: Define as etapas do pipeline. Neste caso, há uma única etapa chamada `build`.
- **variables**: Define variáveis personalizadas. No caso, a variável `APP_NAME` é definida como `login_demo`, e a variável `IMAGE_TAG` será gerada com o nome da imagem usando o repositório, o nome do aplicativo e o hash curto do commit.
- **build_image**: Esta é a etapa de construção da imagem Docker. Ela usa a imagem do Docker e o serviço `docker:dind` (Docker-in-Docker) para construir e empurrar a imagem para o Docker registry.

### Passo 2: Configurar variáveis no GitLab

1. Acesse **Settings > CI / CD** no seu repositório GitLab.
2. Expanda a seção **Variables**.
3. Clique em **Add Variable** para adicionar as variáveis de ambiente necessárias.

Adicione as variáveis da seguinte maneira:

- **Key**: `CI_REGISTRY`
  - **Value**: `registry.gitlab.com`
- **Key**: `CI_REGISTRY_IMAGE`
  - **Value**: `registry.gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-app`
- **Key**: `CI_REGISTRY_PASSWORD`
  - **Value**: (Deixe vazio para que seja preenchido automaticamente)
- **Key**: `CI_REGISTRY_USER`
  - **Value**: (Deixe vazio para que seja preenchido automaticamente)

> **Nota**: As variáveis `CI_REGISTRY_PASSWORD` e `CI_REGISTRY_USER` são preenchidas automaticamente pelo GitLab com base nas credenciais do usuário que executa o pipeline. No entanto, se você precisar configurá-las manualmente, você pode usar o seu nome de usuário do GitLab e um **token de acesso pessoal** (PAT) gerado em **Settings > Access Tokens**.

### Passo 3: Confirmar e iniciar o pipeline

1. Vá até o arquivo `.gitlab-ci.yml` no repositório.
2. Clique em **Commit changes** para salvar o arquivo e iniciar o pipeline.
3. Isso irá validar o arquivo e iniciar o pipeline de construção.

### Passo 4: Verificar a execução do pipeline

1. Após o pipeline ser iniciado, você verá um ícone de "status" indicando se o pipeline foi bem-sucedido (uma marca de seleção verde) ou falhou.
2. Clique na **marca de seleção verde** para ver os detalhes do pipeline.

### Passo 5: Visualizar a imagem no Container Registry

1. Vá para a seção **Deploy > Container Registry** no menu do GitLab.
2. Você verá o repositório `nodejs-app/login_demo` listado no Container Registry.
3. Clique no nome do repositório para ver a imagem Docker que foi construída.

A partir daqui, você poderá ver a imagem Docker que foi criada e empurrada para o registro do GitLab, podendo utilizá-la em outros pipelines ou deployments.

Validar o acesso ao Registry

```bash
docker login -u <seu-usuario> -p <seu-token> registry.gitlab.com
```

---

### Resumo:
- Criamos um arquivo `.gitlab-ci.yml` para definir o pipeline de construção da imagem Docker.
- Configuramos variáveis no GitLab para permitir o acesso ao registro do Docker.
- Iniciamos o pipeline para construir e enviar a imagem para o Container Registry do GitLab.
- Por fim, verificamos se a imagem foi corretamente enviada para o Container Registry.

Agora você tem um pipeline CI/CD básico configurado para construção e upload de imagens Docker no GitLab!

Agora vá para Deploy > Container Registry . Você verá o nodejs-app/login_demo . Clique nele.

Você pode ver a imagem que está sendo construída.