# Análise de Indicadores

## Introdução

Um indicador de segurança raramente conta uma história completa.

Um hash pode apontar para um arquivo.

Um IP pode apontar para uma infraestrutura.

Um domínio pode apontar para um servidor.

Um evento pode apontar para um comportamento.

Mas o verdadeiro valor aparece quando esses elementos são relacionados.

```text
IOC
 ↓
Contexto
 ↓
Correlação
 ↓
Evidência
 ↓
Hipótese
 ↓
Investigação
```

A função do analista não é apenas encontrar indicadores.

É entender **o que eles representam, onde aparecem, qual relação possuem entre si e se existe atividade maliciosa dentro do ambiente**.

---

# O que é um indicador?

Um indicador é um artefato ou característica que pode ajudar a identificar uma atividade, sistema, arquivo ou comportamento.

Alguns exemplos:

```text
IP
Domínio
URL
Hash
E-mail
Hostname
Arquivo
Processo
User-Agent
Certificado
Registro DNS
Evento de autenticação
```

Esses indicadores podem ser classificados como:

```text
IOC
IOA
Indicador contextual
Indicador comportamental
```

---

# IOC

IOC significa:

> **Indicator of Compromise**

É uma evidência ou artefato associado a um possível comprometimento.

Exemplos:

```text
Hash de malware
IP de C2
Domínio utilizado em phishing
Arquivo suspeito
Registro persistente
Credencial comprometida
```

Um IOC normalmente responde:

> **O que foi encontrado?**

---

# IOA

IOA significa:

> **Indicator of Attack**

Está relacionado ao comportamento observado durante uma tentativa ou execução de ataque.

Exemplos:

```text
Múltiplas tentativas de login
Execução suspeita de PowerShell
Criação de usuário inesperada
Modificação de registro
Scanning
Movimentação lateral
Tentativa de exploração
```

Um IOA ajuda a responder:

> **O que o atacante está tentando fazer?**

---

# IOC x IOA

Exemplo:

```text
IOC:
185.x.x.x
```

Pode representar uma infraestrutura maliciosa.

Agora:

```text
IOA:
Host realizou conexões periódicas para o IP.
```

O segundo indicador fornece comportamento.

Quando combinados:

```text
IP suspeito
+
Conexões periódicas
+
Processo desconhecido
```

a investigação ganha contexto.

---

# Indicador não é prova

Esse é um dos conceitos mais importantes em uma investigação.

Encontrar:

```text
example.com
```

não significa automaticamente:

```text
Comprometimento
```

Encontrar:

```text
powershell.exe
```

também não.

E encontrar:

```text
185.x.x.x
```

menos ainda.

A pergunta correta é:

> **O indicador está associado a uma atividade anômala ou maliciosa no contexto observado?**

---

# Indicadores isolados

Exemplo:

```text
IP suspeito
```

Informação limitada.

Agora:

```text
IP suspeito
+
Host interno
+
PowerShell
+
Download
```

Já temos uma cadeia de eventos.

Quanto mais evidências independentes apontarem para a mesma hipótese, maior a confiança da investigação.

---

# Indicadores relacionados

Imagine:

```text
Domínio
 ↓
IP
 ↓
Arquivo
 ↓
Hash
 ↓
Processo
 ↓
Host
```

Esses elementos podem representar uma única cadeia de ataque.

Exemplo:

```text
phishing-domain.com
       ↓
185.x.x.x
       ↓
arquivo.zip
       ↓
malware.exe
       ↓
SHA256
       ↓
WS-023
```

Agora a investigação possui uma história.

---

# Tipos de indicadores

## Indicadores de rede

```text
IP
Domínio
URL
Porta
ASN
DNS
Certificado
```

## Indicadores de endpoint

```text
Hash
Arquivo
Processo
Command Line
Registry Key
Scheduled Task
Service
```

## Indicadores de identidade

```text
Usuário
Login
IP de origem
MFA
Sessão
Privilégio
```

## Indicadores de e-mail

```text
Sender
Recipient
Subject
URL
Attachment
Message-ID
Domain
```

---

# Hash

Hashes são muito utilizados para identificar arquivos.

