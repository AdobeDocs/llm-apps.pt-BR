---
title: Criar um aplicativo
description: Saiba como criar seu primeiro aplicativo LLM e vinculá-lo ao repositório GitHub.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '745'
ht-degree: 0%

---


# Criar um aplicativo

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

>[!NOTE]
>
>Se você for um **participante do programa Beta**, use o [Guia de integração do Beta](/help/beta-onboarding/beta-onboarding.md) — ele abrange a configuração completa para seu aplicativo específico.

>[!NOTE]
>
>Antes de começar, verifique se todos os [pré-requisitos](/help/overview/overview.md#prerequisites) foram atendidos.

Este guia aborda a criação do primeiro [!DNL Adobe LLM Apps] — do estado vazio para um projeto totalmente configurado vinculado ao seu repositório [!DNL GitHub].

## Abrir [!DNL LLM Apps]

Navegue até [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps). Se nenhum aplicativo tiver sido criado ainda, você verá a página de primeiro carregamento com um prompt para criar seu primeiro aplicativo.

![Página de aplicativos — nenhum aplicativo criado ainda](/help/assets/guide-create-app/first-load.png)

A barra lateral esquerda permite navegar entre **[!UICONTROL Aplicativos]** e **[!UICONTROL Ações]**. Clique em **[!UICONTROL Criar aplicativo]** para começar.

## Preencher detalhes do aplicativo

A caixa de diálogo Criar aplicativo abre em tela cheia.

![Caixa de diálogo Criar Aplicativo](/help/assets/guide-create-app/app-details-1.png)

Insira o seguinte:

- **[!UICONTROL Nome do Aplicativo LLM]** (obrigatório) — o nome de exibição do seu aplicativo. Somente letras, números e espaços são permitidos.
- **[!UICONTROL Descrição do aplicativo LLM]** — uma breve descrição do que o seu aplicativo faz. Por exemplo, o *Ajuda os usuários a descobrir produtos e serviços de livro por meio de uma plataforma LLM*.
- **[!UICONTROL Seu site]** (obrigatório) — o URL do site da sua marca. O [!DNL LLM Apps] usa essa opção para criar automaticamente ações pré-configuradas.

## Selecione uma região de dados de análise

Escolha a região onde os dados de análise deste aplicativo serão armazenados.

>[!IMPORTANT]
>
>A região de dados de análise não pode ser alterada após a criação do aplicativo.

![Lista suspensa da região de dados do Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

A lista suspensa da **região do Analytics** é padronizada para **Estados Unidos (EUA)**. As opções disponíveis são **Estados Unidos (EUA)** e **Europa (UE)**. Selecione a região que melhor corresponde aos requisitos de residência de dados antes de continuar.

## Vincular um repositório do [!DNL GitHub]

Abaixo dos detalhes do aplicativo, você pode vincular um repositório do [!DNL GitHub]. Este repositório é onde seu código de manipulador de ações está — O JavaScript funciona em uma pasta `actions/` executada em [!DNL Adobe I/O Runtime] quando a plataforma LLM chama seu aplicativo.

Se essa for a primeira vez, nenhum repositório será exibido na lista. Você precisa instalar o Aplicativo **[!DNL Adobe LLM Apps Link]** [!DNL GitHub] em sua organização:

1. Clique em **Gerenciar repositórios no Github** na parte inferior da caixa de diálogo.
2. A página do Aplicativo [!DNL Adobe LLM Apps Link] [!DNL GitHub] será aberta em uma nova guia.

   ![Link de Aplicativos Adobe LLM — Página de instalação do Aplicativo GitHub](/help/assets/guide-create-app/github-app-install.png)

3. Clique em **[!UICONTROL Instalar]** e selecione sua organização [!DNL GitHub].
4. Em **[!UICONTROL Acesso ao repositório]**, escolha **Somente selecionar repositórios** e selecione o repositório que hospedará o código do aplicativo.

   ![Link de Aplicativos Adobe LLM — acesso ao repositório](/help/assets/guide-create-app/github-repo-access.png)

5. Clique em **[!UICONTROL Salvar]**. Retorne à caixa de diálogo Criar aplicativo — seu repositório agora aparece na lista suspensa **Selecionar repositório**.
6. Selecione o repositório que deseja usar.

![Caixa de diálogo Criar Aplicativo — repositório vinculado](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>Você pode ignorar a vinculação de um repositório durante a criação do aplicativo e fazer isso mais tarde nas configurações do aplicativo. No entanto, não é possível implantar até que um repositório esteja vinculado.

## Criar o aplicativo

Clique em **[!UICONTROL Criar aplicativo]**. Uma tela de carregamento é exibida enquanto o projeto está sendo criado no Developer Console.

![Criando aplicativo — carregando tela](/help/assets/guide-create-app/app-loading.png)

Após a conclusão, você será redirecionado para a página **Detalhes do aplicativo**.

## A página Detalhes do aplicativo

A página Detalhes do aplicativo é o hub central para gerenciar seu aplicativo.

![Página de detalhes do aplicativo — seções principais](/help/assets/guide-create-app/app-detail-top.png)

### Banner do aplicativo

![Banner do aplicativo](/help/assets/guide-create-app/app-banner.png)

O banner colorido na parte superior mostra o aplicativo selecionado no momento, incluindo o avatar, o nome, a descrição e uma lista suspensa do aplicativo para alternar entre aplicativos. O banner permanece fixo na parte superior ao rolar a tela.

### Título da página e ações

![Banner do aplicativo](/help/assets/guide-create-app/page-title.png)

Abaixo do banner, você vê o nome do aplicativo como um cabeçalho, com os seguintes botões de ação:

- **...** (mais ações) — cria um novo aplicativo ou exclui o atual.
- **[!UICONTROL Configurações]** — configure o repositório vinculado e outras opções.
- **[!UICONTROL Implantar]** — implante seu aplicativo em [!DNL Adobe I/O Runtime] (desabilitado até que um repositório seja vinculado).

### Cartão de informações do aplicativo

![Cartão de informações do aplicativo](/help/assets/guide-create-app/app-info-card.png)

Este cartão resume os metadados principais do seu aplicativo: nome, descrição, selo de status (**Não implantado** ou **Implantado**), ID do aplicativo e data de criação. Ela também mostra os dois repositórios vinculados:

- **Repositório de manipulador** — onde está o código do manipulador de ação (o JavaScript funciona em [!DNL Adobe I/O Runtime]).
- **Repositório EDS** — onde fica a interface do widget (blocos e estilos oferecidos por [!DNL Edge Delivery Services]).

### Ações, Testar o aplicativo e Histórico de implantação

![Página de detalhes do aplicativo — seções inferiores](/help/assets/guide-create-app/app-detail-bottom.png)

Abaixo do cartão de informações você encontra três seções:

- **[!UICONTROL Ações]** — lista os manipuladores de ação definidos para o seu aplicativo. Clique em **Ir para Ações** para navegar até a página Ações.
- **[!UICONTROL Testar o aplicativo]** — após a implantação, exibe as URLs do servidor MCP para ambientes de Preparo e Produção.
- **Histórico de implantação** — rastreia todas as implantações em ambientes com status e data.

## Próximas etapas

- [Guia: Criar uma Ação](/help/guides/create-action.md) — Defina uma ação com configurações de metadados e widget.

