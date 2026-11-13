---
source-git-commit: 0276ae4e4627e49157911ad579a42c0e77045eee
workflow-type: tm+mt
source-wordcount: '2314'
ht-degree: 1%
---
# Manifesto de captura de tela

Capture esses estados com a automação do navegador usando
[screenshots.md](screenshots.md). Selecione os pontos de verificação relevantes ao artigo;
este manifesto não autoriza a criação, geração, aprovação ou
implantação.

As capturas brutas permanecem em `docs-captures/<run-id>/`. Publicar somente visualmente revisado,
limpou PNGs em `help/assets/`. Manter os nomes de arquivo abaixo estáveis quando
atualização de imagens.

## Configuração da infraestrutura

Artigo: `/help/guides/create-app.md`

Diretório de saída: `help/assets/guide-infrastructure/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `setup-choice.png` | Introdução ao **Deixe-nos lidar com ele** e **Use minha configuração do Cloud Manager**, chamada na opção de repositório próprio | Criar seu aplicativo LLM - Introdução ao Deixe-nos lidar com isso e usar minha configuração do Cloud Manager |
| `action-repository.png` | Programa e repositório do Cloud Manager selecionados, **Repositório validado** | Configuração da infraestrutura de ação — programa e repositório do Cloud Manager selecionados |
| `widget-source.png` | Origem do widget com **Selecionar repositório de widget manualmente** (Cloud Manager EDS marcado como Em breve) | Configuração da infraestrutura do widget - Origem do widget |
| `widget-repository.png` | Observação de repositório de widget não GitHub validado, **site do Edge Delivery Services** | Configuração da infraestrutura de widgets - repositório de widgets validado e seu site EDS vinculado |
| `app-details.png` | Nome do aplicativo, conjunto de programas AEM do repositório, região de dados do Application Insights | Detalhes do aplicativo - nome, programa AEM e região de dados Application Insights |
| `settings-repositories.png` | Configurações > Repositório com cartões de repositório do Cloud Manager e o site vinculado | Configurações do aplicativo - seção Repositório |
| `provisioning.png` | Configuração gerenciada de Aplicativos LLM aprovada em andamento | Configuração do aplicativo - a Adobe está preparando a infraestrutura |

Última captura (todas menos `provisioning.png` e `da-write-permissions.png`): 2026-11-13, Preparo
(`experience-stage.adobe.com/llm-apps`), repositórios de ação e widget do GitLab com o
Git próprio. Os valores de programa, repositório, site e origem são mascarados; os contornos vermelhos são chamadas.
Não capturado: o caminho do widget GitHub (Instalar sincronização de código do AEM, Validar configuração do AEM). Ainda não
verificado na produção.

Excluir detalhes da conta, IDs da organização, nomes reais de programas, repositório
identificadores e IDs do aplicativo. Use campos vazios ou um nome de ajuste neutro onde
possível. A validação do repositório pode inicializar o código, portanto, obtenha aprovação antes de
capturar um resultado de validação.

## Criar ações e revisar o trabalho gerado

Artigo: `/help/guides/creating-actions.md`

Diretório de saída: `help/assets/guide-creating-actions/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `build-choice.png` | Criar ações selecionadas automaticamente, um site público inserido e um controle de início estável, se incluído | Ações - escolha a geração automática e insira um site público |
| `generating.png` | Geração em execução, tempo estimado e ações Ir para | Ações - a geração está em execução |
| `actions-ready-for-review.png` | Corrigir nomes de ação, selos de revisão e controles de revisão | Ações - as ações geradas estão prontas para revisão e não são implantadas |
| `generated-action-review.png` | Detalhes de ação, detalhes do Widget do LLM, Insights do aplicativo, Extensões do ChatGPT, o aviso **Ramificação enviada** (repositórios próprios) e Marcar como revisado. Última captura: 2026-11-13, Stage, aplicativo GitLab; nome do aplicativo mascarado | Revisão de ação gerada - Ramificação enviada e Marcar como revisada |

Mascarar links de repositório privado e de solicitação de pull. Cortar pessoal ou
detalhes de contato quando o artigo precisar apenas do status de revisão. Não selecionar
**Marcar como revisado** sem aprovação.

Capturar o estado de todas as ações revisadas somente quando a aprovação estiver no escopo e a
Estado-Membro existe realmente. Não aprove o trabalho gerado para satisfazer uma captura de tela
lista de verificação.

