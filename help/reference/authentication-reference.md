---
title: Referência de autenticação
description: Definições de campo, requisitos de token, endpoints de descoberta, API do manipulador e comportamento da plataforma LLM para autenticação do usuário final em aplicativos Adobe LLM.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1260'
ht-degree: 2%
---

# Referência de autenticação {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use esta página para pesquisar campos e contratos de autenticação. Para obter a jornada de instalação, consulte [Autenticar usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md).

## Configurações de autenticação {#authentication-settings}

Encontrado em **[!UICONTROL Configurações]** > **[!UICONTROL Autenticação]**. Todos os campos são armazenados por ambiente — o seletor de **[!UICONTROL Workspace]** seleciona o que você está editando, e salvar nunca afeta o outro.

| Texto | Obrigatório | Descrição |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | A qual ambiente estas configurações se aplicam: **[!UICONTROL Preparo]** ou **[!UICONTROL Produção]** |
| **[!UICONTROL Habilitar autenticação]** | — | Chave mestre. Quando desativado, cada ação é pública, independentemente do modo de autenticação |
| **[!UICONTROL Emissor]** | Sim | A URL do emissor do seu provedor de identidade e a declaração `iss` esperada. Deve ser HTTPS. Também publicado como o servidor de autorização deste aplicativo. Um provedor de identidade por aplicativo |
| **[!UICONTROL Escopos com suporte]** | Não | O conjunto completo de escopos que as ações deste aplicativo podem exigir. Publicado nas plataformas LLM como escopos compatíveis do aplicativo |
| **[!UICONTROL URI JWKS]** | Não | Avançado. O URL HTTPS do conjunto de chaves de assinatura. Necessário somente quando diferente do que os metadados do servidor de autorização anunciam |

### Regras de validação

| Regra | Efeito |
|------|--------|
| **[!UICONTROL Emissor]** está vazio enquanto **[!UICONTROL Habilitar autenticação]** está ativado | Salvamento bloqueado |
| **[!UICONTROL Emissor]** ou **[!UICONTROL URI JWKS]** não é uma URL HTTPS | Salvamento bloqueado |
| Uma ação requer um escopo ausente de **[!UICONTROL Escopos com suporte]** | Salvar estará bloqueado até que você adicione o escopo ou remova-o da ação |
| Um escopo foi removido de **[!UICONTROL Escopos com suporte]** | Ele é removido de todas as ações necessárias, imediatamente, sem esperar um salvamento |
| **[!UICONTROL Escopos com suporte]** está vazio | Nenhum escopo pode ser concedido, portanto, qualquer escopo já em uma ação é removido. Nenhum aviso é exibido neste caso |
| `offline_access` está listado em **[!UICONTROL Escopos com suporte]** ou em uma ação | Removido quando o aplicativo é implantado, independentemente do espaço em branco ao redor ou da letra maiúscula, para que a página de configurações possa mostrar um escopo que o aplicativo implantado não tem. `offline_access` solicita um token de atualização do seu servidor de autorização em vez de conceder acesso a este aplicativo, portanto, não é um escopo anunciado por este aplicativo. Não é necessário listá-lo — a plataforma LLM o solicita diretamente do seu servidor de autorização |

As barras finais em **[!UICONTROL Emissor]** estão normalizadas, e a comparação `iss` tolera a diferença — um provedor que sempre emite uma barra à direita ainda valida.

## Modos de autenticação {#auth-modes}

Definido por ação em **[!UICONTROL Configuração por ação]**.

| Modo | Token obrigatório | Manipulador recebe identidade | Anunciado na plataforma como |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Nenhum]** | Não | Somente quando o chamador fornece um token válido | `noauth` |
| **[!UICONTROL Obrigatório]** | Sim, com cada escopo listado | Sempre | `oauth2` |
| **[!UICONTROL Opcional]** | Não | Quando um token válido estiver presente | `noauth` e `oauth2` |

