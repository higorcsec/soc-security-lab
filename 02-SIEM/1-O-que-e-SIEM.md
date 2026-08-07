# O que é SIEM?

## Visão geral

**SIEM (Security Information and Event Management)** é uma solução utilizada para **coletar, centralizar, correlacionar e analisar eventos de segurança** provenientes de diferentes sistemas e dispositivos.

Na prática, um SIEM funciona como um ponto central onde o SOC consegue enxergar o que está acontecendo no ambiente.

Um servidor pode registrar uma tentativa de login.

Um firewall pode registrar uma conexão bloqueada.

Um endpoint pode detectar a execução de um processo suspeito.

Um serviço de e-mail pode registrar uma tentativa de phishing.

Isoladamente, cada evento pode parecer pouco relevante.

O SIEM entra justamente nesse ponto: **cruzar essas informações para identificar comportamentos que podem representar uma ameaça.**

---

## Por que um SOC precisa de um SIEM?

Imagine um ambiente com:

* 100 servidores;
* 500 computadores;
* firewalls;
* roteadores;
* Active Directory;
* Microsoft 365;
* aplicações web;
* bancos de dados;
* endpoints;
* serviços em nuvem.

Todos esses componentes geram logs.

Sem uma plataforma centralizada, o analista teria que acessar diferentes sistemas para descobrir o que aconteceu.

Isso gera três problemas principais:

1. **Falta de visibilidade**
2. **Grande quantidade de dados**
3. **Dificuldade para identificar relações entre eventos**

O SIEM centraliza essas informações e permite que o analista trabalhe com uma visão unificada do ambiente.

---

# O que um SIEM faz?

Apesar de existirem diferentes produtos, as funções fundamentais de um SIEM normalmente envolvem:

```text
Coleta
   ↓
Normalização
   ↓
Armazenamento
   ↓
Correlação
   ↓
Detecção
   ↓
Alerta
   ↓
Investigação
   ↓
Resposta
```

Cada etapa possui uma função diferente dentro do processo de monitoramento.

---

## 1. Coleta de logs

O primeiro passo é receber eventos de diferentes fontes.

Exemplos:

```text
Windows
Linux
Firewall
IDS/IPS
Antivírus
EDR
Active Directory
Microsoft 365
Cloud
Aplicações
Banco de dados
Servidores Web
VPN
```

Um servidor Linux, por exemplo, pode registrar:

```text
Aug 07 20:14:22 server01 sshd[2841]: Failed password for invalid user admin from 185.XX.XX.XX
```

Esse evento sozinho informa que houve uma tentativa de autenticação.

O SIEM pode receber esse evento juntamente com milhares ou milhões de outros registros.

---

# 2. Normalização

Cada sistema pode gerar logs em formatos diferentes.

Por exemplo:

```text
Windows Event Log
Syslog
JSON
CEF
LEEF
CSV
Logs proprietários
```

Um firewall pode registrar um evento desta forma:

```text
src=192.168.1.50 dst=10.0.0.10 action=blocked
```

Enquanto uma aplicação pode utilizar:

```json
{
  "source_ip": "192.168.1.50",
  "destination_ip": "10.0.0.10",
  "action": "blocked"
}
```

O SIEM precisa interpretar essas informações e transformá-las em dados que possam ser pesquisados e correlacionados.

Esse processo é importante porque permite comparar eventos de fontes diferentes.

---

# 3. Armazenamento

Depois de coletados e processados, os eventos precisam ser armazenados.

Isso permite ao analista:

* pesquisar eventos antigos;
* reconstruir uma sequência de acontecimentos;
* investigar incidentes;
* criar relatórios;
* realizar auditorias;
* identificar padrões;
* gerar evidências.

Por exemplo:

```text
20:01 - Login realizado
20:03 - Processo PowerShell executado
20:05 - Conexão externa realizada
20:06 - Arquivo suspeito criado
20:07 - Conta adicionada a grupo privilegiado
```

Quando esses eventos são analisados em conjunto, o cenário pode ser muito mais preocupante do que cada evento individualmente.

---

# 4. Correlação de eventos

Essa é uma das funções mais importantes de um SIEM.

**Correlação significa relacionar eventos diferentes para identificar um comportamento.**

