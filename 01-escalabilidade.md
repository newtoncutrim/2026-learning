# Escalabilidade

> Como fazer um sistema aguentar mais usuários, mais dados e mais tráfego sem cair ou ficar lento.

## O que é escalabilidade

Um sistema é **escalável** quando, ao adicionar mais recursos (servidores, memória, CPU), ele consegue atender a mais demanda de forma proporcional. Ou seja: o dobro de recursos deveria (idealmente) suportar o dobro de carga.

Uma forma simples de diferenciar dois problemas parecidos:

- **Problema de performance**: o sistema é lento mesmo com **um único usuário**.
- **Problema de escalabilidade**: o sistema é rápido com um usuário, mas **fica lento quando muitos usuários acessam ao mesmo tempo**.

Isso importa muito em entrevista: se você identificar qual dos dois está em jogo, já sabe que tipo de solução propor.

## Escalabilidade vertical vs horizontal

### Escalabilidade vertical (scale up)

Consiste em aumentar a capacidade de uma única máquina: mais RAM, CPU mais rápida, disco mais rápido.

**Vantagens:**
- Simples de implementar (não precisa mudar a arquitetura da aplicação)
- Não precisa lidar com problemas de sincronização entre múltiplos nós

**Desvantagens:**
- Existe um limite físico (não dá pra aumentar para sempre)
- Custo cresce de forma desproporcional (hardware topo de linha é muito mais caro)
- Ponto único de falha: se a máquina cair, tudo cai

### Escalabilidade horizontal (scale out)

Consiste em adicionar mais máquinas (nós) trabalhando em conjunto, em vez de uma máquina mais potente.

**Vantagens:**
- Praticamente sem limite teórico de crescimento
- Maior tolerância a falhas: se um nó cai, os outros continuam funcionando
- Geralmente mais barato usar várias máquinas commodity do que uma máquina "monstro"

**Desvantagens:**
- Mais complexidade: é preciso distribuir a carga entre as máquinas (load balancer)
- As aplicações precisam ser **stateless** (sem guardar estado local), ou você precisa de um lugar centralizado para guardar sessões, cache, etc.
- Introduz problemas de consistência de dados entre os nós

**Na prática:** a maioria dos sistemas de larga escala (Google, Netflix, Amazon) usa escalabilidade horizontal como estratégia principal, porque ela não tem teto.

## Stateless vs Stateful

Esse é um conceito chave para escalar horizontalmente:

- **Stateful**: o servidor guarda informação sobre o usuário localmente (ex: sessão na memória do próprio servidor). Isso dificulta escalar, porque o próximo request desse usuário *precisa* cair no mesmo servidor.
- **Stateless**: o servidor não guarda nada localmente. Qualquer servidor pode atender qualquer request, porque o estado (sessão, dados do usuário) fica em um lugar centralizado, como um banco de dados ou um cache compartilhado (Redis, por exemplo).

Sistemas stateless são muito mais fáceis de escalar horizontalmente, porque você pode simplesmente adicionar mais servidores idênticos atrás de um load balancer, sem se preocupar em "qual servidor tem os dados desse usuário".

## Principais estratégias de escalabilidade

Aqui está um resumo do que vamos ver em mais detalhes nos próximos arquivos:

| Técnica | O que resolve |
|---|---|
| **Load Balancer** | Distribui requisições entre vários servidores |
| **Cache** | Evita reprocessar/reconsultar a mesma coisa repetidamente |
| **CDN** | Serve conteúdo estático mais perto do usuário |
| **Replicação de banco de dados** | Distribui leituras entre várias cópias do banco |
| **Sharding / Particionamento** | Divide os dados entre vários bancos |
| **Filas assíncronas** | Processa tarefas pesadas em background, sem travar o usuário |
| **Microsserviços** | Escala partes específicas do sistema de forma independente |

## Por que isso importa em entrevistas de System Design

Quando te perguntam algo como "projete um sistema que aguente 10 milhões de usuários", o entrevistador quer ver se você sabe:

1. Identificar onde estão os gargalos (bottlenecks)
2. Escolher entre escalar vertical ou horizontalmente (quase sempre horizontal, em escala)
3. Explicar os trade-offs de cada decisão — **não existe solução perfeita, só trade-offs**

## Leituras complementares (em inglês, mas valem a pena)

- [A word on scalability - Werner Vogels (Amazon CTO)](http://www.allthingsdistributed.com/2006/03/a_word_on_scalability.html)
- [Scalability, availability, stability, patterns (slides)](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)

---
**Próximo tópico:** [02 - CAP Theorem](./02-cap-theorem.md)
