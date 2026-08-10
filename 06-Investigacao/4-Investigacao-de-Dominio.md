# Investigação de Domínio

## Introdução

Domínios aparecem constantemente em investigações de segurança.

Um alerta pode apontar para:

```text
login-example.com
update-example.net
api-example.org
```

O domínio pode estar relacionado a:

```text
Phishing
Malware
C2
Exfiltração
Malvertising
Fraude
Infraestrutura comprometida
Serviço legítimo
```

O problema é que um domínio, sozinho, não conta toda a história.

Um domínio pode parecer legítimo e ser malicioso.

Também pode possuir reputação ruim e, ainda assim, aparecer em uma comunicação legítima dentro do ambiente.

Por isso, a investigação deve responder:

> **O domínio é legítimo, suspeito ou malicioso? E qual foi a relação dele com o ambiente investigado?**

---

# Domínio não é evidência isolada

Encontrar:

```text
example.com
```

em um log não significa que houve comprometimento.

O analista precisa descobrir:

```text
Quem acessou?
Quando?
Qual máquina?
Qual processo?
Qual URL?
Qual IP?
Qual resposta?
Qual volume?
```

A investigação transforma:

```text
Domínio
   ↓
Contexto
   ↓
Comportamento
   ↓
Correlação
   ↓
Classificação
```

---

# Primeira etapa: identificar o domínio

Antes de consultar reputação, validar o indicador.

Exemplo:

```text
login-microsoft-secure.com
```

Perguntas iniciais:

```text
O domínio está escrito corretamente?

É um subdomínio?

Possui caracteres incomuns?

Utiliza homógrafos?

Possui muitos níveis?

É recente?

É relacionado à organização?
```

---

# Typosquatting

Um domínio pode tentar imitar outro através de pequenas alterações.

Exemplo conceitual:

```text
microsoft.com
```

versus:

```text
micros0ft.com
```

ou:

```text
microsoft-login.com
```

A aparência visual pode enganar o usuário.

Por isso, analisar:

```text
Nome
Ortografia
TLD
Subdomínio
Estrutura
```

é importante.

---

# Homograph Attack

Atacantes também podem utilizar caracteres visualmente semelhantes.

Exemplo conceitual:

```text
empresa.com
```

versus um domínio utilizando caracteres Unicode visualmente parecidos.

O navegador pode apresentar algo que parece legítimo para o usuário.

Por isso, durante uma investigação, não confiar apenas no que aparece visualmente.

---

# Subdomínios

Um domínio pode possuir diversos subdomínios:

```text
login.example.com
api.example.com
mail.example.com
cdn.example.com
```

Durante a investigação, identificar qual hostname realmente apareceu no tráfego.

Isso é importante porque:

```text
example.com
```

e:

```text
malicious.example.com
```

podem representar aplicações completamente diferentes.

---

# FQDN

O Fully Qualified Domain Name representa o nome completo de um host.

Exemplo:

```text
api.example.com
```

Durante uma investigação, registrar o domínio completo quando possível.

Em vez de:

```text
example.com
```

preferir:

```text
api.example.com
```

quando esse for o indicador observado.

Quanto mais preciso o indicador, melhor o pivoting.

---

# TLD

O TLD é a parte final do domínio.

Exemplos:

```text
.com
.net
.org
.br
.io
```

O TLD sozinho não determina se um domínio é malicioso.

Por exemplo:

```text
.ru
```

não significa automaticamente ameaça.

Da mesma forma:

```text
.com
```

não significa automaticamente legitimidade.

O TLD é apenas uma parte do contexto.

---

# WHOIS e informações de registro

Informações de registro podem ajudar a identificar:

```text
Registrar
Data de criação
Data de atualização
Status
Nameservers
```

Exemplo:

```text
Domain:
example.com

Created:
2026-07-01

Registrar:
Example Registrar
```

Um domínio criado recentemente pode ser relevante em uma investigação.

Mas:

```text
Domínio recente
≠
Domínio malicioso
```

Serviços legítimos também são criados diariamente.

---

# Idade do domínio

A idade pode ser um indicador de contexto.

Imagine:

```text
Domínio:
example-login.net

Criado:
2 dias atrás
```

e:

```text
Foi acessado por:
20 endpoints
```

Isso merece atenção.

Mas a conclusão deve considerar:

