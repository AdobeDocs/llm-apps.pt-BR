---
title: Atualizar um aplicativo existente
description: Atualize o tempo de execução e o EDS SDK de um aplicativo Adobe LLM mais antigo antes de habilitar o Adobe Analytics ou o Customer Journey Analytics.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# Atualizar um aplicativo existente {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use este guia somente se o aplicativo usar o `@adobe/llm-apps-runtime` anterior à versão 1.2.0 ou se os widgets EDS usarem o SDK Aplicativos LLM anterior à versão 0.1.13. Os aplicativos que já atendem a ambos os requisitos podem prosseguir diretamente para [Configurar Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md).

**Neste guia:** saiba como atualizar e verificar o tempo de execução de um aplicativo mais antigo e o EDS SDK. Em seguida, retorne ao guia de configuração do Analytics para ativar o Analytics e verificar a coleta.

## Antes de começar {#before-you-begin}

Você precisa acessar o repositório do manipulador e, para aplicativos com widgets EDS, o repositório EDS vinculado ao seu aplicativo. Se o aplicativo usar a infraestrutura gerenciada da Adobe, solicite acesso ao código antes de fazer atualizações. Consulte [Usar a infraestrutura gerenciada do Adobe](/help/guides/create-app.md#adobe-managed-infrastructure).

Não é necessário criar outro aplicativo. Atualize somente os componentes abaixo das versões compatíveis. Você pode reutilizar a configuração existente do Adobe que atende aos [requisitos de configuração do Analytics](/help/guides/analytics-setup.md#setup-checklist).

Para aplicativos com widgets EDS, publique e verifique a atualização do SDK primeiro, depois mescle a atualização do tempo de execução e reimplante o aplicativo. As duas atualizações do repositório podem ser preparadas independentemente, mas ambas devem estar disponíveis antes de você testar a implantação habilitada para análise.

## Verificar e atualizar o Repositório de Ações dos Aplicativos LLM {#upgrade-runtime}

1. Abra o repositório do manipulador vinculado ao seu aplicativo e instale as dependências dele:

   ```bash
   npm install
   ```

1. Verifique o tempo de execução instalado:

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. Se o tempo de execução for anterior à versão 1.2.0, instale a versão atual:

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   Confirme se a versão instalada é a 1.2.0 ou posterior. Mantenha uma versão compatível mais recente se o aplicativo já usar uma.

1. Confirme se a dependência do tempo de execução e sua versão resolvida atendem ao requisito mínimo. A própria versão do aplicativo é separada da dependência do tempo de execução.
1. Execute as verificações do repositório. Para o padrão de referência Aplicativos LLM:

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   Investigue as falhas antes da implantação. Se uma verificação já falhar na versão original, registre essa linha de base separadamente de todas as falhas introduzidas pela atualização. Uma build local bem-sucedida não verifica a coleção de análises.

1. Confirme a atualização da dependência e seu arquivo de bloqueio, em seguida, envie e mescle a atualização na ramificação padrão do repositório do manipulador, geralmente `main`. A implantação clona essa ramificação. Uma atualização que existe apenas localmente ou em uma ramificação de recurso não mesclada não é incluída na implantação.

   Quando um arquivo de bloqueio existe, a implantação instala dependências com `npm ci`, que usa as versões gravadas. A reimplantação não atualiza o tempo de execução automaticamente. Antes de reimplantar, confirme a dependência atualizada e a versão resolvida na ramificação padrão no GitHub.

## Verificar e atualizar o Repositório de Dispositivos de Aplicativos LLM {#upgrade-sdk}

Para aplicativos com widgets EDS, verifique o SDK no repositório EDS, bem como o tempo de execução do servidor. A atualização de um repositório não atualiza o outro.

Alguns repositórios EDS incluem scripts SDK copiados sem uma dependência npm. O navegador carrega esses scripts fornecidos diretamente, de modo que a ausência de uma entrada de pacote não significa que o SDK está ausente. Atualize a distribuição do SDK copiada usando o processo de atualização do SDK do projeto. Adicionar uma dependência npm persistente não é necessário para um projeto que mantém o SDK como scripts copiados.

Para projetos que gerenciam o SDK por meio do npm:

1. Na raiz do repositório EDS, instale o SDK atual e confirme sua versão:

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   Use 0.1.13 ou posterior. Mantenha uma versão compatível mais recente se o projeto já usar uma.

1. Verifique se a instalação atualizou a distribuição do SDK fornecida. O gancho de instalação substitui os scripts SDK copiados, portanto, revise as personalizações locais antes de confirmar. Atualizar apenas a declaração de dependência não atualiza os scripts que o EDS fornece. Consulte [Instalar o SDK de Aplicativos LLM](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk).
1. Execute as verificações do repositório EDS, como `npm run lint`, e verifique se um widget existente ainda é renderizado e responde às interações.
1. Confirme a distribuição do SDK atualizada junto com qualquer dependência e atualizações de arquivo de bloqueio. O EDS fornece os scripts copiados, não diretamente o pacote npm instalado.

Após qualquer um dos métodos de atualização, envie e mescle a atualização com a ramificação EDS usada pela **[!UICONTROL URL do script]** da ação. Verifique se o URL serve o SDK atualizado e se suas bibliotecas de suporte foram carregadas com êxito. Se o URL usar o site do EDS ativo, conclua as etapas de publicação desse site.

## Próxima etapa {#next-step}

Retorne a [Configurar Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md). Todos os aplicativos devem habilitar o Analytics e implantar para coletar eventos; atualizar o tempo de execução ou o SDK sozinho não habilita a coleção.

Depois de salvar as configurações do Analytics, implante no Palco e [verifique novos eventos de chamada de ação e interação de widget](/help/guides/analytics-setup.md#verify) antes de implantar na Produção. Confirme se a ação e o widget ainda estão funcionando e se uma única seleção de link produz um evento de interação de widget.

O Analytics começa a coletar novas interações após a implantação habilitada para análise. A atualização não reconstrói interações que ocorreram antes da habilitação da coleção.
