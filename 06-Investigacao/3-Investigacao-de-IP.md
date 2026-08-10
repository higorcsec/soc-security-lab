# Investigação de IP

## Introdução

Um endereço IP pode aparecer em praticamente qualquer investigação de segurança.

Ele pode representar:

```text
Origem de um ataque
Servidor comprometido
VPN
Proxy
NAT
Servidor legítimo
Infraestrutura de cloud
C2
Scanner
```

Por isso, encontrar um IP em um alerta não significa automaticamente que ele é malicioso.

O trabalho do analista é descobrir:

> **Quem está por trás desse IP, qual foi a atividade observada e qual relação ele possui com o incidente.**

A investigação normalmente segue:

```text
IP
 ↓
Identificação
 ↓
Contexto
 ↓
Reputação
 ↓
Correlação
 ↓
Escopo
 ↓
Classificação
```

---

# IP não é identidade

Um dos erros mais comuns durante uma investigação é tratar um endereço IP como se fosse uma pessoa ou equipamento específico.

Um único IP público pode representar:

```text
Usuário A
Usuário B
Usuário C
```

através de:

```text
NAT
Proxy
VPN
Gateway
```

Da mesma forma, um IP pode mudar de proprietário ou ser reutilizado.

Por isso:

```text
IP = indicador
```

e não:

```text
IP = atacante confirmado
```

---

# Tipos de IP

Antes de investigar, identificar o tipo do endereço.

## IPv4 privado

Faixas comuns:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

Normalmente utilizados dentro de redes privadas.

---

## IPv4 público

Endereços roteáveis na Internet.

Exemplo:

```text
185.x.x.x
```

Podem pertencer a:

```text
ISP
Cloud Provider
Empresa
Data Center
VPN
Hosting
```

---

## IPv6

O mesmo conceito se aplica ao IPv6.

Um ambiente moderno pode registrar:

```text
IPv4
+
IPv6
```

O analista precisa evitar assumir que toda comunicação externa será IPv4.

---

# Primeira pergunta: o IP é interno ou externo?

Exemplo:

```text
Source:
10.10.10.25
```

provavelmente representa um endereço interno.

Já:

```text
Source:
185.x.x.x
```

provavelmente representa uma origem externa.

Essa distinção define o caminho da investigação.

---

# Investigação de IP interno

Quando o IP é interno:

```text
10.10.10.25
```

a primeira pergunta é:

> **Qual ativo utiliza esse endereço?**

Pesquisar:

```text
Hostname
MAC
DHCP
DNS
Usuário
VLAN
Sistema operacional
```

O objetivo é chegar em:

```text
IP
 ↓
Host
 ↓
Usuário
 ↓
Processo
 ↓
Atividade
```

---

# DHCP

DHCP pode ajudar a descobrir qual dispositivo recebeu determinado endereço em determinado momento.

Exemplo:

```text
IP:
10.10.10.25

Time:
14:30

DHCP:
WS-023
```

Isso é importante porque:

```text
IP pode mudar.
```

O mesmo endereço pode ter sido utilizado por outro dispositivo posteriormente.

Por isso, sempre considerar:

```text
IP + Timestamp
```

---

# DNS interno

O DNS pode ajudar a associar:

```text
IP → Hostname
```

Exemplo:

```text
10.10.10.25
      ↓
WS-FINANCEIRO-23
```

Depois:

```text
WS-FINANCEIRO-23
      ↓
Usuário
```

Essa associação pode ser essencial para determinar o responsável pelo ativo.

---

# IP externo

Para um IP público:

```text
185.x.x.x
```

começar com:

```text
Whois
ASN
ISP
Geolocalização aproximada
Reverse DNS
Reputação
Histórico
```

O objetivo é entender:

> **Que infraestrutura está associada a esse endereço?**

---

# WHOIS

WHOIS pode fornecer informações relacionadas ao registro do IP.

Exemplo conceitual:

```text
IP:
185.x.x.x

Organization:
Example Hosting

Country:
Brazil

ASN:
AS12345
```

