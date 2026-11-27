---
name: descricao-de-pr
description: Escreve a descrição de um pull request a partir das alterações da branch atual. Use quando alguém pedir para descrever, resumir ou abrir um PR.
---

# Descrição de PR

A descrição é lida por quem vai revisar o PR e, mais tarde, por quem tentar entender por que a mudança foi feita. Essas pessoas não acompanharam o desenvolvimento, então o texto precisa se sustentar sozinho.

## Passos

1. Veja o que a branch muda em relação à principal:
   - `git log main..HEAD --oneline` para os commits;
   - `git diff main...HEAD` para o código.
2. Entenda o motivo da mudança. Se ele não estiver claro nos commits nem na conversa, pergunte antes de escrever.
3. Classifique o PR e leia o modelo correspondente em `templates/`:

   | Tipo | Quando usar | Modelo |
   |---|---|---|
   | Feature | Funcionalidade nova ou mudança de comportamento | [templates/feature.md](templates/feature.md) |
   | Fix | Correção de bug | [templates/fix.md](templates/fix.md) |
   | Infra | Infraestrutura, configuração, pipeline de CI/CD ou dependências | [templates/infra.md](templates/infra.md) |

   Use o prefixo da branch ou dos commits (`feat/`, `fix/`, `infra/`, `chore/`) como pista. Se o tipo não estiver claro, use o de feature. Se o PR misturar tipos, use o modelo do tipo principal.
4. Escreva a descrição seguindo o modelo. Os textos dentro de cada seção explicam o que vai nela; substitua por conteúdo real.
5. Mostre o texto e só abra o PR se a pessoa pedir.

## Regras

- Escreva em português, com frases completas.
- Fale do comportamento que muda, não de cada arquivo alterado. O diff já mostra os arquivos.
- Changelog é uma lista de mudanças pontuais, uma por linha. Não repita a "Descrição".
- Se algo não foi testado, diga isso de forma explícita em "Como testar".
- Inclua o link do card ou da issue em "Documentação" quando houver.
- Não invente evidências. Se não houver nenhuma à mão, deixe um lembrete para a pessoa anexar (por exemplo, `> TODO: anexar screenshot da tela X`).
- Remova as seções que não se aplicam, como "Pontos de atenção" sem nenhum ponto ou "Como realizar o deploy" quando o deploy é o padrão, sem passo extra.
