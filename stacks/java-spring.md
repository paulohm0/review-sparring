# Pack: Java + Spring Boot

## Identificação

- **Stack:** Java 21, Spring Boot 3.x, Maven
- **Status:** estável (portado do laboratório original do autor)

## Pré-requisitos

- `java -version` deve mostrar Java 21 ou superior.
- Maven **não precisa** estar instalado: o projeto gerado inclui o Maven Wrapper (`mvnw` e `mvnw.cmd`). Se `mvn` existir, gere o wrapper com `mvn wrapper:wrapper`. Se não existir, crie os arquivos do wrapper à mão (`mvnw`, `mvnw.cmd` e `.mvn/wrapper/maven-wrapper.properties` apontando para uma versão fixa do Maven), ou copie-os de um desafio anterior do repositório. Confirme que `./mvnw -v` funciona antes de seguir.
- Se for usar Testcontainers ou RabbitMQ, Docker precisa estar disponível. Prefira H2 e testes sem Docker quando possível.

## Estrutura do projeto

- Projeto Maven completo dentro de `challenge-NNN-<dominio>/`, com `pom.xml`, `mvnw`, `mvnw.cmd`, `.mvn/wrapper/`, `src/main/java`, `src/test/java`, `README.md` e `TASK.md`.
- Pacote base `com.example.<dominio>`, organizado em camadas (`web`, `service`, `domain`, `repository`, `config` etc.).
- Banco: H2 em memória por padrão. Se o tema pedir RabbitMQ ou Redis, use `docker-compose.yml` e Testcontainers apenas quando necessário.
- `.gitignore` na raiz do repositório: `target/`, `.idea/`, `*.iml`, `.vscode/`, `.DS_Store`.

## Build e testes

- Testes e verificação: `./mvnw -q verify` (Windows: `mvnw.cmd -q verify`).
- Rodar a aplicação: `./mvnw spring-boot:run`.

## Reproduzir um sintoma

- Forneça um arquivo `requests.http` (formato do IntelliJ/VS Code REST Client) e/ou comandos `curl`, e, se útil, um trecho de log.
- A sequência de requisições deve reproduzir o sintoma de fato, partindo de dados criados pela própria aplicação.

## Tamanho do projeto

| Dificuldade | Tamanho | Referência |
|---|---|---|
| FÁCIL | Pequeno | ~10-15 classes |
| MÉDIO | Pequeno ou médio | ~25-40 classes |
| DIFÍCIL | Médio ou grande | ~40-50 classes |
| SÊNIOR | Grande | 50+ classes, múltiplos módulos ou pacotes |

## Temas

- CRUD e camadas (Controller/Service/Repository)
- Separação de responsabilidades e DTOs/Mappers
- Validação e tratamento de exceções
- Autenticação e autorização (Spring Security, JWT, roles)
- Refresh token e gestão de sessão
- JPA/Hibernate (relacionamentos, N+1, lazy loading, transações)
- Mensageria com RabbitMQ (producers, consumers, DLQ, idempotência)
- Cache (Redis ou Caffeine)
- Concorrência e consistência de dados
- Testes (unitários e de integração)
- Configuração, profiles e segredos
- Logs e observabilidade
- Integração com APIs externas (RestClient/WebClient, retry, timeout)

## Catálogo de defeitos

- **FÁCIL** (visíveis numa leitura atenta): senha em texto puro, falta de validação de entrada, `catch` vazio, endpoint sem autorização, magic numbers, segredo no código.
- **MÉDIO** (exigem entender o fluxo): `@Transactional` em método privado ou em chamada interna (o proxy não age), N+1, entidade exposta direto na API, lógica de negócio no controller, comparação de `String` com `==`, `Optional.get()` sem checagem.
- **DIFÍCIL** (exigem raciocínio entre classes): race condition em saldo ou estoque, consumer RabbitMQ sem idempotência ou com ack incorreto, JWT sem validar `exp` ou algoritmo, IDOR, `LazyInitializationException` latente, cache com chave errada, retry que duplica efeito colateral.
- **SÊNIOR** (arquitetura e trade-offs): acoplamento entre módulos, transação distribuída ignorada, ausência de outbox, vazamento de dados sensíveis em logs, modelo de domínio anêmico, testes que passam sem validar nada, problemas de escalabilidade.

## Falsos positivos

**Princípio:** é um trecho que dispara o alarme de um revisor (parece um defeito do catálogo), mas que está correto por uma razão que dá para explicar lendo o contexto ao redor. Quem revisa bem confirma a razão antes de reclamar. Os exemplos abaixo são **só inspiração**: crie outros, ligados ao domínio do desafio.

- Um `catch` que engole a exceção de propósito, com log e motivo de negócio claro.
- Uso de `@SuppressWarnings` justificado por uma limitação de biblioteca.
- Um campo mutável em DTO interno que nunca é compartilhado entre threads.
- Uma consulta que parece N+1 mas está dentro de um lote pequeno e limitado por contrato.
- Uma comparação de `String` com `==` em constante internada, ou um `equals` feito do lado certo para evitar `NullPointerException`.
- Uma entidade devolvida pela API que na verdade é um tipo imutável sem relacionamentos, usado como DTO.
- Um endpoint público sem autenticação que é intencional (health check, documentação) e está explícito na configuração de segurança.
- Um `Optional.get()` logo depois de um `isPresent()` ou de um `orElseThrow` já tratado no mesmo fluxo.

## Limitações

- Testes de integração com RabbitMQ ou Redis exigem Docker; prefira alternativas em memória quando o tema não depender disso.
