# Revisão por Pull Request

Use quando quiser a review **no GitHub**, com comentários nas linhas, perguntas e veredito (`APPROVE`, `REQUEST_CHANGES` ou `COMMENT`).

Antes de começar, configure a conta de revisão: [review-account.md](review-account.md). Sem ela, o plugin ainda funciona (revisão no chat, ou review só como `COMMENT`), mas sem `APPROVE` e `REQUEST_CHANGES` de verdade.

## 1. Preparar o repositório no GitHub

Crie um repositório no GitHub (vazio) e conecte a pasta de treino:

```bash
git remote add origin https://github.com/SEU-USUARIO/meu-treino-review.git
```

Se for usar a conta de revisão, convide-a para esse repositório (veja [review-account.md](review-account.md)).

## 2. Enviar o desafio

O plugin **nunca faz push**: isso é com você, no seu terminal, com a sua conta. Envie o `main`, a tag e a branch de solução (troque `NNN` e `<dominio>` pelos valores reais):

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

## 3. Abrir o PR

Pela interface do GitHub, ou com o `gh`:

```bash
gh pr create --base main --head solution/NNN-<dominio> --title "Challenge NNN solution" --body "Minha solução e comentários REVIEW."
```

O PR precisa ser aberto **com a sua conta**: o autor é você. Use um terminal **fora** do ambiente onde o `GH_TOKEN` da conta de revisão está definido, senão o PR sai com a conta errada.

## 4. Pedir a revisão

Abra o Claude Code (com o `GH_TOKEN` da conta de revisão) na raiz do repositório, na branch de solução, e rode com o número do PR:

```
/review-sparring:review-pr 1
```

O Claude lê o PR, confere se o commit local é o mesmo do PR, roda build e testes e posta a review no GitHub, **como a conta de revisão**, com:

- comentários nas linhas que você alterou (elogios, correções erradas ou incompletas e **perguntas**), cada ponto marcado como **bloqueante** ou **sugestão**;
- um comentário geral com o status da task, sem entregar a causa raiz;
- um veredito.

## 5. Rodadas seguintes

Responda aos comentários no GitHub (ou no chat), faça novos commits, `git push` e rode `/review-sparring:review-pr 1` de novo. Cada rodada revisa só o que mudou. Depois do `APPROVE`, vem a devolutiva final, no PR e no chat.

## Quando o autor e o revisor são a mesma conta

O GitHub **não deixa** aprovar nem pedir mudanças no próprio PR. Se o `gh` estiver autenticado na mesma conta que abriu o PR, o plugin avisa e oferece duas saídas: revisar no chat, ou postar a review apenas como comentário (`COMMENT`).
