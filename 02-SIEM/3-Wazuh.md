# Wazuh

## Visão geral

**Wazuh** é uma plataforma open source de segurança utilizada para **monitoramento, detecção de ameaças, análise de eventos, resposta e proteção de endpoints**.

Dentro de um SOC, o Wazuh pode atuar como uma das principais fontes de visibilidade sobre servidores, estações de trabalho e outros ativos monitorados.

Ele consegue coletar informações de diferentes sistemas e transformar esses dados em eventos que podem ser analisados por regras de segurança.

Uma visão simplificada:

```text
Endpoint
   ↓
Wazuh Agent
   ↓
Wazuh Server
   ↓
Análise
   ↓
Regras
   ↓
Alertas
   ↓
SOC
   ↓
Investigação
```

O Wazuh também possui recursos para:

```text
Monitoramento de logs
Detecção de ameaças
File Integrity Monitoring
Detecção de vulnerabilidades
Monitoramento de configuração
Análise de segurança
Compliance
Inventário
Resposta ativa
```

---

# Onde o Wazuh se encaixa em um SOC?

O Wazuh pode ser utilizado para aumentar a visibilidade dos endpoints e servidores.

Um cenário simples:

```text
                 ┌──────────────┐
                 │ Windows      │
                 │ Workstations │
                 └──────┬───────┘
                        │
                        ▼
                 ┌──────────────┐
                 │ Wazuh Agent  │
                 └──────┬───────┘
                        │
                        │
┌──────────────┐        │        ┌──────────────┐
│ Linux Server │────────┼────────│ Cloud / VM   │
└──────────────┘        │        └──────────────┘
                        ▼
                ┌────────────────┐
                │  Wazuh Server  │
                └───────┬────────┘
                        │
                        ▼
                ┌────────────────┐
                │   Detecção     │
                │   e Alertas    │
                └───────┬────────┘
                        │
                        ▼
                   ┌─────────┐
                   │   SOC   │
                   └─────────┘
```

O objetivo é centralizar a visibilidade.

---

# Arquitetura do Wazuh

Uma implantação tradicional possui alguns componentes principais.

```text
Wazuh Agent
     │
     ▼
Wazuh Server
     │
     ├── Analysis Engine
     │
     └── Rules / Decoders
     │
     ▼
Wazuh Indexer
     │
     ▼
Wazuh Dashboard
```

Cada componente possui uma função específica.

---

# Wazuh Agent

O **Wazuh Agent** é instalado no endpoint ou servidor que será monitorado.

Exemplos:

```text
Windows
Linux
macOS
```

O agente coleta informações do sistema e envia os eventos para o ambiente Wazuh.

Entre os dados que podem ser monitorados:

```text
Logs
Processos
Arquivos
Usuários
Configurações
Eventos de segurança
Vulnerabilidades
Integridade de arquivos
```

Exemplo:

```text
Windows
   ↓
Security Event Log
   ↓
Wazuh Agent
   ↓
Wazuh Server
```

---

# Wazuh Server

O Wazuh Server é responsável por receber e analisar os dados enviados pelos agentes.

Ele participa de etapas como:

```text
Recepção
   ↓
Decodificação
   ↓
Análise
   ↓
Aplicação de regras
   ↓
Geração de alertas
```

É uma das partes centrais da arquitetura.

---

# Wazuh Indexer

O **Wazuh Indexer** é utilizado para armazenar e indexar os dados de segurança.

Isso permite realizar pesquisas sobre grandes quantidades de eventos.

Por exemplo:

```text
Buscar todos os eventos:

source.ip = 185.XX.XX.XX
```

Ou:

```text
Buscar:

event.action = failed_login
```

O armazenamento estruturado facilita a investigação.

---

# Wazuh Dashboard

O Dashboard fornece uma interface para visualizar os dados coletados.

O analista pode utilizar a interface para:

```text
Visualizar alertas
Pesquisar eventos
Consultar agentes
Analisar vulnerabilidades
Monitorar integridade
Criar visualizações
Investigar atividades
```

