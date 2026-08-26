# 🧪 Laboratório de Troubleshooting — 12 Sistemas, 12 Problemas Reais

> Baseado no `11-entrevista-troubleshooting.md`. Cada laboratório é uma aplicação **Node.js + MySQL**
> minúscula e isolada, construída **de propósito** para nascer com o defeito do tópico correspondente.
> Você sobe o ambiente, reproduz o sintoma, investiga como faria em produção, e aplica o fix —
> depois compara com a "conversa" do arquivo original.

**Stack padrão em todos os labs:** Node.js 20 + Express + `mysql2` (pool) + Docker Compose (app + MySQL 8).
Ferramentas de apoio pontuais: `autocannon`/`k6` (carga), `clinic.js`/`--prof` (profiling), `opossum` (circuit breaker).

Cada laboratório segue o mesmo formato de 5 partes, para virar rotina:

1. **Cenário de negócio** (o "porquê" da app existir)
2. **Bug injetado** (o que foi programado errado de propósito)
3. **Como reproduzir o sintoma** (script/comando que dispara o problema)
4. **Roteiro de investigação** (o que olhar, na ordem que olharia em produção)
5. **Fix esperado** (a correção estrutural, não só o patch)

---

## 🧵 LAB 1 — Fila com MySQL como broker (Tópico 1: Filas e Mensageria)

**Cenário:** Serviço de envio de e-mails. Producer insere jobs numa tabela `jobs`, um worker Node faz polling e processa.

**Arquitetura:**
- `producer.js`: insere N jobs/segundo em `jobs (id, payload, status, attempts, created_at)`
- `worker.js`: `SELECT ... FOR UPDATE SKIP LOCKED` (MySQL 8) para pegar jobs `pending`, processa, marca `done`
- Endpoint `/metrics` expondo taxa de produção vs consumo vs tamanho da fila

**Bugs injetados (um por cenário):**
1. **1.1 Atraso:** o worker faz uma query custosa (`SELECT SLEEP(0.3)` simulando dependência lenta) por mensagem — dá pra "descobrir" que aumentar workers não ajuda porque o gargalo é o banco.
2. **1.2 Poison message:** um payload específico (`{"corrompido": true}`) sempre lança exceção; sem DLQ nem limite de tentativas, o worker fica preso reprocessando a mesma linha pra sempre, bloqueando as demais (trava por `ORDER BY id LIMIT 1` sem pular a que falha).
3. **1.3 Duplicidade:** o worker marca `done` **depois** de processar, mas se o processo morre entre "processou" e "salvou o status" (`process.exit()` simulando crash em X% das vezes), a mensagem é pega de novo por outro worker → notificação duplicada.

**Fix esperado:** tentativas com contador + DLQ (tabela `jobs_dlq`), idempotência via `event_id` único (`UNIQUE KEY` em `jobs.event_id` + tabela `processed_events`), e SKIP LOCKED correto para não travar a fila inteira numa mensagem só.

---

## 🗄️ LAB 2 — Esgotamento de conexões, deadlock e réplica (Tópico 2: Banco de Dados)

**Arquitetura:** 2 containers MySQL (`db-primary`, `db-replica` com replicação nativa via binlog) + API Express de "contas e transferências".

**Bugs injetados:**
1. **2.1 Connection leak:** endpoint `/relatorio` pega conexão do pool manualmente (`pool.getConnection()`) e, no `catch` de erro, **não chama `connection.release()`** → sob carga (`autocannon`), o pool esgota e todo o resto da API trava esperando conexão livre.
2. **2.2 Deadlock:** endpoint `/transferir` trava `conta_origem` e depois `conta_destino`; um segundo endpoint `/estornar` trava na ordem **inversa** — disparando duas transações concorrentes reproduz `ER_LOCK_DEADLOCK` de forma consistente.
3. **2.3 Replication lag:** endpoint `/perfil` grava no primary e imediatamente lê no replica; um `worker` de carga em lote no primary aumenta o lag artificialmente (`INSERT` em massa) e o usuário vê o "dado antigo" por alguns segundos.

**Fix esperado:** pool com `try/finally` liberando conexão sempre; ordem consistente de locks (sempre menor ID primeiro) + retry automático em deadlock; padrão "read your own writes" (ler do primary logo após escrita própria, com uma flag de sessão/cookie).

---

