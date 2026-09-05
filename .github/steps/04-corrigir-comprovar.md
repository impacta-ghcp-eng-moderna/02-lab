# Passo 4 — Corrigir e comprovar

**Tempo sugerido: 10 minutos**

Use o relatório para tratar somente a conclusão do cenário vazio:

1. Selecione o handoff **Preparar correção**.
2. Confirme que o Chat mudou para o agente implementador e iniciou o diálogo automaticamente.
3. Quando solicitado, escolha a conclusão do critério 8 em **Não foi possível comprovar**.
4. Confirme novamente essa escolha.
5. Escolha revisar e confirmar as alterações antes da implementação.
6. Aceite somente uma proposta que altere o teste de listagem.
7. Autorize a inclusão de um único teste funcional para treinamento existente sem inscritos.
8. Revise o diff no **Source Control**.
9. Confirme que especificação e código de produção não mudaram.
10. Execute novamente somente `AttendeeListingTests`.
11. Relacione o teste novo ao critério 8 e registre a decisão final.

Não corrija outros itens, não altere o contrato e não transforme a falta de evidência em uma
mudança desnecessária de produção.

> [!TIP]
> Para o prompt final, o teste esperado, comandos e recuperação, consulte as
> [instruções completas](https://github.com/{{ repository }}/blob/main/.github/help/04-corrigir-comprovar.md).

Quando o diff estiver restrito e o teste focado passar, comente `comprovado`.
