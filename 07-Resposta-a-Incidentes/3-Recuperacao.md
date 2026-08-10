# Recuperação

## Introdução

A recuperação é a etapa em que o ambiente começa a voltar à operação depois que a ameaça foi contida e erradicada.

É também uma das etapas mais subestimadas de um incidente.

Um sistema pode estar:

```text
Online
```

e ainda assim não estar:

```text
Seguro
```

Por isso, recuperação não significa apenas restaurar um backup ou reiniciar um servidor.

Significa:

> **restabelecer os serviços de forma controlada, validar sua integridade e acompanhar o ambiente até que exista confiança suficiente para retornar à operação normal.**

O fluxo pode ser representado assim:

```text
Contenção
    ↓
Erradicação
    ↓
Preparação
    ↓
Restauração
    ↓
Validação
    ↓
Monitoramento
    ↓
Operação Normal
```

---

# Objetivos da recuperação

A recuperação busca:

```text
Restaurar serviços
Recuperar dados
Validar sistemas
Restabelecer acessos
Confirmar integridade
Reduzir risco de reincidência
Monitorar o ambiente
Retornar à operação normal
```

O foco deixa de ser apenas:

> **"A ameaça foi removida?"**

e passa a ser:

> **"Podemos confiar novamente neste ambiente?"**

---

# Recuperação não é erradicação

As duas etapas possuem objetivos diferentes.

### Erradicação

```text
Remove a causa do incidente.
```

### Recuperação

```text
Restaura o ambiente afetado.
```

Exemplo:

```text
Servidor comprometido
       ↓
Malware removido
       ↓
Vulnerabilidade corrigida
       ↓
Sistema reconstruído
       ↓
Backup restaurado
       ↓
Servidor validado
       ↓
Produção
```

A limpeza acontece na erradicação.

A volta à operação acontece na recuperação.

---

# Antes de recuperar

Não existe vantagem em colocar um sistema novamente em produção enquanto ainda existem dúvidas básicas sobre o comprometimento.

Antes da recuperação, validar:

```text
[ ] A ameaça foi erradicada?
[ ] Persistência foi removida?
[ ] Vulnerabilidade foi corrigida?
[ ] Credenciais foram protegidas?
[ ] IOC não apresenta novas ocorrências?
[ ] Backup é confiável?
[ ] Sistema foi validado?
[ ] Dependências estão funcionando?
```

Se a resposta for não para pontos críticos, a recuperação deve ser reavaliada.

---

# Critério de recuperação

A decisão deve considerar:

```text
Risco residual
+
Integridade
+
Disponibilidade
+
Impacto no negócio
```

Exemplo:

```text
Risco:
Baixo

Integridade:
Validada

Backup:
Confiável

Monitoramento:
Ativo

Resultado:
Pode iniciar recuperação
```

---

# Inventário dos sistemas afetados

Antes de restaurar, identificar:

```text
Servidor
Endpoint
Banco de dados
Aplicação
Conta
Storage
Cloud Resource
Network Device
```

E classificar:

```text
Crítico
Alto
Médio
Baixo
```

Isso ajuda a definir a ordem de recuperação.

---

# Priorização

Nem todos os sistemas precisam voltar ao mesmo tempo.

Um exemplo:

```text
Prioridade 1
Identidade / DNS / Rede

Prioridade 2
Banco de Dados

Prioridade 3
Aplicações críticas

Prioridade 4
Serviços internos

Prioridade 5
Endpoints
```

A ordem real depende da arquitetura da organização.

O princípio é:

> **Restaurar primeiro aquilo que permite restaurar o restante.**

---

# RTO

RTO significa:

> **Recovery Time Objective**

Representa o tempo máximo aceitável para restaurar um serviço.

Exemplo:

```text
Sistema:
ERP

RTO:
4 horas
```

Isso significa que o processo de recuperação deve buscar retornar o serviço dentro desse objetivo.

---

# RPO

RPO significa:

> **Recovery Point Objective**

Representa a quantidade máxima aceitável de perda de dados.

Exemplo:

```text
RPO:
1 hora
```

Se ocorrer uma falha às 15:00, o objetivo pode ser recuperar dados até aproximadamente 14:00.

---

# RTO x RPO

```text
RTO
"Quanto tempo podemos ficar fora?"

RPO
"Quanto dado podemos perder?"
```

Exemplo:

```text
Sistema crítico

RTO: 2h
RPO: 15min
```

