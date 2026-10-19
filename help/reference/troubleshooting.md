---
title: Solução de problemas de aplicativos Adobe LLM
description: Resolva problemas comuns de infraestrutura, geração de ação, manipulador, widget, implantação, autenticação e conexão da plataforma LLM.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1350'
ht-degree: 0%
---

# Resolução de problemas {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Comece com o sintoma observável. Não compartilhe credenciais, tokens, URLs de MCP privados ou resultados confidenciais de manipuladores durante a solução de problemas.

## Criação e geração de ação do aplicativo

| Sintoma | O que tentar |
|---------|-------------|
| Falha na configuração da infraestrutura gerenciada da Adobe | Leia o erro em **Falha na instalação** e selecione **Repetir**. Se a página não puder verificar o progresso, selecione **Verificar novamente**; a instalação continua em segundo plano |
| Nenhum programa do AEM está disponível | Solicite acesso ao programa ao administrador. Se o carregamento falhar, selecione **Repetir carregamento de programas** |
| Novos repositórios não são exibidos | Use **Conectar outro repositório** para conceder acesso ao Aplicativo GitHub para Aplicativos Adobe LLM, retornar ao assistente e selecione **Atualizar organizações e repositórios** |
| Falha na validação do repositório de ações ou widgets | Use um projeto existente compatível ou um repositório vazio. Use repositórios separados para ações e widgets e selecione **Validar repositório** novamente |
| A sincronização de código do AEM não foi detectada | Instale a Sincronização de código do AEM para o repositório de widgets, adicione a si mesmo como administrador do site em sua página de confirmação, retorne ao assistente e selecione **Validar instalação da Sincronização de Código do AEM** |
| A criação do aplicativo não gerou ações | A criação do aplicativo só configura a infraestrutura. Na página do aplicativo, selecione **Criar ações automaticamente**, insira **Seu site** e selecione **Criar minhas ações**. Consulte [Criar ações](/help/guides/creating-actions.md) |
| As ações ainda estão sendo geradas | A interface do usuário estima até 30 minutos para geração. Siga o progresso na página Ações ou saia da página e retorne posteriormente |
| Falha na geração de relatórios de ação | Confirme se o site é público por HTTPS e, para seus próprios repositórios, se o acesso ao repositório ainda é válido. Entre em contato com a equipe do Beta com a mensagem de erro visível se o problema persistir |

## Ações e manipuladores

| Sintoma | O que tentar |
|---------|-------------|
| A ação não foi invocada | Habilite o plug-in ou conector do aplicativo na conversa. Confirme se a opção **Expor ao modelo de IA** nas configurações de ação e a opção **Visibilidade** da ação na página do aplicativo estão ativadas. Melhorar a descrição e reimplantar alterações de metadados |
| Resposta vazia ou de erro | Execute `npm test` e chame o manipulador com o Inspetor MCP ou `curl`. Consulte [Desenvolvimento e teste de manipulador local](/help/reference/development.md) |
| O manipulador funciona localmente, mas não após a implantação | Verifique a confirmação enviada mais recentemente, confirme a configuração de tempo de execução e verifique se o identificador do código de ação corresponde a `actions/<code-identifier>/index.js` |
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

## Autenticação {#authentication}

Aplica-se quando **[!UICONTROL Habilitar autenticação]** está ativado. Consulte [Autenticar usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md).

