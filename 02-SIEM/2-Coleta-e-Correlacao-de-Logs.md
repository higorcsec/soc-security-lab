# Coleta e Correlação de Logs

## Visão geral

Um SIEM só consegue detectar aquilo que consegue **enxergar**.

Essa visibilidade vem dos logs.

Servidores, firewalls, endpoints, aplicações, bancos de dados, sistemas de autenticação e serviços em nuvem geram registros constantemente. O trabalho do SOC é transformar esses registros em informação útil para identificar comportamentos suspeitos.

O processo pode ser resumido em:

```text
Fonte de log
    ↓
Coleta
    ↓
Transporte
    ↓
Normalização
    ↓
Armazenamento
    ↓
Correlação
    ↓
Detecção
    ↓
Alerta
    ↓
Investigação
```

A diferença entre simplesmente **coletar logs** e possuir uma boa capacidade de detecção está justamente na etapa de correlação.

---

# O que é um log?

Um log é um registro de uma atividade realizada por um sistema.

Exemplo de um servidor Linux:

```text
Aug 07 20:14:22 server01 sshd[2841]: Failed password for invalid user admin from 185.XX.XX.XX
```

Esse registro contém informações como:

```text
Data:        Aug 07
Hora:        20:14:22
Host:        server01
Serviço:     sshd
Evento:      Failed password
Usuário:     admin
Origem:      185.XX.XX.XX
```

Um único log pode parecer pouco relevante.

Porém, quando vários eventos são analisados em conjunto, é possível identificar comportamentos.

---

# Por que os logs são importantes para um SOC?

Sem logs, o analista muitas vezes fica limitado a informações incompletas.

Imagine que um servidor tenha sido comprometido.

Sem registros adequados, algumas perguntas podem ficar sem resposta:

```text
Quando o acesso aconteceu?

Qual usuário foi utilizado?

De qual IP veio a conexão?

Qual processo foi executado?

Qual arquivo foi alterado?

Houve criação de uma nova conta?

O atacante acessou outros servidores?
```

Com logs bem coletados, essas informações podem ser reconstruídas.

Por isso:

> **Logs são uma das principais fontes de evidência durante uma investigação de segurança.**

---

# Fontes de logs

Um ambiente corporativo pode possuir dezenas ou centenas de fontes diferentes.

## Sistemas operacionais

### Windows

Exemplos:

```text
Security Event Log
System Event Log
Application Event Log
PowerShell
Sysmon
Windows Defender
```

Podem registrar:

```text
Login
Logout
Falhas de autenticação
Criação de usuários
Alteração de grupos
Execução de processos
Alteração de políticas
Eventos de segurança
```

### Linux

Exemplos:

```text
/var/log/auth.log
/var/log/syslog
/var/log/secure
journald
auditd
sshd
```

Podem registrar:

```text
SSH
sudo
Autenticação
Execução de comandos
Criação de usuários
Alteração de arquivos
Serviços
```

---

# Firewalls

Firewalls são fontes extremamente importantes para um SIEM.

Um evento pode conter:

```text
Source IP
Destination IP
Source Port
Destination Port
Protocol
Action
Interface
Timestamp
Rule
```

Exemplo:

```text
SRC=185.XX.XX.XX
DST=10.10.10.20
SPORT=51234
DPORT=22
PROTO=TCP
ACTION=DENY
```

Um único bloqueio pode ser normal.

Centenas de tentativas contra diferentes portas podem indicar reconhecimento ou tentativa de exploração.

---

# IDS e IPS

Soluções de IDS/IPS podem gerar eventos relacionados a:

```text
Port Scan
Exploit Attempt
Malware
Command and Control
Network Anomaly
Suspicious Traffic
```

Esses eventos podem ser correlacionados com informações de endpoints.

Exemplo:

```text
IDS detecta exploração
        ↓
SIEM identifica IP de origem
        ↓
Busca conexões do mesmo IP
        ↓
Identifica endpoint afetado
        ↓
Consulta eventos do endpoint
```

Isso aumenta o contexto da investigação.

---

# Endpoints e EDR

Endpoints fornecem informações que normalmente não aparecem em logs de rede.

Por exemplo:

```text
Processo executado
Arquivo criado
Arquivo modificado
Conexão de rede
Usuário
Hash
PowerShell
Script
Persistência
```

