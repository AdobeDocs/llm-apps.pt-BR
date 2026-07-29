---
title: Implante seu aplicativo
description: Saiba como implantar seu aplicativo Adobe LLM no preparo e na produção usando a interface do usuário de aplicativos LLM.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---


# Implantar O Aplicativo {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Depois de gravar o código do manipulador e enviá-lo para o repositório vinculado, você poderá implantar o aplicativo na interface do usuário do [!DNL LLM Apps].

Esta é uma etapa compartilhada para cada jornada. Após a implantação, continue a [testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md) ou [testar o conector Claude](/help/guides/test-in-claude.md).

## Iniciar a implantação

Abra a página Detalhes do aplicativo e selecione **[!UICONTROL Implantar]**.

Selecione o ambiente de destino e selecione **[!UICONTROL Implantar]**.

![Implantar — selecione o ambiente de destino](/help/assets/guide-onboarding-agent/deploy-stage.png)

A implantação é executada em quatro etapas:

1. **Preparando** — recupera a configuração necessária para implantar o aplicativo.
2. **Iniciar implantação** — inicia o processo de implantação em segundo plano.
3. **Criar aplicativo** — instala dependências e cria o código de repositório mais recente.
4. **Publicar** — publica o aplicativo em [!DNL Adobe I/O Runtime].

![Implantar — pipeline de implantação em execução](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>Se uma ação tiver metadados na interface do usuário, mas nenhum arquivo de manipulador correspondente no repositório, ela ainda será registrada. As chamadas usam um manipulador de stub padrão até que você adicione o código real.

## Depois de uma implantação bem-sucedida

Quando todas as etapas forem concluídas, a caixa de diálogo exibirá **Implantação bem-sucedida**.

![Implantação — implantação bem-sucedida](/help/assets/guide-onboarding-agent/deploy-successful.png)

Clique em **Fechar** para fechar a caixa de diálogo. Role para baixo até a seção **[!UICONTROL Testar o aplicativo]** na página Detalhes do aplicativo:

![Detalhes do aplicativo — copie a URL do servidor MCP](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Cada ambiente implantado mostra um URL de servidor MCP. Selecione **[!UICONTROL Copiar URL]** e use-a para criar um plug-in na plataforma LLM de destino.

A seção **Histórico de implantação** mostra as últimas 10 implantações:

![Histórico de implantação](/help/assets/guide-deploy/deployment-history.png)

Cada linha mostra o **Ambiente** de destino (Preparo ou Produção), o **Status** (Bem-sucedido ou Com Falha) e a **Implantação na** data. Você pode usar essa tabela para rastrear quando as implantações ocorreram e verificar se
implantação mais recente bem-sucedida.

## Próxima etapa

- [Testar o aplicativo implantado como um plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
- [Teste o aplicativo implantado como um conector Claude](/help/guides/test-in-claude.md).

