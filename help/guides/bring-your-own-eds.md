---
title: Traga seu próprio projeto do Edge Delivery Services
description: Conecte um projeto existente do Adobe Edge Delivery Services a uma ação do Adobe LLM Apps.
hidefromtoc: true
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# Traga seu próprio projeto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use este guia para conectar um projeto Edge Delivery Services (EDS) compatível existente ou para adicionar um widget a uma ação que você criou. Um projeto existente é opcional. Se você configurar sua própria infraestrutura, o caminho padrão começará com dois repositórios vazios que a plataforma inicializa durante a [configuração de infraestrutura](/help/guides/create-app.md).

Se a plataforma criou seu widget automaticamente, siga [Personalizar um widget gerado](/help/guides/widgets.md). O projeto gerado já inclui os arquivos do SDK, o bloco, o conteúdo e a configuração de ação descritos aqui.

**Neste guia:** Aprenda a preparar seu projeto EDS, instalar o SDK e criar e publicar um widget. Em seguida, conecte o widget a uma ação e teste-o no aplicativo implantado.

## Antes de começar

Você precisa:

- Um repositório EDS compatível com a matriz AEM com a [Sincronização de Código AEM](https://github.com/apps/aem-code-sync) instalada e uma fonte de conteúdo configurada.
- Permissão para adicionar dependências e criar blocos nesse repositório.
- Permissão para configurar cabeçalhos de resposta para o site EDS.
- Uma ação em [!DNL LLM Apps] com um manipulador que retorna `structuredContent`.

Quando você vincula um repositório existente, a plataforma verifica `scripts/aem.js`, `blocks/`, `styles/styles.css` e `head.html`. A inserção também importa `scripts/scripts.js` e chama sua função `decorateMain` exportada para preparar blocos. Mantenha intacto o contrato de carregamento em bloco do EDS. Os arquivos necessários por si só não confirmam se um projeto altamente personalizado é renderizado corretamente.

## Instalar o SDK de Aplicativos LLM

Na raiz do projeto EDS:

```bash
npm install @adobe/llmapps-sdk
```

O gancho pós-instalação do pacote copia o ponto de entrada do widget, a implementação do bridge e as bibliotecas Adobe de suporte no projeto:

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

A URL de Script da ação aponta para `scripts/aem-embed.js`, não `scripts/llmapps-sdk.js`. Verifique se a instalação copiou os arquivos do SDK, confirme-os e envie-os com o projeto. O gancho substitui os arquivos copiados ao instalar ou atualizar o SDK; não personalize os arquivos SDK copiados.

## Criar o bloco de widget

Criar um bloco para a ação:

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

Exporte a função EDS `decorate` padrão com a ponte conectada como seu segundo argumento:

```javascript
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

Use APIs DOM que codificam valores de texto. Não concatene dados externos no HTML.

O exemplo espera `structuredContent.records`. Corresponda aos nomes de propriedade e campos de registro retornados pelo seu manipulador. Verifique `bridge.isConnected`, não apenas se uma ponte existe: `toolResult` nunca resolve no modo autônomo. Na visualização de Aplicativos LLM, use os dados de amostra do bloco quando `bridge.hostContext.preview` indicar o modo de visualização.

## Criar e publicar a página do widget

Crie uma página EDS para o widget e adicione o bloco a essa página. Publique a página.

O URL da página se torna o URL do Widget da ação. Por exemplo, a configuração gerada padrão usa a origem de visualização de EDS:

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

Uma origem EDS ativa, como `https://main--<repo>--<owner>.aem.live`, também funciona quando a página e os ativos são publicados lá. Use a origem real do site; os sites EDS configurados podem ter nomes de site e de organização diferentes do repositório e do proprietário do GitHub.

Verifique se anexar `.plain.html` à URL do Widget retorna o fragmento de página e se a página contém o bloco `search-products`. O caminho da página não precisa corresponder ao identificador de ação, mas uma convenção consistente facilita a manutenção do projeto.

## Configurar CORS

O widget carrega a página, os scripts, os estilos, os blocos e a mídia do EDS nas origens. Configure o cabeçalho para o site de EDS:

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

Use a origem de sandbox widget real exigida pela plataforma LLM compatível, não meramente a origem do site de bate-papo. Verifique o CORS na resposta do `.plain.html` e no JavaScript, estilos, fontes e mídia importados.

A configuração automática do EDS da plataforma configura o `access-control-allow-origin: *` para entrega de widget público e pode substituir um valor existente mais restrito. Use essa política somente para recursos intencionalmente públicos sem solicitações entre origens credenciadas e quando seus requisitos de segurança permitirem. Se você gerencia um site restrito existente, reveja sua política antes de conectá-lo; não exponha o conteúdo de um site privado para que um widget funcione.

Para obter detalhes sobre a configuração de EDS, consulte o [Serviço de Configuração](https://aem.live/docs/config-service-setup).

## Configurar a ação

Em [!DNL LLM Apps], abra a ação e selecione **[!UICONTROL Detalhes do LLM Widget]**. Em **[!UICONTROL Informações do widget]**, defina **[!UICONTROL Tipo de widget]** como **[!UICONTROL EDS]** e expanda **[!UICONTROL URLs de modelo]**.

Insira:

- **[!UICONTROL URL do Script]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL URL do widget]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

Substitua os exemplos com a mesma origem de site de trabalho e página verificadas acima. Configure domínios da CSP e permissões de navegador usando o privilégio mínimo. A origem do EDS deve ser permitida para conexões, como a busca de fragmentos de página, e recursos, como scripts de bloco e estilos. Adicione outras origens somente quando exigido pelo widget.

Selecione **[!UICONTROL Salvar alterações]** e reimplante no ambiente que você está testando. A configuração do widget salvo não atualiza o servidor MCP em execução até a implantação.

Para obter as definições de campo, consulte [Campos de ação e widget](/help/reference/reference-docs.md).

## Testar a integração

1. Visualize a página do EDS diretamente e verifique seu fallback de dados de amostra.
2. Teste o manipulador localmente e compare seu `structuredContent` com a forma esperada pelo bloco.
3. Implante o manipulador e os metadados de ação no Palco. O código EDS e a publicação de conteúdo são uma etapa separada.
4. Chame a ação de uma plataforma LLM compatível. Consulte [Testar no ChatGPT](/help/guides/test-in-chatgpt.md) ou [Testar no Claude](/help/guides/test-in-claude.md).
5. Verifique os estados de carregamento, sucesso, vazio e erro.

Se a página funcionar diretamente, mas não na plataforma LLM, verifique CORS, CSP, URLs HTTPS e a forma `structuredContent`. Consulte [Solução de problemas](/help/reference/troubleshooting.md).
