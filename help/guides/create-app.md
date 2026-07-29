---
title: Criar o primeiro aplicativo LLM automaticamente
description: Crie um aplicativo Adobe LLM no seu site, analise as ações geradas, implante-o e teste-o em uma plataforma LLM compatível, como o ChatGPT.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1217'
ht-degree: 0%

---


# Criar Seu Primeiro Aplicativo Automaticamente {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

A plataforma transforma seu site em um andaime de aplicativo de trabalho. Ele propõe ações, grava código do manipulador e testa, cria widgets EDS e envia os arquivos gerados para dois repositórios [!DNL GitHub] que você possui.

Aguarde aproximadamente 15 minutos para a geração. No final deste tutorial, você terá um aplicativo implantado que poderá testar em uma plataforma LLM compatível, como o [!DNL ChatGPT].

**Jornada:** Confirme os requisitos → crie dois repositórios → crie o aplicativo → revise as ações geradas → implante em Preparo → teste o plug-in → conecte os sistemas de produção.

## Antes de começar

Conclua todos os [requisitos de Aplicativos LLM](/help/overview/overview.md#requirements) antes de iniciar este tutorial.

Este tutorial cria um aplicativo LLM para o [Frescopa Coffee](https://frescopa.coffee/).

## Criar dois repositórios vazios

A plataforma precisa de dois repositórios vazios. Crie ambos na mesma conta ou organização [!DNL GitHub]:

- **Repositório de manipulador** — armazena manipuladores de ação e testes. Por exemplo, `my-brand-llm-app`.
- **Repositório EDS** — armazena blocos de widget e estilos gerados. Por exemplo, `my-brand-llm-app-eds`.

Vá para [github.com/new](https://github.com/new) para cada repositório.

Não inicialize o repositório com um arquivo README, `.gitignore` ou licença. A plataforma prepara a estrutura de projeto necessária.

>[!TIP]
>
>Use nomes de repositório que identifiquem o aplicativo e a finalidade de cada repositório. Isso facilita seu reconhecimento na caixa de diálogo de criação do aplicativo.

## Iniciar o aplicativo

1. Abra os [Aplicativos Adobe LLM](https://experience.adobe.com/#/@llmapps/llm-apps/) e selecione **[!UICONTROL Criar aplicativo]**.
2. Insira o **[!UICONTROL Nome do Aplicativo LLM]** e uma descrição opcional.
3. Selecione a **[!UICONTROL região do Analytics]**.

   >[!IMPORTANT]
   >
   >A região de análise não pode ser alterada após a criação do aplicativo.

4. Em **[!UICONTROL Criar Meu Aplicativo]**, selecione **[!UICONTROL Criar meu aplicativo automaticamente]**.
5. Em **[!UICONTROL seu site]**, digite a URL do site, incluindo o protocolo `https://`. A plataforma analisa esse site para determinar ações úteis e resultados de amostra representativos.

![Criar Aplicativo LLM — detalhes do aplicativo e Compilar Meu Aplicativo habilitado](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## Conceder acesso aos repositórios a [!DNL LLM Apps]

O aplicativo Adobe LLM Apps [!DNL GitHub] dá a [!DNL LLM Apps] acesso aos repositórios selecionados.

>[!NOTE]
>
>A conexão de uma organização [!DNL GitHub] é uma configuração única. Se a organização já aparecer na caixa de diálogo, use **[!UICONTROL Gerenciar repositórios no GitHub]** em vez de conectá-la novamente.

### Organização conectada

Se o aplicativo [!DNL GitHub] do Adobe LLM Apps já tiver sido instalado antes da criação dos repositórios:

1. Selecione a organização conectada.
2. Selecione **[!UICONTROL Gerenciar repositórios no GitHub]**.
3. Adicione os dois repositórios à instalação existente do Aplicativo [!DNL GitHub].
4. Retorne a [!DNL LLM Apps] e atualize as listas de repositório.

### Somente primeira conexão

Se a organização não aparecer na caixa de diálogo:

1. Selecione **[!UICONTROL Conectar a uma organização do GitHub]**.
2. Instalar o Aplicativo [!DNL GitHub] de Aplicativos Adobe LLM.
3. Escolha **[!UICONTROL Selecionar apenas repositórios]** e selecione os dois repositórios.
4. Retorne à caixa de diálogo Criar aplicativo LLM.

Se não conseguir instalar ou atualizar o Aplicativo [!DNL GitHub], peça a um administrador da organização.

## Selecionar os repositórios

1. Em **[!UICONTROL Repositório Modelo]**, selecione a organização e o repositório de manipuladores vazio.
2. Em **[!UICONTROL Repositório EDS]**, selecione a organização e o repositório EDS vazio.

   ![Criar Meu Aplicativo — selecione a organização do GitHub, o Repositório Modelo e o Repositório EDS](/help/assets/guide-onboarding-agent/repos-selected.png)

3. Em **[!UICONTROL Termos e Condições]**, marque **[!UICONTROL Aceito os Termos do Adobe Developer]**.
4. Selecione **[!UICONTROL Criar aplicativo]**.

## Conclua a configuração do EDS

Quando o repositório EDS selecionado está vazio, o [!DNL LLM Apps] o inicializa com o padrão AEM. A caixa de diálogo solicita que você instale a Sincronização de código do AEM antes de tentar criar o aplicativo novamente.

1. Na mensagem abaixo do repositório EDS, selecione **[!UICONTROL Instalar sincronização de código do AEM]**.
2. Em [!DNL GitHub], instale a Sincronização de Código AEM e conceda a ela acesso ao repositório EDS.
3. Retorne à caixa de diálogo Criar aplicativo LLM.

![Criar Aplicativo LLM — repositório EDS vazio inicializado e sincronização de código AEM necessária](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

Você deve ser um administrador do site de EDS. Se a caixa de diálogo relatar que você não é um administrador:

![Criar Aplicativo LLM — Acesso de administrador EDS necessário](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. Selecione **[!UICONTROL Abrir Administrador Do AEM Live]**.
2. Adicione a si mesmo como administrador para o site EDS clicando no botão **[!UICONTROL + Adicionar Usuário(s)]**.

   ![Criar Aplicativo LLM — Adicione você como um Administrador de EDS](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. Retorne a [!DNL LLM Apps], atualize o repositório EDS e selecione **[!UICONTROL Criar Aplicativo]** novamente.

Depois que as verificações do repositório e do administrador forem bem-sucedidas, [!DNL LLM Apps] cria o aplicativo e começa a gerar ações.

## Aguardar a geração de Ações

Vá para a página **[!UICONTROL Ações]**, à esquerda. A página Ações mostra **Descobrindo ações para sua experiência de conversação** enquanto o agente analisa o site e gera o aplicativo. A geração geralmente leva aproximadamente 15 minutos. Você pode sair desta página e retornar mais tarde.

![Ações — gerando recomendações](/help/assets/guide-onboarding-agent/actions-generating.png)

Durante a geração, [!DNL LLM Apps]:

1. Analisa o site e identifica intenções úteis do cliente.
2. Cria metadados de ação, incluindo descrições e parâmetros de entrada.
3. Gera um manipulador e testa para cada ação no repositório do manipulador.
4. Gera um dispositivo EDS para cada ação no repositório EDS.
5. Prepara as ações para sua revisão.

Os manipuladores gerados usam inicialmente dados de amostra derivados do site. Eles demonstram a experiência completa, mas não se conectam aos sistemas de produção.

## Revisar as ações geradas

Quando a geração termina, a página Ações exibe as ações geradas e as visualizações do widget. Cada ação tem uma **[!UICONTROL ação gerada por IA, precisa da medalha review]**.

![Ações — ações geradas prontas para revisão](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

Para cada ação:

1. Selecione **[!UICONTROL Revisão]**.
2. Revise o nome, a descrição, os parâmetros, as anotações, o manipulador gerado e o dispositivo.
3. Selecione **[!UICONTROL Marcar como revisado]**. Isso mescla as solicitações de pull geradas.
4. Retorne à página Ações e repita as ações restantes.

![Ação gerada — pronta para marcar como revisada](/help/assets/guide-onboarding-agent/generated-action-review.png)

Quando todas as ações forem revisadas, selecione **[!UICONTROL Ir para a página do aplicativo]**.

![Ações — todas as ações geradas revisadas](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>O código gerado é um ponto de partida que você possui. Você pode alterar metadados de ação, manipuladores, testes, JavaScript do widget e estilos de widget após a revisão.

## Implantar o aplicativo

1. Retorne à página Detalhes do aplicativo.
2. Selecione **[!UICONTROL Implantar]**.
3. Selecione **[!UICONTROL Preparo]** como o ambiente de destino.
4. Selecione **[!UICONTROL Implantar]**.

![Implantar — selecione o ambiente de Preparo](/help/assets/guide-onboarding-agent/deploy-stage.png)

Aguarde enquanto [!DNL LLM Apps] prepara, compila e publica o aplicativo.

![Implantar — pipeline de implantação em execução](/help/assets/guide-onboarding-agent/deploy-running.png)

![Implantar — implantação de preparo bem-sucedida](/help/assets/guide-onboarding-agent/deploy-successful.png)

Após a implantação, a seção **[!UICONTROL Testar o aplicativo]** exibe a URL do servidor MCP de preparo. Selecione **[!UICONTROL Copiar URL]**.

![Detalhe do Aplicativo — copie a URL do servidor MCP de preparo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## Teste em [!DNL ChatGPT]

Siga [Testar no ChatGPT](/help/guides/test-in-chatgpt.md) para criar um plug-in usando a URL do servidor MCP de preparo.

Faça uma pergunta que corresponda a uma das ações geradas. Verifique se:

- [!DNL ChatGPT] seleciona a ação esperada.
- O widget é renderizado e contém os dados de amostra esperados.
- Os controles de widget produzem o comportamento de acompanhamento esperado.
- A resposta em texto resume com precisão o resultado.

![ChatGPT — resposta de plug-in do aplicativo LLM gerada](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Agora você tem um scaffold completo.

## Prepare o aplicativo para produção

O aplicativo gerado usa dados de amostra. Antes de usá-lo com os clientes:

1. **Conecte seus sistemas** — [personalize cada manipulador gerado](/help/guides/customize-handler.md) para substituir dados de exemplo por chamadas para suas APIs ou fontes de dados.
2. **Proteger credenciais** — armazene URLs de API e credenciais na configuração de tempo de execução gerenciada, nunca no código-fonte ou no widget JavaScript.
3. **Validar dados** — valide argumentos de ação e respostas da API, adicione tempos limite de solicitação e retorne mensagens de erro seguras.
4. **Atualizar os widgets** — mantenha cada widget alinhado com o `structuredContent` do seu manipulador e, em seguida, aplique seus requisitos de identidade visual e acessibilidade. Consulte [Personalizar um widget gerado](/help/guides/widgets.md).
5. **Testar os manipuladores** — cobre entrada válida, entrada inválida, resultados vazios, falhas de API e a forma de dados esperada pelo widget.
6. **Verificar no Estágio** — reimplante e teste cada ação por meio do plug-in [!DNL ChatGPT].
7. **Implantar para produção** — após o êxito no teste de preparo, implante para produção e crie ou atualize o plug-in com a URL do servidor MCP de produção.

Para adicionar um recurso que a plataforma não criou, consulte [Criar uma ação do zero](/help/guides/create-action.md).

