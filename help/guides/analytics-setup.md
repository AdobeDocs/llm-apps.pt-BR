---
title: Configurar o Adobe Analytics e o Customer Journey Analytics
description: Encaminhe eventos de aplicativos Adobe LLM para seu próprio conjunto de relatórios do Adobe Analytics e visualização de dados do Customer Journey Analytics por meio de uma sequência de dados do Adobe Experience Platform.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# Configurar o Adobe Analytics e o Customer Journey Analytics {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Envie os eventos do seu aplicativo para [!DNL Adobe Analytics], [!DNL Customer Journey Analytics] ou ambos. [!DNL LLM Apps] encaminha os eventos para a Edge Network [!DNL Adobe Experience Platform] por meio de uma sequência de dados que você possui. O fluxo de dados roteia os eventos para cada serviço que você adiciona a ele.

- **[!DNL Adobe Analytics]** relata quais ações a plataforma LLM chama, quais links de widget as pessoas selecionam e a intenção por trás de cada chamada.
- **[!DNL Customer Journey Analytics]** analisa os mesmos eventos por pessoa, nas visualizações de dados que você compila nos dados de [!DNL Experience Platform].

Esta configuração é separada da análise interna em [!DNL LLM Apps].

**Neste guia:** Saiba quais configurações o [!DNL LLM Apps] exige no [!DNL Experience Platform], [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics], com links para o procedimento do Experience League para cada tela de produto. Em seguida, habilite o Analytics no aplicativo, implante no Stage e verifique os dados antes de implantar na produção.

## Lista de verificação de configuração {#setup-checklist}

Conclua todas as etapas marcadas como **Obrigatório** para os serviços que você usa. Uma etapa ignorada raramente mostra um erro. Em vez disso, os dados não chegam.

