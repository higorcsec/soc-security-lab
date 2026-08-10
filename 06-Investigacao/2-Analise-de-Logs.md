# Análise de Logs

## Introdução

Logs são o registro histórico do que aconteceu em um sistema.

Um login, uma conexão, uma alteração de arquivo, uma consulta ao banco ou uma execução de processo podem deixar rastros.

Para um SOC, esses registros funcionam como evidências.

```text
Evento
   ↓
Log
   ↓
Correlação
   ↓
Contexto
   ↓
Investigação
```

O problema é que um ambiente corporativo pode gerar milhões de eventos diariamente.

Por isso, analisar logs não significa simplesmente procurar por palavras como:

```text
error
failed
denied
attack
malware
```

O objetivo é entender:

> **O que aconteceu, quem realizou a ação, quando aconteceu, de onde veio, qual foi o impacto e o que aconteceu depois.**

---

# O que é um log?

Um log é um registro estruturado ou não estruturado de uma atividade.

Exemplo:

```text
2026-08-09 14:32:18
User: higor
Source IP: 10.10.10.25
Action: LOGIN
Result: SUCCESS
```

Esse evento informa:

```text
Quando?
Quem?
De onde?
O quê?
Resultado?
```

Essas informações formam a base de uma investigação.

---

# Por que logs são importantes para um SOC?

Sem logs:

```text
Ataque
   ↓
Pouca visibilidade
   ↓
Poucas evidências
```

Com logs:

```text
Ataque
   ↓
Eventos
   ↓
Timeline
   ↓
Evidências
   ↓
Investigação
```

Logs podem ajudar a responder:

```text
Quem acessou?
Quando?
De onde?
Qual sistema?
O que foi alterado?
Qual recurso foi acessado?
O que aconteceu depois?
```

---

# Tipos de logs

Um SOC normalmente trabalha com diversas fontes.

```text
Windows
Linux
Firewall
DNS
Proxy
Web Server
Database
VPN
Active Directory
Cloud
EDR
Antivírus
Aplicações
E-mail
```

Cada fonte mostra uma parte diferente do ambiente.

---

# Logs de autenticação

Registram eventos relacionados a acesso.

Exemplo:

```text
LOGIN SUCCESS
LOGIN FAILURE
LOGOUT
PASSWORD CHANGE
MFA
ACCOUNT LOCKOUT
```

Um padrão simples:

```text
14:00
Failed Login

14:01
Failed Login

14:02
Failed Login

14:03
Successful Login
```

Pode indicar:

```text
Brute Force
```

Mas também pode ser:

```text
Usuário errando a senha.
```

O contexto determina a interpretação.

---

# Logs do Windows

Em ambientes Windows, eventos importantes podem estar relacionados a:

```text
Authentication
Process Creation
Account Management
Privilege Use
Network Connections
PowerShell
Services
Scheduled Tasks
```

Exemplo conceitual:

```text
User:
administrator

Event:
Logon Success

Source:
10.10.10.50
```

O analista pode correlacionar esse evento com:

```text
Processos
PowerShell
Rede
Alterações
```

para entender o que aconteceu depois do login.

---

# Logs do Linux

No Linux, informações de autenticação podem aparecer em arquivos e serviços como:

```text
/var/log/auth.log
/var/log/secure
/var/log/syslog
/var/log/messages
```

Exemplo:

```text
Failed password for invalid user admin
```

Pode indicar tentativa de acesso.

Mas uma única linha não confirma ataque.

É necessário procurar:

```text
Quantidade
Origem
Frequência
Usuários
Sucesso posterior
```

---

# Logs de Firewall

Firewalls podem registrar:

```text
Source IP
Destination IP
Source Port
Destination Port
Protocol
Action
Bytes
Timestamp
```

Exemplo:

```text
SRC: 185.x.x.x
DST: 10.10.10.20
PORT: 443
ACTION: ALLOW
```

O evento informa que uma conexão foi permitida.

Não significa que o tráfego era malicioso.

O SOC precisa investigar:

```text
Quem iniciou?
Qual serviço?
Qual frequência?
Qual volume?
O destino era esperado?
```