Imagine que o SIEM receba:

```text
10:01
Falha de login

10:02
Falha de login

10:03
Falha de login

10:04
Falha de login

10:05
Login realizado com sucesso
```

Individualmente, cada evento pode parecer normal.

Quando relacionados, podem indicar:

```text
Brute Force
        ↓
Múltiplas tentativas
        ↓
Credencial descoberta
        ↓
Login bem-sucedido
```

O SIEM pode gerar um alerta para investigação.

---

# Exemplo de correlação

Uma regra poderia procurar:

```text
Mais de 10 falhas de autenticação
+
mesmo endereço IP
+
período de 5 minutos
+
login bem-sucedido posteriormente
```

Resultado:

```text
ALERTA: Possible Brute Force Attack
Severity: High
Source IP: 185.XX.XX.XX
Target: srv-ad01
User: administrator
```

O analista então inicia a investigação.

---

# 5. Detecção

O SIEM pode utilizar diferentes mecanismos para identificar comportamentos suspeitos.

Entre eles:

* regras;
* correlação;
* listas de indicadores;
* padrões comportamentais;
* inteligência de ameaças;
* anomalias;
* assinaturas;
* thresholds;
* regras baseadas em tempo.

Exemplo:

```text
IF

Failed_Logins > 10

AND

Source_IP = Same_IP

AND

Time_Window < 5 minutes

THEN

Generate Alert
```

A lógica pode variar bastante dependendo da ferramenta e do ambiente.

---

# 6. Geração de alertas

Quando uma condição suspeita é identificada, o SIEM pode gerar um alerta.

Exemplo:

```text
┌─────────────────────────────────────┐
│         SECURITY ALERT              │
├─────────────────────────────────────┤
│ Tipo: Brute Force                   │
│ Severidade: Alta                    │
│ Usuário: administrator              │
│ Origem: 185.XX.XX.XX               │
│ Destino: srv-ad01                   │
│ Tentativas: 37                      │
│ Janela: 5 minutos                   │
└─────────────────────────────────────┘
```

Esse alerta normalmente chega ao SOC para análise.

É importante entender que:

> **Um alerta não significa automaticamente que ocorreu um incidente.**

O alerta é um sinal que precisa ser analisado.

---

# SIEM não substitui o analista

Um erro comum é imaginar que o SIEM simplesmente detecta o ataque e resolve o problema.

Não é assim.

O fluxo real normalmente se aproxima de:

```text
Evento
  ↓
SIEM
  ↓
Regra de detecção
  ↓
Alerta
  ↓
Analista SOC
  ↓
Triagem
  ↓
Investigação
  ↓
Classificação
  ↓
Resposta
```

O SIEM fornece **visibilidade e capacidade de correlação**.

O analista fornece **contexto e tomada de decisão**.

---

# SIEM dentro de um SOC

Um SOC normalmente utiliza o SIEM como uma das principais fontes de visibilidade.

Um fluxo simplificado pode ser representado assim:

```text
                    ┌──────────────┐
                    │   Firewall   │
                    └──────┬───────┘
                           │
┌──────────────┐           │
│   Windows    │───────────┤
└──────────────┘           │
                           ▼
┌──────────────┐      ┌───────────┐
│    Linux     │─────►│           │
└──────────────┘      │   SIEM    │
                      │           │
┌──────────────┐      └─────┬─────┘
│    EDR       │────────────┤
└──────────────┘            │
                            ▼
                       ┌─────────┐
                       │ ALERTA  │
                       └────┬────┘
                            ▼
                      ┌───────────┐
                      │ SOC N1/N2 │
                      └─────┬─────┘
                            ▼
                       INVESTIGAÇÃO
```

---

# Principais fontes de dados

Um SIEM pode receber informações de praticamente qualquer componente que gere eventos relevantes.

## Sistemas operacionais

### Windows

Exemplos:

```text
Windows Security Events
PowerShell
Sysmon
Windows Defender
Active Directory
```

Eventos importantes podem incluir:

```text
Login
Logout
Falha de autenticação
Criação de usuário
Alteração de privilégios
Execução de processos
Alteração de políticas
```

### Linux

Exemplos:

```text
auth.log
syslog
journald
auditd
sshd
sudo
```

