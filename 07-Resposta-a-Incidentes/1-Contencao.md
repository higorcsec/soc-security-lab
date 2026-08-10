# Contenção

## Introdução

Contenção é o momento em que o SOC deixa de apenas investigar o incidente e começa a **limitar sua capacidade de causar dano**.

Quando um incidente é confirmado, a pergunta deixa de ser:

> **"O que aconteceu?"**

e passa a ser:

> **"O que precisamos fazer agora para impedir que isso continue?"**

A contenção busca interromper ou limitar:

```text
Propagação
Execução
Comunicação
Persistência
Movimentação lateral
Roubo de credenciais
Exfiltração
Impacto
```

O objetivo não é necessariamente eliminar a ameaça.

Isso pertence à etapa de erradicação.

A contenção existe para **ganhar controle sobre o incidente**.

---

# Contenção x Erradicação

Esses conceitos são próximos, mas possuem objetivos diferentes.

### Contenção

```text
Impedir que o incidente continue causando impacto.
```

### Erradicação

```text
Remover completamente a causa do incidente.
```

Exemplo:

Um computador está infectado por malware e realizando conexões com um servidor C2.

A contenção pode ser:

```text
Isolar o endpoint da rede.
```

A erradicação pode ser:

```text
Remover o malware
Eliminar persistência
Corrigir a vulnerabilidade
```

Em resumo:

```text
CONTENÇÃO
"Pare o problema."

ERRADICAÇÃO
"Remova a causa."
```

---

# O princípio da contenção

Uma boa contenção precisa equilibrar duas necessidades:

```text
Segurança
+
Continuidade operacional
```

Isolar todos os servidores de uma empresa pode impedir o ataque.

Também pode parar a empresa inteira.

Por isso, a pergunta não deve ser:

> **"Podemos bloquear tudo?"**

Mas:

> **"Qual é a menor ação capaz de reduzir significativamente o risco?"**

---

# Contenção imediata

Em situações críticas, algumas ações precisam acontecer rapidamente.

Exemplos:

```text
Isolar endpoint
Bloquear IP
Bloquear domínio
Desabilitar conta
Revogar sessão
Bloquear hash
Interromper processo
Remover acesso externo
```

A ação depende do tipo de incidente.

---

# Contenção baseada em evidências

Não é recomendado bloquear tudo sem entender o incidente.

Imagine:

```text
Alert:
Suspicious Domain
```

Bloquear imediatamente pode ser correto.

Mas antes de uma ação mais ampla, verificar:

```text
Quem acessou?
Quantos hosts?
Qual processo?
Qual aplicação?
Qual impacto?
```

Isso evita transformar uma resposta de segurança em uma indisponibilidade.

---

# Isolamento de endpoint

Uma das ações mais comuns em incidentes envolvendo malware.

Exemplo:

```text
WS-023
   ↓
Malware
   ↓
C2
```

Após isolamento:

```text
WS-023
   X
Internet
```

O objetivo é impedir:

```text
C2
Movimentação lateral
Exfiltração
Download de novos payloads
```

---

# Quando isolar um endpoint?

Pode ser necessário quando existem evidências de:

```text
Malware ativo
Ransomware
C2
Credenciais comprometidas
Movimentação lateral
Exfiltração
Execução maliciosa
```

Mas o impacto operacional deve ser avaliado.

Um notebook de usuário e um servidor de produção podem exigir decisões diferentes.

---

# Servidores críticos

Imagine:

```text
Servidor:
database-prod
```

e existe suspeita de comprometimento.

Desligar imediatamente pode causar:

```text
Indisponibilidade
Perda de transações
Interrupção de aplicações
Impacto financeiro
```

Nesse cenário, a contenção pode exigir:

```text
Bloqueio de comunicação
Restrição de acesso
Segmentação
Captura de evidências
Failover
```

A resposta deve considerar o papel do ativo.

---

# Bloqueio de IP

Se um endereço IP estiver associado a uma infraestrutura maliciosa:

```text
185.x.x.x
```

pode ser criado um bloqueio:

```text
Firewall
 ↓
185.x.x.x
 ↓
DENY
```

Isso pode interromper:

```text
C2
Download
Exfiltração
Scanning
```

Mas existe um risco:

```text
IP compartilhado
CDN
Cloud
Hosting
NAT
```

Por isso, bloquear um IP sem entender sua infraestrutura pode causar falso positivo em escala.

---

# Bloqueio de domínio

Outra opção:

```text
malicious-example.com
```

pode ser bloqueado no:

```text
DNS
Proxy
Firewall
Secure Web Gateway
EDR
```

Exemplo:

```text
Endpoint
   ↓
DNS Query
   ↓
malicious-example.com
   ↓
BLOCK
```

A vantagem é que o bloqueio acompanha o domínio mesmo que seu IP seja alterado.

---

# Bloqueio de URL

Quando possível, bloquear uma URL específica pode ser mais preciso.

Exemplo:

```text
https://example.com/malware/download.exe
```

Em vez de bloquear:

```text
example.com
```

Isso pode reduzir o impacto sobre serviços legítimos hospedados no mesmo domínio.

---

# Bloqueio de hash

Se um arquivo malicioso for identificado:

```text
SHA256:
abc123...
```

o hash pode ser adicionado às políticas de bloqueio do EDR.

Isso pode impedir:

```text
Execução
Download
Persistência
```

Mas existe uma limitação:

> **Se o atacante modificar o arquivo, o hash muda.**

Por isso, hash é uma camada de contenção, não uma solução completa.

---

# Encerramento de processo

Se um processo malicioso estiver ativo:

```text
unknown.exe
```

pode ser necessário interrompê-lo.

Exemplo:

```text
malware.exe
   ↓
Processo ativo
   ↓
Terminate
```

Antes de encerrar, quando possível, preservar informações relevantes:

```text
PID
Parent Process
Command Line
Path
Hash
Network Connections
Timestamp
```

Encerrar um processo pode interromper a atividade, mas também pode destruir evidências voláteis.

---

# Desabilitar conta

Em incidentes envolvendo identidade:

```text
user01
```

pode ser necessário bloquear temporariamente a conta.

Especialmente quando existem:

```text
Logins anômalos
Credential Theft
Brute Force
MFA Abuse
Impossible Travel
Sessões suspeitas
```

A contenção pode incluir:

```text
Disable Account
Reset Password
Revoke Sessions
Revoke Tokens
Require MFA
```

---

# Revogação de sessões

Trocar a senha nem sempre encerra sessões existentes.

Se uma sessão ou token estiver comprometido:

```text
Password Reset
```

pode não ser suficiente.

Também pode ser necessário:

```text
Revoke Sessions
Revoke Tokens
Invalidate Refresh Tokens
```

O objetivo é impedir que o atacante continue utilizando uma sessão já obtida.

---

# Contenção de credenciais

Quando existe suspeita de comprometimento:

```text
Conta
Senha
Token
API Key
SSH Key
Cookie
Session Token
```

podem precisar ser revogados.

A prioridade depende do nível de acesso.

Uma credencial administrativa comprometida representa risco muito maior do que uma conta com privilégios mínimos.

---

# Contenção de e-mail

Em um ataque de phishing, a contenção pode envolver:

```text
Remover mensagens
Bloquear remetente
Bloquear domínio
Bloquear URL
Bloquear hash do anexo
```

Exemplo:

```text
Phishing Email
     ↓
Mail Gateway
     ↓
Message Search
     ↓
Remove from Mailboxes
```

Isso impede que outros usuários interajam com a mesma ameaça.

---

# Contenção de malware

Uma resposta típica pode seguir:

```text
Detecção
   ↓
Isolamento
   ↓
Bloqueio de IOC
   ↓
Interrupção da execução
   ↓
Preservação de evidências
```

Depois:

```text
Erradicação
```

---

# Contenção de ransomware

Ransomware exige velocidade.

Um possível fluxo:

```text
Ransomware Detected
        ↓
Isolate Endpoint
        ↓
Block C2
        ↓
Disable Compromised Account
        ↓
Protect Backups
        ↓
Identify Other Hosts
```

O objetivo inicial é impedir:

```text
Propagação
Criptografia
Movimentação lateral
Comprometimento de backups
```

---

# Proteção de backups