---

# Logs de DNS

DNS pode ser uma excelente fonte de inteligência.

Exemplo:

```text
Client:
10.10.10.25

Query:
example.com

Response:
93.x.x.x
```

O SOC pode investigar:

```text
Domínio
Reputação
Frequência
Subdomínios
Hosts que consultaram
```

Padrões anormais podem indicar:

```text
Malware
C2
Phishing
DNS Tunneling
```

---

# Logs de Web Server

Servidores web normalmente registram:

```text
IP
Timestamp
HTTP Method
URL
Status Code
User Agent
```

Exemplo:

```text
10.10.10.50
GET
/admin/login
200
```

Outro:

```text
10.10.10.50
GET
../../../../etc/passwd
400
```

O segundo merece investigação porque pode indicar tentativa de exploração.

---

# Logs de banco de dados

Dependendo do banco e da configuração, podem registrar:

```text
Login
Queries
Errors
Permissions
Changes
Connections
```

Exemplo:

```text
User:
app_user

Query:
SELECT * FROM customers
```

O problema é que consultas legítimas e maliciosas podem ser muito parecidas.

Por isso, o contexto é essencial:

```text
Usuário
+
Origem
+
Horário
+
Query
+
Volume
```

---

# Logs de VPN

VPN é especialmente importante para investigação de identidade.

Exemplo:

```text
User:
usuario01

Source:
185.x.x.x

Login:
Success

Time:
02:41
```

O SOC pode correlacionar:

```text
VPN
+
Identity
+
Endpoint
+
Cloud
```

para determinar se o acesso foi legítimo.

---

# Logs de Active Directory

Em ambientes Windows corporativos, Active Directory fornece informações importantes sobre identidade.

Pode ajudar a investigar:

```text
Logon
Logoff
Account Creation
Password Change
Group Membership
Privilege Changes
Account Lockout
```

Exemplo:

```text
User added to:
Domain Admins
```

Esse tipo de alteração pode ser extremamente relevante.

---

# Logs de Cloud

Ambientes cloud geram uma grande quantidade de eventos.

Exemplos:

```text
Login
API Calls
Resource Creation
Permission Changes
Storage Access
Token Usage
Configuration Changes
```

Um evento como:

```text
CreateAccessKey
```

pode ser legítimo.

Mas:

```text
Novo Access Key
+
IP desconhecido
+
usuário comprometido
```

pode indicar persistência.

---

# Estrutura de um log

Um log estruturado normalmente possui campos.

Exemplo:

```text
timestamp
user
source_ip
destination_ip
action
result
process
hostname
```

Isso facilita pesquisas.

Em vez de procurar:

```text
"failed"
```

podemos pesquisar:

```text
result = "failure"
```

---

# Timestamp

O horário é um dos campos mais importantes.

Exemplo:

```text
10:01
Login

10:02
PowerShell

10:04
Download

10:06
Connection
```

Sem timestamp confiável, construir uma timeline fica difícil.

Por isso, sincronização de horário é importante.

---

# NTP

Servidores e dispositivos devem manter horários sincronizados.

Um ambiente com:

```text
Host A:
14:00

Host B:
13:42
```

pode dificultar a investigação.

Uma timeline incorreta pode levar a conclusões erradas.

Por isso:

```text
NTP
+
Timezone consistente
+
Timestamp confiável
```

são importantes para operações de segurança.

---

# Normalização de logs

Cada sistema pode utilizar um formato diferente.

Exemplo:

```text
Windows:
EventID=4624

Linux:
sshd login accepted

Firewall:
ALLOW TCP
```

O SIEM pode normalizar essas informações para algo como:

```text
event_type = authentication
action = login
result = success
```

Isso permite correlacionar diferentes fontes.

---

# Correlação

O verdadeiro valor aparece quando os logs são combinados.

Exemplo:

```text
VPN Login
   ↓
Windows Login
   ↓
PowerShell
   ↓
DNS Query
   ↓
External Connection
```

Individualmente:

```text
Eventos normais
```

Juntos:

