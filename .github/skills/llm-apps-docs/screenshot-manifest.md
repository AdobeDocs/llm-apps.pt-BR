---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1052'
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
| `setup-choice.png` | Introdução às duas opções de infraestrutura visíveis | Crie seu aplicativo LLM - escolha a infraestrutura gerenciada da Adobe ou seus próprios repositórios |
| `app-details.png` | Nome do aplicativo, programa AEM, região do Analytics e aviso de configuração imutável | Detalhes do aplicativo - digite um nome, selecione um programa do AEM e escolha uma região de análise |
| `action-repository.png` | Caminho do próprio repositório com o GitHub selecionado e instruções de conexão do repositório | Configuração da infraestrutura de ação - conectar um repositório de ações vazio ou compatível |
| `provisioning.png` | Configuração gerenciada aprovada do Adobe em andamento | Configuração do aplicativo - a Adobe está preparando a infraestrutura |

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
| `generated-action-review.png` | Detalhes do widget do LLM, detalhes da ação, Analytics, um aviso de solicitação de pull gerado e marcar como revisado | Ação gerada - inspecione o widget e o manipulador antes da aprovação |

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
| `action-basic-info.png` | Pesquisar produtos, seu identificador derivado, uma descrição útil e anotações somente leitura | Criar nova ação - informações básicas e anotações |
| `action-analytics-user-intent.png` | Coletar a intenção do usuário e as instruções de resumo anônimo padrão | Criar nova ação: analisar a coleção de intenções do usuário |
| `widget-metadata.png` | Tipo de bloco do Edge Delivery Services e um exemplo seguro de origem da sandbox | Criar nova ação - tipo de widget e domínio de sandbox |
| `widget-template-urls.png` | Exemplo de URLs de script e widget | Criar nova ação - URLs de script e widget |
| `widget-permissions.png` | Permissões opcionais do navegador, com permissões não usadas desativadas | Criar nova ação - permissões do navegador |
| `widget-csp.png` | Controles de domínio de recursos, conexões, quadros, redirecionamentos e URI de base | Criar nova ação - domínios da política de segurança de conteúdo |

A caixa de diálogo de criação tem guias **Ação** e **Metadados do widget**. O editor salvo
tem **detalhes do Widget de LLM**, **detalhes da ação** e **guias do Analytics**. Capturar o
layout que o procedimento realmente usa. Preencha os campos de exemplo sem salvar e
descartar as edições.

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