Em ransomware, os backups podem ser um dos ativos mais importantes.

Se houver suspeita de comprometimento:

```text
Backup Infrastructure
        ↓
Restrict Access
        ↓
Protect
```

Isso pode envolver:

```text
Isolamento
Credenciais separadas
Imutabilidade
Restrição de acesso
Monitoramento
```

O objetivo é impedir que o atacante transforme:

```text
"Tenho uma cópia dos dados"
```

em:

```text
"Não tenho mais nenhuma cópia confiável."
```

---

# Contenção de movimentação lateral

Se o atacante estiver se movimentando:

```text
WS-01
 ↓
WS-02
 ↓
SERVER-01
 ↓
DC-01
```

a contenção deve tentar interromper a cadeia.

Possíveis ações:

```text
Segmentação
Bloqueio de portas
Isolamento de hosts
Revogação de credenciais
Restrição de administração remota
```

---

# Segmentação

Segmentação reduz o alcance de um incidente.

Exemplo:

```text
Usuários
   │
   X
Servidores
   │
   X
Banco de Dados
```

Em vez de:

```text
Toda a rede
     │
     └── acesso amplo
```

Quanto menor o caminho disponível para o atacante, menor o potencial de propagação.

---

# Contenção em ambiente cloud

Em cloud, a contenção pode envolver:

```text
Revogar Access Keys
Bloquear Security Group
Alterar IAM Policy
Desabilitar usuário
Revogar Tokens
Isolar workload
Bloquear egress
```

Exemplo:

```text
Compromised Instance
        ↓
Restrict Network
        ↓
Revoke Credentials
        ↓
Preserve Evidence
```

A lógica é semelhante à infraestrutura tradicional.

O mecanismo muda.

---

# Contenção em VPS

Em uma VPS comprometida:

```text
VPS
 ↓
Suspicious Process
 ↓
Outbound Connection
```

ações possíveis:

```text
Bloquear destino
Restringir portas
Isolar interface
Suspender serviço
Revogar credenciais
Preservar logs
```

Dependendo do incidente, pode ser preferível preservar a máquina para investigação antes de destruí-la ou reinstalá-la.

---

# Contenção em servidor web

Imagine:

```text
Apache/Nginx
      ↓
Webshell
```

A contenção pode incluir:

```text
Remover acesso externo temporariamente
Restringir endpoint comprometido
Bloquear IP atacante
Desabilitar aplicação vulnerável
Preservar arquivos e logs
```

Evitar simplesmente apagar o webshell antes de coletar evidências quando a investigação exigir preservação.

---

# Contenção em banco de dados

Se houver suspeita de acesso indevido:

```text
Database
   ↓
Suspicious Account
```

possíveis ações:

```text
Revogar usuário
Rotacionar credenciais
Restringir origem
Bloquear acesso externo
Reduzir privilégios
```

Antes disso, avaliar:

```text
Aplicações dependentes
Jobs
APIs
Integrações
```

---

# Contenção de exfiltração

Quando há transferência suspeita de dados:

```text
Internal Server
      ↓
External Destination
      ↓
Large Upload
```

ações possíveis:

```text
Bloquear destino
Bloquear processo
Isolar endpoint
Restringir egress
Revogar credenciais
```

O objetivo é interromper o fluxo sem destruir evidências desnecessariamente.

---

# Contenção de C2

Quando existe comunicação com um possível C2:

```text
Endpoint
   ↓
C2
```

ações:

```text
Block Domain
Block IP
Isolate Host
Terminate Process
```

Mas antes de bloquear, quando possível, registrar:

```text
Destination
Port
Protocol
Timestamp
Process
PID
User
Bytes
```

Essas informações podem ser importantes para entender o incidente.

---

# Contenção temporária x permanente

Nem toda ação de contenção precisa ser definitiva.

### Temporária

```text
Isolar endpoint
Bloquear conta
Bloquear IP
Suspender serviço
```

### Permanente

```text
Remover regra
Revogar credencial
Descontinuar serviço
Corrigir vulnerabilidade
```

A contenção normalmente é temporária.

A erradicação e a recuperação tratam da solução definitiva.

---

# Contenção baseada em risco

