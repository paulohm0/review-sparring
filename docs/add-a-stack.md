# Adicionar uma stack nova

O plugin traz Java/Spring e Flutter. Para treinar em outra linguagem ou framework (Go, Node.js, Python, Kotlin, Rust...), você escreve um **pack**: um arquivo Markdown em `stacks/` que diz ao plugin como compilar, testar, reproduzir um problema, quais temas cobrir e quais defeitos são típicos daquela stack. Tudo que é específico de linguagem vive nesse arquivo; o resto do plugin é genérico.

Há dois caminhos para ter a stack:

- **Só para você:** crie o pack na sua cópia local e use-a. Passos 1 a 6.
- **Para todo mundo:** depois de testar, contribua com o pack por Pull Request. Passo 7.

## Passo 1: baixar o projeto

O pack precisa ser criado numa **cópia local do repositório**. Não edite o plugin instalado: ele fica numa pasta de cache e as mudanças se perdem na atualização.

Se você pretende contribuir, faça primeiro um **fork** no GitHub (botão *Fork* em <https://github.com/paulohm0/review-sparring>) e clone o seu fork. Se for só para uso próprio, clone direto:

```bash
git clone https://github.com/paulohm0/review-sparring.git
```

```bash
cd review-sparring
```

Se for contribuir, crie uma branch para o pack (troque `go-gin` pelo nome da sua stack):

```bash
git checkout -b stack/go-gin
```

## Passo 2: abrir o Claude Code carregando a sua cópia

Se você já instalou o plugin pelo marketplace, desative-o para não misturar versões:

```bash
claude plugin disable review-sparring@review-sparring
```

Abra o Claude Code **dentro da pasta clonada**, carregando o plugin dela:

```bash
claude --plugin-dir .
```

## Passo 3: gerar o pack com o fluxo guiado

Dentro da sessão:

```
/review-sparring:add-stack go-gin
```

O nome é o nome do arquivo (`stacks/go-gin.md`) e o argumento que você passará depois ao `generate-challenge`. Use letras minúsculas, números e hífen.

O comando conduz estas etapas:

1. **Confere** que você está numa cópia local do plugin (senão explica como corrigir e para).
2. **Pergunta o essencial** sobre a stack: linguagem e framework, versão-alvo, tipo de projeto, comando de build, comando de testes e limitações.
3. **Confere o toolchain** na sua máquina e, se estiver instalado, **cria um projeto mínimo temporário** (fora do repositório) para provar que os comandos de build e de teste funcionam de verdade. Os comandos do pack são os que funcionaram.
4. **Escreve `stacks/go-gin.md`** com as dez seções do formato, usando o `java-spring.md` como modelo, com status `experimental`.
5. **Autoverifica** o resultado: seções presentes, nada copiado do Java por engano, defeitos realistas.
6. **Valida** o plugin e mostra os próximos passos.

O Claude rascunha, mas **a qualidade do pack é responsabilidade de quem conhece a stack**. Releia principalmente o catálogo de defeitos, que é o que mais pesa na qualidade dos desafios.

### O que cada seção do pack contém

| Seção | O que escrever |
|---|---|
| Identificação | Nome, versões-alvo e status (`experimental`). |
| Pré-requisitos | Comandos para checar o ambiente (por exemplo, `go version`) e o que fazer se faltar. |
| Estrutura do projeto | Como organizar o projeto gerado, o arquivo de build e o `.gitignore`. |
| Build e testes | Os comandos para compilar, testar e rodar. |
| Reproduzir um sintoma | Como descrever e verificar um chamado do tipo PROBLEMA (teste que falha, requisição, log). |
| Tamanho do projeto | Tabela de dificuldade x tamanho, na unidade da stack. |
| Temas | Assuntos que o desafio pode abordar. |
| Catálogo de defeitos | Exemplos **realistas** por dificuldade (fácil, médio, difícil, sênior). |
| Falsos positivos | O princípio e exemplos de código que parece errado, mas está correto. |
| Limitações | O que a stack não consegue fazer (por exemplo, precisa de um SO específico). |

O contrato completo está em [`stacks/_formato.md`](../stacks/_formato.md).

## Passo 3 (alternativa): criar o pack à mão

Se preferir não usar o fluxo guiado:

```bash
cp stacks/flutter.md stacks/go-gin.md
```

(No PowerShell: `Copy-Item stacks/flutter.md stacks/go-gin.md`.) Reescreva as dez seções para a sua stack seguindo [`stacks/_formato.md`](../stacks/_formato.md), e confira o checklist que está no fim desse arquivo.

## Passo 4: validar

```bash
claude plugin validate .
```

Isso valida o manifesto do plugin. **Não valida o conteúdo do pack**: quem faz isso é o checklist do `_formato.md` e o teste do próximo passo.

## Passo 5: testar com um desafio de verdade

Crie uma pasta de treino vazia, **fora** do clone, e abra o Claude Code carregando a sua cópia do plugin:

```bash
mkdir ../meu-treino-go
```

```bash
cd ../meu-treino-go
```

```bash
git init
```

```bash
claude --plugin-dir ../review-sparring
```

Dentro da sessão:

```
/review-sparring:generate-challenge easy go-gin
```

Confira se o projeto gerado compila e passa nos testes, se o `TASK.md` está claro e se os defeitos plantados são realistas para a stack. Se algo estiver ruim, ajuste o pack e rode `/reload-plugins`.

Faça pelo menos **um desafio completo**, até a devolutiva, antes de chamar o pack de confiável.

## Passo 6: usar só para você

Pronto: enquanto abrir o Claude com `--plugin-dir` apontando para a sua cópia, `generate-challenge <dificuldade> go-gin` funciona.

Para ter a stack disponível sem o `--plugin-dir`, publique a sua versão: faça push do seu fork e instale a partir dele.

```
/plugin marketplace add SEU-USUARIO/review-sparring
/plugin install review-sparring@review-sparring
```

(Remova antes o marketplace original, ou use outro nome no seu fork, para os dois não conflitarem.)

## Passo 7: contribuir com o pack (Pull Request)

Se o pack ficou bom, ele pode entrar no plugin para todo mundo. O fluxo acontece no GitHub:

1. Na pasta clonada, na branch `stack/go-gin`, adicione uma linha na tabela de stacks do `README.md` e faça commit **no seu terminal**:

```bash
git add stacks/go-gin.md README.md
```

```bash
git commit -m "feat(stacks): add go-gin pack"
```

```bash
git push -u origin stack/go-gin
```

2. No GitHub, abra um Pull Request do seu fork para `paulohm0/review-sparring` (`main`). Na descrição, informe: a stack e as versões, como você testou, **qual desafio completo você gerou e revisou**, e o que você não conseguiu verificar.
3. O PR é revisado como qualquer outro, pelo checklist de pack do [`_formato.md`](../stacks/_formato.md). Confira o checklist **antes** de abrir o PR. As regras de contribuição estão no [CONTRIBUTING.md](../CONTRIBUTING.md).
4. Quem mantém o plugin pode usar o próprio `/review-sparring:review-pr <número>` em uma cópia do repositório do plugin para revisar o pack (o comando reconhece PRs que só alteram `stacks/*.md`). Ajustes pedidos são feitos na mesma branch, com novos commits.

O plugin **nunca faz push nem abre PR por você**: os comandos acima são seus, na sua conta.

## Problemas comuns

| Sintoma | O que fazer |
|---|---|
| `add-stack` diz que você não está numa cópia local | Clone o repositório, abra o Claude **dentro** da pasta e use `claude --plugin-dir .` (passos 1 e 2). |
| `generate-challenge` diz que não achou o pack | O nome no comando precisa ser igual ao do arquivo, sem a extensão. Confira `stacks/` e rode `/reload-plugins`. |
| O Claude usa a versão instalada em vez da sua | Rode `claude plugin disable review-sparring@review-sparring` e reabra com `--plugin-dir`. |
| O build do projeto gerado falha | Os comandos do pack não foram verificados. Teste-os à mão, corrija o pack e gere de novo. |