Isso ajuda a contextualizar.

Mas não significa que:

```text
Example Hosting = atacante
```

O IP pode ser:

```text
Servidor compartilhado
VPS
Cliente legítimo
Proxy
Infraestrutura comprometida
```

---

# ASN

O ASN identifica uma rede autônoma.

Exemplo:

```text
IP
 ↓
ASN
 ↓
Organização
```

Isso ajuda a entender se o IP pertence a:

```text
Cloud Provider
ISP
Data Center
Hosting
Empresa
```

Durante uma investigação, o ASN pode revelar que dezenas de IPs suspeitos pertencem à mesma infraestrutura.

---

# Reverse DNS

Uma consulta reversa pode retornar um hostname.

Exemplo:

```text
185.x.x.x
      ↓
server.example.net
```

Isso pode fornecer pistas sobre:

```text
Provider
Serviço
Infraestrutura
Organização
```

Mas reverse DNS não é prova de legitimidade.

---

# Geolocalização

A geolocalização pode indicar:

```text
País
Região
Cidade aproximada
ISP
```

Exemplo:

```text
Usuário:
São Paulo

IP:
Europa
```

Isso pode ser interessante.

Mas:

```text
Localização diferente
```

não significa:

```text
Ataque confirmado
```

O usuário pode estar utilizando:

```text
VPN
Proxy
Cloud
Roaming
```

Geolocalização é contexto, não evidência definitiva.

---

# Reputação do IP

Ferramentas de Threat Intelligence podem indicar se um IP foi associado a:

```text
Malware
Spam
Phishing
Scanning
Brute Force
Botnet
C2
```

Exemplo:

```text
IP:
185.x.x.x

Reputation:
Malicious

Categories:
Brute Force
Scanning
```

Isso aumenta a suspeita.

Mas a pergunta seguinte deve ser:

> **Esse IP realizou alguma atividade relevante contra o meu ambiente?**

---

# Reputação não encerra a investigação

Imagine:

```text
IP:
185.x.x.x

Reputação:
Malicious
```

Isso é relevante.

Mas ainda precisamos saber:

```text
Quando ocorreu?

Qual host foi acessado?

Qual porta?

Qual protocolo?

Qual usuário?

Houve sucesso?

Qual foi o impacto?
```

Um indicador externo precisa ser correlacionado com a telemetria interna.

---

# Primeiro Seen e Last Seen

Durante a investigação, procurar:

```text
First Seen
Last Seen
```

Exemplo:

```text
First Seen:
08/08 13:20

Last Seen:
09/08 15:42
```

Isso ajuda a entender:

```text
Quando começou?
Ainda está acontecendo?
```

---

# Frequência

Quantidade de conexões pode ser um indicador importante.

Exemplo:

```text
1 conexão
```

versus:

```text
5.000 conexões
```

O segundo caso merece mais atenção.

Mas ainda é necessário entender:

```text
Qual serviço?
Qual aplicação?
Qual origem?
Qual destino?
```

---

# Portas

Um IP pode aparecer conectado a várias portas.

Exemplo:

```text
22
80
443
445
3389
```

Isso pode revelar serviços sendo acessados ou tentativas de reconhecimento.

Exemplo:

```text
185.x.x.x
   ↓
22
80
443
445
3389
```

Pode indicar:

```text
Scanning
```

Mas novamente:

```text
Scanning ≠ Comprometimento
```

---

# Estado da conexão

Um firewall pode registrar:

```text
ALLOW
DENY
DROP
RESET
```

Isso muda completamente a investigação.

Exemplo:

```text
IP suspeito
 ↓
Connection attempt
 ↓
DENY
```

significa que o firewall bloqueou a tentativa.

Já:

```text
IP suspeito
 ↓
ALLOW
 ↓
Session
```

exige mais investigação.

---

# IP de origem x IP de destino

Sempre identificar a direção.

```text
Source IP
Destination IP
```

Exemplo:

