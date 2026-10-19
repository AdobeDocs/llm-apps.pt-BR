---
title: Como um aplicativo é conectado em conjunto
description: Entenda as três partes de um aplicativo LLM, como elas funcionam em uma conversa e onde fazer alterações.
source-git-commit: af83670aebde1e6f73585c89a891bc7f5e92fb3a
workflow-type: tm+mt
source-wordcount: '573'
ht-degree: 0%
---

# Como um aplicativo é conectado em conjunto {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Um **aplicativo LLM** traz os serviços de sua marca para uma conversa sobre IA. Por exemplo, um cliente solicita recomendações de café, seu aplicativo encontra produtos correspondentes e o assistente mostra os resultados.

**Neste guia:** saiba o que faz cada parte do aplicativo, onde fazer alterações e como elas funcionam juntas quando alguém usa o aplicativo.

## As três partes de um aplicativo

Um aplicativo pode agrupar várias ações relacionadas, como *Procurar produtos*, *Comparar produtos* e *Localizar uma loja*. Cada ação tem suas próprias configurações, manipulador e widget opcional. Adicionar uma ação não requer outro par de repositórios.

Um aplicativo com widgets interativos tem três partes: configurações de ação, código do manipulador e código do widget. Você gerencia as configurações em [!DNL LLM Apps]. O código do manipulador e do widget está em dois repositórios [!DNL GitHub] separados.

```text
┌───────────────────────┐   ┌───────────────────────┐   ┌───────────────────────┐
│ 1. LLM Apps UI        │   │ 2. Handler repository │   │ 3. Widget repository  │
│                       │   │                       │   │                       │
│ Action descriptions   │   │ Connects to your APIs │   │ Widget layout         │
│ Required information  │   │ Runs the task         │   │ Branding and styles   │
│ When to use an action │   │ Returns results       │   │ Interactive controls  │
└───────────────────────┘   └───────────────────────┘   └───────────────────────┘
            │                           │                           │
            └───────────────────────────┼───────────────────────────┘
                                        ▼
                    ┌───────────────────────────────────────┐
                    │ Your app in an AI conversation        │
                    │ The action runs and the widget        │
                    │ shows the result.                     │
                    └───────────────────────────────────────┘
```

- **As configurações de ação descrevem a tarefa.** Eles informam ao assistente quando usar uma ação e quais informações ela precisa. Para pesquisa de produto, essas informações podem ser uma categoria ou termo de pesquisa.
- **O manipulador faz o trabalho.** Ele obtém dados de seus sistemas ou executa uma tarefa. Um manipulador de pesquisa de produto, por exemplo, recupera produtos correspondentes do catálogo.
- **O widget apresenta o resultado.** Ele controla o que o cliente vê e com o qual interage, como cartões de produto com nomes, preços e botões.

Recomendamos adicionar um widget a cada ação. Um widget oferece controle sobre a aparência dos resultados e como os clientes interagem com eles, em vez de deixar a apresentação para o assistente de IA. Respostas somente texto também são suportadas.

## Onde fazer alterações

| O que você deseja alterar | Onde editar |
|---|---|
| Quando o assistente usa uma ação ou quais informações a ação solicita | [Configurações de ação em aplicativos LLM](/help/reference/reference-docs.md) |
| De onde vêm os resultados ou o que acontece quando uma ação é executada | [Código do manipulador no repositório do manipulador](/help/guides/customize-handler.md) |
| Aparência dos resultados, incluindo layout, marca e interações | [Código do dispositivo no repositório de dispositivos](/help/guides/widgets.md) |

Mantenha as três partes consistentes. Por exemplo, se você alterar as informações retornadas por um manipulador, atualize o widget para exibir o novo resultado.

## O que acontece em uma conversa

Após a implantação, seu aplicativo tem uma URL de conexão chamada de **URL do servidor MCP**. Adicione este URL a uma plataforma de IA compatível para que o assistente possa descobrir e usar as ações do seu aplicativo.

Para um aplicativo com uma ação e um widget de pesquisa de produto:

1. Um cliente pergunta ao assistente, *Mostre-me um café.*
2. O assistente usa a descrição da ação para escolher a ação de pesquisa de produto.
3. O manipulador obtém produtos correspondentes do catálogo.
4. O widget exibe os produtos como cartões na conversa.
5. O cliente explora os resultados ou faz uma pergunta complementar.

O cliente permanece na conversa enquanto seu aplicativo fornece as informações ou o serviço. O suporte a widgets depende da plataforma LLM.

## Aplicar e testar as alterações

Publicar alterações no widget e implantar alterações de ação antes de testar a experiência atualizada. Siga [Implantar seu aplicativo](/help/guides/deploy-your-app.md) para implantar em Preparo e testar o aplicativo em uma plataforma LLM com suporte.

As ações geradas automaticamente começam com dados de amostra. Conecte os manipuladores aos seus sistemas reais e teste o aplicativo antes de disponibilizá-lo aos clientes.