## 🐢 LAB 3 — Latência de endpoint, N+1 e cadeia de timeouts (Tópico 3)

**Arquitetura:** API de "pedidos" com tabela grande (seed de ~2 milhões de linhas) + 3 serviços Express encadeados (A→B→C) para o cenário de timeout.

**Bugs injetados:**
1. **3.1:** `WHERE status = ?` numa coluna sem índice → `EXPLAIN` mostra full scan; depois, mesmo com índice, um loop faz uma query por item da lista (N+1 clássico).
2. **3.2:** p50 normal mas p99 disparado — injete, aleatoriamente em ~1% das requisições, um bloqueio síncrono da CPU (`for` pesado ou `crypto.pbkdf2Sync`) simulando "pausa de GC"/nosy neighbor.
3. **3.3:** A tem timeout de 2s no client; A→B configurado com timeout de 3s (maior que o do cliente!); B→C com 2.5s. C ocasionalmente demora 1.8s. Reproduza e implemente **deadline propagation** (passar o tempo restante via header `x-deadline-ms`).

**Fix esperado:** índice certo + reescrever N+1 como `WHERE id IN (...)`; isolar o bloqueio síncrono do event loop (worker thread ou remover o código); propagar deadline decrescente entre A→B→C.

---

## 🚦 LAB 4 — Volume alto: CPU-bound vs I/O-bound vs rate limit externo (Tópico 4)

**Arquitetura:** API simples + um "provedor externo" simulado (outro serviço Express com latência configurável e rate limit real de X req/s).

**Bugs injetados:**
1. **4.1 CPU-bound:** endpoint faz uma regex catastrófica ou `JSON.parse`/serialização de payload gigante em todo request — sob carga (`autocannon -c 200`), CPU vai a 100% mesmo escalando horizontalmente (simule 2 instâncias com `pm2`/`docker compose scale`).
2. **4.2 I/O-bound:** endpoint chama o "serviço externo" lento **de forma síncrona/bloqueante** com um pool de conexões pequeno — CPU fica ociosa (30-40%) mas todo mundo enfileira esperando thread livre.
3. **4.3 Rate limit externo:** o "provedor externo" retorna 429 acima de 100 req/s; sua API tenta mandar 150 sem controle.

**Fix esperado:** profiling (`--prof` + `node --prof-process`) para achar o hot path e mover pra worker_thread; trocar chamada bloqueante por `axios` assíncrono + aumentar pool com limite sensato; implementar token bucket próprio + fila interna + retry com backoff exponencial respeitando `Retry-After`; circuit breaker com `opossum`.

---

## 🏁 LAB 5 — Race condition, idempotência e deadlock de aplicação (Tópico 5)

**Arquitetura:** API de e-commerce simplificada: `produtos(estoque)`, `pedidos`, `pagamentos`.

**Bugs injetados:**
1. **5.1 Estoque negativo:** `POST /comprar` faz `SELECT estoque` e depois `UPDATE estoque = estoque - 1` em duas queries separadas (sem transação/lock) → disparando 50 requisições concorrentes (`autocannon`) pro mesmo produto gera estoque negativo.
2. **5.2 Cobrança duplicada:** `POST /pagamentos` sem idempotency key — simular "duplo clique" (2 requisições idênticas quase simultâneas) e ver dois registros de cobrança.
3. **5.3 Deadlock de aplicação (não do banco):** dois `Promise` que usam locks em memória (`async-mutex` ou `GET_LOCK()` do MySQL) travando recurso A e depois B numa rota, e B depois A na outra.

**Fix esperado:** `UPDATE produtos SET estoque = estoque - 1 WHERE id = ? AND estoque >= 1` atômico (ou `SELECT ... FOR UPDATE` dentro de transação); tabela `idempotency_keys` com `UNIQUE` no `Idempotency-Key` recebido via header; ordem consistente de aquisição de locks.

---

## 🧠 LAB 6 — Memory leak, payload grande e pausas de GC (Tópico 6)

**Arquitetura:** API que mantém um cache "esperto" em memória.

