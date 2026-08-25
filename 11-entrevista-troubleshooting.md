# Entrevista simulada: Troubleshooting e Debugging de Sistemas em Produção

> Diferente das entrevistas anteriores (que pedem para **projetar** um sistema do zero), esse tipo de entrevista testa como você **investiga e resolve problemas** em um sistema que já existe e está apresentando falhas. É muito comum em entrevistas para vagas de nível pleno/sênior e SRE, porque avalia raciocínio de causa-raiz, não só conhecimento teórico.
>
> O entrevistador não quer a resposta "decorada" de cara — ele quer ver você **investigar passo a passo**, levantar hipóteses, descartar possibilidades e só então propor uma solução. Cada resposta sua gera uma pergunta de aprofundamento.
>
> Organizei por tópico, com pelo menos 3 cenários diferentes em cada um, para cobrir várias formas de o mesmo componente dar problema — nem toda falha de fila é "atraso", nem toda falha de banco é "lentidão genérica".
>
> **Entrevistador:** Arquiteto de Software Sênior
> **Candidato:** Você

---

# TÓPICO 1: Filas e Mensageria

## Cenário 1.1: Fila com atraso entre Producer e Consumer

**Entrevistador:** Vamos começar com um cenário prático. Você tem um sistema com uma fila (por exemplo, RabbitMQ ou Kafka) entre um serviço que produz mensagens (producer) e um serviço que as consome (consumer). Nas últimas horas, você percebeu que a fila está crescendo — mensagens estão se acumulando e não sendo processadas na velocidade esperada. Como você começaria a investigar?

**Você:** Antes de propor qualquer solução, eu preciso entender melhor o sintoma. Posso fazer algumas perguntas primeiro?

**Entrevistador:** Pode.

**Você:** 
1. A fila está crescendo porque o producer está **produzindo mais rápido** do que o normal, ou porque o consumer está **processando mais devagar** do que o normal?
2. Isso começou de forma repentina ou é um crescimento gradual ao longo do tempo?
3. Existem múltiplas instâncias do consumer rodando, ou é uma única instância?

**Entrevistador:** Boa pergunta. Vamos supor que você não tem essa informação ainda — como você **descobriria** isso, na prática, sem eu te contar?

**Você:** Eu olharia três métricas principais em um dashboard de monitoramento (tipo Grafana/Datadog):
- **Taxa de produção** (mensagens publicadas por segundo)
- **Taxa de consumo** (mensagens processadas por segundo)
- **Tamanho da fila ao longo do tempo** (crescente, estável ou oscilando)

Se a taxa de produção estiver normal, mas a taxa de consumo caiu, o problema está no lado do consumer. Se a produção disparou e o consumo está estável, o problema é de volume, não de degradação.

**Entrevistador:** Ok, digamos que você descobriu que a taxa de produção está normal, mas a taxa de consumo caiu pela metade. O que você investigaria em seguida?

**Você:** Eu focaria no consumer e tentaria entender **por que ele está processando mais devagar**. Algumas hipóteses que eu levantaria:
1. O consumer está tendo algum erro/exceção e ficando preso em retry?
2. Alguma dependência externa que o consumer chama (banco de dados, outra API) ficou lenta, e isso está travando o processamento de cada mensagem?
3. O consumer perdeu instâncias — por exemplo, algum pod caiu e não foi religado automaticamente?
4. Teve algum deploy recente que introduziu uma regressão de performance no código do consumer?

**Entrevistador:** Boa lista de hipóteses. Como você validaria cada uma delas, uma por uma, sem ficar só especulando?

**Você:** Eu iria de forma sistemática:
- **Para a hipótese de erros/retries:** eu olharia os logs do consumer e métricas de taxa de erro. Se a taxa de exceções aumentou, isso é um forte indício.
- **Para dependência externa lenta:** eu olharia o tempo médio de processamento por mensagem (latência do consumer), não só a taxa. Se esse tempo aumentou, eu investigaria os "spans" de tracing distribuído (tipo Jaeger ou algo similar), que me mostram quanto tempo cada etapa do processamento está levando — se uma chamada específica (ex: para o banco) está consumindo a maior parte do tempo, encontrei o gargalo.
- **Para instâncias perdidas:** eu checaria o número de réplicas/pods ativos do consumer no orquestrador (Kubernetes, por exemplo) comparado com o esperado.
- **Para regressão de deploy:** eu checaria o histórico de deploys e correlacionaria o horário do início do problema com o horário de algum deploy recente.

**Entrevistador:** Digamos que você descobriu que uma dependência externa (um banco de dados que o consumer consulta) está respondendo mais devagar. Isso explica a fila crescendo. Mas o processamento continua lento mesmo depois de você aumentar o número de instâncias do consumer. Por quê isso pode estar acontecendo?

**Você:** Isso é um sinal importante. Se aumentar o número de consumers não resolve, é bem provável que o gargalo **não seja mais o consumer em si, mas sim a própria dependência** — nesse caso, o banco de dados. Adicionar mais consumers só faz com que mais conexões concorrentes cheguem no banco, o que pode inclusive **piorar** a situação, se o banco já estiver no limite de conexões ou de capacidade. Isso me levaria a investigar o banco de dados diretamente.

**Entrevistador:** Exatamente esse é o raciocínio que eu queria ver — muita gente simplesmente "escala horizontalmente" sem entender se o gargalo realmente está ali. Como você **mitigaria** esse problema imediatamente, enquanto investiga a causa raiz?

**Você:** Para mitigação de curto prazo, eu pensaria em:
1. **Back pressure**: se a fila continuar crescendo sem controle, eu limitaria a taxa de novas mensagens aceitas pelo producer, para não deixar a fila crescer indefinidamente até estourar memória
2. **Priorização**: se possível, eu separaria mensagens críticas de não-críticas em filas diferentes, para garantir que o que é urgente continue sendo processado mesmo sob degradação
3. **Alertas**: eu configuraria um alerta de "tamanho da fila acima de X" para detectar isso mais cedo da próxima vez, antes de virar um incidente maior
4. Eu **não** tentaria simplesmente aumentar o número de consumers sem entender o motivo raiz, porque, como vimos, isso pode piorar a pressão sobre uma dependência já sobrecarregada

---

## Cenário 1.2: Erro no consumer — mensagens caindo em loop de erro (poison message)

**Entrevistador:** Agora um cenário diferente. A fila não está crescendo por lentidão — está crescendo porque o consumer está **falhando repetidamente** ao processar uma mensagem específica, tentando de novo sem parar, e isso está consumindo os recursos do worker e atrasando o processamento das mensagens seguintes. Como você investigaria isso?

**Você:** Primeiro eu confirmaria esse comportamento nos logs: eu esperaria ver a **mesma mensagem** (mesmo ID de mensagem, ou mesmo conteúdo) aparecendo repetidamente nos logs de erro, com o mesmo tipo de exceção. Isso é o padrão clássico de uma **"poison message"** (ou "mensagem envenenada") — uma mensagem específica que, por algum motivo, o consumer nunca consegue processar com sucesso, mas o mecanismo de retry continua tentando de novo indefinidamente.

**Entrevistador:** E por que isso é um problema tão sério para o resto da fila, e não só para aquela mensagem específica?

**Você:** Depende de como a fila está configurada, mas em muitos sistemas (principalmente filas com **garantia de ordem**, como um tópico do Kafka particionado), se o consumer não confirma o processamento de uma mensagem, ele **não avança** para a próxima mensagem daquela partição — ele fica preso tentando processar a mesma mensagem repetidamente. Isso significa que uma única mensagem com problema pode **bloquear todas as mensagens atrás dela** na mesma partição/fila, mesmo que elas sejam perfeitamente processáveis. É um efeito muito mais grave do que parece à primeira vista.

**Entrevistador:** Como você identificaria qual mensagem específica está causando isso, e por quê ela está falhando?

**Você:** Eu olharia os logs de erro do consumer, procurando pelo ID da mensagem que aparece repetidamente. Depois, eu inspecionaria o **conteúdo** dessa mensagem específica, comparando com o formato esperado. Hipóteses comuns:
1. A mensagem tem um **formato/schema inválido** (por exemplo, um campo obrigatório faltando, ou um tipo de dado incompatível) que o código não trata corretamente, lançando uma exceção sempre que tenta processar
2. A mensagem referencia um **dado que não existe mais** (por exemplo, um `user_id` que foi deletado depois que a mensagem foi publicada), e o consumer não tem tratamento para esse caso
3. Existe um **bug específico** no código que só acontece para uma combinação particular de valores presente nessa mensagem (um "edge case" não coberto pelos testes)

**Entrevistador:** Como você resolveria isso estruturalmente, para que uma mensagem problemática não trave o restante da fila da próxima vez?

**Você:** Eu implementaria uma **Dead Letter Queue (DLQ)**: configurar um número máximo de tentativas de reprocessamento (por exemplo, 3 ou 5 tentativas) e, se a mensagem continuar falhando depois disso, ela é **automaticamente movida para uma fila separada** (a DLQ), em vez de ficar bloqueando a fila principal para sempre. Isso permite que o consumer **avance** para as próximas mensagens normalmente, enquanto a mensagem problemática fica isolada, disponível para investigação manual (ou reprocessamento automático depois de corrigido o bug) sem impactar o fluxo saudável do resto do sistema.

**Entrevistador:** E depois que a mensagem está na DLQ, qual seria seu processo para lidar com ela?

**Você:** Eu configuraria um **alerta** avisando sempre que uma mensagem cair na DLQ (para não passar despercebido), investigaria a causa raiz (validando o schema, verificando dados relacionados), corrigiria o bug no consumer se for o caso, e só então **reprocessaria manualmente** essa mensagem específica (ou um lote delas) contra o consumer já corrigido. Eu evitaria simplesmente "descartar" mensagens da DLQ sem entender o impacto de negócio de perder aquele dado.

---

## Cenário 1.3: Mensagens duplicadas ou processadas fora de ordem

**Entrevistador:** Um time reporta que alguns usuários estão recebendo **notificações duplicadas** — o mesmo evento parece estar sendo processado mais de uma vez pelo consumer. Como você investigaria?

**Você:** Primeiro eu confirmaria se a duplicação está acontecendo **na origem** (o producer está de fato publicando a mesma mensagem duas vezes) ou **no consumo** (a mensagem foi publicada uma única vez, mas o consumer processou ela mais de uma vez). Eu verificaria isso olhando os logs do producer, contando quantas vezes aquele evento específico foi publicado.

**Entrevistador:** Digamos que o producer publicou a mensagem uma única vez, mas o consumer processou duas vezes. Por que isso pode acontecer, mesmo com o producer se comportando corretamente?

**Você:** Isso geralmente acontece por causa da garantia de entrega que a maioria dos sistemas de fila oferece na prática: **"at-least-once delivery"** (pelo menos uma vez), não "exactly-once" (exatamente uma vez). Isso significa que, em certas situações, a mesma mensagem pode ser entregue mais de uma vez — por exemplo:
1. O consumer processou a mensagem com sucesso, mas **caiu (ou teve uma falha de rede) antes de conseguir confirmar (ack)** o processamento para a fila. A fila, não recebendo a confirmação, reenviar a mensagem para outro consumer, que processa de novo.
2. Em sistemas com múltiplos consumers, uma mensagem pode ser momentaneamente entregue a mais de um consumer durante um rebalanceamento de partições (por exemplo, quando um consumer entra ou sai do grupo).

**Entrevistador:** Isso significa que existe um jeito de garantir "exactly-once" de verdade e evitar esse retrabalho no código?

**Você:** Na prática, "exactly-once" de ponta a ponta é extremamente difícil (e caro) de garantir de forma genérica em sistemas distribuídos — a abordagem mais realista e amplamente usada é **assumir que duplicatas vão acontecer** e desenhar o consumer para ser **idempotente**: processar a mesma mensagem duas vezes deve produzir o mesmo resultado final que processá-la uma única vez, sem efeito colateral duplicado (por exemplo, sem enviar duas notificações, sem cobrar o cliente duas vezes).

**Entrevistador:** Como você implementaria idempotência na prática, nesse caso da notificação duplicada?

**Você:** Eu adicionaria um identificador único em cada mensagem (um `event_id` ou `message_id`), e manteria um registro (por exemplo, em uma tabela ou no Redis, com um TTL razoável) dos IDs **já processados com sucesso**. Antes de processar uma mensagem, o consumer checa se aquele ID já foi processado — se já foi, ele simplesmente **descarta a mensagem e confirma o ack**, sem repetir a ação (nesse caso, sem reenviar a notificação).

**Entrevistador:** E sobre mensagens chegando **fora de ordem** — isso é um problema parecido? Me dê um exemplo de quando isso importaria.

**Você:** É um problema relacionado, mas com uma causa diferente. Um exemplo clássico: um evento de "usuário atualizou o endereço" e depois um evento de "usuário atualizou o endereço de novo, com um valor diferente" — se esses dois eventos, por algum motivo (reprocessamento, múltiplas partições, retry), chegarem ao consumer **fora da ordem original**, o sistema pode acabar salvando o endereço **antigo** por cima do mais recente, gerando um dado incorreto. Para lidar com isso, eu incluiria um **timestamp ou número de sequência** em cada evento, e o consumer, antes de aplicar uma atualização, checaria se o evento que está processando é realmente **mais recente** do que o último estado já salvo — descartando eventos que cheguem atrasados e sejam mais antigos que o que já foi aplicado.

---

# TÓPICO 2: Banco de Dados

## Cenário 2.1: Banco de dados com degradação generalizada (conexões esgotadas)

**Entrevistador:** Você recebe um alerta de que o banco de dados principal do sistema está com performance degradada — queries que normalmente levam 10ms agora estão levando 500ms-1s. Como você investigaria?

**Você:** Primeiro, eu tentaria entender se é um problema **generalizado** (todas as queries estão lentas) ou **específico** (só algumas queries, ou só uma tabela específica). Isso muda completamente por onde eu começo a investigar.

**Entrevistador:** Vamos supor que é generalizado — praticamente todas as queries estão mais lentas, não importa qual tabela. O que você checaria primeiro?

**Você:** Se é generalizado, minha primeira suspeita não seria mais sobre uma query específica, mas sobre **recursos de infraestrutura do próprio servidor de banco**. Eu checaria, nessa ordem:

1. **CPU do servidor de banco**: está saturada?
2. **Memória**: o banco está tendo que fazer "swap" (usar disco como memória), o que é extremamente lento?
3. **I/O de disco**: a taxa de leitura/escrita em disco está no limite? Discos lentos (ou um volume de rede compartilhado congestionado, em ambientes de nuvem) são uma causa comum de degradação generalizada.
4. **Número de conexões ativas**: o banco atingiu o limite máximo de conexões simultâneas configurado, criando fila de espera por conexão?

**Entrevistador:** Digamos que você descobre que o número de conexões ativas está próximo do limite máximo configurado. Isso por si só explicaria a lentidão, mesmo que cada query individual seja rápida?

**Você:** Sim, e esse é um ponto importante de entender: se o banco está com o limite de conexões quase esgotado, novas requisições que chegam **precisam esperar em fila até uma conexão ficar livre**, mesmo que a query em si, uma vez executada, seja rapidíssima. O tempo "lento" percebido pelo usuário pode ser majoritariamente **tempo de espera por uma conexão livre**, não tempo de execução da query.

**Entrevistador:** E por que o número de conexões teria disparado de repente? Quais hipóteses você levantaria?

**Você:** Algumas hipóteses:
1. **Vazamento de conexões (connection leak)** no código da aplicação — por exemplo, uma conexão sendo aberta mas nunca devidamente fechada/devolvida ao pool após o uso, especialmente em caminhos de código que tratam exceções de forma incompleta
2. **Aumento real de tráfego** na aplicação, gerando mais conexões simultâneas de forma legítima
3. **Queries muito lentas segurando conexões por mais tempo** — se uma query normalmente rápida começou a demorar mais (por exemplo, por falta de índice), ela "segura" a conexão por mais tempo, reduzindo a disponibilidade de conexões livres para as próximas requisições — criando um efeito cascata
4. Alguém rodou uma migração de schema ou uma operação em lote pesada direto no banco de produção, consumindo conexões e recursos inesperadamente

**Entrevistador:** Como você distinguiria entre "vazamento de conexão" e "aumento real de tráfego"?

