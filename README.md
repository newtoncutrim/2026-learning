# Plano de Estudos Completo — Base Forte em Engenharia de Software

> **Duração recomendada:** 36 meses
> **Carga mínima:** 1 hora por dia
> **Carga ideal:** 1 hora por dia + 2 horas no fim de semana
> **Perfil foco:** backend-heavy, fullstack consciente, base forte de computação, banco de dados, arquitetura, produção e entrevistas pleno/sênior.

---

# 1. Objetivo Geral

Este plano tem como objetivo construir uma base técnica forte e duradoura.

A prioridade não é apenas aprender framework, biblioteca ou ferramenta do momento.

A prioridade é entender:

* Como o computador funciona por baixo.
* Como a memória é usada.
* Como processos e threads funcionam.
* Como redes funcionam.
* Como banco de dados executa uma query.
* Como algoritmos e estruturas de dados impactam performance.
* Como escrever código limpo, testável e evolutivo.
* Como desenhar sistemas escaláveis.
* Como investigar problemas reais em produção.
* Como responder perguntas técnicas em entrevistas de nível pleno/sênior.

---

# 2. Legenda de Status

* ✅ **Já domina / prática real em produção**
* 🔁 **Reforçar / aprofundar**
* 🧠 **Evolução estratégica / novo nível**

---

# 3. Por que 36 meses e não 24 meses?

O plano original de 24 meses é bom, mas fica apertado para quem quer construir uma base realmente forte em:

* Algoritmos
* Estruturas de dados
* Redes
* Sistemas operacionais
* Baixo nível
* Banco de dados
* Arquitetura
* Segurança
* DevOps
* System Design
* Produção real

Com 1 hora por dia, 24 meses seriam suficientes para ter uma visão geral.

Mas para dominar bem, praticar, criar projetos, revisar e conseguir explicar em entrevista, o prazo mais realista é **36 meses**.

---

# 4. Ordem Real de Prioridade

## Prioridade 1 — Base raiz

* Linguagem C
* Memória
* Ponteiros
* Stack e Heap
* Algoritmos
* Estruturas de dados
* Sistemas operacionais
* Redes
* Banco de dados por dentro
* EXPLAIN ANALYZE

## Prioridade 2 — Backend forte

* HTTP
* REST
* Linux
* Git
* SQL
* PostgreSQL
* Redis
* Cache
* Filas
* RabbitMQ
* Testes
* APIs

## Prioridade 3 — Engenharia profissional

* SOLID
* Clean Code
* Refatoração
* DDD
* Design Patterns
* Arquitetura Hexagonal
* Monólito Modular
* Microserviços
* Observabilidade
* Segurança

## Prioridade 4 — Produção e senioridade

* Docker
* CI/CD
* AWS
* Kubernetes
* System Design
* SRE
* Kafka
* Streams
* Performance avançada
* Liderança técnica
* Projeto final master

---

# 5. Rotina Recomendada

## Segunda a sexta

* **30 minutos:** estudo teórico do tema principal.
* **20 minutos:** prática com código, terminal, banco ou ferramenta.
* **10 minutos:** anotação em README.

## Fim de semana

* **1 a 2 horas:** projeto prático.
* Revisar a semana.
* Documentar aprendizados.
* Criar perguntas de entrevista sobre o tema estudado.

---

# 6. Estrutura Geral da Trilha

## Fase 1 — Base Raiz de Computação

**Meses 1 a 12**

Foco:

* C
* Baixo nível
* Memória
* Sistemas operacionais
* Redes
* Algoritmos
* Estruturas de dados
* Compiladores
* Interpretadores

---

## Fase 2 — Banco de Dados e Backend Forte

**Meses 13 a 20**

Foco:

* HTTP
* REST
* Git
* Linux
* PostgreSQL
* Modelagem
* Índices
* EXPLAIN ANALYZE
* Locks
* Transações
* Redis
* Cache
* MongoDB
* Swoole
* Hyperf

---

## Fase 3 — Engenharia de Código, Arquitetura e Produção

**Meses 21 a 30**

Foco:

* SOLID
* Clean Code
* Refatoração
* Testes
* DDD
* Arquitetura Hexagonal
* Design Patterns
* RabbitMQ
* Microserviços
* Docker
* CI/CD
* Observabilidade
* Segurança

---

## Fase 4 — Especialização e Projeto Final

**Meses 31 a 36**

Foco:

* Frontend essencial
* System Design
* Performance avançada
* Kafka
* Event Sourcing
* SRE
* Segurança avançada
* Liderança técnica
* Projeto final master

---

# FASE 1 — BASE RAIZ DE COMPUTAÇÃO

---

## Mês 1 — Fundamentos de Programação em C

### Objetivo

Aprender programação em uma linguagem mais próxima do funcionamento real da máquina.

### Conteúdos

* Introdução à linguagem C
* Ambiente de compilação
* GCC
* Makefile
* Estrutura básica de programas
* Tipos primitivos
* Variáveis
* Condicionais
* Laços
* Funções
* Entrada e saída
* Compilação
* Linking

### Projetos

* Hello World com Makefile
* Mini calculadora em C
* Conversor de temperatura
* Programa simples de cadastro em terminal

### Perguntas para revisar

* O que é compilar um programa?
* Qual a diferença entre código-fonte, objeto e executável?
* O que é linking?
* Por que C ajuda a entender baixo nível?

---

## Mês 2 — Memória, Ponteiros e Baixo Nível

### Objetivo

Entender como variáveis, arrays, strings e objetos ocupam memória.

### Conteúdos

* Ponteiros
* Endereços de memória
* Stack x Heap
* Arrays em baixo nível
* Strings em C
* Alocação dinâmica
* `malloc`
* `calloc`
* `realloc`
* `free`
* Manipulação de memória
* Buffer overflow
* Memory leak
* Dangling pointer
* Debug com `gdb`
* Valgrind

### Projetos

* Implementar lista ligada
* Implementar pilha
* Implementar fila
* Criar manipulador de strings sem biblioteca pronta
* Simular vazamento de memória e corrigir com Valgrind

### Perguntas para revisar

* O que é ponteiro?
* Qual a diferença entre Stack e Heap?
* O que acontece se esquecer de chamar `free`?
* O que é memory leak?
* O que é buffer overflow?

---

## Mês 3 — Estruturas de Dados Básicas

### Objetivo

Implementar estruturas de dados manualmente e entender custo computacional.

### Conteúdos

* Big O
* Arrays
* Listas ligadas
* Pilhas
* Filas
* Deque
* HashMap
* Tabelas hash
* Colisão em hash
* Encadeamento
* Endereçamento aberto
* Busca linear
* Busca binária

### Projetos

* Implementar HashMap em C
* Criar biblioteca própria de estruturas de dados
* Criar testes simples para validar as estruturas
* Criar comparação de performance entre array, lista e hash

### Perguntas para revisar

* Quando usar array?
* Quando usar lista ligada?
* Como uma tabela hash funciona?
* O que é colisão?
* O que significa O(1), O(n), O(log n)?

---

## Mês 4 — Algoritmos Fundamentais

### Objetivo

Melhorar raciocínio lógico e capacidade de análise.

### Conteúdos

* Complexidade de tempo
* Complexidade de espaço
* Ordenação
* Bubble Sort
* Selection Sort
* Insertion Sort
* Merge Sort
* Quick Sort
* Busca binária
* Recursão
* Dividir para conquistar

### Projetos

* Implementar algoritmos de ordenação
* Medir tempo de execução
* Comparar algoritmos em diferentes volumes de dados
* Criar relatório explicando os resultados

### Perguntas para revisar

* Por que Bubble Sort é ruim para grandes volumes?
* Por que Merge Sort é O(n log n)?
* Qual a diferença entre busca linear e busca binária?
* O que é recursão?
* O que é complexidade de espaço?

---

## Mês 5 — Sistemas Operacionais I

### Objetivo

Entender como o sistema operacional executa programas.

### Conteúdos

* Processos
* Threads
* Scheduling
* Syscalls
* File descriptors
* Manipulação de arquivos
* I/O básico
* Entrada e saída bloqueante
* Entrada e saída não bloqueante
* Permissões
* Usuários
* Sinais

### Projetos

* Programa que manipula arquivos em C
* Programa que copia arquivos
* Programa que lista diretórios
* Mini utilitário estilo terminal

### Perguntas para revisar

* O que é um processo?
* O que é uma thread?
* O que é uma syscall?
* O que é file descriptor?
* Qual a diferença entre I/O bloqueante e não bloqueante?

---

## Mês 6 — Sistemas Operacionais II

### Objetivo

Entender concorrência e comunicação entre processos.

### Conteúdos

* Concorrência
* Paralelismo
* Mutex
* Semáforos
* Race condition
* Deadlock
* Comunicação entre processos
* Pipes
* Signals
* Threads em C
* Gerenciamento de memória pelo SO
* Context switch

### Projetos