```text
Quem acessou?
Por qual processo?
Qual URL?
Qual comportamento?
```

---

# DNS

DNS é uma das principais fontes durante uma investigação de domínio.

Fluxo:

```text
Host
 ↓
DNS Query
 ↓
Domínio
 ↓
IP
```

Exemplo:

```text
WS-023
   ↓
login-example.com
   ↓
185.x.x.x
```

Agora temos dois indicadores:

```text
Domínio
+
IP
```

Isso permite continuar o pivoting.

---

# Registros DNS

Alguns registros relevantes:

```text
A
AAAA
CNAME
MX
NS
TXT
```

Eles podem ajudar a identificar:

```text
IPv4
IPv6
Alias
Serviços de e-mail
Nameservers
Informações adicionais
```

---

# Registro A

Relaciona um domínio a um endereço IPv4.

Exemplo:

```text
example.com
      ↓
185.x.x.x
```

Isso permite relacionar:

```text
Domínio
→
IP
```

Depois podemos investigar o IP.

---

# Registro AAAA

Relaciona o domínio a um endereço IPv6.

Exemplo:

```text
example.com
      ↓
2001:db8::1
```

Em ambientes modernos, ignorar IPv6 pode deixar uma parte da infraestrutura invisível.

---

# Registro CNAME

Um domínio pode apontar para outro hostname.

Exemplo:

```text
login.example.com
       ↓
service.provider.net
```

Isso pode revelar:

```text
Cloud
CDN
SaaS
Hosting
```

Mas também pode ajudar a identificar a infraestrutura utilizada pelo domínio.

---

# Registro MX

O registro MX indica servidores responsáveis pelo recebimento de e-mails.

Exemplo:

```text
example.com
       ↓
mail.example.com
```

Durante uma investigação de phishing, isso pode ajudar a entender a infraestrutura de e-mail do domínio.

---

# Nameservers

Nameservers também podem revelar relacionamentos.

Exemplo:

```text
example.com
       ↓
ns1.provider.com
ns2.provider.com
```

Se diversos domínios suspeitos utilizam a mesma infraestrutura, isso pode gerar um novo ponto de investigação.

---

# DNS histórico

Uma investigação pode ir além do estado atual.

Perguntar:

```text
Para quais IPs esse domínio já apontou?
```

Exemplo:

```text
Domínio
 ↓
IP A
 ↓
IP B
 ↓
IP C
```

Isso pode revelar infraestrutura anterior.

Um domínio pode trocar de IP rapidamente.

---

# Fast Flux

Algumas infraestruturas maliciosas alteram frequentemente os endereços associados a um domínio.

Conceitualmente:

```text
example.com
 ↓
IP 1
 ↓
IP 2
 ↓
IP 3
 ↓
IP 4
```

Esse comportamento pode ser utilizado para dificultar bloqueios e derrubadas de infraestrutura.

Não significa automaticamente malware, mas pode ser um indicador relevante.

---

# Reputação do domínio

Serviços de Threat Intelligence podem classificar domínios como:

```text
Malicious
Suspicious
Phishing
Malware
Spam
Clean
Unknown
```

Exemplo:

```text
Domain:
example-login.net

Reputation:
Phishing
```

Isso aumenta a prioridade da investigação.

Mas ainda precisamos descobrir:

> **Esse domínio foi realmente acessado pelo ambiente?**

---

# Reputação não é conclusão

Imagine:

```text
Domain:
example.com

Reputation:
Malicious
```

Mas nos logs:

```text
Nenhum host interno acessou.
```

Temos um indicador de inteligência, não necessariamente um incidente interno.

Agora:

```text
Domain:
example.com

Reputation:
Malicious

Hosts:
WS-01
WS-02
WS-03
```

A situação muda.

---

# Domain + Endpoint

Uma das correlações mais importantes:

```text
Domínio
 ↓
Endpoint
```

Exemplo:

```text
login-example.com
       ↓
WS-023
```

Depois investigar:

```text
Usuário
Processo
URL
Horário
Arquivo
```

---

# Domain + DNS

Exemplo:

```text
WS-023
 ↓
DNS Query
 ↓
malicious-example.com
```

Agora procurar:

```text
Quem fez a consulta?
Qual processo?
Quantas vezes?
Quando?
Outros hosts consultaram?
```

---

# Domain + Firewall

Exemplo:

```text
DNS Query
 ↓
IP
 ↓
Firewall Connection
```

