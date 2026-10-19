---
name: analise-de-credito
description: Roda a Análise de Crédito Bbox de um cliente de ponta a ponta, sem abrir a plataforma — acha o cliente, junta o Balanço e a DRE (no acervo, na conversa ou pedindo), manda a plataforma ler, confere o que ela acusar com o consultor, confirma, conclui com o sim dele e devolve o parecer, o link do relatório e o PDF. Use quando o consultor pedir para rodar, fazer ou refazer a análise de crédito, o diagnóstico de crédito ou o parecer de crédito de um cliente.
---

# Rodar a Análise de Crédito Bbox

A Análise de Crédito Bbox lê o **Balanço** e a **DRE** dos dois últimos exercícios de uma empresa,
calcula sete índices, dá um parecer (positivo, neutro ou negativo, no cenário base e no melhor) e
estima os limites de crédito e de giro. Na plataforma ela é um assistente em três etapas; aqui ela
é uma conversa — você é o assistente. A plataforma faz as contas; você junta os papéis, pergunta o
que faltar e **nunca inventa um número**.

Dois "sim" são obrigatórios, e o servidor os exige: para **começar quando vai cobrar** e para
**concluir**. Um terceiro, para **criar a entrega**. Sem o sim, a ferramenta recusa.

## Quando usar

- O consultor pediu para rodar, fazer, refazer ou atualizar a análise de crédito (ou o diagnóstico,
  ou o parecer de crédito) de um cliente.
- Ele mandou um balanço ou uma DRE e perguntou "como está essa empresa?".

## Roteiro

1. **Ache o cliente.** `beasybox_list_clients`; confirme pelo nome e pelo CNPJ. Se não estiver
   lá, siga o roteiro `criar-cliente` e volte com o `client_id`.
0. **Antes de tudo, o crédito.** Chame `beasybox_credit_analysis_start` com o `client_id`, **sem**
   `confirmado_pela_pessoa`. Se a resposta vier com `precisa_confirmar: true`, mostre: "Vai usar 1
   de N créditos de diagnóstico (ficam M)" e espere o sim; só então chame de novo com
   `confirmado_pela_pessoa: true`. A reanálise do mesmo cliente em até 30 dias é grátis e não
   pede o sim. Sem crédito, a recusa traz `compra_url`: entregue o link e pare.
2. **Abra a análise** (o passo 0 já abriu, em rascunho). A resposta traz `o_que_falta`: `setor`
   (pergunte: comércio, indústria ou serviço), `exercicios` (quais os dois anos) e `documentos`.
   Se o cliente já tinha uma análise **aberta**, `start` devolve essa mesma (`retomada: true`),
   sem cobrar: diga que está continuando a versão N de onde parou.
3. **Junte os papéis.** O `start` já devolve `documentos_no_acervo`: os balanços, DREs e planilhas
   que estão no acervo do cliente (`beasybox_search` só acha texto indexado — não confie nele para
   achar um balanço). Depois o que está nesta conversa (arquivos que o consultor anexou). Mostre o
   que achou, com nome e ano, e pergunte: **"são estes?"**. Nada encontrado? Peça: "Anexe o
   Balanço e a DRE dos dois últimos exercícios (PDF, planilha ou foto)". Nunca siga com papéis que
   o consultor não confirmou.
4. **Suba.** Para cada arquivo, `beasybox_credit_analysis_attach`:
   - já no acervo: `document_id` (mais `tipo` e `exercicio`, se souber);
   - na sua máquina ou sandbox (Claude Code, Cowork): `arquivo: { nome, tamanho_bytes }` → a
     resposta traz a `upload_url` e o `curl` pronto; rode o comando com o caminho local. O link
     vale uma vez e por 15 minutos. Se o `curl` falhar por rede, mande ao consultor a
     `pagina_de_envio_url` da mesma resposta: ele abre, solta o arquivo e a página envia;
   - no claude.ai, um PDF ou uma planilha que você consegue LER: `paginas: [{ numero, texto }]`
     com o texto de cada página (e `nome_original`) — a plataforma guarda um `.txt` por página e
     o lê como os outros; até 300 KB por chamada, divida em partes se passar (com os números originais das páginas);
   - no claude.ai, uma foto ou um scan (que você não lê como texto): `arquivo: { nome,
     tamanho_bytes }` e mande ao consultor a `pagina_de_envio_url` — ele solta o arquivo lá.
   Até 10 arquivos de 20 MB: PDF, PNG, JPG, XLSX ou CSV (o `.txt` das páginas, 300 KB).
5. **Leia.** `beasybox_credit_analysis_extract` com o `lock_version` atual, **uma vez, depois de
   anexar tudo** (chamar de novo refaz a leitura inteira). Ela espera a leitura até 45 s; se vier
   `estado: "em_andamento"`, chame `beasybox_credit_analysis_get` até a leitura terminar. Mostre
   ao consultor o que a leitura achou — por exercício, as linhas lidas e os arquivos `lidos` e
   `falhos` — e **o que a `verificacao` acusou**. Para cada bloqueio ou
   alerta, pergunte: linha faltando → "qual o valor?"; incoerência → "confere? qual é o certo?";
   duas empresas → "qual é a principal?"; unidade incerta → "está em reais ou em milhares?".
   Grave as respostas com `beasybox_credit_analysis_set_values` (e `setor`/`exercicios` por lá
   também; até 80 células por chamada). Se `extract` devolver `estado: "indisponivel"`, leia os arquivos você mesmo e grave
   tudo com `set_values`. Repita `get` até a `verificacao` ficar sem `bloqueios`.
6. **Confirme.** `beasybox_credit_analysis_confirm`. Mostre o `resultado_resumido`: parecer (base
   e melhor cenário), limites de crédito e de giro, os positivos de sete índices e os alertas.
   Diga que a leitura da IA (a narrativa) está sendo escrita em segundo plano.
7. **Conclua com o sim.** Pergunte: "Concluo a análise? Isso fecha a versão, grava o cartão de
   Contexto do cliente e gera o PDF." Só com o sim, `beasybox_credit_analysis_complete` com
   `confirmado_pela_pessoa: true` (ela espera a leitura da IA até 50 s; se a narrativa ainda não
   saiu, a análise conclui sem ela e o PDF diz isso). Entregue **os três**: o resumo em uma frase (`resumo`), o `link` do relatório na plataforma e o
   `pdf_download_url` (baixa sem login, vale 7 dias).
8. **A entrega, com o sim.** Pergunte se ele quer criar a entrega do diagnóstico para o cliente.
   Com o sim: `beasybox_list_playbooks`, ofereça primeiro os financeiros, pergunte o prazo (sempre
   dele), e chame `beasybox_apply_playbook` com `credit_analysis_id` e
   `confirmado_pela_pessoa: true`. Devolva o link da entrega.

## O que nunca fazer

- **Inventar ou "estimar" um valor de balanço.** Linha faltando é pergunta ao consultor, não chute.
- **Começar uma análise cobrada, concluir ou criar a entrega sem o sim** dito na conversa.
- **Seguir com papéis que o consultor não confirmou** ("são estes?") ou de outro cliente.
- **Rodar `complete` para "ver o resultado"**: o resultado já está em `confirm`; concluir é
  definitivo e gasta a versão.
- **Subir o mesmo arquivo duas vezes** ou pedir um link novo para um arquivo que já subiu.
