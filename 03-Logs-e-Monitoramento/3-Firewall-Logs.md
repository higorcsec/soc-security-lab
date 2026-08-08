# Firewall Logs

## Visão geral

O firewall funciona como uma das primeiras barreiras entre redes confiáveis e não confiáveis.

Para um SOC, seus logs são uma fonte importante de evidências porque mostram **tentativas de comunicação entre origem e destino**.

Um firewall pode registrar:

```text
IP de origem
IP de destino
Porta de origem
Porta de destino
Protocolo
Ação
Interface
Data e hora
Regra aplicada
Bytes
Pacotes
```

Com essas informações, o analista consegue investigar comportamentos como:

```text
Port Scanning
Brute Force
Tentativas de acesso
Comunicação com IP suspeito
Tráfego bloqueado
Movimentação lateral
Exfiltração
Command & Control
```

O firewall não mostra necessariamente tudo o que aconteceu dentro de um sistema.

Ele mostra principalmente **como os sistemas estão tentando se comunicar**.

---

# O papel do firewall no SOC

Imagine uma infraestrutura:

```text
Internet
    │
    ▼
Firewall
    │
    ├── Web Server
    ├── Database
    ├── VPN
    └── Internal Network
```

Quando um dispositivo tenta estabelecer uma conexão, o firewall pode registrar essa atividade.

Exemplo:

```text
203.0.113.50
      │
      │ TCP/22
      ▼
Firewall
      │
      X
    DROP
```

Esse simples evento pode indicar apenas uma tentativa normal.

Mas centenas de tentativas contra diferentes portas podem revelar um scanner.

---

# Estrutura de um Firewall Log

Um evento pode possuir informações semelhantes a:

```text
Timestamp:
2026-08-07 20:15:32

Source IP:
203.0.113.50

Destination IP:
192.168.10.20

Source Port:
49152

Destination Port:
22

Protocol:
TCP

Action:
DENY
```

Podemos interpretar:

```text
203.0.113.50
        ↓
TCP/22
        ↓
192.168.10.20
        ↓
DENY
```

Ou seja:

> Um host tentou estabelecer uma conexão TCP com a porta 22 e o firewall bloqueou a comunicação.

---

# Principais campos

## Source IP

É o endereço IP que iniciou a comunicação.

Exemplo:

```text
SRC=203.0.113.50
```

Durante uma investigação, pode ser utilizado para:

```text
Identificar origem
Agrupar eventos
Pesquisar recorrência
Correlacionar com outros dispositivos
```

---

## Destination IP

É o destino da comunicação.

Exemplo:

```text
DST=192.168.10.20
```

Esse campo ajuda a identificar:

```text
Qual servidor foi alvo?
Qual máquina foi acessada?
Qual serviço estava sendo procurado?
```

---

## Source Port

Porta utilizada pela origem.

Normalmente pode ser uma porta dinâmica.

Exemplo:

```text
SRC_PORT=52341
```

Nem sempre ela possui grande valor investigativo isoladamente.

---

## Destination Port

É uma das informações mais importantes.

Exemplos:

```text
22    SSH
25    SMTP
53    DNS
80    HTTP
110   POP3
143   IMAP
443   HTTPS
445   SMB
3389  RDP
3306  MySQL
5432  PostgreSQL
```

Uma tentativa externa contra:

```text
TCP/3389
```

pode ser relevante em uma investigação de RDP.

---

# Protocol

Os protocolos mais comuns:

```text
TCP
UDP
ICMP
```

### TCP

Utilizado por diversos serviços:

```text
HTTP
HTTPS
SSH
SMTP
RDP
SMB
```

### UDP

Muito utilizado por:

```text
DNS
DHCP
NTP
VoIP
```

### ICMP

Utilizado para funções de rede, como:

```text
Ping
Diagnóstico
Controle de rede
```

---

# Action

O firewall normalmente registra a ação tomada.

Exemplos:

```text
ALLOW
ACCEPT
DENY
DROP
REJECT
```

Uma diferença importante:

```text
DROP
```

normalmente significa que o pacote foi descartado sem resposta.

Enquanto:

```text
REJECT
```

normalmente indica que o firewall respondeu informando que a conexão foi recusada.

A implementação exata depende da tecnologia utilizada.

---

# ALLOW não significa ataque

Um erro comum em análise de firewall é pensar:

```text
ALLOW = seguro
DROP = ataque
```

Não funciona assim.

Exemplo:

```text
Internet
 ↓
TCP/443
 ↓
Web Server
 ↓
ALLOW
```

Isso é esperado em um servidor web.

Mas:

```text
Internet
 ↓
TCP/22
 ↓
Servidor interno
 ↓
ALLOW
```

pode ser completamente diferente.

O contexto é o que determina o risco.

---

# DROP também não significa ataque

Um firewall pode registrar milhares de bloqueios todos os dias.

Exemplo:

```text
DROP
DROP
DROP
DROP
DROP
```

Isso pode ser simplesmente:

```text
Internet noise
Scanners
Bots
Tráfego inválido
Tentativas automáticas
```

O SOC precisa separar:

```text
Ruído
```

de:

```text
Comportamento realmente relevante
```

---

# Port Scanning

Um dos casos mais comuns analisados através de firewall logs é o **port scanning**.

Imagine:

```text
203.0.113.50
      ↓
TCP/21
TCP/22
TCP/23
TCP/25
TCP/53
TCP/80
TCP/110
TCP/443
TCP/445
TCP/3389
```

Um único IP tentando várias portas em sequência pode indicar reconhecimento.

Fluxo:

```text
Atacante
   ↓
Reconhecimento
   ↓
Port Scan
   ↓
Firewall
   ↓
Logs
   ↓
SOC
```

---

# Identificando um scanner

Imagine que o firewall registre:

```text
20:01:01 203.0.113.50 → 192.168.10.10:22 DROP
20:01:02 203.0.113.50 → 192.168.10.10:23 DROP
20:01:03 203.0.113.50 → 192.168.10.10:25 DROP
20:01:04 203.0.113.50 → 192.168.10.10:80 ALLOW
20:01:05 203.0.113.50 → 192.168.10.10:443 ALLOW
```

O padrão é mais interessante do que qualquer evento individual.

---

# Horizontal Scan

Um atacante pode testar uma mesma porta em vários hosts.

Exemplo:

```text
203.0.113.50
      │
      ├── 192.168.10.10:22
      ├── 192.168.10.11:22
      ├── 192.168.10.12:22
      ├── 192.168.10.13:22
      └── 192.168.10.14:22
```

Isso pode indicar:

```text
Horizontal Scan
```

Por exemplo, procurar SSH em toda a rede.

---

# Vertical Scan

No vertical scan, o atacante testa várias portas de um mesmo host.

```text
203.0.113.50
      │
      └── 192.168.10.10
              ├── 21
              ├── 22
              ├── 23
              ├── 80
              ├── 443
              ├── 445
              └── 3389
```

Isso pode indicar reconhecimento do servidor.

---

# Brute Force

Firewall logs podem ajudar a identificar tentativas de brute force, especialmente quando correlacionados com logs do serviço.

Exemplo:

```text
Firewall
    ↓
Muitas conexões
    ↓
TCP/22
    ↓
Servidor Linux
    ↓
auth.log
    ↓
Failed password
```

O firewall mostra a comunicação.

O servidor mostra o resultado da autenticação.

A combinação é muito mais poderosa.

---

# Exemplo de SSH Brute Force

Firewall:

```text
203.0.113.50 → 192.168.10.20:22
203.0.113.50 → 192.168.10.20:22
203.0.113.50 → 192.168.10.20:22
203.0.113.50 → 192.168.10.20:22
```

Linux:

```text
Failed password
Failed password
Failed password
Failed password
```

Agora existe uma evidência muito mais forte.

---

# RDP

A porta:

```text
3389/TCP
```

é tradicionalmente associada ao Remote Desktop Protocol.

Uma grande quantidade de tentativas externas:

```text
Internet
   ↓
3389
   ↓
Firewall
```

deve ser investigada, principalmente quando o serviço não deveria estar exposto diretamente à Internet.

---

# SMB

A porta:

```text
445/TCP
```

é utilizada pelo SMB.

Tráfego inesperado nessa porta pode ser relevante em investigações envolvendo:

```text
Windows
Active Directory
File Sharing
Lateral Movement
```

Por exemplo:

```text
Servidor A
    ↓
TCP/445
    ↓
Servidor B
```

pode ser completamente legítimo.

Mas:

```text
Workstation
    ↓
TCP/445
    ↓
Diversos servidores
```

pode merecer investigação dependendo do comportamento.

---

# DNS

DNS normalmente utiliza:

```text
53/UDP
53/TCP
```

Firewall logs podem ajudar a identificar:

```text
Consultas DNS inesperadas
Servidores realizando DNS diretamente para a Internet
Comunicação com DNS externo
Volume anormal
```

Esse tipo de análise fica ainda mais interessante quando combinado com:

```text
DNS Logs
```

que serão tratados em outro arquivo deste repositório.

---

