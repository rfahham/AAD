# Gerando e configurando chaves SSH para autenticação segura


## Gerando e configurando chaves SSH para autenticação segura

1. **Criando o diretório `.ssh`**

   Crie o diretório `.ssh` na pasta inicial (`~`) se ele ainda não existir:

   ```bash
   mkdir -p ~/.ssh
   ```

2. **Alterando permissões do diretório `.ssh`**

   Altere as permissões do diretório `.ssh` para torná-lo acessível somente ao proprietário:

   ```bash
   chmod 700 ~/.ssh
   ```

3. **Gerando um par de chaves SSH**

   Gere um par de chaves SSH (chave privada e chave pública) com um método seguro para autenticação com servidores remotos ou repositórios Git sem precisar de senha:

   ```bash
   ssh-keygen -t rsa -b 4096 -C "rfahham@gmail.com"
   ```

   **Nota**: Durante a execução do comando, você pode escolher o caminho onde a chave será salva e definir uma senha para maior segurança (opcional).

4. **Verificando a criação das chaves SSH**

   Liste o conteúdo do diretório `.ssh` com informações detalhadas para verificar se o par de chaves foi criado com sucesso (o arquivo `id_rsa` é a chave privada e `id_rsa.pub` é a chave pública):

   ```bash
   ls -l ~/.ssh
   ```

5. **Exibindo o conteúdo da chave pública**

   Exiba o conteúdo do arquivo de chave pública e copie o conteúdo para adicionar ao GitLab:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

6. **Adicionando a chave pública ao GitLab**

   - No GitLab, clique no seu perfil (ícone no canto superior direito) e selecione **Editar Perfil**.
   - No menu lateral, selecione **Chaves SSH**.
   - Clique em **Adicionar chave SSH**.
   - Cole o conteúdo da chave pública (copiado na etapa anterior) no campo **Chave** e adicione um título para identificar a chave, por exemplo, "Chave SSH do pipeline".

7. **Exibindo o conteúdo da chave privada**

   Exiba o conteúdo do arquivo de chave privada, **com cuidado** para não expor esse conteúdo publicamente:

   ```bash
   cat ~/.ssh/id_rsa
   ```

   **Importante**: Mantenha a chave privada sempre segura e nunca compartilhe seu conteúdo publicamente.

8. **Criando a variável no GitLab para a chave privada**

   Agora, volte para o GitLab, acesse o repositório do seu projeto, vá até **CI / CD** > **Variáveis** e crie uma nova variável com o seguinte nome e valor:

   - **Chave da variável**: `SSH_PRIVATE_KEY`
   - **Valor da variável**: O conteúdo da chave privada copiado na etapa anterior.

   **Exemplo**: O valor da variável deve ser algo como:

   ```
   -----BEGIN OPENSSH PRIVATE KEY-----
   <conteúdo da chave privada>
   -----END OPENSSH PRIVATE KEY-----
   ```

9. **Testando a chave SSH localmente**

   Para garantir que o SSH e a chave privada estão funcionando corretamente, você pode testar a autenticação SSH localmente com o comando:

   ```bash
   ssh -T git@gitlab.com
   ```

   Se for a primeira vez que você está se conectando ao GitLab via SSH, você verá uma mensagem de aviso sobre a autenticidade do host. Digite `yes` para continuar:

   ```
   The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
   ED25519 key fingerprint is SHA256:eUXGGm1YGsMAS7vkcx6JOJdOGHPem5gQp4taiCfCLB8.
   This key is not known by any other names
   Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
   Warning: Permanently added 'gitlab.com' (ED25519) to the list of known hosts.
   Welcome to GitLab, @rfahham!
   ```

   Caso a autenticação seja bem-sucedida, você verá a mensagem **"Welcome to GitLab, @rfahham!"**, indicando que a chave SSH foi configurada corretamente.

---

### Considerações finais

- As chaves SSH proporcionam uma forma segura de autenticar e acessar repositórios Git sem a necessidade de senhas, facilitando automações, como CI/CD.
- **Nunca compartilhe a chave privada**. Mantenha-a em segurança e use a chave pública apenas para autenticação.
- No GitLab, sempre armazene sua chave privada em variáveis de ambiente **criptografadas** e configure adequadamente os acessos.

Com este processo, você poderá realizar autenticação SSH de forma segura para interagir com repositórios GitLab e automatizar seus fluxos de trabalho com CI/CD.

