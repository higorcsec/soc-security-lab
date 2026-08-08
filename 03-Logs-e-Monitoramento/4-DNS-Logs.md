# DNS Logs

## Visão geral

O DNS normalmente é lembrado apenas como o serviço responsável por transformar:

```text
www.exemplo.com
        ↓
    203.0.113.10
```

Para um SOC, porém, o DNS é muito mais do que isso.

Cada consulta pode ajudar a responder:

```text
Quem consultou?
Qual domínio foi consultado?
Quando?
Qual servidor DNS respondeu?
Qual foi a resposta?
Com que frequência?
```

Essa visibilidade torna os logs DNS extremamente úteis para detectar:

```text
Malware
Phishing
Command & Control
DNS Tunneling
Domínios maliciosos
Botnets
Comunicações suspeitas
Máquinas comprometidas
```

Uma máquina pode não gerar um alerta evidente no endpoint, mas seu comportamento DNS pode revelar que algo está errado.

---

# O que é um DNS Log?

Um DNS log registra eventos relacionados às consultas e respostas DNS.

Um evento pode conter:

```text
Timestamp
Cliente
Servidor DNS
Domínio
Tipo de consulta
Resposta
Código de resposta
IP retornado
```

Exemplo:

```text
2026-08-07 20:15:32
Client: 192.168.10.25
Query: www.example.com
Type: A
Response: 93.184.216.34
```

Podemos interpretar:

```text
192.168.10.25
      ↓
Consulta
      ↓
www.example.com
      ↓
Tipo A
      ↓
93.184.216.34
```

---

# Por que DNS é importante para o SOC?

DNS aparece em praticamente toda infraestrutura conectada.

Um computador pode utilizar DNS para:

```text
Acessar um site
Baixar uma atualização
Conectar em uma API
Encontrar um servidor
Acessar um serviço cloud
Comunicar com infraestrutura maliciosa
```

Por isso:

> **Observar DNS é observar uma parte importante do comportamento da rede.**

---

# Fluxo de uma consulta DNS

Um fluxo simplificado:

```text
Usuário
   ↓
Computador
   ↓
DNS Resolver
   ↓
Servidor DNS
   ↓
Resposta
   ↓
Aplicação
```

Em uma empresa:

```text
Endpoint
   ↓
DNS interno
   ↓
Firewall
   ↓
Internet
```

O SOC pode monitorar esse caminho.

---

# Principais campos

## Timestamp

Indica quando a consulta aconteceu.

Exemplo:

```text
2026-08-07 20:15:32
```

O horário é fundamental para construir uma linha do tempo.

---

## Client IP

Indica quem realizou a consulta.

Exemplo:

```text
192.168.10.25
```

Durante uma investigação:

```text
Qual máquina consultou?
```

é uma das primeiras perguntas.

---

## Query

É o domínio solicitado.

Exemplo:

```text
login.example.com
```

Esse campo normalmente é o centro da investigação.

---

## Query Type

Indica o tipo de registro solicitado.

Exemplos:

```text
A
AAAA
CNAME
MX
TXT
NS
PTR
```

---

# Registro A

Consulta por um endereço IPv4.

```text
www.example.com
        ↓
A
        ↓
203.0.113.10
```

É um dos tipos mais comuns.

---

# Registro AAAA

Consulta por IPv6.

```text
www.example.com
        ↓
AAAA
        ↓
2001:db8::10
```

---

# Registro CNAME

Indica um alias para outro domínio.

Exemplo:

```text
www.example.com
        ↓
CNAME
        ↓
app.example.net
```

Durante investigações, isso pode ser importante porque o domínio inicialmente consultado pode não ser o destino final.

---

# Registro TXT

Registros TXT podem ser utilizados para diversas finalidades.

Por exemplo:

```text
SPF
DKIM
Verificações
Políticas
Configurações
```

Porém, consultas TXT também podem aparecer em técnicas abusivas, incluindo alguns casos de DNS tunneling.

O contexto é fundamental.

---

# Registro MX

Relaciona um domínio aos servidores de e-mail.

```text
example.com
     ↓
MX
     ↓
mail.example.com
```

Pode ser útil em investigações relacionadas a infraestrutura de e-mail.

---

# Registro PTR

Utilizado para resolução reversa.

```text
203.0.113.10
      ↓
PTR
      ↓
server.example.com
```

É bastante utilizado para investigação e validação de infraestrutura.

---

# Response Code

O DNS também pode informar códigos de resposta.

Alguns exemplos:

```text
NOERROR
NXDOMAIN
SERVFAIL
REFUSED
```

