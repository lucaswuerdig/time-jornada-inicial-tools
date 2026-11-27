# time-jornada-inicial-tools

Repositório do time Jornada Inicial para guardar e compartilhar **agents** e **skills** usados no dia a dia com o Claude Code.

A ideia é ter um lugar único, versionado e revisável, para que o que uma pessoa cria e funciona bem fique disponível para o time todo.

## O que fica aqui

| Tipo | O que é | Quando usar |
| --- | --- | --- |
| **Agent** | Um subagente com papel, instruções e ferramentas próprias. | Tarefas delegáveis com escopo fechado, como revisar um PR, investigar um bug ou escrever testes. |
| **Skill** | Um conjunto de instruções (e arquivos de apoio) que o Claude carrega quando a tarefa pede. | Procedimentos repetíveis do time, como passos de deploy, checklist de revisão ou padrões de um projeto. |

## Estrutura

Os arquivos ficam em `.claude/`, no mesmo formato que o Claude Code lê. Assim, ao abrir o Claude Code dentro deste repositório, tudo já está disponível sem configuração.

```
.
└── .claude/
    ├── agents/
    │   ├── executor.md
    │   ├── pesquisador.md
    │   ├── revisor-de-codigo.md
    │   ├── revisor.md
    │   └── varredor.md
    └── skills/
        ├── coding-guidelines/
        │   └── SKILL.md
        ├── descricao-de-pr/
        │   ├── SKILL.md
        │   └── templates/
        │       ├── feature.md
        │       ├── fix.md
        │       └── infra.md
        ├── desenvolver-tarefa/
        │   └── SKILL.md
        ├── gerar-prd/
        │   └── SKILL.md
        ├── refinar-tarefa/
        │   └── SKILL.md
        ├── revisar-pr/
        │   └── SKILL.md
        ├── security-check/
        │   └── SKILL.md
        ├── setup/
        │   ├── SKILL.md
        │   ├── checar.sh
        │   ├── orquestracao.py
        │   └── orquestracao/
        └── tlc-spec-lean/
            ├── SKILL.md
            ├── references/
            └── scripts/
```

O guia do fluxo fica em [docs/fluxo-de-desenvolvimento.md](docs/fluxo-de-desenvolvimento.md).

- Cada agent é um arquivo `.md` em `.claude/agents/`.
- Cada skill é uma pasta em `.claude/skills/` com um `SKILL.md` dentro, mais os arquivos de apoio que ela precisar.
- Nomes em `kebab-case`, descrevendo o que a ferramenta faz.

## Disponíveis

| Nome | Tipo | O que faz |
| --- | --- | --- |
| [refinar-tarefa](.claude/skills/refinar-tarefa/SKILL.md) | Skill | Refina uma tarefa do Jira com pouca informação, de qualquer tipo (funcionalidade, bug, técnica, segurança ou investigação): diz o que entendeu, tira as dúvidas com quem pediu, reescreve a descrição e, depois do aceite, grava no Jira e move a tarefa para "Refinado". Precisa do MCP do Jira configurado. Uso: `/refinar-tarefa ABC-123` ou `/refinar-tarefa <link da tarefa>`. |
| [revisor-de-codigo](.claude/agents/revisor-de-codigo.md) | Agent | Revisa as alterações locais ou de uma branch em busca de bugs, riscos e pontos de melhoria. |
| [descricao-de-pr](.claude/skills/descricao-de-pr/SKILL.md) | Skill | Escreve a descrição de um pull request a partir das alterações da branch atual. |
| [gerar-prd](.claude/skills/gerar-prd/SKILL.md) | Skill | Gera o PRD de uma tarefa do Jira já refinada, de qualquer tipo: ajusta o tamanho do documento ao tipo da tarefa, tira as dúvidas, escreve o documento e, depois do aceite, grava como comentário na tarefa e move para "PRD em revisão". Precisa do MCP do Jira configurado. Uso: `/gerar-prd ABC-123` ou `/gerar-prd <link da tarefa>`. |
| [setup](.claude/skills/setup/SKILL.md) | Skill | Prepara a máquina: copia os agents e as skills para a pasta pessoal do Claude Code, configura o MCP do Jira e oferece o kit de orquestração. Só funciona dentro deste repositório. |
| [desenvolver-tarefa](.claude/skills/desenvolver-tarefa/SKILL.md) | Skill | Desenvolve uma tarefa do Jira pelo fluxo spec-driven: lê a tarefa e o PRD, prepara o repositório do projeto e conduz o plano, a implementação em fatias e a revisão independente com o `tlc-spec-lean`, fechando com checagem de segurança, descrição do PR e a atualização do status da tarefa no Jira. Precisa do MCP do Jira configurado. Uso: `/desenvolver-tarefa ABC-123`, no repositório do projeto. |
| [tlc-spec-lean](.claude/skills/tlc-spec-lean/SKILL.md) | Skill | Framework de desenvolvimento spec-driven em três fases: um plano aprovado por uma pessoa, a construção em fatias com um commit cada e uma revisão por um subagente que não escreveu o código. Traz scripts em Python 3 que conferem o plano e a mensagem de commit. De terceiros, em inglês. |
| [revisar-pr](.claude/skills/revisar-pr/SKILL.md) | Skill | Revisa o PR de outra pessoa e entrega um relatório classificado em Blocking, High, Medium e Suggestions, com a regra de quando aprovar e o comentário pronto para o PR. |
| [security-check](.claude/skills/security-check/SKILL.md) | Skill | Checagem de segurança do projeto, de um caminho ou só das mudanças pendentes (`/security-check diff`): vulnerabilidades, segredos vazados, injeção e problemas de autenticação. |
| [coding-guidelines](.claude/skills/coding-guidelines/SKILL.md) | Skill | Diretrizes de comportamento para reduzir erros comuns de IA ao escrever código. De terceiros, em inglês. |
| [varredor](.claude/agents/varredor.md) | Agent | Trabalho mecânico com critério objetivo, no Haiku: varrer arquivos, contar, listar, extrair, resumir um log ou um documento longo. Só lê. |
| [pesquisador](.claude/agents/pesquisador.md) | Agent | Pesquisa em código e documentação, no Sonnet: onde algo mora, como um fluxo funciona, onde algo é usado. Só lê. |
| [executor](.claude/agents/executor.md) | Agent | Implementa uma mudança já delimitada, no Sonnet: código, testes, correção de bug com reprodução conhecida. |
| [revisor](.claude/agents/revisor.md) | Agent | Revisa um diff ou um conjunto de arquivos contra os critérios que recebe, no Sonnet. Só lê. É o revisor independente do kit de orquestração. |