| Etapa | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. Criar o namespace de identidade](#identity-namespace) | Obrigatório | Obrigatório |
| [2. Criar o esquema](#schema) | Obrigatório | Obrigatório |
| [3. Criar o conjunto de dados](#dataset) | Não é necessário | Obrigatório |
| [4. Preparar o conjunto de relatórios](#report-suite) | Obrigatório | Não é necessário |
| [5. Criar a sequência de dados](#datastream) | Obrigatório | Obrigatório |
| [6. Habilitar o Analytics e implantar no Palco](#enable-analytics) | Obrigatório | Obrigatório |
| [7. Criar a conexão e a exibição de dados](#cja) | Não é necessário | Obrigatório |
| [8. Verifique os dados e implante para Produção](#verify) | Obrigatório | Obrigatório |

Crie o namespace, esquema, conjunto de dados e sequência de dados na mesma sandbox [!DNL Experience Platform]. Use a sandbox e a sequência de dados que você planeja relatar na produção. Depois de salvar a sequência de dados no aplicativo, entre em contato com a equipe de aplicativos LLM para alterá-la.

## Antes de começar {#before-you-begin}

Você precisa:

- **Um tempo de execução com suporte e o SDK.** Seu repositório de manipuladores deve usar o `@adobe/llm-apps-runtime` 1.2.0 ou posterior. Para widgets EDS, use o LLM Apps SDK 0.1.13 ou posterior. Para atualizar um aplicativo antigo para o tempo de execução mais recente e para o SDK antes de habilitar o Analytics, siga [Atualizar um aplicativo existente](/help/guides/upgrade-existing-app.md). Os aplicativos que atendem a esses requisitos não precisam de uma atualização.
- **Os grupos de campos agênticos.** Sua organização deve estar habilitada para os grupos de campos Informações de Agentes e Evento de Conversa. Se esses grupos de campos não aparecerem quando você adicionar grupos de campos a um esquema, entre em contato com a equipe de conta da Adobe. Consulte [Implementar Insights de Conversa](https://experienceleague.adobe.com/en/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets). As chamadas de ação são registradas no campo `agenticExperience.tools` de Informações Agenciais, que está no Beta em [!DNL Experience Platform].
- **[!DNL Experience Platform]permissões** para criar namespaces de identidade, esquemas, conjuntos de dados e fluxos de dados em sua sandbox.
- **Para [!DNL Adobe Analytics]:** permissão para gerenciar conjuntos de relatórios e regras de processamento. Os administradores de produto têm ambos por padrão.
- **Por [!DNL Customer Journey Analytics]:** acesso de administrador de produto para criar conexões e visualizações de dados.

## O que seu aplicativo envia {#what-your-app-sends}

Depois de habilitar o Analytics e implantar, seu aplicativo envia dois tipos de eventos:

| Evento | `eventType` | Enviado quando | Inclui |
|---|---|---|---|
| Chamada de ação | `mcp.tool_call` | A plataforma LLM chama uma das ações do seu aplicativo, incluindo chamadas que falham ou retornam um prompt de entrada. | O identificador de código da ação e se a chamada falhou. A intenção do usuário, quando a ação a coleta e a plataforma fornece uma. Quando a plataforma fornece esses itens: o nome da plataforma, a ID da conversa, a ID de usuário da plataforma, o local e a organização, além da localização aproximada do usuário. |
| Interação do widget | `web.webinteraction.linkClicks` | Alguém seleciona um link ou botão em um widget do Edge Delivery Services. | O rótulo do link ou botão, até 100 caracteres. Quando fornecido pela chamada de ação, exibe a ID de conversa e a ID de ativação. O Adobe Web SDK também adiciona o URL do quadro do widget, o tamanho da tela e da janela do navegador, a hora local e o fuso horário, e sua própria versão. |

Em [!DNL Adobe Analytics], cada chamada de ação conta como uma exibição de página de uma página chamada `mcp:<code identifier>`, e cada interação de widget conta como um clique de link. Consulte [tipos de evento do Edge Network no Adobe Analytics](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/hit-types).

Cada chamada de ação e interação de widget é uma ocorrência de [!DNL Adobe Analytics] e um evento no conjunto de dados [!DNL Experience Platform]. Em um aplicativo de alto tráfego, verifique se o contrato cobre esse volume antes de habilitar o Analytics.

Lembre-se desses detalhes ao ler os relatórios:

- **Chamadas com falha incluem prompts de entrada.** Se algumas ações exigirem logon, espere uma taxa de falha mais alta para elas.
- **Algumas chamadas rejeitadas não foram gravadas.** Uma chamada é rejeitada antes da execução da ação e não envia nenhum evento quando uma plataforma diferente de [!DNL ChatGPT] chama uma ação que requer entrada sem uma entrada válida ou o escopo necessário. Em aplicativos em que cada ação requer logon, qualquer chamada sem logon é rejeitada da mesma forma.
- **A intenção do usuário está frequentemente ausente.** A plataforma decide se deve fornecê-lo, tantas chamadas não têm nenhum e aparecem como **Não Especificado**.
- **Os detalhes da plataforma variam.** Atualmente, [!DNL ChatGPT] fornece todos eles. Para [!DNL Claude], somente o nome da plataforma é gravado.
- **Os rótulos do widget vêm da página.** Para definir um rótulo estável, adicione um atributo `data-analytics` ao link ou botão. Caso contrário, o texto visível será usado.

Para escolher quais ações coletam a intenção do usuário, consulte [Configurar análises](/help/guides/create-action.md#configure-analytics).

>[!NOTE]
>
>Os eventos podem incluir uma ID de usuário da plataforma e o local aproximado. Analise sua política de privacidade e as configurações de governança de dados antes de habilitar o Analytics.

### Como os eventos identificam pessoas {#identities}

| Evento | Identidade principal | Namespace |
|---|---|---|
| Chamada de ação | A ID de usuário da plataforma LLM, quando a plataforma fornece uma | `MCPHOSTUSER` |
| Interação do widget | Uma Experience Cloud ID (ECID) que a Edge Network emite durante a chamada de ação. A ID `MCPHOSTUSER` é incluída como uma identidade secundária quando disponível. | `ECID` |

Algumas plataformas, como [!DNL Claude], não fornecem uma ID de usuário. As chamadas de ação dessas plataformas não possuem identidade, portanto, [!DNL Customer Journey Analytics] as ignora.

>[!NOTE]
>
>Cada chamada de ação recebe uma nova ECID, e uma interação de widget usa a ECID da chamada de ação que mostrou o widget. Como resultado:
>
>- [!DNL Customer Journey Analytics] conta as chamadas de ação de um usuário como uma pessoa, mas conta as interações de widget como uma pessoa diferente para cada chamada de ação.
>- [!DNL Adobe Analytics] pode contar cada chamada de ação como um novo visitante.

## Etapa 1: criar o namespace de identidade {#identity-namespace}

**Necessário para:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

As chamadas de ação identificam pessoas no namespace `MCPHOSTUSER`, portanto, o namespace deve existir em sua sandbox. Criar um namespace personalizado com estas configurações:

| Configuração | Valor |
|---|---|
| Nome de exibição | `MCP host user`, ou outro nome que você escolher |
| Símbolo de identidade | `MCPHOSTUSER`, exatamente como mostrado |
| Tipo de identidade | **[!UICONTROL Identificação individual entre dispositivos]** |

Para obter o procedimento, consulte [Criar namespaces personalizados](https://experienceleague.adobe.com/en/docs/experience-platform/identity/features/namespaces#create-namespaces).

## Etapa 2: Criar o esquema {#schema}

**Necessário para:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

O fluxo de dados e o conjunto de dados usam esse esquema. Crie um esquema com estas configurações:

| Configuração | Valor |
|---|---|
| Tipo de esquema | **[!UICONTROL Padrão]**, criado com a opção **[!UICONTROL Manual]** |
| Classe base | **[!UICONTROL Evento de experiência]** |
| Grupos de campos | Informações sobre Agentes, Evento de Conversa, Detalhes da Web, Detalhes do Ambiente e Detalhes de Implementação |
| Nome de exibição | Qualquer nome, por exemplo `AEM LLM Apps` |

>[!IMPORTANT]
>
>Adicione todos os cinco grupos de campos. [!DNL LLM Apps] requer Informações de Agente e Evento de Conversa para chamadas de ação. As outras três contêm a página, o link, o dispositivo, o ambiente e os campos de implementação que os eventos também incluem. Se um grupo de campos estiver ausente, [!DNL Experience Platform] poderá descartar esses campos ou rejeitar o evento.

Você não precisa do grupo de campos Adobe Analytics ExperienceEvent. A intenção do usuário atinge [!DNL Adobe Analytics] através de uma regra de processamento. Consulte [Etapa 4](#report-suite).

Para obter o procedimento, consulte [Criar um novo esquema](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#create) e [Adicionar grupos de campos](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups).

## Etapa 3: Criar o conjunto de dados {#dataset}

**Necessário para:** [!DNL Customer Journey Analytics].

O conjunto de dados armazena os eventos do seu aplicativo em [!DNL Experience Platform]. Crie um conjunto de dados com estas configurações:

| Configuração | Valor |
|---|---|
| Método de criação | **[!UICONTROL Criar conjunto de dados a partir do esquema]** |
| Esquema | O esquema da [Etapa 2](#schema) |
| Nome | Qualquer nome, por exemplo `AEM LLM Apps` |

Para obter o procedimento, consulte [Criar um conjunto de dados](https://experienceleague.adobe.com/en/docs/experience-platform/catalog/datasets/user-guide#create).

## Etapa 4: Preparar o conjunto de relatórios {#report-suite}

**Necessário para:** [!DNL Adobe Analytics].

O [!DNL Adobe Analytics] mapeia o nome da página e os campos de link automaticamente; portanto, as páginas `mcp:<code identifier>` e os cliques no link do widget não precisam de configuração extra. A intenção do usuário precisa de uma eVar e uma regra de processamento.

### Criar ou escolher um conjunto de relatórios {#create-report-suite}

Crie um conjunto de relatórios ou escolha um existente. Anote a ID completa do conjunto de relatórios, incluindo o prefixo que [!DNL Adobe Analytics] adiciona. Você precisa da ID em [Etapa 5](#datastream).

Para obter o procedimento, consulte [Criar um conjunto de relatórios](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite).

### Ativar a eVar de intenção do usuário {#user-intent-evar}

Nas variáveis de conversão do conjunto de relatórios, ative uma eVar com estas configurações:

| Configuração | Valor |
|---|---|
| eVar | EVAR3 |
| Nome | `User Intent` |
| Tipo | **[!UICONTROL Cadeia de caracteres de texto]** |
| Alocação | **[!UICONTROL Mais recente (último)]** |
| Expirar após | **[!UICONTROL Visita]** |
| Status | **[!UICONTROL Habilitado]** |

Se o conjunto de relatórios já usar o eVar3, escolha outro eVar não utilizado e use esse eVar na regra de processamento.

Para obter o procedimento, consulte [Adicionar ou editar variáveis de conversão](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables).

### Criar a regra de processamento {#processing-rule}

[!DNL Adobe Analytics] recebe a intenção do usuário como uma variável de dados de contexto, não como uma eVar. Para saber o motivo, consulte [Mapeamento implícito](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping). Crie uma regra de processamento que copie a intenção do usuário na eVar:

- **Condição:** a variável de dados de contexto `a.x.conversation.prompt.raw.0.purpose` é igual a `intents`.
- **Ação:** substituir o valor de **Intenção de Usuário (eVar3)** pela variável de dados de contexto `a.x.conversation.prompt.raw.0.text`.

Sem esta regra, a dimensão **Intenção de usuário** permanece vazia. A regra se aplica somente aos dados coletados após serem salvos. As interações de widget não têm intenção de usuário, portanto, a regra não as altera.

Outros campos, como a falha da chamada ou o nome da plataforma, também chegam como variáveis de dados de contexto. Para relatar sobre eles em [!DNL Adobe Analytics], crie mais regras de processamento da mesma maneira.

Para obter o procedimento, consulte [Visão geral das regras de processamento](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview) e [Casos de uso das regras de processamento](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases).

## Etapa 5: criar a sequência de dados {#datastream}

**Necessário para:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

A sequência de dados recebe os eventos do aplicativo e os encaminha para cada serviço adicionado.

### Criar a sequência de dados {#create-datastream}

Criar um fluxo de dados com estas configurações:

| Configuração | Valor |
|---|---|
| Nome | Qualquer nome, por exemplo `AEM LLM Apps` |
| Esquema de mapeamento | O esquema da [Etapa 2](#schema) |

Crie o fluxo de dados na mesma sandbox do esquema. Não é possível alterar a sandbox de um fluxo de dados após criá-lo.

Para obter o procedimento, consulte [Criar uma sequência de dados](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#create).

### Adicionar os serviços {#add-services}

Adicione um serviço para cada destino:

| Serviço | Configurações | Obrigatório para |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL Habilitado]**. Em **[!UICONTROL ID do Conjunto de Relatórios]**, insira a ID da [Etapa 4](#report-suite). | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL Habilitado]**. Em **[!UICONTROL Conjunto de Dados do Evento]**, selecione o conjunto de dados da [Etapa 3](#dataset). Deixe as outras opções em branco. | [!DNL Customer Journey Analytics] |

![Adicionar Serviço - Adobe Analytics habilitado com um exemplo de ID de conjunto de relatórios](/help/assets/guide-analytics/datastream-analytics-service.png)

![Serviço Adobe Experience Platform - o conjunto de dados do evento selecionado e as outras opções limpas](/help/assets/guide-analytics/datastream-platform-service.png)

Para obter o procedimento, consulte [Adicionar serviços a uma sequência de dados](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#add-services), [configurações do Adobe Analytics](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#analytics) e [configurações do Adobe Experience Platform](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#aep).

Em seguida, copie a **[!UICONTROL ID da sequência de dados]** dos detalhes da sequência de dados. Você precisa dele na [Etapa 6](#enable-analytics).

![Detalhes da sequência de dados - Serviços Adobe Analytics e Adobe Experience Platform habilitados, com o exemplo de ID da sequência de dados e o esquema de mapeamento no painel lateral](/help/assets/guide-analytics/datastream-services.png)

## Etapa 6: habilitar o Analytics e implantar no preparo {#enable-analytics}

**Necessário para:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

>[!IMPORTANT]
>
>Depois de salvar, a sequência de dados é somente leitura nas Configurações do aplicativo. Entre em contato com a equipe de Aplicativos LLM para alterá-lo ou desativar o Analytics. Verifique a ID do fluxo de dados antes de salvar. Você ainda pode alterar o conjunto de relatórios e o conjunto de dados na própria sequência de dados.

1. Abra o aplicativo e selecione **[!UICONTROL Configurações]** na navegação à esquerda.
2. Selecione a guia **[!UICONTROL Analytics]**.
3. Ative **[!UICONTROL Habilitar Analytics]**.
4. Em **[!UICONTROL ID da Sequência de Dados]**, cole a ID da [Etapa 5](#datastream).
5. Em **[!UICONTROL Requisitos]**, selecione **[!UICONTROL Exibir detalhes]** e confirme se seu aplicativo atende a cada requisito.

   ![Configurações do aplicativo, guia Analytics - Habilitar Analytics ativado, um campo de ID de sequência de dados vazio e a lista de Requisitos expandida](/help/assets/guide-analytics/analytics-settings.png)

6. Selecione **[!UICONTROL Salvar alterações]**.
7. [Implante seu aplicativo](/help/guides/deploy-your-app.md) para o Preparo. A configuração entra em vigor na próxima implantação em cada ambiente.

>[!NOTE]
>
>A sequência de dados se aplica a todo o aplicativo. O preparo e a produção enviam eventos para o mesmo fluxo de dados, e os eventos não registram qual ambiente os enviou. As chamadas de teste no Palco aparecem nos mesmos relatórios que o tráfego de Produção.

## Etapa 7: criar a conexão e a visualização de dados {#cja}

**Necessário para:** [!DNL Customer Journey Analytics].

### Criar a conexão {#connection}

Crie uma conexão e adicione o conjunto de dados da [Etapa 3](#dataset) com estas configurações:

| Configuração | Valor |
|---|---|
| Sandbox | A sandbox que contém seu conjunto de dados |
| Número médio de eventos diários | O intervalo que corresponde ao volume esperado |
| Conjunto de dados | O conjunto de dados da [Etapa 3](#dataset) |
| ID de pessoa | **[!UICONTROL Mapa de identidade]** |
| Usar namespace de identidade principal | Selecionado |
| Carimbo de data e hora | **[!UICONTROL Carimbo de data/hora]** |
| Tipo de fonte de dados | **[!UICONTROL Outros]** |
| Descrição da fonte de dados | Qualquer descrição, por exemplo `LLM Apps events` |
| Importar novos dados | **[!UICONTROL Importar todos os novos dados]** ativados |
| Preenchimento retroativo do conjunto de dados | **[!UICONTROL Preencher retroativamente todos os dados existentes]** ativado, se o conjunto de dados já contiver eventos que você deseja analisar |

>[!IMPORTANT]
>
>Ative **[!UICONTROL Importar todos os novos dados]**. Essa configuração cria uma conexão contínua, para que novos eventos fiquem disponíveis no Workspace automaticamente.

Com esta ID de pessoa, [!DNL Customer Journey Analytics] usa a identidade principal de cada evento como a pessoa e ignora eventos que não têm identidade principal. Consulte [Como os eventos identificam pessoas](#identities).

Para o procedimento, consulte [Criar uma conexão](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#create-connection), [Configurações do conjunto de dados de eventos](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings) e [Usar o Mapa de Identidade como uma ID de Pessoa](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#id-map).

### Criar a visualização de dados {#data-view}

Crie uma visualização de dados na conexão com estas configurações:

| Configuração | Valor |
|---|---|
| Conexão | A conexão que você acabou de criar |
| Nome | Qualquer nome, por exemplo `LLM Apps` |
| Componentes | As dimensões e métricas sobre as quais você deseja criar relatórios. Consulte a tabela abaixo. |

Adicione estes campos de esquema como componentes:

| Relatório sobre | Campo de esquema | Adicionar como |
|---|---|---|
| Ação | `agenticExperience.tools.name` | Dimensão |
| Chamadas de ação com falha | `agenticExperience.tools.failed` | Dimensão |
| Motivo da falha | `agenticExperience.tools.errorReason` | Dimensão |
| Platform | `agenticExperience.agents.name` | Dimensão |
| Tentativa do usuário | `conversation.prompt.raw.text` | Dimensão |
| Conversa | `conversation.conversationID` | Dimensão |
| Rótulo do widget | `web.webInteraction.name` | Dimensão |

Adicione campos de texto, como intenção do usuário, como dimensões. Um campo de texto adicionado como uma métrica só pode ser contado, e dividi-lo por seus valores mostra **Inválido**.

Para obter o procedimento, consulte [Configurar uma visualização de dados](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#configure) e [Componentes](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#components).

## Etapa 8: verificar os dados e implantar na produção {#verify}

**Necessário para:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

Chame uma ação no aplicativo Stage de uma plataforma LLM compatível e selecione um link em seu widget. Consulte [Testar no ChatGPT](/help/guides/test-in-chatgpt.md) ou [Testar no Claude](/help/guides/test-in-claude.md). Em seguida, verifique cada serviço que você configurou:

| Serviço | Onde verificar | O que esperar |
|---|---|---|
| [!DNL Experience Platform] | **[!UICONTROL Atividade do conjunto de dados]** para o conjunto de dados da [Etapa 3](#dataset) | Novos lotes assimilados e sem lotes com falha |
| [!DNL Adobe Analytics] | Um projeto do Workspace que usa o conjunto de relatórios da [Etapa 4](#report-suite), com uma tabela de Forma livre | Linhas de **[!UICONTROL Página]** chamadas `mcp:<code identifier>`, **[!UICONTROL Link Personalizado]** linhas para interações de widget e texto de intenção na dimensão **Intenção de Usuário** |
| [!DNL Customer Journey Analytics] | Um projeto do Workspace que usa a visualização de dados da [Etapa 7](#cja) | **[!UICONTROL Eventos]** para suas chamadas de ação e interações de widget |

Cada serviço processa os dados com algum atraso, de modo que novos eventos não aparecem imediatamente.

Para criar os relatórios, consulte [Criar projetos no Adobe Analytics](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects), [Visão geral da tabela de forma livre](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table) e [Criar projetos no Customer Journey Analytics](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects).

Quando os dados estiverem corretos, [implante seu aplicativo](/help/guides/deploy-your-app.md) para produção.

## Limitações {#limitations}

- **As interações do widget foram divididas em várias pessoas.** Cada chamada de ação recebe uma nova ECID, portanto, [!DNL Customer Journey Analytics] conta as interações do widget de chamadas de ação diferentes como pessoas diferentes. Consulte [Como os eventos identificam pessoas](#identities).
- **Nenhum link para seu site.** Quando alguém segue um link de widget para o seu site, a visita ao site não é vinculada à conversa.
- **Relatórios de compartilhamento de preparo e produção.** Ambos os ambientes enviam para o mesmo fluxo de dados e os eventos não registram qual ambiente os enviou.
- **Algumas plataformas não enviam identidade.** As chamadas de ação de plataformas que não fornecem uma ID de usuário, como [!DNL Claude], são ignoradas em [!DNL Customer Journey Analytics].
- **Um turno por chamada de ação.** Cada chamada de ação é registrada como sua própria vez de conversa, portanto, um prompt que aciona várias ações conta como várias vezes.

## Resolução de problemas {#troubleshooting}

| O que você vê | O que verificar |
|---|---|
| O conjunto de dados não recebe lotes | Você implantou o aplicativo depois de salvar as configurações do Analytics no ambiente que está testando. A ID da sequência de dados está correta. A sequência de dados tem o serviço **[!UICONTROL Adobe Experience Platform]** com seu conjunto de dados. |
| Lotes com falha | O esquema inclui todos os cinco grupos de campos da [Etapa 2](#schema). |
| [!DNL Adobe Analytics] não tem dados | A sequência de dados tem o serviço **[!UICONTROL Adobe Analytics]** habilitado com a ID de conjunto de relatórios correta. |
| A **Página** mostra `mcp:<code identifier>`, mas a **Intenção do Usuário** e os campos de ação estão vazios | Seu repositório de manipuladores usa o `@adobe/llm-apps-runtime` 1.2.0 ou posterior e você implantou após a atualização. Consulte [Antes de começar](#before-you-begin). |
| **Intenção de Usuário** está vazia para algumas ações | A regra de processamento da [Etapa 4](#processing-rule) usa as variáveis de dados de contexto exatamente como mostradas, em minúsculas e na eVar correta. A ação tem a opção **[!UICONTROL Coletar intenção de usuário]** ativada. A regra se aplica somente aos dados coletados após você salvá-los. |
| [!DNL Customer Journey Analytics] mostra menos eventos do que o conjunto de dados | **[!UICONTROL Importar todos os novos dados]** está ativado. Eventos sem uma identidade primária são ignorados, por exemplo, chamadas de ação de plataformas que não fornecem uma ID de usuário. |
| Não é possível alterar a ID do fluxo de dados ou desativar o Analytics | Essas configurações são somente leitura nas Configurações do aplicativo após a ativação. Entre em contato com a equipe de Aplicativos LLM para obter ajuda. Para rotear dados para outro lugar, altere o conjunto de relatórios ou o conjunto de dados no próprio fluxo de dados. |

## O que vem a seguir {#whats-next}

- [Configurar análises para uma ação](/help/guides/create-action.md#configure-analytics)
- [Implante seu aplicativo](/help/guides/deploy-your-app.md)
