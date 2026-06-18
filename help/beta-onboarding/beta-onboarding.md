---
title: Integração do Beta para aplicativos Adobe LLM
description: Introdução aos aplicativos Adobe LLM como participante do programa Beta.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '1551'
ht-degree: 0%

---


# Integração do Beta {#beta-onboarding}

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

>[!NOTE]
>
>Antes de começar, verifique se todos os [pré-requisitos](/help/beta-onboarding/prerequisites.md) foram atendidos.

Como participante do programa Beta, você receberá um email com dois arquivos zip e uma referência de configuração de aplicativo. Siga as etapas abaixo para colocar seu aplicativo em funcionamento.

## Antes de começar

Antes de mergulhar nas etapas, familiarize-se com os principais conceitos usados neste guia. Isso economizará tempo e ajudará tudo a se encaixar.

**Aplicativo LLM** — o seu assistente de marca com o qual os usuários interagem dentro do [!DNL ChatGPT] ou de outras plataformas LLM.

**Ação** — um recurso que seu aplicativo oferece. Por exemplo, &quot;Encontre um distribuidor&quot; ou &quot;Procurar produtos&quot;. Cada ação é invocada pelo LLM quando o usuário faz uma pergunta relevante.

**Manipulador de ação** — o código que é executado quando uma ação é invocada. Ele pode chamar suas APIs, buscar dados em tempo real ou retornar dados estáticos. Os manipuladores de amostra fornecidos pelo Adobe retornam dados codificados para que você possa verificar a configuração de ponta a ponta antes de conectar seu back-end real.

**Widget** — a resposta visual mostrada ao usuário — um cartão, carrossel, tabela ou qualquer interface do usuário personalizada renderizada junto com a resposta de texto do LLM.

**Referência da configuração do aplicativo** — um arquivo fornecido pela Adobe que informa exatamente o que inserir para cada ação ao configurar seu aplicativo.


## Etapa 1: Encaminhar os arquivos fornecidos para [!DNL GitHub]

O Adobe fornece dois arquivos zip por email:

- **Código do aplicativo** (`<project-name>.zip`) — os manipuladores de ação que são executados em [!DNL Adobe I/O Runtime] e potencializam a lógica do aplicativo. Você implantará esses no estado em que se encontram para que o aplicativo funcione de ponta a ponta e, em seguida, os atualizará posteriormente para conectar seu back-end real.
- **Projeto EDS** (`<project-name>-eds.zip`) — o código de front-end dos seus widgets. A Adobe pré-criou esses itens para você; essa é a sua base de código para possuir, personalizar e criar estilos para corresponder à sua marca.

Crie **dois novos repositórios vazios** em [!DNL GitHub] (um por arquivo), descompacte cada arquivo e envie-o por push. Recomendamos nomear cada repositório após o arquivo zip correspondente — `<project-name>` para o código do aplicativo e `<project-name>-eds` para o projeto EDS.

`<your-github-org>` refere-se ao seu nome de usuário pessoal [!DNL GitHub] ou a uma organização [!DNL GitHub] — seja qual for a conta que terá os repositórios.

**Repositório de código do aplicativo** — descompacte o arquivo, inicialize um repositório Git local e envie-o por push para [!DNL GitHub]:

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**Repositório EDS** — repita as mesmas etapas para o arquivo EDS, apontando para o segundo repositório:

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## Etapa 2: criar um aplicativo LLM

Navegue até [experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/) e clique em **[!UICONTROL Criar Aplicativo LLM]**.

![Página de aplicativos — nenhum aplicativo criado ainda](/help/assets/guide-create-app/first-load.png)

Preencha os **[!UICONTROL Detalhes do aplicativo]** usando os valores da seção **[!UICONTROL Detalhes do aplicativo]** na referência de configuração do aplicativo:

- **[!UICONTROL Nome do Aplicativo LLM]**
- **[!UICONTROL Descrição do aplicativo LLM]**
- **[!UICONTROL Seu site]**

![Caixa de diálogo Criar Aplicativo](/help/assets/guide-create-app/app-details-1.png)

Em **[!UICONTROL Região de dados do Analytics]**, selecione a região onde os dados do Analytics serão armazenados. Este **não pode ser alterado** após a criação do aplicativo.

>[!IMPORTANT]
>
>A região de dados de análise não pode ser alterada após a criação do aplicativo.

