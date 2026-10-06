# Pack: Kotlin Multiplatform (KMP)

## Identificação

- **Stack:** Kotlin Multiplatform, Kotlin 2.x, Gradle com Kotlin DSL, JDK 17 ou superior
- **Status:** experimental. O catálogo de defeitos ainda não foi validado por quem trabalha com KMP no dia a dia. Os comandos de build e de teste foram **verificados** num projeto mínimo, no Windows, com Kotlin 2.2.20, Gradle 8.14 e JDK 21.

## Pré-requisitos

- `java -version` deve mostrar JDK 17 ou superior (21 funciona). Se faltar, avise o usuário e pare.
- Gradle **não precisa** estar instalado: o projeto gerado inclui o Gradle Wrapper (`gradlew`, `gradlew.bat` e `gradle/wrapper/`, com o `gradle-wrapper.jar`). O wrapper tem de ser **obtido de um destes jeitos, nesta ordem**, e o projeto só segue depois que `./gradlew --version` funcionar:
  1. **Copiar de um desafio anterior** do repositório (`challenge-*/gradlew`, `gradlew.bat` e `gradle/`), se existir um.
  2. **Gerar com o Gradle instalado.** Se o comando `gradle` existir no PATH, crie primeiro o `settings.gradle.kts` do projeto (o comando falha numa pasta sem ele) e rode, dentro da pasta do desafio, `gradle wrapper --gradle-version 8.14` (ou outra versão compatível com o Kotlin escolhido). Verificado: gera `gradlew`, `gradlew.bat` e `gradle/wrapper/`.
  3. **Pedir ao usuário.** Se não houver nem desafio anterior nem `gradle` instalado, **pare e peça** uma destas opções, em vez de improvisar: (a) instalar o Gradle (por exemplo, com `scoop`, `choco`, `brew` ou SDKMAN) e rodar de novo; ou (b) informar o caminho de um projeto Gradle que já tenha wrapper, para você copiar `gradlew`, `gradlew.bat` e `gradle/`.
  - **Nunca** crie o `gradle-wrapper.jar` à mão nem o baixe de uma fonte que o usuário não indicou: o jar é um binário executável.
- A primeira execução baixa o Gradle e as dependências, então precisa de internet e demora.
- Os desafios usam **só o alvo JVM**: não exigem Android SDK, Xcode nem emulador.

## Estrutura do projeto

- Projeto Gradle dentro de `challenge-NNN-<dominio>/`, com `settings.gradle.kts`, `build.gradle.kts`, `gradle.properties`, `gradlew`, `gradlew.bat`, `gradle/wrapper/`, `README.md` e `TASK.md`.
- Um módulo, com o plugin `kotlin("multiplatform")` (versão `2.2.20`, a verificada) e **apenas** o alvo `jvm()`, com `jvmToolchain(21)`, ou a versão do JDK usado. Os testes comuns usam `commonTest.dependencies { implementation(kotlin("test")) }`.
- Source sets: `src/commonMain/kotlin` (a maior parte da lógica de negócio, algo como 70% a 80%), `src/commonTest/kotlin`, `src/jvmMain/kotlin` (as implementações `actual`) e `src/jvmTest/kotlin`.
- Dependências, todas com versão fixa: `kotlinx-coroutines-core` e `kotlinx-serialization-json` (com o plugin de serialização) quando o tema pedir; `kotlin("test")` e `kotlinx-coroutines-test` nos testes.
- O código deve ter alguns `expect`/`actual` simples (por exemplo, relógio, gerador de identificadores, armazenamento), para o tema "código compartilhado x específico de plataforma" ser real, mesmo com um alvo só.
- Rede: use um serviço simulado (fake em memória), sem depender de servidor externo.
- `.gitignore` na raiz do repositório: `build/`, `.gradle/`, `.kotlin/`, `.idea/`, `*.iml`, `local.properties`, `.DS_Store`.

## Build e testes

Comandos verificados num projeto mínimo (Kotlin 2.2.20, Gradle 8.14, JDK 21, Windows). A primeira execução baixa as dependências e leva cerca de 30 segundos a alguns minutos; as seguintes levam segundos.

- Dependências e build (inclui os testes): `./gradlew build` (Windows: `gradlew.bat build`)
- Testes (os de `commonTest` e os de `jvmTest`, rodando na JVM): `./gradlew jvmTest`
- Todos os testes: `./gradlew allTests` (com um alvo só, equivale ao `jvmTest`)
- Verificação completa: `./gradlew check`
- Resultado dos testes em XML: `build/test-results/jvmTest/`; relatório em HTML: `build/reports/tests/jvmTest/index.html`

## Reproduzir um sintoma

- Prefira um **teste (`commonTest` ou `jvmTest`) que falha** ou que demonstra o comportamento errado, rodando com `./gradlew jvmTest`, e/ou passos curtos de uso de um ponto de entrada, com o resultado esperado x observado.
- Um trecho de log pode acompanhar o chamado. Verificado: usar `Dispatchers.Main` num teste da JVM lança `IllegalStateException: Module with the Main dispatcher is missing. Add dependency providing the Main dispatcher...`, e um `assertEquals` que falha aparece como `ComparisonFailure` com o teste e a linha em `./gradlew jvmTest`, que termina com `BUILD FAILED`.
- Não dependa de emulador, simulador nem de nenhum serviço externo para reproduzir.

