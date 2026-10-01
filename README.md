# review-sparring

Plugin do [Claude Code](https://claude.com/claude-code) para **treinar code review**.

O treino tem **duas camadas de revisão**:

1. **Você revisa o código.** O Claude gera um projeto de verdade, com defeitos escondidos, e uma *task* (um chamado ou ticket) para resolver. Você lê esse código **como se fosse de outra pessoa**, acha o que está errado, corrige e deixa comentários `// REVIEW:`. Enquanto você trabalha, o Claude atua como **tech lead**: tira dúvidas, faz perguntas e **não entrega a resposta**.
2. **O Claude revisa a sua solução.** Quando você termina, ele revisa o **seu trabalho** em rodadas, no chat ou no seu Pull Request no GitHub, e fecha com uma devolutiva: o que você achou, o que perdeu, onde errou, nota e pontos de estudo.

O foco é treino: você pratica revisar código que não escreveu, e a IA avalia a qualidade da sua revisão.

## Sumário

1. [Como funciona](#1-como-funciona)
2. [Stacks disponíveis](#2-stacks-disponíveis)
3. [Pré-requisitos](#3-pré-requisitos)
4. [Instalação](#4-instalação)
5. [Passo a passo: do primeiro desafio à devolutiva](#5-passo-a-passo-do-primeiro-desafio-à-devolutiva)
6. [Revisão por Pull Request](#6-revisão-por-pull-request)
7. [Referência dos comandos](#7-referência-dos-comandos)
8. [Quero outra stack](#8-quero-outra-stack)
9. [Problemas comuns](#9-problemas-comuns)
10. [Limitações e segurança](#10-limitações-e-segurança)
11. [Desenvolvendo o plugin](#11-desenvolvendo-o-plugin)

---

## 1. Como funciona

```
/review-sparring:generate-challenge   ->  a IA gera um projeto com defeitos + TASK.md + branch de solução
            |
            v
   VOCÊ revisa o código: acha os defeitos, corrige e comenta (// REVIEW:)
   e tira dúvidas (/review-sparring:mentor); o mentor pergunta, não confirma,
   não entrega a solução
            |
            v
   você abre o PR com as suas mudanças (ou pede a revisão no chat)
            |
            v
/review-sparring:review-pr   ->  A IA revisa a SUA solução, em rodadas, até aprovar
            |
            v
   devolutiva final: acertos, defeitos perdidos, nota e pontos de estudo
```

Alguns detalhes importantes:

- **Não existe arquivo de gabarito.** A lista de defeitos fica só na memória da sessão do Claude, para você não conseguir "espiar". Se a sessão for perdida, o comando `resume-challenge` reconstrói a lista auditando o commit baseline.
- **Tudo é local.** O plugin é só um conjunto de arquivos de texto (instruções). Não há servidor. Os comandos rodam no seu Claude Code, com a sua conta e o seu consumo.

## 2. Stacks disponíveis

| Stack | Argumento |
|---|---|
| Java 21 + Spring Boot 3 + Maven | `java-spring` (padrão) |
| Flutter / Dart 3 | `flutter` |

## 3. Pré-requisitos

**Sempre:**

- [Claude Code](https://claude.com/claude-code) instalado e logado
- `git`

**Conforme a stack:**

| Stack | O que precisa | Como conferir |
|---|---|---|
| `java-spring` | Java 21 ou superior (Maven não precisa: o projeto gerado usa o Maven Wrapper) | `java -version` |
| `flutter` | Flutter SDK (os desafios são verificados com `flutter test`, sem emulador) | `flutter --version` |

**Só para revisão por Pull Request:**

- [GitHub CLI](https://cli.github.com/) (`gh`) autenticado: `gh auth login`
- Um repositório no GitHub para enviar os desafios

## 4. Instalação

### 4.1 Adicionar o marketplace e instalar o plugin

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

`paulohm0/review-sparring` é o endereço deste repositório no GitHub: <https://github.com/paulohm0/review-sparring>.

Depois de instalar, **reinicie a sessão** (ou rode `/reload-plugins`) para os comandos aparecerem.

### 4.2 Conferir

```bash
claude plugin list
```

Deve aparecer `review-sparring@review-sparring` com status `enabled`. Na sessão, digitar `/review-sparring:` deve sugerir os quatro comandos.

### 4.3 Atualizar e remover

```bash
claude plugin marketplace update review-sparring
claude plugin uninstall review-sparring@review-sparring
claude plugin marketplace remove review-sparring
```

## 5. Passo a passo: do primeiro desafio à devolutiva

### Passo 1: criar um repositório vazio para o treino

```bash
mkdir meu-treino-review
cd meu-treino-review
git init
claude
```

Use uma pasta separada. É nela que o plugin vai criar os desafios, os commits, as tags e as branches.

### Passo 2: gerar o desafio

Dentro do Claude Code:

```
/review-sparring:generate-challenge medium java-spring
```

- O primeiro argumento é a **dificuldade**: `easy`, `medium`, `hard` ou `senior`.
- O segundo é a **stack** (opcional). Sem ele, usa `java-spring`.

O Claude vai decidir sozinho o domínio, os temas e o tipo de task, planejar os defeitos (sem mostrar), gerar o projeto, rodar o build e os testes, e criar:

- a pasta `challenge-001-<dominio>/` com o projeto, um `README.md` (como rodar) e o `TASK.md` (o briefing);
- um commit baseline, por exemplo `feat(challenge-001): add <dominio> baseline`;
- a tag `challenge-001-base` nesse commit;
- a branch `solution/001-<dominio>`, onde você vai trabalhar.

Ele termina apresentando a task, **sem revelar defeitos**. O tipo de task é sorteado:

| Tipo | O que é |
|---|---|
| PROBLEMA | Há um sintoma (bug, lentidão, dado inconsistente). Você investiga e acha a causa raiz. |
| REFATORAÇÃO | Há problemas de design. Você melhora sem quebrar o comportamento. |
| MELHORIA | Algo funciona, mas precisa ficar melhor em algo concreto. |
| FEATURE | Você implementa algo novo respeitando a arquitetura. |

### Passo 3: ler o código e trabalhar

Abra a pasta do desafio no editor. Leia o `TASK.md` e o código **como se fosse um PR de outra pessoa**:

- Resolva a task.
- Corrija o que achar errado e/ou deixe comentários `// REVIEW: o que vi e por quê` no próprio código (use a sintaxe de comentário da linguagem).
- Rode o projeto e os testes:

```bash
# java-spring (Windows: mvnw.cmd)
./mvnw -q verify
./mvnw spring-boot:run

# flutter
flutter pub get
flutter analyze
flutter test
```

Você não precisa preencher relatório nenhum.

### Passo 4: tirar dúvidas (mentoria)

Pergunte no chat o que quiser sobre regras de negócio, conceitos, como rodar. O mentor:

- **responde dúvidas** e ajuda a reproduzir o problema;
- **faz perguntas** em vez de dar respostas ("e se duas requisições chegarem ao mesmo tempo?");
- **não confirma** se um trecho tem defeito e não aponta arquivo nem linha;
- dá **dicas progressivas só se você pedir** (nível 1: vaga; nível 2: a área e o tipo de problema). A solução completa só se você disser que desistiu.

Se a conversa for retomada em outra sessão e o mentor parecer perdido, rode `/review-sparring:mentor`.

### Passo 5: pedir a revisão

Quando terminar, escolha um dos dois modos.

**Modo chat (o mais simples, sem conta extra):**

```
/review-sparring:review-pr
```

O Claude compara o seu trabalho com o commit baseline, roda build e testes, faz **a entrevista** (2 a 4 perguntas por rodada, sem confirmar acertos) e então dá a devolutiva final.

**Modo Pull Request:** veja a [seção 6](#6-revisão-por-pull-request).

### Passo 6: a devolutiva final

Ao fim, você recebe:

1. se a task foi cumprida (causa raiz ou só o sintoma) e a qualidade da solução;
2. o que você acertou entre os defeitos extras;
3. o que você perdeu, com arquivo, linha, impacto e correção;
4. onde você errou (mudanças desnecessárias, falsos positivos tratados como bug, correções que criaram problemas);
5. como poderia ter percebido cada defeito perdido;
6. a qualidade da sua revisão como prática;
7. nota de 0 a 10 para a task e o percentual de defeitos extras encontrados, ponderado por severidade (desconta bugs introduzidos e dicas usadas);
8. pontos de estudo e a dificuldade sugerida para o próximo desafio.

### Passo 7: o próximo desafio

Rode `generate-challenge` de novo, na mesma pasta. O número sobe (`challenge-002-...`), e o plugin evita repetir domínio e tipo de task dos desafios anteriores.

```
/review-sparring:generate-challenge hard flutter
```

### Se a sessão for perdida

Abra o Claude Code na raiz do repositório, na branch `solution/NNN-...`, e rode:

```
/review-sparring:resume-challenge
```

(Opcionalmente com o número: `/review-sparring:resume-challenge 1`.) O Claude lê o `TASK.md`, audita o commit baseline para reconstruir a lista de defeitos e volta ao modo mentor. Como a lista é reconstruída por auditoria, ela **pode estar incompleta**, e ele avisa isso.

## 6. Revisão por Pull Request

Use quando quiser a review no GitHub, com comentários nas linhas, perguntas e veredito (`APPROVE`, `REQUEST_CHANGES` ou `COMMENT`).

### 6.1 Preparar o repositório no GitHub

Crie um repositório vazio no GitHub e conecte:

```bash
git remote add origin https://github.com/SEU-USUARIO/meu-treino-review.git
```

### 6.2 Enviar o desafio

O plugin **nunca faz push**: isso é com você. Envie o `main`, a tag e a branch de solução (troque `NNN` e `<dominio>` pelos valores reais):

```bash
git push -u origin main
git push origin challenge-NNN-base
git push -u origin solution/NNN-<dominio>
```

Quando terminar de trabalhar, faça commit e push na branch de solução:

```bash
git add .
git commit -m "fix: solve challenge NNN"
git push
```

### 6.3 Abrir o PR

Pela interface do GitHub, ou com o `gh`:

```bash
gh pr create --base main --head solution/NNN-<dominio> --title "Challenge NNN solution" --body "Minha solução e comentários REVIEW."
```

O PR precisa ser aberto **com a sua conta**: o autor é você.

### 6.4 Pedir a revisão

Abra o Claude Code na raiz do repositório, na branch de solução, e rode (com o número do PR):

```
/review-sparring:review-pr 1
```

O Claude lê o PR, confere se o commit local é o mesmo do PR, roda build e testes e posta a review no GitHub, com:

- comentários nas linhas que você alterou (elogios, correções erradas ou incompletas e **perguntas**), cada ponto marcado como **bloqueante** ou **sugestão**;
- um comentário geral com o status da task, sem entregar a causa raiz;
- um veredito.

### 6.5 Rodadas seguintes

Responda aos comentários no GitHub (ou no chat), faça novos commits, `git push` e rode `/review-sparring:review-pr 1` de novo. Cada rodada revisa só o que mudou. Depois do `APPROVE`, vem a devolutiva final, no PR e no chat.

### 6.6 A conta do GitHub e o `GH_TOKEN`

O GitHub **não deixa** aprovar nem pedir mudanças no próprio PR. Se o `gh` estiver logado na **mesma conta** que abriu o PR, o plugin avisa e oferece duas saídas: revisar no chat, ou postar a review apenas como comentário.

Para ver `APPROVE` e `REQUEST_CHANGES` de verdade, use uma **segunda conta** do GitHub só para revisão:

1. Crie (ou use) uma segunda conta e dê a ela acesso ao repositório.
2. Gere um token de acesso pessoal dessa conta com o **menor escopo possível** (acesso ao repositório e permissão para pull requests).
3. Defina o token no ambiente **antes** de abrir o Claude Code:

```bash
# bash / zsh
export GH_TOKEN="cole-o-token-aqui"
claude
```

```powershell
# PowerShell
$env:GH_TOKEN = "cole-o-token-aqui"
claude
```

Nunca cole o token no chat. O plugin não o exibe nem o grava.

## 7. Referência dos comandos

Todos têm o prefixo `/review-sparring:`.

| Comando | Argumentos | O que faz |
|---|---|---|
| `generate-challenge` | `<easy\|medium\|hard\|senior> [stack]` | Gera o projeto, o `TASK.md`, o commit baseline, a tag e a branch de solução, e entra no papel de mentor. |
| `mentor` | nenhum | Modo mentor: responde dúvidas sem confirmar defeitos nem entregar a solução; dicas progressivas só se você pedir. |
| `review-pr` | `[numero-do-pr]` | Com número: revisa o PR no GitHub, em rodadas. Sem número: entrevista e devolutiva no chat. |
| `resume-challenge` | `[NNN]` | Reconstrói a lista de defeitos após perder a sessão e volta ao modo mentor. |

Dificuldades: `easy` (≈30-60 min), `medium` (≈1-2 h), `hard` (≈2-3 h), `senior` (≈3-4 h). Também são aceitos `facil`, `medio` e `dificil`.

`generate-challenge`, `review-pr` e `resume-challenge` só rodam quando **você** os digita: o Claude não os dispara sozinho.

## 8. Quero outra stack

O plugin traz Java/Spring e Flutter. Para usar outra linguagem ou framework (Go, Node.js, Python, Kotlin, Rust...), você escreve um **pack**: um arquivo Markdown em `stacks/` que diz ao plugin como compilar, testar, reproduzir um problema, quais temas cobrir e quais defeitos são típicos da stack. Tudo que é específico de linguagem vive nesse arquivo; o resto do plugin é genérico.

O caminho é baixar o projeto, adicionar o seu pack e rodar a sua cópia local.

### Passo 1: baixar o projeto

```bash
git clone https://github.com/paulohm0/review-sparring.git review-sparring
cd review-sparring
```

### Passo 2: criar o arquivo da stack

Copie um pack existente como ponto de partida. O nome do arquivo é o nome que você vai passar ao comando (por exemplo, `go-gin`):

```bash
cp stacks/flutter.md stacks/go-gin.md
```

(No PowerShell: `Copy-Item stacks/flutter.md stacks/go-gin.md`.)

Leia `stacks/_formato.md`, que descreve as dez seções obrigatórias, e reescreva o arquivo novo para a sua stack:

| Seção | O que escrever |
|---|---|
| Identificação | Nome, versões-alvo e status (`experimental`). |
| Pré-requisitos | Comandos para checar o ambiente (por exemplo, `go version`) e o que fazer se faltar. |
| Estrutura do projeto | Como organizar o projeto gerado, o arquivo de build e o `.gitignore`. |
| Build e testes | Os comandos para compilar, testar e rodar. |
| Reproduzir um sintoma | Como descrever e verificar um chamado do tipo PROBLEMA (teste que falha, requisição, log). |
| Tamanho do projeto | Tabela de dificuldade x tamanho, na unidade da stack. |
| Temas | Assuntos que o desafio pode abordar. |
| Catálogo de defeitos | Exemplos **realistas** por dificuldade. É a seção que mais pesa na qualidade dos desafios. |
| Falsos positivos | O princípio e exemplos de código que parece errado, mas está correto. |
| Limitações | O que a stack não consegue fazer (por exemplo, precisa de um SO específico). |

Dica: peça ao próprio Claude para rascunhar o pack. Abra o Claude Code na pasta do projeto e peça algo como "escreva `stacks/go-gin.md` seguindo `stacks/_formato.md` e com o mesmo nível de detalhe de `stacks/java-spring.md`". Depois revise o catálogo de defeitos com atenção: ele precisa refletir problemas reais da stack.

### Passo 3: validar o plugin

```bash
claude plugin validate .
```

### Passo 4: rodar a sua cópia local

Crie uma pasta vazia para o treino e abra o Claude Code carregando o plugin da sua cópia:

```bash
mkdir ../meu-treino-go
cd ../meu-treino-go
git init
claude --plugin-dir ../review-sparring
```

Dentro da sessão:

```
/review-sparring:generate-challenge medium go-gin
```

Se você já tinha instalado o plugin pelo marketplace, desative-o antes para não misturar versões:

```bash
claude plugin disable review-sparring@review-sparring
```

Depois de editar o pack, rode `/reload-plugins` na sessão para carregar a mudança.

### Passo 5 (opcional): contribuir ou publicar a sua versão

- **Contribuir:** abra um Pull Request neste repositório com o seu `stacks/<nome>.md`, marcado como `experimental`.
- **Publicar a sua versão:** faça um fork, envie o pack para ele e instale a partir do fork: `/plugin marketplace add SEU-USUARIO/SEU-FORK` seguido de `/plugin install review-sparring@review-sparring`.

## 9. Problemas comuns

| Sintoma | O que fazer |
|---|---|
| `/review-sparring:` não aparece | Reinicie a sessão ou rode `/reload-plugins`. Confira com `claude plugin list`. |
| "Plugin not found in marketplace" | Use o formato completo: `/plugin install review-sparring@review-sparring`. |
| O Claude diz que não achou o pack da stack | Confira se existe `stacks/<stack>.md` e se o nome no comando é igual ao do arquivo, sem extensão. |
| `java -version` / `flutter --version` falha | Instale o toolchain da stack e reabra o terminal. |
| `./mvnw` não executa no Windows | Use `mvnw.cmd`. |
| A review por PR avisa que o autor é você | O `gh` está logado na mesma conta do PR. Use a revisão no chat, `COMMENT`, ou uma segunda conta (seção 6.6). |
| `gh` não autenticado | `gh auth login`. |
| O Claude perdeu o contexto no meio do desafio | `/review-sparring:resume-challenge`. |

## 10. Limitações e segurança

- **Lista de defeitos só na sessão.** Sem arquivo de gabarito. Se a sessão cair, a lista é reconstruída por auditoria e pode ficar incompleta.
- **Consumo de tokens.** Gerar projetos maiores e revisar rodando build e testes gasta tokens da conta de quem usa.
- **Qualidade dos defeitos.** Depende do pack e do modelo. Packs experimentais podem gerar desafios menos realistas.
- **Regras são instruções.** "Nunca faz merge, push, fecha ou edita o PR e nunca exibe o token" são instruções ao modelo, e não bloqueios técnicos. Use um token de menor escopo possível e revise o que o Claude pede para executar.
- **O conteúdo do PR é tratado como dado.** O plugin instrui o Claude a ignorar instruções que apareçam em diffs, comentários ou arquivos.

## 11. Desenvolvendo o plugin

Estrutura:

```
review-sparring/
├── .claude-plugin/
│   ├── plugin.json           # nome, versão e descrição do plugin
│   └── marketplace.json      # catálogo para o /plugin marketplace add
├── skills/
│   ├── generate-challenge/SKILL.md
│   ├── mentor/SKILL.md
│   ├── review-pr/SKILL.md
│   └── resume-challenge/SKILL.md
└── stacks/
    ├── _formato.md           # contrato para criar um pack
    ├── java-spring.md
    └── flutter.md
```

Testar localmente, sem instalar:

```bash
claude plugin validate .
claude --plugin-dir .
```

Depois de editar qualquer arquivo, rode `/reload-plugins` na sessão. Para testar o fluxo de instalação a partir de uma pasta local:

```bash
claude plugin marketplace add .
claude plugin install review-sparring@review-sparring
```
