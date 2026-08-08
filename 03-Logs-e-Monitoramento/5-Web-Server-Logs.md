# Web Server Logs

## Visão geral

Web servers estão entre os sistemas mais expostos de uma infraestrutura.

Servidores como:

```text
Nginx
Apache
IIS
```

recebem constantemente requisições de:

```text
Usuários
Bots
APIs
Crawlers
Scanners
Aplicações
Atacantes
```

Por isso, seus logs são uma das principais fontes de evidências para um SOC.

Uma requisição aparentemente simples:

```text
GET /login HTTP/1.1
```

pode representar desde um acesso legítimo até uma tentativa de exploração.

O trabalho do SOC é entender o **padrão por trás das requisições**.

---

# O que são Web Server Logs?

São registros das requisições processadas por um servidor web.

Um log pode registrar:

```text
Data e hora
IP de origem
Método HTTP
URL
Status HTTP
User-Agent
Referer
Bytes enviados
Tempo de resposta
Host
```

Exemplo:

```text
192.168.10.50 - - [07/Aug/2026:20:15:32]
"GET /login HTTP/1.1"
200
1842
```

Podemos interpretar:

```text
IP:
192.168.10.50

Método:
GET

URI:
/login

Status:
200

Resposta:
1842 bytes
```

---

# Por que Web Logs são importantes?

Porque representam diretamente a interação entre clientes e aplicações web.

Eles podem revelar:

```text
Brute Force
Web Scanning
Directory Traversal
SQL Injection
XSS
Command Injection
File Inclusion
Upload malicioso
Web Shell
Exploração de vulnerabilidades
Bots
Abuso de APIs
```

Além disso, são fundamentais para reconstruir uma linha do tempo.

---

# Fluxo básico

Uma arquitetura simples:

```text
Internet
    ↓
Firewall
    ↓
Reverse Proxy
    ↓
Web Server
    ↓
Application
    ↓
Database
```

Cada camada pode gerar logs.

Por exemplo:

```text
Firewall
   ↓
Web Server
   ↓
Application
   ↓
Database
```

O SOC pode correlacionar todos eles.

---

# Access Log

O access log registra as requisições recebidas.

Exemplo:

```text
192.168.10.50 - - [07/Aug/2026:20:15:32]
"GET /index.html HTTP/1.1"
200
5321
```

Informações importantes:

```text
IP
Timestamp
Método
URI
Status
Tamanho da resposta
```

---

# Error Log

O error log registra problemas encontrados pelo servidor.

Exemplo:

```text
2026/08/07 20:15:40
[error]
open()
"/var/www/html/admin.php"
failed
```

Durante uma investigação, os dois logs podem ser analisados juntos.

```text
Access Log
    ↓
Requisição
    ↓
Error Log
    ↓
Erro gerado
```

---

# Nginx

Em instalações tradicionais:

```text
/var/log/nginx/access.log
/var/log/nginx/error.log
```

Exemplo:

```text
192.168.10.20 - - [07/Aug/2026:20:10:12]
"GET /index.php HTTP/1.1"
200
1250
```

O caminho pode variar conforme a distribuição e a configuração.

---

# Apache

Em ambientes Apache, é comum encontrar:

```text
/var/log/apache2/access.log
/var/log/apache2/error.log
```

ou:

```text
/var/log/httpd/access_log
/var/log/httpd/error_log
```

dependendo da distribuição.

---

# IIS

No Windows/IIS, os logs possuem estrutura própria e normalmente são armazenados em diretórios como:

```text
C:\inetpub\logs\LogFiles\
```

Os campos podem incluir:

```text
date
time
c-ip
cs-method
cs-uri-stem
sc-status
sc-substatus
sc-win32-status
```

---

# HTTP Methods

Os métodos HTTP aparecem frequentemente nos logs.

## GET

Solicita um recurso.

```text
GET /index.html
```

