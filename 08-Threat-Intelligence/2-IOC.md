# IOC — Indicators of Compromise

## Introdução

IOC significa **Indicator of Compromise**, ou **Indicador de Comprometimento**.

É uma evidência que pode indicar que um sistema, usuário ou ambiente teve algum tipo de contato com uma ameaça.

Os indicadores podem aparecer durante:

* Detecção
* Investigação
* Threat Hunting
* Resposta a Incidentes
* Threat Intelligence

Exemplos:

```text
IP
Domínio
URL
Hash
E-mail
Arquivo
Processo
Certificado
User-Agent
```

Mas existe uma diferença importante:

> **Encontrar um IOC não significa automaticamente encontrar um ataque.**

O indicador precisa ser analisado dentro de contexto.

---

# O que é um IOC?

Um IOC é uma evidência observável associada a uma possível atividade maliciosa.

Exemplo:

```text
Host-023
   ↓
Conexão
   ↓
185.xxx.xxx.xxx
   ↓
IP associado a malware
```

O IP é o IOC.

Mas a investigação precisa descobrir:

```text
Quem realizou a conexão?
Quando?
Qual processo?
Qual domínio?
Qual usuário?
O que aconteceu antes?
O que aconteceu depois?
```

O IOC é o ponto de partida.

Não necessariamente a conclusão.

---

# IOC x Evento

Um evento é qualquer atividade registrada.

Exemplo:

```text
Usuário realizou login.
```

Isso é um evento.

Agora:

```text
Usuário realizou login
de um país nunca observado anteriormente
e em horário incomum.
```

O evento passa a possuir contexto suspeito.

Se o endereço utilizado também estiver associado a uma infraestrutura maliciosa:

```text
Login
+
IP malicioso
+
Horário anômalo
+
MFA suspeito
```

o conjunto de evidências pode indicar comprometimento.

---

# IOC x IOA

IOC e IOA possuem funções diferentes.

### IOC

Procura evidências que já foram associadas a uma ameaça.

```text
Hash
IP
Domínio
URL
Arquivo
```

### IOA

Procura comportamentos associados a um ataque.

```text
Execução suspeita
Movimentação lateral
Credential Dumping
PowerShell anômalo
Criação de persistência
```

Uma operação madura utiliza os dois.

```text
IOC
+
IOA
+
Contexto
=
Melhor detecção
```

---

# Principais tipos de IOC

## Endereço IP

Pode representar:

```text
C2
Servidor de malware
Phishing
Scanner
Proxy
Infraestrutura comprometida
```

Exemplo:

```text
185.xxx.xxx.xxx
```

O IP deve ser analisado considerando:

```text
ASN
Geolocalização
Reputação
Histórico
Domínios relacionados
Data da observação
```

---

# Domínio

Domínios podem ser utilizados para:

```text
Phishing
C2
Distribuição de malware
Redirecionamento
Infraestrutura de ataque
```

Exemplo:

```text
login-example-secure.com
```

A análise pode incluir:

```text
DNS
WHOIS
Certificado
Subdomínios
IP relacionado
Data de criação
Histórico
```

---

# URL

URLs podem apontar diretamente para recursos maliciosos.

Exemplo:

```text
https://example.com/update.exe
```

Uma URL pode revelar:

```text
Domínio
Caminho
Arquivo
Parâmetros
Campanha
```

---

# Hash

Hashes são utilizados para identificar arquivos.

Exemplos:

```text
MD5
SHA-1
SHA-256
```

Preferencialmente:

```text
SHA-256
```

Exemplo:

```text
SHA-256:
a8f5f167f44f4964e6c998dee827110c...
```

Se o hash estiver associado a malware conhecido, pode ser utilizado para pesquisa em:

```text
EDR
SIEM
Threat Intelligence
Sandbox
File Analysis
```

---

# E-mail

Um e-mail também pode conter indicadores.

Exemplo:

```text
From:
security@example-malicious.com

Reply-To:
attacker@example.net

URL:
login-example.com
```

Indicadores possíveis:

