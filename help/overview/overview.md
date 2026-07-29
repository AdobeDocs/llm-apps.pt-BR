---
title: Uma visão geral dos aplicativos Adobe LLM
description: Saiba o que são os aplicativos LLM do Adobe, como funcionam e o que é necessário para começar.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '970'
ht-degree: 1%

---


# Aplicativos Adobe LLM - Uma visão geral {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

## O que é [!DNL Adobe LLM Apps]?

O [!DNL Adobe LLM Apps] permite que sua marca ofereça ações úteis — como descoberta de produtos, verificações de disponibilidade ou reservas de serviços — dentro de assistentes de IA, como o [!DNL ChatGPT].

[!DNL LLM Apps] está disponível em [experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/).

## O que você pode fazer com [!DNL LLM Apps]

- **Criar ações LLM de propriedade da marca** — Defina os fluxos comerciais específicos que você deseja ativar dentro dos assistentes de IA (por exemplo, *Agendar um Teste*, *Comparar Produtos*, *Reservar um Serviço*).
- **Criar widgets LLM interativos** — Crie componentes visuais de interface do usuário (cartões de produto, formulários de reserva, localizadores de lojas) gerenciados como componentes do AEM em seu repositório [!DNL GitHub].
- **Manter o controle centralizado de marcas** — Os autores e desenvolvedores mantêm controle total sobre todo o conteúdo, cópia e visuais expostos na plataforma LLM, com aprovações gerenciadas pelo AEM.
- **Implantar para preparo e produção** — um pipeline de implantação controlada permite que você teste a experiência em um ambiente de preparo antes de promover para produção.
- **Controlar visibilidade no nível de ação** — Após a implantação, ações individuais podem ser ativadas ou desativadas sem reimplantar todo o aplicativo.
- **Meça o que impulsiona as decisões** — contagens de acionadores de ação de superfície de análise interna (viabilizada pelo Adobe Customer Journey Analytics), taxas de sucesso, taxas de abandono, principais prompts de usuário e pontuações de visibilidade.

## Por que [!DNL LLM Apps] é importante

As interações LLM são fundamentalmente diferentes da pesquisa tradicional. A duração média das sessões LLM é quatro vezes maior do que a de uma sessão de pesquisa tradicional. Mais de 40% dos consumidores dependem de ferramentas de IA para decisões de compra complexas. Sem o [!DNL LLM Apps], você poderá ganhar a menção, mas perderá o cliente. O [!DNL LLM Apps] garante que sua marca não só fique visível, como também seja acionável no exato momento em que um usuário estiver pronto para decidir.

## Principais conceitos {#key-concepts}

### Aplicativo LLM

Seu assistente de marca com o qual os usuários interagem dentro do [!DNL ChatGPT] ou de outras plataformas do LLM. Ele agrupa todas as suas ações e faz a implantação como uma única unidade.

### Ação {#actions}

Um recurso que seu aplicativo oferece, como *Localizar um distribuidor* ou *Procurar produtos*. A plataforma LLM invoca uma ação quando uma solicitação corresponde à sua descrição. Os metadados de ação são gerenciados em [!DNL LLM Apps], enquanto seu manipulador é o código em seu repositório [!DNL GitHub].

### Manipulador de ação

A função do lado do servidor executada quando uma ação é chamada. Ele pode validar a entrada, chamar suas APIs e retornar texto e dados estruturados.

### Widget {#widgets-eds}

A resposta visual mostrada com a resposta do LLM, como um cartão, carrossel ou tabela. Os widgets gerados são blocos em um repositório do [!DNL Edge Delivery Services] (EDS) que você possui.

### Servidor MCP

O endpoint exposto após a implantação. Uma plataforma LLM compatível se conecta a esse endpoint para descobrir e invocar suas ações.

## Como funciona

O diagrama abaixo mostra como as partes se encaixam, desde a definição de um aplicativo na interface até a visualização dos resultados ao vivo na plataforma LLM.

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Requisitos {#requirements}

Conclua todos os requisitos a seguir antes de criar um aplicativo.

### Console do desenvolvedor da Adobe

Sua organização do Adobe IMS deve ter acesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/). Você precisa da função **Desenvolvedor** ou **Administrador do Sistema**.