Imagine:

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
Download de arquivo
     ↓
Execução
     ↓
Conexão externa
```

Quando esses eventos são enviados para um SIEM, podem ser utilizados para detectar uma possível cadeia de ataque.

---

# Active Directory

Em ambientes Windows corporativos, o Active Directory é uma fonte crítica de logs.

Eventos importantes incluem:

```text
Falha de autenticação
Login bem-sucedido
Criação de usuário
Alteração de senha
Alteração de grupos
Uso de contas privilegiadas
Bloqueio de conta
```

Um exemplo:

```text
20:01 - 12 falhas de login
20:02 - 15 falhas de login
20:03 - login bem-sucedido
20:04 - usuário adicionado a grupo privilegiado
```

A análise isolada de cada evento pode não revelar o ataque.

A sequência completa é muito mais relevante.

---

# Aplicações

Aplicações também podem gerar informações importantes:

```text
Login
Logout
Erro
Acesso administrativo
Alteração de dados
Upload
Download
API
Autenticação
```

Em aplicações web:

```text
GET /login
POST /login
GET /admin
POST /admin
GET /wp-admin
```

Uma grande quantidade de requisições anormais pode indicar:

```text
Reconhecimento
Brute Force
Directory Traversal
SQL Injection
Tentativa de exploração
```

---

# Bancos de dados

Dependendo do ambiente, logs de banco de dados podem registrar:

```text
Login
Queries
Alterações
Criação de usuários
Alteração de permissões
Acesso administrativo
Falhas
```

Um exemplo relevante seria:

```text
Usuário comum
      ↓
Acesso ao banco
      ↓
Consulta administrativa
      ↓
Alteração de privilégio
```

Quando correlacionado com logs do sistema operacional, o cenário pode se tornar ainda mais relevante.

---

# Coleta de logs

Existem diferentes maneiras de coletar logs.

Uma arquitetura simples pode ser:

```text
Servidor
   │
   │ Log
   ▼
Agente
   │
   │
   ▼
SIEM
```

Outra arquitetura pode utilizar um coletor intermediário:

```text
Servidor ─────┐
Firewall ─────┤
Linux ────────┤
Windows ──────┤
EDR ──────────┤
              ▼
        Log Collector
              │
              ▼
             SIEM
```

Essa arquitetura pode facilitar a administração e reduzir a quantidade de conexões diretas com o SIEM.

---

# Agente de coleta

Um agente é um software instalado no sistema que coleta eventos e os encaminha para uma plataforma central.

Exemplo conceitual:

```text
[Windows Server]

Event Logs
    ↓
Agente
    ↓
HTTPS / TCP
    ↓
SIEM
```

O agente pode coletar:

```text
Event Logs
Arquivos de log
Processos
Integridade de arquivos
Informações do sistema
```

Soluções diferentes utilizam agentes diferentes.

---

# Syslog

Em ambientes Linux e de rede, **Syslog** é um dos mecanismos mais conhecidos para envio de eventos.

Fluxo simplificado:

```text
Firewall
    │
    │ Syslog
    ▼
Servidor de Logs
    │
    ▼
SIEM
```

Os eventos podem utilizar diferentes transportes, dependendo da implementação:

```text
UDP
TCP
TLS
```

Quando segurança e confiabilidade são importantes, é necessário avaliar cuidadosamente o método de transporte utilizado.

---

# Formatos de logs

Os logs podem aparecer em diferentes formatos.

## Texto

```text
Failed login from 185.XX.XX.XX
```

## JSON

```json
{
  "timestamp": "2026-08-07T20:14:22Z",
  "user": "admin",
  "source_ip": "185.XX.XX.XX",
  "event": "failed_login"
}
```

## CEF

```text
CEF:0|Vendor|Product|1.0|1001|Failed Login|7|src=185.XX.XX.XX
```

## LEEF

```text
LEEF:2.0|Vendor|Product|Version|EventID
```

A capacidade de interpretar esses formatos é importante para que o SIEM consiga utilizar corretamente os dados.

---

# Normalização

Imagine que três sistemas utilizem nomes diferentes para representar o mesmo conceito:

```text
Windows:
SourceAddress

Firewall:
src

