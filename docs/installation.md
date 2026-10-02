# Instalação

## Pré-requisitos

**Sempre:**

- [Claude Code](https://claude.com/claude-code) instalado e logado
- `git`

**Conforme a stack:**

| Stack | O que precisa | Como conferir |
|---|---|---|
| `java-spring` | Java 21 ou superior (Maven não precisa: o projeto gerado usa o Maven Wrapper) | `java -version` |
| `flutter` | Flutter SDK (os desafios são verificados com `flutter test`, sem emulador) | `flutter --version` |

**Só para revisão por Pull Request:**

- Um repositório no GitHub para enviar os desafios
- Uma segunda conta do GitHub para a revisão, com token: [review-account.md](review-account.md)
- O [GitHub CLI](https://cli.github.com/) (`gh`) só é necessário se você quiser abrir PRs pelo terminal (`gh pr create`). A revisão em si roda dentro do Claude Code, com o `gh` autenticado pelo token da conta de revisão.

## Instalar o plugin

Dentro de uma sessão do Claude Code:

```
/plugin marketplace add paulohm0/review-sparring
/plugin install review-sparring@review-sparring
```

Ou direto no terminal, sem abrir sessão:

```bash
claude plugin marketplace add paulohm0/review-sparring
claude plugin install review-sparring@review-sparring
```

`paulohm0/review-sparring` é o endereço do repositório no GitHub: <https://github.com/paulohm0/review-sparring>.

Depois de instalar, **reinicie a sessão** (ou rode `/reload-plugins`) para os comandos aparecerem.

## Conferir

```bash
claude plugin list
```

Deve aparecer `review-sparring@review-sparring` com status `enabled`. Na sessão, digitar `/review-sparring:` deve sugerir os comandos.

## Atualizar e remover

```bash
claude plugin marketplace update review-sparring
claude plugin uninstall review-sparring@review-sparring
claude plugin marketplace remove review-sparring
```
