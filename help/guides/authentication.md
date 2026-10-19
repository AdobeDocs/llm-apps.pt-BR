---
title: Autentique usuários finais com seu próprio provedor de identidade
description: Ative a autenticação de usuário final para seu aplicativo Adobe LLM para que uma plataforma LLM compatível faça com que o usuário entre com seu provedor de identidade antes de chamar ações protegidas.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# Autentique usuários finais com seu próprio provedor de identidade {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Por padrão, cada ação no aplicativo é pública. Qualquer plataforma LLM com o URL do servidor MCP pode chamar essas ações e o manipulador não pode identificar o usuário final.

Ative a autenticação quando uma ação precisar saber qual usuário final está solicitando — por exemplo, para retornar seus pedidos, direitos ou detalhes da conta. A plataforma LLM faz com que o usuário entre com o **seu** provedor de identidade (IdP), envia o token de acesso resultante com cada chamada e seu manipulador recebe a identidade verificada.

**Neste guia:** Saiba como conectar seu provedor de identidade e escolher quais ações exigem entrada. Use a identidade verificada em seus manipuladores, implante o aplicativo e teste as ações de logon e protegidas.

A configuração da autenticação é uma tarefa avançada. Conclua primeiro a [Configuração de infraestrutura](/help/guides/create-app.md), [Criar ações](/help/guides/creating-actions.md) e [Implantar seu aplicativo](/help/guides/deploy-your-app.md).

## Como funciona

Você traz seu próprio provedor de identidade. O aplicativo implantado é apenas um **servidor de recursos** do OAuth 2.1 — ele verifica os tokens emitidos pelo servidor de autorização. Ele nunca emite tokens e o [!DNL Adobe] nunca armazena a ID do cliente ou o segredo do cliente.

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

Configurar autenticação **por ambiente**. O **[!UICONTROL Preparo]** e a **[!UICONTROL Produção]** contêm configurações independentes. Verifique a configuração em relação a um locatário IdP de desenvolvimento antes de habilitar a autenticação na **[!UICONTROL Produção]**.

## Antes de começar

