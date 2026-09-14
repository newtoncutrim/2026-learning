# Trilha 12 meses — TypeScript + DevOps

> Plano de estudos montado a partir de **280 vagas** de desenvolvimento e DevOps
> publicadas no LinkedIn para o Brasil e lidas em **13/09/2026** — 200 de dev e
> 80 de DevOps/SRE/Cloud.
>
> Cada módulo carrega o percentual de vagas que pede aquilo. Nada entrou na lista
> por gosto, e o que ficou de fora tem o motivo escrito.

**Formato:** as duas trilhas em paralelo, ~8h por semana (1h em dias úteis, 1–3h
no fim de semana).
**Saída:** 26 módulos, 4 projetos, 24 das 25 tecnologias acima de 15% de demanda
cobertas.

Quando o número diz "dev", é sobre as 200 vagas de desenvolvimento; quando diz
"DevOps", é sobre as 80 de DevOps/SRE/Cloud.

---

## Meses 1–3 · A base que aparece em toda vaga

Nada aqui é opcional: são os itens que atravessam dev e DevOps e que nenhum
processo seletivo deixa passar.

### Trilha dev

- [ ] **TypeScript a sério** — *29% das vagas de dev*
  `tsconfig` estrito, generics, tipos utilitários, narrowing, discriminated
  unions. Entrega: migrar um projeto seu de JS para TS sem um único `any`.
- [ ] **Node e API REST** — *REST em 81%, Node em 14%*
  Express primeiro para ver o que o framework esconde, depois NestJS. Rotas,
  middleware, validação com Zod, erros padronizados, autenticação JWT.
- [ ] **PostgreSQL aplicado** — *21% das vagas de dev*
  Modelagem, índices, `EXPLAIN ANALYZE`, transações e níveis de isolamento, N+1.
  Entrega: achar e corrigir três queries lentas com o plano de execução na mão.
- [ ] **Git de time** — *37% das vagas de dev*
  Rebase interativo, conflito sem pânico, PR pequeno e revisável, conventional
  commits, `bisect` para achar o commit que quebrou.

### Trilha DevOps

- [ ] **Linux de verdade** — *34% das vagas de DevOps*
  Permissões, processos, systemd, journalctl, rede com `ss`/`dig`/`tcpdump`, e o
  que fazer quando disco ou memória enchem.
- [ ] **Bash que resolve** — *31% das vagas de DevOps*
  `set -euo pipefail`, trap, argumentos, pipes, `jq`. Entrega: um script que
  coleta logs de vários serviços e devolve um resumo legível.
- [ ] **Docker** — *44% DevOps · 33% dev*
  Dockerfile multi-stage, ordem de camadas e cache, compose, volumes, redes,
  imagem pequena rodando sem root.

### Entrega do trimestre — Projeto 1: API containerizada

API em TypeScript com Postgres, tudo em `docker compose`, com migrations, seed e
um script de diagnóstico que diz por que o serviço não subiu.

---

## Meses 4–6 · Nuvem e entrega contínua

O ponto em que o código sai da sua máquina sozinho. É também o bloco que mais
pesa em filtro de currículo de DevOps.

### Trilha dev

- [ ] **Testes que seguram refactor** — *TDD em 21%, Jest em 12%*
  Unitário com Jest, integração com Testcontainers subindo Postgres de verdade,
  Supertest na API. Cobertura como sintoma, não como meta.
- [ ] **Clean Code e SOLID** — *32% das vagas de dev*
  Camadas, casos de uso, injeção de dependência, refatoração guiada por teste.
  O item mais citado depois das próprias tecnologias.
- [ ] **API além do CRUD** — *REST em 81%, GraphQL em 11%*
  Paginação, idempotência, versionamento, rate limit, OpenAPI. De GraphQL, só o
  suficiente para sustentar uma conversa.

### Trilha DevOps

- [ ] **AWS núcleo** — *61% DevOps · 36% dev*
  IAM com política mínima, VPC e security groups, EC2, S3, RDS, ECR, ECS Fargate,
  CloudWatch. Entrega: a API do projeto 1 no ar.
- [ ] **CI/CD** — *93% das vagas de DevOps*
  GitHub Actions como principal (31%) e `.gitlab-ci.yml` para ler e escrever
  (30%), porque boa parte do mercado brasileiro roda nele. Matriz, cache,
  secrets, ambientes.
