# Microsoft Sentinel

## Visão geral

O **Microsoft Sentinel** é uma plataforma **SIEM e SOAR** baseada em nuvem, desenvolvida pela Microsoft.

Ele permite centralizar dados de segurança, detectar ameaças, investigar incidentes e automatizar respostas.

Dentro de um SOC, o Sentinel pode receber informações de:

```text
Microsoft 365
Azure
Entra ID
Defender
Windows
Linux
Firewalls
EDRs
Aplicações
Cloud
Serviços externos
```

O fluxo básico pode ser representado assim:

```text
Fontes de dados
      ↓
Data Connectors
      ↓
Log Analytics Workspace
      ↓
KQL
      ↓
Analytics Rules
      ↓
Incidents
      ↓
Investigation
      ↓
Automation
      ↓
Response
```

O diferencial do Sentinel está principalmente na integração com o ecossistema Microsoft e na capacidade de utilizar **KQL (Kusto Query Language)** para pesquisa e análise.

---

# Onde o Microsoft Sentinel entra em um SOC?

Imagine uma organização utilizando:

```text
Microsoft 365
Entra ID
Windows
Defender
Azure
Exchange Online
Teams
SharePoint
```

Cada serviço gera informações diferentes.

O Sentinel centraliza esses dados para permitir uma visão de segurança mais ampla.

```text
              Microsoft 365
                   │
                   ▼
                Entra ID
                   │
                   ▼
               Defender
                   │
                   ▼
               Windows
                   │
                   ▼
              Azure / Cloud
                   │
                   ▼
        ┌─────────────────────┐
        │ Microsoft Sentinel  │
        ├─────────────────────┤
        │ KQL                 │
        │ Analytics           │
        │ Incidents           │
        │ Automation          │
        └──────────┬──────────┘
                   │
                   ▼
                  SOC
```

---

# SIEM e SOAR

O Sentinel combina funções de **SIEM** e recursos de **SOAR**.

## SIEM

A parte SIEM envolve:

```text
Coleta
Centralização
Correlação
Detecção
Alertas
Investigação
```

## SOAR

A parte de automação permite:

```text
Orquestração
Playbooks
Ações automáticas
Integração com serviços
Resposta a incidentes
```

De forma simplificada:

```text
SIEM
 ↓
Detecta

SOAR
 ↓
Responde
```

---

# Microsoft Sentinel e Azure

O Sentinel é um serviço baseado no ecossistema Azure.

Uma arquitetura simplificada:

```text
Azure
 │
 ├── Microsoft Sentinel
 │
 └── Log Analytics Workspace
          │
          ├── Security Logs
          ├── Identity Logs
          ├── Application Logs
          └── Cloud Logs
```

O **Log Analytics Workspace** possui um papel importante no armazenamento e consulta dos dados.

---

# Log Analytics Workspace

O Workspace funciona como uma área central para armazenar e consultar logs.

Exemplos de dados:

```text
Sign-in Logs
Audit Logs
Windows Events
Firewall Events
Defender Alerts
Application Logs
Cloud Activity
```

O analista utiliza KQL para consultar essas informações.

---

# Data Connectors

Os **Data Connectors** são utilizados para conectar fontes de dados ao Sentinel.

Exemplos:

```text
Microsoft Entra ID
Microsoft Defender
Microsoft 365
Azure Activity
Windows
Linux
Firewall
Syslog
Threat Intelligence
```

Fluxo:

```text
Fonte
 ↓
Connector
 ↓
Workspace
 ↓
Sentinel
```

---

# Microsoft Entra ID

O **Microsoft Entra ID** é uma das fontes mais importantes para um ambiente Microsoft.

Ele fornece informações relacionadas a identidade e autenticação.

Exemplos:

```text
Login
Logout
Falha de autenticação
MFA
Alteração de senha
Alteração de grupo
Criação de usuário
Privilégios
Aplicações
```

Isso permite detectar comportamentos suspeitos relacionados a identidade.

---

# Exemplo de investigação de login

