# Como criar um gerenciamento de APIs em cloud

Exercício realizado no Bootcamp Microsoft Azure Cloud Native

## Faça Login

az login

## Crie um grupo de recursos
az group create --name RESOURCE_GROUP --location eastus

## Crie um plano de Serviços
az appservice plan create --name PLAN_SERVICE --resource-group RESOURCE_GROUP --sku F1  

## Crie um Web App
az webapp create --resource-group RESOURCE_GROUP --plan PLAN_SERVICE --runtime "dotnet:8" --name WEB_APP

## Suba o código para o Web App
Crie uma API e publique-a no Web App criado.

## Crie o API management
az apim create --name APIM_NAME --resource-group RESOURCE_GROUP --publisher-email "SEU_EMAIL" --publisher-name "SEU_NOME --sku-name Developer --location brazilsouth

## Configure o ambiente

Após criar todo o ambiente, você pode acessar a API Management no portal do Azure e adicionar a API que você criou no Web App.

### Adicionar a API ao management

Acesse o APIM no portal do Azure:
Vá em APIs > + Add API

- Escolha a opção Web App
- Selecione o Web App criado anteriormente
- As rotas da sua API serão importadas automaticamente.

[""](imagens/add-api.PNG)

Defina:

- Nome interno
- Nome de exibição (Display Name)
- Sufixo da API (ex: /v1)

[""](imagens/create-api.PNG)


### Adicionar Subscriptions

⚙️ Ativar Subscriptions na API
Acesse a API importada

Vá em Settings
Ative a opção Require subscription

`Defina o header como x-api-key`

[""](imagens/config-subscriptions.PNG)

➕ Criar uma Subscription
Vá em Subscriptions > + Add

Defina o nome, Display Name e descrição

Salve o valor da Primary Key para usar nas requisições

[""](imagens/add-subscription.PNG)


📬 Enviar requisições com Subscription Key
Use o valor da key no header:

`x-api-key: SUA_PRIMARY_KEY`

### Configurar Design 

Com a API criada, crie uma política de segurança para o GET da API. Vá até a aba "Design" da API e clique em "Inbound processing". Crie um rewrite URL para mudar o caminho da URL.


### Criar Aplicativo no Azure AD
Vá em Azure Active Directory > App registrations > + New registration

Copie:

- Client ID
- OpenID (em Endpoints)
- Token endpoint (também em Endpoints)

Vá até Certificates & secrets > + New client secret

- Salve o valor do Client Secret

[""](imagens/secret.PNG)

🔐 Criar Scopes e Roles
Vá em Expose an API > Add a scope

- Vá em App roles > Add roles 
- Vá em API permissions > Add permission

**SALVE O VALOR DO ESCOPO**

[""](imagens/roles.PNG)
[""](imagens/roles-permission.PNG)


### Adicionar Política de Validação JWT no APIM

Gere o Token passando essas informações em seu body.
```json
{
  "client_id": "SEU_CLIENT_ID",
  "scope": "SEU_SCOPE",
  "client_secret": "SEU_CLIENT_SECRET",
  "grant_type": "client_credentials"
} 
```
[""](imagens/token-gerado.PNG)

**Pegue as informações**

[""](imagens/decoder-payload.PNG)

Vá até Design da API > Inbound processing

Clique em + Add policy > validate-jwt > Full

Configure:

- Header: Authorization
- Esquema: Bearer
- OpenID URL: URL do OpenID
- Audience (aud) e Issuer (iss): Copie do jwt.io usando seu token

Clique em Save para salvar as configurações.

[""](imagens/config-jwt.PNG)

Resultado Final
Sua API estará:

- Hospedada no Azure Web App
- Gerenciada com API Management
- Segura com autenticação JWT
- Controlada por roles e subscriptions
