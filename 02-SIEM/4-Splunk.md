# Splunk

## Visão geral

O **Splunk** é uma plataforma utilizada para **coletar, indexar, pesquisar, analisar e visualizar grandes volumes de dados**.

Em segurança da informação, o Splunk pode ser utilizado como plataforma **SIEM**, permitindo centralizar eventos de diferentes fontes e transformá-los em alertas e informações úteis para o SOC.

A lógica básica é:

```text
Fontes de dados
      ↓
Coleta
      ↓
Indexação
      ↓
Pesquisa
      ↓
Correlação
      ↓
Detecção
      ↓
Alerta
      ↓
Investigação
      ↓
Resposta
```

O grande diferencial do Splunk está na capacidade de pesquisar grandes quantidades de eventos utilizando uma linguagem própria chamada **SPL (Search Processing Language)**.

---

# Onde o Splunk entra em um SOC?

Imagine uma empresa com:

```text
Windows
Linux
Firewall
VPN
EDR
Active Directory
Microsoft 365
Aplicações Web
Cloud
Banco de dados
```

Cada ambiente gera eventos diferentes.

O Splunk centraliza essas informações para que o analista possa pesquisar e correlacionar os dados.

```text
┌──────────────┐
│   Windows    │
└──────┬───────┘
       │
┌──────▼───────┐
│    Linux     │
└──────┬───────┘
       │
┌──────▼───────┐
│   Firewall   │
└──────┬───────┘
       │
┌──────▼───────┐
│     EDR      │
└──────┬───────┘
       │
       ▼
┌────────────────┐
│     Splunk     │
│                │
│ Search / SPL   │
│ Correlation    │
│ Detection      │
└───────┬────────┘
        │
        ▼
   ┌─────────┐
   │   SOC   │
   └─────────┘
```

---

# Splunk como SIEM

O Splunk pode ser utilizado para funções típicas de uma operação SIEM:

```text
Coleta de eventos
Centralização
Indexação
Pesquisa
Correlação
Detecção
Alertas
Dashboards
Investigação
Threat Intelligence
```

Em ambientes corporativos, o **Splunk Enterprise Security (ES)** adiciona funcionalidades específicas para operações de segurança.

Isso permite construir uma camada de monitoramento voltada para:

```text
Detecção
Investigação
Priorização
Threat Intelligence
Risk Analysis
Incident Response
```

---

# Arquitetura básica

Uma arquitetura simplificada pode ser representada assim:

```text
                 FONTES
                    │
        ┌───────────┼───────────┐
        │           │           │
     Windows      Linux      Firewall
        │           │           │
        └───────────┼───────────┘
                    │
                    ▼
             Data Collection
                    │
                    ▼
             Indexers
                    │
                    ▼
                 Search
                    │
                    ▼
             Splunk Platform
                    │
          ┌─────────┴─────────┐
          │                   │
       Dashboards           Alerts
          │                   │
          └─────────┬─────────┘
                    ▼
                   SOC
```

Em ambientes maiores, a arquitetura pode ser distribuída entre diferentes componentes e funções.

---

# Principais componentes

Os componentes podem variar de acordo com a arquitetura utilizada, mas alguns conceitos aparecem com frequência.

## Forwarder

O **Forwarder** é utilizado para coletar e encaminhar dados para o ambiente Splunk.

Exemplo:

```text
Windows Server
      ↓
Universal Forwarder
      ↓
Splunk
```

Isso permite enviar eventos sem necessariamente instalar toda a plataforma Splunk no servidor monitorado.

---

# Universal Forwarder

O **Universal Forwarder** é muito utilizado para coleta de dados.

Ele pode enviar:

```text
Windows Events
Linux Logs
Application Logs
Files
System Logs
```

Fluxo:

```text
Servidor
   ↓
Universal Forwarder
   ↓
Splunk
```

O agente funciona como uma ponte entre a fonte de dados e a plataforma.

---

# Indexer

O **Indexer** é responsável por processar e armazenar os dados para posterior pesquisa.

Uma visão simplificada:

```text
Evento
  ↓
Indexer
  ↓
Indexação
  ↓
Armazenamento
  ↓
Pesquisa
```

A indexação é uma das partes fundamentais para permitir pesquisas rápidas sobre grandes volumes de dados.