**Bugs injetados:**
1. **6.1 Memory leak:** cache implementado como `Map` global que **nunca expira nem tem limite de tamanho** — cada request nova adiciona uma entrada; `process.memoryUsage().heapUsed` sobe monotonicamente até o container morrer (OOM).
2. **6.2 Payload grande retido:** endpoint de upload/exportação faz `buffer` completo do arquivo em memória (`Buffer.concat`) em vez de usar streams — poucos uploads simultâneos de arquivo grande derrubam a instância.
3. **6.3 Pausas de GC:** gerar lixo de forma desnecessária (criar milhares de objetos temporários por request) e medir o impacto no p99 com `--trace-gc` / `perf_hooks`.

**Fix esperado:** cache com TTL e limite de tamanho (LRU) ou mover pra Redis; trocar buffer completo por `stream.pipeline`; reduzir alocação desnecessária (reusar objetos, evitar closures pesadas em hot path).

---

## 🧊 LAB 7 — Cache stampede, inconsistência e cache envenenado (Tópico 7)

**Arquitetura:** endpoint `/produto/:id` cacheado (pode ser um `Map` in-process por instância, ou Redis se quiser ir além do MySQL puro — ambos valem a lição).

**Bugs injetados:**
1. **7.1 Stampede:** cache com TTL curto (2s); ao expirar, uma rajada de 100 requisições simultâneas todas dão miss e batem no MySQL ao mesmo tempo — mede-se o pico de queries no banco no instante da expiração.
2. **7.2 Inconsistência entre instâncias:** rode 2 instâncias da API (`docker compose scale api=2`) cada uma com seu próprio cache **em memória** (não compartilhado) — atualizar um produto numa instância não invalida o cache da outra.
3. **7.3 Cache envenenado:** uma falha temporária do banco faz o endpoint cachear o **erro** (ou um resultado vazio) com o mesmo TTL de sucesso — mesmo depois do banco voltar, o erro continua sendo servido do cache até expirar.

**Fix esperado:** lock/single-flight (só 1 requisição recalcula, as demais esperam) para 7.1; mover cache para um store compartilhado (Redis) ou invalidação via pub/sub para 7.2; nunca cachear respostas de erro, ou usar TTL negativo bem curto, para 7.3.

---

## 🚀 LAB 8 — Deploy que causa erro e migração que quebra rollback (Tópico 8)

**Arquitetura:** duas versões da mesma API (`v1`, `v2`) atrás de um proxy simples em Node fazendo round-robin, + scripts de migration versionada (`migrate up` / `migrate down`).

**Bugs injetados:**
1. **8.1 Erro 500 pós-deploy:** `v2` introduz uma mudança que quebra uma validação e lança exceção não tratada em X% dos casos.
2. **8.2 Degradação silenciosa:** `v2` **não** dá erro, mas calcula um valor errado (ex: arredondamento de preço trocado) — só aparece comparando os dados gerados, não nos logs de erro.
3. **8.3 Rollback quebra migration:** migration `v2` faz `ALTER TABLE pedidos DROP COLUMN status_antigo` (destrutiva); ao tentar reverter para o código `v1` (que ainda espera essa coluna), a aplicação quebra porque o rollback do código não desfez o rollback do schema.

**Fix esperado:** canary/rollout gradual com métricas de erro por versão antes de 100%; testes de regressão com dados conhecidos (golden dataset) para pegar degradação silenciosa; migrations expand/contract (nunca dropar coluna no mesmo deploy que remove o uso dela no código).

---

## 🔗 LAB 9 — Falha em cascata, retry storm e contrato quebrado (Tópico 9)

**Arquitetura:** 3 serviços Express (A→B→C), sem timeout/circuit breaker inicialmente.

**Bugs injetados:**
1. **9.1 Cascata:** C fica lento (injete `SLEEP`); sem timeout em B→C, os workers/threads de B ficam presos, e A começa a falhar também por esgotamento do pool de conexões com B.
2. **9.2 Retry storm:** cliente de A faz retry **sem backoff** (retry imediato em loop) quando falha — sob a mesma falha de C, isso multiplica o tráfego e piora ainda mais a degradação (visível no gráfico de req/s).
3. **9.3 Contrato quebrado:** C muda o formato de um campo de resposta (`"total": 10` → `"total": "10.00"`) sem versionamento — B quebra silenciosamente ao fazer `total + 1` (`NaN` em cascata).

**Fix esperado:** timeout curto em cada chamada + circuit breaker (`opossum`) isolando a falha de C; backoff exponencial com jitter no client de A; versionamento de contrato (`/v1/`, `/v2/`) ou schema validation (ex: `zod`) rejeitando resposta fora do formato esperado, com alerta.

