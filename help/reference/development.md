---
title: Desenvolvimento e teste do manipulador local
description: Manipule a estrutura do projeto, os comandos do servidor local, o teste de MCP e o teste de unidade para aplicativos Adobe LLM.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
---

# Desenvolvimento e teste do manipulador local {#development}

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

Para a infraestrutura gerenciada da Adobe, solicite acesso ao repositório antes de seguir esta referência. A execução do manipulador local não requer credenciais do Adobe, mas os serviços upstream protegidos ainda exigem seu próprio acesso autorizado.

## Estrutura de projeto

O modelo do manipulador atual segue este layout:

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

- **`entry.js`** é o ponto de entrada do webpack. No momento da compilação, ele descobre cada arquivo `actions/*/index.js` e os agrupa em um único `dist/index.js`. Não modifique o ponto de entrada.
- **`actions.json`** está sendo ignorado. O pipeline de implantação o grava automaticamente dos metadados de ação em [!DNL LLM Apps].
- **Testes** ativos em `test/actions/`, **não** dentro de `actions/`. A build descobre pontos de entrada do manipulador e agrupa suas importações; mantenha os testes fora dessa árvore de descoberta.
- **Os caminhos de ação** usam o identificador de código ou sua forma hifenizada. Por exemplo, `search_products` resolve para `actions/search_products/index.js` ou, para código gerado, `actions/search-products/index.js`. O nome de exibição não é a chave de pesquisa.
- **Dados de suporte**, como `data.json` gerado, são agrupados quando o manipulador os importa.

## Desenvolvimento local

Para desenvolver e testar manipuladores localmente sem credenciais do Adobe, execute:

```bash
npm install
npm run dev:local
```

O comando de desenvolvimento compila o projeto com o webpack e inicia um servidor HTTP Node.js simples em `http://localhost:9080`. Interrompa e execute novamente `npm run dev:local` após alterações no manipulador ou nos metadados locais; o comando não observa arquivos.

Para usar uma porta diferente:

```bash
PORT=9081 npm run dev:local
```

### Comportamento de metadados locais

A interface atual não fornece um download de `actions.json`, mesmo que o banner de inicialização de preparação e tempo de execução mencione um. O servidor local também é iniciado sem um arquivo populado: a compilação cria `{"actions":[]}` quando o arquivo está ausente e o tempo de execução retorna para a descoberta do manipulador.

A descoberta é útil para a listagem de manipuladores, mas não é um modo completo de teste de argumento. Sem um esquema de entrada, o SDK do MCP chama o manipulador com contexto de tempo de execução como seu primeiro argumento em vez de usar a assinatura `(args, extra)` normal. O SDK não encaminha os argumentos de ação fornecidos.

Para chamadas MCP com argumentos, crie um **local-only** `actions.json` com metadados representativos que correspondam ao editor de ação. Para o manipulador `echo` padrão:

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

Quando a matriz `actions` não está vazia, o tempo de execução registra somente as ações nomeadas na matriz. Use cada identificador de código de ação como `name`. Mesmo uma ação sem parâmetros precisa de um esquema de objeto com `"properties": {}` para manter a assinatura de manipulador normal.

Os metadados locais não atualizam a configuração da interface ou da implantação. O tempo de execução valida tipos suportados, campos obrigatórios, enumerações e itens de matriz, mas não todas as restrições do Esquema JSON; valida comprimentos, intervalos, objetos aninhados e regras de negócios no manipulador. Os testes de integração padronizados usam `test/fixtures/actions.json` como metadados representativos.

### Variáveis do aplicativo local

As variáveis do aplicativo alcançam o manipulador através de `extra.variables`, não `process.env`. Leia com `getVariable(extra, name)` de `@adobe/llm-apps-runtime`. Para simular uma configuração não sensível após a criação:

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

Forneça o manifesto JSON e o valor nomeado. Inclua o manipulador no `actions.json` local com um esquema de entrada ao testar seu contrato `(args, extra)` normal. Os sinalizadores de linha de comando não carregam configurações do aplicativo da plataforma. Consulte [Configurar variáveis e segredos do aplicativo](/help/guides/app-variables.md) para configurações de implantação específicas do ambiente.

### Testar com curl

Após adicionar os metadados `echo` locais acima, compile e inicie o servidor e, em seguida, execute:

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
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

Os testes de unidade de manipulador ficam em `test/actions/` e chamam a função exportada diretamente, sem validação de esquema MCP:

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
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

Alguns testes gerados usam o executor de teste interno do Nó em vez do Jest. Verifique as importações de teste e use o executor correspondente, por exemplo `node --test test/actions/search-products.test.js` para um arquivo de importação `node:test`. Verifique as importações relativas geradas após colocar o arquivo em `test/actions/`.

Testes de unidade por si só não verificam metadados, validação de MCP, carregamento de widget ou comportamento do host. Teste o endpoint local com metadados representativos e, em seguida, teste o aplicativo implantado em uma plataforma LLM compatível.

Depois que os testes locais forem aprovados, envie as alterações por push e siga [Implantar alterações](/help/guides/deploy-your-app.md).