```text
Possível cadeia de ataque
```

---

# Timeline de investigação

Uma timeline pode ser:

```text
08:12:10
VPN Login

08:13:02
Windows Authentication

08:15:41
PowerShell Started

08:16:05
DNS Query

08:16:08
External Connection

08:17:21
File Created
```

Agora o analista possui uma narrativa.

Essa narrativa é muito mais útil do que milhares de eventos isolados.

---

# Antes, durante e depois

Uma boa investigação divide os eventos em três momentos.

```text
ANTES
↓
Como o ataque começou?

DURANTE
↓
O que aconteceu?

DEPOIS
↓
Qual foi o impacto?
```

Exemplo:

```text
ANTES:
Phishing

DURANTE:
Login

DEPOIS:
Download
```

Isso ajuda a reconstruir a cadeia de ataque.

---

# Buscar contexto

Ao encontrar um evento:

```text
Failed Login
```

não parar nele.

Pesquisar:

```text
Mesmo IP
Mesmo usuário
Mesmo host
Mesmo período
```

Talvez apareça:

```text
50 Failed Login
+
1 Successful Login
```

Agora a investigação muda.

---

# Pivoting

Pivotar significa utilizar uma informação encontrada para buscar outras evidências.

Exemplo:

```text
Alerta
   ↓
IP: 185.x.x.x
```

Pivot:

```text
185.x.x.x
   ↓
Todos os hosts que falaram com esse IP
```

Depois:

```text
Host
   ↓
Usuários
```

Depois:

```text
Usuário
   ↓
Outros logins
```

Esse processo pode revelar o escopo do incidente.

---

# Exemplo de pivot

```text
IP suspeito
   ↓
3 hosts
   ↓
2 usuários
   ↓
1 servidor
   ↓
PowerShell
   ↓
Download
```

O alerta inicial era:

```text
"Comunicação com IP suspeito."
```

A investigação revelou:

```text
Possível comprometimento de servidor.
```

---

# IOC em logs

Indicadores podem ser encontrados diretamente nos registros.

Exemplos:

```text
IP
Domínio
URL
Hash
E-mail
Nome de arquivo
Processo
User Agent
```

Exemplo:

```text
IOC:
185.x.x.x
```

Pesquisar:

```text
185.x.x.x
```

em:

```text
Firewall
DNS
Proxy
EDR
VPN
Web Server
```

---

# IOA em logs

Indicadores de ataque são comportamentos.

Exemplo:

```text
PowerShell
+
Download
+
Execução
```

ou:

```text
Falhas de login
+
Sucesso
+
Acesso privilegiado
```

IOAs normalmente possuem mais valor quando correlacionados.

---

# Logs e MITRE ATT&CK

Os logs podem fornecer evidências de técnicas utilizadas.

Exemplo:

```text
PowerShell
```

pode estar relacionado a:

```text
T1059.001
PowerShell
```

Outro exemplo:

```text
Uso de credenciais válidas
```

pode estar relacionado a:

```text
T1078
Valid Accounts
```

O objetivo é utilizar MITRE para contextualizar o comportamento.

---

# Análise de frequência

Quantidade e frequência podem revelar padrões.

Exemplo:

```text
1 evento
```

versus:

```text
1000 eventos em 1 minuto
```

Pode indicar:

```text
Scanning
Brute Force
Flood
Automação
```

Mas novamente:

> **Volume não significa automaticamente ataque.**

Processos automatizados também geram grandes volumes.

---

# Análise de baseline

Conhecer o comportamento normal ajuda a identificar desvios.

Exemplo:

```text
Servidor:
100 conexões/dia
```

Agora:

```text
5.000 conexões
```

Isso é uma anomalia.

Mas pode ser:

```text
Deploy
Pico de tráfego
Backup
Mudança de arquitetura
Ataque
```

Baseline gera contexto.

---

# Logs e falsos positivos

Uma detecção pode parecer grave.

Exemplo:

```text
1000 DNS Queries
```

Mas depois:

```text
Processo:
Browser

Domínios:
Google
Microsoft
Cloudflare
```

