---
title: Referência de autenticação
description: Definições de campo, requisitos de token, endpoints de descoberta, API do manipulador e comportamento da plataforma LLM para autenticação do usuário final em aplicativos Adobe LLM.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1428'
ht-degree: 1%
---

# Referência de autenticação {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use esta página para pesquisar campos e contratos de autenticação. Para obter instruções de configuração, consulte [Autenticar usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md).

## Configurações de autenticação {#authentication-settings}

Abra **[!UICONTROL Configurações]** > **[!UICONTROL Autenticação]**. Cada ambiente armazena suas próprias configurações. Use o seletor de **[!UICONTROL Workspace]** para selecionar o ambiente a ser editado; salvar as configurações nunca afeta o outro ambiente.

| Texto | Obrigatório | Descrição |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | Ambiente para as configurações de autenticação: **[!UICONTROL Preparo]** ou **[!UICONTROL Produção]** |
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

Barras à direita em **[!UICONTROL Emissor]** estão normalizadas. A comparação `iss` tolera a diferença, portanto, os tokens de um provedor que sempre inclui uma barra à direita ainda são validados.

## Modos de autenticação {#auth-modes}

Definido por ação em **[!UICONTROL Configuração por ação]**.

| Modo | Token obrigatório | Manipulador recebe identidade | Anunciado na plataforma como |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Nenhum]** | Não | Somente quando o chamador fornece um token válido | `noauth` |
| **[!UICONTROL Obrigatório]** | Sim, com cada escopo listado | Sempre | `oauth2` |
| **[!UICONTROL Opcional]** | Não | Quando um token válido estiver presente | `noauth` e `oauth2` |

Um manipulador de ação **[!UICONTROL Obrigatório]** nunca é executado sem um token válido e com escopo correto. Um manipulador da ação **[!UICONTROL Opcional]** sempre é executado e pode solicitar a entrada com `extra.challengeAuth()`.

**[!UICONTROL Obrigatório]** é, portanto, o único modo que rejeita chamadores não autenticados. Um aplicativo rejeita todas as chamadas anônimas apenas quando cada ação é **[!UICONTROL Obrigatória]**. Se alguma ação for **[!UICONTROL Nenhuma]** ou **[!UICONTROL Opcional]**, o aplicativo usará autenticação mista porque pelo menos uma ação aceita chamadas anônimas.

Os modos de autenticação só têm efeito quando **[!UICONTROL Habilitar autenticação]** está ativado. As alterações entrarão em vigor na próxima implantação do aplicativo.

Alterar o switch de autenticação também reescreve os modos por ação:

| Mudar | Efeito nos modos por ação |
|---------------|----------------------------|
| Desativado para ativado | Toda ação **[!UICONTROL Nenhuma]** torna-se **[!UICONTROL Obrigatória]**. As ações que já são **[!UICONTROL Obrigatórias]** ou **[!UICONTROL Opcionais]** mantêm seu modo |
| Ligado a desligado | O modo e os escopos de cada ação são limpos para esse ambiente. A configuração não será restaurada se você ligar o switch novamente |

A alternância de **[!UICONTROL Workspace]** nunca regrava modos. O seletor carrega a configuração salva do outro ambiente sem alterá-la.

Com **[!UICONTROL Habilitar autenticação]** ativada e cada ação definida como **[!UICONTROL Nenhuma]**, o aplicativo não requer autenticação, mas ainda publica seu servidor de autorização para descoberta. Para tornar o aplicativo totalmente público, desligue o.

