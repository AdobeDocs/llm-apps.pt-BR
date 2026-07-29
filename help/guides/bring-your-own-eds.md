---
title: Traga seu próprio projeto do Edge Delivery Services
description: Conecte um projeto existente do Adobe Edge Delivery Services a uma ação do Adobe LLM Apps.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 2%

---


# Traga seu próprio projeto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use este guia quando você já tiver um projeto do Edge Delivery Services (EDS) ou quando tiver criado um aplicativo sem o Agente de integração.

Se o Agente de integração criou seu widget, siga [Personalizar um widget gerado](/help/guides/widgets.md). O projeto gerado já inclui os arquivos do SDK, o bloco, o conteúdo e a configuração de ação descritos aqui.

**Jornada:** Prepare o projeto do EDS → instale o SDK → crie e publique o bloco → configure a ação → implante e teste.

## Antes de começar

Você precisa:

- Um repositório EDS com a [Sincronização de Código AEM](https://github.com/apps/aem-code-sync) instalada.
- Permissão para adicionar dependências e criar blocos nesse repositório.
- Permissão para configurar cabeçalhos de resposta para o site EDS.
- Uma ação em [!DNL LLM Apps] com um manipulador que retorna `structuredContent`.

## Instalar o SDK de Aplicativos LLM

Na raiz do projeto EDS:

```bash
npm install @adobe/llmapps-sdk
```

O pacote copia o ponto de entrada do widget e a implementação da ponte no projeto:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

A URL de Script usada pela ação aponta para `scripts/aem-embed.js`.

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
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

Use APIs DOM que codificam valores de texto. Não concatene dados externos no HTML.

## Criar e publicar a página do widget

Crie uma página EDS para o widget e adicione o bloco a essa página. Publique a página.

O URL da página ao vivo se torna o URL do Widget da ação:

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

O caminho da página não precisa corresponder ao nome da ação, mas uma convenção consistente facilita a manutenção do projeto.

## Configurar CORS

O widget carrega a página do EDS, além de scripts, estilos, blocos e mídia nas origens. Configure o cabeçalho para o site de EDS:

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

Use a origem de host específica exigida pela plataforma LLM compatível. Use o `*` somente quando o widget for intencionalmente público, não usar solicitações entre origens credenciadas e seus requisitos de segurança permitirem.

Para obter detalhes sobre a configuração de EDS, consulte o [Serviço de Configuração](https://aem.live/docs/config-service-setup).

## Configurar a ação

Em [!DNL LLM Apps], abra a ação e selecione **[!UICONTROL Metadados do widget]**.

Insira:

- **[!UICONTROL URL do Script]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL URL do widget]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

Configure domínios da CSP e permissões de navegador usando o privilégio mínimo. Adicione somente as origens e recursos exigidos pelo widget.

Para obter as definições de campo, consulte [Campos de ação e widget](/help/reference/reference-docs.md).

## Testar a integração

1. Visualize a página do EDS diretamente e verifique seu fallback de dados de amostra.
2. Teste o manipulador localmente e compare seu `structuredContent` com a forma esperada pelo bloco.
3. Implante o aplicativo para preparo.
4. Invocar a ação de [!DNL ChatGPT].
5. Verifique os estados de carregamento, sucesso, vazio e erro.

Se a página funcionar diretamente, mas não na plataforma LLM, verifique CORS, CSP, URLs HTTPS e a forma `structuredContent`. Consulte [Solução de problemas](/help/reference/troubleshooting.md).
