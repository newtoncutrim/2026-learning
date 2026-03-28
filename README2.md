# 🗓️ Plano de 24 Meses — Backend, DevOps e Arquitetura de Software

> **Carga principal:** 1 hora por dia, todos os dias  
> **Carga complementar (trilha raiz):** 20 minutos por dia  
> **Objetivo:** Evoluir de desenvolvedor backend para um profissional com base sólida em **engenharia de software**, **DevOps** e **arquitetura**, com visão prática de sistemas reais.

---

# 🧭 Direcionamento da Trilha

Este plano foi reorganizado em 3 camadas:

## 1. 🧱 Trilha Principal
Foco direto na carreira:

- Backend
- Banco de dados
- Arquitetura
- DevOps
- Cloud
- Observabilidade
- Segurança

## 2. ⚙️ Trilha Técnica Raiz
Base de profundidade técnica:

- Memória
- Processos
- Redes
- Sistemas operacionais
- Estruturas de dados
- Debugging

## 3. 🧪 Trilha de Exploração
Assuntos avançados e opcionais, que entram depois da base principal:

- Kafka
- Event Sourcing
- Swoole
- Hyperf
- Chaos Engineering
- Compiladores

---

# 🧠 Legenda de Status

- ✅ **Já domina / usa em produção**
- 🔁 **Reforçar / aprofundar**
- 🧠 **Novo nível / evolução estratégica**
- 🧪 **Opcional / exploração futura**

---

# 📌 Regras do Plano

Cada mês deve ter:

- **Objetivo**
- **Conteúdos**
- **Projeto do mês**
- **Simulação de demandas reais**
- **Evidências de aprendizado**

---

# 🚀 TRILHA PRINCIPAL — 24 MESES

---

## 🔴 MÊS 1 — Fundamentos Operacionais do Desenvolvedor

### 🎯 Objetivo
Fortalecer a base de trabalho real em ambiente Linux, terminal, HTTP, Git e configuração de aplicações.

### Conteúdos
- HTTP e REST ✅
- Git básico → intermediário 🔁
  - rebase
  - reflog
  - cherry-pick
- Regex 🔁
- Linux básico 🔁
  - shell
  - navegação
  - permissões
  - processos
  - redirecionamento
- 12 Factors 🔁

### 🧱 Projeto do mês
API HTTP Playground com execução local, logs e documentação de uso.

### 💼 Tarefas simulando empresa real
- Criar uma API simples com rotas REST padronizadas
- Organizar variáveis de ambiente da aplicação
- Escrever um script para subir a aplicação localmente
- Corrigir problema de permissão em diretório de logs
- Investigar processo ocupando uma porta
- Criar coleção HTTP para validar endpoints
- Fazer um rebase de branch com conflito e resolver corretamente
- Criar documentação com comandos úteis para onboarding de dev

### ✅ Evidências de aprendizado
- README com instruções de setup
- Script `.sh` para subir aplicação
- Documento com comandos Linux usados
- Coleção de requests HTTP
- Resumo prático de Git intermediário

---

## 🟠 MÊS 2 — PostgreSQL e Modelagem Relacional

### 🎯 Objetivo
Aprender a modelar dados corretamente e escrever consultas com mais consciência estrutural.

### Conteúdos
- Modelagem relacional 🔁
- Normalização e desnormalização 🔁
- Chaves primárias e estrangeiras 🔁
- Índices e otimização 🔁
- Transações 🔁
- EXPLAIN / ANALYZE 🔁

### 🧱 Projeto do mês
Sistema financeiro com foco em modelagem e consultas performáticas.

### 💼 Tarefas simulando empresa real
- Modelar banco para sistema de contas, transações e categorias
- Criar migrations versionadas
- Ajustar relacionamento incorreto entre tabelas
- Identificar query lenta e propor índice
- Explicar uma consulta com `EXPLAIN ANALYZE`
- Resolver problema de integridade referencial
- Criar rotina de carga inicial de dados
- Revisar se a modelagem está normalizada ou precisa de desnormalização controlada

