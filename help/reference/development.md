---
title: Desenvolvimento para aplicativos Adobe LLM
description: Estrutura de projeto, fluxo de trabalho de desenvolvimento local e configuração de teste para o código do manipulador de aplicativos Adobe LLM.
source-git-commit: 51ffb31eec82f9639bd7ade9052d61028c262d0e
workflow-type: tm+mt
source-wordcount: '318'
ht-degree: 4%

---


# Desenvolvimento {#development}

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

Esta seção aborda a estrutura do projeto do manipulador, o fluxo de trabalho de desenvolvimento local e a configuração de teste. Para obter o contrato do manipulador e o código de exemplo, consulte [Gravar o Manipulador de Ação](/help/guides/write-action-handler.md).

## Estrutura de projeto

O repositório vinculado segue este layout:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Pontos principais:

- **`entry.js`** é o ponto de entrada do webpack. No momento da compilação, ele descobre cada arquivo `actions/*/index.js` e os agrupa em um único `dist/index.js`. Não modifique.
- **`actions.json`** está sendo ignorado. Baixe-o da página Ações na interface do usuário para desenvolvimento local. Para implantações, o pipeline o grava automaticamente da API.
- **Testes** ativos em `test/actions/`, **não** dentro de `actions/`. O Webpack agrupa tudo em `actions/` no artefato implantado — os testes de co-localização os enviariam para [!DNL Adobe I/O Runtime].

## Desenvolvimento local

Você pode desenvolver e testar manipuladores localmente sem credenciais do Adobe:

```bash
npm install
npm run dev:local
```

Isso cria o projeto com o webpack e inicia um servidor HTTP Node.js simples em `http://localhost:9080`. O servidor detecta automaticamente os arquivos do manipulador em `actions/` e os registra como ferramentas MCP.

### Baixar `actions.json`

Para que o servidor local saiba mais sobre os metadados de ação (nome, descrição, esquema de entrada), baixe `actions.json` da página Ações na interface do usuário do [!DNL LLM Apps] e coloque-o na raiz do repositório. Sem ele, o servidor descobre os manipuladores, mas os registra com o mínimo de metadados.

Você também pode copiar `actions.example.json` para `actions.json` como ponto de partida.

### Testar com curl

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### Testar com Inspetor MCP

```bash
npx @modelcontextprotocol/inspector
```

Definir **Tipo de Transporte** para `streamable-http` e **URL** para `http://localhost:9080`.

## Testes

Os testes de unidade de manipulador ficam em `test/actions/` e espelham o layout `actions/`:

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

Executar testes com:

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## Implantação

Você não cria nem implanta manualmente. Para obter uma apresentação completa do pipeline de implantação, consulte [Implantar seu aplicativo](/help/guides/deploy-your-app.md).

Seu fluxo de trabalho diário é:

| Etapa | Ação |
|------|--------|
| &#x200B;1. Manipulador de gravação ou edição | `actions/<name>/index.js` |
| &#x200B;2. Baixar metadados | Página Ações → **Baixar actions.json** |
| &#x200B;3. Testar localmente | `npm run dev:local` |
| &#x200B;4. Código push | `git push` |
| &#x200B;5. Implantar | Página de detalhes do aplicativo → **[!UICONTROL Implantar]** |