Imagine:

```text
Usuário:
usuario@empresa.com

Login:
Sucesso

Local:
Brasil

Poucos minutos depois:

Login:
Sucesso

Local:
Europa
```

Esse comportamento pode indicar:

```text
Impossible Travel
```

Mas o analista não deve assumir imediatamente que ocorreu comprometimento.

Pode existir:

```text
VPN
Proxy
Rede corporativa
Cloud
Viagem legítima
Erro de geolocalização
```

A investigação precisa considerar contexto.

---

# KQL

O **Kusto Query Language (KQL)** é uma linguagem utilizada para consultar e analisar dados no ecossistema Microsoft.

Uma consulta simples:

```kql
SigninLogs
```

Pode ser refinada:

```kql
SigninLogs
| where ResultType != 0
```

Isso permite procurar eventos de autenticação que não foram bem-sucedidos.

---

# Filtrando por usuário

Podemos procurar eventos de determinado usuário:

```kql
SigninLogs
| where UserPrincipalName == "usuario@empresa.com"
```

Ou por período:

```kql
SigninLogs
| where TimeGenerated > ago(24h)
```

Combinando:

```kql
SigninLogs
| where TimeGenerated > ago(24h)
| where UserPrincipalName == "usuario@empresa.com"
```

---

# Contando eventos

Podemos utilizar `summarize`:

```kql
SigninLogs
| summarize count() by UserPrincipalName
```

Isso permite descobrir quais usuários possuem maior quantidade de eventos.

Podemos também agrupar por IP:

```kql
SigninLogs
| summarize count() by IPAddress
```

Resultado conceitual:

```text
IP               Eventos
185.XX.XX.XX     892
190.XX.XX.XX     120
200.XX.XX.XX     32
```

Um volume elevado pode merecer investigação.

---

# Falhas de autenticação

Uma pesquisa simples:

```kql
SigninLogs
| where ResultType != 0
| summarize FailedAttempts=count() by IPAddress
| order by FailedAttempts desc
```

A lógica:

```text
Falhas
  ↓
Agrupar por IP
  ↓
Contar
  ↓
Ordenar
  ↓
Investigar maiores volumes
```

---

# Brute Force

Imagine:

```text
IP:
185.XX.XX.XX

Falhas:
500

Usuários:
37
```

Isso pode indicar tentativa de ataque.

Uma pesquisa pode ser:

```kql
SigninLogs
| where ResultType != 0
| summarize
    FailedAttempts=count(),
    Users=dcount(UserPrincipalName)
    by IPAddress
| where FailedAttempts > 100
```

Agora o SOC possui um indicador mais estruturado.

---

# Password Spray

Um cenário interessante é o **Password Spray**.

Em vez de atacar uma única conta várias vezes:

```text
User A → senha
User B → senha
User C → senha
User D → senha
```

O atacante pode tentar a mesma senha contra diversos usuários.

O Sentinel pode ajudar a identificar esse comportamento.

Exemplo conceitual:

```kql
SigninLogs
| where ResultType != 0
| summarize
    Users=dcount(UserPrincipalName),
    Attempts=count()
    by IPAddress
| where Users > 20
```

A ideia é identificar:

```text
Um IP
   ↓
Muitos usuários
   ↓
Muitas falhas
   ↓
Possível Password Spray
```

---

# Analytics Rules

As **Analytics Rules** transformam consultas e condições em mecanismos de detecção.

Exemplo:

```text
KQL Query
    ↓
Analytics Rule
    ↓
Condition
    ↓
Alert
    ↓
Incident
```

Uma regra pode procurar:

```text
Muitas falhas de login
Login suspeito
Execução de processo
Comunicação com IP malicioso
Alteração de privilégios
Criação de usuário
```

---

# Scheduled Analytics Rule

Uma regra pode executar uma consulta periodicamente.

Exemplo:

```text
A cada 5 minutos
      ↓
Executa KQL
      ↓
Analisa últimos eventos
      ↓
Encontrou condição?
      ↓
Sim
      ↓
Cria alerta
```