| Sintoma | O que tentar |
|---------|-------------|
| As ações ainda são públicas depois de salvar as configurações | Implante o aplicativo novamente nesse ambiente. As alterações de autenticação têm efeito na próxima implantação |
| As configurações parecem incorretas após alternar os ambientes | Confirme se o seletor de **[!UICONTROL Workspace]** mostra o ambiente pretendido. O **[!UICONTROL Estágio]** e a **[!UICONTROL Produção]** estão configurados independentemente |
| A entrada não inicia | Confirme que você implantou o aplicativo após habilitar a autenticação e que a ação usa **[!UICONTROL Obrigatório]**. Uma ação **[!UICONTROL Opcional]** só é solicitada quando seu manipulador solicita entrada |
| A plataforma envia o usuário para a página de entrada errada | Verifique se **[!UICONTROL Emissor]** corresponde exatamente à URL do emissor do seu provedor de identidade e se ela pode ser acessada por HTTPS público |
| O logon foi bem-sucedido, mas todas as chamadas ainda foram rejeitadas | Confirme se o provedor de identidade emite tokens cujo público-alvo é o URL do servidor MCP do aplicativo para esse ambiente e que o token é um JWT assinado com um algoritmo assimétrico. Consulte [Requisitos de token](/help/reference/authentication-reference.md#token-requirements) |
| O provedor de identidade nunca recebe tráfego | A descoberta, a autorização e os endpoints do token do seu provedor devem ser acessíveis por HTTPS público. Verifique um firewall, WAF ou incluo na lista de permissões IP na frente dele — seu aplicativo pode ser acessado enquanto seu provedor não estiver |
| O provedor de identidade se recusa a emitir um token para o público solicitado | Alguns provedores só emitem tokens para um recurso que está registrado com eles. Confirme se o URL do servidor MCP do aplicativo está registrado como um identificador de recurso em seu provedor de identidade |
| Um escopo recém-adicionado não é solicitado na entrada | As plataformas LLM armazenam em cache os metadados publicados do aplicativo por alguns minutos. Aguarde e tente fazer logon novamente |
| O usuário é solicitado a fazer logon novamente para obter um escopo | A ação requer um escopo que o token não carrega. Adicionar o escopo para a concessão do cliente no seu provedor de identidade ou removê-lo da ação |
| O usuário recebe prompts de entrada repetidos | Um manipulador que retorna `extra.challengeAuth()` em cada chamada sem verificar a identidade do chamador solicita a entrada novamente após a autenticação. Solicitar autenticação somente quando a identidade necessária à ação estiver ausente |
| O logon é rejeitado antes que o usuário chegue ao seu provedor de identidade | O URI de redirecionamento que a plataforma LLM enviou não está registrado no servidor de autorização. Algumas plataformas emitem uma diferente para cada conector, portanto, registre o valor mostrado na tela de configuração do conector |
| Salvar está bloqueado com uma mensagem de escopo sem suporte | Adicione o escopo a **[!UICONTROL Escopos com suporte]** ou remova-o da ação que o exige |
| Uma ação pública ainda solicita a entrada em [!DNL Claude] | Verifique se o conector usa **[!UICONTROL Entrar quando necessário]**, não **[!UICONTROL Entrar agora]**. Para um aplicativo implantado mais antigo, atualize o tempo de execução e reimplante. Um aplicativo misto no tempo de execução atual deixa ações públicas chamadas anonimamente |
| Uma ação **[!UICONTROL Opcional]** retorna o texto de entrada, mas nenhum prompt de entrada Claude | `extra.challengeAuth()` é um desafio de resultado de ferramenta ChatGPT. Para uma porta de entrada portátil, use uma ação **[!UICONTROL Obrigatório]** para que o tempo de execução retorne o desafio HTTP que Claude espera |
| O aplicativo solicita entrada mesmo que cada ação seja **[!UICONTROL Nenhuma]** | Desative **[!UICONTROL Habilitar autenticação]** e implante. Enquanto estiver ativado, o aplicativo anuncia um servidor de autorização mesmo quando nenhuma ação é bloqueada |

Não cole tokens de acesso, conjuntos de declarações ou segredos de cliente do provedor de identidade em uma solicitação de suporte.

## Plug-ins do ChatGPT

| Sintoma | O que tentar |
|---------|-------------|
| O plug-in não aparece | Abra [chatgpt.com/plugins](https://chatgpt.com/plugins), confirme se sua conta ou espaço de trabalho permite servidores MCP personalizados e instale ou conecte o plug-in resultante |
| Falha na criação do plug-in | Copie novamente a URL completa do servidor MCP HTTPS de **Teste o aplicativo** e escolha o método de autenticação que corresponda ao aplicativo. Use **Sem autenticação** somente para um aplicativo público, não como solução alternativa para OAuth com falha |
| O plug-in se conecta, mas não pode invocar ações | Confirme se o plug-in está anexado ao bate-papo, as ações são expostas ao modelo e a versão mais recente é implantada |
| O plug-in usa o ambiente errado | Editar ou recriar o plug-in com o URL do servidor de Preparo ou Produção MCP pretendido |
| O plug-in ainda mostra metadados de ação antigos | Implante as alterações, selecione **Atualizar** na conexão ChatGPT e inicie uma nova conversa |

Se o problema persistir, registre o nome do aplicativo, o ambiente, a etapa com falha, o horário e a mensagem de erro visível antes de entrar em contato com a equipe do Beta. Não inclua segredos ou dados confidenciais do cliente.
