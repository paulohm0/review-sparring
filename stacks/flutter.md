# Pack: Flutter

## Identificação

- **Stack:** Flutter estável, Dart 3
- **Status:** experimental (catálogo de defeitos ainda não validado por quem trabalha com Flutter no dia a dia)

## Pré-requisitos

- `flutter --version` deve funcionar. Se faltar, avise o usuário e pare.
- `flutter doctor` não precisa estar 100% verde: os desafios devem ser verificáveis só com testes (`flutter test`), sem emulador nem dispositivo.

## Estrutura do projeto

- Criar com `flutter create` dentro de `challenge-NNN-<dominio>/` (use `--org com.example` e um nome de pacote em snake_case), e remover o que não for usado.
- Organização em `lib/` por feature ou por camada (`data`, `domain`, `presentation`), coerente em todo o projeto.
- Gerenciamento de estado: escolha **uma** abordagem e mantenha (por exemplo `provider`, `flutter_bloc` ou `riverpod`). Dependências devem ser poucas e conhecidas.
- Rede: prefira um serviço simulado (fake em memória ou `http` com `MockClient`) para não depender de servidor externo.
- `.gitignore` do `flutter create` já é adequado; confirme que `build/` e `.dart_tool/` estão ignorados.

## Build e testes

- Dependências: `flutter pub get`
- Análise estática: `flutter analyze`
- Testes (unitários e de widget): `flutter test`
- Rodar o app (opcional, exige dispositivo ou emulador): `flutter run`

## Reproduzir um sintoma

- Prefira um **teste de widget ou unitário que falha** (ou que demonstra o comportamento errado) e/ou passos curtos na tela, com o resultado esperado x observado.
- Não dependa de rodar no emulador para validar a reprodução.
- Um log curto pode acompanhar o chamado (por exemplo, a exceção `setState() called after dispose()`).

## Tamanho do projeto

| Dificuldade | Tamanho | Referência |
|---|---|---|
| FÁCIL | Pequeno | ~10-15 arquivos Dart (2 a 3 telas) |
| MÉDIO | Pequeno ou médio | ~25-40 arquivos |
| DIFÍCIL | Médio ou grande | ~40-60 arquivos |
| SÊNIOR | Grande | 60+ arquivos, módulos por feature |

## Temas

- Gerenciamento de estado (setState, Provider, Bloc, Riverpod)
- Ciclo de vida de widgets (`initState`, `dispose`, `didUpdateWidget`)
- Assincronia (`Future`, `async/await`, `Stream`)
- Navegação e passagem de dados entre telas
- Performance de rebuild (`const`, `keys`, `ListView.builder`)
- Persistência local (SharedPreferences, SQLite, Hive)
- Consumo de API HTTP, parsing de JSON e tratamento de erro
- Formulários e validação
- Testes (unitários, de widget, mocks)
- Acessibilidade e responsividade
- Gerenciamento de recursos (controllers, subscriptions, timers)

## Catálogo de defeitos

- **FÁCIL:** (visíveis numa leitura atenta):`setState` chamado depois de `dispose`; ausência de validação em `TextFormField`; JSON parseado sem tratar campo nulo; texto fixo (hardcoded) onde deveria haver constante; `print` de dado sensível; token salvo em `SharedPreferences` sem necessidade.
- **MÉDIO:** (exigem entender o fluxo):`BuildContext` usado depois de um `await` sem checar `mounted`; `ListView` com itens que perdem estado por falta de `key`; `Future` criado dentro de `build` (dispara a cada rebuild); `StreamSubscription` ou `AnimationController` não cancelado em `dispose`; estado duplicado entre widget pai e filho; `FutureBuilder` recriando o `Future` a cada rebuild.
- **DIFÍCIL:** (exigem raciocínio entre classes): race condition entre duas requisições, com a resposta antiga sobrescrevendo a nova; `Bloc` ou `Provider` com estado mutável alterado in-place (a UI não atualiza); `InheritedWidget` ou provider acima do ponto errado da árvore; cache que nunca invalida; navegação que empilha a mesma tela repetidamente; testes de widget que passam por `pumpAndSettle` mascarando loop ou animação infinita.
- **SÊNIOR:** (arquitetura e trade-offs): estado global acoplando features que deveriam ser independentes; camada de dados acoplada à UI; tratamento de erro inconsistente entre camadas; vazamentos de memória por listeners não removidos; ausência de estratégia de offline e de retry; testes que mockam tudo e não validam comportamento.

## Falsos positivos

**Princípio:** é um trecho que dispara o alarme de um revisor (parece um defeito do catálogo), mas que está correto por uma razão que dá para explicar lendo o contexto ao redor. Quem revisa bem confirma a razão antes de reclamar. Os exemplos abaixo são **só inspiração**: crie outros, ligados ao domínio do desafio.

- Um `setState` dentro de callback que parece perigoso, mas protegido por `if (!mounted) return;`.
- Um widget `StatefulWidget` onde um `StatelessWidget` bastaria, mas que mantém um controller legítimo.
- Um `late` que parece arriscado mas é sempre inicializado em `initState`.
- Um `dynamic` isolado na fronteira do parsing de JSON, seguido de conversão e validação.
- Um `ListView` sem `key` em uma lista estática, sem reordenação nem estado por item.
- Um `Future` criado em `build` que é na verdade cacheado em uma variável de estado, criada uma única vez.
- Um `StreamController` que não é fechado em `dispose` porque vive no escopo do app inteiro, de propósito.
- Um `BuildContext` usado depois de `await`, mas já guardado em uma variável local antes, como o padrão recomendado (por exemplo, o `Navigator` capturado antes).

## Limitações

- Sem dispositivo, não dá para verificar comportamentos visuais. Concentre os defeitos e a verificação em lógica, estado e testes.
- Dependências externas devem ter versões fixas no `pubspec.yaml`, para o projeto compilar de forma reprodutível.