Isso exige uma estratégia de recuperação mais robusta.

---

# Backup

Backup é uma das principais ferramentas de recuperação.

Mas:

> **Backup existente não significa backup confiável.**

É necessário verificar:

```text
Data
Integridade
Origem
Retenção
Consistência
Acessibilidade
```

---

# Backup comprometido

Em um incidente de ransomware:

```text
Produção
   ↓
Ransomware
   ↓
Backup
```

Se o atacante também comprometeu o backup:

```text
Backup ≠ Recuperação garantida
```

Por isso, durante a recuperação, verificar:

```text
Quando foi criado?
Foi afetado?
Está isolado?
Pode ser restaurado?
Foi validado?
```

---

# Backup limpo

Idealmente, utilizar um ponto de recuperação anterior ao comprometimento.

Exemplo:

```text
01:00 Backup
02:00 Backup
03:00 Backup
04:00 Comprometimento
05:00 Detecção
```

Um backup de:

```text
03:00
```

pode ser preferível a um backup posterior ao comprometimento.

Mas isso depende da análise do incidente e da integridade do backup.

---

# Restaurar sem validar

Um erro comum:

```text
Backup
 ↓
Restore
 ↓
Produção
```

O processo correto deve incluir:

```text
Backup
 ↓
Restore
 ↓
Validação
 ↓
Hardening
 ↓
Testes
 ↓
Produção
```

---

# Ambiente de recuperação

Quando possível, restaurar primeiro em ambiente controlado.

Exemplo:

```text
Backup
   ↓
Recovery Environment
   ↓
Validation
   ↓
Security Checks
   ↓
Production
```

Isso permite verificar:

```text
Integridade
Malware
Configuração
Aplicação
Dependências
```

antes de expor o sistema novamente.

---

# Validação de integridade

Depois da restauração:

```text
Arquivo
Hash
Configuração
Sistema
Banco
Aplicação
```

podem ser comparados com referências confiáveis.

Exemplo:

```text
Backup
SHA256: abc123

Restored
SHA256: abc123

Resultado:
Integridade confirmada
```

---

# Validação do sistema operacional

Depois de reconstruir ou restaurar um servidor:

```text
Verificar:
```

```text
Usuários
Serviços
Processos
Portas
Firewall
Pacotes
Atualizações
Logs
Cron
Systemd
SSH
```

No Windows:

```text
Services
Scheduled Tasks
Users
Registry
Defender / EDR
Event Logs
Firewall
```

---

# Validação de aplicações

Um servidor pode estar seguro e ainda assim a aplicação estar quebrada.

Testar:

```text
Login
API
Banco
Upload
Download
Integrações
Autenticação
Fila
Jobs
```

Exemplo:

```text
Servidor:
Online

Aplicação:
Online

Banco:
Online

API:
Erro 500
```

Recuperação ainda não terminou.

---

# Banco de dados

Em uma recuperação envolvendo banco:

```text
Restore
   ↓
Integrity Check
   ↓
Application Test
   ↓
Data Validation
```

Verificar:

```text
Tabelas
Índices
Usuários
Permissões
Integridade
Quantidade de registros
Últimos dados
```

---

# Validação de DNS

Se o serviço depende de DNS:

```text
Domain
 ↓
DNS
 ↓
Application
```

verificar:

```text
A
AAAA
CNAME
MX
TXT
```

Também confirmar se os registros não foram alterados durante o incidente.

---

# Certificados e TLS

Depois da recuperação:

```text
HTTPS
 ↓
Certificate
 ↓
Validation
```

Verificar:

```text
Validade
Domínio
Chain
Configuração
TLS
```

Um sistema pode voltar ao ar com certificado expirado ou configuração insegura.

---

# Recuperação de identidade

Se contas foram comprometidas:

```text
Reset Password
Revogar Sessões
Rotacionar Tokens
Reconfigurar MFA
Revisar Privilégios
```

Depois:

```text
Login
 ↓
MFA
 ↓
Access
 ↓
Monitoring
```

---

# Recuperação de endpoints

Para um endpoint comprometido:

```text
Isolamento
   ↓
Erradicação
   ↓
Reimage
   ↓
Patch
   ↓
EDR
   ↓
Configuração
   ↓
Validação
   ↓
Rede
```

Não é recomendável simplesmente remover o isolamento sem verificar a integridade do sistema.

---

# Recuperação de servidor

Um processo possível:

```text
Servidor comprometido
        ↓
Preservar evidências
        ↓
Rebuild / Restore
        ↓
Patch
        ↓
Hardening
        ↓
Configuração
        ↓
Testes
        ↓
Monitoramento
        ↓
Produção
```

---

# Hardening antes do retorno

Antes de colocar novamente em produção:

```text
MFA
Least Privilege
Firewall
Updates
EDR
Logging
Secure Configuration
Network Segmentation
```

O incidente deve ser uma oportunidade para eliminar fragilidades que ajudaram no comprometimento.

---

# Monitoramento pós-recuperação

O sistema não deve simplesmente voltar para:

```text
Monitoramento normal
```

imediatamente.

Durante um período definido, aumentar a observação:

```text
Logs
EDR
DNS
Firewall
Authentication
Network
Processes
Files
```

A ideia é identificar rapidamente qualquer sinal de retorno da ameaça.

---

# IOC Monitoring

Se o incidente envolveu:

```text
IP
Domain
Hash
URL
```

esses indicadores devem continuar sendo monitorados.

Exemplo:

```text
IOC:
malicious-domain.com

Recovery:
No activity

Monitoring:
No activity
```

Se voltar a aparecer:

```text
IOC detected
   ↓
Reinvestigation
```

---

# Baseline pós-incidente

Depois da recuperação, criar ou atualizar o baseline.

Exemplo:

```text
Antes:
Servidor → 443

Após:
Servidor → 443

Novo comportamento:
Servidor → 4444
```

Um desvio pode indicar:

```text
Persistência
Erro de configuração
Novo ataque
```

---

# Monitoramento aumentado

Dependendo do incidente:

```text
Normal:
100 eventos/h

Pós-incidente:
500 eventos/h
```

Isso pode ser intencional durante o período de monitoramento.

O objetivo é aumentar a visibilidade.

---

# Recuperação gradual

Evitar colocar todos os sistemas de volta simultaneamente quando o incidente ainda possui risco residual.

Exemplo:

```text
Servidor 01
   ↓
Validar
   ↓
Servidor 02
   ↓
Validar
   ↓
Servidor 03
```

Isso facilita identificar qual etapa introduziu um problema.

---

# Canary / sistema piloto

Em ambientes maiores, pode ser útil recuperar primeiro um pequeno conjunto de sistemas.

Exemplo:

```text
10.000 endpoints
       ↓
100 endpoints
       ↓
Monitorar
       ↓
Sem anomalias
       ↓
Expandir
```

Essa estratégia reduz o risco de uma falha em escala.

---

# Recuperação e dependências

Um sistema raramente funciona sozinho.

Exemplo:

```text
Aplicação
   ↓
API
   ↓
Database
   ↓
DNS
   ↓
Identity
```

Se o banco estiver indisponível:

```text
Aplicação ≠ operacional
```

Por isso, mapear dependências antes do retorno.

---

# Testes funcionais

Antes do retorno:

```text
[ ] Serviço inicia
[ ] Usuário consegue autenticar
[ ] Aplicação responde
[ ] API responde
[ ] Banco funciona
[ ] DNS resolve
[ ] HTTPS funciona
[ ] Logs estão sendo gerados
[ ] EDR está ativo
[ ] Firewall está ativo
```

---

# Testes de segurança

Além dos testes funcionais:

```text
[ ] Vulnerabilidade corrigida
[ ] IOC não encontrado
[ ] Persistência removida
[ ] Contas desconhecidas removidas
[ ] Portas inesperadas fechadas
[ ] Processos suspeitos inexistentes
[ ] Comunicação C2 inexistente
```

Um sistema pode passar no teste funcional e falhar no teste de segurança.

---

# Recuperação de ransomware

Um fluxo possível:

```text
Ransomware
    ↓
Contenção
    ↓
Erradicação
    ↓
Identificação do backup confiável
    ↓
Restore
    ↓
Validação
    ↓
Hardening
    ↓
Monitoramento
    ↓
Produção
```

Não restaurar indiscriminadamente.

Primeiro garantir:

```text
Backup confiável
+
Ambiente limpo
+
Vetor corrigido
```

---

# Recuperação de conta comprometida

Exemplo:

```text
user01
```

Fluxo:

```text
Conta comprometida
      ↓
Disable
      ↓
Investigar
      ↓
Reset Password
      ↓
Revoke Sessions
      ↓
Revoke Tokens
      ↓
MFA
      ↓
Review Permissions
      ↓
Enable
      ↓
Monitor
```