Para verificar seu acesso, abra o [Adobe Developer Console](https://developer.adobe.com/console). A tela Início rápido confirma que você tem o acesso necessário.

![Adobe Developer Console — Tela de Início Rápido confirmando o acesso do desenvolvedor](/help/assets/overview/dev-console-access-granted.png)

Se você vir **Acesso restrito**, entre em contato com o administrador da organização IMS e solicite a função de Desenvolvedor.

![Adobe Developer Console — Mensagem de acesso restrito](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

Você precisa de uma conta do [!DNL GitHub] que possa:

- Crie dois repositórios na conta ou organização que será proprietária do aplicativo.
- Instale ou solicite a instalação do Aplicativo [!DNL GitHub] do Adobe LLM Apps.
- Instale ou solicite a instalação da Sincronização de código AEM para o repositório EDS.

Para verificar o acesso de criação de repositório, abra [github.com/new](https://github.com/new) e confirme se a conta ou organização pretendida aparece em **Proprietário**.

![GitHub — selecione um proprietário de repositório](/help/assets/overview/github-repo-owner-dropdown.png)

Para repositórios de propriedade da organização, um administrador da organização pode precisar aprovar os aplicativos [!DNL GitHub]. Conceda acesso a cada aplicativo somente aos repositórios usados pelo aplicativo LLM.

### AEM Sites com Edge Delivery Services

Sua organização precisa de uma licença do Adobe Experience Manager Sites que inclua o Edge Delivery Services (EDS). Você também precisará de acesso de administrador ao site do EDS criado a partir do repositório do widget.

Para verificar o acesso, abra a [Ferramenta de administração de usuário do EDS](https://tools.aem.live/tools/user-admin/index.html), digite o nome da organização e busque os usuários. Confirme se sua conta tem o selo **admin**.

### Site

Você precisa de um site HTTPS público que represente os produtos, serviços ou tarefas que o aplicativo deve suportar. A plataforma analisa esse site para propor ações e criar dados de amostra representativos.

Não use um site que exponha informações confidenciais ou de acesso controlado.

### [!DNL ChatGPT] ou [!DNL Claude] para teste

Para concluir o tutorial de introdução, use um plano [!DNL ChatGPT] com suporte e o modo de desenvolvedor habilitado, ou um plano [!DNL Claude] com suporte e conectores personalizados habilitados. Os administradores da Workspace ou da organização podem restringir o acesso. Consulte [Testar no ChatGPT](/help/guides/test-in-chatgpt.md#plan-requirements) ou [Testar no Claude](/help/guides/test-in-claude.md#plan-requirements).

## Escolha sua jornada {#choose-your-journey}

### &#x200B;1. Crie e inicie seu primeiro aplicativo

Comece com [Crie e inicie seu primeiro aplicativo](/help/guides/create-app.md). Esta jornada começa com dois repositórios vazios e termina com um aplicativo pronto para produção testado como plug-in em uma plataforma LLM compatível, como o [!DNL ChatGPT].

### &#x200B;2. Personalizar o aplicativo gerado

Escolha esta jornada quando a plataforma tiver criado o aplicativo automaticamente e você quiser substituir o comportamento de amostra:

1. [Personalize os manipuladores gerados](/help/guides/customize-handler.md) para conectar suas APIs e definir os dados retornados por cada ação.
2. [Personalize os widgets gerados](/help/guides/widgets.md) para usar esses dados e aplicar suas interações e design.

### &#x200B;3. Adicionar uma nova ação do zero

Escolha [Adicionar uma nova ação do zero](/help/guides/create-action.md) para definir novos metadados, gravar o manipulador, conectar um widget, testar e implantar a ação.

### &#x200B;4. Conectar um projeto EDS existente

Escolha [Conectar um projeto EDS existente](/help/guides/bring-your-own-eds.md) quando já tiver um site EDS ou não tiver compilado o aplicativo automaticamente.

Cada jornada usa a etapa [implantação](/help/guides/deploy-your-app.md) compartilhada e, em seguida, o [teste de plug-in ChatGPT](/help/guides/test-in-chatgpt.md) ou o [teste de conector Claude](/help/guides/test-in-claude.md) compartilhado.

