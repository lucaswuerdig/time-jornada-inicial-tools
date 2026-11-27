---
name: explicar-mudanca
description: Explica o que uma branch muda em relação à branch de origem, para quem vai revisar código escrito pela IA. Descobre a base, lê o diff e o pedido (tarefa, PRD, plano em .specs/) e entrega um roteiro de leitura, a aderência ao pedido, os pontos de atenção e perguntas para validar o entendimento. Use quando alguém pedir para explicar, entender, resumir ou "me situar" no que a branch faz, perguntar "o que a IA fez aqui?", ou quiser se preparar para revisar a própria branch antes do PR, mesmo sem falar em "diff". Não é para revisar o PR de outra pessoa com veredito (use revisar-pr) nem para escrever a descrição do PR (use descricao-de-pr).
argument-hint: "[branch de origem]"
---

# Explicar mudança

Branch de origem informada: $ARGUMENTS

Quando a IA escreve o código, quem pediu a tarefa vira revisor de algo que não digitou. O `git diff` cru mostra arquivos em ordem alfabética, mistura o essencial com o acessório e não diz por que cada coisa está ali. O resultado costuma ser uma leitura rápida demais, e é aí que passam os erros típicos de código gerado: uma mudança que ninguém pediu, um teste que só testa o mock, uma função que parece certa mas trata o caso errado.

Esta skill não revisa no lugar da pessoa. Ela prepara a pessoa para revisar: explica o que muda, em que ordem ler, onde olhar com cuidado e o que perguntar. Por isso não dá veredito de aprovação e não altera arquivos.

## Fluxo

### 1. Descubra a branch de origem

Comparar com a base errada gera uma explicação errada com cara de certa, então vale gastar um momento aqui. Siga esta ordem e pare na primeira que responder:

1. A branch informada em `$ARGUMENTS`, se houver.
2. A base do PR aberto para a branch, quando a sessão tiver ferramenta para isso (no GitHub, `gh pr view --json baseRefName -q .baseRefName`). Falha ou ausência de PR não é problema, só siga.
3. As candidatas de sempre (`main`, `master`, `develop`, e as versões `origin/` delas que existirem). Para cada uma, conte os commits da branch atual desde o ponto de partida: `git rev-list --count $(git merge-base <candidata> HEAD)..HEAD`. A base provável é a de menor contagem. Se duas empatarem ou a branch atual for a própria candidata, pergunte.

Diga no relatório qual base foi usada e como foi escolhida. Se a referência local estiver atrás do remoto, rode `git fetch` antes.

Com a base em mãos:
- `git log --oneline <base>..HEAD` para os commits;
- `git diff --stat <base>...HEAD` para o tamanho de cada arquivo;
- `git diff <base>...HEAD` para o código. Os três pontos comparam com o ponto de partida da branch, e não com o estado atual da base, que pode ter andado.
- `git status --short`: se houver mudança não commitada, avise, porque ela não entra no diff acima. Pergunte se deve incluir (`git diff <base>` sem `...HEAD` inclui o que está na pasta de trabalho).

### 2. Encontre o pedido

A aderência ao pedido é a seção que mais ajuda em código de IA, porque a IA tende a fazer um pouco mais, ou um pouco diferente, do que foi pedido. Procure o pedido nesta ordem:

- `.specs/features/*/` (padrão do `tlc-spec-lean`): `request.md` tem a tarefa e o PRD como vieram, `plan.md` tem o plano aprovado e os critérios de aceite, `review.md` tem o parecer do revisor. Quando há várias pastas, use a que aparece no diff ou cuja chave bate com a branch.
- A chave do Jira no nome da branch ou nos commits (ex.: `ABC-123`). Se a sessão tiver o MCP do Jira, leia a tarefa.
- As mensagens de commit e o que a pessoa disse na conversa.

Se não encontrar nada, diga isso na seção de aderência e baseie a comparação só nos commits. Não invente critérios.

### 3. Leia além do diff

O diff mostra as linhas que mudaram, mas o significado delas depende do que está em volta. Para cada mudança relevante, leia o arquivo inteiro quando precisar, procure quem chama a função alterada (`grep`) e confira se o que o código usa existe de fato no projeto. Isso é o que permite afirmar algo em vez de supor.