Uma visão simplificada:

```text
Wazuh Agent
      ↓
Wazuh Server
      ↓
Wazuh Indexer
      ↓
Wazuh Dashboard
      ↓
Analista SOC
```

---

# Fluxo de um evento

Imagine uma tentativa de login SSH.

No servidor Linux:

```text
Failed password for root
```

O fluxo pode ser:

```text
SSH
 ↓
auth.log
 ↓
Wazuh Agent
 ↓
Wazuh Server
 ↓
Decoder
 ↓
Rule
 ↓
Alert
 ↓
Dashboard
 ↓
Analista
```

Esse fluxo é fundamental para entender como o Wazuh funciona.

---

# Decoders

Antes que uma regra consiga analisar corretamente um evento, o Wazuh precisa entender sua estrutura.

Os **decoders** são utilizados para interpretar os logs e extrair campos relevantes.

Um log:

```text
Failed password for root from 185.XX.XX.XX
```

pode ser interpretado como:

```text
user = root
source.ip = 185.XX.XX.XX
event = failed_login
```

Isso permite que regras trabalhem com informações estruturadas.

---

# Rules

As regras definem condições que podem gerar alertas.

Exemplo conceitual:

```text
IF

failed_login

AND

same_source_ip

AND

count > 10

THEN

generate_alert
```

A regra pode classificar o evento de acordo com sua importância.

---

# Níveis de alerta

O Wazuh utiliza níveis de severidade para classificar eventos.

De forma geral:

```text
Baixo
   ↓
Médio
   ↓
Alto
   ↓
Crítico
```

Um evento de baixo nível pode representar apenas uma atividade relevante.

Um evento de alta severidade pode indicar um comportamento com maior potencial de impacto.

É importante lembrar:

> **A severidade do alerta não substitui a análise do contexto.**

---

# Exemplo de alerta

Um alerta pode apresentar informações como:

```text
Rule:
Possible SSH Brute Force

Level:
10

Agent:
linux-server-01

Source IP:
185.XX.XX.XX

User:
root

Event:
Multiple failed login attempts
```

O analista pode então iniciar a investigação.

---

# File Integrity Monitoring — FIM

Um dos recursos importantes do Wazuh é o **File Integrity Monitoring (FIM)**.

O objetivo é identificar alterações em arquivos monitorados.

Por exemplo:

```text
/etc/passwd
/etc/shadow
/etc/ssh/sshd_config
```

ou arquivos críticos de uma aplicação.

O sistema pode detectar:

```text
Arquivo criado
Arquivo modificado
Arquivo removido
Permissão alterada
Hash alterado
```

---

# Exemplo de FIM

Imagine:

```text
/etc/ssh/sshd_config
```

Estado original:

```text
Hash A
```

Depois:

```text
Arquivo alterado

Hash B
```

O Wazuh pode gerar:

```text
FILE MODIFIED

File:
 /etc/ssh/sshd_config

Previous hash:
Hash A

Current hash:
Hash B
```

Isso não significa automaticamente que ocorreu um ataque.

O analista precisa investigar:

```text
Quem alterou?
Quando?
Por quê?
Qual processo realizou a alteração?
A mudança era autorizada?
```

---

# Monitoramento de logs

O Wazuh pode monitorar diferentes fontes de logs.

Exemplos:

```text
SSH
Apache
Nginx
Authentication
System logs
Application logs
Windows Event Logs
```

Um servidor web pode gerar:

```text
404
403
500
Login
Request
User-Agent
IP
URL
```

Esses eventos podem ser analisados pelo Wazuh.

---

# Detecção de Brute Force

Um dos casos mais conhecidos de uso do Wazuh é identificar múltiplas tentativas de autenticação.

Exemplo:

```text
20:01 Failed login
20:02 Failed login
20:02 Failed login
20:03 Failed login
20:03 Failed login
20:04 Successful login
```

O Wazuh pode correlacionar os eventos e gerar um alerta.