# Tráfego de saída

Um dos pontos mais importantes em segurança é analisar o **egress traffic**.

Não basta observar:

```text
Internet → Empresa
```

Também é necessário observar:

```text
Empresa → Internet
```

Exemplo:

```text
Servidor comprometido
        ↓
Conexão externa
        ↓
IP desconhecido
        ↓
Firewall
        ↓
ALLOW
```

Esse tráfego pode estar relacionado a:

```text
Command & Control
Download de malware
Exfiltração
Atualização legítima
API
Serviço externo
```

Novamente, contexto.

---

# Command & Control

Um servidor comprometido pode tentar estabelecer comunicação com infraestrutura controlada pelo atacante.

Exemplo:

```text
Servidor
   ↓
HTTPS
   ↓
IP externo suspeito
   ↓
Firewall
   ↓
ALLOW
```

O firewall pode fornecer:

```text
IP
Porta
Horário
Volume
Frequência
```

Esses dados podem ser correlacionados com:

```text
DNS
EDR
Proxy
Web Logs
Processos
SIEM
```

---

# Beaconing

Um comportamento interessante durante threat hunting é a comunicação periódica.

Exemplo:

```text
10:00 → IP externo
10:05 → IP externo
10:10 → IP externo
10:15 → IP externo
10:20 → IP externo
```

Uma comunicação regular pode indicar:

```text
Beaconing
```

Isso não significa automaticamente malware.

Aplicações legítimas também podem realizar conexões periódicas.

O padrão precisa ser investigado.

---

# Exfiltração

Firewall logs podem contribuir para investigações de exfiltração.

Imagine:

```text
Servidor
   ↓
500 MB
   ↓
IP externo
   ↓
TCP/443
```

O tráfego HTTPS é comum e pode ser legítimo.

Por isso, o firewall sozinho normalmente não consegue determinar:

```text
O que foi enviado?
```

Mas pode revelar:

```text
Quem enviou?
Para onde?
Quando?
Quanto?
Com que frequência?
```

Isso já é uma informação importante.

---

# Bytes e Packets

Alguns firewalls registram quantidade de:

```text
Bytes
Packets
```

Exemplo:

```text
SRC=192.168.10.20
DST=203.0.113.50
BYTES=524288000
```

Isso representa aproximadamente:

```text
500 MB
```

Uma quantidade inesperada de dados enviados para um destino externo pode justificar investigação.

---

# NAT

Em ambientes corporativos, muitos dispositivos podem compartilhar um endereço IP público através de NAT.

Exemplo:

```text
192.168.1.10 ─┐
192.168.1.11 ─┤
192.168.1.12 ─┤
              ▼
          Firewall/NAT
              ▼
        203.0.113.10
```

Se o SOC visualizar apenas:

```text
203.0.113.10
```

pode não saber qual máquina interna originou a conexão.

Por isso, logs de NAT são importantes.

---

# NAT Logs

Dependendo do firewall, podem existir informações como:

```text
Private IP
Private Port
Public IP
Public Port
Destination IP
Timestamp
```

Isso permite reconstruir:

```text
IP público
   ↓
NAT
   ↓
IP interno
```

Sem essa informação, uma investigação pode ficar limitada.

---

# Firewall em ambientes corporativos

Uma arquitetura comum:

```text
Internet
    │
    ▼
[ Firewall ]
    │
    ├── DMZ
    │    ├── Web
    │    └── Mail
    │
    └── Internal
         ├── Users
         ├── Servers
         └── Database
```

O firewall pode gerar logs de cada zona.

Isso permite ao SOC analisar:

```text
Internet → DMZ
Internet → Internal
DMZ → Internal
Internal → Internet
Internal → Internal
```

---

# DMZ

A DMZ normalmente contém serviços que precisam ser acessíveis por redes externas.

Exemplos:

```text
Web Server
Mail Gateway
Reverse Proxy
VPN Gateway
```

Uma regra pode ser:

```text
Internet
   ↓
TCP/443
   ↓
Web Server
   ↓
ALLOW
```

Enquanto:

```text
Internet
   ↓
TCP/3306
   ↓
Database
   ↓
DENY
```

Essa diferença é importante para a segurança da arquitetura.

---

# Firewall Logs e regras

Um evento pode informar qual regra foi aplicada.

Exemplo:

```text
Rule:
ALLOW-WEB-HTTPS

Source:
Internet

Destination:
Web-Server

Port:
443

Action:
ALLOW
```

Durante uma investigação, saber **qual regra permitiu ou bloqueou a comunicação** pode ser tão importante quanto o próprio IP.

---

