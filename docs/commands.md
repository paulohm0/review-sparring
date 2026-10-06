# Referência dos comandos

Todos têm o prefixo `/review-sparring:`.

| Comando | Argumentos | O que faz |
|---|---|---|
| `generate-challenge` | `<easy\|medium\|hard\|senior> [stack]` | Gera o projeto, o `TASK.md`, o commit baseline, a tag e a branch de solução, e entra no papel de mentor. |
| `mentor` | nenhum | Modo mentor: responde dúvidas sem confirmar defeitos nem entregar a solução; dicas progressivas só se você pedir. |
| `review-pr` | `[numero-do-pr]` | Com número: revisa o PR no GitHub, em rodadas. Sem número: entrevista e devolutiva no chat. |
| `resume-challenge` | `[NNN]` | Reconstrói a lista de defeitos após perder a sessão e volta ao modo mentor. |

## Dificuldades

| Argumento | Tempo estimado |
|---|---|
| `easy` | ≈ 30-60 min |
| `medium` | ≈ 1-2 h |
| `hard` | ≈ 2-3 h |
| `senior` | ≈ 3-4 h |

Também são aceitos `facil`, `medio` e `dificil`.

## Execução

`generate-challenge`, `review-pr` e `resume-challenge` só rodam quando **você** os digita: o Claude não os dispara sozinho. O `mentor` pode ser acionado também pelo Claude durante a conversa.

## O que nenhum comando faz

Nenhum comando do plugin dá `git push`, abre ou fecha PR, faz merge, apaga comentários ou exibe tokens. Isso é sempre com você, no seu terminal.