* Programa multithread em C
* Simulador simples de tarefas
* Mini shell em C
* Simular race condition e corrigir com mutex

### Perguntas para revisar

* O que é race condition?
* O que é deadlock?
* Como mutex resolve concorrência?
* Qual a diferença entre concorrência e paralelismo?
* O que é context switch?

---

## Mês 7 — Redes de Computadores I

### Objetivo

Entender comunicação entre máquinas.

### Conteúdos

* Modelo OSI
* TCP
* UDP
* IP
* Portas
* Sockets
* DNS
* HTTP por baixo
* Latência
* Perda de pacotes
* Handshake TCP
* Three-way handshake

### Projetos

* Cliente TCP em C
* Servidor TCP em C
* Cliente/servidor simples via socket
* Simular troca de mensagens entre cliente e servidor

### Perguntas para revisar

* Qual a diferença entre TCP e UDP?
* O que é uma porta?
* O que é socket?
* Como funciona o handshake TCP?
* O que acontece quando uma requisição HTTP é feita?

---

## Mês 8 — Redes de Computadores II

### Objetivo

Entender redes aplicadas a sistemas reais.

### Conteúdos

* TLS
* Handshake TLS
* Certificados
* Proxies
* Reverse Proxy
* Load Balancing
* Keep-alive
* Timeouts
* Retries
* Circuit Breaker
* Rate limit
* Diagnóstico com `ping`
* Diagnóstico com `curl`
* Diagnóstico com `telnet`
* Diagnóstico com `nc`
* Diagnóstico com `dig`
* Diagnóstico com `traceroute`

### Projetos

* Chat via socket em C
* Implementação de protocolo próprio simples
* Simular timeout
* Simular retry
* Criar documentação de diagnóstico de rede

### Perguntas para revisar

* O que é TLS?
* Qual a diferença entre proxy e reverse proxy?
* O que é load balancer?
* O que é timeout?
* Quando retry pode piorar um problema?

---

## Mês 9 — Árvores e Grafos

### Objetivo

Dominar estruturas usadas em banco, busca, compiladores, redes e entrevistas.

### Conteúdos

* Árvores
* Árvore binária
* Árvore binária de busca
* Heap
* Trie
* Grafos
* BFS
* DFS
* Caminho mínimo
* Hashing profundo

### Projetos

* Implementar árvore binária
* Implementar BFS
* Implementar DFS
* Implementar heap
* Criar mini biblioteca de algoritmos em C

### Perguntas para revisar

* O que é uma árvore?
* O que é uma árvore binária de busca?
* O que é heap?
* Qual a diferença entre BFS e DFS?
* Onde grafos aparecem em sistemas reais?

---

## Mês 10 — Algoritmos Intermediários

### Objetivo

Aprender padrões comuns de resolução de problemas.

### Conteúdos

* Two pointers
* Sliding window
* Prefix sum
* Recursão avançada
* Backtracking
* Greedy
* Programação dinâmica básica
* Ordenação topológica
* Union Find
* Grafos direcionados
* Detecção de ciclo

### Projetos

* Resolver 30 problemas de algoritmos
* Documentar cada padrão no README
* Criar exemplos em C, PHP ou JavaScript

### Perguntas para revisar

* Quando usar sliding window?
* O que é programação dinâmica?
* O que é backtracking?
* O que é Union Find?
* Como detectar ciclo em grafo?

---

## Mês 11 — Compiladores e Interpretadores I

### Objetivo

Entender como linguagens funcionam.

### Conteúdos

* Como linguagens funcionam
* Lexer
* Tokens
* Parser
* AST
* Expressões
* Gramática simples
* Análise léxica
* Análise sintática

### Projetos

* Parser simples de expressões matemáticas
* Interpretador de calculadora
* Tokenizador simples

### Perguntas para revisar

* O que é lexer?
* O que é parser?
* O que é AST?
* Como uma linguagem interpreta uma expressão?
* Qual a diferença entre erro léxico e erro sintático?

---

## Mês 12 — Compiladores e Interpretadores II

### Objetivo

Criar visão profunda sobre runtime e execução.

### Conteúdos

* Bytecode
* Máquina virtual
* Interpretadores
* Ambientes de execução
* Garbage collector, visão geral
* Runtime de linguagens
* Diferença entre compilado e interpretado
* JIT, visão geral
* Mini linguagem funcional

### Projetos

* Criar uma mini linguagem
* Criar interpretador básico
* Criar execução de expressões com variáveis
* Documentar como código vira execução

### Perguntas para revisar

* O que é bytecode?
* O que é uma máquina virtual?
* O que é runtime?
* O que é garbage collector?
* Qual a diferença entre linguagem compilada e interpretada?

---

# FASE 2 — BANCO DE DADOS E BACKEND FORTE

---

## Mês 13 — Fundamentos de Engenharia de Software

### Objetivo

Consolidar fundamentos usados no dia a dia profissional.

### Conteúdos

* HTTP
* REST
* Verbos HTTP
* Status Code
* Headers
* Git básico e intermediário
* Rebase
* Reflog
* Cherry-pick
* Expressões Regulares
* Linux básico
* Shell
* Processos
* Permissões
* 12 Fatores
* Contratos de API
* Versionamento de API

### Projeto

API HTTP Playground com contratos claros.

### Atividades

* Criar endpoints REST.
* Documentar status codes.
* Criar respostas de erro padronizadas.
* Criar README com exemplos de request e response.

---

## Mês 14 — Banco de Dados Relacional com PostgreSQL

### Objetivo

Elevar nível técnico em modelagem relacional.

### Conteúdos

* Modelagem relacional
* Normalização
* Desnormalização
* Entidades
* Relacionamentos
* Chaves primárias
* Chaves estrangeiras
* Constraints
* Índices básicos
* Transações
* Isolamento
* ACID
* Integridade referencial

### Projeto

Sistema financeiro com foco em queries performáticas.

### Atividades

* Criar modelagem.
* Criar migrations.
* Criar constraints.
* Criar queries de listagem, relatório e filtros.
* Documentar decisões de modelagem.

---

## Mês 15 — Performance em Banco de Dados I

### Objetivo

Entender gargalos reais de banco de dados.

### Conteúdos

* Query tuning
* Índices
* EXPLAIN
* EXPLAIN ANALYZE
* Cost
* Actual time
* Rows
* Loops
* Filter
* Index Cond
* Problema N+1
* Índices compostos
* Seletividade
* Ordenação
* Paginação
* Cursor pagination
* Evitar OFFSET alto

### Projeto

Laboratório de performance com tabelas grandes.

### Atividades

* Criar tabela com 100 mil registros.
* Rodar query sem índice.
* Criar índice.
* Rodar query com índice.
* Comparar Seq Scan e Index Scan.
* Documentar resultado.

---

## Mês 16 — Algoritmos de Scan e EXPLAIN

### Objetivo

Entender como o banco acessa dados internamente.

### Scans básicos

* Full Table Scan
* Sequential Scan / Seq Scan
* Table Scan
* Heap Scan

### Scans com índice

* Index Scan
* Index Seek
* Index Range Scan
* Index Only Scan
* Covering Index
* Unique Index Scan
* Primary Key Index Scan

### Bitmap Scans

* Bitmap Index Scan
* Bitmap Heap Scan
* BitmapAnd
* BitmapOr

### Scans com particionamento

* Partition Scan
* Partition Pruning
* Partition-wise Scan

### Scans paralelos

* Parallel Sequential Scan
* Parallel Index Scan
* Parallel Bitmap Heap Scan

### Scans relacionados a joins

* Nested Loop
* Index Nested Loop
* Hash Join
* Merge Join

### Scans especiais

* CTE Scan
* Subquery Scan
* Function Scan
* Materialized View Scan
* Foreign Table Scan

### Índices importantes

* B-Tree
* Hash Index
* Bitmap Index
* GIN
* GiST
* BRIN
* R-Tree
* Inverted Index

### Exercício prático — Query sem índice vs com índice

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(150),
    idade INT
);
```

```sql
INSERT INTO usuarios (nome, email, idade)
SELECT 
    'Usuario ' || generate_series,
    'usuario' || generate_series || '@email.com',
    (random() * 60)::int