**Você:** Eu compararia o número de conexões ativas com o número de requisições por segundo chegando na aplicação. Se o tráfego está estável, mas o número de conexões abertas no banco está crescendo de forma constante e não estabiliza (mesmo com tráfego constante), isso é um forte indício de vazamento — conexões estão sendo abertas e nunca devolvidas ao pool, se acumulando com o tempo. Se, por outro lado, o número de conexões cresce proporcionalmente ao aumento real de tráfego, é simplesmente volume.

**Entrevistador:** Suponha que você confirmou: é um vazamento de conexão, introduzido por um deploy recente. Qual seria sua ação imediata versus sua correção definitiva?

**Você:** 

**Ação imediata (mitigação):** 
- Se possível, fazer **rollback** do deploy que introduziu o problema, já que é a forma mais rápida de estabilizar o sistema
- Se rollback não for viável na hora, reiniciar as instâncias da aplicação periodicamente (uma solução temporária e não ideal, mas que libera as conexões vazadas) enquanto a correção definitiva não é feita

**Correção definitiva:**
- Revisar o código do deploy recente, procurando especificamente por caminhos onde uma conexão é aberta mas pode não ser fechada corretamente — por exemplo, dentro de um bloco de tratamento de exceção que não inclui um `finally` (ou equivalente) garantindo o fechamento
- Adicionar testes automatizados ou métricas específicas de "conexões abertas vs conexões fechadas" para detectar esse tipo de regressão mais cedo, antes de ir para produção

---

## Cenário 2.2: Deadlock entre transações concorrentes

**Entrevistador:** Cenário diferente agora. Você começa a ver, nos logs do banco, erros do tipo "deadlock detected" acontecendo várias vezes por hora, e algumas transações da aplicação estão sendo abortadas com erro. Como você investigaria?

**Você:** Primeiro eu entenderia o conceito exato do que está acontecendo: um **deadlock** ocorre quando duas (ou mais) transações estão cada uma esperando por um recurso (lock) que a outra está segurando, e nenhuma consegue prosseguir — o banco, para não deixar isso travado para sempre, detecta essa situação e **aborta uma das transações** automaticamente, liberando o impasse. Eu começaria olhando os logs do banco (a maioria dos bancos relacionais registra detalhes do deadlock, incluindo quais queries e quais linhas/tabelas estavam envolvidas).

**Entrevistador:** E o que, especificamente, você procuraria nesses logs para entender a causa?

**Você:** Eu procuraria por:
1. **Quais duas transações** estavam envolvidas no deadlock
2. **Quais tabelas e linhas** cada uma estava tentando travar
3. **A ordem** em que cada transação tentou adquirir os locks

O padrão mais comum de deadlock acontece quando duas transações acessam os **mesmos dois recursos, mas em ordens diferentes**. Por exemplo: a Transação 1 trava a linha do "Pedido" e depois tenta travar a linha do "Estoque"; ao mesmo tempo, a Transação 2 trava a linha do "Estoque" primeiro e depois tenta travar a linha do "Pedido". Cada uma está esperando pelo recurso que a outra já travou — um impasse circular.

**Entrevistador:** Como você resolveria isso de forma definitiva, e não só "torcendo para não acontecer de novo"?

**Você:** A solução mais robusta é garantir uma **ordem consistente de acesso aos recursos** em todo o código da aplicação. Ou seja: se existe uma operação que precisa travar tanto "Pedido" quanto "Estoque", eu garantiria que **todo** trecho de código sempre trave essas duas tabelas/linhas na **mesma ordem** (por exemplo, sempre Pedido primeiro, depois Estoque), independentemente de qual funcionalidade está chamando essa lógica. Isso elimina a possibilidade de um ciclo de espera circular, que é a causa raiz de praticamente todo deadlock.

Outras medidas complementares:
- Reduzir o **escopo e a duração** das transações (transações mais curtas e enxutas seguram locks por menos tempo, reduzindo a janela de oportunidade para um deadlock acontecer)
- Configurar a aplicação para **capturar o erro de deadlock especificamente** e tentar a transação novamente automaticamente (retry), já que, uma vez abortada uma das transações, a outra geralmente consegue prosseguir normalmente — então um simples retry na transação abortada costuma resolver do ponto de vista do usuário

**Entrevistador:** E se você não conseguisse mudar a ordem de acesso facilmente, por exemplo porque a lógica está espalhada em vários serviços diferentes de um sistema de microsserviços — o que você faria?

**Você:** Nesse caso, eu buscaria reduzir a necessidade de travar múltiplos recursos ao mesmo tempo em primeiro lugar — por exemplo, redesenhando a operação para que cada transação afete **apenas um recurso por vez**, usando um padrão como **Saga** (onde uma operação de negócio é dividida em uma sequência de passos menores, cada um com sua própria transação local, coordenados de forma assíncrona, em vez de uma única transação distribuída travando tudo de uma vez). Isso é mais trabalhoso de implementar, mas elimina a classe inteira de problema em cenários onde a ordenação centralizada de locks não é viável.

---

## Cenário 2.3: Réplica de leitura com lag alto, servindo dados desatualizados

**Entrevistador:** Sua aplicação usa uma réplica de leitura (read replica) do banco para distribuir a carga de consultas. Um usuário reclama que, depois de atualizar o próprio perfil, a tela de confirmação continua mostrando os dados **antigos** por alguns segundos. Como você investigaria?

**Você:** Isso tem cara de **replication lag** — o atraso natural entre o banco principal (master, que recebe as escritas) e a réplica (que só recebe leituras, e é atualizada de forma assíncrona a partir do master). Eu confirmaria isso checando a métrica de **lag de replicação** (a maioria dos bancos expõe isso, geralmente em segundos ou em quantidade de dados pendentes de replicar).

**Entrevistador:** Confirmado, o lag está em torno de 3-5 segundos nesse momento, bem acima do normal de ~200ms. O que você investigaria para entender por que o lag aumentou?

**Você:** Algumas hipóteses:
1. O **volume de escritas** no master aumentou muito recentemente (mais dados para replicar significa mais tempo para a réplica "alcançar" o master)
2. A própria réplica está com **recursos insuficientes** (CPU, I/O) para processar o replication stream na mesma velocidade que o master está gerando mudanças
3. Alguma **operação pesada** (como uma migração de schema, um `ALTER TABLE`, ou um job de limpeza em massa) rodou no master recentemente, gerando um volume desproporcional de mudanças para replicar de uma vez
4. Problemas de **rede** entre o master e a réplica, causando lentidão na transmissão dos dados replicados

**Entrevistador:** E enquanto você investiga a causa raiz, como você resolveria o sintoma imediato — o usuário vendo dados desatualizados logo após uma escrita?

**Você:** Esse é fundamentalmente um problema de **consistência**: como vimos no CAP Theorem, ao usar réplicas assíncronas, eu estou trocando consistência forte por disponibilidade/escalabilidade de leitura. Para o caso específico de "usuário vê os próprios dados desatualizados logo após uma escrita", eu aplicaria o padrão de **"read your own writes"**: depois que o próprio usuário faz uma escrita, eu direcionaria as leituras **dele especificamente**, por um curto período (ou até uma confirmação de que a réplica já processou aquela escrita), para o **master** em vez da réplica — garantindo que ele sempre veja o dado mais recente que ele mesmo acabou de escrever, enquanto outros usuários (que não acabaram de escrever nada) continuam sendo servidos normalmente pela réplica, mais rápida e escalável.

**Entrevistador:** Isso resolve para o próprio usuário, mas e para outros usuários que também precisam ver aquela mudança quase imediatamente, como no caso de um chat ou um painel colaborativo?

**Você:** Nesse caso, "eventual consistency" de poucos segundos pode não ser aceitável dependendo do produto. Eu consideraria: reduzir o TTL/intervalo de replicação (se a infraestrutura permitir), usar uma estratégia de **cache invalidation ativo** (avisando explicitamente os clientes conectados de que aquele dado mudou, via WebSocket ou eventos, em vez de depender só da réplica), ou, para os casos mais críticos de consistência, aceitar o custo de ler diretamente do master mesmo que isso reduza um pouco a escalabilidade daquela operação específica — é uma decisão de trade-off caso a caso, como vimos no CAP Theorem.

---

# TÓPICO 3: Latência e Comportamento de APIs

## Cenário 3.1: Lentidão constante em um endpoint específico

**Entrevistador:** Um time te avisa que uma API específica do seu sistema está respondendo mais devagar do que o normal — o tempo de resposta subiu de 200ms para 2 segundos. Como você começaria a investigar?

**Você:** Antes de qualquer coisa, eu tentaria entender o **escopo** do problema:
1. Isso está acontecendo em **todos** os endpoints dessa API, ou só em um endpoint específico?
2. Está acontecendo para **todos** os usuários, ou só para um subconjunto (ex: usuários de uma região específica, ou com um volume de dados maior)?
3. Isso é constante, ou intermitente (picos de lentidão de vez em quando)?

**Entrevistador:** Boa. Digamos que você descobriu que é um endpoint específico, para todos os usuários, e é constante desde ontem à noite. Por onde você começaria a investigação técnica?

**Você:** Eu dividiria a investigação em camadas, de fora para dentro, tentando isolar onde o tempo está sendo gasto:

1. Primeiro, eu olharia **tracing distribuído** (se disponível) para aquele endpoint — ele me mostra quanto tempo cada etapa da requisição está levando: tempo na rede, tempo no load balancer, tempo no servidor de aplicação processando, tempo esperando o banco de dados, etc.
2. Se não tiver tracing configurado, eu adicionaria logs com timestamps em pontos-chave do código (início da requisição, antes e depois da consulta ao banco, antes e depois de chamadas a serviços externos) para descobrir manualmente onde o tempo está sendo consumido.

**Entrevistador:** Certo, digamos que o tracing aponta que a maior parte do tempo está sendo gasta numa consulta ao banco de dados. Como você prosseguiria a partir daí?

**Você:** Uma possível solução seria analisar as queries do banco de dados que esse endpoint executa, para entender se alguma delas está performando mal.

**Entrevistador:** Como, na prática, você analisaria essas queries?

**Você:** Eu usaria o comando `EXPLAIN` (ou `EXPLAIN ANALYZE`, dependendo do banco) na query em questão. Isso me mostra o **plano de execução** que o banco está usando: se ele está usando um índice ou fazendo um "full table scan" (varrendo a tabela inteira), quantas linhas ele está estimando examinar, e onde o tempo está sendo gasto dentro da própria query.

**Entrevistador:** E o que, especificamente, no resultado do `EXPLAIN`, indicaria pra você que existe um problema?

**Você:** Alguns sinais de alerta que eu procuraria:
- Um **"Seq Scan" (sequential scan / full table scan)** em uma tabela grande, quando eu esperava que houvesse um índice sendo usado — isso indica que a query está varrendo a tabela inteira em vez de usar um índice
- Um número de linhas estimado (ou realmente examinado, no caso do `EXPLAIN ANALYZE`) muito maior do que o número de linhas realmente necessário para o resultado final
- Operações de **join** custosas, especialmente do tipo "nested loop" em tabelas grandes, que podem ser bem mais lentas que um "hash join" dependendo do volume de dados

**Entrevistador:** Digamos que você encontrou um "Seq Scan" numa tabela de milhões de linhas, numa coluna que é usada no `WHERE` da query. O que isso te diz, e o que você faria?

**Você:** Isso é um forte indício de que **falta um índice** naquela coluna. Um índice permite que o banco encontre as linhas relevantes de forma muito mais rápida (parecido com o índice de um livro, em vez de ler página por página). Eu adicionaria um índice nessa coluna e rodaria o `EXPLAIN` novamente para confirmar que o plano de execução mudou para usar o índice, e que o tempo de execução caiu.

**Entrevistador:** E se o índice já existisse, mas mesmo assim a query estivesse lenta — o que mais você investigaria?

**Você:** Boa pergunta, isso me faria pensar em outras hipóteses:
1. **A tabela cresceu muito** desde a última vez que essa query foi otimizada — talvez o volume de dados simplesmente ficou grande demais para aquele padrão de consulta continuar rápido
2. **Estatísticas desatualizadas do banco** — bancos como PostgreSQL mantêm estatísticas sobre a distribuição dos dados para decidir o melhor plano de execução; se essas estatísticas estiverem desatualizadas (por exemplo, depois de uma inserção em massa), o otimizador pode escolher um plano ruim mesmo com índice disponível. Eu rodaria um `ANALYZE` na tabela para atualizar essas estatísticas.
3. **Lock/contenção**: outra transação pode estar segurando um lock na tabela ou em linhas específicas, fazendo a query esperar. Eu verificaria isso olhando as queries/transações ativas no banco no momento.
4. **Falta de recursos no próprio servidor de banco** (CPU, memória, I/O de disco no limite) — o que me levaria a olhar métricas de infraestrutura do banco, não só da query em si.

**Entrevistador:** Muito completo. E se, depois de tudo isso, você descobrir que a query em si está ótima, mas o problema é que ela é chamada **repetidamente** dentro do mesmo request (por exemplo, dentro de um loop) — o famoso problema de "N+1 queries"? Como você identificaria isso e o que faria?

**Você:** Esse é um padrão bem comum, principalmente em código que usa ORM. Eu identificaria isso olhando os logs de queries do banco durante uma única requisição — se eu ver a **mesma query, com parâmetros diferentes, sendo executada dezenas ou centenas de vezes** dentro de uma única chamada de API, é um forte indício de N+1.

Para resolver, eu reescreveria o código para buscar os dados necessários **em uma única query** (usando um `JOIN` ou um `WHERE IN (...)` com todos os IDs de uma vez), em vez de fazer uma query separada para cada item de uma lista.

---

## Cenário 3.2: Latência intermitente — p50 normal, mas p99 disparado

**Entrevistador:** Cenário diferente: a **latência média** da sua API parece normal (200ms), mas o time de produto está reclamando que "às vezes" a API demora muito, alguns segundos. Ao olhar o dashboard, você percebe que o **p50** (mediana) está em 200ms, mas o **p99** está em 4 segundos. Como você interpretaria isso, e por onde começaria a investigar?

**Você:** Isso me diz uma coisa importante logo de cara: o problema **não afeta a maioria das requisições** — se afetasse, a média (e a mediana) também estariam altas. O que está acontecendo é que uma **pequena fração** das requisições (por definição, cerca de 1% delas, já que estamos falando do p99) está demorando muito mais que o normal, e isso está sendo mascarado quando alguém olha só a média. Esse tipo de problema exige olhar especificamente para **as requisições mais lentas**, não para o agregado geral.

**Entrevistador:** Como você isolaria e investigaria justamente essas requisições lentas específicas, em vez do sistema como um todo?

**Você:** Eu filtraria os logs/traces para mostrar **apenas as requisições acima de, digamos, 1 segundo**, e procuraria por um padrão em comum entre elas:
1. Elas acontecem em um **horário específico** (por exemplo, sempre durante o job de backup noturno, ou durante um pico de tráfego)?
2. Elas envolvem um **payload maior** que o normal (por exemplo, um usuário com muito mais dados que a média)?
3. Elas estão concentradas em uma **instância específica** do servidor, ou distribuídas aleatoriamente entre todas as instâncias?

**Entrevistador:** Você descobre que essas requisições lentas estão distribuídas aleatoriamente entre todas as instâncias, sem relação com horário ou payload — parece realmente "aleatório". O que mais você investigaria?

**Você:** Quando o padrão parece "aleatório" e distribuído entre instâncias, minha suspeita se volta para causas que acontecem de forma esporádica **dentro do próprio processo da aplicação**, independente da requisição em si. As duas hipóteses mais comuns nesse cenário são:

1. **Pausas de Garbage Collector (GC)**: em linguagens com coleta de lixo automática (Java, .NET, Go, etc), o GC ocasionalmente precisa pausar a execução do programa (um "stop-the-world") para liberar memória não utilizada. Se essa pausa acontecer bem no meio do processamento de uma requisição, aquela requisição específica sofre um atraso, mesmo que o código dela não tenha nada de errado.
2. **"Noisy neighbor"** em infraestrutura compartilhada: se as instâncias rodam em máquinas virtuais ou containers compartilhando recursos físicos com outras cargas de trabalho (comum em ambientes de nuvem), um pico de uso de CPU/I/O de um "vizinho" pode ocasionalmente roubar recursos da sua aplicação por um curto período.

**Entrevistador:** Como você confirmaria se é realmente pausa de GC, e não outra coisa?