![Lista suspensa da região de dados do Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

Em **Repositório**, selecione a organização [!DNL GitHub] e o **repositório de código do aplicativo** enviados anteriormente.

>[!NOTE]
>
>Se esta for a primeira vez que você configura um aplicativo, a organização [!DNL GitHub] ainda não aparecerá na lista. Clique em **[!UICONTROL Conectar outra organização do GitHub]** para vincular sua organização e conceder acesso ao repositório.

![Caixa de diálogo Criar Aplicativo — repositório vinculado](/help/assets/guide-create-app/app-details-repo-linked.png)

Deixe **[!UICONTROL Sugerir ações automaticamente com base no seu site]** desmarcado — você irá configurar ações manualmente.

Aceite os **[!UICONTROL Termos do Adobe Developer]** e clique em **[!UICONTROL Criar aplicativo]**.

![Criando aplicativo — carregando tela](/help/assets/guide-create-app/app-loading.png)

![Página de detalhes do aplicativo](/help/assets/guide-create-app/app-detail-top.png)


## Etapa 3: colocar seus widgets em funcionamento

Nesta etapa, você configura o projeto EDS fornecido pela Adobe e o publica por meio de [!DNL DA.live] — criação da Adobe e camada de CDN. Cada documento publicado se torna o widget exibido para o usuário quando uma ação é chamada.

### Etapa 3.1: Conectar o repositório EDS a [!DNL DA.live]

1. Ir para [github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync). Se o aplicativo ainda não estiver instalado, clique em **[!UICONTROL Instalar]**. Se já estiver instalado, clique em **[!UICONTROL Configurar]** e adicione `<your-eds-repo>` à lista de repositórios que ele pode acessar.
2. Após a instalação, você acessa uma página de confirmação **[!DNL AEM Code Sync]registrada**. Em **O que vem a seguir → Crie seu conteúdo**, clique no link [!DNL DA.live].
3. Na tela **Conteúdo de demonstração**, selecione **Nenhum** e clique em **Tornar algo maravilhoso**.
4. Você é levado ao modo de exibição de Autor [!DNL DA.live] do seu site.

### Etapa 3.2: Criar um documento [!DNL DA.live] para cada ação

Em [!DNL DA.live] você precisa criar **um documento por ação**. Depois de publicado, cada documento se torna o widget mostrado ao usuário quando essa ação é chamada.

Para cada ação:

1. No [!DNL DA.live], crie um novo documento na raiz do site e nomeie-o como especificado na referência de configuração do aplicativo (consulte a seção **[!DNL DA.live]documentos**).
2. No documento, use a barra lateral esquerda e clique em **[!UICONTROL Bloquear]** para inserir um novo bloco.
3. Defina o cabeçalho do bloco com o nome do bloco especificado na referência de configuração do aplicativo (consulte a seção **[!DNL DA.live]documentos**).
4. Publique o documento usando o botão **[!UICONTROL Publicar]** (o ícone de plano de papel na barra de ferramentas superior).

Após a publicação, cada documento estará acessível em `https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`. Esta URL é o que você digitará no campo **[!UICONTROL URL do Widget]** ao configurar cada ação na Etapa 4.


### Etapa 3.3: Configurar cabeçalhos CORS para o site EDS

Para permitir que as plataformas LLM carreguem seus widgets entre origens, é necessário adicionar um cabeçalho `Access-Control-Allow-Origin` ao site EDS.

Vá para o **Editor de HTTP Headers** em [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html).

1. Insira sua **Organização** (`<your-github-org>`) e o **Site** (seu nome de repositório EDS) e clique em **[!UICONTROL Buscar]**. Você receberá uma solicitação para autenticar e autorizar o acesso ao seu site.
2. No caminho `/**`, clique em **[!UICONTROL Adicionar cabeçalho]**.
3. Defina o nome do cabeçalho como `Access-Control-Allow-Origin` e o valor como `*`.
4. Clique em **[!UICONTROL Salvar]**.

Para obter a documentação completa sobre cabeçalhos HTTP personalizados em [!DNL AEM Edge Delivery Services], consulte [aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers).

Depois de salvar os cabeçalhos, acione uma sincronização de código para propagar as alterações para todos os arquivos:

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## Etapa 4: adicionar ações

Na [Interface de Aplicativos do LLM](https://experience.adobe.com/llm-apps/), abra o aplicativo e navegue até **[!UICONTROL Ações]** na barra lateral esquerda. Clique em **+** para criar uma nova ação. Repita para cada ação descrita na sua referência de configuração de aplicativo (consulte as seções **Ação 1**, **Ação 2**, **Ação 3**).

![Página Ações — nenhuma ação ainda](/help/assets/guide-create-action/actions-empty.png)

### Guia Ação

- **Nome da ação** e **Descrição** — usado pelas plataformas LLM para decidir quando invocar a ação. Use os valores exatos da seção **guia Ação** na referência de configuração do aplicativo.
- **Parâmetros de entrada** — nome, tipo e descrição de cada parâmetro. Use os valores da seção **Guia Ação** na referência de configuração do aplicativo.

![Criar Ação — informações básicas](/help/assets/guide-create-action/action-basic-info.png)

### Guia Metadados do widget

- **Tipo** — selecione **[!UICONTROL EDS]**.

Expanda **[!UICONTROL Configuração da CSP]** e preencha:

- **[!UICONTROL CSP — Conectar domínios]** — use os valores da seção **guia Metadados do Widget** na referência de configuração do aplicativo.
- **[!UICONTROL CSP — Domínios de recursos]** — use os valores da seção **guia Metadados do Widget** na referência de configuração do aplicativo.

![Criar Ação — metadados do widget](/help/assets/guide-create-action/widget-metadata.png)

![Criar Ação — permissões e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### Guia Construtor de widgets

Em **[!UICONTROL Origem do widget]**, selecione **[!UICONTROL Usar um widget existente]** e preencha com:

- **[!UICONTROL URL do Script]** — use o valor da seção **Guia de Metadados do Widget** na referência de configuração do aplicativo.
- **[!UICONTROL URL do Widget]** — use o valor da seção **Guia de Metadados do Widget** na referência de configuração do aplicativo.

Clique em **[!UICONTROL Criar ação]**. A ação aparece como um cartão na página Ações com uma medalha **[!UICONTROL EDS]** e contagem de parâmetros.

![Página Ações — ação criada](/help/assets/guide-create-action/actions-with-action.png)


## Etapa 5: implantar

Depois que todas as ações forem configuradas, vá para a página Detalhes do aplicativo e clique em **[!UICONTROL Implantar]** no canto superior direito.

![Detalhes do aplicativo — pronto para implantar](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Selecione o ambiente de destino e clique em **[!UICONTROL Implantar]**. O pipeline é executado em quatro etapas: preparação de credenciais, início da implantação, compilação do aplicativo a partir do repositório e publicação no [!DNL Adobe I/O Runtime].

![Implantar pipeline em execução](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

Quando terminar, role até a seção **[!UICONTROL Testar o aplicativo]** na página Detalhes do Aplicativo e copie a **[!UICONTROL URL do servidor MCP]** — será necessário registrá-lo no [!DNL ChatGPT].

![Implantação bem-sucedida](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![Testar o aplicativo — URLs implantadas](/help/assets/guide-deploy/test-app-deployed.png)


## Etapa 6: adicionar o aplicativo a [!DNL ChatGPT]

Adicionar aplicativos personalizados ao [!DNL ChatGPT] requer uma assinatura do **Pro**, **Business** ou **Enterprise**. Os planos Gratuito e Plus não são compatíveis com aplicativos MCP personalizados.

1. Em [!DNL ChatGPT], clique no avatar do seu perfil e vá para **[!UICONTROL Configurações]**.

   ![ChatGPT — menu Configurações](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. Selecione **[!UICONTROL Aplicativos]** na barra lateral, clique em **[!UICONTROL Configurações avançadas]** e habilite o **[!UICONTROL Modo de desenvolvedor]**.

   ![ChatGPT — Modo de desenvolvedor habilitado](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. Vá para **[!UICONTROL Configurações] → [!UICONTROL Aplicativos]** e clique em **[!UICONTROL Criar aplicativo]**.

   ![ChatGPT — Caixa de diálogo Criar aplicativo](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. Cole a **[!UICONTROL URL do servidor MCP]** copiada de [!DNL LLM Apps], defina a **[!UICONTROL Autenticação]** como *Sem Autenticação*, marque a caixa de seleção de confirmação e clique em **Criar**.

Seu aplicativo aparece em **[!UICONTROL Aplicativos habilitados]** com um selo **[!UICONTROL DEV]**.

![ChatGPT — aplicativo habilitado](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

Inicie uma nova conversa, anexe seu aplicativo usando o botão **+** ou digitando **@** seguido do nome do aplicativo e faça uma pergunta que corresponda a uma de suas ações configuradas.

![ChatGPT — selecione o aplicativo no menu](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — resultado da ação](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## O que vem a seguir

O aplicativo de amostra implantado usa dados codificados. Para torná-la uma experiência pronta para produção:

- **Conectar suas APIs** — Atualize os manipuladores de ação no repositório de código do aplicativo para chamar suas APIs, bancos de dados ou serviços reais. Cada manipulador reside em `actions/<action-name>/index.js`.
- **Revise e refine seus widgets** — Abra o projeto EDS, ajuste os estilos de bloco e o layout para corresponder à sua marca e verifique se o widget é renderizado corretamente com os dados dinâmicos.
- **Reimplantar** — Depois que seus manipuladores e widgets forem atualizados, envie suas alterações por push para [!DNL GitHub] e clique em **[!UICONTROL Implantar]** na interface do usuário do [!DNL LLM Apps] para publicar a nova versão.
- **Enviar para publicação** — Quando estiver satisfeito com a experiência, envie seu aplicativo para revisão por meio do plug-in [!DNL ChatGPT] ou do processo de publicação do conector. A Adobe não controla esse processo — consulte a documentação da plataforma LLM para conhecer os requisitos e os cronogramas de envio.