O `revisor-de-codigo` e a `descricao-de-pr` servem de exemplo para quem for criar os próximos.

### Skills de terceiros

Estas skills não foram escritas pelo time. São cópias sem alteração, mantidas aqui para todo mundo usar a mesma versão.

| Skill | Origem | Licença |
| --- | --- | --- |
| `tlc-spec-lean` 3.2.0 | [Tech Lead's Club](https://github.com/tech-leads-club/agent-skills), derivada do `tlc-spec-driven` de Felipe Rodrigues | CC-BY-4.0 |
| `coding-guidelines` 1.0.0 | Catálogo do [Tech Lead's Club](https://github.com/tech-leads-club/agent-skills), autor "ale", com base nas diretrizes de Andrej Karpathy | CC-BY-4.0, que é o padrão do catálogo. A skill não declara licença própria. |

Para atualizar uma delas, substitua a pasta pela versão nova e ajuste a versão nesta tabela. Não edite os arquivos: uma cópia alterada deixa de bater com a original e complica a próxima atualização.

## Fluxo de desenvolvimento

O time trabalha no modelo spec-driven: a tarefa é refinada no Jira, ganha um PRD quando precisa e só então vai para o desenvolvimento, que usa o TLC Spec Lean para planejar, construir em fatias e revisar.

```
/refinar-tarefa  →  /gerar-prd  →  /desenvolver-tarefa  →  /revisar-pr
```

O passo a passo, com quem faz cada etapa e o que sai dela, está em [docs/fluxo-de-desenvolvimento.md](docs/fluxo-de-desenvolvimento.md).

## Kit de orquestração

Opcional, para quem desenvolve. Com o kit, a sessão principal do Claude Code passa a coordenar, e o trabalho mecânico ou volumoso vai para subagentes com modelos mais baratos. A regra pede que toda mudança de arquivo feita por um subagente passe por um revisor independente antes de chegar a você, e um hook lembra o Claude disso quando ele esquece.

| Parte | O que é |
| --- | --- |
| Agents | `varredor`, `pesquisador`, `executor` e `revisor`. São copiados pelo `/setup` como os outros agents e funcionam mesmo sem o resto do kit. |
| Regra | [regra.md](.claude/skills/setup/orquestracao/regra.md): o que a sessão principal delega, para quem, como escrever o pedido e como conferir o que volta. |
| Hooks | Um injeta em todo subagente, de qualquer projeto, o contrato de executor: fazer o que foi pedido e responder curto. Os outros três formam a trava da revisão: se um subagente editou arquivos com as ferramentas de edição e nenhum `revisor` rodou, o Claude é impedido de encerrar a resposta, uma vez, com o lembrete. Edição feita por comando de terminal não é detectada. |
| Configuração | Duas variáveis de ambiente: os subagentes usam o Sonnet por padrão e não podem criar outros subagentes. |

O `/setup` oferece o kit no fim e só instala com um aceite específico, porque ele altera o `settings.json` da sua pasta pessoal e vale para todos os seus projetos. Antes de alterar, o instalador grava uma cópia do arquivo. Ele nunca troca uma variável que você já tinha com outro valor, nem um arquivo seu com conteúdo diferente.

Para ver o estado, instalar ou desfazer fora do `/setup`, na raiz deste repositório:

```bash
python3 .claude/skills/setup/orquestracao.py checar
```

```bash
python3 .claude/skills/setup/orquestracao.py instalar
```

```bash
python3 .claude/skills/setup/orquestracao.py remover
```

O `revisor` e o `revisor-de-codigo` são diferentes. O `revisor` confere uma mudança contra os critérios que recebe e é chamado pela sessão principal durante o trabalho. O `revisor-de-codigo` você chama antes de abrir o PR, para uma revisão geral da sua mudança.

## Como usar

### Setup

Abra o Claude Code na raiz do repositório e rode `/setup`. A skill verifica o que falta na sua máquina, mostra o que vai fazer e, depois do seu aceite:

- copia os agents e as skills para `~/.claude`, para valerem em todos os seus projetos;
- oferece o [kit de orquestração](#kit-de-orquestração), que é opcional;
- configura o MCP oficial da Atlassian, se você ainda não tiver nenhum MCP de Jira. Skills que leem ou gravam no Jira dependem dele.

O login no Jira é feito por você no navegador, com a sua conta Atlassian. O setup não pede token nem senha.

Rode `/setup` de novo depois de um `git pull` para atualizar as cópias. Se você alterou alguma delas, a skill mostra a diferença e pergunta antes de substituir.

O que o setup não faz: instalar o Claude Code. Sem ele, siga antes a [documentação de instalação](https://code.claude.com/docs/pt/setup).

### Neste repositório

Abra o Claude Code na raiz do repositório. Os agents aparecem em `/agents` e as skills podem ser chamadas pelo nome, como `/descricao-de-pr`.

### Em todos os seus projetos

O `/setup` faz isso por você. Para copiar à mão só o que precisar para a sua pasta pessoal do Claude Code:

```bash
mkdir -p ~/.claude/agents ~/.claude/skills
cp .claude/agents/revisor-de-codigo.md ~/.claude/agents/
cp -r .claude/skills/descricao-de-pr ~/.claude/skills/
```

### Em um projeto específico

Copie para a pasta `.claude/` do projeto:

```bash
mkdir -p <projeto>/.claude/agents <projeto>/.claude/skills
cp .claude/agents/revisor-de-codigo.md <projeto>/.claude/agents/
cp -r .claude/skills/descricao-de-pr <projeto>/.claude/skills/
```

As cópias não se atualizam sozinhas. Quando algo mudar aqui, copie de novo.

## Como contribuir

1. Crie uma branch a partir da `main`.
2. Adicione o agent ou a skill seguindo os modelos abaixo.
3. Teste em uma tarefa real antes de abrir o PR.
4. Inclua a ferramenta na tabela [Disponíveis](#disponíveis).
5. Abra o PR explicando o que a ferramenta faz e em que situação você usou.

### Modelo de agent

`.claude/agents/nome-do-agent.md`

```markdown
---
name: nome-do-agent
description: O que o agent faz e quando deve ser usado.
tools: Read, Grep, Glob, Bash
---

Instruções do agent: papel, passos que deve seguir e formato da resposta.
```

O campo `tools` limita o que o agent pode usar. Sem ele, o agent herda todas as ferramentas da sessão, incluindo as de conectores como o Jira. Para herdar tudo e bloquear só algumas, use `disallowedTools` no lugar.

Um agent roda separado da conversa e devolve um resultado no fim. Se a ferramenta precisa perguntar, esperar resposta ou pedir aceite, faça uma skill, como a `refinar-tarefa`.

### Modelo de skill

`.claude/skills/nome-da-skill/SKILL.md`

```markdown
---
name: nome-da-skill
description: O que a skill faz e quando deve ser usada.
---

Instruções da skill: passo a passo, regras e exemplos.
```

A `description` é o que o Claude lê para decidir se usa a ferramenta, então escreva de forma específica: o que ela faz e em que situação se aplica.

## Boas práticas

- **Sem segredos.** Nada de tokens, senhas ou dados de clientes nos arquivos.
- **Uma responsabilidade por ferramenta.** É melhor ter duas skills pequenas do que uma que tenta fazer tudo.
- **Explique o porquê.** Instruções com o motivo junto funcionam melhor do que regras soltas.
- **Mantenha vivo.** Se algo parou de funcionar ou ficou obsoleto, atualize ou remova.