**Você:** Eu habilitaria (ou já teria habilitado como prática padrão) **logs específicos de GC**, que mostram quando cada pausa aconteceu e por quanto tempo durou. Eu correlacionaria os timestamps dessas pausas com os timestamps das requisições lentas identificadas anteriormente — se elas baterem consistentemente, confirma a hipótese.

**Entrevistador:** E se de fato for confirmado que são pausas de GC — o que você faria para reduzir esse impacto?

**Você:** Algumas opções, da mais simples à mais estrutural:
1. **Tuning dos parâmetros do garbage collector** (por exemplo, escolher um algoritmo de GC com pausas mais curtas e frequentes, em vez de pausas raras e longas — muitas linguagens oferecem opções de GC otimizadas para baixa latência)
2. **Reduzir a criação desnecessária de objetos temporários** no código (menos "lixo" gerado significa coletas menos frequentes e/ou mais rápidas)
3. Se o problema persistir e for crítico, considerar linguagens/runtimes com gerenciamento de memória diferente para os componentes mais sensíveis a latência (uma mudança bem mais estrutural, que eu só recomendaria se o impacto de negócio justificasse)

---

## Cenário 3.3: Timeout mal configurado numa cadeia de chamadas entre serviços

**Entrevistador:** Você tem uma cadeia de chamadas: o Serviço A chama o Serviço B, que chama o Serviço C. O cliente que chama o Serviço A configura um timeout de 2 segundos para toda a operação. Mesmo assim, os usuários às vezes recebem erro de timeout mesmo quando, individualmente, cada serviço (A, B e C) responde rápido o suficiente. Como você investigaria?

**Você:** Isso soa como um problema de **orçamento de timeout mal distribuído** entre as camadas da cadeia. Eu verificaria como o timeout de cada chamada individual está configurado: A→B tem seu próprio timeout, e B→C tem o seu próprio timeout também. Se a soma (ou a forma como esses timeouts interagem) não fizer sentido em relação ao limite total de 2 segundos que o cliente espera, isso pode gerar timeouts mesmo quando cada etapa isoladamente parece rápida.

**Entrevistador:** Me dê um exemplo concreto de como isso quebraria, com números.

**Você:** Por exemplo: o cliente espera até 2 segundos no total. O Serviço A, ao chamar o Serviço B, está configurado com um timeout de 3 segundos (maior que o próprio limite total do cliente!). O Serviço B, por sua vez, ao chamar o Serviço C, tem um timeout de 2.5 segundos. Se o Serviço C, ocasionalmente, demorar 1.8 segundos para responder (dentro do limite dele, mas próximo do limite), somado ao tempo de processamento do próprio B e do próprio A, a soma total facilmente ultrapassa os 2 segundos que o cliente original está disposto a esperar — mesmo que nenhum timeout **individual** tenha estourado. O cliente desiste e recebe um erro, enquanto o Serviço C continua processando "normalmente" do seu próprio ponto de vista.

**Entrevistador:** Como você corrigiria essa configuração de forma estruturada, para evitar esse tipo de inconsistência?

**Você:** Eu adotaria o conceito de **"orçamento de tempo" (deadline propagation)**: em vez de cada serviço definir seu próprio timeout de forma independente e arbitrária, o timeout total (por exemplo, os 2 segundos do cliente) seria **propagado através de toda a cadeia de chamadas** — cada serviço saberia "quanto tempo ainda resta" no momento em que recebe a requisição, e usaria esse valor (descontando uma margem de segurança para seu próprio processamento) como timeout para a próxima chamada na cadeia. Isso garante que a soma nunca ultrapasse o limite real esperado pelo cliente original, e que um timeout "estoure" de forma consistente e previsível em toda a cadeia, ao invés de gerar esse comportamento confuso.

**Entrevistador:** E se o Serviço C for uma dependência compartilhada, chamada por vários outros serviços com expectativas de timeout diferentes — como você lidaria com isso?

**Você:** Eu manteria um timeout **padrão conservador** no próprio Serviço C (um limite de segurança que ele nunca deveria ultrapassar, independente de quem o chama), mas permitiria que cada chamador enviasse, como parte da requisição (por exemplo, em um cabeçalho), o **orçamento de tempo restante** que ele tem disponível — e o Serviço C respeitaria esse valor específico daquela chamada, desde que dentro do seu próprio limite máximo de segurança. Isso combina flexibilidade (cada chamador define sua urgência) com proteção (o serviço nunca aceita esperar além do que é seguro para sua própria operação).

---

# TÓPICO 4: Alto Volume e Gargalos de Capacidade

## Cenário 4.1: API recebendo 1 milhão de requisições por minuto, com CPU saturada

**Entrevistador:** Sua API está recebendo cerca de 1 milhão de requisições por minuto — o que dá em torno de 16-17 mil requisições por segundo. Recentemente, ela começou a apresentar gargalo: aumento de latência e algumas requisições retornando erro 503 (serviço indisponível). Como você abordaria esse problema?

**Você:** Antes de sair aplicando soluções, eu preciso entender **onde exatamente** está o gargalo. "A API está lenta" pode significar coisas bem diferentes dependendo de qual camada está sobrecarregada. Eu começaria perguntando:
1. Esse volume de 1 milhão de requisições/minuto é **novo** (cresceu recentemente) ou é o volume normal que só agora começou a causar problema?
2. Os erros 503 estão vindo do load balancer, do servidor de aplicação, ou de alguma dependência (banco, cache, serviço externo)?
3. O uso de CPU, memória e I/O dos servidores de aplicação está alto, ou eles estão ociosos enquanto as requisições demoram?

**Entrevistador:** Boa. Digamos que o volume cresceu 3x nas últimas duas semanas (ex: por causa de uma campanha de marketing), e os servidores de aplicação estão com CPU em 95-100% constantemente. Como você prosseguiria?

**Você:** Isso indica que os servidores de aplicação estão genuinamente saturados de processamento — não é uma dependência externa lenta, é falta de capacidade computacional mesmo. Eu pensaria em duas frentes, uma imediata e outra estrutural:

**Imediata (mitigação rápida):**
- Escalar horizontalmente: adicionar mais instâncias do servidor de aplicação atrás do load balancer, para dividir a carga
- Verificar se existe **auto-scaling** configurado, e se os limites (máximo de instâncias) permitem esse crescimento

**Entrevistador:** E se você escalar horizontalmente e a CPU continuar em 100% em cada instância nova também, sem melhora proporcional? O que isso sugeriria?

**Você:** Isso sugeriria que o problema não é só "falta de servidores", mas que existe algo **ineficiente no código** consumindo CPU de forma desproporcional ao volume de trabalho útil sendo feito. Eu investigaria:
1. **Profiling de CPU**: rodar um profiler no processo da aplicação para ver exatamente quais funções estão consumindo mais tempo de CPU. Às vezes existe uma operação cara (ex: serialização/deserialização ineficiente de JSON, uma expressão regular mal otimizada, um loop desnecessariamente complexo) que só se torna perceptível em alto volume.
2. Verificar se existe alguma chamada síncrona e bloqueante para uma dependência lenta, fazendo os threads/processos ficarem "presos" esperando, em vez de liberar a CPU para outras requisições.
3. Verificar se o volume de logging aumentou de forma desproporcional — logging excessivo (principalmente logs síncronos, escrevendo em disco a cada requisição) pode consumir CPU e I/O de forma surpreendente em alta escala.

**Entrevistador:** Boa. E do lado da arquitetura — existe algo estrutural que você mudaria para essa API aguentar esse volume de forma mais sustentável, além de só adicionar mais servidores?

**Você:** Sim, eu pensaria em:

1. **Cache**: se uma parcela significativa dessas requisições está pedindo os mesmos dados repetidamente (o que é comum em picos de tráfego, por exemplo todo mundo acessando a mesma promoção), eu colocaria um cache (Redis, ou até cache na camada de CDN/reverse proxy) na frente, para que nem toda requisição precise ser processada do zero.
2. **Rate limiting**: para proteger o sistema de tráfego abusivo ou de um cliente específico consumindo recursos desproporcionalmente, eu implementaria limites de taxa por cliente/API key.
3. **Circuit breaker**: se a API depende de outros serviços, eu adicionaria um circuit breaker, para que, se uma dependência começar a falhar, a própria API pare de tentar chamá-la repetidamente (o que só pioraria a situação) e retorne uma resposta de fallback rapidamente, em vez de deixar requisições penduradas esperando timeout.
4. **Otimizar o payload**: verificar se a API está retornando mais dados do que o necessário, aumentando o tempo de serialização e transferência à toa.

**Entrevistador:** Você mencionou circuit breaker. Explica melhor por que isso ajudaria especificamente num cenário de alto volume.

**Você:** Quando uma dependência externa começa a ficar lenta ou falhar, sem um circuit breaker, cada requisição nova continua tentando chamar essa dependência, esperando o timeout completo (que pode ser vários segundos) antes de desistir. Com um volume de 16 mil requisições por segundo, isso significa um número gigantesco de threads/conexões ficando **presas esperando**, consumindo recursos do servidor sem produzir nenhum resultado útil — e isso frequentemente é o que causa o efeito cascata de um problema pequeno virar uma indisponibilidade total.

Um circuit breaker monitora a taxa de falha de chamadas para essa dependência e, ao ultrapassar um limiar, **"abre o circuito"**: passa a rejeitar chamadas imediatamente (ou retornar uma resposta de fallback) por um tempo, sem sequer tentar chamar a dependência com problema. Isso libera os recursos do servidor rapidamente, evita o efeito cascata, e dá tempo para a dependência se recuperar sem ser bombardeada de tentativas.

**Entrevistador:** Como você **preveniria** que isso aconteça de novo, da próxima vez que houver um pico de tráfego previsível, como uma campanha de marketing?

**Você:** Eu trabalharia em duas frentes:
1. **Testes de carga (load testing)**: simular o volume esperado antes do evento real, para descobrir os limites do sistema de forma controlada, em vez de descobrir em produção.
2. **Alertas proativos de capacidade**: monitorar tendências de crescimento de tráfego e ter alertas configurados bem antes de atingir o limite de capacidade, não só quando o sistema já está saturado.
3. Conversar com o time de negócio/marketing para que picos de tráfego planejados sejam **comunicados com antecedência** à engenharia, permitindo escalar preventivamente em vez de reativamente.

---

## Cenário 4.2: Gargalo de I/O — CPU está tranquila, mas a API mesmo assim está lenta sob volume

**Entrevistador:** Situação parecida com a anterior — alto volume de requisições, latência subindo — mas dessa vez a CPU dos servidores está em apenas 30-40% de uso, bem longe da saturação. Isso te faz descartar "falta de capacidade" como explicação?

**Você:** Não necessariamente descartar — só me faz olhar para **outro tipo de recurso** que pode estar saturado, já que CPU claramente não é o gargalo aqui. Eu verificaria, na mesma ordem de prioridade:
1. **I/O de disco**: a aplicação está fazendo muita leitura/escrita em disco (logs, arquivos temporários, cache local em disco)?
2. **I/O de rede**: quantidade de conexões abertas simultaneamente, uso de banda de rede, ou até o número de "file descriptors" disponíveis no sistema operacional (que também cobre sockets de rede)
3. **Threads/processos bloqueados**: mesmo com CPU ociosa, se todos os threads disponíveis no pool da aplicação estiverem **ocupados esperando** alguma operação de I/O (uma chamada de rede lenta, uma leitura de disco lenta), novas requisições ficam na fila mesmo com CPU sobrando — porque simplesmente não há "trabalhador" livre para atendê-las

**Entrevistador:** Você descobre que o número de threads disponíveis no pool da aplicação está no limite máximo configurado, e todos estão ocupados esperando respostas de uma API externa de terceiros. O que isso te diz?

**Você:** Isso confirma a hipótese de que o gargalo é **I/O-bound**, não CPU-bound: os threads não estão "trabalhando" (usando CPU), estão **esperando** uma resposta de rede. Com um número limitado de threads no pool, e todos ocupados esperando essa API externa, novas requisições simplesmente não conseguem ser atendidas, mesmo com CPU disponível — e isso é exatamente o tipo de situação em que "adicionar mais CPU" não ajudaria em nada.

**Entrevistador:** Como você resolveria isso, considerando que você não controla a velocidade da API externa de terceiros?

**Você:** Algumas abordagens, que eu combinaria:
1. **Chamadas assíncronas / não-bloqueantes**: em vez de um thread ficar "preso" esperando sincronamente a resposta da API externa, usar um modelo assíncrono (event loop, `async/await`, ou I/O não-bloqueante) permite que aquele mesmo thread/processo atenda **outras** requisições enquanto espera a resposta da chamada externa, em vez de ficar ocioso esperando.
2. **Aumentar o tamanho do pool de threads/conexões** — uma mitigação mais simples, mas com limite: threads não são infinitos, e aumentar demais pode trazer outros problemas (uso de memória, overhead de troca de contexto).
3. **Timeout mais agressivo** na chamada para a API externa, para que um thread não fique preso por muito tempo em uma chamada que está demorando anormalmente — liberando o recurso mais rápido, mesmo que isso signifique retornar um erro para aquela requisição específica.
4. **Cache do resultado da API externa** (se o dado não mudar com muita frequência), para reduzir a quantidade de chamadas reais que precisam esperar por essa dependência.

**Entrevistador:** Entre chamadas assíncronas e aumentar o pool de threads, qual você priorizaria, e por quê?

**Você:** Eu priorizaria o modelo assíncrono como solução estrutural, porque ele resolve o problema na raiz — desacopla "número de requisições simultâneas que consigo atender" de "número de threads do sistema operacional", que é um recurso finito e relativamente caro. Aumentar o pool de threads é uma mitigação rápida e útil no curto prazo, mas tem um teto — em algum volume ainda maior de tráfego, eu voltaria a esbarrar no mesmo problema, só que com o "limite" um pouco mais alto.

---

## Cenário 4.3: Rate limit de uma dependência externa sendo atingido

**Entrevistador:** Sua aplicação chama uma API de um provedor externo (por exemplo, um gateway de pagamento ou um serviço de envio de SMS) para completar uma parte crítica de um fluxo. Nos últimos dias, algumas dessas chamadas começaram a retornar erro **HTTP 429 (Too Many Requests)**. Como você investigaria e resolveria isso?

**Você:** Primeiro, eu confirmaria que estou de fato **atingindo o limite de taxa (rate limit)** definido pelo provedor externo — geralmente a própria resposta 429 vem acompanhada de cabeçalhos indicando o limite e quando ele é resetado (por exemplo, `Retry-After` ou cabeçalhos específicos de rate limit). Eu também checaria a documentação do provedor para confirmar qual é exatamente o limite contratado (por exemplo, X requisições por segundo/minuto).

**Entrevistador:** Digamos que você confirma: o provedor permite 100 requisições por segundo, e seu sistema, em picos, está tentando mandar 150. Quais soluções você consideraria?

**Você:** Eu pensaria em algumas camadas de solução, dos mais simples aos mais estruturais:

1. **Rate limiting do lado do meu próprio sistema**: implementar um controle de taxa **antes** de enviar a requisição para o provedor externo, garantindo que eu nunca envie mais do que o limite contratado (por exemplo, usando um algoritmo de "token bucket" ou "sliding window" para suavizar o envio).
2. **Fila com controle de vazão**: em vez de chamar a API externa diretamente e de forma síncrona a cada requisição do usuário, eu colocaria essas chamadas em uma fila interna, e um worker consumiria dessa fila numa taxa controlada, compatível com o limite do provedor — desacoplando o "quão rápido o usuário pede" de "quão rápido eu de fato envio para o terceiro".
3. **Retry com backoff exponencial**: para as requisições que já estouraram o limite e receberam 429, eu implementaria uma lógica de nova tentativa, esperando um tempo crescente entre cada tentativa (em vez de tentar imediatamente de novo, o que só pioraria o problema), respeitando o cabeçalho `Retry-After` quando disponível.

**Entrevistador:** E se, mesmo com essas medidas, o volume de negócio simplesmente exigir mais do que o limite atual contratado permite — o que você faria?

**Você:** Nesse caso, é uma conversa que sai do âmbito puramente técnico e entra em uma decisão de produto/negócio: eu levantaria os dados de volume real e projeção de crescimento, e levaria essa informação para o time responsável negociar um **aumento de limite (rate limit mais alto)** com o provedor, ou avaliar **provedores alternativos** com limites mais compatíveis com a nossa escala, dependendo da criticidade e do custo envolvido.

