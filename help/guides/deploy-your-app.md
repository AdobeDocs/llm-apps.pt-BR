---
title: Implantar O Aplicativo
description: Saiba como implantar seu aplicativo Adobe LLM no preparo e na produção usando a interface do usuário de aplicativos LLM.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '354'
ht-degree: 0%

---


# Implantar O Aplicativo

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

Depois de gravar o código do manipulador e enviá-lo para o repositório vinculado, você poderá implantar o aplicativo na interface do usuário do [!DNL LLM Apps].

## Iniciar a implantação

Navegue até a página Detalhes do aplicativo. Clique no botão **[!UICONTROL Implantar]** no canto superior direito:

![Detalhes do aplicativo — pronto para implantar](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Isso abre a caixa de diálogo de implantação. Selecione o ambiente de destino na lista suspensa:

![Caixa de diálogo Implantar — selecionar ambiente de destino](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

Clique em **[!UICONTROL Implantar]** para iniciar o pipeline. As quatro etapas são:

1. **Coletar credenciais** — lê os metadados do aplicativo, gera um token [!DNL GitHub] e busca credenciais de Tempo de Execução da API do Console.
2. **Acionar pipeline de compilação** — envia todos os parâmetros para o pipeline de compilação.
3. **Clonar e compilar** — o pipeline clona seu repositório, gera `actions.json` dos metadados da interface do usuário, executa o `npm install` e o webpack para produzir `dist/index.js`.
4. **Implantar em Tempo de Execução** — implanta o pacote no namespace [!DNL Adobe I/O Runtime] do seu aplicativo.

Depois de iniciado, o pipeline é executado automaticamente e mostra o progresso em tempo real:

![Implantar pipeline em execução](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>Se uma ação tiver metadados na interface do usuário, mas nenhum arquivo de manipulador correspondente no repositório, ela ainda será registrada. As chamadas usam um manipulador de stub padrão até que você adicione o código real.

## Depois de uma implantação bem-sucedida

Quando todas as etapas forem concluídas, a caixa de diálogo mostrará uma confirmação **Implantação bem-sucedida** com a URL implantada e os detalhes do artefato:

![Implantação bem-sucedida](/help/assets/guide-deploy/app-detail-deploy-finish.png)

Clique em **Fechar** para fechar a caixa de diálogo. Role para baixo até a seção **[!UICONTROL Testar o aplicativo]** na página Detalhes do aplicativo:

![Testar o aplicativo — URLs implantadas](/help/assets/guide-deploy/test-app-deployed.png)

Cada ambiente (**Preparo** e **Produção**) mostra a URL do servidor MCP em [!DNL Adobe I/O Runtime]. Este é o URL fornecido à plataforma LLM ao registrar seu aplicativo. Clique em **Copiar URL** para copiá-la para a área de transferência.

A seção **Histórico de implantação** abaixo mantém um log completo de cada implantação entre ambientes:

![Histórico de implantação](/help/assets/guide-deploy/deployment-history.png)

Cada linha mostra o **Ambiente** de destino (Preparo ou Produção), o **Status** (Bem-sucedido ou Com Falha) e a **Implantação na** data. Você pode usar essa tabela para rastrear quando as implantações ocorreram e verificar se
implantação mais recente bem-sucedida.