Exemplo:

```text
SHA256:
a8f9...1234
```

Podem ajudar a verificar:

```text
Malware conhecido
Arquivo suspeito
Duplicidade
Integridade
```

Mas existe uma limitação importante:

> **O hash identifica o arquivo, não necessariamente a intenção ou o impacto.**

---

# MD5, SHA-1 e SHA-256

Durante investigações podem aparecer:

```text
MD5
SHA-1
SHA-256
```

Para identificação e Threat Intelligence, SHA-256 geralmente oferece uma representação mais robusta.

Mesmo assim, o importante é registrar exatamente qual algoritmo foi utilizado.

Exemplo:

```text
Hash Type:
SHA-256

Hash:
abc123...
```

---

# IP

Um IP pode representar:

```text
Origem
Destino
Servidor
C2
Proxy
VPN
NAT
Cloud
CDN
```

Nunca assumir automaticamente que:

```text
IP suspeito = atacante
```

O IP precisa ser relacionado a:

```text
Timestamp
Host
Porta
Processo
Usuário
Comportamento
```

---

# Domínio

Domínios podem estar relacionados a:

```text
Phishing
C2
Malware
Redirecionamento
Exfiltração
Serviços legítimos
```

Investigar:

```text
Criação
DNS
IP
Certificado
Reputação
URLs
Hosts internos
```

---

# URL

Uma URL fornece mais informação que um domínio.

Exemplo:

```text
https://example.com/login/reset
```

Podemos analisar:

```text
Scheme:
HTTPS

Domain:
example.com

Path:
/login/reset
```

O path pode revelar:

```text
Login
Download
Administração
Exploração
Payload
```

---

# E-mail

Em phishing, o e-mail pode fornecer diversos indicadores:

```text
Sender
Reply-To
Return-Path
Domain
IP
URL
Attachment
Hash
Message-ID
```

Um único e-mail pode gerar vários pivots.

Exemplo:

```text
E-mail
 ↓
URL
 ↓
Domínio
 ↓
IP
 ↓
Arquivo
 ↓
Hash
```

---

# Arquivo

Um arquivo suspeito pode fornecer:

```text
Nome
Extensão
Tamanho
Hash
Path
Criador
Timestamp
Assinatura
Processo
```

Exemplo:

```text
invoice.pdf.exe
```

O nome já pode gerar uma hipótese.

Mas ainda precisamos confirmar:

```text
Foi executado?
Quem executou?
Quando?
Em qual host?
```

---

# Processo

Processos são importantes para entender o comportamento do endpoint.

Exemplo:

```text
WINWORD.EXE
   ↓
powershell.exe
   ↓
unknown.exe
```

Essa cadeia pode ser relevante.

O processo pai e a linha de comando podem fornecer ainda mais contexto.

---

# Command Line

A linha de comando pode revelar intenção.

Exemplo:

```text
powershell.exe -enc ...
```

ou:

```text
cmd.exe /c ...
```

O binário sozinho pode não ser suficiente.

A combinação:

```text
Process
+
Parent Process
+
Command Line
+
User
```

é muito mais útil.

---

# Usuário

O usuário pode ajudar a determinar:

```text
Quem estava utilizando o equipamento?
```

Mas:

```text
Usuário associado
≠
Usuário responsável pelo ataque
```

Uma conta pode estar:

```text
Comprometida
Compartilhada
Automatizada
Utilizada por serviço
```

Por isso, correlacionar com autenticação e endpoint.

---

# Timestamp

Tempo é um dos indicadores mais importantes.

Um evento isolado:

```text
10:30
```

é limitado.

Uma sequência:

```text
10:28
E-mail recebido

10:30
URL acessada

10:31
Arquivo baixado

10:32
PowerShell executado

10:33
Conexão externa
```

conta uma história.

---

# Timeline

Uma timeline ajuda a visualizar a sequência.

```text
10:28 ─ E-mail recebido
   ↓
10:30 ─ URL acessada
   ↓
10:31 ─ Download
   ↓
10:32 ─ Execução
   ↓
10:33 ─ Conexão externa
   ↓
10:35 ─ Persistência
```

Esse tipo de análise é fundamental para determinar:

```text
Initial Access
Execution
Persistence
Command and Control
Impact
```

---

# First Seen

Registrar quando um indicador apareceu pela primeira vez.

Exemplo:

```text
Domain:
example.net

First Seen:
09/08/2026 10:20
```

Isso ajuda a responder:

> **Quando esse indicador entrou no ambiente?**

---

# Last Seen

Também registrar a última ocorrência.

```text
Last Seen:
09/08/2026 14:45
```

A diferença entre:

```text
First Seen
+
Last Seen
```

ajuda a entender a duração da atividade.

---

# Frequência

Um indicador observado uma vez é diferente de um indicador observado milhares de vezes.

Exemplo:

```text
1 ocorrência
```

versus:

```text
12.000 ocorrências
```

A frequência pode ajudar a identificar:

```text
Scanning
Beaconing
Brute Force
Automação
Exfiltração
```

---

# Prevalência

Prevalência significa entender quantos ativos apresentam determinado indicador.

Exemplo:

```text
IOC:
abc123

Hosts:
1
```

versus:

```text
IOC:
abc123

Hosts:
250
```

O segundo cenário exige outra abordagem.

Pode ser:

```text
Software legítimo
Atualização
Biblioteca comum
ou
Malware distribuído
```

---

# Raridade

Um indicador raro pode ser mais interessante.

Exemplo:

```text
Domínio:
microsoft.com

Prevalência:
Alta
```

versus:

```text
Domínio:
x7a9-example.net

Prevalência:
1 host
```

O segundo pode merecer maior atenção.

Mas:

```text
Raro ≠ Malicioso
```

Raridade é um fator de priorização.

---

# Contexto

O contexto é o que transforma um indicador em informação útil.

Exemplo:

```text
PowerShell
```

pode ser:

```text
Administração
Automação
Software legítimo
Ataque
```

Agora:

```text
PowerShell
+
Office
+
Download
+
Domínio suspeito
```

é muito mais relevante.

---

# Contexto do ativo

Um servidor web pode normalmente realizar:

```text
Conexões externas
```

Uma estação administrativa pode apresentar:

```text
PowerShell
```

Um servidor de banco de dados pode:

```text
Realizar conexões específicas
```

Por isso, o mesmo indicador pode ter diferentes níveis de risco dependendo do ativo.

---

# Baseline

Baseline representa o comportamento esperado.

Exemplo:

```text
Servidor:
web01

Normal:
80/443
```

Um novo comportamento:

```text
web01
 ↓
Outbound connection
 ↓
3389
```

pode ser relevante.

Sem baseline:

```text
Normal?
Anormal?
```

Com baseline:

```text
Desvio identificado.
```

---

# Correlação

Correlação significa relacionar eventos diferentes.

Exemplo:

```text
Firewall
+
DNS
+
EDR
+
Authentication
```

Imagine:

```text
DNS:
malicious.com

Firewall:
Connection Allowed

EDR:
powershell.exe

Authentication:
user01
```

Agora temos quatro fontes apontando para o mesmo evento.

---

# Correlação temporal

Eventos próximos no tempo podem indicar relação.

Exemplo:

```text
10:00
Login

10:01
PowerShell

10:02
DNS Query

10:02
Connection

10:03
File Creation
```

A sequência pode indicar uma cadeia de execução.

---

# Correlação por host

Pesquisar todos os indicadores associados a um host.

```text
WS-023
 │
 ├── IP
 ├── Domain
 ├── Process
 ├── File
 ├── User
 └── Authentication
```

Isso permite determinar se existe um padrão.

---

# Correlação por usuário

Também podemos partir do usuário:

```text
user01
 │
 ├── Login
 ├── Host
 ├── IP
 ├── Process
 └── Domain
```

Isso pode ajudar a identificar:

```text
Comprometimento de conta
Movimentação lateral
Uso anômalo
```

---

# Correlação por indicador

Começando pelo IOC:

```text
185.x.x.x
 │
 ├── WS-01
 ├── WS-03
 ├── WS-07
 ├── domain-a.com
 └── domain-b.net
```

Isso pode revelar o alcance da infraestrutura.