---

# NXDOMAIN

`NXDOMAIN` indica que o domínio consultado não existe.

Uma quantidade muito grande de NXDOMAIN pode ser interessante.

Exemplo:

```text
host1.example.com
host2.example.com
host3.example.com
host4.example.com
host5.example.com
```

Se uma máquina estiver gerando centenas ou milhares de consultas para domínios inexistentes, o SOC deve investigar.

Possibilidades:

```text
Aplicação mal configurada
Software legítimo
Erro de configuração
Malware
Domain Generation Algorithm
```

---

# Domain Generation Algorithm

Alguns malwares utilizam algoritmos capazes de gerar diversos domínios.

Exemplo:

```text
xj39dk.example
p92kda.example
q82mxa.example
k29dsa.example
z82lqp.example
```

O objetivo pode ser encontrar um domínio de Command & Control ativo.

O padrão pode gerar:

```text
Muitas consultas
Domínios aparentemente aleatórios
Alto número de NXDOMAIN
```

Esse comportamento pode ser um forte indicador de investigação.

---

# DNS e Malware

Malwares frequentemente precisam descobrir algum destino.

O fluxo pode ser:

```text
Malware
   ↓
DNS Query
   ↓
Domínio
   ↓
IP
   ↓
Conexão
```

Exemplo:

```text
workstation
     ↓
update-example.com
     ↓
198.51.100.20
     ↓
HTTPS
```

O DNS pode ser o primeiro indicador de comprometimento.

---

# Command & Control

Uma máquina comprometida pode consultar repetidamente um domínio associado à infraestrutura de comando e controle.

Exemplo:

```text
10:00 → c2.example.com
10:05 → c2.example.com
10:10 → c2.example.com
10:15 → c2.example.com
```

Esse padrão pode indicar:

```text
Beaconing
```

Mas também pode representar uma aplicação legítima.

O analista precisa verificar:

```text
Quem fez a consulta?
Qual processo?
Qual domínio?
Qual IP?
Qual frequência?
```

---

# Beaconing DNS

Um padrão periódico pode ser bastante interessante.

```text
20:00:00
20:05:00
20:10:00
20:15:00
20:20:00
```

Sempre para o mesmo domínio.

O SOC pode investigar:

```text
Periodicidade
Volume
TTL
Domínio
IP retornado
Processo responsável
```

---

# DNS Tunneling

DNS pode ser utilizado para transportar informações dentro de consultas DNS.

Um exemplo simplificado:

```text
arquivo secreto
      ↓
codificação
      ↓
dados.example.com
      ↓
DNS
      ↓
servidor controlado
```

Os dados podem aparecer como subdomínios anormalmente longos.

Exemplo:

```text
aGVsbG9fV29ybGQxMjM0.example.com
```

Um evento isolado não significa tunneling.

Mas um grande volume de consultas contendo subdomínios:

```text
longos
aleatórios
variáveis
codificados
```

pode justificar investigação.

---

# Características de DNS Tunneling

Alguns sinais:

```text
Subdomínios muito longos
Alta quantidade de consultas
Entropia elevada
Strings aparentemente aleatórias
Muitos subdomínios diferentes
Consultas periódicas
Domínio raro na organização
Grande quantidade de TXT
```

O ideal é combinar vários indicadores.

---

# Entropia

Entropia pode ser utilizada para identificar strings com aparência aleatória.

Compare:

```text
login
```

com:

```text
x8K29dLq91Pz73Nm
```

O segundo possui características mais próximas de dados codificados ou gerados automaticamente.

Entretanto:

> Alta entropia não significa automaticamente atividade maliciosa.

CDNs, tokens, aplicações e serviços legítimos também podem gerar strings semelhantes.

---

# Domínios recém-observados

Um domínio que nunca apareceu no ambiente pode merecer atenção.

Imagine:

```text
Domínios mais acessados:

google.com
microsoft.com
github.com
example.com
```

De repente:

```text
xj29kq-example.net
```

aparece em apenas uma máquina.

O SOC pode perguntar:

```text
Quem consultou?
Qual processo?
Qual usuário?
Qual IP foi retornado?
O domínio é conhecido?
```

---

# Rare Domain

Um domínio raro é aquele consultado por poucas máquinas.

Exemplo:

```text
1000 computadores
      ↓
example.com
      ↓
900 máquinas

1 computador
      ↓
random-domain.xyz
```

Isso não significa que o domínio seja malicioso.

Mas pode ser um excelente ponto de partida para Threat Hunting.

---

# Top Domains