Application:
client_ip
```

O SIEM precisa entender que os três representam, conceitualmente:

```text
Source IP
```

A normalização facilita consultas e correlações.

Um modelo simplificado seria:

```text
Origem:
SourceAddress
src
client_ip

        ↓

Campo normalizado:

source.ip
```

O mesmo pode acontecer com:

```text
destination.ip
user.name
host.name
event.action
event.category
event.timestamp
```

---

# Timestamp e sincronização de horário

Um detalhe frequentemente ignorado em ambientes de logs é o horário.

Imagine:

```text
Servidor A:
20:00:05

Servidor B:
19:56:10

Firewall:
20:03:42
```

Se os relógios estiverem incorretos, reconstruir a sequência de eventos pode se tornar extremamente difícil.

Por isso, ambientes corporativos normalmente utilizam **NTP (Network Time Protocol)** para manter os sistemas sincronizados.

Durante uma investigação, o timestamp é essencial para montar uma linha do tempo.

---

# A importância da linha do tempo

Uma investigação normalmente busca responder:

```text
O que aconteceu primeiro?
O que aconteceu depois?
Qual foi o ponto de entrada?
Qual foi o próximo movimento?
Qual foi o impacto?
```

Exemplo:

```text
19:42:11
Phishing recebido

19:45:32
Usuário abriu documento

19:45:35
WINWORD.EXE executado

19:45:37
PowerShell iniciado

19:45:40
Conexão externa

19:46:02
Arquivo criado

19:47:11
Persistência criada
```

Essa sequência é muito mais útil do que analisar os eventos separadamente.

---

# O que é correlação de logs?

**Correlação de logs é o processo de relacionar diferentes eventos para identificar uma atividade ou padrão relevante.**

A lógica pode envolver:

```text
Tempo
+
Usuário
+
IP
+
Host
+
Processo
+
Destino
+
Tipo de evento
```

Quanto mais contexto disponível, maior a capacidade de identificar comportamentos.

---

# Correlação baseada em tempo

Exemplo:

```text
10:00
10 falhas de login

10:01
15 falhas de login

10:02
Login bem-sucedido
```

Uma regra pode identificar:

```text
>10 failed logins
+
successful login
+
same account
+
same source
+
5 minutes
```

Resultado:

```text
Possible Brute Force
```

---

# Correlação baseada em IP

Imagine:

```text
185.XX.XX.XX
```

Esse IP aparece:

```text
Firewall
↓
VPN
↓
Active Directory
↓
Servidor Linux
↓
Aplicação Web
```

O SIEM pode relacionar esses eventos.

Isso permite responder:

> O mesmo endereço IP apareceu em diferentes partes do ambiente?

---

# Correlação baseada em usuário

Exemplo:

```text
Usuário: joao.silva
```

O SIEM pode pesquisar:

```text
Login
VPN
Active Directory
Endpoint
Aplicação
Cloud
```

Isso permite construir uma visão das atividades realizadas pela identidade.

---

# Correlação baseada em host

Exemplo:

```text
PC-FINANCEIRO-023
```

O SIEM pode relacionar:

```text
Processos
Login
DNS
Firewall
EDR
Arquivos
Conexões
```

Isso permite entender o comportamento do endpoint.

---

# Correlação entre diferentes fontes

É aqui que a correlação começa a gerar muito valor.

Imagine:

```text
[Firewall]

IP externo
   ↓
Tentativa de conexão
```

Depois:

```text
[Web Server]

Mesmo IP
   ↓
Requisições suspeitas
```

Depois:

```text
[Endpoint]

Processo suspeito
   ↓
Conexão para o mesmo IP
```

O SIEM consegue relacionar:

```text
Firewall
   +
Web Server
   +
Endpoint
```

O resultado pode representar uma cadeia de ataque.

---

# Exemplo: Brute Force

Imagine os seguintes eventos:

```text
20:01:10
Failed login - user admin

20:01:14
Failed login - user admin

20:01:19
Failed login - user admin

20:01:25
Failed login - user administrator

20:01:31
Failed login - user administrator

20:01:40
Successful login - administrator
```

O SIEM pode identificar:

```text
Múltiplas falhas
       ↓
Mesma origem
       ↓
Mesmo período
       ↓
Login bem-sucedido
       ↓