- [ ] **Python para automação** — *50% das vagas de DevOps*
  Não é backend em Python: é boto3, requests, click e scripts que arrumam infra.
  É a linguagem mais pedida do lado de operações.

### Entrega do trimestre — Projeto 2: pipeline de verdade

A cada merge na `main`: roda teste, builda a imagem, publica no ECR e faz deploy
no ECS. Com ambiente de staging separado e rollback documentado.

---

## Meses 7–9 · O trimestre que fecha o maior buraco

Infra como código é o item de maior peso isolado nas vagas de DevOps e o que
falta no plano atual. Este bloco existe por causa disso.

### Trilha dev

- [ ] **Cache com Redis** — *14% das vagas de dev*
  Cache-aside, TTL, invalidação, cache stampede, lock distribuído. Medir antes e
  depois, sempre.
- [ ] **Mensageria** — *Kafka 18% · RabbitMQ 17%*
  Produtor e consumidor, retry com backoff, dead letter queue, idempotência do
  consumidor, ordenação, padrão outbox.
- [ ] **Microsserviços** — *38% das vagas de dev*
  Começando por quando **não** usar. Contratos, síncrono versus assíncrono, saga,
  e o custo operacional que ninguém menciona na vaga.

### Trilha DevOps

- [ ] **Terraform** — *69% das vagas de DevOps*
  O maior buraco do plano atual. HCL, state remoto no S3 com lock, módulos
  reaproveitáveis, variáveis por ambiente, `plan`/`apply` dentro do CI, import de
  recurso que já existe.
- [ ] **Kubernetes** — *66% DevOps · 23% dev*
  Pod, Deployment, Service, Ingress, ConfigMap e Secret, probes, requests e
  limits, HPA — e saber debugar o pod que fica em `CrashLoopBackOff`.
- [ ] **Helm** — *13% das vagas de DevOps*
  Chart próprio, values por ambiente, upgrade e rollback. Pouco conteúdo, muito
  usado na prática.

### Entrega do trimestre — Projeto 3: infra descrita, não clicada

Toda a infra do projeto 2 em Terraform, a aplicação rodando em Kubernetes com
chart Helm e dois ambientes. Destruir e recriar tudo com um comando é o critério
de aprovação.

---

## Meses 10–12 · Produção e o diferencial

Aqui você deixa de ser alguém que faz funcionar e passa a ser alguém que sabe o
que está acontecendo. E entra o item que ainda dá vantagem por chegar cedo.

### Trilha dev

- [ ] **Observabilidade no código** — *28% das vagas de dev*
  Log estruturado com id de correlação, métricas de negócio, tracing distribuído
  com OpenTelemetry. O código precisa contar a própria história.
- [ ] **IA e LLM aplicada** — *18% dev · 13% DevOps*
  Zero no plano atual. Chamar API de modelo, embeddings, RAG simples, streaming,
  avaliação e controle de custo. É o único item da lista onde chegar cedo ainda
  vale vantagem.
- [ ] **React o suficiente** — *34% das vagas de dev*
  Componentes, estado, data fetching, formulários. O objetivo é ser full stack
  contratável, não virar pessoa frontend.

### Trilha DevOps

- [ ] **Prometheus e Grafana** — *25% cada, nas vagas de DevOps*
  Métricas, PromQL, dashboard que alguém usa de madrugada, alerta que não vira
  ruído, SLI, SLO e error budget.
- [ ] **GitOps com Argo CD** — *16% das vagas de DevOps*
  Deploy declarativo, sync automático, rollback por commit. O estado do cluster
  mora no Git.
- [ ] **Segurança prática** — *19% das vagas de DevOps*
  Secrets fora do código, princípio do menor privilégio, scan de imagem e de
  dependências, LGPD no que toca o sistema.
- [ ] **Azure e GCP em duas semanas** — *40% e 30% das vagas de DevOps*
  Sem profundidade: só o mapa de equivalências com AWS e o vocabulário para não
  travar numa entrevista.

### Entrega final — Projeto 4: o sistema que você mostra

API em TypeScript com fila e cache, infra em Terraform, deploy GitOps em
Kubernetes, dashboards e alertas funcionando, e um recurso de IA que resolve algo
real. Documentado com ADRs explicando cada escolha.

---

## Transversal — cerca de 20% do tempo

Não cabe num trimestre porque não termina. Fica diluído nas 52 semanas.

