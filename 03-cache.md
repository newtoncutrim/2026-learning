# Cache

> Guardar uma cópia de um dado "caro de buscar" em um lugar rápido de acessar, para não ter que recalcular ou rebuscar toda vez.

## Por que cache existe

Bancos de dados e cálculos complexos são relativamente lentos. Se a mesma informação é pedida repetidamente, faz sentido guardar uma cópia dela em um lugar rápido (geralmente em memória RAM) para as próximas vezes.

Cache melhora:
- **Tempo de resposta** (latência menor)
- **Carga no banco de dados** (menos consultas repetidas)
- **Custo de infraestrutura** (menos servidores de banco necessários)

## Onde o cache pode viver

Cache não é uma coisa só — pode existir em várias camadas do sistema:

1. **Cache no cliente**: no navegador ou app do usuário (ex: cache de imagens, cache de resposta de API)
2. **CDN**: cache de conteúdo estático próximo geograficamente do usuário (veremos isso melhor no material sobre CDN/load balancer)
3. **Cache no servidor web**: reverse proxies como Varnish guardam respostas prontas
4. **Cache de aplicação**: Redis, Memcached — fica entre a aplicação e o banco de dados
5. **Cache no próprio banco de dados**: a maioria dos bancos já tem cache interno configurado

## Redis vs Memcached (os mais usados na prática)

| | Memcached | Redis |
|---|---|---|
| Estrutura de dados | Apenas chave-valor simples | Chave-valor + listas, sets, hashes, sorted sets |
| Persistência em disco | Não | Sim (opcional) |
| Complexidade | Mais simples | Mais recursos, um pouco mais complexo |
| Uso comum | Cache simples e efêmero | Cache + filas + contadores + leaderboards |

Na prática, Redis é hoje a escolha mais comum por ser mais versátil.

## Estratégias de atualização de cache

Essa é a parte que mais cai em entrevista: **quando e como o cache é atualizado**.

### 1. Cache-aside (lazy loading)

A aplicação é responsável por checar o cache antes de ir ao banco:

1. Aplicação procura o dado no cache
2. Se não encontrar (**cache miss**), busca no banco
3. Guarda o resultado no cache
4. Retorna o dado

```
função buscar_usuario(id):
    usuario = cache.get("usuario:" + id)
    se usuario não existe:
        usuario = banco.query("SELECT * FROM usuarios WHERE id = ?", id)
        cache.set("usuario:" + id, usuario)
    retorna usuario
```

**Vantagem:** só guarda em cache o que realmente é pedido (evita desperdiçar memória).
**Desvantagem:** o primeiro acesso é sempre mais lento (precisa ir ao banco); dado pode ficar desatualizado (stale) se o banco mudar sem avisar o cache.

### 2. Write-through

A aplicação escreve diretamente no cache, e o cache é responsável por replicar a escrita no banco de forma síncrona.

**Vantagem:** dado no cache nunca fica desatualizado.
**Desvantagem:** escrita fica mais lenta (precisa esperar confirmar no banco também); dados que nunca são lidos ainda ocupam espaço no cache.

### 3. Write-behind (write-back)

A aplicação escreve no cache, e a escrita no banco acontece de forma **assíncrona** depois.

**Vantagem:** escrita extremamente rápida do ponto de vista do usuário.
**Desvantagem:** risco de perder dado se o cache cair antes de sincronizar com o banco; mais complexo de implementar.

### 4. Refresh-ahead

O sistema atualiza automaticamente entradas do cache que estão prestes a expirar, antes mesmo de serem pedidas de novo.

**Vantagem:** reduz a chance do usuário pegar um cache miss.
**Desvantagem:** se a predição de "o que vai ser pedido de novo" estiver errada, você gasta recursos à toa.

## Cache invalidation — o problema mais difícil

Existe uma piada clássica em engenharia de software:

> "Existem só duas coisas difíceis em Ciência da Computação: cache invalidation e nomear variáveis."

O problema é: quando o dado original muda, como garantir que o cache também seja atualizado (ou invalidado) na hora certa? Estratégias comuns:

- **TTL (Time To Live)**: o dado expira automaticamente depois de X segundos/minutos
- **Invalidação manual/ativa**: quando o dado muda no banco, o próprio código dispara a remoção da entrada do cache
- **Eventos/pub-sub**: um serviço avisa outros serviços que um dado mudou, e eles invalidam suas cópias em cache

## O que cachear

Bons candidatos para cache:
- Sessões de usuário
- Resultados de consultas pesadas e repetitivas
- Páginas ou fragmentos de HTML renderizados
- Contadores (curtidas, visualizações)
- Resultado de cálculos custosos que não mudam com frequência

## Desvantagens gerais de usar cache

- Mais uma peça de infraestrutura para manter e monitorar
- Risco de servir dado desatualizado (stale) se a invalidação não for bem pensada
- Adiciona complexidade ao código da aplicação

## Como isso aparece em entrevistas

Quando o entrevistador pergunta "como você reduziria a carga no banco de dados?", cache costuma ser uma das primeiras respostas esperadas. O que diferencia uma resposta boa de uma ótima é você conseguir explicar **qual estratégia de cache usar e por quê**, considerando o trade-off entre consistência e performance (lembra do CAP Theorem? Aqui ele aparece de novo, em menor escala).

## Leituras complementares (em inglês)

- [AWS ElastiCache strategies](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/Strategies.html)
- [Scalable system design patterns](http://horicky.blogspot.com/2010/10/scalable-system-design-patterns.html)

---
**Anterior:** [02 - CAP Theorem](./02-cap-theorem.md) | **Próximo:** [04 - Load Balancer](./04-load-balancer.md)
