# 🗓️ Plano de 12 Meses — Base Técnica Completa em Engenharia de Software

> **Carga:** 1 hora por dia, todos os dias  
> **Objetivo:** Construir base sólida de engenheiro de software (backend-heavy, fullstack consciente)

---

## 🔴 MÊS 1 — Fundamentos de Engenharia de Software

### 🎯 Objetivo
Entender como a web funciona, versionar código corretamente e criar base para tudo que vem depois.

### Conteúdos
- HTTP e REST (verbo, status code, headers) [YouTube](https://www.youtube.com/watch?v=9SbUPqKEWcY) ✅ Concluído
- Git (básico → intermediário) [YouTube](https://www.youtube.com/watch?v=5NFuu4ivz-Y) 
- Expressões Regulares (Regex) [YouTube] (https://www.youtube.com/watch?v=tlVI8mV1dQY)
- Linux básico
- 12 Fatores (visão geral)

### Materiais
- 📘 *HTTP – The Definitive Guide* (capítulos iniciais)
- 📚 Git Book (documentação oficial)
- 🎓 RegexOne
- 📄 https://12factor.net
- 🐧 Linux Journey

### Exercícios
- Criar requisições HTTP com `curl`
- Regex para:
  - validar email
  - extrair números de textos
- Git:
  - criar branches
  - resolver conflitos de merge

### 🧱 Projeto do Mês
**API HTTP Playground**

**Descrição:**  
API simples apenas para praticar HTTP, REST e versionamento.

**Requisitos:**
- Endpoints GET, POST, PUT, DELETE
- Git organizado (commits claros)
- README explicando status codes usados
- Logs simples no console

---

## 🟠 MÊS 2 — Banco de Dados Relacional (PostgreSQL)

### 🎯 Objetivo
Dominar modelagem relacional e começar a pensar em performance.

### Conteúdos
- Modelagem relacional
- Normalização
- Índices
- Transações
- EXPLAIN / ANALYZE

### Materiais
- 📘 *Use The Index, Luke*
- 📚 postgresqltutorial.com
- 📄 PostgreSQL Docs (Indexes, Explain)

### Exercícios
- Criar schema relacional
- Simular queries lentas
- Criar índices e comparar performance

### 🧱 Projeto do Mês
**Sistema de Cadastro Financeiro**

**Requisitos:**
- PostgreSQL
- Tabelas normalizadas
- Queries otimizadas
- Scripts SQL versionados no Git

---

## 🟡 MÊS 3 — PL/SQL e Performance

### 🎯 Objetivo
Entender lógica no banco e impacto de performance.

### Conteúdos
- Functions
- Procedures
- Triggers
- Problema N+1
- Cache de resultados

### Materiais
- 📄 PostgreSQL Functions & Triggers
- 📘 *SQL Performance Explained*

### Exercícios
- Trigger de auditoria
- Procedure de cálculo
- Identificar e corrigir N+1

### 🧱 Projeto do Mês
**Auditoria e Histórico**

**Requisitos:**
- Trigger para histórico
- Functions de cálculo
- Queries otimizadas
- Documentação justificando uso de PL/SQL

---

## 🟢 MÊS 4 — Redis e NoSQL

### 🎯 Objetivo
Aprender cache, concorrência e quando usar NoSQL.

### Conteúdos
- Redis (cache, TTL, locks)
- MongoDB (modelagem)
- SQL vs NoSQL

### Materiais
- 📄 redis.io
- 🎓 MongoDB University (M001)
- 📄 Artigo SQL vs NoSQL (Martin Fowler)

### Exercícios
- Cache de queries
- TTL
- Lock simples com Redis

### 🧱 Projeto do Mês
**Cache Layer**

**Requisitos:**
- PostgreSQL como fonte
- Redis como cache
- Fallback automático
- Métricas de hit/miss

---

## 🔵 MÊS 5 — Arquitetura de Software

### 🎯 Objetivo
Pensar sistemas grandes antes de escrever código.

### Conteúdos
- Monólito modular
- Escala vertical vs horizontal
- DDD (conceitos)
- Bounded Context

### Materiais
- 📘 *Domain-Driven Design Quickly*
- 📘 *Clean Architecture* (Robert Martin)
- 📄 Modular Monolith (Simon Brown)

### Exercícios
- Diagramas de arquitetura
- Separação de domínios
- Identificar anti-patterns

### 🧱 Projeto do Mês
**Monólito Modular**

**Requisitos:**
- Separação clara por domínio
- Camadas bem definidas
- Dependências controladas

---

## 🟣 MÊS 6 — NestJS e Testes

### 🎯 Objetivo
Criar backend profissional e testável.

### Conteúdos
- NestJS
- Controllers e Services
- Injeção de dependência
- Testes unitários e integração

### Materiais
- 📄 NestJS Official Docs
- 🎓 NestJS Zero to Hero
- 📘 *Testing JavaScript Applications*

### Exercícios
- Serviços testáveis
- Mocks de dependência
- Testes de integração reais

### 🧱 Projeto do Mês
**API Profissional**

**Requisitos:**
- NestJS
- Testes unitários
- Testes de integração
- Coverage mínimo definido

---

## 🟤 MÊS 7 — Microserviços e Mensageria

### 🎯 Objetivo
Aprender comunicação assíncrona e desacoplamento.

### Conteúdos
- Microserviços
- RabbitMQ
- Comunicação assíncrona
- Idempotência

### Materiais
- 📘 *Building Microservices*
- 📄 RabbitMQ Tutorials

### 🧱 Projeto do Mês
**Sistema Assíncrono**

**Requisitos:**
- Serviço produtor
- Serviço consumidor
- RabbitMQ
- Retry e DLQ

---

## 🟠 MÊS 8 — Frontend Fundamental

### 🎯 Objetivo
Dominar base web para integrar bem com backend.

### Conteúdos
- JavaScript
- TypeScript
- HTML e CSS
- Git avançado

### Materiais
- 📘 *You Don’t Know JS*
- 📄 TypeScript Handbook
- 🎮 Flexbox Froggy / Grid Garden

### 🧱 Projeto do Mês
**Dashboard Web**

---

## 🔵 MÊS 9 — Angular Completo

### 🎯 Objetivo
Criar frontend moderno e integrado.

### Conteúdos
- Angular básico e avançado
- RxJS
- Autenticação
- REST e GraphQL

### Materiais
- 📄 Angular.io
- 🎓 Angular Deep Dive

### 🧱 Projeto do Mês
**Frontend Completo**

---

## 🟣 MÊS 10 — DevOps

### 🎯 Objetivo
Automatizar build, deploy e ambientes.

### Conteúdos
- Linux
- Docker
- Docker Compose
- CI/CD

### Materiais
- 📘 *Docker Deep Dive*
- 📄 GitHub Actions Docs

### 🧱 Projeto do Mês
**Pipeline Automatizado**

---

## ⚫ MÊS 11 — Cloud e Kubernetes

### 🎯 Objetivo
Rodar sistemas reais em cloud.

### Conteúdos
- AWS (EC2, S3, IAM, VPC)
- Kubernetes básico

### Materiais
- 🎓 AWS Cloud Practitioner
- 📄 Kubernetes Official Docs

### 🧱 Projeto do Mês
**Deploy em Cloud**

---

## ⚪ MÊS 12 — Observabilidade, Segurança e Finalização

### 🎯 Objetivo
Pensar como quem mantém sistemas em produção.

### Conteúdos
- Prometheus
- Grafana
- ELK / Loki
- DevSecOps
- Terraform
- n8n

### 🧱 Projeto Final
**Sistema SaaS Completo**

**Requisitos:**
- Backend escalável
- Frontend Angular
- Docker + CI/CD
- Observabilidade
- Segurança básica
- Infra as Code

---

## ✅ Resultado Final
Ao fim de 12 meses você:
- Pensa como engenheiro
- Discute arquitetura com segurança
- Constrói sistemas escaláveis
- Está pronto para nível **Sênior Técnico**

---
