# Transferências SPEI

----[mlb]----
Com o Checkout Transparente do Mercado Pago, é possível oferecer pagamentos via **Transferências SPEI**. Este serviço permite fazer pagamentos de qualquer banco ou instituição financeira usando sua CLABE **(Clave Bancaria Estandarizada)**.

------------

----[mla, mlm, mpe, mco, mlu, mlc]----
Com o Checkout API do Mercado Pago, é possível oferecer pagamentos via **Transferências SPEI**. Este serviço permite fazer pagamentos de qualquer banco ou instituição financeira usando sua CLABE **(Clave Bancaria Estandarizada)**.

------------

Para uma lista detalhada de todos os meios de pagamento disponíveis para integração, envie um **GET** com seu `access_token` ao endpoint [/v1/payment_methods](/developers/pt/reference/payment_methods/_payment_methods/get)  e execute a requisição ou, se preferir, faça a requisição usando um dos nossos SDKs.

[[[
```php
<?php

MercadoPago\SDK::setAccessToken("ENV_ACCESS_TOKEN");

$payment_methods = MercadoPago::get("/v1/payment_methods");

?>
```
```node
var Mercadopago = require('mercadopago');
Mercadopago.configurations.setAccessToken(config.access_token);

var response = await Mercadopago.payment_methods.listAll();
var payment_methods = response.body;

```
```java
MercadoPagoConfig.setAccessToken("ENV_ACCESS_TOKEN");

PaymentMethodClient client = new PaymentMethodClient();
client.list();

```
```ruby
require 'mercadopago'
sdk = Mercadopago::SDK.new('ENV_ACCESS_TOKEN')

payment_methods_response = sdk.payment_methods.get()
payment_methods = payment_methods_response[:response]

```
```csharp
using MercadoPago.Client.PaymentMethod;
using MercadoPago.Config;
using MercadoPago.Resource;
using MercadoPago.Resource.PaymentMethod;

MercadoPagoConfig.AccessToken = "ENV_ACCESS_TOKEN";

var client = new PaymentMethodClient();
ResourcesList<PaymentMethod> paymentMethods = await client.ListAsync();

```
```python
import market
sdk = Mercadopago.SDK("ACCESS_TOKEN")

payment_methods_response = sdk.payment_methods().list_all()
payment_methods = payment_methods_response["response"]

```
```curl
curl -X GET \
-H 'accept: application/json' \
-H 'content-type: application/json' \
-H 'Authorization: Bearer ENV_ACCESS_TOKEN' \
'https://api.mercadopago.com/v1/payment_methods' \

```
]]]

Para oferecer **pagamentos via Transferências SPEI**, siga as etapas abaixo.

## Importar MercadoPago.js

Para realizar a integração do Checkout Transparente é preciso capturar os dados necessários para processar o pagamento.

Esta captura é feita a partir da inclusão da biblioteca MercadoPago.js em seu projeto, seguida do formulário de pagamento. Utilize o código abaixo para importar a biblioteca MercadoPago.js antes de adicionar o formulário de pagamento.

[[[
```html
<body>
<script src="https://sdk.mercadopago.com/js/v2"></script>
</body>

```
```bash
npm install @mercadopago/sdk-js

```
]]]

## Configurar credencial

As credenciais são senhas únicas com as quais identificamos uma integração na sua conta. Servem para capturar pagamentos em lojas virtuais e outras aplicações de forma segura.

Esta é a primeira etapa de uma estrutura completa de código que deverá ser seguida para a correta integração dos pagamentos. Atente-se aos blocos abaixo para adicionar aos códigos conforme indicado.


[[[
```javascript
const mp = new MercadoPago('YOUR_PUBLIC_KEY');

```
]]]


## Adicionar formulário de pagamento

Com a **biblioteca MercadoPago.js** incluída e as **credenciais configuradas**, adicione o formulário de pagamento abaixo ao seu projeto para garantir a captura segura dos dados dos compradores. Nesta etapa é importante utilizar a lista que você consultou para obter os meios de pagamento disponíveis para criar as opções de pagamento que deseja oferecer.