```text
Source:
185.x.x.x

Destination:
10.10.10.20
```

Nesse caso:

```text
Internet
   ↓
Servidor interno
```

Agora:

```text
Source:
10.10.10.20

Destination:
185.x.x.x
```

temos:

```text
Servidor interno
   ↓
Internet
```

A hipótese muda.

---

# Investigação de tráfego de entrada

Exemplo:

```text
Internet
   ↓
Firewall
   ↓
Web Server
```

Investigar:

```text
IP
Porta
URL
Método
User Agent
Quantidade
Status Code
```

Pode indicar:

```text
Scanning
Exploração
Brute Force
Web Attack
```

---

# Investigação de tráfego de saída

Agora:

```text
Host interno
   ↓
Firewall
   ↓
Internet
```

Investigar:

```text
Destino
Porta
Domínio
Processo
Usuário
Volume
Frequência
```

Pode revelar:

```text
C2
Malware
Exfiltração
Atualização legítima
Cloud Service
```

---

# IP e DNS

Um IP pode estar associado a vários domínios.

Exemplo:

```text
IP
 ↓
domain-a.com
domain-b.com
domain-c.com
```

Isso é comum em:

```text
Shared Hosting
CDN
Cloud
Reverse Proxy
```

Portanto:

> **Um domínio apontando para um IP não significa que todo o IP representa aquele domínio.**

---

# Domínios associados

Durante a investigação:

```text
IP
 ↓
Domains
 ↓
Subdomains
 ↓
URLs
```

Isso pode revelar infraestrutura relacionada.

Exemplo:

```text
185.x.x.x

login.example.com
cdn.example.com
api.example.com
```

Depois investigar quais desses domínios realmente apareceram nos logs internos.

---

# IP e certificados

Certificados TLS podem fornecer informações adicionais.

Um IP pode hospedar serviços associados a certificados contendo:

```text
Common Name
Subject Alternative Names
Organization
Validity
```

Isso pode ajudar a descobrir infraestrutura relacionada.

Mas certificados também podem ser:

```text
Wildcard
Automáticos
Compartilhados
```

Portanto, devem ser tratados como fonte de contexto.

---

# IP e User-Agent

Em logs HTTP, o User-Agent pode revelar:

```text
Browser
Operating System
Bot
Script
Scanner
Library
```

Exemplo:

```text
User-Agent:
curl/8.x
```

pode ser legítimo.

Mas:

```text
IP suspeito
+
scanner
+
centenas de URLs
```

torna a hipótese mais interessante.

---

# IP e comportamento

Um IP desconhecido não é necessariamente perigoso.

O comportamento é mais importante.

Compare:

### Cenário A

```text
IP novo
 ↓
1 acesso
 ↓
Página pública
 ↓
200 OK
```

### Cenário B

```text
IP novo
 ↓
500 requisições
 ↓
Vários endpoints
 ↓
Tentativas de exploração
 ↓
Upload
```

O segundo possui muito mais sinais de ataque.

---

# IP e Brute Force

Exemplo:

```text
185.x.x.x
   ↓
/login
   ↓
100 tentativas
   ↓
20 usuários
```

Pode indicar:

```text
Password Spraying
```

Se houver:

```text
100 falhas
+
1 sucesso
```

o risco aumenta consideravelmente.

Investigar:

```text
Usuário
Host
Sessão
Atividade posterior
```

---

# IP e Scanning

Um IP pode tentar:

```text
22
23
25
53
80
443
445
3389
```

em vários hosts.

Isso pode indicar:

```text
Port Scanning
Network Discovery
```

A investigação deve determinar:

```text
Quantos hosts?
Quantas portas?
Qual intervalo?
Qual frequência?
```

---

# IP e Exploração

Um IP pode aparecer em:

```text
GET /admin
GET /.env
GET /wp-login.php
GET /../../etc/passwd
```

A análise deve procurar:

```text
Status Code
Resposta
Payload
Endpoint
Servidor
```

Importante:

```text
Tentativa de exploração
```

não significa:

```text
Exploração bem-sucedida
```

É necessário procurar evidências de sucesso.

---

# IP e Malware

Um endpoint pode estabelecer conexão com:

```text
185.x.x.x
```

O SOC deve procurar:

```text
Qual processo abriu a conexão?
Qual usuário?
Qual host?
Qual domínio?
Qual frequência?
```

Exemplo:

```text
svchost.exe
   ↓
185.x.x.x
```

pode ser legítimo ou malicioso.

Mas:

```text
unknown.exe
   ↓
185.x.x.x
```

merece investigação adicional.

---

# IP e C2

Indicadores que podem aumentar a suspeita:

```text
Conexões periódicas
Destino incomum
Processo desconhecido
Domínio suspeito
Comunicação persistente
```

Exemplo:

```text
10:00
Connection

10:05
Connection

10:10
Connection

10:15
Connection
```

Esse padrão periódico pode justificar investigação.

---

# IP e Exfiltração

Exemplo:

```text
Host interno
   ↓
IP externo
   ↓
500 MB
```

Investigar:

```text
Destino
Usuário
Processo
Arquivo
Horário
Volume
```

Um upload grande pode ser:

```text
Backup
Sincronização
Cloud Storage
Exfiltração
```

O contexto decide.

---

# Pivoting por IP

Encontrou um IP suspeito?

Não pare.

Use-o para pesquisar:

```text
IP
 ↓
Hosts
 ↓
Usuários
 ↓
Processos
 ↓
Domínios
 ↓
URLs
 ↓
Arquivos
 ↓
Outros IOCs
```

Exemplo:

```text
IP suspeito
 ↓
3 hosts
 ↓
1 usuário comum
 ↓
PowerShell
 ↓
Download
 ↓
Hash
```

Agora você possui vários indicadores para continuar a investigação.

---

# Pivoting reverso

Também é possível começar pelo host.

```text
Host:
WS-023
```

Pesquisar:

```text
Todos os IPs externos
```

Resultado:

```text
IP 1
IP 2
IP 3
IP 4
```

Depois identificar:

```text
Qual desses IPs é anômalo?
```

Essa abordagem é útil durante investigações de endpoint.

---

# Correlação com Firewall

Exemplo:

```text
EDR:
Suspicious Process
```

Pivot:

```text
Process
 ↓
Network Connection
 ↓
IP
```

Depois:

```text
IP
 ↓
Firewall
```

Confirmar:

```text
Quando?
Qual porta?
Qual volume?
Foi permitido?
```

Essa correlação aumenta a confiança da investigação.

---

# Correlação com DNS

Exemplo:

```text
Endpoint
 ↓
DNS Query
 ↓
example.com
 ↓
IP 185.x.x.x
```

Depois:

```text
IP
 ↓
Firewall
 ↓
Connection Allowed
```

Agora temos:

```text
DNS
+
Firewall
+
Endpoint
```

Uma visão muito mais completa.

---

# Correlação com Proxy

Se o ambiente possui proxy:

```text
Host
 ↓
Proxy
 ↓
URL
 ↓
IP
```

Pode ser possível descobrir:

```text
Usuário
URL
Método
User-Agent
Bytes
Resultado
```

Isso ajuda a determinar a finalidade da conexão.

---

# Correlação com EDR

O EDR pode responder uma pergunta importante:

> **Qual processo iniciou a conexão?**

Exemplo:

```text
IP:
185.x.x.x

Host:
WS-023

Process:
powershell.exe
```

Esse dado pode aumentar significativamente a prioridade da investigação.

---

# Investigação de IP no SIEM

Um fluxo comum:

```text
IP:
185.x.x.x
```

Pesquisar no SIEM:

```text
Source IP
Destination IP
```

Depois:

```text
Timestamp
User
Hostname
Port
Protocol
Action
```

Depois expandir:

```text
DNS
Proxy
EDR
Authentication
```

---

# Exemplo prático

## Alerta

```text
Source IP:
185.x.x.x

Destination:
10.10.10.20

Port:
443
```

