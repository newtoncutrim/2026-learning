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

# 50 Perguntas Técnicas de Vivência — Pleno/Sênior

Este material reúne perguntas técnicas comuns em entrevistas para desenvolvedores nível pleno/sênior, com foco em arquitetura, segurança, performance, cache, filas, DDD, SOLID, padrões de projeto, banco de dados, CI/CD e produção.

A ideia é treinar cada pergunta no formato:

```markdown
1. Contexto do problema
2. Como eu investigaria
3. Possíveis causas
4. Como eu resolveria
5. Como evitaria acontecer novamente
```

---

## 1. Arquitetura de Sistemas

### 1. Como você desenharia a arquitetura de um SaaS multitenant?

**O que o entrevistador quer avaliar:**
Se você entende isolamento de dados, tenant, permissões, escalabilidade e manutenção.

**Pontos para responder:**

* Tenant por clínica/empresa.
* Usuários vinculados ao tenant.
* Middleware para resolver o tenant.
* Isolamento por `tenant_id`, banco por tenant ou schema por tenant.
* Policies/guards para autorização.
* Auditoria.
* Testes garantindo que um tenant não acessa dados de outro.

---

### 2. Banco único ou banco por tenant: quando usar cada abordagem?

**Pontos para responder:**

* Banco único é mais simples e barato.
* Banco por tenant dá mais isolamento.
* Banco por tenant facilita backup/restauração individual.
* Banco único exige muito cuidado com escopo por tenant.
* A decisão depende de custo, compliance, volume e operação.

---

### 3. Como você separaria responsabilidades em uma aplicação grande?

**Pontos para responder:**

* Controller fino.
* Service/Application Layer para casos de uso.
* Repository/Query Object para consultas.
* DTO/FormRequest para entrada de dados.
* Domain para regra de negócio.
* Jobs para tarefas assíncronas.
* Events para desacoplamento.

---

### 4. Quando você criaria um microserviço?

**Pontos para responder:**

* Quando há domínio bem separado.
* Quando precisa escalar de forma independente.
* Quando precisa deployar de forma independente.
* Quando há times diferentes.
* Quando o módulo tem carga muito diferente do restante do sistema.
* Evitar microserviço por moda.
* Considerar custo operacional, observabilidade e comunicação entre serviços.

---

### 5. Como você projetaria um módulo de relatórios pesados?

**Pontos para responder:**

* Relatório assíncrono.
* Uso de fila.
* Status de processamento.
* Cache do resultado.
* Exportação para arquivo.
* Tabelas agregadas/materialized views.
* Evitar travar o banco transacional.
* Notificação quando finalizar.

---

## 2. Performance e Gargalos

### 6. Um endpoint está lento em produção. Como você investiga?

**Pontos para responder:**

* Ver logs e tempo de resposta.
* Identificar queries lentas.
* Medir CPU, RAM, disco e rede.
* Ver chamadas externas.
* Usar APM/tracing se existir.
* Verificar N+1 queries.
* Verificar locks no banco.
* Reproduzir com carga controlada.

---

### 7. Um endpoint recebe muitas requisições e começa a gargalar. Como resolver?

**Pontos para responder:**

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

### 8. Como você identifica e resolve problema de N+1 queries?

**Pontos para responder:**

* Analisar logs SQL.
* Usar profiler/debugbar/APM.
* Identificar queries repetidas dentro de loop.
* Usar eager loading.
* Revisar relacionamentos do ORM.
* Criar testes ou métricas para evitar regressão.

---

### 9. Como você otimiza uma listagem com milhões de registros?

**Pontos para responder:**

* Paginação real.
* Cursor pagination.
* Índices corretos.
* Filtros obrigatórios.
* Evitar `OFFSET` muito alto.
* Reduzir colunas retornadas.
* Cache quando fizer sentido.
* Busca assíncrona para casos pesados.

---

### 10. Como você reduziria o tempo de resposta de uma API crítica?

**Pontos para responder:**

* Medir antes de otimizar.
* Identificar o gargalo principal.
* Melhorar query.
* Aplicar cache.
* Remover processamento desnecessário da requisição.
* Usar fila para tarefas demoradas.
* Reduzir serialização/payload.
* Melhorar infraestrutura se necessário.

---

## 3. Banco de Dados e Queries

### 11. Como você investiga uma query lenta?

**Pontos para responder:**

