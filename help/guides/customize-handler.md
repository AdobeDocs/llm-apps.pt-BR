---
title: Personalizar um manipulador de ação gerado
description: Entenda o contrato do manipulador de aplicativos LLM do Adobe, substitua os dados de amostra gerados e mantenha a saída do manipulador alinhada com o widget.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 0%

---


# Personalizar um manipulador gerado {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

O Agente de integração cria um manipulador de trabalho para cada ação gerada. Inicialmente, o manipulador retorna dados de amostra para que você possa testar a experiência completa.

Use este guia para entender o contrato do manipulador e substituir os dados de amostra pelas APIs ou fontes de dados.

**Jornada:** Encontre o manipulador gerado → entenda suas entradas e resultado → conecte seu sistema → mantenha o contrato do widget alinhado → teste e implante.

## Localizar o manipulador gerado

Abra o repositório do manipulador selecionado durante a integração:

```text
actions/
└── <action-name>/
    └── index.js
```

Os testes correspondentes são armazenados separadamente:

```text
test/
└── actions/
    └── <action-name>.test.js
```

Editar o `index.js` gerado. Não altere arquivos de tempo de execução como `entry.js`.

## Contrato do manipulador

Cada manipulador exporta uma função assíncrona:

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

A função recebe um objeto `args` e retorna um objeto de resultado.

### Entrada: `args`

`args` contém os parâmetros definidos para a ação em [!DNL LLM Apps].

Para uma ação com `category` e `query` parâmetros:

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

O tempo de execução valida o esquema de entrada quando os metadados da ação incluem `inputSchema`, como ocorre após a implantação. A descoberta de manipulador local sem `actions.json` não aplica a validação de esquema. O manipulador deve sempre aplicar regras de negócios, como valores compatíveis, tamanhos máximos e combinações permitidas.

### Saída: `content`

Sempre retornar `content`. É uma variedade de partes de conteúdo lidas pela plataforma LLM e por hosts que não exibem widgets.

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

Mantenha essa resposta concisa. Não inclua credenciais, erros internos ou dados que o usuário não está autorizado a ver.

### Saída: `structuredContent`

Retorna `structuredContent` quando a ação tiver um widget. Deve ser um objeto simples, não uma matriz simples.

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent` é enviado ao widget, não ao LLM. Retorne somente os campos exigidos pela interface.

Para uma ação somente texto, `structuredContent` pode ser omitido.

## O contrato do manipulador-widget

O manipulador e o widget compartilham um contrato: a forma de `structuredContent`.

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

O widget lê o resultado do manipulador da ponte Aplicativos LLM do SDK:

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

Se o manipulador retornar:

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

o widget deve ler `structuredContent.products` e `structuredContent.total`.

A alteração do nome ou do tipo de um campo pode quebrar o widget. Atualize o manipulador, o widget e os testes juntos.

## Substituir dados de amostra

Os manipuladores gerados geralmente contêm uma matriz de amostra na memória. Substitua essa pesquisa de dados por uma chamada do lado do servidor para o sistema.

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

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
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

Mantenha o acesso protegido à rede no manipulador. Nunca coloque credenciais de API no JavaScript widget ou no controle de origem.

## Gerenciar estados esperados

Preservar uma forma de saída previsível para cada resultado.

### Resultados encontrados

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### Nenhum resultado

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

O widget agora pode renderizar um estado vazio sem adivinhar se `products` existe.

Para falhas de serviço, retorne ou acione um erro seguro sem expor rastreamentos de pilha, tokens, hosts internos ou corpos de resposta de upstream.

## Testar o contrato

Atualize os testes gerados sempre que o manipulador for alterado. Capa:

- Argumentos válidos e inválidos.
- Estados de resultados e sem resultados.
- Falhas de API e tempos limite.
- Respostas de API malformadas.
- `content` está sempre presente.
- `structuredContent` é um objeto simples.
- A forma esperada pelo widget.

Executar:

```bash
npm test
```

Para testes de MCP local, consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md).

## Implantar a alteração

1. Confirme e envie as alterações do manipulador por push.
2. Se a forma de dados mudou, atualize e envie por push o widget.
3. [Implante o aplicativo](/help/guides/deploy-your-app.md) para o Preparo.
4. [Testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
5. Após o Êxito do preparo, implante para produção.

Em seguida, consulte [Personalizar um widget gerado](/help/guides/widgets.md).