O firewall pode mostrar:

```text
Source
Destination
Port
Protocol
Action
Bytes
Timestamp
```

Isso confirma se a comunicação realmente ocorreu.

---

# Domain + Proxy

Se existe proxy corporativo:

```text
Host
 ↓
Proxy
 ↓
Domain
 ↓
URL
```

Pode ser possível obter:

```text
Usuário
URL completa
Método HTTP
User-Agent
Status
Bytes enviados
Bytes recebidos
```

Essa informação pode ser decisiva.

---

# Domain + EDR

O EDR pode responder:

> **Qual processo acessou esse domínio?**

Exemplo:

```text
Domain:
example.com

Host:
WS-023

Process:
WINWORD.EXE
```

Isso é diferente de:

```text
Process:
chrome.exe
```

E diferente de:

```text
Process:
powershell.exe
```

O contexto muda completamente a investigação.

---

# Domínio acessado pelo navegador

Exemplo:

```text
chrome.exe
 ↓
example.com
```

Pode ser:

```text
Navegação legítima
Phishing
Malvertising
Download
```

Precisamos investigar:

```text
URL
Usuário
Página
Download
Arquivo
Processos posteriores
```

---

# Domínio acessado por PowerShell

Agora:

```text
powershell.exe
 ↓
example.com
```

A prioridade pode aumentar.

Investigar:

```text
Command Line
Parent Process
URL
Download
Arquivo criado
Hash
Conexões posteriores
```

Especialmente se houver:

```text
PowerShell
+
Internet
+
Download
```

---

# Domain + Processo

Uma correlação importante:

```text
Domínio
+
Processo
+
Usuário
+
Host
```

Exemplo:

```text
malicious-example.com
       ↓
powershell.exe
       ↓
usuario01
       ↓
WS-023
```

Agora existe contexto suficiente para aprofundar a investigação.

---

# Domain + Download

Um padrão importante:

```text
Usuário
 ↓
Domínio
 ↓
Download
 ↓
Arquivo
```

Depois:

```text
Arquivo
 ↓
Execução
 ↓
Conexão externa
```

Essa cadeia pode indicar comprometimento.

---

# Domain + Phishing

Exemplo:

```text
E-mail
 ↓
URL
 ↓
Domínio
 ↓
Login Page
 ↓
Credenciais
```

O domínio pode ser apenas uma parte do ataque.

A investigação deve correlacionar:

```text
Mail Gateway
DNS
Proxy
Endpoint
Identity
```

---

# Domain + Malware

Um malware pode utilizar um domínio para:

```text
C2
Download
Atualização
Exfiltração
Redirect
```

Exemplo:

```text
Processo desconhecido
       ↓
DNS Query
       ↓
malicious-domain.com
       ↓
Connection
```

Essa sequência merece investigação.

---

# Domain + C2

Possíveis sinais:

```text
Conexões periódicas
Domínio recém-criado
Processo desconhecido
Pouco tráfego
Comunicação persistente
```

Exemplo:

```text
10:00 → domain.com
10:05 → domain.com
10:10 → domain.com
10:15 → domain.com
```

Esse padrão pode ser compatível com beaconing.

Mas é necessário comparar com o comportamento esperado da aplicação.

---

# Beaconing

Beaconing é quando um sistema realiza comunicações periódicas com um destino.

Exemplo:

```text
Host
 ↓
10:00
 ↓
10:05
 ↓
10:10
 ↓
10:15
```

Se:

```text
Destino desconhecido
+
Processo desconhecido
+
Intervalo consistente
```

a investigação ganha prioridade.

---

# Domain + Exfiltração

Um domínio pode aparecer em um fluxo de saída:

```text
Servidor
 ↓
Domínio externo
 ↓
Upload
 ↓
Grande volume
```

Investigar:

```text
Usuário
Processo
Destino
Bytes
Arquivos
Horário
```

Nem todo upload grande é exfiltração.

Pode ser:

```text
Backup
Cloud Sync
Upload legítimo
Atualização
```

O contexto novamente decide.

---

# Domínio e URL

É importante diferenciar:

```text
Domínio:
example.com
```

de:

```text
URL:
https://example.com/login/reset?token=123
```

A URL pode revelar:

```text
Endpoint
Path
Parâmetros
Token
Arquivo
Recurso
```

Durante a investigação, preservar a URL observada pode ser importante.