### ✅ Evidências de aprendizado
- DER ou diagrama do banco
- Migrations organizadas
- Consultas SQL comentadas
- Documento explicando índices escolhidos
- Antes/depois de otimização de query

---

## 🟡 MÊS 3 — Performance em Banco e Concorrência

### 🎯 Objetivo
Entender gargalos reais de leitura, escrita, concorrência e acesso excessivo a banco.

### Conteúdos
- Query tuning 🔁
- Locks e concorrência 🔁
- Problema N+1 🔁
- Réplica leitura/escrita 🧠
- Triggers e procedures com uso responsável 🔁
- Estratégias de acesso eficiente ao banco 🔁

### 🧱 Projeto do mês
Módulo de auditoria e histórico com preocupação real de performance.

### 💼 Tarefas simulando empresa real
- Investigar lentidão em listagem paginada
- Resolver problema de N+1 em endpoint
- Diagnosticar lock em atualização concorrente
- Separar leitura e escrita conceitualmente
- Criar tabela de histórico com estratégia de consulta eficiente
- Avaliar quando trigger ajuda e quando atrapalha
- Escrever consulta de relatório sem derrubar performance
- Criar benchmark simples entre duas abordagens SQL

### ✅ Evidências de aprendizado
- Documento “problemas comuns de performance em banco”
- Caso de N+1 resolvido
- Query otimizada com comparação
- Análise de lock/conflito
- Relatório técnico curto com trade-offs

---

## 🟢 MÊS 4 — Redis, Cache e Concorrência Aplicada

### 🎯 Objetivo
Aprender a reduzir carga no banco e melhorar resposta da aplicação com cache e controle de concorrência.

### Conteúdos
- Redis (TTL, cache, locks distribuídos) 🔁
- Estratégias de cache 🧠
- Cache aside / invalidação 🧠
- SQL vs NoSQL 🔁
- MongoDB 🧪
- Assincronismo em PHP 🧪
- Swoole 🧪

### 🧱 Projeto do mês
Camada de cache com medição de hit/miss e invalidação controlada.

### 💼 Tarefas simulando empresa real
- Colocar cache em endpoint caro
- Definir TTL por tipo de dado
- Medir taxa de cache hit/miss
- Resolver problema de cache stale
- Implementar lock para evitar dupla execução
- Criar fallback quando Redis estiver indisponível
- Documentar estratégia de invalidação
- Comparar cenário com e sem cache

### ✅ Evidências de aprendizado
- Endpoint com cache funcionando
- Dashboard simples de hit/miss
- Documento de estratégia de cache
- Exemplo de lock distribuído
- Comparação de tempo de resposta

---

## 🟣 MÊS 5 — Engenharia de Código

### 🎯 Objetivo
Escrever código mais limpo, sustentável, testável e fácil de evoluir.

### Conteúdos
- SOLID na prática 🧠
- Refatoração segura 🧠
- Clean Code 🔁
- Code Smells 🧠
- Design de APIs RESTful 🧠
- Boas práticas de OO 🔁
- Code Review eficaz 🧠

### 🧱 Projeto do mês
Refatoração de um módulo legado com melhoria estrutural e testes.

### 💼 Tarefas simulando empresa real
- Refatorar service grande demais
- Quebrar classe com múltiplas responsabilidades
- Padronizar nomes e contratos de método
- Remover duplicação de regra de negócio
- Melhorar legibilidade sem alterar comportamento
- Corrigir endpoint com contrato confuso
- Revisar PR simulando feedback técnico
- Documentar decisão de refatoração

### ✅ Evidências de aprendizado
- Antes/depois do código
- Lista de code smells encontrados
- PR simulado com comentários
- ADR simples de decisão técnica
- README de padrões adotados

---

## 🔵 MÊS 6 — Arquitetura de Software Aplicada

### 🎯 Objetivo
Entender arquitetura como ferramenta prática para organizar sistemas reais.