Muito comum em acessos normais.

---

## POST

Normalmente envia dados ao servidor.

```text
POST /login
```

É especialmente importante para analisar:

```text
Login
Upload
Formulários
APIs
```

---

## PUT

Pode ser utilizado para criação ou atualização de recursos em APIs.

---

## DELETE

Solicita a remoção de um recurso.

---

## HEAD

Semelhante ao GET, mas normalmente solicita apenas os headers.

Pode aparecer em:

```text
Scanners
Monitoramento
Crawlers
```

---

# HTTP Status Codes

Os códigos HTTP são extremamente importantes.

## 200

```text
200 OK
```

A requisição foi processada com sucesso.

---

## 201

```text
201 Created
```

Um recurso foi criado.

Muito comum em APIs.

---

## 301 / 302

Redirecionamentos.

```text
301 Moved Permanently
302 Found
```

Podem ser normais, especialmente em:

```text
HTTP → HTTPS
Login
Aplicações web
```

---

## 400

```text
400 Bad Request
```

A requisição não foi aceita pelo servidor.

Uma quantidade anormal pode indicar:

```text
Requisições malformadas
Scanner
Abuso
```

---

## 401

```text
401 Unauthorized
```

Normalmente relacionado à necessidade de autenticação.

Muitos `401` podem indicar tentativa de acesso a um recurso protegido.

---

## 403

```text
403 Forbidden
```

O servidor entendeu a requisição, mas recusou o acesso.

Pode aparecer durante:

```text
Scanning
Directory Enumeration
Tentativas de acesso
```

---

## 404

```text
404 Not Found
```

O recurso não foi encontrado.

Muitos `404` de uma mesma origem podem indicar:

```text
Directory Scanning
Reconhecimento
Bot
Links quebrados
```

---

## 405

```text
405 Method Not Allowed
```

O método HTTP não é permitido naquele endpoint.

Pode ser relevante durante scans automatizados.

---

## 500

```text
500 Internal Server Error
```

Erro interno da aplicação ou servidor.

Uma sequência de `500` pode indicar:

```text
Bug
Exploração
Payload malformado
Sobrecarga
Falha da aplicação
```

---

# IP de origem

O IP é um dos primeiros campos analisados.

Exemplo:

```text
203.0.113.50
```

Mas cuidado:

> O IP registrado no Web Server nem sempre representa diretamente o atacante.

Pode existir:

```text
CDN
Reverse Proxy
Load Balancer
NAT
WAF
```

entre o cliente e o servidor.

---

# X-Forwarded-For

Em arquiteturas com proxy, pode aparecer:

```text
X-Forwarded-For
```

Exemplo:

```text
Client:
203.0.113.50

Proxy:
192.168.10.5
```

O servidor pode registrar:

```text
X-Forwarded-For: 203.0.113.50
```

Mas esse cabeçalho precisa ser tratado corretamente.

Não se deve confiar cegamente em um `X-Forwarded-For` enviado diretamente pelo cliente.

A infraestrutura precisa estar configurada para confiar apenas nos proxies conhecidos.

---

# User-Agent

O User-Agent informa o cliente que realizou a requisição.

Exemplo:

```text
Mozilla/5.0
```

Pode indicar:

```text
Browser
Bot
Crawler
Script
Ferramenta de segurança
```

User-Agent suspeito:

```text
sqlmap
nikto
curl
python-requests
```

não significa automaticamente ataque.

Ferramentas legítimas de administração e monitoramento também podem utilizar esses clientes.

---

# Scanning

Um atacante pode realizar reconhecimento da aplicação.

Exemplo:

```text
GET /admin
GET /backup
GET /login
GET /test
GET /.git
GET /config
GET /phpmyadmin
GET /robots.txt
```

Se isso acontecer em sequência, pode indicar:

```text
Directory Enumeration
Web Scanning
Reconhecimento
```

---

# Directory Enumeration