Um manipulador de ação **[!UICONTROL Obrigatório]** nunca é executado sem um token válido e com escopo correto. Um manipulador da ação **[!UICONTROL Opcional]** sempre é executado e pode solicitar a entrada com `extra.challengeAuth()`.

**[!UICONTROL Obrigatório]** é, portanto, o único modo que recusa chamadores não autenticados. Um aplicativo é totalmente restrito somente quando cada uma de suas ações é **[!UICONTROL Obrigatório]**; uma única ação **[!UICONTROL Nenhum]** ou **[!UICONTROL Opcional]** torna o aplicativo misto, porque chamadas anônimas ainda são bem-sucedidas para pelo menos uma ação.

Os modos de autenticação só estão em vigor quando **[!UICONTROL Habilitar autenticação]** está ativado. As alterações entrarão em vigor na próxima implantação do aplicativo.

Alternar esse switch reescreve os modos por ação:

| Mudar | Efeito nos modos por ação |
|---------------|----------------------------|
| Desativado para ativado | Toda ação **[!UICONTROL Nenhuma]** torna-se **[!UICONTROL Obrigatória]**. As ações que já são **[!UICONTROL Obrigatórias]** ou **[!UICONTROL Opcionais]** mantêm seu modo |
| Ligado a desligado | O modo e os escopos de cada ação são limpos para esse ambiente. A configuração não será restaurada se você ligar o switch novamente |

A alternância do **[!UICONTROL Workspace]** nunca regrava os modos — ela carrega a configuração salva do outro ambiente como está.

**[!UICONTROL Habilitar autenticação]** em com cada ação definida como **[!UICONTROL Nenhuma]** é uma combinação válida, mas inerte: nenhuma chamada é recusada, mas o aplicativo ainda publica seu servidor de autorização para descoberta. Desligue o botão para tornar o aplicativo totalmente público.