Isso permite transformar uma pesquisa manual em uma detecção contínua.

---

# Incidents

No Sentinel, alertas relacionados podem ser agrupados em **Incidents**.

Isso é importante porque um incidente representa um contexto maior.

Exemplo:

```text
Alert 1
Failed Login

Alert 2
Successful Login

Alert 3
Suspicious PowerShell

Alert 4
Malicious IP

        ↓

     INCIDENT
```

Em vez de analisar cada alerta isoladamente, o analista investiga a história completa.

---

# Alert x Incident

Uma distinção importante:

```text
Alert
 ↓
Um evento ou detecção relevante
```

Enquanto:

```text
Incident
 ↓
Agrupamento de evidências relacionadas
```

Exemplo:

```text
Alert:
Suspicious Login

Alert:
MFA Disabled

Alert:
New Admin Role

        ↓

Incident:
Possible Account Compromise
```

---

# Entity

O Sentinel utiliza entidades para representar objetos envolvidos nos eventos.

Exemplos:

```text
User
IP
Host
Account
File
URL
Application
```

Um incidente pode mostrar:

```text
User:
usuario01

Host:
PC-FINANCEIRO-07

IP:
185.XX.XX.XX

Application:
Microsoft 365
```

Isso ajuda o analista a conectar os eventos.

---

# Investigation Graph

A investigação pode utilizar relações entre entidades.

Exemplo:

```text
          User
           │
           ▼
       Workstation
           │
           ▼
      PowerShell
           │
           ▼
       Malicious IP
           │
           ▼
      Suspicious File
```

Essa visão ajuda a entender a cadeia de eventos.

---

# Exemplo de investigação

Imagine que o SOC receba:

```text
Incident:
Possible Account Compromise

User:
usuario@empresa.com
```

O analista pode começar verificando:

```text
1. Logins recentes
2. IPs utilizados
3. Localização
4. MFA
5. Dispositivos
6. Alterações de senha
7. Alterações de privilégios
8. Aplicações acessadas
9. Atividades posteriores
```

---

# Investigação utilizando KQL

### 1. Histórico de login

```kql
SigninLogs
| where UserPrincipalName == "usuario@empresa.com"
| project TimeGenerated, IPAddress, Location, ResultType, AppDisplayName
| order by TimeGenerated desc
```

---

### 2. IPs utilizados

```kql
SigninLogs
| where UserPrincipalName == "usuario@empresa.com"
| summarize count() by IPAddress
| order by count_ desc
```

---

### 3. Falhas de login

```kql
SigninLogs
| where UserPrincipalName == "usuario@empresa.com"
| where ResultType != 0
| project TimeGenerated, IPAddress, ResultDescription
| order by TimeGenerated desc
```

---

### 4. Atividades administrativas

Dependendo das fontes conectadas:

```kql
AuditLogs
| where InitiatedBy.user.userPrincipalName == "usuario@empresa.com"
```

O objetivo é descobrir se a conta realizou alterações administrativas suspeitas.

---

# Microsoft Defender + Sentinel

Uma das maiores vantagens do Sentinel em ambientes Microsoft é a integração com o ecossistema Defender.

Por exemplo:

```text
Microsoft Defender
        ↓
Endpoint Detection
        ↓
Sentinel
        ↓
Correlation
        ↓
Incident
```

Isso permite combinar:

```text
Endpoint
+
Identity
+
Email
+
Cloud
+
Network
```

em uma mesma investigação.

---

# Microsoft Defender for Endpoint

O Defender for Endpoint pode fornecer informações relacionadas aos endpoints.

Exemplos:

```text
Processos
Arquivos
Alertas
Conexões
Dispositivos
Usuários
Malware
Comportamentos suspeitos
```

O Sentinel pode utilizar esses dados para ampliar o contexto de um incidente.

---

# Exemplo de ataque

Imagine:

```text
E-mail malicioso
      ↓
Usuário abre arquivo
      ↓
PowerShell executado
      ↓
Endpoint gera alerta
      ↓
Conexão externa
      ↓
IP suspeito
      ↓
Sentinel correlaciona
      ↓
Incident
```

Agora o SOC consegue investigar a cadeia completa.

---

# Microsoft 365

O Sentinel também pode receber dados relacionados ao Microsoft 365.

Dependendo das integrações utilizadas:

```text
Exchange
SharePoint
OneDrive
Teams
Entra ID
Microsoft Defender
```

Isso é especialmente útil para investigação de comprometimento de contas.

---

# Exemplo: conta comprometida

Imagine:

```text
08:00
Login normal

08:10
MFA alterado

08:15
Novo login de IP desconhecido

08:17
Acesso ao SharePoint

08:20
Download de grande volume de arquivos

08:25
Regra de detecção acionada
```

Isoladamente, alguns eventos podem parecer normais.

Quando correlacionados:

```text
Alteração de MFA
+
Login suspeito
+
Acesso a arquivos
+
Volume anormal
```

o risco aumenta.

---

# Threat Intelligence

O Sentinel pode utilizar indicadores de Threat Intelligence.

Exemplos:

```text
IP
Domínio
URL
Hash
```

O fluxo:

```text
Evento
  ↓
IOC
  ↓
Threat Intelligence
  ↓
Match
  ↓
Alert
```

Por exemplo:

```text
Endpoint
   ↓
Conecta em domínio
   ↓
Domínio consta em Threat Intelligence
   ↓
Detecção
```

---

# Automation Rules

As **Automation Rules** ajudam a controlar o que acontece depois que um incidente é criado.

Exemplo:

```text
Incident
   ↓
Automation Rule
   ↓
Classificação
   ↓
Atribuição
   ↓
Ação
```

Podemos utilizar automação para:

```text
Atribuir incidente
Alterar status
Adicionar tags
Executar ações
Acionar Playbooks
```

---

# Playbooks

Os **Playbooks** permitem automatizar respostas.

Eles são construídos utilizando o ecossistema de automação da Microsoft, como o **Azure Logic Apps**.

Exemplo:

```text
Malicious IP
      ↓
Incident
      ↓
Playbook
      ↓
Consultar Threat Intelligence
      ↓
Enviar notificação
      ↓
Bloquear indicador
```

Outro exemplo:

```text
Conta comprometida
      ↓
Playbook
      ↓
Desabilitar conta
      ↓
Revogar sessões
      ↓
Notificar SOC
```

A ação exata depende das integrações e permissões disponíveis.

---

# Automação exige cuidado

Automatizar uma resposta não significa que ela sempre deve ser executada.

Imagine:

```text
Detecção incorreta
      ↓
Playbook
      ↓
Conta legítima bloqueada
      ↓
Usuário sem acesso
```

Por isso, antes de automatizar ações críticas, é importante avaliar:

```text
Precisão da detecção
Impacto
Permissões
Possibilidade de rollback
Escopo
Criticidade
```

Uma boa automação deve reduzir o trabalho do SOC sem criar novos problemas.

---

# KQL para investigação

O KQL é uma das habilidades mais importantes para quem trabalha com Sentinel.

Alguns operadores fundamentais:

```text
where
project
summarize
count
distinct
sort
extend
join
union
```

Exemplo:

```kql
SigninLogs
| where ResultType != 0
| summarize count() by IPAddress
| sort by count_ desc
```

A lógica pode ser entendida como:

```text
Selecionar
   ↓
Filtrar
   ↓
Agrupar
   ↓
Contar
   ↓
Ordenar
```

---

# `where`

Utilizado para filtrar eventos.

```kql
SigninLogs
| where ResultType != 0
```

Significa:

```text
Retorne apenas eventos
que atendam à condição.
```

---

# `project`

Utilizado para selecionar os campos que interessam.

```kql
SigninLogs
| project TimeGenerated, UserPrincipalName, IPAddress
```

Isso torna o resultado mais limpo.

