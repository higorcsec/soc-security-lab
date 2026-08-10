# O que é Threat Intelligence

## Introdução

Threat Intelligence, ou **Inteligência de Ameaças**, é o processo de transformar informações sobre ameaças em conhecimento útil para a segurança.

Na prática, não basta saber que:

```text
IP: 185.xxx.xxx.xxx
Hash: abc123...
Domínio: malicious-example.com
```

Esses dados isolados possuem pouco valor.

O verdadeiro objetivo é descobrir:

```text
Quem pode estar atacando?
Como está atacando?
O que está tentando alcançar?
Quais sistemas estão em risco?
Como podemos detectar?
Como podemos impedir?
```

Por isso:

> **Threat Intelligence não é apenas coletar indicadores. É transformar dados sobre ameaças em contexto para tomar decisões melhores.**

---

# Dados, informação e inteligência

Uma forma simples de entender Threat Intelligence é separar três conceitos.

### Dados

São eventos ou informações brutas.

```text
IP:
185.xxx.xxx.xxx
```

### Informação

É quando existe algum contexto.

```text
IP observado em campanhas maliciosas.
```

### Inteligência

É quando esse conhecimento pode orientar uma ação.

```text
O IP está associado a uma infraestrutura
utilizada em campanhas de phishing.

Ação:
Bloquear comunicação e investigar
hosts que realizaram conexão.
```

O valor aumenta conforme o contexto aumenta.

```text
Dados
  ↓
Informação
  ↓
Contexto
  ↓
Análise
  ↓
Inteligência
  ↓
Decisão
```

---

# Threat Intelligence dentro de um SOC

Threat Intelligence pode alimentar praticamente todas as etapas de um SOC.

```text
Threat Intelligence
        │
        ├── Detecção
        ├── Investigação
        ├── Threat Hunting
        ├── Resposta
        ├── Prevenção
        └── Gestão de Risco
```

Exemplo:

```text
Threat Intelligence
        ↓
Domínio malicioso identificado
        ↓
SIEM
        ↓
Regra de detecção
        ↓
Alerta
        ↓
Investigação
        ↓
Resposta
```

A inteligência deixa de ser informação armazenada e passa a fazer parte da operação.

---

# Para que serve?

Threat Intelligence pode ajudar a responder perguntas como:

```text
Quais ameaças estão mirando nossa indústria?

Quais vulnerabilidades estão sendo exploradas?

Quais grupos estão associados a determinada campanha?

Quais domínios estão relacionados a uma infraestrutura maliciosa?

Existe atividade relacionada a uma ameaça conhecida dentro do ambiente?

Quais técnicas estão sendo utilizadas?

Quais controles precisam ser reforçados?
```

---

# Threat Intelligence não é apenas IOC

Um erro comum é tratar Threat Intelligence como:

```text
IP
Hash
Domínio
URL
```

Esses elementos são importantes, mas representam apenas uma parte.

Inteligência também pode incluir:

```text
Threat Actor
Campanha
Malware
TTPs
Infraestrutura
Motivação
Alvo
Vulnerabilidades
Técnicas
Procedimentos
```

Por isso:

```text
IOC
≠
Threat Intelligence completa
```

---

# IOC

IOC significa:

> **Indicator of Compromise**

É uma evidência que pode indicar comprometimento ou atividade maliciosa.

Exemplos:

```text
IP malicioso
Domínio malicioso
Hash
URL
E-mail
Arquivo
Certificado
```

Exemplo:

```text
Hash:
44d88612fea8a8f36de82e1278abb02f
```

Um SOC pode pesquisar esse indicador no ambiente.

---

# TTPs

TTP significa:

> **Tactics, Techniques and Procedures**

Representa o comportamento utilizado pelo atacante.

Exemplo:

```text
Tática:
Credential Access

Técnica:
Brute Force

Procedimento:
Tentativas automatizadas de autenticação
contra contas expostas.
```

