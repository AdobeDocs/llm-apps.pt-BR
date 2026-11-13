---
title: Campos de ação e widget
description: Definições de campo para metadados de ação, parâmetros, widgets, CSP e permissões em aplicativos Adobe LLM.
source-git-commit: 9b2ec369baba5d533088d2dd4adefcb80f5c9727
workflow-type: tm+mt
source-wordcount: '1273'
ht-degree: 2%
---

# Campos de ação e widget {#action-widget-configuration}

>[!IMPORTANT]
>
>Aplicativos LLM está disponível para todos os clientes do AEM Sites Cloud Service para desenvolver e gerenciar seus próprios aplicativos. Depois que a medição for iniciada, cada chamada de aplicativo será contabilizada nas Solicitações de conteúdo do AEM Sites do cliente. A Adobe anunciará a taxa de medição e a data de início antes de a medição entrar em vigor.

Use esta página para pesquisar campos no editor de ações. Para as etapas de criação, consulte [Criar uma ação do zero](/help/guides/create-action.md).

## Encontrar as configurações

A caixa de diálogo **[!UICONTROL Criar nova ação]** tem as guias **[!UICONTROL Ação]**, **[!UICONTROL Metadados do widget]** e **[!UICONTROL Extensões de ChatGPT]**.

Após a criação, o editor de ação salva usa quatro guias:

| Guia | Configurações |
|---|---|
| **[!UICONTROL Detalhes do LLM Widget]** | Tipo de dispositivo, domínio de sandbox, descrição do dispositivo, URLs de modelo e CSP |
| **[!UICONTROL Detalhes da ação]** | Identificador de código, descrição, parâmetros de entrada e **[!UICONTROL configurações de LLM]**, incluindo anotações, texto de status de invocação e visibilidade |
| **[!UICONTROL Análises]** | Coleta de intenção do usuário e instruções |
| **[!UICONTROL Extensões ChatGPT]** | Pontos de entrada de abertura direta, ícones de ação, preferências de exibição e menções do compositor |

Para salvar suas edições, selecione **[!UICONTROL Salvar alterações]**. As ações geradas que aguardam revisão mostram **[!UICONTROL Marcar como revisado]**, que também mescla suas solicitações de pull ou ramificações geradas.

## Parâmetros de ação

Parâmetros de entrada são os valores que a plataforma LLM envia para o manipulador de ação. O modelo extrai os valores da mensagem do usuário e os mapeia para os parâmetros configurados.

