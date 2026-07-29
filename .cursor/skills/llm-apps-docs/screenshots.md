---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '695'
ht-degree: 0%

---
# Procedimento de captura de tela da produção

Use este procedimento para capturar capturas de tela reais de documentação pública do:

`https://experience.adobe.com/#/@llmapps/llm-apps/`

O fluxo de trabalho preferido é a captura humana seguida pela ingestão assistida por agente. O usuário decide quais estados de Produção são relevantes; a habilidade organiza, limpa e integra esses estados à documentação.

## Capturar caixa de entrada

Coloque cada execução de captura em:

```text
docs-captures/<YYYY-MM-DD>/
```

O diretório é ignorado pelo Git. Capturas de tela brutas devem permanecer locais e nunca devem ser enviadas.

O usuário pode usar qualquer nome de arquivo, mas nomes ordenados facilitam a revisão:

```text
01-create-app.png
02-connect-github.png
03-onboarding-enabled.png
04-generating-actions.png
05-review-actions.png
```

Um `capture-notes.md` opcional pode descrever estados ausentes, comportamento incomum ou a ordem pretendida.

## Limites de segurança

- O usuário insere as credenciais do Adobe, GitHub e plataforma LLM diretamente no navegador.
- Pare para MFA, chaves de acesso, captchas, seleção de organização e consentimento privilegiado.
- Nunca leia, imprima, salve ou confirme tokens, cookies, armazenamento no navegador ou credenciais.
- Use um site público não confidencial e novos repositórios somente documentação.
- Conceda acesso aos Aplicativos GitHub somente aos dois repositórios usados pela correção.
- Pergunte antes de criar, implantar, excluir, arquivar ou alterar o acesso ao repositório.
- Não capture informações pessoais, IDs de organização, IDs de instalação de repositório, tokens ou URLs de tempo de execução completo.

## Nomeação do dispositivo

Use nomes que identifiquem claramente os recursos de documentação descartáveis:

```text
App: LLM Apps Docs <YYYY-MM-DD>
Handler repo: llm-apps-docs-<YYYYMMDD>
EDS repo: llm-apps-docs-<YYYYMMDD>-eds
```

Antes de criar qualquer coisa, confirme a organização do Adobe de destino, o proprietário do GitHub, o site público e os nomes de correções com o usuário.

Crie ambos os repositórios vazios e privados. Não os inicialize com um README, licença ou `.gitignore`.

## Configurações de captura

- Use um visor de desktop grande o suficiente para mostrar caixas de diálogo completas sem o cromo do navegador.
- Mantenha o zoom em 100%.
- Use o tema de produto padrão, a menos que o artigo especificamente ensine temas.
- Capture a menor região completa que contenha a tarefa e o contexto necessário.
- Evite cursores, menus abertos não relacionados à etapa, brindes de ações anteriores e giradores transitórios, a menos que o girador seja o estado documentado.
- Use PNG.
- Manter nomes de arquivo estáveis; substituir conteúdo de imagem em vez de renomear arquivos durante atualizações.

## Sequência de captura recomendada

O usuário deve capturar os estados relevantes do manifesto, incluindo:

1. Crie o aplicativo antes que o GitHub seja conectado.
2. Seleção de acesso ao repositório do aplicativo GitHub.
3. **Criar meu aplicativo automaticamente** habilitado com ambos os repositórios selecionados.
4. Criação de aplicativo ou inicialização de agente de integração.
5. Ações sendo geradas.
6. Ações geradas prontas para revisão.
7. Metadados, manipulador e widget de uma ação representativa.
8. Revisão por ação e o estado de todas as ações revisadas.
9. Implantação de preparo bem-sucedida.
10. Registro do aplicativo e um resultado representativo na plataforma LLM.

Capture telas adicionais quando explicarem uma decisão, erro ou pré-requisito real. Não capture cada clique.

## Fluxo de trabalho de entrada de habilidades

Quando o usuário solicita a atualização da documentação de uma pasta de captura:

1. Confirme o diretório de captura exato.
2. Inventarie todos os arquivos PNG, JPEG e WebP e inspecione visualmente cada imagem.
3. Criar um mapeamento de arquivos de origem para entradas em `screenshot-manifest.md`.
4. Compare os rótulos e a sequência da interface do usuário visíveis com o tutorial existente.
5. Relatório:
   - estados obrigatórios ausentes;
   - imagens duplicadas ou redundantes;
   - ordenação ambígua;
   - capturas de tela obsoletas;
   - informações sensíveis;
   - Comportamento de produção em conflito com os documentos.
6. Não edite capturas de origem.
7. Para cada imagem aceita, crie uma cópia limpa com o nome de arquivo de manifesto estável em `help/assets/guide-onboarding-agent/`.
8. Recortar somente quando a interface ao redor não adiciona contexto útil.
9. Mascarar valores confidenciais. Se o mascaramento seguro não for possível, peça uma recaptura.
10. Atualize o artigo e o texto alternativo para corresponder ao fluxo de trabalho capturado.
11. Execute a validação de links e ativos.
12. Deixe a pasta de captura no lugar até que o usuário solicite explicitamente a remoção.

## Captura opcional guiada por agente

Se o usuário solicitar que o agente direcione o navegador, use o mesmo manifesto e limites de segurança. Pausar para autenticação, consentimento privilegiado, alterações no repositório, criação, implantação e limpeza do aplicativo. Nunca execute esse fluxo de mutação de produção sem supervisão.

## Análise de imagem

Para cada imagem:

- Corresponda a uma entrada de manifesto.
- Verifique a cópia da interface do usuário em relação ao artigo.
- Cortar a navegação da conta quando não for necessário.
- Mascarar nomes pessoais, avatares, identificadores de organização, identificadores de instalação de repositório, namespaces de tempo de execução e aplicativos não relacionados.
- Confirme se nenhum preenchimento automático do navegador, email, token de acesso ou detalhe do repositório privado está visível.
- Escreva um texto alternativo que identifique a tela e o estado.

## Quando parar

Interromper e relatar um bloqueador quando:

- A produção não corresponde ao workflow que está sendo documentado.
- O fluxo de revisão é materialmente diferente da documentação publicada.
- A validação do repositório rejeita o fluxo de repositório vazio pretendido.
- Falha no pipeline de integração.
- Uma ação privilegiada requer um usuário ou administrador.
- Uma captura de tela não pode se tornar segura sem ocultar informações essenciais para a etapa.