### Passo 1 — Identificação

```text
IP público
```

### Passo 2 — Contexto

```text
ASN:
Hosting Provider
```

### Passo 3 — Reputação

```text
Malicious:
Yes
Category:
Scanning
```

### Passo 4 — Firewall

```text
Allowed
```

### Passo 5 — Web Server

```text
GET /admin
GET /.env
GET /login
GET /wp-login.php
```

### Passo 6 — Resultado

```text
Várias tentativas
+
IP conhecido por scanning
```

Classificação:

```text
Tentativa de reconhecimento/exploração
```

Ainda investigar se:

```text
Houve sucesso?
```

---

# Outro exemplo

## Alerta

```text
Host:
WS-023

Destination:
185.x.x.x
```

Firewall:

```text
ALLOW
```

DNS:

```text
example.com
```

EDR:

```text
powershell.exe
```

Timeline:

```text
14:20
User opened document

14:21
PowerShell

14:21
DNS Query

14:22
Connection to IP

14:23
File Created
```

Agora existe uma cadeia:

```text
Documento
 ↓
PowerShell
 ↓
DNS
 ↓
IP externo
 ↓
Arquivo
```

Esse cenário merece investigação de malware/phishing.

---

# IP compartilhado

Um IP pode hospedar múltiplos serviços.

Exemplo:

```text
185.x.x.x
   ├── site-a.com
   ├── site-b.com
   ├── site-c.com
```

Isso é comum em:

```text
Hosting
Cloud
CDN
Reverse Proxy
```

Portanto:

```text
IP malicioso
```

não significa necessariamente:

```text
Todos os domínios associados são maliciosos.
```

---

# IP de Cloud

Cloud providers podem possuir enormes blocos de IP.

Um atacante pode utilizar:

```text
AWS
Azure
Google Cloud
DigitalOcean
Cloudflare
```

Isso não significa que o provedor seja malicioso.

O que deve ser investigado é:

```text
IP
Conta/serviço quando disponível
Domínio
Certificado
Comportamento
Timestamp
```

---

# NAT

Em redes com NAT:

```text
Usuário A
Usuário B
Usuário C
   ↓
NAT
   ↓
IP público
```

Nos logs externos:

```text
185.x.x.x
```

pode parecer que existe apenas uma origem.

Por isso, dentro da rede é necessário correlacionar:

```text
IP público
+
Porta de origem
+
Timestamp
```

quando essa informação estiver disponível.

---

# VPN

VPN também altera a interpretação.

Um usuário pode aparecer:

```text
IP:
185.x.x.x
```

mas o IP pode pertencer ao servidor VPN corporativo.

Antes de classificar como origem suspeita:

```text
Verificar VPN
```

---

# False Positive

Exemplo:

```text
IP:
185.x.x.x

Reputation:
Suspicious
```

Mas:

```text
IP pertence à CDN utilizada pela empresa.
```

Resultado:

```text
Benigno
```

O indicador externo estava correto ao apontar risco histórico.

O contexto interno mostrou que a atividade era legítima.

---

# False Negative

O cenário oposto também existe.

Um IP:

```text
Reputation:
Clean
```

pode ser utilizado por um atacante hoje.

Reputação não acompanha todos os eventos em tempo real.

Por isso:

```text
Clean IP
≠
Trusted Activity
```

---

# O que registrar

Uma investigação de IP deve registrar:

```text
IP:
185.x.x.x

Tipo:
Public

ASN:
AS12345

Organization:
Example Hosting

Country:
Brazil

First Seen:
09/08/2026 10:20

Last Seen:
09/08/2026 14:42

Reputation:
Suspicious

Observed Activity:
Scanning

Affected Hosts:
3

Users:
2

Ports:
80, 443, 3389

Action:
Escalated
```

---

# Evidências

Sempre registrar evidências.

Exemplo:

```text
Firewall:
Connection allowed

DNS:
Domain resolved

EDR:
powershell.exe initiated connection

Web Server:
Multiple suspicious requests
```

