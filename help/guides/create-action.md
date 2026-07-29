---
title: Criar uma ação do zero
description: Defina os metadados da ação, implemente o manipulador, conecte um widget de EDS, teste-o e implante-o com os aplicativos Adobe LLM.
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '1141'
ht-degree: 0%

---


# Criar uma ação do zero {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

>[!NOTE]
>
>Este guia pressupõe uma familiaridade básica com os Serviços de entrega de borda (EDS) da Adobe. Se você é novo no EDS, primeiro leia o [Tutorial do desenvolvedor do EDS](https://www.aem.live/developer/tutorial) e o [Explorar blocos](https://www.aem.live/docs/exploring-blocks) para aprender o básico — blocos, a função `decorate` e a estrutura do projeto do EDS — antes de conectar um widget.

Use este guia para adicionar um recurso que o Agente de integração não criou. Você definirá a ação em [!DNL LLM Apps], gravará o manipulador no repositório vinculado, adicionará um widget, se necessário, e o testará e implantará.

**Jornada:** Planeje a ação → crie seus metadados → escreva o manipulador → conecte o dispositivo → teste localmente → implante e teste o plug-in.

Para seu primeiro aplicativo, comece com [Crie seu primeiro aplicativo com o Agente de integração](/help/guides/create-app.md).

## Antes de começar

Você precisa:

- Um aplicativo LLM existente.
- Um repositório de manipulador vinculado.
- O repositório foi clonado localmente com suas dependências instaladas.
- Um projeto EDS se a ação exibir um widget.
- Uma API ou fonte de dados limpa para os resultados da produção.

## Planejar a ação

Uma ação deve executar uma tarefa de limpeza do usuário. Antes de abrir a interface do usuário, defina:

- **Intenção** — o que o usuário está tentando fazer.
- **Descrição** — quando a plataforma LLM deve selecionar esta ação.
- **Entradas** — as informações mínimas necessárias do usuário.
- **Resultado** — o texto e os dados estruturados retornados pelo manipulador.
- **Comportamento** — se a ação lê dados, altera dados ou chama sistemas externos.
- **Widget** — se o resultado precisa de uma interface visual.

Por exemplo, uma ação **Pesquisar Produtos** pode usar:

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

Mantenha as tarefas relacionadas, mas diferentes, separadas. A pesquisa e a compra de produtos não devem ser uma única ação, pois têm requisitos de entrada, riscos e confirmação diferentes.

## Criar os metadados da ação

Abra o aplicativo e selecione **[!UICONTROL Ações]** e, em seguida, **[!UICONTROL Criar Ação]**.

O editor contém as guias **[!UICONTROL Ação]** e **[!UICONTROL Metadados do widget]**.

### Inserir informações básicas

![Criar Ação — informações básicas](/help/assets/guide-create-action/action-basic-info.png)

Insira:

- **[!UICONTROL Nome da ação]** — um nome de tarefa curto, como *Pesquisar Produtos*.
- **[!UICONTROL Descrição]** — explique quando usar a ação e o que ela retorna.

Uma descrição útil é específica:

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

Evite descrições vagas como *Obtém informações sobre o produto*. A plataforma LLM usa a descrição para escolher entre as ações.

### Selecionar anotações

As anotações descrevem o comportamento da ação:

- **Dica destrutiva** — a ação pode excluir ou alterar dados permanentemente.
- **Idempotente (mesmos argumentos = sem efeito extra)** — repetir a mesma solicitação tem o mesmo efeito.
- **Abrir dica do mundo** — a ação se comunica com sistemas externos.
- **Dica somente leitura** — a ação não altera os dados.

Selecione somente anotações que sejam verdadeiras. Por exemplo, a pesquisa de produtos normalmente é somente leitura, idempotente e de mundo aberto.

### Adicionar metadados do OpenAI

Insira mensagens curtas exibidas enquanto a ação é executada e após sua conclusão:

```text
Invoking: Searching products...
Invoked: Products found
```

Para ações com widgets, adicione **[!UICONTROL Descrição do widget]**. Isso é diferente da descrição da ação:

- **A descrição da ação** ajuda o modelo a decidir quando invocar a ação.
- A **Descrição do widget** mapeia para `_meta["openai/widgetDescription"]` e resume o que o componente renderizado mostra, reduzindo a narração repetida.

[!DNL LLM Apps] aplica isso como metadados de componente. Não o retorne do manipulador.

### Configurar visibilidade

- **[!UICONTROL Expor ao modelo de IA]** permite que o modelo selecione a ação.
- **[!UICONTROL Mostrar como widget na superfície do aplicativo]** exibe o widget configurado.

Desativa a visibilidade do widget quando a ação retorna somente texto.

### Adicionar parâmetros de entrada

Adicione um parâmetro para cada valor que o manipulador aceita. Todos os parâmetros precisam:

- **Nome** — a chave recebida pelo manipulador.
- **Tipo** — String, Número, Inteiro ou Booleano.
- **Descrição** — como o modelo deve extrair o valor.
- **Obrigatório** — se a ação pode ser executada sem ela.

Para **Pesquisar Produtos**:

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

Use nomes de parâmetros estáveis. Alterar um nome também requer alterar o manipulador e seus testes.

### Configurar análises

Habilite **[!UICONTROL Coletar intenção de usuário]** quando quiser que a análise inclua um resumo da conversa que levou à ação.

![Criar Ação — análise de intenção de usuário](/help/assets/guide-create-action/action-analytics-user-intent.png)

Para obter definições completas de campo, consulte [Campos de ação e widget](/help/reference/reference-docs.md).

## Configurar o widget

Ignorar esta seção para uma ação somente texto.

Abra Os **[!UICONTROL Metadados Do Widget]**.

![Criar Ação — metadados do widget](/help/assets/guide-create-action/widget-metadata.png)

Configurar o:

- **Tipo** — selecione EDS.
- **Domínio do widget** — a origem EDS que hospeda o widget.
- **Borda preferencial** — solicita um contêiner com bordas no host.
- **URL do Script** — o ponto de entrada do widget EDS.
- **URL do Widget** — a página de EDS publicada para esta ação.

Os URLs típicos são:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Conceda somente as permissões do navegador e os domínios CSP necessários.

![Criar Ação — permissões e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Se a página do projeto ou widget do EDS ainda não existir, conclua [Trazer seu próprio projeto EDS](/help/guides/bring-your-own-eds.md) e retorne à ação.

## Salvar a ação

Selecione **[!UICONTROL Criar nova ação]**. A ação aparece na página Ações com um selo **Não implantado**.

Neste ponto, os metadados existem, mas a ação ainda precisa de um manipulador.

## Implementar o manipulador

Clonar o repositório do manipulador vinculado e instalar suas dependências:

```bash
npm install
```

Criar:

```text
actions/
└── search-products/
    └── index.js
```

O nome da pasta deve corresponder ao identificador de código da ação mostrado no editor de ações.

Para obter o contrato de resultado completo e a relação manipulador-widget, consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md).

### Contrato do manipulador

Exportar uma função assíncrona:

```javascript
module.exports = async (args) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

O manipulador recebe os parâmetros definidos na interface do usuário do.

### Retornar `content`

`content` é o fallback de texto lido pela plataforma LLM:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Sempre retornar `content` úteis, mesmo quando a ação tiver um widget.

### Retornar `structuredContent`

`structuredContent` é um objeto simples consumido pelo widget:

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

A forma deve corresponder ao que o bloco EDS lê de `bridge.toolResult`.

### Conectar uma API

Mantenha o acesso protegido à API no manipulador do lado do servidor. Carregue a configuração do ambiente de tempo de execução e use uma origem HTTPS fixa.

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

Não coloque credenciais de API no código-fonte, nos metadados de ação, no JavaScript widget, nos logs ou em erros voltados para o usuário.

Para código de produção, valide a resposta upstream completa antes de mapear campos aprovados para `structuredContent`.

## Adicionar testes de manipulador

Criar o teste correspondente:

```text
test/
└── actions/
    └── search-products.test.js
```

Testar pelo menos:

- Entrada válida.
- Entrada ausente ou inválida.
- Resultados vazios.
- Tempo limite ou falha da API.
- Dados de API malformados.
- A forma `structuredContent` esperada pelo widget.

Executar:

```bash
npm test
```

Para layout de projeto e teste de MCP local, consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md).

## Testar a ação localmente

Executar:

```bash
npm run dev:local
```

Sem um `actions.json` local, o servidor descobre o manipulador com o mínimo de metadados e sem validação do esquema de entrada.

Use o Inspetor de MCP ou `curl` para:

1. Listar as ações registradas.
2. Chame a nova ação com argumentos representativos.
3. Verificar `content` e `structuredContent`.
4. Teste solicitações inválidas e vazias.

## Conectar e testar o widget

Se a ação tiver um widget:

1. Faça com que o widget leia o `structuredContent` do manipulador.
2. Renderize valores externos com APIs DOM seguras, como `textContent`.
3. Adicionar estados de carregamento, vazio e erro.
4. Visualize a página de EDS localmente.
5. Verifique a CSP, o CORS e os URLs do widget.

Consulte [Trazer seu próprio projeto de EDS](/help/guides/bring-your-own-eds.md).

## Implantar e testar

1. Confirme e envie por push as alterações do manipulador e do widget.
2. [Implante o aplicativo](/help/guides/deploy-your-app.md) para o Preparo.
3. [Testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
4. Verifique os prompts que devem e não devem invocar a ação.
5. Após o Êxito do preparo, implante para produção.

Se os metadados existirem sem um manipulador correspondente, a implantação registrará a ação com um stub padrão. Adicione o manipulador antes de disponibilizar a ação aos usuários.
- [Guia: configurar o widget (EDS)](/help/guides/widgets.md)
