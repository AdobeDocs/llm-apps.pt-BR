---
source-git-commit: 03c918b1643d9c4e8ebee40fd67694acb6751a14
workflow-type: tm+mt
source-wordcount: '1080'
ht-degree: 0%
---
# Manifesto de captura de tela

Capturar caixa de entrada: `docs-captures/<YYYY-MM-DD>/`

Capture somente pontos de verificação que ajudem materialmente o usuário a tomar uma decisão ou verificar o estado.

Os nomes de arquivos do Source não precisam corresponder aos nomes de arquivos finais. As capturas de tela dos mapas de habilidades por estado visível da interface do usuário, preservam os arquivos brutos e criam cópias limpas usando os nomes abaixo.

Cada guia abaixo declara seu próprio diretório de saída. Use o da seção à qual a captura pertence.

# Guia de integração

Diretório de saída: `help/assets/guide-onboarding-agent/`

## Capturas necessárias

### `app-details-onboarding.png`

- Estado: nome do aplicativo, região de análise e **Criar meu aplicativo automaticamente** selecionados.
- Inclua: detalhes do aplicativo, região de análise e o início de Criar meu aplicativo.
- Texto alternativo: `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- Estado: repositório EDS vazio inicializado com padrão AEM; sincronização de código AEM necessária.
- Incluir: mensagem de validação do repositório EDS e link de instalação.
- Texto alternativo: `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- Estado: a Sincronização de Código do AEM está instalada, mas o usuário atual não é um administrador de site de EDS.
- Incluir: a mensagem de validação completa e **Abrir o AEM Live Admin**.
- Texto alternativo: `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- Estado: página Ações enquanto a integração estiver ativa.
- Incluir: mensagem de progresso e etapas de geração.
- Texto alternativo: `Actions — generating recommendations`

### `actions-ready-for-review.png`

- Estado: lista de ações gerada após a conclusão da integração e antes da aprovação.
- Incluir: nomes da ação, status gerado/revisado e controle de revisão.
- Use apenas conteúdo de fixação.
- Texto alternativo: `Actions — generated actions ready for review`

### `generated-action-review.png`

- Estado: uma ação gerada por representante.
- Incluir: navegação de Metadados de Ação e Widget, resultado da geração de manipulador e **Marcar como revisado**.
- Máscara: proprietário do repositório, se necessário.
- Texto alternativo: `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- Estado: cada ação gerada foi revisada.
- Incluir: **Todas as ações são revisadas**, medalhas de ação e **Ir para a página do aplicativo**.
- Texto alternativo: `Actions — all generated actions reviewed`

### `deploy-stage.png`

- Estado: caixa de diálogo de implantação antes de iniciar.
- Incluir: ambiente de destino de preparo e **Implantação**.
- Texto alternativo: `Deploy — select the Stage environment`

### `deploy-running.png`

- Estado: pipeline de implantação em execução.
- Inclua: etapas de preparação, inicialização, criação e publicação.
- Texto alternativo: `Deploy — deployment pipeline running`

### `deploy-successful.png`

- Estado: implantação de preparo bem-sucedida.
- Inclua: ambiente e status de sucesso.
- Máscara: namespace de tempo de execução, URL completo do MCP, IDs, carimbos de data e hora se estiverem identificando.
- Texto alternativo: `Deploy — successful staging deployment`

### `app-mcp-url.png`

- Estado: testar a seção do aplicativo após a implantação.
- Incluir: ambiente de preparo, **Copiar URL** e histórico de implantação bem-sucedida.
- Mask: o URL do servidor MCP.
- Texto alternativo: `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- Estado: página de plug-ins ChatGPT.
- Incluir: guia Plug-ins, pesquisar e criar botão.
- Texto alternativo: `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- Estado: caixa de diálogo Novo plug-in.
- Inclua: nome, descrição, URL do servidor, autenticação, confirmação e Criar.
- Mask: o URL do servidor MCP.
- Texto alternativo: `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- Estado: confirmação após a criação do plug-in.
- Incluir: **Adicionar <plugin> para ChatGPT **e** Connect **.
- Máscara: URL do navegador e identificadores do conector.
- Texto alternativo: `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- Estado: plug-in de correção chamado no ChatGPT.
- Incluir: aplicativo anexado, widget gerado e resposta de texto.
- Excluir: histórico da conversa, nome da conta e aplicativos não relacionados.
- Texto alternativo: `ChatGPT — generated LLM App plugin response`

## Capturas opcionais

Adicione uma captura somente quando a prosa não puder explicar a decisão claramente:

- Seleção de acesso ao repositório do aplicativo GitHub.
- Estado de integração com falha para solução de problemas.
- Ícone de plug-in fazer upload.

Não adicione capturas de tela para listas de campos estáticos que já estão limpas em prosa.

# Guia de autenticação

Diretório de saída: `help/assets/guide-authentication/`

Referenciado por [authentication.md](../../../help/guides/authentication.md).

A etapa **[!UICONTROL Copiar o identificador de recurso]** reutiliza o guia de integração
`app-mcp-url.png`. Não o capture novamente.

Cada captura nesta seção mostra a configuração de segurança. Mascarar antes de salvar:

- A URL do **[!UICONTROL Emissor]** e qualquer nome de host que identifique o provedor de identidade ou seu fornecedor.
- O URL do servidor MCP, por completo, onde quer que apareça.
- Identificadores de locatário, cliente e organização.
- Nome da conta, avatar e email.