```text
Remetente
Reply-To
Domínio
URL
IP
Anexo
Hash
Assunto
```

---

# Arquivos

Arquivos podem ser identificados por:

```text
Nome
Caminho
Hash
Extensão
Tamanho
Assinatura
Metadados
```

Exemplo:

```text
C:\Users\User\AppData\Local\Temp\update.exe
```

O caminho também pode ser relevante.

Um executável desconhecido em uma pasta temporária pode merecer investigação.

---

# Processos

Processos podem se tornar indicadores quando associados a comportamento suspeito.

Exemplo:

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
Internet
```

Isoladamente:

```text
powershell.exe
```

não significa malware.

Mas:

```text
Office
 ↓
PowerShell
 ↓
Download
 ↓
Execução
```

pode indicar uma cadeia de ataque.

---

# Registry

Em ambientes Windows, alterações específicas no Registry podem ser indicadores.

Exemplo:

```text
Run Keys
Services
Autoruns
```

Por exemplo:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

O contexto é fundamental para determinar se a alteração é legítima ou suspeita.

---

# Scheduled Tasks

Uma tarefa agendada pode ser utilizada para persistência.

Exemplo:

```text
schtasks
   ↓
Nova tarefa
   ↓
Executa arquivo suspeito
```

O nome da tarefa, comando e horário devem ser analisados.

---

# Certificados

Certificados TLS podem ajudar a relacionar infraestrutura.

Exemplo:

```text
Domínio A
   ↓
Certificado
   ↓
Domínio B
   ↓
IP
```

Isso pode revelar relacionamentos que não aparecem em uma simples consulta DNS.

---

# User-Agent

Um User-Agent pode ajudar a identificar ferramentas ou padrões incomuns.

Exemplo:

```text
Mozilla/5.0
```

é extremamente comum.

Mas um User-Agent incomum combinado com:

```text
IP suspeito
+
Endpoint comprometido
+
Requisições repetitivas
```

pode gerar uma hipótese de investigação.

---

# Indicadores compostos

Um dos maiores erros é analisar cada IOC isoladamente.

Considere:

```text
IP:
185.xxx.xxx.xxx
```

Agora:

```text
IP
+
Domínio
+
Hash
+
Processo
+
Usuário
+
Timestamp
```

A relação entre os indicadores pode formar uma evidência muito mais forte.

```text
IOC
   ↓
IOC
   ↓
IOC
   ↓
IOC
   ↓
Contexto
```

---

# IOC Lifecycle

Um indicador possui um ciclo de vida.

```text
Descoberta
   ↓
Validação
   ↓
Enriquecimento
   ↓
Distribuição
   ↓
Detecção
   ↓
Monitoramento
   ↓
Reavaliação
   ↓
Expiração
```

Isso evita tratar indicadores como informações permanentes.

---

# Descoberta

Um IOC pode ser descoberto através de:

```text
Alerta
Investigação
Threat Hunting
Malware Analysis
Threat Feed
OSINT
Incidente
EDR
SIEM
```

Exemplo:

```text
Alerta
 ↓
Analista identifica domínio suspeito
 ↓
Domínio vira IOC
```

---

# Validação

Antes de utilizar o IOC:

```text
É realmente malicioso?
```

Avaliar:

```text
Fonte
Reputação
Data
Contexto
Histórico
Correlação
```

Um indicador encontrado em apenas uma fonte desconhecida não deve necessariamente receber o mesmo tratamento de um indicador confirmado por múltiplas fontes.

---

# Enriquecimento

Depois de encontrar um IOC, buscar mais informações.

Exemplo:

```text
IP
 ↓
ASN
 ↓
Domínios
 ↓
DNS histórico
 ↓
Certificados
 ↓
Malware
 ↓
