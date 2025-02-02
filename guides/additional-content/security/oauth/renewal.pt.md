# Renovar Access Token
 
O fluxo **Refresh token** é utilizado para trocar um **temporary grants** do tipo `refresh_token` por um Access Token quando o Access Token em uso estiver **próximo do vencimento** e este tiver sido obtido a partir do fluxo [Authorization code](/developers/pt/docs/security/oauth/creation#bookmark_authorization_code). O Access Token recebido através do endpoint tem **validade de 180 dias** (6 meses) e passado esse período é preciso reconfigurar todo o fluxo de autorização.
 
Além disso, o fluxo permite continuar utilizando um Access Token válido com as mesmas características do token original sem a necessidade de uma nova interação com o usuário. Ao realizar este fluxo, o token original é trocado por um novo token que também oferece a possibilidade de limitar os scopes devolvendo um novo refresh token para ser trocado no futuro.
 
> WARNING
>
> Importante
>
> Só é possível utilizar este fluxo se a aplicação retornar o parâmetro `scope`indicando o valor `offline_access` e o vendedor tiver autorizado previamente esta ação a partir do fluxo de [Authorization code](/developers/pt/docs/security/oauth/creation#bookmark_authorization_code)
 
Siga os passos abaixo para renovar o **Access Token**.
 
1. Envie o código do `refresh_token`, as suas [credenciais](/developers/pt/docs/your-integrations/credentials) e o `authorization_code` (veja [Criação](/developers/pt/docs/security/oauth/creation#bookmark_authorization_code)) ao endpoint [/oauth/token](/developers/pt/reference/oauth/_oauth_token/post) com o código do `refresh_token` no string `grant_type` para receber uma nova resposta com um novo `access_token` e um novo `refresh_token`.
2. Atualize a aplicação com o Access Token recebido na resposta.
 
> WARNING
>
> Importante
>
> Lembre-se que cada vez que você renovar o `access_token`, o `refresh_token` também vai ser renovado, por tanto, você deverá armazená-lo novamente.

[[[
```php
<?php
  $client = new OauthClient();
  $request = new OAuthRefreshRequest();
    $request->client_secret = "CLIENT_SECRET";
    $request->client_id = "CLIENT_ID";
    $request->refresh_token = "REFRESH_TOKEN";

  $client->refresh($request);
?>
```
```java

OauthClient client = new OauthClient();

String refreshtoken = "TG-XXXXXXXX-241983636";
client.createCredential(refreshtoken, null);
```
```node
const client = new MercadoPagoConfig({ accessToken: 'access_token', options: { timeout: 5000 } });

const oauth = new OAuth(client);

oauth.refresh({
	'client_secret': 'your-client-secret',
	'client_id': 'your-client-id',
	'refresh_token': 'refresh-token'
}).then((result) => console.log(result))
	.catch((error) => console.log(error));
```
```curl
curl -X POST \
'https://api.mercadopago.com/oauth/token'\
-H 'Content-Type: application/json' \
-d '{
 "client_id": "client_id",
 "client_secret": "client_secret",
 "grant_type": "refresh-token",
 "refresh_token": "TG-XXXXXXXX-241983636",
}'
```
]]]