### Conteúdos
- Monólito modular 🔁
- Escala vertical vs horizontal 🔁
- DDD aplicado 🔁
- Bounded Context 🔁
- Arquitetura Hexagonal 🧠
- Clean x Onion x Hexagonal 🧠
- ADRs 🧠
- Padrões de projeto essenciais 🧠

### 🧱 Projeto do mês
Monólito modular com separação clara de domínio, aplicação e infraestrutura.

### 💼 Tarefas simulando empresa real
- Separar módulos por contexto de negócio
- Definir fronteiras entre domínios
- Criar porta/adaptador para serviço externo
- Escrever ADR justificando escolha arquitetural
- Aplicar Strategy em regra variável
- Aplicar Factory em criação complexa
- Reduzir acoplamento com abstrações corretas
- Revisar se a arquitetura está exagerada para o porte do sistema

### ✅ Evidências de aprendizado
- Diagrama de módulos
- ADRs
- Exemplo de padrão aplicado
- Documento comparando estilos arquiteturais
- Estrutura de pastas explicada

---

## 🟤 MÊS 7 — Testes Profissionais

### 🎯 Objetivo
Ganhar segurança para evoluir sistemas com qualidade.

### Conteúdos
- Testes unitários 🔁
- TDD consciente 🔁
- Testes de integração 🔁
- Mutation Testing 🧠
- Testabilidade independente de framework 🧠

### 🧱 Projeto do mês
API com suíte robusta de testes e estratégia clara de cobertura.

### 💼 Tarefas simulando empresa real
- Cobrir regra crítica com testes unitários
- Criar teste de integração para fluxo principal
- Corrigir teste frágil
- Melhorar testabilidade de classe acoplada
- Separar testes rápidos de testes mais caros
- Medir cobertura de pontos críticos
- Rodar mutation testing em módulo importante
- Evitar falso positivo em teste

### ✅ Evidências de aprendizado
- Estrutura de testes por camada
- Relatório de cobertura
- Caso de mutation testing
- Documento de estratégia de testes
- Exemplo de refatoração guiada por teste

---

## 🟠 MÊS 8 — Sistemas Distribuídos e Mensageria

### 🎯 Objetivo
Introduzir integração assíncrona, comunicação entre componentes e resiliência básica.

### Conteúdos
- Microserviços com senso crítico 🔁
- RabbitMQ 🔁
- Event-Driven Architecture 🧠
- CQRS 🧠
- Sagas 🧠
- Idempotência 🧠

### 🧱 Projeto do mês
Sistema assíncrono com filas e processamento resiliente.

### 💼 Tarefas simulando empresa real
- Criar fila para processamento em background
- Enviar evento após ação importante
- Tratar reprocessamento sem duplicidade
- Implementar idempotência em consumidor
- Separar comando de leitura mais pesada
- Criar dead-letter queue conceitualmente
- Simular falha de processamento e retry
- Documentar fluxo assíncrono

### ✅ Evidências de aprendizado
- Diagrama do fluxo assíncrono
- Consumidor com idempotência
- Documento sobre retry e falhas
- Exemplo de evento de domínio
- ADR de uso de fila

---

## ⚫ MÊS 9 — DevOps Essencial

### 🎯 Objetivo
Ganhar autonomia para empacotar, subir, automatizar e operar aplicações.

### Conteúdos
- Docker 🔁
- Docker Compose 🔁
- CI/CD 🔁
- Secrets 🧠
- Build e runtime 🧠

### 🧱 Projeto do mês
Aplicação containerizada com pipeline básica de build, teste e execução.

### 💼 Tarefas simulando empresa real
- Criar Dockerfile otimizado
- Separar ambiente de desenvolvimento e produção
- Montar `docker-compose` para app + banco + cache
- Corrigir problema de permissão em container
- Reduzir tamanho de imagem
- Criar pipeline para rodar testes
- Injetar variáveis com segurança
- Diagnosticar falha de container que sobe e morre

### ✅ Evidências de aprendizado
- Dockerfile comentado
- Compose funcional
- Pipeline simples
- Documento de troubleshooting
- Checklist de deploy local

---

## ⚪ MÊS 10 — Cloud e Kubernetes