Um scanner pode tentar descobrir arquivos e diretórios.

Exemplo:

```text
GET /admin
GET /administrator
GET /backup
GET /old
GET /test
GET /dev
GET /.env
```

O resultado pode ser:

```text
404
404
404
403
200
```

O `200` pode ser especialmente interessante porque indica que um recurso realmente existe.

---

# Arquivos sensíveis

Alguns caminhos merecem atenção especial:

```text
/.env
/.git/
/backup
/config
/wp-config.php
/phpinfo.php
/admin
```

O risco depende do ambiente.

Por exemplo, uma exposição de `.env` pode revelar:

```text
Credenciais
API Keys
Database Credentials
Secrets
```

Por isso, requisições a esses arquivos são importantes para investigação.

---

# Directory Traversal

Uma tentativa de traversal pode aparecer como:

```text
GET /../../etc/passwd
```

ou através de variantes codificadas.

O objetivo pode ser acessar arquivos fora do diretório permitido.

O SOC deve observar:

```text
URI
Status
Resposta
Origem
Frequência
```

---

# Path Traversal e codificação

Ataques podem utilizar URL encoding.

Exemplo conceitual:

```text
%2e%2e%2f
```

que representa uma variação de:

```text
../
```

Por isso, procurar apenas pela string literal:

```text
../
```

pode não ser suficiente.

A normalização dos logs é importante.

---

# SQL Injection

Uma tentativa de SQL Injection pode aparecer em parâmetros.

Exemplo:

```text
GET /products?id=1' OR '1'='1
```

Outros padrões podem envolver palavras e operadores SQL.

O importante para o SOC não é decorar payloads.

É reconhecer:

```text
Entrada anormal
      ↓
Parâmetro
      ↓
Aplicação
      ↓
Resposta inesperada
```

---

# SQL Injection + Status Code

Imagine:

```text
20:00 GET /product?id=1       200
20:01 GET /product?id='       500
20:02 GET /product?id=' OR... 500
```

Uma sequência assim pode ser mais relevante do que uma requisição isolada.

---

# Cross-Site Scripting

Tentativas de XSS podem aparecer em parâmetros ou caminhos.

Exemplo conceitual:

```text
GET /search?q=<script>...</script>
```

O SOC deve procurar:

```text
Entrada suspeita
Endpoint afetado
Origem
Resposta
Repetição
```

---

# Command Injection

Aplicações vulneráveis podem receber comandos através de parâmetros.

Exemplo conceitual:

```text
GET /ping?host=example.com
```

seguido de entrada maliciosa.

O Web Server Log pode mostrar o primeiro estágio:

```text
HTTP Request
```

mas a confirmação normalmente exige correlação com:

```text
Application Logs
Process Logs
EDR
Linux audit logs
```

---

# File Inclusion

Ataques de inclusão de arquivos podem aparecer em parâmetros.

Exemplo:

```text
GET /page?file=...
```

O servidor pode registrar:

```text
200
403
404
500
```

Dependendo do comportamento da aplicação.

Novamente:

> O Web Server Log mostra a tentativa. Outros logs podem confirmar o impacto.

---

# Brute Force

Web logs são excelentes para detectar tentativas repetitivas de autenticação.

Exemplo:

```text
POST /login
POST /login
POST /login
POST /login
POST /login
```

Mesmo IP.

Mesmo endpoint.

Pouco intervalo.

Isso pode indicar:

```text
Brute Force
Credential Stuffing
Password Spraying
```

---

# Password Spraying

No password spraying, o atacante pode tentar uma senha ou poucas senhas contra muitos usuários.

Exemplo:

```text
POST /login user=admin
POST /login user=joao
POST /login user=maria
POST /login user=suporte
```

O padrão é diferente de simplesmente atacar uma única conta repetidamente.

---

# Credential Stuffing

Pode ocorrer quando credenciais vazadas são testadas contra uma aplicação.

