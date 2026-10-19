---
title: Testar seu aplicativo LLM como um plug-in ChatGPT
description: Crie um plug-in ChatGPT a partir do URL do servidor MCP dos aplicativos Adobe LLM e teste-o em uma conversa.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '546'
ht-degree: 0%
---

# Testar seu aplicativo LLM como um plug-in [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Após a implantação, o aplicativo LLM expõe um URL de servidor MCP. Adicione esta URL a [!DNL ChatGPT] como um plug-in e teste as ações e widgets gerados.

**Neste guia:** Saiba como conectar seu aplicativo implantado ao [!DNL ChatGPT] e testar suas ações e widgets em uma conversa. Atualize a conexão após alterar o aplicativo.

## Requisitos do plano

Use uma conta e um espaço de trabalho que permitam adicionar servidores MCP personalizados. A disponibilidade e os controles do administrador variam de acordo com a conta e o espaço de trabalho. Consulte a documentação atual do [Connect e teste seu plug-in](https://developers.openai.com/plugins/deploy/connect-chatgpt) do OpenAI.

## Verificar o acesso a servidores MCP personalizados

Abra os [ChatGPT Plugins](https://chatgpt.com/plugins) e verifique se sua conta permite adicionar um servidor MCP personalizado. Se sua conta exigir o modo de desenvolvedor, ative esse modo usando os controles da conta. Se a opção de servidor MCP personalizado estiver ausente ou bloqueada, pergunte ao administrador do espaço de trabalho.

## Copie o URL do servidor MCP

Em [!DNL LLM Apps]:

1. Abra a página Detalhes do aplicativo.
2. Localizar **[!UICONTROL Testar o aplicativo]**.
3. Em **[!UICONTROL Ambiente de preparo]**, selecione **[!UICONTROL Copiar URL]**.

## Criar o plug-in

1. Abra [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. Selecione **+**, depois **[!UICONTROL Adicionar servidor MCP personalizado]**.
3. Configure a conexão:
   - **[!UICONTROL Nome]** — o nome do plug-in.
   - **[!UICONTROL Descrição]** — opcional.
   - Escolha o método de conexão de servidor público e insira o URL completo do servidor HTTPS do seu aplicativo.
   - Escolha o método de autenticação que corresponde ao seu aplicativo. Para um aplicativo público com autenticação desabilitada, use **[!UICONTROL Sem Autenticação]** quando oferecido.

   >[!NOTE]
   >
   >Se você ativou a autenticação do usuário final, selecione **[!UICONTROL OAuth]** quando cada ação estiver definida como **[!UICONTROL Obrigatória]** ou **[!UICONTROL Mista]** para uma combinação de ações públicas e protegidas, em que essas opções são oferecidas. Consulte [Autenticar usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md).

4. Selecione **[!UICONTROL Entendo e desejo continuar]**.
5. Selecione **[!UICONTROL Criar como um plug-in]**.
6. Revise as ferramentas e os metadados descobertos, instale ou conecte o plug-in resultante. Conclua o logon se seu aplicativo exigir.

O OpenAI é proprietário da interface do usuário de configuração e seu layout pode variar de acordo com a conta. Siga as [instruções de conexão](https://developers.openai.com/plugins/deploy/connect-chatgpt) atuais se os rótulos exibidos forem diferentes.

## Testar o plug-in

1. Inicie um novo chat.
2. Digite `@` na caixa de prompt e selecione o plug-in instalado.
3. Faça uma pergunta que corresponda a uma das ações geradas. Por exemplo: *Mostre-me um pouco de café.*

![Resposta Ilustrativa do ChatGPT — uma ação do Frescopa retorna cartões de produto na conversa](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Verifique se:

- [!DNL ChatGPT] invoca a ação esperada.
- O widget exibe os dados de amostra esperados.
- A resposta de texto corresponde ao widget.
- Os controles de widget funcionam conforme esperado.

Teste também entradas inválidas, resultados vazios, prompts não relacionados que não chamam o aplicativo e ações que alteram dados. Revise cada prompt de confirmação e autenticação em vez de aprovar todas as solicitações antecipadamente.

## Atualizar após alterações no aplicativo

Depois de alterar as descrições de ação, entradas, autenticação ou configuração de widget:

1. Implante o aplicativo atualizado no mesmo ambiente.
2. Abra sua conexão em [ChatGPT Plugins](https://chatgpt.com/plugins) e selecione **[!UICONTROL Atualizar]**.
3. Verifique se as ações anunciadas foram alteradas.
4. Inicie uma nova conversa e repita os testes afetados.

## O que vem a seguir

- [Personalizar os widgets gerados](/help/guides/widgets.md).
- [Criar uma ação do zero](/help/guides/create-action.md).