Eventos relevantes:

```text
SSH
sudo
autenticação
criação de usuários
alteração de arquivos
execução de comandos
```

---

# Firewalls

Firewalls podem fornecer informações como:

```text
IP de origem
IP de destino
Porta
Protocolo
Ação
Data/Hora
Interface
Regra aplicada
```

Exemplo:

```text
SRC: 185.XX.XX.XX
DST: 10.0.0.25
PORT: 22
PROTOCOL: TCP
ACTION: DENY
```

Ao correlacionar vários eventos, o SIEM pode identificar padrões de reconhecimento ou tentativa de exploração.

---

# EDR e antivírus

Soluções de endpoint podem enviar eventos relacionados a:

```text
Malware
Processos
PowerShell
Scripts
Arquivos
Conexões de rede
Persistência
Alterações no sistema
```

Exemplo:

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
Download de arquivo externo
     ↓
Execução
```

Essa cadeia pode representar um comportamento suspeito e merece investigação.

---

# Active Directory

O Active Directory é uma fonte extremamente importante para ambientes corporativos.

Eventos interessantes incluem:

```text
Falha de login
Login bem-sucedido
Criação de usuário
Exclusão de usuário
Alteração de senha
Alteração de grupos
Uso de contas privilegiadas
Bloqueio de conta
```

Uma correlação como:

```text
Login suspeito
+
uso de conta privilegiada
+
alteração de grupo administrativo
```

pode representar um possível comprometimento de credenciais.

---

# Aplicações e servidores Web

Logs de aplicações também podem ser enviados para o SIEM.

Exemplo:

```text
GET /login
POST /login
POST /admin
GET /wp-admin
GET /etc/passwd
```

Uma sequência anormal pode indicar:

```text
Reconhecimento
      ↓
Tentativa de exploração
      ↓
Acesso não autorizado
```

Isso é especialmente relevante em ambientes que possuem aplicações expostas à Internet.

---

# Threat Intelligence

O SIEM também pode utilizar informações provenientes de fontes de **Threat Intelligence**.

Exemplos:

```text
IP malicioso
Domínio malicioso
Hash de malware
URL maliciosa
Indicadores de comprometimento (IoC)
```

Exemplo:

```text
Endpoint
   ↓
Conexão para 185.XX.XX.XX
   ↓
SIEM consulta Threat Intelligence
   ↓
IP identificado como malicioso
   ↓
Alerta elevado de severidade
```

A inteligência de ameaças adiciona contexto aos eventos.

---

# Severidade de alertas

Os SIEMs normalmente permitem classificar alertas de acordo com sua severidade.

Um modelo simples:

| Severidade  | Exemplo                           |
| ----------- | --------------------------------- |
| Informativo | Evento normal                     |
| Baixo       | Comportamento incomum             |
| Médio       | Possível atividade maliciosa      |
| Alto        | Forte indício de comprometimento  |
| Crítico     | Incidente grave / impacto elevado |

A classificação pode variar conforme a ferramenta e principalmente conforme o contexto do ambiente.

Uma tentativa de login em um servidor de testes pode ser irrelevante.

A mesma tentativa contra um **Domain Controller** utilizando uma conta privilegiada pode ser extremamente relevante.

> **Severidade não deve ser analisada isoladamente. Contexto importa.**

---

# Falso positivo

Um dos maiores desafios de um SOC é lidar com **falsos positivos**.

Um falso positivo ocorre quando o sistema gera um alerta que parece representar uma ameaça, mas após investigação é identificado como uma atividade legítima.

Exemplo:

```text
Alerta:
Múltiplas tentativas de login
```

Investigação:

```text
Usuário:
joao.silva

Origem:
IP corporativo

Motivo:
Senha alterada recentemente

Resultado:
Atividade legítima
```

O alerta não deve simplesmente ser ignorado.

O analista deve entender **por que a regra disparou**.

Isso permite melhorar a detecção posteriormente.

---

# Falso negativo

O problema oposto é o **falso negativo**.

Nesse caso, uma atividade maliciosa acontece, mas o sistema não gera um alerta.

Exemplo:

```text
Atacante
   ↓
Rouba credencial
   ↓
Realiza login
   ↓
Executa comandos
   ↓
