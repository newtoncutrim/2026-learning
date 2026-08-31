# Perguntas & Respostas para Entrevista — Dev Pleno/Sênior

> Foco: **Fluxo de Desenvolvimento**, **Comportamento**, **Trabalho sob Pressão**, **Tomada de Decisão** e **IA aplicada ao desenvolvimento**.
> Inspirado na trilha técnica de [newtoncutrim/2026-learning](https://github.com/newtoncutrim/2026-learning) (plano de estudos backend-heavy voltado a entrevistas pleno/sênior) — aqui o recorte é o lado **de processo, soft skills e IA** que complementa a parte técnica de algoritmos, banco de dados e arquitetura.

---

## Como usar este arquivo

Cada pergunta vem com:
- **O que o entrevistador quer ouvir** — o critério real de avaliação por trás da pergunta.
- **Resposta-modelo** — um exemplo de resposta boa, adaptável à sua experiência real.
- Quando aplicável, a estrutura **STAR** (Situação, Tarefa, Ação, Resultado) é sugerida.

> Dica: nunca decore a resposta-modelo. Substitua pelos seus próprios casos reais — entrevistadores seniores percebem respostas genéricas rapidamente.

---

## Índice

1. [Fluxo de Desenvolvimento](#1-fluxo-de-desenvolvimento)
2. [Comportamento](#2-comportamento)
3. [Trabalho sob Pressão](#3-trabalho-sob-pressão)
4. [Tomada de Decisão](#4-tomada-de-decisão)
5. [IA no Desenvolvimento](#5-ia-no-desenvolvimento)
6. [Checklist rápido antes da entrevista](#6-checklist-rápido-antes-da-entrevista)

---

## 1. Fluxo de Desenvolvimento

### 1.1 Descreva o fluxo de desenvolvimento que você segue, do ticket até a produção.

**O que avaliam:** se você entende o ciclo completo (não só "escrevo código"), incluindo planejamento, review, testes, deploy e monitoramento pós-release.

**Resposta-modelo:**
> "Normalmente o fluxo começa no refinamento do ticket — entender critério de aceite e dependências antes de estimar. Depois crio uma branch a partir da main/develop, desenvolvo com testes junto (não depois), abro um PR pequeno e objetivo, peço review de pelo menos uma pessoa, ajusto conforme feedback, e só faço merge com CI verde. Em produção, acompanho métricas e logs nas primeiras horas para garantir que não hajam regressões silenciosas."

---

### 1.2 Como você lida com um PR que ficou parado há dias esperando review?

**O que avaliam:** proatividade e comunicação, não só reclamação passiva.

**Resposta-modelo:**
> "Primeiro verifico se o PR está claro o suficiente — às vezes o review trava porque falta contexto. Se está claro, sinalizo diretamente para quem precisa revisar, seja no canal do time ou individualmente, explicando o impacto do atraso. Se for recorrente no time, levanto isso como um problema de processo — por exemplo, sugerindo um SLA de review ou revezamento de responsáveis."

---

### 1.3 O que você faz quando encontra débito técnico durante uma tarefa não relacionada a ele?

**O que avaliam:** equilíbrio entre entregar o que foi pedido e não ignorar problemas — sem "boiar o escopo" sem avisar.

**Resposta-modelo:**
> "Eu não paro a tarefa atual para resolver o débito na hora, a menos que ele bloqueie diretamente o que estou fazendo. Documento o problema (ticket, comentário no código, ou conversa com o time) e avalio com o time se vale a pena resolver agora, depois, ou nunca — depende do risco e do custo de não resolver."

---

### 1.4 Como você garante qualidade em um time onde nem todos escrevem testes?

**O que avaliam:** liderança técnica sem ser autoritário, e senso prático (nem tudo se resolve com regra).

**Resposta-modelo:**
> "Prefiro liderar pelo exemplo: escrevo testes no meu próprio código e mostro o valor disso em review (ex: um bug que o teste teria pego). Também sugiro, em vez de impor, práticas simples como cobertura mínima em CI para código novo, e discuto com o time o motivo da resistência — às vezes é falta de tempo, às vezes falta de conhecimento da ferramenta."

---

### 1.5 Como você lida com estimativas que sempre estouram?

**O que avaliam:** maturidade em planejamento, não perfeccionismo ou otimismo cego.

**Resposta-modelo:**
> "Estimativa estourada repetidamente costuma ser sintoma, não causa. Eu reviso: as tarefas estão granuladas o suficiente? Existem dependências externas não mapeadas? O time está estimando otimista demais por pressão? Prefiro quebrar tarefas grandes em partes menores e comparar estimativa vs. realizado ao longo do tempo, para calibrar."

---

## 2. Comportamento

> Use sempre a estrutura **STAR**: Situação → Tarefa → Ação → Resultado. Feche toda resposta com o resultado/aprendizado — é o ponto mais esquecido pelos candidatos.

### 2.1 Conte sobre um conflito que teve com um colega ou gestor.

**O que avaliam:** maturidade emocional, capacidade de resolver sem escalar desnecessariamente, e se você assume parte da responsabilidade (em vez de só culpar o outro).

**Resposta-modelo (estrutura):**
> "Situação: discordei de uma decisão técnica de um colega sênior sobre a abordagem de uma feature. Tarefa: eu achava que a solução dele geraria problema de performance a médio prazo. Ação: em vez de discutir publicamente no PR, chamei uma conversa 1:1, expliquei minha preocupação com dados concretos (benchmark simples), e ouvi o ponto de vista dele. Resultado: chegamos a uma solução híbrida, e isso virou prática — discutir decisões técnicas maiores em conversa antes do PR."

---

### 2.2 Fale sobre um erro que você cometeu em produção.

**O que avaliam:** accountability (assume o erro sem se autoflagelar) e o que mudou depois — processo, não só "aprendi a lição".

**Resposta-modelo:**
> "Fiz um deploy sem validar uma migration em um ambiente com volume de dados maior que o esperado, e isso travou uma tabela por alguns minutos em horário de pico. Assumi o erro imediatamente com o time, ajudei a mitigar (rollback), e depois propusemos um checklist de pré-deploy para migrations em tabelas grandes, incluindo teste em uma cópia de produção antes do release."

---

### 2.3 Como você dá feedback difícil para um colega?

**O que avaliam:** se você tem estrutura (não é feedback vago ou passivo-agressivo) e se prioriza a relação/o resultado, não "estar certo".

**Resposta-modelo:**
> "Prefiro feedback direto, privado e focado em comportamento observável — não em traço de personalidade. Por exemplo, em vez de 'você não se importa com qualidade', digo 'nos últimos três PRs os testes não cobriram os casos de erro, isso está gerando retrabalho — como posso ajudar nisso?'. Isso abre espaço para diálogo em vez de defesa."

---

### 2.4 Descreva uma situação em que você discordou de uma decisão da liderança.

**O que avaliam:** "disagree and commit" — se você sabe expressar discordância de forma construtiva e, ao final, executar a decisão tomada (mesmo não sendo a sua preferida), sem sabotagem passiva.

**Resposta-modelo:**
> "Discordei da escolha de uma tecnologia nova para um projeto crítico, por achar que a curva de aprendizado do time atrasaria a entrega. Expus meus argumentos com dados (tempo estimado de ramp-up, exemplos de riscos), mas a decisão final ficou com a liderança, que tinha contexto de negócio que eu não tinha. Uma vez decidido, me comprometi 100% com a execução e até ajudei a criar material de estudo para acelerar o time."

---

### 2.5 Como você lida com receber feedback negativo?

**O que avaliam:** abertura genuína, sem defensividade nem drama.

**Resposta-modelo:**
> "Tento primeiro entender o feedback antes de reagir — faço perguntas para ter exemplos concretos. Depois, separo o que é ajustável rapidamente do que exige mudança de hábito mais longa, e crio um plano pequeno (ex: pedir para o próprio colega revisar de novo em duas semanas)."

---

## 3. Trabalho sob Pressão

### 3.1 Descreva uma entrega com prazo muito apertado. Como você conduziu?

**O que avaliam:** priorização com critério (não "trabalhei até tarde"), e comunicação de risco antecipada.

**Resposta-modelo (STAR):**
> "Situação: duas semanas antes de um lançamento importante, o escopo cresceu porque um requisito de compliance apareceu tarde. Tarefa: entregar dentro do prazo sem comprometer segurança. Ação: mapeei com o time o que era essencial vs. o que podia ficar para uma segunda fase, comuniquei o risco ao gestor no mesmo dia (não esperei o prazo chegar), e redistribuí tarefas conforme força de cada pessoa. Resultado: entregamos o essencial no prazo, e o restante foi lançado uma semana depois, sem impacto no cliente."

---

### 3.2 Como você reage quando várias prioridades urgentes chegam ao mesmo tempo?

**O que avaliam:** critério de priorização real (impacto x urgência x esforço), não "faço tudo ao mesmo tempo".

**Resposta-modelo:**
> "Primeiro busco entender o impacto real de cada uma — nem tudo que é chamado de 'urgente' é, de fato. Uso algo como impacto x esforço para decidir ordem, e negocio prazos abertamente com quem pediu, em vez de aceitar tudo silenciosamente e falhar depois."

---

### 3.3 Conte sobre um incidente crítico em produção que você ajudou a resolver.

**O que avaliam:** clareza sob estresse, comunicação durante a crise, e ação pós-incidente (post-mortem).

**Resposta-modelo:**
> "Durante um pico de tráfego, um serviço começou a retornar erro 500 para parte dos usuários. Priorizei estabilizar (rollback de um deploy recente que coincidiu com o horário) antes de investigar a causa raiz a fundo. Mantive o time e os stakeholders informados a cada 15-20 minutos, mesmo sem solução ainda. Depois do incidente, fizemos um post-mortem sem culpados, identificamos falta de um teste de carga antes do deploy, e isso virou item obrigatório do checklist de release."

---

### 3.4 Como você evita o burnout quando a pressão é constante, não pontual?

**O que avaliam:** sustentabilidade — se sua resposta padrão para pressão contínua é "aguentar" (sinal de alerta) ou "ajustar processo/escopo" (sinal saudável).

**Resposta-modelo:**
> "Pressão pontual eu absorvo, mas pressão constante costuma ser sintoma de escopo maior que a capacidade do time, e isso eu trago para discussão explícita com a liderança, com dados — não é sustentável resolver estrutural com esforço individual repetido."

---

## 4. Tomada de Decisão

### 4.1 Como você decide entre duas soluções técnicas concorrentes (ex: reescrever vs. remendar)?

**O que avaliam:** raciocínio estruturado, não só preferência técnica pessoal.

**Resposta-modelo:**
> "Avalio custo de curto prazo vs. risco de longo prazo: quanto tempo o remendo resolve o problema, e por quanto tempo a dívida técnica que ele gera é aceitável? Se o remendo resolve por semanas mas o problema vai voltar em escala maior, geralmente vale investir na reescrita parcial. Documento essa análise para o time entender o motivo, não só a decisão."

---

### 4.2 Já teve que tomar uma decisão com dados incompletos? O que fez?

**O que avaliam:** capacidade de agir sob incerteza sem paralisar, e se você comunica o nível de confiança da decisão.

**Resposta-modelo:**
> "Sim — em uma decisão de arquitetura, não tínhamos dados reais de volume esperado. Optei por uma solução que era 'boa o suficiente e reversível' em vez de tentar prever o volume perfeitamente, e deixei claro para o time que essa decisão seria revisada em X meses com dados reais."

---

### 4.3 Como você reverte uma decisão que se mostrou errada?

**O que avaliam:** ego baixo, comunicação transparente do erro, sem esconder ou minimizar.

**Resposta-modelo:**
> "Assumo publicamente que a decisão não funcionou como esperado, explico o que mudou de contexto ou o que não considerei, e proponho o caminho de correção com o menor custo de retrabalho possível. Prefiro isso a manter uma decisão ruim só para não admitir erro."

---

### 4.4 Como você prioriza entre "fazer rápido" e "fazer certo"?

**O que avaliam:** senso de trade-off — não existe resposta certa fixa, o entrevistador quer ver o raciocínio.

**Resposta-modelo:**
> "Depende do custo de reverter. Para uma feature nova e ainda incerta no mercado, prefiro rápido e simples — vou aprender com o uso real. Para algo que mexe com dados críticos, segurança, ou que será difícil de mudar depois (ex: modelo de dados core), prefiro investir mais tempo fazendo certo, porque o custo de retrabalho ali é muito maior."

---

### 4.5 Como você lida quando sua decisão desagrada parte do time?

**O que avaliam:** liderança madura — decidir sem precisar de consenso total, mas sem ignorar as objeções.

**Resposta-modelo:**
> "Escuto a objeção antes de decidir, não depois. Se decidir diferente do que a pessoa queria, explico o motivo específico da escolha, para que ela entenda que foi ouvida, mesmo discordando do resultado."

---

## 5. IA no Desenvolvimento

### 5.1 Como você usa IA no seu dia a dia de desenvolvimento?

**O que avaliam:** uso real (não discurso), com exemplos concretos e recentes.

**Resposta-modelo:**
> "Uso principalmente para acelerar tarefas repetitivas: boilerplate, testes unitários, entender uma stack trace complexa, ou uma primeira versão de uma função que depois refino manualmente. Também uso para explicar código legado que não escrevi, e para revisar meu próprio código antes de abrir um PR, como uma segunda opinião rápida."

---

### 5.2 Você confia no código gerado por IA? Como valida?

**O que avaliam:** senso crítico — se você trata a IA como fonte de verdade ou como sugestão a ser checada.

**Resposta-modelo:**
> "Trato como um rascunho, nunca como pronto. Leio linha a linha, rodo os testes, e se for algo que usa uma API ou biblioteca que não conheço bem, confirmo na documentação oficial — já vi IA sugerir métodos que não existem (alucinação de API), então isso é hábito, não exceção."

---

### 5.3 Já teve um caso em que a IA gerou algo errado ou arriscado?

**O que avaliam:** experiência real e consciência de risco, incluindo segurança e dados sensíveis.

**Resposta-modelo:**
> "Sim — pedi ajuda para uma função de validação e ela veio sem tratar um caso de borda importante (input vazio), que só percebi porque escrevi o teste antes de aceitar o código. Isso reforçou meu hábito de sempre ter teste como critério de aceite do código gerado, não só leitura visual."

---

### 5.4 Onde você não usaria IA no seu processo?

**O que avaliam:** limites claros — sinal de responsabilidade técnica, não medo da ferramenta.

**Resposta-modelo:**
> "Evito usar IA para decisões de arquitetura crítica sem validação humana, para lógica de negócio sensível (financeiro, saúde, segurança), e nunca colo dados sensíveis ou credenciais em prompts, mesmo em ferramentas internas."

---

### 5.5 Como a IA muda o papel de um desenvolvedor pleno/sênior?

**O que avaliam:** visão de carreira e adaptação — não discurso apocalíptico nem negação da mudança.

**Resposta-modelo:**
> "Acho que reduz o tempo gasto em tarefas mecânicas e aumenta a importância de habilidades como revisão crítica, arquitetura, entendimento profundo do problema de negócio e comunicação — porque a parte 'escrever sintaxe' fica mais barata, mas entender o que deveria ser construído continua sendo humano."

---

### 5.6 Como você formula prompts para tarefas de código complexas?

**O que avaliam:** maturidade técnica na interação com a ferramenta.

**Resposta-modelo:**
> "Dou contexto claro (linguagem, framework, restrições do projeto, convenções do time), peço para a IA explicar o raciocínio junto com o código gerado, e itero em vez de aceitar a primeira resposta — geralmente peço para revisar o próprio código dela em busca de problemas antes de eu mesmo revisar."

---

## 6. Checklist rápido antes da entrevista

- [ ] Tenho pelo menos **2 histórias reais** prontas para cada tema (fluxo, comportamento, pressão, decisão, IA) — não genéricas.
- [ ] Toda história tem **resultado/aprendizado** no final, não só a ação.
- [ ] Sei citar **uma ferramenta de IA específica** que uso e **um exemplo concreto** da última semana.
- [ ] Consigo explicar **um erro meu** sem soar defensivo nem se autoflagelar.
- [ ] Tenho pelo menos **um exemplo de decisão técnica revertida** e o que aprendi com isso.
- [ ] Sei separar, em qualquer resposta, "o que eu fiz" de "o que o time fez" — sem apagar o time nem se esconder atrás dele.

---

*Documento gerado como material de apoio a estudos para entrevistas de desenvolvedor pleno/sênior, cobrindo a dimensão de processo e comportamento — complementar à trilha técnica (algoritmos, banco de dados, arquitetura, sistemas distribuídos) de referências como [2026-learning](https://github.com/newtoncutrim/2026-learning).*
