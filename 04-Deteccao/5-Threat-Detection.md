# Threat Detection

## Introdução

Threat Detection é a capacidade de identificar comportamentos que podem representar uma ameaça dentro de um ambiente.

Em um SOC, isso significa muito mais do que procurar por malware conhecido.

Um atacante pode trocar:

```text
Hash
IP
Domínio
Arquivo
Infraestrutura
```

mas ainda precisa executar ações para alcançar seu objetivo.

Ele precisa executar código.

Obter credenciais.

Escalar privilégios.

Descobrir o ambiente.

Mover-se lateralmente.

Manter acesso.

Comunicar-se com sua infraestrutura.

Exfiltrar dados.

É nesses comportamentos que uma estratégia de **Threat Detection** busca atuar.

---

# De evento para ameaça

Um ambiente corporativo gera milhares ou milhões de eventos.

Exemplo:

```text id="e4x7q2"
Login
DNS
Firewall
Processos
PowerShell
HTTP
VPN
Active Directory
Cloud
Endpoint
Aplicações
```

A maioria desses eventos é legítima.

O desafio é identificar quando vários deles começam a formar um padrão.

```text id="n6v3k8"
Evento
   ↓
Comportamento
   ↓
Contexto
   ↓
Correlação
   ↓
Hipótese
   ↓
Detecção
   ↓
Investigação
```

Threat Detection existe justamente para transformar grandes volumes de telemetria em **sinais de segurança relevantes**.

---

# Threat Detection x Threat Intelligence

Os conceitos estão relacionados, mas não são a mesma coisa.

### Threat Intelligence

Busca entender:

```text id="c2m7p9"
Quem?
O quê?
Como?
Por quê?
```

Pode fornecer:

* IoCs;
* TTPs;
* grupos de ameaça;
* campanhas;
* infraestrutura;
* malware;
* técnicas utilizadas.

### Threat Detection

Busca responder:

> **"Como podemos identificar esse comportamento acontecendo no nosso ambiente?"**

Exemplo:

```text id="k8r4w1"
Threat Intelligence
        ↓
Grupo utiliza PowerShell
        ↓
Técnica identificada
        ↓
Threat Detection
        ↓
Criar lógica para identificar o comportamento
```

A inteligência ajuda a entender a ameaça.

A detecção transforma esse conhecimento em capacidade operacional.

---

# Estratégia de detecção

Uma estratégia de Threat Detection pode ser dividida em camadas.

```text id="s7f1m3"
IOC Detection
      ↓
Behavior Detection
      ↓
Correlation
      ↓
Anomaly Detection
      ↓
Threat Hunting
```

Cada camada possui uma função.

### IOC Detection

Procura indicadores conhecidos.

### Behavior Detection

Procura comportamentos suspeitos.

### Correlation

Relaciona eventos diferentes.

### Anomaly Detection

Procura desvios em relação ao comportamento esperado.

### Threat Hunting

Busca ativamente sinais que podem não ter gerado alertas.

Uma estratégia madura combina todas essas abordagens.

---

# IOC Detection

A camada mais direta utiliza indicadores conhecidos.

Exemplo:

```text id="b5q8z2"
DNS Request
       ↓
malicious-domain.com
       ↓
Threat Intelligence Match
       ↓
Alert
```

Outra possibilidade:

```text id="m4v7x1"
File Hash
       ↓
Threat Intelligence
       ↓
Known Malware
       ↓
Alert
```

Essa abordagem é eficiente quando o indicador é confiável.

Porém, possui uma limitação:

> **o que é conhecido pode ser alterado.**

Por isso, IOC Detection não deve ser a única camada.

---

# Behavior Detection

A detecção baseada em comportamento procura ações que podem indicar abuso.

Exemplo:

```text id="u2r9c5"
WINWORD.EXE
      ↓
PowerShell
      ↓
EncodedCommand
      ↓
Internet
```