| Tema | Peso nas vagas | Como tratar |
|---|---|---|
| **Inglês técnico** | 14% exigem | Documentação sem tradutor, uma conferência por semana, descrições de PR em inglês. Meia hora, três vezes por semana. |
| **System design** | 54 das 200 vagas de dev | Um estudo de caso por mês, usando as entrevistas simuladas que já estão no repositório. Escrever a solução antes de ler a resposta. |
| **Cenários de produção** | o melhor material já existente aqui | Os 30+ casos de debug e os labs do repositório, um por semana. É o que transforma conhecimento em resposta de entrevista. |
| **Ágil e Scrum** | 24% citam | Absorvido pela prática dos projetos. Não merece bloco dedicado — merece vocabulário correto. |

---

## O que ficou de fora, e por quê

Corte é a parte difícil de um plano. Cada item aqui saiu com número na mão.

- **C, Assembly, compiladores, Valgrind, gdb** — *0 menções em 280 vagas.*
  Nem C, nem Assembly, nem Valgrind aparecem uma única vez. São doze meses do
  plano anterior com retorno zero em filtro de vaga. Voltam depois, se o objetivo
  virar arquitetura de sistemas de baixo nível.
- **PHP, Laravel, Swoole, Hyperf** — *PHP 3% · Laravel 3% · Swoole 0 · Hyperf 1 vaga.*
  Continua sendo a base de trabalho e a fonte de renda — só não recebe tempo de
  estudo.
- **Java e Spring, C# e .NET** — *Java 25% · .NET 17%.*
  Stacks concorrentes da escolhida, não itens esquecidos. Se em seis meses o
  mercado da região pedir muito mais Java, a troca custa uns dois meses — o resto
  da trilha continua valendo.
- **Jenkins** — *25% das vagas de DevOps.*
  Aparece bastante, mas quem sabe GitHub Actions e GitLab CI lê um Jenkinsfile em
  uma tarde. Não vale um módulo.
- **Vue e Angular** — *5% e 13% das vagas de dev.*
  React sozinho cobre 34%. Aprender os três é diluir esforço para cobrir menos.

---

## Conferência — as 25 tecnologias acima de 15% de demanda

Tudo que aparece em mais de 15% das 280 vagas, e onde é tratado.

| Tecnologia | % do total | Onde entra |
|---|---:|---|
| REST / APIs | 63% | Dev, meses 1–3 e 4–6 |
| CI/CD | 55% | DevOps, meses 4–6 |
| Cloud (genérico) | 52% | DevOps, meses 4–6 |
| AWS | 43% | DevOps, meses 4–6 |
| Docker | 36% | DevOps, meses 1–3 |
| Observabilidade | 35% | Dev e DevOps, meses 10–12 |
| Git | 35% | Dev, meses 1–3 |
| Kubernetes | 35% | DevOps, meses 7–9 |
| Microsserviços | 32% | Dev, meses 7–9 |
| Python | 28% | DevOps, meses 4–6 |
| Azure | 27% | DevOps, meses 10–12 |
| Clean Code / SOLID | 26% | Dev, meses 4–6 |
| React | 24% | Dev, meses 10–12 |
| Terraform | 23% | DevOps, meses 7–9 |
| TypeScript | 23% | Dev, meses 1–3 |
| Scrum / Ágil | 21% | Transversal |
| PostgreSQL | 20% | Dev, meses 1–3 |
| SQL | 20% | Dev, meses 1–3 |
| **Java** | **19%** | **Fora — stack concorrente** |
| Infra como código | 19% | DevOps, meses 7–9 |
| GCP | 18% | DevOps, meses 10–12 |
| IA / LLM | 16% | Dev, meses 10–12 |
| JavaScript | 16% | Dev, meses 1–3 |
| TDD / testes | 15% | Dev, meses 4–6 |
| Kafka | 15% | Dev, meses 7–9 |

---

## Metodologia

Amostra de 280 anúncios do LinkedIn para o Brasil, coletados em 13/09/2026 a
partir de nove buscas (desenvolvedor, developer, devops, backend, frontend, full
stack, SRE, cloud engineer, engenheiro de software). A contagem é por menção no
texto da vaga — uma vaga que cita Kubernetes duas vezes conta uma.

**Vieses conhecidos:** o LinkedIn pesa para vagas remotas (39% da amostra) e
sênior (41% dos títulos contra 2% de júnior/estágio), e para empresas de médio e
grande porte. Consultorias e bancos de talentos aparecem bastante, o que infla
stacks corporativas. Ausência aqui não significa ausência no mercado.
