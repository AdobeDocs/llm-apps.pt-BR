---
title: Testar seu aplicativo LLM como um conector Claude
description: Crie um conector Claude a partir do URL do servidor MCP do Adobe LLM Apps e teste-o em uma conversa.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 0%

---


# Testar seu aplicativo LLM como um conector do [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Após a implantação, o aplicativo LLM expõe um URL de servidor MCP. Adicione esta URL a [!DNL Claude] como um conector personalizado e teste as ações e widgets gerados.

Esta é a etapa final de verificação após criar, personalizar ou estender um aplicativo.

## Requisitos do plano

Conectores personalizados usando MCP remoto estão disponíveis nos planos do [!DNL Claude], [!DNL Claude] Desktop e Cowork para Free, Pro, Max, Team e Enterprise. As contas de plano gratuito estão limitadas a um conector personalizado. Para organizações Team e Enterprise, um Proprietário ou Proprietário Principal deve habilitar conectores antes que outros membros possam usá-los.

## Copie o URL do servidor MCP

Em [!DNL LLM Apps]:

1. Abra a página Detalhes do aplicativo.
2. Localizar **[!UICONTROL Testar o aplicativo]**.
3. Em **[!UICONTROL Ambiente de preparo]**, selecione **[!UICONTROL Copiar URL]**.

## Adicionar o conector personalizado

1. Abra [claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors). Isso abre diretamente a caixa de diálogo **[!UICONTROL Adicionar conector personalizado]**.
2. Insira:
   - **[!UICONTROL Nome]** — o nome do conector.
   - **[!UICONTROL URL do servidor MCP remoto]** — o URL do servidor MCP copiado.
3. Selecione **[!UICONTROL Adicionar]**.

   ![Claude — Caixa de diálogo Adicionar conector personalizado](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>Use conectores somente de desenvolvedores confiáveis. A Anthropic não controla quais ferramentas os desenvolvedores disponibilizam e não pode verificar se funcionarão conforme o esperado ou se não serão alteradas.

## Permitir as ferramentas geradas

Cada ação gerada está listada em **[!UICONTROL Permissões de ferramenta]** na página do conector. Por padrão, as novas ferramentas estão definidas como **[!UICONTROL Precisa de aprovação]**, que solicita a aprovação de cada chamada durante o teste.

Defina cada ferramenta — ou o grupo inteiro de **[!UICONTROL Ferramentas interativas]** — como **[!UICONTROL Sempre permitir]**, para que o teste não seja interrompido por prompts de aprovação.

![Claude — definir as permissões da ferramenta como Sempre permitir](/help/assets/guide-test-claude/claude-tool-permissions.png)

## Teste o conector

1. Inicie um novo chat.
2. Selecione **+** na caixa de mensagem (ou digite `/`), passe o mouse sobre **[!UICONTROL Conectores]** e ative o conector adicionado para esta conversa.

   ![Claude — habilitar o conector para a conversa](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. Faça uma pergunta que corresponda a uma das ações geradas. Por exemplo: *Mostre-me um pouco de café.*

Verifique se:

- [!DNL Claude] invoca a ação esperada.
- O widget exibe os dados de amostra esperados.
- A resposta de texto corresponde ao widget.
- Os controles de widget funcionam conforme esperado.

## O que vem a seguir

- [Personalizar os widgets gerados](/help/guides/widgets.md).
- [Criar uma ação do zero](/help/guides/create-action.md).