Cria persistência
```

Se o SIEM não possuir regras ou dados suficientes para identificar esse comportamento, o ataque pode passar despercebido.

Por isso, um SOC precisa trabalhar continuamente na evolução das regras de detecção.

---

# Tuning de regras

Uma regra de detecção não deve ser criada e esquecida.

O ambiente muda.

Usuários mudam.

Aplicações mudam.

Servidores mudam.

Processos legítimos mudam.

Por isso existe o **tuning**.

O objetivo é melhorar a qualidade dos alertas.

Exemplo:

```text
Regra inicial:

10 falhas de login em 5 minutos
```

Depois da análise:

```text
Excluir:
- servidores de autenticação conhecidos
- IPs internos específicos
- contas de serviço autorizadas
```

Resultado:

```text
Menos ruído
     ↓
Menos falsos positivos
     ↓
Melhor produtividade
     ↓
Analistas focados em ameaças reais
```

---

# O problema do volume de logs

Um ambiente corporativo pode gerar uma quantidade enorme de eventos.

O desafio não é simplesmente **coletar tudo**.

O desafio é transformar dados em informação útil.

Podemos pensar assim:

```text
Milhões de eventos
       ↓
Processamento
       ↓
Correlação
       ↓
Detecção
       ↓
Centenas de alertas
       ↓
Triagem
       ↓
Poucos incidentes relevantes
```

Um SOC eficiente não é aquele que possui mais alertas.

É aquele que consegue **encontrar ameaças relevantes em meio ao ruído**.

---

# SIEM x Log Management

Embora sejam conceitos relacionados, não são exatamente a mesma coisa.

### Log Management

Foco principal:

```text
Coletar
Armazenar
Pesquisar
Consultar
```

### SIEM

Além dessas funções, busca:

```text
Correlacionar
Detectar
Alertar
Contextualizar
Investigar
```

Uma forma simples de visualizar:

```text
Log Management
       │
       ├── Coleta
       ├── Armazenamento
       └── Pesquisa
       
SIEM
       │
       ├── Coleta
       ├── Normalização
       ├── Correlação
       ├── Detecção
       ├── Alertas
       └── Investigação
```

Na prática, as fronteiras entre essas categorias podem variar conforme o produto.

---

# SIEM x SOAR

Outra confusão comum é entre SIEM e SOAR.

### SIEM

Principalmente:

```text
Coletar
Correlacionar
Detectar
Alertar
Investigar
```

### SOAR

Principalmente:

```text
Orquestrar
Automatizar
Responder
Executar playbooks
```

Exemplo:

```text
SIEM detecta:

IP malicioso
      ↓
Alerta
      ↓
SOAR recebe o alerta
      ↓
Consulta Threat Intelligence
      ↓
Bloqueia IP no Firewall
      ↓
Isola endpoint
      ↓
Abre ticket
      ↓
Notifica SOC
```

O SIEM identifica o problema.

O SOAR pode automatizar parte da resposta.

---

# Principais soluções SIEM

Existem diversas soluções utilizadas no mercado.

Entre as mais conhecidas:

* **Wazuh**
* **Splunk Enterprise Security**
* **Microsoft Sentinel**
* **IBM QRadar**
* **Google Security Operations**
* **Elastic Security**

Cada plataforma possui arquitetura, recursos, custos e casos de uso diferentes.

Neste laboratório, algumas dessas soluções serão estudadas individualmente.

---

# Wazuh

O **Wazuh** é uma plataforma open source bastante utilizada para monitoramento de segurança, detecção e análise de eventos.

Pode trabalhar com:

```text
Endpoints
Servidores
Logs
File Integrity Monitoring
Vulnerabilidades
Detecção de ameaças
Compliance
```

É especialmente interessante para laboratórios e ambientes em que existe necessidade de uma solução com forte capacidade de monitoramento sem depender exclusivamente de soluções comerciais.

---

# Splunk

O **Splunk** é uma plataforma amplamente utilizada para análise de dados e segurança.

No contexto de segurança, pode ser utilizado para:

```text
Coleta de logs
Pesquisa
Correlação
Dashboards
Alertas
Investigação
Threat Intelligence
```

Um dos seus pontos fortes é a capacidade de pesquisar grandes volumes de dados e construir análises personalizadas.

---

# Microsoft Sentinel

O **Microsoft Sentinel** é uma plataforma SIEM baseada em nuvem da Microsoft.

É especialmente relevante em ambientes que utilizam o ecossistema Microsoft.

Pode integrar dados de:

```text
Microsoft Entra ID
Microsoft Defender
Microsoft 365
Azure
Firewalls
Endpoints
Aplicações
Serviços de terceiros
```

Também possui recursos de automação e integração com ferramentas do ecossistema Microsoft.

---

# Exemplo de investigação utilizando SIEM

Imagine o seguinte alerta:

```text
ALERT: Suspicious PowerShell Execution
Severity: High