---

# `summarize`

Utilizado para agregação.

```kql
SigninLogs
| summarize count() by IPAddress
```

Muito útil para encontrar:

```text
Top IPs
Top usuários
Quantidade de eventos
Frequência
```

---

# `extend`

Pode ser utilizado para criar novos campos calculados.

Exemplo conceitual:

```kql
SigninLogs
| extend LoginType = iff(ResultType == 0, "Success", "Failure")
```

Agora o evento pode possuir:

```text
LoginType
```

com:

```text
Success
Failure
```

---

# `join`

O `join` permite combinar dados de diferentes tabelas.

Por exemplo:

```text
SigninLogs
      +
AuditLogs
      ↓
Investigação
```

Isso pode ser útil quando uma investigação precisa conectar autenticação com atividade administrativa.

---

# `union`

Pode ser utilizado quando precisamos combinar resultados de diferentes tabelas.

Conceitualmente:

```kql
union TableA, TableB
```

Isso permite pesquisar fontes diferentes em uma única consulta.

---

# Detecção baseada em comportamento

O objetivo de um SOC moderno não é procurar apenas assinaturas conhecidas.

Também é importante detectar comportamento.

Exemplo:

```text
Usuário normalmente:
Brasil
09:00–18:00
Notebook corporativo
```

De repente:

```text
Login
Outro país
03:00
Novo dispositivo
MFA alterado
```

O comportamento merece investigação.

---

# MITRE ATT&CK

As detecções do Sentinel podem ser relacionadas ao **MITRE ATT&CK**.

Exemplo:

```text
PowerShell
      ↓
T1059.001
```

```text
Brute Force
      ↓
T1110
```

```text
Valid Accounts
      ↓
T1078
```

Isso permite acompanhar a cobertura de técnicas.

Exemplo:

```text
T1059.001  ✓
T1110      ✓
T1078      ✓
T1003      ?
T1053      ?
```

As lacunas ajudam a identificar oportunidades para novas detecções.

---

# Incidente completo

Considere o seguinte cenário:

```text
Usuário:
admin@empresa.com
```

### Evento 1

```text
Falhas de autenticação
```

### Evento 2

```text
Login bem-sucedido
```

### Evento 3

```text
Novo dispositivo
```

### Evento 4

```text
MFA alterado
```

### Evento 5

```text
Criação de usuário privilegiado
```

O Sentinel pode correlacionar esses eventos.

Resultado:

```text
             ┌──────────────┐
             │ Failed Login │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │    Login     │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ New Device   │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ MFA Changed  │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ Privilege    │
             │ Escalation   │
             └──────┬───────┘
                    ↓
               INCIDENT
```

Agora o analista possui uma narrativa para investigar.

---

# Hunting

Além dos alertas automáticos, o SOC pode realizar **Threat Hunting**.

Nesse caso, o analista procura comportamentos suspeitos mesmo sem existir um incidente.

Exemplo:

```text
Hipótese:

"Um atacante pode estar utilizando
PowerShell para executar comandos
em endpoints."
```

O analista pesquisa:

```kql
DeviceProcessEvents
| where FileName =~ "powershell.exe"
| summarize count() by DeviceName
| order by count_ desc
```

A ideia é testar uma hipótese.

```text
Hipótese
   ↓
KQL
   ↓
Dados
   ↓
Evidências
   ↓
Conclusão
```

---

# Hunting baseado em IOC

Se um IOC for descoberto:

```text
IP:
185.XX.XX.XX
```

o analista pode procurar sua presença no ambiente.

```kql
DeviceNetworkEvents
| where RemoteIP == "185.XX.XX.XX"
```

Pode então investigar:

```text
Qual máquina?
Qual processo?
Qual usuário?
Quando ocorreu?
Quantas vezes?
```

---

# Hunting baseado em comportamento

Outra abordagem é procurar comportamentos anormais.

Exemplo:

```text
PowerShell
+
Download externo
+
Execução de arquivo
```

Ou:

```text
Novo usuário
+
Privilégio administrativo
+
Login externo
```

O objetivo é identificar cadeias de comportamento.

---

# Falsos positivos

Uma detecção pode estar correta tecnicamente e ainda assim não representar um incidente.

Exemplo:

```text
Múltiplos logins
```

Pode ser:

```text
Usuário legítimo
VPN
Aplicação
Script
Conta de serviço
```

O analista precisa investigar contexto.

---

# Tuning das detecções

Uma regra inicial pode gerar:

```text
1000 alertas
```

Depois da análise:

```text
800 falsos positivos
150 eventos esperados
50 relevantes
```

O SOC pode ajustar:

```text
Threshold
Exceções
Entidades confiáveis
Horários
IPs
Contas de serviço
```

Resultado:

```text
1000 alertas
      ↓
200 alertas
      ↓
Maior qualidade
```

O objetivo não é simplesmente diminuir alertas.

É diminuir **ruído sem perder cobertura**.

---

# Custos e retenção

Como o Sentinel trabalha com dados armazenados e processados na nuvem, a estratégia de ingestão e retenção precisa ser planejada.

Antes de conectar tudo, é importante perguntar:

```text
Quais logs são realmente necessários?

Qual volume será ingerido?

Qual período precisa ser mantido?

Quais dados são críticos?

Quais fontes possuem maior valor para segurança?
```

Uma arquitetura eficiente não significa coletar absolutamente tudo.

Significa coletar aquilo que fornece **visibilidade suficiente para detectar e investigar ameaças**.

---

# Boas práticas

## 1. Comece pelas fontes críticas

Priorize:

```text
Entra ID
Defender
Windows
Microsoft 365
Azure
Firewalls
Endpoints críticos
```

---

## 2. Padronize os dados

Verifique:

```text
Campos
Timestamp
Usuário
IP
Host
Event ID
Source
```

Dados inconsistentes prejudicam a detecção.

---

## 3. Crie detecções baseadas em risco

Nem todo evento precisa virar incidente.

Priorize:

```text
Impacto
Probabilidade
Contexto
Criticidade do ativo
Privilégio
```

---

## 4. Documente as regras

Cada detecção importante deve possuir:

```text
Nome
Objetivo
Fonte
KQL
Severidade
MITRE ATT&CK
Falsos positivos
Procedimento de investigação
Resposta esperada
```

---

## 5. Automatize somente o que for confiável

Comece com ações de baixo risco:

```text
Notificação
Tag
Enriquecimento
Consulta de Threat Intelligence
Atribuição
```

Depois evolua para:

```text
Bloqueio
Isolamento
Desabilitação
Revogação de sessão
```

quando houver confiança suficiente na detecção.

---

# Checklist

```text
[ ] Sentinel habilitado

[ ] Log Analytics Workspace configurado

[ ] Data Connectors definidos

[ ] Entra ID integrado

[ ] Microsoft Defender integrado

[ ] Microsoft 365 integrado

[ ] Logs de endpoints disponíveis

[ ] KQL testado

[ ] Analytics Rules configuradas

[ ] Incidents sendo gerados

[ ] Entidades corretamente identificadas

[ ] Threat Intelligence integrada

[ ] MITRE ATT&CK considerado

[ ] Playbooks avaliados

[ ] Automation Rules configuradas

[ ] Falsos positivos analisados

[ ] Detecções ajustadas

[ ] Retenção definida

[ ] Custos de ingestão monitorados

[ ] Procedimentos de investigação documentados
```

---

# Microsoft Sentinel x Wazuh x Splunk