ALERTA
```

---

# Exemplo: possível comprometimento

Agora um cenário mais completo:

```text
Evento 1
Usuário recebe e-mail suspeito
```

```text
Evento 2
Documento do Office é aberto
```

```text
Evento 3
WINWORD.EXE inicia PowerShell
```

```text
Evento 4
PowerShell acessa endereço externo
```

```text
Evento 5
Arquivo executável é criado
```

```text
Evento 6
Endpoint realiza conexão para IP malicioso
```

Separadamente:

```text
E-mail
Office
PowerShell
Network
File
Endpoint
```

Correlacionados:

```text
Possível cadeia de comprometimento
```

---

# Regras de correlação

Uma regra pode ser representada de maneira simplificada:

```text
IF

event.action = "failed_login"

AND

count > 10

AND

same source.ip

AND

time < 5 minutes

THEN

generate_alert("Possible Brute Force")
```

Outra:

```text
IF

parent_process = "WINWORD.EXE"

AND

child_process = "powershell.exe"

AND

network_connection = true

THEN

generate_alert("Suspicious Office Activity")
```

Essas regras variam conforme a plataforma SIEM.

---

# Threshold

Um dos mecanismos mais simples de correlação é o **threshold**.

Exemplo:

```text
Mais de 20 falhas de login
em menos de 5 minutos
```

Pode ser representado:

```text
20 failed logins
        │
        ▼
    5 minutos
        │
        ▼
      ALERTA
```

Thresholds são úteis, mas podem gerar ruído se forem configurados sem considerar o comportamento normal do ambiente.

---

# Baseline

Para detectar anomalias, o SOC precisa entender o que é normal.

Exemplo:

```text
Normal:
50 logins por minuto
```

De repente:

```text
1.500 logins por minuto
```

Isso pode indicar:

```text
Ataque
Automação
Erro de aplicação
Configuração incorreta
```

O SIEM pode identificar desvios desse comportamento.

Esse conceito é conhecido como **baseline**.

---

# Correlação não significa necessariamente ataque

Um ponto importante:

> **Correlação gera contexto; ela não garante que o evento seja malicioso.**

Exemplo:

```text
100 falhas de login
```

Pode ser:

```text
Ataque de força bruta
```

Mas também pode ser:

```text
Sistema mal configurado
Senha de serviço expirada
Script com credencial antiga
Usuário com problema de autenticação
```

Por isso, o alerta ainda precisa passar pela análise do SOC.

---

# Redução de ruído

Uma boa correlação ajuda a reduzir o volume de alertas.

Sem correlação:

```text
Evento 1 → alerta
Evento 2 → alerta
Evento 3 → alerta
Evento 4 → alerta
Evento 5 → alerta
```

Com correlação:

```text
Evento 1
Evento 2
Evento 3
Evento 4
Evento 5
     ↓
Correlação
     ↓
1 incidente potencial
```

Isso evita que o analista precise investigar dezenas de alertas relacionados como se fossem problemas diferentes.

---

# Enriquecimento de eventos

Um evento pode ser enriquecido com informações adicionais.

Por exemplo:

```text
IP: 185.XX.XX.XX
```

O SIEM pode adicionar:

```text
Geolocalização
ASN
Reputação
Threat Intelligence
Histórico
Quantidade de ocorrências
```

Resultado:

```text
IP:
185.XX.XX.XX

Reputação:
Malicious

Occurrences:
37

Threat Intelligence:
Known Scanner
```

Esse contexto ajuda o analista a tomar decisões mais rápidas.

---

# Correlação e MITRE ATT&CK

As correlações podem ser relacionadas às técnicas do MITRE ATT&CK.

Exemplo:

```text
PowerShell
     ↓
T1059.001
     ↓
Command and Scripting Interpreter:
PowerShell
```

Outro exemplo:

```text
Múltiplas autenticações
     ↓
Brute Force
     ↓
T1110
```

Isso permite que o SOC acompanhe não apenas os alertas, mas também as técnicas que estão sendo observadas no ambiente.

---

# Coleta de logs e cobertura de detecção

Ter muitos logs não significa necessariamente ter boa segurança.

Imagine:

```text
Windows
████████████████████ 100%

Firewall
████████████████████ 100%