---

# Pivoting

Pivoting é uma das principais técnicas de investigação.

Começamos com:

```text
IP
```

e buscamos:

```text
Domínios
```

Depois:

```text
Hosts
```

Depois:

```text
Processos
```

Depois:

```text
Arquivos
```

Depois:

```text
Hashes
```

Exemplo:

```text
IP
 ↓
Domain
 ↓
URL
 ↓
File
 ↓
Hash
 ↓
Process
 ↓
Host
 ↓
User
```

Cada pivot pode gerar novos indicadores.

---

# Grafo de relacionamento

Uma forma eficiente de visualizar a investigação:

```text
                  IP
                   │
                   │
                Domain
                   │
                ┌──┴──┐
                │     │
              URL    Host
                │     │
              File   User
                │
              Hash
                │
             Process
```

Isso permite enxergar relacionamentos que seriam difíceis de perceber analisando eventos isolados.

---

# Indicador positivo

Exemplo:

```text
Hash:
abc123

Found:
WS-023
```

Isso significa que o indicador foi encontrado no ambiente.

Agora investigar:

```text
Quando?
Onde?
Como chegou?
Foi executado?
```

---

# Indicador negativo

Também é importante registrar quando não há ocorrência.

Exemplo:

```text
IOC:
abc123

SIEM:
No matches

EDR:
No matches
```

Isso não prova que o IOC nunca esteve no ambiente.

Apenas significa:

> **Não foram encontradas evidências nas fontes consultadas e no período analisado.**

---

# Período da busca

Toda investigação deve definir um intervalo.

Exemplo:

```text
Start:
08/08/2026 00:00

End:
09/08/2026 23:59
```

Uma busca sem período pode gerar:

```text
Muito ruído
Eventos antigos
Falsos positivos
```

---

# Fontes de evidência

Quanto mais fontes independentes, melhor.

Exemplo:

```text
SIEM
EDR
Firewall
DNS
Proxy
Mail Gateway
Authentication
Web Server
Cloud Logs
```

Cada fonte responde perguntas diferentes.

---

# Confiabilidade da fonte

Nem toda fonte possui o mesmo nível de confiabilidade.

Exemplo:

```text
Threat Intelligence:
Malicious
```

é diferente de:

```text
EDR:
Malicious Process Executed
```

E diferente de:

```text
Firewall:
Connection Attempt
```

A força da evidência depende do contexto.

---

# Threat Intelligence

Threat Intelligence pode fornecer:

```text
Reputação
Relacionamentos
Campanhas
Hashes
IPs
Domínios
TTPs
```

Mas deve ser utilizada para:

```text
Enriquecer
Priorizar
Correlacionar
```

e não simplesmente:

```text
Classificar automaticamente
```

---

# Indicadores de alta confiança

Exemplos:

```text
Hash confirmado de malware
IOC confirmado por múltiplas fontes
Domínio utilizado em campanha conhecida
Arquivo identificado por EDR
```

Mesmo nesses casos, investigar o contexto interno.

---

# Indicadores de baixa confiança

Exemplos:

```text
IP desconhecido
Domínio recém-criado
User-Agent incomum
Processo raro
TLD incomum
```

Esses indicadores podem gerar:

```text
Hipótese
```

mas não necessariamente:

```text
Incidente confirmado
```

---

# Score de confiança

Uma investigação pode utilizar uma classificação simples:

```text
Baixa
Média
Alta
Crítica
```

Exemplo:

```text
IOC:
IP suspeito

Reputação:
Malicious

Connection:
Allowed

Process:
Unknown

Confidence:
High
```

O objetivo não é criar um número mágico.

É comunicar claramente o nível de evidência disponível.

---

# Indicadores duplicados

O mesmo IOC pode aparecer em vários alertas.

Exemplo:

```text
185.x.x.x
```

aparece:

```text
Alert 001
Alert 017
Alert 023
```

Isso não significa necessariamente três incidentes.

Pode ser:

```text
Um mesmo indicador
+
Múltiplos eventos
```

Por isso, correlacionar antes de abrir novos casos.

---

# Deduplicação

Antes de criar uma nova investigação:

```text
IOC
Timestamp
Host
Usuário
Evento
```

devem ser comparados com casos existentes.

Isso reduz:

```text
Alert Fatigue
Duplicidade
Trabalho desnecessário
```

---

# Indicadores em múltiplos hosts

Se o mesmo IOC aparece em:

```text
WS-01
WS-02
WS-03
WS-04
```

a investigação deve procurar:

```text
Origem comum
Usuário comum
E-mail comum
Software comum
Campanha
```

Isso pode indicar:

```text
Campanha de phishing
Malware distribuído
Ataque coordenado
```

---

# Escopo

Uma das principais perguntas da investigação:

> **Quantos ativos foram afetados?**

Exemplo:

```text
IOC:
malicious.com

Hosts:
23

Users:
8

Departments:
3
```

Agora é possível estimar o escopo.

---

# Impacto

Depois de descobrir o escopo:

```text
O que aconteceu?
```

Investigar:

```text
Credenciais expostas?
Dados acessados?
Arquivos executados?
Privilégios elevados?
Movimentação lateral?
Exfiltração?
Persistência?
```

Um IOC pode estar presente sem causar impacto.

---

# Contenção

Se a investigação confirmar atividade maliciosa:

```text
Bloquear IOC
Isolar host
Revogar sessão
Resetar credencial
Remover persistência
```

A ação depende do incidente.

O indicador serve como ponto de apoio para a contenção.

---

# Indicadores derivados

Uma investigação pode produzir novos IOCs.

Exemplo:

```text
Domínio suspeito
 ↓
IP
 ↓
Arquivo
 ↓
Hash
```

O hash é um novo IOC.

Depois:

```text
Hash
 ↓
Outros hosts
```

Isso amplia o escopo.

---

# Indicadores relacionados

Exemplo:

```text
IOC-001:
Domain

IOC-002:
IP

IOC-003:
Hash

IOC-004:
File

IOC-005:
Process
```

Todos podem estar relacionados ao mesmo incidente.

Registrar esses relacionamentos facilita:

```text
Detecção
Threat Hunting
Resposta
Documentação
```

---

# Threat Hunting

Depois de identificar um indicador:

```text
IOC:
malicious-domain.com
```

não pesquisar apenas:

```text
"Foi encontrado?"
```

Também procurar:

```text
Outros domínios
Outros IPs
Outros hashes
Outros hosts
Padrões semelhantes
```

Isso transforma a investigação em Threat Hunting.

---

# Exemplo de Threat Hunting

Foi identificado:

```text
powershell.exe
```

O analista pode pesquisar:

```text
PowerShell
+
Internet
+
Download
```

Depois:

```text
PowerShell
+
Encoded Command
```

Depois:

```text
PowerShell
+
Suspicious Domain
```

A investigação deixa de depender de um IOC específico.

---

# Indicadores e MITRE ATT&CK

Indicadores também podem ser relacionados a técnicas.

Exemplo:

```text
PowerShell
```

pode estar relacionado a:

```text
T1059.001
PowerShell
```

Scanning:

```text
Network Service Scanning
```

Brute Force:

```text
Valid Accounts
Brute Force
```

Essa relação ajuda a entender:

```text
O que o atacante está fazendo?
```

e não apenas:

```text
Qual indicador ele utilizou?
```

---

# Indicador técnico x comportamento

Imagine:

```text
IOC:
185.x.x.x
```

Esse indicador pode desaparecer.

O atacante pode trocar:

```text
IP
Domínio
Hash
```

Mas o comportamento pode continuar:

```text
Beaconing
PowerShell
Credential Dumping
Scanning
Exfiltration
```

Por isso, uma boa detecção combina:

```text
IOC
+
IOA
+
Contexto
```

---

# Exemplo completo

## Indicador inicial

```text
Domain:
login-example.net
```

### Pivot 1

```text
DNS
 ↓
185.x.x.x
```

### Pivot 2

```text
Proxy
 ↓
WS-023
```

### Pivot 3

```text
EDR
 ↓
chrome.exe
 ↓
download.zip
```

### Pivot 4

```text
download.zip
 ↓
script.js
```

### Pivot 5

