# Criando um segredo do Kubernetes

## Passo 1: Fazer login no Registro de Contêineres do GitLab

Primeiro, faça o login no Registro de Contêineres do GitLab utilizando o Docker.

```bash
docker login registry.gitlab.com
Authenticating with existing credentials...
Login Succeeded
```

Durante o login, o GitLab solicita seu nome de usuário e o token de acesso pessoal (ou senha) para autenticação. O Docker armazena as credenciais de login no arquivo `~/.docker/config.json` em sua máquina local.

## Passo 2: Verificar as credenciais no arquivo de configuração do Docker

Para verificar se as credenciais de login foram armazenadas corretamente, exiba o conteúdo do arquivo de configuração do Docker localizado em `~/.docker/config.json`:

```bash
cat ~/.docker/config.json
```

O conteúdo do arquivo deve ser semelhante a este:

```json
{
    "auths": {
        "registry.gitlab.com": {}
    },
    "credsStore": "desktop.exe"
}
```

## Passo 3: Codificar o arquivo `config.json` em Base64

Agora, você precisa codificar o conteúdo do arquivo `config.json` em uma string Base64 de linha única. Essa string codificada será adicionada ao arquivo `secret.yaml`. Execute o seguinte comando para gerar a string Base64:

```bash
cat ~/.docker/config.json | base64 -w 0
```

A opção `-w 0` garante que a string Base64 seja gerada em uma única linha, sem quebras de linha.

## Passo 4: Adicionar o segredo no GitLab

De volta ao GitLab, localize o arquivo `secret.yaml` no repositório `nodejs-manifest`, no caminho `login-app > templates > secret.yaml`.

Clique em **Editar** e selecione **Editar arquivo único**.

Adicione o segredo codificado no formato abaixo dentro do arquivo `secret.yaml` e clique em **Confirmar alterações**.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-docker-registry
type: Opaque
data:
  .dockerconfigjson: <string_base64_gerada>
```

Substitua `<string_base64_gerada>` pela string Base64 que você gerou no Passo 3.

## Passo 5: Aplicar o segredo no Kubernetes

Após adicionar o segredo no arquivo `secret.yaml`, você pode aplicar o segredo ao Kubernetes usando o seguinte comando:

```bash
kubectl apply -f path/to/secret.yaml
```

Isso criará o segredo no Kubernetes, permitindo que ele seja utilizado para acessar o Registro de Contêineres do GitLab de forma segura.

---

## Considerações Finais

- **Segurança**: Certifique-se de que o arquivo `secret.yaml` esteja adequadamente protegido, pois ele contém informações sensíveis, como credenciais codificadas.
- **Facilidade de uso**: Usando o segredo no Kubernetes, você pode configurar o acesso ao Registro de Contêineres do GitLab para puxar imagens de maneira segura e automatizada.

Com este processo, você terá configurado com sucesso um segredo do Kubernetes para autenticação no Registro de Contêineres do GitLab, permitindo que seus pods acessem as imagens Docker de maneira segura e eficiente.