---

# Search Head

O **Search Head** é responsável por executar pesquisas e apresentar os resultados.

É onde o analista normalmente trabalha com:

```text
SPL
Dashboards
Reports
Alerts
Investigação
```

Em uma arquitetura distribuída:

```text
Analista
   ↓
Search Head
   ↓
Indexers
   ↓
Eventos
```

---

# Splunk Enterprise Security

O **Splunk Enterprise Security (ES)** é uma solução voltada especificamente para operações de segurança.

Ele adiciona recursos para:

```text
Security Monitoring
Correlation
Risk Analysis
Incident Review
Threat Intelligence
Detection
Investigation
```

O objetivo é facilitar o trabalho do SOC.

---

# Eventos, campos e dados

Um dos conceitos mais importantes no Splunk é entender que os eventos podem possuir campos pesquisáveis.

Imagine:

```text
2026-08-07 20:10:22
user=admin
src_ip=185.XX.XX.XX
action=failed_login
host=server01
```

O Splunk pode interpretar:

```text
user
src_ip
action
host
timestamp
```

Isso permite realizar pesquisas específicas.

---

# O que é SPL?

**SPL (Search Processing Language)** é a linguagem utilizada pelo Splunk para pesquisar e processar dados.

Uma busca simples:

```spl
index=security
```

pode pesquisar eventos dentro de determinado índice.

Podemos adicionar filtros:

```spl
index=security action="failed_login"
```

Ou:

```spl
index=security user="admin"
```

A ideia é começar com um conjunto de eventos e ir refinando a pesquisa.

---

# SPL na investigação

Imagine que o SOC receba:

```text
Possible Brute Force
```

O analista pode começar procurando falhas de autenticação:

```spl
index=security action="failed_login"
```

Depois limitar para um usuário:

```spl
index=security action="failed_login" user="admin"
```

Depois procurar uma origem:

```spl
index=security action="failed_login" src_ip="185.XX.XX.XX"
```

A pesquisa vai ficando cada vez mais específica.

---

# Contagem de eventos

Podemos utilizar comandos de agregação para descobrir a quantidade de eventos.

Exemplo:

```spl
index=security action="failed_login"
| stats count by src_ip
```

Resultado conceitual:

```text
src_ip             count
185.XX.XX.XX       87
190.XX.XX.XX       12
200.XX.XX.XX       4
```

O primeiro IP merece uma investigação mais detalhada.

---

# Identificando possíveis Brute Force

Uma pesquisa pode procurar muitas falhas de autenticação:

```spl
index=security action="failed_login"
| stats count by src_ip, user
| where count > 10
```

A lógica é:

```text
Falhas
   ↓
Agrupar por IP e usuário
   ↓
Contar
   ↓
Aplicar threshold
   ↓
Encontrar comportamento suspeito
```

Isso pode ser transformado posteriormente em uma regra de detecção.

---

# Correlacionando falha e sucesso

Uma investigação mais interessante é procurar:

```text
Múltiplas falhas
+
Login bem-sucedido
```

Conceitualmente:

```text
Failed Login
Failed Login
Failed Login
       ↓
Successful Login
```

Esse padrão pode indicar:

```text
Credential Attack
      ou
Brute Force
```

O analista deve investigar o contexto antes de concluir que houve comprometimento.

---

# Timechart

O Splunk também permite visualizar eventos ao longo do tempo.

Exemplo:

```spl
index=security action="failed_login"
| timechart span=5m count
```

Isso pode mostrar:

```text
20:00 ███
20:05 ███████
20:10 ███████████████
20:15 ██
```

Um pico repentino pode ser um indicador importante.

---

# Dashboards

Dashboards permitem transformar consultas em visualizações.

Um SOC pode acompanhar:

```text
Total de alertas
Alertas críticos
Falhas de login
IPs suspeitos
Endpoints afetados
Malware
Eventos de firewall
Incidentes
```

Exemplo conceitual:

```text
┌──────────────────────────────────────┐
│          SOC DASHBOARD               │
├──────────────────────────────────────┤
│ Critical Alerts        12            │
│ High Alerts            37            │
│ Failed Logins          1,842         │
│ Suspicious IPs         19            │
│ Affected Hosts         7             │
├──────────────────────────────────────┤
│       Alerts by Time                 │
│ ████                                 │
│ ███████                              │
│ █████████████                        │
└──────────────────────────────────────┘
```