## Criar uma ação do zero

Artigo: `/help/guides/create-action.md`

Diretório de saída: `help/assets/guide-create-action/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `action-basic-info.png` | Detalhes da ação: Pesquisar Produtos, seu identificador de código, descrição e os parâmetros `category` e `query` | Criar nova ação - Detalhes da ação com informações de ação e parâmetros de entrada |
| `action-llm-settings.png` | Configurações do LLM expandidas: somente leitura, idempotente, anotações de mundo aberto, textos de status, visibilidade | Criar nova ação - Configurações de LLM |
| `action-analytics-user-intent.png` | Application Insights: Coletar a intenção do usuário e as instruções padrão | Criar nova ação - Application Insights |
| `widget-metadata.png` | Detalhes do widget do LLM: Bloco do Edge Delivery Services, exemplo de domínio do widget, descrição do widget e URLs do modelo | Criar nova ação - Detalhes do dispositivo LLM |
| `widget-csp.png` | URLs de modelo e as listas de domínio de Configuração da CSP | Criar nova ação - Configuração da CSP |

Última captura: 2026-11-13, Preparo, após a refatoração da caixa de diálogo (mesmas guias que o editor de ação:
Detalhes de ação, detalhes do Widget do LLM, Insights do aplicativo, Extensões ChatGPT; Permissões,
Borda preferida e a seção Parâmetros do arquivo removida). Somente valores de exemplo; a caixa de diálogo
foi cancelado sem salvar. Os contornos vermelhos são chamadas de retorno.

## Autenticação

Artigo: `/help/guides/authentication.md`

Diretório de saída: `help/assets/guide-authentication/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `auth-core-settings.png` | Configurações de preparo, autenticação habilitada, `https://auth.example.com`, `catalog:read`, `profile:read` e Avançadas | Autenticação - habilite a autenticação e conclua as configurações principais |
| `auth-per-action.png` | Ações de correção que mostram Nenhum, Obrigatório, Opcional e um escopo atribuído | Autenticação - escolha um modo de autenticação e escopos para cada ação |

Usar configurações não salvas e descartá-las posteriormente. Verifique se a autenticação foi salva
mantém-se inalterado. A etapa do identificador de recursos reutiliza
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

Se a interface do usuário mostrar um aviso que está em conflito com o tempo de execução implantado ou a atual
comportamento da plataforma, documente a discrepância. Um corte somente de tabela é apropriado
ao ensinar modos, mas não oculte um aviso relevante sem explicá-lo.

## Variáveis do aplicativo

Artigo: `/help/guides/app-variables.md`

Diretório de saída: `help/assets/guide-app-variables/`

Use `GREETING_PREFIX` com `Good day` como valor de exemplo e `Howdy` como
valor de substituição. Nunca capture um valor real de configuração.

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `variables-empty.png` | Espaço de trabalho de Preparo vazio, seletor de Workspace e Adicionar | Variáveis e segredos - espaço de trabalho vazio do Palco |
| `add-variable-dialog.png` | Variável GREETING_PREFIX não salva e o aviso de suporte secreto atual | Adicionar variável ou segredo - digite GREETING_PREFIX e Good day |
| `variable-added.png` | Variável de luminária aprovada com controles de nome, tipo, valor, carimbo de data e hora e linha | Variáveis e segredos - GREETING_PREFIX no espaço de trabalho Preparo |
| `update-variable-dialog.png` | Valor atual Bom dia e novo valor não salvo Howdy | Atualizar GREETING_PREFIX - alterar Good day para Howdy |
| `delete-variable-dialog.png` | Caixa de diálogo de confirmação sem confirmar a exclusão | Delete GREETING_PREFIX - verifique a confirmação da exclusão |

A abertura de uma caixa de diálogo não autoriza um salvamento ou exclusão. Reutilizar um existente seguro
corrigir variável ou perguntar antes de criar um. Manter uma imagem mais antiga somente após
verificando se os campos e controles ainda correspondem à interface do usuário atual.

## Extensões da ChatGPT

Artigo: `/help/guides/chatgpt-extensions.md`

Diretório de saída: `help/assets/guide-chatgpt-extensions/`