Fluxo:

```text
Failed Logins
      ↓
Correlação
      ↓
Threshold
      ↓
Detecção
      ↓
Alert
```

---

# Investigando um Brute Force

O analista deve evitar simplesmente bloquear o IP sem entender o contexto.

Uma investigação pode começar com:

```text
1. Qual é o IP de origem?

2. Qual usuário foi atacado?

3. Quantas tentativas ocorreram?

4. Houve login bem-sucedido?

5. Qual servidor foi afetado?

6. O mesmo IP atacou outros servidores?

7. A conta possui privilégios?

8. Houve atividades posteriores?
```

Se houver:

```text
Brute Force
   ↓
Successful Login
   ↓
Privileged Account
   ↓
Suspicious Commands
```

o incidente merece prioridade maior.

---

# Vulnerability Detection

O Wazuh também possui recursos para identificar vulnerabilidades nos ativos monitorados.

O objetivo é identificar softwares e componentes que possam possuir vulnerabilidades conhecidas.

Exemplo:

```text
Host:
WEB-01

Software:
Apache

Version:
X.Y.Z

Known vulnerability:
CVE-XXXX-XXXX
```

O SOC pode utilizar essas informações para priorizar correções.

---

# Vulnerabilidade não significa comprometimento

Essa distinção é importante.

Encontrar uma vulnerabilidade significa:

```text
Existe uma condição potencialmente explorável.
```

Não significa:

```text
O sistema foi comprometido.
```

Por exemplo:

```text
Vulnerability detected
        ≠
Successful exploitation
```

O SOC precisa trabalhar com as duas informações separadamente.

---

# Security Configuration Assessment

O Wazuh também pode ajudar a verificar configurações de segurança.

Exemplos:

```text
Configurações de sistema
Políticas
Hardening
Controles de segurança
Boas práticas
```

A ideia é identificar desvios de uma configuração esperada.

Por exemplo:

```text
SSH root login
     ↓
PermitRootLogin yes
     ↓
Configuração de risco
```

Isso pode gerar uma recomendação para endurecimento do servidor.

---

# Rootcheck

O Wazuh possui mecanismos para identificar possíveis indicadores associados a comportamentos suspeitos no sistema.

Isso pode incluir verificações relacionadas a:

```text
Arquivos suspeitos
Processos
Configurações
Rootkits
Comportamentos anormais
```

O resultado deve ser tratado como indicador para investigação e não como prova isolada de comprometimento.

---

# Inventário

O Wazuh pode ajudar a manter informações sobre os ativos monitorados.

Por exemplo:

```text
Hostname
Sistema operacional
Pacotes
Software
Processos
Interfaces
Informações do sistema
```

Isso pode ser útil para o SOC porque permite responder:

> Quais ativos estão sendo monitorados e qual é o estado deles?

---

# Active Response

Um recurso importante do Wazuh é o **Active Response**.

Ele permite executar ações automatizadas após determinadas condições serem identificadas.

Exemplo:

```text
Brute Force
     ↓
Regra acionada
     ↓
Active Response
     ↓
Bloqueio do IP
```

Outro exemplo:

```text
Malicious IP detected
       ↓
Alert
       ↓
Active Response
       ↓
Firewall rule
       ↓
Connection blocked
```

---

# Cuidado com automação

Automação é poderosa, mas pode ser perigosa quando configurada incorretamente.

Imagine:

```text
Regra mal configurada
       ↓
Falso positivo
       ↓
Active Response
       ↓
Bloqueio de IP legítimo
       ↓
Indisponibilidade
```

Por isso, ações automáticas devem considerar:

```text
Precisão da regra
Impacto
Escopo
Tempo de bloqueio
Possibilidade de rollback
```

Em ambientes críticos, nem todo alerta deve resultar automaticamente em bloqueio.

---

# Wazuh e MITRE ATT&CK

O Wazuh pode ajudar a relacionar detecções com técnicas do **MITRE ATT&CK**.

Por exemplo:

```text
PowerShell
    ↓
T1059.001
```

Ou:

```text
Brute Force
    ↓
T1110
```

Isso ajuda o SOC a entender o comportamento do atacante em termos de técnicas conhecidas.

---

# Wazuh e Threat Intelligence

O Wazuh também pode ser integrado a fontes externas de inteligência.

Imagine:

```text
Endpoint
   ↓
Conexão para IP
   ↓
Wazuh
   ↓
Threat Intelligence
   ↓
IP conhecido como malicioso
```

O evento passa a possuir mais contexto.

Isso pode aumentar a prioridade da investigação.

---

# Wazuh em um ambiente Linux

Um exemplo simples:

```text
┌───────────────────────┐
│     Linux Server      │
├───────────────────────┤
│ SSH                   │
│ Nginx                 │
│ System Logs           │
│ File System           │
└───────────┬───────────┘
            │
            ▼
     Wazuh Agent
            │
            ▼
     Wazuh Server
            │
            ▼
        Detection
            │
            ▼
        Dashboard
```

O SOC pode monitorar:

```text
SSH
Nginx
Arquivos
Usuários
Processos
Autenticação
```

---

# Wazuh em Windows

No Windows, o agente pode coletar informações como:

```text
Windows Event Logs
PowerShell
Defender
Processos
Arquivos
Usuários
Configurações
```

Um caso interessante:

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
Network Connection
     ↓
Suspicious File
```

Essa cadeia pode ser utilizada em uma investigação.

---

# Exemplo de investigação no Wazuh

Imagine o seguinte alerta:

```text
ALERT

Rule:
Suspicious PowerShell Activity

Level:
12

Agent:
PC-FINANCEIRO-07

User:
usuario01
```

O analista começa pela triagem:

```text
1. Quem é o usuário?

2. Qual máquina foi afetada?

3. Qual comando PowerShell foi executado?

4. Qual processo iniciou o PowerShell?

5. Houve conexão externa?

6. Qual domínio/IP foi acessado?

7. O arquivo possui hash conhecido?

8. Outros endpoints executaram o mesmo comando?
```

Depois, pode ser criada uma timeline:

```text
09:31
Usuário recebeu documento

09:33
Documento aberto

09:33
PowerShell iniciado

09:34
Conexão externa

09:34
Arquivo criado

09:35
Processo executado
```

Agora o alerta possui contexto.

---

# Wazuh e resposta a incidentes

O Wazuh pode participar de diferentes etapas da resposta:

```text
Detecção
   ↓
Triagem
   ↓
Investigação
   ↓
Contenção
   ↓
Remediação
   ↓
Monitoramento
```

Por exemplo:

```text
Detecção:
IP malicioso

↓

Investigação:
Endpoint afetado

↓

Contenção:
Bloqueio da conexão

↓

Remediação:
Remoção do artefato

↓

Monitoramento:
Verificar novas tentativas
```

---

# Wazuh não substitui um SOC

Essa é uma distinção importante.

O Wazuh é uma ferramenta.

Um SOC é uma operação.

```text
Wazuh
    +
SIEM
    +
Analistas
    +
Processos
    +
Playbooks
    +
Threat Intelligence
    +
Monitoramento
    =
Operação de Segurança
```

Mesmo com uma excelente ferramenta, ainda é necessário:

```text
Definir regras
Investigar alertas
Reduzir falsos positivos
Criar procedimentos
Manter cobertura
Avaliar riscos
Responder incidentes
```

---

# Wazuh como laboratório de SOC

Uma das vantagens do Wazuh é a possibilidade de montar um ambiente de laboratório para estudar conceitos de segurança.

Um laboratório pode conter:

```text
┌─────────────────────┐
│   Wazuh Server      │
└──────────┬──────────┘
           │
     ┌─────┴─────┐
     │           │
     ▼           ▼
 Windows       Linux
 Agent         Agent
