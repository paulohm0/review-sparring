# Passo a passo: do primeiro desafio à devolutiva

## Passo 1: criar um repositório vazio para o treino

```bash
mkdir meu-treino-review
cd meu-treino-review
git init
claude
```

Use uma pasta separada. É nela que o plugin vai criar os desafios, os commits, as tags e as branches.

## Passo 2: gerar o desafio

Dentro do Claude Code:

```
/review-sparring:generate-challenge medium java-spring
```

- O primeiro argumento é a **dificuldade**: `easy`, `medium`, `hard` ou `senior`.
- O segundo é a **stack** (opcional). Sem ele, usa `java-spring`.

O Claude vai decidir sozinho o domínio, os temas e o tipo de task, planejar os defeitos (sem mostrar), gerar o projeto, rodar o build e os testes, e criar:

- a pasta `challenge-001-<dominio>/` com o projeto, um `README.md` (como rodar) e o `TASK.md` (o briefing);
- um commit baseline, por exemplo `feat(challenge-001): add <dominio> baseline`;
- a tag `challenge-001-base` nesse commit;
- a branch `solution/001-<dominio>`, onde você vai trabalhar.

Ele termina apresentando a task, **sem revelar defeitos**. O tipo de task é sorteado:

| Tipo | O que é |
|---|---|
| PROBLEMA | Há um sintoma (bug, lentidão, dado inconsistente). Você investiga e acha a causa raiz. |
| REFATORAÇÃO | Há problemas de design. Você melhora sem quebrar o comportamento. |
| MELHORIA | Algo funciona, mas precisa ficar melhor em algo concreto. |
| FEATURE | Você implementa algo novo respeitando a arquitetura. |

## Passo 3: ler o código e trabalhar

Abra a pasta do desafio no editor. Leia o `TASK.md` e o código **como se fosse um PR de outra pessoa**:

- Resolva a task.
- Corrija o que achar errado e/ou deixe comentários `// REVIEW: o que vi e por quê` no próprio código (use a sintaxe de comentário da linguagem).
- Rode o projeto e os testes:

```bash
# java-spring (Windows: mvnw.cmd)
./mvnw -q verify
./mvnw spring-boot:run

# flutter
flutter pub get
flutter analyze
flutter test
```

Você não precisa preencher relatório nenhum.

## Passo 4: tirar dúvidas (mentoria)

Pergunte no chat o que quiser sobre regras de negócio, conceitos, como rodar. O mentor:

- **responde dúvidas** e ajuda a reproduzir o problema;
- **faz perguntas** em vez de dar respostas ("e se duas requisições chegarem ao mesmo tempo?");
- **não confirma** se um trecho tem defeito e não aponta arquivo nem linha;
- dá **dicas progressivas só se você pedir** (nível 1: vaga; nível 2: a área e o tipo de problema). A solução completa só se você disser que desistiu.

Se a conversa for retomada em outra sessão e o mentor parecer perdido, rode `/review-sparring:mentor`.

## Passo 5: pedir a revisão

Quando terminar, escolha um dos dois modos.

**Modo chat (o mais simples, sem conta extra):**

```
/review-sparring:review-pr
```

O Claude compara o seu trabalho com o commit baseline, roda build e testes, faz **a entrevista** (2 a 4 perguntas por rodada, sem confirmar acertos) e então dá a devolutiva final.

**Modo Pull Request:** a review aparece no GitHub, escrita pela conta de revisão. Veja [pull-request-review.md](pull-request-review.md) e, para configurar a segunda conta, [review-account.md](review-account.md).

## Passo 6: a devolutiva final

Ao fim, você recebe:

1. se a task foi cumprida (causa raiz ou só o sintoma) e a qualidade da solução;
2. o que você acertou entre os defeitos extras;
3. o que você perdeu, com arquivo, linha, impacto e correção;
4. onde você errou (mudanças desnecessárias, falsos positivos tratados como bug, correções que criaram problemas);
5. como poderia ter percebido cada defeito perdido;
6. a qualidade da sua revisão como prática;
7. nota de 0 a 10 para a task e o percentual de defeitos extras encontrados, ponderado por severidade (desconta bugs introduzidos e dicas usadas);
8. pontos de estudo e a dificuldade sugerida para o próximo desafio.

## Passo 7: o próximo desafio

Rode `generate-challenge` de novo, na mesma pasta. O número sobe (`challenge-002-...`), e o plugin evita repetir domínio e tipo de task dos desafios anteriores.

```
/review-sparring:generate-challenge hard flutter
```

## Se a sessão for perdida

Abra o Claude Code na raiz do repositório, na branch `solution/NNN-...`, e rode:

```
/review-sparring:resume-challenge
```

(Opcionalmente com o número: `/review-sparring:resume-challenge 1`.) O Claude lê o `TASK.md`, audita o commit baseline para reconstruir a lista de defeitos e volta ao modo mentor. Como a lista é reconstruída por auditoria, ela **pode estar incompleta**, e ele avisa isso.
