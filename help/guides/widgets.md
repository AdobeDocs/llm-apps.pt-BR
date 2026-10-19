---
title: Personalizar um widget de EDS gerado
description: Entenda e personalize o widget do Edge Delivery Services criado automaticamente pelos aplicativos LLM do Adobe.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# Personalizar um widget gerado {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

>[!NOTE]
>
>Este guia pressupõe uma familiaridade básica com os Serviços de entrega de borda (EDS) da Adobe. Se você é novo no EDS, leia o [Tutorial do desenvolvedor do EDS](https://www.aem.live/developer/tutorial) e [Explorar blocos](https://www.aem.live/docs/exploring-blocks) antes de personalizar um widget. Esses recursos cobrem blocos, a função `decorate` e a estrutura do projeto EDS.

Após a geração bem-sucedida, a plataforma fornece um widget EDS vinculado à ação em [!DNL LLM Apps]. O widget renderiza dados representativos para visualização e lê o resultado do manipulador quando conectado a uma plataforma LLM compatível. Revise o design e o comportamento gerados antes de personalizar o widget.

Comece testando o widget gerado. Em seguida, personalize o contrato de dados, a interação e o design visual.

**Neste guia:** Aprenda a encontrar o widget gerado e adaptá-lo aos dados de sua ação e ao design de sua marca. Em seguida, visualize suas alterações localmente e teste-as no aplicativo implantado.

## Localizar o widget gerado

Abra o repositório EDS conectado durante a [configuração de infraestrutura](/help/guides/create-app.md). Se você usa a infraestrutura gerenciada da Adobe, solicite acesso ao código antes de continuar. Cada widget gerado é um bloco EDS:

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- O arquivo JavaScript lê o resultado da ação e cria a interface.
- O arquivo CSS controla o layout, o comportamento responsivo e o design visual.
- A solicitação de pull gerada mostra os arquivos exatos criados para a ação.

O slug usa hifens: uma ação com o identificador de código `search_products` usa um bloco como `blocks/search-products/`. A página de widget gerada normalmente é `/eds-widgets/search-products-demo`; use a **[!UICONTROL URL do Widget]** real nos metadados da ação.

A plataforma também configura os URLs do widget e os arquivos SDK de suporte. Não é necessário criar um segundo projeto EDS ou inserir novamente os URLs do widget para personalizar um widget gerado.

## Como o SDK de aplicativos LLM conecta o widget

O pacote `@adobe/llmapps-sdk` conecta o dispositivo EDS ao host LLM. Seus pontos de entrada no repositório EDS gerado são:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

O `aem-embed.js` estabelece a conexão de host, carrega a página de EDS e chama seu bloco:

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

O incorporado busca o conteúdo `.plain.html` da página do widget e importa seus blocos. Você não importa a SDK no bloco. A inserção fornece automaticamente `bridge`; o carregamento normal da página do EDS não fornece essa informação. A ponte permite que o widget:

- Ler o resultado do manipulador com `bridge.toolResult`.
- Aplicar estilo de host com `bridge.applyHostStyles()`.
- Continuar a conversa com `bridge.sendMessage()`.
- Invocar outra ação com `bridge.callTool()`.
- Mantenha seu tamanho sincronizado com `bridge.autoResize()`.

Este guia aborda os métodos comuns de ponte. Consulte o [`@adobe/llmapps-sdk` pacote](https://www.npmjs.com/package/@adobe/llmapps-sdk) para obter a API completa.

## Entender o contrato de dados

O manipulador de ações retorna `structuredContent` e o bloco o lê de `bridge.toolResult`.

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

Neste exemplo, `PREVIEW_RESULT` é o objeto de dados de amostra local do bloco. Os widgets gerados no momento usam `structuredContent.arguments` e `structuredContent.records`; verifique se os arquivos gerados têm a forma exata. Nunca substitua um resultado ao vivo vazio por registros de visualização.

`bridge.toolResult` resolve somente com o primeiro resultado. Use `bridge.onToolResult(callback)` se um widget permanecer montado e precisar lidar com resultados posteriores.

Quando você alterar `structuredContent`, atualize o manipulador e o widget juntos. Não presuma que os dados estruturados estão ocultos do modelo; isso faz parte do resultado da ação do host. Consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md) para obter o contrato de retorno completo.

## Renderizar dados externos com segurança

Tratar saída do manipulador como dados não confiáveis. Prefira APIs DOM como `textContent` em vez de inserir valores de resposta em `innerHTML`.

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

Valide as URLs antes de atribuí-las a `href` ou `src` e permita somente os protocolos exigidos pela experiência.

## Usar a ponte do host

O código incorporado passa uma ponte para `decorate(block, bridge)`. Proteger chamadas interativas com `bridge?.isConnected` para que o bloco também seja renderizado durante a visualização direta do EDS. Um objeto de ponte pode existir no modo autônomo sem estar conectado; aguardar `toolResult` no modo autônomo nunca resolve. A visualização dos Aplicativos LLM também define `bridge.hostContext.preview` para permitir que o bloco escolha dados de amostra.

### Aplicar estilos de host

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

O método aplica variáveis CSS de host disponíveis e estilos de fonte. A aplicação de estilos de host é opcional: os widgets gerados podem usar seus próprios tokens de design. A incorporação mantém classes `.light` e `.dark` em seu corpo sombra. Use `bridge.hostContext.theme` e `bridge.onContextChange(callback)` quando o bloco também precisar reagir às alterações de tema.

### Enviar uma mensagem de acompanhamento

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

Use `sendMessage` para continuar a conversa após uma interação.

### Chamar uma ação diretamente

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

Use `callTool` para uma interação explícita que precisa de um resultado de ação. Passe o identificador de código da ação de destino real, não o nome da pasta de bloqueio ou o nome para exibição; `search_products` é um exemplo. O host deve aceitar chamadas de ação e a ação de destino deve permitir a visibilidade do aplicativo. Chamar uma ação não renderiza automaticamente o widget atual. Transmita apenas valores validados e lide com chamadas rejeitadas sem expor detalhes internos.

Se a visibilidade da ação de destino precisar mudar, edite somente essa ação em **[!UICONTROL Detalhes da ação]**, salve a alteração e reimplante no ambiente que você está testando.

### Manter o tamanho do widget sincronizado

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

Para permitir que o host responda às alterações de conteúdo, chame `autoResize` após a renderização inicial. O método retorna uma função de limpeza que interrompe a observação quando o widget é removido.

## Visualizar suas alterações

Os blocos gerados incluem dados de amostra para visualização direta quando a ponte está ausente, desconectada ou marcada como pré-visualização. Mantenha o fallback de dados de amostra separado dos estados de erro e vazio ativo.

Para visualizar o projeto EDS localmente:

```bash
npm install -g @adobe/aem-cli
aem up
```

Abra o caminho da URL do Widget da ação no servidor local, por exemplo `http://localhost:3000/eds-widgets/search-products-demo`, em vez de somente na raiz do site. Verificar:

- Estados de vazio, carregamento, sucesso e erro.
- Texto longo e campos opcionais ausentes.
- Navegação pelo teclado e foco visível.
- Temas claros e escuros.
- Layouts estreitos e largos.

A visualização direta do EDS não testa o handshake do host ou os dados de ação ativa. Publicar as alterações de EDS e testar com `structuredContent` em tempo real na plataforma LLM com suporte.

## Publicar a personalização

1. Confirme e envie as alterações de EDS.
2. Verifique se a página do widget e os scripts e estilos alterados estão disponíveis nos URLs do EDS configurados. Publique alterações de conteúdo por meio do fluxo de trabalho de publicação do EDS.
3. Se você alterou os metadados do manipulador ou da ação, envie essas alterações por push e implante o aplicativo no Palco.
4. Teste a ação e o widget em uma plataforma LLM compatível. Consulte [Testar no ChatGPT](/help/guides/test-in-chatgpt.md) ou [Testar no Claude](/help/guides/test-in-claude.md).
5. Após os testes serem bem-sucedidos, implante as alterações de manipulador e metadados na produção, conforme necessário.

A publicação do EDS é separada da implantação do aplicativo. Se Preparo e Produção usarem os mesmos URLs EDS, as alterações de widget nesses URLs afetarão ambos os ambientes; a implantação no Preparo não cria uma cópia de widget isolada.

## Outras configurações de EDS

Se você mesmo criou ações ou deseja integrar um site de EDS existente compatível, consulte [Trazer seu próprio projeto de EDS](/help/guides/bring-your-own-eds.md).
