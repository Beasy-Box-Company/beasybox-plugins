---
name: importar-contexto
description: Varre o que você já sabe das conversas passadas do consultor — empresas, fatos e documentos —, mostra tudo para ele aprovar, e envia o aprovado para a Beasybox triar. Use quando o consultor pedir para importar o contexto dele, quando colar o pedido do botão "Importar meu contexto do Claude", ou na primeira vez que ele conectar a Beasybox.
---

# Importar o contexto que você já tem do consultor

Você tem, nas suas conversas passadas com este consultor, coisas que a Beasybox ainda não sabe:
empresas que ele mencionou, fatos sobre elas, documentos que produziu. Este roteiro é a ordem
certa de achar isso, mostrar a ele, e mandar para a plataforma o que ele aprovar.

Há **dois portões** nesta importação. O primeiro é seu, aqui na conversa: nada sai para a
Beasybox sem o consultor ver e aprovar antes. O segundo é da plataforma: o que você envia cai
num lote pendente, e o consultor ainda decide, na tela de triagem, o que entra de fato na
carteira. Você só controla o primeiro.

## Quando usar

- O consultor pediu para importar, trazer ou puxar o contexto dele para a Beasybox.
- Ele colou o texto do botão "Importar meu contexto do Claude".
- É a primeira vez que ele conecta a Beasybox e ainda não tem nada na carteira.

## Roteiro

1. **Comece pelo briefing.** Chame `beasybox_get_import_briefing`. Ele traz a carteira atual (o
   que já é cliente cadastrado, com `client_id`) e os lotes que você ou uma sessão anterior já
   enviaram — abertos e já triados. Sem isso você corre o risco de propor de novo uma empresa ou
   um fato que a plataforma já tem, ou de duplicar um lote que ainda está esperando o consultor
   decidir.

2. **Monte um plano de busca — e seja honesto sobre o alcance dele.** A busca nas suas conversas
   passadas é por relevância, não uma enumeração: não existe "listar todas as conversas". Você
   **não varre tudo**, e não deve dizer ao consultor que varreu. O que dá para fazer, e o que este
   roteiro pede, é uma bateria de buscas:
   - uma busca pelo nome de cada empresa que o briefing já lista na carteira;
   - depois, buscas pelos termos genéricos de trabalho de consultoria: cliente, proposta,
     contrato, diagnóstico, reunião, escopo, orçamento.
   Toda vez que uma dessas buscas trouxer uma empresa nova (que não estava na carteira nem já
   apareceu numa busca anterior), isso vira mais uma busca — pelo nome dela. Diga ao consultor,
   quando terminar, que essa importação pode ser rodada de novo mais tarde e vai acumular: a
   completude vem de repetir o processo em conversas futuras, não de uma passada única e
   definitiva.

3. **O portão 1 é seu — não pule para o envio.** Antes de chamar `beasybox_import_context`,
   mostre ao consultor, agrupado por empresa, o que você achou: de qual conversa veio, e o que
   você propõe gravar (empresa, fato ou documento). Espere a aprovação dele. Um resumo em número
   ("achei 40 coisas, posso mandar?") não é aprovação — ele precisa ver os itens. E diga a ele,
   nestas palavras, para onde isso vai se ele aprovar: **o workspace da organização dele, visível
   a todos os consultores dela.** Sem essa frase, ele pode achar que está aprovando algo que só
   ele vê.

4. **Filtre antes de propor, não depois.** Duas coisas nunca chegam a virar item, muito menos a
   ir para `beasybox_import_context`:
   - Senha, token, chave, ou qualquer credencial — nem resumida, nem no trecho original.
   - Assunto que não é trabalho de cliente (vida pessoal, outro contratante, algo de terceiro).
   Descartar depois de propor, deixando o consultor recusar item a item, é tarde demais: ele já
   viu o que devia ter sido cortado por você.

5. **Envie em lotes, depois da aprovação.** Até 50 itens por chamada de `beasybox_import_context`.
   Se sobrar mais, use o `import_id` que a primeira chamada devolveu nas chamadas seguintes, para
   somar ao mesmo lote em vez de abrir um novo a cada vez. Se a ferramenta recusar algum item, a
   resposta traz o motivo junto com o limite que faltou — corrija e reenvie só os itens recusados,
   não o lote inteiro de novo. Um fato comum tem teto de 280 caracteres em `text`; só um fato de
   seção `resumo`, e os itens de empresa e de documento, vão até 600 — um modelo que ignore essa
   diferença escreve parágrafos e tem os fatos recusados por `texto_longo`.

6. **Termine com o link, não com "pronto".** Chame `beasybox_get_import_status` com o `import_id`
   e passe ao consultor o endereço da triagem que ele devolve. **Não diga que o contexto "já está
   na plataforma"** — ele está num lote pendente, esperando o consultor aprovar item a item na
   tela de triagem. O trabalho dele não terminou quando você termina o seu.

## O que nunca fazer

- Chamar `beasybox_import_context` antes de o consultor ver os itens e aprovar.
- Dizer que a busca "varreu todas as conversas" — ela buscou pelo que este roteiro mandou buscar.
- Enviar senha, token, chave, ou assunto que não é de cliente, mesmo que só no trecho original.
- Prometer 600 caracteres para um fato fora da seção `resumo` — o teto dele é 280.
- Dizer ao consultor que o contexto "entrou" ou "já está lá" antes da triagem dele.
- **Propor um fato com `section: "resumo"`.** O cartão de resumo de cada cliente é ÚNICO e
  gerado automaticamente pela plataforma, que apaga o anterior antes de gravar o novo toda vez
  que roda. Um fato de resumo que você importar e o consultor aprovar hoje é apagado em SILÊNCIO
  na próxima geração — sem aviso na tela, sem erro em lugar nenhum. Use qualquer outra seção
  (empresa, pessoas, mercado, dores, oportunidades, decisoes, riscos) para o mesmo conteúdo.

## Se a conexão não estiver disponível

Se as ferramentas `beasybox_get_import_briefing`, `beasybox_import_context` e
`beasybox_get_import_status` não aparecem nesta conversa, o consultor precisa conectar a
Beasybox em Integrações, na plataforma, e autorizar esta conta do Claude antes de importar.