O padrão pode envolver:

```text
Muitos usuários
Muitas tentativas
IPs variados
Mesmo endpoint
```

A correlação com autenticação e identidade é fundamental.

---

# Web Shell

Um dos eventos mais importantes em servidores comprometidos é a presença de uma Web Shell.

Um atacante pode explorar uma vulnerabilidade e conseguir enviar um arquivo executável.

Depois:

```text
POST /upload
```

e posteriormente:

```text
GET /uploads/shell.php
```

Essa sequência pode ser extremamente relevante.

---

# Upload suspeito

Observe especialmente:

```text
POST
/upload
200
```

seguido por:

```text
GET /uploads/arquivo.php
```

ou comportamento equivalente.

A confirmação deve ser feita com:

```text
File System
Process Logs
EDR
Application Logs
```

---

# Web Shell + Command Execution

Um possível fluxo:

```text
Internet
   ↓
Exploit
   ↓
Upload
   ↓
Web Shell
   ↓
Command Execution
   ↓
Reverse Shell
```

Os Web Server Logs podem mostrar apenas algumas dessas etapas.

Por isso, correlação é essencial.

---

# Bots

Nem todo bot é malicioso.

Existem:

```text
Googlebot
Bingbot
Monitors
Crawlers
SEO Tools
Security Scanners
```

Também existem:

```text
Malicious Bots
Scrapers
Credential Attack Bots
Scanners
```

O comportamento é mais importante que o simples User-Agent.

---

# Bot Detection

Indicadores:

```text
Grande quantidade de requisições
Intervalos muito pequenos
Muitos endpoints
User-Agent incomum
Ausência de comportamento típico de navegador
Muitos erros 404
Tentativas de caminhos sensíveis
```

---

# HTTP Flood

Uma aplicação pode receber uma quantidade anormal de requisições HTTP.

Exemplo:

```text
Normal:
100 req/s

Evento:
20.000 req/s
```

Isso pode estar relacionado a:

```text
DoS
DDoS
Bot
Abuso
Crawler descontrolado
```

A análise deve considerar o comportamento normal da aplicação.

---

# API Logs

Web Server Logs também podem registrar chamadas de APIs.

Exemplo:

```text
POST /api/login
POST /api/token
GET /api/users
GET /api/admin
```

Isso permite detectar:

```text
Abuso de API
Brute Force
Enumeration
Acesso indevido
```

---

# API Enumeration

Um atacante pode tentar descobrir endpoints:

```text
GET /api/v1/users
GET /api/v1/admin
GET /api/v1/debug
GET /api/v1/config
```

Uma sequência assim pode indicar reconhecimento.

---

# Host Header

O campo Host pode ser importante em ambientes com múltiplos sites.

Exemplo:

```text
Host: site1.com
Host: site2.com
Host: internal.example
```

Durante uma investigação, o SOC pode descobrir que uma requisição está tentando acessar um virtual host específico.

---

# Referer

O `Referer` pode indicar de onde veio uma requisição.

Exemplo:

```text
Referer:
https://example.com/login
```

Pode ajudar em investigações de:

```text
Phishing
Redirecionamentos
Navegação
Ataques
```

Mas também pode ser ausente ou falsificado.

---

# Tempo de resposta

Alguns logs registram o tempo necessário para processar uma requisição.

Exemplo:

```text
Request:
GET /search

Response Time:
4.8 seconds
```

Uma mudança anormal pode indicar:

```text
Banco de dados lento
Aplicação sobrecarregada
Payload causando processamento excessivo
DoS
Problema de infraestrutura
```

---

# Web Logs + Firewall

Uma correlação básica:

```text
Firewall
   ↓
203.0.113.50
   ↓
HTTPS
   ↓
Web Server
```

Depois:

```text
Web Log
GET /admin
GET /.env
GET /wp-login.php
```

