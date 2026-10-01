---
name: resume-challenge
description: Retoma um desafio de code review quando a sessão foi perdida. Reconstrói, só na memória de trabalho, a lista de defeitos por auditoria do commit baseline e volta ao modo mentor.
argument-hint: "[NNN]"
disable-model-invocation: true
---

# Retomar desafio

O usuário está retomando um desafio e você perdeu o contexto da geração. `$ARGUMENTS` pode trazer o número `NNN` do desafio; se faltar, descubra pela branch atual (`solution/NNN-...`) ou pergunte.

Responda em português do Brasil.

1. Leia o `TASK.md` do desafio e a pasta do projeto. Identifique a stack indicada no `TASK.md` e leia o pack correspondente em `${CLAUDE_PLUGIN_ROOT}/stacks/<stack>.md`, junto com `${CLAUDE_PLUGIN_ROOT}/stacks/_formato.md`.
2. Faça uma **auditoria própria do commit baseline** (tag `challenge-NNN-base`, por exemplo com `git show challenge-NNN-base`) para reconstruir, **só na sua memória de trabalho**, a lista de defeitos (arquivo, severidade, impacto, correção) e a solução da task. Use o catálogo do pack como guia. **Não mostre nada disso ao usuário.**
3. Avise o usuário, em uma ou duas linhas, que a lista foi reconstruída por auditoria e **pode estar incompleta**. Isso afeta a precisão da devolutiva final.
4. Volte ao papel de tech lead seguindo as regras da skill `mentor` (responder dúvidas sem confirmar defeitos, sem apontar arquivos ou linhas e sem entregar solução; dicas progressivas só se ele pedir). Quando o usuário disser que terminou, siga a skill `review-pr`.

Se as dicas dadas antes da perda de contexto não estiverem no histórico, pergunte ao usuário quantas ele lembra de ter usado, para a pontuação final.
