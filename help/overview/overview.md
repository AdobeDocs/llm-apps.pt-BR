---
title: Uma visão geral dos aplicativos Adobe LLM
description: Saiba o que são os aplicativos Adobe LM, como funcionam e como começar a criar seu aplicativo.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '624'
ht-degree: 0%
---

# Aplicativos Adobe LLM - Uma visão geral {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

## O que é [!DNL Adobe LLM Apps]?

O [!DNL Adobe LLM Apps] permite que sua marca ofereça ações úteis — como descoberta de produtos, verificações de disponibilidade ou reservas de serviços — dentro de assistentes de IA com suporte, como o [!DNL ChatGPT] e o [!DNL Claude].

[!DNL LLM Apps] está disponível em [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).

## O que você pode fazer com [!DNL LLM Apps]

- **Criar ações LLM de propriedade da marca** — Defina os fluxos comerciais específicos que você deseja ativar dentro dos assistentes de IA (por exemplo, *Agendar um Teste*, *Comparar Produtos*, *Reservar um Serviço*).
- **Criar widgets LLM interativos** — Crie componentes visuais de interface do usuário (cartões de produto, formulários de reserva, localizadores de lojas) gerenciados como componentes do AEM em seu repositório [!DNL GitHub].
- **Manter o controle centralizado de marcas** — Os autores e desenvolvedores mantêm controle total sobre todo o conteúdo, cópia e visuais expostos na plataforma LLM, com aprovações gerenciadas pelo AEM.
- **Implantar para preparo e produção** — um pipeline de implantação controlada permite que você teste a experiência em um ambiente de preparo antes de promover para produção.
- **Controlar visibilidade no nível de ação** — Habilite ou desabilite ações individuais e implante o aplicativo para aplicar a alteração.
- **Meça o que impulsiona as decisões** — contagens de acionadores de ação de superfície de análise interna (viabilizada pelo Adobe Customer Journey Analytics), taxas de sucesso, taxas de abandono, principais prompts de usuário e pontuações de visibilidade.

## Por que [!DNL LLM Apps] é importante

As interações LLM são fundamentalmente diferentes da pesquisa tradicional. A duração média das sessões LLM é quatro vezes maior do que a de uma sessão de pesquisa tradicional. Mais de 40% dos consumidores dependem de ferramentas de IA para decisões de compra complexas. Sem o [!DNL LLM Apps], você poderá ganhar a menção, mas perderá o cliente. O [!DNL LLM Apps] garante que sua marca não só fique visível, como também seja acionável no exato momento em que um usuário estiver pronto para decidir.

## Principais conceitos {#key-concepts}

### Aplicativo LLM

Seu assistente de marca com o qual os usuários interagem dentro do [!DNL ChatGPT] ou de outras plataformas do LLM. Ele agrupa todas as suas ações e faz a implantação como uma única unidade.

### Ação {#actions}

Um recurso que seu aplicativo oferece, como *Localizar um distribuidor* ou *Procurar produtos*. A plataforma LLM invoca uma ação quando uma solicitação corresponde à sua descrição. [!DNL LLM Apps] gerencia metadados de ação, e o repositório [!DNL GitHub] armazena o código do manipulador.

### Manipulador de ação

A função do lado do servidor executada quando a plataforma LLM chama uma ação. Use o manipulador para validar a entrada, chamar as APIs e retornar texto e dados estruturados.

### Widget {#widgets-eds}

A resposta visual mostrada com a resposta do LLM, como um cartão, carrossel ou tabela. Os widgets gerados são blocos em um repositório [!DNL Edge Delivery Services] (EDS). Com a infraestrutura gerenciada da Adobe, a Adobe prepara e hospeda o repositório para você. Como alternativa, conecte seu próprio repositório.

### Servidor MCP

O endpoint exposto após a implantação. Uma plataforma LLM compatível se conecta a esse endpoint para descobrir e invocar suas ações.

## Como funciona

Você define os recursos da sua marca no [!DNL LLM Apps]. A plataforma disponibiliza esses recursos como ações, e um assistente de IA usa as ações para responder às solicitações do cliente na conversa.

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

Para obter detalhes técnicos sobre o que criar e como os componentes funcionam juntos, consulte [Como conectar um aplicativo](/help/guides/app-architecture.md).

## Introdução {#choose-your-journey}

A criação de um aplicativo e de suas ações são etapas separadas:

1. [Configurar a infraestrutura](/help/guides/create-app.md). Use a infraestrutura gerenciada da Adobe para uma configuração rápida ou use seus próprios repositórios do [!DNL GitHub].
2. [Crie suas ações](/help/guides/creating-actions.md). Peça à plataforma que analise seu site e gere ações ou crie você mesmo.
3. [Implante e teste o aplicativo](/help/guides/deploy-your-app.md). Revise o trabalho gerado, implante no ambiente de preparo e teste em uma plataforma de LLM compatível.

A geração automática oferece um ponto de partida com comportamento de amostra. Antes de disponibilizar o aplicativo para os clientes, [conecte os manipuladores aos seus sistemas](/help/guides/customize-handler.md) e [personalize os widgets](/help/guides/widgets.md).

Para criar relatórios sobre seu aplicativo em seu próprio [!DNL Adobe Analytics] ou [!DNL Customer Journey Analytics], consulte [Configurar o Adobe Analytics e o Customer Journey Analytics](/help/guides/analytics-setup.md).