Nenhum desses eventos isoladamente precisa significar comprometimento.

Mas a combinação é relevante.

Outro exemplo:

```text id="x8k3v6"
Conta comum
      ↓
Adicionada a grupo administrativo
      ↓
Fora do horário
      ↓
Por uma conta incomum
```

A detecção passa a considerar **o comportamento e o contexto**.

---

# Detecção baseada em TTPs

Uma abordagem ainda mais resiliente é detectar TTPs — **Tactics, Techniques and Procedures**.

Imagine que um atacante utilize:

```text id="p3n7s2"
Malware A
```

A regra baseada em hash detecta o malware.

Mas o atacante troca para:

```text id="d6r1m8"
Malware B
```

A regra pode deixar de funcionar.

Agora imagine que os dois malwares utilizem:

```text id="j5w8q4"
PowerShell
+
Credential Access
+
Scheduled Task
+
C2
```

As ferramentas mudaram.

O comportamento permaneceu.

Por isso, detectar TTPs pode aumentar a resiliência da estratégia.

---

# MITRE ATT&CK como mapa

O MITRE ATT&CK pode ser utilizado como referência para estruturar essa cobertura.

Exemplo:

```text id="v9m2k5"
Tática
Execution

        ↓

Técnica
Command and Scripting Interpreter

        ↓

Subtécnica
PowerShell

        ↓

Telemetria
Process Creation
PowerShell Logs

        ↓

Detecção
Execução suspeita
```

Esse processo pode ser repetido para diferentes técnicas.

O objetivo não é simplesmente "cobrir o MITRE".

É descobrir:

> **quais comportamentos relevantes conseguimos observar e quais ainda são invisíveis para o SOC?**

---

# Telemetria

Não existe boa detecção sem dados suficientes.

Antes de criar uma regra, precisamos entender quais evidências estão disponíveis.

Exemplo de endpoint:

```text id="e9w3k7"
Process
Parent Process
Command Line
User
Hash
Network Connection
File Creation
Registry
Authentication
```

Exemplo de rede:

```text id="t2q6m1"
Source IP
Destination IP
Port
Protocol
Domain
URL
Bytes
Timestamp
```

Exemplo de identidade:

```text id="r7v4n2"
User
Source
Authentication Method
Device
Location
Result
Timestamp
```

Quanto melhor a telemetria, maior a capacidade de criar detecções contextualizadas.

---

# A importância do contexto

Considere:

```text id="h4p8c2"
Login bem-sucedido
```

Sozinho, esse evento possui pouca informação.

Agora:

```text id="a7k3m9"
Login bem-sucedido
+
usuário administrativo
+
país incomum
+
novo dispositivo
+
horário incomum
+
acesso a servidor crítico
```

A análise muda completamente.

O evento deixou de ser apenas um login.

Agora existe uma **anomalia contextualizada**.

---

# Behavioral Baseline

Uma técnica útil é criar uma linha de base do comportamento normal.

Exemplo:

```text id="w5m1q8"
Usuário:
08:00 → 18:00

Hosts normalmente acessados:
10.0.10.10
10.0.10.20

Aplicações:
Microsoft 365
VPN
Sistema interno
```

Um evento aparece:

```text id="k9v2r6"
03:14
Novo dispositivo
Novo IP
Servidor crítico
```

Isso não significa automaticamente ataque.

Mas representa um desvio que merece análise.

O baseline ajuda o SOC a identificar:

> **"Isso é normal para esse usuário, dispositivo ou aplicação?"**

---

# Detecção baseada em risco

Nem todo comportamento suspeito possui o mesmo impacto.

Uma forma de pensar é:

```text id="m3x7q1"
Risco =
Probabilidade
×
Impacto
```

Na prática, a detecção pode incorporar informações como:

```text id="d8n4v5"
Criticidade do ativo
+
Privilégio do usuário
+
Confiança da detecção
+
Sensibilidade do dado
+
Contexto da ameaça
```

