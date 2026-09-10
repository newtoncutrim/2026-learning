# 🏛️ Trilha: Dev Pleno → Arquiteto de Software / DevOps

> Este documento **não substitui** o `README2.md` (Plano de 24 Meses). Ele é um **mapa de decisão**
> que conecta três coisas que você já tem espalhadas:
> 1. O roadmap genérico do Miro (fundamentos → arquiteto)
> 2. O seu plano de 24 meses (`README2.md`), que já cobre boa parte do caminho
> 3. As certificações AWS, que você começou e não sabia como encaixar
>
> Objetivo: sair de **Pleno** e chegar em **Arquiteto de Software / DevOps**, com uma ordem clara
> do "e depois da próxima certificação, o que eu faço?".

---

## 📍 Onde você está agora

- Nível: Desenvolvedor Pleno
- Já fez (ou está fazendo): **AWS Cloud Practitioner** (a mais básica)
- Próximo passo natural: **AWS Solutions Architect Associate (SAA-C03)**
- Dúvida: o que vem depois disso

A resposta curta: **depois da Associate, o caminho se bifurca em duas certificações
profissionais** (Solutions Architect Professional e DevOps Engineer Professional), e entre
uma coisa e outra você precisa construir a base de **arquitetura de software** e **prática de
DevOps** que a prova sozinha não ensina. É isso que as fases abaixo cobrem.

---

## 🧭 Visão geral das 5 fases

| Fase | Foco | Duração estimada | Certificação-alvo |
|---|---|---|---|
| 1 | Cloud + Infra como Código | 2–3 meses | AWS SAA-C03 |
| 2 | DevOps na prática (containers, K8s, pipelines) | 3–4 meses | CKA ou AWS SysOps/DevOps Associate |
| 3 | Arquitetura de Software (DDD, patterns, resiliência) | 3–4 meses | — (portfólio, ADRs) |
| 4 | Arquiteto de Soluções Cloud | 3–4 meses | AWS Solutions Architect **Professional** |
| 5 | DevOps Sênior + Liderança Técnica | 3–4 meses | AWS DevOps Engineer **Professional** |

Total: **~16–19 meses**, compatível com o ritmo de 1h/dia que você já usa no `README2.md`.
Isso substitui e reorganiza, na prática, os Meses 9 em diante do seu plano de 24 meses.

---

## 🟢 FASE 1 — Cloud e Infraestrutura como Código

**Por que agora:** você já vai estudar para o SAA-C03, então é o momento de sair da teoria da
prova e começar a construir de verdade.

### Conteúdo
- AWS SAA-C03 (VPC, EC2, S3, RDS, IAM, Load Balancers, Auto Scaling)
- Terraform (infraestrutura como código) — **não está no seu README2, é o maior gap**
- Redes: sub-redes, rotas, NAT, VPC peering (aprofunda o "AWS básico" do Mês 10)
- Git avançado aplicado a infra (branches por ambiente, PRs de infra)

### Projeto do mês
Subir uma aplicação real (pode ser a do Mês 9 do seu plano) inteiramente via Terraform,
em uma VPC própria, com banco gerenciado (RDS) e load balancer — documentado com diagrama.

### Evidência
- Repositório Terraform versionado
- Diagrama de arquitetura da VPC
- Certificação SAA-C03

---

## 🟡 FASE 2 — DevOps na Prática

**Conecta com:** Mês 9 (DevOps Essencial) e Mês 10 (Cloud e Kubernetes) do `README2.md` —
mas indo mais fundo do que "conceitos".

### Conteúdo
- Kubernetes de verdade (não só manifests básicos): Deployments, Services, Ingress, ConfigMaps/Secrets
- CI/CD completo: build → test → deploy automatizado (GitHub Actions ou GitLab CI)
- GitOps (ArgoCD ou Flux) — outro gap do seu plano atual
- Observabilidade aplicada (Mês 11): Prometheus + Grafana + CloudWatch

### Certificação
- **CKA (Certified Kubernetes Administrator)** — se quer o caminho DevOps mais forte, ou
- **AWS SysOps Administrator Associate** — se quer ficar 100% dentro do ecossistema AWS

### Projeto do mês
Pipeline completo: push no Git → build → testes → deploy automático no Kubernetes (ou ECS),
com dashboard de métricas e rollback documentado.

### Evidência
- Cluster funcional (mesmo que local, com kind/minikube, ou EKS)
- Pipeline documentada
- Certificação escolhida

---

## 🔵 FASE 3 — Arquitetura de Software

**Por que aqui e não antes:** arquitetura sem experiência operacional (fases 1 e 2) vira
teoria vazia. Agora você já sabe o que dói em produção — é a hora certa de formalizar.

