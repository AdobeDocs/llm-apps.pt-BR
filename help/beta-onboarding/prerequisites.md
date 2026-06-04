---
title: Pré-requisitos
description: O que é necessário configurar antes de iniciar a sessão de integração do Adobe LLM Apps Beta.
source-git-commit: 1ff383dff82068f68746d665d079216375ba523a
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 1%

---


Antes de sua sessão de integração com o Adobe, confirme se você tem o seguinte em vigor. Sempre que possível, execute as etapas de verificação abaixo — os resultados informam quem precisa estar na sala, não se você pode continuar.

## Console do desenvolvedor da Adobe

Você precisa acessar o [Adobe Developer Console](https://developer.adobe.com/console) com a função de **Desenvolvedor** (ou a função de **Administrador do Sistema**) em sua organização do Adobe IMS. Verifique se sua organização tem acesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Para verificar, vá para [developer.adobe.com/console](https://developer.adobe.com/console). Se aparecer a tela Início rápido, suas permissões estão configuradas corretamente.

![Adobe Developer Console — Tela de Início Rápido confirmando o acesso do desenvolvedor](/help/assets/overview/dev-console-access-granted.png)

Se, em vez disso, você vir uma mensagem de **Acesso restrito**, você não terá a função de Desenvolvedor. Convide o administrador da organização de IMS para a sessão de integração.

![Adobe Developer Console — Mensagem de acesso restrito](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

Você precisa de uma conta do [!DNL GitHub] com as seguintes permissões em sua organização:

- **Criar repositórios** — é necessário criar dois repositórios em sua organização: um para o código do aplicativo e outro para o projeto EDS. Para verificar, vá para [github.com/new](https://github.com/new) — se você puder selecionar sua organização na lista suspensa **Proprietário**, terá a permissão.

  ![Lista suspensa de Proprietário de novo repositório do GitHub mostrando a seleção da organização](/help/assets/overview/github-repo-owner-dropdown.png)

- **Instalar [!DNL GitHub] Aplicativos** — você precisa das permissões apropriadas para instalar um Aplicativo [!DNL GitHub] na sua organização. Consulte [Requisitos para instalar um aplicativo GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

**Verifique suas permissões antes da sessão de integração**

Execute esta verificação rápida antes da reunião com a Adobe. O resultado diz quem precisa estar na sala — não se você pode continuar.

1. Vá para [github.com/new](https://github.com/new), selecione sua organização como o proprietário e crie um repositório chamado `llm-apps-test`.
2. Vá para a página de instalação do [Verificador de Permissão de Aplicativos Adobe LLM](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new) e instale o aplicativo somente para o repositório `llm-apps-test`.

| Resultado | O que significa | Ação |
|---|---|---|
| Ambas as etapas tiveram êxito | Você tem as permissões necessárias | Você está pronto para a sessão de integração |
| A etapa 2 mostra **Solicitação** em vez de **Instalação** | Você não tem permissão para instalar [!DNL GitHub] aplicativos | Convidar o administrador da organização do [!DNL GitHub] para a reunião de integração |

Depois de concluído, exclua o repositório `llm-apps-test` e desinstale o aplicativo verificador de permissões das configurações da organização.

## AEM Sites com [!DNL Edge Delivery Services]

Os widgets de ação estão hospedados em **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. Sua organização precisa de uma licença do AEM Sites que inclua [!DNL Edge Delivery Services]. Você deve ter a função de **Administrador** em sua organização de EDS.

Para verificar, vá para a [Ferramenta de administração de usuários do EDS](https://tools.aem.live/tools/user-admin/index.html), digite o nome da sua organização, deixe o **Site** em branco e clique em **Buscar usuários**. Encontre sua conta na lista e confirme se ela mostra o selo **admin**.

![Ferramenta de administração de usuário do EDS mostrando um usuário com a função de administrador](/help/assets/overview/eds-user-admin.png)

Se você ainda não tiver uma organização EDS, nenhuma ação será necessária — uma será criada para você durante o processo de integração.

## Plataforma LLM (para teste)

Para testar seu aplicativo implantado, você precisa de uma camada de assinatura com suporte que permita aplicativos MCP personalizados e o **Modo de Desenvolvedor** habilitado. Por exemplo, [!DNL ChatGPT] requer uma assinatura do **Pro**, **Business** ou **Enterprise / Edu**.
