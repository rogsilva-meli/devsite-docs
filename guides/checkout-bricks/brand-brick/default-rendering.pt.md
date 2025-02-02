# Renderização padrão

Veja abaixo as informações necessárias para você configurar e renderizar o Brand Brick.

> WARNING
>
> Importante
>
> Para realizar a renderização do Brand Brick, primeiro execute os [passos de inicialização](/developers/pt/docs/checkout-bricks/common-initialization) compartilhados entre todos os Brick. 

## Configurar o Brick

Crie a configuração de inicialização do Brick.

```javascript
bricksBuilder.create(
    "brand",
    "brandBrick_container"
  );
};
```

## Renderizar o Brick

Uma vez criadas as configurações, insira o código abaixo para renderizar o Brick. 

> NOTE
> 
> Importante
>
> O id `brandBrick_container` da _div html_ abaixo, deve corresponder ao valor enviado dentro do método create() da etapa anterior.

[[[
```html
<div id="brandBrick_container"></div>
```
```react-jsx
import { Brand } from '@mercadopago/sdk-react';

<Brand />
```
]]]

O resultado de renderizar o Brick deve ser como na imagem abaixo.

![brand-brick-pt](checkout-bricks/brand-brick-pt.gif)