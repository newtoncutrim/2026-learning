# Filas e Mensageria (Asynchronism)

> Nem toda tarefa precisa ser feita "na hora" enquanto o usuário espera. Filas permitem processar coisas em background.

## Por que usar processamento assíncrono

Algumas operações são lentas ou custosas (enviar email, gerar relatório, processar vídeo, redimensionar imagem). Se você fizer o usuário esperar por isso de forma síncrona, a experiência fica ruim e o servidor fica ocupado por mais tempo que o necessário.

A solução: em vez de processar na hora, você **coloca a tarefa em uma fila** e responde ao usuário imediatamente. Um processo separado (worker) vai lá na fila, pega a tarefa e processa, sem travar o fluxo principal.

Fluxo típico:
1. Aplicação publica uma tarefa na fila
2. Aplicação responde ao usuário na hora (ex: "seu pedido está sendo processado")
3. Um worker consome a tarefa da fila, processa, e (se necessário) avisa quando terminar

**Exemplo clássico:** ao postar algo no Twitter, o post aparece instantaneamente no seu perfil, mas a distribuição para todos os seus seguidores pode acontecer de forma assíncrona, alguns milissegundos depois.

## Message Queue vs Task Queue

Esses dois termos são usados de forma um pouco intercambiável, mas há uma diferença sutil:

- **Message Queue**: focado em entregar **mensagens** entre sistemas/serviços (ex: RabbitMQ, Amazon SQS). Um serviço publica um evento, outro(s) consomem.
- **Task Queue**: focado em executar **tarefas/jobs** específicos, geralmente com suporte a agendamento e retry (ex: Celery). É mais "execute essa função com esses parâmetros" do que "entregue essa mensagem".

Na prática, ferramentas modernas (como Amazon SQS + Lambda, ou RabbitMQ + workers) cobrem os dois casos de uso.

## Ferramentas comuns

- **Redis**: pode funcionar como um broker de mensagens simples, mas com risco de perda de mensagens em caso de falha
- **RabbitMQ**: popular, segue o protocolo AMQP, exige que você administre seus próprios nós
- **Amazon SQS**: totalmente gerenciado pela AWS, mas pode ter latência maior e permite entrega duplicada de mensagens (por isso o processamento precisa ser **idempotente** — processar a mesma mensagem duas vezes não deve causar efeito colateral duplicado)
- **Apache Kafka**: voltado para streaming de eventos em altíssima escala, muito usado quando você precisa que múltiplos consumidores processem o mesmo stream de eventos de formas diferentes

## Back Pressure (Contrapressão)

O que acontece quando a fila cresce mais rápido do que os workers conseguem processar? Se você deixar crescer sem limite, a fila pode consumir toda a memória disponível, causando lentidão generalizada.

**Back pressure** é a estratégia de limitar o tamanho da fila. Quando ela enche:
- Novos itens são rejeitados (o cliente recebe um erro tipo "servidor ocupado", HTTP 503)
- O cliente pode tentar de novo depois, idealmente com **exponential backoff** (esperar um tempo crescente entre tentativas, para não sobrecarregar ainda mais o sistema)

## Quando NÃO usar processamento assíncrono

Nem tudo deveria ser assíncrono. Operações muito rápidas e que o usuário precisa ver o resultado imediatamente (ex: validar uma senha no login) geralmente são melhores de forma síncrona — introduzir uma fila aqui só adicionaria complexidade e atraso desnecessários.

## Como isso aparece em entrevistas

Filas costumam aparecer quando o sistema precisa:
- Lidar com picos de tráfego sem derrubar o backend (a fila funciona como um "amortecedor")
- Desacoplar serviços diferentes (ex: serviço de pedidos não precisa saber diretamente como o serviço de envio de emails funciona)
- Processar tarefas pesadas sem travar a experiência do usuário

Um ponto que impressiona entrevistadores: mencionar que mensagens podem ser entregues **mais de uma vez** (at-least-once delivery) e que por isso o processamento deve ser **idempotente**.

## Leituras complementares (em inglês)

- [Applying back pressure when overloaded](http://mechanical-sympathy.blogspot.com/2012/05/apply-back-pressure-when-overloaded.html)
- [What is the difference between a message queue and a task queue?](https://www.quora.com/What-is-the-difference-between-a-message-queue-and-a-task-queue)

---
**Anterior:** [05 - Bancos de Dados SQL vs NoSQL](./05-bancos-de-dados-sql-nosql.md)
