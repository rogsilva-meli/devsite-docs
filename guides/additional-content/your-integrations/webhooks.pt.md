# Webhooks

O **Webhooks** (também conhecido como retorno de chamada web) é um método simples que facilita com que um app ou sistema forneça informações em tempo real sempre que um evento acontece, ou seja, é um modo de receber dados entre dois sistemas de forma passiva através de um `HTTP POST`.

As notificações Webhooks poderão ser configuradas para uma ou mais aplicações criadas em seu [Painel do desenvolvedor](/developers/panel/app).

Uma vez configurado, o Webhook será enviado sempre que ocorrer um ou mais eventos cadastrados, evitando que haja um trabalho de pesquisa a cada minuto em busca de uma resposta e, por consequência, que ocorra uma sobrecarga do sistema e a perda de dados sempre que houver alguma situação. Após receber uma notificação na sua plataforma, o Mercado Pago aguardará uma resposta para validar se você a recebeu corretamente.

Nesta documentação, explicaremos as configurações necessárias para o recebimento das mensagens (através do Painel do desenvolvedor ou durante a criação de pagamentos), além de apresentar quais são as ações necessárias que você deverá ter para que o Mercado Pago valide que as notificações foram devidamente recebidas.

## Configuração através do Painel do desenvolvedor

Abaixo explicaremos como: indicar as URLs que serão notificadas, configurar os eventos dos quais se receberá a notificação, simular o recebimento de diversos tipos de notificações e validar que as notificações que recebe são enviadas pelo Mercado Pago.

![webhooks](/images/dashboard/webhooks-pt.png)

### Configurar URLs e Eventos

1. Caso ainda não tenha, crie uma aplicação no [Painel do desenvolvedor](/developers/panel/app).
2. Com a aplicação criada, navegue até a seção Webhooks na página de "Detalhes da aplicação" e configure as URLs de **produção** e **teste** da qual serão recebidas as notificações.
3. Caso seja necessário identificar múltiplas contas, no final da URL indicada você poderá indicar o parâmetro `?cliente=(nomedovendedor) endpoint` para identificar os vendedores.
4. Em seguida, selecione os **eventos** dos quais você receberá notificações em formato `json` através de um `HTTP POST` para a URL especificada anteriormente. Um evento é qualquer tipo de atualização no objeto relatado, incluindo alterações de status ou atributo. Veja na tabela abaixo os eventos que poderão ser configurados.

| Tipo de notificação | Ação | Descrição |
| :--- | :--- | :--- |
| `payment` | `payment.created` | Criação de pagamento |
| `payment` | `payment.updated` | Atualização de pagamento |
| `mp-connect` | `application.deauthorized` | Desvinculação de conta |
| `mp-connect` | `application.authorized` | Vinculação de conta |
| `subscription_preapproval` | `created - updated` | Assinatura |
| `subscription_preapproval_plan` | `created - updated` | Plano de assinatura |
| `subscription_authorized_payment` | `created - updated` | Pagamento recorrente de uma assinatura |
| `point_integration_wh` | `state_FINISHED`| Processo de pagamento concluído |
| `point_integration_wh` | `state_CANCELED` | Processo de pagamento cancelado |
| `point_integration_wh` | `state_ERROR`| Ocorreu um erro ao processar a tentativa de pagamento |
| `topic_instore_integration_wh` | - | Notificações de integrações presenciais |
| `shipments` | - | Notificações de envios |
| `delivery` | `delivery.updated`| Dados de envio e atualização do pedido |
| `delivery_cancellation` | `case_created`| Solicitação de cancelamento do envio |
| `wallet_connect` | - | Notificações de transações com [Wallet Connect](/developers/pt/docs/wallet-connect/landing) |
| `stop_delivery_op_wh` | - | Alertas de fraude |
| `topic_claims_integration_wh` | `updated`| Reclamações feitas pelas vendas |
| `topic_card_id_wh` | `card.updated`| O cartão de usuário comprador foi atualizado* |

> *O _Card Updater_ recupera informações de cartões e atualiza essas informações dentro do Mercado Pago. Cartões recuperáveis com este recurso: cartões com alguma informação errada (como data de vencimento, número do cartão, CVV, nome, etc.) e cartões que tenham sido trocados pela instituição financeira (por motivo de validade, upgrade de cartão, etc.).

