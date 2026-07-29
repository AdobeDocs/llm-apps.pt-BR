---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '387'
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

Tutorial primário: `help/guides/create-app.md`.

### Conceito

Explica como as partes se relacionam sem se tornar um procedimento ou catálogo de campo.

- Concentre-se em modelos mentais e limites de propriedade.
- Use um pequeno diagrama quando ele melhorar a compreensão.
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
- **Agente de integração** — recurso que cria o scaffold inicial.
- **Criar Meu Aplicativo** — seção de interface do usuário na caixa de diálogo criar aplicativo.
- **Criar meu aplicativo automaticamente** — rótulo exato da caixa de seleção.
- **Ação** — recurso exposto à plataforma LLM.
- **Metadados de ação** — nome, descrição, esquema, anotações, visibilidade e configuração de widget armazenados por aplicativos LLM.
- **Manipulador de ação** — função do lado do servidor no repositório do manipulador.
- **Repositório de manipulador** — repositório contendo manipuladores e testes. Use o rótulo da interface do usuário **Repositório Modelo** somente ao descrever esse controle.
- **Repositório EDS** — repositório que contém blocos de widget e conteúdo.
- **Widget** — resposta visual renderizada na plataforma do LLM.
- **URL do servidor MCP** — ponto de extremidade implantado registrado com uma plataforma LLM.
- **Plug-in ChatGPT** — a integração ChatGPT criada a partir de uma URL de servidor MCP.
- **Preparo** e **Produção** — ambientes de implantação.

Evite alternar entre &quot;ferramenta&quot; e &quot;ação&quot; no prose voltado para o usuário, a menos que explique um detalhe de protocolo MCP.

## Jornada de leitor recomendada

1. Visão geral e pré-requisitos.
2. Crie um aplicativo com o Agente de integração.
3. Revise as ações geradas.
4. Implante o para Preparo e teste o plug-in ChatGPT.
5. Personalize manipuladores e widgets gerados.
6. Implante o aplicativo personalizado para produção.

Criar uma ação do zero e trazer um projeto EDS são ramificações avançadas, não a jornada padrão de primeira execução.
