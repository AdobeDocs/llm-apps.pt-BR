---
title: Autentique usuários finais com seu próprio provedor de identidade
description: Ative a autenticação de usuário final para seu aplicativo Adobe LLM para que uma plataforma LLM compatível faça com que o usuário entre com seu provedor de identidade antes de chamar ações protegidas.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# Autentique usuários finais com seu próprio provedor de identidade {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Por padrão, cada ação no aplicativo é pública: qualquer plataforma LLM com o URL do servidor MCP pode chamá-la e o manipulador não pode identificar o usuário final.

Ative a autenticação quando uma ação precisar saber qual usuário final está solicitando — por exemplo, para retornar seus pedidos, direitos ou detalhes da conta. A plataforma LLM faz com que o usuário entre com o **seu** provedor de identidade (IdP), envia o token de acesso resultante com cada chamada e seu manipulador recebe a identidade verificada.

**Jornada:** Copiar o identificador de recurso → configurar seu provedor de identidade → ativar autenticação → definir um modo de autenticação para cada ação → implantar → ler a identidade em seu manipulador → testar o aplicativo protegido.

Esta é uma ramificação avançada, que não faz parte da jornada de primeira execução. Conclua [Criar o primeiro aplicativo automaticamente](/help/guides/create-app.md) e [Implantar o aplicativo](/help/guides/deploy-your-app.md) primeiro.

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

A autenticação está configurada **por ambiente**. O **[!UICONTROL Preparo]** e a **[!UICONTROL Produção]** contêm configurações independentes, de modo que você pode verificar a configuração em relação a um locatário IdP de desenvolvimento antes de habilitá-lo na **[!UICONTROL Produção]**.

## Antes de começar