[[[
```html
<form id="form-checkout" action="/process_payment" method="post">
    <div>
      <div>
        <label for="payerFirstName">Name</label>
        <input id="form-checkout__payerFirstName" name="payerFirstName" type="text">
      </div>
      <div>
        <label for="payerLastName">Last name</label>
        <input id="form-checkout__payerLastName" name="payerLastName" type="text">
      </div>
      <div>
        <label for="email">E-mail</label>
        <input id="form-checkout__email" name="email" type="text">
      </div>
    <div>
      <div>
        <input type="hidden" name="transactionAmount" id="transactionAmount" value="5000">
        <input type="hidden" name="description" id="description" value="Nome do Produto">
        <br>
        <button type="submit">Pay</button>
      </div>
    </div>
  </form>

```
]]]

## Obter tipos de documentos

Após configurar a credencial, é preciso obter os tipos de documento que farão parte do preenchimento do formulário para pagamento.

Incluindo o elemento do tipo `select` com o id: id = `docType` que está no formulário, será possível preencher automaticamente as opções disponíveis quando chamar a função a seguir:


[[[
```javascript
(async function getIdentificationTypes() {
try {
const identificationTypes = await mp.getIdentificationTypes();
const identificationTypeElement = document.getElementById('form-checkout__identificationType');

createSelectOptions(identificationTypeElement, identificationTypes);
} catch (e) {
return console.error('Error getting identificationTypes: ', e);
}
})();

function createSelectOptions(elem, options, labelsAndKeys = { label: "name", value: "id" }) {
const { label, value } = labelsAndKeys;

elem.options.length = 0;

const tempOptions = document.createDocumentFragment();

options.forEach(option => {
const optValue = option[value];
const optLabel = option[label];

const opt = document.createElement('option');
opt.value = optValue;
opt.textContent = optLabel;

tempOptions.appendChild(opt);
});

elem.appendChild(tempOptions);
}

```
]]]

## Enviar pagamento

Ao finalizar a inclusão do formulário de pagamento e a obtenção dos tipos de documento, é necessário encaminhar o e-mail do comprador, tipo e número de documento, o meio de pagamento utilizado e o detalhe do valor a ser pago utilizando nossa **API de Pagamentos** ou um de nossos **SDKs**.

Para configurar pagamentos via **Transferências SPEI**, envie um **POST** com os seguintes parâmetros ao endpoint [/v1/payments](/developers/pt/reference/payments/_payments/post) e execute a requisição ou, se preferir, utilize um de nossos SDKs abaixo.

