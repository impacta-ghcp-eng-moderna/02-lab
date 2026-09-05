# Passo 4 — Corrigir e comprovar — Instruções completas

## 1. Preparar o handoff

1. No final do relatório, selecione **Corrigir lacuna selecionada**.
2. Confirme que o Chat mudou para o agente implementador.
3. Confirme que o prompt apareceu no campo de entrada sem ser enviado.
4. Substitua o início do prompt para deixá-lo específico:

```text
Corrija somente a lacuna de evidência do critério 8:
"treinamento existente sem inscritos retorna 200 com coleção vazia".

Não altere a especificação nem o código de produção e não amplie o escopo. Antes de editar,
indique os arquivos necessários. Depois da alteração, execute somente AttendeeListingTests,
relacione o resultado ao critério 8 e apresente o diff para revisão.
```

5. Leia o texto uma vez e somente então envie.

O fato de o prompt não ser enviado automaticamente comprova a supervisão humana configurada
por `send: false`.

## 2. Aprovar somente a alteração necessária

O agente deve indicar apenas:

```text
src/Tests/Api.Tests/AttendeeListingTests.cs
```

Se ele propuser alterar `Program.cs`, a especificação, contratos, persistência ou interface,
responda:

```text
Não aprovei esses arquivos. A implementação parece compatível e a lacuna é somente de
evidência. Restrinja a mudança a AttendeeListingTests.cs e adicione um único teste funcional
para o critério 8.
```

## 3. Conferir o teste produzido

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

## 4. Revisar o diff

Abra **Source Control** e selecione o arquivo alterado. Como alternativa:

```bash
git diff -- src/Tests/Api.Tests/AttendeeListingTests.cs
git diff -- docs/specs src/Api src/Application src/Infrastructure src/Client
```

O segundo comando não deve produzir saída.

## 5. Executar a validação focada

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

## 6. Registrar a conclusão

Use este registro:

```text
Critério 8: Atendido.
Evidência: ReturnsEmptyCollectionWhenTrainingHasNoAttendees executa a API pública para um
treinamento existente sem inscritos, confirma 200 OK e coleção vazia.
Validação: AttendeeListingTests passou.
Escopo: especificação e código de produção permaneceram inalterados.
```

Volte à issue e comente `comprovado`.