---

# URL suspeita

Exemplo:

```text
https://example.com/login
```

parece normal.

Agora:

```text
https://example.com/login.php?redirect=...
```

ou:

```text
https://example.com/download/file.exe
```

podem exigir análise adicional.

O path pode fornecer informações que o domínio sozinho não mostra.

---

# Domínio e Redirect

Um domínio pode redirecionar para outro.

Exemplo:

```text
site-a.com
 ↓
site-b.net
 ↓
site-c.ru
```

Durante phishing ou malvertising, isso pode ser especialmente relevante.

Investigar a cadeia:

```text
Origem
 ↓
Redirect
 ↓
Destino
```

---

# Cadeia de infraestrutura

Um domínio pode revelar:

```text
Domínio
 ↓
IP
 ↓
ASN
 ↓
Provider
 ↓
Outros domínios
```

Isso permite descobrir infraestrutura relacionada.

Exemplo:

```text
malicious-example.com
       ↓
185.x.x.x
       ↓
AS12345
       ↓
Hosting Provider
       ↓
outros indicadores
```

---

# Pivoting por domínio

Encontrou:

```text
malicious-example.com
```

Pesquisar:

```text
IP
Subdomínios
DNS
Certificados
Nameservers
URLs
Hosts internos
Usuários
Processos
```

O objetivo é expandir o indicador.

```text
Domínio
 ↓
IP
 ↓
Host
 ↓
Usuário
 ↓
Processo
 ↓
Arquivo
 ↓
Hash
```

---

# Pivoting reverso

Também podemos começar pelo endpoint.

```text
WS-023
 ↓
Domínios acessados
```

Resultado:

```text
google.com
microsoft.com
example.com
unknown-domain.net
```

Agora perguntar:

```text
Qual domínio foge do padrão?
```

Essa abordagem é útil para caça a ameaças.

---

# Domínios recém-criados

Um domínio recém-criado pode ser relevante em:

```text
Phishing
Malware
C2
Fraude
```

Exemplo:

```text
Created:
08/08/2026

First Seen:
09/08/2026
```

Isso merece atenção, principalmente se combinado com:

```text
Reputação ruim
Endpoint comprometido
Download
Credenciais
```

---

# DGA

Alguns malwares podem gerar domínios automaticamente.

Exemplo:

```text
xk29as8d.com
p92kdl2a.net
q7w81zmx.org
```

Características possíveis:

```text
Nomes aparentemente aleatórios
Grande quantidade de domínios
Consultas frequentes
Baixa popularidade
```

Isso pode estar relacionado a Domain Generation Algorithms.

Mas domínios aleatórios também podem existir em serviços legítimos.

---

# NXDOMAIN

Consultas DNS que não retornam um domínio válido podem ser relevantes.

Exemplo:

```text
abc123.example.com
→ NXDOMAIN

xyz456.example.com
→ NXDOMAIN

k92abc.example.com
→ NXDOMAIN
```

Uma quantidade anormal de consultas inexistentes pode justificar investigação.

Especialmente quando associada a:

```text
Endpoint desconhecido
Processo suspeito
Padrão automatizado
```

---

# DNS Tunneling

DNS também pode ser abusado para transportar informações.

Possíveis sinais:

```text
Subdomínios muito longos
Alta frequência
Strings aparentemente aleatórias
Grande quantidade de consultas
```

Exemplo:

```text
ajd83k29s8d92.example.com
92jd82jd92jd.example.com
```

Nenhum indicador isolado confirma tunneling.

A análise deve considerar:

```text
Volume
Entropia
Frequência
Host
Processo
Domínio
```

---

# Domínio legítimo comprometido

Nem todo ataque utiliza infraestrutura criada pelo atacante.

Um domínio legítimo pode ser:

```text
Comprometido
Redirecionado
Abusado
Hospedando conteúdo malicioso
```

Por isso:

```text
Domínio conhecido
≠
Atividade segura
```

É necessário investigar a atividade observada.

---

# Serviços legítimos abusados

Atacantes também podem utilizar serviços legítimos:

```text
Cloud Storage
CDN
Git
Paste Services
File Sharing
URL Shorteners
```

Isso dificulta a detecção baseada apenas em reputação.

O processo iniciado pelo endpoint e o comportamento da conexão ganham ainda mais importância.

---

# Short URLs

