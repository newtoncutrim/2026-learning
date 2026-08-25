# Bancos de Dados: SQL vs NoSQL

> Escolher o tipo certo de banco de dados é uma das decisões mais importantes (e mais cobradas em entrevista) em system design.

## Bancos relacionais (SQL / RDBMS)

São bancos como PostgreSQL, MySQL, SQL Server. Os dados ficam organizados em tabelas com esquema (schema) fixo e bem definido, com relacionamentos entre elas.

### Propriedades ACID

Bancos relacionais garantem transações **ACID**:

- **Atomicity (Atomicidade)**: uma transação acontece por completo, ou não acontece nada. Não existe "meio caminho".
- **Consistency (Consistência)**: toda transação leva o banco de um estado válido para outro estado válido.
- **Isolation (Isolamento)**: transações rodando ao mesmo tempo não interferem umas nas outras.
- **Durability (Durabilidade)**: uma vez confirmada (commit), a transação persiste, mesmo se o sistema cair logo depois.

### Como escalar um banco relacional

Bancos SQL não escalam horizontalmente com a mesma facilidade que NoSQL, mas existem técnicas:

- **Replicação master-slave**: um servidor (master) recebe leituras e escritas; um ou mais servidores (slaves/réplicas) recebem cópias dos dados e atendem apenas leituras. Se o master cair, o sistema pode continuar em modo somente-leitura até promover um slave.
- **Replicação master-master**: dois (ou mais) servidores aceitam leituras e escritas, sincronizando entre si. Mais complexo, com risco de conflitos de escrita.
- **Federação**: dividir o banco por função/domínio (ex: um banco só de usuários, outro só de pedidos, outro só de produtos).
- **Sharding**: dividir os dados de uma mesma tabela entre vários bancos, geralmente por algum critério (ex: id do usuário, região geográfica).
- **Desnormalização**: duplicar dados de propósito para evitar joins caros, trocando espaço em disco por velocidade de leitura.

## Bancos NoSQL

Bancos não-relacionais, geralmente sem schema fixo. Favorecem escalabilidade horizontal e flexibilidade, geralmente abrindo mão de parte da consistência forte (lembra do CAP Theorem? Aqui ele volta a aparecer).

### BASE (em vez de ACID)

NoSQL costuma seguir o modelo **BASE**:

- **Basically Available**: o sistema garante disponibilidade
- **Soft state**: o estado do sistema pode mudar com o tempo, mesmo sem novas entradas
- **Eventually consistent**: o sistema vai ficar consistente eventualmente, dado tempo suficiente sem novas escritas

### Tipos de bancos NoSQL

**1. Key-Value Store** (ex: Redis, DynamoDB)
Abstração: uma tabela hash gigante. Extremamente rápido para leitura/escrita simples por chave. Bom para cache, sessões, dados que mudam com frequência.

**2. Document Store** (ex: MongoDB, CouchDB)
Guarda documentos (geralmente JSON) com estrutura flexível — documentos diferentes na mesma coleção podem ter campos diferentes. Bom quando os dados têm estrutura variável ou que muda com frequência.

**3. Wide-Column Store** (ex: Cassandra, HBase, Bigtable)
Organiza dados em famílias de colunas, muito eficiente para grandes volumes de dados e alta taxa de escrita. Muito usado em sistemas de big data.

**4. Graph Database** (ex: Neo4j)
Guarda dados como nós e relacionamentos (arestas). Excelente para dados extremamente relacionados entre si, como redes sociais (quem segue quem, amigos de amigos).

## Quando usar SQL

- Dados estruturados, com schema bem definido e estável
- Você precisa de relacionamentos complexos entre entidades
- Transações são críticas (ex: sistema financeiro, onde não pode haver inconsistência)
- Você precisa de queries complexas com joins
- O time já tem bastante experiência/ferramentas maduras para SQL (fator prático, mas real)

## Quando usar NoSQL

- Dados semi-estruturados ou schema que muda com frequência
- Volume gigantesco de dados (terabytes/petabytes)
- Necessidade de altíssima taxa de leitura/escrita (IOPS)
- Não há necessidade de joins complexos
- Exemplos típicos: dados de clickstream/logs, carrinho de compras temporário, tabelas de "hot data" acessadas com muita frequência, leaderboards

## Tabela resumo rápida

| Critério | SQL | NoSQL |
|---|---|---|
| Schema | Fixo, rígido | Flexível, dinâmico |
| Relacionamentos | Fortes (joins) | Fracos ou nenhum |
| Escalabilidade | Mais vertical, horizontal com esforço | Horizontal nativo |
| Consistência | Forte (ACID) | Eventual, geralmente (BASE) |
| Casos de uso | Sistemas financeiros, ERPs, dados estruturados | Big data, cache, redes sociais, IoT |

## Como isso aparece em entrevistas

Praticamente toda pergunta de system design em algum momento pergunta "qual banco você usaria aqui, e por quê?". A resposta ideal não é "sempre uso X" — é analisar:
1. Os dados têm estrutura fixa ou variável?
2. Preciso de relacionamentos complexos?
3. Qual o volume esperado de dados e tráfego?
4. Consistência forte é obrigatória, ou consistência eventual é aceitável?

Frequentemente a resposta certa é até **usar os dois** (ex: PostgreSQL para dados transacionais + Redis para cache + Elasticsearch para busca).

## Leituras complementares (em inglês)

- [NoSQL databases: a survey and decision guidance](https://medium.com/baqend-blog/nosql-databases-a-survey-and-decision-guidance-ea7823a822d)
- [Transitioning from RDBMS to NoSQL](https://www.infoq.com/articles/Transition-RDBMS-NoSQL/)

---
**Anterior:** [04 - Load Balancer](./04-load-balancer.md) | **Próximo:** [06 - Filas e Mensageria](./06-filas-e-mensageria.md)