TTPs são extremamente úteis porque comportamentos podem continuar relevantes mesmo quando os IOCs mudam.

---

# IOC x TTP

Imagine que um atacante troque:

```text
IP
Domínio
Hash
```

Os indicadores mudam.

Mas ele pode continuar utilizando:

```text
PowerShell
Credential Dumping
Phishing
Scheduled Task
Remote Services
```

Por isso:

```text
IOC
→ O que observar?

TTP
→ Como o atacante opera?
```

Uma boa estratégia utiliza os dois.

---

# Contexto é tudo

Considere:

```text
IP:
8.8.8.8
```

O endereço sozinho não significa que existe um ataque.

Agora:

```text
8.8.8.8
+
DNS
+
Host
+
Timestamp
+
Processo
+
Destino
```

tem muito mais contexto.

Threat Intelligence precisa considerar:

```text
Quem?
O quê?
Quando?
Onde?
Como?
Por quê?
```

---

# Ciclo de Threat Intelligence

Threat Intelligence normalmente segue um ciclo.

```text
        Requisitos
            ↓
         Coleta
            ↓
       Processamento
            ↓
         Análise
            ↓
      Disseminação
            ↓
         Feedback
            ↓
        Requisitos
```

Esse ciclo é importante porque inteligência não é um processo de mão única.

O SOC aprende com o que encontra e ajusta o que precisa procurar.

---

# 1. Requisitos

Antes de coletar informações:

> **O que queremos descobrir?**

Exemplo:

```text
Quais campanhas de phishing
estão mirando nossa organização?
```

Ou:

```text
Quais vulnerabilidades críticas
estão sendo exploradas atualmente?
```

Sem uma pergunta clara, a coleta pode gerar muito ruído.

---

# 2. Coleta

Informações podem vir de:

```text
Threat Feeds
OSINT
SIEM
EDR
Firewall
DNS
CERTs
CSIRTs
Relatórios
Pesquisas
Dark Web
Sandbox
```

A fonte deve ser avaliada conforme:

```text
Confiabilidade
Relevância
Atualidade
Contexto
```

---

# 3. Processamento

Dados brutos precisam ser organizados.

Exemplo:

```text
Feed
 ↓
IP
 ↓
Normalização
 ↓
Deduplicação
 ↓
Enriquecimento
 ↓
SIEM
```

Sem processamento:

```text
10.000 indicadores
```

podem se transformar em:

```text
10.000 alertas
```

Isso pode gerar fadiga no SOC.

---

# 4. Análise

O analista precisa determinar:

```text
Esse indicador é relevante?
Existe contexto?
Existe relação com nosso ambiente?
Existe comportamento suspeito?
```

Exemplo:

```text
IOC:
malicious-example.com

SIEM:
Host-023 acessou o domínio.

EDR:
PowerShell iniciou antes da conexão.

DNS:
Domínio foi resolvido.

Resultado:
Alta prioridade para investigação.
```

O valor está na correlação.

---

# 5. Disseminação

A inteligência precisa chegar a quem pode utilizá-la.

Pode ser enviada para:

```text
SOC
Threat Hunting
Incident Response
Firewall
EDR
SIEM
Gestão de Risco
DevSecOps
Infraestrutura
```

Cada equipe pode receber uma visão diferente.

---

# 6. Feedback

Depois de utilizar a inteligência:

```text
Funcionou?
Foi relevante?
Gerou muitos falsos positivos?
Faltou contexto?
A fonte é confiável?
```

Esse feedback melhora o próximo ciclo.

---

# Strategic Threat Intelligence

É voltada para decisões de alto nível.

Exemplo:

```text
Quais ameaças representam maior risco
para a organização nos próximos meses?
```

Pode envolver:

```text
Tendências
Grupos
Geopolítica
Setores atacados
Risco empresarial
Campanhas
```

Normalmente é mais utilizada por:

```text
Gestão
CISO
Risk Management
Security Leadership
```

---

# Tactical Threat Intelligence

Foca principalmente nas técnicas utilizadas pelos atacantes.