O dashboard não substitui a investigação.

Ele serve para facilitar a visualização do estado do ambiente.

---

# Alertas

Uma pesquisa pode ser transformada em um alerta.

Por exemplo:

```text
Mais de 20 falhas de login
em menos de 5 minutos
```

Quando a condição é atingida:

```text
SPL Search
    ↓
Condition
    ↓
Alert
    ↓
SOC
```

O alerta pode possuir:

```text
Nome
Severidade
Descrição
Fonte
Horário
Host
Usuário
IP
```

---

# Correlation Search

No contexto do Splunk Enterprise Security, uma **Correlation Search** é utilizada para identificar comportamentos relevantes de segurança.

Exemplo conceitual:

```text
IF

failed_login > 20

AND

same_source_ip

AND

time_window < 5 minutes

THEN

generate_security_notable
```

A pesquisa deixa de ser apenas uma consulta manual e passa a fazer parte da estratégia de detecção.

---

# Notable Event

No Splunk Enterprise Security, um comportamento detectado pode gerar um **Notable Event**.

Exemplo:

```text
┌──────────────────────────────┐
│       NOTABLE EVENT          │
├──────────────────────────────┤
│ Detection: Brute Force       │
│ Risk: High                   │
│ Source IP: 185.XX.XX.XX      │
│ User: administrator           │
│ Target: DC01                 │
└──────────────────────────────┘
```

Isso fornece ao analista um ponto inicial para investigação.

---

# Risk-Based Alerting

Um conceito importante no Splunk Enterprise Security é trabalhar com **risco**.

Nem todo evento deve receber a mesma importância.

Imagine:

```text
Evento A
Falha de login
Risco: baixo
```

Depois:

```text
Evento B
Execução de PowerShell
Risco: médio
```

Depois:

```text
Evento C
Conexão para IP malicioso
Risco: alto
```

Correlacionados:

```text
Login suspeito
     +
PowerShell
     +
IP malicioso
     ↓
Risco elevado
```

Isso permite priorizar comportamentos que, quando combinados, apresentam maior risco.

---

# Threat Intelligence

O Splunk pode utilizar feeds de Threat Intelligence para enriquecer eventos.

Imagine:

```text
Endpoint
   ↓
Connection to 185.XX.XX.XX
   ↓
Splunk
   ↓
Threat Intelligence
   ↓
Known Malicious IP
```

O evento pode receber contexto adicional:

```text
Reputação
Categoria
Fonte
Primeira observação
Última observação
Confiança
```

Isso ajuda o analista a decidir se o indicador merece investigação imediata.

---

# Exemplo: investigação de IP malicioso

Alerta:

```text
Suspicious Outbound Connection

Source:
192.168.10.50

Destination:
185.XX.XX.XX
```

O analista pode começar:

```spl
index=network dest_ip="185.XX.XX.XX"
```

Depois:

```spl
index=network dest_ip="185.XX.XX.XX"
| stats count by src_ip
```

Agora pode descobrir quais hosts internos fizeram conexão.

Depois:

```spl
index=endpoint dest_ip="185.XX.XX.XX"
| stats count by host, user, process
```

Isso pode revelar:

```text
Host:
PC-FINANCEIRO-07

User:
usuario01

Process:
powershell.exe
```

O alerta agora possui muito mais contexto.

---

# Investigação utilizando SPL

Imagine:

```text
ALERTA

Suspicious PowerShell
Host: PC-07
User: usuario01
```

O analista pode começar:

### 1. Pesquisar o host

```spl
index=endpoint host="PC-07"
```

### 2. Pesquisar o usuário

```spl
index=endpoint user="usuario01"
```

### 3. Pesquisar PowerShell

```spl
index=endpoint process="powershell.exe"
```

### 4. Pesquisar conexões

```spl
index=network src_host="PC-07"
```

### 5. Procurar outros hosts

```spl
index=endpoint process="powershell.exe"
| stats count by host
```

A investigação vai expandindo conforme surgem novos indicadores.

---

# Linha do tempo

Um dos objetivos da investigação é reconstruir a sequência de eventos.

Exemplo:

```text
09:30
E-mail recebido

09:32
Documento aberto

09:32
PowerShell iniciado

09:33
Conexão externa

09:33
Arquivo criado

09:34
Processo executado

09:35
Conexão para IP suspeito
```

No Splunk, o analista pode utilizar consultas e visualizações temporais para reconstruir essa sequência.

---

# Busca por IOC

Um IOC pode ser:

```text
IP
Domínio
Hash
URL
Nome de arquivo
E-mail
```

Imagine que o SOC recebeu:

```text
Hash:
ABC123...
```

O analista pode procurar o indicador em todo o ambiente:

```spl
index=*
hash="ABC123..."
```

Isso permite descobrir:

```text
Quais hosts possuem o arquivo?
Quando apareceu?
Qual usuário estava logado?
Foi executado?
Houve conexão de rede?
```

---

# Pesquisa ampla

Uma vantagem do Splunk é poder pesquisar diferentes fontes.

Por exemplo:

```spl
index=* "185.XX.XX.XX"
```

A intenção é procurar o indicador em diferentes conjuntos de dados.

Isso pode revelar que o mesmo IP apareceu em:

```text
Firewall
VPN
Windows
Linux
EDR
Web Server
```

Essa visão ajuda a descobrir relações que poderiam passar despercebidas quando cada sistema é analisado separadamente.

---

# Data Models

Em ambientes com Splunk Enterprise Security, **Data Models** ajudam a estruturar e padronizar dados de segurança.

Exemplos conceituais:

```text
Authentication
Network Traffic
Endpoint
Web
Change
Malware
```

Isso facilita a construção de pesquisas e detecções sobre diferentes fontes.

---

# CIM — Common Information Model

O **Common Information Model (CIM)** é importante no ecossistema Splunk.

A ideia é padronizar os dados para que diferentes fontes possam ser utilizadas de forma consistente.

Por exemplo, diferentes sistemas podem chamar o IP de origem de:

```text
src
source_ip
client_ip
sourceAddress
```

O CIM ajuda a mapear essas informações para um modelo comum.

Conceitualmente:

```text
Firewall
source_ip
     ↓
     ┐
EDR  │
client_ip
     ↓
     ├──► CIM
     │
VPN  │
src
     ↓
```

Isso facilita a criação de pesquisas e correlações que funcionam sobre diferentes fontes.

---

# Parsing e normalização

Antes de criar uma detecção, é importante garantir que os dados estejam sendo interpretados corretamente.

Imagine um log:

```text
user=admin src=185.XX.XX.XX action=failed
```

O Splunk deve conseguir identificar:

```text
user
src
action
```

Se o campo não estiver corretamente extraído, uma pesquisa como:

```spl
action="failed"
```

pode não retornar os resultados esperados.

Por isso:

> **Uma boa detecção começa com dados confiáveis.**

---

# Sourcetype

O **sourcetype** ajuda o Splunk a identificar o tipo de dado recebido.

Exemplo:

```spl
index=security sourcetype=linux_secure
```

Ou:

```spl
index=windows sourcetype=WinEventLog:Security
```

Isso permite separar e pesquisar dados de acordo com sua origem e estrutura.

---

# Index

Um **index** é utilizado para organizar e armazenar dados.

Exemplos conceituais:

```text
security
windows
network
endpoint
web
firewall
```

Uma pesquisa pode começar por:

```spl
index=security
```

e depois ser refinada.

---

# Boas práticas com SPL

## Comece pequeno

Em vez de começar com uma pesquisa extremamente complexa:

```spl
index=*
```

comece delimitando:

```spl
index=security
```

Depois:

```spl
index=security action="failed_login"
```

Depois:

```spl
index=security action="failed_login"
| stats count by src_ip
```

A investigação cresce conforme a necessidade.

---

# Evite pesquisas desnecessariamente pesadas

Pesquisar todos os índices, todos os eventos e todo o histórico pode consumir muitos recursos.

Prefira:

```text
Índice específico
+
Intervalo de tempo
+
Campos específicos
```

Exemplo:

```spl
index=security action="failed_login" earliest=-24h
```

Isso torna a pesquisa mais objetiva.

---

# Falsos positivos

Uma regra pode identificar:

```text
50 falhas de login
```

