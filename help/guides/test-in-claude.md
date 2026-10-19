---
title: Testar seu aplicativo LLM como um conector Claude
description: Crie um conector Claude a partir do URL do servidor MCP do Adobe LLM Apps e teste-o em uma conversa.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%
---

# Testar seu aplicativo LLM como um conector [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Após a implantação, o aplicativo LLM expõe um URL de servidor MCP. Adicione esta URL a [!DNL Claude] como um conector personalizado e teste as ações e widgets gerados.

**Neste guia:** Saiba como adicionar seu aplicativo implantado como um conector [!DNL Claude], escolher seu comportamento de entrada e testar suas ações e widgets em uma conversa.

Para um aplicativo com ações públicas e protegidas, o [!DNL Claude] oferece suporte à entrada adiada até chamar uma ação protegida. Um aplicativo cujas ações exigem autenticação precisa de logon antes de ser usado. Consulte [Autenticar usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md).

## Requisitos do plano

Conectores personalizados usando MCP remoto estão disponíveis para planos Gratuitos, Pro, Max, Team e Enterprise. Contas gratuitas oferecem suporte a um conector personalizado. No Team e Enterprise, um proprietário autorizado adiciona o conector para a organização antes que os membros se conectem a ela. As funções personalizadas do Enterprise também podem conceder essa permissão.

Consulte as [instruções do conector personalizado atual do Claude](https://claude.com/docs/connectors/custom/add-unlisted) para obter os controles do plano e da organização. O terminal MCP remoto deve ser acessível a partir da infraestrutura do Claude, não apenas a partir do seu navegador local.

## Copie o URL do servidor MCP

Em [!DNL LLM Apps]:

1. Abra a página Detalhes do aplicativo.
2. Localizar **[!UICONTROL Testar o aplicativo]**.
3. Em **[!UICONTROL Ambiente de preparo]**, selecione **[!UICONTROL Copiar URL]**.

## Adicionar o conector personalizado

1. Abra [Personalizar > Conectores](https://claude.ai/customize/connectors). Para um conector para toda a organização, um proprietário autorizado abre [Configurações da organização > Conectores](https://claude.ai/admin-settings/connectors).
2. Selecione **[!UICONTROL Adicionar conector personalizado]** ou **[!UICONTROL Adicionar]** > **[!UICONTROL Personalizado]** > **[!UICONTROL Web]** para o fluxo da organização.
3. Insira o nome do conector, se solicitado, e cole o URL completo do servidor HTTPS MCP.
4. Se a caixa de diálogo tiver várias etapas, selecione **[!UICONTROL Continuar]** e verifique as configurações de autenticação detectadas:
   - **[!UICONTROL Nenhuma entrada]** para um aplicativo público com autenticação desabilitada.
   - **[!UICONTROL Entre quando necessário]** para um aplicativo com ações públicas e protegidas.
   - **[!UICONTROL Entre agora]** para solicitar entrada antes de usar o conector.
5. Para OAuth, escolha um método de identidade do cliente compatível com seu provedor de identidade. Se você usar um cliente pré-registrado, insira seus detalhes somente nas configurações OAuth do conector, não nos metadados de URL ou de ação.
6. Selecione **[!UICONTROL Adicionar]**. Para um conector de organização, cada membro seleciona **[!UICONTROL Conectar]** em sua própria lista de conectores.

Algumas contas mostram uma caixa de diálogo de tela única com **[!UICONTROL Configurações avançadas]**, enquanto outras mostram um fluxo de várias etapas. Use as [instruções da plataforma atual](https://claude.com/docs/connectors/custom/add-unlisted#choose-authentication-settings) se o seu layout for diferente.

>[!NOTE]
>
>Use apenas conectores de desenvolvedores confiáveis. A Anthropic não controla quais ferramentas os desenvolvedores disponibilizam ou garantem que essas ferramentas funcionem conforme o esperado ou permaneçam inalteradas.

## Permitir as ferramentas geradas

[!DNL Claude] lista cada ação gerada em **[!UICONTROL Permissões de ferramenta]** na página do conector. Novas ferramentas são padronizadas como **[!UICONTROL Precisa de aprovação]**, solicitando a aprovação de cada chamada durante o teste.

Mantenha os prompts de aprovação ativados para ações que alteram dados ou executam operações confidenciais. Use **[!UICONTROL Sempre permitir]** somente para ações específicas confiáveis e de baixo risco quando apropriado para seu teste.

## Teste o conector

1. Inicie um novo chat.
2. Selecione **+** na caixa de mensagem (ou digite `/`), passe o mouse sobre **[!UICONTROL Conectores]** e ative o conector adicionado para esta conversa.
3. Faça uma pergunta que corresponda a uma das ações geradas. Por exemplo: *Mostre-me um pouco de café.*

Verifique se:

- [!DNL Claude] invoca a ação esperada.
- O widget exibe os dados de amostra esperados.
- A resposta de texto corresponde ao widget.
- Os controles de widget funcionam conforme esperado.

Para autenticação mista, chame primeiro uma ação pública sem fazer logon. Em seguida, chame uma ação protegida, conclua o prompt de logon e confirme se a mesma solicitação é bem-sucedida posteriormente. Teste permissões ausentes e entrada inválida também.

Depois de alterar o aplicativo, reimplante e repita os testes afetados. Se você precisar alterar as configurações de autenticação salva de um conector, [!DNL Claude] pode exigir que você remova e adicione o conector novamente.

## O que vem a seguir

- [Personalizar os widgets gerados](/help/guides/widgets.md).
- [Criar uma ação do zero](/help/guides/create-action.md).
