---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1109'
ht-degree: 0%
---
# Procedimento de captura de tela orientada por agente

Capture a interface do usuário em execução por conta própria com a automação do navegador. O usuário fornece
e aprova alterações de recursos; o agente navega, captura,
limpa e integra as imagens. Não exigir uma montagem manual
pacote de captura de tela antes de iniciar.

## Escolha o ambiente

Use o URL fornecido pelo usuário. Caso contrário, use Produção:

`https://experience.adobe.com/llm-apps`

Registre a data da captura, o ambiente e o estado observado em
[screenshot-manifest.md](screenshot-manifest.md). Uma captura local ou de preparo não
verifique o comportamento Produção. Comparar a interface do com a implementação correspondente e
relatar conflitos em vez de apresentar um fluxo não verificado como lançado.

## Abrir e autenticar o navegador

1. Verifique se a interface solicitada está em execução. Não iniciar um servidor duplicado.
2. Use a automação do navegador disponível. Reutilizar uma sessão do navegador somente quando seu
propriedade e o escopo de acesso são claros.
3. Se a automação não puder ser anexada, abra um navegador separado sob o controle do agente.
Para o Chrome, use um perfil de sessão dedicado fora do repositório e vincule
depuração remota para `127.0.0.1` em uma porta local não usada. Nunca copiar do usuário
perfil de navegador pessoal ou expor a porta de depuração à rede.
4. Verifique se o navegador está em execução e se seu terminal de automação responde.
5. Abra o aplicativo e faça uma pausa para que o usuário se autentique. O usuário lida com o Adobe,
Credenciais do GitHub e da plataforma LLM, MFA, chaves de acesso, captchas, organização
seleção e consentimento privilegiado.
6. Retomar depois que o usuário confirmar a autenticação. Com o dramaturgo, anexe através de
   `chromium.connect_over_cdp()` ao usar uma sessão de depuração controlada do Chrome.
7. Localize o quadro do aplicativo por sua origem ou conteúdo visível. O Unified Shell pode colocar
o aplicativo dentro de um iframe; não presuma um índice de quadro fixo.

Nunca ler, imprimir, exportar ou confirmar credenciais, tokens, cookies ou navegador
armazenamento. Não chame `storage_state()` nem inspecione cabeçalhos de autenticação. Deixe que o
o navegador manipula o logon sem extrair seu estado.

## Inspecionar e navegar com segurança

- Preferir localizadores semânticos como `get_by_role()` e `get_by_label()`. Inspecionar
a árvore de acessibilidade e o texto visível antes de escolher um controle.
- Verifique os rótulos exatos da interface do usuário em relação à tela renderizada. O diálogo de criação e
o editor de ação salva tem guias diferentes; não inferir uma da outra.
- Sempre que possível, reutilize um aplicativo somente documentação aprovado. Usar uma conta pública,
sites não sensíveis e conteúdo de fixação reconhecível.
- Preencha os formulários não salvos com valores de exemplo somente após confirmar que o formulário
não salva automaticamente. Cancele ou descarte essas alterações após a captura e verifique
que as configurações salvas permaneçam inalteradas.
- Pergunte antes de criar aplicativos ou repositórios, iniciar a geração de ação,
validar um repositório que possa receber arquivos padronizados, salvar configurações,
adição ou exclusão de variáveis, mesclagem de solicitações pull, implantação, exclusão ou
recursos de arquivamento ou alteração do acesso ao repositório.
- Tratar **Marca como revisada** como uma mutação do repositório porque ela pode mesclar pull
solicitações. A abertura da tela de revisão não autoriza a aprovação.
- Mantenha o acesso do aplicativo GitHub limitado aos repositórios fixture. Solicitar
aprovação do administrador quando necessário.
- Não aprove ações ou implante um aplicativo apenas para obter uma captura de tela opcional.
Capture estados acessíveis e relate o estado ausente.

Quando forem necessários novos recursos, confirme a organização de destino, o site,
opção de infraestrutura, programa AEM e nome da correção antes de criá-los. Para
Infraestrutura gerenciada pela Adobe, deixe que a plataforma crie os repositórios. Para o
caminho do próprio repositório, use dois repositórios vazios e obtenha aprovação antes de
a validação os inicializa.

## Capturar estados estáveis da interface