Mas isso não significa necessariamente ataque.

Pode ser:

```text
Senha expirada
Script mal configurado
Conta de serviço
Usuário bloqueado
Sistema legado
```

O analista precisa entender o contexto.

---

# Tuning

Assim como em outras plataformas SIEM, o tuning é essencial.

Fluxo:

```text
Regra
  ↓
Alertas
  ↓
Análise
  ↓
Falsos positivos
  ↓
Ajustes
  ↓
Nova análise
```

O objetivo é:

```text
Menos ruído
+
Mais contexto
+
Melhores detecções
```

---

# Splunk e MITRE ATT&CK

Detecções podem ser relacionadas às técnicas do MITRE ATT&CK.

Exemplo:

```text
PowerShell
     ↓
T1059.001
```

Ou:

```text
Brute Force
     ↓
T1110
```

Isso permite construir uma visão de cobertura:

```text
T1059.001  ✓
T1110      ✓
T1078      ✓
T1053      ?
T1003      ?
```

O ponto de interrogação representa uma possível lacuna de detecção que precisa ser investigada.

---

# Splunk e Incident Response

O Splunk pode fornecer informações para diferentes etapas da resposta:

```text
Detecção
   ↓
Triagem
   ↓
Investigação
   ↓
Escopo
   ↓
Contenção
   ↓
Remediação
   ↓
Monitoramento
```

Por exemplo:

```text
Detecção:
IP malicioso

↓

Investigação:
Quais hosts acessaram?

↓

Escopo:
7 endpoints

↓

Resposta:
Isolar endpoints

↓

Monitoramento:
Verificar novas conexões
```

O Splunk fornece dados para apoiar essas decisões.

---

# Exemplo completo de incidente

## Cenário

Um alerta indica:

```text
Possible Brute Force

Source IP:
185.XX.XX.XX

Target:
DC01

User:
administrator
```

### Etapa 1 — Quantidade de tentativas

```spl
index=security
action="failed_login"
src_ip="185.XX.XX.XX"
| stats count
```

Resultado:

```text
87
```

---

### Etapa 2 — Verificar usuários

```spl
index=security
action="failed_login"
src_ip="185.XX.XX.XX"
| stats count by user
```

Resultado:

```text
administrator     62
backup             15
guest              10
```

---

### Etapa 3 — Procurar sucesso

```spl
index=security
src_ip="185.XX.XX.XX"
action="successful_login"
```

Resultado:

```text
administrator
```

Agora o cenário merece prioridade.

---

### Etapa 4 — Pesquisar atividade posterior

```spl
index=security
user="administrator"
src_ip="185.XX.XX.XX"
```

O analista pode procurar:

```text
Alterações de grupo
Criação de usuários
Execução de comandos
Acesso a outros hosts
Alterações de configuração
```

---

### Etapa 5 — Procurar o IP em todo o ambiente

```spl
index=*
"185.XX.XX.XX"
```

Pode aparecer:

```text
Firewall
VPN
Windows
Linux
Web Server
```

Agora é possível entender o alcance da atividade.

---

# O que o analista deve registrar?

Durante uma investigação, é importante documentar:

```text
Data/Hora
Alerta
Host
Usuário
IP
Indicadores
Eventos relacionados
Evidências
Hipótese
Conclusão
Ações realizadas
```

Exemplo:

```text
Alerta:
Possible Brute Force

Origem:
185.XX.XX.XX

Alvo:
DC01

Usuário:
administrator

Tentativas:
87

Login bem-sucedido:
Sim

Atividade posterior:
Alteração de grupo privilegiado

Classificação:
Incidente de segurança

Severidade:
Alta
```

Essa documentação facilita o trabalho de outros analistas e mantém rastreabilidade.

---

# Dashboards úteis para um SOC

Um ambiente Splunk pode possuir dashboards como:

```text
SOC Overview
```

```text
Authentication Monitoring
```

```text
Endpoint Security
```

```text
Network Security
```

```text
Threat Intelligence
```

```text
Incident Monitoring
```

```text
MITRE ATT&CK Coverage
```

O dashboard deve responder perguntas úteis.

Por exemplo:

```text
Quantos alertas críticos existem?

Quais hosts estão mais envolvidos?

Quais IPs aparecem com maior frequência?

Quais usuários estão envolvidos?

Existe algum pico anormal?

Quais técnicas estão sendo detectadas?
```