* Usar `EXPLAIN` ou `EXPLAIN ANALYZE`.
* Ver tipo de scan.
* Ver linhas estimadas versus linhas reais.
* Ver joins.
* Ver ordenação.
* Ver filtros.
* Ver índices existentes.
* Ver locks e transações abertas.

---

### 12. O que você observa em um `EXPLAIN ANALYZE`?

**Pontos para responder:**

* Tempo real.
* Custo estimado.
* Linhas estimadas.
* Linhas reais.
* Tipo de scan.
* Uso de índice.
* Nested loop, hash join ou merge join.
* Sort em memória ou disco.
* Gargalo principal do plano.

---

### 13. Quando um índice ajuda e quando atrapalha?

**Ajuda quando:**

* Existem filtros frequentes.
* Existem joins.
* Existe ordenação.
* O campo possui boa seletividade.

**Atrapalha quando:**

* Há escrita muito intensa.
* Existem índices duplicados.
* A coluna tem baixa seletividade.
* A tabela é pequena.
* O índice nunca é usado.

---

### 14. Como escolher a ordem das colunas em um índice composto?

**Pontos para responder:**

* Colunas de igualdade primeiro.
* Colunas mais seletivas primeiro, dependendo do caso.
* Depois colunas de range.
* Considerar `ORDER BY`.
* Considerar o padrão real das queries.
* Não criar índice no chute.

---

### 15. Como você investigaria deadlock ou lock no banco?

**Pontos para responder:**

* Ver transações abertas.
* Ver queries bloqueadas.
* Ver ordem dos updates.
* Ver tempo de transação.
* Ver índices ausentes.
* Reduzir escopo da transação.
* Padronizar ordem de atualização.
* Criar retry controlado para deadlock.

---

## 4. Cache e Redis

### 16. Quando você usaria cache em uma aplicação?

**Pontos para responder:**

* Dados lidos com frequência.
* Dados que mudam pouco.
* Queries caras.
* Configurações.
* Relatórios.
* Listagens públicas.
* Cuidado com dados sensíveis e tenant.

---

### 17. Como você evita cache desatualizado?

**Pontos para responder:**

* TTL.
* Invalidação por evento.
* Cache key bem definida.
* Cache por tenant/usuário/filtro.
* Atualizar cache após escrita.
* Cache tags, se disponível.
* Monitorar hit/miss.

---

### 18. O que é cache stampede e como resolver?

**Pontos para responder:**

* Muitos requests tentando recriar o mesmo cache ao mesmo tempo.
* Usar lock.
* TTL com jitter.
* Stale-while-revalidate.
* Pré-aquecimento.
* Limitar concorrência.

---

### 19. Como você definiria uma chave de cache segura em sistema multitenant?

**Pontos para responder:**

* Incluir `tenant_id`.
* Incluir filtros relevantes.
* Incluir versão da regra, se necessário.
* Evitar misturar dados entre clientes.
* Não colocar dados sensíveis desnecessários na chave.

---

### 20. Quando você não usaria cache?

**Pontos para responder:**

* Dados mudam o tempo todo.
* Dados altamente sensíveis.
* Regras de invalidação muito complexas.
* A query já é rápida.
* O cache pode gerar inconsistência crítica.

---

## 5. Filas, Consumers e Mensageria

### 21. Um consumer quebrou ao processar uma mensagem. Como você investiga?

**Pontos para responder:**

* Ver logs.
* Ver payload.
* Ver stack trace.
* Ver tentativas/retries.
* Ver DLQ/dead letter.
* Ver se o erro é de regra, infraestrutura ou contrato.
* Reprocessar com segurança.
* Criar correção para evitar loop infinito.

---

### 22. Como evitar que uma mensagem seja processada duas vezes?

**Pontos para responder:**

* Idempotência.
* Chave única.
* Controle por `message_id` ou chave de negócio.
* Transação.
* Lock quando necessário.
* `ack` somente após sucesso.
* Garantia contra duplicidade no banco.

---

### 23. O que fazer com mensagens que falham várias vezes?

**Pontos para responder:**

* Enviar para dead letter.
* Guardar payload e erro.
* Alertar o time.
* Classificar erro temporário ou definitivo.
* Corrigir causa raiz.
* Reprocessar com comando controlado.

---

### 24. Como dimensionar consumers para alto volume?

**Pontos para responder:**

* Medir taxa de entrada.
* Medir taxa de consumo.
* Aumentar workers.
* Ajustar prefetch.
* Separar filas por prioridade.
* Otimizar processamento.
* Ver gargalo no banco ou API externa.