Uma análise simples é descobrir os domínios mais consultados.

```text
google.com          500.000
microsoft.com       320.000
github.com          120.000
example.com           2.000
random.xyz               3
```

O domínio com três consultas não é necessariamente mais perigoso.

Mas ele é:

```text
Raro
```

e pode ser interessante dependendo do contexto.

---

# DNS e Phishing

DNS logs podem ajudar em investigações de phishing.

Exemplo:

```text
microsoft.com
```

versus:

```text
microsoft-login-security.example
```

O segundo pode tentar se passar por uma marca conhecida.

O SOC pode correlacionar:

```text
DNS
+
Proxy
+
Endpoint
+
E-mail
```

---

# Typosquatting

Atacantes podem registrar domínios parecidos com nomes legítimos.

Exemplo:

```text
microsoft.com
```

versus:

```text
micros0ft.com
```

ou:

```text
microssoft.com
```

O objetivo pode ser enganar usuários.

DNS logs podem revelar que uma estação acessou esse domínio.

---

# DNS Rebinding

DNS também pode aparecer em ataques mais específicos, como DNS rebinding.

O conceito envolve alterar a resolução DNS para fazer um domínio apontar para diferentes destinos.

A análise exige contexto adicional, mas os logs DNS podem ajudar a reconstruir as alterações.

---

# Fast Flux

Algumas infraestruturas maliciosas utilizam técnicas em que um domínio resolve para vários IPs que mudam rapidamente.

Exemplo:

```text
malicious.example
       ↓
IP 1
IP 2
IP 3
IP 4
IP 5
```

Com mudanças frequentes.

Esse padrão pode ser associado a:

```text
Botnets
C2
Infraestrutura resiliente
```

Novamente, CDNs e serviços legítimos também utilizam múltiplos IPs.

---

# TTL

O TTL informa por quanto tempo uma resposta pode permanecer em cache.

Durante investigações, valores de TTL podem ajudar a entender o comportamento de determinados domínios.

Por exemplo:

```text
Domínio
 ↓
IP
 ↓
TTL muito curto
 ↓
Mudanças frequentes
```

Isso pode ser interessante em alguns cenários de infraestrutura maliciosa.

Mas TTL baixo também é perfeitamente legítimo.

---

# DNS interno

Empresas normalmente possuem DNS interno.

Exemplo:

```text
Computador
    ↓
DNS Corporativo
    ↓
Servidor interno
```

Consultas internas podem revelar:

```text
Servidores
Aplicações
Domínios internos
Active Directory
Serviços
```

Por isso, DNS também pode ser importante para detectar movimentação lateral e reconhecimento interno.

---

# Active Directory

Em ambientes Windows, o DNS é extremamente importante para o funcionamento do Active Directory.

Serviços podem consultar registros relacionados a:

```text
Domain Controllers
LDAP
Kerberos
Global Catalog
Serviços
```

Um aumento inesperado de consultas internas pode merecer investigação.

---

# DNS e Reconhecimento

Um atacante dentro da rede pode tentar descobrir recursos através de DNS.

Exemplo:

```text
dc01.corp.local
fileserver.corp.local
sql01.corp.local
backup.corp.local
```

Isso pode ajudar a mapear a infraestrutura.

O SOC pode procurar:

```text
Quem está consultando?
Quantos nomes?
Em quanto tempo?
Quais hosts?
```

---

# DNS Logs + Firewall

Uma correlação muito útil:

```text
DNS
 ↓
Domínio
 ↓
IP
 ↓
Firewall
 ↓
Conexão
```

Exemplo:

```text
20:00
Host consulta:
suspicious.example

20:00
DNS responde:
203.0.113.50

20:00
Firewall:
10.10.10.20 → 203.0.113.50:443
ALLOW
```

Agora existe uma cadeia de eventos.

---

# DNS Logs + Endpoint

Imagine:

```text
DNS
 ↓
suspicious.example
```

Depois:

```text
Endpoint
 ↓
powershell.exe
 ↓
conexão externa
```

A correlação aumenta significativamente a relevância do evento.

---

# DNS Logs + Linux

Em um servidor Linux:

```text
DNS
 ↓
domínio suspeito
```

pode ser correlacionado com:

```text
auth.log
sudo
auditd
processos
firewall
```

Exemplo:

```text
SSH Login
    ↓
sudo
    ↓
Processo criado
    ↓
DNS Query
    ↓
Conexão externa
```

Essa sequência é muito mais relevante do que uma consulta DNS isolada.

---

# DNS Logs + SIEM

O fluxo normalmente fica:

```text
DNS Server
     ↓
Log Collector
     ↓
SIEM
     ↓
Parsing
     ↓
Normalization
     ↓
Correlation
     ↓
Detection
     ↓
Alert
```

O SIEM permite pesquisar milhões de eventos sem depender de consultas manuais em cada servidor.

---

# Exemplo de investigação no Splunk

Uma pesquisa conceitual:

```spl
index=dns
```

Para procurar um domínio:

```spl
index=dns query="suspicious.example"
```

Para encontrar domínios mais consultados:

```spl
index=dns
| stats count by query
| sort - count
```

Para identificar clientes que fizeram consultas:

```spl
index=dns
| stats count by src_ip
| sort - count
```

Os campos dependem da normalização realizada no ambiente.

---

# Microsoft Sentinel

Em um ambiente com Sentinel:

```text
DNS
 ↓
Log Analytics
 ↓
Microsoft Sentinel
 ↓
Analytics Rule
 ↓
Incident
```

O analista pode correlacionar DNS com:

```text
Entra ID
Defender
Firewall
Windows
Linux
Cloud
```

Isso transforma uma consulta DNS em parte de uma investigação maior.

---

# Wazuh

Em ambientes utilizando Wazuh:

```text
DNS / Syslog
      ↓
Wazuh Agent ou coleta
      ↓
Wazuh Manager
      ↓
Rules
      ↓
Alerts
```

O Wazuh pode então contribuir para detecções relacionadas ao comportamento DNS quando os logs e regras apropriados estão configurados.

---

# Threat Hunting com DNS

DNS é excelente para Threat Hunting.

Uma hipótese:

> "Existe uma máquina consultando domínios que nenhuma outra máquina consulta?"

Fluxo:

```text
Todos os DNS Logs
       ↓
Agrupar por domínio
       ↓
Identificar domínios raros
       ↓
Descobrir clientes
       ↓
Investigar endpoint
```

Outra hipótese:

> "Existe algum host realizando consultas em grande quantidade para domínios inexistentes?"

Fluxo:

```text
DNS Logs
   ↓
NXDOMAIN
   ↓
Agrupar por cliente
   ↓
Identificar anomalias
```

---

# Indicadores importantes

Durante uma investigação, fique atento a:

```text
Domínio recém-observado
Domínio raro
Muitos NXDOMAIN
Domínios aleatórios
Subdomínios muito longos
Alta entropia
Consultas periódicas
Grande quantidade de TXT
Mudança frequente de IP
TTL incomum
Domínios parecidos com marcas
Consulta direta a DNS externo
DNS fora do padrão corporativo
```

Nenhum desses indicadores, sozinho, confirma um incidente.

---

# DNS externo não autorizado

Em algumas empresas, todos os endpoints deveriam utilizar o DNS corporativo.

Imagine:

```text
Endpoint
   ↓
8.8.8.8
```

quando a política exige:

```text
Endpoint
   ↓
DNS Corporativo
```

Isso pode indicar:

```text
Configuração incorreta
Aplicação específica
VPN
Malware
Tentativa de contornar monitoramento
```

A política do ambiente precisa ser considerada.

---

# DoH e DoT

Tecnologias como:

```text
DNS over HTTPS (DoH)
DNS over TLS (DoT)
```

podem dificultar a visibilidade tradicional de DNS.

Em vez de:

```text
Endpoint
 ↓
DNS Corporativo
```

pode existir:

```text
Endpoint
 ↓
HTTPS/TLS
 ↓
DNS Provider
```

Para um SOC, isso é importante porque o conteúdo das consultas pode deixar de estar disponível nos logs tradicionais da infraestrutura corporativa.

Por isso, políticas e controles de endpoint/rede podem ser necessários.

---

# Caso prático

Imagine que um SOC receba um alerta:

```text
Host:
192.168.10.50
```

O DNS mostra:

```text
a8d92ks.example
x82kq91.example
m29xk22.example
p92la01.example
```

Todos possuem:

```text
Subdomínios longos
```

e muitos retornam:

```text
NXDOMAIN
```

O analista investiga.

Depois descobre:

```text
192.168.10.50
      ↓
powershell.exe
      ↓
HTTPS
      ↓
IP externo
```

Agora temos:

```text
DNS Anomaly
      ↓
Endpoint Activity
      ↓
Network Connection
```

A investigação ganhou contexto suficiente para aprofundar a análise.

---

# Outro caso prático

Um usuário recebe um e-mail aparentemente legítimo.

Depois:

