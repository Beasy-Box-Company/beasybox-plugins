---
name: iniciar-projeto
description: Começa um projeto na Beasybox de ponta a ponta — escolhe ou cadastra o cliente, escolhe ou monta o playbook, pergunta o prazo e aplica o playbook ao cliente, criando a entrega e as tarefas. Use quando o consultor pedir para começar, abrir ou montar um projeto, uma consultoria ou uma entrega para um cliente.
---

# Começar um projeto na Beasybox

Um projeto, na Beasybox, é uma **entrega** num cliente, criada a partir de um **playbook**. Cada
etapa do playbook vira uma tarefa, com datas contadas para trás a partir do prazo. Este roteiro
junta as três peças. Nada é gravado sem o sim do consultor.

## Quando usar

- O consultor pediu para começar, abrir ou montar um projeto, uma consultoria ou uma entrega.
- Ele acabou de cadastrar um cliente e quer pôr o trabalho em andamento.

## Roteiro

1. **O cliente.** Chame `beasybox_list_clients`.
   - Se o cliente estiver lá, confirme qual é pelo nome e pelo CNPJ.
   - Se não estiver, siga o roteiro `criar-cliente` e volte para cá com o `client_id`.
2. **O playbook.** Chame `beasybox_list_playbooks` e sugira os que combinam com o que o consultor
   descreveu. Para cada um, mostre a área, o ciclo em dias e o número de etapas.
   - Se ele quiser ver as etapas, chame `beasybox_get_playbook`.
   - Se nenhum servir, siga o roteiro `criar-playbook` e volte para cá com o `playbook_id`.
   - Os playbooks da Beasybox (`origem: base`) podem ser aplicados normalmente. Só não se editam
     por aqui.
3. **O prazo.** Pergunte ao consultor a data de entrega. A data é **sempre** dele: não sugira
   uma calculada, não use a data de hoje mais o ciclo, não invente. Se ele disser "daqui a três
   semanas", converta para `YYYY-MM-DD` e confirme a data com ele.
4. **O nome.** Proponha `<playbook> · <cliente>` e deixe o consultor trocar.
5. **Mostre a entrega e espere o sim.**
   - Mostre o cliente, o playbook, o prazo e o nome.
   - Mostre a lista de tarefas que vão nascer: uma por etapa, cada uma com o período aproximado
     dentro do ciclo.
   - Se o ciclo do playbook não couber até o prazo (por exemplo, ciclo de 30 dias e prazo daqui a
     10), avise: as primeiras tarefas vão nascer com datas no passado.
6. **Com o sim, aplique.** Chame `beasybox_apply_playbook` com `client_id`, `playbook_id`,
   `due_date` e `name`. Cada chamada cria uma entrega nova: não repita.
7. **Entregue e ofereça o próximo passo.**
   - Passe ao consultor a `url` da entrega e a lista de tarefas com as datas.
   - As tarefas nascem como proposta: o consultor as confirma na tela.
   - Para começar o trabalho, ofereça o roteiro `executar-tarefa` na primeira tarefa, com o
     `task_id` dela.