FROM generate_series(1, 100000);
```

```sql
EXPLAIN ANALYZE
SELECT *
FROM usuarios
WHERE email = 'usuario50000@email.com';
```

Resultado esperado sem índice:

```txt
Seq Scan on usuarios
```

Criar índice:

```sql
CREATE INDEX idx_usuarios_email
ON usuarios(email);
```

Rodar novamente:

```sql
EXPLAIN ANALYZE
SELECT *
FROM usuarios
WHERE email = 'usuario50000@email.com';
```

Resultado esperado com índice:

```txt
Index Scan using idx_usuarios_email on usuarios
```

### O que observar no EXPLAIN ANALYZE

* Seq Scan
* Index Scan
* Bitmap Scan
* Cost
* Actual time
* Rows
* Loops
* Filter
* Index Cond
* Join type
* Sort
* Memory
* Buffers

---

## Mês 17 — Locks, Concorrência e Transações

### Objetivo

Entender problemas de produção envolvendo concorrência no banco.

### Conteúdos

* Locks
* Deadlocks
* Transações abertas
* Tempo de transação
* Ordem de updates
* Isolamento
* Read committed
* Repeatable read
* Serializable
* Concorrência
* Réplicas leitura/escrita
* Instância para leitura e outra para escrita
* Cache de consultas
* Consistência dos dados
* Retry controlado para deadlock

### Projeto

Simulador de concorrência em banco.

### Atividades

* Simular deadlock.
* Simular lock por transação aberta.
* Investigar queries bloqueadas.
* Criar documentação de investigação de lock.

---

## Mês 18 — Recursos Avançados de Banco

### Objetivo

Conhecer recursos avançados sem usar de forma irresponsável.

### Conteúdos

* Functions
* Procedures
* Triggers
* Uso responsável de triggers
* PL/SQL
* PL/pgSQL
* Materialized Views
* Views
* Auditoria
* Histórico
* Particionamento
* Arquivamento de dados
* Query para relatórios
* Dados agregados

### Projeto

Auditoria e histórico com foco em performance.

### Atividades

* Criar tabela de auditoria.
* Criar trigger controlada.
* Criar materialized view.
* Criar relatório rápido baseado em dados agregados.

---

## Mês 19 — Redis, Cache e Concorrência

### Objetivo

Usar cache com critério profissional.

### Conteúdos

* Redis
* TTL
* Cache key
* Cache por tenant
* Cache hit/miss
* Locks distribuídos
* Estratégias de cache
* Cache Aside
* Write-through
* Write-behind
* Cache stampede
* TTL com jitter
* Stale-while-revalidate
* SQL vs NoSQL
* MongoDB visão prática

### Projeto

Camada de cache com métricas de hit/miss.

### Atividades

* Criar cache por tenant.
* Criar invalidação por evento.
* Criar métrica de hit/miss.
* Simular cache stampede e resolver com lock.

---

## Mês 20 — PHP Assíncrono, Swoole e Hyperf

### Objetivo

Entender PHP em cenários assíncronos e de alta concorrência.

### Conteúdos

* Programação assíncrona com PHP
* Introdução ao Swoole
* Corrotinas
* Workers
* Servidor HTTP com Swoole
* Hyperf Framework
* Arquitetura assíncrona em PHP
* Dependency Injection no Hyperf
* Middlewares
* Servidores async
* Diferença entre PHP-FPM e Swoole
* Cuidados com estado em memória

### Projeto

API simples com Swoole ou Hyperf.

### Atividades

* Criar endpoint HTTP.
* Criar worker.
* Comparar com Laravel tradicional.
* Documentar diferenças de runtime.

---

# FASE 3 — CÓDIGO, ARQUITETURA E PRODUÇÃO

---

## Mês 21 — Engenharia de Código

### Objetivo

Elevar qualidade do código para nível profissional.

### Conteúdos

* SOLID na prática
* Refatoração segura
* Clean Code
* Code Smells
* Design de APIs RESTful
* Boas práticas de OO
* Code Review eficaz
* Separação de responsabilidades
* Controller fino
* Service Layer
* DTO
* FormRequest
* Repository
* Query Object
* Application Layer
* Domain Layer
* Infrastructure Layer

### Projeto

Refatorar sistema legado aplicando todos os conceitos.

### Atividades

* Remover regra de controller.
* Criar service.
* Criar DTO.
* Criar Query Object.
* Criar testes antes da refatoração.
* Documentar antes/depois.

---

## Mês 22 — Testes Profissionais

### Objetivo

Conseguir evoluir sistemas com segurança.

### Conteúdos

* Testes unitários
* TDD consciente
* Testes de integração
* Testes de feature
* Testabilidade independente de framework
* Mutation Testing
* Mocks
* Fakes
* Stubs
* Testes de contrato
* Cobertura útil
* Testes em português
* Testes de regra de negócio
* Testes de regressão

### Projeto

API com cobertura robusta.

### Atividades

* Criar testes unitários.
* Criar testes de integração.
* Criar testes de regra de negócio.
* Criar testes para casos de erro.
* Criar testes para evitar acesso indevido entre tenants.

---

## Mês 23 — Arquitetura de Software I

### Objetivo

Organizar sistemas grandes com clareza.

### Conteúdos

* Monólito Modular
* Escala vertical vs horizontal
* DDD na prática
* Bounded Context
* Entidade
* Value Object
* Aggregate
* Domain Service
* Repository
* Eventos de domínio
* ADRs
* Separação por módulos
* Context Map

### Projeto

Monólito modular com ADRs e padrões aplicados.

### Atividades

* Separar módulos por domínio.
* Criar ADRs.
* Criar evento de domínio.
* Criar Value Object.
* Criar Aggregate simples.

---

## Mês 24 — Arquitetura de Software II

### Objetivo

Dominar estilos arquiteturais usados em sistemas profissionais.

### Conteúdos

* Arquitetura Hexagonal
* Clean Architecture
* Onion Architecture
* Clean x Onion x Hexagonal
* Ports and Adapters
* Camada de aplicação
* Camada de domínio
* Camada de infraestrutura
* Casos de uso
* Interfaces
* Adapters
* Separação framework x regra de negócio

### Projeto

Reestruturar um módulo usando arquitetura hexagonal.

### Atividades

* Criar caso de uso isolado.
* Criar porta de repositório.
* Criar adapter de banco.
* Criar adapter HTTP.
* Testar regra sem framework.

---

## Mês 25 — Design Patterns

### Objetivo

Aplicar padrões de projeto em problemas reais.

### Criacionais

* Factory
* Abstract Factory
* Builder
* Singleton
* Prototype

### Estruturais

* Adapter
* Decorator
* Facade
* Proxy
* Composite

### Comportamentais

* Strategy
* Observer
* Command
* Chain of Responsibility
* State
* Template Method

### Projeto

Aplicar padrões em regras de negócio reais.

### Atividades

* Aplicar Strategy em cálculo.
* Aplicar Observer em eventos.
* Aplicar Adapter em API externa.
* Aplicar Factory na criação de objetos.
* Aplicar Chain of Responsibility em validações.

---

## Mês 26 — Sistemas Distribuídos e Mensageria

### Objetivo

Entender sistemas assíncronos resilientes.

### Conteúdos

* Microserviços
* RabbitMQ
* Event-Driven Architecture
* CQRS
* Sagas
* Idempotência
* Consumers
* Producers
* Ack
* Nack
* Retry
* Dead Letter Queue
* Reprocessamento
* Prefetch
* Filas por prioridade
* Contrato de mensagem
* Mensagem duplicada
* Falha parcial

### Projeto

Sistema assíncrono resiliente.

### Atividades

* API publica mensagem.
* Consumer processa mensagem.
* Criar DLQ.
* Criar retry.
* Criar idempotência.
* Criar tela/status de processamento.

---

## Mês 27 — DevOps Essencial

### Objetivo

Ter autonomia para colocar sistemas no ar.

### Conteúdos

* Docker
* Docker Compose
* Imagens
* Containers
* Volumes
* Networks
* Healthcheck
* CI/CD
* GitHub Actions
* Self-hosted runner
* Secrets
* Build
* Deploy
* Rollback
* Ambientes
* Logs de deploy
* Pipeline segura

### Projeto

Pipeline completa.

### Atividades

* Rodar testes.
* Buildar imagem.
* Fazer push para registry.
* Fazer deploy.
* Rodar health check.
* Criar rollback.
* Proteger secrets.

---

## Mês 28 — Cloud e Kubernetes

### Objetivo

Entender infraestrutura moderna.

### Conteúdos

* AWS básico
* EC2
* Security Groups
* IAM
* S3
* RDS
* ECR
* Kubernetes
* Pods
* Services
* Deployments
* ConfigMaps
* Secrets
* Ingress
* Autoscaling
* HPA
* Requests e limits
* Rolling update

### Projeto

Aplicação simples em cloud e Kubernetes.

### Atividades

* Subir aplicação simples na AWS.
* Criar imagem Docker.
* Criar deployment Kubernetes.
* Criar service.
* Criar ConfigMap e Secret.
* Documentar arquitetura.

---

## Mês 29 — Observabilidade e Resiliência

### Objetivo

Saber investigar produção com método.

### Conteúdos

* Logs centralizados
* Logs estruturados
* Request ID
* Tenant ID
* User ID
* Métricas
* Tracing
* Prometheus
* Grafana
* SLO
* SLA
* SLI
* Circuit Breaker
* Backpressure
* Health check
* Alertas
* Runbooks
* Debug de produção

### Projeto

Observabilidade em API real.

### Atividades

* Adicionar logs estruturados.
* Adicionar request ID.
* Criar métricas.
* Criar dashboard Grafana.
* Criar alerta de erro.
* Criar alerta de fila acumulada.

---

## Mês 30 — Segurança

### Objetivo

Construir sistemas com menos risco.

### Conteúdos

* OWASP Top 10
* SQL Injection
* XSS
* CSRF
* SSRF
* Autenticação
* Autorização
* Roles
* Permissions
* Policies
* MFA
* Rate limit
* Threat Modeling
* Upload seguro
* Secrets em CI/CD
* Segurança em multitenancy
* Auditoria
* Proteção de rotas administrativas
* Validação de entrada

### Projeto

Revisão de segurança de uma API.

### Atividades

* Criar checklist OWASP.
* Criar testes de autorização.
* Criar teste contra vazamento entre tenants.
* Criar upload seguro.
* Criar rate limit.
* Revisar secrets.

---

# FASE 4 — ESPECIALIZAÇÃO, SYSTEM DESIGN E PROJETO FINAL

---

## Mês 31 — Frontend Base Necessária

### Objetivo

Ter base suficiente para construir dashboards e conversar tecnicamente com frontend.

### Conteúdos

* JavaScript moderno
* TypeScript
* HTML semântico
* CSS
* Responsividade
* Consumo de APIs
* Autenticação no frontend
* Tratamento de erro
* Estados de loading
* Paginação
* Filtros
* Integração com backend
* Build com Vite
* Noções de React/Vue

### Projeto

Dashboard simples integrado ao backend.

### Atividades

* Login.
* Listagem.
* Filtros.
* Paginação.
* Tratamento de erro.
* Loading.
* Consumo de API real.

---

## Mês 32 — System Design

### Objetivo

Aprender a desenhar sistemas reais.

### Conteúdos

* Desenho de sistemas reais
* Trade-offs de escala
* Escala vertical
* Escala horizontal
* Load balancer
* Cache
* Banco relacional
* NoSQL
* Filas
* CDN
* Rate limit
* Alta disponibilidade
* Escalabilidade
* Consistência
* Disponibilidade
* Particionamento
* Sharding
* Replicação
* Failover
* Multi-tenant system design

### Projetos

* Desenhar arquitetura de SaaS multitenant.
* Desenhar módulo de relatórios pesados.
* Desenhar chat/suporte em tempo real.
* Desenhar sistema de notificações.
* Desenhar sistema de auditoria.

---

## Mês 33 — Performance Avançada

### Objetivo

Investigar gargalos reais de aplicação.

### Conteúdos

* Profiling
* Load testing
* Stress testing
* Tuning de aplicações
* Gargalo de CPU
* Gargalo de memória
* Gargalo de rede
* Gargalo de banco
* Otimização de payload
* Otimização de serialização
* Testes de carga
* Pool de conexões
* Connection leak
* Memory leak
* Throughput
* Latência p50, p95, p99

### Projeto

Teste de carga com antes/depois.

### Atividades

* Criar teste de carga.
* Identificar gargalo.
* Otimizar query.
* Aplicar cache.
* Reduzir payload.
* Comparar antes/depois.

---

## Mês 34 — Arquitetura Avançada e Dados

### Objetivo

Entender padrões usados em sistemas maiores.

### Conteúdos

* Event Sourcing
* Consistência eventual
* Kafka
* Streams
* Processamento assíncrono avançado
* Mensageria avançada
* Projeções de leitura
* Reprocessamento
* Auditoria baseada em eventos
* Outbox Pattern
* Inbox Pattern
* CDC
* Data pipelines

### Projeto

Fluxo de eventos com projeção de leitura.

### Atividades

* Criar evento de domínio.
* Publicar evento.
* Criar projeção.
* Simular reprocessamento.
* Criar auditoria baseada em eventos.

---

## Mês 35 — SRE, Liderança Técnica e Segurança Avançada

### Objetivo

Subir maturidade profissional.

### Conteúdos

* SRE
* Runbooks
* On-call
* Incidentes
* Postmortem
* Chaos Engineering
* Backpressure
* Code review avançado
* Mentoria
* Liderança técnica
* Criptografia
* OAuth profundo
* OpenID Connect
* Decisões técnicas orientadas a negócio
* Comunicação técnica
* Documentação de decisões

### Projeto

Kit de operação profissional.

### Atividades

* Criar runbook.
* Criar postmortem simulado.
* Criar checklist de code review.
* Criar modelo de decisão técnica.
* Criar plano de resposta a incidente.

---

## Mês 36 — Projeto Final Master

### Objetivo

Consolidar tudo em um projeto forte de portfólio e entrevista.

### Projeto final

Construir um sistema completo com:

* Arquitetura evolutiva
* Backend escalável
* Banco bem modelado
* Índices documentados
* EXPLAIN ANALYZE documentado
* Cache Redis
* Fila RabbitMQ
* DLQ
* Retry
* Idempotência
* Observabilidade
* Logs estruturados
* Métricas
* CI/CD
* Docker
* Segurança aplicada
* Testes automatizados
* Documentação profissional
* ADRs
* Runbooks
* Dashboard simples no frontend
* Health check
* Rollback
* Auditoria

### Sugestões de projeto

* SaaS multitenant de clínicas
* Sistema de relatórios assíncronos
* Plataforma de suporte em tempo real
* Sistema financeiro com auditoria
* API de gestão com filas, cache, métricas e deploy automatizado

---

# 7. Certificações Sugeridas

As certificações não devem ser prioridade no começo.

Primeiro vem base.

Depois considerar:

* Linux Foundation LFS101
* Docker Certified Associate
* CKAD Kubernetes
* AWS Solutions Architect
* iSAQB Foundation

---

# 8. Documentação Contínua

Criar uma pasta de estudos:

```bash
estudos-engenharia-software/
```

Estrutura sugerida:

```bash
estudos-engenharia-software/
├── 01-c-baixo-nivel/
├── 02-estruturas-dados/
├── 03-algoritmos/
├── 04-sistemas-operacionais/
├── 05-redes/
├── 06-banco-dados/
├── 07-backend/
├── 08-cache-redis/
├── 09-filas-rabbitmq/
├── 10-arquitetura/
├── 11-testes/
├── 12-devops/
├── 13-seguranca/
├── 14-system-design/
├── 15-entrevistas/
└── projeto-final-master/
```

---

# 9. Modelo de Anotação por Tema

Para cada assunto estudado, documentar assim:

```markdown
# Tema estudado

