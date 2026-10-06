# review-sparring

Plugin do [Claude Code](https://claude.com/claude-code) para **treinar code review**, no chat ou direto no GitHub.

O treino tem **duas camadas de revisão**:

1. **Você revisa o código.** O Claude gera um projeto de verdade, com defeitos escondidos, e uma *task* (um chamado ou ticket) para resolver. Você lê esse código **como se fosse de outra pessoa**, acha o que está errado, corrige e deixa comentários `// REVIEW:`. Enquanto isso, o Claude é o **tech lead**: tira dúvidas, faz perguntas e **não entrega a resposta**.
2. **O Claude revisa a sua solução.** Quando você termina, ele revisa o **seu trabalho** em rodadas, no chat ou no seu Pull Request, e fecha com uma devolutiva: o que você achou, o que perdeu, onde errou, nota e pontos de estudo.

```
generate-challenge  ->  a IA gera o projeto com defeitos escondidos
        |
        v
  VOCÊ revisa o código (acha, corrige, comenta) e tira dúvidas com o mentor
        |
        v
  você abre o PR (ou pede a revisão no chat)
        |
        v
review-pr  ->  a IA revisa a SUA solução, em rodadas, até aprovar
        |
        v
  devolutiva final: acertos, defeitos perdidos, nota e pontos de estudo
```

## Comece em 3 passos

**1. Instale o plugin** (precisa do Claude Code e do `git`):

```bash
claude plugin marketplace add paulohm0/review-sparring
```

```bash
claude plugin install review-sparring@review-sparring
```

**2. Crie uma pasta vazia, abra o Claude Code nela e gere um desafio:**

```bash
mkdir meu-treino-review && cd meu-treino-review && git init && claude
```

```
/review-sparring:generate-challenge easy java-spring
```

**3. Resolva a task, deixe os seus `// REVIEW:` no código e peça a revisão:**

```
/review-sparring:review-pr
```

Esse é o modo chat: não precisa de conta extra nem de PR. Para a revisão **dentro do GitHub**, veja [Revisão no GitHub](#revisão-no-github-com-uma-segunda-conta).

> Depois de instalar, abra uma sessão nova (ou rode `/reload-plugins`) para os comandos aparecerem.

## Comandos

Todos têm o prefixo `/review-sparring:`.

| Comando | Argumentos | O que faz |
|---|---|---|
| `generate-challenge` | `<easy\|medium\|hard\|senior> [stack]` | Gera o projeto com defeitos, o `TASK.md`, o commit baseline, a tag e a branch de solução. |
| `mentor` | nenhum | Modo mentor: responde dúvidas sem confirmar defeitos nem entregar a solução. |
| `review-pr` | `[numero-do-pr]` | Com número: revisa o PR no GitHub. Sem número: entrevista e devolutiva no chat. |
| `resume-challenge` | `[NNN]` | Reconstrói a lista de defeitos se a sessão for perdida e volta ao modo mentor. |

Detalhes, dificuldades e regras de cada comando: [docs/commands.md](docs/commands.md).

## Stacks

| Stack | Argumento | Pré-requisito |
|---|---|---|
| Java 21 + Spring Boot 3 + Maven | `java-spring` (padrão) | Java 21 (`java -version`) |
| Flutter / Dart 3 | `flutter` | Flutter SDK (`flutter --version`) |
| Kotlin Multiplatform | `kotlin-multiplatform` | JDK 17+ (`java -version`); o código é Kotlin |

Quer treinar em outra linguagem ou framework? [Abra um pedido de stack](https://github.com/paulohm0/review-sparring/issues/new?template=stack-request.yml): o mantenedor cria e avisa na issue. Passo a passo: [docs/request-a-stack.md](docs/request-a-stack.md).

## Revisão no GitHub com uma segunda conta

A parte mais legal do projeto: a review aparece **no seu Pull Request, escrita por outra conta do GitHub**, com comentários nas linhas, perguntas, veredito e rodadas, como num time de verdade. Isso exige uma **segunda conta** só para revisão, porque o GitHub não deixa aprovar nem pedir mudanças no próprio PR.

O guia completo, do zero (criar a conta, convidar, gerar o token, configurar e usar): [docs/review-account.md](docs/review-account.md). O fluxo do PR em si: [docs/pull-request-review.md](docs/pull-request-review.md).

## Documentação

| Documento | Conteúdo |
|---|---|
| [docs/installation.md](docs/installation.md) | Pré-requisitos, instalação, conferir, atualizar e remover |
| [docs/walkthrough.md](docs/walkthrough.md) | Passo a passo do primeiro desafio à devolutiva final |
| [docs/pull-request-review.md](docs/pull-request-review.md) | Enviar o desafio, abrir o PR e pedir a revisão |
| [docs/review-account.md](docs/review-account.md) | Segunda conta do GitHub: criação, convite, token e uso |
| [docs/request-a-stack.md](docs/request-a-stack.md) | Pedir uma stack nova por issue |
| [docs/commands.md](docs/commands.md) | Referência completa dos comandos |
| [docs/troubleshooting.md](docs/troubleshooting.md) | Problemas comuns |
| [docs/limits-and-security.md](docs/limits-and-security.md) | Limitações e segurança |
| [docs/development.md](docs/development.md) | Estrutura do plugin e como testar localmente |

## Bom saber

- **Não existe arquivo de gabarito.** A lista de defeitos fica só na memória da sessão, para você não conseguir "espiar". Se a sessão cair, `resume-challenge` a reconstrói.
- **Tudo roda no seu Claude Code**, com a sua conta e o seu consumo de tokens. O plugin é só um conjunto de arquivos de texto, sem servidor.