---

### 25. Qual a diferença entre `ack`, `nack` e retry?

**Pontos para responder:**

* `ack`: mensagem processada com sucesso.
* `nack`: falha, podendo reenfileirar ou descartar.
* Retry: nova tentativa controlada.
* Cuidado com loop infinito.
* Após limite de tentativas, enviar para DLQ.

---

## 6. Segurança

### 26. Como proteger uma API contra SQL Injection?

**Pontos para responder:**

* Prepared statements.
* Query builder/ORM com bindings.
* Nunca concatenar input diretamente no SQL.
* Validação de entrada.
* Permissão mínima no banco.
* Logs e WAF como camada adicional.

---

### 27. Como proteger rotas administrativas?

**Pontos para responder:**

* Autenticação.
* Autorização.
* Roles/permissions.
* Policies/gates.
* MFA se necessário.
* Rate limit.
* Auditoria.
* Nunca confiar apenas no frontend.

---

### 28. Como evitar vazamento de dados entre tenants?

**Pontos para responder:**

* Middleware de tenant.
* Escopo obrigatório por `tenant_id`.
* Policies.
* Testes automatizados.
* Índices com tenant.
* Não confiar em IDs enviados pelo frontend.
* Auditoria.

---

### 29. Como tratar upload de arquivos com segurança?

**Pontos para responder:**

* Validar tamanho.
* Validar MIME real.
* Não confiar apenas na extensão.
* Renomear arquivo.
* Armazenar fora da pasta pública direta.
* Controlar permissão de download.
* Usar antivírus quando necessário.
* Impedir execução de arquivo enviado.

---

### 30. Como você lida com secrets em CI/CD?

**Pontos para responder:**

* GitHub Secrets.
* Nunca commitar `.env`.
* Rotacionar token vazado.
* Escopo mínimo.
* Secrets por ambiente.
* Não imprimir secrets em logs.
* Revisar permissões do runner.

---

## 7. SOLID, DDD e Código Limpo

### 31. Como você aplicaria SOLID em um módulo real?

**Pontos para responder:**

* Classe com responsabilidade única.
* Interfaces para dependências.
* Evitar acoplamento forte.
* Injeção de dependência.
* Separar regra de negócio de infraestrutura.
* Facilitar testes.

---

### 32. O que é Single Responsibility Principle na prática?

**Pontos para responder:**

* Uma classe deve ter um motivo principal para mudar.
* Controller não deve conter regra complexa.
* Service não deve misturar persistência, HTTP e regra de negócio.
* Separar validação, regra, persistência e resposta.

---

### 33. Como você identificaria uma violação de Open/Closed Principle?

**Pontos para responder:**

* Muitos `if/else` para tipos diferentes.
* Toda nova regra exige alterar uma classe central.
* Resolver com Strategy, polimorfismo, handler ou rule object.
* Código aberto para extensão e fechado para modificação.

---

### 34. Como você modelaria uma regra de negócio usando DDD?

**Pontos para responder:**

* Entidade com identidade.
* Value Object para valores imutáveis.
* Aggregate para consistência.
* Domain Service quando a regra não pertence a uma entidade.
* Repository como abstração de persistência.
* Eventos de domínio quando algo importante acontece.

---

### 35. Qual a diferença entre Entity e Value Object?

**Entity:**

* Tem identidade.
* Pode mudar estado.
* Exemplo: Usuário, Clínica, Pedido.

**Value Object:**

* Não tem identidade própria.
* É comparado por valor.
* Deve ser imutável.
* Exemplo: Dinheiro, CPF, Email, Endereço.

---

## 8. Padrões de Projeto

### 36. Quando você usaria Strategy?

**Pontos para responder:**

* Quando há várias formas de executar uma regra.
* Para evitar `if/else` grande.
* Exemplo: cálculo por tipo de operação, formas de pagamento, regras por segmento.
* Facilita extensão e teste.

---

### 37. Quando você usaria Factory?

**Pontos para responder:**

* Quando a criação de objetos é complexa.
* Quando o tipo criado depende de regra.
* Evita espalhar `new` pela aplicação.
* Centraliza criação.
* Facilita troca de implementação.

---

### 38. Quando você usaria Observer/Event?

**Pontos para responder:**

* Quando uma ação gera efeitos colaterais.
* Exemplo: pedido criado → enviar e-mail, auditar, notificar.
* Desacopla regra principal.
* Cuidado para não esconder fluxo crítico demais.

---

