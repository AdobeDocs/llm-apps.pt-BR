---
title: Testar no ChatGPT
description: Saiba como adicionar seu aplicativo Adobe LLM implantado ao ChatGPT e testá-lo em uma conversa real.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '804'
ht-degree: 2%

---


# Teste em [!DNL ChatGPT]

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

>[!NOTE]
>
>Este guia usa [!DNL ChatGPT] como exemplo. As etapas gerais — registro de um URL de servidor MCP e teste em uma conversa — também se aplicam a outras plataformas LLM, embora o fluxo de configuração e a interface variem.

Após uma implantação bem-sucedida com o [!DNL Adobe LLM Apps], seu aplicativo está sendo executado no [!DNL Adobe I/O Runtime] e expõe uma URL de servidor MCP. Este guia mostra como adicioná-lo a [!DNL ChatGPT] e testá-lo em uma conversa real.

## Requisitos do plano

A adição de aplicativos de desenvolvedor personalizados a [!DNL ChatGPT] é regida pelos níveis de assinatura da OpenAI. Essa não é uma limitação de [!DNL LLM Apps], mas a forma como a OpenAI atualmente gerencia o acesso a aplicativos MCP personalizados.

| Plano [!DNL ChatGPT] | Aplicativos MCP personalizados |
|--------------|-----------------|
| Gratuito | Não disponível |
| Ir | Não disponível |
| Mais | Não disponível |
| Pro | Disponível |
| Negócios | Disponível |
| Enterprise / Edu | Disponível |

>[!NOTE]
>
>Se você estiver em um plano Gratuito, Ir ou Mais, você **não poderá adicionar seu aplicativo implantado** a [!DNL ChatGPT]. Atualize para o **Pro** ou peça ao administrador da sua organização para habilitá-lo em um espaço de trabalho do **Business** ou do **Enterprise**.

## Ativar modo de desenvolvedor

Para adicionar um aplicativo MCP personalizado, você deve ter o **modo de desenvolvedor** habilitado em sua conta do [!DNL ChatGPT]. Seguir
siga as etapas abaixo para verificá-la e ativá-la.

### Abrir configurações

Clique no avatar do seu perfil no canto inferior esquerdo e em **[!UICONTROL Configurações]**.

![ChatGPT — menu Configurações](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### Navegar até Aplicativos

Na caixa de diálogo Configurações, selecione **[!UICONTROL Aplicativos]** na barra lateral esquerda. Clique em **[!UICONTROL Configurações avançadas]** na parte inferior.

![ChatGPT — Configurações de aplicativos](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### Ativar modo de desenvolvedor

Verifique se a opção de alternância **[!UICONTROL Modo de desenvolvedor]** está ativada (azul). Isso permite registrar URLs de servidor MCP personalizados e não verificados.

>[!NOTE]
>
>O modo de desenvolvedor está rotulado como *Risco elevado* porque permite aplicativos que não foram revisados pelo OpenAI. O [!DNL ChatGPT] desabilita automaticamente a Memória para conversas que usam aplicativos no modo de desenvolvedor.

![ChatGPT — Modo de desenvolvedor habilitado](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## Adicionar seu aplicativo a [!DNL ChatGPT]

### Copie o URL do servidor MCP

Vá para a página **Detalhes do Aplicativo** em [!DNL LLM Apps] e localize a seção **[!UICONTROL Testar o aplicativo]**. Copie a URL de **Preparo** ou de **Produção** — ela se parece com:

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### Abra a página Aplicativos

Em [!DNL ChatGPT], vá para **[!UICONTROL Configurações] → [!UICONTROL Aplicativos]**.

![ChatGPT — página Aplicativos](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### Criar um novo aplicativo

Clique em **[!UICONTROL Criar aplicativo]** na linha Configurações avançadas.

![ChatGPT — Caixa de diálogo Criar aplicativo](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

Preencha o seguinte:

| Texto | Valor |
|-------|-------|
| **Ícone** | Opcional — fazer upload de um arquivo PNG 128x128 (máx. 10 KB) |
| **Nome** | Um nome de exibição para o seu aplicativo (por exemplo, *Meu aplicativo da marca*) |
| **Descrição** | Uma breve descrição do que o aplicativo faz |
| **URL do Servidor MCP** | Colar a URL de [!DNL LLM Apps] |
| **[!UICONTROL Autenticação]** | Selecionar *Sem Autenticação* |

Marque a caixa de seleção **Entendo e desejo continuar**, que reconhece que o servidor MCP
não foi revisado pelo OpenAI — e clique em **Criar**.

### Verifique se o aplicativo está ativado

Após a criação, seu aplicativo aparece em **[!UICONTROL Aplicativos habilitados]** com uma medalha **[!UICONTROL DEV]**, confirmando que está ativo.

>[!NOTE]
>
>Seu aplicativo também aparece em **Rascunhos** — estes são aplicativos privados que você criou no modo de desenvolvedor que só são visíveis para sua conta.

Seu aplicativo está pronto para uso em [!DNL ChatGPT] conversas.

![ChatGPT — aplicativo habilitado](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## Testar em uma conversa

Após habilitar o aplicativo, inicie uma nova conversa em [!DNL ChatGPT]. Antes de fazer uma pergunta, anexe seu aplicativo usando um destes dois métodos.

### Opção 1 — Selecionar no menu

Clique no botão **+** na entrada do chat e em **Mais** para expandir a lista completa de ferramentas disponíveis. Selecione seu aplicativo na lista para anexá-lo à conversa atual.

![ChatGPT — selecione o aplicativo no menu](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### Opção 2 — Utilizar @mention

Digite **@** na entrada de chat e selecione seu aplicativo na lista suspensa. Isso anexa o aplicativo em linha e você pode continuar digitando sua pergunta na mesma mensagem.

>[!NOTE]
>
>Usar **@mention** uma segunda vez no mesmo aplicativo irá desmarcá-lo e removê-lo da conversa.

![ChatGPT — @mention o aplicativo](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

Depois de selecionado, o aplicativo é anexado em linha e você pode digitar sua pergunta na mesma mensagem:

![ChatGPT — aplicativo anexado via @mention](/help/assets/guide-test-chatgpt/chatgpt-mention.png)

### Ver o resultado

Depois que o aplicativo for anexado, digite uma pergunta alinhada a uma de suas ações configuradas — por exemplo, *&quot;Mostre-me seus produtos.&quot;* [!DNL ChatGPT] corresponde à ação relevante, extrai os parâmetros de entrada, chama seu manipulador em [!DNL Adobe I/O Runtime] e renderiza o resultado:

![ChatGPT — resultado da ação](/help/assets/guide-test-chatgpt/chatgpt-response.png)

A resposta inclui:

- **Widget do EDS** — um componente avançado da interface do usuário com imagens, classificações e botões de ação.
- **A resposta de texto** — abaixo do widget, [!DNL ChatGPT] usa o `content` retornado pelo seu manipulador
para formular um resumo dos resultados em linguagem natural.
- **Indicador de status** — o *texto de status chamado* que você configurou na caixa de diálogo Criar Ação.

## O que vem a seguir

- **Adicionar mais ações** — defina ações adicionais na interface, grave seus manipuladores e reimplante.
- **Implantar para produção** — se você testou no Preparo, implante para produção para obter a experiência ativa.
- **Compartilhar com sua equipe** — use **Copiar URL** na página Detalhes do Aplicativo para compartilhar a URL do servidor MCP com colegas de equipe.