Agora o comportamento fica muito mais claro.

---

# Web Logs + DNS

Exemplo:

```text
DNS
 ↓
suspicious.example
 ↓
203.0.113.50
```

Depois:

```text
Firewall
 ↓
203.0.113.50:443
```

E:

```text
Web Server
 ↓
GET /login
```

Essas três fontes podem formar uma linha do tempo.

---

# Web Logs + Linux

Em um servidor Linux:

```text
Web Log
   ↓
POST /upload
   ↓
200
```

Depois:

```text
Linux
   ↓
php-fpm
   ↓
processo suspeito
```

E:

```text
Firewall
   ↓
Outbound Connection
```

Essa combinação pode indicar comprometimento.

---

# Web Logs + Windows/IIS

Em IIS:

```text
IIS Log
   ↓
POST /upload
```

pode ser correlacionado com:

```text
Windows Event Logs
PowerShell
Process Creation
Defender
EDR
```

---

# Web Logs + SIEM

O fluxo:

```text
Nginx / Apache / IIS
          ↓
       Collector
          ↓
          SIEM
          ↓
       Parsing
          ↓
      Detection
          ↓
        Alert
          ↓
         SOC
```

permite centralizar e correlacionar eventos.

---

# Exemplo no Splunk

Pesquisar Web Logs:

```spl
index=web
```

Procurar erros:

```spl
index=web status=500
```

Identificar IPs com mais requisições:

```spl
index=web
| stats count by src_ip
| sort - count
```

Identificar endpoints mais acessados:

```spl
index=web
| stats count by uri
| sort - count
```

Encontrar muitos erros 404:

```spl
index=web status=404
| stats count by src_ip
| sort - count
```

Os nomes dos campos variam conforme o parsing.

---

# Wazuh

Uma arquitetura possível:

```text
Web Server
    ↓
Wazuh Agent
    ↓
Wazuh Manager
    ↓
Rules
    ↓
Alerts
```

O Wazuh pode analisar logs de:

```text
Apache
Nginx
IIS
Aplicações
```

e gerar alertas baseados nas regras configuradas.

---

# Microsoft Sentinel

Fluxo:

```text
Web Server
    ↓
Collector
    ↓
Log Analytics
    ↓
Microsoft Sentinel
    ↓
Analytics Rules
    ↓
Incident
```

O Sentinel pode correlacionar:

```text
Web
DNS
Firewall
Identity
Endpoint
Cloud
```

---

# Threat Hunting

Uma hipótese:

> "Existe algum IP realizando reconhecimento sistemático da aplicação?"

O analista pode procurar:

```text
IP
 ↓
404
 ↓
403
 ↓
404
 ↓
404
 ↓
200
```

Depois verificar:

```text
Quais URLs?
Qual frequência?
Qual User-Agent?
Qual origem?
Qual recurso retornou 200?
```

---

# Caso prático

Imagine:

```text
20:00
GET /admin
404

20:00
GET /.env
404

20:01
GET /.git/config
403

20:01
GET /backup.zip
200
```

O último evento muda completamente a investigação.

O SOC deve verificar:

```text
O arquivo existe?
Foi realmente entregue?
Qual tamanho?
Quem fez o download?
Existe informação sensível?
```

---

# Outro caso prático

```text
20:10
POST /login
401

20:10
POST /login
401

20:10
POST /login
401

20:11
POST /login
401

20:11
POST /login
200
```

Pode indicar:

```text
Tentativas de autenticação
      ↓
Falhas
      ↓
Sucesso
```

Agora é necessário correlacionar:

```text
IP
Usuário
Endpoint
Sessão
Eventos de autenticação
Atividade posterior
```

---

# Linha do tempo de um possível ataque

Um incidente web pode ser reconstruído assim:

```text
Reconhecimento
      ↓
Scanning
      ↓
Exploração
      ↓
Upload
      ↓
Execução
      ↓
Persistência
      ↓
C2
      ↓
Exfiltração
```