```text
script.js
 ↓
wscript.exe
```

### Pivot 6

```text
wscript.exe
 ↓
185.x.x.x
```

Agora existe:

```text
Domínio
+
IP
+
Host
+
Arquivo
+
Processo
+
Comunicação
```

A investigação possui uma cadeia coerente.

---

# Como documentar

Um bom registro pode seguir:

```text
Indicador:
login-example.net

Tipo:
Domain

Origem:
Proxy Alert

First Seen:
09/08/2026 10:20

Last Seen:
09/08/2026 10:32

Hosts:
WS-023

Users:
user01

Resolved IP:
185.x.x.x

Related Hash:
abc123...

Related Process:
wscript.exe

Reputation:
Malicious

Classification:
Confirmed Malicious

Action:
Endpoint isolated
```

---

# Checklist de análise

```text
[ ] Identifiquei o tipo do indicador?

[ ] Identifiquei a origem?

[ ] Registrei o timestamp?

[ ] Defini o período da busca?

[ ] Consultei a reputação?

[ ] Verifiquei o contexto?

[ ] Pesquisei o IOC no SIEM?

[ ] Pesquisei no EDR?

[ ] Verifiquei DNS?

[ ] Verifiquei Firewall?

[ ] Verifiquei Proxy?

[ ] Verifiquei autenticação?

[ ] Identifiquei hosts afetados?

[ ] Identifiquei usuários?

[ ] Identifiquei processos?

[ ] Identifiquei arquivos?

[ ] Identifiquei hashes?

[ ] Identifiquei domínios relacionados?

[ ] Fiz pivoting?

[ ] Procurei indicadores adicionais?

[ ] Avaliei prevalência?

[ ] Avaliei frequência?

[ ] Analisei a timeline?

[ ] Avaliei o escopo?

[ ] Avaliei o impacto?

[ ] Classifiquei o indicador?

[ ] Registrei evidências?

[ ] Existe necessidade de contenção?

[ ] Existe necessidade de Threat Hunting?

[ ] O caso precisa ser escalado?
```

---

# Fluxo de Análise

```text
                       INDICADOR
                           │
                           ▼
                     Identificação
                           │
                           ▼
                      Validação
                           │
                           ▼
                     Enriquecimento
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
             SIEM         EDR          TI
              │            │            │
              └────────────┼────────────┘
                           ▼
                       Correlação
                           │
                           ▼
                        Pivoting
                           │
                           ▼
                        Timeline
                           │
                           ▼
                          Escopo
                           │
                           ▼
                         Impacto
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                 Benigno       Malicioso
                    │             │
                    ▼             ▼
                 Encerrar    Conter/Escalar
                                  │
                                  ▼
                            Threat Hunting
```

---

# O objetivo da análise

O objetivo não é encontrar o maior número possível de indicadores.

É encontrar os **indicadores que realmente ajudam a explicar o evento**.

Uma investigação ruim pode terminar assim:

```text
"Encontramos 15 IOCs."
```

Uma investigação boa termina assim:

```text
"Identificamos o domínio utilizado no acesso inicial,
o IP associado à infraestrutura,
o endpoint afetado,
o processo responsável pela comunicação,
o arquivo baixado e o hash correspondente.

A correlação temporal confirmou a sequência
entre acesso, execução e comunicação externa."
```

A diferença está no contexto.

---

# Conclusão

Indicadores são peças de uma investigação.

Um IP pode levar a um domínio.

Um domínio pode levar a uma URL.

Uma URL pode levar a um arquivo.

O arquivo pode revelar um hash.

O hash pode aparecer em outros endpoints.

E esses endpoints podem revelar usuários, processos e novos indicadores.

```text
Indicador
   ↓
Pivot
   ↓
Novo indicador
   ↓
Correlação
   ↓
Contexto
   ↓
Evidência
```

O analista de SOC não deve simplesmente perguntar:

> **"Esse IOC é malicioso?"**

A pergunta mais importante é:

> **"Que história os indicadores estão contando quando são analisados juntos?"**

É essa capacidade de conectar eventos aparentemente isolados que transforma **logs, alertas e IOCs em uma investigação de segurança.**
