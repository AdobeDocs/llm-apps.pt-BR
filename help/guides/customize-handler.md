---
title: Personalizar um manipulador de ação gerado
description: Entenda o contrato do manipulador de aplicativos LLM do Adobe, substitua os dados de amostra gerados e mantenha a saída do manipulador alinhada com o widget.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# Personalizar um manipulador gerado {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

A plataforma cria um manipulador de trabalho para cada ação gerada. Inicialmente, o manipulador retorna dados de amostra para testar a experiência completa.

**Neste guia:** Saiba como localizar o manipulador gerado, substituir seus dados de exemplo por chamadas para seus sistemas e manter sua resposta compatível com o widget. Em seguida, teste e implante suas alterações.

## Localizar o manipulador gerado

Abra o repositório do manipulador conectado durante a [configuração de infraestrutura](/help/guides/create-app.md). Se você usa a infraestrutura gerenciada da Adobe, solicite acesso ao código antes de continuar.

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

Os testes correspondentes ficam em um diretório separado:

```text
test/
└── actions/
    └── <action-slug>.test.js
```

Use a solicitação de pull gerada para localizar os arquivos exatos. A pasta slug usa hifens: a plataforma gera uma ação com o identificador de código `search_products` em `actions/search-products/`. O tempo de execução reconhece o formulário hifenizado, bem como uma pasta que corresponde exatamente ao identificador.

Edite o `index.js` gerado e quaisquer dados de amostra associados. Não altere arquivos de tempo de execução como `entry.js`.

## Contrato do manipulador

Cada manipulador exporta uma função assíncrona:

```javascript
module.exports = async (args, extra) => {
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

Para uma ação com um esquema de entrada, a função recebe um objeto `args` e um contexto de tempo de execução opcional, `extra`, e retorna um objeto de resultado. Use `extra` para auxiliares de tempo de execução como `getVariable(extra, 'PRODUCT_API_ORIGIN')`; as variáveis de aplicativo não são expostas automaticamente por meio de `process.env`. Consulte [Configurar variáveis e segredos do aplicativo](/help/guides/app-variables.md).

### Entrada: `args`

`args` contém os parâmetros definidos para a ação em [!DNL LLM Apps].

Para uma ação com `category` e `query` parâmetros:

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

Com os metadados de ação, o tempo de execução converte o esquema de entrada declarado em validação para regras compatíveis, incluindo campos obrigatórios, tipos primitivos, enumerações e itens de matriz. O tempo de execução não executa a validação completa do Esquema JSON. Verifique restrições, como comprimentos de string, intervalos numéricos e propriedades de objetos aninhados no manipulador.

A descoberta local sem metadados de esquema não é equivalente à implantação: o tempo de execução não passa argumentos de ação no formulário `(args, extra)` normal. Use testes de manipulador direto ou metadados `actions.json` locais representativos para testar argumentos. Consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md).

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

Mantenha a resposta do texto concisa. Não inclua credenciais, erros internos ou dados que o usuário não está autorizado a ver.

### Saída: `structuredContent`

Retorna `structuredContent` quando a ação tiver um widget. Deve ser um objeto simples, não uma matriz simples.

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent` faz parte do resultado da ação de MCP entregue ao host. Não suponha que ele esteja oculto do modelo ou do usuário. Retorne somente os dados autorizados exigidos pela experiência, nunca as credenciais ou a configuração do servidor privado.

Para uma ação somente texto, retornar `structuredContent` é opcional.

## O contrato do manipulador-widget

O manipulador e o widget compartilham um contrato: a forma de `structuredContent`.

| Saída do manipulador | Propósito |
|---|---|
| `content` | Resposta legível por modelo e fallback somente de texto |
| `structuredContent` | Dados entregues ao host e lidos pelo widget através de `bridge.toolResult` |

O widget lê o resultado do manipulador da ponte Aplicativos LLM do SDK:

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

Os manipuladores atuais gerados usam um objeto contendo `arguments` e `records`. Os aplicativos existentes podem usar uma forma diferente, portanto, verifique os arquivos gerados antes de editar. A alteração do nome ou do tipo de um campo pode quebrar o widget. Atualize o manipulador, o widget e os testes juntos.

## Substituir dados de amostra

Os manipuladores gerados carregam registros representativos de `data.json`. Substitua a pesquisa de dados de amostra por uma chamada do lado do servidor para o sistema. O exemplo a seguir usa uma API pública de catálogo HTTPS e lê sua origem não confidencial de uma variável de aplicativo. Adapte a validação de resposta à sua API.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

Mantenha o acesso protegido à rede no manipulador. Nunca coloque credenciais de API no JavaScript widget ou no controle de origem. Para solicitações feitas em nome de um usuário conectado, consulte [Autenticar usuários finais](/help/guides/authentication.md).

Para configurações não confidenciais de que seu manipulador precisa, como uma URL de serviço, consulte [Configurar variáveis e segredos do aplicativo](/help/guides/app-variables.md). Configure variáveis separadamente para cada ambiente. As alterações de variáveis entram em vigor na próxima implantação. A interface atual oferece suporte somente à adição de variáveis; não use variáveis para armazenar credenciais de API.

## Gerenciar estados esperados

Preservar uma forma de saída previsível para cada resultado.

### Resultados encontrados

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### Nenhum resultado

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

Uma forma de resultado previsível permite que o widget renderize um estado vazio sem adivinhar se `records` existe.

Para falhas de serviço, retorne um resultado de erro seguro com `isError: true`, como no exemplo. Verifique `result.isError` no widget para que uma solicitação com falha não seja apresentada como uma pesquisa vazia bem-sucedida. Não exponha rastreamentos de pilha, credenciais, hosts internos ou corpos de resposta upstream.

## Testar o contrato

Atualize os testes gerados sempre que o manipulador for alterado. Capa:

- Argumentos válidos e inválidos.
- Estados de resultados e sem resultados.
- Falhas de API e tempos limite.
- Respostas de API malformadas.
- Presença de `content` em cada resultado.
- Um objeto simples para `structuredContent`.
- A forma esperada pelo widget.

Execute o conjunto de testes configurado do repositório:

```bash
npm test
```

Se um teste gerado importar `node:test` em vez de Jest, execute-o com `node --test test/actions/<action-slug>.test.js` e verifique se seu caminho de importação resolve de `test/actions/`. Use o executor de teste apropriado para o arquivo; o comando `npm test` da placa-mãe executa Jest.

Para testes de MCP local, consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md).

## Implantar a alteração

1. Confirme e envie as alterações do manipulador por push.
2. Se você alterar a forma dos dados, atualize e envie o widget por push.
3. [Implante o aplicativo](/help/guides/deploy-your-app.md) para o Preparo.
4. Testar em uma plataforma LLM compatível. Consulte [Testar no ChatGPT](/help/guides/test-in-chatgpt.md) ou [Testar no Claude](/help/guides/test-in-claude.md).
5. Depois que os testes forem aprovados no Preparo, implante para produção.

Em seguida, consulte [Personalizar um widget gerado](/help/guides/widgets.md).