Um aplicativo pode usar diferentes modos de autenticação para ações individuais. Consulte [Comportamento da plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para saber como cada plataforma as aplica.

## Requisitos de token {#token-requirements}

Seu provedor de identidade deve emitir tokens de acesso que atendam a todos os requisitos a seguir. O tempo de execução trata um token com falha em qualquer verificação como ausente: o chamador não está autenticado e uma ação **[!UICONTROL Obrigatório]** desafia o chamador a entrar.

| Requisito | Detalhe |
|-------------|--------|
| Formato | JWT assinado. Não há suporte para tokens opacos |
| Algoritmo de assinatura | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` ou `PS512`. Algoritmos HMAC como `HS256` são rejeitados |
| `iss` | Deve corresponder a **[!UICONTROL Emissor]** |
| `aud` | Deve conter o identificador de recursos do aplicativo — o URL do servidor MCP desse ambiente |
| `exp` | Deve estar no futuro |
| `scope` ou `scp` | O tempo de execução lê uma cadeia de caracteres `scope` delimitada por espaços ou retorna para uma matriz de cadeias de caracteres em `scp`. Usar uma dessas formas para escopos verificados em relação aos requisitos de cada ação |
| `sub` | O identificador de usuário que seu manipulador lê através de `getAuthenticatedUser` |
| Transporte | `Authorization: Bearer <token>` cabeçalho da solicitação |

Quaisquer declarações simples adicionais incluídas pelo seu provedor — por exemplo, `tenant` ou `email` — são passadas para o seu manipulador. Os objetos aninhados são descartados e os valores de sequência longa são truncados.

## Descoberta do provedor de identidade {#discovery}

Seu aplicativo publica seus próprios metadados de recursos protegidos do [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) para que as plataformas LLM possam localizar seu servidor de autorização. Não é necessário criar, hospedar ou configurar os metadados de recursos protegidos.

Seu provedor de identidade deve fornecer as seguintes informações de descoberta:

| Requisito | Detalhe |
|-------------|--------|
| Metadados do servidor de autorização | O servidor de autorização deve publicar metadados de descoberta para clientes LLM. A menos que o **[!UICONTROL URI JWKS]** esteja definido, o tempo de execução deste aplicativo procurará especificamente os metadados do servidor de autorização OAuth [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) para localizar `jwks_uri`; ele não retornará ao caminho de descoberta do OIDC |
| Um emissor com um caminho | O segmento conhecido segue antes do caminho, não depois dele. Um emissor em `https://auth.example.com/oauth2/default` serve seus metadados em `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| URL da chave de assinatura direta | Defina o **[!UICONTROL URI JWKS]** quando as chaves estiverem hospedadas em outro lugar ou quando o provedor expor somente a descoberta OIDC. Isso ignora a descoberta de tempo de execução das chaves, não a descoberta exigida pelos clientes LLM |

## API de autenticação do manipulador {#handler-auth-api}

O pacote `@adobe/llm-apps-runtime` exporta os seguintes auxiliares. Cada auxiliar aceita `extra`, o segundo argumento que seu manipulador recebe.

| Auxiliar | Devoluções |
|--------|---------|
| `getAuthenticatedUser(extra)` | A declaração `sub` do usuário conectado, ou `undefined` quando a chamada não é autenticada |
| `hasScope(extra, scope)` | `true` quando o token do chamador carrega `scope` |

As informações de token verificadas estão disponíveis em `extra.authInfo`, que é `undefined` para uma chamada não autenticada.

| Propriedade | Descrição |
|----------|-------------|
| `authInfo.token` | O token bruto do portador. Não o registre nem retorne ao cliente |
| `authInfo.clientId` | A declaração `client_id` ou `azp`, ou `unknown` |
| `authInfo.scopes` | Matriz de escopos concedidos |
| `authInfo.expiresAt` | Expiração do token, como a declaração `exp` |
| `authInfo.resource` | O identificador de recurso do aplicativo no qual o token foi validado |
| `authInfo.extra` | `sub` mais qualquer outra declaração simples incluída por seu provedor de identidade |

`extra.challengeAuth(options)` está disponível somente nas ações **[!UICONTROL Opcional]**. Retorne o resultado do manipulador para solicitar que o usuário entre, em vez de retornar o conteúdo.

O auxiliar retorna um desafio de resultado de ferramenta para [!DNL ChatGPT]. [!DNL Claude] requer um desafio HTTP de nível de transporte. Os suprimentos de tempo de execução que desafiam as ações **[!UICONTROL Necessárias]**, mas não através do auxiliar de manipulador opcional.

| Opção | Descrição |
|--------|-------------|
| `error` | Um código de erro de portador [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750): `invalid_token`, `insufficient_scope` ou `invalid_request`. O padrão é `insufficient_scope` |
| `errorDescription` | A mensagem mostrada ao usuário. O padrão é um prompt de entrada genérico |
| `scope` | Escopos delimitados por espaço a serem solicitados. Omita-a para permitir que a plataforma retorne aos escopos compatíveis do aplicativo |

>[!IMPORTANT]
>
>Sempre definir `error` explicitamente. Use `invalid_token` para um chamador sem sessão válida e `insufficient_scope` somente para um chamador cujo token é válido, mas não tem um escopo necessário. O tempo de execução passa o valor para a plataforma LLM, que escolhe o texto do prompt de logon. Envie o código que descreve com precisão a condição em vez do código com o prompt de sua preferência.

## Comportamento da plataforma LLM {#platform-behavior}

Ambas as plataformas oferecem suporte a ações públicas e protegidas no mesmo aplicativo, mas os acionadores de logon são diferentes.

| Comportamento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Autenticação mista | Ações públicas funcionam anonimamente; **[!UICONTROL Necessárias]** ações solicitam entrada | Com **[!UICONTROL Entrar quando necessário]**, as ações públicas funcionam anonimamente e uma chamada protegida inicia a entrada |
| Aplicativo com cada ação **[!UICONTROL Necessária]** | Configurar autenticação OAuth | Requer autenticação antes do uso |
| Configuração do conector | Use **[!UICONTROL Sem Autenticação]** para um aplicativo público, **[!UICONTROL OAuth]** quando cada ação for **[!UICONTROL Necessária]**, e **[!UICONTROL Mista]** caso contrário, onde essas opções forem oferecidas | Revise as configurações detectadas. Escolha **[!UICONTROL Entrar quando necessário]** para aplicativos mistos ou **[!UICONTROL Entrar agora]** para entrar pela primeira vez |
| Identidade ou escopo necessário ausente | O tempo de execução retorna o desafio de autenticação da plataforma | O tempo de execução retorna HTTP 401 para identidade ausente/inválida ou HTTP 403 com um desafio de escopo insuficiente para um token válido sem um escopo necessário |
| `extra.challengeAuth()` do manipulador opcional | Pode iniciar a reautenticação | Uma resposta de resultado de ferramenta HTTP bem-sucedida não inicia o logon. Usar uma ação **[!UICONTROL Obrigatória]** para um portão de entrada portátil |

Consulte [Autenticação lenta](https://claude.com/docs/connectors/building/lazy-authentication) e [ChatGPT](https://developers.openai.com/plugins/build/auth) do Claude para conhecer o comportamento atual do cliente. A interface do usuário e a política do conector podem variar de acordo com a conta e a organização.


## Relacionados

- [Autentique usuários finais com seu próprio provedor de identidade](/help/guides/authentication.md)
- [Campos de ação e widget](/help/reference/reference-docs.md)
- [Resolução de problemas](/help/reference/troubleshooting.md#authentication)