```

Depois, podem ser simulados eventos como:

```text
Tentativas de SSH
Falhas de login
Criação de usuário
Alteração de arquivos
Execução de PowerShell
Alteração de privilégios
Conexões suspeitas
```

O objetivo não é apenas gerar alertas.

É entender:

```text
Evento
 ↓
Regra
 ↓
Alerta
 ↓
Investigação
 ↓
Conclusão
```

---

# Exemplo de laboratório

### Cenário

Um servidor Linux possui SSH exposto.

```text
Servidor:
192.168.10.20

Serviço:
SSH

Porta:
22
```

O atacante realiza:

```text
10 tentativas
20 tentativas
30 tentativas
```

O Wazuh identifica:

```text
Multiple authentication failures
```

O SOC então:

```text
1. Identifica o IP
2. Verifica a quantidade de tentativas
3. Pesquisa o histórico
4. Verifica se houve sucesso
5. Avalia a conta atacada
6. Verifica comandos posteriores
7. Classifica o alerta
8. Executa contenção se necessário
```

---

# Exemplo de regra personalizada

O Wazuh permite trabalhar com regras personalizadas.

Um exemplo conceitual:

```xml
<rule id="100001" level="10">
    <if_matched_sid>5710</if_matched_sid>
    <description>
        Multiple SSH authentication failures
    </description>
</rule>
```

A estrutura exata deve ser adaptada à versão e aos eventos existentes no ambiente.

O ponto importante é entender a lógica:

```text
Evento
   ↓
Regra base
   ↓
Condição adicional
   ↓
Nova detecção
   ↓
Alerta
```

---

# Tuning no Wazuh

Uma instalação inicial pode gerar bastante ruído.

Exemplo:

```text
1000 alertas
     ↓
600 benignos
300 esperados
100 relevantes
```

O objetivo do tuning é aumentar a proporção de alertas realmente úteis.

Pode ser necessário:

```text
Ajustar regras
Excluir comportamentos conhecidos
Alterar níveis
Criar correlações
Adicionar contexto
Criar exceções controladas
```

Resultado esperado:

```text
Menos ruído
      ↓
Melhor triagem
      ↓
Menor fadiga
      ↓
Maior foco em ameaças reais
```

---

# Wazuh e o conceito de visibilidade

Uma das perguntas mais importantes durante a implantação é:

> **O que eu consigo enxergar?**

Por exemplo:

```text
Windows
✓

Linux
✓

Firewall
✓

Endpoints
✓

Cloud
?
```

Se uma parte importante do ambiente não gera dados para o Wazuh, existe uma lacuna de visibilidade.

Por isso, a maturidade não deve ser medida apenas pela quantidade de agentes instalados.

É necessário avaliar:

```text
Cobertura
Qualidade dos logs
Qualidade das regras
Taxa de falsos positivos
Capacidade de investigação
Tempo de detecção
```

---

# Boas práticas

## 1. Defina o que precisa ser monitorado

Não instale agentes sem uma estratégia.

Comece pelos ativos mais importantes:

```text
Domain Controllers
Servidores críticos
Endpoints administrativos
Servidores Web
Bancos de dados
Sistemas expostos à Internet
```

---

## 2. Monitore a saúde dos agentes

Verifique:

```text
Agente conectado?
Último evento recebido?
Existe atraso?
Existe erro?
```

Um agente offline significa potencial perda de visibilidade.

---

## 3. Ajuste as regras

Não aceite milhares de alertas sem analisar o motivo.

Faça:

```text
Monitoramento
   ↓
Análise
   ↓
Tuning
   ↓
Teste
   ↓
Monitoramento novamente
```

---

## 4. Proteja o ambiente Wazuh

O próprio Wazuh é um componente crítico.

Portanto:

```text
Atualizações
Controle de acesso
Credenciais fortes
TLS
Backup
Monitoramento
Segmentação de rede
```

também devem ser considerados.

---

## 5. Documente as detecções

Para cada regra importante, registre:

```text
Objetivo
Fonte
Condição
Severidade
MITRE ATT&CK
Possíveis falsos positivos
Procedimento de investigação
Resposta esperada
```

Isso facilita a operação do SOC.

---

# Checklist de implantação

```text
[ ] Wazuh Server configurado