Campanhas
```

O enriquecimento pode transformar:

```text
IP suspeito
```

em:

```text
Infraestrutura associada a uma campanha conhecida.
```

---

# Correlação

O IOC precisa ser pesquisado no ambiente.

Exemplo:

```text
IOC:
malicious-domain.com
```

Pesquisar:

```text
DNS Logs
Proxy
Firewall
EDR
SIEM
```

Resultado:

```text
Host-01 → domínio
Host-07 → domínio
Host-12 → domínio
```

Agora o IOC possui impacto interno.

---

# IOC Hit

Quando um IOC é encontrado dentro do ambiente, temos um **hit**.

Exemplo:

```text
Threat Feed
     ↓
IP malicioso
     ↓
SIEM
     ↓
Host-023 realizou conexão
```

O hit não significa automaticamente comprometimento.

Ele significa:

> **Existe uma correspondência que precisa ser analisada.**

---

# IOC Hit ≠ Incidente

Esse ponto é importante.

Imagine:

```text
IOC:
IP malicioso
```

O firewall registra:

```text
Host-01 → IP
```

Mas a conexão foi:

```text
Bloqueada
```

E não houve:

```text
Execução
Persistência
Comunicação posterior
```

O resultado pode ser diferente de:

```text
Host
 ↓
Conectou
 ↓
Download
 ↓
Execução
 ↓
C2
```

Por isso:

```text
IOC Hit
→ Investigação
→ Contexto
→ Classificação
```

---

# Contexto temporal

Sempre considerar quando o IOC foi observado.

Exemplo:

```text
IOC:
IP malicioso

Última atividade:
2024
```

Se o evento interno ocorreu:

```text
2026
```

a relevância precisa ser reavaliada.

Indicadores possuem validade operacional.

---

# IOC Score

Uma organização pode utilizar uma classificação de confiança.

Exemplo:

```text
Confiança Alta
Confiança Média
Confiança Baixa
```

Ou:

```text
Score:
0 → 100
```

Exemplo:

```text
IOC:
185.xxx.xxx.xxx

Fonte:
Alta

Observado:
Recentemente

Múltiplas fontes:
Sim

Contexto:
C2 confirmado

Score:
95/100
```

A metodologia deve ser definida pela organização.

---

# IOC e False Positive

Um IOC pode gerar falso positivo.

Exemplo:

```text
IP:
1.2.3.4
```

O endereço pode ter sido utilizado anteriormente para atividade maliciosa, mas atualmente hospedar um serviço legítimo.

Por isso:

```text
IOC
+
Contexto
+
Atualidade
=
Decisão
```

---

# IOC e Threat Intelligence

Threat Intelligence pode fornecer:

```text
IOC
+
Contexto
+
Relacionamentos
+
TTPs
+
Campanhas
```

Exemplo:

```text
IP
 ↓
C2
 ↓
Malware
 ↓
Threat Actor
 ↓
TTP
```

O IOC se torna parte de uma inteligência maior.

---

# IOC e SIEM

Um SIEM pode correlacionar indicadores com eventos.

Exemplo:

```text
Threat Intelligence
       ↓
IOC
       ↓
SIEM
       ↓
Logs
       ↓
Correlação
       ↓
Alerta
```

Exemplo de lógica:

```text
IF
destination_ip IN threat_intelligence
AND
event_action = allowed
THEN
generate_alert
```

Na prática, regras devem incluir contexto para evitar excesso de ruído.

---

# IOC e EDR

EDR permite procurar indicadores diretamente nos endpoints.

Exemplo:

```text
IOC:
SHA-256

EDR
 ↓
File Search
 ↓
Hosts afetados
```

Resultado:

```text
Host-02
Host-08
```

A investigação pode continuar nesses endpoints.

---

# IOC e Firewall

Um IOC pode ser utilizado como indicador de bloqueio.

```text
IP malicioso
     ↓
Firewall
     ↓
Block
```

Mas o bloqueio automático deve considerar:

```text
Confiança
Criticidade
Contexto
Origem
Atualidade
```

---

# IOC e DNS

DNS é especialmente útil para indicadores baseados em domínio.

Exemplo:

```text
Host-22
 ↓
DNS Query
 ↓