Os modos podem ser misturados livremente em um aplicativo. Consulte [Comportamento da plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para saber como cada plataforma as aplica.

## Requisitos de token {#token-requirements}

Seu provedor de identidade deve emitir tokens de acesso que satisfaçam a todos os itens a seguir. Um token reprovado em qualquer verificação é tratado como ausente — o chamador não é autenticado e uma ação **[!UICONTROL Obrigatório]** desafia-o a entrar.

| Requisito | Detalhe |
|-------------|--------|
| Formato | JWT assinado. Não há suporte para tokens opacos |
| Algoritmo de assinatura | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` ou `PS512`. Algoritmos HMAC como `HS256` são rejeitados |
| `iss` | Deve corresponder a **[!UICONTROL Emissor]** |
| `aud` | Deve conter o identificador de recursos do aplicativo — o URL do servidor MCP desse ambiente |
| `exp` | Deve estar no futuro |
| `scope` ou `scp` | String delimitada por espaço ou uma matriz de strings. Fornece os escopos verificados em relação aos requisitos de cada ação |
| `sub` | O identificador de usuário que seu manipulador lê através de `getAuthenticatedUser` |
| Transporte | `Authorization: Bearer <token>` cabeçalho da solicitação |

Quaisquer declarações simples adicionais incluídas pelo seu provedor — por exemplo, `tenant` ou `email` — são passadas para o seu manipulador. Os objetos aninhados são descartados e os valores de sequência longa são truncados.

## Descoberta do provedor de identidade {#discovery}

Seu aplicativo publica seus próprios metadados de recursos protegidos do [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) para que as plataformas LLM possam localizar seu servidor de autorização. Você não cria, hospeda ou configura nada para ele.

Você deve fornecer a descoberta por seu próprio lado:

| Requisito | Detalhe |
|-------------|--------|
| Metadados do servidor de autorização | Seu emissor deve atender aos seus próprios metadados [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414), ou Descoberta [!DNL OpenID Connect], no caminho `/.well-known/`. Seu aplicativo o lê para localizar suas chaves de assinatura |
| Um emissor com um caminho | O segmento conhecido segue antes do caminho, não depois dele. Um emissor em `https://auth.example.com/oauth2/default` serve seus metadados em `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| Chaves hospedadas em outro lugar | Defina o **[!UICONTROL URI JWKS]** quando suas chaves de assinatura não estiverem onde os metadados as anunciam |

## API de autenticação do manipulador {#handler-auth-api}

Exportado de `@adobe/llm-apps-runtime`. Cada auxiliar recebe `extra`, o segundo argumento que seu manipulador recebe.

| Auxiliar | Devoluções |
|--------|---------|
| `getAuthenticatedUser(extra)` | A declaração `sub` do usuário conectado, ou `undefined` quando a chamada não é autenticada |
| `hasScope(extra, scope)` | `true` quando o token do chamador carrega `scope` |

As informações brutas do token verificado estão em `extra.authInfo`, que é `undefined` para uma chamada não autenticada.

| Propriedade | Descrição |
|----------|-------------|
| `authInfo.token` | O token bruto do portador. Não o registre nem retorne ao cliente |
| `authInfo.clientId` | A declaração `client_id` ou `azp`, ou `unknown` |
| `authInfo.scopes` | Matriz de escopos concedidos |
| `authInfo.expiresAt` | Expiração do token, como a declaração `exp` |
| `authInfo.resource` | O identificador de recurso do aplicativo no qual o token foi validado |
| `authInfo.extra` | `sub` mais qualquer outra declaração simples incluída por seu provedor de identidade |

`extra.challengeAuth(options)` está disponível somente nas ações **[!UICONTROL Opcional]**. Retorne o resultado do manipulador para solicitar que o usuário entre, em vez de retornar o conteúdo.

| Opção | Descrição |
|--------|-------------|
| `error` | Um código de erro de portador [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750): `invalid_token`, `insufficient_scope` ou `invalid_request`. O padrão é `insufficient_scope` |
| `errorDescription` | A mensagem mostrada ao usuário. O padrão é um prompt de entrada genérico |
| `scope` | Escopos delimitados por espaço a serem solicitados. Omita-a para permitir que a plataforma retorne aos escopos compatíveis do aplicativo |

>[!IMPORTANT]
>
>Sempre definir `error` explicitamente. Use `invalid_token` para um chamador sem sessão válida e `insufficient_scope` somente para um chamador cujo token é válido, mas não tem um escopo necessário. O valor é transmitido para a plataforma do LLM, que decide por si mesmo como escrever o prompt que mostra ao usuário. Envie o código que descreve com precisão a condição em vez da condição cujo prompt você prefere.

## Comportamento da plataforma LLM {#platform-behavior}

O suporte para autenticar ações individuais varia de acordo com a plataforma. Configure da mesma forma para ambos; a diferença é o que o usuário experiencia.

| Comportamento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Granularidade | Por ação | Por conector |
| Autenticação mista, quando o aplicativo não está totalmente fechado | Compatível. Somente as ações **[!UICONTROL Necessárias]** solicitam entrada | Não suportado. Todo o conector solicita entrada, incluindo as ações não atendidas |
| Configuração do conector | Definir **[!UICONTROL Autenticação]** como **[!UICONTROL Sem Autenticação]** quando cada ação for **[!UICONTROL Nenhuma]**, **[!UICONTROL OAuth]** quando cada ação for **[!UICONTROL Necessária]** e **[!UICONTROL Mista]** caso contrário | Nenhuma escolha de autenticação a ser feita; a entrada começa em **[!UICONTROL Conectar]** |
| Reautenticação | Solicitado na conversa quando uma ação fechada é chamada | Solicitado pelo conector |


## Relacionados

- [Autentique usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md)
- [Campos de ação e widget](/help/reference/reference-docs.md)
- [Resolução de problemas](/help/reference/troubleshooting.md#authentication)
