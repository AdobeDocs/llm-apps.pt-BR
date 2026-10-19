---
name: llm-apps-docs
description: Crie, revise e valide a documentação de aplicativos Adobe LLM, aplique seus padrões editoriais e capture as capturas de tela atuais da interface do usuário por meio da automação do navegador. Use para artigos llm-apps.en, navegação, capturas de tela e pacotes de revisão offline.
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '1380'
ht-degree: 0%
---

# Documentação de aplicativos LLM

Crie documentação pública verificável e orientada a tarefas para aplicativos Adobe LLM.

Esta habilidade do Copilot local do repositório está em `.github/skills/llm-apps-docs/`.

## Ordem da Source da verdade

Usar a interface e o ambiente em execução especificados pelo usuário. Caso contrário, o padrão será
Produção em `https://experience.adobe.com/llm-apps`.
Verifique as declarações do produto nesta ordem para esse ambiente:

1. A interface do usuário atual, observada por meio da automação do navegador.
2. A implementação da interface e da API correspondentes no espaço de trabalho.
3. A SDK pública, o tempo de execução e a placa-mãe atuais.
4. Documentação pública existente

Registre a data de captura e o ambiente de origem no manifesto da captura de tela. Não
apresentar observações locais ou de preparo como comportamento de produção verificado. Se a interface do usuário,
orientações publicadas discordam, comunicam a incompatibilidade e descrevem as
apenas o comportamento que as evidências disponíveis suportam.

## Iniciar todas as tarefas

1. Ler `help/main-toc/TOC.md`.
2. Leia o artigo do target e os artigos diretamente relacionados.
3. Classifique o conteúdo usando [content-model.md](content-model.md).
4. Identifique rótulos de interface, URLs, comandos e contratos que exigem verificação.
5. Mantenha a amostra e a terminologia completas consistentes em todas as páginas.
6. Quando a tarefa precisar de verificação da interface do usuário ou capturas de tela, inspecione a interface do usuário em execução e capture as imagens por conta própria. Peça ao usuário para autenticar ou aprovar alterações de recursos, não para montar um pacote de captura de tela.

## Regras de criação

- Usuários potenciais iniciantes por meio da **Configuração de infraestrutura** e depois de **Criar ações**. O assistente de criação de aplicativos configura somente a infraestrutura; **[!UICONTROL Criar ações automaticamente]** e **[!UICONTROL Criar minhas ações]** iniciam a geração de ações baseadas no site posteriormente, a partir da página do aplicativo. Use a **infraestrutura gerenciada pelo Adobe** para a opção rotulada **[!UICONTROL Deixe-nos manipulá-la]** na interface.
- Descreva a infraestrutura gerenciada da Adobe de forma neutra, conforme a infraestrutura que a Adobe cria e gerencia. Não o rotule como uma opção de avaliação, de configuração temporária ou não somente de produção e não faça declarações não confirmadas sobre a qualificação da produção.
- Organize a navegação em torno das jornadas e resultados do usuário, não em tópicos de implementação.
- Próximo ao início de cada guia, use **Neste guia:** seguido por uma ou duas frases em linguagem simples que explicam a tarefa e a próxima etapa. Preferir &quot;Saiba como...&quot; sobre promessas de futuro. Não use **Jornada:** rótulos ou listas de etapas separadas por seta.
- Não use nomes de código internos (por exemplo, &quot;Agente de integração&quot;) em documentos voltados para o cliente — esse recurso nunca é exposto na interface do usuário do produto. Descreva-a genericamente (por exemplo, &quot;a plataforma&quot;) e use a cópia exata da interface do usuário atual, como **[!UICONTROL Criar minhas ações]** para controles.
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

## Padrões editoriais