**Entrevistador:** Do ponto de vista de experiência do usuário, o que você faria enquanto essas requisições estão na fila esperando para respeitar o rate limit, para que o usuário não fique com a sensação de que o sistema travou?

**Você:** Eu trataria essa etapa como **assíncrona do ponto de vista do usuário**: em vez de fazer o usuário esperar a resposta síncrona da chamada externa (que pode estar momentaneamente na fila), eu responderia rapidamente algo como "seu pedido está sendo processado" e notificaria o usuário (via WebSocket, polling, ou notificação push) assim que a chamada de fato fosse concluída — reaproveitando o mesmo padrão de processamento assíncrono que vimos no tópico de filas.

---

# TÓPICO 5: Concorrência e Condições de Corrida

## Cenário 5.1: Condição de corrida (race condition) causando estoque negativo

**Entrevistador:** Você recebe relatos de que, ocasionalmente, dois pedidos diferentes de e-commerce conseguem comprar a última unidade de um produto em estoque — ou seja, o estoque fica negativo. Isso acontece raramente, e você não consegue reproduzir facilmente. Como você começaria a investigar?

**Você:** Esse sintoma — "acontece raramente e não reproduz facilmente" — é uma característica bem típica de **condição de corrida (race condition)**: um problema que só se manifesta quando duas operações concorrentes acontecem numa janela de tempo muito específica. Antes de mais nada, eu localizaria exatamente **o trecho de código** responsável por verificar e decrementar o estoque, porque é ali que a corrida provavelmente está acontecendo.

**Entrevistador:** Suponha que o código faz basicamente isso: "lê o estoque atual do banco, verifica se é maior que zero, e se for, decrementa". Onde está o problema nessa lógica?

**Você:** O problema está exatamente entre o momento de **ler** o estoque e o momento de **escrever** o novo valor — essas são duas operações separadas, não uma operação atômica única. Se duas requisições diferentes executarem essa leitura **quase ao mesmo tempo**, ambas podem ler o estoque como "1" (a última unidade), ambas concluem que podem prosseguir com a compra, e ambas decrementam para "0" — mesmo que só devesse existir uma unidade disponível. É um clássico problema de **"check-then-act"** sem controle de concorrência adequado.

**Entrevistador:** Como você resolveria isso, tecnicamente? Quais opções você consideraria, e quais os trade-offs entre elas?

**Você:** Eu vejo algumas abordagens possíveis:

1. **Lock otimista (optimistic locking)**: cada linha de estoque teria uma coluna de versão. A escrita só é aceita se a versão ainda for a mesma que foi lida — se outra transação já modificou a linha nesse meio tempo, a escrita falha e a aplicação tenta de novo. Vantagem: baixo custo quando a concorrência real é rara. Desvantagem: em cenários de altíssima concorrência (ex: um produto virando "viral"), pode gerar muitas tentativas repetidas (retries).

2. **Lock pessimista (pessimistic locking)**: a transação trava a linha específica no banco (`SELECT ... FOR UPDATE`) assim que começa a operação, impedindo que outra transação leia ou escreva aquela linha até a primeira terminar. Vantagem: garante a exclusão mútua de forma direta. Desvantagem: pode reduzir o throughput em cenários de altíssima concorrência, porque as transações ficam esperando na fila do lock.

3. **Operação atômica no próprio banco**: em vez de "ler, verificar na aplicação, escrever", eu faria isso numa única instrução SQL atômica, do tipo `UPDATE estoque SET quantidade = quantidade - 1 WHERE produto_id = ? AND quantidade > 0`. O próprio banco garante que essa operação de leitura-e-escrita acontece de forma atômica, e eu simplesmente checo quantas linhas foram afetadas (se for zero, significa que não havia estoque disponível).

**Entrevistador:** Qual dessas você escolheria para esse caso específico, de controle de estoque em e-commerce, e por quê?

**Você:** Eu escolheria a **operação atômica direto no SQL** (opção 3) como primeira tentativa, porque é a solução mais simples, com menor overhead, e resolve o problema na raiz sem precisar de locks explícitos ou lógica de retry na aplicação. Eu só migraria para lock pessimista se a lógica de negócio fosse mais complexa do que um simples decremento — por exemplo, se envolvesse múltiplas tabelas sendo alteradas de forma coordenada dentro da mesma transação.

**Entrevistador:** E como você **detectaria** e corrigiria os dados que já ficaram inconsistentes (estoque negativo) antes da correção ser aplicada?

**Você:** Eu rodaria uma consulta para identificar todos os registros com estoque negativo, avaliaria caso a caso (ou em lote, dependendo do volume) se é preciso cancelar algum dos pedidos duplicados ou simplesmente ajustar o estoque para zero e notificar o time de operações, dependendo da política de negócio da empresa para esse tipo de situação.

---

## Cenário 5.2: Processamento duplicado por falta de idempotência (double-click / retry do cliente)

**Entrevistador:** Cenário diferente de concorrência. Alguns clientes estão sendo **cobrados duas vezes** pelo mesmo pedido. Ao investigar, você percebe que não é um problema de fila (já tratamos isso no Tópico 1) — é o **próprio frontend** que, por uma falha de rede, reenviou a mesma requisição de "finalizar compra" duas vezes, e o backend processou ambas como pedidos válidos e distintos. Como você resolveria isso do lado do backend?

**Você:** Esse é fundamentalmente o mesmo princípio de idempotência que vimos no contexto de filas, só que aplicado a uma chamada de API síncrona em vez de uma mensagem assíncrona. Eu implementaria uma **chave de idempotência (idempotency key)**: o cliente (frontend) gera um identificador único para cada "intenção de compra" no momento em que o usuário clica em "finalizar", e envia esse identificador junto com a requisição. O backend mantém um registro de quais chaves de idempotência já foram processadas com sucesso.

**Entrevistador:** Como exatamente o backend usaria essa chave para evitar a duplicidade, passo a passo?

**Você:**
1. A requisição chega com a chave de idempotência no cabeçalho (ou no corpo da requisição)
2. O backend verifica se já existe um registro de **processamento em andamento ou já concluído** para aquela chave específica
3. Se **não existir** nenhum registro: o backend marca aquela chave como "em processamento", processa o pedido normalmente, e ao final salva o resultado (por exemplo, o ID do pedido criado) associado a essa chave
4. Se **já existir** um registro (seja "em processamento" ou "já concluído") para aquela mesma chave: o backend **não processa de novo** — ele simplesmente retorna o resultado já obtido anteriormente (o mesmo ID de pedido da primeira vez), como se a operação tivesse acontecido só uma vez

**Entrevistador:** E se a segunda requisição chegar exatamente enquanto a primeira **ainda está sendo processada** (não terminou ainda) — como você evita que as duas processem ao mesmo tempo, gerando de novo uma condição de corrida, só que agora na própria checagem de idempotência?

**Você:** Boa observação — essa é justamente a armadilha de implementar idempotência de forma ingênua. Eu resolveria isso da mesma forma que o Cenário 5.1: a operação de "verificar se a chave existe, e se não existir, criar o registro marcando como em processamento" precisa ser **atômica**, não um "check-then-act" separado. Na prática, eu usaria uma constraint de **unicidade** na coluna da chave de idempotência no banco (ou uma operação atômica equivalente em Redis, tipo `SETNX`), de forma que, se duas requisições tentarem criar o registro "em processamento" para a mesma chave ao mesmo tempo, o banco/Redis garante que **apenas uma** consiga, e a segunda receba um erro de violação de unicidade — que o backend interpretaria como "já existe uma operação em andamento para essa chave, aguarde ou retorne o resultado que já está sendo processado".

---

## Cenário 5.3: Threads presas por gerenciamento incorreto de locks (deadlock na aplicação, não no banco)

**Entrevistador:** Diferente do deadlock de banco de dados que vimos antes — agora o problema está **dentro do próprio processo da aplicação**. Você percebe que, periodicamente, a aplicação para de responder completamente, mesmo sem erros nos logs, e precisa ser reiniciada manualmente. Como você investigaria isso?

**Você:** "Parou de responder, sem erros nos logs, precisa reiniciar" é um padrão clássico de **deadlock entre threads dentro do próprio código da aplicação** — duas ou mais threads presas esperando, cada uma, por um lock (mutex/semáforo) que a outra está segurando, sem nenhuma delas conseguir prosseguir, e sem que isso gere necessariamente uma exceção (é simplesmente um travamento silencioso). Eu confirmaria isso tirando um **thread dump** da aplicação no momento em que ela está travada — a maioria dos runtimes oferece uma forma de "fotografar" o estado de todas as threads ativas naquele instante.

**Entrevistador:** O que você procuraria especificamente nesse thread dump para confirmar a hipótese?

**Você:** Eu procuraria por threads no estado **"BLOCKED"** ou "waiting for lock", e verificaria se existe um padrão circular: por exemplo, a Thread 1 está esperando por um lock que a Thread 2 está segurando, e a Thread 2, por sua vez, está esperando por um lock que a Thread 1 está segurando. Muitos runtimes/ferramentas de profiling inclusive **detectam e apontam esse ciclo automaticamente** no próprio thread dump, identificando explicitamente "deadlock detectado entre a Thread 1 e a Thread 2".

**Entrevistador:** Suponha que você confirmou o ciclo. Como você corrigiria o código para eliminar esse deadlock?

**Você:** O princípio é o mesmo que vimos no deadlock de banco de dados: garantir uma **ordem consistente de aquisição dos locks** em todo o código. Se existe um trecho que precisa segurar o Lock A e depois o Lock B, **todo** trecho de código que precisa desses dois locks deve sempre adquiri-los na mesma ordem (sempre A antes de B, nunca o contrário em nenhum outro lugar do código). Isso elimina matematicamente a possibilidade de um ciclo de espera circular. Eu também consideraria:
- Reduzir o **escopo** de cada lock (segurar o lock pelo menor tempo possível, liberando assim que a seção crítica termina)
- Usar, quando a linguagem/runtime oferecer, uma versão de aquisição de lock **com timeout**, em vez de uma espera bloqueante infinita — se o timeout estourar, a thread pode desistir, logar o problema, e tentar de novo, em vez de ficar presa para sempre

**Entrevistador:** Existe alguma prática de design que evite esse problema desde o início, em vez de só corrigir depois que ele aparece?

**Você:** Sim — de forma mais estrutural, eu tentaria **reduzir ao máximo o compartilhamento de estado mutável entre threads**, que é a causa raiz de precisar de locks em primeiro lugar. Padrões como passar mensagens entre componentes (em vez de compartilhar memória diretamente), ou usar estruturas de dados imutáveis sempre que possível, eliminam boa parte da necessidade de sincronização explícita — e, por consequência, eliminam boa parte do risco de deadlock. Quando locks são realmente inevitáveis, eu mantenho a lista de "quais locks existem e em que ordem eles devem ser adquiridos" documentada e centralizada, para facilitar revisão de código nesse aspecto específico.

---

# TÓPICO 6: Consumo de Memória

## Cenário 6.1: Aplicação consumindo cada vez mais memória (memory leak) até crashar

**Entrevistador:** Sua aplicação está sendo reiniciada sozinha, aproximadamente a cada 6 horas, por um "OOMKilled" (Out of Memory) no Kubernetes. Como você investigaria?

**Você:** Primeiro eu confirmaria o padrão: o uso de memória cresce **gradualmente e de forma constante** ao longo do tempo (sugerindo vazamento de memória), ou ele sobe e desce normalmente, mas de vez em quando tem um pico repentino que estoura o limite (sugerindo um evento específico, tipo uma requisição pesada, e não um vazamento contínuo)?

**Entrevistador:** É crescimento gradual e constante, quase uma linha reta subindo, até bater no limite configurado do container. O que isso te diz?

**Você:** Isso é o padrão clássico de um **vazamento de memória (memory leak)**: alguma coisa está alocando memória e nunca liberando, e isso vai se acumulando até estourar o limite. Eu investigaria:
1. **Heap dump / profiling de memória**: tirar um "snapshot" da memória da aplicação em produção (ou em um ambiente que reproduza o problema) para ver **o que exatamente** está ocupando o espaço — que tipos de objetos, quantas instâncias, e se esse número só cresce com o tempo.
2. Correlacionar o início do crescimento de memória com um deploy recente, procurando por mudanças de código que possam ter introduzido o vazamento.
3. Verificar se existem **caches locais sem limite de tamanho** (in-memory caches sem TTL ou sem limite de entradas) que crescem indefinidamente conforme novos dados chegam.
4. Verificar se existem **conexões, listeners ou event handlers sendo registrados repetidamente e nunca removidos** — um padrão comum em código que se inscreve em eventos dentro de um loop ou a cada requisição, sem cancelar a inscrição depois.

**Entrevistador:** Digamos que o heap dump mostra um número enorme de objetos de um tipo específico, muito maior do que o esperado, e esse número não para de crescer. Como você rastrearia a origem exata desses objetos no código?

**Você:** Eu usaria o próprio profiler de memória para ver as **referências** que estão mantendo esses objetos vivos — ou seja, "quem ainda está segurando uma referência para esse objeto, impedindo o coletor de lixo (garbage collector) de liberá-lo". Isso geralmente aponta direto para a estrutura de dados culpada (por exemplo, uma lista ou mapa estático que só cresce, nunca remove itens). A partir daí, eu vou no código-fonte daquela estrutura e procuro o ponto onde itens são adicionados, mas não há lógica correspondente de remoção.

**Entrevistador:** E se não for um vazamento de memória "verdadeiro", mas sim a aplicação estando configurada com um limite de memória baixo demais para o que ela realmente precisa? Como você diferenciaria isso de um vazamento real?

**Você:** Eu observaria o comportamento do uso de memória **depois de estabilizar sob carga constante**. Se for apenas uma questão de dimensionamento (limite baixo), a memória sobe até um platô e se estabiliza ali, oscilando dentro de uma faixa razoável, sem continuar subindo indefinidamente. Se for um vazamento real, a memória **nunca estabiliza** — continua subindo mesmo com carga constante, até bater no limite e a aplicação cair. Esse padrão de "nunca estabilizar" é o que diferencia os dois casos.

**Entrevistador:** Qual seria sua mitigação imediata enquanto a causa raiz não é corrigida?

**Você:** Eu configuraria um **restart programado e controlado** da aplicação em um horário de baixo tráfego (bem diferente de deixar ela crashar sozinha em produção sob carga, o que pode derrubar requisições de usuários reais no meio). Isso não é uma correção, é só uma forma de manter o sistema estável e previsível enquanto a causa raiz é corrigida com calma.

---

## Cenário 6.2: Payloads grandes retidos em memória sobrecarregam a aplicação (sem ser um "leak")

**Entrevistador:** Diferente do cenário anterior: dessa vez, o uso de memória não cresce de forma constante e infinita — ele sobe rapidamente e de forma abrupta sempre que um usuário específico faz upload de um arquivo grande (por exemplo, um CSV de vários gigabytes), chegando perto do limite do container só com aquele único upload. Como você investigaria e resolveria isso?

**Você:** Diferente de um memory leak (que é um acúmulo gradual e permanente), isso parece ser um problema de **como um único request grande está sendo processado** — provavelmente o arquivo inteiro está sendo **carregado na memória de uma vez só** antes de ser processado, em vez de ser tratado de forma incremental. Eu confirmaria isso olhando o código responsável por receber o upload: ele está lendo o arquivo inteiro para um array/buffer na memória (por exemplo, um `readAll()` ou equivalente) antes de processar, ou está processando em pedaços (streaming)?

**Entrevistador:** Confirmado: o código lê o arquivo inteiro para a memória antes de processar linha por linha. Como você corrigiria isso?

**Você:** Eu reescreveria o processamento para usar **streaming**: em vez de carregar o arquivo inteiro na memória de uma vez, o código leria e processaria o arquivo **em pedaços menores** (por exemplo, linha por linha, ou em blocos de um tamanho fixo), descartando cada pedaço da memória assim que ele já foi processado. Isso significa que, independente do tamanho total do arquivo (seja 10MB ou 10GB), o uso de memória da aplicação permanece **praticamente constante**, proporcional ao tamanho do pedaço processado por vez, não ao tamanho do arquivo inteiro.

**Entrevistador:** Existe algum trade-off ou dificuldade extra em processar dessa forma incremental, comparado a simplesmente carregar tudo de uma vez?

