---
title: Configurar variáveis e segredos do aplicativo
description: Adicione variáveis específicas do ambiente ao aplicativo Aplicativos Adobe LLM, leia-as em um manipulador de ação, implante e solucione problemas comuns.
source-git-commit: 141d7a263a6937299b3ff52bdcc7c16197e55632
workflow-type: tm+mt
source-wordcount: '1158'
ht-degree: 0%
---

# Configurar variáveis e segredos do aplicativo {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] está atualmente na Beta.
>
>Os recursos, fluxos de trabalho e interface mostrados aqui não representam necessariamente o estado final do produto. Para participar da Beta, envie um email para llm-apps-beta@adobe.com.

Use variáveis e segredos para configurar seu aplicativo sem codificar valores nos manipuladores de ação. Por exemplo, defina o URL da API do catálogo de produtos para a qual seu aplicativo chama, usando um serviço de teste no Preparo e o serviço em tempo real na Produção, sem alterar o código do manipulador.

As variáveis mantêm configurações não confidenciais. Os segredos são destinados a valores confidenciais, como chaves de API e tokens de acesso.

>[!IMPORTANT]
>
>Atualmente, há suporte para **somente variáveis**. O suporte secreto está planejado para uma versão futura. Até lá, **NÃO armazene** senhas, chaves de API, tokens de acesso ou outras informações confidenciais **em variáveis**: seus valores são visíveis e copiáveis na tabela de configurações.

**Jornada:** Adicione uma variável ou segredo → leia-o em seu manipulador → implantar → teste.

## Antes de começar {#before-you-begin}

Você precisa:

- **Acesse o repositório do manipulador do seu aplicativo**, para que você possa atualizar os manipuladores para ler suas variáveis, se necessário.
- **`@adobe/llm-apps-runtime`1.1.0 ou posterior** nesse repositório. Os aplicativos criados desde agosto de 2026 já o incluem.

Para verificar a versão, execute isso no repositório do manipulador:

```bash
npm ls @adobe/llm-apps-runtime
```

Se a versão for anterior à 1.1.0, atualize-a e confirme e envie por push `package.json` e `package-lock.json`:

```bash
npm install @adobe/llm-apps-runtime@latest
```

## Gerenciar variáveis {#manage-variables}

Cada ambiente, **[!UICONTROL Preparo]** ou **[!UICONTROL Produção]**, tem suas próprias variáveis. Portanto, verifique o **[!UICONTROL Workspace]** antes de adicionar, atualizar ou excluir um ambiente. As alterações entrarão em vigor na próxima vez que você implantar o aplicativo nesse ambiente.

### Adicionar uma variável {#add-variable-to-app}

Este guia usa uma variável nomeada `GREETING_PREFIX` com o valor `Good day` como um exemplo não sensível, substituindo a saudação padrão do manipulador de `Hello`. A adição de uma variável **não** altera uma ação automaticamente: o manipulador **deve** lê-la.

#### Etapa 1: adicionar uma variável na interface do {#add-a-variable}

1. Abra o aplicativo e selecione **[!UICONTROL Configurações]** na navegação à esquerda.
2. Abra a guia **[!UICONTROL Variáveis e segredos]**.



3. Em **[!UICONTROL Workspace]**, selecione **[!UICONTROL Estágio]** ou **[!UICONTROL Produção]**.
4. Selecione **[!UICONTROL Adicionar]**.

   ![Variáveis e segredos — espaço de trabalho vazio do Palco com o botão Adicionar](/help/assets/guide-app-variables/variables-empty.png)

5. Na caixa de diálogo, digite:
   - **[!UICONTROL Nome]**: `GREETING_PREFIX`.
   - **[!UICONTROL Tipo]**: deixe **[!UICONTROL Variável]** selecionada.
   - **[!UICONTROL Valor]**: `Good day`.

   ![Adicionar variável ou segredo — GREETING_PREFIX definido como Dia Bom](/help/assets/guide-app-variables/add-variable-dialog.png)

6. Selecione **[!UICONTROL Adicionar]** para salvar.

A variável aparece na tabela com seu **[!UICONTROL Nome]**, **[!UICONTROL Tipo]**, **[!UICONTROL Valor]** e data da **[!UICONTROL Última atualização]**. Use o controle de cópia ao lado do valor se precisar copiá-lo.

![Variáveis e segredos — GREETING_PREFIX salvo no espaço de trabalho de Preparo](/help/assets/guide-app-variables/variable-added.png)



>[!IMPORTANT]
>
>Salvar adiciona a variável à configuração do ambiente selecionado. Ele **não** atualiza o aplicativo implantado até que você implante novamente.

#### Etapa 2: leia a variável no seu manipulador {#use-a-variable}

No repositório do manipulador, abra `actions/<action-name>/index.js`. Verifique se o manipulador aceita um segundo argumento, `extra`, e leia a variável com `getVariable`:

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

Com o valor `Good day`, uma chamada com `name` definida como `Ada` retorna `Good day, Ada!` em vez do `Hello, Ada!` padrão. Posteriormente, se você alterar o valor para `Howdy`, a saudação será alterada após a reimplantação, sem que outro manipulador seja editado.

O nome no código **deve** corresponder exatamente ao nome na interface do usuário. Se a variável não estiver definida, `getVariable` retornará `undefined`. Decida se sua ação pode usar um padrão adequado, como no exemplo, ou se deve retornar um erro claro, pois requer a configuração.

>[!NOTE]
>
>As variáveis estão disponíveis somente nos manipuladores de ação do seu aplicativo; elas **não** estão disponíveis automaticamente para os widgets.