### 🎯 Objetivo
Entender o básico de infraestrutura moderna e orquestração de aplicações.

### Conteúdos
- AWS básico 🔁
- Conceitos de cloud 🧠
- Kubernetes 🧠
- Autoscaling 🧠
- Load balancing 🧠

### 🧱 Projeto do mês
Deploy conceitual de aplicação em ambiente cloud/container orchestration.

### 💼 Tarefas simulando empresa real
- Desenhar arquitetura simples em cloud
- Subir aplicação em VM ou serviço gerenciado
- Configurar healthcheck
- Entender diferença entre container e orquestração
- Escrever manifest básico
- Definir recurso mínimo para app
- Simular rollout conceitualmente
- Documentar estratégia de deploy

### ✅ Evidências de aprendizado
- Diagrama da arquitetura
- Manifestos básicos
- Documento explicando componentes cloud
- Comparação entre deploy local e orquestrado
- Resumo de custos e trade-offs

---

## 🔴 MÊS 11 — Observabilidade

### 🎯 Objetivo
Aprender a enxergar o comportamento do sistema em produção.

### Conteúdos
- Logs centralizados 🔁
- Prometheus / Grafana 🧠
- Métricas 🧠
- SLO / SLA / SLI 🧠
- Circuit Breaker 🧠

### 🧱 Projeto do mês
Sistema com logs estruturados, métricas e painel de observação.

### 💼 Tarefas simulando empresa real
- Padronizar logs estruturados
- Criar correlação entre requisição e log
- Expor métricas básicas da aplicação
- Construir dashboard com indicadores
- Definir SLI e SLO iniciais
- Investigar erro em produção usando logs
- Simular aumento de falhas externas e circuit breaker
- Criar runbook simples para incidente

### ✅ Evidências de aprendizado
- Dashboard
- Estrutura de logs
- Documento com SLI/SLO
- Runbook simples
- Caso de investigação guiada por logs

---

## 🟠 MÊS 12 — Segurança Aplicada

### 🎯 Objetivo
Fortalecer a segurança da aplicação e das integrações desde a base.

### Conteúdos
- OWASP Top 10 🧠
- Autenticação e autorização 🔁
- Threat Modeling 🧠
- Segurança de API 🧠
- Gestão de secrets 🧠

### 🧱 Projeto do mês
Aplicação com autenticação, autorização, proteção de endpoints e revisão básica de riscos.

### 💼 Tarefas simulando empresa real
- Proteger endpoints por perfil
- Revisar exposição indevida de dados
- Validar entrada de dados corretamente
- Evitar falhas comuns de autenticação
- Guardar secrets de forma segura
- Desenhar ameaça simples do sistema
- Revisar logs sem vazar informação sensível
- Criar checklist de segurança para PR

### ✅ Evidências de aprendizado
- Fluxo de autenticação documentado
- Matriz simples de autorização
- Threat model básico
- Checklist de segurança
- Casos de validação crítica

---

## 🟡 MÊS 13 — Frontend Base Necessária

### 🎯 Objetivo
Ter autonomia mínima para integrar interfaces, consumir APIs e colaborar melhor com frontend.

### Conteúdos
- JavaScript moderno 🔁
- TypeScript 🔁
- HTML semântico 🔁
- CSS responsivo 🔁
- Consumo de APIs 🔁

### 🧱 Projeto do mês
Dashboard simples consumindo backend.

### 💼 Tarefas simulando empresa real
- Criar tela simples de listagem
- Consumir endpoint paginado
- Tratar loading e erro
- Exibir feedback visual de ação concluída
- Validar formulário básico
- Corrigir problema de integração frontend-backend
- Mapear contrato de API
- Documentar payload esperado

### ✅ Evidências de aprendizado
- Dashboard funcional
- Integração com backend
- Documento de contrato API/UI
- Formulário simples validado
- Resumo de fluxo frontend-backend

---

# 🚀 SEGUNDA METADE — APROFUNDAMENTO

---

## MÊS 14 — System Design