**Você:** Sim — processamento em streaming costuma ser um pouco mais complexo de implementar, principalmente se a lógica de negócio precisar de informação que depende do arquivo **inteiro** (por exemplo, calcular uma média geral, ou validar relacionamentos entre linhas distantes umas das outras no arquivo). Nesses casos, eu preciso pensar em uma estratégia de processamento em duas passadas, ou acumular apenas os dados agregados estritamente necessários (por exemplo, uma soma e uma contagem, em vez de guardar cada linha individual), mantendo o uso de memória sob controle mesmo quando a lógica de negócio exige alguma visão agregada do arquivo completo.

**Entrevistador:** E como mitigação de curto prazo, antes de reescrever o processamento para streaming — o que você faria para não deixar a aplicação vulnerável a esse tipo de upload enquanto isso?

**Você:** Eu adicionaria um **limite máximo de tamanho de arquivo aceito no upload**, rejeitando de forma explícita e com uma mensagem clara qualquer arquivo acima desse limite, até que a correção estrutural (streaming) esteja implementada. Isso evita que um único upload grande derrube a aplicação para todos os outros usuários, mesmo que isso signifique, temporariamente, impedir esse caso de uso específico para arquivos muito grandes.

---

## Cenário 6.3: Pausas de Garbage Collector afetando a latência sob alta carga

**Entrevistador:** Sua aplicação está rodando bem na maior parte do tempo, mas durante os horários de pico de tráfego, você percebe "engasgos" perceptíveis — a aplicação, por frações de segundo, parece "congelar" completamente antes de voltar ao normal, afetando todas as requisições em andamento naquele momento. Como você investigaria isso?

**Você:** "Congela por um instante, afeta todas as requisições ao mesmo tempo, e volta ao normal" é um padrão bem característico de uma **pausa de Garbage Collector do tipo "stop-the-world"** — um momento em que o runtime da linguagem precisa parar completamente a execução de todas as threads da aplicação para fazer a limpeza de memória, e só libera a execução depois que termina. Eu confirmaria isso habilitando (se ainda não estiver habilitado) **logs detalhados de GC**, que mostram quando cada pausa aconteceu e por quanto tempo durou, e correlacionaria esses horários com os "engasgos" percebidos.

**Entrevistador:** Confirmado que são pausas de GC, e que elas ficam mais frequentes e mais longas justamente durante os picos de tráfego. Por que o volume de tráfego afetaria diretamente a frequência e duração dessas pausas?

**Você:** Faz sentido: mais tráfego geralmente significa mais objetos sendo criados na memória por segundo (mais requisições sendo processadas simultaneamente, cada uma alocando seus próprios objetos temporários). Isso preenche o heap mais rapidamente, forçando o coletor de lixo a rodar com **mais frequência**. Além disso, se o heap estiver com mais objetos "vivos" no momento da coleta (por exemplo, mais requisições em andamento simultaneamente, cada uma com seus dados ainda em uso), a própria coleta pode demorar **mais tempo** para completar, já que precisa examinar mais objetos para decidir o que pode ser liberado.

**Entrevistador:** Quais opções você consideraria para reduzir esse impacto, sem necessariamente reescrever a aplicação inteira?

**Você:** Eu consideraria, em ordem de esforço crescente:
1. **Tuning dos parâmetros do coletor de lixo**: a maioria dos runtimes modernos oferece diferentes algoritmos de GC, alguns otimizados especificamente para **latência baixa e previsível** (pausas mais curtas e frequentes) em vez de throughput máximo (pausas mais raras, porém mais longas). Trocar para um algoritmo otimizado para latência costuma reduzir bastante o impacto percebido pelo usuário, mesmo sem mudar uma linha do código da aplicação.
2. **Aumentar o tamanho do heap disponível**: isso dá "mais espaço de manobra" antes que uma coleta seja necessária, mas é uma solução com limites — mais heap também pode significar coletas eventualmente mais demoradas quando finalmente acontecem, dependendo do algoritmo.
3. **Reduzir a criação desnecessária de objetos temporários no código** (por exemplo, evitar concatenações de string repetidas em loops, reutilizar buffers em vez de alocar novos a cada requisição) — isso ataca a causa raiz do volume de "lixo" gerado, reduzindo a frequência necessária de coleta.

**Entrevistador:** Se, mesmo depois desse tuning, a latência ainda precisar ser extremamente previsível (por exemplo, para um sistema de trading de altíssima frequência) — o que você consideraria como próximo passo?

**Você:** Nesse nível de exigência, eu consideraria migrar os componentes mais sensíveis a latência para uma linguagem/runtime com um modelo de gerenciamento de memória diferente — por exemplo, linguagens com gerenciamento de memória manual ou baseado em ownership (como Rust), que eliminam completamente as pausas de GC do tipo "stop-the-world", às custas de um código mais explícito sobre como a memória é alocada e liberada. Essa é uma decisão bem mais estrutural e cara, que eu só recomendaria depois de esgotar as opções de tuning, e apenas para os componentes onde a latência é genuinamente crítica ao negócio.

---

# TÓPICO 7: Cache

## Cenário 7.1: "Cache stampede" — todo mundo bate no banco ao mesmo tempo

**Entrevistador:** Você tem uma chave de cache muito popular (por exemplo, os dados da página inicial do site) com TTL de 5 minutos. Você percebeu que, a cada 5 minutos, exatamente quando essa chave expira, o banco de dados sofre um pico repentino de carga, quase derrubando o sistema, e depois volta ao normal até a próxima expiração. Como você explicaria esse padrão?

**Você:** Esse é um padrão bem característico conhecido como **"cache stampede"** ou **"thundering herd"**. Quando a chave expira, a próxima requisição que chega tem um cache miss e vai buscar o dado no banco. Mas, como o tráfego é alto, **centenas ou milhares de outras requisições chegam quase simultaneamente** nesse mesmo intervalo de milissegundos, e **todas elas também têm cache miss** (porque a primeira ainda não terminou de repopular o cache) — então todas elas, ao mesmo tempo, vão bater diretamente no banco de dados, gerando um pico de carga desproporcional.

**Entrevistador:** Como você resolveria isso?

**Você:** Existem algumas estratégias, e eu combinaria mais de uma:

1. **Lock/mutex na repopulação do cache**: quando ocorre um cache miss, em vez de deixar todas as requisições irem ao banco, apenas **uma** delas (a primeira a chegar) efetivamente busca o dado no banco e repopula o cache; as demais requisições **esperam** brevemente essa primeira terminar, e então leem o valor já atualizado do cache, em vez de irem ao banco também.

2. **Recomputação antecipada (refresh-ahead)**: em vez de deixar a chave expirar completamente e só então buscar um novo valor, o sistema recalcula e atualiza a chave de cache **um pouco antes** dela expirar de fato (por exemplo, quando já passou 80% do TTL), de forma assíncrona em background, para que ela nunca chegue a ficar "vazia" do ponto de vista das requisições normais.

3. **TTL com jitter (variação aleatória)**: se existem múltiplas chaves relacionadas expirando exatamente no mesmo momento, adicionar uma pequena variação aleatória no TTL de cada uma evita que todas expirem simultaneamente, distribuindo a carga de repopulação ao longo do tempo.

4. **Servir dado "stale" temporariamente**: permitir que o sistema continue servindo a versão antiga (levemente desatualizada) do cache por alguns segundos extras enquanto o valor novo é buscado em background, em vez de forçar todo mundo a esperar ou ir ao banco.

**Entrevistador:** Qual você aplicaria primeiro, com menos esforço de implementação, para estancar o problema rapidamente?

**Você:** Eu começaria pelo **refresh-ahead** combinado com **TTL com jitter**, porque são mudanças relativamente simples de configuração/lógica, sem exigir um mecanismo novo de lock distribuído. Se ainda assim o problema persistisse (por exemplo, em um cenário de tráfego extremamente concentrado), eu evoluiria para um lock de repopulação mais robusto.

---

## Cenário 7.2: Inconsistência de cache entre múltiplas instâncias da aplicação

**Entrevistador:** Sua aplicação roda em várias instâncias, cada uma com um cache **local** em memória (não um Redis compartilhado). Um usuário atualiza seu perfil, mas, dependendo de qual instância atende a próxima requisição dele (por trás do load balancer), às vezes ele vê o dado atualizado, às vezes ainda vê o dado antigo. Como você explicaria isso, e como resolveria?

**Você:** Isso é esperado, dado o desenho descrito: como cada instância mantém seu **próprio** cache local, independente das outras, quando o dado é atualizado, apenas a instância que processou a atualização (ou nenhuma, se a atualização não passar pelo cache) tem a informação nova. As **outras instâncias** continuam servindo a versão antiga que já tinham em cache, até que o TTL local delas expire e elas busquem o dado novo do banco.

**Entrevistador:** Quais são as opções para resolver esse tipo de inconsistência entre instâncias?

**Você:** Eu veria basicamente três caminhos, com trade-offs diferentes:

1. **Migrar para um cache compartilhado e centralizado (por exemplo, Redis)**, em vez de caches locais independentes por instância. Assim, existe **uma única fonte de verdade** para o cache, e todas as instâncias enxergam a mesma versão do dado ao mesmo tempo. Essa é geralmente a solução mais simples e mais recomendada quando consistência entre instâncias importa.

2. **Invalidação ativa (cache invalidation broadcast)**: manter os caches locais por instância (por questões de performance — cache local costuma ser ainda mais rápido que uma chamada de rede para o Redis), mas, sempre que um dado é atualizado, publicar um **evento de invalidação** (por exemplo, via Kafka ou Redis Pub/Sub) que todas as instâncias escutam, removendo aquela entrada específica dos seus caches locais imediatamente, independente do TTL.

3. **Aceitar a inconsistência temporária como um trade-off consciente**, reduzindo apenas o TTL do cache local para um valor bem pequeno (por exemplo, poucos segundos), o suficiente para que a janela de inconsistência entre instâncias seja curta o bastante para não incomodar o usuário na prática, sem o custo de implementar um mecanismo de invalidação distribuída.

**Entrevistador:** Qual você escolheria para o caso específico descrito — perfil do usuário sendo visto de forma inconsistente logo após uma atualização?

**Você:** Dado que se trata do próprio usuário vendo seus próprios dados de forma inconsistente logo após uma ação dele mesmo (atualizar o perfil), eu priorizaria a opção 1 (cache compartilhado) ou 2 (invalidação ativa), porque esse tipo de inconsistência é **muito perceptível e frustrante** para quem acabou de fazer a mudança — é diferente de, por exemplo, um contador de curtidas que pode ficar levemente desatualizado sem ninguém perceber. Eu tenderia a começar pela opção 1, por ser mais simples de implementar e operar, e só migraria para invalidação ativa em componentes onde a performance de um cache local fosse realmente crítica.

---

## Cenário 7.3: Cache "envenenado" com um resultado de erro

**Entrevistador:** Um endpoint que normalmente funciona bem começou a retornar erro para **todos** os usuários que pedem um recurso específico (por exemplo, os detalhes de um produto específico), mesmo que o dado no banco esteja perfeitamente correto e acessível. Reiniciar a aplicação resolve temporariamente, mas o problema volta depois de um tempo. O que você suspeitaria, dado esse padrão?

**Você:** "Reiniciar resolve temporariamente, e depois volta" é um forte indício de que o problema está em algo que é **reconstruído gradualmente após o restart** — e cache é um candidato natural para esse padrão, já que ele começa vazio depois de um restart e vai sendo preenchido aos poucos. Minha hipótese seria de que, em algum momento, uma **resposta de erro** (por exemplo, um erro transitório de banco, ou um resultado vazio por engano) foi armazenada no cache **como se fosse um resultado válido**, e agora todo mundo que pede aquele recurso específico recebe esse erro "cacheado", em vez de uma tentativa nova de buscar o dado real.

**Entrevistador:** Como você confirmaria essa hipótese, e como identificaria o ponto exato no código onde isso está acontecendo?

**Você:** Eu inspecionaria diretamente o **conteúdo daquela chave específica no cache** (por exemplo, consultando o Redis diretamente pela chave correspondente àquele produto) para confirmar se o valor armazenado é de fato uma mensagem de erro ou um dado inválido, em vez do resultado esperado. Depois, eu revisaria o código responsável por popular o cache (o padrão cache-aside que vimos anteriormente), procurando por um caminho onde uma exceção ou um resultado de erro **não é tratado como falha**, sendo acidentalmente serializado e armazenado no cache como se fosse um sucesso.

**Entrevistador:** Como você corrigiria o código para que isso não aconteça de novo?

**Você:** Eu garantiria, de forma explícita, que a lógica de "popular o cache" só armazene o resultado **depois de confirmar que a operação de busca no banco foi bem-sucedida** — nunca armazenando exceções, valores nulos inesperados, ou respostas de erro de uma dependência externa como se fossem o dado real. Isso normalmente significa envolver a chamada ao banco em um tratamento de erro adequado, e só chamar `cache.set(...)` dentro do caminho de sucesso, nunca dentro (ou logo após) de um bloco que capturou uma falha.

**Entrevistador:** E para remediar imediatamente os usuários afetados agora, sem esperar um novo deploy da correção — o que você faria?

**Você:** Eu **removeria manualmente a chave específica** (ou as chaves afetadas, se identificadas em maior número) diretamente do cache, forçando a próxima requisição a ter um cache miss e buscar o dado novamente do banco, que sabemos estar correto. Isso resolve o sintoma imediatamente para os usuários, enquanto a correção definitiva no código é preparada, testada e implantada com calma.

---

# TÓPICO 8: Deploys e Releases

## Cenário 8.1: Taxa de erros 500 sobe repentinamente logo após um deploy

**Entrevistador:** Vinte minutos depois de um deploy, a taxa de erros HTTP 500 da sua API subiu de menos de 0.1% para 8%. Como você reage?

**Você:** A correlação temporal com o deploy é o sinal mais forte aqui, então minha primeira pergunta seria: **esse deploy está sendo entregue de forma gradual (canary/rolling) ou para 100% do tráfego de uma vez?**

**Entrevistador:** Foi para 100% do tráfego de uma vez, sem canary. O que você faz imediatamente?

**Você:** Considerando que temos alta confiança de que o deploy é a causa (pela correlação temporal), e o impacto já é significativo (8% de erro), minha prioridade número um **não é investigar a causa raiz agora** — é **mitigar o impacto para os usuários o mais rápido possível**. Nesse caso, eu faria um **rollback imediato** para a versão anterior, que sabemos que estava estável. Investigar a causa exata do bug pode ser feito com calma depois, em um ambiente controlado, sem usuários reais sendo afetados enquanto isso.

**Entrevistador:** Boa priorização — muita gente perde tempo precioso "debugando em produção" antes de estancar o sangramento. Depois do rollback, como você investigaria a causa raiz com calma?

**Você:** Eu olharia:
1. Os **logs de erro** específicos daquele período, para entender qual exceção estava sendo lançada e em qual parte do código
2. O **diff exato do deploy** (quais arquivos/linhas mudaram), para correlacionar o erro com uma mudança específica
3. Tentaria **reproduzir o erro em um ambiente de staging**, aplicando o mesmo deploy, mas sem tráfego de produção real
4. Verificaria se o erro acontece para **todas** as requisições ou só para um subconjunto (por exemplo, só para um tipo específico de payload ou um tipo específico de usuário) — isso ajuda a isolar rapidamente qual parte da mudança de código introduziu o problema

**Entrevistador:** Como você mudaria o processo de deploy para evitar que esse tipo de incidente tenha um impacto tão amplo (8% de todos os usuários) da próxima vez?

**Você:** Eu introduziria uma estratégia de **deploy gradual**, como:
- **Canary release**: liberar a nova versão para uma pequena fatia do tráfego (por exemplo, 1-5%) primeiro, monitorar as métricas de erro por um tempo, e só então ir expandindo gradualmente para 100% se tudo estiver saudável
- **Feature flags**: para mudanças de comportamento mais arriscadas, colocar atrás de uma flag que pode ser desativada instantaneamente (sem precisar de um novo deploy) se algo der errado
- **Alertas automáticos vinculados ao deploy**: configurar um alerta que dispara automaticamente (e idealmente já sugere rollback automático) se a taxa de erro ultrapassar um limiar logo após um deploy, para reduzir o tempo entre "o problema começou" e "alguém percebeu"

---

## Cenário 8.2: Degradação silenciosa — o deploy não gera erros, mas produz dados incorretos

**Entrevistador:** Um deploy diferente agora: não houve aumento de erros HTTP, nenhuma exceção nova nos logs, latência normal — mas, três dias depois, o time financeiro percebe que os relatórios de faturamento estão com valores errados desde o dia do deploy. Por que esse tipo de problema é mais perigoso, em geral, do que o cenário anterior de erros 500?