## O que é

## Para que serve

## Onde aparece em sistemas reais

## Exemplo prático

## Problemas comuns

## Como investigar

## Como resolver

## Perguntas de entrevista

## Resumo final
```

---

# 10. Banco de Perguntas Técnicas de Entrevista

Este banco junta perguntas clássicas de pleno/sênior com perguntas novas baseadas na trilha de estudos.

A ideia é treinar cada resposta no formato:

```markdown
## Pergunta

## Resposta simulada

## Exemplo real ou parecido que eu vivi

## Como eu investigaria

## Possíveis causas

## Como eu resolveria

## Como eu evitaria acontecer novamente
```

---

# 11. Perguntas Técnicas — Arquitetura de Sistemas

## 1. Como você desenharia a arquitetura de um SaaS multitenant?

Pontos para responder:

* Tenant por clínica/empresa.
* Usuários vinculados ao tenant.
* Middleware para resolver tenant.
* Isolamento por `tenant_id`, schema ou banco por tenant.
* Policies/guards.
* Auditoria.
* Testes para impedir vazamento entre tenants.

---

## 2. Banco único ou banco por tenant: quando usar cada abordagem?

Pontos para responder:

* Banco único é mais simples e barato.
* Banco por tenant dá mais isolamento.
* Banco por tenant facilita backup/restauração individual.
* Banco único exige cuidado com escopo por tenant.
* Decisão depende de custo, compliance, volume e operação.

---

## 3. Como você separaria responsabilidades em uma aplicação grande?

Pontos para responder:

* Controller fino.
* Service/Application Layer.
* Repository/Query Object.
* DTO/FormRequest.
* Domain para regra de negócio.
* Jobs para tarefas assíncronas.
* Events para desacoplamento.

---

## 4. Quando você criaria um microserviço?

Pontos para responder:

* Domínio bem separado.
* Necessidade de escalar de forma independente.
* Deploy independente.
* Times diferentes.
* Carga muito diferente do restante do sistema.
* Evitar microserviço por moda.
* Considerar custo operacional e observabilidade.

---

## 5. Como você projetaria um módulo de relatórios pesados?

Pontos para responder:

* Relatório assíncrono.
* Fila.
* Status de processamento.
* Cache do resultado.
* Exportação para arquivo.
* Tabelas agregadas/materialized views.
* Evitar travar banco transacional.
* Notificação quando finalizar.

---

# 12. Perguntas Técnicas — Performance e Gargalos

## 6. Um endpoint está lento em produção. Como você investiga?

Pontos para responder:

* Ver logs.
* Ver tempo de resposta.
* Identificar queries lentas.
* Medir CPU, RAM, disco e rede.
* Ver chamadas externas.
* Usar APM/tracing.
* Verificar N+1.
* Verificar locks.
* Reproduzir com carga controlada.

---

## 7. Um endpoint recebe muitas requisições e começa a gargalar. Como resolver?

Pontos para responder:

* Paginação.
* Cache.
* Rate limit.
* Otimização de query.
* Índices.
* Processamento assíncrono.
* Redução de payload.
* Escalar horizontalmente se necessário.
* Separar leitura e escrita em casos avançados.

---

## 8. Como você identifica e resolve problema de N+1 queries?

Pontos para responder:

* Analisar logs SQL.
* Usar profiler/debugbar/APM.
* Identificar queries repetidas dentro de loop.
* Usar eager loading.
* Revisar relacionamentos do ORM.
* Criar testes/métricas para evitar regressão.

---

## 9. Como você otimiza uma listagem com milhões de registros?

Pontos para responder:

* Paginação real.
* Cursor pagination.
* Índices corretos.
* Filtros obrigatórios.
* Evitar OFFSET alto.
* Reduzir colunas retornadas.
* Cache quando fizer sentido.
* Busca assíncrona para casos pesados.

---

## 10. Como você reduziria o tempo de resposta de uma API crítica?

Pontos para responder:

* Medir antes de otimizar.
* Identificar gargalo principal.
* Melhorar query.
* Aplicar cache.
* Remover processamento desnecessário.
* Usar fila para tarefas demoradas.
* Reduzir payload.
* Melhorar infraestrutura se necessário.

---

# 13. Perguntas Técnicas — Banco de Dados

## 11. Como você investiga uma query lenta?

Pontos para responder:

* Usar EXPLAIN ou EXPLAIN ANALYZE.
* Ver tipo de scan.
* Ver linhas estimadas versus reais.
* Ver joins.
* Ver ordenação.
* Ver filtros.
* Ver índices existentes.
* Ver locks e transações abertas.

---

## 12. O que você observa em um EXPLAIN ANALYZE?

Pontos para responder:

* Tempo real.
* Custo estimado.
* Linhas estimadas.
* Linhas reais.
* Tipo de scan.
* Uso de índice.
* Nested Loop, Hash Join ou Merge Join.
* Sort em memória ou disco.
* Gargalo principal do plano.

---

## 13. Quando um índice ajuda e quando atrapalha?

Ajuda quando:

* Existem filtros frequentes.
* Existem joins.
* Existe ordenação.
* Campo possui boa seletividade.

Atrapalha quando:

* Há escrita muito intensa.
* Existem índices duplicados.
* A coluna tem baixa seletividade.
* A tabela é pequena.
* O índice nunca é usado.

---

## 14. Como escolher a ordem das colunas em um índice composto?

Pontos para responder:

* Colunas de igualdade primeiro.
* Colunas mais seletivas primeiro, dependendo do caso.
* Depois colunas de range.
* Considerar ORDER BY.
* Considerar padrão real das queries.
* Não criar índice no chute.

---

## 15. Como você investigaria deadlock ou lock no banco?

Pontos para responder:

* Ver transações abertas.
* Ver queries bloqueadas.
* Ver ordem dos updates.
* Ver tempo de transação.
* Ver índices ausentes.
* Reduzir escopo da transação.
* Padronizar ordem de atualização.
* Criar retry controlado.

---

# 14. Perguntas Técnicas — Cache e Redis

## 16. Quando você usaria cache em uma aplicação?

Pontos para responder:

* Dados lidos com frequência.
* Dados que mudam pouco.
* Queries caras.
* Configurações.
* Relatórios.
* Listagens públicas.
* Cuidado com dados sensíveis e tenant.

---

## 17. Como você evita cache desatualizado?

Pontos para responder:

* TTL.
* Invalidação por evento.
* Cache key bem definida.
* Cache por tenant/usuário/filtro.
* Atualizar cache após escrita.
* Cache tags, se disponível.
* Monitorar hit/miss.

---

## 18. O que é cache stampede e como resolver?

Pontos para responder:

* Muitos requests tentando recriar o mesmo cache ao mesmo tempo.
* Usar lock.
* TTL com jitter.
* Stale-while-revalidate.
* Pré-aquecimento.
* Limitar concorrência.

---

## 19. Como você definiria uma chave de cache segura em sistema multitenant?

Pontos para responder:

* Incluir `tenant_id`.
* Incluir filtros relevantes.
* Incluir versão da regra.
* Evitar misturar dados entre clientes.
* Não colocar dados sensíveis desnecessários na chave.

---

## 20. Quando você não usaria cache?

Pontos para responder:

* Dados mudam o tempo todo.
* Dados altamente sensíveis.
* Invalidação muito complexa.
* Query já é rápida.
* Cache pode gerar inconsistência crítica.

---

# 15. Perguntas Técnicas — Filas, Consumers e Mensageria

## 21. Um consumer quebrou ao processar uma mensagem. Como você investiga?

Pontos para responder:

* Ver logs.
* Ver payload.
* Ver stack trace.
* Ver tentativas/retries.
* Ver DLQ.
* Ver se o erro é regra, infraestrutura ou contrato.
* Reprocessar com segurança.
* Criar correção para evitar loop infinito.

---

## 22. Como evitar que uma mensagem seja processada duas vezes?

Pontos para responder:

* Idempotência.
* Chave única.
* Controle por message_id ou chave de negócio.
* Transação.
* Lock quando necessário.
* Ack somente após sucesso.
* Garantia contra duplicidade no banco.

---

## 23. O que fazer com mensagens que falham várias vezes?

Pontos para responder:

* Enviar para dead letter.
* Guardar payload e erro.
* Alertar o time.
* Classificar erro temporário ou definitivo.
* Corrigir causa raiz.
* Reprocessar com comando controlado.

---

## 24. Como dimensionar consumers para alto volume?

Pontos para responder:

* Medir taxa de entrada.
* Medir taxa de consumo.
* Aumentar workers.
* Ajustar prefetch.
* Separar filas por prioridade.
* Otimizar processamento.
* Ver gargalo no banco ou API externa.

---

## 25. Qual a diferença entre ack, nack e retry?

Pontos para responder:

* Ack: mensagem processada com sucesso.
* Nack: falha, podendo reenfileirar ou descartar.
* Retry: nova tentativa controlada.
* Cuidado com loop infinito.
* Após limite de tentativas, enviar para DLQ.

---

# 16. Perguntas Técnicas — Segurança

## 26. Como proteger uma API contra SQL Injection?

Pontos para responder:

* Prepared statements.
* Query builder/ORM com bindings.
* Nunca concatenar input diretamente no SQL.
* Validação de entrada.
* Permissão mínima no banco.
* Logs e WAF como camada adicional.

---

## 27. Como proteger rotas administrativas?

Pontos para responder:

* Autenticação.
* Autorização.
* Roles/permissions.
* Policies/gates.
* MFA se necessário.
* Rate limit.
* Auditoria.
* Nunca confiar apenas no frontend.

---

## 28. Como evitar vazamento de dados entre tenants?

Pontos para responder:

* Middleware de tenant.
* Escopo obrigatório por tenant_id.
* Policies.
* Testes automatizados.
* Índices com tenant.
* Não confiar em IDs enviados pelo frontend.
* Auditoria.

---

## 29. Como tratar upload de arquivos com segurança?

Pontos para responder:

* Validar tamanho.
* Validar MIME real.
* Não confiar apenas na extensão.
* Renomear arquivo.
* Armazenar fora da pasta pública direta.
* Controlar permissão de download.
* Usar antivírus quando necessário.
* Impedir execução de arquivo enviado.

---

## 30. Como você lida com secrets em CI/CD?

Pontos para responder:

* GitHub Secrets.
* Nunca commitar `.env`.
* Rotacionar token vazado.
* Escopo mínimo.
* Secrets por ambiente.
* Não imprimir secrets em logs.
* Revisar permissões do runner.

---

# 17. Perguntas Técnicas — SOLID, DDD e Código Limpo

## 31. Como você aplicaria SOLID em um módulo real?

Pontos para responder:

* Classe com responsabilidade única.
* Interfaces para dependências.
* Evitar acoplamento forte.
* Injeção de dependência.
* Separar regra de negócio de infraestrutura.
* Facilitar testes.

---

## 32. O que é Single Responsibility Principle na prática?

Pontos para responder:

* Uma classe deve ter um motivo principal para mudar.
* Controller não deve conter regra complexa.
* Service não deve misturar persistência, HTTP e regra.
* Separar validação, regra, persistência e resposta.

---

## 33. Como você identificaria violação de Open/Closed Principle?

Pontos para responder:

* Muitos if/else para tipos diferentes.
* Toda nova regra exige alterar classe central.
* Resolver com Strategy, polimorfismo, handler ou rule object.
* Código aberto para extensão e fechado para modificação.

---

## 34. Como você modelaria uma regra de negócio usando DDD?

Pontos para responder:

* Entidade com identidade.
* Value Object para valores imutáveis.
* Aggregate para consistência.
* Domain Service quando regra não pertence a uma entidade.
* Repository como abstração de persistência.
* Eventos de domínio quando algo importante acontece.

---

## 35. Qual a diferença entre Entity e Value Object?

Entity:

* Tem identidade.
* Pode mudar estado.
* Exemplo: Usuário, Clínica, Pedido.

Value Object:

* Não tem identidade própria.
* É comparado por valor.
* Deve ser imutável.
* Exemplo: Dinheiro, CPF, Email, Endereço.

---

# 18. Perguntas Técnicas — Padrões de Projeto

## 36. Quando você usaria Strategy?

Pontos para responder:

* Quando há várias formas de executar uma regra.
* Para evitar if/else grande.
* Exemplo: cálculo por tipo de operação.
* Facilita extensão e teste.

---

## 37. Quando você usaria Factory?

Pontos para responder:

* Quando criação de objetos é complexa.
* Quando tipo criado depende de regra.
* Evita espalhar `new`.
* Centraliza criação.
* Facilita troca de implementação.

---

## 38. Quando você usaria Observer/Event?

Pontos para responder:

* Quando uma ação gera efeitos colaterais.
* Exemplo: pedido criado → enviar email, auditar, notificar.
* Desacopla regra principal.
* Cuidado para não esconder fluxo crítico demais.

---

## 39. Quando você usaria Repository?

Pontos para responder:

* Para abstrair acesso a dados.
* Separar domínio da persistência.
* Centralizar queries.
* Facilitar testes.
* Cuidado para não criar repository genérico inútil.

---

## 40. Quando você usaria Adapter?

Pontos para responder:

* Integração com API externa.
* Gateway de pagamento.
* Serviço de e-mail.
* Storage.
* Permite trocar fornecedor sem afetar domínio.
* Traduz contrato externo para contrato interno.

---

# 19. Perguntas Técnicas — Observabilidade e Produção

## 41. Como investigar um erro que só acontece em produção?

Pontos para responder:

* Logs centralizados.
* Request ID.
* Payload sanitizado.
* Tracing.
* Métricas.
* Ver deploy recente.
* Comparar staging/prod.
* Criar teste reproduzindo erro.

---

## 42. Quais métricas você acompanha em uma API?

Pontos para responder:

* Latência p50, p95, p99.
* Taxa de erro.
* Throughput.
* CPU.
* Memória.
* Tempo de query.
* Conexões com banco.
* Tamanho de fila.
* Tempo de jobs.

---

## 43. O que fazer se aplicação começa a retornar 500 após deploy?

Pontos para responder:

* Ver logs.
* Ver alteração recente.
* Ver migrations.
* Ver `.env`.
* Ver cache de config.
* Ver permissões.
* Fazer rollback se impacto for alto.
* Corrigir com hotfix.

---

## 44. Como você usaria logs de auditoria para investigar um bug?

Pontos para responder:

* Buscar por tenant.
* Buscar por usuário.
* Buscar por request ID.
* Ver antes/depois.
* Ver payload sanitizado.
* Ver ordem dos eventos.
* Cruzar com logs da aplicação.

---

## 45. Como você estruturaria logs para facilitar debug?

Pontos para responder:

* Request ID.
* Tenant ID.
* User ID.
* Rota.
* Status.
* Tempo de execução.
* Erro/stack trace.
* Payload sanitizado.
* Nunca logar senha/token.

---

# 20. Perguntas Técnicas — CI/CD, Docker e Infraestrutura

## 46. Como montar uma pipeline segura?

Pontos para responder:

* Checkout.
* Instalar dependências.
* Lint.
* Testes.
* Build.
* Análise de segurança.
* Deploy com aprovação.
* Secrets protegidos.
* Rollback.

---

## 47. Qual o risco de usar self-hosted runner?

Pontos para responder:

* Workflow executa comandos na sua máquina.
* PR malicioso pode comprometer ambiente.
* Secrets podem vazar.
* Runner pode acessar rede interna.
* Usar apenas em repositórios confiáveis.
* Rodar com usuário sem privilégios.
* Isolar por VM/container.

---

## 48. Como investigar um container que reinicia sozinho?

Pontos para responder:

* `docker logs`
* `docker inspect`
* Exit code
* Healthcheck
* Variáveis de ambiente
* Permissões de volume
* Falta de memória
* Serviço dependente indisponível

---

## 49. Como fazer deploy com menor risco de downtime?

Pontos para responder:

* Build antes de trocar versão.
* Symlink para release atual.
* Health check.
* Reload controlado.
* Migrations compatíveis.
* Rollback rápido.
* Blue-green ou rolling deploy.
* Monitorar após deploy.

---

## 50. Como lidar com migrations perigosas em produção?

Pontos para responder:

* Evitar mudanças destrutivas diretas.
* Criar coluna nova antes de remover antiga.
* Backfill em lotes.
* Índice criado com cuidado.
* Deploy em etapas.
* Testar tempo de execução.
* Ter plano de rollback.

---

# 21. Novas Perguntas Técnicas — Baixo Nível, C e Memória

## 51. O que acontece quando um programa em C é compilado?

Pontos para responder:

* Pré-processamento.
* Compilação.
* Assembly.
* Linkedição.
* Geração do executável.
* Diferença entre código-fonte, objeto e binário.

---

## 52. Qual a diferença entre Stack e Heap?

Pontos para responder:

* Stack é usada para chamadas de função e variáveis locais.
* Heap é usada para alocação dinâmica.
* Stack é gerenciada automaticamente.
* Heap exige alocação e liberação manual em C.
* Erros comuns: memory leak, dangling pointer e double free.

---

## 53. O que é um ponteiro e por que ele é importante?

Pontos para responder:

* Ponteiro guarda endereço de memória.
* Permite manipular dados diretamente.
* Usado em arrays, strings, estruturas dinâmicas e passagem por referência.
* Pode gerar bugs graves se usado errado.

---

## 54. O que é memory leak e como investigar?

Pontos para responder:

* Memória alocada e não liberada.
* Em C, ocorre quando não chama `free`.
* Investigar com Valgrind.
* Observar crescimento de memória.
* Corrigir liberando recursos no fluxo correto.

---

## 55. O que é buffer overflow?

Pontos para responder:

* Escrita além do espaço reservado.
* Pode corromper memória.
* Pode gerar falha de segurança.
* Evitar validando tamanho e usando funções seguras.

---

## 56. O que é dangling pointer?

Pontos para responder:

* Ponteiro apontando para memória já liberada.
* Pode causar comportamento indefinido.
* Evitar setando ponteiro para NULL após free.
* Controlar ownership da memória.

---

## 57. O que é double free?

Pontos para responder:

* Liberar a mesma região de memória duas vezes.
* Pode corromper heap.
* Pode causar crash.
* Evitar definindo claramente quem é dono da memória.

---

## 58. Como uma string funciona em C?

Pontos para responder:

* Array de char.
* Terminada com `\0`.
* Tamanho precisa ser controlado.
* Risco de buffer overflow.
* Diferença entre string em C e string em linguagens de alto nível.

---

## 59. Como você implementaria uma lista ligada?

Pontos para responder:

* Struct Node.
* Campo de valor.
* Ponteiro para próximo nó.
* Inserção.
* Remoção.
* Percurso.
* Liberação da memória.

---

## 60. Como você implementaria um HashMap simples?

Pontos para responder:

* Array de buckets.
* Função hash.
* Tratamento de colisões.
* Encadeamento ou endereçamento aberto.
* Resize quando fator de carga cresce.

---

# 22. Novas Perguntas Técnicas — Algoritmos e Estruturas de Dados

## 61. Como explicar Big O de forma prática?

Pontos para responder:

* Mede crescimento do custo conforme entrada aumenta.
* O(1), O(log n), O(n), O(n log n), O(n²).
* Ajuda a prever gargalos.
* Não substitui medição real, mas orienta decisões.

---

## 62. Quando usar array e quando usar lista ligada?

Pontos para responder:

* Array tem acesso rápido por índice.
* Lista ligada facilita inserção/remoção em alguns cenários.
* Array costuma ter melhor localidade de cache.
* Lista ligada tem overhead de ponteiros.
* Escolha depende do padrão de acesso.

---

## 63. Como funciona busca binária?

Pontos para responder:

* Requer dados ordenados.
* Divide espaço de busca pela metade.
* Complexidade O(log n).
* Muito usada em índices e problemas de busca.

---

## 64. Qual a diferença entre BFS e DFS?

Pontos para responder:

* BFS explora por nível.
* DFS explora profundidade.
* BFS usa fila.
* DFS usa pilha ou recursão.
* BFS útil para menor caminho em grafo não ponderado.
* DFS útil para exploração, ciclo e componentes.

---

## 65. O que é uma árvore binária de busca?

Pontos para responder:

* Cada nó tem até dois filhos.
* Menores à esquerda, maiores à direita.
* Busca média O(log n) se balanceada.
* Pode degradar para O(n) se desbalanceada.

---

## 66. O que é heap e onde ele é usado?

Pontos para responder:

* Estrutura de prioridade.
* Min-heap ou max-heap.
* Usado em priority queue.
* Usado em algoritmos como Dijkstra.
* Inserção e remoção O(log n).

---

## 67. O que é tabela hash e por que acesso pode ser O(1)?

Pontos para responder:

* Usa função hash para calcular posição.
* Em média acesso é O(1).
* Colisões podem piorar desempenho.
* Requer boa função hash e controle de load factor.

---

## 68. O que é programação dinâmica?

Pontos para responder:

* Técnica para problemas com subproblemas repetidos.
* Usa memoization ou tabulação.
* Evita recalcular resultados.
* Exemplo: Fibonacci otimizado.

---

## 69. Quando usar sliding window?

Pontos para responder:

* Problemas com subarrays ou substrings.
* Janela fixa ou variável.
* Reduz complexidade de O(n²) para O(n) em muitos casos.

---

## 70. O que é Union Find?

Pontos para responder:

* Estrutura para conjuntos disjuntos.
* Operações find e union.
* Usado em conectividade, grafos e Kruskal.
* Otimizações: path compression e union by rank.

---

# 23. Novas Perguntas Técnicas — Sistemas Operacionais

## 71. O que é um processo?

Pontos para responder:

* Programa em execução.
* Possui espaço de memória próprio.
* Tem PID.
* Gerenciado pelo sistema operacional.

---

## 72. O que é uma thread?

Pontos para responder:

* Unidade de execução dentro de um processo.
* Threads compartilham memória do processo.
* Permitem concorrência.
* Exigem cuidado com sincronização.

---

## 73. Qual a diferença entre processo e thread?

Pontos para responder:

* Processo tem memória isolada.
* Thread compartilha memória.
* Processo é mais pesado.
* Thread é mais leve, mas mais perigosa para concorrência.

---

## 74. O que é race condition?

Pontos para responder:

* Resultado depende da ordem de execução.
* Ocorre com acesso concorrente a recurso compartilhado.
* Resolver com mutex, semáforo, lock ou design sem estado compartilhado.

---

## 75. O que é deadlock?

Pontos para responder:

* Dois ou mais processos/threads ficam esperando recursos entre si.
* Condições: exclusão mútua, hold and wait, não preempção e espera circular.
* Resolver padronizando ordem de locks, timeout e redução de escopo.

---

## 76. O que é context switch?

Pontos para responder:

* Troca de execução entre processos/threads.
* Tem custo.
* Muitos context switches podem degradar performance.

---

## 77. O que é uma syscall?

Pontos para responder:

* Chamada do programa para o kernel.
* Usada para I/O, arquivos, rede, processos.
* Exemplo: read, write, open, close.

---

## 78. O que é file descriptor?

Pontos para responder:

* Identificador numérico para arquivo, socket ou pipe.
* Usado pelo SO para operações de I/O.
* stdin, stdout e stderr são file descriptors.

---

## 79. Qual a diferença entre I/O bloqueante e não bloqueante?

Pontos para responder:

* Bloqueante espera operação terminar.
* Não bloqueante retorna imediatamente.
* Importante para servidores de alta concorrência.
* Relacionado a event loop e async I/O.

---

## 80. Como investigar consumo alto de CPU em um servidor?

Pontos para responder:

* Usar top/htop.
* Identificar processo.
* Ver logs.
* Ver threads.
* Usar profiler.
* Ver loops infinitos.
* Ver carga de requests.
* Ver queries ou jobs pesados.

---

# 24. Novas Perguntas Técnicas — Redes

## 81. O que acontece quando você acessa uma URL no navegador?

Pontos para responder:

* Resolução DNS.
* Conexão TCP.
* Handshake TLS se HTTPS.
* Envio da requisição HTTP.
* Processamento pelo servidor.
* Resposta HTTP.
* Renderização no navegador.

---

## 82. Qual a diferença entre TCP e UDP?

Pontos para responder:

* TCP é orientado à conexão.
* TCP garante entrega e ordem.
* UDP não garante entrega.
* UDP é mais leve.
* TCP usado em HTTP, banco, SSH.
* UDP usado em streaming, DNS, jogos.

---

## 83. O que é DNS?

Pontos para responder:

* Resolve nome de domínio para IP.
* Usa registros como A, AAAA, CNAME, MX, TXT.
* Pode impactar disponibilidade.
* Cache DNS pode causar comportamento confuso.

---

## 84. O que é TLS e por que ele importa?

Pontos para responder:

* Criptografa comunicação.
* Garante confidencialidade e integridade.
* Usa certificados.
* Protege contra interceptação.

---

## 85. Qual a diferença entre proxy e reverse proxy?

Pontos para responder:

* Proxy representa o cliente.
* Reverse proxy representa o servidor.
* Reverse proxy comum com Nginx, Traefik, Cloudflare.
* Usado para TLS, load balancing, cache e roteamento.

---

## 86. Como investigar timeout entre dois serviços?

Pontos para responder:

* Testar conectividade.
* Ver DNS.
* Ver firewall/security group.
* Ver porta.
* Ver logs dos dois lados.
* Medir latência.
* Ver timeout configurado.
* Ver carga no serviço destino.

---

## 87. O que é load balancing?

Pontos para responder:

* Distribuição de carga entre servidores.
* Pode ser round-robin, least connections, weighted.
* Ajuda escalabilidade e disponibilidade.
* Precisa de health check.

---

## 88. O que é rate limit?

Pontos para responder:

* Limita número de requisições.
* Protege contra abuso.
* Protege recursos internos.
* Pode ser por IP, usuário, token ou tenant.

---

## 89. Quando retry pode ser perigoso?

Pontos para responder:

* Pode aumentar carga em serviço já degradado.
* Pode duplicar operações.
* Precisa de backoff.
* Precisa de idempotência.
* Precisa de limite de tentativas.

---

## 90. O que é circuit breaker?

Pontos para responder:

* Padrão para interromper chamadas a serviço instável.
* Evita cascata de falhas.
* Estados: fechado, aberto e meio aberto.
* Trabalha junto com timeout e retry.

---

# 25. Novas Perguntas Técnicas — Banco Profundo

## 91. Por que o banco pode escolher Seq Scan mesmo existindo índice?

Pontos para responder:

* Query retorna muitos registros.
* Tabela pequena.
* Estatísticas indicam que índice não compensa.
* Coluna com baixa seletividade.
* Filtro não usa índice de forma eficiente.

---

## 92. O que é seletividade de índice?

Pontos para responder:

* Capacidade do índice de filtrar poucos registros.
* Coluna com muitos valores distintos tende a ser mais seletiva.
* Coluna booleana geralmente tem baixa seletividade.
* Impacta decisão do otimizador.

---

## 93. O que é Index Only Scan?

Pontos para responder:

* Banco responde usando apenas o índice.
* Evita acessar tabela principal.
* Requer que colunas estejam no índice.
* Pode depender de visibility map no PostgreSQL.

---

## 94. O que é Bitmap Heap Scan?

Pontos para responder:

* Banco usa índice para montar bitmap de páginas.
* Depois acessa páginas da tabela.
* Útil quando muitos registros são retornados.
* Meio termo entre Index Scan e Seq Scan.

---

## 95. Qual a diferença entre Nested Loop, Hash Join e Merge Join?

Pontos para responder:

* Nested Loop compara linhas de uma tabela com outra.
* Hash Join cria tabela hash para junção.
* Merge Join usa entradas ordenadas.
* Escolha depende de volume, índices e estatísticas.

---

## 96. O que são estatísticas do banco e por que ANALYZE importa?

Pontos para responder:

* Estatísticas ajudam otimizador a escolher plano.
* ANALYZE atualiza informações sobre distribuição dos dados.
* Estatísticas desatualizadas podem gerar plano ruim.
* Importante após grandes mudanças nos dados.

---

## 97. Como investigar uma transação aberta causando lock?

Pontos para responder:

* Ver sessões ativas.
* Ver queries em execução.
* Ver tempo da transação.
* Identificar processo bloqueador.
* Finalizar com cuidado.
* Corrigir fluxo que deixa transação aberta.

---

## 98. Quando usar materialized view?

Pontos para responder:

* Consultas pesadas e repetidas.
* Relatórios.
* Dados agregados.
* Quando aceita defasagem.
* Precisa estratégia de refresh.

---

## 99. Quando particionar uma tabela?

Pontos para responder:

* Tabela muito grande.
* Filtros por data, tenant ou categoria.
* Facilitar manutenção.
* Melhorar pruning.
* Cuidado com complexidade operacional.

---

## 100. Como evitar problema com OFFSET alto?

Pontos para responder:

* Usar cursor pagination.
* Usar seek pagination.
* Índices adequados.
* Ordenação determinística.
* Evitar páginas muito profundas em listagens grandes.

---

# 26. Novas Perguntas Técnicas — PHP, Runtime e Async

## 101. Qual a diferença entre PHP-FPM e Swoole?

Pontos para responder:

* PHP-FPM segue modelo request/response tradicional.
* Cada request tem ciclo isolado.
* Swoole mantém processo em memória.
* Swoole suporta corrotinas e alta concorrência.
* Em Swoole é preciso cuidado com estado compartilhado.

---

## 102. O que muda ao usar servidor async em PHP?

Pontos para responder:

* Processo não morre a cada request.
* Objetos podem permanecer em memória.
* Conexões podem ser reaproveitadas.
* Bugs de estado vazado podem aparecer.
* Performance pode melhorar em alta concorrência.

---

## 103. O que são corrotinas?

Pontos para responder:

* Unidade leve de execução cooperativa.
* Permite concorrência sem thread tradicional.
* Útil para I/O.
* Requer runtime compatível.

---

## 104. Por que código bloqueante pode ser problema em servidor async?

Pontos para responder:

* Pode travar o worker.
* Reduz concorrência.
* Afeta latência.
* Precisa usar APIs não bloqueantes quando possível.

---

## 105. O que é worker?

Pontos para responder:

* Processo ou unidade que executa trabalho.
* Pode atender requests ou consumir filas.
* Número de workers impacta concorrência.
* Deve ser dimensionado conforme CPU, memória e I/O.

---

# 27. Novas Perguntas Técnicas — System Design e Produção

## 106. Como desenhar um sistema de chat em tempo real?

Pontos para responder:

* WebSocket ou SSE.
* Autenticação.
* Persistência das mensagens.
* Status de leitura.
* Escalabilidade com pub/sub.
* Controle por tenant.
* Observabilidade.
* Fallback se conexão cair.

---

## 107. Como desenhar um sistema de notificações?

Pontos para responder:

* Eventos.
* Fila.
* Preferências do usuário.
* Canais: email, push, sistema.
* Retry.
* DLQ.
* Idempotência.
* Auditoria.

---

## 108. Como desenhar um sistema de auditoria?

Pontos para responder:

* Registrar ator.
* Tenant.
* Ação.
* Entidade afetada.
* Antes/depois quando necessário.
* Request ID.
* IP/User-Agent.
* Payload sanitizado.
* Imutabilidade quando possível.

---

## 109. Como desenhar um sistema de relatórios assíncronos?

Pontos para responder:

* Pedido de relatório cria job.
* Fila processa.
* Status processando/concluído/falhou.
* Arquivo gerado.
* Download seguro.
* Cache/agregações.
* Retry e DLQ.
* Notificação ao finalizar.

---

## 110. Como desenhar um sistema resiliente contra falha de serviço externo?

Pontos para responder:

* Timeout.
* Retry com backoff.
* Circuit breaker.
* Fallback.
* Fila.
* DLQ.
* Monitoramento.
* Idempotência.
* Logs claros.

---

## 111. Como decidir entre monólito modular e microserviços?

Pontos para responder:

* Monólito modular reduz complexidade operacional.
* Microserviços aumentam independência e escalabilidade.
* Microserviços exigem observabilidade, deploy, rede, versionamento e resiliência.
* Começar com monólito modular costuma ser mais seguro.
* Extrair serviço quando houver dor real.

---

## 112. O que é consistência eventual?

Pontos para responder:

* Dados podem ficar temporariamente diferentes entre partes do sistema.
* Com o tempo convergem.
* Comum em sistemas distribuídos.
* Exige comunicação clara ao usuário.
* Requer idempotência e reprocessamento.

---

## 113. O que é Outbox Pattern?

Pontos para responder:

* Grava dado e evento na mesma transação.
* Processo separado publica evento.
* Evita perder evento após commit.
* Muito útil em sistemas distribuídos.

---

## 114. O que é Inbox Pattern?

Pontos para responder:

* Controla mensagens recebidas.
* Evita processar duplicadas.
* Usa chave única/message_id.
* Ajuda idempotência no consumidor.

---

## 115. Como você faria rollback seguro?

Pontos para responder:

* Health check.
* Versão anterior disponível.
* Migrations compatíveis.
* Deploy em etapas.
* Backup se necessário.
* Observabilidade após deploy.
* Plano documentado.

---

# 28. Novas Perguntas Técnicas — Segurança Avançada

## 116. O que é Threat Modeling?

Pontos para responder:

* Análise de ameaças antes ou durante o desenvolvimento.
* Identificar ativos sensíveis.
* Identificar atacantes.
* Mapear riscos.
* Criar mitigação.
* Revisar pontos críticos.

---

## 117. Como proteger um sistema multitenant?

Pontos para responder:

* Isolamento por tenant.
* Middleware obrigatório.
* Policies.
* Testes automatizados.
* Índices com tenant_id.
* Auditoria.
* Logs.
* Não confiar no frontend.

---

## 118. O que é SSRF?

Pontos para responder:

* Server-Side Request Forgery.
* Servidor faz requisições indevidas a partir de input do usuário.
* Pode acessar rede interna.
* Mitigar validando URLs, bloqueando IPs internos e usando allowlist.

---

## 119. Como proteger tokens e sessões?

Pontos para responder:

* HTTPS.
* Expiração.
* Refresh token com cuidado.
* HttpOnly cookie quando aplicável.
* Rotação.
* Revogação.
* Nunca logar token.
* Proteção contra XSS/CSRF conforme estratégia.

---

## 120. Como lidar com dados sensíveis em logs?

Pontos para responder:

* Nunca logar senha/token.
* Mascarar CPF, email, telefone quando necessário.
* Sanitizar payload.
* Controlar acesso aos logs.
* Definir retenção.
* Auditar acesso.

---

# 29. Como Treinar as Perguntas

Para cada pergunta, monte a resposta assim:

```markdown
## Pergunta