Capturas de tela de ponto de entrada globais e de thread, configurações de exibição e menção do compositor
as capturas de tela de configuração do foram fornecidas em 11/2026 e são publicadas como
registrados abaixo. Outros pontos de verificação permanecem planejados, não ativos existentes. Não
adicione links de imagem do artigo até que as capturas reais tenham sido higienizadas e visualmente
revistos.

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `extensions-tab.png` | Ação salva, apoiada por widget, com extensões ChatGPT selecionadas | Extensões do ChatGPT - menções a pontos de entrada, modo de exibição e compositor |
| `global-thread-entrypoints.png` | Global e Thread selecionados juntos para o widget Início, com a opção Arquivo desmarcada | Extensões ChatGPT - Global e Thread selecionadas para a ação Página inicial |
| `global-entrypoint-menu.png` | Ponto de entrada inicial no menu de navegação do cliente | Menu de navegação do ChatGPT mostrando o ponto de entrada global da Página inicial |
| `global-entrypoint-open.png` | Janela completa do cliente com a tela inicial aberta e o painel de navegação visível | Painel inicial aberto a partir do ponto de entrada global |
| `thread-entrypoint-menu.png` | Menu de conversa com plug-ins expandidos | Menu Conversa - escolha o ponto de entrada do thread Início |
| `thread-entrypoint-open.png` | Painel inicial ao lado de uma conversa | Painel inicial aberto ao lado de uma conversa no ChatGPT |
| `file-entrypoint.png` | Ponto de entrada de arquivo não salvo com .csv | Ponto de entrada do arquivo - ofereça um visualizador personalizado para arquivos CSV |
| `entrypoint-icon.png` | URL do ícone opcional não salvo compartilhado pelos pontos de entrada selecionados da ação | Ícone de ponto de entrada - defina um ícone de ação compartilhada opcional |
| `display-settings.png` | Preferência de inicialização do modelo, modos de recursos compatíveis e exibição inicial nos padrões do host | Modo de exibição - preferências de lançamento de recurso e modelo nos padrões |
| `composer-mentions.png` | Pesquisar menções somente texto com a opção de menção ativada | Menções de pesquisa - ativadas menções ao compositor |
| `composer-mentions-query.png` | Detalhes da ação Pesquisar menções com uma entrada de consulta de sequência opcional | Menções de pesquisa - entrada opcional de sequência de caracteres chamada query |
| `composer-mentions-conflict.png` | Desativação da opção de menção e do aviso ao nomear o provedor existente | As menções do compositor não estão disponíveis porque as menções de pesquisa já são o provedor |

Usar um equipamento de catálogo aprovado e não sensível. Confirmar se os formulários não são salvos automaticamente
antes de inserir valores de exemplo; descarte as alterações não salvas posteriormente. Não salvar,
aprove as ações geradas ou implante apenas para concluir esta lista de verificação.
Os resultados de terceiros precisam de acesso autenticado a um cliente compatível. Arquivo
Os pontos de entrada exigem desktop; o navegador Trabalho não é o ChatGPT clássico.

### Atualização da documentação do ponto de entrada, 05/11/2026

- Source: componentes `llm-apps-ui` locais atuais e validação de entrada, não um
Apresentação da interface de produção.
- Os controles de ação rápida foram removidos. Uso global, de thread e de arquivo
caixas de seleção que suportam várias seleções para um widget. O URL do ícone é um
configuração opcional de nível de ação compartilhada por pontos de entrada.
- Selecionar Arquivo adiciona uma entrada de Objeto opcional chamada `file` quando ausente.
A combinação de Arquivo com Global ou Thread requer que a entrada permaneça opcional.
- A inspeção do navegador foi bloqueada porque ambos os perfis do navegador de automação foram
já está em uso. Nenhuma captura de tela nova foi capturada.

### Texto da origem do rascunho, 2026-11-03

- Ambiente do Source: implementação local de UI/API/runtime/SDK, não uma concluída
Apresentação do editor de ação de produção.
- A verificação do pacote publicado confirmou as APIs de extensão no tempo de execução 1.3.0 e
SDK 0.2.1 Estas são as versões documentadas da linha de base, não uma alegação de
recurso foi introduzido nessas versões exatas.
- A especificação de extensão pública do OpenAI foi verificada quanto a restrições de plataforma.
- Logon de produção bem-sucedido em um navegador separado controlado por agente. Nenhum aplicativo
as configurações foram salvas, nenhuma correção foi selecionada e nenhuma implantação foi iniciada.
- O usuário adiou as capturas de tela. Nenhuma imagem nova foi capturada ou substituída.
Os rótulos exatos do controle de produção e o comportamento completo do host ainda exigem um
apresentação ao vivo antes da publicação.

