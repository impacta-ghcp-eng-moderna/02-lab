# Passo 2 — Configurar a delegação

**Tempo sugerido: 8 minutos**

Crie uma fronteira explícita entre coleta de evidências e julgamento:

1. Crie o agente **Pesquisador de critérios** em `.github/agents/`.
2. Oculte-o da seleção manual e permita sua invocação como subagente.
3. Disponibilize somente leitura e busca.
4. Impeça nova delegação pelo pesquisador.
5. Adapte o **Revisor da entrega** para:
   - usar a tool de subagentes;
   - permitir somente o pesquisador;
   - manter o julgamento final;
   - continuar sem tools de edição.
6. Adicione o handoff **Corrigir lacuna selecionada** para o agente implementador.
7. Configure `send: false` para manter a decisão humana antes do envio.
8. Reabra **Chat: Open Customizations** e confira as duas definições.

O pesquisador deve levantar fatos; o revisor deve avaliar se as evidências sustentam os
critérios. Nenhum deles deve editar a entrega durante a revisão.

> [!TIP]
> Para os dois arquivos completos e a verificação na interface, consulte as
> [instruções completas](https://github.com/{{ repository }}/blob/main/.github/help/02-configurar-delegacao.md).

Quando as restrições e o handoff estiverem conferidos, comente `configurado`.
