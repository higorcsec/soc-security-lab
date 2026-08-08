# Windows Event Logs

## Visão geral

Os **Windows Event Logs** são registros gerados pelo sistema operacional e pelos serviços executados no Windows.

Para um SOC, eles são uma das principais fontes de evidências.

Através dos eventos, é possível identificar:

```text
Logins
Falhas de autenticação
Criação de usuários
Alteração de privilégios
Execução de processos
Alterações no sistema
Uso de PowerShell
Acesso a arquivos
Atividade de serviços
Eventos de segurança
```

Um simples evento pode parecer irrelevante isoladamente.

Porém, quando combinado com outros eventos, pode revelar uma tentativa de ataque.

```text
Login suspeito
      ↓
PowerShell
      ↓
Criação de usuário
      ↓
Elevação de privilégio
      ↓
Conexão externa
      ↓
Possível comprometimento
```

Por isso, logs são uma das principais fontes utilizadas durante uma investigação de segurança.

---

# Onde os logs do Windows ficam?

O Windows Event Log utiliza arquivos com extensão:

```text
.evtx
```

Os principais registros ficam normalmente em:

```text
C:\Windows\System32\winevt\Logs\
```

Exemplos:

```text
Security.evtx
System.evtx
Application.evtx
```

Também existem logs específicos para diferentes componentes e serviços.

---

# Principais categorias

Os três logs tradicionais mais conhecidos são:

```text
Application
Security
System
```

Além deles, versões atuais do Windows possuem diversos canais adicionais.

---

# Application

Registra eventos relacionados às aplicações.

Exemplos:

```text
Erros
Falhas
Avisos
Inicialização
Problemas de aplicações
```

Pode ser útil para investigar:

```text
Application crash
Serviço com problema
Software desconhecido
Comportamento inesperado
```

---

# System

Registra eventos relacionados ao próprio sistema operacional e seus componentes.

Exemplos:

```text
Drivers
Serviços
Boot
Hardware
Kernel
Falhas do sistema
```

Durante uma investigação, pode ajudar a descobrir:

```text
Quando um serviço iniciou?
Quando parou?
O sistema reiniciou?
Houve algum erro de driver?
```

---

# Security

Para um SOC, o log **Security** é especialmente importante.

Ele registra eventos relacionados à segurança e autenticação.

Exemplos:

```text
Login
Falha de login
Criação de usuário
Alteração de grupos
Uso de privilégios
Acesso a objetos
```

É uma das principais fontes para investigar atividades suspeitas em endpoints Windows.

---

# Event ID

Cada evento pode possuir um **Event ID**.

O Event ID ajuda o analista a identificar o tipo de atividade registrada.

Alguns eventos importantes:

| Event ID | Descrição                                      |
| -------- | ---------------------------------------------- |
| 4624     | Logon bem-sucedido                             |
| 4625     | Falha de logon                                 |
| 4634     | Logoff                                         |
| 4647     | Logoff iniciado pelo usuário                   |
| 4672     | Privilégios especiais atribuídos               |
| 4688     | Novo processo criado                           |
| 4720     | Usuário criado                                 |
| 4728     | Usuário adicionado a grupo global de segurança |
| 4732     | Usuário adicionado a grupo local de segurança  |
| 4740     | Conta bloqueada                                |
| 4768     | Solicitação de Ticket Kerberos                 |
| 4769     | Solicitação de Service Ticket Kerberos         |
| 4771     | Falha de pré-autenticação Kerberos             |
| 4776     | Tentativa de validação de credenciais          |

> Os eventos disponíveis e seus detalhes dependem da versão do Windows e das políticas de auditoria configuradas.

---

# Event ID 4624 — Logon bem-sucedido

O evento **4624** indica que uma sessão de logon foi criada com sucesso.

Exemplo conceitual:

```text
Event ID: 4624

Account:
administrator

Source IP:
192.168.10.50

Logon Type:
10
```

O evento sozinho não significa que algo malicioso aconteceu.

O analista precisa analisar:

```text
Quem?
Quando?
De onde?
Como?
Em qual máquina?
```

---

# Logon Type

O campo **Logon Type** é extremamente útil.

Alguns tipos comuns:

| Tipo | Significado       |
| ---- | ----------------- |
| 2    | Interactive       |
| 3    | Network           |
| 4    | Batch             |
| 5    | Service           |
| 7    | Unlock            |
| 8    | NetworkCleartext  |
| 9    | NewCredentials    |
| 10   | RemoteInteractive |
| 11   | CachedInteractive |

Um exemplo importante:

```text
Logon Type 10
```

normalmente está associado a uma sessão de **Remote Desktop (RDP)**.

