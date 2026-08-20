---
title: Como um aplicativo é conectado em conjunto
description: Uma análise mais detalhada de como as partes que você possui — metadados de ação, código de manipulador e widgets — se unem em um aplicativo LLM em execução, no momento da criação e no tempo de execução.
source-git-commit: e066f66b37914e2f747176e865e26dcc074bff20
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# Como um aplicativo é conectado em conjunto {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

## Em uma frase

Um **Aplicativo LLM** é um conjunto de **Ações** (cada uma exposta por uma ferramenta sobre o **Modelo
Protocolo de Contexto&#x200B;**, ou &#x200B;** MCP**) que você publica em um único ponto de extremidade. Um host de chat
como [!DNL ChatGPT] descobre essas ferramentas, chama-as de meio de conversa e renderiza
um **widget interativo** com o resultado — bem dentro do chat.

## Toda a fiação, construir → executar

**Diagrama 1 — tempo de compilação.** Você tem três superfícies separadas; a plataforma se funde
em um aplicativo implantável.

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **Interface do Usuário de Aplicativos LLM** — onde você cria, edita e gerencia cada definição de Ação:
seu **identificador de código** (um slug fixo que você definiu aqui uma vez, ex.: `my_action`, que
une essa mesma ação na interface do usuário, no manipulador e no widget),
sinalizadores de descrição, esquema de entrada, escolha do widget e CSP/visibilidade. Sem código.
- **repositório de Manipulador de Ações** — o repositório do lado do servidor (scaffolded da nossa placa-mãe)
onde você escreve a lógica de negócios. Cada função de manipulador retorna duas coisas:
  `content` (texto simples lido pelo *LLM*) e `structuredContent` (o objeto de dados
  o *widget* (lê).
- **Widget repo** — o repositório EDS em que cada widget vive como um bloco e é recebido
publicado em uma URL pública `*.aem.page`. Cada bloco usa
  [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk), o
ponte entre o widget e o host/servidor. Ele implementa os **Aplicativos MCP
especificação** — o protocolo subjacente — por trás de uma API simples, e
abstrai o próprio host do LLM, de modo que o mesmo widget funciona inalterado no
  [!DNL ChatGPT], [!DNL Claude], Gemini ou qualquer outro host MCP.

**Diagrama 2 — tempo de execução.** O que acontece em cada mensagem enviada pelo usuário, uma vez
que um servidor está ativo. Mostrado com [!DNL ChatGPT] como o host de exemplo — o
a mesma sequência é executada para qualquer host MCP, como [!DNL Claude].

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
