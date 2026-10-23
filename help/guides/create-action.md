---
title: Criar uma ação do zero
description: Defina os metadados da ação, implemente o manipulador, conecte um widget de EDS, teste-o e implante-o com os aplicativos Adobe LLM.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '1476'
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

Adicione um recurso que a plataforma não criou. Defina a ação em [!DNL LLM Apps], grave o manipulador no repositório vinculado e adicione um widget, se necessário.

**Neste guia:** Aprenda a definir uma ação, implementar seu manipulador e conectar um widget opcional. Teste a ação localmente, implante o aplicativo e tente a ação em uma plataforma LLM compatível.

Para seu primeiro aplicativo, comece com [Configuração de infraestrutura](/help/guides/create-app.md) e depois [Criar ações](/help/guides/creating-actions.md). O guia de ações Criar explica a geração automática de um site e a criação manual.

## Antes de começar

Você precisa:

- Um aplicativo LLM existente.
- Um repositório de manipulador vinculado.
- Um clone local do repositório com suas dependências instaladas.
- Um projeto EDS se a ação exibir um widget.
- Uma API ou fonte de dados limpa para os resultados da produção.

## Planejar a ação

Projete cada ação em torno de uma tarefa de usuário limpa. Antes de abrir a interface do usuário, defina:

- **Intenção** — o que o usuário está tentando fazer.
- **Descrição** — quando selecionar a ação.
- **Entradas** — as informações mínimas necessárias do usuário.
- **Resultado** — o texto e os dados estruturados retornados pelo manipulador.
- **Comportamento** — se a ação lê dados, altera dados ou chama sistemas externos.
- **Widget** — se o resultado precisa de uma interface visual.

Por exemplo, use este plano para uma ação **Pesquisar Produtos**:

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

Mantenha as tarefas relacionadas, mas diferentes, separadas. Use ações separadas para pesquisa de produto e compra de produto porque as tarefas têm diferentes requisitos de entrada, riscos e confirmação.

## Criar os metadados da ação

Abra o aplicativo e selecione **[!UICONTROL Ações]** e, em seguida, **[!UICONTROL Criar Ação]**.

A caixa de diálogo de criação contém as guias **[!UICONTROL Ação]** e **[!UICONTROL Metadados do widget]**. Expanda cada seção para configurar seus campos.

### Inserir informações básicas

![Criar Ação — informações básicas](/help/assets/guide-create-action/action-basic-info.png)

Insira:

- **[!UICONTROL Nome da ação]** — um nome de tarefa curto, como *Pesquisar Produtos*.
- **[!UICONTROL Descrição]** — explique quando usar a ação e o que ela retorna.

A caixa de diálogo deriva um **[!UICONTROL Identificador de código]** do nome, como `search-products`. O identificador é corrigido após a criação e deve ser exclusivo no aplicativo. Use o identificador exato para a pasta do manipulador.

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

Para ações com widgets, adicione **[!UICONTROL Descrição do widget]**. A descrição do widget é diferente da descrição da ação:

- **A descrição da ação** ajuda o modelo a decidir quando invocar a ação.
- A **Descrição do widget** mapeia para `_meta["openai/widgetDescription"]` e resume o que o componente renderizado mostra, reduzindo a narração repetida.

[!DNL LLM Apps] aplica a descrição do widget como metadados do componente. Não retorne a descrição do manipulador.

### Configurar visibilidade

- **[!UICONTROL Expor ao modelo de IA]** permite que o modelo selecione a ação.
- **[!UICONTROL Mostrar como widget na superfície do aplicativo]** exibe o widget configurado.

Para uma ação somente texto, selecione também **[!UICONTROL Nenhum widget]** em **[!UICONTROL Metadados do widget]** > **[!UICONTROL Tipo]**. Os controles de visibilidade não escolhem o tipo de widget da ação.

### Adicionar parâmetros de entrada

Adicione um parâmetro para cada valor que o manipulador aceita. Todos os parâmetros precisam:

- **Nome** — a chave que o manipulador recebe.
- **Tipo** — String, Número, Inteiro ou Booleano.
- **Descrição** — como extrair o valor.
- **Obrigatório** — se a ação requer o valor.

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

### Configurar análises {#configure-analytics}

Habilite **[!UICONTROL Coletar intenção de usuário]** quando quiser que a análise inclua um resumo da conversa que levou à ação.

Se você configurar seu próprio [!DNL Adobe Analytics] ou [!DNL Customer Journey Analytics], a intenção do usuário também será enviada para lá. Consulte [Configurar Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md).

A coleta de intenção do usuário é ativada por padrão. Revise a **[!UICONTROL Descrição da intenção do usuário]**, que contém as instruções para solicitar o resumo. Limite as instruções a uma intenção não confidencial e anônima e descreva sua coleta de dados na sua política de privacidade. A descrição aceita até 2.000 caracteres.

![Criar Ação — análise de intenção de usuário](/help/assets/guide-create-action/action-analytics-user-intent.png)