[[[
```php
<?php
require '../vendor/autoload.php';

MercadoPago\SDK::setAccessToken("ENV_ACCESS_TOKEN");

$payment = new MercadoPago\Payment();
$payment->transaction_amount = 5000;
$payment->description = "Título del producto";
$payment->payment_method_id = "clabe";

$payer = new MercadoPago\Payer();
$payer->email = $_POST['email'];
$payer->first_name = $_POST['payerFirstName']
$payer->last_name = $_POST['payerLastName']
$payer->entity_type = "individual";

$payment->payer = $payer;

$payment->save();

$response = array(
    'status' => $payment->status,
    'payment_link' => $payment->transaction_details->external_resource_url,
    'id' => $payment->id
);
echo json_encode($response);

?>

```
```node
const mercadopago = require('mercadopago');
mercadopago.configurations.setAccessToken(config.access_token);
 
var payment = req.body;

var payment_data = {
 	transaction_amount: 5000,
 	description: 'Título del producto',
 	payment_method_id: 'clabe',
 	payer: {
 		entity_type: 'individual',
 		email: payment.email,
 		first_name: payment.payerFirstName,
    last_name: payment.payerLastName
 	}
};

var payment = mercadopago.payment.save(payment_data)
 	.then(function(response) {
 		res.status(response.status).json({
 			status: response.body.status,
 			status_detail: response.body.status_detail,
 			id: response.body.id,
 		});
 	})
 	.catch(function(error) {
 		res.status(error.status).send(error);
 	});

var payment_link = payment.transaction_details.external_resource_url;

```
```java
MercadoPagoConfig.setAccessToken("YOUR_ACCESS_TOKEN");

  PaymentClient client = new PaymentClient();

  PaymentPayerRequest payer =
  	PaymentPayerRequest.builder()
  	.type("customer")
  	.email(request.getEmail())
    .firstName(request.getPayerFirstName())
    .lastName(request.getPayerLastName())
  	.entityType("individual")
  	.build();

  PaymentCreateRequest paymentCreateRequest = PaymentCreateRequest.builder()
  	.transactionAmount(new BigDecimal(5000))
  	.description("description")
  	.paymentMethodId("clabe")
  	.payer(payer)
  	.build();

  Payment payment = client.create(paymentCreateRequest);
  String paymentLink = payment.transactionDetails.externalResourceUrl;

```
```ruby
require 'mercadopago'
sdk = Mercadopago::SDK.new('ACCESS_TOKEN')

payment_data = {

  transaction_amount: 5000,
  description: "description",
  payment_method_id: "clabe",
  payer: {
    type: "customer",
    email: params[: email],
    entity_type: "individual",
    first_name: params[: payerFirstName]
    last_name: params[: payerLastName]
  }
}

payment_response = sdk.payment.create(payment_data)
payment = payment_response[: response]
payment_link = payment.transaction_details.external_resource_url;

```
```csharp
using System;
using MercadoPago.Client.Common;
using MercadoPago.Client.Payment;
using MercadoPago.Config;
using MercadoPago.Resource.Payment;

MercadoPagoConfig.AccessToken = "ACCESS_TOKEN";

var client = new PaymentClient();

var payer = new PaymentPayerRequest() {
    Type = "customer",
    Email = request.Email,
    EntityType = "individual",
    FirstName = request.PayerFirstName,
    LastName = request.PayerLastName
};

var paymentCreateRequest = new PaymentCreateRequest() {
  TransactionAmount = 5000,
    Description = "description",
    PaymentMethodId = "clabe",
    AdditionalInfo = additionalInfo,
    CallbackUrl = "https://your-site.com",
    Payer = payer
};

var payment = await client.CreateAsync(paymentCreateRequest);
var paymentLink = payment.TransactionDetails.externalResourceUrl; 

```
```python
import mercadopago
sdk = mercadopago.SDK("ACCESS_TOKEN")
 
payment_data = {
   "transaction_amount": 5000,
   "description": "description",
   "payment_method_id": "clabe",
   "payer": {
       "type": "customer",
       "email": request.POST.get("email"),
       "entity_type": "individual",
       "first_name": request.POST.get("first_name"),
       "last_name": request.POST.get("last_name"),
   }
}
 
payment_response = sdk.payment().create(payment_data)
payment = payment_response["response"]
Payment_link = payment.transaction_details.external_resource_url

```
```curl
curl --location --request POST 'https://api.mercadopago.com/v1/payments' \
-H 'Authorization: Bearer ENV_ACCESS_TOKEN' \
-H 'Content-Type: application/json' \
--d '{
    "transaction_amount": 5000,
    "description": "Título del producto",
    "payment_method_id": "clabe",
    "payer": {
        "email": "test_user_19549678@testuser.com",
        "entity_type": "individual",
        "first_name": "John",
        "last_name": "Doe"
    }
}'

```
]]]

A resposta mostrará o status `pendente` até que o comprador realize o pagamento. Além disso, na resposta à requisição, o parâmetro `payment_method.data.external_resource_url` devolverá uma URL que contém as instruções para que o comprador realize o pagamento. Você pode redirecioná-lo para este mesmo link para conclusão do fluxo de pagamento. Veja abaixo um exemplo de retorno.

[[[
```json
{
    "id": 51096146182,
    "version": null,
    "date_created": "2023-05-10T13:43:14.586-04:00",
    ...
    "payment_method_id": "clabe",
    "payment_type_id": "bank_transfer",
    "payment_method": {
        "id": "clabe",
        "type": "bank_transfer",
        "data": {
            "reference_id": "6410293433784980810",
            "external_reference_id": "1009",
            "external_resource_url": "https://www.mercadopago.com.mx/payments/51096146182/ticket?caller_id=34728475&hash=f3a8630a-f06a-48e4-b2a6-f95750af7346"
        }
    },
    "status": "pending",
    ...
}

```
]]]

> NOTE
>
> Importante
>
> Caso receba um erro ao gerar um pagamento, consulte a lista de possíveis erros na seção [Referência de API](/developers/pt/reference/payments/_payments/post).

## Visualização do pagamento

Para que o usuário possa realizar a transferência, direcione-o diretamente para a URL disponível em `external_resource_url` ou disponibilize-a por meio de um botão, seguindo o exemplo abaixo.

[[[
```html
<a href="https://www.mercadopago.com.mx/payments/51096146182/ticket?caller_id=34728475&hash=f3a8630a-f06a-48e4-b2a6-f95750af7346" target="_blank">Pagar com Transferências SPEI</a>

```
]]]