Aqui entra tudo aquilo que você viu no roadmap do Miro (DDD, Clean/Hexagonal/Onion,
SOLID, Design Patterns) — a maior parte disso você já pratica como Pleno. O que falta é
**aplicar em escala de sistema**, não de classe.

### Conteúdo
- Domain-Driven Design tático e estratégico (Bounded Contexts, Agregados, Context Map)
- Clean Architecture / Hexagonal / Onion — aplicadas, não só teoria
- Padrões de resiliência (Mês 17 do README2): Circuit Breaker, Retry, Bulkhead, Timeout
- Arquitetura orientada a eventos (Mês 18): filas vs streaming (Kafka)
- **ADRs (Architecture Decision Records)** — prática que você já lista no Mês 24, comece a usar desde já
- System Design (você já tem isso pronto: `07` a `10-entrevista-*.md`) — revisite com olhar de arquiteto, não de candidato

### Projeto do mês
Pegue um dos sistemas simulados que você já escreveu (YouTube, encurtador, feed, WhatsApp)
e **reescreva a decisão arquitetural em formato de ADR real**, com trade-offs, alternativas
descartadas e critérios de decisão.

### Evidência
- 5+ ADRs escritos
- Um diagrama C4 (Contexto → Container → Componente) de um sistema real seu

---

## 🟣 FASE 4 — Arquiteto de Soluções Cloud

**Conecta com:** Mês 16 (Arquitetura Avançada) e fecha o ciclo AWS iniciado na Fase 1.

### Conteúdo
- **AWS Well-Architected Framework** (os 6 pilares: excelência operacional, segurança,
  confiabilidade, performance, otimização de custos, sustentabilidade) — isso não está em
  nenhum dos seus arquivos hoje e é exatamente o que separa "sabe AWS" de "é arquiteto AWS"
- Multi-conta e multi-região (Organizations, Control Tower)
- Segurança de arquitetura (Mês 12/21): IAM avançado, KMS, GuardDuty, Security Hub
- FinOps básico: como justificar custo de arquitetura para o negócio (conecta com Mês 23)

### Certificação
- **AWS Solutions Architect Professional (SAP-C02)**

### Projeto do mês
Desenhar (documento + diagrama) a arquitetura de migração de um sistema monolítico
para múltiplas contas AWS, com plano de disaster recovery e estimativa de custo.

### Evidência
- Certificação SAP-C02
- Documento de arquitetura com os 6 pilares do Well-Architected aplicados

---

## 🔴 FASE 5 — DevOps Sênior + Liderança Técnica

**Conecta com:** Mês 19 (SRE), Mês 20 (Liderança Técnica) e Mês 23 (Produto e Arquitetura).

### Conteúdo
- SRE avançado: error budgets, SLO como contrato entre times
- Chaos Engineering aplicado (você já cita como exploração — hora de tirar do "opcional")
- Mentoria técnica e revisão de arquitetura de terceiros (não só código)
- Comunicação de decisões técnicas para negócio (RFC/ADR para stakeholders não técnicos)

### Certificação
- **AWS DevOps Engineer Professional (DOP-C02)**

### Projeto do mês — Projeto Final da Trilha
Converge com o "Projeto Final Master" do seu Mês 24: um sistema real, projetado por você
como arquiteto, com pipeline DevOps completo, multi-conta AWS, observabilidade, resiliência
e documentação de arquitetura (ADRs + C4 + Well-Architected review).

---

## 📋 Resumo: o que já está coberto vs. o que falta

| Já coberto no seu `README2.md` | Falta adicionar |
|---|---|
| Docker, Docker Compose | **Terraform / IaC** |
| Conceitos de Cloud e K8s | **Kubernetes aplicado (CKA)** |
| Observabilidade (Prometheus/Grafana) | **GitOps (ArgoCD/Flux)** |
| System Design (mock interviews) | **AWS Well-Architected Framework** |
| Resiliência, SRE, Segurança | **Multi-conta AWS, FinOps** |
| DDD/Arquitetura (via roadmap Miro) | **ADRs e diagramas C4 em prática real** |
| Liderança técnica | **Certificações AWS Professional (SAP-C02, DOP-C02)** |

---

## 🎯 Ordem de certificações AWS recomendada

1. ✅ AWS Cloud Practitioner *(feito/em andamento)*
2. 🔜 AWS Solutions Architect Associate (SAA-C03) *(próxima)*
3. AWS SysOps Administrator Associate **ou** CKA *(Fase 2)*
4. AWS Solutions Architect Professional (SAP-C02) *(Fase 4)*
5. AWS DevOps Engineer Professional (DOP-C02) *(Fase 5)*

As duas Professional não competem entre si — Solutions Architect te dá o lado **arquiteto**,
DevOps Engineer te dá o lado **operacional/DevOps**. Como você quer os dois papéis, essa é
a combinação certa.