Provavelmente legítimo.

Agora:

```text
1000 DNS Queries
+
subdomínios aleatórios
+
alta frequência
+
processo desconhecido
```

merece investigação.

---

# Logs e malware

Para investigar malware:

```text
Process Creation
+
File Creation
+
DNS
+
Network Connection
```

Exemplo:

```text
arquivo.exe criado
       ↓
processo executado
       ↓
DNS Query
       ↓
Conexão externa
```

Essa sequência pode revelar comportamento malicioso.

---

# Logs e phishing

Uma investigação pode começar com um e-mail.

Depois:

```text
E-mail
 ↓
URL acessada
 ↓
DNS
 ↓
Web Proxy
 ↓
Download
 ↓
Processo
```

A correlação pode mostrar se o phishing resultou em comprometimento.

---

# Logs e brute force

Exemplo:

```text
21:00
Failed

21:01
Failed

21:01
Failed

21:02
Failed

21:02
Success
```

Depois pesquisar:

```text
IP
Usuário
Host
Sessão
```

Se o mesmo IP tentar várias contas:

```text
usuario01
usuario02
usuario03
usuario04
```

pode indicar:

```text
Password Spraying
```

---

# Logs e Account Compromise

Um caso:

```text
Login
   ↓
Novo IP
   ↓
Novo dispositivo
   ↓
MFA
   ↓
Acesso a arquivos
```

A análise de logs permite reconstruir a atividade.

O objetivo é descobrir:

```text
Quando começou?
Como ocorreu?
O que foi acessado?
O que aconteceu depois?
```

---

# Logs e exfiltração

Exemplo:

```text
File Access
   ↓
Archive Created
   ↓
External Connection
   ↓
Large Upload
```

Esse padrão pode indicar:

```text
Collection
+
Staging
+
Exfiltration
```

A investigação deve procurar:

```text
Usuário
Processo
Destino
Volume
Arquivos
```

---

# Logs e Ransomware

Um cenário:

```text
PowerShell
   ↓
Process Creation
   ↓
File Modification
   ↓
Mass Rename
   ↓
Network Activity
```

Uma alteração massiva de arquivos pode ser um forte indicador.

A investigação deve buscar:

```text
Primeiro host
Primeiro processo
Primeiro usuário
Primeiro horário
```

Isso pode ajudar a identificar o ponto inicial.

---

# Qualidade do log

Não basta possuir logs.

É necessário avaliar:

```text
Estão sendo coletados?
Estão completos?
Possuem timestamps?
Estão sincronizados?
Estão centralizados?
São pesquisáveis?
São protegidos?
```

Um SOC pode possuir:

```text
10 TB de logs
```

e ainda assim ter pouca visibilidade se os registros forem incompletos ou mal estruturados.

---

# Logs que não estão chegando

Um problema importante:

```text
Firewall
   ↓
Logs esperados
   ↓
SIEM
```

Se os logs pararem:

```text
Firewall
   X
SIEM
```

existe uma perda de visibilidade.

Por isso, o SOC também deve monitorar:

```text
Log Source Health
```

---

# Integridade dos logs

Logs são evidências.

Devem possuir mecanismos de proteção contra:

```text
Alteração
Exclusão
Manipulação
```

Um atacante que compromete um servidor pode tentar apagar seus rastros.

Por isso, centralizar logs em um SIEM reduz a dependência do sistema local.

---

# Retenção

A organização precisa definir por quanto tempo os logs serão armazenados.

Exemplo:

```text
30 dias
90 dias
180 dias
1 ano
```

A retenção depende de:

```text
Necessidade operacional
Compliance
Capacidade
Risco
Política interna
```

Um incidente descoberto meses depois pode exigir logs antigos.

---

# SIEM

O SIEM centraliza e correlaciona eventos.

Arquitetura simplificada:

```text
Windows
Linux
Firewall
DNS
EDR
Cloud
   ↓
SIEM
   ↓
Correlação
   ↓
Alertas
   ↓
SOC
```

Exemplos de SIEM:

```text
Wazuh
Splunk
Microsoft Sentinel
IBM QRadar
```

