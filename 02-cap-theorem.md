# CAP Theorem (Teorema CAP)

> Em um sistema distribuído, você não pode ter tudo. Precisa escolher onde ceder.

## O que é o CAP Theorem

Em qualquer sistema distribuído (isto é, um sistema com dados espalhados em mais de uma máquina), você só consegue garantir **2 das 3** propriedades abaixo ao mesmo tempo:

- **C - Consistency (Consistência)**: toda leitura recebe o dado mais recente que foi escrito, ou recebe um erro. Não existe "meio termo": ou você tem o dado atualizado, ou você é informado que não conseguiu.
- **A - Availability (Disponibilidade)**: toda requisição recebe uma resposta (sem erro), mesmo que essa resposta não seja o dado mais recente.
- **P - Partition Tolerance (Tolerância a partição)**: o sistema continua funcionando mesmo quando há falha de comunicação entre os nós (rede caiu, mensagem se perdeu, etc).

## Por que você sempre escolhe P

Na prática, redes falham. Cabos são cortados, pacotes se perdem, data centers ficam temporariamente isolados uns dos outros. **Tolerância a partição não é opcional** em um sistema distribuído real — ela é praticamente obrigatória.

Isso significa que, na prática, a escolha real é entre:

- **CP** (Consistência + Tolerância a partição)
- **AP** (Disponibilidade + Tolerância a partição)

Você não escolhe "não ter P". Você escolhe entre C e A quando uma partição de rede acontece.

## CP - Consistência + Tolerância a partição

Quando ocorre uma falha de rede, o sistema prefere **recusar responder** (ou dar erro/timeout) a responder com um dado potencialmente desatualizado.

**Quando usar:** sistemas onde dado errado é pior que sistema fora do ar. Exemplos:
- Sistemas bancários (saldo de conta, transferências)
- Sistemas de reserva de assentos (você não quer vender o mesmo assento duas vezes)
- Qualquer coisa que exija leitura/escrita atômica

## AP - Disponibilidade + Tolerância a partição

Quando ocorre uma falha de rede, o sistema continua respondendo, mesmo que o dado retornado não seja o mais recente. Depois que a partição se resolve, os dados são sincronizados ("eventual consistency" — veremos isso em detalhe no arquivo de cache/bancos de dados).

**Quando usar:** sistemas onde estar disponível é mais importante que ter o dado 100% atualizado na hora. Exemplos:
- Feed de rede social (não tem problema se um post demorar alguns segundos para aparecer para todo mundo)
- Contador de likes/visualizações
- Sistemas de DNS
- Carrinho de compras (geralmente é aceitável alguma inconsistência temporária)

## Tabela resumo

| | Prioriza | Sacrifica | Exemplo de uso |
|---|---|---|---|
| **CP** | Dado correto | Disponibilidade | Sistema bancário, reservas |
| **AP** | Sistema sempre responde | Consistência imediata | Redes sociais, DNS, cache distribuído |

## Como isso aparece em entrevistas

Quando você projeta um sistema distribuído, o entrevistador quer ouvir você **justificar** sua escolha entre CP e AP baseado no caso de uso. Não existe resposta "certa" universal — existe a resposta certa **para aquele contexto**.

Um erro comum é tentar dizer "quero os três" — isso mostra que você não entendeu o conceito. A resposta esperada é: "dado que aqui uma partição de rede pode acontecer, prefiro sacrificar consistência imediata em favor de disponibilidade, porque [motivo do negócio]" (ou o contrário).

## Relação com os próximos tópicos

O CAP Theorem é a base teórica para entender:
- Por que bancos NoSQL costumam escolher AP (ex: Cassandra, DynamoDB)
- Por que bancos relacionais tradicionais tendem a priorizar CP
- Os padrões de consistência que veremos no arquivo de bancos de dados (consistência forte, fraca, eventual)

## Leituras complementares (em inglês)

- [CAP theorem revisited - Robert Greiner](https://robertgreiner.com/cap-theorem-revisited/)
- [A plain english introduction to CAP theorem](http://ksat.me/a-plain-english-introduction-to-cap-theorem)

---
**Anterior:** [01 - Escalabilidade](./01-escalabilidade.md) | **Próximo:** [03 - Cache](./03-cache.md)