### 39. Quando você usaria Repository?

**Pontos para responder:**

* Para abstrair acesso a dados.
* Para separar domínio da persistência.
* Para centralizar queries.
* Para facilitar testes.
* Cuidado para não criar repository genérico inútil.

---

### 40. Quando você usaria Adapter?

**Pontos para responder:**

* Integração com API externa.
* Gateway de pagamento.
* Serviço de e-mail.
* Storage.
* Permite trocar fornecedor sem afetar domínio.
* Traduz contrato externo para contrato interno.

---

## 9. Observabilidade e Produção

### 41. Como investigar um erro que só acontece em produção?

**Pontos para responder:**

* Logs centralizados.
* Request ID.
* Payload sanitizado.
* Tracing.
* Métricas.
* Ver deploy recente.
* Comparar staging/prod.
* Criar teste reproduzindo erro.

---

### 42. Quais métricas você acompanha em uma API?

**Pontos para responder:**

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

### 43. O que fazer se a aplicação começa a retornar 500 após deploy?

**Pontos para responder:**

* Ver logs.
* Ver alteração recente.
* Ver migrations.
* Ver `.env`.
* Ver cache de config.
* Ver permissões.
* Fazer rollback se o impacto for alto.
* Corrigir com hotfix.

---

### 44. Como você usaria logs de auditoria para investigar um bug?

**Pontos para responder:**

* Buscar por tenant.
* Buscar por usuário.
* Buscar por request ID.
* Ver antes/depois.
* Ver payload sanitizado.
* Ver ordem dos eventos.
* Cruzar com logs da aplicação.

---

### 45. Como você estruturaria logs para facilitar debug?

**Pontos para responder:**

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

## 10. CI/CD, Docker e Infraestrutura

### 46. Como montar uma pipeline segura?

**Pontos para responder:**

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

### 47. Qual o risco de usar self-hosted runner?

**Pontos para responder:**

* Workflow executa comandos na sua máquina.
* PR malicioso pode comprometer ambiente.
* Secrets podem vazar.
* Runner pode acessar rede interna.
* Usar apenas em repositórios confiáveis.
* Rodar com usuário sem privilégios.
* Isolar por VM/container.

---

### 48. Como investigar um container que reinicia sozinho?

**Pontos para responder:**

* `docker logs`.
* `docker inspect`.
* Exit code.
* Healthcheck.
* Variáveis de ambiente.
* Permissões de volume.
* Falta de memória.
* Serviço dependente indisponível.

---

### 49. Como fazer deploy com menor risco de downtime?

**Pontos para responder:**

* Build antes de trocar versão.
* Symlink para release atual.
* Health check.
* Reload controlado.
* Migrations compatíveis.
* Rollback rápido.
* Blue-green ou rolling deploy.
* Monitorar após deploy.

---

### 50. Como lidar com migrations perigosas em produção?

**Pontos para responder:**

* Evitar mudanças destrutivas diretas.
* Criar coluna nova antes de remover antiga.
* Backfill em lotes.
* Índice criado com cuidado.
* Deploy em etapas.
* Testar tempo de execução.
* Ter plano de rollback.

---

## Como treinar essas perguntas

Para cada pergunta, monte sua resposta neste formato:

```markdown
### Pergunta

### Resposta simulada

### Exemplo real ou parecido que eu vivi

### Como eu investigaria

### Como eu resolveria

### Como eu evitaria acontecer novamente
```

Exemplo:

```markdown
### Pergunta

Um consumer quebrou ao processar uma mensagem. Como você investiga?

### Resposta simulada

Primeiro eu verificaria os logs do consumer para entender o erro e identificar se a falha foi causada por payload inválido, regra de negócio, timeout, banco indisponível ou API externa. Depois analisaria a mensagem original, tentativas de retry e se ela foi enviada para a DLQ. Também verificaria se houve mudança de contrato entre o produtor e o consumidor.

### Exemplo real ou parecido que eu vivi

Em um projeto com filas, eu investiguei mensagens que falhavam por inconsistência no payload e precisei tratar melhor o erro, registrar o payload bruto e criar uma forma segura de reprocessamento.

### Como eu resolveria

Corrigiria a causa raiz, criaria tratamento de erro, aplicaria idempotência e garantiria que mensagens com falha definitiva fossem para dead letter.

### Como eu evitaria acontecer novamente

Criaria validação de contrato, logs com request ID, alerta para DLQ e testes cobrindo payloads inválidos.
```