O retorno da conta deve ocorrer somente depois de controlar o risco.

---

# Recuperação após phishing

Após um ataque de phishing:

```text
Remover mensagem
       ↓
Resetar credenciais
       ↓
Revogar sessões
       ↓
Validar MFA
       ↓
Investigar logins
       ↓
Verificar regras de e-mail
       ↓
Restaurar acesso
       ↓
Monitorar
```

Também verificar se o atacante criou:

```text
Inbox Rules
Forwarding Rules
Delegations
OAuth Applications
```

---

# Recuperação de exfiltração

Se houve exfiltração:

```text
Bloquear canal
   ↓
Revogar credenciais
   ↓
Corrigir vetor
   ↓
Validar sistemas
   ↓
Monitorar destinos
```

Também avaliar:

```text
Quais dados foram acessados?
Qual volume?
Qual período?
Qual conta?
Qual sistema?
```

Essas respostas podem ser importantes para etapas de compliance e comunicação.

---

# Recuperação em Cloud

Em ambientes cloud:

```text
Instance
Container
Bucket
Database
IAM
API
```

podem precisar de recuperação.

Ações possíveis:

```text
Rebuild Instance
Rotate Keys
Restore Snapshot
Review IAM
Review Security Groups
Validate Logs
Enable Monitoring
```

---

# Recuperação de container

Em ambientes Docker/Kubernetes, pode ser mais seguro reconstruir o workload a partir de uma imagem confiável.

Exemplo:

```text
Container comprometido
        ↓
Preservar evidências
        ↓
Remover workload
        ↓
Atualizar imagem
        ↓
Recriar container
        ↓
Validar
```

Evitar simplesmente:

```text
restart
```

quando existe suspeita de comprometimento.

Reiniciar não significa reconstruir um ambiente confiável.

---

# Recuperação de VPS

Para uma VPS comprometida:

```text
Investigar
   ↓
Backup confiável
   ↓
Rebuild
   ↓
Patch
   ↓
Hardening
   ↓
Restore
   ↓
SSL
   ↓
DNS
   ↓
Monitoramento
```

Dependendo do comprometimento, reconstruir a VPS pode ser mais seguro do que tentar remover manualmente todos os artefatos.

---

# Recuperação de website

Depois de um comprometimento web:

```text
Backup confiável
       ↓
Rebuild
       ↓
Atualizar CMS
       ↓
Atualizar Plugins
       ↓
Revisar Usuários
       ↓
Revisar Arquivos
       ↓
Revisar Banco
       ↓
Validar
       ↓
Produção
```

Também verificar:

```text
.htaccess
Cron
Uploads
PHP
Database
Admin Users
API Keys
```

---

# Recuperação e disponibilidade

Segurança não deve ignorar o negócio.

Exemplo:

```text
Sistema crítico
```

pode ter:

```text
RTO baixo
```

e exigir:

```text
Failover
Redundância
Backup quente
DR Site
```

A estratégia de recuperação precisa estar alinhada ao impacto que a indisponibilidade causa.

---

# Disaster Recovery

Disaster Recovery, ou DR, é o conjunto de processos e recursos utilizados para recuperar serviços após uma interrupção significativa.

Pode envolver:

```text
Backup
Replication
Failover
Secondary Site
Cloud
Snapshots
Redundancy
```

Um SOC pode participar identificando:

```text
Quando recuperar?
O que recuperar?
O que está seguro?
```

---

# Recuperação x continuidade

São conceitos relacionados.

### Recuperação

```text
Restaurar o ambiente.
```

### Continuidade

```text
Manter o negócio funcionando durante ou após uma interrupção.
```

Exemplo:

```text
Produção indisponível
       ↓
Failover
       ↓
Operação continua
       ↓
Recuperação da produção
```

---

# Validação final

Antes de considerar o sistema recuperado:

```text
Segurança
+
Disponibilidade
+
Integridade
+
Monitoramento
```

devem estar validados.

Exemplo:

```text
Sistema:
ERP

Availability:
OK

Security:
OK

Data Integrity:
OK

Monitoring:
OK

IOC:
0 hits

Status:
Recovered
```

---

# Período de observação

Depois do retorno, estabelecer um período de monitoramento reforçado.

Durante esse período:

```text
Aumentar visibilidade
Monitorar IOCs
Monitorar autenticação
Monitorar processos
Monitorar rede
Monitorar alterações
```

