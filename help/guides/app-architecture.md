---
title: Como um aplicativo é conectado em conjunto
description: Uma análise mais detalhada de como as partes que você possui — metadados de ação, código de manipulador e widgets — se unem em um aplicativo LLM em execução, no momento da criação e no tempo de execução.
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
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

Um **Aplicativo LLM** é um conjunto de **Ações** (cada uma exposta por uma ferramenta sobre o **Protocolo de Contexto de Modelo** ou **MCP**) que você publica em um único ponto de extremidade. Um host de chat como [!DNL ChatGPT] descobre essas ferramentas, chama-as de meio da conversa e renderiza um **widget interativo** com o resultado — bem dentro do bate-papo.

## Toda a fiação, construir → executar

**Diagrama 1 — tempo de compilação.** Você tem três superfícies separadas; a plataforma as funde em um aplicativo implantável.

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

- **Interface do Usuário de Aplicativos LLM** — onde você cria, edita e gerencia cada definição de Ação: seu **identificador de código** (uma descrição fixa que você define uma vez aqui, por exemplo, `my_action`, que vincula esta mesma Ação na interface, no manipulador e no widget), descrição, esquema de entrada, escolha do widget e sinalizadores de CSP/visibilidade. Sem código.
- **repositório de Manipulador de Ações** — o repositório do lado do servidor (com base em nosso modelo) onde você escreve a lógica de negócios. Cada função de manipulador retorna duas coisas: `content` (texto sem formatação lido pelo *LLM*) e `structuredContent` (o objeto de dados lido pelo *widget*).
- **Widget repo** — o repositório EDS em que cada widget reside como um bloco e é publicado em uma URL pública `*.aem.page`. Cada bloco usa [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk), a ponte entre o widget e o host/servidor. Ele implementa a **especificação de Aplicativos MCP** — o protocolo subjacente — por trás de uma API simples, e abstrai o próprio host LLM, de modo que o mesmo widget funciona sem modificação em [!DNL ChatGPT], [!DNL Claude], Gemini ou qualquer outro host MCP.

**Diagrama 2 — tempo de execução.** O que acontece em cada mensagem enviada pelo usuário depois que um servidor está ativo. Mostrado com [!DNL ChatGPT] como o host de exemplo — a mesma sequência é executada para qualquer host MCP, como [!DNL Claude].

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