**Você:** Esse tipo de problema — o que costuma ser chamado de **"degradação silenciosa"** — é mais perigoso justamente porque **não dispara nenhum dos alertas tradicionais** (taxa de erro, latência, disponibilidade). O sistema "funciona", do ponto de vista técnico, só que produz um **resultado de negócio incorreto**. Isso significa que ele pode passar despercebido por muito mais tempo, e o estrago (nesse caso, dados financeiros incorretos acumulados por três dias) tende a ser maior antes de ser descoberto.

**Entrevistador:** Como você investigaria a causa raiz, já que não há uma exceção clara apontando para o problema?

**Você:** Eu partiria da mesma lógica de correlação temporal: qual foi o **deploy** (ou mudança de configuração, ou migração de dados) que aconteceu **exatamente no dia** em que os valores começaram a ficar incorretos? Depois, eu revisaria o diff daquele deploy especificamente **procurando por mudanças relacionadas ao cálculo em questão** — por exemplo, uma alteração em uma fórmula, em uma taxa de conversão, em um arredondamento, ou em uma condição lógica que define quais registros entram no cálculo do faturamento.

**Entrevistador:** Suponha que você encontrou: uma mudança recente trocou um arredondamento de "sempre para cima" para "arredondamento padrão", uma alteração aparentemente pequena e inocente, feita como parte de uma refatoração maior de código, sem relação direta com faturamento. O que essa descoberta te ensina sobre como prevenir esse tipo de problema no futuro?

**Você:** Isso reforça a importância de **testes automatizados que validem regras de negócio críticas de ponta a ponta**, não só a ausência de exceções. Para uma área sensível como faturamento, eu implementaria:
1. **Testes de regressão específicos** para os cálculos financeiros, com casos conhecidos e seus resultados esperados, que rodam automaticamente antes de qualquer deploy que toque (mesmo que indiretamente) código relacionado
2. **Monitoramento de métricas de negócio**, não só métricas técnicas — por exemplo, um alerta que dispara se o faturamento diário total variar de forma anormal (muito acima ou muito abaixo) em relação à média histórica, o que teria pego essa mudança de forma bem mais rápida do que 3 dias
3. **Revisão de código mais cautelosa** em áreas identificadas como de alto risco de negócio, mesmo quando a mudança parece pequena ou "só uma refatoração" — refatorações que tocam lógica de cálculo merecem o mesmo nível de escrutínio que uma mudança de funcionalidade nova

**Entrevistador:** Como você corrigiria os dados que já foram gerados incorretamente nesses três dias?

**Você:** Eu identificaria precisamente **quais registros** foram afetados (usando o intervalo de tempo do deploy problemático até a correção), calcularia o valor **correto** para cada um deles usando a lógica já corrigida, e coordenaria com o time de negócio/financeiro a melhor forma de aplicar essa correção retroativa — que pode envolver reprocessar os registros automaticamente, ou, dependendo da sensibilidade (como implicações fiscais/contábeis), um processo mais manual e auditado.

---

## Cenário 8.3: Rollback de deploy quebra por incompatibilidade de migração de banco de dados

**Entrevistador:** Voltando ao Cenário 8.1: você decide fazer o rollback do deploy problemático. Só que, ao fazer isso, a aplicação (agora na versão anterior) começa a lançar erros **novos**, diferentes dos originais — porque o deploy que você está revertendo também incluía uma migração de schema no banco de dados (por exemplo, uma coluna nova foi adicionada, e o código novo passou a depender dela). O que aconteceu, e como você lida com essa situação?

**Você:** Isso é um problema clássico de **incompatibilidade entre versões de código e schema de banco de dados** durante um rollback. O que aconteceu: a migração de banco (adicionar a coluna nova) já foi aplicada e permanece no banco mesmo depois do rollback do código (rollbacks de deploy de aplicação normalmente não desfazem migrações de banco automaticamente). Só que, nesse caso específico, o problema é o oposto: o código **antigo** (para o qual estamos revertendo) provavelmente não tem lógica para essa coluna nova, então isso *não deveria* quebrar nada por si só — a menos que a migração também tenha **removido ou renomeado** algo que o código antigo ainda espera encontrar. Nesse caso, o código antigo está tentando acessar uma coluna/tabela que não existe mais (ou mudou de nome/formato), e por isso está falhando.

**Entrevistador:** Isso te ensina o quê sobre como migrações de banco deveriam ser desenhadas, para que rollbacks de aplicação sejam sempre seguros?

**Você:** A prática recomendada é que migrações de banco de dados sejam **retrocompatíveis (backward compatible)** com a versão anterior do código, especialmente em qualquer mudança que **remova ou renomeie** algo (colunas, tabelas). Uma abordagem comum é fazer essas mudanças em **múltiplas etapas separadas**, em vez de uma única migração "destrutiva":

1. **Etapa 1**: adicionar a coluna/tabela nova, mantendo a antiga também presente e funcionando (o código antigo continua funcionando normalmente, ignorando a coluna nova que ele não conhece)
2. **Etapa 2**: fazer o deploy do novo código, que já usa a coluna nova, mas ainda mantendo compatibilidade de leitura com o formato antigo se necessário
3. **Etapa 3** (só depois de ter confiança de que o novo código está estável em produção por um tempo razoável): remover a coluna/tabela antiga, agora que nenhuma versão do código em uso depende mais dela

Dessa forma, um rollback do código, a qualquer momento entre as etapas 1 e 2, **nunca quebra**, porque a versão anterior do código nunca depende de algo que foi removido — só depende de algo que pode ter sido adicionado, e simplesmente ignora.

**Entrevistador:** E para a situação imediata, já com o rollback quebrado em produção — o que você faz agora?

**Você:** Nesse momento crítico, eu avaliaria rapidamente duas opções e escolheria a de menor risco: (a) reverter também a migração de banco (se houver um script de "down migration" seguro e testado para isso), restaurando o schema anterior compatível com o código antigo; ou (b) se reverter a migração for arriscado ou não estiver disponível, **avançar** em vez de recuar — corrigir rapidamente o bug específico que causou os erros 500 originais na versão nova do código (já que agora sabemos que reverter não é uma saída limpa) e fazer um novo deploy, direto para a versão corrigida, em vez de tentar voltar para trás. Essa decisão dependeria de qual das duas opções consigo executar com mais confiança e velocidade naquele momento específico.

---

# TÓPICO 9: Falhas em Cascata e Microsserviços

## Cenário 9.1: Falha em cascata entre A → B → C

**Entrevistador:** Você tem uma arquitetura de microsserviços. O Serviço A chama o Serviço B, que chama o Serviço C. O Serviço C começa a ficar lento (por um problema pontual de banco de dados, digamos). Algumas horas depois, você percebe que o Serviço A e o Serviço B **também** ficaram completamente indisponíveis, mesmo não tendo nenhum bug próprio. Como você explicaria isso?

**Você:** Isso tem cara de uma **falha em cascata**. Minha hipótese inicial: o Serviço C ficou lento, e o Serviço B, ao chamá-lo, não tinha um timeout adequado configurado (ou tinha um timeout muito longo) — então cada requisição do Serviço B para o Serviço C ficou "pendurada" esperando por um longo tempo. Se isso aconteceu com um volume alto de requisições simultâneas, o Serviço B esgotou seus próprios recursos (threads, conexões, memória) só de ficar esperando o Serviço C, e por consequência **o próprio Serviço B também ficou indisponível** — mesmo sem nenhum bug nele. E o mesmo efeito se propagou do Serviço B para o Serviço A, repetindo o padrão.

**Entrevistador:** Como você confirmaria essa hipótese, em vez de só assumir que é isso?

**Você:** Eu olharia os dashboards de latência e taxa de erro de cada serviço, na ordem cronológica: eu esperaria ver o Serviço C degradar primeiro, depois o Serviço B começar a acumular requisições pendentes/timeout pouco depois, e só então o Serviço A degradar por último. Se essa sequência temporal se confirmar nos dados, isso sustenta fortemente a hipótese de efeito cascata partindo do C.

**Entrevistador:** Quais mecanismos de arquitetura você implementaria para que isso não aconteça de novo?

**Você:** Vários mecanismos trabalhando em conjunto:

1. **Timeouts agressivos e bem definidos** em toda chamada entre serviços — nenhuma chamada deveria poder "esperar para sempre". Se o Serviço C não responder dentro de um tempo razoável, o Serviço B deveria desistir e tratar isso como uma falha, liberando seus próprios recursos rapidamente.

2. **Circuit breaker** entre B e C: se o Serviço C começar a falhar/ficar lento repetidamente, o Serviço B para de tentar chamá-lo por um tempo, retornando uma resposta de fallback (ou erro rápido) em vez de continuar tentando e acumulando requisições penduradas.

3. **Isolamento de recursos (bulkhead pattern)**: separar os "pools" de conexões/threads usados para chamar o Serviço C dos pools usados para outras operações do Serviço B, para que uma degradação específica do C não consuma **todos** os recursos do B, deixando pelo menos as outras funcionalidades do B operacionais.

4. **Fallback gracioso**: definir, para cada dependência crítica, o que fazer quando ela está indisponível — por exemplo, retornar um dado em cache um pouco desatualizado, ou uma resposta parcial, em vez de falhar a requisição inteira.

**Entrevistador:** Você mencionou "bulkhead pattern". Explica essa analogia e por que o nome faz sentido.

**Você:** O nome vem dos compartimentos estanques (bulkheads) de um navio: o casco é dividido em várias seções seladas, de forma que, se uma seção for perfurada e alagar, **apenas aquela seção** enche de água — o navio inteiro não afunda, porque as outras seções continuam isoladas e secas. Aplicado a software, isso significa: se eu isolar os recursos (conexões, threads, memória) usados para chamar um serviço específico, uma falha **naquele** serviço específico não consome os recursos necessários para as outras partes do sistema continuarem funcionando normalmente.

---

## Cenário 9.2: "Retry storm" piorando uma degradação já existente

**Entrevistador:** Um serviço downstream começa a ficar instável, respondendo com erro em cerca de 20% das chamadas. Os serviços que o chamam foram configurados, de forma bem-intencionada, para **tentar de novo automaticamente (retry)** sempre que recebem um erro. O que você esperaria que acontecesse a seguir, e por quê isso pode piorar a situação em vez de ajudar?

**Você:** Isso tem potencial de gerar o que se costuma chamar de **"retry storm"**: se cada chamador re-tenta automaticamente toda chamada que falhou, o volume **efetivo** de requisições chegando no serviço já instável **aumenta** (em vez de diminuir), porque agora, além das chamadas originais, também chegam todas as retentativas. Se o serviço já estava com dificuldade para processar 100% do volume original, processar 100% + as retentativas só vai aumentar ainda mais a taxa de erro/timeout, gerando **ainda mais retentativas** em um ciclo que se realimenta e piora progressivamente — em vez de o sistema se recuperar, ele afunda ainda mais rápido.

**Entrevistador:** Como você identificaria que um retry storm está acontecendo, em vez de simplesmente "muito tráfego orgânico"?

**Você:** Eu compararia o número de requisições **originais** (a partir dos logs/métricas do lado do chamador, contando apenas a primeira tentativa de cada operação) com o número **total** de requisições chegando no serviço afetado (incluindo retentativas). Se o total for significativamente maior que o número de operações originais — por exemplo, 3x ou 5x mais chamadas do que operações de negócio de fato iniciadas —, isso é um forte indício de que as retentativas estão inflando artificialmente o volume.

**Entrevistador:** Como você desenharia a lógica de retry para evitar esse problema, sem simplesmente eliminar retries (que também têm seu valor legítimo para falhas transitórias)?

**Você:** Eu combinaria algumas práticas:

1. **Backoff exponencial com jitter**: em vez de tentar novamente imediatamente após uma falha, esperar um tempo **crescente** entre cada tentativa (por exemplo, 1s, depois 2s, depois 4s...), com uma pequena variação aleatória (jitter) para evitar que múltiplos clientes re-tentem exatamente no mesmo instante, criando novos picos sincronizados.
2. **Limite máximo de tentativas**: nunca re-tentar indefinidamente — depois de um número máximo (por exemplo, 3 tentativas), desistir e propagar o erro para cima, em vez de continuar insistindo para sempre.
3. **Circuit breaker combinado com retry**: se o circuit breaker já detectou que o serviço downstream está com problema e "abriu o circuito", os retries automáticos deveriam **respeitar** esse estado e nem tentar chamar o serviço, retornando erro imediatamente — evitando adicionar mais carga a um sistema que já sabemos que está instável.
4. **Retry apenas para erros que fazem sentido re-tentar**: nem todo erro deveria gerar uma nova tentativa automática (por exemplo, um erro de validação de dados do próprio cliente nunca vai ter sucesso só porque foi re-tentado — só erros genuinamente transitórios, como um timeout de rede pontual, justificam retry).

**Entrevistador:** Excelente. Você amarrou bem os conceitos de circuit breaker e retry, mostrando que eles precisam trabalhar **juntos**, não de forma isolada — muita gente implementa um sem pensar no outro, e acaba criando justamente esse tipo de ciclo vicioso.

---

## Cenário 9.3: Incompatibilidade de contrato entre versões de serviços (breaking change)

**Entrevistador:** Você tem um Serviço A que consome uma API do Serviço B. O time do Serviço B faz um deploy que, na visão deles, é uma "pequena melhoria": eles renomeiam um campo no JSON de resposta, de `client_name` para `customer_name`, porque acham o novo nome mais claro. Logo depois desse deploy, o Serviço A começa a falhar em produção. Como você investigaria e o que isso revela sobre como serviços deveriam evoluir seus contratos de API?

**Você:** Isso é um exemplo clássico de **breaking change em um contrato de API** que não foi tratado com o devido cuidado — do ponto de vista do time do Serviço B, foi uma mudança pequena e até uma melhoria; do ponto de vista do Serviço A (e de qualquer outro consumidor daquela API), é uma mudança que **quebra** o código existente, porque ele está programado para procurar especificamente pelo campo `client_name`, que deixou de existir. Eu confirmaria isso rapidamente comparando a resposta da API do Serviço B antes e depois do deploy, e correlacionando com o início dos erros no Serviço A.

**Entrevistador:** Isso te ensina o quê sobre como o time do Serviço B deveria ter feito essa mudança, para não impactar consumidores?

**Você:** Contratos de API **públicos** (ou seja, consumidos por outros times/serviços) deveriam seguir o mesmo princípio de compatibilidade retroativa que vimos nas migrações de banco de dados. Para renomear um campo com segurança, eu recomendaria uma abordagem em etapas:

1. **Etapa 1**: adicionar o campo novo (`customer_name`) na resposta, mantendo o campo antigo (`client_name`) também presente, com o mesmo valor — os dois convivem temporariamente
2. **Etapa 2**: comunicar formalmente a todos os consumidores conhecidos da API que o campo antigo está **depreciado** e será removido em uma data futura, dando tempo para eles migrarem seu código para usar o campo novo
3. **Etapa 3**: só depois que a maioria (idealmente todos) dos consumidores confirmarem a migração, remover o campo antigo

**Entrevistador:** E se o Serviço B não sabe exatamente **quais** e **quantos** consumidores dependem daquela API, o que dificulta saber quando é seguro remover o campo antigo — como resolver esse problema de visibilidade?

**Você:** Isso indica uma lacuna de **observabilidade e governança de API** que vale a pena resolver de forma mais estrutural, não só para esse caso específico. Eu sugeriria:
1. Implementar **versionamento explícito de API** (por exemplo, `/v1/` e `/v2/` nos endpoints, ou um cabeçalho de versão), tornando claro e documentado desde o início que existem consumidores que podem estar em versões diferentes do contrato
2. Adicionar **métricas de uso por campo/versão da API** (por exemplo, contar quantas requisições ainda estão sendo feitas para uma versão antiga, ou quantas respostas ainda incluem o campo depreciado sendo efetivamente lido pelo consumidor), dando visibilidade real de quando é seguro descontinuar algo
3. Manter um **catálogo/registro de consumidores conhecidos** de cada API interna (mesmo que simples), para que qualquer mudança de contrato passe primeiro por uma checagem de "quem depende disso, e foram avisados?"