- Um provedor de identidade OAuth 2.1 ou OpenID Connect que emite tokens de acesso **JWT** assinados com um algoritmo assimétrico. Não há suporte para tokens opacos e tokens assinados por HMAC. Consulte [Requisitos de token](/help/reference/authentication-reference.md#token-requirements).
- Acesso de administrador a esse provedor de identidade para registrar uma API e um cliente.
- Seu aplicativo foi implantado pelo menos uma vez no ambiente que você está configurando. O URL do servidor MCP implantado é o valor para o qual os tokens devem ter escopo.

## Copiar o identificador de recurso

O **identificador de recursos** do seu aplicativo é a URL do servidor MCP. Todos os tokens de acesso com problemas do provedor de identidade do aplicativo devem nomear esse URL exato como público-alvo. Essa associação de público impede o uso de um token emitido para outro serviço.

1. Abra a página Detalhes do aplicativo.
2. Role para **[!UICONTROL Testar o aplicativo]**.
3. No ambiente que você está configurando, selecione **[!UICONTROL Copiar URL]**.

![Detalhe do Aplicativo — copie a URL do servidor MCP de preparo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Mantenha o URL do servidor MCP copiado para a configuração do provedor de identidade na próxima etapa. Cole o URL em vez de digitá-lo novamente. A verificação de público-alvo requer uma correspondência exata de sequência, incluindo o caminho. Uma única diferença de caractere faz com que cada token falhe na validação.

>[!NOTE]
>
>Cada ambiente tem seu próprio URL de servidor MCP e, portanto, seu próprio público-alvo. Configure o **[!UICONTROL Preparo]** e a **[!UICONTROL Produção]** separadamente.

## Configurar o provedor de identidade

As etapas variam de acordo com o provedor. Configure os quatro itens a seguir.

1. **Registre seu aplicativo como uma API (recurso).** Defina seu identificador — o valor que o provedor coloca na declaração `aud` do token — para a URL de servidor MCP copiada. Os provedores geralmente rotulam este campo *Identificador* ou *Público*. Use um identificador exclusivo do aplicativo, não um valor genérico como `api`, para impedir o uso de tokens emitidos para outro serviço.
2. **Defina os escopos** que protegem suas ações, por exemplo `orders:read` ou `profile:read`. Use um escopo por permissão significativa para que cada ação solicite somente o acesso necessário.
3. **Suporte a PKCE.** As plataformas LLM enviam um `code_challenge` com `code_challenge_method=S256` em cada solicitação de autorização. Portanto, seu servidor de autorização deve dar suporte a S256 PKCE e anunciar `"code_challenge_methods_supported": ["S256"]` em seus metadados.
4. **Escolha como a plataforma LLM identifica seu cliente OAuth.** Use os Documentos de metadados de ID do cliente (CIMD) se o provedor suportar os requisitos da plataforma, o Registro dinâmico de cliente (DCR) ou um cliente OAuth pré-registrado. Forneça uma ID de cliente e um segredo somente quando o método escolhido exigi-los. Registre o URI de redirecionamento exato documentado ou exibido pela plataforma; para as superfícies hospedadas de [!DNL Claude] que é `https://claude.ai/api/mcp/auth_callback`. [!DNL ChatGPT] pode usar um retorno de chamada estável ou específico do conector, dependendo dos recursos anunciados do provedor. Copie o URI exibido em vez de adivinhar. Consulte a [documentação de autenticação do ChatGPT](https://developers.openai.com/plugins/build/auth) e a [documentação de autenticação do Claude](https://claude.com/docs/connectors/building/authentication).

>[!IMPORTANT]
>
>O emissor do seu provedor de identidade, JWKS, autorização e endpoints do token devem estar acessíveis por HTTPS público. Tanto a plataforma LLM quanto o aplicativo implantado buscam metadados diretamente do provedor. Um firewall VPN, incluo na lista de permissões IP ou firewall de aplicativo da Web que bloqueia essas solicitações impede a entrada, mesmo que o próprio aplicativo permaneça acessível.

## Ativar autenticação

1. Na navegação à esquerda, selecione **[!UICONTROL Configurações]** e abra a guia **[!UICONTROL Autenticação]**.
2. No **[!UICONTROL Workspace]**, escolha **[!UICONTROL Estágio]** ou **[!UICONTROL Produção]**.
3. Ativar **[!UICONTROL Habilitar autenticação]**.
4. Em **[!UICONTROL Configurações principais]**, digite:
   - **[!UICONTROL Emissor]** — a URL do emissor HTTPS do seu provedor de identidade, que também é o valor que ele coloca na declaração `iss` de cada token. A plataforma exige esse URL e publica o emissor como o servidor de autorização do aplicativo para a descoberta de cliente LLM. Cada aplicativo oferece suporte a um provedor de identidade.
   - **[!UICONTROL Escopos com suporte]** — todos os escopos que as ações deste aplicativo podem exigir. Espelhar os escopos definidos no provedor de identidade.
5. **[!UICONTROL Configurações avançadas]** é opcional. Defina o **[!UICONTROL URI JWKS]** somente quando suas chaves de assinatura não estiverem onde os metadados do servidor de autorização os anunciam; caso contrário, o aplicativo os descobrirá automaticamente.
6. Selecione **[!UICONTROL Salvar]**.

![Autenticação — habilite a autenticação e conclua as configurações principais](/help/assets/guide-authentication/auth-core-settings.png)

Para obter os requisitos do campo, consulte [Configurações de autenticação](/help/reference/authentication-reference.md#authentication-settings).

## Escolha um modo de autenticação para cada ação

Quando você ativa a **[!UICONTROL Habilitar autenticação]**, cada ação atualmente definida como **[!UICONTROL Nenhuma]** é alterada para **[!UICONTROL Necessária]**. Em **[!UICONTROL Configuração por ação]**, revise essa atribuição e defina o modo de que cada ação precisa:

| Modo | Comportamento |
|------|----------|
| **[!UICONTROL Nenhum]** | Público. Os chamadores não precisam de um token. |
| **[!UICONTROL Obrigatório]** | Protegido. Os chamadores precisam de um token válido que carregue cada escopo listado. O tempo de execução desafia os chamadores não autenticados a entrar. |
| **[!UICONTROL Opcional]** | Chamável anonimamente, mas a ação também anuncia que oferece suporte para logon. Seu manipulador decide, por chamada, se deseja fornecer um resultado genérico ou solicitar que o usuário entre para obter um resultado personalizado. |

![Autenticação — defina um modo de autenticação e escopos para cada ação](/help/assets/guide-authentication/auth-per-action.png)

As ações já definidas como **[!UICONTROL Obrigatórias]** ou **[!UICONTROL Opcionais]** mantêm o modo existente.

Para uma ação **[!UICONTROL Obrigatória]** ou **[!UICONTROL Opcional]**, adicione os **[!UICONTROL Escopos]** necessários. Todos os escopos já devem aparecer em **[!UICONTROL Escopos com suporte]** para que o aplicativo anuncie cada permissão necessária para plataformas LLM. A plataforma bloqueia o salvamento até que você resolva qualquer incompatibilidade de escopo.

**[!UICONTROL Escopos com suporte]** controla os escopos disponíveis. A remoção de um escopo dessa lista remove imediatamente o escopo de cada ação que o exigiu. Adicione cada escopo à lista antes de atribuir o escopo a uma ação.

Selecionar **[!UICONTROL Exigir autenticação em todas as ações]** altera as ações **[!UICONTROL Nenhuma]** para **[!UICONTROL Obrigatório]**, mas preserva as ações já definidas como **[!UICONTROL Opcional]**. Para exigir a entrada em cada ação, altere as **[!UICONTROL Opcionais]** linhas restantes para **[!UICONTROL Obrigatório]** individualmente. Desmarcar a caixa de seleção retorna cada ação como **[!UICONTROL Nenhuma]**.

Selecione **[!UICONTROL Salvar]** quando terminar. As alterações no modo de autenticação e no escopo são salvas junto com as configurações no nível do aplicativo.

>[!IMPORTANT]
>
>Desativar a **[!UICONTROL Habilitação de autenticação]** limpa o modo de autenticação e os escopos de cada ação para o ambiente selecionado. A plataforma não retém essas configurações. Ativar a autenticação novamente define todas as ações como **[!UICONTROL Obrigatório]**.

>[!NOTE]
>
>Configurar cada ação como **[!UICONTROL Nenhuma]** não desabilita a autenticação. Nenhuma ação requer logon nesse estado, mas o aplicativo ainda anuncia seu servidor de autorização para plataformas LLM. Um cliente pode oferecer um logon que não concede acesso adicional. Para tornar o aplicativo totalmente público, desative a **[!UICONTROL Habilitar autenticação]** e implante.

O [!DNL ChatGPT] oferece suporte a modos de autenticação mistos em um aplicativo e aplica o modo de cada ação individualmente. As ações públicas não solicitam o logon; as ações protegidas solicitam, conforme necessário.

[!DNL Claude] também oferece suporte a aplicativos mistos por meio de **autenticação lenta**. Com **[!UICONTROL Entrar quando necessário]** selecionado durante a configuração do conector, as ações públicas permanecem utilizáveis anonimamente e a plataforma solicita entrada quando uma ação **[!UICONTROL Obrigatório]** é chamada. Um aplicativo cujas ações são todas **[!UICONTROL Obrigatórias]** requer entrada antes do uso. Consulte [Comportamento da plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para obter a distinção entre uma ação necessária e um manipulador opcional solicitando entrada.

>[!NOTE]
>
>O tempo de execução atual suporta esse comportamento de autenticação mista, verificado em relação à versão 1.1.5. Aplicativos implantados mais antigos podem exigir uma atualização e reimplantação em tempo de execução. Algumas compilações de interface ainda mostram um aviso herdado de que o Claude requer autenticação para todo o conector. Verifique o tempo de execução implantado e use a opção **[!UICONTROL Entrar quando necessário]** atual do Claude, em vez de depender do aviso.

## Implantar a alteração

As alterações de autenticação têm efeito na próxima implantação do aplicativo. **Implante o aplicativo novamente** no ambiente que você configurou. Consulte [Implantar seu aplicativo](/help/guides/deploy-your-app.md).

A URL do servidor MCP não é alterada. Atualize os metadados da conexão e verifique se suas configurações de autenticação correspondem à nova configuração do aplicativo. Uma plataforma pode exigir que você reconecte ou recrie a conexão quando seu método de autenticação mudar.

## Ler a identidade no seu manipulador

Uma identidade verificada atinge seu manipulador como o segundo argumento sempre que o chamador envia um token válido, independentemente do modo de autenticação da ação. Uma ação **[!UICONTROL Opcional]** pode usar essa identidade para personalizar resultados quando disponíveis e retornar um resultado anônimo caso contrário.

Usar `getAuthenticatedUser` para ler o usuário conectado:

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

O tempo de execução verifica o token para você. Para uma ação **[!UICONTROL Obrigatório]**, ele bloqueia todas as chamadas que não tenham um token válido com os escopos listados. O manipulador é executado somente para um chamador autorizado. Use `hasScope` para escolher o comportamento do manipulador com base em uma permissão — por exemplo, em uma ação **[!UICONTROL Opcional]**.

Para solicitar entrada durante uma conversa, retorne `extra.challengeAuth()` do manipulador. O auxiliar está disponível somente em **[!UICONTROL Ações]** opcionais:

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

Use um parâmetro de entrada explícito, como `signIn` neste exemplo, para decidir quando solicitar autenticação. Não inferir essa decisão a partir do texto do usuário.

Defina `error` para corresponder à condição reportada. Use `invalid_token` quando o chamador não tiver uma sessão válida e precisar entrar, como no exemplo acima. Use `insufficient_scope` quando o chamador estiver conectado, mas o token não tiver um escopo necessário. Cada plataforma do LLM escolhe a redação de prompt e como responder ao código de erro. Envie o código que descreve com precisão a condição.

Solicite autenticação somente quando a identidade necessária estiver ausente, como faz a verificação `!extra.authInfo` neste exemplo. Fazer logon não resolve um desafio incondicional: o manipulador solicita a autenticação novamente em cada chamada.

>[!NOTE]
>
>`extra.challengeAuth()` retorna um desafio de resultado de ferramenta para [!DNL ChatGPT]. [!DNL Claude] precisa de um desafio de autenticação HTTP antes que o manipulador seja executado. Para uma operação que requer entrada entre plataformas com suporte, use uma ação **[!UICONTROL Obrigatório]**. Mantenha uma ação **[!UICONTROL Nenhuma]** separada para a experiência pública, se necessário.

Manter a identidade do lado do servidor. Transmita apenas os dados de que o widget precisa para `structuredContent`. Nunca inclua um token de acesso em `structuredContent`. Consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md).

Para obter o contrato completo, consulte [API de autenticação do manipulador](/help/reference/authentication-reference.md#handler-auth-api).

## Testar o aplicativo protegido

Seu plug-in ou conector existente usa a configuração atualizada após a implantação. Para criar uma nova conexão, siga as instruções para sua plataforma:

### [!DNL ChatGPT]

Na caixa de diálogo **[!UICONTROL Novo Plug-in]**, defina a **[!UICONTROL Autenticação]** para corresponder à forma como você configurou as ações do aplicativo:

| Ações do seu aplicativo | Selecionar |
|--------------------|--------|
| Tudo configurado como **[!UICONTROL Nenhum]** | **[!UICONTROL Sem Autenticação]** |
| Tudo definido como **[!UICONTROL Obrigatório]** | **[!UICONTROL OAuth]** |
| Qualquer outra combinação | **[!UICONTROL Misto]** |

Uma ação **[!UICONTROL Opcional]** sempre aceita chamadas anônimas, portanto, um aplicativo que contém uma chamada nunca é totalmente restringido — escolha **[!UICONTROL Misto]** mesmo que cada ação esteja definida como **[!UICONTROL Opcional]**. Somente **[!UICONTROL Obrigatório]** recusa chamadores não autenticados.

Consulte [Testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md) para o restante da caixa de diálogo.

### [!DNL Claude]

Adicione o conector personalizado e revise as configurações de autenticação. Para um aplicativo misto público/protegido, escolha **[!UICONTROL Fazer logon quando necessário]** quando disponível. Para solicitar a entrada antes do uso do conector, selecione **[!UICONTROL Entrar agora]**. A caixa de diálogo varia de acordo com a conta ou organização. Consulte [Testar o conector Claude](/help/guides/test-in-claude.md).

### Verificar

- A plataforma redireciona você para a página de logon do seu próprio provedor de identidade.
- Uma ação protegida retorna dados específicos do usuário após o logon.
- Uma ação protegida solicita que você entre quando sair.
- Em um aplicativo misto, uma ação definida como **[!UICONTROL Nenhuma]** ainda responde sem entrar. Em [!DNL Claude], verifique a ação pública com **[!UICONTROL Entrar quando necessário]**.
- Após fazer logon, tente novamente a ação protegida e confirme se o tempo de execução impõe as permissões esperadas.

Se a entrada não for iniciada ou um token for rejeitado, consulte [Solução de problemas](/help/reference/troubleshooting.md#authentication).

## Orientação de segurança

- Conceda o escopo mais restrito que cada ação precisa. Não reutilize um escopo amplo em cada ação.
- Mantenha o cliente em segredo no provedor de identidade e na configuração do conector da plataforma LLM. Nunca os coloque em metadados de ação, código do manipulador, JavaScript do widget ou controle do código-fonte.
- Tratar declarações de token como entrada de um sistema externo. Valide valores de `authInfo.extra` antes de usar esses valores em uma consulta.
- Autorizar e autenticar. Um token válido comprova a identidade do usuário, não a permissão para ler um registro específico. Verifique a propriedade no manipulador antes de retornar os dados.
- Não registre tokens, conjuntos completos de declarações ou identificadores de usuários.
- Retornar erros de segurança. Não mostre respostas do provedor de identidade upstream nem empilhe rastreamentos para o usuário.
- Configure e verifique o **[!UICONTROL Stage]** em relação a um locatário de provedor de identidade de não produção antes de habilitar a autenticação em **[!UICONTROL Production]**.

## O que vem a seguir

- [Referência de autenticação](/help/reference/authentication-reference.md) — campos, requisitos de token e comportamento da plataforma.
- [Personalizar um manipulador gerado](/help/guides/customize-handler.md) — chame uma API upstream protegida de um manipulador.
- [Implante seu aplicativo](/help/guides/deploy-your-app.md).