Links encurtados podem esconder o destino real.

Exemplo:

```text
short.example/abc123
```

Durante a investigação:

```text
Short URL
 ↓
Redirect
 ↓
Final Domain
```

O domínio final deve ser registrado.

---

# Certificados TLS

Certificados podem fornecer pistas sobre a infraestrutura.

Investigar:

```text
Common Name
SAN
Issuer
Validity
```

Exemplo:

```text
Domain:
login-example.com

SAN:
login-example.com
www.login-example.com
```

Isso pode ajudar a descobrir outros hosts relacionados.

---

# Certificado compartilhado

O mesmo certificado pode aparecer associado a múltiplos hostnames.

Isso pode gerar novos pivots:

```text
Certificado
 ↓
Domínios
 ↓
IPs
```

Mas infraestrutura compartilhada pode produzir muitos falsos positivos.

Sempre validar o relacionamento.

---

# Investigação no SIEM

Exemplo de pesquisa:

```text
domain = "example.com"
```

Depois expandir:

```text
domain
+
hostname
+
user
+
process
+
timestamp
```

Depois:

```text
domain
+
other hosts
```

O objetivo é descobrir o alcance.

---

# Exemplo prático

## Alerta

```text
Suspicious Domain Access

Domain:
login-example.net
```

### Passo 1 — Registro

```text
Created:
3 dias atrás
```

### Passo 2 — DNS

```text
A:
185.x.x.x
```

### Passo 3 — Reputação

```text
Phishing
```

### Passo 4 — SIEM

```text
Hosts:
WS-021
WS-023
WS-045
```

### Passo 5 — Proxy

```text
URL:
/login

User-Agent:
Chrome
```

### Passo 6 — Endpoint

```text
WS-023
 ↓
Chrome
 ↓
Download
```

### Passo 7 — Arquivo

```text
document.zip
```

Depois:

```text
document.zip
 ↓
arquivo.js
 ↓
wscript.exe
```

Agora a investigação deixa de ser apenas:

```text
Domínio suspeito
```

e passa a ser:

```text
Phishing
 ↓
Acesso ao domínio
 ↓
Download
 ↓
Execução
```

---

# Exemplo de investigação de C2

Alerta:

```text
Suspicious Outbound Connection
```

Domínio:

```text
update-example.net
```

DNS:

```text
Host:
WS-032
```

EDR:

```text
Process:
unknown.exe
```

Firewall:

```text
Connections:
a cada 5 minutos
```

Timeline:

```text
09:00
DNS

09:00
Connection

09:05
DNS

09:05
Connection

09:10
DNS

09:10
Connection
```

Esse padrão merece investigação de possível:

```text
Command and Control
```

---

# Exemplo de falso positivo

Alerta:

```text
Suspicious Domain
```

Domínio:

```text
cdn-example.com
```

Reputação:

```text
Suspicious
```

Investigação:

```text
Process:
chrome.exe

User:
Marketing

Destination:
CDN legítima

Activity:
Website autorizado
```

Resultado:

```text
Benigno
```

O indicador tinha reputação suspeita, mas o contexto interno mostrou atividade legítima.

---

# Exemplo de domínio comprometido

Domínio:

```text
legitimate-example.com
```

Reputação:

```text
Clean
```

Mas:

```text
Endpoint:
WS-023

URL:
/uploads/file.exe

Process:
powershell.exe

Download:
file.exe
```

Aqui:

```text
Clean Reputation
```

não encerra a investigação.

O comportamento é o indicador mais importante.

---

# Classificação

Depois da investigação, o domínio pode ser classificado como:

```text
Benigno
```

```text
Suspeito
```

```text
Malicioso
```

```text
Desconhecido
```

Exemplo:

```text
Domínio:
example.net

Classificação:
Suspicious

Motivos:
- recém-criado
- acessado por endpoint comprometido
- processo desconhecido
- conexão periódica
```

---

# Evidências

Uma boa investigação deve registrar:

```text
Domínio
Timestamp
Host
Usuário
Processo
IP
DNS
URL
Reputação
Comportamento
Outros IOCs
```

Exemplo:

```text
Domain:
example.net

First Seen:
09/08/2026 10:20

Host:
WS-023

User:
usuario01

Process:
powershell.exe

Resolved IP:
185.x.x.x

Activity:
Download

Classification:
Malicious
```

---

# Quando bloquear um domínio

