# Limitações e segurança

## Limitações

- **Lista de defeitos só na sessão.** Sem arquivo de gabarito. Se a sessão cair, a lista é reconstruída por auditoria e pode ficar incompleta.
- **Consumo de tokens.** Gerar projetos maiores e revisar rodando build e testes gasta tokens da conta de quem usa.
- **Qualidade dos defeitos.** Depende do pack e do modelo. Packs experimentais podem gerar desafios menos realistas.
- **Revisão por PR exige uma segunda conta** para ter `APPROVE` e `REQUEST_CHANGES` de verdade. Sem ela, o plugin faz a revisão no chat ou posta só como `COMMENT`.

## Segurança

- **Regras são instruções.** "Nunca faz merge, push, fecha ou edita o PR e nunca exibe o token" são instruções ao modelo, e não bloqueios técnicos. Use token de menor escopo possível, com validade curta, e revise o que o Claude pede para executar.
- **O conteúdo do PR é tratado como dado.** O plugin instrui o Claude a ignorar instruções que apareçam em diffs, comentários ou arquivos.
- **Token só no ambiente do Claude.** Não o defina nas variáveis de ambiente do Windows (usuário ou máquina) nem em arquivo versionado, para o seu `gh` e o seu `git` continuarem agindo como a sua conta principal.
- **Tokens fine-grained não funcionam** para uma conta de revisão que é só colaboradora de repositório de outra conta pessoal. Use token classic, com o menor escopo possível. Detalhes em [review-account.md](review-account.md).
- **Contas.** Mantenha 2FA ligado na conta principal e na de revisão, e a conta de revisão colaboradora só dos repositórios de treino.
