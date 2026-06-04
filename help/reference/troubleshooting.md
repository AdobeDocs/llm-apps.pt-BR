---
title: Resolução de problemas
description: Soluções para problemas comuns ao criar, implantar e testar aplicativos Adobe LLM.
source-git-commit: c0f4affd586e77379f5c79731c7aed2c7a5d5d20
workflow-type: tm+mt
source-wordcount: '435'
ht-degree: 0%

---


# Resolução de problemas

>[!IMPORTANT]
>
>**Aviso de isenção de responsabilidade:** esta é uma versão beta do [!DNL LLM Apps]. Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do aplicativo ou do produto.

## Problemas comuns

| Sintoma | Causa possível | O que tentar |
|---------|----------------|-------------|
| O aplicativo não aparece na plataforma LLM | Sua assinatura da plataforma LLM não é compatível com aplicativos MCP personalizados ou o modo de desenvolvedor não está habilitado | Verifique se seu plano oferece suporte a aplicativos MCP personalizados. Habilitar modo de desenvolvedor em **Configurações → Aplicativos → Configurações avançadas** |
| Erro &quot;Falha ao conectar&quot; na plataforma LLM | A URL do servidor MCP está incorreta ou a implantação falhou | Verifique novamente o URL na página Detalhes do aplicativo. Verifique se há falhas no histórico de implantação |
| A ação não foi invocada | A plataforma LLM não pôde corresponder a pergunta do usuário à sua ação | Use `@YourApp` para chamá-lo explicitamente. Melhorar a descrição da ação para ajudar a intenção de correspondência do modelo |
| O dispositivo não é renderizado | Os URLs do dispositivo EDS ou os domínios CSP estão configurados incorretamente | Verifique o URL do script e o URL incorporado do widget na caixa de diálogo Criar ação. Verifique se os domínios de conexão e de recurso da CSP incluem sua origem de EDS |
| Resposta vazia ou de erro | O manipulador tem um erro ou está ausente | Testar localmente com `npm start` primeiro. Ver [Desenvolvimento local](/help/reference/development.md#local-development) |
| O widget é carregado, mas não mostra dados | A forma `structuredContent` não corresponde ao que o bloco espera | Registre `bridge.toolResult` na função `decorate` do seu bloco e compare com a saída do manipulador |
| A implantação falha ao &quot;Clonar e criar&quot; | `npm install` ou erro de compilação do webpack no seu repositório | Execute `npm install && npm run build` localmente para reproduzir o erro |
| A implantação falha em &quot;Coletar credenciais&quot; | Repositório não vinculado ou projeto do Developer Console configurado incorretamente | Verifique se o repositório está vinculado na página de configurações Detalhes do aplicativo |
| Erro do CORS ao carregar o widget | Cabeçalhos `access-control-allow-origin` ausentes no site EDS | Configurar cabeçalhos CORS via `admin.hlx.page` |
| O Editor de cabeçalhos HTTP retorna `404 Error updating config: config not found` ao salvar cabeçalhos CORS | A configuração do site não tem uma seção `headers` | Consulte [a seguir, a seção Inicializar os cabeçalhos de configuração do site de EDS](#initialize-the-eds-site-config-headers-section) |
| O widget é renderizado na visualização, mas não na plataforma LLM | O bloco retorna aos dados de amostra no modo de visualização, mas falha com dados em tempo real | Testar com o real `structuredContent` usando o Inspetor MCP ou curl |

## Inicializar a seção de cabeçalhos de configuração do site de EDS

Se o Editor de Cabeçalhos HTTP retornar `404 Error updating config: config not found`, a configuração do site não terá uma seção `headers`. Corrigir manualmente:

1. Vá para [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html), insira sua organização e site e clique em **[!UICONTROL Buscar]**.
2. Abra o navegador DevTools (guia Rede) e copie o valor do cabeçalho `x-auth-token` da solicitação Fetch.
3. Recuperar a configuração atual do site:

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. Abra `config.json` e adicione `"headers": {}` ao objeto JSON.
5. POST the updated config back: (Retorne a configuração atualizada)

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. Recarregue o Editor de Cabeçalhos e salve o cabeçalho `Access-Control-Allow-Origin` normalmente.

