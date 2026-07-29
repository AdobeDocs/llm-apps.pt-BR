---
title: Solução de problemas de aplicativos Adobe LLM
description: Resolva problemas comuns de repositório, integração, manipulador, widget, implantação e plug-in do ChatGPT.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '632'
ht-degree: 0%

---


# Resolução de problemas {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Comece com o sintoma que você pode ver. Não compartilhe credenciais, tokens, URLs de MCP privados ou resultados confidenciais de manipuladores durante a solução de problemas.

## Criação e integração de aplicativos

| Sintoma | O que tentar |
|---------|-------------|
| Novos repositórios não são exibidos | Selecione **Gerenciar repositórios no GitHub**, conceda aos Aplicativos Adobe LLM o acesso ao Aplicativo GitHub para ambos os repositórios, retorne à caixa de diálogo e atualize as listas |
| O repositório EDS requer sincronização de código do AEM | Instale a Sincronização de código do AEM para o repositório EDS e retorne à caixa de diálogo Criar aplicativo LLM |
| A validação do EDS informa que você não é um administrador | Selecione **Abrir o administrador do AEM Live**, adicione a si mesmo como administrador do site EDS e atualize o repositório |
| A integração ainda está gerando | Aguarde aproximadamente 15 minutos. Você pode sair da página e retornar mais tarde |
| Falha nos relatórios de integração | Confirme se os dois repositórios estão acessíveis e se o site é público por HTTPS, em seguida, entre em contato com a equipe do Beta com a mensagem de erro visível |

## Ações e manipuladores

| Sintoma | O que tentar |
|---------|-------------|
| A ação não foi invocada | Anexe o plug-in ChatGPT, confirme se o **Expor ao modelo de IA** está habilitado, melhore a descrição da ação e reimplante as alterações de metadados |
| Resposta vazia ou de erro | Execute `npm test` e chame o manipulador com o Inspetor MCP ou `curl`. Consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md) |
| O manipulador funciona localmente, mas não após a implantação | Confirme se a confirmação mais recente foi enviada por push, se há configuração de tempo de execução e se o identificador de código de ação corresponde a `actions/<code-identifier>/index.js` |
| A ação gerada não pode ser marcada como revisada | Confirme a geração do manipulador e widget com êxito. Verifique se há conflitos de mesclagem nas solicitações de pull geradas, recarregue a ação e selecione **Marcar como revisado** novamente |

## Widgets

| Sintoma | O que tentar |
|---------|-------------|
| O dispositivo não é renderizado | Verificar o URL do script, o URL do widget, a publicação HTTPS, os domínios da CSP e os cabeçalhos do CORS |
| O widget é renderizado, mas não mostra dados | Chame o manipulador com o Inspetor MCP e compare sua forma `structuredContent` com os campos lidos de `bridge.toolResult` |
| O widget funciona em visualização direta, mas não no ChatGPT | A visualização direta pode usar dados de amostra. Teste o resultado do manipulador implantado e verifique se a origem do EDS é permitida pelo CORS e pelo CSP |
| Solicitação de navegador bloqueada | Adicione somente a origem necessária ao campo CSP correto e reimplante |
| O Editor de Cabeçalhos HTTP não pode salvar a configuração | Use o [Serviço de Configuração do AEM](https://aem.live/docs/config-service-setup) ou peça ao administrador do EDS para inicializar a configuração dos cabeçalhos do site |

Não registre em log os valores `bridge.toolResult` concluídos quando eles puderem conter dados pessoais ou confidenciais.

## Implantação

| Sintoma | O que tentar |
|---------|-------------|
| Falha na implantação durante **Preparando** | Verifique se o repositório do manipulador está vinculado e se o acesso ao Adobe Developer Console ainda é válido |
| Falha na implantação durante **Compilar aplicativo** | Executar `npm install`, `npm test` e `npm run build` localmente. Corrija dependências, sintaxe ou falhas de teste e envie as alterações |
| A implantação foi bem-sucedida, mas as alterações estão ausentes | Confirmar se a confirmação esperada foi enviada e reimplantar no mesmo ambiente |
| A ação permanece **Não implantada** | Implantar novamente depois de revisar a ação ou alterar seus metadados |

## Plug-ins do ChatGPT

| Sintoma | O que tentar |
|---------|-------------|
| O plug-in não aparece | Habilite o modo de desenvolvedor, abra [chatgpt.com/plugins](https://chatgpt.com/plugins), verifique se o plug-in existe e selecione **Conectar** |
| Falha na criação do plug-in | Confirme se o modo de desenvolvedor está habilitado, copie novamente a URL do servidor MCP de **Testar o aplicativo** e use a **URL do Servidor** com **Sem Autenticação** |
| O plug-in se conecta, mas não pode invocar ações | Confirme se o plug-in está anexado ao bate-papo, as ações são expostas ao modelo e a versão mais recente é implantada |
| O plug-in usa o ambiente errado | Editar ou recriar o plug-in com o URL do servidor de Preparo ou Produção MCP pretendido |

Se o problema persistir, registre o nome do aplicativo, o ambiente, a etapa com falha, o horário e a mensagem de erro visível antes de entrar em contato com a equipe do Beta. Não inclua segredos ou dados confidenciais do cliente.
