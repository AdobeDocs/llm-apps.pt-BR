---
title: Personalizar um widget de EDS gerado
description: Entenda e personalize o widget do Edge Delivery Services criado pelo Agente de integração de aplicativos do Adobe LM.
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '650'
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
>Este guia pressupõe uma familiaridade básica com os Serviços de entrega de borda (EDS) da Adobe. Se você é novo no EDS, primeiro leia o [Tutorial do desenvolvedor do EDS](https://www.aem.live/developer/tutorial) e o [Explorar blocos](https://www.aem.live/docs/exploring-blocks) para aprender o básico — blocos, a função `decorate` e a estrutura do projeto do EDS — antes de personalizar um widget.

O Agente de integração cria um dispositivo EDS para cada ação gerada. O widget já recebe o resultado da ação, renderiza dados de exemplo, aplica o estilo do host e está vinculado à ação em [!DNL LLM Apps].

Comece testando o widget gerado. Em seguida, personalize o contrato de dados, a interação e o design visual.

**Jornada:** encontre o bloco gerado → alinhar seu contrato de dados → personalizar com segurança → visualizar localmente → implantar e testar.

## Localizar o widget gerado

Abra o repositório EDS selecionado ao criar o aplicativo. Cada widget gerado é um bloco EDS:

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- O arquivo JavaScript lê o resultado da ação e cria a interface.
- O arquivo CSS controla o layout, o comportamento responsivo e o design visual.
- A solicitação de pull gerada mostra os arquivos exatos criados para a ação.

O Agente de integração também configura os URLs do widget e os arquivos de suporte do SDK. Não é necessário criar um segundo projeto EDS ou inserir novamente esses valores para personalizar um widget gerado.

## Como o SDK de aplicativos LLM conecta o widget

O pacote `@adobe/llmapps-sdk` conecta o dispositivo EDS ao host LLM. O repositório EDS gerado inclui:

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

Você não importa a SDK no bloco. O `bridge` conectado é fornecido automaticamente. Ele permite que o widget:

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

Quando você alterar `structuredContent`, atualize o manipulador e o widget juntos. Consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md) para obter o contrato de retorno completo.

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

Valide as URLs antes de atribuí-las a `href` ou `src` e permita somente os protocolos exigidos pela experiência.

## Usar a ponte do host

O EDS passa uma ponte conectada para `decorate(block, bridge)`. A ponte de proteção chama, portanto, o bloco também é renderizado durante a pré-visualização direta do EDS.

### Aplicar estilos de host

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

Isso se aplica à tipografia do host e às variáveis de tema. O CSS do widget deve suportar temas de host claros e escuros.

### Enviar uma mensagem de acompanhamento

```javascript
await bridge.sendMessage('Show me similar products.');
```

Use `sendMessage` quando uma interação precisar continuar a conversa.

### Chamar outra ação

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

Use `callTool` para uma interação explícita que precisa de outro resultado de ação. Transmita apenas valores validados e lide com falhas sem expor detalhes internos.

### Manter o tamanho do widget sincronizado

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

Chame `autoResize` após a renderização inicial para que o host possa responder às alterações de conteúdo.

## Visualizar suas alterações

Os blocos gerados devem incluir dados de amostra para visualização direta quando `bridge` não estiver disponível.

Para visualizar o projeto EDS localmente:

```bash
npm install -g @adobe/aem-cli
aem up
```

Abra a página de widget gerada em `http://localhost:3000`. Verificar:

- Estados de vazio, carregamento, sucesso e erro.
- Texto longo e campos opcionais ausentes.
- Navegação pelo teclado e foco visível.
- Temas claros e escuros.
- Layouts estreitos e largos.

Em seguida, implante o aplicativo para preparo e teste com `structuredContent` ativo na plataforma LLM.

## Publicar a personalização

1. Confirme e envie as alterações de EDS.
2. Se você alterou a forma dos dados, confirme e empurre as alterações do manipulador correspondente.
3. Implante o aplicativo para preparo.
4. Testar a ação e o widget em [!DNL ChatGPT].
5. Promova a versão verificada para produção.

## Outras configurações de EDS

Se você não usou o Agente de Integração ou deseja integrar um site de EDS existente, consulte [Trazer seu próprio projeto de EDS](/help/guides/bring-your-own-eds.md).
