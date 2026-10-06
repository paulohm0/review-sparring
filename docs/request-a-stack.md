# Pedir uma stack nova

O plugin traz Java/Spring e Flutter. Quer treinar em outra linguagem ou framework (Kotlin, Go, Node.js, Python...)? Você não precisa escrever nada: **abra uma issue** e o mantenedor cria a stack. Ele atende logo e avisa na própria issue quando estiver publicada.

## Como pedir

1. Tenha uma conta no GitHub (a gratuita basta).
2. Antes de pedir, veja as [issues existentes](https://github.com/paulohm0/review-sparring/issues). Se alguém já pediu a mesma stack, reaja com 👍 nela e pronto.
3. Se não existir, [abra um pedido de stack](https://github.com/paulohm0/review-sparring/issues/new?template=stack-request.yml). Ou, pelo repositório: aba **Issues** → **New issue** → **Pedido de stack nova** → **Get started**.
4. Preencha a **linguagem e o framework** (é o único campo obrigatório). Se quiser ajudar, informe também a versão e, no campo de observações, os comandos de build e de teste que você usa e problemas típicos que já viu em code reviews dessa stack.
5. Clique em **Submit new issue**.

## Quando estiver pronta

O mantenedor comenta na issue. Para receber a stack, atualize o plugin:

```bash
claude plugin marketplace update review-sparring
```

```bash
claude plugin update review-sparring@review-sparring
```

Reinicie a sessão (ou rode `/reload-plugins`) e gere um desafio com ela:

```
/review-sparring:generate-challenge easy kotlin
```

(troque `kotlin` pelo nome da stack, que o mantenedor informa na issue.)

Se testar o desafio e achar algo estranho, conte na própria issue: isso ajuda a melhorar o pack.