O SIEM não substitui o analista.

Ele organiza e acelera a investigação.

---

# Busca eficiente

Uma busca simples:

```text
failed login
```

pode retornar milhares de resultados.

Melhor:

```text
user = "usuario01"
AND
result = "failure"
AND
timestamp >= "14:00"
```

Depois:

```text
source_ip = "185.x.x.x"
```

Quanto melhor o filtro:

```text
Menos ruído
+
Mais contexto
+
Investigação mais rápida
```

---

# Exemplo de investigação

## Alerta

```text
Suspicious Login
User: usuario01
IP: 185.x.x.x
```

### Passo 1

Pesquisar:

```text
185.x.x.x
```

Resultado:

```text
5 hosts
```

### Passo 2

Pesquisar:

```text
usuario01
```

Resultado:

```text
3 logins
```

### Passo 3

Timeline:

```text
02:10
VPN

02:12
Windows Login

02:15
PowerShell

02:17
DNS Query

02:20
External Connection
```

### Passo 4

Investigar:

```text
PowerShell
```

Encontrado:

```text
Download de arquivo
```

Agora:

```text
Login
↓
Execução
↓
Download
↓
Comunicação
```

O caso deve ser escalado.

---

# Exemplo de investigação de Web Server

Alerta:

```text
Possible Web Attack
```

Logs:

```text
10.0.0.20
GET /login
401

10.0.0.20
GET /admin
403

10.0.0.20
GET /../../etc/passwd
400

10.0.0.20
GET /upload
200
```

A sequência pode indicar:

```text
Reconhecimento
+
Tentativa de exploração
+
Acesso a funcionalidade
```

O analista deve investigar:

```text
Qual aplicação?
Qual endpoint?
Houve sucesso?
Houve upload?
O servidor executou algo?
```

---

# Exemplo de investigação Linux

Logs:

```text
Failed password for invalid user admin
```

repetidamente.

Depois:

```text
Accepted password for user01
```

O analista deve verificar:

```text
IP
Horário
Usuário
Processos
SSH Sessions
Comandos
Arquivos
```

A investigação não termina no:

```text
Accepted password
```

É justamente aí que começa a pergunta:

> **O que essa sessão fez?**

---

# Exemplo de investigação Windows

Uma sequência:

```text
Logon Success
      ↓
New Process
      ↓
PowerShell
      ↓
Network Connection
```

O analista pode pesquisar:

```text
Event ID
User
Host
Process
Parent Process
Command Line
Destination
```

Depois comparar com:

```text
MITRE ATT&CK
```

para identificar possíveis técnicas utilizadas.

---

# Exemplo de investigação DNS

Um endpoint apresenta:

```text
10.10.10.20
```

consultando:

```text
x7d9s.example.com
a82k.example.com
k91z.example.com
```

em alta frequência.

O SOC pode verificar:

```text
Domínio
Subdomínio
Processo
Frequência
Outros hosts
Reputação
```

Possibilidades:

```text
Aplicação legítima
CDN
Telemetry
DNS Tunneling
Malware
```

O padrão precisa ser investigado antes de classificar.

---

# Logs e contexto temporal

Um evento pode parecer normal em um horário.

Exemplo:

```text
Admin Login
09:00
```

Agora:

```text
Admin Login
03:00
```

O risco pode ser diferente.

Mas o horário sozinho não determina malícia.

Sempre correlacionar:

```text
Horário
+
Usuário
+
IP
+
Host
+
Atividade
```

---

# Logs e identidade

A identidade deve estar presente sempre que possível.

Exemplo:

```text
IP → Host → User → Process → Action
```

Essa cadeia ajuda a responder:

```text
Quem fez?
```

Em ambientes modernos, IP sozinho é cada vez menos suficiente.

Um IP pode representar:

```text
NAT
VPN
Proxy
Cloud
Gateway
```

A identidade adiciona contexto.

---

# Logs e ativos

Também é importante saber:

```text
Quem é o host?
```

Exemplo:

```text
WEB-01
```

pode ser:

```text
Servidor web público
```

