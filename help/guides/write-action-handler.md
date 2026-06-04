---
title: Gravar o manipulador de ação
description: Saiba como escrever um manipulador de ação para seu aplicativo Adobe LLM, incluindo o contrato do manipulador, structuredContent e um exemplo de trabalho.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '714'
ht-degree: 0%

---


# Gravar o manipulador de ação

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

Depois de criar uma ação na interface do usuário, os metadados são armazenados na API [!DNL LLM Apps], mas ainda não há código. Este guia mostra como escrever a função de manipulador que é executada quando uma plataforma do LLM (como [!DNL ChatGPT] ou Claude) invoca sua ação.

Para obter detalhes sobre o layout do projeto, desenvolvimento local e testes, consulte [Desenvolvimento](/help/reference/development.md).

## Contrato do desenvolvedor

Você escreve somente manipuladores. Todo o resto — nome da ação, descrição, esquema de entrada, anotações, visibilidade do widget, permissões, CSP — reside na interface do usuário do [!DNL LLM Apps] e é entregue ao tempo de execução automaticamente no momento da implantação. Nunca edita manualmente os metadados no repositório e nunca registra uma ferramenta no código.

| Preocupação | Onde ele mora |
|---------|----------------|
| Metadados (nome, descrição, esquema, configurações do widget) | Interface do usuário do [!DNL LLM Apps] — salva na API |
| Código do manipulador (a função que é executada) | Seu repositório [!DNL GitHub] — `actions/<name>/index.js` |
| `actions.json` (instantâneo de metadados) | Escrito pelo pipeline de implantação; baixado da interface para desenvolvimento local |

## Introdução

Seu repositório vinculado precisa da estrutura do projeto antes que você possa gravar manipuladores. Clonar a **[placa-padrão de aplicativos Adobe LLM](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)** para começar com um ponto de partida vazio.

Enviar o conteúdo para o repositório que você vinculou durante a criação do aplicativo (por exemplo, `your-org/your-repo`).

Quando o código estiver em vigor, execute:

```bash
npm install
```

Isso instala todas as dependências, incluindo [`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime) — o tempo de execução que lida com a comunicação do protocolo MCP, a descoberta de ações e o roteamento de solicitações. Você não interage diretamente com o tempo de execução; ele é consumido por `entry.js` no momento da compilação.

>[!TIP]
>
>Se você usar o [Código Claude](https://claude.ai/code) ou o [Cursor](https://cursor.com), o modelo inclui uma habilidade Claude pronta para uso em `.claude/skills/llm-apps-action-author/`. Ele pode criar andaimes de novas ações, gerar arquivos de teste, validar formas de manipulador e orientá-lo pelo contrato do manipulador — tudo a partir do seu editor. Para usá-lo, peça a Claude para *&quot;adicionar uma ação chamada search-products&quot;* e ela seguirá automaticamente as convenções de projeto corretas.

## Contrato do manipulador

Um manipulador é um único arquivo em `actions/<name>/index.js` que exporta uma função assíncrona:

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

A função recebe os argumentos de entrada da ação como um objeto simples — esses são os parâmetros definidos na caixa de diálogo Criar ação. O servidor os valida em relação ao esquema de entrada antes que o manipulador seja chamado.

### `content` (obrigatório)

Uma matriz de partes de conteúdo enviadas para os hosts LLM e somente texto. É o que a plataforma LLM lê para formular sua resposta.

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

Sempre retornar `content` — é o fallback universal para qualquer host.

### `structuredContent`

Um objeto do JavaScript simples enviado para o widget. Estes dados têm **custo zero de token** — são consumidos pelo bloco de widget EDS para renderizar uma interface do usuário avançada como um carrossel de produtos ou um mapa.

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

A estrutura depende de você — ela deve corresponder ao que o bloco de widget EDS espera por meio de `bridge.toolResult`.

>[!IMPORTANT]
>
>`structuredContent` deve ser um objeto simples, não uma matriz simples.

### `_meta` (opcional)

Metadados adicionais enviados junto com o resultado. A tecla `openai/widgetDescription` informa à plataforma LLM como apresentar o widget:

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## Exemplo: manipulador Pesquisar produtos

Este é um exemplo de manipulador `search-products`. Ele aceita um filtro `category` opcional e um `query` de texto livre, pesquisa um catálogo de produtos e retorna um resumo de texto para o LLM e dados estruturados para o carrossel de widgets.

>[!NOTE]
>
>Este exemplo usa uma matriz de produtos codificada para simplificar. Em um aplicativo real, você normalmente chamaria sua própria API de produto ou banco de dados para buscar resultados dinamicamente.

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**O que acontece em tempo de execução:**

1. Um usuário pergunta à plataforma LLM *&quot;Mostre-me seus produtos para café.&quot;*
2. A plataforma LLM corresponde à intenção de *Pesquisar Produtos* e extrai `category`.
3. O servidor MCP chama seu manipulador com `{ category: 'bagged-coffee' }`.
4. Seu manipulador filtra o catálogo e retorna `content` (resumo de texto do LLM) + `structuredContent` (matriz de produto do widget).
5. A plataforma LLM mostra a resposta de texto e transmite os dados estruturados para o dispositivo EDS, que renderiza um carrossel de produtos.

## E se o manipulador estiver ausente?

Se você definiu uma ação na interface do usuário, mas ainda não criou o arquivo do manipulador, a ação ainda será registrada no momento da implantação. As chamadas usarão um manipulador de stub padrão que retorna um conteúdo vazio até que você adicione o código real. Isso significa que é possível definir todas as ações na interface primeiro e implementá-las de forma incremental.