Exemplo:

```text
Técnicas utilizadas:
Phishing
Credential Theft
PowerShell
Lateral Movement
C2
```

Pode ajudar a responder:

> **Como os atacantes estão operando?**

É bastante útil para:

```text
SOC
Threat Hunting
Detection Engineering
Blue Team
```

---

# Operational Threat Intelligence

Está relacionada às operações e campanhas em andamento.

Exemplo:

```text
Campanha de phishing
   ↓
Domínios
   ↓
Infraestrutura
   ↓
Malware
   ↓
Alvos
```

Pode ajudar o SOC a entender:

```text
Quem está atacando?
Qual campanha?
Qual objetivo?
Qual infraestrutura?
```

---

# Technical Threat Intelligence

É a camada mais próxima da operação técnica.

Exemplos:

```text
IP
Domain
URL
Hash
File
Email
Certificate
User-Agent
```

É frequentemente integrada diretamente com:

```text
SIEM
EDR
Firewall
DNS
SOAR
```

---

# As quatro camadas

```text
Strategic
     ↓
Operational
     ↓
Tactical
     ↓
Technical
```

Cada uma responde perguntas diferentes.

```text
Strategic:
"Qual risco devemos considerar?"

Operational:
"Quem está atacando e qual campanha?"

Tactical:
"Como está atacando?"

Technical:
"Quais indicadores podemos procurar?"
```

---

# Threat Intelligence e SIEM

O SIEM pode utilizar inteligência para enriquecer eventos.

Exemplo:

```text
Evento:
Host-01 → 185.xxx.xxx.xxx

Threat Intelligence:
IP associado a infraestrutura maliciosa

Correlação:
Processo suspeito + IP malicioso

Resultado:
Alerta de alta prioridade
```

Sem Threat Intelligence:

```text
Conexão externa
```

Com Threat Intelligence:

```text
Conexão com infraestrutura associada
a atividade maliciosa.
```

O contexto muda a prioridade.

---

# Threat Intelligence e EDR

Um EDR pode utilizar indicadores para investigar endpoints.

Exemplo:

```text
IOC:
malicious.exe
```

Pesquisa:

```text
EDR
 ↓
Hosts
 ↓
Processos
 ↓
Execuções
 ↓
Conexões
```

Resultado:

```text
3 endpoints executaram o arquivo.
```

Isso transforma um indicador em uma investigação.

---

# Threat Intelligence e Firewall

Indicadores podem ser utilizados para bloquear comunicação.

Exemplo:

```text
Threat Intelligence
       ↓
IP malicioso
       ↓
Firewall
       ↓
Block
```

Mas bloquear automaticamente qualquer indicador recebido pode ser perigoso.

Um feed ruim pode causar:

```text
False Positive
   ↓
Bloqueio legítimo
   ↓
Indisponibilidade
```

Por isso, inteligência precisa ser validada.

---

# Threat Intelligence e DNS

DNS é uma excelente fonte de contexto.

Exemplo:

```text
Host
 ↓
DNS Query
 ↓
malicious-example.com
 ↓
Threat Intelligence
 ↓
Malicious
```

A correlação pode revelar:

```text
Qual host consultou?
Quando?
Quantas vezes?
Qual processo?
Qual usuário?
```

---

# Threat Intelligence e Threat Hunting

Threat Intelligence pode gerar hipóteses para hunting.

Exemplo:

```text
Threat Intelligence:
Atacante utiliza PowerShell
para baixar payload.
```

Hipótese:

```text
Existem endpoints executando
PowerShell seguido de conexão externa?
```

Hunting:

```text
SIEM
 ↓
PowerShell Events
 ↓
Network Connections
 ↓
DNS
 ↓
EDR
```

Resultado:

```text
Novo host identificado.
```

---

# Threat Intelligence e MITRE ATT&CK

Threat Intelligence pode ser mapeada para MITRE ATT&CK.

Exemplo:

```text
Threat Actor
     ↓
TTP
     ↓
MITRE ATT&CK
     ↓
Detection
     ↓
SOC
```

Isso ajuda a transformar relatórios de ameaças em hipóteses de detecção.

---

# Threat Intelligence e vulnerabilidades

Inteligência também pode ajudar a priorizar vulnerabilidades.

Imagine:

```text
Vulnerability:
CVE-XXXX-XXXX
```

Isoladamente:

```text
Severidade:
Alta
```

Agora:

```text
Alta
+
Explorada ativamente
+
Exposta na internet
+
Sistema crítico
```

A prioridade muda significativamente.

Threat Intelligence adiciona contexto ao risco técnico.

---

# Threat Intelligence e vulnerabilidade

Uma vulnerabilidade não explorada pode ter uma prioridade.

Uma vulnerabilidade:

```text
Explorada ativamente
```

pode exigir resposta imediata.

Por isso:

```text
CVSS
+
Threat Intelligence
+
Exposição
+
Ativo
=
Prioridade
```

---

# Threat Actor

Threat Intelligence também pode acompanhar grupos ou atores de ameaça.

Informações podem incluir:

```text
Nome
Aliases
Alvos
Motivação
TTPs
Malware
Infraestrutura
Campanhas
```

O objetivo não é apenas saber "quem".

É entender:

> **Como esse ator opera e o que isso significa para nosso ambiente?**

---

# Malware Intelligence

Pode incluir informações sobre:

```text
Família
Hash
Comportamento
C2
Persistência
Técnicas
Infraestrutura
Alvos
```

Exemplo:

```text
Malware
   ↓
Hash
   ↓
C2
   ↓
TTP
   ↓
MITRE ATT&CK
```

Isso permite criar múltiplos pontos de detecção.

---

# Campanhas

Um indicador isolado pode parecer irrelevante.

Uma campanha revela relacionamento.

Exemplo:

```text
Domínio A
   ↓
IP A
   ↓
Malware A
   ↓
Phishing
   ↓
Grupo X
```

Esse contexto possui muito mais valor para o SOC.

---

# Infraestrutura

Atacantes precisam de infraestrutura.

Pode incluir:

```text
Domains
IPs
DNS
Certificates
Hosting
Cloud
C2
Redirectors
```

Mapear relacionamentos pode revelar novos indicadores.

---

# Enriquecimento

Um IOC pode ser enriquecido com:

```text
WHOIS
DNS
Geolocation
ASN
Reputation
Passive DNS
Certificates
Malware Associations
Threat Actors
```

Exemplo:

```text
IP
 ↓
ASN
 ↓
Domínios
 ↓
Certificados
 ↓
Malware
 ↓
Campanha
```

Cada camada pode revelar novas pistas.

---

# Confiabilidade da inteligência

Nem toda fonte possui a mesma qualidade.

Avaliar:

```text
Fonte
Reputação
Histórico
Data
Contexto
Confirmação
```

Um indicador sem contexto:

```text
IP malicioso
```

deve ser tratado de forma diferente de:

```text
IP observado recentemente
em múltiplas campanhas
com comportamento confirmado.
```

---

# Freshness

Threat Intelligence envelhece.

Um domínio malicioso pode:

```text
Ser derrubado
Ser reutilizado
Expirar
Mudar de infraestrutura
```

Por isso, considerar:

```text
Data de descoberta
Última observação
Última atualização
Validade
```

---

# Expiração de indicadores

Um IOC antigo não deve necessariamente permanecer bloqueado para sempre.

Pode existir:

```text
IOC
 ↓
Validade
 ↓
Reavaliação
 ↓
Manter / Remover
```

Isso ajuda a evitar:

```text
Bloqueios desnecessários
Ruído
Falsos positivos
```

---

# Contexto interno

Threat Intelligence externa precisa ser combinada com dados internos.

Exemplo:

```text
Threat Intelligence
        +
DNS Logs
        +
EDR
        +
Firewall
        +
Identity
```