Isso é muito mais útil do que:

```text
"IP malicioso."
```

---

# Quando bloquear um IP

Bloquear um IP pode ser uma ação de contenção.

Mas antes:

```text
Confirmar indicador
Verificar impacto
Verificar dependências
```

Especialmente quando o IP pertence a:

```text
CDN
Cloud
Hosting
Proxy
Serviço compartilhado
```

Um bloqueio incorreto pode interromper serviços legítimos.

---

# Bloqueio como resposta

Quando houver evidência suficiente:

```text
IP confirmado malicioso
       ↓
Firewall
       ↓
Block
```

Depois monitorar:

```text
Novos IPs
Novos domínios
Novos indicadores
```

Porque bloquear um único IP não necessariamente elimina o atacante.

---

# IP como ponto de partida

O IP raramente deve ser o ponto final.

Exemplo:

```text
IP
 ↓
Domínio
 ↓
URL
 ↓
Hash
 ↓
Processo
 ↓
Usuário
 ↓
Host
```

Cada elemento pode revelar uma nova parte do incidente.

---

# Checklist de Investigação de IP

```text
[ ] Identifiquei se o IP é interno ou externo?

[ ] Verifiquei o timestamp?

[ ] Identifiquei o host?

[ ] Identifiquei o usuário?

[ ] Verifiquei DHCP?

[ ] Verifiquei DNS?

[ ] Consultei WHOIS?

[ ] Identifiquei o ASN?

[ ] Identifiquei o provedor?

[ ] Verifiquei reverse DNS?

[ ] Consultei reputação?

[ ] Verifiquei histórico?

[ ] Identifiquei portas?

[ ] Identifiquei protocolos?

[ ] Verifiquei se a conexão foi permitida?

[ ] Analisei frequência?

[ ] Pesquisei outros hosts?

[ ] Pesquisei outros usuários?

[ ] Correlacionei com EDR?

[ ] Correlacionei com DNS?

[ ] Correlacionei com Firewall?

[ ] Correlacionei com Proxy?

[ ] Procurei domínios relacionados?

[ ] Procurei outros IOCs?

[ ] Avaliei impacto?

[ ] Registrei evidências?

[ ] Precisa de contenção?

[ ] Precisa de escalonamento?
```

---

# Fluxo de Investigação

```text
                  IP
                   │
                   ▼
          Interno ou Externo?
             │           │
             ▼           ▼
          Interno       Externo
             │           │
             ▼           ▼
           DHCP        WHOIS
           DNS          ASN
           Host       Reputation
             │           │
             └─────┬─────┘
                   ▼
              Contexto
                   │
                   ▼
              Firewall
                   │
                   ▼
                 DNS
                   │
                   ▼
                Proxy
                   │
                   ▼
                 EDR
                   │
                   ▼
               Pivoting
                   │
                   ▼
               Correlação
                   │
                   ▼
                Impacto
                   │
             ┌─────┴─────┐
             ▼           ▼
          Benigno     Malicioso
             │           │
             ▼           ▼
          Encerrar   Conter/Escalar
```

---

# Conclusão

Investigar um IP não significa apenas descobrir sua localização ou consultar uma lista de reputação.

A investigação real começa quando o indicador é colocado dentro do contexto do ambiente.

```text
IP
+
Timestamp
+
Host
+
Usuário
+
Processo
+
DNS
+
Firewall
+
Comportamento
=
Contexto
```

Um IP pode ser:

```text
Malicioso
Legítimo
Comprometido
Compartilhado
Desconhecido
```

A classificação depende das evidências.

A principal habilidade do analista é transformar:

```text
"Encontramos um IP suspeito."
```

em:

```text
"Esse IP realizou X contra Y,
no período Z,
utilizando determinado comportamento,
afetando N ativos,
e existem evidências que sustentam
a classificação como tentativa de ataque."
```

Esse é o objetivo de uma investigação de IP: **sair do indicador e chegar ao contexto.**