| Característica      | Wazuh                  | Splunk                | Microsoft Sentinel |
| ------------------- | ---------------------- | --------------------- | ------------------ |
| SIEM                | Sim                    | Sim                   | Sim                |
| SOAR                | Limitado/integrações   | Sim, via ecossistema  | Sim                |
| Cloud               | Sim                    | Sim                   | Muito forte        |
| Endpoint            | Forte                  | Via integrações       | Forte com Defender |
| KQL                 | Não                    | Não                   | Sim                |
| SPL                 | Não                    | Sim                   | Não                |
| Open source         | Sim                    | Não                   | Não                |
| Microsoft 365       | Integração             | Integração            | Nativo/ecossistema |
| Entra ID            | Integração             | Integração            | Forte              |
| Defender            | Integração             | Integração            | Forte              |
| Threat Intelligence | Sim                    | Sim                   | Sim                |
| MITRE ATT&CK        | Sim                    | Sim                   | Sim                |
| Automação           | Sim                    | Sim                   | Forte              |
| Modelo de consumo   | Infraestrutura própria | Licenciamento/consumo | Cloud/consumo      |

A escolha da plataforma depende do ambiente.

Um SOC predominantemente Microsoft pode encontrar grande vantagem no Sentinel devido à integração com:

```text
Entra ID
Microsoft Defender
Microsoft 365
Azure
Windows
```

Já ambientes heterogêneos podem exigir uma análise mais ampla de integrações, custos e arquitetura.

---

# Wazuh, Splunk e Sentinel no aprendizado de SOC

Conhecer mais de uma plataforma é interessante porque os conceitos de SOC se repetem.

Por exemplo:

```text
             WAZUH
               │
          Rules / Alerts
               │
               ▼
            ANALISTA
               ▲
               │
       SPLUNK / SPL
               │
          Searches
               │
               ▼
            ANALISTA
               ▲
               │
       SENTINEL / KQL
               │
          Queries
```

As ferramentas mudam.

A lógica de investigação permanece:

```text
Evento
  ↓
Contexto
  ↓
Correlação
  ↓
Detecção
  ↓
Investigação
  ↓
Decisão
  ↓
Resposta
```

---

# O que um analista precisa saber?

Para trabalhar com Sentinel, não basta saber navegar pelo portal.

É importante desenvolver conhecimentos em:

```text
Microsoft Entra ID
Windows
Microsoft 365
Networking
KQL
SIEM
MITRE ATT&CK
Threat Intelligence
Incident Response
Threat Hunting
```

E principalmente:

```text
Capacidade de investigação.
```

Uma boa consulta KQL não é útil apenas porque funciona.

Ela precisa responder uma pergunta.

---

# Perguntas que uma investigação deve responder

Ao receber um incidente, pense:

```text
O que aconteceu?

Quando aconteceu?

Qual usuário está envolvido?

Qual máquina está envolvida?

Qual IP está envolvido?

O comportamento é esperado?

Existe evidência de comprometimento?

O incidente afetou outros ativos?

Qual técnica pode estar sendo utilizada?

Qual é o impacto?

O que deve ser feito agora?
```

Essa mentalidade é mais importante do que decorar dezenas de consultas.

---

# Conclusão

O Microsoft Sentinel permite construir uma operação de segurança integrada ao ecossistema Microsoft, centralizando dados de identidade, endpoints, aplicações, cloud e outros componentes.

Seu fluxo pode ser resumido em:

```text
Coletar
   ↓
Normalizar
   ↓
Pesquisar
   ↓
Correlacionar
   ↓
Detectar
   ↓
Criar Incident
   ↓
Investigar
   ↓
Automatizar
   ↓
Responder
```

O **KQL** se torna uma das principais ferramentas do analista para transformar grandes volumes de eventos em informações úteis.

Um alerta pode começar simplesmente como:

```text
"Falha de autenticação."
```

Mas uma investigação bem executada pode revelar:

```text
Múltiplas falhas
       ↓
IP desconhecido
       ↓
Login bem-sucedido
       ↓
Novo dispositivo
       ↓
MFA alterado
       ↓
Privilégio elevado
       ↓
Possível comprometimento de conta
```

Esse é o verdadeiro objetivo de uma plataforma SIEM.

Não apenas **mostrar logs**, mas permitir que o analista transforme dados dispersos em uma **história de segurança que possa ser investigada, validada e respondida**.