---

# Erros comuns

## Usar o Splunk apenas como visualizador de logs

Ter um dashboard bonito não significa possuir boa detecção.

O objetivo é:

```text
Dados
 ↓
Análise
 ↓
Detecção
 ↓
Investigação
```

---

## Criar alertas demais

Mais alertas não significam mais segurança.

```text
10.000 alertas
       ↓
Analista sobrecarregado
       ↓
Alert fatigue
```

É melhor possuir menos alertas, mas com maior qualidade.

---

## Não normalizar os dados

Sem campos consistentes, correlações podem falhar.

---

## Não realizar tuning

Regras antigas podem gerar:

```text
Falsos positivos
Ruído
Baixa produtividade
```

---

# Checklist

```text
[ ] Fontes de dados definidas

[ ] Forwarders configurados

[ ] Comunicação validada

[ ] Índices definidos

[ ] Sourcetypes configurados

[ ] Parsing validado

[ ] Campos importantes disponíveis

[ ] Retenção definida

[ ] Pesquisas SPL testadas

[ ] Correlações configuradas

[ ] Alertas revisados

[ ] Falsos positivos avaliados

[ ] Dashboards criados

[ ] Threat Intelligence integrada

[ ] MITRE ATT&CK considerado

[ ] Procedimentos de investigação documentados
```

---

# Comparação rápida

| Recurso             | Função                             |
| ------------------- | ---------------------------------- |
| Forwarder           | Coleta e encaminhamento            |
| Indexer             | Processamento e armazenamento      |
| Search Head         | Pesquisas e análise                |
| SPL                 | Linguagem de pesquisa              |
| Index               | Organização dos dados              |
| Sourcetype          | Identificação do tipo de dado      |
| CIM                 | Padronização dos dados             |
| Dashboard           | Visualização                       |
| Alert               | Detecção automatizada              |
| Correlation Search  | Detecção baseada em correlação     |
| Notable Event       | Evento relevante para investigação |
| Risk-Based Alerting | Priorização baseada em risco       |
| Enterprise Security | Recursos voltados para segurança   |

---

# Splunk x Wazuh

As duas plataformas podem participar de uma operação de segurança, mas possuem características e posicionamentos diferentes.

| Característica          | Wazuh                     | Splunk                       |
| ----------------------- | ------------------------- | ---------------------------- |
| Open source             | Sim                       | Não                          |
| Endpoint monitoring     | Forte                     | Disponível via integrações   |
| FIM                     | Sim                       | Via fontes/integracões       |
| SIEM                    | Sim                       | Sim                          |
| SPL                     | Não                       | Sim                          |
| Dashboards              | Sim                       | Sim                          |
| Threat Intelligence     | Sim                       | Sim                          |
| Enterprise Security     | Não é o foco              | Sim                          |
| Ecossistema corporativo | Forte                     | Muito amplo                  |
| Custo                   | Geralmente mais acessível | Pode ser elevado             |
| Curva de aprendizado    | Moderada                  | Maior em ambientes avançados |

A escolha depende do ambiente, orçamento, requisitos técnicos e maturidade da operação.

---

# Conclusão

O Splunk é muito mais do que uma ferramenta para visualizar logs.

Dentro de um SOC, ele pode funcionar como uma plataforma para:

```text
Coletar
   ↓
Pesquisar
   ↓
Correlacionar
   ↓
Detectar
   ↓
Priorizar
   ↓
Investigar
   ↓
Responder
```

Um dos pontos mais importantes para quem trabalha com Splunk é dominar a lógica de investigação.

Um alerta pode começar assim:

```text
"Falha de login detectada."
```

Mas uma boa investigação pode transformar isso em:

```text
87 tentativas de autenticação
        ↓
mesmo IP externo
        ↓
conta privilegiada
        ↓
login bem-sucedido
        ↓
alteração de privilégio
        ↓
possível comprometimento
```

É nesse momento que o conhecimento de **SPL, correlação, contexto e investigação** se torna mais importante do que simplesmente saber navegar pela interface.

O objetivo de um analista não é apenas encontrar eventos.

É **transformar eventos em evidências e evidências em decisões de segurança**.