Host:
PC-FINANCEIRO-023

User:
usuario.financeiro

Process:
powershell.exe

Parent Process:
WINWORD.EXE
```

O analista não deve simplesmente fechar o alerta.

Ele precisa buscar contexto.

### Pergunta 1 — O usuário executou isso?

Verificar:

```text
Quem estava logado?
Qual era o processo?
Qual era o horário?
```

### Pergunta 2 — Qual comando foi executado?

Pesquisar eventos relacionados ao processo.

```text
powershell.exe
```

### Pergunta 3 — Houve conexão externa?

Pesquisar:

```text
Source IP
Destination IP
Destination Port
Domain
URL
```

### Pergunta 4 — Outros computadores apresentam o mesmo comportamento?

Pesquisar o mesmo indicador no ambiente.

```text
Host A
Host B
Host C
Host D
```

### Pergunta 5 — O arquivo ou domínio é conhecido?

Consultar:

```text
Threat Intelligence
Hash
Domain
IP
URL
```

Depois disso, o analista pode classificar o alerta.

```text
Benigno
      ou
Falso positivo
      ou
Incidente
```

---

# Exemplo de investigação de Brute Force

Um alerta pode indicar:

```text
37 failed login attempts
Source IP: 185.XX.XX.XX
Target: DC01
User: administrator
```

O analista pode seguir:

```text
1. Identificar o IP de origem
        ↓
2. Verificar reputação do IP
        ↓
3. Identificar a conta atacada
        ↓
4. Verificar se houve login bem-sucedido
        ↓
5. Verificar outros usuários atacados
        ↓
6. Procurar o mesmo IP no ambiente
        ↓
7. Verificar alterações posteriores
        ↓
8. Classificar o incidente
```

Se houver:

```text
Brute Force
      ↓
Login bem-sucedido
      ↓
Conta privilegiada
      ↓
Alteração de grupo
```

o nível de risco aumenta consideravelmente.

---

# MITRE ATT&CK e SIEM

O **MITRE ATT&CK** pode ser utilizado como referência para mapear comportamentos de ataque.

Por exemplo:

```text
PowerShell
      ↓
MITRE ATT&CK
      ↓
T1059.001 - PowerShell
```

Isso ajuda o SOC a entender **qual técnica está sendo observada**, em vez de apenas visualizar um alerta isolado.

Uma detecção madura pode relacionar:

```text
Evento
  ↓
Regra
  ↓
Técnica MITRE ATT&CK
  ↓
Contexto
  ↓
Severidade
  ↓
Resposta
```

---

# SIEM e MITRE ATT&CK na prática

Uma organização pode criar uma matriz de cobertura:

| Técnica                           | Detecção | Fonte            |
| --------------------------------- | -------- | ---------------- |
| PowerShell                        | Sim      | Windows / Sysmon |
| Brute Force                       | Sim      | Active Directory |
| RDP                               | Sim      | Windows          |
| Credential Dumping                | Parcial  | EDR              |
| Command and Scripting Interpreter | Sim      | Endpoint         |
| Persistence                       | Parcial  | Endpoint         |

Isso permite identificar **lacunas de detecção**.

Não basta possuir um SIEM.

É necessário saber **o que ele consegue detectar e o que ainda passa despercebido**.

---

# Métricas relacionadas ao SIEM

Alguns indicadores podem ajudar a avaliar a eficiência do monitoramento.

### Volume de eventos

```text
Eventos recebidos por período
```

### Volume de alertas

```text
Alertas gerados por período
```

### Taxa de falsos positivos

```text
Alertas benignos / total de alertas
```

### MTTD

**Mean Time to Detect**

Tempo médio para detectar uma ameaça.

### MTTR

**Mean Time to Respond/Recover**

Tempo médio para responder ou recuperar de um incidente, conforme a definição adotada pela organização.

Essas métricas ajudam a avaliar a eficiência operacional do SOC.

---

# O que um analista SOC procura no SIEM?

Durante uma investigação, algumas perguntas são recorrentes:

```text
O que aconteceu?

