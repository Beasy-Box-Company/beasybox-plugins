---
name: criar-playbook
description: Monta com o consultor um playbook da Beasybox — o método de um tipo de entrega, com etapas, dias, metodologia, critérios de aceite, erros comuns e a Entrega final — e o grava na biblioteca depois do sim. Use quando o consultor pedir para criar, montar ou escrever um playbook, transformar um trabalho feito em método, ou editar um playbook da casa.
---

# Montar um playbook na Beasybox

Um playbook é o **método** de um tipo de entrega, como "Diagnóstico comercial" ou "Estratégia de
marca". Ele se aplica a vários clientes, e cada aplicação vira uma entrega com uma tarefa por
etapa. A regra que vale para tudo o que você escrever é esta: **o documento é um caso; o playbook
é o método.** Nada de um cliente específico entra no playbook: nem nome, nem número, nem pessoa.

## Quando usar

- O consultor pediu para criar, montar ou escrever um playbook.
- Ele quer transformar um trabalho já feito num método repetível.
- Ele quer mudar um playbook da casa. Nesse caso, vá direto à seção "Editar".

## Roteiro

1. **Confira a biblioteca.** Chame `beasybox_list_playbooks`. Se já existir um playbook para o
   mesmo tipo de entrega, mostre-o e pergunte se é para editar esse ou criar outro.
2. **Entreviste o consultor, uma pergunta por vez.**
   - O que esse método entrega, no fim, e para que tipo de cliente.
   - Em quantos dias o ciclo inteiro acontece (de 1 a 90).
   - Em que etapas o trabalho se divide, na ordem, e quanto tempo cada uma leva.
   - O que cada etapa produz (o output) e o checklist de cada uma.
   - Como ele sabe que a entrega está boa (critérios de aceite) e onde as pessoas erram (erros
     comuns).
   - Se ele partir de um documento ou de um trabalho feito para um cliente, extraia o método e
     tire o caso.
3. **Monte o rascunho.**
   - **Nome:** o tipo de entrega, nunca o cliente.
   - **Área:** a área da consultoria (Marketing, Finanças, Operações…), não o setor do cliente.
   - **Etapas:** `start_day` e `end_day` contados do dia 1 do ciclo. Nenhuma termina depois de
     `cycle_days`, e as etapas podem se sobrepor. Cada uma leva briefing (o que fazer), outputs
     (o que entrega), subtarefas (o checklist) e, quando o consultor descrever, o método: técnica,
     passos, "pronto quando" e "onde trava".
   - **Metodologia:** as sete seções — objetivo, público, princípios, fases, técnicas, papéis e
     sinais. Escreva só as que a conversa sustenta e deixe as outras vazias.
   - **Briefing, aceite e erros comuns:** curtos e verificáveis.
   - **Entrega final** (`skill_md`): como a casa faz esta entrega — formato, estrutura, tom e o
     que nunca pode faltar —, em markdown, com seções `## `, em até 6000 caracteres. Este texto
     entra no briefing de toda tarefa feita com o playbook.
4. **Mostre o rascunho inteiro e espere o sim.** Mostre as etapas numa tabela (dias, nome,
   output) e depois o resto. Ajuste o que o consultor pedir.
5. **Com o sim, grave.** Chame `beasybox_create_playbook`. Cada chamada cria um playbook novo:
   não repita para "corrigir". Para corrigir, use `beasybox_update_playbook`.
6. **Entregue o link e ofereça o próximo passo.** Passe ao consultor a `url` e pergunte se ele
   quer aplicar o playbook a um cliente (`iniciar-projeto`). O exemplo fictício da Entrega final
   é gerado pelo botão na tela do playbook, e não por aqui.

## Editar

1. Leia o playbook com `beasybox_get_playbook`.
2. Se `origin` for `base`, o playbook é da Beasybox e não se edita pelo conector. Diga ao
   consultor que, para mudar, ele cria uma cópia da casa na tela de Playbooks. A cópia pode ser
   editada por aqui depois.
3. Mostre o que vai mudar e espere o sim.
4. Chame `beasybox_update_playbook` só com os campos que mudam.
   - Em `methodology`, mande só as seções alteradas.
   - Em `stages`, mande a lista **inteira**, na ordem: a etapa que não vier é apagada. A etapa
     que mantém o nome conserva o documento de exemplo que tiver na tela.
5. Cada edição sobe a versão do playbook. As entregas já criadas com ele não mudam.