| Propriedade | Descrição |
|----------|-------------|
| **Nome** | O identificador de parâmetro (por exemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer`, `Boolean` ou `Object`; o editor salvo também oferece `File` para parâmetros de arquivos carregados |
| **Descrição** | Uma explicação legível — a plataforma LLM usa a explicação para extrair o valor correto |
| **Obrigatório** | Se marcado, o modelo deve fornecer este parâmetro antes de chamar a ação |

### Parâmetros de arquivo

Parâmetros de arquivo são nomes de campos de entrada configurados no editor de ação. Quando um usuário carrega um arquivo, o host fornece um objeto de arquivo para esses argumentos, normalmente incluindo `download_url` e `file_id`.

Os parâmetros do arquivo carregado são diferentes de [pontos de entrada do arquivo](/help/guides/chatgpt-extensions.md#file-entrypoint). Um ponto de entrada File usa um parâmetro Object chamado `file`, contendo os `name` e `resourceUri` do arquivo aberto.

## Campos de metadados

### Informações básicas

| Texto | Obrigatório | Descrição |
|-------|----------|-------------|
| **Nome da ação** | Sim | Nome para exibição da ação (por exemplo, *Pesquisar Produtos*) |
| **Descrição** | Sim | Explicação do que a ação faz — a plataforma LLM usa a explicação para decidir quando invocar a ação |

A caixa de diálogo de criação pré-visualiza um **Identificador de código** imutável derivado do nome da ação. Ele é exclusivo no aplicativo e contém até 63 caracteres, começando com uma letra minúscula e usando letras minúsculas, dígitos, hifens ou sublinhados. Ele mapeia a ação para `actions/<code-identifier>/index.js` no repositório do manipulador.

### Anotações

Dicas opcionais que descrevem o comportamento da ação:

| Anotação | Descrição |
|------------|-------------|
| **Dica destrutiva** | A ação pode excluir ou substituir dados |
| **Idempotente (mesmos argumentos = sem efeito extra)** | Repetir a mesma solicitação não tem efeito adicional no ambiente; solicitações repetidas não retornam necessariamente dados idênticos |
| **Abrir dica do mundo** | A ação interage com sistemas externos |
| **Dica somente leitura** | A ação lê apenas dados, nunca grava |

### Metadados do OpenAI

| Texto | Comprimento máx. | Descrição |
|-------|------------|-------------|
| **Chamando texto de status** | 64 caracteres | Mensagem mostrada na plataforma LLM enquanto a ação é executada (por exemplo, *Carregando produtos...*) |
| **Texto de status chamado** | 64 caracteres | Mensagem mostrada após a ação ser concluída (por exemplo, *Produtos carregados...*) |
| **Descrição do widget** | 512 caracteres | Mapeia para `_meta["openai/widgetDescription"]`; resume o componente renderizado para o modelo e reduz a narração repetida |

A descrição da ação controla quando o modelo seleciona a ação. A descrição do widget explica o que o componente mostra após ser renderizado.

### Visibilidade

Para ativar ou desativar uma ação, use a opção **[!UICONTROL Visibilidade]** na página do aplicativo. O switch controla se a próxima implantação inclui a ação. O editor de ações não possui uma chave liga/desliga separada.

As configurações do editor de ação a seguir controlam o modelo e a exposição do widget, não o estado ligado/desligado no nível do aplicativo:

| Alternar | Descrição |
|--------|-------------|
| **Expor ao modelo de IA** | A ação pode ser invocada pelo modelo de IA durante conversas |
| **Mostrar como widget na superfície do aplicativo** | A ação renderiza um widget visual no aplicativo |

As configurações de exposição do modelo e do widget não substituem a escolha de um tipo de widget.

### Analytics

| Texto | Descrição |
|-------|-------------|
| **Coletar intenção de usuário** | Ativado por padrão. Solicita um resumo anônimo da meta que levou à ação do Analytics |
| **Descrição da intenção do usuário** | Instruções usadas para solicitar o resumo, até 2.000 caracteres. O padrão exclui informações pessoais e confidenciais. Revisar qualquer personalização em relação à sua política de privacidade |

## Campos de widget

### Informações do widget

| Texto | Descrição |
|-------|-------------|
| **Tipo / Tipo de widget** | Na caixa de diálogo de criação, selecione **[!UICONTROL Bloco Edge Delivery Services]** ou **[!UICONTROL Nenhum widget]**. O editor salvo rotula a opção somente texto **[!UICONTROL Nenhuma interface interativa]** |
| **Domínio do widget** | Origem da sandbox usada pelo host. A interface o identifica conforme necessário para o envio do aplicativo e exclusivo por aplicativo. Isso é separado da **URL do Widget** da página do EDS |
| **Borda preferencial** | Solicita um contêiner com borda; a renderização depende do host. Disponível na caixa de diálogo de criação |

### URLs de modelo

| Texto | Descrição |
|-------|-------------|
| **[!UICONTROL URL do Script]** | URL HTTPS para o ponto de entrada EDS `scripts/aem-embed.js`. Compartilhado entre ações no mesmo projeto EDS |
| **URL do widget** | URL HTTPS da página EDS renderizada pela ação. Ações geradas configuram o URL automaticamente |

## Configuração da CSP

A Política de segurança de conteúdo controla com quais domínios externos o iframe do widget pode entrar em contato. Cada domínio externo deve ser explicitamente classificado.

| Diretiva | Descrição |
|-----------|-------------|
| **Domínios de recursos** | Domínios para ativos estáticos — imagens, fontes, scripts, estilos |
| **Conectar domínios** | Domínios que o widget pode contatar via `fetch`, `XHR` ou `WebSocket` |
| **Enquadrar domínios** | Origens permitidas para iframes aninhados; aciona revisão mais rígida do aplicativo |
| **Redirecionar domínios** | Destinos confiáveis para `openExternal` links de redirecionamento ([!DNL ChatGPT]-específico) |
| **Domínios URI de base** | A diretiva CSP `base-uri` (somente MCP Apps SDK, não [!DNL ChatGPT]) |

## Campos de extensão do ChatGPT {#chatgpt-extension-fields}

Essas configurações opcionais são somente ChatGPT; elas não configuram o Claude ou outras plataformas LLM. A disponibilidade varia de acordo com o cliente ChatGPT. Para obter os requisitos e as etapas, consulte [Configurar extensões ChatGPT](/help/guides/chatgpt-extensions.md).

### Pontos de entrada e ícones de ação

Os pontos de entrada exigem um widget. Selecione qualquer combinação de Global, Thread e Arquivo para o mesmo widget, ou deixe todas desmarcadas. Global e Thread invocam a ação sem entradas; File fornece apenas o objeto `file`. Outras entradas necessárias impedem que o ponto de entrada correspondente funcione. Se Arquivo for combinado com Global ou Thread, `file` deve ser opcional.

| Texto | Descrição |
|---|---|
| **Onde os usuários podem abrir este aplicativo** | Caixas de seleção para Global (tela cheia da navegação), Thread (uma guia de conteúdo na conversa) e Arquivo (um visualizador personalizado para extensões de arquivo correspondentes). Vários tipos podem ser selecionados |
| **URL do ícone** | Ícone de ação opcional compartilhado por seus pontos de entrada. Aceita um URL HTTP(S) ou URI de dados; prefira um URL de imagem HTTPS público. Alterar ou limpar o URL substitui qualquer variante de ícone salva |
| **Extensões de arquivo** | Pelo menos uma extensão, como `.csv`, para um ponto de entrada File. O editor adiciona um ponto à esquerda quando omitido |

Selecionar Arquivo adiciona uma entrada de Objeto opcional chamada `file` se estiver ausente. As definições existentes são preservadas e devem aceitar os `name` e `resourceUri` do arquivo aberto.

### Preferências de exibição

Essas configurações do widget são separadas dos pontos de entrada. O host escolhe a apresentação final.

| Texto | Opções e comportamento padrão |
|---|---|
| **Exibir quando iniciado pelo modelo** | `inline`, `fullscreen` ou **Nenhuma preferência** |
| **Modos de exibição de recursos com suporte** | **Inline**, **Fullscreen** ou ambos. Nenhum dos selecionados deixa os padrões do host em vigor |
| **Exibição inicial do recurso** | **Padrão de host**, **Em linha** ou **Tela cheia**. Quando os modos suportados são selecionados, o modo inicial deve ser incluído |

### Menções do compositor

**Habilitar menções ao compositor** designa a ação de pesquisa de recursos do aplicativo. Somente uma ação por aplicativo pode fornecer menção à pesquisa. A ação deve ser somente leitura e visível para aplicativo, com um parâmetro de Cadeia de Caracteres chamado `query` que aceite texto vazio e nenhuma outra entrada necessária. Um provedor pode ser somente texto; ativar a visibilidade do aplicativo não requer a configuração de um widget para o provedor de pesquisa.

## Permissões

As permissões de widgets controlam o acesso a APIs de hardware e navegador. As permissões são mapeadas para a política de permissão iframe.

Os controles de permissão aparecem em **[!UICONTROL Permissões (específicas de MCP)]** na caixa de diálogo de criação. Conceder somente os recursos exigidos pelo widget; o suporte ao host e o consentimento do usuário também se aplicam.

| Permissão | Descrição |
|------------|-------------|
| **Câmera** | Acessar a câmera do dispositivo |
| **Microfone** | Acessar o microfone do dispositivo |
| **Geolocalização** | Acessar a localização do usuário |
| **Área de transferência** | Ler ou gravar na área de transferência |
