---
title: Criar uma ação
description: Saiba como definir uma ação na interface de aplicativos do LLM, incluindo metadados, parâmetros de entrada e configuração de widget.
source-git-commit: ae2748319b5401555c3a616971f5697c17e74ac3
workflow-type: tm+mt
source-wordcount: '900'
ht-degree: 1%

---


# Criar uma ação

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Este guia aborda a definição de uma ação na interface do usuário do [!DNL LLM Apps]. Para obter informações sobre quais ações são executadas e como elas funcionam, consulte [Conceitos principais](/help/overview/overview.md#actions).

## Abra a página Ações

Navegue até **[!UICONTROL Ações]** na barra lateral esquerda ou clique em **Ir para Ações** na página Detalhes do Aplicativo. Se nenhuma ação ainda não existir, a página mostrará um estado vazio.

![Página Ações — nenhuma ação ainda](/help/assets/guide-create-action/actions-empty.png)

Clique em **+ Criar Ação** para abrir a caixa de diálogo de tela cheia.

## Cartões de ação

Cada ação é exibida como um cartão que mostra:

- O **nome** e a **descrição** da ação
- Uma **imagem de visualização do widget** — gerada automaticamente a partir do widget, mostrando como a saída da ação se parece dentro da plataforma LLM
- **Medalhas**: tipo de widget (**[!UICONTROL EDS]**), status da implantação (**Não implantada**, **Implantada no preparo**, **Implantada na produção**), **Alterações não implantadas** quando a ação foi modificada desde a última implantação e contagem de parâmetros
- Uma opção de **Visibilidade** — habilita ou desabilita a ação no ponto de extremidade ativo sem reimplantar
- Um link **Revisão** no canto superior direito para abrir o editor de ações

![Página Ações — cartões de ação](/help/assets/guide-create-action/action-card.png)

Quando uma ou mais ações foram modificadas desde a última implantação, um banner **Implantação necessária** é exibido na parte superior da página Ações. Reimplante o aplicativo para aplicar as alterações.

## Guia Ação

A caixa de diálogo tem duas guias: **Ação** e **[!UICONTROL Metadados do widget]**.

### Informações básicas

![Criar Ação — informações básicas](/help/assets/guide-create-action/action-basic-info.png)

- **Nome da ação** (obrigatório) — o identificador da sua ação (por exemplo, *Pesquisar Produtos*).
- **Descrição** (obrigatório) — uma explicação clara do que a ação faz. A plataforma LLM usa isso para decidir quando invocar sua ação. Por exemplo: *Pesquise o catálogo de produtos por palavra-chave. Retorna produtos correspondentes com nome, categoria, imagem e preço.*
- **Anotações** — dicas opcionais que descrevem o comportamento da ação:

  | Anotação | Descrição |
  |-----------|-------------|
  | **Dica destrutiva** | A ação modifica ou exclui dados |
  | **Idempotente** | Chamar a ação várias vezes com os mesmos argumentos produz o mesmo resultado |
  | **Abrir dica do mundo** | A ação interage com sistemas externos |
  | **Dica somente leitura** | A ação lê apenas dados, nunca grava |

  Consulte [Referência: Campos de Metadados](/help/reference/reference-docs.md) para obter detalhes.

### Metadados do OpenAI

- **Texto de status de chamada** — a mensagem mostrada na plataforma LLM enquanto a ação é executada (máximo de 64 caracteres). Exemplo: *Carregando produtos...*
- **Texto de status chamado** — a mensagem mostrada após a ação ser concluída (máximo de 64 caracteres). Exemplo: *Produtos carregados.*

### Visibilidade e parâmetros de entrada

**Visibilidade** controla onde a ação está disponível:

- **Expor ao modelo de IA** — a ação pode ser invocada pelo modelo de IA.
- **Mostrar como widget na superfície do aplicativo** — a ação renderiza um widget visual.

**Parâmetros de entrada** são os valores que a plataforma LLM envia para o seu manipulador. O modelo os extrai automaticamente da mensagem do usuário. Para *Pesquisar Produtos*, definimos:

- **categoria** (Cadeia de caracteres, opcional) — filtro de categoria para restringir resultados (por exemplo, um tipo de produto ou departamento).
- **consulta** (Cadeia de caracteres, opcional) — termo de pesquisa de texto livre.

Cada parâmetro tem uma caixa de seleção **Nome**, **Tipo** (Cadeia de caracteres, Número, Inteiro, Booleano), **Descrição** e **Obrigatório**. Clique em **+ Adicionar** para adicionar mais parâmetros.

Para obter mais detalhes, consulte [Referência: Parâmetros de ação](/help/reference/reference-docs.md).

### Analytics

![Criar ação — intenção de usuário do analytics](/help/assets/guide-create-action/action-analytics-user-intent.png)

- **Tentativa de usuário** — quando habilitada, [!DNL ChatGPT] é solicitado a resumir a conversa que levou a chamar esta ação. Esse resumo é coletado e exibido na análise, fornecendo insight sobre o que os usuários estavam tentando realizar quando a ação foi acionada.

## Guia Metadados do widget

Esta guia configura como a resposta visual da ação é renderizada na plataforma LLM. Para obter uma explicação completa de como os widgets funcionam, consulte [Guia: Configurar o Widget (EDS)](/help/guides/widgets.md).

![Criar Ação — metadados do widget](/help/assets/guide-create-action/widget-metadata.png)

### Informações do widget

- **Tipo** — a tecnologia de widget (atualmente **[!UICONTROL EDS]**).
- **Domínio do widget (origem da sandbox)** — a origem em que o widget está hospedado. Necessário para o envio do aplicativo para OpenAI; deve ser exclusivo por aplicativo.
- **Borda preferencial** — renderiza o widget dentro de um cartão com bordas.

### URLs de modelo

- **[!UICONTROL URL do Script]** — o ponto de entrada que inicializa o widget, compartilhado em todas as ações:
  `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`
- **URL de inserção do dispositivo** — a página EDS para esta ação específica:
  `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`

### Permissões

APIs de hardware e navegador que o dispositivo pode acessar:

| Permissão | Descrição |
|-----------|-------------|
| **Câmera** | Acessar a câmera do dispositivo |
| **Microfone** | Acessar o microfone do dispositivo |
| **Geolocalização** | Acessar a localização do usuário |
| **Área de transferência** | Ler ou gravar na área de transferência |

### Configuração da CSP

![Criar Ação — permissões e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Controla quais domínios externos o iframe do widget pode contatar. Cada domínio externo deve ser explicitamente classificado.

| Diretiva | Descrição |
|-----------|-------------|
| **Domínios de recursos** | Domínios para ativos estáticos — imagens, fontes, scripts, estilos |
| **Conectar domínios** | Domínios que o widget pode contatar via `fetch`, `XHR` ou `WebSocket` |
| **Enquadrar domínios** | Origens permitidas para iframes aninhados; a adição de entradas aciona uma revisão mais rígida do aplicativo a partir do OpenAI |
| **Redirecionar domínios** | Destinos confiáveis para `openExternal` links de redirecionamento ([!DNL ChatGPT]-específico) |
| **Domínios URI de base** | A diretiva CSP `base-uri` (somente SDK de aplicativos MCP, sem suporte de [!DNL ChatGPT]) |

Clique em **Criar nova ação** para salvar.

## Depois de criar uma ação

Sua ação aparece como um cartão na página Ações:

![Página Ações — ação criada](/help/assets/guide-create-action/actions-with-action.png)

Cada cartão mostra o nome da ação, a descrição, o símbolo de tipo (**[!UICONTROL EDS]**), o status da implantação (**Não implantada**) e a contagem de parâmetros. Você pode clicar em **...** para editar ou excluir, ou em **Revisar** para inspecionar a configuração.

![Detalhes do aplicativo — não implantado](/help/assets/guide-create-action/app-detail-not-deployed.png)

Os metadados da ação são salvos, mas nenhum código foi implantado ainda. Para tornar a ação funcional, é necessário:

1. **Configurar o widget EDS** — consulte o [Guia: Configurar o Widget (EDS)](/help/guides/widgets.md).
2. **Gravar o manipulador** — consulte o [Guia: Gravar o Manipulador de Ação](/help/guides/write-action-handler.md).
3. **[!UICONTROL Implantar]** — consulte o [Guia: Implantar Seu Aplicativo](/help/guides/deploy-your-app.md).

## Próximas etapas

- [Guia: configurar o widget (EDS)](/help/guides/widgets.md)
