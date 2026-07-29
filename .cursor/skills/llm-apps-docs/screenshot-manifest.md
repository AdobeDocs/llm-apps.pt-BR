---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# Manifesto de captura de tela de integração

Capturar caixa de entrada: `docs-captures/<YYYY-MM-DD>/`

Diretório de saída: `help/assets/guide-onboarding-agent/`

Capture somente pontos de verificação que ajudem materialmente o usuário a tomar uma decisão ou verificar o estado.

Os nomes de arquivos do Source não precisam corresponder aos nomes de arquivos finais. As capturas de tela dos mapas de habilidades por estado visível da interface do usuário, preservam os arquivos brutos e criam cópias limpas usando os nomes abaixo.

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
- Incluir: **Adicionar <plugin> para ChatGPT &#x200B;** e**&#x200B; Connect &#x200B;**.
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