Uma forma simples de priorizar:

```text
Risco = Probabilidade × Impacto
```

Exemplo:

### Baixo

```text
IOC suspeito
Sem execução
Sem conexão
Sem impacto
```

### Alto

```text
Malware executado
C2 confirmado
Credencial comprometida
```

### Crítico

```text
Ransomware ativo
Data Exfiltration
Domain Controller comprometido
Infraestrutura crítica afetada
```

Quanto maior o risco, mais agressiva pode precisar ser a contenção.

---

# Contenção e criticidade do ativo

Considere:

```text
Endpoint:
WS-023
```

Isolamento normalmente possui impacto limitado.

Agora:

```text
Servidor:
ERP-PROD
```

O mesmo procedimento pode causar impacto operacional significativo.

Por isso, antes da ação:

```text
Identificar ativo
Identificar proprietário
Identificar criticidade
Avaliar dependências
Escolher contenção
```

---

# Contenção sem destruir evidências

Uma resposta rápida não precisa significar uma investigação ruim.

Sempre que possível, registrar:

```text
Timestamp
Ação executada
Responsável
Motivo
Evidência
Resultado
```

Exemplo:

```text
10:42
WS-023 isolated from network.

Reason:
Confirmed malware communication.

Evidence:
EDR + Firewall + DNS.

Analyst:
SOC-N1
```

---

# Chain of Custody

Em incidentes que podem exigir investigação forense ou implicações legais, a preservação das evidências deve ser controlada.

Registrar:

```text
Quem coletou
Quando coletou
Onde estava
Como foi armazenado
Quem acessou
```

Isso ajuda a preservar a integridade da investigação.

---

# Comunicação durante a contenção

Contenção não é apenas uma atividade técnica.

Dependendo da gravidade, pode envolver:

```text
SOC
Infraestrutura
Cloud
Network
IAM
Gestão
Jurídico
Compliance
DPO
Fornecedor
```

A comunicação precisa ser:

```text
Objetiva
Rastreável
Baseada em evidências
```

---

# O que informar

Uma comunicação interna pode conter:

```text
Incidente:
Possible Account Compromise

Impact:
1 user

Affected Asset:
WS-023

Action:
Account disabled

Current Status:
Contained

Next Step:
Credential reset and investigation
```

Sem excesso de especulação.

---

# Contenção e SLA

A velocidade da resposta importa.

Um incidente crítico não deve esperar horas para ser tratado enquanto o atacante continua ativo.

Exemplo:

```text
Critical
 ↓
Immediate Response

High
 ↓
Priority Response

Medium
 ↓
Scheduled Investigation

Low
 ↓
Monitoring / Review
```

Os tempos exatos devem ser definidos pelo processo da organização.

---

# Contenção e SOAR

Em ambientes maduros, algumas ações podem ser automatizadas.

Exemplo:

```text
Alert
 ↓
SIEM
 ↓
SOAR
 ↓
Threat Intelligence
 ↓
Confirmed IOC
 ↓
Block
```

Pode automatizar:

```text
Bloqueio de IP
Bloqueio de domínio
Isolamento de endpoint
Disable Account
Ticket Creation
Notification
```

Mas automação deve considerar risco de falso positivo.

---

# Exemplo de Playbook

## Cenário

```text
EDR detecta malware.
```

### 1. Validar

```text
Host
User
Process
Hash
Timestamp
```

### 2. Conter

```text
Isolate Host
```

### 3. Bloquear

```text
Hash
Domain
IP
```

### 4. Verificar alcance

```text
Search IOC
Search Hash
Search Domain
```

### 5. Proteger identidade

```text
Reset / Revoke Credentials
```

### 6. Preservar evidências

```text
Logs
Process Tree
Network Connections
Files
```

### 7. Escalar

```text
SOC N2/N3
IR Team
```

---

# Exemplo de ransomware

```text
08:30
EDR detecta comportamento de criptografia.

08:31
Endpoint isolado.

08:32
Conta do usuário desabilitada.

08:33
Conexões C2 bloqueadas.

08:35
Busca por IOC iniciada.

08:40
Outros 3 endpoints identificados.

08:42
Endpoints isolados.

08:45
Backups protegidos.

09:00
Escalonamento para Incident Response.
```