Isso pode ser relevante em investigações envolvendo acesso remoto.

---

# Event ID 4625 — Falha de logon

O evento **4625** indica uma tentativa de logon que falhou.

Exemplo:

```text
4625
User: administrator
Source IP: 185.XX.XX.XX
```

Uma única falha pode ser normal.

Mas:

```text
4625
4625
4625
4625
4625
4625
4625
...
```

em poucos minutos pode indicar:

```text
Brute Force
Password Spray
Credencial incorreta
Script mal configurado
Conta de serviço
```

O contexto é o que determina a relevância.

---

# Detectando possível Brute Force

Imagine:

```text
185.XX.XX.XX
       ↓
200 falhas
       ↓
administrator
```

O comportamento merece investigação.

No próprio Windows, podemos procurar eventos 4625.

Em um SIEM, a lógica pode ser:

```text
4625
   ↓
Agrupar por IP
   ↓
Contar tentativas
   ↓
Aplicar threshold
   ↓
Gerar alerta
```

Exemplo conceitual:

```text
Mais de 30 falhas
em 5 minutos
```

---

# Event ID 4688 — Criação de processo

O evento **4688** registra a criação de um novo processo, quando a auditoria correspondente está habilitada.

Exemplo:

```text
Event ID: 4688

Parent:
cmd.exe

New Process:
powershell.exe

User:
administrator
```

Esse tipo de evento pode ser muito importante em investigação.

Por exemplo:

```text
winword.exe
     ↓
powershell.exe
     ↓
download
     ↓
execução
```

Esse encadeamento pode indicar comportamento suspeito.

---

# Process Creation

Para investigar processos, procure:

```text
Process Name
Parent Process
Command Line
User
Timestamp
Host
```

Exemplo:

```text
Parent:
winword.exe

Process:
powershell.exe

User:
usuario01
```

O processo não deve ser analisado sozinho.

O relacionamento entre processos é muitas vezes mais importante.

---

# PowerShell

PowerShell é uma ferramenta legítima de administração do Windows.

Por isso:

> PowerShell não é sinônimo de malware.

Porém, atacantes frequentemente utilizam PowerShell durante ataques.

Exemplo:

```text
powershell.exe
      ↓
Download
      ↓
Execução
      ↓
Persistência
```

Por isso, eventos relacionados a PowerShell são importantes em um SOC.

---

# PowerShell Logging

Para aumentar a visibilidade, o ambiente pode habilitar mecanismos adicionais de auditoria do PowerShell.

Entre eles:

```text
Script Block Logging
Module Logging
Transcription
```

Esses mecanismos podem fornecer mais contexto do que apenas observar o processo `powershell.exe`.

---

# Event ID 4720 — Criação de usuário

O evento **4720** indica criação de uma conta de usuário.

Exemplo:

```text
New User Created

Username:
backup-admin

Created By:
administrator
```

A pergunta do analista deve ser:

```text
Essa conta deveria existir?
Quem criou?
Quando?
Por quê?
Possui privilégios?
Foi criada durante um incidente?
```

Uma conta administrativa criada sem justificativa merece investigação.

---

# Event ID 4728 e 4732

Esses eventos podem indicar adição de usuários a grupos de segurança.

Isso é importante porque um atacante pode tentar aumentar seus privilégios.

Exemplo:

```text
User
 ↓
Added to Administrators
 ↓
Privilege escalation
```

A investigação deve verificar:

```text
Quem adicionou?
Qual usuário foi adicionado?
Qual grupo?
Quando?
Qual máquina?
```

---

# Event ID 4672

O **4672** indica que privilégios especiais foram atribuídos a uma nova sessão de logon.

Pode aparecer em atividades legítimas de administradores.

Mas também pode ser importante quando combinado com:

```text
4624
+
4672
+
4688
```

Exemplo:

```text
Login administrativo
       ↓
Privilégios especiais
       ↓
PowerShell
       ↓
Comando suspeito
```

Nesse cenário, a correlação é muito mais importante do que um evento isolado.

---

# Event ID 4740 — Conta bloqueada

O evento **4740** indica bloqueio de uma conta.

Pode ser causado por:

```text
Senha incorreta
Credencial antiga
Serviço utilizando senha antiga
Script
Password Spray
Brute Force
```

Uma sequência de bloqueios de várias contas em pouco tempo pode ser um indicador de ataque.

---

# Kerberos

Em ambientes Active Directory, eventos relacionados ao Kerberos são muito importantes.

Alguns eventos:

```text
4768
4769
4771
```

Eles podem ajudar na investigação de:

```text
Autenticação
Tickets
Falhas de autenticação
Atividades suspeitas envolvendo contas
```