malicious-domain.com
```

O SOC pode então investigar:

```text
Processo responsável
Usuário
Horário
IP retornado
Conexões posteriores
```

---

# IOC e E-mail

Em phishing:

```text
E-mail
 ↓
Remetente
 ↓
Domínio
 ↓
URL
 ↓
IP
 ↓
Hash
```

Um único e-mail pode produzir vários indicadores.

---

# IOC e Malware

Durante a análise de malware podem ser encontrados:

```text
Hash
C2
Domínio
IP
Mutex
Arquivo
Registry Key
URL
```

Esses indicadores podem alimentar:

```text
SIEM
EDR
Firewall
DNS
Threat Intelligence
```

---

# IOC Pyramid

Uma forma de pensar sobre indicadores é através da pirâmide de dificuldade de alteração.

Indicadores simples como:

```text
Hash
IP
Domínio
```

podem ser alterados relativamente rápido por um atacante.

Já:

```text
TTP
Comportamento
```

podem ser mais difíceis de modificar sem alterar a própria forma de operação.

Exemplo:

```text
        TTP
       /   \
  Comportamento
    /       \
   Domínio  IP
      \     /
       Hash
```

Isso reforça a importância de combinar IOC com análise comportamental.

---

# IOC não substitui comportamento

Imagine:

```text
Atacante troca o IP.
```

Uma detecção baseada somente no IP deixa de funcionar.

Mas:

```text
PowerShell
+
Download
+
Execução
+
Persistência
```

continua podendo ser detectado.

Por isso:

```text
IOC
+
IOA
+
TTP
```

produzem uma cobertura mais robusta.

---

# IOC interno

Nem todo indicador precisa vir de fontes externas.

Um SOC pode descobrir seus próprios IOCs.

Exemplo:

```text
Investigação
 ↓
Arquivo malicioso encontrado
 ↓
Hash calculado
 ↓
Hash vira IOC interno
```

Depois:

```text
SIEM
 ↓
Pesquisar hash
 ↓
Encontrar outros hosts
```

---

# IOC externo

Pode vir de:

```text
Threat Feeds
CERTs
CSIRTs
OSINT
Relatórios
Parceiros
Fornecedores
Comunidades de segurança
```

A fonte deve ser registrada.

---

# IOC Database

Uma organização pode manter um repositório de indicadores.

Exemplo:

```text
IOC
Tipo
Valor
Fonte
Data
Confiança
Campanha
Threat Actor
TTP
Status
Expiração
```

Exemplo:

| Tipo    | Indicador             | Confiança | Fonte | Status |
| ------- | --------------------- | --------- | ----- | ------ |
| IP      | 185.xxx.xxx.xxx       | Alta      | Feed  | Ativo  |
| Domain  | malicious-example.com | Alta      | OSINT | Ativo  |
| SHA-256 | abc123...             | Alta      | EDR   | Ativo  |

---

# Status do IOC

Um indicador pode possuir estados:

```text
New
Validated
Active
Expired
False Positive
Deprecated
```

Exemplo:

```text
New
 ↓
Validated
 ↓
Active
 ↓
Expired
```

Isso evita indicadores abandonados no ambiente.

---

# Expiração

Indicadores devem ser revisados.

Exemplo:

```text
IOC:
malicious-domain.com

Criado:
01/08

Revisão:
30/08
```

Após a revisão:

```text
Continuar ativo
```

ou:

```text
Expirar
```

---

# IOC e automação

Indicadores podem alimentar processos automatizados.

Exemplo:

```text
IOC confirmado
      ↓
SOAR
      ↓
Firewall
      ↓
Block
      ↓
EDR
      ↓
Investigate
      ↓
Ticket
```

Mas automação deve ser proporcional à confiança do indicador.

Uma abordagem possível:

```text
Alta confiança
→ Bloqueio automático

Média confiança
→ Alerta + enriquecimento

Baixa confiança
→ Monitoramento
```

A política exata depende do ambiente.

---

# Exemplo prático

## Cenário

Um usuário recebe um e-mail contendo:

```text
"Atualize sua conta imediatamente."
```

O link aponta para:

```text
login-example.com
```

Durante a investigação:

```text
Domínio
 ↓