Um consumer quebrou ao processar uma mensagem. Como você investiga?

## Resposta simulada

Primeiro eu verificaria os logs do consumer para entender o erro e identificar se a falha foi causada por payload inválido, regra de negócio, timeout, banco indisponível ou API externa. Depois analisaria a mensagem original, tentativas de retry e se ela foi enviada para a DLQ. Também verificaria se houve mudança de contrato entre o produtor e o consumidor.

## Exemplo real ou parecido que eu vivi

Em um projeto com filas, eu investiguei mensagens que falhavam por inconsistência no payload e precisei tratar melhor o erro, registrar o payload bruto e criar uma forma segura de reprocessamento.

## Como eu investigaria

- Ver logs.
- Ver payload.
- Ver stack trace.
- Ver retries.
- Ver DLQ.
- Ver contrato da mensagem.
- Ver mudanças recentes no produtor ou consumidor.

## Possíveis causas

- Payload inválido.
- Mudança de contrato.
- Banco indisponível.
- API externa fora.
- Timeout.
- Erro de regra de negócio.
- Duplicidade.
- Falta de idempotência.

## Como eu resolveria

Corrigiria a causa raiz, criaria tratamento de erro, aplicaria idempotência e garantiria que mensagens com falha definitiva fossem para dead letter.