Exemplo:

```text id="c6q2s9"
PowerShell suspeito

Workstation comum
→ risco moderado

Servidor de banco
→ risco elevado

Domain Controller
→ risco crítico
```

A mesma atividade pode exigir respostas completamente diferentes dependendo do ativo afetado.

---

# Detection Engineering

Threat Detection também se aproxima diretamente de **Detection Engineering**.

O processo pode ser representado por:

```text id="r4w8m2"
Hipótese de ameaça
       ↓
Técnica
       ↓
Telemetria
       ↓
Lógica
       ↓
Regra
       ↓
Teste
       ↓
Deploy
       ↓
Monitoramento
       ↓
Tuning
```

A detecção passa a ser tratada como um processo de engenharia.

Ela possui:

* objetivo;
* requisito;
* lógica;
* teste;
* documentação;
* métricas;
* manutenção.

---

# Exemplo de hipótese de detecção

Uma hipótese simples:

> Um atacante pode utilizar PowerShell para executar código após comprometer uma estação de trabalho.

A partir disso:

### Técnica

```text id="y7q3m8"
MITRE ATT&CK:
T1059.001 - PowerShell
```

### Telemetria

```text id="z2k6v1"
Process Creation
PowerShell
EDR
Network
```

### Lógica

```text id="q5m9r4"
PowerShell
+
CommandLine suspeita
+
Parent Process incomum
```

### Enriquecimento

```text id="n8c3w7"
Usuário
Host
Hash
IP
Domínio
Threat Intelligence
```

### Resultado

```text id="s4v2k9"
Alerta
      ↓
Triagem
      ↓
Investigação
```

Essa é a essência de transformar uma hipótese de ameaça em uma detecção operacional.

---

# Detection Pipeline

Uma arquitetura conceitual pode ser:

```text id="g1v8x5"
              ┌─────────────┐
              │   Fontes    │
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │    Coleta   │
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │ Normalização│
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │ Correlação  │
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │  Detecção   │
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │    Alerta   │
              └──────┬──────┘
                     ↓
              ┌─────────────┐
              │ Investigação│
              └─────────────┘
```

Cada etapa possui uma função.

Se a coleta for ruim, a detecção será limitada.

Se a normalização for ruim, a correlação pode falhar.

Se a regra for ruim, haverá ruído.

Se o alerta não possuir contexto, a investigação será mais lenta.

---

# Detection Logic

Uma lógica de detecção pode ser simples:

```text id="k2m7s4"
IF
Source IP = known malicious IP

THEN
Alert
```

Mas pode também ser composta:

```text id="v6r1p8"
IF
User login
AND
New device
AND
Unusual location
AND
Privileged account
AND
Access to critical resource

THEN
High Risk Alert
```

Ou baseada em sequência:

```text id="n4x8c2"
Login
  ↓
PowerShell
  ↓
File Creation
  ↓
External Connection
```

Quanto mais evidências relevantes forem combinadas, maior pode ser a confiança da detecção.

Mas complexidade excessiva também pode prejudicar a detecção.

O objetivo é encontrar o equilíbrio entre:

```text id="q9w3m6"
Cobertura
+
Precisão
+
Desempenho
+
Investigabilidade
```

---

# Threat Hunting

Nem toda ameaça gera um alerta.

Por isso, o SOC também precisa procurar comportamentos de maneira proativa.

Isso é **Threat Hunting**.

Um hunting pode começar com uma pergunta:

> "Existem sinais de execução suspeita de PowerShell no ambiente que nossas regras atuais não estão detectando?"

O analista pode pesquisar:

```text id="f7k2m9"
Todos os PowerShells
        ↓
Filtrar horários incomuns
        ↓
Filtrar usuários privilegiados
        ↓
Analisar parent process
        ↓
Analisar command line
        ↓
Pesquisar conexões externas
```

O hunting pode encontrar:

* novas ameaças;
* gaps de detecção;
* falsos negativos;
* comportamentos anômalos;
* indicadores desconhecidos.

---

# Detection Gap

Uma das perguntas mais importantes para um programa de detecção é:

> **"O que nós não conseguimos enxergar?"**

Imagine:

```text id="u5r8k2"
Endpoint
██████████

Identity
████████

Network
██████

Cloud
██
```

Pode existir uma ótima cobertura de endpoint e, ao mesmo tempo, pouca visibilidade em cloud.

Isso cria um gap.

A análise de cobertura deve considerar:

```text id="c7m3v9"
Tecnologias
Técnicas
Fontes de dados
Ativos críticos
Cenários de ataque
```

---

# Purple Team

Uma maneira de validar a capacidade de detecção é combinar ataque e defesa em exercícios controlados.

Esse conceito é frequentemente associado ao **Purple Team**.

O processo pode ser:

```text id="r8k4n1"
Red Team
   ↓
Executa técnica controlada
   ↓
Blue Team
   ↓
Verifica se detectou
   ↓
Analisa telemetria
   ↓
Ajusta regra
```

Exemplo:

```text id="s2v7q5"
Técnica:
PowerShell

Teste:
Execução controlada

Resultado:
Detectado?

SIM → validar qualidade

NÃO → investigar gap
```

Isso permite validar a detecção em condições próximas de um cenário real.

---

# Métricas de Threat Detection

Uma estratégia de detecção precisa ser medida.

Algumas métricas importantes:

### Detection Coverage

Quantos comportamentos relevantes conseguimos detectar?

### False Positive Rate

Quantos alertas são benignos?

### Mean Time to Detect — MTTD

Quanto tempo levamos para identificar uma ameaça?

### Mean Time to Respond — MTTR

Quanto tempo levamos para responder?

### Alert Volume

Quantos alertas são produzidos?

### Detection Latency

Quanto tempo existe entre o comportamento e a geração do alerta?

### Investigation Time

Quanto tempo o analista precisa para entender o alerta?

Essas métricas ajudam a transformar detecção em um processo mensurável.

---

# Exemplo de Detection Use Case

## Credential Access

### Hipótese

Um atacante pode tentar acessar credenciais armazenadas em um endpoint comprometido.

### Técnica

```text id="w3m9x7"
Credential Access
```

### Telemetria

```text id="k8v2q4"
Process Creation
EDR
Authentication Logs
Endpoint Security
```

### Detecção

```text id="p6r1n5"
Processo suspeito
+
acesso a processo sensível
+
usuário não esperado
```

### Contexto

```text id="m4c7s2"
Host
Usuário
Processo pai
Command Line
Hash
Histórico do endpoint
```

### Resultado

```text id="x9q3v6"
Alerta
    ↓
Investigação
    ↓
Verificação de comprometimento
```

---

# Detecção não termina no alerta

Um erro comum é considerar que o trabalho terminou quando o SIEM gerou o alerta.

Na realidade:

```text id="d2k8r5"
Detecção
   ↓
Alerta
   ↓
Triagem
   ↓
Investigação
   ↓
Classificação
   ↓
Resposta
   ↓
Lições aprendidas
   ↓
Melhoria da detecção
```

Se um incidente foi confirmado, as evidências encontradas durante a investigação podem gerar novas detecções.

Por exemplo:

```text id="a7v3m1"
Incidente
   ↓
Novo IOC
   ↓
Novo comportamento
   ↓
Nova regra
   ↓
Melhor cobertura
```

Esse ciclo é uma das formas mais importantes de evolução de um SOC.

---

# Detection-as-Code

Em ambientes mais maduros, regras de detecção podem ser tratadas como código.

Isso significa utilizar práticas como:

```text id="q4n8w2"
Git
Versionamento
Pull Requests
Code Review
Testes
Documentação
Rollback
```

Exemplo:

```text id="h6r2k9"
Nova regra
   ↓
Git
   ↓
Pull Request
   ↓
Review
   ↓
Teste
   ↓
Deploy
```

Isso reduz mudanças não documentadas e permite acompanhar a evolução das detecções.

Também torna possível saber:

* quem alterou a regra;
* quando foi alterada;
* por que foi alterada;
* qual versão estava ativa;
* qual comportamento a mudança deveria corrigir.

---

# Exemplo de ciclo completo

Considere uma campanha de phishing.

```text id="t7m3x8"
Phishing
   ↓
Usuário abre documento
   ↓
Processo suspeito
   ↓
PowerShell
   ↓
Download
   ↓
Persistência
   ↓
C2
```

A estratégia de detecção pode acompanhar cada etapa:

```text id="v2k9q4"
E-mail
 ↓
URL suspeita

Endpoint
 ↓
Processo suspeito

PowerShell
 ↓
Execution

Persistência
 ↓
Scheduled Task

Rede
 ↓
C2

SIEM
 ↓
Correlação
```

Agora o SOC não depende de uma única regra.

Existe uma **cadeia de detecção**.

---

# O princípio da defesa em profundidade

Uma boa estratégia não deve depender de um único controle.

Se uma detecção falhar:

```text id="n8r5m1"
IOC
  X

Behavior
  ↓

Correlation
  ↓

Hunting
```

Outra camada pode identificar o comportamento.

Isso é defesa em profundidade aplicada à detecção.

O objetivo é reduzir a dependência de um único indicador, ferramenta ou regra.

---

# O que torna uma detecção boa?

Uma boa detecção deve ser:

### Relevante

Detecta algo que realmente importa.

### Investigável

Entrega contexto suficiente para iniciar uma análise.

### Resiliente

Não depende exclusivamente de um IOC facilmente alterável.

### Mensurável

Pode ter sua qualidade acompanhada.

### Testável

Pode ser validada em ambiente controlado.

### Mantível

Pode ser atualizada conforme o ambiente muda.

### Documentada

Outro analista consegue entender seu objetivo e funcionamento.

---

# Checklist de Threat Detection

Antes de considerar uma detecção madura:

```text id="p5v8c1"
[ ] Qual ameaça estamos tentando detectar?

[ ] Qual comportamento esperamos encontrar?

[ ] Existe técnica MITRE relacionada?

[ ] Quais dados precisamos?

[ ] A telemetria está disponível?

[ ] A regra foi criada?

[ ] A regra foi testada?

[ ] Existem falsos positivos conhecidos?

[ ] O alerta possui contexto?

[ ] Existe procedimento de investigação?

[ ] O comportamento foi validado?

[ ] Existe métrica de desempenho?

[ ] A detecção possui documentação?

[ ] Existe processo de revisão?
```

---

# Conclusão

Threat Detection é o ponto onde conhecimento sobre ameaças encontra os dados reais de um ambiente.

Não basta saber que determinada técnica existe.

É necessário descobrir:

```text id="j4x7m2"
Como ela aparece?
        ↓
Onde aparece?
        ↓
Quais evidências deixa?
        ↓
Como podemos detectá-la?
        ↓
Como diferenciá-la de atividade legítima?
        ↓
O que fazemos quando encontrá-la?
```

Uma estratégia de detecção madura conecta:

```text id="c9r2w6"
Threat Intelligence
        +
MITRE ATT&CK
        +
Telemetria
        +
Detection Engineering
        +
Threat Hunting
        +
SIEM
        +
Resposta a Incidentes
```

O resultado não é simplesmente uma coleção de regras.

É uma capacidade contínua de **observar, detectar, investigar e aprender**.

O objetivo final de Threat Detection pode ser resumido em uma pergunta:

> **Se um atacante estivesse dentro do nosso ambiente agora, quais sinais deixaria — e nós saberíamos onde procurar?**

É essa pergunta que transforma monitoramento em defesa.