- Endereçar o leitor diretamente e usar instruções imperativas: &quot;Selecione Salvar&quot;, não &quot;Você pode selecionar Salvar&quot;.
- Coloque a meta antes da ação quando a finalidade precisar de explicação: &quot;Para adicionar um parâmetro, selecione Adicionar&quot;. Evite cláusulas de finalidade redundantes.
- Prefira voz ativa e tempo presente. Nomeie o ator quando for importante: &quot;A plataforma gera o manipulador&quot;.
- Substitua referências ambíguas como &quot;this&quot;, &quot;these&quot; ou &quot;it&quot; por um substantivo específico quando a referência não estiver clara.
- Mantenha uma ideia principal por frase. Encurtar frases longas e dividir cláusulas incorporadas quando o detalhe extra obscurece a tarefa. Preserve as condições e avisos necessários.
- Reduza cadeias preposicionais e frases substantivas densas. Prefira &quot;configurar as variáveis do aplicativo para o Stage&quot; a &quot;executar a configuração das variáveis do aplicativo no Stage&quot;.
- Use palavras simples em vez de expressões idiomáticas, linguagem figurativa ou frases desnecessárias. Explique termos técnicos desconhecidos quando o leitor os encontrar pela primeira vez.
- Verifique a ordem das palavras, a estrutura das frases, as palavras ausentes ou duplicadas, a pontuação e o início das frases em maiúsculas. Mantenha os itens da lista gramaticalmente paralelos.
- Verifique os nomes oficiais e as letras maiúsculas e minúsculas dos produtos: Adobe LLM Apps, Adobe I/O Runtime, Edge Delivery Services, GitHub, ChatGPT, Claude e Model Context Protocol (MCP). Preserva os rótulos exatos da interface do usuário e a ortografia do código, mesmo quando diferem das convenções de prosa.
- Use uma linguagem inclusiva e respeitosa. Evite estereótipos, suposições de gênero, metáforas estigmatizantes e textos que suponham que cada leitor tenha as mesmas habilidades ou experiência. Use termos como &quot;incluir na lista de permissões&quot; e &quot;incluir na lista de bloqueios&quot; onde preciso; não renomeie APIs, ramificações, comandos ou identificadores de código literais para uma preferência editorial.
- Mantenha os verbos modais quando eles expressarem opções, recursos, permissões, condições, incerteza ou variação do host real. Não transforme uma estimativa em uma garantia ou recomendação em uma necessidade.
- Preservar rótulos exatos da interface do usuário, comandos, exemplos de código, identificadores, contratos técnicos e cópia aprovada pelo PM durante uma passagem editorial.
- Use descobertas automatizadas como sinais de revisão, não substituições obrigatórias. Localize a frase sinalizada, corrija o problema real ou retenha textos significativos com um motivo. Uma categoria de gramática genérica ou pontuação agregada não é suficiente para identificar um erro específico. Não solicite uma pontuação aprimorada sem executar o mesmo avaliador.

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

Capture capturas de tela por conta própria com a automação disponível do navegador. Seguir
[screenshots.md](screenshots.md) para configuração de navegador, autenticação, navegação segura,
verificações de estabilidade, higienização e revisão de imagens. Usar
[screenshot-manifest.md](screenshot-manifest.md) para pontos de verificação e nomes de arquivo atuais.

1. Abra a interface de execução solicitada e pause para que o usuário se autentique.
2. Reutilize uma correção de documentação aprovada e inspecione os estados da interface do usuário necessários.
3. Capture capturas de tela estáveis no diretório `docs-captures/<run-id>/` ignorado. Não aguarde um pacote de captura criado pelo usuário.
4. Recortar ou mascarar cópias seguras para `help/assets/` e inspecionar visualmente cada imagem final.
5. Faça a correspondência do artigo, dos rótulos exatos da interface do usuário e do texto alternativo ao estado observado.
6. Valide links e ativos e inspecione o site de revisão renderizado.

Pergunte antes de criar aplicativos ou repositórios, iniciar a geração e validar um
repositório que pode receber arquivos padrão, salvar configurações, mesclar gerados
pull requests, implantação, exclusão ou alteração de acesso. Manter autenticação e
consentimento privilegiado com o usuário. Nunca inspecione ou exporte credenciais, tokens,
cookies ou armazenamento do navegador, e nunca executar um fluxo de captura mutante sem aprovação.

Se a automação, o acesso ou um estado obrigatório não estiver disponível, relate o
bloqueador. Não invente uma captura de tela, reutilize silenciosamente uma imagem desatualizada ou apresente
verificação somente de origem como uma captura de interface do usuário nova. Preservar capturas de origem existentes.

## Criar um arquivo de revisão

Gerar um site do HTML offline compartilhável e um arquivo ZIP:

```bash
node .github/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

A build é gravada ao lado do repositório, não dentro dele. Inclui apenas
artigos publicados e ativos saneados, converte diretivas do Experience League
para revisão offline e observa que seu estilo não é a experiência final
Renderização da liga.

## Validar

Executar:

```bash
python3 .github/skills/llm-apps-docs/scripts/validate_docs.py
```

Corrija cada artigo interno ausente, ativo ausente, caminho relativo à raiz inválido e campo de front-matter ausente antes de entregar.

Verifique também:

- Os rótulos da interface do usuário e as capturas de tela correspondem ao ambiente de origem gravado.
- Os exemplos de script e de URL de widget estão em acordo entre as guias e a referência.
- Os comandos correspondem à tabela padrão atual.
- Novas páginas são vinculadas do índice, a menos que intencionalmente marcadas como `hidefromtoc: true`.
- O fluxo de trabalho de validação do artigo do Adobe é aprovado quando disponível.

## Referências de suporte

- [Modelo de conteúdo e terminologia](content-model.md)
- [Procedimento de captura de tela orientada por agente](screenshots.md)
- [Manifesto de captura de tela](screenshot-manifest.md)