Endpoints
██████████░░░░░░░░░░ 50%

Cloud
██░░░░░░░░░░░░░░░░░░ 10%
```

O ambiente pode possuir milhões de eventos, mas ainda ter pontos cegos.

Uma boa estratégia de monitoramento precisa perguntar:

```text
Quais ativos estão gerando logs?

Quais eventos estão sendo coletados?

Quais eventos estão sendo descartados?

Quais fontes são críticas?

Quais técnicas de ataque conseguimos detectar?
```

---

# Logs que merecem prioridade

Nem todos os logs possuem o mesmo valor para segurança.

Em muitos ambientes, é interessante priorizar:

```text
Autenticação
Privilégios
Active Directory
Firewall
VPN
EDR
DNS
Servidores críticos
Aplicações expostas
Cloud
Bancos de dados
```

A prioridade deve considerar:

```text
Risco
+
Importância do ativo
+
Valor da informação
+
Exposição
+
Capacidade de detecção
```

---

# Retenção de logs

Logs precisam ser armazenados por um período adequado.

A retenção depende de:

```text
Política da empresa
Compliance
Legislação
Capacidade de armazenamento
Risco
Necessidade de investigação
```

Um exemplo:

```text
Hot Data
↓
Logs recentes
↓
Consulta rápida

Cold Data
↓
Logs antigos
↓
Consulta menos frequente
```

O objetivo é equilibrar:

```text
Custo
+
Performance
+
Necessidade investigativa
+
Requisitos de retenção
```

---

# Integridade dos logs

Logs podem ser importantes como evidência.

Por isso, é necessário considerar:

```text
Quem pode alterar?
Quem pode excluir?
Onde estão armazenados?
Existe controle de acesso?
Existe proteção contra alteração?
Existe backup?
```

Em ambientes mais maduros, mecanismos de integridade e controle de acesso ajudam a garantir que os registros não sejam facilmente manipulados.

---

# O que acontece quando uma fonte para de enviar logs?

Esse é um cenário importante.

Imagine:

```text
Firewall
     ↓
SIEM
```

O fluxo funcionava normalmente.

De repente:

```text
Firewall
     X
SIEM
```

Se o SOC só procurar ataques, pode não perceber que perdeu visibilidade.

Por isso, ambientes maduros também monitoram:

```text
Health Checks
Volume de logs
Último evento recebido
Status do agente
Conectividade
Falhas de parsing
```

Um alerta pode ser:

```text
CRITICAL

No logs received from:
FW-01

Last event:
18 minutes ago
```

Isso é importante porque:

> **Um sistema de segurança que deixou de gerar dados também é um evento de segurança operacional.**

---

# Pipeline de logs

Uma arquitetura mais completa pode ser visualizada assim:

```text
┌───────────────┐
│     Fonte     │
│ Windows/Linux │
│ Firewall/EDR  │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    Coleta     │
│ Agent/Syslog  │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   Transporte  │
│ TCP/TLS/etc.  │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Normalização  │
│   Parsing     │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│  Armazenamento│
└───────┬───────┘
        │
        ▼
┌───────────────┐
│  Correlação   │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   Detecção    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    ALERTA     │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Analista SOC  │
└───────────────┘
```

---

# Exemplo prático de investigação

Considere o seguinte alerta:

```text
ALERT: Possible Brute Force

Source IP:
185.XX.XX.XX

Target:
DC01

User:
administrator

Failed Attempts:
42

Successful Login:
Yes
```

O analista pode pesquisar no SIEM:

```text
1. Todos os eventos do IP
```

```text
2. Todos os eventos da conta
```

```text
3. Todos os eventos do DC01
```

```text
4. Eventos próximos ao login bem-sucedido
```

```text
5. Alterações de privilégio
```

```text
6. Novos processos
```

```text
7. Conexões externas
```

```text
8. Outros hosts acessados
```

A investigação começa com um alerta, mas pode terminar revelando uma cadeia muito maior.

---

# Boas práticas na coleta

## Coletar o que realmente importa

Mais dados não significam necessariamente mais segurança.

Priorize fontes que contribuam para:

```text
Detecção
Investigação
Resposta
Auditoria
Compliance
```

---

## Sincronizar horários

Utilize NTP e mantenha os sistemas sincronizados.

Isso facilita:

```text
Timeline
Correlação
Investigação
Forense
```

---

## Monitorar a saúde da coleta

Não basta configurar o agente.

É necessário monitorar:

```text
Está conectado?
Está enviando?
Está gerando erros?
Os logs estão sendo processados?
Existe atraso?
```

---

## Proteger os logs

Controle:

```text
Acesso
Permissões
Retenção
Integridade
Backup
```

---

## Revisar regras

As regras devem evoluir conforme o ambiente.

```text
Criar
  ↓
