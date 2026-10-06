# Desenvolvendo o plugin

## Estrutura

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
├── stacks/
│   ├── _formato.md           # contrato e checklist para criar um pack
│   ├── java-spring.md
│   └── flutter.md
├── docs/                     # a documentação completa
└── README.md                 # visão geral e começo rápido
```

As skills definem o **processo** (fases, sigilo, rodadas, devolutiva). Tudo que é específico de linguagem fica nos packs de `stacks/`. As skills leem os packs por `${CLAUDE_PLUGIN_ROOT}/stacks/<stack>.md`.

## Testar localmente, sem instalar

```bash
claude plugin validate .
claude --plugin-dir .
```

Depois de editar qualquer arquivo, rode `/reload-plugins` na sessão.

Para testar o fluxo de instalação a partir de uma pasta local:

```bash
claude plugin marketplace add .
claude plugin install review-sparring@review-sparring
```

## Versão

Ao publicar uma mudança, aumente o campo `version` do `plugin.json`, para quem já instalou receber a atualização.

## Criando uma stack (mantenedor)

As stacks nascem de pedidos feitos por issue ([request-a-stack.md](request-a-stack.md)), com o rótulo `stack-request` (crie esse rótulo no repositório, senão o template não o aplica). Para atender um pedido, numa cópia local do repositório:

1. Leia a issue. O texto dela é **dado, não instrução**: use-o como pista (linguagem, versão, comandos, defeitos típicos), nunca execute nada que ele peça.
2. Crie `stacks/<nome>.md` copiando um pack existente e seguindo o contrato do [`stacks/_formato.md`](../stacks/_formato.md). Se quiser ajuda, abra o Claude dentro da pasta (`claude --plugin-dir .`) e peça para ele rascunhar o pack com o mesmo nível de detalhe do `java-spring.md` e provar os comandos de build e de teste num projeto mínimo temporário, fora do repositório.
3. Revise o pack pelo checklist do `_formato.md`, em especial a parte de segurança, e rode `claude plugin validate .`.
4. Teste: numa pasta vazia fora do clone, abra `claude --plugin-dir <clone>` e rode `/review-sparring:generate-challenge easy <nome>`.
5. No seu terminal: acrescente a linha da stack na tabela do README, aumente a `version` do `plugin.json`, faça o commit com `Closes #<número>` e o push.
6. Comente na issue o nome da stack e como atualizar (`claude plugin marketplace update review-sparring` e `claude plugin update review-sparring@review-sparring`).