### Conteúdos
- Desenho de sistemas reais 🧠
- Trade-offs de escala 🧠
- Capacidade, gargalos, limites 🧠

### 💼 Tarefas simulando empresa real
- Desenhar arquitetura de sistema de pedidos
- Definir gargalos prováveis
- Decidir entre monólito modular e microserviço
- Justificar uso de cache, fila e banco
- Estimar pontos de falha

---

## MÊS 15 — Performance Avançada

### Conteúdos
- Profiling 🧠
- Load testing 🧠
- Tuning de aplicações 🧠

### 💼 Tarefas simulando empresa real
- Rodar teste de carga em endpoint crítico
- Identificar gargalo de CPU, I/O ou banco
- Comparar duas abordagens de implementação
- Reduzir tempo de resposta de operação crítica

---

## MÊS 16 — Arquitetura Avançada

### Conteúdos
- Event Sourcing 🧪
- Consistência eventual 🧠
- Trade-offs arquiteturais 🧠

### 💼 Tarefas simulando empresa real
- Avaliar se event sourcing faz sentido ou é exagero
- Modelar fluxo com consistência eventual
- Descrever impactos para produto e suporte

---

## MÊS 17 — Resiliência

### Conteúdos
- Retry
- Timeout
- Backpressure 🧠
- Chaos Engineering 🧪

### 💼 Tarefas simulando empresa real
- Simular falha de integração externa
- Implementar retry com critério
- Definir timeout adequado
- Proteger sistema contra sobrecarga

---

## MÊS 18 — Dados e Mensageria Avançada

### Conteúdos
- Kafka 🧪
- Streams 🧪
- Arquiteturas orientadas a eventos 🧠

### 💼 Tarefas simulando empresa real
- Comparar RabbitMQ e Kafka
- Modelar pipeline de eventos
- Desenhar fluxo de processamento contínuo

---

## MÊS 19 — SRE

### Conteúdos
- Runbooks 🧠
- On-call 🧠
- Gestão de incidente 🧠

### 💼 Tarefas simulando empresa real
- Criar runbook para indisponibilidade
- Definir passos de diagnóstico
- Escrever pós-mortem técnico simples
- Melhorar sinalização de alerta

---

## MÊS 20 — Liderança Técnica

### Conteúdos
- Code review avançado 🧠
- Mentoria 🧠
- Comunicação técnica 🧠

### 💼 Tarefas simulando empresa real
- Revisar PR com feedback útil
- Escrever comentário técnico claro
- Propor melhoria de arquitetura
- Explicar trade-off para time não técnico

---

## MÊS 21 — Segurança Avançada

### Conteúdos
- Criptografia 🔁
- OAuth profundo 🧠
- Segurança de integração 🧠

### 💼 Tarefas simulando empresa real
- Integrar autenticação externa
- Avaliar escopo de token
- Evitar fluxo inseguro de credenciais
- Revisar comunicação segura entre serviços

---

## MÊS 22 — Especialização na Stack Principal

### Conteúdos
- Domínio profundo da linguagem principal 🧠
- Runtime, performance e boas práticas 🧠

### 💼 Tarefas simulando empresa real
- Investigar gargalo específico da linguagem
- Estudar internals relevantes para produção
- Melhorar uso de memória, concorrência ou I/O

---

## MÊS 23 — Produto e Arquitetura

### Conteúdos
- Decisões técnicas orientadas a negócio 🧠
- Priorização técnica 🧠
- Trade-offs entre prazo, custo e qualidade 🧠

### 💼 Tarefas simulando empresa real
- Escolher solução boa o suficiente em vez da mais sofisticada
- Priorizar dívida técnica com critério
- Defender decisão técnica com impacto de negócio

---

## MÊS 24 — Projeto Final Master

### 🎯 Objetivo
Consolidar tudo em um sistema completo, com visão de produto, arquitetura, operação e manutenção.

### Sistema final deve incluir
- Arquitetura evolutiva
- Backend escalável
- Banco modelado com consciência
- Cache
- Fila
- Observabilidade
- CI/CD
- Segurança aplicada
- Documentação profissional
- ADRs
- Runbooks
- Deploy reproduzível

