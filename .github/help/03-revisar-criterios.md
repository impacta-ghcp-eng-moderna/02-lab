# Passo 3 — Revisar os critérios 8 e 9 — Instruções completas

## 1. Iniciar a revisão

1. Abra uma conversa nova no Chat.
2. Selecione **Revisor da entrega**.
3. Envie exatamente:

```text
Revise somente os critérios 8 e 9 de
`docs/specs/training-attendees-vertical-slice.md`.

Delegue o levantamento inicial ao subagente `Pesquisador de critérios`. Forneça a ele uma
tarefa autossuficiente que indique a especificação, `src/Api/Program.cs`,
`src/Tests/Api.Tests/AttendeeListingTests.cs` e o formato de evidências esperado.

Depois, faça você mesmo o julgamento final. Execute somente:

dotnet test src/Tests/Api.Tests/TrainingCatalog.Api.Tests.csproj --filter FullyQualifiedName~AttendeeListingTests

Não altere arquivos e não revise outros critérios.
```

Quando o VS Code mostrar a confirmação nativa do terminal, autorize somente esse comando.

## 2. Inspecionar a delegação

Expanda a chamada do subagente no Chat e confirme:

- nome **Pesquisador de critérios**;
- critérios 8 e 9 no prompt recebido;
- os três caminhos indicados;
- formato de evidências solicitado;
- somente tools de leitura e busca;
- ausência de comandos e edições.

Se outro agente for usado, interrompa a revisão, confira o campo `agents` do revisor e envie
novamente o prompt em uma conversa nova.

## 3. Conferir manualmente uma evidência

Abra `src/Tests/Api.Tests/AttendeeListingTests.cs`.

Confirme que existe um teste que:

1. cria um treinamento;
2. cadastra um inscrito;
3. consulta a listagem;
4. verifica o inscrito retornado.

Esse cenário é evidência para o critério 9. Não use a presença do endpoint ou o sucesso geral
da classe como substituto para um cenário não exercitado.

## 4. Interpretar o relatório

O relatório esperado deve concluir:

| Critério | Situação esperada antes da correção | Motivo |
| --- | --- | --- |
| 8 — treinamento existente sem inscritos | **Não foi possível comprovar** | a implementação parece compatível, mas falta o teste funcional exigido para a coleção vazia |
| 9 — treinamento existente com inscritos | **Atendido** | implementação e teste funcional exercitam a coleção com inscrito |

Se o critério 8 aparecer como **Não atendido**, peça ao revisor para identificar a evidência de
comportamento contrário. Ausência de teste, sozinha, não comprova defeito funcional.

## 5. Confirmar ausência de edição

Execute:

```bash
git status --short
```

Antes do próximo passo, somente os dois arquivos de agentes criados no passo 2 podem aparecer.
A revisão não deve adicionar outras mudanças.

Volte à issue e comente `revisado`.
