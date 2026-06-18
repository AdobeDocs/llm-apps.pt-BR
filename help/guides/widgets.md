---
title: Configurar o dispositivo (EDS)
description: Saiba como configurar um projeto de widget do Edge Delivery Services e implementar o contrato de bloco para renderizar respostas visuais dentro das plataformas LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '1226'
ht-degree: 1%

---


# Configurar o dispositivo (EDS)

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Este guia explica como criar um widget EDS de ponta a ponta: desde a configuração da sua ação na interface do usuário do [!DNL LLM Apps], até a configuração do seu projeto EDS, passando pela gravação do código de bloco que renderiza os seus dados na plataforma LLM. Para obter uma visão geral de alto nível, consulte [Conceitos principais](/help/overview/overview.md#widgets-eds).

## O SDK [!DNL LLM Apps]

Tudo começa com o pacote [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm. O SDK é a biblioteca do JavaScript que ativa o canal de comunicação bidirecional entre o dispositivo e o host do LLM.

O SDK também envia `aem-embed.js` — o ponto de entrada específico do EDS que conecta o SDK ao pipeline de bloco do EDS padrão. Quando você `npm install @adobe/llmapps-sdk`, um script de pós-instalação copia automaticamente dois arquivos no seu projeto:

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

Em projetos EDS, **você nunca usa o SDK diretamente no seu código de bloco.** `aem-embed.js` cria e gerencia a conexão do SDK e transmite uma instância `LLMApp` totalmente conectada para o seu bloco como o argumento `bridge` em `decorate(block, bridge)`. A API completa do SDK está disponível em `bridge` — nenhuma importação é necessária.

Se você estiver criando um widget **sem EDS** (um bundler padrão ou projeto TypeScript), poderá usar o SDK diretamente:

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## Como tudo se encaixa

Quando a IA chama sua ação e o manipulador retorna `structuredContent`, a plataforma LLM renderiza um widget interativo na conversa. Três coisas fazem isso funcionar em conjunto:

**Interface do usuário do [!DNL LLM Apps]** — ao criar uma ação, você insere um **[!UICONTROL URL de Script]** e um **[!UICONTROL URL do Widget]** na guia Metadados do Widget. A URL do Script aponta para `aem-embed.js` — o arquivo que vem com a SDK e reside no repositório EDS em `scripts/llm-apps/aem-embed.js`. Isso informa à plataforma LLM qual script carregar quando a ação for chamada.

**`aem-embed.js`** — a plataforma LLM carrega esse script em uma superfície de widget em sandbox. `aem-embed.js` é um elemento personalizado do HTML (`<aem-embed>`) que atua como ponto de entrada com reconhecimento de EDS para o seu widget. Ele executa o handshake com o host LLM usando o SDK, suprime o pipeline de página EDS normal (sem cabeçalho/rodapé), busca o conteúdo da página EDS na URL do Widget, executa o pipeline de bloco EDS e fornece um objeto `bridge` ativo para a função `decorate()` de cada bloco.

**Código do bloco** — você grava um bloco EDS padrão que exporta uma função `decorate(block, bridge)`. O `bridge` é a instância conectada do SDK — ele fornece o resultado estruturado da ação e permite que você envie mensagens de volta para a conversa.

## Adicionar a um projeto EDS existente

Se você já tiver um projeto EDS, há apenas duas etapas antes de começar a escrever blocos.

1. Instalar `@adobe/llmapps-sdk`. O script de pós-instalação copia `aem-embed.js` e `llmapps-sdk.js` para `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. Configure os cabeçalhos CORS para que a plataforma LLM possa carregar suas páginas de widget e scripts entre origens — consulte [Configurar cabeçalhos CORS](#configure-cors-headers) abaixo.

Em seguida, crie seu bloco após o [`decorate(block, bridge)` contrato](#the-decorateblock-bridge-contract), crie a página do widget e insira as URLs na caixa de diálogo Criar Ação.

## Configurar um novo projeto de EDS

### Criar o repositório

1. Crie um novo repositório [!DNL GitHub] com base no modelo [AEM boilerplate](https://github.com/adobe/aem-boilerplate).
2. Adicione o [Aplicativo GitHub da Sincronização de Código do AEM](https://github.com/apps/aem-code-sync) ao repositório.
3. Instale a CLI do AEM para desenvolvimento local: `npm install -g @adobe/aem-cli`.
4. Instalar `@adobe/llmapps-sdk`. O script de pós-instalação copia `aem-embed.js` e `llmapps-sdk.js` para `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

Para obter um guia completo sobre projetos EDS, consulte o [tutorial para desenvolvedores do AEM](https://www.aem.live/developer/tutorial) e [anatomia do projeto](https://www.aem.live/developer/anatomy-of-a-project).

Depois de configurado, seu site de EDS estará disponível em:

- **Visualizar:** `https://main--<repo>--<owner>.aem.page/`
- **Ao vivo:** `https://main--<repo>--<owner>.aem.live/`

### Estrutura do repositório

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### Configurar cabeçalhos do CORS

As páginas do widget EDS são carregadas dentro de uma superfície de widget em sandbox pela plataforma LLM. O site EDS deve retornar cabeçalhos `access-control-allow-origin` corretos para que o host possa buscar o conteúdo do widget entre as origens.

Os cabeçalhos são configurados por meio do painel de administração do AEM em `admin.hlx.page` usando o [Serviço de Configuração](https://aem.live/docs/config-service-setup). Adicione cabeçalhos de resposta personalizados para os caminhos em que suas páginas de widget e scripts SDK residem:

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>É aceitável usar `*` como valor de origem para o conteúdo de widget público no domínio `.aem.live`. Se o site tiver conteúdo protegido, restrinja a origem a domínios específicos.

### Criar a página do widget

Crie uma página na ferramenta de criação do EDS e adicione o bloco a ela. A URL da página se torna a **[!UICONTROL URL do Widget]** configurada por você na ação — essa é a única conexão entre a ação e o bloco. Não há requisito de nomenclatura entre o bloco e o nome da ação.

![Criação de EDS — bloco adicionado à página do widget](/help/assets/guide-widget/aem-author.png)

### Insira os URLs na caixa de diálogo Criar ação

Após configurar seu repositório EDS, vá para **Metadados do widget → URLs de modelo** ao criar sua ação:

**[!UICONTROL URL do Script]** — aponta para `aem-embed.js` no seu repositório EDS. Este é o mesmo valor para cada ação no mesmo projeto EDS:

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL URL do Widget]** — o URL da página EDS criada para este widget. Exclusivo por ação:

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

A plataforma LLM carrega `aem-embed.js` do URL do Script. `aem-embed.js` então busca `.plain.html` da URL do Widget para obter o conteúdo do bloco.

## Fluxo de dados

O caminho completo do seu manipulador para um widget renderizado:

1. **O manipulador de ações** retorna `structuredContent`:

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. A **plataforma LLM** abre uma superfície de widget e carrega `aem-embed.js` da URL do Script.

1. O **`aem-embed.js`** se conecta ao host por meio da SDK, obtém `.plain.html` da URL do Widget, executa o pipeline de blocos EDS e chama `decorate(block, bridge)` no bloco.

1. **Seu bloco** lê os dados de `bridge.toolResult` e renderiza a interface.

1. **A interação do usuário** aciona `bridge.sendMessage(...)` ou `bridge.callTool(...)`, enviando um acompanhamento para a conversa.

## O contrato `decorate(block, bridge)`

Cada bloco de widget EDS deve exportar uma função `decorate` padrão. Esta é a assinatura de bloco EDS padrão, estendida com um segundo argumento — o `bridge` conectado, que é uma instância do SDK [`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) com a API completa disponível:

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

`bridge` está presente apenas quando executado na superfície do widget da plataforma LLM. Sempre proteja suas chamadas de ponte para que seu bloco também seja renderizado quando visualizado diretamente em um navegador ou em seu servidor de desenvolvimento local.

### Renderização de dados a partir do resultado da ação

`bridge.toolResult` é uma Promessa que resolve com o resultado completo que seu manipulador retornou, incluindo `structuredContent`.

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### Aplicação do tema do host

Chame `bridge.applyHostStyles()` no início de `decorate` para inserir as variáveis e fontes CSS do host (tema claro/escuro, tipografia) no widget. Isso mantém o widget visualmente consistente com a interface do usuário da plataforma LLM ao redor.

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

Para reagir às alterações de tema no tempo de execução (por exemplo, quando o usuário alterna entre o modo claro e escuro):

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### Envio de uma mensagem de acompanhamento

`bridge.sendMessage(text)` injeta uma mensagem de usuário na conversa. Essa é a principal maneira de um widget acionar mais interação com a IA, por exemplo, quando um usuário clica em um cartão de produto para solicitar detalhes.

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### Chamada direta de outra ação

`bridge.callTool(name, args)` invoca outra ação de dentro do widget sem passar pela mensagem do usuário. Útil para carregar dados relacionados sob demanda.

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### Redimensionamento automático do widget

A plataforma LLM dimensiona o widget com base no que você relata. Use o `bridge.autoResize(element)` para manter a altura do widget sincronizada à medida que o conteúdo for alterado — ele usa um `ResizeObserver` internamente. Chame-o após a renderização inicial:

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

Ou relatar um tamanho fixo manualmente:

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### Modo de visualização e desenvolvimento local

Ao visualizar uma página EDS diretamente em um navegador ou no servidor de desenvolvimento local, `bridge` é `undefined`. Use o padrão de fallback de dados de amostra mostrado acima para que seu bloco seja renderizado imediatamente sem um manipulador ativo.

Para iniciar um servidor de desenvolvimento local:

```bash
npm install -g @adobe/aem-cli
aem up
```

Isso abre o `http://localhost:3000`, onde você pode navegar até as páginas do seu widget e ver os blocos serem renderizados com dados de exemplo. As alterações no bloqueio de JS e CSS são refletidas imediatamente.

## Próximas etapas

- [Guia: Gravar o manipulador de ação](/help/guides/write-action-handler.md)

