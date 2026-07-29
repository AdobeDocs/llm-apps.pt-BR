---
title: Testar seu aplicativo LLM como um plug-in ChatGPT
description: Crie um plug-in ChatGPT a partir do URL do servidor MCP dos aplicativos Adobe LLM e teste-o em uma conversa.
source-git-commit: b7199fbb387d91a5c77deac47a2bc883381931c1
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# Testar seu aplicativo LLM como um plug-in [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Após a implantação, o aplicativo LLM expõe um URL de servidor MCP. Adicione esta URL a [!DNL ChatGPT] como um plug-in e teste as ações e widgets gerados.

Esta é a etapa final de verificação após criar, personalizar ou estender um aplicativo.

## Requisitos do plano

O modo de desenvolvedor está disponível na Web para contas Pro, Plus, Business, Enterprise e Education. Os administradores do Workspace podem restringir o acesso.

## Ativar modo de desenvolvedor

Em [!DNL ChatGPT]:

1. Abrir **[!UICONTROL Configurações] → [!UICONTROL Segurança e logon]**.
2. Ative o **[!UICONTROL Modo de desenvolvedor]**.

O botão de mais na página Plug-ins cria plug-ins com suporte a MCP somente após a ativação do modo de desenvolvedor. Consulte [Modo de desenvolvedor do ChatGPT](https://developers.openai.com/api/docs/guides/developer-mode).

## Copie o URL do servidor MCP

Em [!DNL LLM Apps]:

1. Abra a página Detalhes do aplicativo.
2. Localizar **[!UICONTROL Testar o aplicativo]**.
3. Em **[!UICONTROL Ambiente de preparo]**, selecione **[!UICONTROL Copiar URL]**.

## Criar o plug-in

1. Abra [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. Na guia **[!UICONTROL Plug-ins]**, selecione **+** ao lado do campo de pesquisa.

   ![ChatGPT — Página de plug-ins](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. Em **[!UICONTROL Novo Plug-in]**, digite:
   - **[!UICONTROL Nome]** — o nome do plug-in.
   - **[!UICONTROL Descrição]** — opcional.
   - **[!UICONTROL Conexão]** — selecione **[!UICONTROL URL do Servidor]** e cole a URL do servidor MCP.
   - **[!UICONTROL Autenticação]** — selecione **[!UICONTROL Sem autenticação]**.
4. Selecione **[!UICONTROL Entendo e desejo continuar]**.
5. Selecione **[!UICONTROL Criar]**.

   ![ChatGPT — crie um plug-in com a URL do servidor MCP](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)

6. No diálogo de confirmação, selecione **[!UICONTROL Conectar]**.

   ![ChatGPT — conectar o novo plug-in](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## Testar o plug-in

1. Inicie um novo chat.
2. No menu Mais, escolha **[!UICONTROL Modo de desenvolvedor]** e selecione o plug-in.
3. Faça uma pergunta que corresponda a uma das ações geradas. Por exemplo: *Mostre-me um pouco de café.*

![ChatGPT — resposta de plug-in do aplicativo LLM gerada](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Verifique se:

- [!DNL ChatGPT] invoca a ação esperada.
- O widget exibe os dados de amostra esperados.
- A resposta de texto corresponde ao widget.
- Os controles de widget funcionam conforme esperado.

## O que vem a seguir

- [Personalizar os widgets gerados](/help/guides/widgets.md).
- [Criar uma ação do zero](/help/guides/create-action.md).
