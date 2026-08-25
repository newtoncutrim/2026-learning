# Load Balancer (Balanceador de Carga)

> Distribui as requisições que chegam entre vários servidores, para nenhum deles ficar sobrecarregado.

## Por que existe

Quando um único servidor não aguenta mais tráfego sozinho, a solução é ter vários servidores idênticos rodando a mesma aplicação. Mas alguém precisa decidir **para qual servidor** cada requisição vai. Esse "alguém" é o load balancer.

Principais benefícios:
- Evita sobrecarregar um único servidor
- Evita mandar tráfego para servidores que estão fora do ar (health checks)
- Elimina ponto único de falha (se um servidor cai, o load balancer redireciona para os outros)
- Permite escalar horizontalmente de forma transparente para o usuário

## Onde o load balancer fica na arquitetura

Normalmente ele fica entre o cliente (usuário) e os servidores de aplicação:

```
Cliente → Load Balancer → [Servidor 1, Servidor 2, Servidor 3, ...]
```

Mas load balancers também podem existir entre outras camadas, por exemplo entre servidores de aplicação e réplicas de banco de dados.

## Algoritmos de distribuição de tráfego

- **Round robin**: distribui em sequência, um de cada vez (1, 2, 3, 1, 2, 3...)
- **Round robin ponderado**: igual ao round robin, mas servidores mais potentes recebem mais requisições
- **Menor número de conexões ativas**: manda para o servidor que está com menos trabalho no momento
- **Baseado em sessão/cookie**: garante que o mesmo usuário sempre caia no mesmo servidor (útil quando o servidor guarda estado local — embora, como vimos, o ideal seja evitar isso)
- **Aleatório**: distribui de forma randômica

## Camada 4 vs Camada 7 (Layer 4 vs Layer 7)

Essa divisão é baseada no modelo OSI de redes:

### Load balancing na Camada 4 (transporte)

Decide para onde mandar o tráfego olhando apenas informações de rede: IP de origem, IP de destino, porta. **Não olha o conteúdo da mensagem**.

- Mais rápido (menos processamento)
- Menos flexível (não pode tomar decisões baseadas no conteúdo da requisição)

### Load balancing na Camada 7 (aplicação)

Decide para onde mandar olhando o conteúdo real da requisição: cabeçalhos HTTP, cookies, URL, tipo de conteúdo.

- Mais lento (mais processamento envolvido)
- Muito mais flexível: por exemplo, pode mandar requisições de vídeo para servidores otimizados para isso, e requisições de pagamento para servidores com mais segurança

**Na prática:** a maioria dos sistemas modernos usa load balancing de Camada 7 (ex: NGINX, HAProxy, AWS Application Load Balancer), porque a flexibilidade compensa o custo extra de processamento.

## Active-passive vs Active-active

Isso se refere a como você evita que o **próprio load balancer** seja um ponto único de falha (afinal, de nada adianta ter vários servidores de aplicação se o load balancer cair):

### Active-passive

Um load balancer fica ativo, atendendo tráfego. O outro fica em standby, monitorando via heartbeat. Se o ativo cair, o passivo assume.

### Active-active

Os dois load balancers ficam ativos ao mesmo tempo, dividindo o tráfego entre si.

## Load Balancer vs Reverse Proxy

Esses dois conceitos são frequentemente confundidos:

- **Load balancer**: faz sentido quando você tem **múltiplos** servidores fazendo a mesma coisa, e você quer distribuir carga entre eles.
- **Reverse proxy**: útil mesmo com um único servidor. Ele fica na frente do servidor, escondendo detalhes de infraestrutura, fazendo terminação SSL, compressão, cache, servindo arquivos estáticos, etc.

Ferramentas como NGINX e HAProxy fazem as duas coisas ao mesmo tempo, o que gera essa confusão.

## Desvantagens de usar load balancer

- Mais um componente de infraestrutura para gerenciar
- Pode virar gargalo se não tiver recursos suficientes
- Precisa de estratégia de alta disponibilidade para ele mesmo (senão vira ponto único de falha)

## Como isso aparece em entrevistas

Quando alguém pede para você "escalar" um sistema de um servidor único para múltiplos servidores, load balancer é praticamente sempre parte da resposta. É importante você conseguir explicar:
1. Por que precisa dele (distribuir carga, evitar SPOF)
2. Qual algoritmo de distribuição faz sentido pro seu caso
3. Se faz sentido camada 4 ou camada 7
4. Como você evita que ELE MESMO vire um ponto único de falha

## Leituras complementares (em inglês)

- [NGINX architecture](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
- [HAProxy architecture guide](http://www.haproxy.org/download/1.2/doc/architecture.txt)

---
**Anterior:** [03 - Cache](./03-cache.md) | **Próximo:** [05 - Bancos de Dados SQL vs NoSQL](./05-bancos-de-dados-sql-nosql.md)