1. Escolha os pontos de verificação relevantes no manifesto. Capturar decisões e
resultados verificáveis, nem todos os cliques.
2. Use uma janela de visualização de desktop que se ajuste ao painel relevante, 100% de zoom e
tema de produto padrão.
3. Aguarde até que o conteúdo, as fontes, os menus e o layout da caixa de diálogo esperados se ajustem.
O Unified Shell redimensiona seu iframe ao redor de caixas de diálogo, para que um cabeçalho visível sozinho
não prova que a região de captura é estável.
4. Confirme se a região de destino permanece na mesma posição e tamanho. Aguardar
carregamento de sobreposições, animações de saída, notificações do sistema e transições desativadas para ativadas
para terminar, a menos que um desses estados seja o assunto da imagem.
5. Afastar o foco das entradas e fechar menus não relacionados quando isso não ocorrer
alterar o estado documentado.
6. Capturar a menor região completa que inclua a tarefa e as
contexto. Preferir uma captura de tela de painel ou diálogo sobre uma imagem inteira da área de trabalho.
7. Com o Playwright, use capturas de tela de localizador e `animations='disabled'`, mais
máscaras de tempo de captura para valores confidenciais. Inspecione o PNG resultante; a variável
O êxito na API de captura de tela não prova que a região está correta.

Nunca altere o DOM, repita as etiquetas ou altere a aparência de um controle para criar uma
a captura de tela está pronta para ser utilizada com sucesso.

## Armazenar e limpar imagens

Salve cada execução no diretório ignorado pelo Git:

```text
docs-captures/<run-id>/
```

Use nomes de arquivo descritivos como `setup-choice.png`, `action-basic-info.png`,
e `generated-action-review.png`. Preservar capturas de origem inalteradas. Manter bruto
captura perfis de navegador fora dos arquivos de confirmações e revisão.

Crie cópias finais nos diretórios de saída no manifesto:

- Cortar cromo do navegador, navegação da conta, aplicativos não relacionados e tela vazia.
- Excluir totalmente os segredos da captura, incluindo capturas brutas.
- Mascarar nomes pessoais, avatares, identificadores de organização e instalação, privado
detalhes do repositório, namespaces de tempo de execução e URLs completos do servidor MCP. Preferir sólido
máscaras neutras para desfoque, o que pode deixar os valores recuperáveis.
- Use valores seguros, como `https://auth.example.com`, `catalog:read`,
  `GREETING_PREFIX` e `Good day` em formulários de exemplo não salvos.
- Excluir detalhes de contato gerados e outros dados de amostra pessoais quando
que o conteúdo é desnecessário para a etapa.
- Preservar o estado real da interface do usuário. Cortar ou redigir conteúdo confidencial, não erros,
avisos ou recursos ausentes que o artigo precisa explicar.
- Reutilizar nomes de arquivo públicos estáveis ao atualizar um ponto de verificação existente.

Não exclua capturas de origem, perfis de navegador ou recursos de correção sem
aprovação. Não remova um perfil de navegador ativo enquanto o navegador estiver em execução.

## Revisar e integrar

Para cada imagem final:

1. Abra a imagem e inspecione-a visualmente.
2. Verifique se o painel, os rótulos, o estado e os controles relevantes desejados estão visíveis.
3. Verifique se há texto recortado, regiões vazias ou incorretas, sobreposições de carregamento e transientes
menus e detalhes da conta.
4. Confirme se o artigo descreve o fluxo de trabalho observado e se o texto alternativo
identifica a tela e o estado.
5. Atualize o manifesto com o ambiente de captura, a data e quaisquer limites em
verificação.
6. Execute o validador de documentação e crie o site de revisão usando os comandos
em `SKILL.md`.
7. Inspecione o artigo renderizado e confirme se cada imagem é carregada em um formato legível
tamanho.

Para plataformas de terceiros, use um navegador autenticado somente no do
escopo aprovado. Se o acesso não estiver disponível, verifique as instruções em relação aos
documentação oficial do fornecedor, omitir capturas de tela de configuração não verificadas e estado
que essas telas não foram capturadas. Não apresentar uma configuração antiga de terceiros
imagem como atual.

Se o usuário fornecer imagens, inspecione e verifique essas imagens antes de reutilizá-las. Eles
são materiais de origem adicionais, não um pré-requisito para o workflow de captura.

## Bloqueadores de relatórios

Pausar para autenticação, consentimento ou uma alteração de recurso não aprovada. Relatório
falhas de automação, estados indisponíveis, capturas inseguras, validação de repositório
erros, falhas de geração e incompatibilidades de interface/origem explicitamente. Continuar com
trabalho verificado e não mutante, sempre que possível; nunca invente um estado ou
substituir silenciosamente a evidência ausente por uma imagem obsoleta.