Resultado:

```text
Contexto completo
```

Esse é um dos pontos em que Threat Intelligence realmente ganha valor dentro de um SOC.

---

# Threat Intelligence sem contexto

Exemplo:

```text
Feed:
10.000 IPs maliciosos
```

A equipe pode simplesmente bloquear tudo.

Resultado:

```text
Muito ruído
Falsos positivos
Pouco contexto
```

Uma abordagem mais madura:

```text
Feed
 ↓
Validação
 ↓
Enriquecimento
 ↓
Correlação
 ↓
Priorização
 ↓
Ação
```

---

# Qualidade > quantidade

Um SOC não precisa necessariamente de:

```text
1 milhão de IOCs
```

Pode ser mais útil possuir:

```text
5.000 IOCs relevantes
+
Contexto
+
Atualização
+
Correlação
```

O objetivo não é acumular indicadores.

É produzir inteligência acionável.

---

# Intelligence-driven Detection

Uma detecção pode ser construída a partir da inteligência.

Exemplo:

```text
Threat Intelligence
        ↓
TTP identificado
        ↓
Hipótese
        ↓
Regra
        ↓
Teste
        ↓
Alerta
```

Isso cria uma ligação direta entre:

```text
Threat Intelligence
        ↓
Detection Engineering
        ↓
SOC
```

---

# Intelligence Requirements

O SOC pode definir requisitos de inteligência.

Exemplos:

```text
Quais grupos atacam nosso setor?

Quais CVEs estão sendo exploradas?

Quais campanhas de phishing estão ativas?

Quais técnicas são mais utilizadas?

Existe infraestrutura maliciosa relacionada ao nosso ambiente?
```

Essas perguntas orientam a coleta.

---

# PIR

PIR significa:

> **Priority Intelligence Requirement**

É uma pergunta de inteligência considerada prioritária.

Exemplo:

```text
PIR:

Existem campanhas de phishing
ativas contra nossa organização?
```

A coleta passa a ser direcionada para responder essa pergunta.

---

# Threat Intelligence e risco

Threat Intelligence pode melhorar a avaliação de risco.

Exemplo:

```text
Ativo:
Servidor VPN

Vulnerabilidade:
Crítica

Threat Intelligence:
Exploração ativa

Exposição:
Internet

Resultado:
Risco muito elevado
```

A inteligência adiciona contexto que uma simples pontuação de vulnerabilidade pode não representar.

---

# Intelligence → Action

O objetivo final pode ser resumido em:

```text
Dado
 ↓
Informação
 ↓
Inteligência
 ↓
Decisão
 ↓
Ação
```

Exemplo:

```text
Domínio associado a phishing
        ↓
Enriquecimento
        ↓
Campanha identificada
        ↓
Usuários expostos identificados
        ↓
Bloqueio
        ↓
Investigação
```

É aqui que a inteligência deixa de ser apenas informação.

---

# Exemplo prático de SOC

Imagine que um Threat Feed informe:

```text
Domain:
example-malicious.com
```

O SOC não deve simplesmente bloquear.

Primeiro:

```text
Threat Intelligence
       ↓
Validar domínio
       ↓
Enriquecer
       ↓
Consultar SIEM
       ↓
Consultar DNS
       ↓
Consultar EDR
```

Resultado:

```text
Host-042
     ↓
DNS Query
     ↓
example-malicious.com
     ↓
PowerShell
     ↓
Outbound Connection
```

Agora existe uma hipótese concreta de comprometimento.

O processo passa para:

```text
Detecção
 ↓
Triagem
 ↓
Investigação
 ↓
Contenção
```

---

# Exemplo de integração

```text
                THREAT INTELLIGENCE
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
           SIEM         EDR       Firewall
             │           │           │
             └───────────┼───────────┘
                         ▼
                       SOC
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
         Detection     Hunting    Incident Response
```

Threat Intelligence funciona melhor quando está conectada ao restante da operação.

---

# Erros comuns