### 💼 Tarefas simulando empresa real
- Entregar sistema com documentação de onboarding
- Publicar pipeline funcional
- Criar checklist de deploy
- Criar dashboard de operação
- Registrar decisões arquiteturais
- Simular incidente e resposta

---

# 🧩 TRILHA TÉCNICA RAIZ — COMPLEMENTAR

> **Objetivo:** fortalecer entendimento profundo de computação sem perder foco da trilha principal.

---

## CAMADA 1 — Base de C, Memória e I/O (Meses 1 a 4)

### Conteúdos
- Introdução à linguagem C
- GCC e Makefile
- Ponteiros
- Stack x Heap
- Alocação de memória
- Arrays e strings
- Manipulação de memória
- File descriptors
- Syscalls
- I/O básico
- gdb
- Valgrind

### 💼 Tarefas simulando empresa real
- Corrigir vazamento de memória
- Investigar segmentation fault
- Ler arquivo grande sem travar programa
- Criar parser simples de CSV
- Depurar comportamento inesperado com gdb

---

## CAMADA 2 — Processos, Threads e Concorrência (Meses 5 e 6)

### Conteúdos
- Processos
- Threads
- Mutex
- Semáforos
- Scheduling
- IPC
- I/O bloqueante vs não bloqueante

### 💼 Tarefas simulando empresa real
- Simular worker concorrente
- Investigar race condition
- Comparar execução paralela vs sequencial
- Criar mini shell simples

---

## CAMADA 3 — Redes e Comunicação (Meses 7 e 8)

### Conteúdos
- TCP x UDP
- Sockets
- DNS
- TLS
- Handshake
- Latência
- Proxies
- Load balancing

### 💼 Tarefas simulando empresa real
- Criar cliente e servidor simples
- Diagnosticar falha de conexão
- Simular timeout de rede
- Explicar caminho de uma requisição até a aplicação

---

## CAMADA 4 — Estruturas de Dados e Algoritmos (Meses 9 e 10)

### Conteúdos
- Big O
- Listas
- Hash
- Árvores
- Grafos
- BFS / DFS
- Ordenação
- Complexidade

### 💼 Tarefas simulando empresa real
- Escolher estrutura certa para problema real
- Melhorar desempenho de busca
- Comparar custo de duas abordagens
- Implementar biblioteca pequena de estruturas

---

## CAMADA 5 — Compiladores e Interpretadores (Meses 11 e 12) 🧪

### Conteúdos
- Lexer
- Parser
- AST
- Bytecode
- Máquina virtual
- Interpretadores

### 💼 Tarefas simulando empresa real
- Construir parser simples
- Interpretar expressões
- Entender como runtime executa instruções
- Explorar funcionamento de linguagem por baixo

---

# 🕒 ROTINA RECOMENDADA

## Durante a semana
- **40 min** trilha principal
- **20 min** Linux / trilha raiz / revisão

## Sábado
- Projeto prático
- Simulação de tarefa real
- Escrita de documentação

## Domingo
- Revisão leve
- Organização da próxima semana
- Atualização de progresso

---

# 📚 CERTIFICAÇÕES SUGERIDAS

## Fase 1
- LPIC-1
- Linux Foundation LFS101

## Fase 2
- Docker / containers
- AWS Cloud Practitioner ou Solutions Architect Associate

## Fase 3
- CKAD
- iSAQB Foundation

> Certificação deve servir para organizar estudo, não substituir prática real.

---

# ✅ RESULTADO FINAL ESPERADO

Ao final do plano, você deverá ser capaz de:

- Atuar como backend forte com visão sistêmica
- Operar melhor aplicações em ambiente real
- Entender banco, infraestrutura, observabilidade e segurança
- Tomar decisões arquiteturais mais maduras
- Se comunicar melhor tecnicamente
- Projetar e evoluir sistemas com mais autonomia

---

# 📄 DOCUMENTAÇÃO DO PROJETO

- [Abrir documentação em PDF](./opsflow_documentacao_projeto.pdf)