Bloqueio pode ser uma ação de contenção.

Mas antes:

```text
Validar domínio
Confirmar atividade
Avaliar impacto
```

Principalmente quando o domínio pertence a:

```text
Cloud
CDN
SaaS
Hosting compartilhado
Serviços utilizados pela empresa
```

Um bloqueio mal planejado pode interromper aplicações legítimas.

---

# Bloqueio no DNS

Uma organização pode utilizar:

```text
DNS Filtering
Secure DNS
Firewall
Proxy
```

para impedir resolução ou acesso.

Exemplo conceitual:

```text
Endpoint
 ↓
DNS
 ↓
Blocked Domain
 ↓
Connection prevented
```

Depois do bloqueio:

```text
Monitorar novos domínios
```

Atacantes podem trocar de infraestrutura.

---

# Bloqueio não substitui investigação

Bloquear:

```text
malicious-domain.com
```

pode interromper a comunicação.

Mas não responde:

```text
Como o domínio foi acessado?
Qual usuário?
Qual máquina?
Qual processo?
O que foi baixado?
Houve comprometimento?
```

A contenção resolve parte do problema.

A investigação explica o incidente.

---

# Checklist de Investigação de Domínio

```text
[ ] Identifiquei o domínio completo?

[ ] Identifiquei subdomínio?

[ ] Verifiquei possíveis typosquatting?

[ ] Analisei caracteres suspeitos?

[ ] Verifiquei TLD?

[ ] Consultei informações de registro?

[ ] Verifiquei idade do domínio?

[ ] Analisei DNS?

[ ] Verifiquei registros A?

[ ] Verifiquei AAAA?

[ ] Verifiquei CNAME?

[ ] Verifiquei MX?

[ ] Verifiquei Nameservers?

[ ] Analisei histórico DNS?

[ ] Identifiquei IPs relacionados?

[ ] Consultei reputação?

[ ] Verifiquei URLs?

[ ] Analisei redirects?

[ ] Verifiquei certificados?

[ ] Identifiquei outros domínios relacionados?

[ ] Pesquisei o domínio no SIEM?

[ ] Identifiquei hosts internos?

[ ] Identifiquei usuários?

[ ] Identifiquei processos?

[ ] Correlacionei com DNS?

[ ] Correlacionei com Firewall?

[ ] Correlacionei com Proxy?

[ ] Correlacionei com EDR?

[ ] Procurei downloads?

[ ] Procurei conexões periódicas?

[ ] Procurei outros IOCs?

[ ] Avaliei impacto?

[ ] Registrei evidências?

[ ] Precisa de bloqueio?

[ ] Precisa de escalonamento?
```

---

# Fluxo de Investigação

```text
                       DOMÍNIO
                          │
                          ▼
                 Identificar domínio
                          │
                          ▼
              ┌───────────┴───────────┐
              │                       │
              ▼                       ▼
           DNS/WHOIS              Reputação
              │                       │
              └───────────┬───────────┘
                          ▼
                     Infraestrutura
                          │
                    ┌─────┴─────┐
                    ▼           ▼
                   IP        Certificado
                    │
                    ▼
                 SIEM
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
         DNS      Proxy      EDR
          │         │         │
          └─────────┼─────────┘
                    ▼
                 Contexto
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
             ┌──────┴──────┐
             ▼             ▼
          Benigno       Suspeito
             │             │
             ▼             ▼
          Encerrar    Conter/Escalar
```

---

# Conclusão

Investigar um domínio não significa simplesmente consultar uma ferramenta de reputação.

O verdadeiro trabalho começa quando o indicador é relacionado ao ambiente.

```text
Domínio
+
DNS
+
IP
+
Host
+
Usuário
+
Processo
+
URL
+
Comportamento
=
Contexto
```

Um domínio recém-criado pode ser legítimo.

Um domínio antigo pode ser comprometido.

Um domínio com reputação ruim pode nunca ter sido acessado pela organização.

Um domínio com reputação limpa pode estar sendo utilizado em um ataque.

Por isso, a investigação deve partir de evidências.

No SOC, a pergunta não é simplesmente:

> **"Esse domínio é malicioso?"**

A pergunta mais importante é:

> **"O que esse domínio está fazendo dentro do meu ambiente?"**

Essa mudança de perspectiva transforma uma consulta de reputação em uma investigação de segurança.
