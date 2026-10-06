# Problemas comuns

| Sintoma | O que fazer |
|---|---|
| `/review-sparring:` não aparece | Reinicie a sessão ou rode `/reload-plugins`. Confira com `claude plugin list`. |
| "Plugin not found in marketplace" | Use o formato completo: `/plugin install review-sparring@review-sparring`. |
| O Claude diz que não achou o pack da stack | Confira se existe `stacks/<stack>.md` e se o nome no comando é igual ao do arquivo, sem extensão. |
| `java -version` / `flutter --version` falha | Instale o toolchain da stack e reabra o terminal. |
| `./mvnw` não executa no Windows | Use `mvnw.cmd`. |
| A review por PR avisa que o autor é você | O `gh` está com o token da mesma conta do PR. Use a revisão no chat, `COMMENT`, ou configure a conta de revisão ([review-account.md](review-account.md)). |
| `Bad credentials` | O `GH_TOKEN` expirou ou foi revogado. Gere outro ([review-account.md](review-account.md), Passo 3). |
| `Resource not accessible by personal access token` | Token *fine-grained* em repositório de outra conta. Use token **classic**. |
| `gh` não autenticado | `gh auth login`. Só é necessário para abrir PRs pelo terminal com a sua conta. |
| O PR saiu com a conta de revisão como autora | O PR foi aberto num terminal com o `GH_TOKEN` da revisão. Abra pelo site do GitHub ou por um terminal sem esse token. |
| O Claude perdeu o contexto no meio do desafio | `/review-sparring:resume-challenge`. |
| A stack que eu quero não existe | Peça por issue: [request-a-stack.md](request-a-stack.md). |
