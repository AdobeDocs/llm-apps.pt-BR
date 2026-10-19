---
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '715'
ht-degree: 0%
---
# Modelo de conteúdo e terminologia

## Tipos de conteúdo

### Tutorial

Ensina um novo usuário por meio de uma jornada completa e bem-sucedida.

- Indique o resultado e os pré-requisitos.
- Use um aplicativo de amostra e uma sequência.
- Explique apenas os conceitos necessários em cada etapa.
- Termine com um resultado funcional e limpe as próximas etapas.

Jornada de primeira execução: `help/guides/create-app.md` (configuração de infraestrutura), depois `help/guides/creating-actions.md` (geração e revisão da ação).

### Conceito

Explica como as partes se relacionam sem se tornar um procedimento ou catálogo de campo.

- Concentre-se em modelos mentais e limites de propriedade.
- Explique o que cada parte faz, como o usuário a experimenta e onde fazer alterações.
- Mantenha as ferramentas de criação, caminhos de arquivo, chamadas de protocolo e contratos de código nos guias técnicos ou referências vinculados, não na visão geral geral.
- Use um pequeno diagrama quando ele melhorar a compreensão.
- Preservar os diagramas úteis existentes ao simplificar uma página; simplificar os rótulos em vez de remover a explicação visual.
- Link para tutoriais, guias passo a passo e referência.

### Guia de instruções

Ajuda um usuário informado a concluir uma tarefa.

- Comece com o resultado desejado.
- Incluir somente os pré-requisitos específicos da tarefa.
- Prefira um caminho recomendado.
- Link para referência para campos exaustivos.

Exemplos: criar uma ação do zero, personalizar um widget, trazer um projeto EDS, implantar e testar.

### Referência

Fornece informações fatuais que os usuários consultam enquanto trabalham.

- Organize pelo produto ou pela superfície de código.
- Defina cada campo, contrato, comando, limite e status com precisão.
- Evite a narração tutorial e exemplos repetidos.

### Resolução de problemas

Começa com um sintoma observável.

- Descreva as causas prováveis.
- Dê etapas de diagnóstico seguras.
- Evite solicitar que os usuários revelem credenciais ou registros confidenciais.

## Terminologia canônica

- **Aplicativos Adobe LLM** — nome completo do produto na primeira menção.
- **Aplicativo LLM** — um aplicativo gerenciado pelo produto.
- **infraestrutura gerenciada pelo Adobe** — infraestrutura criada e gerenciada pelo Adobe; selecione **Let us handle it** no assistente create-app. Não descreva essa opção como somente de avaliação ou faça declarações não confirmadas sobre a qualificação da produção.
- **Eu mesmo vou configurá-lo** — opção de assistente para conectar seus próprios repositórios, não é um requisito para gravar ações manualmente. O caminho padrão começa com dois repositórios vazios que a plataforma inicializa; projetos existentes compatíveis são opcionais.
- **Criar ações automaticamente** — opção de criação de ação recomendada na página do aplicativo após a configuração da infraestrutura.
- **Criar minhas ações** — botão que inicia a geração de ação baseada no site.
- **Ação** — recurso exposto à plataforma LLM.
- **Metadados de ação** — nome, descrição, esquema, anotações, visibilidade e configuração de widget armazenados por aplicativos LLM.
- **Visibilidade** — a chave liga/desliga da ação da página do aplicativo. Ela controla se a próxima implantação inclui a ação. O editor de ação não tem um switch Enabled separado; suas configurações de exposição do modelo e do widget atendem a um propósito diferente.
- **Manipulador de ação** — função do lado do servidor no repositório do manipulador.
- **Identificador de código** — identificador de ação imutável usado em chamadas MCP, distinto do nome de exibição. Os nomes de manipuladores e pastas de blocos gerados usam um formulário hifenizado; o tempo de execução também resolve sublinhados no identificador para hifens em caminhos de manipuladores.
- **Repositório de manipulador** — repositório contendo manipuladores e testes, selecionado durante a **Configuração da infraestrutura da ação** ao conectar seus próprios repositórios.
- **Repositório EDS** — repositório que contém blocos de widget e conteúdo.
- **Widget** — resposta visual renderizada na plataforma do LLM.
- **`structuredContent`** — dados estruturados de resultado de ação usados pelo dispositivo. Não o descreva como privado, invisível ao modelo ou livre de token; ele faz parte do resultado do MCP entregue ao host.
- **URL do servidor MCP** — ponto de extremidade implantado registrado com uma plataforma LLM.
- **Plug-in ChatGPT** — a integração ChatGPT criada a partir de uma URL de servidor MCP.
- **Preparo** e **Produção** — ambientes de implantação.

Evite alternar entre &quot;ferramenta&quot; e &quot;ação&quot; no prose voltado para o usuário, a menos que explique um detalhe de protocolo MCP.

O produto é independente de plataforma: seu servidor MCP funciona com qualquer plataforma LLM compatível, não apenas com o ChatGPT. Use &quot;uma plataforma LLM compatível, como ChatGPT&quot; (ou semelhante) para declarações gerais ou ilustrativas. Nomeie o ChatGPT sozinho apenas no conteúdo que é genuinamente específico do ChatGPT hoje — o guia Test in ChatGPT, seus links cruzados diretos e o conteúdo de referência ou solução de problemas específico do ChatGPT.

## Sequência de leitura recomendada

1. Visão geral.
2. Crie um aplicativo usando a infraestrutura gerenciada da Adobe ou seus próprios repositórios.
3. Inicie a geração de ação baseada no site a partir da página do aplicativo e revise as ações geradas.
4. Implante para preparar e testar em uma plataforma LLM compatível.
5. Personalize manipuladores e widgets gerados.
6. Implante o aplicativo personalizado para produção.

Criar uma ação do zero e trazer um projeto EDS são ramificações avançadas, não a jornada padrão de primeira execução.

O guia EDS BYO está oculto no momento do índice com `hidefromtoc: true`.
Preserve esse status, a menos que o usuário solicite a restauração da entrada de navegação.
