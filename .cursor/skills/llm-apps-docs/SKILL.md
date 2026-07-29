---
name: llm-apps-docs
description: Crie, atualize, revise e valide a documentação pública e as capturas de tela dos Aplicativos Adobe LLM. Use sempre que editar artigos llm-apps.en, seu índice do Experience League, orientação do agente de integração, documentos de widget do EDS, orientação de prontidão de produção ou capturas de tela de documentação.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '813'
ht-degree: 0%

---


# Documentação de aplicativos LLM

Crie documentação pública verificável e orientada a tarefas para aplicativos Adobe LLM.

## Ordem da Source da verdade

Verifique as declarações do produto neste pedido:

1. Interface de Produção Atual em `https://experience.adobe.com/#/@llmapps/llm-apps/`
2. Implementação atual de interface e API disponível no espaço de trabalho
3. Comportamento atual de SDK público e padronização
4. Documentação pública existente

Se a Produção entrar em conflito com a origem ou os planos, documente a Produção e relate a incompatibilidade. Não publique um workflow futuro como disponível no momento.

## Iniciar todas as tarefas

1. Ler `help/main-toc/TOC.md`.
2. Leia o artigo do target e os artigos diretamente relacionados.
3. Classifique o conteúdo usando [content-model.md](content-model.md).
4. Identifique rótulos de interface, URLs, comandos e contratos que exigem verificação.
5. Mantenha a amostra e a terminologia completas consistentes em todas as páginas.

## Regras de criação

- Usuários potenciais pela primeira vez através da criação automática de aplicativos (o fluxo **[!UICONTROL Criar meu aplicativo automaticamente]**).
- Organize a navegação em torno das jornadas e resultados do usuário, não em tópicos de implementação.
- Indique a sequência de jornadas próximo ao início de cada guia e forneça a próxima etapa compartilhada.
- Não use nomes de código internos (por exemplo, &quot;Agente de integração&quot;) em documentos voltados para o cliente — esse recurso nunca é exposto na interface do usuário do produto. Descreva-o genericamente (por exemplo, &quot;a plataforma&quot;) e use uma cópia exata da interface do usuário, como **[!UICONTROL Criar meu aplicativo automaticamente]** para controles.
- [!DNL Adobe LLM Apps] é independente de plataforma — seu servidor MCP funciona com qualquer plataforma LLM compatível, não apenas [!DNL ChatGPT]. Não use frases com afirmações gerais ou ilustrativas como se [!DNL ChatGPT] fosse o único alvo (por exemplo, prefira &quot;uma plataforma LLM com suporte, como [!DNL ChatGPT]&quot;, a &quot;ChatGPT&quot; sozinha). Somente nomeie [!DNL ChatGPT] explicitamente no conteúdo que é genuinamente e atualmente específico para [!DNL ChatGPT]: o [Guia de Teste no ChatGPT](/help/guides/test-in-chatgpt.md) dedicado, seus links cruzados diretos/etapas de procedimento e o conteúdo de referência ou solução de problemas específico para [!DNL ChatGPT].
- Explicar um conceito técnico quando o usuário o encontra pela primeira vez; vincular ao conceito mais profundo ou material de referência.
- Mantenha tutoriais lineares, guias passo a passo focados em tarefas e páginas de referência fatuais.
- Inclua apenas as informações de que o leitor precisa para a tarefa atual; prefira frases curtas e diretas.
- Use um aplicativo representativo na jornada.
- Diferencie o scaffold gerado da integração pronta para produção.
- Evite nomes de trabalhadores internos, campos de banco de dados, tíquetes de implementação e detalhes instáveis do pipeline.
- Não duplique tabelas de campo entre guias; vincule à referência.
- Preservar a interface e as diretivas do Experience League: `[!DNL]`, ``, `[!IMPORTANT]`, `[!NOTE]` e `[!TIP]`.
- Usar links internos relativos à raiz: `/help/...`.
- Use as frases em maiúsculas e minúsculas para títulos e cabeçalhos, a menos que o rótulo de um produto exija o contrário.
- Use o texto alternativo de imagem descritiva que explica a tela e o estado.