## Implantação e teste

Artigo: `/help/guides/deploy-your-app.md`

| Diretório de saída | Nome de arquivo | Estado e contexto útil |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | Ambiente de destino Preparo e o controle de Implantação, antes de iniciar |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | Atual Teste o cartão do aplicativo, rótulos de ambiente e Copiar URL; mascare todo URL de tempo de execução |
| `help/assets/guide-deploy/` | `deployment-history.png` | Colunas de ambiente, status e data de implantação sem identificadores de aplicativo |

Use uma implantação existente somente leitura para o cartão de teste e o histórico, quando permitido.
Não inicie uma implantação para obter esses estados, a menos que o usuário a aprove.
Capturar caixas de diálogo em execução ou de implantação bem-sucedida somente quando esse estado estiver em ambos
pertinentes e autorizados.

Para `/help/guides/test-in-chatgpt.md`, o valor retido
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png` ilustra um produto
resposta. Ele não verifica a interface de configuração atual. Nova configuração do ChatGPT ou do Claude
as capturas de tela exigem acesso autenticado no momento; caso contrário, use o fornecedor oficial
instruções e omitir imagens não verificadas.

## Configuração do Analytics

Artigo: `/help/guides/analytics-setup.md`

Diretório de saída: `help/assets/guide-analytics/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `analytics-settings.png` | Configurações do aplicativo, guia Analytics, Habilitar Analytics ativado, ID de sequência de dados vazia, Requisitos expandidos, Salvar alterações desativado | Configurações do aplicativo, guia Analytics - Habilitar o Analytics ativado, um campo de ID de sequência de dados vazio e a lista de Requisitos expandida |
| `datastream-analytics-service.png` | Coleta de dados, Adicionar serviço, Adobe Analytics ativado com uma ID de conjunto de relatórios fictícia | Adicionar serviço - Adobe Analytics ativado com uma ID de conjunto de relatórios de exemplo |
| `datastream-platform-service.png` | Coleta de dados, serviço Adobe Experience Platform com somente o conjunto de dados do evento selecionado | Serviço Adobe Experience Platform - o conjunto de dados do evento selecionado e as outras opções limpas |
| `datastream-services.png` | Detalhes da sequência de dados com ambos os serviços ativados, uma ID de sequência de dados fictícia e o esquema de mapeamento | Detalhes da sequência de dados - ambos os serviços ativados, com o exemplo ID da sequência de dados e o esquema de mapeamento |

Nunca capture uma ID de sequência de dados real. Não selecione **Salvar alterações**: depois de salvar,
somente um administrador pode alterar a sequência de dados. Mostrar Experience Platform, Adobe Analytics,
e Customer Journey Analytics somente quando eles mostrarem uma configuração específica para
Aplicativos LLM; vincular ao Experience League para todo o resto. Substituir organização
nomes, IDs de conjunto de relatórios, IDs de sequência de dados, IDs de conjunto de dados, emails e nomes pessoais
com estes valores fictícios: `example-org`, `demo.<suffix>` conjuntos de relatórios,
`00000000-0000-0000-0000-000000000000`, `0000…0001`, `user@example.com` e
`Example User`.

## Preparar para envio

Artigo: `/help/guides/prepare-submission.md`

Diretório de saída: `help/assets/guide-submission/`

| Nome de arquivo | Estado e contexto útil | Texto alternativo sugerido |
|---|---|---|
| `submission-card.png` | Aplicativo após uma implantação de Produção, Prepare para o cartão de envio com a Configuração | Preparar para envio - o status de preparação da Produção e Configurar |
| `submission-setup.png` | Caixa de diálogo Preparar para envio, Configurar e preparar, detalhes do editor de exemplo e ambos os ícones salvos | Configurar e preparar - Editor e detalhes legais, ícones Aprovado e Salvar alterações |
| `submission-prepare.png` | A mesma caixa de diálogo foi rolada para Prepare your package, Readiness mostra Ready to prepare (Pronto para preparar), caixa de seleção desmarcada | Preparar o pacote - Pronto para preparar, a caixa de seleção de confirmação e Preparar pacote |
| `submission-progress.png` | Mesma caixa de diálogo enquanto uma execução está em andamento | Preparação em andamento - barra de progresso e hora de início |
| `submission-results.png` | Resultados após uma execução concluída com links legais de espaço reservado: a preparação requer atenção, Download do pacote desabilitado | Resultados - o resumo da preparação, o relatório de Download, o pacote de Download e a gravação do Download |
| `submission-test-cases.png` | Resultados, casos de teste, primeiro caso descrito | Casos de teste - um caso de revisão com sua solicitação, comportamento esperado e status Registrado |
| `submission-before-submit.png` | Resultados, Antes de enviar a lista e as verificações APROVAR/AVISAR/FALHAR; execute a ID abaixo do recorte | Antes de enviar - os detalhes restantes, os detalhes de Atualização e as verificações do pacote |

