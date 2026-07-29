---
title: Desenvolvimento e teste do manipulador local
description: Manipule a estrutura do projeto, os comandos do servidor local, o teste de MCP e o teste de unidade para aplicativos Adobe LLM.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

---


# Desenvolvimento e teste de manipulador local {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use essa referência ao desenvolver manipuladores localmente. Para obter o contrato de resultado do manipulador, consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md).

## Requisitos

- Node.js 24 ou posterior.
- npm.
- Um clone local do repositório do manipulador vinculado.

## Estrutura de projeto

O repositório vinculado segue este layout:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Pontos principais:

- **`entry.js`** é o ponto de entrada do webpack. No momento da compilação, ele descobre cada arquivo `actions/*/index.js` e os agrupa em um único `dist/index.js`. Não modifique.
- **`actions.json`** está sendo ignorado. O pipeline de implantação o grava automaticamente dos metadados de ação em [!DNL LLM Apps].
- **Testes** ativos em `test/actions/`, **não** dentro de `actions/`. O Webpack agrupa tudo em `actions/` no artefato implantado — os testes de co-localização os enviariam para [!DNL Adobe I/O Runtime].

## Desenvolvimento local

Você pode desenvolver e testar manipuladores localmente sem credenciais do Adobe:

```bash
npm install
npm run dev:local
```

Isso cria o projeto com o webpack e inicia um servidor HTTP Node.js simples em `http://localhost:9080`. O servidor detecta automaticamente os arquivos do manipulador em `actions/` e os registra como ferramentas MCP.

### Comportamento de metadados locais

A interface atual não fornece um download de `actions.json`. Você pode executar o servidor local sem este arquivo; ele descobre manipuladores em `actions/` e os registra com metadados mínimos.

Sem `actions.json`, os argumentos de ação local não são validados em relação ao esquema de entrada da interface do usuário. Os testes de unidade e integração usam `test/fixtures/actions.json` para metadados representativos.

### Testar com curl

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### Testar com Inspetor MCP

```bash
npx @modelcontextprotocol/inspector
```

Definir **Tipo de Transporte** para `streamable-http` e **URL** para `http://localhost:9080`.

## Testes

Os testes de unidade de manipulador ficam em `test/actions/` e espelham o layout `actions/`:

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

Executar testes com:

```bash
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

Depois que os testes locais forem aprovados, envie as alterações por push e siga [Implantar alterações](/help/guides/deploy-your-app.md).