IP
 ↓
Certificado
 ↓
URL
 ↓
Hash do arquivo
```

O SOC identifica:

```text
Domain:
login-example.com

IP:
185.xxx.xxx.xxx

SHA-256:
abc123...
```

Agora existem três IOCs relacionados.

---

# Investigação

Pesquisar:

```text
SIEM
DNS
Proxy
Firewall
EDR
E-mail Gateway
```

Resultado:

```text
Usuário A:
Acessou URL

Usuário B:
Recebeu e-mail

Usuário C:
Baixou arquivo
```

Agora a investigação pode priorizar:

```text
Usuário C
```

porque houve download.

---

# Contenção

Dependendo do caso:

```text
Bloquear domínio
Bloquear IP
Quarentenar e-mail
Isolar endpoint
Revogar sessão
Resetar credencial
```

---

# Pós-incidente

Os indicadores podem alimentar:

```text
Threat Intelligence
Detection Rules
Threat Hunting
Playbooks
Firewall
EDR
SIEM
```

O incidente gera novos controles.

---

# Checklist de análise de IOC

```text
[ ] Qual é o tipo do IOC?

[ ] Qual é o valor?

[ ] Qual é a fonte?

[ ] A fonte é confiável?

[ ] Quando foi observado?

[ ] O indicador ainda é válido?

[ ] Existe contexto?

[ ] Existem IOCs relacionados?

[ ] Existe campanha associada?

[ ] Existe Threat Actor associado?

[ ] Existem TTPs associados?

[ ] O IOC aparece no ambiente?

[ ] Quais hosts foram afetados?

[ ] Quais usuários estão envolvidos?

[ ] Qual foi o primeiro evento?

[ ] Houve comunicação permitida?

[ ] Houve execução?

[ ] Houve persistência?

[ ] Houve movimentação lateral?

[ ] Houve exfiltração?

[ ] O IOC deve ser bloqueado?

[ ] O IOC deve ser monitorado?

[ ] O IOC possui prazo de expiração?

[ ] A investigação foi documentada?
```

---

# Fluxo de investigação de IOC

```text
                    IOC
                     │
                     ▼
                  Validar
                     │
                     ▼
                Enriquecer
                     │
                     ▼
                 Correlacionar
                     │
                     ▼
              Pesquisar Ambiente
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       Sem Hit                Hit
          │                     │
          ▼                     ▼
     Monitorar             Investigar
                                │
                                ▼
                           Classificar
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
                 Benigno                  Malicioso
                    │                       │
                    ▼                       ▼
                 Fechar                  Responder
```

---

# O pensamento do analista

Um IOC não deve gerar apenas:

```text
"Bloquear IP."
```

A pergunta correta é:

```text
"Por que esse indicador está aparecendo
no meu ambiente?"
```

Depois:

```text
Quem utilizou?
Quando?
Como?
Qual processo?
Qual ativo?
Qual impacto?
Existe persistência?
Existem outros indicadores?
```

É essa análise que transforma uma simples correspondência em investigação de segurança.

---

# Resumo

IOCs são fundamentais para operações de segurança porque fornecem evidências observáveis que podem ser utilizadas para detectar, investigar e responder a ameaças.

Os principais indicadores incluem:

```text
IP
Domínio
URL
Hash
E-mail
Arquivo
Processo
Certificado
```

Porém, um IOC isolado possui valor limitado.

O verdadeiro valor surge quando ele é combinado com:

```text
Contexto
+
Tempo
+
Comportamento
+
Threat Intelligence
+
Logs
+
EDR
+
SIEM
```

O objetivo não é simplesmente encontrar indicadores.

É responder:

> **Existe uma ameaça no meu ambiente e o que preciso fazer a respeito?**

```text
IOC
 ↓
Contexto
 ↓
Correlação
 ↓
Investigação
 ↓
Decisão
 ↓
Resposta
```

> **Um bom analista não trata um IOC como uma sentença. Trata-o como uma pista.**