Capturado no palco, 13/11/2026, de um aplicativo implantado na produção. Somente uso
exemplo de valores do publicador (`Frescopa Coffee (example)`, `https://www.example.com/...`)
e ícones de espaço reservado. Cada execução de preparação chama o aplicativo implantado e leva sobre
20 minutos; inicie um somente quando as telas Resultados precisarem de atualização. Mascaramento de URLs MCP,
IDs de execução e nomes de aplicativos nas capturas de Resultados.

## Origem de captura existente

A execução de captura 2026-10-19 usou a interface local solicitada pelo usuário, com interface/API correspondente
verificações de origem. Ele não estabelece a paridade de produção.

- Capturas brutas: `docs-captures/2026-10-19/` e seu subdiretório `full-review/`.
- As imagens públicas substituídas ou anteriores permanecem nos subdiretórios locais `retired/`.
- A correção de documentação atingiu o estágio de revisão de ação gerada. Nenhuma ação
aprovações, mesclagens de solicitações de pull, implantações ou autenticação/variável salva
as alterações faziam parte dessa execução de captura.
- As imagens de linha variável, atualização e exclusão de uma execução anterior foram retidas após
comparando os controles com a implementação atual.
- As telas de configuração de terceiros não foram capturadas novamente. As capturas de tela de configuração obsoletas foram
removido em vez de apresentado como atual.

Para cada nova atualização, atualize a data, o ambiente de origem, os estados verificados e
higienização e quaisquer limites de verificação remanescentes. Não copiar a origem antiga
como evidência de uma nova captura.

### Remoção do switch do editor de ações, 2026-10-19

Atualizado `guide-creating-actions/generated-action-review.png` a partir da execução
da interface do usuário local após remover a opção Ativada do editor. A visibilidade da página do aplicativo
interruptor permanece o controle de ação ligado/desligado; configurações de exposição do modelo e widget
permanecem no editor. O link do repositório é mascarado e a navegação da conta é
excluído. Nenhuma ação foi salva, aprovada, alternada ou implantada durante esta atualização.
Captura bruta: `docs-captures/2026-10-19/remove-enabled/`.

### Configurações do Analytics, 21/10/2026

Atualizado `guide-analytics/analytics-settings.png` a partir do fornecido pelo usuário
`Screenshot 2026-10-21 at 11.48.36.png`, não é uma execução orientada por agente. Origem
ambiente: não gravado e não visível na imagem; a paridade de produção não é
verificado. Revisão visual confirmada Habilitar o Analytics está ativado, o campo ID da sequência de dados
está vazio, Requisitos é expandido e Salvar alterações é desativado. A imagem foi
copiado sem edições; nenhum identificador ou detalhe de conta está visível.

### Capturas de tela da sequência de dados, 10/10/2026

Adicionado `guide-analytics/datastream-analytics-service.png`,
`datastream-platform-service.png` e `datastream-services.png` de existentes
Capturas internas de coleção de dados (29/07/2026 e 10/10/2026). A ID do conjunto de relatórios
e a ID da sequência de dados foram substituídas por sobreposições de texto fictício (`demo.aem.llm` e
`00000000-0000-0000-0000-000000000000`); a fonte de sobreposição é São Francisco, então
O é ligeiramente diferente da fonte do produto. A imagem de serviço da plataforma não precisava de
alterações. A imagem de serviço do Analytics ainda mostra o ponteiro do mouse do
captura original. Um OCR transmite as imagens finais e não encontra identificadores originais.
Substitua-os por capturas novas que usam valores fictícios digitados na interface do usuário quando
o artigo é atualizado em seguida.
