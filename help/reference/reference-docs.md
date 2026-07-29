---
title: Campos de ação e widget
description: Definições de campo para metadados de ação, parâmetros, widgets, CSP e permissões em aplicativos Adobe LLM.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '606'
ht-degree: 5%

---


# Campos de ação e widget {#action-widget-configuration}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use esta página para pesquisar campos no editor de ações. Para obter a jornada de criação completa, consulte [Criar uma ação do zero](/help/guides/create-action.md).

## Parâmetros de ação

Parâmetros de entrada são os valores que a plataforma LLM envia para o manipulador de ação. O modelo os extrai da mensagem do usuário e os mapeia para esses campos.

| Propriedade | Descrição |
|----------|-------------|
| **Nome** | O identificador de parâmetro (por exemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` ou `Boolean` |
| **Descrição** | Uma explicação legível — a plataforma LLM usa isso para extrair o valor correto |
| **Obrigatório** | Se marcado, o modelo deve fornecer este parâmetro antes de chamar a ação |

### Parâmetros de arquivo

Parâmetros de arquivo são nomes de campos de entrada configurados no editor de ação. Quando um usuário carrega um arquivo, o host fornece um objeto de arquivo para esses argumentos, normalmente incluindo `download_url` e `file_id`.

## Campos de metadados

### Informações básicas

| Texto | Obrigatório | Descrição |
|-------|----------|-------------|
| **Nome da ação** | Sim | Nome para exibição da ação (por exemplo, *Pesquisar Produtos*) |
| **Descrição** | Sim | Explicação do que a ação faz — a plataforma LLM usa isso para decidir quando chamá-la |

Após a criação, o editor também mostra um **Identificador de código** imutável. Ele mapeia a ação para `actions/<code-identifier>/index.js` no repositório do manipulador.

### Anotações

Dicas opcionais que descrevem o comportamento da ação:

| Anotação | Descrição |
|------------|-------------|
| **Dica destrutiva** | A ação modifica ou exclui dados |
| **Idempotente** | Chamar a ação várias vezes com os mesmos argumentos tem o mesmo resultado |
| **Abrir dica do mundo** | A ação interage com sistemas externos |
| **Dica somente leitura** | A ação lê apenas dados, nunca grava |

### Metadados do OpenAI

| Texto | Comprimento máx. | Descrição |
|-------|------------|-------------|
| **Chamando texto de status** | 64 caracteres | Mensagem mostrada na plataforma LLM enquanto a ação é executada (por exemplo, *Carregando produtos...* ) |
| **Texto de status chamado** | 64 caracteres | Mensagem mostrada após a ação ser concluída (por exemplo, *Produtos carregados...* ) |
| **Descrição do widget** | 512 caracteres | Mapeia para `_meta["openai/widgetDescription"]`; resume o componente renderizado para o modelo e reduz a narração repetida |

A descrição da ação controla quando o modelo seleciona a ação. A descrição do widget explica o que o componente mostra após ser renderizado.

### Visibilidade

| Alternar | Descrição |
|--------|-------------|
| **Expor ao modelo de IA** | A ação pode ser invocada pelo modelo de IA durante conversas |
| **Mostrar como widget na superfície do aplicativo** | A ação renderiza um widget visual no aplicativo |

### Analytics

| Texto | Descrição |
|-------|-------------|
| **Coletar intenção de usuário** | Coleta um resumo da conversa que levou à ação para análise |

## Campos de widget

### Informações do widget

| Texto | Descrição |
|-------|-------------|
| **Tipo** | Tecnologia de widget — atualmente **[!UICONTROL EDS]** |
| **Domínio do widget (origem da sandbox)** | Origem onde o widget está hospedado; deve ser exclusivo por aplicativo |
| **Borda preferencial** | Se marcado, o widget é renderizado dentro de um cartão com bordas na plataforma LLM |

### URLs de modelo

| Texto | Descrição |
|-------|-------------|
| **[!UICONTROL URL do Script]** | URL HTTPS para o ponto de entrada EDS `scripts/aem-embed.js`. Compartilhado entre ações no mesmo projeto EDS |
| **URL do widget** | URL HTTPS da página EDS renderizada por esta ação. As ações geradas configuram isso automaticamente |

## Configuração da CSP

A Política de segurança de conteúdo controla com quais domínios externos o iframe do widget pode entrar em contato. Cada domínio externo deve ser explicitamente classificado.

| Diretiva | Descrição |
|-----------|-------------|
| **Domínios de recursos** | Domínios para ativos estáticos — imagens, fontes, scripts, estilos |
| **Conectar domínios** | Domínios que o widget pode contatar via `fetch`, `XHR` ou `WebSocket` |
| **Enquadrar domínios** | Origens permitidas para iframes aninhados; aciona revisão mais rígida do aplicativo |
| **Redirecionar domínios** | Destinos confiáveis para `openExternal` links de redirecionamento ([!DNL ChatGPT]-específico) |
| **Domínios URI de base** | A diretiva CSP `base-uri` (somente MCP Apps SDK, não [!DNL ChatGPT]) |

## Permissões

As APIs de hardware e navegador que o dispositivo tem permissão para acessar. Eles são mapeados para a política de permissão iframe.

| Permissão | Descrição |
|------------|-------------|
| **Câmera** | Acessar a câmera do dispositivo |
| **Microfone** | Acessar o microfone do dispositivo |
| **Geolocalização** | Acessar a localização do usuário |
| **Área de transferência** | Ler ou gravar na área de transferência |