5. Por fim, clique em **Salvar** para gerar uma **assinatura secreta** para a aplicação. A assinatura é um método de validação para garantir que as notificações recebidas foram enviadas pelo Mercado Pago, por isso, é importante conferir as informações de autenticidade para evitar fraudes.

> WARNING
>
> Importante
>
> O Mercado Pago sempre enviará essa assinatura nas notificações Webhooks. Sempre confira essa informação de autenticidade para evitar fraudes.
> <br/>
> A assinatura gerada não tem prazo de validade e, embora não seja obrigatório, recomendamos renovar periodicamente a **assinatura secreta**. Para isso, basta clicar no botão de redefinição ao lado da assinatura. 

### Validar origem da notificação

No momento em que a URL cadastrada receber uma notificação, você poderá validar se o conteúdo enviado no _header_ `x-signature` foi enviado pelo Mercado Pago, a fim de obter mais segurança no recebimento das suas notificações.

> NOTE
>
> Exemplo do conteúdo enviado no header x-signature
>
> `ts=1704908010,v1=618c85345248dd820d5fd456117c2ab2ef8eda45a0282ff693eac24131a5e839`

Veja abaixo o passo a passo de como configurar essa validação e, ao final, disponibilizamos alguns SDKs com um **exemplo de código completo** para facilitar o seu processo de configuração.

1. Extraia o _timestamp_ (`ts`) e a assinatura do _header_ `x-signature`. Para isso, divida o conteúdo do _header_ pelo caractere `,`, o que resultará em uma lista de elementos. O valor para o prefixo `ts` é o _timestamp_ (em milissegundos) da notificação e `v1` é a assinatura encriptada. Exemplo: `ts=1704908010` e `v1=618c85345248dd820d5fd456117c2ab2ef8eda45a0282ff693eac24131a5e839`.
2. Utilizando o _template_ abaixo, substitua os parâmetros pelos dados recebidos na sua notificação. 

```template
id:[data.id_url];request-id:[x-request-id_header];ts:[ts_header];
```

No _template_, os valores englobados por `[]` devem ser trocados pelos valores da notificação, como:

- Parâmetros com sufixo `_url` são provenientes de _query params_. Exemplo: `[data.id_url]` será substituido pelo valor correspondente ao ID do evento (`data.id`).
- `[ts_header]` será o valor `ts` extraído do _header_ `x-signature`.

> Caso algum dos valores apresentados no _template_ acima não esteja presente em sua notificação, você deverá removê-los do template.