Quando as alterações do manipulador estiverem prontas, confirme-as e envie-as por push para o repositório do manipulador do aplicativo. A próxima implantação usa o código enviado mais recente. Para saber mais sobre a edição de manipuladores, consulte [Personalizar um manipulador gerado](/help/guides/customize-handler.md).

#### Etapa 3: implantar e testar {#deploy-and-verify}

1. [Implante seu aplicativo](/help/guides/deploy-your-app.md) no mesmo ambiente selecionado no **[!UICONTROL Workspace]**.
2. Chame a ação de uma plataforma LLM com suporte, com `name` definido como `Ada`. Consulte [Testar o plug-in ChatGPT](/help/guides/test-in-chatgpt.md) ou [Testar o conector Claude](/help/guides/test-in-claude.md).
3. Confirme se a resposta é `Good day, Ada!`. Isso confirma que o manipulador lê a variável configurada e substitui a saudação padrão.

Para configurar o outro ambiente, selecione-o no **[!UICONTROL Workspace]**, repita a instalação com o valor apropriado e, em seguida, implante-o e verifique-o.


>[!NOTE]
>
>Preparo e Produção têm **configurações independentes**. As alterações em um ambiente **não** afetam o outro. Use o mesmo nome em ambos os ambientes, se necessário, e escolha o valor apropriado para cada um.

>[!TIP]
>
>Para testar localmente antes de implantar, consulte [Desenvolvimento e teste do manipulador local](/help/reference/development.md) e passe as variáveis para o servidor local:
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### Atualizar uma variável {#update-or-delete}

1. Selecione o controle de edição na linha da variável.
2. Revise o **[!UICONTROL Valor atual]** da variável e insira um **[!UICONTROL Novo valor]**.

   ![Atualizar GREETING_PREFIX — altere o valor de Bom dia para Olá](/help/assets/guide-app-variables/update-variable-dialog.png)

3. Selecione **[!UICONTROL Atualizar]**.
4. Implante o aplicativo novamente no mesmo ambiente e verifique o comportamento alterado.

A atualização altera somente o valor. As variáveis **não podem** ser renomeadas; para usar um nome diferente, exclua a variável existente e adicione uma nova e atualize o manipulador para ler o novo nome.

### Excluir uma variável {#delete-a-variable}

1. Verifique se alguma ação ainda requer a variável. Se necessário, atualize e envie por push o manipulador **primeiro**.
2. Selecione o controle de exclusão na linha da variável. Para excluir várias entradas, marque suas caixas de seleção e escolha **[!UICONTROL Excluir]**.
3. Revise os nomes na caixa de diálogo de confirmação e selecione **[!UICONTROL Excluir]**.

   ![Excluir GREETING_PREFIX — confirme a exclusão permanente](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. Implante o aplicativo novamente no mesmo ambiente.

>[!IMPORTANT]
>
>A exclusão **NÃO PODE** ser desfeita, portanto, verifique se você selecionou a variável correta. O aplicativo implantado mantém a configuração existente até a próxima implantação. Após essa implantação, os manipuladores **não mais** recebem a variável excluída, portanto, uma ação que a exija pode falhar.

## Regras e limites {#good-to-know}

| Item | Regra |
|------|------|
| Nome | Até 64 caracteres: letras maiúsculas, dígitos e sublinhados, não começando com um dígito. **Deve** ser exclusivo por aplicativo e ambiente e deve corresponder ao manipulador. |
| Nomes reservados | Nomes que começam com `LLMA_` e `MCP_SERVER_URL`. |
| Valor | **Obrigatório**, até 500 caracteres. Espaços à esquerda e à direita são removidos. |
| Limite | 50 variáveis por aplicativo e ambiente. |
| Visibilidade | Os valores das variáveis são visíveis e copiáveis. O suporte para segredo planejado mantém os valores salvos ocultos. |
| Alterações | Entrará em vigor na próxima implantação para o ambiente selecionado. |

## Resolução de problemas {#verify-configuration}

| O que você vê | O que fazer |
|--------------|------------|
| **[!UICONTROL Adicionar]** está desabilitado e a página mostra **[!UICONTROL Limite de Workspace atingido]** | Exclua variáveis desnecessárias. |
| *Use somente letras maiúsculas, dígitos e sublinhados* | Renomeie, por exemplo `API_BASE_URL`. |
| *Este nome é reservado pela plataforma* | Escolha um nome que não comece com `LLMA_` e não seja `MCP_SERVER_URL`. |
| *Já existe uma variável com este nome* | Em vez disso, atualize a variável existente. |
| *Esta variável acabou de ser modificada em outro lugar* | Atualize a página e tente novamente. |
| *Não foi possível carregar variáveis* | Recarregue a página. Se persistir, verifique se você tem acesso ao aplicativo. |
| A ação não usa o novo valor | Verifique se você implantou **depois** de salvar a variável, no mesmo ambiente testado, se a alteração do manipulador foi enviada por push e se o nome corresponde a **exatamente**. **[!UICONTROL Última atualização]** mostra quando o valor foi salvo, não quando foi implantado. |
| `getVariable is not a function` | Seu aplicativo usa um tempo de execução anterior a 1.1.0. Atualize conforme descrito em [Antes de começar](#before-you-begin) e implante. |
| A ação falha após a exclusão de uma variável | Adicione a variável novamente ou atualize o manipulador para que ele não precise mais dela e, em seguida, implante. |

## O que vem a seguir {#whats-next}

- [Personalizar um manipulador gerado](/help/guides/customize-handler.md)
- [Implante seu aplicativo](/help/guides/deploy-your-app.md)