## Tamanho do projeto

| Dificuldade | Tamanho | Referência |
|---|---|---|
| FÁCIL | Pequeno | ~10-15 arquivos Kotlin |
| MÉDIO | Pequeno ou médio | ~25-40 arquivos |
| DIFÍCIL | Médio ou grande | ~40-60 arquivos |
| SÊNIOR | Grande | 60+ arquivos, vários pacotes ou módulos Gradle |

## Temas

- Código compartilhado x específico de plataforma (`expect`/`actual`)
- Coroutines, dispatchers e concorrência
- `Flow` e `StateFlow`
- Cancelamento e escopos estruturados
- Serialização com `kotlinx.serialization`
- Data e hora com `kotlinx-datetime`
- Tratamento de erros (exceções, `Result`, `CancellationException`)
- Modelagem de domínio (`sealed class`, `value class`, `data class`)
- Nulabilidade e inicialização (`lateinit`, `!!`, `by lazy`)
- Testes com `kotlin.test` e `runTest`
- Organização de source sets e de módulos
- Cache e persistência em memória
- Logs e dados sensíveis

## Catálogo de defeitos

- **FÁCIL:** (visíveis numa leitura atenta): `!!` em valor que pode ser nulo; `catch` vazio; segredo ou chave no código; falta de validação de entrada; `println` de dado sensível; números mágicos; `var` público onde `val` bastaria.
- **MÉDIO:** (exigem entender o fluxo): `GlobalScope.launch` sem controle de ciclo de vida; `runBlocking` dentro de função `suspend` ou de código chamado de uma coroutine; `catch (e: Exception)` que engole a `CancellationException`; `Dispatchers.Main` usado em código compartilhado (falha em teste na JVM); `data class` com `var` usada como chave de `Map` ou elemento de `Set`; `lateinit` lido antes de ser inicializado; implementação `actual` que diverge do contrato do `expect`.
- **DIFÍCIL:** (exigem raciocínio entre classes): race condition em estado compartilhado (um `MutableMap` ou contador alterado por várias coroutines em `Dispatchers.Default` sem `Mutex` nem atômicos); `MutableStateFlow` atualizado com leitura e escrita separadas em vez de `update {}`; `Flow` frio coletado mais de uma vez, repetindo o efeito colateral; `async` cujo `await` nunca é chamado, perdendo a exceção; trabalho bloqueante sem `withContext` em um dispatcher adequado; retry que duplica efeito colateral por falta de idempotência; `Json` sem `ignoreUnknownKeys`, quebrando quando a API ganha um campo.
- **SÊNIOR:** (arquitetura e trade-offs): `expect`/`actual` com contrato frouxo, com implementações que se comportam de forma diferente; camada compartilhada acoplada a detalhes de plataforma por interfaces que vazam; escopos de coroutine sem dono e sem estratégia de cancelamento; erros tratados de jeitos diferentes (exceção, `Result`, `null`) em cada camada; testes que usam `runBlocking` e `delay` reais em vez de `runTest`, ficando lentos e instáveis; vazamento de dados sensíveis em logs; modelo de domínio anêmico; ausência de estratégia de retry e de modo offline.

## Falsos positivos

**Princípio:** é um trecho que dispara o alarme de um revisor (parece um defeito do catálogo), mas que está correto por uma razão que dá para explicar lendo o contexto ao redor. Quem revisa bem confirma a razão antes de reclamar. Os exemplos abaixo são **só inspiração**: crie outros, ligados ao domínio do desafio.

- Um `!!` logo depois de uma checagem ou de um `requireNotNull` que garante o valor no mesmo fluxo.
- Um `runBlocking` na função `main` de uma ferramenta de linha de comando, ou em um teste, onde bloquear a thread é o comportamento esperado.
- Um `catch (e: Exception)` que primeiro relança a `CancellationException` e só então trata o erro.
- Um `lateinit` sempre inicializado no construtor de teste ou em um bloco `init` que o precede.
- Uma `var` em `data class` interna que nunca é usada como chave nem compartilhada entre coroutines.
- Uma `expect class` pequena cuja `actual` é trivial de propósito, porque só existe um alvo.

## Limitações

- Este pack gera projetos **só com o alvo JVM**: apps Android e iOS exigem Android SDK, macOS e Xcode, que não dá para verificar em qualquer ambiente.
- Os comandos de build e de teste foram verificados **apenas com o alvo JVM, no Windows**, com Kotlin 2.2.20, Gradle 8.14 e JDK 21. Outras combinações de versões e outros sistemas não foram testados.
- As versões do Kotlin, do Gradle e do JDK precisam ser compatíveis entre si: fixe todas no projeto gerado.
- O catálogo de defeitos e os temas ainda não foram validados por quem trabalha com KMP no dia a dia. Só dois itens foram comprovados na prática: o erro de `Dispatchers.Main` em teste da JVM e a saída de um teste que falha.
