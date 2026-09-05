# Passo 4 — Corrigir e comprovar

**Tempo sugerido: 10 minutos**

Use o relatório para corrigir somente a lacuna de evidência do cenário vazio:

1. Selecione o handoff **Corrigir lacuna selecionada**.
2. Confirme que o agente mudou e que o prompt foi preenchido, mas não enviado.
3. Ajuste o prompt para identificar somente o critério 8.
4. Envie o prompt e aceite apenas um plano que altere o teste de listagem.
5. Autorize a inclusão de um único teste funcional para treinamento existente sem inscritos.
6. Revise o diff no **Source Control**.
7. Confirme que especificação e código de produção não mudaram.
8. Execute novamente somente `AttendeeListingTests`.
9. Relacione o teste novo ao critério 8 e registre a decisão final.

Não corrija outros itens, não altere o contrato e não transforme a falta de evidência em uma
mudança desnecessária de produção.

> [!TIP]
> Para o prompt final, o teste esperado, comandos e recuperação, consulte as
> [instruções completas](https://github.com/{{ repository }}/blob/main/.github/help/04-corrigir-comprovar.md).

Quando o diff estiver restrito e o teste focado passar, comente `comprovado`.