Quando aconteceu?

Onde aconteceu?

Qual usuário estava envolvido?

Qual máquina foi afetada?

Qual IP iniciou a atividade?

Qual foi o destino?

Qual processo foi executado?

Houve persistência?

Houve movimentação lateral?

Houve exfiltração?

O comportamento ainda está acontecendo?
```

Essas perguntas ajudam a transformar um simples alerta em uma investigação estruturada.

---

# O verdadeiro valor do SIEM

O valor de um SIEM não está apenas em armazenar milhões de logs.

O valor está na capacidade de transformar:

```text
Eventos
   ↓
Contexto
   ↓
Correlação
   ↓
Detecção
   ↓
Investigação
   ↓
Decisão
```

Um log isolado pode não significar nada.

Dez eventos relacionados podem contar uma história completamente diferente.

É essa capacidade de **construir uma linha do tempo e encontrar relações entre eventos** que torna o SIEM tão importante para um SOC.

---

# Resumo

| Conceito            | Função                                                    |
| ------------------- | --------------------------------------------------------- |
| Log                 | Registro de uma atividade                                 |
| Evento              | Ocorrência registrada em um sistema                       |
| SIEM                | Centraliza, correlaciona e analisa eventos de segurança   |
| Correlação          | Relaciona eventos diferentes                              |
| Regra               | Define uma condição de detecção                           |
| Alerta              | Indica uma possível ameaça                                |
| Falso positivo      | Alerta que não representa uma ameaça real                 |
| Falso negativo      | Ameaça que não foi detectada                              |
| Tuning              | Ajuste das regras para melhorar a detecção                |
| Threat Intelligence | Adiciona contexto sobre ameaças                           |
| SOAR                | Automatiza e orquestra respostas                          |
| MITRE ATT&CK        | Framework para mapear técnicas e comportamentos de ataque |

---

# Fluxo completo

```text
                    FONTES DE LOG
                         │
        ┌────────────────┼────────────────┐
        │                │                │
     Windows           Linux          Firewall
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                    ┌─────────┐
                    │   SIEM  │
                    └────┬────┘
                         │
                  Normalização
                         │
                    Correlação
                         │
                    Detecção
                         │
                    ┌────▼────┐
                    │ ALERTA  │
                    └────┬────┘
                         │
                    SOC / N1
                         │
                      Triagem
                         │
                  ┌──────┴──────┐
                  │             │
              Benigno        Suspeito
                                │
                           Investigação
                                │
                         ┌──────▼──────┐
                         │   Incidente │
                         └──────┬──────┘
                                │
                             Resposta
                                │
                             Contenção
                                │
                            Recuperação
                                │
                            Lições aprendidas
```

---

## Conclusão

Um SIEM é uma das peças centrais de um ambiente de monitoramento de segurança.

Ele permite que o SOC deixe de analisar sistemas de forma isolada e passe a trabalhar com uma **visão correlacionada do ambiente**.

Mas a ferramenta, por si só, não garante segurança.

Um SIEM mal configurado pode gerar milhares de alertas inúteis.

Um SIEM bem configurado, alimentado por boas fontes de dados e acompanhado por analistas capacitados, pode revelar a sequência de eventos que antecede um incidente.

Por isso, trabalhar com SIEM não significa apenas **“ver alertas”**.

Significa entender:

```text
O que aconteceu
      ↓
Por que aconteceu
      ↓
Se é legítimo
      ↓
Se representa uma ameaça
      ↓
Qual o impacto
      ↓
O que precisa ser feito
```

É nesse ponto que o SIEM deixa de ser apenas uma ferramenta de logs e passa a ser uma peça fundamental da operação de um **Security Operations Center (SOC)**.
