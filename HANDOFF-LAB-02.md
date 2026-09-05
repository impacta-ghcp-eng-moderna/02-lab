# Handoff — construção do Lab 02

## Estado do repositório

Este repositório foi recriado com histórico limpo:

- `main` contém somente o commit inicial vazio;
- `construcao-lab-02` contém um snapshot único de todo o conteúdo de `impacta-ghcp-eng-moderna/01-lab@main`;
- o repositório é público e está marcado como template;
- todo o desenvolvimento do Lab 02 deve ocorrer em `construcao-lab-02`;
- não fazer merge em `main` durante a construção;
- quando o lab estiver concluído e validado, integrá-lo em outro momento por squash merge para manter `main` com histórico limpo.

Não recriar branches a partir do `01-lab` nem importar seu histórico. A branch atual já contém a aplicação final do Lab 01 como um snapshot.

## Objetivo do Lab 02

O lab continua diretamente a aplicação concluída no Lab 01. Ele não deve repetir a construção da aplicação nem pedir que os alunos corrijam todos os mecanismos de customização.

Seu foco é um único fluxo novo e útil no trabalho diário:

> delegar a pesquisa de critérios e evidências a um subagente, manter o julgamento em um agente revisor e usar um handoff supervisionado para corrigir uma lacuna selecionada.

## Resultado de aprendizagem

Ao concluir o lab, o aluno deverá ser capaz de:

1. separar coleta de evidências de julgamento;
2. configurar um subagente com contexto e ferramentas mínimos;
3. limitar quais subagentes um agente coordenador pode invocar;
4. classificar critérios como atendidos, não atendidos ou não comprovados;
5. verificar manualmente uma conclusão produzida pelos agentes;
6. usar um handoff com `send: false` para transferir uma lacuna aprovada para implementação;
7. revisar o diff e validar a correção contra o critério original.

## Escopo pedagógico

### Conceitos centrais

- custom agent como coordenador;
- subagent com contexto isolado;
- delegação com menor privilégio;
- revisão baseada em critérios e evidências;
- diferença entre comportamento incorreto e ausência de evidência;
- handoff entre revisão e implementação;
- aprovação humana antes da alteração;
- validação focada após a correção.

### Fora de escopo

- implementar nova fatia vertical;
- alterar instructions, prompt files ou Agent Skills existentes;
- criar ou configurar hooks;
- usar MCP;
- criar outro agente implementador versionado;
- revisar toda a aplicação;
- corrigir todas as lacunas que eventualmente forem encontradas;
- exigir uma resposta idêntica entre alunos;
- escrever scripts complexos.

As instructions, o prompt file, a skill e as demais customizações herdadas devem permanecer disponíveis, mas não são tarefas do lab.

## Cenário verificável

Limitar a revisão aos critérios 8 e 9 de `docs/specs/training-attendees-vertical-slice.md`:

- treinamento existente sem inscritos retorna `200` com coleção vazia;
- treinamento existente com inscritos retorna `200` com os inscritos daquele treinamento.

No snapshot inicial:

- `src/Tests/Api.Tests/AttendeeListingTests.cs` comprova a listagem com um inscrito;
- o arquivo também comprova `404` para treinamento inexistente;
- não existe teste funcional para um treinamento existente sem inscritos.

A implementação aparentemente atende ao cenário vazio, mas a evidência automatizada exigida pela especificação está ausente. O resultado esperado da primeira revisão é:

- critério 9: **Atendido**;
- critério 8: **Não foi possível comprovar**;
- não classificar automaticamente a falta do teste como defeito funcional.

A correção deve ser pequena: adicionar somente o teste funcional do cenário vazio, executar a validação focada e repetir ou atualizar a revisão.

Antes de consolidar o cenário, confirmar novamente a especificação, a implementação e os testes. Se o código mudar durante a construção, preservar a mesma característica pedagógica: uma única lacuna de evidência pequena, determinística e corrigível sem alterar o contrato ou o código de produção.

## Agentes a construir

### `Pesquisador de critérios`

Criar `.github/agents/pesquisador-criterios.md` como subagente especializado.

Características esperadas:

- não aparecer como agente selecionável pelo aluno;
- possuir somente leitura e busca;
- não executar comandos;
- não editar arquivos;
- não delegar para outros agentes;
- receber uma tarefa autossuficiente do coordenador;
- extrair somente os critérios indicados;
- relacionar cada critério a evidências concretas em especificação, implementação e testes;
- reportar fatos e lacunas de evidência sem aprovar a entrega e sem propor alterações fora do escopo.

