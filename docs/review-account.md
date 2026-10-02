# Segunda conta do GitHub: a revisão dentro do GitHub

Este é o guia completo, do zero. No fim você terá o Claude escrevendo reviews **nos seus Pull Requests, como outra conta do GitHub**, com comentários nas linhas, perguntas, veredito (`APPROVE`, `REQUEST_CHANGES` ou `COMMENT`) e rodadas, como num time de verdade.

## Por que uma segunda conta

O GitHub não deixa aprovar nem pedir mudanças **no próprio PR**. Se o Claude revisasse usando a sua conta, a review sairia como `COMMENT` e com o seu nome, o que tira o sentido do treino. Com uma conta separada, o papel de cada um fica claro:

| Quem | O que faz | Onde |
|---|---|---|
| **Você** (conta principal) | Escreve a solução, faz push e abre o PR | Seu terminal, fora do ambiente do revisor |
| **Conta de revisão** | Lê o PR e posta a review | O Claude Code, com o `GH_TOKEN` dessa conta |

```
você (conta principal)  --push/PR-->  repositório de treino
                                           |
              review (comentários, veredito)  <--  Claude Code + GH_TOKEN da conta de revisão
```

## Antes de criar a conta: os termos do GitHub

Os termos do GitHub limitam cada pessoa a **uma conta gratuita**, com uma exceção: uma **conta de máquina** (machine account) adicional, usada **exclusivamente para tarefas automatizadas**. A conta de revisão se encaixa nesse uso. Pelo que os termos dizem, essa conta precisa ser criada por uma pessoa (nada de criação automática), ter um e-mail válido, e você continua responsável por tudo que ela fizer. Leia os [Termos de Serviço do GitHub](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) antes de criar, porque eles podem mudar.

## Passo 1: criar a conta de revisão

1. Abra uma janela anônima (para não misturar com a sua sessão) e acesse <https://github.com/signup>.
2. Use um e-mail **diferente do da conta principal** (um alias do seu e-mail costuma servir, mas o GitHub precisa conseguir enviar o e-mail de verificação).
3. Escolha um nome de usuário que deixe claro o papel, por exemplo `seuusuario-reviewer`.
4. Verifique o e-mail.
5. Ative a **autenticação em dois fatores** (Settings → Password and authentication). Essa conta guarda um token com acesso ao seu repositório, então proteja-a.
6. Opcional: no perfil, escreva na bio que é uma conta de revisão automatizada de `seuusuario`.

## Passo 2: dar acesso ao repositório de treino

Escolha uma das duas opções.

### Opção A: repositório privado + colaborador (a mais direta)

1. Na **conta principal**, crie o repositório de treino (pode ser privado).
2. No repositório: **Settings → Collaborators → Add people**, digite o usuário da conta de revisão e envie o convite.
3. Na **conta de revisão**, aceite o convite (pelo e-mail, ou em `https://github.com/SEU-USUARIO/NOME-DO-REPO/invitations`).

Em repositório pessoal, o colaborador tem permissão de **escrita**. Isso significa que quem tiver o token da conta de revisão consegue dar push nesse repositório. Por isso o token precisa ter validade curta e ficar bem guardado (Passo 3).

### Opção B: repositório público, sem convite

Se o repositório de treino for **público**, a conta de revisão consegue comentar e revisar PRs sem ser colaboradora, e fica **sem permissão de escrita**. É a opção de menor privilégio. Ela depende de o GitHub aceitar, num repositório público, uma review `APPROVE` ou `REQUEST_CHANGES` de quem não é colaborador (essas reviews aparecem no PR, mas não contam para regras de merge). Eu não testei esse caminho de ponta a ponta: faça uma review de teste antes de contar com ele.

## Passo 3: gerar o token da conta de revisão

Faça isto **logado na conta de revisão**.

Importante: use um token **classic**. Os tokens *fine-grained* ficam restritos aos recursos que pertencem a uma única conta ou organização, então **não funcionam** quando a conta de revisão é só colaboradora de um repositório que pertence a outra conta pessoal. O erro típico é `Resource not accessible by personal access token`.

1. Acesse **Settings → Developer settings → Personal access tokens → Tokens (classic)**.
2. Clique em **Generate new token (classic)**.
3. **Note:** `review-sparring`.
4. **Expiration:** curta, por exemplo 30 dias. Quando expirar, você gera outro.
5. **Scopes:**
   - Opção A (repositório privado): marque `repo`.
   - Opção B (repositório público): marque só `public_repo`.