[ ] Wazuh Indexer configurado

[ ] Dashboard configurado

[ ] Agentes instalados

[ ] Comunicação validada

[ ] Logs chegando corretamente

[ ] Horários sincronizados

[ ] Regras testadas

[ ] Alertas revisados

[ ] FIM configurado

[ ] Vulnerability Detection configurado

[ ] Active Response avaliado

[ ] Backup configurado

[ ] Controle de acesso configurado

[ ] Monitoramento dos agentes configurado

[ ] Procedimentos de investigação documentados
```

---

# Wazuh x SIEM

O Wazuh pode desempenhar funções associadas a uma plataforma SIEM/XDR, dependendo da arquitetura e dos recursos utilizados.

A relação pode ser entendida assim:

```text
SIEM
 │
 ├── Coleta
 ├── Correlação
 ├── Detecção
 ├── Alertas
 └── Investigação
```

O Wazuh adiciona recursos específicos de endpoint e segurança, como:

```text
FIM
Vulnerability Detection
Configuration Assessment
Inventory
Active Response
Endpoint Monitoring
```

Por isso, é importante analisar o produto pelo conjunto de capacidades, e não apenas pelo nome da categoria.

---

# O que estudar depois do Wazuh?

Depois de entender a arquitetura básica, uma sequência interessante de estudo é:

```text
01. Instalação
        ↓
02. Agentes
        ↓
03. Coleta de logs
        ↓
04. Decoders
        ↓
05. Rules
        ↓
06. Alertas
        ↓
07. FIM
        ↓
08. Vulnerabilidades
        ↓
09. Active Response
        ↓
10. MITRE ATT&CK
        ↓
11. Tuning
        ↓
12. Investigação
```

Isso transforma o aprendizado da ferramenta em uma experiência mais próxima da operação de um SOC.

---

# Resumo

| Componente/Recurso      | Função                            |
| ----------------------- | --------------------------------- |
| Wazuh Agent             | Coleta dados dos endpoints        |
| Wazuh Server            | Recebe e analisa eventos          |
| Wazuh Indexer           | Armazena e indexa dados           |
| Wazuh Dashboard         | Interface de análise              |
| Decoder                 | Interpreta eventos                |
| Rules                   | Detectam condições                |
| Alert                   | Indica uma possível ameaça        |
| FIM                     | Monitora alterações em arquivos   |
| Vulnerability Detection | Identifica vulnerabilidades       |
| SCA                     | Avalia configurações de segurança |
| Active Response         | Automatiza ações de resposta      |
| MITRE ATT&CK            | Relaciona detecções a técnicas    |
| Threat Intelligence     | Adiciona contexto sobre ameaças   |

---

# Conclusão

O Wazuh é mais interessante quando utilizado como parte de uma estratégia de monitoramento, e não apenas como uma ferramenta que gera alertas.

A lógica fundamental continua sendo:

```text
Coletar
   ↓
Entender
   ↓
Correlacionar
   ↓
Detectar
   ↓
Investigar
   ↓
Responder
```

Um bom laboratório com Wazuh permite reproduzir grande parte desse ciclo.

O ponto principal para um analista não é saber apenas onde clicar no Dashboard.

É conseguir olhar para um alerta e perguntar:

```text
O que gerou isso?

Qual ativo está envolvido?

Qual usuário está envolvido?

Esse comportamento é esperado?

Existe evidência de comprometimento?

O mesmo indicador aparece em outros ativos?

Qual técnica de ataque pode estar relacionada?

Qual é o impacto?

Qual deve ser a próxima ação?
```

Quando essas perguntas começam a fazer parte da análise, o Wazuh deixa de ser apenas uma ferramenta de monitoramento e passa a ser uma plataforma para **desenvolver a mentalidade operacional de um analista de SOC**.