## Como eu evitaria acontecer novamente

Criaria validação de contrato, logs com request ID, alerta para DLQ e testes cobrindo payloads inválidos.
```

---

# 30. Resultado Esperado ao Final dos 36 Meses

Ao final desse plano, você deverá conseguir:

* Entender memória, ponteiros, processos e redes.
* Explicar algoritmos e estruturas de dados.
* Investigar gargalos em banco de dados.
* Usar EXPLAIN ANALYZE com segurança.
* Projetar APIs bem organizadas.
* Criar cache com Redis de forma consciente.
* Trabalhar com filas, DLQ, retry e idempotência.
* Escrever código melhor com SOLID, DDD e padrões de projeto.
* Criar testes profissionais.
* Fazer deploy com Docker e CI/CD.
* Investigar produção com logs, métricas e tracing.
* Proteger APIs contra falhas comuns de segurança.
* Desenhar sistemas em entrevistas de System Design.
* Defender decisões técnicas com maturidade.
* Ter repertório real para entrevistas pleno/sênior.

---

# 31. Conselho Final

Não pule a base.

Framework você já pratica bastante.

O diferencial agora é estudar o que muita gente evita:

* C
* Memória
* Estruturas de dados
* Algoritmos
* Redes
* Sistemas operacionais
* Banco de dados por dentro
* Produção real

Essa base vai melhorar tudo:

* Seu Laravel
* Seu Docker
* Seu CI/CD
* Seu banco
* Suas filas
* Suas entrevistas
* Sua arquitetura
* Sua capacidade de investigar problema difícil

O foco não é estudar rápido.

O foco é estudar certo.