6. **Generate token** e copie o valor **na hora**: o GitHub só mostra uma vez.

O escopo `repo` é amplo, mas o acesso real da conta de revisão se limita aos repositórios em que ela foi convidada. Por isso a conta de revisão deve ser colaboradora **só dos seus repositórios de treino**, e de mais nada.

## Passo 4: configurar o token no ambiente do Claude

O token só pode existir no ambiente onde o **Claude** roda. Ele **não** deve valer para o seu terminal normal, senão o `gh` e o `git` dali passariam a agir como a conta de revisão (o PR sairia com o autor errado).

**No app de desktop do Claude:** defina `GH_TOKEN` nas variáveis de ambiente do próprio app, na configuração do ambiente das sessões (o lugar exato do menu depende da versão). Assim ele vale para as sessões do Claude e não para o resto do Windows.

**No terminal:** defina a variável só na janela em que você abre o Claude:

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

Nunca defina esse token nas variáveis de ambiente do Windows para o usuário ou a máquina inteira.

Nunca cole o token no chat, em commits, em README ou em issues. O plugin não o exibe nem o grava.

## Passo 5: conferir

Em uma janela onde o `GH_TOKEN` esteja definido:

```bash
gh api user --jq .login
```

Deve responder o usuário da **conta de revisão**. Se responder `Bad credentials`, o token está errado ou expirou. Em seguida, no repositório de treino:

```bash
gh repo view SEU-USUARIO/NOME-DO-REPO --json name
```

Se o repositório aparecer, a conta de revisão enxerga o repositório.

## Passo 6: usar

1. Faça o seu trabalho e o push **no seu terminal normal** (conta principal).
2. Abra o PR com a conta principal (site do GitHub ou `gh pr create`, sem o `GH_TOKEN` da revisão).
3. No Claude Code com o `GH_TOKEN` da revisão, na raiz do repositório de treino:

```
/review-sparring:review-pr 1
```

O plugin confere quem você é (`gh api user`) e quem abriu o PR. Se forem contas diferentes, posta a review e você a vê no PR **com o nome da conta de revisão**. Se forem a mesma, avisa e oferece revisar no chat ou postar só como comentário.

4. Responda nos comentários, faça novos commits e peça nova rodada. Veja o fluxo completo em [pull-request-review.md](pull-request-review.md).

## Segurança, resumindo

- Token **classic** com o menor escopo possível (`public_repo` se o repositório for público) e **validade curta**.
- Conta de revisão colaboradora **só** dos repositórios de treino.
- 2FA ligado nas duas contas.
- Token só no ambiente do Claude, nunca no Windows inteiro nem em arquivo versionado.
- As regras do plugin ("não faz merge, não faz push, não edita o PR, não exibe o token") são **instruções ao modelo**, e não bloqueios técnicos. Com a Opção A, o token tem escrita no repositório, então revise o que o Claude pede para executar.
- Para revogar: logado na conta de revisão, **Settings → Developer settings → Personal access tokens → Tokens (classic)** e apague o token. Se o token vazar, faça isso imediatamente.

## Não quero uma segunda conta

Tudo bem. Você pode usar a revisão **no chat** (`/review-sparring:review-pr` sem número), ou rodar `/review-sparring:review-pr 1` com o `gh` na sua conta principal: o plugin avisa que o autor é você e posta a review apenas como `COMMENT`.

## Problemas comuns

| Sintoma | Causa provável | O que fazer |
|---|---|---|
| `Bad credentials` | Token expirado, revogado ou digitado errado | Gere outro (Passo 3) e atualize no ambiente do Claude. |
| `Resource not accessible by personal access token` | Token *fine-grained* sendo usado em repositório de outra conta | Use um token **classic** (Passo 3). |
| `Not Found` (404) ao ler o repositório | A conta de revisão não aceitou o convite, ou o escopo está errado | Aceite o convite; confira `repo` (privado) ou `public_repo` (público). |
| O plugin diz que o autor do PR é a mesma conta | O `gh` está com o token da conta principal | Confira o `GH_TOKEN` com `gh api user --jq .login`. |
| O PR saiu com a conta de revisão como autora | O PR foi aberto em um terminal com `GH_TOKEN` da revisão | Feche e reabra pelo terminal normal ou pelo site, com a conta principal. |
| `Can not approve your own pull request` | Autor e revisor são a mesma conta | Veja o Passo 4 e o Passo 5. |