Os Web Server Logs podem fornecer evidências de várias dessas etapas.

---

# MITRE ATT&CK

Web logs podem contribuir para investigações relacionadas a técnicas como:

```text
T1190
Exploit Public-Facing Application
```

```text
T1059
Command and Scripting Interpreter
```

```text
T1505.003
Web Shell
```

```text
T1110
Brute Force
```

```text
T1046
Network Service Scanning
```

```text
T1071.001
Web Protocols
```

O mapeamento deve ser feito com base no comportamento observado.

---

# Falsos positivos

Alguns comportamentos podem parecer ataques, mas serem legítimos:

```text
Health Checks
Load Balancers
Crawlers
Pentests autorizados
Scanners de vulnerabilidade
Monitoramento
Backup
SEO Bots
CDNs
```

Por isso, o SOC precisa conhecer:

```text
Ativos autorizados
Scanners internos
Janelas de manutenção
IPs confiáveis
Aplicações publicadas
```

---

# Boas práticas

## Centralizar os logs

```text
Nginx
Apache
IIS
Application
     ↓
SIEM
```

---

## Sincronizar horário

Use NTP para manter:

```text
Firewall
DNS
Web Server
Application
SIEM
```

com horários consistentes.

---

## Registrar informações suficientes

Sempre que possível:

```text
IP
Timestamp
Method
URI
Status
User-Agent
Host
Referer
Response Time
Bytes
```

---

## Proteger os próprios logs

Logs também são ativos de segurança.

Um atacante que compromete um servidor pode tentar:

```text
Apagar logs
Alterar logs
Reduzir retenção
Desativar logging
```

Por isso, é importante enviá-los para uma infraestrutura centralizada.

---

# Checklist

```text
[ ] Access Logs habilitados

[ ] Error Logs habilitados

[ ] Timestamp registrado

[ ] Source IP registrado

[ ] HTTP Method registrado

[ ] URI registrada

[ ] HTTP Status registrado

[ ] User-Agent registrado

[ ] Host registrado

[ ] Referer registrado quando necessário

[ ] Response Time registrado

[ ] Logs centralizados

[ ] NTP configurado

[ ] Retenção definida

[ ] 404 monitorado

[ ] 401 monitorado

[ ] 403 monitorado

[ ] 500 monitorado

[ ] Brute Force monitorado

[ ] Directory Scanning monitorado

[ ] Uploads monitorados

[ ] Web Shell monitorado

[ ] Exploração monitorada

[ ] SIEM integrado

[ ] Threat Hunting configurado
```

---

# Resumo para o SOC

Web Server Logs mostram uma das partes mais importantes de uma aplicação:

> **O que clientes e sistemas estão tentando fazer com ela.**

Uma requisição:

```text
GET /index.html
200
```

é provavelmente normal.

Mas:

```text
GET /.env
404
GET /.git/config
403
GET /backup.zip
200
POST /upload
200
GET /uploads/shell.php
200
```

conta uma história completamente diferente.

O analista precisa sair da análise de eventos isolados e procurar **sequências de comportamento**.

---

# Conclusão

Um Web Server Log não é apenas um histórico de acessos.

Ele pode ser uma das principais fontes de evidência durante um incidente em uma aplicação pública.

O SOC deve analisar:

```text
Quem acessou?
Quando?
Qual endpoint?
Qual método?
Qual resposta?
Quantas vezes?
Qual User-Agent?
Qual foi o comportamento antes?
O que aconteceu depois?
```

Quando combinado com:

```text
Firewall
DNS
Endpoint
Linux/Windows
Application Logs
WAF
SIEM
```

o Web Server Log deixa de ser apenas um arquivo de texto e passa a fazer parte da investigação.

> **Uma requisição isolada pode ser ruído. Uma sequência de requisições pode contar a história de um ataque.**