O analista deve compreender o funcionamento básico de:

```text
Active Directory
Kerberos
Domain Controller
Authentication
Tickets
```

para interpretar esses eventos corretamente.

---

# Windows Event Forwarding

Em ambientes com vários computadores, não é eficiente analisar os logs manualmente em cada máquina.

O **Windows Event Forwarding (WEF)** permite encaminhar eventos para um servidor coletor.

Fluxo:

```text
Windows PC
     │
     ▼
Windows Event Forwarding
     │
     ▼
Collector
     │
     ▼
SIEM
     │
     ▼
SOC
```

Isso melhora a centralização dos eventos.

---

# Windows Logs + SIEM

Na prática, o SOC normalmente não depende apenas do Event Viewer.

Os eventos podem ser enviados para:

```text
Wazuh
Splunk
Microsoft Sentinel
QRadar
Elastic
```

Exemplo:

```text
Windows
   ↓
Security Events
   ↓
Agent / Collector
   ↓
SIEM
   ↓
Correlation
   ↓
Alert
```

---

# Exemplo com Wazuh

Um endpoint Windows pode enviar eventos para o Wazuh.

```text
Windows
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Rules
   ↓
Alert
   ↓
SOC
```

Um evento 4625 pode ser utilizado em uma regra para detectar múltiplas falhas de autenticação.

---

# Exemplo com Splunk

No Splunk, os eventos podem ser coletados e pesquisados utilizando SPL.

Exemplo conceitual:

```spl
index=windows EventCode=4625
```

Para contar por IP:

```spl
index=windows EventCode=4625
| stats count by src_ip
```

Isso permite identificar origens com grande quantidade de falhas.

---

# Exemplo com Microsoft Sentinel

No Sentinel, os eventos podem ser consultados usando KQL.

Por exemplo, dependendo da tabela utilizada:

```kql
SecurityEvent
| where EventID == 4625
| summarize count() by IpAddress
```

A estrutura exata dos campos depende da fonte e da configuração de coleta.

---

# Exemplo de investigação

Imagine que o SOC recebeu:

```text
ALERTA

Multiple Failed Logons

Host:
DC01

User:
administrator
```

O primeiro passo não deve ser simplesmente bloquear o IP.

Primeiro:

```text
1. Identificar a origem
2. Quantificar as tentativas
3. Verificar se houve sucesso
4. Identificar o tipo de logon
5. Verificar atividades posteriores
```

---

## Etapa 1 — Falhas

```text
4625
4625
4625
4625
4625
...
```

Resultado:

```text
87 tentativas
```

---

## Etapa 2 — Login bem-sucedido

Procurar:

```text
4624
```

Resultado:

```text
Login bem-sucedido após várias falhas.
```

Agora o incidente ganha prioridade.

---

## Etapa 3 — Verificar privilégios

Procurar:

```text
4672
```

Se aparecer:

```text
4624
+
4672
```

o analista deve verificar se a sessão possui privilégios administrativos.

---

## Etapa 4 — Processos

Procurar:

```text
4688
```

Exemplo:

```text
powershell.exe
cmd.exe
net.exe
whoami.exe
```

Agora existe mais contexto.

---

## Etapa 5 — Alterações

Pesquisar eventos relacionados a:

```text
Criação de usuários
Alteração de grupos
Serviços
Persistência
Políticas
```

---

# Linha do tempo

A investigação pode ser organizada assim:

```text
02:10
4625 - Falha

02:11
4625 - Falha

02:12
4625 - Falha

02:13
4624 - Login bem-sucedido

02:13
4672 - Privilégios especiais

02:14
4688 - PowerShell

02:15
4720 - Novo usuário

02:16
4728 - Usuário adicionado a grupo
```

Agora existe uma sequência clara.

```text
Tentativa de acesso
        ↓
Acesso obtido
        ↓
Privilégio
        ↓
Execução
        ↓
Persistência / Escalação
```

Isso pode representar um incidente grave.

---

# Windows Event Logs e MITRE ATT&CK

Os eventos do Windows podem ajudar a identificar técnicas do MITRE ATT&CK.

Exemplos:

```text
PowerShell
    ↓
T1059.001
```

```text
Valid Accounts
    ↓
T1078
```

```text
Account Manipulation
    ↓
T1098
```

```text
Brute Force
    ↓
T1110
```

A associação depende do contexto observado.

---

# Logs que merecem atenção

Em um ambiente Windows, algumas categorias são especialmente importantes:

```text
Authentication
Process Creation
PowerShell
Account Management
Group Management
Privilege Use
Scheduled Tasks
Services
Remote Desktop
Kerberos
Windows Defender
Firewall
```

