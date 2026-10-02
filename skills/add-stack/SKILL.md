---
name: add-stack
description: Guia a criação de um pack para uma stack nova (stacks/<nome>.md) dentro de uma cópia local do plugin, verifica o toolchain, valida o resultado e explica como testar e contribuir por Pull Request.
argument-hint: "<stack-name>"
disable-model-invocation: true
---

# Adicionar uma stack nova

Você ajuda o usuário a criar um **pack de stack**: o arquivo `stacks/<nome>.md` que diz ao plugin como compilar, testar, reproduzir um problema, quais temas cobrir e quais defeitos são típicos da stack. Responda em português do Brasil.

`$ARGUMENTS` traz o nome da stack. A qualidade do pack decide a qualidade dos desafios, então o objetivo é um pack **verificado**, e não só plausível.

## Regras

- Crie ou altere **apenas** `stacks/<nome>.md` na cópia local. Só mexa na tabela de stacks do `README.md` se o usuário concordar.
- **Nunca** faça `git add`, `git commit`, `git push`, abra PR ou crie fork. Os comandos de git ficam para o usuário, na etapa final.
- **Nunca** escreva dentro de `${CLAUDE_PLUGIN_ROOT}` (no plugin instalado, ele é uma pasta de cache e as mudanças se perdem). Escreva sempre na pasta de trabalho atual.
- Não invente fatos da stack. Se não conseguir verificar um comando, marque-o no pack como "não verificado" e avise o usuário.

## Etapa 1: conferir o ambiente

A pasta de trabalho atual precisa ser uma **cópia local do plugin**: confira que existem `.claude-plugin/plugin.json` (com `"name": "review-sparring"`) e `stacks/_formato.md`.

Se não existirem, explique em poucas linhas e **pare**:

- o pack precisa ser criado numa cópia do repositório (`git clone https://github.com/paulohm0/review-sparring.git`; para contribuir, clonar um fork);
- depois, abrir o Claude Code dentro da pasta clonada com `claude --plugin-dir .` e rodar de novo `/review-sparring:add-stack <nome>`;
- o passo a passo está em `docs/add-a-stack.md`.

## Etapa 2: nome

Normalize o nome para minúsculas, com letras, números e hífens (por exemplo, `go-gin`). Não pode começar com `_` nem conter espaços ou `/`. Se faltar, pergunte. Se `stacks/<nome>.md` já existir, pergunte se o usuário quer escolher outro nome ou revisar o existente.

## Etapa 3: coletar o essencial

Faça **uma única rodada curta** de perguntas (no máximo 5), propondo um padrão sensato para cada uma para o usuário só confirmar:

1. Linguagem, framework e versão-alvo.
2. Tipo de projeto dos desafios (API, CLI, app, biblioteca).
3. Gerenciador de dependências e comando de build.
4. Comando para rodar os testes (precisa funcionar sem serviços externos, de preferência).
5. Limitações (sistema operacional, ferramentas pesadas, emulador, Docker).

## Etapa 4: verificar o toolchain

1. Rode o comando de versão da ferramenta (por exemplo, `go version`). Se não estiver instalada, avise, marque os comandos do pack como "não verificados" e siga.
2. Se estiver instalada, **prove o fluxo**: crie um projeto mínimo num diretório **temporário fora do repositório**, com um teste trivial, e rode os comandos de dependências, build e teste propostos. Anote os que funcionaram e use exatamente esses no pack. Apague o diretório temporário ao terminar.

## Etapa 5: escrever o pack

Leia `stacks/_formato.md` e `stacks/java-spring.md` (modelo de nível de detalhe). Escreva `stacks/<nome>.md` com **as dez seções do formato, com os títulos exatos**, em português:

- Identificação (status `experimental`), Pré-requisitos, Estrutura do projeto, Build e testes, Reproduzir um sintoma, Tamanho do projeto, Temas, Catálogo de defeitos, Falsos positivos e Limitações.
- **Temas:** pelo menos 8, reais para a stack.
- **Catálogo de defeitos:** pelo menos 4 itens por dificuldade (FÁCIL, MÉDIO, DIFÍCIL, SÊNIOR), todos **realistas**, do tipo que aparece em PRs de verdade e que funcionam no tipo de projeto da stack. Nada de erro de sintaxe. Escale a sutileza: visível numa leitura atenta, depende do fluxo, depende de raciocínio entre classes ou módulos, falhas de arquitetura.
- **Falsos positivos:** o parágrafo do princípio (código que parece errado, mas é correto por uma razão explicável) e pelo menos 4 exemplos, marcados como inspiração.
- **Reproduzir um sintoma:** o formato natural da stack (teste que falha, requisição, log), sem depender de emulador ou de serviços externos quando possível.

## Etapa 6: autoverificação

Antes de mostrar o resultado, confira e corrija, usando o checklist do fim de `stacks/_formato.md`:

- as dez seções existem, com os títulos do formato;
- nenhum trecho foi copiado de Java/Spring ou Flutter por engano (procure termos como `Spring`, `@Transactional`, `Maven`, `widget`, `setState`, a não ser que existam na stack);
- os comandos são os que você verificou na Etapa 4, ou estão marcados como "não verificados";
- o catálogo cobre os quatro níveis e não tem itens vagos;
- o status é `experimental`.

Depois rode `claude plugin validate .` e informe o resultado. Esse comando valida o manifesto do plugin e **não** o conteúdo do pack.

## Etapa 7: próximos passos

Mostre ao usuário um resumo curto: o que o pack cobre, o que **não** foi verificado e onde ele deve olhar com mais atenção (em geral, o catálogo de defeitos). Depois explique:

1. **Testar:** rodar `/reload-plugins`, criar uma pasta de treino vazia fora do clone, abrir `claude --plugin-dir <caminho-do-clone>` e rodar `/review-sparring:generate-challenge easy <nome>`. Fazer pelo menos um desafio completo, até a devolutiva, antes de confiar no pack.
2. **Contribuir (opcional)**, com os comandos dele, no terminal dele (adapte o nome da stack):
   - `git checkout -b stack/<nome>` (se ainda não estiver nessa branch)
   - `git add stacks/<nome>.md README.md`
   - `git commit -m "feat(stacks): add <nome> pack"`
   - `git push -u origin stack/<nome>`
   - abrir um PR do fork para `paulohm0/review-sparring` informando a stack, como testou, qual desafio completo gerou e revisou e o que não conseguiu verificar.
3. Apontar `docs/add-a-stack.md` para o fluxo completo.
