# Contribuindo

Obrigado por querer melhorar o review-sparring! Este arquivo diz **o que aceitamos** e **o que o mantenedor confere**. Os passos e comandos estão em outro lugar, para ficarem em um só.

## O que aceitamos

- **Packs de stack novas** (Kotlin, Go, Node.js, Python...). É a contribuição mais bem-vinda.
- **Correções e melhorias** em packs existentes, nas skills ou na documentação.
- **Relatos de problemas e ideias**, por issue.

Para mudar o comportamento de uma skill (`skills/`), abra uma issue antes, para alinharmos a ideia antes de você escrever código.

## Como contribuir com uma stack

O passo a passo completo, com os comandos, está em [docs/add-a-stack.md](docs/add-a-stack.md). O formato do pack e o checklist de revisão estão em [stacks/_formato.md](stacks/_formato.md).

## O que o mantenedor confere

Tudo que está no checklist do [`stacks/_formato.md`](stacks/_formato.md), com atenção especial à **segurança**: um pack é texto que o Claude segue como instrução em toda sessão de quem usar a stack, então um pack que peça algo além de descrever a stack não é aceito.

## Como a revisão acontece

O PR é revisado no GitHub pelo mantenedor, que pode pedir ajustes na mesma branch. Packs novos entram como `experimental` e deixam de ser depois que alguém que trabalha com a stack validar o catálogo de defeitos.

## Licença

Ao contribuir, você concorda que o seu trabalho será distribuído sob a [licença MIT](LICENSE) do projeto.
