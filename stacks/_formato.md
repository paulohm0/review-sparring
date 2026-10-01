# Formato de um pack de stack

Um pack é um arquivo `stacks/<nome>.md` que diz ao plugin tudo o que é específico de uma linguagem ou framework. As skills (`generate-challenge`, `resume-challenge`, `review-pr`) só definem o processo; o conteúdo técnico vem daqui.

Para criar uma stack nova, copie um pack existente e preencha **todas** as seções abaixo, com estes títulos.

## Seções obrigatórias

1. **Identificação:** nome da stack, versões-alvo e status (`estável` ou `experimental`).
2. **Pré-requisitos:** comandos para checar o ambiente (ex.: `java -version`) e o que fazer se faltar algo.
3. **Estrutura do projeto:** como organizar o projeto gerado (pastas, arquivo de build, `.gitignore` da stack, banco ou serviços usados nos desafios).
4. **Build e testes:** comandos para compilar, rodar a suíte e rodar a aplicação.
5. **Reproduzir um sintoma:** como descrever e verificar um chamado do tipo PROBLEMA nesta stack (arquivo `.http`, teste que falha, passos na tela, log).
6. **Tamanho do projeto:** tabela de dificuldade x tamanho, na unidade natural da stack (classes, telas, tipos).
7. **Temas:** lista de assuntos que o desafio pode abordar.
8. **Catálogo de defeitos:** exemplos realistas por dificuldade (FÁCIL, MÉDIO, DIFÍCIL, SÊNIOR). É a seção mais importante: ela dá qualidade aos desafios.
9. **Falsos positivos:** um parágrafo com o **princípio** (código que parece errado mas é correto por uma razão explicável) e uma lista de exemplos, marcados como inspiração, para o plugin criar variações novas a cada desafio.
10. **Limitações:** o que esta stack não consegue fazer (ex.: precisa de macOS), e como contornar.

## Regras para o catálogo

- Defeitos reais, do tipo que aparece em PRs de verdade; nada de erro de sintaxe.
- Cada item descreve o defeito e, entre parênteses, por que ele passa despercebido.
- O catálogo é referência, não lista fechada: o plugin pode (e deve) variar além dele.
- Marque o pack como `experimental` até alguém que trabalhe com a stack revisar o catálogo e gerar ao menos um desafio de ponta a ponta.