Use valores de espaço reservado neutros em que um campo deve permanecer legível — por exemplo, um emissor de
`https://auth.example.com`. Os nomes de escopo devem ser lidos como exemplos genéricos, como `orders:read`.

## Capturas necessárias

### `auth-core-settings.png`

- Estado: **[!UICONTROL Configurações]** > **[!UICONTROL Autenticação]** com **[!UICONTROL Habilitar autenticação]** ativada e **[!UICONTROL Configurações principais]** preenchidas.
- Incluir: o seletor de **[!UICONTROL Workspace]** que mostra o **[!UICONTROL Estágio]**, **[!UICONTROL Habilitar autenticação]** em seu estado ligado, **[!UICONTROL Emissor]** e **[!UICONTROL Escopos com suporte]** que contém pelo menos dois escopos.
- Inclua o controle **[!UICONTROL Configurações avançadas]** recolhido, para que o leitor possa ver que o **[!UICONTROL URI JWKS]** é opcional e onde ele está.
- Mask: o nome de host do emissor.
- Texto alternativo: `Authentication — enable authentication and complete the core settings`

Capturado em 25 de agosto de 2026. Recortado para soltar a tela vazia; não é necessário mascaramento, porque
**[!UICONTROL Emissor]** foi definido como `https://auth.example.com` no produto antes de
captura. Prefira isso à edição da imagem posteriormente. **[!UICONTROL Escopos com suporte]** suspensões
um escopo (`read:all`); dois ilustrariam melhor o campo, mas isso não vale a pena
capturar novamente por conta própria.

### `auth-per-action.png`

- Estado: **[!UICONTROL Configuração por ação]** após habilitar a autenticação, com os modos deliberadamente misturados.
- Inclua: pelo menos três ações, uma por modo — **[!UICONTROL Nenhuma]**, **[!UICONTROL Obrigatório]** e **[!UICONTROL Opcional]** — e a coluna **[!UICONTROL Escopos]** preenchida nas restritas.
- Incluir: **[!UICONTROL Exigir autenticação em todas as ações]**, idealmente em seu estado indeterminado, que é o que uma configuração mista produz.
- Use apenas nomes de ações de correção.
- Texto alternativo: `Authentication — set an auth mode and scopes for each action`

Capturado em 25 de agosto de 2026. Cortado apenas, nada para mascarar. Mostra todos os três modos, um preenchido
**[!UICONTROL Escopos]** célula e **[!UICONTROL Exigir autenticação em todas as ações]** em sua
estado indeterminado, com `Test Action 1/2/3` como nomes de correção.

Cortar **dentro** da própria borda do contêiner do painel de configurações — uma regra 1px de altura completa fica em cada borda
lado da captura e deixar qualquer um no quadro é lido como uma linha reta abaixo da borda do
imagem.

O próprio aviso do produto sobre [!DNL Claude] aplicando autenticação por conector foi
**não observado nesta guia em duas rodadas de captura**, portanto, não é necessário aqui. O
o guia declara esse comportamento em prosa. Se o aviso existir em uma build posterior,
capturar como `auth-claude-warning.png` e adicionar uma entrada.

### `chatgpt-authentication-mode.png`

- Estado: a caixa de diálogo **[!UICONTROL Novo Plug-in]** com a lista suspensa **[!UICONTROL Autenticação]** aberta.
- Inclua: todos os três valores — **[!UICONTROL Sem Autenticação]**, **[!UICONTROL Misto]** e **[!UICONTROL OAuth]** — para que a tabela de mapeamento no guia possa ser verificada em relação ao controle real.
- Máscara: o URL do servidor MCP e qualquer identificador de conector no URL do navegador.
- Texto alternativo: `ChatGPT — select the authentication mode for the plugin`

Enquadre-o da mesma forma que o `chatgpt-new-plugin.png` do guia de integração: o cartão de diálogo com
uma margem da página ainda visível ao redor dela, aproximadamente 40px para a esquerda e para cima. Não cortar a liberação para
o cartão.

Capturado em 25/08/2026, modo de luz, para corresponder a todas as outras capturas na documentação. O
a lista suspensa oculta o campo **[!UICONTROL URL do Servidor]**, portanto, a URL do MCP não é legível — mas
seu material translúcido deixa uma imagem borrada do conteúdo desse campo sangrar ao lado da
opções. As três linhas não destacadas foram repintadas com o preenchimento do painel e seus rótulos
renderizado novamente, o que o remove. Verificar por amostragem, não por olho: o sangramento é fraco o suficiente para
e é o URL do servidor MCP.

Observe que o controle em tempo real oferece **quatro** valores — **[!UICONTROL OAuth]**, **[!UICONTROL Access
token/chave de API]**, **[!UICONTROL Sem autenticação]** e **[!UICONTROL Misto]**. O mapeamento do guia
A tabela abrange apenas os três que os modos de autenticação de um aplicativo podem mapear, o que é correto, mas não
descreva a lista suspensa como tendo três opções.

## Capturas opcionais

Adicionar somente se a prosa for insuficiente:

- `auth-scope-blocked.png` — **[!UICONTROL Salvar]** bloqueado porque uma ação requer um escopo ausente de **[!UICONTROL Escopos com suporte]**. Útil para a entrada da solução de problemas.
- O prompt de entrada no meio da conversa uma ação **[!UICONTROL Opcional]** gera. Interface de usuário de propriedade de plataforma que muda com frequência e já está descrita em prosa.

Não capture a página de logon do próprio provedor de identidade. Identifica o fornecedor, que esta documentação não nomeia.