## Coletar tudo

```text
Mais feeds
≠
Mais inteligência
```

---

## Bloquear tudo automaticamente

Um indicador pode estar:

```text
Desatualizado
Compartilhado
Comprometido
Incorreto
```

Automação precisa considerar confiança e contexto.

---

## Ignorar dados internos

Threat Intelligence externa sem correlação com o ambiente interno possui valor limitado.

---

## Não medir resultados

Perguntas importantes:

```text
Quantos indicadores geraram detecções?

Quantos eram relevantes?

Quantos eram falsos positivos?

Quais ajudaram em investigações?

Quais melhoraram a detecção?
```

---

# Métricas

Algumas métricas úteis:

```text
Quantidade de IOCs processados
Quantidade de IOCs relevantes
Hits por IOC
Taxa de falsos positivos
Tempo até atualização
Quantidade de detecções geradas
Quantidade de incidentes relacionados
```

Mais importante que volume:

```text
IOC
   ↓
Detecção
   ↓
Investigação
   ↓
Ação
```

---

# Threat Intelligence no ciclo de resposta

Threat Intelligence pode participar de todas as etapas:

```text
Preparação
    ↓
Detecção
    ↓
Triagem
    ↓
Investigação
    ↓
Contenção
    ↓
Erradicação
    ↓
Recuperação
    ↓
Pós-Incidente
```

Exemplo:

```text
Antes:
Identifica ameaças

Durante:
Enriquece investigação

Depois:
Atualiza controles
```

---

# O que diferencia um analista

Um analista iniciante pode olhar:

```text
IP malicioso.
```

Um analista mais preparado pergunta:

```text
Por que esse IP é malicioso?

Quando foi observado?

Quem utilizou?

Qual campanha está relacionada?

Qual malware utiliza?

Quais outros indicadores estão associados?

Algum ativo interno se comunicou com ele?

Qual TTP foi utilizado?

Precisamos bloquear ou investigar primeiro?
```

Essa mudança de pensamento é fundamental.

---

# Checklist de Threat Intelligence

```text
[ ] Existe uma pergunta de inteligência?

[ ] A fonte é confiável?

[ ] O dado possui contexto?

[ ] O indicador está atualizado?

[ ] O IOC foi enriquecido?

[ ] Existe relação com alguma campanha?

[ ] Existe relação com algum Threat Actor?

[ ] Existem TTPs associados?

[ ] O IOC foi pesquisado no ambiente interno?

[ ] SIEM foi consultado?

[ ] EDR foi consultado?

[ ] DNS foi consultado?

[ ] Firewall foi consultado?

[ ] O indicador é relevante?

[ ] Existe risco de falso positivo?

[ ] A inteligência gerou alguma ação?

[ ] O resultado foi documentado?
```

---

# Fluxo de Threat Intelligence

```text
                    REQUISITO
                       │
                       ▼
                     COLETA
                       │
                       ▼
                  PROCESSAMENTO
                       │
                       ▼
                   ENRIQUECIMENTO
                       │
                       ▼
                    ANÁLISE
                       │
                       ▼
                 INTELIGÊNCIA
                       │
                       ▼
                  DISSEMINAÇÃO
                       │
                       ▼
                     AÇÃO
                       │
                       ▼
                    FEEDBACK
                       │
                       └──────────────► NOVO REQUISITO
```

---

# Threat Intelligence em uma frase

Threat Intelligence não é saber que um IP é malicioso.

É entender:

```text
O que ele representa
+
Como está relacionado
+
Se afeta meu ambiente
+
O que devo fazer com essa informação
```

O objetivo final é transformar:

```text
Informação
```

em:

```text
Decisão de segurança
```

E uma operação de Threat Intelligence madura transforma essa decisão em:

```text
Detecção
Prevenção
Investigação
Resposta
```

> **O valor da Threat Intelligence não está na quantidade de ameaças conhecidas, mas na capacidade de transformar conhecimento sobre o adversário em ação defensiva.**