Separe o ruído antes de explicar: lockfiles, arquivos gerados, snapshots, cópias de código de terceiros, renomeações puras e formatação. Eles entram no relatório em uma linha só, para que quem revisa não gaste atenção ali. Mas confira que o ruído é mesmo ruído: uma "cópia de terceiros" com uma linha alterada no meio não é mais cópia.

Em diffs grandes, comece pelo `--stat` e pelos commits para montar o mapa, e só depois leia o código por grupo de intenção.

### 4. Escreva o relatório

Use o modelo abaixo e mostre na conversa.

## Modelo

```markdown
# O que esta branch faz

**Branch:** `<atual>` · **Base:** `<base>` (<como foi escolhida>) · **Commits:** N · **Arquivos:** N (+X −Y)
**Pedido:** <onde foi encontrado, ou "não encontrado">

## Em uma frase

<O comportamento que muda para quem usa o sistema, não a lista de arquivos.>

## O que muda

<Agrupado por intenção. Cada grupo diz o que faz, por que existe, e os arquivos que o compõem. Exemplo: "Novo endpoint de cancelamento (`api/cancelamento.py`, `rotas.py`)". Termine com uma linha para o ruído, se houver: "Sem revisão necessária: `poetry.lock`, 12 arquivos em `vendor/`".>

## Roteiro de leitura

1. `caminho/arquivo.ext:linha` — o que olhar aqui e por que começar por ele.
2. ...

<Do ponto de entrada (rota, comando, handler, tela) para dentro, e por último os testes. Cada passo diz o que confirmar naquele trecho, não só o nome do arquivo.>

## Aderência ao pedido

| Item do pedido | Situação | Onde |
| --- | --- | --- |
| <critério de aceite ou item do plano> | Feito / Parcial / Não encontrado | `arquivo:linha` |

**Além do pedido:** <mudanças que nenhum item pede, com arquivo. "Nenhuma." se não houver.>

## Pontos de atenção

- **<título curto>** — `arquivo:linha`. O fato, e por que merece olhar cuidadoso.

## Perguntas para validar o entendimento

- <Pergunta que quem revisa deveria saber responder antes de aceitar a mudança.>
```

## O que procurar nos pontos de atenção

Estes são os deslizes mais comuns de código gerado por IA. Use como lente, não como checklist a preencher: só entra no relatório o que você encontrou no código.

- **Mudança fora do escopo**: refatoração, renomeação ou "melhoria" em arquivo que o pedido não toca.
- **Teste que não testa**: o teste verifica o mock, repete a implementação, ou passa mesmo se o código estiver errado. Critério de aceite sem nenhum teste correspondente.
- **Erro engolido**: `try/except` ou `catch` genérico que esconde falha, valor padrão que mascara dado ausente.
- **Coisa inventada**: função, método, parâmetro, variável de ambiente ou configuração usada no código e que não existe no projeto nem na biblioteca.
- **Duplicação**: lógica nova que já existia em outro lugar do projeto.
- **Código morto ou sobra**: função não chamada, `TODO`, `print` de depuração, arquivo de rascunho.
- **Mudança com efeito fora do diff**: migração de banco, contrato de API, variável de ambiente nova, permissão, dependência nova.
- **Segurança**: segredo no código, entrada sem validação, consulta montada por concatenação. Para uma checagem completa, indique o `/security-check`.

## Regras

- Escreva em português, com frases completas.
- Cite `arquivo:linha` sempre que falar de código. O relatório é um mapa para o diff: sem o endereço, quem revisa precisa procurar sozinho.
- Afirme só o que foi conferido no código. Suposição vai marcada como "confirmar".
- Explique, não julgue. Nada de "aprovado" ou "pode mergear": essa decisão é de quem revisa. Se a pessoa quiser um parecer, indique o `/revisar-pr` ou o agent `revisor-de-codigo`.
- Não altere arquivos, não faça commit e não rode o código. Se encontrar um bug, ele vai para os pontos de atenção.
- Ajuste o tamanho ao diff. Uma correção de três linhas cabe em um parágrafo e dois passos de roteiro. Seção sem conteúdo leva "Nenhum." em vez de ser inventada.