**Entrevistador:** Muito boa cobertura de todos os tópicos. Você demonstrou, em cada cenário, o mesmo padrão de raciocínio: investigar antes de concluir, validar hipóteses com dados reais em vez de suposições, diferenciar mitigação de curto prazo de correção estrutural, e sempre puxar a conversa para "como evitamos que isso aconteça de novo" — que é exatamente o nível de maturidade técnica que eu, como entrevistador, espero de um candidato pleno/sênior.

---

# TÓPICO 10: Índices de Banco de Dados

## Cenário 10.1: Adicionar um índice acelerou a leitura, mas deixou a escrita mais lenta

**Entrevistador:** Vocês tinham uma query de leitura muito lenta em uma tabela grande, e você adicionou um índice na coluna certa — a leitura melhorou drasticamente, como esperado. Alguns dias depois, o time de dados reporta que as operações de `INSERT` e `UPDATE` naquela mesma tabela ficaram perceptivelmente mais lentas. Isso é uma coincidência, ou existe uma relação direta entre as duas coisas?

**Você:** Existe uma relação direta, e é importante deixar claro que isso **não é um efeito colateral inesperado** — é um trade-off inerente a como índices funcionam. Um índice não é "gratuito": ele é uma estrutura de dados adicional (geralmente uma B-tree) que o banco mantém **em paralelo** à tabela, para acelerar buscas. Toda vez que uma linha é inserida, atualizada ou removida, o banco precisa **atualizar não só a tabela, mas também todos os índices** que existem sobre ela. Ou seja: eu troquei "leitura mais rápida" por "escrita um pouco mais lenta", porque agora cada escrita tem mais trabalho a fazer.

**Entrevistador:** Como você mediria, na prática, o quanto esse índice específico está impactando a escrita, para saber se o trade-off vale a pena?

**Você:** Eu compararia o tempo de execução de operações de escrita naquela tabela **antes e depois** da criação do índice (se eu tiver esse histórico de métricas), ou faria um teste controlado em um ambiente de staging: rodar o mesmo volume de inserções/atualizações com e sem o índice presente, medindo a diferença de tempo. Eu também levaria em conta **quantos índices já existem** naquela tabela — o impacto de escrita não é apenas do índice novo isoladamente, é cumulativo com todos os índices já existentes.

**Entrevistador:** Suponha que essa tabela recebe uma proporção de leitura para escrita de 1000:1 (é fortemente lida, raramente escrita). Isso muda sua análise sobre se vale a pena manter o índice?

**Você:** Sim, e bastante. Com uma proporção de 1000 leituras para cada escrita, o benefício de ter a leitura muito mais rápida **supera de longe** o pequeno custo adicional em uma operação de escrita que é rara. Eu manteria o índice sem dúvida nesse cenário. A decisão fica bem mais delicada quando a tabela é escrita com muita frequência e lida raramente — nesse caso oposto, eu questionaria se aquele índice específico realmente se paga, ou se está sendo mantido só "porque parecia uma boa prática geral", sem considerar o padrão de acesso real daquela tabela específica.

**Entrevistador:** E se a tabela recebesse um volume de escrita realmente alto (por exemplo, milhares de inserções por segundo), e a leitura que o índice acelera fosse usada raramente — o que você consideraria fazer?

**Você:** Eu avaliaria seriamente **remover o índice** se o ganho de leitura não justificasse o custo de escrita naquele volume, ou buscaria alternativas: por exemplo, se a leitura acelerada por aquele índice é usada apenas por um relatório interno que roda uma vez por dia, talvez fizesse mais sentido **não ter um índice permanente**, e sim rodar esse relatório contra uma **réplica de leitura** dedicada (que não sofre a mesma pressão de escrita da tabela principal), ou até materializar o resultado daquele relatório periodicamente em uma tabela separada, evitando pagar o custo de manutenção do índice a cada escrita na tabela de origem.

---

## Cenário 10.2: Índice composto existe, mas a query continua fazendo "Seq Scan"

**Entrevistador:** Você tem uma tabela de pedidos com um índice composto criado sobre as colunas `(status, criado_em)`, nessa ordem. Uma query que filtra `WHERE criado_em > '2026-01-01'` (sem filtrar por `status`) continua fazendo um "Seq Scan" completo, ignorando esse índice, mesmo com milhões de linhas na tabela. Por que isso acontece, já que a coluna `criado_em` está no índice?

**Você:** Isso acontece por causa de como índices compostos (multi-coluna) funcionam internamente — eles seguem o que se chama de **"regra do prefixo mais à esquerda" (leftmost prefix rule)**. Um índice composto em `(status, criado_em)` é fisicamente organizado **primeiro por `status`, e dentro de cada valor de `status`, ordenado por `criado_em`** — é como um catálogo telefônico organizado por sobrenome e depois por nome: se eu quero buscar só por nome, sem saber o sobrenome, o catálogo organizado dessa forma não me ajuda muito, porque a ordenação por nome só existe **dentro** de cada sobrenome, não de forma global. Da mesma forma, minha query filtrando **apenas** por `criado_em`, sem mencionar `status`, não consegue aproveitar de forma eficiente esse índice específico, porque o banco não sabe "em qual parte" do índice procurar sem primeiro saber o `status`.

**Entrevistador:** Isso significa que a ordem das colunas no índice composto é uma decisão arbitrária, ou existe um critério para decidir qual coluna vem primeiro?

**Você:** Não é arbitrário — o critério mais comum é: **colunas usadas em filtros de igualdade (`=`) geralmente devem vir antes de colunas usadas em filtros de intervalo/range (`>`, `<`, `BETWEEN`)** no índice composto, porque isso permite ao banco "localizar" rapidamente o subconjunto exato de linhas pela igualdade, e depois varrer de forma eficiente dentro daquele subconjunto pelo intervalo. Além disso, a ordem das colunas no índice deveria refletir **os padrões de consulta reais** da aplicação — eu olharia quais combinações de filtros as queries mais frequentes de fato usam, em vez de escolher a ordem de forma genérica.

**Entrevistador:** Dado que essa query específica (filtrar só por `criado_em`) é comum e importante, mas a combinação `(status, criado_em)` também é usada por outras queries importantes — o que você faria?

**Você:** Eu teria duas opções, com trade-offs diferentes:

1. **Criar um segundo índice**, dedicado só à coluna `criado_em` (ou invertendo a ordem, criando um índice em `(criado_em, status)`, se essa combinação também for útil para outras consultas) — isso resolve o problema de performance para ambos os padrões de consulta, mas **adiciona mais um índice**, o que, como vimos no cenário anterior, tem um custo de escrita adicional.
2. **Reavaliar se realmente preciso de ambos os índices**, verificando com que frequência cada padrão de consulta acontece de fato, e se o custo extra de escrita de manter os dois índices se justifica pelo ganho de leitura em ambos os casos.

Eu tomaria essa decisão olhando os dados reais de uso (frequência de cada tipo de query, volume de escrita na tabela), em vez de simplesmente adicionar mais um índice por precaução.

---

## Cenário 10.3: Excesso de índices (over-indexing) degradando um job de carga em lote

**Entrevistador:** Um job noturno que faz inserção em massa de dados (batch insert) costumava levar 10 minutos, e agora está levando quase 2 horas. Ao investigar, você descobre que, ao longo do tempo, diferentes engenheiros do time foram adicionando índices na mesma tabela, cada um resolvendo uma necessidade pontual de otimizar uma query específica, sem coordenação entre si. Hoje a tabela tem 15 índices diferentes. Como você investigaria quais desses índices realmente valem a pena manter?

**Você:** Primeiro eu confirmaria a causa raiz — eu esperaria que o tempo do job de inserção em massa tivesse **degradado progressivamente**, à medida que cada índice novo foi adicionado ao longo do tempo, e não de uma vez só. Eu correlacionaria o histórico de criação de cada índice (data em que cada um foi adicionado) com o histórico de duração desse job, para confirmar essa relação de causa e efeito antes de agir.

**Entrevistador:** Confirmado esse padrão de degradação progressiva. Como você decidiria quais dos 15 índices são realmente necessários, e quais podem ser removidos com segurança?

**Você:** A maioria dos bancos relacionais expõe estatísticas de **uso real de cada índice** — por exemplo, no PostgreSQL, a view `pg_stat_user_indexes` mostra quantas vezes cada índice foi de fato utilizado em uma consulta (o número de "index scans"). Eu analisaria essas estatísticas para identificar:
1. **Índices nunca usados, ou usados muito raramente** — fortes candidatos a remoção, já que estão pagando o custo de manutenção em toda escrita, sem trazer benefício de leitura proporcional
2. **Índices redundantes** — por exemplo, se já existe um índice composto em `(coluna_a, coluna_b, coluna_c)`, um índice separado apenas em `(coluna_a)` é, na prática, redundante, porque o próprio índice composto já serve eficientemente para consultas que filtram só por `coluna_a` (aproveitando a regra do prefixo mais à esquerda que vimos no cenário anterior) — esse índice extra só está adicionando custo de escrita sem necessidade real
3. **Índices sobre colunas de baixíssima seletividade** (por exemplo, uma coluna booleana com poucos valores possíveis, como `ativo: true/false`) — índices desse tipo geralmente trazem pouco ganho de leitura, porque o banco frequentemente decide que um "Seq Scan" ainda é mais eficiente do que usar um índice que não reduz muito o conjunto de linhas candidatas

**Entrevistador:** Depois de identificar os candidatos a remoção, como você validaria com segurança que remover um índice específico não vai quebrar nenhuma query importante que dependa dele, antes de aplicar em produção?

**Você:** Eu faria isso de forma controlada e reversível:
1. Primeiro, testaria a remoção em um ambiente de staging (ou réplica), rodando a suíte completa de queries importantes da aplicação, comparando os planos de execução (`EXPLAIN`) antes e depois, para confirmar que nenhuma query crítica volta a fazer "Seq Scan" de forma problemática
2. Em produção, eu **desabilitaria** o índice temporariamente (em vez de removê-lo definitivamente de imediato, se o banco suportar essa opção) e monitoraria de perto por um período — se nenhuma query degradar visivelmente, aí sim eu removeria o índice de forma definitiva, já com confiança de que ele era de fato desnecessário
3. Eu documentaria essa decisão, incluindo os dados de uso que embasaram a remoção, para que o próximo engenheiro que revisitar essa tabela não precise refazer toda essa investigação do zero

---

# TÓPICO 11: Rede, DNS e Certificados

## Cenário 11.1: Certificado TLS expira e derruba integrações silenciosamente

**Entrevistador:** Em uma madrugada, um serviço interno que depende de uma API de um parceiro para de funcionar completamente — todas as chamadas passam a falhar com um erro relacionado a "certificado inválido" ou "handshake TLS falhou". Nada mudou no código nem foi feito nenhum deploy recente. Como você investigaria?

**Você:** "Nada mudou, mas do nada parou de funcionar, e o erro menciona certificado" é um padrão bem característico de um **certificado TLS/SSL que expirou** — seja o certificado do próprio parceiro (do lado deles), ou um certificado que a minha aplicação usa para autenticação mútua (mTLS), se for esse o caso. Eu verificaria a data de expiração do certificado envolvido, usando uma ferramenta simples de linha de comando (por exemplo, `openssl s_client` para inspecionar o certificado apresentado pelo servidor remoto), para confirmar se ele de fato expirou exatamente no horário em que o problema começou.

**Entrevistador:** Confirmado: o certificado do lado do parceiro expirou hoje à meia-noite. Isso é responsabilidade deles, não sua — mas mesmo assim, o que isso revela sobre uma lacuna no **seu próprio** monitoramento?

**Você:** Isso revela que eu não tinha nenhum **alerta proativo de expiração de certificado** — nem para os certificados que eu mesmo controlo, nem, idealmente, para os certificados de dependências externas críticas. Um certificado TLS tem uma data de expiração conhecida com **muita antecedência** (geralmente meses) — não é um evento repentino e imprevisível como uma falha de hardware. Deixar isso ser descoberto só quando o certificado já expirou e o sistema já está fora do ar é um problema de processo, não só de sorte.

**Entrevistador:** Como você mudaria seu processo de monitoramento para nunca mais ser pego de surpresa por isso, tanto para seus próprios certificados quanto, na medida do possível, para os de terceiros críticos?