Validar os campos atuais do front matter na documentação oficial antes de publicar. A intenção é equivalente a `user-invocable: false`, tools `read` e `search` e lista de subagentes vazia.

### `Revisor da entrega`

Evoluir `.github/agents/revisor-entrega.md`, já existente no Lab 01, sem transformá-lo em implementador.

Características esperadas:

- manter leitura, busca e execução focada;
- adicionar capacidade de delegação;
- permitir somente o `Pesquisador de critérios` como subagente;
- delegar o levantamento inicial de critérios e evidências;
- fazer o julgamento final no próprio revisor;
- executar apenas o menor conjunto de testes necessário, após aprovação;
- não editar arquivos;
- produzir as categorias:
  - **Atendido**;
  - **Não atendido**;
  - **Não foi possível comprovar**;
- citar critério, arquivo e evidência para cada conclusão;
- deixar claro que teste passando não comprova critérios não exercitados.

Não criar uma comparação artificial de permissões excessivas. O aluno deve configurar diretamente o estado de menor privilégio esperado.

## Handoff para correção

Adicionar ao `Revisor da entrega` um handoff com finalidade equivalente a **Corrigir lacuna selecionada**.

Requisitos:

- destino: agente local de implementação;
- `send: false`;
- aparecer após o relatório do revisor;
- preparar um prompt que reutilize o contexto da revisão;
- pedir correção de apenas uma lacuna escolhida pelo aluno;
- proibir alteração da especificação e ampliação de escopo;
- exigir indicação dos arquivos antes da edição;
- exigir teste focado e relação entre resultado e critério;
- permitir que o aluno revise e ajuste o prompt antes de enviá-lo.

Não apresentar o handoff como aprovação automática. O clique prepara a transição e o prompt; a decisão e o envio continuam sob responsabilidade do aluno.

Prompt-base sugerido:

> Corrija somente a lacuna selecionada no relatório anterior. Não altere a especificação nem amplie o escopo. Antes de editar, indique os arquivos necessários. Depois da alteração, execute a validação focada, relacione o resultado ao critério correspondente e apresente o diff para revisão.

## Fluxo esperado do aluno

1. executar a linha de base e localizar a especificação, o revisor e os testes de listagem;
2. definir a fronteira entre pesquisador e revisor;
3. criar o subagente com leitura e busca somente;
4. adaptar o revisor para delegar exclusivamente ao pesquisador;
5. configurar o handoff com `send: false`;
6. solicitar revisão somente dos critérios 8 e 9;
7. inspecionar a chamada do subagente e o relatório final;
8. conferir manualmente pelo menos uma evidência;
9. selecionar a lacuna do cenário vazio;
10. acionar o handoff e revisar o prompt preparado;
11. autorizar a criação somente do teste funcional ausente;
12. revisar o diff;
13. executar o teste focado;
14. relacionar o resultado ao critério 8 e registrar a decisão final.

## Distribuição de tempo — 65 minutos

| Período | Atividade |
| --- | --- |
| 0–8 min | Preparar o ambiente e compreender o cenário |
| 8–20 min | Criar o subagente pesquisador |
| 20–30 min | Adaptar o revisor e configurar o handoff |
| 30–43 min | Executar a revisão delegada |
| 43–48 min | Conferir evidências e selecionar a lacuna |
| 48–60 min | Acionar o handoff, revisar o prompt e implementar o teste |
| 60–65 min | Executar validação focada, revisar o diff e concluir |

Validar essa duração em uma execução piloto real. Se necessário, fornecer front matters incompletos ou dicas graduais, sem entregar antecipadamente a solução completa.

## Entregáveis e critérios de conclusão

| Entregável | Evidência esperada |
| --- | --- |
| Subagente restrito | Front matter e ausência de edição, execução e nova delegação |
| Revisor coordenador | Delegação somente ao pesquisador e relatório final próprio |
| Revisão rastreável | Matriz critério → evidência → situação |
| Verificação humana | Registro da evidência conferida manualmente |
| Handoff supervisionado | `send: false` e prompt revisado antes do envio |
| Correção delimitada | Diff contendo somente o teste necessário, salvo ajuste justificado |
| Validação | Teste focado passando e associado ao critério 8 |
| Integridade | Especificação e código de produção inalterados |

Não avaliar a redação exata do relatório nem exigir igualdade com a solução de referência. Avaliar limites, rastreabilidade, evidências, supervisão e comportamento.

## Estrutura do lab

Reutilizar o modelo guiado por issue do `01-lab`:

- cenário e resultado esperado;
- pré-requisitos verificáveis;
- etapas orientadas a resultado;
- palavra de avanço por etapa;
- feedback automatizado;
- dicas graduais em `.github/help/`;
- recuperação de erros comuns;
- solução do instrutor;
- encerramento conectado aos objetivos da aula.

Substituir os arquivos e automações específicos do Lab 01 apenas quando forem incompatíveis com o novo lab. Preservar aplicação, especificações, customizações e CI que continuem relevantes.

Uma progressão inicial possível:

| Etapa | Entrega |
| --- | --- |
| 1. Preparar | Linha de base e arquivos relevantes localizados |
| 2. Delegar | Subagente criado e restrito |
| 3. Coordenar | Revisor e handoff configurados |
| 4. Revisar | Relatório dos critérios 8 e 9 conferido |
| 5. Corrigir | Teste criado por handoff e validado |
| 6. Encerrar | Evidências e decisão final registradas |

Os workflows não devem tentar julgar semanticamente toda a resposta do modelo. Automatizar somente estados objetivos, como presença e estrutura dos arquivos, restrições de tools, existência do teste, execução dos testes e ausência de alterações proibidas.

## Estratégia futura de branches do template

Durante a construção, trabalhar somente em `construcao-lab-02`.

Quando o lab estiver pronto:

1. decidir e documentar qual branch será usada pelos alunos como ponto inicial;
2. produzir nessa branch a aplicação final do Lab 01 com toda a infraestrutura comum do Lab 02, mas sem os agentes e o teste que constituem a solução;
3. manter a solução de referência concluída em `main` após squash merge;
4. destacar no README a necessidade de copiar todas as branches, se o lab depender de uma branch inicial separada;
5. testar o template a partir de uma cópia nova.

Não criar agora a branch inicial definitiva nem fazer merge em `main`; essas ações pertencem à etapa de publicação, depois da validação da experiência.

## README voltado aos alunos

Atualizar o README para apresentar:

- Lab 02 e resultado de aprendizagem;
- continuidade explícita a partir do Lab 01;
- instrução para criar uma cópia incluindo todas as branches, se aplicável;
- branch correta de início;
- duração aproximada de 65 minutos;
- dinâmica guiada por issue;
- critérios gerais, sem antecipar a solução ou o relatório esperado.

Não incluir no README:

- roteiro minuto a minuto do instrutor;
- conteúdo deste handoff;
- resposta esperada completa;
- código pronto dos agentes;
- detalhes internos da automação;
- indicação explícita de qual teste está ausente.

## Dicas e recuperação

Preparar ajuda gradual para:

1. distinguir coleta de fatos de julgamento;
2. limitar tools e subagents;
3. tornar a tarefa delegada autossuficiente;
4. localizar referências e chamadas do subagente;
5. interpretar “não foi possível comprovar”;
6. revisar o prompt preenchido pelo handoff;
7. executar somente `AttendeeListingTests`;
8. restaurar um checkpoint sem perder trabalho válido.

Fallbacks recomendados:

- subagente completo disponível após tentativa autônoma;
- revisor completo disponível após a etapa de coordenação;
- captura ou execução registrada da delegação se a interface variar;
- teste de referência liberado somente depois do debrief;
- checkpoint funcional antes da etapa de correção.

## Validação obrigatória antes da publicação

1. percorrer toda a atividade como aluno em uma cópia nova;
2. concluir em até 65 minutos sem conhecimento da solução;
3. testar palavras de avanço incorretas e corretas;
4. validar links de ajuda na cópia, não apenas no repositório original;
5. confirmar que o pesquisador não aparece para seleção manual;
6. confirmar que o revisor delega somente ao pesquisador;
7. confirmar que a primeira revisão encontra a lacuna de evidência prevista;
8. confirmar que o handoff preenche o prompt, mas não o envia;
9. confirmar que a correção altera somente o teste esperado;
10. executar a validação focada e a suíte necessária;
11. confirmar que especificação e produção permanecem inalteradas;
12. verificar README, descrição, visibilidade pública e estado de template;
13. remover este `HANDOFF-LAB-02.md` da versão publicada quando ele não for mais necessário;
14. fazer o merge para `main` somente depois da aprovação do usuário e preferencialmente por squash.

## Referências para a construção

Priorizar documentação oficial atualizada sobre:

- custom agents e handoffs no VS Code;
- configuração de custom agents do GitHub Copilot;
- subagents e restrição de `agents`;
- Agent Customizations editor;
- padrão de labs e workflows já implementado no `01-lab`.

Não copiar artefatos das demos sem adaptar o cenário e a experiência do aluno. As demos são referência conceitual; o Lab 02 deve continuar diretamente a aplicação concluída no Lab 01.