O objetivo não é simplesmente coletar tudo.

É garantir visibilidade sobre as atividades que podem indicar comprometimento.

---

# Event Viewer

O **Event Viewer** pode ser utilizado para análise local.

Atalho:

```text
eventvwr.msc
```

Estrutura comum:

```text
Event Viewer
└── Windows Logs
    ├── Application
    ├── Security
    ├── Setup
    └── System
```

Para investigação:

```text
Windows Logs
   ↓
Security
   ↓
Filter Current Log
   ↓
Event ID
   ↓
Investigação
```

---

# Filtrando eventos

Por exemplo, para procurar:

```text
4625
```

o analista pode utilizar o filtro do Event Viewer.

Isso permite reduzir milhares de eventos para aqueles relacionados à investigação.

---

# XML do evento

O Event Viewer também apresenta os detalhes em XML.

Isso é importante porque o resumo visual nem sempre mostra todas as informações necessárias.

Um evento pode conter:

```text
Provider
Event ID
Version
Level
Task
TimeCreated
Computer
User
EventData
```

O campo `EventData` pode conter informações importantes para a investigação.

---

# Campos importantes

Ao analisar um evento, procure:

```text
TimeCreated
Computer
Account Name
Account Domain
Source Network Address
Logon Type
Process Name
Parent Process
Command Line
Privileges
```

Nem todos estarão presentes em todos os eventos.

---

# Logs não significam incidente

Um dos erros mais comuns de quem está começando em SOC é interpretar qualquer evento como ataque.

Exemplo:

```text
4625
```

não significa automaticamente:

```text
Brute Force
```

Da mesma forma:

```text
powershell.exe
```

não significa:

```text
Malware
```

O analista precisa buscar:

```text
Contexto
Frequência
Origem
Usuário
Horário
Processo pai
Comando
Relacionamento com outros eventos
```

---

# Correlação

A verdadeira força dos logs aparece quando eventos diferentes são relacionados.

Exemplo:

```text
4625
Failed Login
       +
4624
Successful Login
       +
4672
Privileged Logon
       +
4688
PowerShell
       +
4720
New Account
```

Cada evento isolado pode possuir explicações legítimas.

Todos juntos podem contar uma história completamente diferente.

---

# Boas práticas de coleta

## Sincronização de horário

Todos os sistemas devem possuir horário consistente.

Normalmente isso é feito através de:

```text
NTP
```

Sem sincronização:

```text
Servidor A: 10:00
Servidor B: 10:04
Servidor C: 09:58
```

uma investigação pode ficar muito mais difícil.

---

## Retenção

Defina quanto tempo os logs precisam ser mantidos.

A retenção depende de:

```text
Compliance
Risco
Capacidade
Custo
Necessidade de investigação
```

---

## Integridade

Logs podem conter evidências importantes.

Por isso, é necessário considerar:

```text
Controle de acesso
Permissões
Retenção
Centralização
Monitoramento
Proteção contra alteração
```

Um atacante que consegue apagar ou modificar seus próprios rastros pode dificultar uma investigação.

---

# Checklist

```text
[ ] Windows Event Logs habilitados

[ ] Security Log monitorado

[ ] System Log monitorado

[ ] Application Log monitorado

[ ] Auditoria configurada

[ ] Process Creation monitorado

[ ] PowerShell Logging avaliado

[ ] Authentication Events monitorados

[ ] Account Management monitorado

[ ] Group Management monitorado

[ ] RDP monitorado

[ ] Kerberos monitorado

[ ] Logs centralizados

[ ] NTP configurado

[ ] Retenção definida

[ ] SIEM integrado

[ ] Regras de correlação configuradas

[ ] Falsos positivos avaliados

[ ] Procedimentos de investigação documentados
```

---

# Resumo para o SOC

```text
Windows Event Logs
        ↓
Coleta
        ↓
Centralização
        ↓
SIEM
        ↓
Correlação
        ↓
Detecção
        ↓
Investigação
        ↓
Resposta
```

O ponto principal é entender que **um log isolado raramente conta toda a história**.

Um `4625` pode ser apenas uma senha digitada incorretamente.

Mas:

```text
4625
 ↓
4625
 ↓
4625
 ↓
4624
 ↓
4672
 ↓
4688
 ↓
4720
```

é uma sequência que merece atenção imediata.

É essa capacidade de transformar eventos aparentemente desconectados em uma **linha do tempo de ataque** que torna os Windows Event Logs tão importantes para um SOC.

> **O objetivo do analista não é decorar Event IDs. É entender o que aconteceu, quem realizou a ação, de onde veio, o que aconteceu depois e qual risco isso representa.**
