# Detalhes da aplicação

Para acessar os dados gerais da sua aplicação, navegue até o [Painel do desenvolvedor](/developers/panel/app) e clique sobre o card de uma aplicação para acessar os **Detalhes da aplicação**.

## Dados da aplicação

* **Dados da aplicação**: esta seção exibe os dados básicos da aplicação, incluindo:
  - **User ID**: número (criado automaticamente) de identificação do usuário.
  - **Número da aplicação**: número de identificação da aplicação criado automaticamente.
  - **Integração com**: o produto ou plataforma integrada com a aplicação. 
  - **Modelo da integração** (se houver): as opções de modelo de integração são disponibilizadas de acordo com o produto ou plataforma utilizada. 

### Editar dados

Você pode clicar no botão **Editar dados** para visualizar e editar as **configurações básicas e avançadas** que incluem os dados da sua aplicação e o produto a ser integrado. São elas:

#### Configurações básicas

* **Logotipo**: imagem em formato JPG ou PNG de até 1MB.
* **Nome de aplicação**: para identificar suas aplicações com mais facilidade (máximo de 50 caracteres).
* **Nome curto da aplicação**: identificador secundário da aplicação (este campo não pode ter espaços ou caracteres especiais). 
* **Descrição da aplicação** (máximo de 150 caracteres).
* **Setor**: escolha a categoria que melhor descreve seu negócio.
* **URL do site em produção** (opcional).
* **Solução de pagamento a ser integrada**: edite a solução de pagamento a ser integrada entre **Pagamentos online** e **Pagamentos presenciais**.
  - **Pagamentos online**: se você pretende utilizar uma plataforma de comércio eletrônico, marque **Sim**. Em seguida, selecione a **plataforma** com a qual você irá integrar. Por fim, escolha o **produto** que está integrando. Caso você não esteja utilizando uma plataforma de comércio eletrônico, marque **Não** e selecione o **produto** que está integrando. Opcionalmente, você poderá selecionar o(s) modelo(s) de integração.
  - **Pagamentos presenciais**: selecione o **produto** que você está integrando. Se você selecionar a opção Código QR, opcionalmente você também poderá escolher o(s) modelo(s) de integração.

#### Configurações avançadas

* **URLs de redirecionamento**: URLs (em https) na qual você deseja receber o código de autorização quando sua integração for configurada como Marketplace ou realizada por meio do fluxo **Authorization code** de OAuth. **Certifique-se de que seja uma URL estática**. Veja [OAuth](/developers/pt/docs/security/oauth/introduction) para mais detalhes. 
* **Usar o fluxo de código de autorização com o PKCE**: caso a integração seja realizada por meio do fluxo **Authorization code** de OAuth, você poderá habilitar o PKCE (_Proof Key for Code Exchange_) para que seja gerado um código secreto adicional a ser usado durante o processo de autorização. Veja [Configurar PKCE](/developers/pt/docs/security/oauth/creation#:~:text=Access%20Token.-,Configurar%20PKCE,-O%20PKCE%20) para mais detalhes.
* **Permissões da aplicação**: opções de acesso da sua aplicação, como **leitura**, **acesso offline** e **escrita**. Por padrão, sua aplicação é criada com todas as permissões ativadas, mas você pode desativar uma permissão clicando na caixa de seleção referente à permissão que você deseja alterar.

### Excluir aplicação

Para remover uma aplicação, siga estas etapas:

1. Acesse a página "Editar aplicação". 
2. Role até o final da página e clique no botão **Excluir aplicação**. 
Dessa forma, a aplicação será excluída com sucesso.

> WARNING
>
> Atenção
>
> Ao excluir uma aplicação, é importante ter em mente que sua loja perderá a capacidade de receber pagamentos por meio da integração associada a essa aplicação. Além disso, todas as configurações, incluindo as credenciais associadas, serão perdidas. **Uma vez excluída uma aplicação, não há como recuperá-la**. <br><br>

## Qualidade da integração

Nesta seção, vamos garantir que sua aplicação atenda aos requisitos de qualidade e segurança necessários para proporcionar a melhor experiência tanto para vendedores quanto para compradores com o Mercado Pago. [Clique aqui](/developers/pt/guides/additional-content/homologator/homologator) e conheça todas as informações necessárias para saber como homologar corretamente a sua integração.

----[mla, mlm, mlu, mco, mlc, mpe]----

> WARNING
>
> Atenção
>
> Antes de iniciar a avaliação, certifique-se de que a homologação da aplicação em ambiente de produção foi concluída, incluindo a realização de pelo menos um pagamento produtivo.
> <br><br>
>É necessário que seja uma aplicação em que haja um produto a ser integrado daqueles em que a ferramenta de medição está disponível. Por enquanto, a ferramenta para medir a qualidade da integração só está disponível para integrações com o [Checkout Pro](/developers/pt/docs/checkout-pro/landing) [Checkout API,](/developers/pt/docs/checkout-api/landing) [Checkout Bricks](/developers/pt/docs/checkout-bricks/landing) e [Mercado Pago Point.](/developers/pt/docs/mp-point/landing)

------------
----[mlb]----

> WARNING
>
> Atenção
>
> Antes de iniciar a avaliação, certifique-se de que a homologação da aplicação em ambiente de produção foi concluída, incluindo a realização de pelo menos um pagamento produtivo.
> <br><br>
> É necessário que seja uma aplicação em que haja um produto a ser integrado daqueles em que a ferramenta de medição está disponível. Por enquanto, a ferramenta para medir a qualidade da integração só está disponível para integrações com o [Checkout Pro](/developers/pt/docs/checkout-pro/landing) [Checkout Transparente,](/developers/pt/docs/checkout-api/landing) [Checkout Bricks](/developers/pt/docs/checkout-bricks/landing) e [Mercado Pago Point.](/developers/pt/docs/mp-point/landing)

------------

### Avaliar a qualidade

O **status** indica as etapas indicadas para poder avaliar a qualidade de sua aplicação e, após a avaliação, a **pontuação** indica o quão segura e alinhada com as boas práticas de integração do Mercado Pago é a configuração da sua aplicação.

Ao clicar em **Avaliar a qualidade**, você será redirecionado para a ferramenta de medição e iniciará o processo de análise da sua integração. Durante essa análise, é importante identificar os pontos de melhoria e realizar as alterações necessárias em sua integração. Este processo envolve a revisão de uma série de campos associados.

Acesse [Qualidade da integração](/developers/pt/docs/integration-quality) e conheça todas as informações necessárias para saber como medir a qualidade da sua aplicação.

> Após implementar melhorias, é necessário clicar novamente em **Atualizar pontuação** para reavaliar sua integração e verificar se ela atende aos padrões exigidos. 

## Teste de integração

Nesta seção, você tem um passo a passo para testar sua integração, permitindo que você valide se ela está atendendo aos requisitos necessários com base no produto integrado. 

Além disso, você tem links diretos para a documentação correspondente, bem como uma barra de status que lhe permitirá visualizar facilmente o seu progresso.

![tela de validação de teste de integração](/images/dashboard/testing-validation-pt.gif)