## Narrativa protegida aprovada pela PM

Em `help/overview/overview.md`, estas seções foram aprovadas pelo PM:

- **O que você pode fazer com aplicativos LLM**
- **Por que os aplicativos LLM são importantes**

Preservar os títulos, marcadores, texto, ordem e textos das declarações.
Não reduza, reescreva, reorganize ou remova esses itens durante a documentação geral
atualizações. Altere qualquer uma das seções somente quando o usuário a solicitar explicitamente e
confirma que a nova cópia foi aprovada pelo PM.

## Requisitos de segurança

- Nunca inclua credenciais, tokens, URLs privados, dados pessoais, nomes de host internos ou identificadores de clientes.
- Mostrar segredos carregados da configuração gerenciada, nunca codificados.
- Exigir HTTPS para serviços externos.
- Validar respostas de entrada externa e upstream.
- Renderize valores externos com APIs DOM seguras; não é recomendável interpolá-los em `innerHTML`.
- Recomende permissões de aplicativos GitHub, API, CSP, CORS e navegador de menor privilégio.
- Use erros seguros voltados para o usuário e evite o registro de dados confidenciais.

## Fluxo de trabalho de captura de tela

Para imagens novas ou atualizadas, siga [screenshots.md](screenshots.md) e [screenshot-manifest.md](screenshot-manifest.md).

O fluxo de trabalho padrão usa um pacote de captura de Produção criado pelo usuário:

1. Procure capturas de tela em `docs-captures/<run-id>/` ou use a pasta fornecida pelo usuário.
2. Inventariar e inspecionar visualmente cada arquivo PNG, JPEG e WebP; não depende apenas do nome do arquivo.
3. Faça a correspondência das capturas de tela com os estados de manifesto usando o conteúdo visível da interface do usuário.
4. Relate capturas ausentes, duplicadas, ambíguas, obsoletas ou inseguras antes de alterar a documentação.
5. Preservar capturas de origem inalteradas.
6. Criar cópias finais limpas, usando os nomes de arquivo de manifesto estáveis em `help/assets/`.
7. Atualize o tutorial e os guias relacionados para corresponder ao fluxo de trabalho de Produção capturado real.
8. Adicione texto alternativo preciso e execute a validação da documentação.

A captura do navegador guiada pelo agente continua sendo um fallback opcional. Não armazene o estado ou as credenciais do navegador e não execute a captura de tela com mutação em produção no CI.

Quando solicitado a &quot;atualizar documentos a partir de capturas de tela&quot;:

- Tratar a pasta de captura mais recente explicitamente selecionada como a origem.
- Pergunte somente quando o fluxo do aplicativo ou o mapeamento de captura de tela for genuinamente ambíguo.
- Nunca confirme pastas de captura brutas.
- Nunca exclua ou modifique capturas de origem sem aprovação explícita.
- Se não for possível remover as informações confidenciais sem ocultar a tarefa, solicite uma recaptura segura.

## Criar um arquivo de revisão

Gerar um site do HTML offline compartilhável e um arquivo ZIP:

```bash
node .cursor/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

A build é gravada ao lado do repositório, não dentro dele. Inclui apenas
artigos publicados e ativos saneados, converte diretivas do Experience League
para revisão offline e observa que seu estilo não é a experiência final
Renderização da liga.

## Validar

Executar:

```bash
python3 .cursor/skills/llm-apps-docs/scripts/validate_docs.py
```

Corrija cada artigo interno ausente, ativo ausente, caminho relativo à raiz inválido e campo de front-matter ausente antes de entregar.

Verifique também:

- Os rótulos da interface do usuário e as capturas de tela correspondem à Produção.
- Os exemplos de script e de URL de widget estão em acordo entre as guias e a referência.
- Os comandos correspondem à tabela padrão atual.
- Novas páginas são vinculadas pelo índice.
- O fluxo de trabalho de validação do artigo do Adobe é aprovado quando disponível.

## Referências de suporte

- [Modelo de conteúdo e terminologia](content-model.md)
- [Procedimento de captura de tela da produção](screenshots.md)
- [Manifesto de captura de tela](screenshot-manifest.md)
