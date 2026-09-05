# Passo 4 — Corrigir e comprovar — Instruções completas

## 1. Iniciar o handoff

1. No final do relatório, selecione **Preparar correção**.
2. Confirme que o Chat mudou para o agente implementador.
3. Como o handoff usa `send: true`, confirme que o diálogo começou automaticamente.
4. Quando o agente pedir a conclusão que deve ser corrigida, responda:

```text
Quero tratar o critério 8 apresentado em "Não foi possível comprovar": falta um teste que
crie um treinamento sem inscritos, consulte a listagem e verifique 200 OK com coleção vazia.
```

5. Quando o agente repetir a conclusão e pedir confirmação, responda:

```text
Sim, essa é a conclusão correta.
```

Se o agente repetir uma conclusão diferente, responda `Não` e reinicie a seleção.

## 2. Confirmar a alteração antes da implementação

Quando o agente perguntar se pode começar imediatamente ou se você deseja revisar e confirmar
as alterações, escolha:

```text
Quero revisar e confirmar as alterações antes da implementação.
```

O agente deve indicar somente:

```text
src/Tests/Api.Tests/AttendeeListingTests.cs
```

O sumário deve propor um único teste funcional para treinamento existente sem inscritos,
esperando `200 OK` e coleção vazia.

## 3. Aprovar somente a alteração necessária

Se a proposta estiver correta, responda:

```text
Confirmo. Pode implementar somente essa alteração.
```

Se ele propuser alterar `Program.cs`, a especificação, contratos, persistência ou interface,
responda:

```text
Não aprovei esses arquivos. A conclusão informa somente ausência de evidência automatizada.
Restrinja a mudança a AttendeeListingTests.cs e adicione um único teste funcional para o
critério 8.
```

## 4. Conferir o teste produzido

O teste deve:

1. criar um treinamento existente;
2. não cadastrar inscritos;
3. executar `GET /api/trainings/{trainingId}/attendees`;
4. verificar `200 OK`;
5. desserializar a coleção;
6. verificar que a coleção está vazia.

Se o agente não produzir o teste corretamente, use este fallback dentro da classe
`AttendeeListingTests`:

```csharp
[Fact]
public async Task ReturnsEmptyCollectionWhenTrainingHasNoAttendees()
{
	using var factory = new TrainingCatalogApiFactory();
	using var client = factory.CreateClient();
	var training = await CreateTraining(client, "2026-09-14");

	var response = await client.GetAsync($"/api/trainings/{training.Id}/attendees");

	Assert.Equal(HttpStatusCode.OK, response.StatusCode);
	var attendees = await response.Content.ReadFromJsonAsync<Attendee[]>();
	Assert.Empty(attendees!);
}
```

## 5. Revisar o diff

Abra **Source Control** e selecione o arquivo alterado. Como alternativa:

```bash
git diff -- src/Tests/Api.Tests/AttendeeListingTests.cs
git diff -- docs/specs src/Api src/Application src/Infrastructure src/Client
```

O segundo comando não deve produzir saída.

## 6. Executar a validação focada

```bash
dotnet test src/Tests/Api.Tests/TrainingCatalog.Api.Tests.csproj \
  --filter FullyQualifiedName~AttendeeListingTests
```

Todos os testes da classe devem passar, incluindo o cenário novo.

Se houver falha:

1. leia a primeira mensagem de erro;
2. não altere o contrato;
3. confirme que o treinamento foi criado antes do `GET`;
4. confirme que nenhum inscrito foi cadastrado;
5. corrija somente o teste e repita o mesmo comando.

## 7. Confirmar que o critério foi comprovado

Antes de avançar, confirme:

- [ ] `ReturnsEmptyCollectionWhenTrainingHasNoAttendees` executa a API pública;
- [ ] o teste usa um treinamento existente sem inscritos;
- [ ] o resultado esperado é `200 OK` com coleção vazia;
- [ ] `AttendeeListingTests` passou;
- [ ] especificação e código de produção permaneceram inalterados.

Se todos os itens estiverem confirmados, volte à issue e comente apenas `comprovado`.