3. No [Painel do desenvolvedor](/developers/panel/app), selecione a aplicação integrada, navegue até a seção Webhooks e **revele a assinatura secreta** gerada.
4. Gere a contra chave para validação. Para isso, calcule um [HMAC](https://pt.wikipedia.org/wiki/HMAC) com a função de `hash SHA256` em base hexadecimal, utilize a **assinatura secreta** como chave e o _template_ populado com os valores como mensagem. Exemplo:

[[[
```php
$cyphedSignature = hash_hmac('sha256', $data, $key);
```
```node
const crypto = require('crypto');
const cyphedSignature = crypto
    .createHmac('sha256', secret)
    .update(signatureTemplateParsed)
    .digest('hex'); 
```
```java
String cyphedSignature = new HmacUtils("HmacSHA256", secret).hmacHex(signedTemplate);
```
```python
import hashlib, hmac, binascii

cyphedSignature = binascii.hexlify(hmac_sha256(secret.encode(), signedTemplate.encode()))
```
]]]

5. Por fim, compare a chave gerada com a chave extraída do cabeçalho, considerando elas devem ter uma correspondência exata. Além disso, é possível usar o _timestamp_ extraído do _header_ para comparação com um _timestamp_ gerado na hora do recebimento da notificação, a fim de estipular uma tolerância de atraso no recebimento da mensagem.

- Exemplo de código completo:

[[[
```php
<?php
// Obtain the x-signature value from the header
$xSignature = $_SERVER['HTTP_X_SIGNATURE'];
$xRequestId = $_SERVER['HTTP_X_REQUEST_ID'];

// Obtain Query params related to the request URL
$queryParams = $_GET;

// Extract the "data.id" from the query params
$dataID = isset($queryParams['data.id']) ? $queryParams['data.id'] : '';

// Separating the x-signature into parts
$parts = explode(',', $xSignature);

// Initializing variables to store ts and hash
$ts = null;
$hash = null;

// Iterate over the values to obtain ts and v1
foreach ($parts as $part) {
    // Split each part into key and value
    $keyValue = explode('=', $part, 2);
    if (count($keyValue) == 2) {
        $key = trim($keyValue[0]);
        $value = trim($keyValue[1]);
        if ($key === "ts") {
            $ts = $value;
        } elseif ($key === "v1") {
            $hash = $value;
        }
    }
}

// Obtain the secret key for the user/application from Mercadopago developers site
$secret = "your_secret_key_here";

// Generate the manifest string
$manifest = "id:$dataID;request-id:$xRequestId;ts:$ts;";

// Create an HMAC signature defining the hash type and the key as a byte array
$sha = hash_hmac('sha256', $manifest, $secret);
if ($sha === $hash) {
    // HMAC verification passed
    echo "HMAC verification passed";
} else {
    // HMAC verification failed
    echo "HMAC verification failed";
}
?>
```
```javascript
// Obtain the x-signature value from the header
const xSignature = headers['x-signature']; // Assuming headers is an object containing request headers
const xRequestId = headers['x-request-id']; // Assuming headers is an object containing request headers

// Obtain Query params related to the request URL
const urlParams = new URLSearchParams(window.location.search);
const dataID = urlParams.get('data.id');

// Separating the x-signature into parts
const parts = xSignature.split(',');

// Initializing variables to store ts and hash
let ts;
let hash;

// Iterate over the values to obtain ts and v1
parts.forEach(part => {
    // Split each part into key and value
    const [key, value] = part.split('=');
    if (key && value) {
        const trimmedKey = key.trim();
        const trimmedValue = value.trim();
        if (trimmedKey === 'ts') {
            ts = trimmedValue;
        } else if (trimmedKey === 'v1') {
            hash = trimmedValue;
        }
    }
});

// Obtain the secret key for the user/application from Mercadopago developers site
const secret = 'your_secret_key_here';

// Generate the manifest string
const manifest = `id:${dataID};request-id:${xRequestId};ts:${ts};`;

// Create an HMAC signature
const hmac = crypto.createHmac('sha256', secret);
hmac.update(manifest);

// Obtain the hash result as a hexadecimal string
const sha = hmac.digest('hex');

if (sha === hash) {
    // HMAC verification passed
    console.log("HMAC verification passed");
} else {
    // HMAC verification failed
    console.log("HMAC verification failed");
}
```
```python
import hashlib
import hmac
import urllib.parse

# Obtain the x-signature value from the header
xSignature = request.headers.get("x-signature")
xRequestId = request.headers.get("x-request-id")

# Obtain Query params related to the request URL
queryParams = urllib.parse.parse_qs(request.url.query)

# Extract the "data.id" from the query params
dataID = queryParams.get("data.id", [""])[0]

# Separating the x-signature into parts
parts = xSignature.split(",")

# Initializing variables to store ts and hash
ts = None
hash = None

# Iterate over the values to obtain ts and v1
for part in parts:
    # Split each part into key and value
    keyValue = part.split("=", 1)
    if len(keyValue) == 2:
        key = keyValue[0].strip()
        value = keyValue[1].strip()
        if key == "ts":
            ts = value
        elif key == "v1":
            hash = value

# Obtain the secret key for the user/application from Mercadopago developers site
secret = "your_secret_key_here"

# Generate the manifest string
manifest = f"id:{dataID};request-id:{xRequestId};ts:{ts};"

# Create an HMAC signature defining the hash type and the key as a byte array
hmac_obj = hmac.new(secret.encode(), msg=manifest.encode(), digestmod=hashlib.sha256)

# Obtain the hash result as a hexadecimal string
sha = hmac_obj.hexdigest()
if sha == hash:
    # HMAC verification passed
    print("HMAC verification passed")
else:
    # HMAC verification failed
    print("HMAC verification failed")
```
```go
import (
	"crypto/hmac"
	"crypto/sha256"
	"encoding/hex"
	"fmt"
	"net/http"
	"strings"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		// Obtain the x-signature value from the header
		xSignature := r.Header.Get("x-signature")
		xRequestId := r.Header.Get("x-request-id")

		// Obtain Query params related to the request URL
		queryParams := r.URL.Query()

		// Extract the "data.id" from the query params
		dataID := queryParams.Get("data.id")

		// Separating the x-signature into parts
		parts := strings.Split(xSignature, ",")

		// Initializing variables to store ts and hash
		var ts, hash string

		// Iterate over the values to obtain ts and v1
		for _, part := range parts {
			// Split each part into key and value
			keyValue := strings.SplitN(part, "=", 2)
			if len(keyValue) == 2 {
				key := strings.TrimSpace(keyValue[0])
				value := strings.TrimSpace(keyValue[1])
				if key == "ts" {
					ts = value
				} else if key == "v1" {
					hash = value
				}
			}
		}

		// Get secret key/token for specific user/application from Mercadopago developers site
		secret := "your_secret_key_here"

		// Generate the manifest string
		manifest := fmt.Sprintf("id:%v;request-id:%v;ts:%v;", dataID, xRequestId, ts)

		// Create an HMAC signature defining the hash type and the key as a byte array
		hmac := hmac.New(sha256.New, []byte(secret))
		hmac.Write([]byte(manifest))

		// Obtain the hash result as a hexadecimal string
		sha := hex.EncodeToString(hmac.Sum(nil))

if sha == hash {
    // HMAC verification passed
    fmt.Println("HMAC verification passed")
} else {
    // HMAC verification failed
    fmt.Println("HMAC verification failed")
}

	})
}
```
]]]

### Simular o recebimento da notificação 

1. Após configurar as URLs e os Eventos, clique em **Simular** para experimentar e testar se a URL indicada está recebendo as notificações corretamente.
2. Na tela em questão, selecione a URL a ser testada, podendo ser **de teste ou de produção**. 
3. Em seguida, selecione o **tipo de evento** e insira a **identificação** que será enviada no corpo da notificação.
4. Por fim, cique em **Enviar teste** para verificar a solicitação, a resposta dada pelo servidor e a descrição do evento.

## Configuração durante a criação de pagamentos

É possível configurar a URL de notificação de modo mais específico, para cada pagamento utilizando o campo `notification_url`. Veja abaixo como fazer isso com uso dos SDKs.

1. No campo `notificaction_url`, indique a URL da qual serão recebidas as notificações como exemplificado abaixo.

[[[
```php
<?php 
$client = new PaymentClient();

        $body = [
            'transaction_amount' => 100,
            'token' => 'token',
            'description' => 'description',
            'installments' => 1,
            'payment_method_id' => 'visa',
            'notification_url' => 'http://test.com',
            'payer' => array(
                'email' => 'test@test.com',
                'identification' => array(
                    'type' => 'CPF',
                    'number' => '19119119100'
                )
            )
        ];

$client->create(body);
?>
```
```node
const client = new MercadoPagoConfig({ accessToken: 'ACCESS_TOKEN' });
const payment = new Payment(client);

const body = {
 transaction_amount: '100',
  token: 'token',
  description: 'description',
  installments: 1,
  payment_method_id: 'visa',
  notification_url: 'http://test.com',
  payer: {
    email: 'test@test.com',
    identification: {
      type: 'CPF',
      number: '19119119100'
    }
  }
};

payment.create({ body: body, requestOptions: { idempotencyKey: '<SOME_UNIQUE_VALUE>' } }).then(console.log).catch(console.log);
```
```java
MercadoPago.SDK.setAccessToken("YOUR_ACCESS_TOKEN");


Payment payment = new Payment();
payment.setTransactionAmount(Float.valueOf(request.getParameter("transactionAmount")))
      .setToken(request.getParameter("token"))
      .setDescription(request.getParameter("description"))
      .setInstallments(Integer.valueOf(request.getParameter("installments")))
      .setPaymentMethodId(request.getParameter("paymentMethodId"))
      .setNotificationUrl("http://requestbin.fullcontact.com/1ogudgk1");


Identification identification = new Identification();----[mla, mlb, mlu, mlc, mpe, mco]----
identification.setType(request.getParameter("docType"))
             .setNumber(request.getParameter("docNumber"));------------ ----[mlm]----
identification.setNumber(request.getParameter("docNumber"));------------


Payer payer = new Payer();
payer.setEmail(request.getParameter("email"))
    .setIdentification(identification);
   
payment.setPayer(payer);


payment.save();


System.out.println(payment.getStatus());


```
```ruby
require 'mercadopago'
sdk = Mercadopago::SDK.new('YOUR_ACCESS_TOKEN')


payment_data = {
 transaction_amount: params[:transactionAmount].to_f,
 token: params[:token],
 description: params[:description],
 installments: params[:installments].to_i,
 payment_method_id: params[:paymentMethodId],
 notification_url: "http://requestbin.fullcontact.com/1ogudgk1",
 payer: {
   email: params[:email],
   identification: {----[mla, mlb, mlu, mlc, mpe, mco]----
     type: params[:docType],------------
     number: params[:docNumber]
   }
 }
}


payment_response = sdk.payment.create(payment_data)
payment = payment_response[:response]


puts payment


```
```csharp
using System;
using MercadoPago.Client.Common;
using MercadoPago.Client.Payment;
using MercadoPago.Config;
using MercadoPago.Resource.Payment;


MercadoPagoConfig.AccessToken = "YOUR_ACCESS_TOKEN";


var paymentRequest = new PaymentCreateRequest
{
   TransactionAmount = decimal.Parse(Request["transactionAmount"]),
   Token = Request["token"],
   Description = Request["description"],
   Installments = int.Parse(Request["installments"]),
   PaymentMethodId = Request["paymentMethodId"],
   NotificationUrl = "http://requestbin.fullcontact.com/1ogudgk1",


   Payer = new PaymentPayerRequest
   {
       Email = Request["email"],
       Identification = new IdentificationRequest
       {----[mla, mlb, mlu, mlc, mpe, mco]----
           Type = Request["docType"],------------
           Number = Request["docNumber"],
       },
   },
};


var client = new PaymentClient();
Payment payment = await client.CreateAsync(paymentRequest);


Console.WriteLine(payment.Status);


```
```python
import mercadopago
sdk = mercadopago.SDK("ACCESS_TOKEN")


payment_data = {
   "transaction_amount": float(request.POST.get("transaction_amount")),
   "token": request.POST.get("token"),
   "description": request.POST.get("description"),
   "installments": int(request.POST.get("installments")),
   "payment_method_id": request.POST.get("payment_method_id"),
   "notification_url" =  "http://requestbin.fullcontact.com/1ogudgk1",
   "payer": {
       "email": request.POST.get("email"),
       "identification": {----[mla, mlb, mlu, mlc, mpe, mco]----
           "type": request.POST.get("type"), ------------
           "number": request.POST.get("number")
       }
   }
}


payment_response = sdk.payment().create(payment_data)
payment = payment_response["response"]


print(payment)
```
```go
accessToken := "{{ACCESS_TOKEN}}"


cfg, err := config.New(accessToken)
if err != nil {
   fmt.Println(err)
   return
}


client := payment.NewClient(cfg)


request := payment.Request{
   TransactionAmount: <transactionAmount>,
   Token: <token>,
   Description: <description>,
   Installments: <installments>,
   PaymentMethodID:   <paymentMethodId>,
   NotificationURL: "https:/mysite.com/notifications/new",
   Payer: &payment.PayerRequest{
      Email: <email>,
      Identification: &payment.IdentificationRequest{
         Type: <type>,
         Number: <number>,
      },
   },
}


resource, err := client.Create(context.Background(), request)
if err != nil {
fmt.Println(err)
}


fmt.Println(resource)
```
```curl
curl -X POST \
   -H 'accept: application/json' \
   -H 'content-type: application/json' \
   -H 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
   'https://api.mercadopago.com/v1/payments' \
   -d '{
         "transaction_amount": 100,
         "token": "ff8080814c11e237014c1ff593b57b4d",
         "description": "Blue shirt",
         "installments": 1,
         "payment_method_id": "visa",
         "issuer_id": 310,
         "notification_url": "http://requestbin.fullcontact.com/1ogudgk1",
         "payer": {
           "email": "test@test.com"


         }
   }'


```
]]]

2. Implemente o receptor de notificações usando o seguinte código como exemplo:

```php
<?php
 MercadoPago\SDK::setAccessToken("ENV_ACCESS_TOKEN");
 switch($_POST["type"]) {
     case "payment":
         $payment = MercadoPago\Payment::find_by_id($_POST["data"]["id"]);
         break;
     case "plan":
         $plan = MercadoPago\Plan::find_by_id($_POST["data"]["id"]);
         break;
     case "subscription":
         $plan = MercadoPago\Subscription::find_by_id($_POST["data"]["id"]);
         break;
     case "invoice":
         $plan = MercadoPago\Invoice::find_by_id($_POST["data"]["id"]);
         break;
     case "point_integration_wh":
         // $_POST contém as informações relacionadas à notificação.
         break;
 }
?>
```

3. Feitas as devidas configurações, a notificação via Webhooks terá o seguinte formato:

> NOTE
>
> Importante
>
> Para o tipo de evento `point_integration_wh`, o formato da notificação muda. [Clique aqui](/developers/pt/docs/mp-point/landing) para consultar a documentação do **Mercado Pago Point**.</br></br>
> </br></br>
> No caso dos eventos de `delivery`, `topic_claims_integration_wh` e `topic_card_id_wh'`, também teremos alguns atributos diferentes na resposta. Veja na tabela abaixo quais são essas particularidades.

```json
{
 "id": 12345,
 "live_mode": true,
 "type": "payment",
 "date_created": "2015-03-25T10:04:58.396-04:00",
 "user_id": 44444,
 "api_version": "v1",
 "action": "payment.created",
 "data": {
     "id": "999999999"
 }
}
```
Isso indica que foi criado o pagamento **999999999** para o usuário **44444** em modo de produção com a versão V1 da API e que esse evento ocorreu na data **2016-03-25T10:04:58.396-04:00**.

| Atributo | Descrição |
| --- | --- |
| **id** | ID de notificação |
| **live_mode** | Indica se a URL informada é valida |
| **type** | Tipo de notificação recebida (payments, mp-connect, subscription, claim, automatic-payments, etc) |
| **date_create** | Data de criação do recurso |
| **user_id**| UserID de vendedor |
| **api_version** | Indica se é uma notificação duplicada ou não |
| **action** | Tipo de notificação recebida, indicando se se trata da atualização de um recurso ou da criação de um novo |
| **data - id** | ID do payment, do merchant_order ou da reclamação |
| **data - customer_id** (card updater)| ID do comprador que teve o cartão atualizado |
| **data - new_card_id** (card updater)| Número atualizado do cartão |
| **data - old_card_id** (card updater)| Número antigo do cartão |
| **attempts** (delivery) | Número de vezes que uma notificação foi enviada |
| **received** (delivery) | Data de criação do recurso |
| **resource** (delivery) | Tipo de notificação recebida, indicando se trata-se da atualização de um recurso ou da criação de um novo |
| **sent** (delivery) | Data de envio da notificação |
| **topic** (delivery) | Tipo de notificação recebida  |
| **resource** (claims) | Tipo de notificação recebida, indicando notificações relacionadas à reclamações feitas por vendas |

4. Caso deseje receber notificações apenas de Webhook e não de IPN, você pode adicionar na `notification_url` o parâmetro `source_news=webhooks`. Por exemplo: https://www.yourserver.com/notifications?source_news=webhooks

## Ações necessárias após receber uma notificação

[TXTSNIPPET][/guides/snippets/test-integration/notification-response]

> NOTE
>
> Importante
>
> No caso do tipo de evento `delivery`, para evitar que o tópico de notificações realize novas tentativas de envio será necessário confirmar o recebimento das mensagens retornando um `HTTP STATUS 200 (OK)` em até **500 ms**. Caso não seja enviada uma mensagem confirmando o recebimento da notificação, **novas tentativas serão feitas em um período de 12 horas**.

Depois de dar um retorno à notificação e confirmar o seu recebimento, você obterá as informações completas do recurso notificado acessando o endpoint correspondente da API:

----[mpe, mco, mlu, mlc]---- 
| Tipo | URL | Documentação |
| --- | --- | --- |
| payment | `https://api.mercadopago.com/v1/payments/[ID]` | [ver documentação](/developers/pt/reference/payments/_payments_id/get) |
| subscription_preapproval | `https://api.mercadopago.com/preapproval` | [ver documentação](/developers/pt/reference/subscriptions/_preapproval/post) |
| subscription_preapproval_plan | `https://api.mercadopago.com/preapproval_plan` | [ver documentación](/developers/pt/reference/subscriptions/_preapproval_plan/post)  |
| subscription_authorized_payment | `https://api.mercadopago.com/authorized_payments` | [ver documentación](/developers/pt/reference/subscriptions/_authorized_payments_id/get) |
| topic_claims_integration_wh | `https://api.mercadopago.com/claim_resource` | [ver documentação](/developers/pt/developers/pt/reference/claims/_data_resource/get) |

------------
----[mlm, mlb]---- 
| Tipo | URL | Documentação |
| --- | --- | --- |
| payment | `https://api.mercadopago.com/v1/payments/[ID]` | [ver documentação](/developers/pt/reference/payments/_payments_id/get) |
| subscription_preapproval | `https://api.mercadopago.com/preapproval` | [ver documentação](/developers/pt/reference/subscriptions/_preapproval/post) |
| subscription_preapproval_plan | `https://api.mercadopago.com/preapproval_plan` | [ver documentación](/developers/pt/reference/subscriptions/_preapproval_plan/post)  |
| subscription_authorized_payment | `https://api.mercadopago.com/authorized_payments` | [ver documentación](/developers/pt/reference/subscriptions/_authorized_payments_id/get) |
| point_integration_wh | - | [ver documentação](/developers/pt/docs/mp-point/integration-configuration/integrate-with-pdv/notifications) |
| topic_claims_integration_wh | `https://api.mercadopago.com/claim_resource` | [ver documentação](/developers/pt/developers/pt/reference/claims/_data_resource/get) |

------------
----[mla]---- 
| Tipo | URL | Documentação |
| --- | --- | --- |
| payment | `https://api.mercadopago.com/v1/payments/[ID]` | [ver documentação](/developers/pt/reference/payments/_payments_id/get) |
| subscription_preapproval | `https://api.mercadopago.com/preapproval` | [ver documentação](/developers/pt/reference/subscriptions/_preapproval/post) |
| subscription_preapproval_plan | `https://api.mercadopago.com/preapproval_plan` | [ver documentación](/developers/pt/reference/subscriptions/_preapproval_plan/post)  |
| subscription_authorized_payment | `https://api.mercadopago.com/authorized_payments` | [ver documentación](/developers/pt/reference/subscriptions/_authorized_payments_id/get) |
| point_integration_wh | - | [ver documentação](/developers/pt/docs/mp-point/integration-configuration/integrate-with-pdv/notifications) |
| delivery | - | [ver documentação](/developers/pt/reference/mp_delivery/_proximity-integration_shipments_shipment_id_accept/put) |
| topic_claims_integration_wh | `https://api.mercadopago.com/claim_resource` | [ver documentação](/developers/pt/developers/pt/reference/claims/_data_resource/get) |

------------

Além disso, especificamente em alertas de fraude, o pedido não deve ser entregue e o cancelamento precisa ser realizado através da [API de cancelamentos](/developers/pt/reference/chargebacks/_payments_payment_id/put).

Na notificação, você receberá um `JSON` com as seguintes informações contendo o id de pagamento para efetuar o cancelamento.

```json


 "description": ".....",
 "merchant_order": 4945357007,
 "payment_id": 23064274473


```

> NOTE
>
> Importante
>
> É possível obter mais detalhes sobre o pedido utilizando a API [Obter pedido](/developers/pt/reference/merchant_orders/_merchant_orders_id/get).

Com essas informações, você poderá realizar as atualizações necessárias na sua plataforma como, por exemplo, atualizar um pagamento aprovado.