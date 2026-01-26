# 🗓️ Plano de 12 Meses — Base Técnica Completa em Engenharia de Software

> **Carga:** 1 hora por dia, todos os dias
> **Objetivo:** Construir base sólida de engenheiro de software (**backend-heavy**, fullstack consciente, **frontend por último**)

---

## 🧠 Legenda de Status (baseado no seu perfil)

* ✅ **Já domina / prática real em produção**
* 🔁 **Reforçar / aprofundar**
* 🧠 **Evolução estratégica (novo nível)**

---

## 🔴 MÊS 1 — Fundamentos de Engenharia de Software

### 🎯 Objetivo

Consolidar fundamentos já utilizados no dia a dia, agora com **consciência técnica, precisão conceitual e discurso de engenheiro**.

### Conteúdos

* HTTP e REST (verbo, status code, headers) [YouTube](https://www.youtube.com/watch?v=9SbUPqKEWcY) ✅ Concluído
* Git (básico → intermediário) 🔁 *(rebase, reflog, cherry-pick)* [YouTube](https://www.youtube.com/watch?v=5NFuu4ivz-Y) 
* Expressões Regulares (Regex) 🔁 [YouTube](https://www.youtube.com/watch?v=tlVI8mV1dQY)
* Linux básico (shell, processos, permissões) 🔁
* 12 Fatores (visão arquitetural) 🔁

### 🧱 Projeto do Mês

**API HTTP Playground**
Foco em contratos HTTP claros, versionamento limpo e decisões explícitas.

---

## 🟠 MÊS 2 — Banco de Dados Relacional (PostgreSQL)

### 🎯 Objetivo

Sair do uso cotidiano e entrar no **nível engenharia de dados relacionais e consistência**.

### Conteúdos

* Modelagem relacional 🔁
* Normalização e desnormalização consciente 🔁
* Índices (B-Tree, cardinalidade, seletividade) 🔁
* Transações e isolamento 🔁
* EXPLAIN / ANALYZE 🔁

### 🧱 Projeto do Mês

**Sistema de Cadastro Financeiro**
Modelagem previsível, queries explicáveis e decisões documentadas.

---

## 🟡 MÊS 3 — PL/SQL, Consistência e Performance

### 🎯 Objetivo

Entender **quando levar lógica para o banco** e os impactos reais dessa decisão.

### Conteúdos

* Functions e Procedures 🔁
* Triggers (uso responsável) 🔁
* Problema N+1 🔁
* Cache de resultados 🔁
* Limites de performance do banco 🧠

### 🧱 Projeto do Mês

**Auditoria e Histórico**
Documentar trade-offs entre domínio, banco e aplicação.

---

## 🟢 MÊS 4 — Redis, NoSQL e Concorrência

### 🎯 Objetivo

Pensar em **concorrência, cache, locking e escala**, não apenas CRUD.

### Conteúdos

* Redis (cache, TTL, locks distribuídos) 🔁
* MongoDB (modelagem orientada a acesso) 🧠
* SQL vs NoSQL (trade-offs reais) 🔁

### 🧱 Projeto do Mês

**Cache Layer**
Cache consciente, métricas de hit/miss e fallback previsível.

---

## 🔵 MÊS 5 — Arquitetura de Software

### 🎯 Objetivo

Transformar prática em **arquitetura defendável, evolutiva e consciente de custo**.

### Conteúdos

* Monólito Modular 🔁
* Escala vertical vs horizontal 🔁
* DDD (aplicação prática) 🔁
* Bounded Context 🔁
* Arquitetura Hexagonal (Ports & Adapters) 🧠
* Clean × Onion × Hexagonal (comparação) 🧠
* ADRs (Architecture Decision Records) 🧠
* Trade-offs arquiteturais 🧠

### 🧱 Projeto do Mês

**Monólito Modular Evolutivo**
Decisões arquiteturais documentadas com ADRs.

---

## 🟣 MÊS 6 — Backend Profissional, Domínio e Testes

### 🎯 Objetivo

Refinar design, domínio rico e **testabilidade independente de framework**.

### Conteúdos

* Arquitetura orientada a contratos 🔁
* Testes unitários e integração (TDD consciente) 🔁
* Anti-patterns de testes 🧠
* Boundary Testing 🔁
* Mutation Testing (conceito) 🧠
* Rich Domain vs Anemic Model 🧠

### 🧱 Projeto do Mês

**API Profissional**
Testes como contrato e domínio protegido.

---

## 🟤 MÊS 7 — Sistemas Distribuídos, Eventos e Mensageria

### 🎯 Objetivo

Atuar com sistemas distribuídos **sem romantização e sem dogmas**.

### Conteúdos

* Microserviços 🔁
* RabbitMQ 🔁
* Comunicação assíncrona 🔁
* Event-Driven Architecture 🧠
* Domain Events vs Integration Events 🧠
* CQRS (quando usar e quando evitar) 🧠
* Saga Pattern (orquestrada vs coreografada) 🧠
* Idempotência avançada 🧠
* Retry, DLQ e backpressure 🔁

### 🧱 Projeto do Mês

**Sistema Assíncrono Resiliente**
Foco em falha, consistência eventual e recuperação.

---

## 🟠 MÊS 8 — DevOps e Entrega Contínua

### 🎯 Objetivo

Reduzir dependência externa e **entender o ciclo completo de entrega**.

### Conteúdos

* Docker 🔁
* Docker Compose 🔁
* CI/CD 🔁
* Linux para produção 🔁
* Secrets management 🧠

### 🧱 Projeto do Mês

**Pipeline Automatizado Confiável**

---

## ⚫ MÊS 9 — Cloud, Kubernetes e System Design

### 🎯 Objetivo

Rodar sistemas reais com **consciência de custo, risco e disponibilidade**.

### Conteúdos

* AWS (EC2, S3, IAM, VPC) 🔁
* Kubernetes 🧠
* Single Point of Failure 🧠
* Autoscaling consciente 🔁
* FinOps básico 🧠

### 🧱 Projeto do Mês

**Deploy em Cloud Escalável**

---

## ⚪ MÊS 10 — Observabilidade, Confiabilidade e Segurança

### 🎯 Objetivo

Pensar como quem **mantém sistemas vivos em produção**.

### Conteúdos

* Prometheus / Grafana 🧠
* ELK / Loki 🔁
* SLO / SLA / SLI 🧠
* Error Budget 🧠
* Circuit Breaker 🔁
* Graceful Degradation 🧠
* DevSecOps 🔁
* OWASP Top 10 (API) 🧠
* Threat Modeling 🧠

---

## 🟠 MÊS 11 — Frontend Fundamental (R&D)

### 🎯 Objetivo

Conhecer frontend **apenas o suficiente para integrar, proteger e escalar o backend**.

### Conteúdos

* JavaScript 🔁
* TypeScript 🔁
* HTML e CSS 🔁

### 🧱 Projeto do Mês

**Dashboard Técnico**

---

## 🔵 MÊS 12 — Angular Avançado + Projeto Final

### 🎯 Objetivo

Fechar o ciclo fullstack com visão de produto e engenharia.

### Conteúdos

* Angular avançado 🔁
* RxJS 🔁
* Autenticação e autorização 🔁
* Integração com API 🔁

### 🧱 Projeto Final

**Sistema SaaS Completo**

**Requisitos:**

* Backend escalável e resiliente
* Frontend Angular
* Docker + CI/CD
* Observabilidade e métricas
* Segurança aplicada
* Infra as Code

---

## ✅ Resultado Final

Ao fim de 12 meses você:

* Atua como **engenheiro de software completo**
* Defende decisões arquiteturais com clareza
* Constrói e mantém sistemas distribuídos
* Opera com segurança em nível **Sênior / Staff-ready**