enquanto:

```text
WS-023
```

pode ser:

```text
Workstation
```

A criticidade do ativo influencia diretamente a prioridade da investigação.

---

# Erros comuns

## Olhar apenas um log

```text
Evento isolado
↓
Conclusão
```

Problema:

```text
Pouco contexto
```

---

## Ignorar o que aconteceu depois

```text
Login
↓
Fim da investigação
```

Problema:

```text
Pode perder comprometimento.
```

---

## Confiar apenas em reputação

```text
IP desconhecido
↓
Malicioso
```

Problema:

```text
Pode ser VPN ou serviço legítimo.
```

---

## Ignorar falsos positivos

```text
Muitos alertas
↓
Tudo é ataque
```

Problema:

```text
Alert Fatigue
```

---

## Não registrar evidências

```text
"Investigado."
```

Problema:

```text
Ninguém sabe o que foi analisado.
```

---

# Boas práticas

```text
Centralizar logs

Sincronizar horários

Normalizar eventos

Monitorar fontes

Definir retenção

Proteger registros

Criar correlações

Criar baselines

Documentar investigações

Evitar regras genéricas

Monitorar qualidade da telemetria
```

---

# Checklist de Análise de Logs

```text
[ ] Identifiquei a fonte do log?

[ ] O timestamp está correto?

[ ] Identifiquei o usuário?

[ ] Identifiquei o host?

[ ] Identifiquei o IP?

[ ] Identifiquei o processo?

[ ] Identifiquei a ação?

[ ] Identifiquei o resultado?

[ ] Analisei eventos anteriores?

[ ] Analisei eventos posteriores?

[ ] Fiz pivot usando IOCs?

[ ] Procurei o mesmo indicador em outros hosts?

[ ] Procurei o mesmo usuário?

[ ] Analisei a frequência?

[ ] Comparei com o baseline?

[ ] Verifiquei contexto legítimo?

[ ] Correlacionei fontes diferentes?

[ ] Determinei o impacto?

[ ] Registrei as evidências?

[ ] Precisa de escalonamento?
```

---

# Fluxo de investigação

```text
              ALERTA
                 │
                 ▼
          Identificar fonte
                 │
                 ▼
          Validar timestamp
                 │
                 ▼
          Identificar usuário
                 │
                 ▼
            Identificar host
                 │
                 ▼
           Analisar evento
                 │
                 ▼
         Criar timeline
                 │
                 ▼
             Pivotar
                 │
                 ▼
          Correlacionar
                 │
                 ▼
          Avaliar impacto
                 │
          ┌──────┴──────┐
          ▼             ▼
       Benigno       Suspeito
          │             │
          ▼             ▼
       Encerrar      Escalonar
```

---

# A diferença entre ler logs e investigar logs

Ler:

```text
Login failed.
```

Investigar:

```text
50 falhas
+
mesmo IP
+
10 usuários
+
1 sucesso
+
novo dispositivo
```

Ler:

```text
PowerShell executed.
```

Investigar:

```text
Word → PowerShell
→ Download
→ Arquivo executado
→ DNS
→ Conexão externa
```

Ler logs é encontrar eventos.

**Investigar logs é conectar eventos.**

---

# Conclusão

Logs são uma das principais fontes de evidência de um SOC.

Mas o valor não está na quantidade de registros armazenados.

Está na capacidade de transformar:

```text
Milhares de eventos
        ↓
Poucos eventos relevantes
        ↓
Timeline
        ↓
Contexto
        ↓
Hipótese
        ↓
Evidência
        ↓
Decisão
```

Um bom analista não olha apenas para o evento que gerou o alerta.

Ele procura o que aconteceu:

```text
Antes
Durante
Depois
```

e utiliza cada nova descoberta para pivotar pela infraestrutura.

No final, a pergunta não é:

> **"O que esse log significa?"**

A pergunta mais importante é:

> **"Que história esses logs estão contando?"**

É essa capacidade de transformar registros aparentemente desconectados em uma narrativa técnica que torna a análise de logs uma das habilidades centrais de um analista de SOC.