Testar
  ↓
Monitorar
  ↓
Ajustar
  ↓
Reduzir ruído
  ↓
Melhorar detecção
```

---

# Erros comuns

### Coletar tudo sem estratégia

Resultado:

```text
Muito dado
+
Alto custo
+
Muito ruído
```

---

### Criar regras sem entender o ambiente

Resultado:

```text
Falsos positivos
+
Alertas demais
+
Fadiga do analista
```

---

### Não monitorar fontes de log

Resultado:

```text
Fonte para de enviar
      ↓
SOC não percebe
      ↓
Ponto cego
```

---

### Não sincronizar horários

Resultado:

```text
Timeline incorreta
      ↓
Correlação comprometida
      ↓
Investigação mais difícil
```

---

### Confiar apenas em um indicador

Exemplo:

```text
IP suspeito
```

Um IP sozinho pode não ser suficiente.

É melhor buscar:

```text
IP
+
Usuário
+
Host
+
Processo
+
Tempo
+
Destino
```

Quanto maior o contexto, melhor a investigação.

---

# Checklist de coleta e correlação

Antes de considerar uma fonte integrada ao SIEM:

```text
[ ] A fonte gera logs de segurança?

[ ] Os logs estão chegando ao SIEM?

[ ] O horário está sincronizado?

[ ] Os campos estão sendo interpretados corretamente?

[ ] O endereço IP de origem está disponível?

[ ] O usuário está disponível?

[ ] O hostname está disponível?

[ ] Os eventos possuem timestamp?

[ ] Existe retenção adequada?

[ ] Existe monitoramento da saúde da fonte?

[ ] Existem regras de detecção?

[ ] As regras foram testadas?

[ ] Os falsos positivos foram avaliados?

[ ] Existe mapeamento para técnicas relevantes?
```

---

# Resumo

| Conceito       | Função                                  |
| -------------- | --------------------------------------- |
| Log            | Registro de uma atividade               |
| Coleta         | Captura dos eventos                     |
| Agente         | Software responsável pela coleta/envio  |
| Syslog         | Mecanismo comum de envio de logs        |
| Parsing        | Interpretação dos eventos               |
| Normalização   | Padronização dos campos                 |
| Retenção       | Período de armazenamento                |
| Correlação     | Relação entre diferentes eventos        |
| Threshold      | Limite usado para gerar uma detecção    |
| Baseline       | Comportamento considerado normal        |
| Enriquecimento | Adição de contexto aos eventos          |
| Detecção       | Identificação de comportamento suspeito |
| Alerta         | Resultado de uma condição de detecção   |
| Tuning         | Ajuste das regras                       |
| MITRE ATT&CK   | Mapeamento de técnicas adversárias      |

---

# Conclusão

Coletar logs é apenas o primeiro passo.

O verdadeiro valor aparece quando esses dados conseguem responder perguntas durante uma investigação.

```text
O que aconteceu?
       ↓
Quando?
       ↓
Onde?
       ↓
Com qual usuário?
       ↓
A partir de qual origem?
       ↓
Qual processo foi executado?
       ↓
Qual foi o próximo evento?
       ↓
Existe relação com outros sistemas?
       ↓
É uma atividade legítima ou maliciosa?
```

Um SOC eficiente não precisa apenas de **mais logs**.

Precisa de **logs confiáveis, bem estruturados, sincronizados, protegidos e realmente úteis para detecção**.

A correlação transforma eventos isolados em contexto.

E contexto é o que permite ao analista sair de:

```text
"Existe um alerta."
```

para:

```text
"Entendo o que aconteceu,
consigo reconstruir a sequência
e tenho evidências suficientes
para decidir o que fazer."
```

Esse é o objetivo de uma boa arquitetura de coleta e correlação de logs.
