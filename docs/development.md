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
│   ├── resume-challenge/SKILL.md
│   └── add-stack/SKILL.md
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