# Regra excessivamente permissiva

Imagine uma regra:

```text
Source:
ANY

Destination:
ANY

Service:
ANY

Action:
ALLOW
```

Uma regra assim pode representar um enorme risco dependendo do contexto.

Logs podem ajudar a demonstrar o impacto de regras muito permissivas.

Por exemplo:

```text
Internet
   ↓
ANY
   ↓
ANY
   ↓
Internal Network
```

O problema não está apenas no log.

Está na arquitetura e na política de controle de acesso.

---

# Correlação com outros logs

Firewall logs ficam muito mais valiosos quando correlacionados com outras fontes.

Exemplo:

```text
Firewall
    ↓
Connection
    ↓
Server
    ↓
Authentication Log
    ↓
Process Log
    ↓
DNS Log
    ↓
SIEM
```

---

# Caso prático

Imagine o seguinte cenário:

```text
203.0.113.50
      ↓
TCP/22
      ↓
Servidor Linux
```

O firewall registra:

```text
20:10 DROP
20:10 DROP
20:10 DROP
20:11 DROP
20:11 ALLOW
```

O Linux registra:

```text
Failed password
Failed password
Failed password
Accepted password
```

Depois:

```text
sudo
```

E:

```text
cron altered
```

A investigação agora possui:

```text
Reconhecimento
      ↓
Tentativa de acesso
      ↓
Acesso obtido
      ↓
Privilégio
      ↓
Persistência
```

O firewall foi apenas uma das peças.

---

# Threat Hunting

O firewall também pode ser utilizado de forma proativa.

Exemplo de hipótese:

> "Existe algum servidor interno realizando conexões periódicas para um destino externo desconhecido?"

O analista pode procurar:

```text
Conexões repetitivas
```

Depois:

```text
Agrupar por IP destino
```

Depois:

```text
Verificar frequência
```

Depois:

```text
Correlacionar com DNS
```

E finalmente:

```text
Identificar o processo responsável
```

---

# Indicadores importantes

Durante uma análise, alguns padrões merecem atenção:

```text
Muitas conexões para uma única porta
Muitas portas em um mesmo host
Uma mesma porta em vários hosts
Conexões externas periódicas
Volume anormal de saída
Comunicação com IP desconhecido
Serviço interno exposto
Conexões entre segmentos que não deveriam se comunicar
Mudança repentina no padrão de tráfego
```

---

# Firewall + SIEM

Em ambientes maiores, os logs podem ser enviados para um SIEM.

Fluxo:

```text
Firewall
   ↓
Syslog / API / Agent
   ↓
SIEM
   ↓
Parsing
   ↓
Normalization
   ↓
Correlation
   ↓
Alert
   ↓
SOC
```

---

# Firewall + Wazuh

Dependendo do ambiente, eventos de firewall podem ser coletados e analisados pelo Wazuh.

Exemplo:

```text
Firewall
   ↓
Syslog
   ↓
Wazuh
   ↓
Rules
   ↓
Alert
```

Um caso de uso pode ser detectar grande quantidade de conexões bloqueadas originadas do mesmo IP.

---

# Firewall + Splunk

No Splunk, os logs podem ser pesquisados e agregados.

Exemplo conceitual:

```spl
index=firewall action=blocked
```

Para identificar os IPs com maior número de bloqueios:

```spl
index=firewall action=blocked
| stats count by src_ip
| sort - count
```

Para analisar portas:

```spl
index=firewall action=blocked
| stats count by dest_port
| sort - count
```

A nomenclatura dos campos depende do firewall e do parsing configurado.

---

# Firewall + Microsoft Sentinel

Os logs também podem ser enviados ao Microsoft Sentinel.

Fluxo:

```text
Firewall
   ↓
Connector / Syslog
   ↓
Log Analytics
   ↓
Microsoft Sentinel
   ↓
Analytics Rules
   ↓
Incident
```

O SOC pode então correlacionar firewall com:

```text
Windows
Linux
DNS
Identity
Cloud
Endpoint
```

---

# MITRE ATT&CK

Firewall logs podem fornecer evidências relacionadas a diversas técnicas.

Exemplos:

```text
T1046
Network Service Scanning
```

```text
T1021
Remote Services
```

```text
T1071
Application Layer Protocol
```

```text
T1041
Exfiltration Over C2 Channel
```

```text
T1571
Non-Standard Port
```

O mapeamento depende do comportamento identificado e do contexto da investigação.

---

# Falsos positivos

Nem todo comportamento estranho é malicioso.

Exemplos:

```text
Scanner interno autorizado
Monitoramento
Backup
Atualização
Antivírus
CDN
API
Cloud Service
Health Check
```

