# 🗓️ Plano de 24 Meses — Base Técnica Completa em Engenharia de Software

> **Carga:** 1 hora por dia, todos os dias  
> **Objetivo:** Construir base sólida de engenheiro de software (**backend-heavy**, fullstack consciente, frontend agnóstico)

---

## 🧠 Legenda de Status

- ✅ **Já domina / prática real em produção**
- 🔁 **Reforçar / aprofundar**
- 🧠 **Evolução estratégica (novo nível)**

---

## 🔴 MÊS 1 — Fundamentos de Engenharia de Software

### 🎯 Objetivo
Consolidar fundamentos com clareza conceitual.

### Conteúdos
* HTTP e REST (verbo, status code, headers) [YouTube](https://www.youtube.com/watch?v=9SbUPqKEWcY) ✅ Concluído
* Git (básico → intermediário) 🔁 *(rebase, reflog, cherry-pick)* [YouTube](https://www.youtube.com/watch?v=5NFuu4ivz-Y), [YouTube2](https://www.youtube.com/watch?v=6OokP-NE49k), [doc](https://git-scm.com/docs/git#_high_level_commands_porcelain).
* Expressões Regulares (Regex) 🔁 [YouTube](https://www.youtube.com/watch?v=tlVI8mV1dQY), [Site Teste](https://regexr.com/).
* Linux básico (shell, processos, permissões) 🔁
* 12 Fatores (visão arquitetural) 🔁

### 🧱 Projeto do Mês
API HTTP Playground com contratos claros.

---

## 🟠 MÊS 2 — Banco de Dados Relacional (PostgreSQL)

### 🎯 Objetivo
Elevar nível técnico em modelagem e performance.

### Conteúdos
- Modelagem relacional 🔁  
- Normalização / desnormalização 🔁  
- Índices e otimização 🔁  
- Transações e isolamento 🔁  
- EXPLAIN / ANALYZE 🔁  

### 🧱 Projeto
Sistema financeiro com foco em queries performáticas.

---

## 🟡 MÊS 3 — Performance em Banco de Dados

### 🎯 Objetivo
Entender profundamente gargalos reais de banco.

### Conteúdos
- Query tuning 🔁  
- Locks e concorrência 🔁  
- Réplicas leitura/escrita 🧠  
- Cache de consultas 🔁
- Functions e Procedures 🔁
- Triggers (uso responsável) 🔁
- PL/SQL e procedures (visão geral e uso responsável) 🔁
- Problema N+1
- Instancia para leitura e outra para escrita

### 🧱 Projeto
Auditoria e histórico com foco em performance.

---

## 📌 Complemento de Estudo — Algoritmos de Scan e EXPLAIN

### 🎯 Objetivo

Entender como o banco de dados acessa os dados internamente, quando ele varre a tabela inteira e quando ele usa índice para otimizar a busca.

---

## 🧠 Tópicos para estudar

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

### Índices importantes para estudar junto

* B-Tree
* Hash Index
* Bitmap Index
* GIN
* GiST
* BRIN
* R-Tree
* Inverted Index

---

## 🧪 Exercício prático — Query sem índice vs com índice

### 🎯 Objetivo

Executar a mesma query duas vezes:

1. Primeiro sem índice, para observar um `Seq Scan`
2. Depois com índice, para observar um `Index Scan`

Esse exercício serve para fixar como o banco escolhe o plano de execução.

---

## 1. Criar tabela de teste

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(150),
    idade INT
);
```

---

## 2. Inserir muitos registros

```sql
INSERT INTO usuarios (nome, email, idade)
SELECT 
    'Usuario ' || generate_series,
    'usuario' || generate_series || '@email.com',
    (random() * 60)::int
FROM generate_series(1, 100000);
```

---

## 3. Rodar query sem índice

```sql
EXPLAIN ANALYZE
SELECT *
FROM usuarios
WHERE email = 'usuario50000@email.com';
```

### Resultado esperado

```txt
Seq Scan on usuarios
```

### Interpretação

O banco precisou varrer a tabela inteira procurando o e-mail informado.

---

## 4. Criar índice na coluna `email`

```sql
CREATE INDEX idx_usuarios_email
ON usuarios(email);
```

---

## 5. Rodar a mesma query novamente

```sql
EXPLAIN ANALYZE
SELECT *
FROM usuarios
WHERE email = 'usuario50000@email.com';
```

### Resultado esperado

```txt
Index Scan using idx_usuarios_email on usuarios
```

### Interpretação

O banco usou o índice para localizar o registro de forma mais eficiente, sem precisar varrer a tabela inteira.

---

## 📊 Comparação

| Situação                       | Plano esperado           | O que acontece                                 |
| ------------------------------ | ------------------------ | ---------------------------------------------- |
| Sem índice                     | `Seq Scan`               | O banco lê a tabela inteira                    |
| Com índice                     | `Index Scan`             | O banco usa o índice para encontrar o registro |
| Query muito seletiva           | Índice tende a ajudar    | Retorna poucos registros                       |
| Query que retorna muitos dados | Seq Scan pode ser melhor | Índice nem sempre compensa                     |

---

## 🔎 O que observar no `EXPLAIN ANALYZE`

* `Seq Scan`
* `Index Scan`
* `cost`
* `actual time`
* `rows`
* `loops`
* `Filter`
* `Index Cond`

---

## ✅ Conclusão

Índice não serve apenas para “deixar rápido”.

Ele muda a forma como o banco acessa os dados.

Sem índice, o banco tende a fazer uma varredura completa da tabela.

Com índice, o banco pode localizar os registros diretamente pelo índice, reduzindo o custo da busca em tabelas grandes.

Esse exercício deve ser repetido com diferentes colunas, filtros e tamanhos de tabela para fixar bem o funcionamento do `EXPLAIN ANALYZE`.


## 🟢 MÊS 4 — Redis, Cache e Concorrência

### Conteúdos
- Redis (TTL, cache, locks distribuídos) 🔁  
- Estratégias de cache 🧠  
- SQL vs NoSQL 🔁  
- MongoDB – visão prática 🧠
- Programação assíncrona com PHP 🧠
- Introdução ao Swoole 🧠
- Corrotinas e workers no Swoole 🧠
- Servidor HTTP com Swoole 🧠

### 🧱 Projeto
Camada de cache com métricas de hit/miss.

---

## 🟣 MÊS 5 — Engenharia de Código

### 🎯 Objetivo
Elevar qualidade do código para nível profissional.

### Conteúdos
- SOLID na prática 🧠  
- Refatoração segura 🧠  
- Clean Code 🔁  
- Code Smells 🧠  
- Design de APIs RESTful 🧠  
- Boas práticas de OO 🔁  
- Code Review eficaz 🧠  

### 🧱 Projeto
Refatorar um sistema legado aplicando todos os conceitos.

---

## 🔵 MÊS 6 — Arquitetura de Software

### Conteúdos

#### Fundamentos
- Monólito Modular 🔁  
- Escala vertical vs horizontal 🔁  
- DDD na prática 🔁  
- Bounded Context 🔁  
- Arquitetura Hexagonal 🧠  
- Clean × Onion × Hexagonal 🧠  
- ADRs 🧠
- Hyperf Framework 🧠
- Arquitetura assíncrona em PHP 🧠
- Dependency Injection no Hyperf 🧠
- Middlewares e servidores async 🧠

#### Padrões de Projeto

**Criacionais**
- Factory, Abstract Factory, Builder, Singleton, Prototype 🧠  

**Estruturais**
- Adapter, Decorator, Facade, Proxy, Composite 🧠  

**Comportamentais**
- Strategy, Observer, Command, Chain of Responsibility, State, Template Method 🧠  

### 🧱 Projeto
Monólito modular com ADRs e padrões aplicados.

---

## 🟤 MÊS 7 — Testes Profissionais

- Testes unitários 🔁  
- TDD consciente 🔁  
- Testes de integração 🔁  
- Mutation Testing 🧠  
- Testabilidade independente de framework 🧠  

### 🧱 Projeto
API com cobertura robusta.

---

## 🟠 MÊS 8 — Sistemas Distribuídos

- Microserviços 🔁  
- RabbitMQ 🔁  
- Event-Driven Architecture 🧠  
- CQRS e Sagas 🧠  
- Idempotência 🧠  

### 🧱 Projeto
Sistema assíncrono resiliente.

---

## ⚫ MÊS 9 — DevOps Essencial

- Docker 🔁  
- Docker Compose 🔁  
- CI/CD 🔁  
- Secrets 🧠  

---

## ⚪ MÊS 10 — Cloud e Kubernetes

- AWS básico 🔁  
- Kubernetes 🧠  
- Autoscaling 🧠  

---

## 🔴 MÊS 11 — Observabilidade

- Logs centralizados 🔁  
- Prometheus/Grafana 🧠  
- SLO/SLA/SLI 🧠  
- Circuit Breaker 🧠  

---

## 🟠 MÊS 12 — Segurança

- OWASP Top 10 🧠  
- Autenticação e autorização 🔁  
- Threat Modeling 🧠  

---

# 🔷 FRONTEND (APENAS BASE NECESSÁRIA)

## 🟡 MÊS 13 — Fundamentos de Frontend

- JavaScript moderno 🔁  
- TypeScript 🔁  
- HTML semântico 🔁  
- CSS e responsividade 🔁  
- Consumo de APIs 🔁  

### 🧱 Projeto
Dashboard simples integrado ao backend.

---

# 🚀 SEGUNDA METADE – APROFUNDAMENTO (14–24)

## MÊS 14 — System Design
- Desenho de sistemas reais 🧠  
- Trade-offs de escala 🧠  

## MÊS 15 — Performance Avançada
- Profiling  
- Load testing  
- Tuning de aplicações  

## MÊS 16 — Arquitetura Avançada
- Event sourcing  
- Consistência eventual  

## MÊS 17 — Resiliência
- Chaos Engineering  
- Backpressure  

## MÊS 18 — Dados e Mensageria Avançada
- Kafka  
- Streams  

## MÊS 19 — SRE
- Runbooks  
- On-call  

## MÊS 20 — Liderança Técnica
- Code review avançado  
- Mentoria  

## MÊS 21 — Segurança Avançada
- Criptografia  
- OAuth profundo  

## MÊS 22 — Especialização na Stack
- Domínio profundo da linguagem principal  

## MÊS 23 — Produto e Arquitetura
- Decisões técnicas orientadas a negócio  

## MÊS 24 — Projeto Final Master

Sistema completo com:

- Arquitetura evolutiva  
- Backend escalável  
- Observabilidade  
- CI/CD  
- Segurança aplicada  
- Documentação profissional  

---

## Certificações Sugeridas

- Linux Foundation – LFS101  
- Docker Certified Associate  
- CKAD – Kubernetes  
- AWS Solutions Architect  
- iSAQB Foundation  

---

## ✅ Resultado Final

Ao final de 24 meses você será capaz de:

- Atuar como **engenheiro de software completo**
- Tomar decisões arquiteturais maduras
- Construir sistemas distribuídos confiáveis
- Entregar soluções com qualidade profissional

======================================================================================================================

# 🧩 TRILHA TÉCNICA RAIZ – BASE DE BAIXO NÍVEL
> Complemento ao plano profissional de 24 meses  
> Foco: fundamentos profundos de computação e engenharia

**Carga recomendada:**  
- 20 minutos por dia  
- Projetos técnicos aos fins de semana

---

## 🎯 Objetivo Geral

Transformar você em um programador realmente técnico:

- Entender como o computador funciona por baixo  
- Dominar memória, processos e redes  
- Saber depurar problemas complexos  
- Não depender apenas de frameworks  
- Ter base sólida de ciência da computação  

---

# 🟢 CAMADA 1 – PROGRAMAÇÃO DE BAIXO NÍVEL  
### (Meses 1 a 4)

### Conteúdos Fundamentais
- Introdução à linguagem C  
- Ambiente de compilação  
- GCC e Makefile  
- Estrutura básica de programas  
- Tipos primitivos  
- Compilação e linking  
- Ponteiros  
- Stack x Heap  
- Alocação de memória  
- Arrays e strings em baixo nível  
- Manipulação de memória  
- Debug com gdb  
- Valgrind  
- Syscalls  
- File descriptors  
- Manipulação de arquivos  
- I/O básico  

### Projetos Técnicos
- Hello World com Makefile  
- Mini calculadora em C  
- Implementar lista ligada  
- Implementar HashMap  
- Parser de arquivo CSV  

---

# 🟢 CAMADA 2 – SISTEMAS OPERACIONAIS  
### (Meses 5 e 6)

### Conteúdos
- Processos  
- Threads  
- Concorrência  
- Mutex e semáforos  
- Scheduling  
- Gerenciamento de memória pelo SO  
- I/O bloqueante vs não bloqueante  
- Comunicação entre processos  

### Projetos Técnicos
- Programa multithread em C  
- Criar um MINI SHELL em C  
- Simulador simples de tarefas  

---

# 🟢 CAMADA 3 – REDES DE COMPUTADORES  
### (Meses 7 e 8)

### Conteúdos
- TCP x UDP  
- Modelo OSI  
- Sockets  
- DNS  
- TLS  
- Handshake  
- Latência e perda de pacotes  
- Proxies e load balancing  

### Projetos Técnicos
- Cliente/servidor via socket  
- Chat via socket em C  
- Implementação de protocolo próprio simples  

---

# 🟢 CAMADA 4 – ESTRUTURAS DE DADOS E ALGORITMOS  
### (Meses 9 e 10)

### Conteúdos
- Big O  
- Estruturas de dados clássicas  
- Algoritmos de ordenação  
- Árvores  
- Grafos  
- BFS e DFS  
- Hashing profundo  
- Complexidade de algoritmos  

### Projetos Técnicos
- Implementar árvore binária  
- Biblioteca de algoritmos em C  
- Implementações próprias de ordenação  

---

# 🟢 CAMADA 5 – COMPILADORES E INTERPRETADORES  
### (Meses 11 e 12)

### Conteúdos
- Como linguagens funcionam  
- Lexer  
- Parser  
- AST  
- Bytecode  
- Máquina virtual  
- Interpretadores  

### Projetos Técnicos
- Parser simples de expressões  
- Interpretador básico  
- Criar uma mini linguagem funcional  

---

# 🔧 APLICAÇÃO CONTÍNUA (Meses 13 a 24)

A partir do mês 13 a trilha técnica passa a servir de base para tudo:

- Analisar sistemas pensando em SO e rede  
- Profiling de verdade  
- Entender consistência eventual  
- Diagnosticar falhas de rede  
- Protocolos de mensageria  
- Observabilidade técnica  
- Criptografia real  
- Dominar runtime da linguagem principal  

---

# 🕒 ROTINA RECOMENDADA

**Durante a semana**
- 20 min por dia de estudo técnico  
- Exercícios práticos em C

**Fim de semana**
- 1 hora dedicada a projetos técnicos  
- Implementação dos projetos do mês  

---

## 🎯 Resultado Esperado

Ao final dessa trilha você será capaz de compreender de verdade:

- Memória  
- Processos  
- Redes  
- Sistemas operacionais  
- Compilação  
- Performance real   

## Documentação do Projeto

📄 [Abrir documentação em PDF](./opsflow_documentacao_projeto.pdf)