```text
Clique
 ↓
DNS Query
 ↓
domínio parecido com uma marca
 ↓
IP externo
 ↓
HTTPS
```

O SOC pode correlacionar:

```text
E-mail
+
DNS
+
Proxy
+
Endpoint
```

e determinar se o comportamento é compatível com phishing.

---

# Linha do tempo

Uma investigação pode ficar assim:

```text
20:10
Usuário recebe e-mail

20:12
DNS Query → login-example.com

20:12
DNS Response → 203.0.113.50

20:12
Firewall → HTTPS ALLOW

20:13
Endpoint → Browser

20:14
Endpoint → Download
```

O DNS não explica sozinho o incidente.

Mas ajuda a conectar as peças.

---

# MITRE ATT&CK

Logs DNS podem contribuir para investigações relacionadas a diversas técnicas.

Exemplos:

```text
T1071.004
DNS
```

```text
T1046
Network Service Scanning
```

```text
T1568
Dynamic Resolution
```

```text
T1071.004
Application Layer Protocol: DNS
```

```text
T1048
Exfiltration Over Alternative Protocol
```

O mapeamento depende do comportamento efetivamente observado.

---

# Falsos positivos

É importante não transformar qualquer domínio estranho em incidente.

Exemplos de comportamentos legítimos:

```text
CDN
Antivírus
Atualização automática
Cloud
Telemetria
Aplicações SaaS
Publicidade
Serviços de terceiros
Sistemas de monitoramento
```

Até domínios aparentemente aleatórios podem ser legítimos.

A pergunta correta não é:

```text
"Esse domínio parece estranho?"
```

Mas:

```text
"Esse comportamento faz sentido para esse host?"
```

---

# Boas práticas

## Centralize os logs

```text
DNS Server
    ↓
Collector
    ↓
SIEM
```

---

## Sincronize horário

DNS, firewall, endpoints e SIEM devem possuir timestamps consistentes.

Isso permite construir uma linha do tempo confiável.

---

## Registre o cliente

Sempre que possível, mantenha:

```text
Client IP
Client Hostname
User
```

A capacidade de responder:

> "Qual máquina fez essa consulta?"

é fundamental.

---

## Preserve histórico

Mantenha retenção suficiente para investigar:

```text
Incidentes
Threat Hunting
Forense
Auditoria
Tendências
```

---

## Monitore DNS fora do padrão

Considere alertas para:

```text
DNS externo
Domínios raros
NXDOMAIN elevado
DNS tunneling
Domínios maliciosos
Consultas periódicas
```

---

# Checklist

```text
[ ] DNS Logs habilitados

[ ] Timestamp registrado

[ ] Client IP registrado

[ ] Query registrada

[ ] Query Type registrado

[ ] Response registrado

[ ] Response Code registrado

[ ] Logs centralizados

[ ] NTP configurado

[ ] Retenção definida

[ ] NXDOMAIN monitorado

[ ] Domínios raros monitorados

[ ] DNS Tunneling monitorado

[ ] Consultas externas monitoradas

[ ] DoH/DoT considerado

[ ] Threat Hunting configurado

[ ] DNS integrado ao SIEM

[ ] Correlação com Firewall

[ ] Correlação com Endpoint

[ ] Correlação com Proxy
```

---

# Resumo para o SOC

DNS pode parecer apenas uma infraestrutura auxiliar.

Para um SOC, ele funciona como uma espécie de **mapa de intenção de comunicação**.

Quando uma máquina consulta:

```text
dominio.com
```

ela está tentando descobrir como chegar a algum destino.

Por isso, a sequência:

```text
DNS Query
    ↓
DNS Response
    ↓
Firewall Connection
    ↓
Endpoint Process
    ↓
Network Traffic
```

pode revelar muito mais do que qualquer evento isolado.

---

# Conclusão

O valor dos DNS logs está na capacidade de transformar consultas aparentemente simples em evidências de comportamento.

Uma consulta:

```text
google.com
```

é provavelmente apenas uma consulta.

Mas:

```text
Host desconhecido
      ↓
Centenas de NXDOMAIN
      ↓
Subdomínios aleatórios
      ↓
Consultas periódicas
      ↓
IP externo
      ↓
Conexão HTTPS
```

é um padrão que merece investigação.

O analista de SOC não deve olhar apenas para o domínio.

Deve olhar para:

```text
Quem
Quando
O quê
Para onde
Com que frequência
Qual processo
Qual resposta
O que aconteceu depois
```

> **DNS não mostra apenas para onde uma máquina foi. Ele pode mostrar para onde ela estava tentando chegar.**
