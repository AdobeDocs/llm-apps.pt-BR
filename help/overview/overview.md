---
title: Visão geral
description: Saiba o que são os aplicativos LLM do Adobe, como funcionam e o que é necessário para começar.
source-git-commit: f144ccfc0ede6c556ccf4d99173f91d372add6f7
workflow-type: tm+mt
source-wordcount: '863'
ht-degree: 1%

---


>[!NOTE]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

## O que é [!DNL Adobe LLM Apps]?

O [!DNL Adobe LLM Apps] permite que sua marca exponha ações importantes — como descoberta de produtos, verificações de disponibilidade ou reservas de serviços — diretamente dentro dos assistentes de IA, como o [!DNL ChatGPT] ou o Claude. Em vez de ser mencionado passivamente em respostas geradas por IA, sua marca pode orientar os clientes por fluxos comerciais reais sem que eles saiam da conversa.

[!DNL LLM Apps] está disponível em [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).

## O que você pode fazer com [!DNL LLM Apps]

- **Criar ações LLM de propriedade da marca** — Defina os fluxos comerciais específicos que você deseja ativar dentro dos assistentes de IA (por exemplo, *Agendar um Teste*, *Comparar Produtos*, *Reservar um Serviço*).
- **Criar widgets LLM interativos** — Crie componentes visuais de interface do usuário (cartões de produto, formulários de reserva, localizadores de lojas) gerenciados como componentes do AEM em seu repositório [!DNL GitHub].
- **Manter o controle centralizado de marcas** — Os autores e desenvolvedores mantêm controle total sobre todo o conteúdo, cópia e visuais expostos na plataforma LLM, com aprovações gerenciadas pelo AEM.
- **Implantar para preparo e produção** — um pipeline de implantação controlada permite que você teste a experiência em um ambiente de preparo antes de promover para produção.
- **Controlar visibilidade no nível de ação** — Após a implantação, ações individuais podem ser ativadas ou desativadas sem reimplantar todo o aplicativo.
- **Meça o que impulsiona as decisões** — contagens de acionadores de ação de superfície de análise interna (viabilizada pelo Adobe Customer Journey Analytics), taxas de sucesso, taxas de abandono, principais prompts de usuário e pontuações de visibilidade.

## Por que [!DNL LLM Apps] é importante

As interações LLM são fundamentalmente diferentes da pesquisa tradicional. A sessão média [!DNL ChatGPT] dura quatro vezes mais do que uma sessão de pesquisa tradicional. Mais de 40% dos consumidores dependem de ferramentas de IA para decisões de compra complexas. Sem o [!DNL LLM Apps], você poderá ganhar a menção, mas perderá o cliente. O [!DNL LLM Apps] garante que sua marca não só fique visível, como também seja acionável no exato momento em que um usuário estiver pronto para decidir.

## Principais conceitos

**Aplicativo LLM** — o seu assistente de marca com o qual os usuários interagem dentro do [!DNL ChatGPT] ou de outras plataformas LLM. Ele agrupa todas as suas ações e faz a implantação como uma única unidade.

**Ação** — um recurso que seu aplicativo oferece. Por exemplo, &quot;Encontre um distribuidor&quot; ou &quot;Procurar produtos&quot;. Cada ação é invocada pelo LLM quando o usuário faz uma pergunta relevante. Cada ação tem duas partes: metadados (nome, descrição, parâmetros) gerenciados na interface do usuário do [!DNL LLM Apps] e um manipulador (seu código) no [!DNL GitHub].

**Manipulador de ação** — o código que é executado quando uma ação é invocada. Ele pode chamar suas APIs, buscar dados em tempo real ou retornar dados estáticos. Os manipuladores ficam no repositório [!DNL GitHub] em `actions/<name>/index.js`.

**Widget** — a resposta visual mostrada ao usuário — um cartão, carrossel, tabela ou qualquer interface do usuário personalizada renderizada junto com a resposta de texto do LLM. Os widgets são páginas do HTML hospedadas em um site do [!DNL Edge Delivery Services] (EDS).

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

## Pré-requisitos

### Console do desenvolvedor da Adobe

Você precisa acessar o [Adobe Developer Console](https://developer.adobe.com/console) com a função de **Desenvolvedor** (ou a função de **Administrador do Sistema**) em sua organização do Adobe IMS. Verifique se sua organização tem acesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Para verificar, vá para [developer.adobe.com/console](https://developer.adobe.com/console). Se aparecer a tela Início rápido, suas permissões estão configuradas corretamente.

![Adobe Developer Console — Tela de Início Rápido confirmando o acesso do desenvolvedor](/help/assets/overview/dev-console-access-granted.png)

Se, em vez disso, você vir uma mensagem de **Acesso restrito**, você não terá a função de Desenvolvedor. Entre em contato com o administrador da organização IMS para solicitar acesso.

![Adobe Developer Console — Mensagem de acesso restrito](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

Você precisa de uma conta do [!DNL GitHub] com as seguintes permissões em sua organização:

- **Criar repositórios** — é necessário criar dois repositórios em sua organização: um para o código do aplicativo e outro para o projeto EDS. Para verificar, vá para [github.com/new](https://github.com/new) — se você puder selecionar sua organização na lista suspensa **Proprietário**, terá a permissão.

  ![Lista suspensa de Proprietário de novo repositório do GitHub mostrando a seleção da organização](/help/assets/overview/github-repo-owner-dropdown.png)

- **Instalar [!DNL GitHub] Aplicativos** — você precisa das permissões apropriadas para instalar um Aplicativo [!DNL GitHub] na sua organização. Consulte [Requisitos para instalar um aplicativo GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

### AEM Sites com [!DNL Edge Delivery Services]

Os widgets de ação estão hospedados em **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. Sua organização precisa de uma licença do AEM Sites que inclua [!DNL Edge Delivery Services]. Você deve ter a função de **Administrador** em sua organização de EDS.

Para verificar, vá para a [Ferramenta de administração de usuários do EDS](https://tools.aem.live/tools/user-admin/index.html), digite o nome da sua organização, deixe o **Site** em branco e clique em **Buscar usuários**. Encontre sua conta na lista e confirme se ela mostra o selo **admin**.

![Ferramenta de administração de usuário do EDS mostrando um usuário com a função de administrador](/help/assets/overview/eds-user-admin.png)

### Plataforma LLM (para teste)

Para testar seu aplicativo implantado, você precisa de uma camada de assinatura com suporte que permita aplicativos MCP personalizados e o **Modo de Desenvolvedor** habilitado. Por exemplo, [!DNL ChatGPT] requer uma assinatura do **Pro**, **Business** ou **Enterprise / Edu**.

## Introdução

Escolha o caminho que corresponda à sua situação:

| | **Participante do Beta** | **Disponibilidade geral** |
|---|---|---|
| **Você** | Você participa do programa Beta e recebeu um arquivo de código do aplicativo, um arquivo de projeto EDS e uma referência de configuração de aplicativo do Adobe | Caso de uso em mente: o Adobe orienta você na criação e implantação de seu aplicativo |
| **Comece aqui** | [Integração com o Beta](/help/beta-onboarding/beta-onboarding.md) | [Criar um aplicativo](/help/guides/create-app.md) |