**Você:** Eu implementaria:
1. **Monitoramento automático de expiração de certificados** para todos os domínios/serviços que a minha própria aplicação expõe ou depende diretamente, com alertas disparando bem antes do vencimento (por exemplo, 30 dias, 14 dias e 3 dias antes) — muitas ferramentas de observabilidade já têm essa checagem pronta, é só habilitar
2. **Automatizar a renovação** de certificados sempre que possível (por exemplo, usando Let's Encrypt com renovação automática), eliminando a dependência de alguém lembrar manualmente de renovar
3. Para dependências de terceiros críticas, mesmo sem controle direto sobre o certificado deles, eu manteria um **monitor externo simples** que verifica periodicamente a validade do certificado apresentado por aquele domínio, me alertando com antecedência mesmo que a responsabilidade de renovação seja do parceiro — me dando tempo de contatá-los proativamente antes que o problema aconteça de fato

---

## Cenário 11.2: Mudança de DNS afeta só uma parte dos usuários, de forma imprevisível

**Entrevistador:** Você atualizou o registro DNS de um domínio para apontar para um novo servidor. Horas depois, alguns usuários já conseguem acessar normalmente pelo novo servidor, mas outros ainda estão sendo direcionados para o servidor antigo (que você já desligou), recebendo erro de conexão. Não há um padrão óbvio de quem é afetado. Como você explicaria isso?

**Você:** Isso é um sintoma clássico e esperado de **propagação de DNS**: mudanças em registros DNS não se propagam instantaneamente para todo o mundo. Cada resolvedor de DNS (o do provedor de internet do usuário, ou um resolvedor público como o do Google) mantém um **cache** da resposta anterior, respeitando o **TTL (Time To Live)** configurado naquele registro — enquanto esse TTL não expirar do lado de cada resolvedor específico, ele continua respondendo com o endereço IP antigo, mesmo que eu já tenha atualizado o registro na origem. Diferentes usuários, usando diferentes resolvedores de DNS com caches em estados diferentes, vão "enxergar" a mudança em momentos diferentes — por isso o padrão parece aleatório, mas na verdade só reflete o estado de cache de cada resolvedor.

**Entrevistador:** Isso significa que você simplesmente precisa esperar a propagação acontecer, sem poder fazer nada? Como você poderia ter evitado, ou ao menos reduzido, esse período de impacto misto?

**Você:** Não dava para eliminar completamente a propagação (isso é inerente a como o DNS funciona), mas o **tamanho da janela de impacto** poderia ter sido bem menor com um planejamento prévio:
1. **Reduzir o TTL do registro DNS com bastante antecedência** (por exemplo, um TTL de 24h reduzido para 5 minutos, dias antes da mudança real) — assim, quando a mudança de fato acontece, os resolvedores já estão configurados para "checar de novo" rapidamente, e a propagação completa acontece muito mais rápido
2. **Manter o servidor antigo ativo por um período de transição**, mesmo depois de já ter migrado o tráfego principal para o novo — em vez de desligá-lo imediatamente, eu manteria ambos funcionando (idealmente com o mesmo conteúdo/comportamento) até ter alta confiança de que a propagação alcançou a grande maioria dos usuários, só então desligando o antigo com segurança

**Entrevistador:** E para a situação imediata, com alguns usuários ainda impactados agora — o que você faria, já que reduzir o TTL retroativamente não ajuda para quem já tem o cache antigo?

**Você:** Como o servidor antigo já foi desligado, a mitigação mais rápida seria **religar temporariamente o servidor antigo** (se ainda for possível), mesmo que de forma simplificada, só para atender esse tráfego residual enquanto a propagação termina de se completar naturalmente — evitando deixar uma parcela de usuários genuinamente sem acesso, mesmo que por um curto período adicional.

---

## Cenário 11.3: Latência de resolução DNS adicionando atraso perceptível em cada requisição

**Entrevistador:** Sua aplicação faz chamadas frequentes para um serviço externo. Ao investigar a latência dessas chamadas com tracing detalhado, você percebe que uma fração significativa do tempo total de cada chamada — algo como 100-200ms — está sendo gasta apenas na etapa de **resolução DNS** (transformar o nome de domínio em um endereço IP), antes mesmo da conexão de rede real começar. Isso te surpreende, e como você investigaria por que a resolução DNS está tão lenta?

**Você:** Isso é surpreendente porque, normalmente, resultados de DNS deveriam ser **cacheados** depois da primeira consulta, tornando consultas subsequentes praticamente instantâneas. Se estou vendo esse atraso de forma consistente **em toda chamada**, e não só na primeira, minha suspeita principal seria que o **cache de DNS não está sendo efetivamente utilizado** pela aplicação (ou pelo sistema operacional/biblioteca de rede usada), fazendo uma consulta DNS completa a cada chamada, em vez de reaproveitar um resultado já resolvido recentemente.

**Entrevistador:** Como você confirmaria essa hipótese, e o que investigaria a seguir?

**Você:** Eu verificaria a configuração de cache de DNS do cliente HTTP (ou biblioteca de rede) usado pela aplicação — muitas bibliotecas, por padrão, respeitam o TTL do próprio registro DNS para decidir quanto tempo cachear, mas algumas configurações (ou bugs específicos de certas versões de bibliotecas) podem acabar **ignorando o cache completamente**, resolvendo o DNS do zero a cada chamada. Eu também verificaria se a aplicação está criando uma **nova conexão** a cada chamada, em vez de reutilizar conexões existentes (connection pooling/keep-alive) — se cada chamada abre uma conexão totalmente nova, o custo de resolução DNS (e de handshake TCP/TLS) é pago repetidamente, mesmo que o cache de DNS esteja funcionando corretamente em outro nível.

**Entrevistador:** Suponha que você confirma: a aplicação não está reutilizando conexões, abrindo uma conexão nova (com resolução DNS e handshake completos) a cada chamada. Como você resolveria isso, e qual seria o ganho esperado?

**Você:** Eu configuraria o cliente HTTP para usar **connection pooling com keep-alive**, reaproveitando conexões já estabelecidas para o mesmo destino em vez de abrir uma nova a cada requisição. Isso eliminaria, na grande maioria das chamadas subsequentes, tanto o custo de resolução DNS quanto o custo do handshake TCP/TLS inicial, que juntos costumam ser responsáveis por boa parte da latência "extra" observada — o ganho esperado seria justamente reduzir aqueles 100-200ms observados para próximo de zero na maior parte das chamadas, mantendo esse custo apenas na primeira conexão estabelecida (ou quando uma conexão precisa ser recriada por algum motivo, como um timeout de inatividade).

---

# TÓPICO 12: Configuração, Segredos e Variáveis de Ambiente

## Cenário 12.1: Rotação de uma chave de API quebra uma integração crítica

**Entrevistador:** O time de segurança, seguindo uma política de boas práticas, rotaciona periodicamente as chaves de API usadas para autenticação com um serviço externo. Depois da rotação mais recente, uma integração crítica para de funcionar, com erros de autenticação (401/403). Como você investigaria?

**Você:** Primeiro eu confirmaria a correlação temporal — o horário exato em que a integração começou a falhar bate com o horário da rotação da chave? Assumindo que sim, minha hipótese principal seria que **algum lugar do sistema ainda está usando a chave antiga**, que deixou de ser válida após a rotação, enquanto outro lugar (talvez o processo de rotação em si) atualizou a chave em apenas parte dos locais onde ela é utilizada.

**Entrevistador:** Como você localizaria exatamente **onde** essa chave antiga ainda está sendo usada, considerando que ela pode estar espalhada em múltiplos lugares (variáveis de ambiente, arquivos de configuração, um serviço de secrets management, cache da aplicação)?

**Você:** Eu mapearia sistematicamente todos os lugares onde essa chave poderia estar armazenada ou cacheada:
1. **Variáveis de ambiente** de cada instância/serviço que faz essa integração — é possível que a rotação tenha atualizado o serviço de secrets, mas as instâncias já em execução continuem com a variável de ambiente antiga carregada em memória desde a inicialização, sem um mecanismo de "reload" automático
2. **Cache da própria aplicação**: se a aplicação armazena a chave em algum cache interno (por exemplo, para evitar buscar do serviço de secrets a cada chamada), esse cache pode estar segurando o valor antigo além do esperado
3. **Múltiplas instâncias/ambientes**: é possível que a rotação tenha sido aplicada com sucesso em produção, mas esquecida em algum ambiente de contingência/disaster recovery, ou vice-versa
4. **Configuração hardcoded ou desatualizada em algum lugar não óbvio**: por exemplo, um script de automação ou um job agendado que usa a chave de forma separada do fluxo principal da aplicação, e que ninguém lembrou de atualizar durante a rotação

**Entrevistador:** Suponha que você descobre que o problema é o item 2: a aplicação cacheia a chave em memória com um TTL de 24 horas, e a rotação não teve um mecanismo de invalidar esse cache imediatamente. Como você corrigiria isso estruturalmente, para que a próxima rotação seja transparente?

**Você:** Eu implementaria um mecanismo de **invalidação ativa do cache de credenciais**, similar ao que vimos no tópico de cache — em vez de depender apenas de um TTL passivo, o processo de rotação de chave publicaria um evento (ou faria uma chamada explícita) avisando a aplicação para **descartar imediatamente** a credencial em cache e buscar a nova, assim que a rotação acontece, eliminando a janela de tempo em que a aplicação continuaria usando o valor antigo já invalidado.

**Entrevistador:** E como você desenharia o próprio processo de rotação, do lado de quem gera a chave nova, para reduzir ainda mais o risco de indisponibilidade durante futuras rotações?

**Você:** Eu adotaria uma estratégia de **rotação com sobreposição (overlap)**: em vez de invalidar a chave antiga no exato instante em que a nova é criada, eu manteria **ambas as chaves válidas simultaneamente** por um período de transição (por exemplo, algumas horas ou dias, dependendo da criticidade), dando tempo para que todos os consumidores daquela chave migrem para a nova de forma gradual e sem pressa, e só então revogaria a chave antiga definitivamente — o mesmo princípio de compatibilidade retroativa que vimos em migrações de banco e em contratos de API, aplicado agora a credenciais.

---

## Cenário 12.2: "Funciona no staging, mas não em produção" — divergência de configuração entre ambientes

**Entrevistador:** Um time reporta que uma nova funcionalidade funciona perfeitamente no ambiente de staging, mas falha de forma consistente em produção, com um comportamento visivelmente diferente (não é só "mais lento" — o comportamento é **funcionalmente distinto**). O código deployado é exatamente o mesmo nos dois ambientes. Como você investigaria essa diferença?

**Você:** Se o código é idêntico, mas o comportamento é diferente, a causa quase certamente está em algo **externo ao código**: configuração, variáveis de ambiente, feature flags, ou dados/infraestrutura específicos de cada ambiente. Eu começaria comparando, sistematicamente, **tudo que pode diferir** entre staging e produção:
1. Variáveis de ambiente e arquivos de configuração de cada ambiente
2. Valores de feature flags (algumas podem estar habilitadas em staging e desabilitadas em produção, ou vice-versa)
3. Versões de dependências externas (por exemplo, uma versão diferente de um serviço de terceiros, ou até uma versão diferente do próprio banco de dados)
4. Volume e formato dos dados reais (produção geralmente tem dados mais antigos, mais variados e com mais "sujeira" acumulada do que um ambiente de staging recém-populado com dados de teste)

**Entrevistador:** Suponha que você encontra uma feature flag específica habilitada em staging, mas desabilitada em produção — e o comportamento errado só acontece quando essa flag está desabilitada. Isso resolve o mistério, mas o que essa descoberta revela sobre um risco maior no processo de testes desse time?

**Você:** Isso revela uma lacuna importante: o time estava **testando efetivamente apenas um dos dois caminhos possíveis** (com a flag habilitada), sem testar explicitamente o caminho que de fato está ativo em produção (com a flag desabilitada). Isso é um risco recorrente em qualquer sistema com feature flags: cada combinação relevante de flags representa, na prática, um **caminho de código diferente**, e "funcionar em staging" só significa algo se staging estiver testando a **mesma combinação de configuração** que produção está usando de verdade.

**Entrevistador:** Como você mudaria o processo de testes/deploy desse time para evitar esse tipo de surpresa no futuro?

**Você:** Eu recomendaria:
1. **Sincronizar os valores de feature flags entre staging e produção** por padrão, a menos que exista uma razão explícita e documentada para divergirem (por exemplo, uma funcionalidade sendo testada exclusivamente em staging antes do lançamento)
2. Quando uma flag **precisar** ter valores diferentes entre ambientes por um período (o que é legítimo, por exemplo, durante o desenvolvimento incremental de uma funcionalidade nova), garantir que os testes automatizados cubram **ambos os estados** da flag, não só o estado "ligado"
3. Adotar uma ferramenta ou processo de **detecção de "config drift"** (divergência de configuração) entre ambientes, que alerte automaticamente quando staging e produção divergem em algo além do esperado (por exemplo, credenciais e endpoints específicos de cada ambiente, que devem mesmo ser diferentes, versus flags de comportamento, que idealmente deveriam ser iguais)

---

## Cenário 12.3: Feature flag mal configurada afeta um subconjunto inesperado de usuários

**Entrevistador:** Um time lança uma nova funcionalidade atrás de uma feature flag, configurada para ser exibida a apenas 10% dos usuários, de forma aleatória, como parte de um teste controlado (A/B test). Poucas horas depois, vocês notam que a funcionalidade nova está aparecendo para **100% dos usuários de um país específico**, não para uma amostra aleatória de 10% como planejado. Como você investigaria essa discrepância?

**Você:** Isso sugere que a lógica de **segmentação da flag** não está funcionando como o time pretendia — em vez de uma seleção verdadeiramente aleatória e uniforme entre todos os usuários, parece que algum critério (nesse caso, aparentemente o país) está influenciando desproporcionalmente quem recebe a funcionalidade nova. Eu investigaria diretamente a configuração da regra da flag na ferramenta de feature flags utilizada, procurando por qualquer condição, filtro, ou regra de segmentação configurada além do simples "10% aleatório" pretendido.

**Entrevistador:** Suponha que você encontra a causa: a lógica de "seleção aleatória de 10%" usa, internamente, um hash do `user_id` do usuário para decidir de forma determinística (e consistente entre sessões) se aquele usuário está no grupo do experimento ou não. E descobre que, por coincidência, os `user_ids` dos usuários daquele país específico foram todos gerados em um formato ou intervalo particular (por exemplo, esse país foi o primeiro a ser lançado na plataforma, e por isso tem IDs sequenciais mais baixos), fazendo com que o algoritmo de hash, de forma não intencional, os classifique de forma desproporcional dentro do grupo dos 10%. O que essa descoberta te ensina sobre os riscos de usar um algoritmo de hash simples para segmentação aleatória?

**Você:** Isso revela um problema sutil e importante: um algoritmo de hash que não é bem distribuído (ou que é aplicado sobre um conjunto de IDs que não é uniformemente aleatório, como IDs sequenciais) pode produzir **agrupamentos não intencionais e correlacionados com outras características dos dados** (nesse caso, país, que está correlacionado com a faixa de IDs sequenciais), mesmo que a intenção fosse uma seleção puramente aleatória e sem correlação nenhuma. Isso é particularmente perigoso em testes A/B, porque um resultado de experimento pode ficar **viesado** sem ninguém perceber, caso o grupo do experimento acabe, sem querer, super-representando um segmento específico de usuários.

**Entrevistador:** Como você corrigiria isso, e como validaria que a correção realmente produz uma distribuição uniforme entre os grupos?

**Você:** Eu trocaria o algoritmo de hash usado para segmentação por um que tenha propriedades de **distribuição uniforme comprovadas** mesmo diante de entradas sequenciais ou correlacionadas (por exemplo, aplicando uma função de hash criptográfica bem estabelecida sobre o `user_id`, em vez de uma função simples e ingênua). Para validar, eu rodaria essa nova lógica de segmentação contra a base real de usuários (ou uma amostra representativa dela) **antes** de usá-la em produção, verificando explicitamente se a distribuição resultante do grupo de 10% está de fato equilibrada entre diferentes segmentos conhecidos (país, data de cadastro, faixa de ID, etc.) — e não apenas confiando que "parece aleatório o suficiente" sem essa checagem explícita.

---

## Resumo dos conceitos e técnicas usados nessa entrevista

**Filas e Mensageria**
- Diferenciar gargalo do producer, do consumer, ou de uma dependência do consumer, usando taxa de produção vs consumo
- Poison message e Dead Letter Queue (DLQ), evitando que uma mensagem problemática bloqueie toda a fila
- At-least-once delivery, idempotência e uso de `event_id`/sequência para lidar com duplicidade e ordem (ver [06 - Filas e Mensageria](./06-filas-e-mensageria.md))

**Banco de Dados**
- Conexões esgotadas e connection leak vs aumento real de tráfego
- Deadlock entre transações e a importância de ordem consistente de locks
- Replication lag e o padrão "read your own writes" (ver [05 - Bancos de Dados](./05-bancos-de-dados-sql-nosql.md) e [02 - CAP Theorem](./02-cap-theorem.md))

**Latência de API**
- Investigação em camadas com tracing distribuído, `EXPLAIN`/`EXPLAIN ANALYZE` e o problema de N+1 queries
- Diferença entre p50 normal e p99 alto, e como isolar causas raras (pausas de GC, noisy neighbor)
- Propagação de orçamento de timeout (deadline propagation) em cadeias de chamada entre serviços

**Alto Volume e Capacidade**
- Escalabilidade horizontal, profiling de CPU e quando adicionar servidores não resolve (ver [01 - Escalabilidade](./01-escalabilidade.md))
- Gargalo de I/O vs CPU, e chamadas assíncronas/não-bloqueantes
- Rate limit de dependências externas e filas com controle de vazão

**Concorrência**
- Race condition e o padrão "check-then-act", com lock otimista, lock pessimista e operações atômicas
- Idempotência para evitar processamento duplicado por retries do cliente
- Deadlock entre threads dentro da própria aplicação e ordenação consistente de locks

**Memória**
- Memory leak vs subdimensionamento, diferenciados pelo padrão de crescimento (nunca estabiliza vs platô)
- Processamento de payloads grandes via streaming, em vez de carregar tudo na memória de uma vez
- Pausas de Garbage Collector e como reduzir seu impacto na latência

**Cache**
- Cache stampede/thundering herd e mitigação via lock de repopulação, refresh-ahead e TTL com jitter (ver [03 - Cache](./03-cache.md))
- Inconsistência de cache entre múltiplas instâncias e a escolha entre cache compartilhado e invalidação ativa
- Cache "envenenado" com erro, e a importância de só cachear resultados de sucesso confirmado

**Deploys e Releases**
- Priorizar rollback (mitigação) sobre investigação da causa raiz em incidentes críticos
- Degradação silenciosa (dado errado sem exceção) e a importância de métricas de negócio, não só técnicas
- Migrações de banco retrocompatíveis, para que um rollback de código nunca quebre por incompatibilidade de schema

**Falhas em Cascata e Microsserviços**
- Timeouts, circuit breaker e bulkhead pattern para conter falhas entre serviços
- Retry storm e como desenhar retries com backoff exponencial, jitter e limite máximo de tentativas
- Versionamento e evolução retrocompatível de contratos de API entre serviços

**Índices de Banco de Dados**
- O trade-off fundamental entre índices: leitura mais rápida em troca de escrita mais lenta, já que todo índice precisa ser mantido a cada `INSERT`/`UPDATE`/`DELETE`
- Índices compostos e a regra do prefixo mais à esquerda (leftmost prefix rule): a ordem das colunas determina quais consultas conseguem de fato aproveitar o índice
- Over-indexing e como identificar índices não utilizados ou redundantes usando estatísticas de uso do próprio banco (ver [05 - Bancos de Dados](./05-bancos-de-dados-sql-nosql.md))

**Rede, DNS e Certificados**
- Monitoramento proativo de expiração de certificados TLS, para não depender de descobrir o problema só quando ele já derrubou o sistema
- Propagação de DNS e TTL: por que uma mudança de DNS afeta usuários em momentos diferentes, e como planejar a transição com antecedência
- Cache de DNS e connection pooling/keep-alive como forma de eliminar latência repetida de resolução de nome e handshake de conexão

**Configuração, Segredos e Variáveis de Ambiente**
- Rotação de credenciais com sobreposição (overlap) entre chave antiga e nova, evitando indisponibilidade durante a transição
- Divergência de configuração entre ambientes ("config drift") como causa comum de bugs que só aparecem em produção
- Riscos de algoritmos de hash mal distribuídos em segmentação de feature flags, e como isso pode enviesar testes A/B sem ninguém perceber

---
**Anterior:** [10 - Entrevista WhatsApp](./10-entrevista-whatsapp.md)