Se algo voltar a aparecer:

```text
Anomalia
 ↓
Investigação
```

---

# Critérios de sucesso

Uma recuperação pode ser considerada bem-sucedida quando:

```text
[✓] Serviço restaurado
[✓] Dados íntegros
[✓] Vulnerabilidade corrigida
[✓] Credenciais protegidas
[✓] IOC sem atividade
[✓] Monitoramento ativo
[✓] Usuários conseguem operar
[✓] Dependências funcionando
[✓] Segurança validada
[✓] Risco residual aceitável
```

---

# Checklist de Recuperação

```text
[ ] Ameaça erradicada?

[ ] Causa raiz tratada?

[ ] Backup validado?

[ ] Backup é anterior ao comprometimento?

[ ] Sistema foi reconstruído quando necessário?

[ ] Patches aplicados?

[ ] Hardening realizado?

[ ] Credenciais rotacionadas?

[ ] Tokens revogados?

[ ] MFA validado?

[ ] Configurações revisadas?

[ ] Dependências identificadas?

[ ] Banco restaurado?

[ ] Aplicação validada?

[ ] DNS validado?

[ ] TLS/SSL validado?

[ ] Firewall validado?

[ ] EDR ativo?

[ ] Logs funcionando?

[ ] IOC pesquisado novamente?

[ ] Threat Hunting realizado?

[ ] Monitoramento reforçado?

[ ] RTO atendido?

[ ] RPO atendido?

[ ] Serviço retornou?

[ ] Usuários conseguem operar?

[ ] Segurança validada?

[ ] Risco residual aceito?

[ ] Recuperação documentada?
```

---

# Fluxo de Recuperação

```text
                    ERRADICAÇÃO
                         │
                         ▼
                 Ambiente Preparado
                         │
                         ▼
                  Backup Confiável?
                    │          │
                   NÃO        SIM
                    │          │
                    ▼          ▼
                 Reavaliar   Restore
                               │
                               ▼
                         Validação Técnica
                               │
                               ▼
                         Validação Segurança
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
                 Falhou                Aprovado
                    │                     │
                    ▼                     ▼
               Reinvestigar          Produção
                                          │
                                          ▼
                                  Monitoramento
                                          │
                                          ▼
                                    Operação Normal
```

---

# Exemplo completo

## Incidente

```text
Ransomware comprometeu 12 endpoints.
```

### Contenção

```text
12 endpoints isolados
Conta comprometida desabilitada
C2 bloqueado
Backups protegidos
```

### Erradicação

```text
Malware removido
Persistência identificada e removida
Credenciais rotacionadas
Vetor inicial corrigido
Endpoints reconstruídos
```

### Recuperação

```text
Backup validado
Endpoints restaurados
EDR instalado
Patches aplicados
Configurações validadas
Sistemas testados
Monitoramento reforçado
```

Resultado:

```text
12/12 endpoints recuperados
0 novos IOCs
0 novas comunicações C2
Serviços operacionais
Monitoramento ativo
```

---

# Quando a recuperação falha

Nem todo retorno à operação significa sucesso.

Exemplo:

```text
Servidor restaurado
      ↓
Produção
      ↓
Novo C2 detectado
```

Isso pode indicar:

```text
Persistência não removida
Backup comprometido
Vetor não corrigido
Outro host comprometido
Credencial ainda válida
```

Nesse cenário:

```text
Recuperação
   ↓
Interrompida
   ↓
Nova investigação
```

Voltar uma etapa não significa fracasso.

Significa que a evidência mudou a avaliação do risco.

---

# Recuperação não encerra o incidente

Mesmo após o serviço voltar:

```text
Investigação
Monitoramento
Threat Hunting
Documentação
```

podem continuar.

O encerramento só deve ocorrer quando:

```text
Ameaça controlada
+
Ambiente recuperado
+
Risco residual avaliado
+
Evidências documentadas
```

---

# Recuperação em uma frase

Recuperar não é simplesmente colocar o sistema novamente online.

É garantir que ele volte:

```text
Íntegro
Seguro
Monitorado
Operacional
```

A sequência ideal é:

```text
Conter
   ↓
Erradicar
   ↓
Restaurar
   ↓
Validar
   ↓
Monitorar
   ↓
Retornar à operação
```

> **O objetivo da recuperação não é apenas fazer o serviço voltar. É fazer o serviço voltar sem devolver ao atacante o mesmo caminho que ele utilizou para entrar.**