Para obter definições completas de campo, consulte [Campos de ação e widget](/help/reference/reference-docs.md).

## Configurar o widget

Para uma ação somente texto, abra **[!UICONTROL Metadados do widget]** e defina **[!UICONTROL Tipo]** como **[!UICONTROL Nenhum widget]**. Em seguida, ignore as configurações restantes do widget.

Abra Os **[!UICONTROL Metadados Do Widget]**.

![Criar Ação — metadados do widget](/help/assets/guide-create-action/widget-metadata.png)

Configurar o:

- **[!UICONTROL Tipo]** — selecione **[!UICONTROL Bloco Edge Delivery Services]** para um widget interativo.
- **[!UICONTROL Domínio do widget]** — a origem da sandbox do widget. A interface o identifica conforme necessário para o envio do aplicativo e exclusivo por aplicativo; não é o mesmo que o URL da página de EDS.
- **Borda preferencial** — solicita um contêiner com bordas no host.

Expanda **[!UICONTROL URLs de modelo]** e insira:

- **[!UICONTROL URL do Script]** — o ponto de entrada do widget EDS.
- **[!UICONTROL URL do Widget]** — a página de EDS publicada para esta ação.

![Criar nova ação — URLs de script e widget para um widget EDS](/help/assets/guide-create-action/widget-template-urls.png)

Os URLs típicos são:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Conceda somente as permissões do navegador e os domínios CSP necessários.

Expanda **[!UICONTROL Permissões (específicas do MCP)]** somente se o widget precisar de recursos do navegador, como localização geográfica. Deixe as permissões não usadas desativadas.

![Criar nova ação — permissões opcionais do navegador](/help/assets/guide-create-action/widget-permissions.png)

Em **[!UICONTROL Configuração da CSP]**, adicione as origens necessárias para ativos e solicitações de rede. Um widget que carrega recursos externos pode não ser renderizado se o CSP estiver incompleto.

![Criar nova ação — listas de domínios da política de segurança de conteúdo](/help/assets/guide-create-action/widget-csp.png)

Se a página do projeto ou widget do EDS ainda não existir, conclua [Trazer seu próprio projeto EDS](/help/guides/bring-your-own-eds.md) e retorne à ação.

## Salvar a ação

Selecione **[!UICONTROL Criar nova ação]**. A ação aparece na página Ações com um selo **Não implantado**.

Salvar cria os metadados, mas a ação ainda precisa de um manipulador.

Quando você reabre a ação salva, o editor tem **[!UICONTROL detalhes do Widget de LLM]**, **[!UICONTROL detalhes da ação]** e **[!UICONTROL guias do Analytics]**. Para salvar edições posteriores, selecione **[!UICONTROL Salvar alterações]**. A caixa de diálogo de criação e o editor de ação salva têm layouts diferentes.

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

A plataforma LLM lê `content` como fallback de texto:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Sempre retornar `content` úteis, mesmo quando a ação tiver um widget.

### Retornar `structuredContent`

O widget consome `structuredContent` como um objeto simples:

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

Manter chamadas de API no manipulador do lado do servidor. Este exemplo chama uma API de produto pública. Configure a origem HTTPS da API em uma [variável de aplicativo](/help/guides/app-variables.md) chamada `PRODUCT_API_ORIGIN`. O exemplo aceita os parâmetros `query` e `category` definidos acima, requer pelo menos um parâmetro e não requer credenciais.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '', category = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  const normalizedCategory = typeof category === 'string' ? category.trim() : '';
  if (typeof query !== 'string' || typeof category !== 'string'
      || (!normalizedQuery && !normalizedCategory)
      || normalizedQuery.length > 200 || normalizedCategory.length > 100) {
    return {
      isError: true,
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
  if (!apiOrigin) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(apiOrigin);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  if (normalizedQuery) {
    url.searchParams.set('query', normalizedQuery);
  }
  if (normalizedCategory) {
    url.searchParams.set('category', normalizedCategory);
  }

  const response = await fetch(url, {
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

Não coloque credenciais de API no código-fonte, nos metadados de ação, no JavaScript widget, nos logs ou em erros voltados para o usuário. Atualmente, a interface do usuário **[!UICONTROL Variáveis e segredos]** aceita somente variáveis não confidenciais, não segredos. Não armazene um token de API em uma variável. Se sua API upstream exigir credenciais, organize um mecanismo de credenciais do lado do servidor aprovado antes de conectá-lo.

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
3. Teste o aplicativo em [ChatGPT](/help/guides/test-in-chatgpt.md) ou [Claude](/help/guides/test-in-claude.md).
4. Verifique a seleção da ação com prompts que correspondam à ação e prompts não relacionados que não a chamem.
5. Após o Êxito do preparo, implante para produção.

Se os metadados existirem sem um manipulador correspondente, a implantação registrará a ação com um stub padrão. Adicione o manipulador antes de disponibilizar a ação aos usuários.
