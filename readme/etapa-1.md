# Criando o Pipeline de CI

Criaremos repositórios para o aplicativo chamados `nodejs-app` e `nodejs-manifest`.

Então, primeiro faça login na sua conta do Gitlab e clique em Novo projeto.


Selecione Criar projeto em branco.


Insira o nome do projeto e selecione o usuário. 
O resto o mantém como está. 
Aqui estamos criando repositórios privados. 
Por fim, clique em Create project.


Você pode ver o repositório criado `nodejs-app`.

Siga o mesmo procedimento para criar outro repositório `nodej-manifest`.

Você pode ver o repositório criado `nodejs-manifest` .

Agora vá para o repositório `nodejs-app`, clique em code e copie Clone with HTTPs code.

Em seguida, vamos copiar o repositório usando o git clone e o código copiado.

```bash
git clone git clone https://gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-app.git
```

Abrir o Visual Studio Code

```bash
code .
```

Vá para o repositório `nodejs-app`


Crie um arquivo Dockerfile.


Adicione o seguinte código. E salve o arquivo.

# Stage 1: Build the Node.js application
FROM node:16 AS node-build
WORKDIR /app
COPY source_code/package.json source_code/server.js ./
RUN npm install
# Stage 2: Expose the Node.js application
FROM node:16
WORKDIR /app
# Copy the Node.js application and dependencies from the build stage
COPY --from=node-build /app /app
# Expose the port the app will run on
EXPOSE 3000
# Start the Node.js application
CMD ["node", "server.js"]


Em seguida, crie um diretório `source_code`.

Dentro do diretório, primeiro crie um arquivo `package.json`.

Adicione o seguinte código nele.

{
  "name": "nodejs-app",
  "version": "1.0.0",
  "description": "A simple Node.js app",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}


Crie um arquivo `server.js` com o seguinte código.

'use strict';
const express = require('express');
const app = express();
// Define the port and host for the Node.js app
const PORT = 3000;
const HOST = '0.0.0.0';
// Define a simple API endpoint
app.get('/api', (req, res) => {
    res.json({ message: 'Node.js API is running!' });
});
app.get('/', (req, res) => {
  res.send('Hello World!');
});
// Start the server and listen on the defined port
app.listen(PORT, HOST, () => {
    console.log(`Node.js server running on http://${HOST}:${PORT}`);
});

Em seguida, vamos tentar fazer push do nosso repositório. 

Primeiro, execute o comando git status. Este comando exibe o estado do diretório de trabalho e da área de preparação. Ele ajuda você a rastrear:

- Quais arquivos foram modificados, mas ainda não foram preparados para confirmação.
- Arquivos preparados e prontos para serem confirmados.
- Arquivos não rastreados (novos arquivos ainda não adicionados ao controle de versão).

```bash
git status
```

Então execute o seguinte comando.

```bash
git add .
```
Este comando prepara todas as alterações no diretório atual e seus subdiretórios para o próximo commit.

Agora você pode ver as alterações executando o comando git status novamente.

```bash
git status
```

Vamos fazer o commit. Ele organiza as alterações em arquivos rastreados e cria um commit com a mensagem especificada “Files added to repo” .

```bash
git commit -am "Files added to repo"
```

Vamos enviar o repositório usando git push para o gitlab. Isso carrega os commits do seu repositório local para o repositório remoto. Você precisa digitar o nome de usuário e a senha do gitlab para isso.

```bash
git push
```

Volte para seu repositório nodejs-app na conta gitlab e atualize a página. Você pode ver que os arquivos e pastas foram enviados com sucesso.

Em seguida, vá para o repositório nodejs-manifest , clique em código e copie Clone with HTTPs code .

Abra a aba duplicada. Em seguida, vamos puxar o repositório nodejs-manifest usando git clone e código copiado.

```bash
git clone https://gitlab.com/add-k8s-helm-gitlab-argocd/nodejs-manifest.git
```

Navegue até o diretório do repositório nodejs-manifest 

crie e navegue até o diretório login-app .

Crie um arquivo Chart.yaml com o seguinte código:

apiVersion: v2
name: login-app
description: A Helm chart for Kubernetes
# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application
# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.16.0"

Crie um arquivo values.yaml com o seguinte código.

# Default values for login-app.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 1
image:
  repository: registry.gitlab.com/devopshint/nodejs-app/login_demo:ebcfd93c
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""
imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""
serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: ""
podAnnotations: {}
podSecurityContext: {}
  # fsGroup: 2000
securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000
service:
  type: NodePort
  port: 80
ingress:
  enabled: false
  className: ""
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local
resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi
autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80
nodeSelector: {}
tolerations: []
affinity: {}


Crie e navegue até o diretório de `templates`.

Crie um arquivo.yaml de implantação com o código a seguir.

deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: logindep
  name: {{ .Release.Name }}-logindep
spec:
  replicas: 3
  selector:
    matchLabels:
      app: logindep
  template:
    metadata:
      labels:
        app: logindep
    spec:
      containers:
      - image: {{ .Values.image.repository }}
        name: sample
      imagePullSecrets:
      - name: helm-secret

Crie um arquivo secret.yaml com o seguinte código.


apiVersion: v1 
data: 
  .dockerconfigjson: ewoJImF1dGhzIjogewoJCSJyZWdpc3RyeS5naXRsYWIuY29tIjogewoJCQkiYXV0aCI6ICJaR1YyYjNCemFHbHVkRHBFWlhaUGNITklhVzUwUURFeU13PT0iCgkJfQoJfQp9 
tipo: Segredo 
metadados: 
  creationTimestamp: nulo 
  nome: helm-secret 
tipo: kubernetes.io/dockerconfigjson

Crie um arquivo service.yaml com o seguinte código.

apiVersion: v1
kind: Service
metadata:
  labels:
    app: webapp-svc
  name: {{ .Release.Name }}-webapp-svc
spec:
  ports:
  - name: webapp-svc
    port: 3000
    protocol: TCP
    targetPort: 3000
    #    nodePort: 31000
  selector:
    app: logindep
  type: {{ .Values.service.type }}
status:
  loadBalancer: {}

Em seguida, vamos tentar fazer push do nosso repositório, primeiro execute o comando git status. Este comando exibe o estado do diretório de trabalho e da área de preparação. Ele ajuda você a rastrear:

Quais arquivos foram modificados, mas ainda não foram preparados para confirmação.
Arquivos preparados e prontos para serem confirmados.
Arquivos não rastreados (novos arquivos ainda não adicionados ao controle de versão).

```bash
git status
```

```bash
git add .
```

git commit -am "Files added to repo"

Vamos enviar o repositório usando git push para  gitlab. Isso carrega os commits do seu repositório local para o repositório remoto. Você precisa digitar o nome de usuário e a senha do gitlab para isso.

```bash
git push
```

Volte para seu repositório nodejs-manifest na conta gitlab e atualize a página. Você pode ver que os arquivos e pastas foram enviados com sucesso.