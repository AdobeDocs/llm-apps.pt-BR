---
title: Referência
description: Referência de campo para configuração de ação na interface do usuário de aplicativos do Adobe LLM.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '489'
ht-degree: 6%

---


# Referência

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

Esta seção fornece referência em nível de campo para a configuração de ação na interface do usuário do [!DNL LLM Apps].

## Parâmetros de ação

Parâmetros de entrada são os valores que a plataforma LLM ([!DNL ChatGPT], Claude) envia para o manipulador de ação. O modelo os extrai da mensagem do usuário e os mapeia para esses campos automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Nome** | O identificador de parâmetro (por exemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` ou `Boolean` |
| **Descrição** | Uma explicação legível — a plataforma LLM usa isso para extrair o valor correto |
| **Obrigatório** | Se marcado, o modelo deve fornecer este parâmetro antes de chamar a ação |

### Parâmetros de arquivo

Os parâmetros de arquivo transportam objetos de arquivo com propriedades `download_url` e `file_id`. Defina nomes de campos de entrada que devem receber dados de arquivo quando um usuário carrega um arquivo na conversa.

## Campos de metadados

### Informações básicas

| Texto | Obrigatório | Descrição |
|-------|----------|-------------|
| **Nome da ação** | Sim | Identificador da ação (por exemplo, *Pesquisar Produtos*) |
| **Descrição** | Sim | Explicação do que a ação faz — a plataforma LLM usa isso para decidir quando chamá-la |

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

### Visibilidade

| Alternar | Descrição |
|--------|-------------|
| **Expor ao modelo de IA** | A ação pode ser invocada pelo modelo de IA durante conversas |
| **Mostrar como widget na superfície do aplicativo** | A ação renderiza um widget visual no aplicativo |

### Informações do widget

| Texto | Descrição |
|-------|-------------|
| **Tipo** | Tecnologia de widget — atualmente **[!UICONTROL EDS]** |
| **Domínio do widget (origem da sandbox)** | Origem onde o widget está hospedado; deve ser exclusivo por aplicativo |
| **Borda preferencial** | Se marcado, o widget é renderizado dentro de um cartão com bordas na plataforma LLM |

### URLs de modelo

| Texto | Descrição |
|-------|-------------|
| **[!UICONTROL URL do Script]** | Script de ponto de entrada — `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`. Compartilhado em todas as ações |
| **URL de inserção do widget** | Página EDS para esta ação — `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`. Exclusivo por ação |

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