- Um provedor de identidade OAuth 2.1 ou OpenID Connect que emite tokens de acesso **JWT** assinados com um algoritmo assimétrico. Não há suporte para tokens opacos e tokens assinados por HMAC. Consulte [Requisitos de token](/help/reference/authentication-reference.md#token-requirements).
- Acesso de administrador a esse provedor de identidade, para que você possa registrar uma API e um cliente.
- Seu aplicativo foi implantado pelo menos uma vez no ambiente que você está configurando. O URL do servidor MCP implantado é o valor para o qual os tokens devem ter escopo.

## Copiar o identificador de recurso

O **identificador de recursos** do seu aplicativo é a URL do servidor MCP. Cada token de acesso que seu provedor de identidade emite para este aplicativo deve nomear esse URL exato como seu público-alvo; essa vinculação é o que impede que um token cunhado para outro serviço seja reproduzido no aplicativo.

1. Abra a página Detalhes do aplicativo.
2. Role para **[!UICONTROL Testar o aplicativo]**.
3. No ambiente que você está configurando, selecione **[!UICONTROL Copiar URL]**.

![Detalhe do Aplicativo — copie a URL do servidor MCP de preparo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Keep this value: você precisará dele no provedor de identidade na próxima etapa. Cole o URL copiado em vez de digitá-lo novamente. A verificação de público-alvo é uma correspondência de sequência exata, incluindo qualquer componente do caminho, portanto, uma única diferença de caractere faz com que cada token falhe na validação.

>[!NOTE]
>
>Cada ambiente tem seu próprio URL de servidor MCP e, portanto, seu próprio público-alvo. Configure o **[!UICONTROL Preparo]** e a **[!UICONTROL Produção]** separadamente.

## Configurar o provedor de identidade

As etapas exatas diferem de acordo com o provedor, mas cada provedor precisa das mesmas quatro coisas.

1. **Registre seu aplicativo como uma API (recurso).** Defina seu identificador — o valor que o provedor coloca na declaração `aud` do token — para o URL do servidor MCP copiado. Os provedores rotulam este campo de forma variada, normalmente *Identificador* ou *Público*. Não use um valor genérico como `api`; o identificador deve ser exclusivo para este aplicativo ou um token emitido para outro serviço pode ser reproduzido nele.
2. **Defina os escopos** com os quais deseja adicionar ações, por exemplo `orders:read` ou `profile:read`. Use um escopo por permissão significativa, para que uma ação solicite apenas o que for necessário.
3. **Suporte a PKCE.** As plataformas LLM enviam um `code_challenge` com `code_challenge_method=S256` em cada solicitação de autorização. Portanto, seu servidor de autorização deve dar suporte a S256 PKCE e anunciar `"code_challenge_methods_supported": ["S256"]` em seus metadados.
4. **Permitir que a plataforma LLM se registre como cliente.** As plataformas LLM compatíveis criam seu próprio cliente OAuth em relação ao servidor de autorização. Portanto, ative o registro dinâmico do cliente se o provedor oferecer. Caso contrário, crie um cliente público manualmente e forneça sua ID de cliente — e secreta, somente se seu provedor exigir autenticação confidencial de cliente — durante a configuração do conector na plataforma. Registre o URI de redirecionamento que a plataforma documenta; para as superfícies hospedadas de [!DNL Claude] que é `https://claude.ai/api/mcp/auth_callback`. Algumas plataformas emitem um URI de redirecionamento distinto para cada conector que o usuário cria, [!DNL ChatGPT] entre elas, portanto, leia o valor na tela de configuração do conector e registre-o antes da primeira entrada. Um URI de redirecionamento não registrado faz com que o servidor de autorização rejeite totalmente a solicitação de autorização.

>[!IMPORTANT]
>
>O emissor do seu provedor de identidade, JWKS, autorização e endpoints do token devem estar acessíveis por HTTPS público. Tanto a plataforma LLM quanto o aplicativo implantado buscam metadados diretamente do provedor, de modo que um provedor de identidade por trás de uma VPN ou um incluo na lista de permissões IP não pode concluir o logon. Um firewall ou firewall de aplicativo da Web na frente do seu provedor é uma causa comum, e pode interromper o fluxo mesmo quando o próprio aplicativo está acessível.

## Ativar autenticação

1. Na navegação à esquerda, selecione **[!UICONTROL Configurações]** e abra a guia **[!UICONTROL Autenticação]**.
2. No **[!UICONTROL Workspace]**, escolha **[!UICONTROL Estágio]** ou **[!UICONTROL Produção]**.
3. Ativar **[!UICONTROL Habilitar autenticação]**.
4. Em **[!UICONTROL Configurações principais]**, digite:
   - **[!UICONTROL Emissor]** — a URL do emissor do seu provedor de identidade, que também é o valor inserido na declaração `iss` de cada token. Isso é obrigatório, deve ser HTTPS e também é publicado como o servidor de autorização do seu aplicativo para que as plataformas LLM possam descobrir para onde enviar os usuários. Somente um provedor de identidade é compatível por aplicativo.
   - **[!UICONTROL Escopos com suporte]** — todos os escopos que as ações deste aplicativo podem exigir. Espelhar os escopos definidos no provedor de identidade.
5. **[!UICONTROL Configurações avançadas]** é opcional. Defina o **[!UICONTROL URI JWKS]** somente quando suas chaves de assinatura não estiverem onde os metadados do servidor de autorização os anunciam; caso contrário, o aplicativo os descobrirá automaticamente.
6. Selecione **[!UICONTROL Salvar]**.

![Autenticação — habilite a autenticação e conclua as configurações principais](/help/assets/guide-authentication/auth-core-settings.png)

Para saber o que cada campo aceita, consulte [Configurações de autenticação](/help/reference/authentication-reference.md#authentication-settings).

## Escolha um modo de autenticação para cada ação

Quando você ativa a **[!UICONTROL Habilitar autenticação]**, cada ação atualmente definida como **[!UICONTROL Nenhuma]** é alterada para **[!UICONTROL Necessária]**. Em **[!UICONTROL Configuração por ação]**, revise essa atribuição e defina o modo de que cada ação precisa:

| Modo | Comportamento |
|------|----------|
| **[!UICONTROL Nenhum]** | Público. A ação pode ser chamada sem um token. |
| **[!UICONTROL Obrigatório]** | Com portões. A ação só pode ser chamada com um token válido que contenha todos os escopos listados para ela. Chamadores não autenticados são desafiados a fazer logon. |
| **[!UICONTROL Opcional]** | Chamável anonimamente, mas a ação também anuncia que oferece suporte para logon. Seu manipulador decide, por chamada, se deseja fornecer um resultado genérico ou solicitar que o usuário entre para obter um resultado personalizado. |

![Autenticação — defina um modo de autenticação e escopos para cada ação](/help/assets/guide-authentication/auth-per-action.png)

As ações já definidas como **[!UICONTROL Obrigatórias]** ou **[!UICONTROL Opcionais]** mantêm o modo existente.

Para uma ação **[!UICONTROL Obrigatória]** ou **[!UICONTROL Opcional]**, adicione os **[!UICONTROL Escopos]** necessários. Todos os escopos já devem aparecer em **[!UICONTROL Escopos com suporte]** acima; caso contrário, o aplicativo exigirá uma permissão que não anunciará nas plataformas LLM. Salvamento bloqueado até que a incompatibilidade seja resolvida.

**[!UICONTROL Escopos com suporte]** é a autoridade para esta lista. Se você remover um escopo dele, esse escopo será removido de todas as ações que exigirem essa alteração assim que você fizer a alteração. Portanto, adicione um escopo lá primeiro e, em seguida, atribua-o a uma ação.

**[!UICONTROL Exigir autenticação em todas as ações]** define cada ação como **[!UICONTROL Necessária]**. Limpá-la retorna cada ação para **[!UICONTROL Nenhuma]**.

Selecione **[!UICONTROL Salvar]** quando terminar. As alterações no modo de autenticação e no escopo são salvas junto com as configurações no nível do aplicativo.

>[!IMPORTANT]
>
>A desativação da **[!UICONTROL Habilitação de autenticação]** descarta essa configuração por ação para o ambiente selecionado — o modo e os escopos de cada ação são limpos, não são lembrados. Ativar novamente começa novamente a partir de todos os-**[!UICONTROL Necessários]**.

>[!NOTE]
>
>Configurar cada ação como **[!UICONTROL Nenhuma]** não desabilita a autenticação. Nenhuma chamada é recusada nesse estado, mas o aplicativo ainda anuncia seu servidor de autorização para plataformas LLM, para que um cliente possa oferecer ao usuário uma entrada que não conceda acesso adicional. Para tornar o aplicativo totalmente público, desative a **[!UICONTROL Habilitar autenticação]** e implante.

A combinação de modos em um aplicativo — algumas ações públicas, outras restritas — é suportada, e [!DNL ChatGPT] aplica o modo de cada ação individualmente: somente as ações restritas solicitam que o usuário entre.

>[!IMPORTANT]
>
>[!DNL Claude] é a exceção. Ela aplica autenticação por conector em vez de por ação, portanto, se qualquer ação no aplicativo estiver definida como **[!UICONTROL Obrigatório]** ou **[!UICONTROL Opcional]**, [!DNL Claude] solicitará que o usuário entre antes de usar o conector, incluindo as ações definidas como **[!UICONTROL Nenhum]**. Para manter uma ação pública para [!DNL Claude] usuários, hospede-a em um aplicativo separado.

## Implantar a alteração

As alterações de autenticação têm efeito na próxima implantação do aplicativo. **Implante o aplicativo novamente** no ambiente que você configurou. Consulte [Implantar seu aplicativo](/help/guides/deploy-your-app.md).

O URL do servidor MCP não é alterado, portanto, qualquer plug-in ou conector já criado continua a funcionar. Agora ele está restrito, portanto, os usuários são solicitados a fazer logon na próxima vez que o usarem.

## Ler a identidade no seu manipulador

Uma identidade verificada atinge seu manipulador como o segundo argumento. Ele estará presente sempre que o chamador enviar um token válido, independentemente do modo de autenticação da ação, portanto uma ação **[!UICONTROL Opcional]** pode personalizar seu resultado quando um token estiver presente e ainda retornar um resultado quando não estiver.

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

Você não precisa verificar o token sozinho. Para uma ação **[!UICONTROL Obrigatório]**, o tempo de execução bloqueia todas as chamadas que não tenham um token válido carregando os escopos listados, portanto, o manipulador só é executado para um chamador autorizado. Use `hasScope` quando quiser ramificar em uma permissão em vez de depender da porta — por exemplo, em uma ação **[!UICONTROL Opcional]**.

Uma ação **[!UICONTROL Opcional]** pode solicitar que o usuário entre no meio da conversa retornando `extra.challengeAuth()`. Isto está disponível somente em **[!UICONTROL Ações]** opcionais:

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

Decida se escalará a partir de um parâmetro de entrada explícito, como `signIn` faz aqui, em vez de inspecionar o texto do usuário.

Defina `error` para corresponder à condição que você está relatando. Use `invalid_token` quando o chamador não tiver uma sessão válida e precisar entrar, como no exemplo acima, e `insufficient_scope` quando o chamador já estiver conectado, mas o token não tiver um escopo de que a ação precise. A plataforma LLM escolhe o texto do prompt que o usuário vê e a variação desse texto em relação a esse valor varia de acordo com a plataforma; portanto, envie o código que descreve a condição com precisão.

Desafie somente quando a identidade necessária estiver realmente ausente, como a verificação `!extra.authInfo` faz aqui. Um manipulador que desafia incondicionalmente não pode ser satisfeito ao fazer logon, portanto, o usuário é solicitado a se autenticar novamente em cada chamada.

>[!NOTE]
>
>Em [!DNL ChatGPT], uma entrada criada dessa maneira solicita que o usuário reconecte o conector, em vez de conceder uma permissão adicional. Em [!DNL Claude], o usuário entra antes da execução de qualquer ação, portanto, uma ação nunca precisa criar uma.

Manter a identidade do lado do servidor. Passe somente o que o widget precisa para `structuredContent`, e nunca coloque o token de acesso lá — consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md).

Para obter o contrato completo, consulte [API de autenticação do manipulador](/help/reference/authentication-reference.md#handler-auth-api).

## Testar o aplicativo protegido

Seu plug-in ou conector existente seleciona a alteração após a implantação. Para configurar um do zero:

### [!DNL ChatGPT]

Na caixa de diálogo **[!UICONTROL Novo Plug-in]**, defina a **[!UICONTROL Autenticação]** para corresponder à forma como você configurou as ações do aplicativo:

| Ações do seu aplicativo | Selecionar |
|--------------------|--------|
| Tudo configurado como **[!UICONTROL Nenhum]** | **[!UICONTROL Sem Autenticação]** |
| Tudo definido como **[!UICONTROL Obrigatório]** | **[!UICONTROL OAuth]** |
| Qualquer outra combinação | **[!UICONTROL Misto]** |

![ChatGPT — selecione o modo de autenticação para o plug-in](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

Uma ação **[!UICONTROL Opcional]** sempre aceita chamadas anônimas, portanto, um aplicativo que contém uma chamada nunca é totalmente restringido — escolha **[!UICONTROL Misto]** mesmo que cada ação esteja definida como **[!UICONTROL Opcional]**. Somente **[!UICONTROL Obrigatório]** recusa chamadores não autenticados.

Consulte [Testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md) para o restante da caixa de diálogo.

### [!DNL Claude]

Adicione o conector personalizado, selecione **[!UICONTROL Conectar]** e conclua a entrada que seu provedor de identidade apresenta. Não há opção de autenticação a ser feita — [!DNL Claude] portas o conector inteiro sempre que qualquer ação for fechada. Consulte [Testar o conector Claude](/help/guides/test-in-claude.md).

### Verificar

- A plataforma redireciona você para a página de logon do seu próprio provedor de identidade.
- Uma ação protegida retorna dados específicos do usuário após o logon.
- Uma ação protegida solicita que você entre quando sair.
- Em [!DNL ChatGPT], uma ação definida como **[!UICONTROL Nenhuma]** ainda responde sem entrar. Em [!DNL Claude], todo o conector está fechado.

Se a entrada não for iniciada ou um token for rejeitado, consulte [Solução de problemas](/help/reference/troubleshooting.md#authentication).

## Orientação de segurança

- Conceda o escopo mais restrito que cada ação precisa. Não reutilize um escopo amplo em cada ação.
- Mantenha o cliente em segredo no provedor de identidade e na configuração do conector da plataforma LLM. Nunca os coloque em metadados de ação, código do manipulador, JavaScript do widget ou controle do código-fonte.
- Tratar declarações de token como entrada de um sistema externo. Valide tudo o que você leu do `authInfo.extra` antes de usá-lo em uma consulta.
- Autorizar e autenticar. Um token válido comprova quem é o usuário, mas não que ele possa ver um registro específico — verifique a propriedade no manipulador antes de retornar os dados.
- Não registre tokens, conjuntos completos de declarações ou identificadores de usuários.
- Retornar erros de segurança. Não mostre respostas do provedor de identidade upstream nem empilhe rastreamentos para o usuário.
- Configure e verifique o **[!UICONTROL Stage]** em relação a um locatário de provedor de identidade de não produção antes de habilitar a autenticação em **[!UICONTROL Production]**.

## O que vem a seguir

- [Referência de autenticação](/help/reference/authentication-reference.md) — campos, requisitos de token e comportamento da plataforma.
- [Personalizar um manipulador gerado](/help/guides/customize-handler.md) — chame uma API upstream protegida de um manipulador.
- [Implante seu aplicativo](/help/guides/deploy-your-app.md).
