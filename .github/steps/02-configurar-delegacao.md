# Passo 2 — Configurar a delegação

**Tempo sugerido: 8 minutos**

Crie uma fronteira explícita entre coleta de evidências e julgamento:

1. Crie o agente **Pesquisador de critérios** em `.github/agents/`.
2. Oculte-o da seleção manual e permita sua invocação como subagente.
3. Disponibilize somente leitura e busca.
4. Use `agents: []` para impedir que o pesquisador delegue novamente.
5. Adapte o **Revisor da entrega** para:
   - usar a tool de subagentes;
   - permitir somente o pesquisador;
   - manter o julgamento final;
   - continuar sem tools de edição.
6. Adicione o handoff **Preparar correção** para o agente implementador.
7. Configure `send: true` para iniciar o diálogo de seleção e confirmação ao acionar o
   handoff.
8. Reabra **Chat: Open Customizations** e confira as duas definições.
9. Abra o seletor de agentes do Chat e confirme que somente o revisor pode ser escolhido
   manualmente.

O pesquisador deve levantar fatos; o revisor deve avaliar se as evidências sustentam os
critérios. Nenhum deles deve editar a entrega durante a revisão.

> [!TIP]
> Para os dois arquivos completos e a verificação na interface, consulte as
> [instruções completas](https://github.com/{{ repository }}/blob/main/.github/help/02-configurar-delegacao.md).

Quando as restrições e o handoff estiverem conferidos, comente `configurado`.