O objetivo da contenção foi:

```text
Impedir propagação
+
Proteger backups
+
Interromper comunicação
```

A recuperação virá depois.

---

# O que não fazer

## Não apagar tudo imediatamente

Apagar:

```text
Logs
Arquivos
Processos
Servidor
```

pode destruir evidências.

---

## Não reiniciar automaticamente

Um reboot pode eliminar informações voláteis.

Dependendo do caso:

```text
RAM
Processos
Conexões
Sessões
```

podem ser importantes para a investigação.

---

## Não bloquear infraestrutura inteira sem contexto

Bloquear:

```text
IP
Domínio
ASN
Range inteiro
```

pode causar indisponibilidade.

---

## Não assumir que um IOC é suficiente

```text
IOC encontrado
≠
Incidente confirmado
```

Correlacionar.

---

## Não esquecer outros hosts

Se um malware foi encontrado em:

```text
WS-023
```

pesquisar:

```text
Hash
Domain
IP
Filename
Process
```

em todo o ambiente.

---

# Critérios para considerar a contenção efetiva

Uma contenção pode ser considerada efetiva quando:

```text
[✓] Comunicação maliciosa interrompida
[✓] Propagação interrompida
[✓] Conta comprometida controlada
[✓] Endpoint afetado isolado
[✓] IOC bloqueado quando necessário
[✓] Outros ativos investigados
[✓] Evidências preservadas
[✓] Risco reduzido
```

Mas isso não significa que o incidente terminou.

Ainda podem existir:

```text
Persistência
Malware
Backdoors
Credenciais comprometidas
Vulnerabilidades
```

Por isso, a próxima etapa é a erradicação.

---

# Checklist de Contenção

```text
[ ] Incidente confirmado?

[ ] Severidade definida?

[ ] Ativo afetado identificado?

[ ] Criticidade do ativo conhecida?

[ ] Escopo inicial definido?

[ ] Endpoint precisa ser isolado?

[ ] Conta precisa ser desabilitada?

[ ] Sessões precisam ser revogadas?

[ ] Credenciais precisam ser rotacionadas?

[ ] IP precisa ser bloqueado?

[ ] Domínio precisa ser bloqueado?

[ ] URL precisa ser bloqueada?

[ ] Hash precisa ser bloqueado?

[ ] Processo precisa ser interrompido?

[ ] Comunicação C2 foi interrompida?

[ ] Exfiltração foi interrompida?

[ ] Movimentação lateral foi bloqueada?

[ ] Backups estão protegidos?

[ ] Outros hosts foram pesquisados?

[ ] Evidências foram preservadas?

[ ] Ações foram documentadas?

[ ] Stakeholders foram comunicados?

[ ] Incidente foi escalado?

[ ] Contenção foi validada?
```

---

# Fluxo de Contenção

```text
                     INCIDENTE
                         │
                         ▼
                    Confirmado?
                         │
                ┌────────┴────────┐
                │                 │
               NÃO               SIM
                │                 │
                ▼                 ▼
            Investigar        Classificar
                                  │
                                  ▼
                              Avaliar Risco
                                  │
                                  ▼
                            Definir Contenção
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
          Endpoint             Identity             Network
              │                   │                   │
          Isolar              Revogar              Bloquear
              │                   │                   │
              └───────────────────┼───────────────────┘
                                  ▼
                         Preservar Evidências
                                  │
                                  ▼
                           Validar Contenção
                                  │
                                  ▼
                              Escalonar
                                  │
                                  ▼
                            Erradicação
```

---

# Contenção em uma frase

A contenção não precisa resolver o incidente.

Ela precisa **impedir que o incidente continue crescendo enquanto a equipe trabalha para entender e eliminar a causa**.

```text
Detectar
   ↓
Investigar
   ↓
Conter
   ↓
Erradicar
   ↓
Recuperar
   ↓
Aprender
```

Em um SOC, uma boa contenção é aquela que reduz o impacto **sem perder o controle da investigação**.

O objetivo final é simples:

> **Limitar o dano, preservar as evidências e criar condições para eliminar a ameaça com segurança.**