Por isso, antes de abrir um incidente, pergunte:

```text
Esse tráfego era esperado?
O destino é conhecido?
Existe mudança recente?
Existe uma janela de manutenção?
Esse servidor deveria realizar essa conexão?
```

---

# O que um firewall não consegue responder sozinho?

Essa é uma distinção importante.

O firewall pode mostrar:

```text
A → B
TCP/443
ALLOW
```

Mas não necessariamente consegue dizer:

```text
Qual processo abriu a conexão?
Qual arquivo foi enviado?
Qual comando foi executado?
Qual usuário iniciou a ação?
```

Para isso, precisamos correlacionar com outras fontes:

```text
Firewall
+
Endpoint
+
Linux/Windows Logs
+
DNS
+
Proxy
+
SIEM
```

---

# Linha do tempo

Uma investigação pode ficar assim:

```text
20:00
Firewall → Port Scan

20:02
Firewall → SSH Attempt

20:03
Firewall → SSH Allowed

20:03
Linux → Successful Login

20:04
Linux → sudo

20:05
Linux → Process Created

20:06
Firewall → Outbound Connection

20:07
DNS → Suspicious Domain
```

Agora o SOC possui uma narrativa:

```text
Reconhecimento
      ↓
Acesso
      ↓
Execução
      ↓
Comunicação externa
```

---

# Boas práticas

## Centralizar os logs

Não dependa apenas do armazenamento local do firewall.

```text
Firewall
   ↓
Central Collector
   ↓
SIEM
```

---

## Sincronizar horário

Utilize NTP.

Todos os dispositivos precisam possuir timestamps consistentes.

```text
Firewall
Server
DNS
SIEM
Endpoint
```

---

## Monitorar tráfego de entrada e saída

Não observe apenas:

```text
Internet → Empresa
```

Observe também:

```text
Empresa → Internet
```

Em muitos incidentes, o tráfego de saída revela o comprometimento.

---

## Monitorar alterações de regras

Mudanças nas regras do firewall devem ser registradas.

Exemplo:

```text
20:00
ALLOW TCP/22

20:01
Regra alterada

20:02
Internet → SSH
```

Uma alteração inesperada pode ser parte do próprio incidente.

---

## Definir retenção

Firewall logs podem crescer rapidamente.

Defina:

```text
Retenção
Rotação
Armazenamento
Compressão
Backup
```

considerando o volume e os requisitos do ambiente.

---

# Checklist

```text
[ ] Firewall gerando logs

[ ] Logs de ALLOW coletados

[ ] Logs de DENY/DROP coletados

[ ] Source IP registrado

[ ] Destination IP registrado

[ ] Source Port registrado

[ ] Destination Port registrado

[ ] Protocol registrado

[ ] Timestamp sincronizado

[ ] Regra aplicada registrada

[ ] NAT registrado quando necessário

[ ] Tráfego de entrada monitorado

[ ] Tráfego de saída monitorado

[ ] Alterações de regras monitoradas

[ ] Logs centralizados

[ ] SIEM integrado

[ ] Alertas de port scan

[ ] Alertas de brute force

[ ] Alertas de tráfego anormal

[ ] Threat Hunting configurado

[ ] Retenção definida
```

---

# Resumo para o SOC

O firewall responde uma pergunta fundamental:

> **Quem está tentando conversar com quem?**

A investigação começa com:

```text
Origem
  ↓
Destino
  ↓
Porta
  ↓
Protocolo
  ↓
Ação
  ↓
Horário
```

Mas o trabalho do SOC começa quando essas informações são correlacionadas.

```text
Firewall
   +
DNS
   +
Windows
   +
Linux
   +
Endpoint
   +
SIEM
```

Uma conexão isolada pode ser normal.

Uma sequência de eventos pode revelar um ataque.

---

# Conclusão

Firewall logs são uma das principais fontes de visibilidade sobre o tráfego de uma infraestrutura.

Seu valor não está apenas em mostrar o que foi bloqueado.

Um dos maiores benefícios para um SOC é conseguir identificar **mudanças de comportamento na comunicação entre sistemas**.

Um único:

```text
DROP TCP/22
```

pode ser apenas ruído da Internet.

Mas:

```text
Port Scan
   ↓
SSH Attempt
   ↓
SSH Allowed
   ↓
Successful Login
   ↓
Privilege Escalation
   ↓
Outbound Connection
```

é uma sequência que merece investigação.

> **O firewall mostra o caminho da comunicação. O SOC transforma esse caminho em contexto, evidência e decisão.**