---

## 📇 LAB 10 — Trade-offs de índice (Tópico 10)

**Arquitetura:** mesma tabela grande do Lab 3, mas o foco aqui é **medir**, não só investigar.

**Roteiro (é mais um benchmark guiado que um "bug" único):**
1. **10.1** Medir tempo de 10.000 `INSERT`s antes e depois de adicionar um índice na tabela — mostrar que leitura melhora mas escrita piora.
2. **10.2** Criar índice composto `(a, b)` e rodar uma query filtrando só por `b` — mostrar no `EXPLAIN` que o índice não é usado (ordem errada) e comparar com índice `(b, a)` ou índice simples em `b`.
3. **10.3** Adicionar 8 índices na mesma tabela e medir a degradação de um job de carga em lote (`INSERT` de 100k linhas) comparado com 1 índice só.

**Fix esperado:** entender que índice não é "sempre bom"; escolher a ordem das colunas do composto conforme o padrão de consulta real; indexar só o que os `EXPLAIN`s realmente pedem.

---

## 🌐 LAB 11 — TLS expirado e DNS dividido (Tópico 11)

**Arquitetura:** aqui sai um pouco do "só MySQL" porque o tópico é de rede — mas ainda 100% Node, com um pouco de `openssl`/Docker.

**Bugs injetados:**
1. **11.1 Certificado expirando:** gerar um certificado autoassinado com validade de 2 minutos (`openssl req -x509 -days 0.0014 ...`) para um servidor HTTPS Node; a integração começa a falhar silenciosamente quando o cert expira — reproduzir e automatizar a renovação.
2. **11.2 DNS dividido:** simular via `/etc/hosts` diferentes em dois containers apontando pro mesmo hostname para IPs diferentes — um subconjunto de "usuários" (containers) resolve errado.
3. **11.3 Latência de DNS:** comparar `dns.lookup()` sem cache vs com cache (`dnscache`/manual) chamando um hostname externo repetidamente e medindo o overhead por requisição.

**Fix esperado:** automação de renovação de certificado (ou alerta de expiração N dias antes); processo de rollout de DNS gradual/validado; cache de resolução DNS na aplicação.

---

## 🔐 LAB 12 — Config divergente, rotação de chave e feature flag (Tópico 12)

**Arquitetura:** API com dois `.env` (staging/produção) e uma tabela `feature_flags` no MySQL controlando rollout percentual.

**Bugs injetados:**
1. **12.1 API key rotacionada:** um "provedor externo" mockado exige uma key; rotacione a key no provedor sem atualizar o `.env` da app — a integração quebra sem deploy nenhum ter acontecido.
2. **12.2 "Funciona no staging, não em produção":** staging usa `TIMEZONE=UTC` e um charset; produção usa outro — uma comparação de datas ou acentuação quebra só em produção.
3. **12.3 Feature flag mal configurada:** rollout de 10% implementado com `hash(user_id) % 100 < 10`, mas um bug no hash faz ele sempre cair no mesmo intervalo de `user_id`s (ex: sempre os primeiros 10% cadastrados, não uma amostra aleatória) — afeta sempre o mesmo grupo, não um grupo aleatório como o esperado.

**Fix esperado:** processo de reload de secrets sem downtime + alerta de falha de autenticação externa; paridade de configuração entre ambientes validada em CI; hash bem distribuído (ex: hash criptográfico do `user_id` + salt do flag, não um módulo ingênuo) e testes de distribuição do rollout.

---

## 📋 Como cada laboratório deve ser entregue (evidências)

Para manter o mesmo padrão do seu plano de estudos original, cada lab deveria gerar:

- `README.md` do lab: sintoma observado, hipóteses levantadas, investigação feita, causa raiz, fix aplicado
- Script ou comando que **reproduz** o bug de forma determinística (ou majoritariamente reproduzível)
- Prints/logs de antes e depois do fix (latência, EXPLAIN, gráfico de conexões, etc.)
- Testes automatizados que teriam pego o bug (quando fizer sentido)

## 🗺️ Ordem sugerida de execução

1, 5, 2 → 3, 4 → 6, 7 → 9, 8 → 10 → 12 → 11 (deixando rede por último, é o mais "fora da curva" do stack Node+MySQL).
