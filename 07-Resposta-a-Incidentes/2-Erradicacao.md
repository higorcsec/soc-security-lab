# Erradicação

## Introdução

A contenção impede que o incidente continue crescendo.

A erradicação vai um passo além:

> **remover a ameaça, a persistência e a causa do comprometimento.**

Um endpoint isolado continua comprometido.

Uma conta bloqueada pode continuar tendo credenciais expostas.

Um servidor reinstalado pode voltar a ser comprometido se a vulnerabilidade continuar aberta.

Por isso, erradicar não significa apenas remover o malware.

Significa eliminar as condições que permitem que o atacante permaneça ou volte ao ambiente.

```text
Contenção
     ↓
Entender a causa
     ↓
Remover ameaça
     ↓
Eliminar persistência
     ↓
Corrigir vetor de ataque
     ↓
Validar
     ↓
Recuperação
```

---

# O objetivo da erradicação

A erradicação busca remover:

```text
Malware
Backdoors
Webshells
Persistência
Contas maliciosas
Credenciais comprometidas
Processos maliciosos
Arquivos maliciosos
Tarefas agendadas
Serviços criados pelo atacante
Chaves SSH comprometidas
Tokens
Vulnerabilidades exploradas
Configurações inseguras
```

O objetivo é fazer com que:

> **o mesmo caminho utilizado pelo atacante deixe de funcionar.**

---

# Contenção não é erradicação

Essa diferença é fundamental.

Imagine:

```text
Malware
   ↓
Endpoint isolado
```

A ameaça está contida.

Mas ainda existe:

```text
Malware
Persistência
Credencial comprometida
Vulnerabilidade
```

Portanto:

```text
Isolar
≠
Erradicar
```

Outro exemplo:

```text
Conta comprometida
   ↓
Conta desabilitada
```

Isso contém o acesso.

Mas ainda pode existir:

```text
Senha comprometida
Token válido
Sessão ativa
Chave API
```

A erradicação precisa tratar a causa.

---

# Antes de erradicar

Um erro comum é começar a apagar tudo assim que o incidente é identificado.

Isso pode destruir evidências importantes.

Antes da remoção, quando possível:

```text
Identificar causa
Preservar evidências
Definir escopo
Identificar persistência
Identificar credenciais afetadas
Identificar sistemas afetados
Definir plano de remoção
```

A pergunta principal é:

> **"Sabemos o suficiente para remover a ameaça sem perder a capacidade de investigar o incidente?"**

---

# Identificação da causa raiz

Um incidente normalmente possui uma origem.

Exemplo:

```text
Phishing
   ↓
Credencial roubada
   ↓
Login
   ↓
Acesso ao servidor
   ↓
Malware
```

Se remover apenas:

```text
Malware
```

mas não:

```text
Credencial comprometida
```

o atacante pode voltar.

Por isso:

```text
Sintoma
≠
Causa
```

---

# Exemplo

Imagine:

```text
Servidor comprometido
```

A equipe encontra:

```text
webshell.php
```

Remover o arquivo pode parecer suficiente.

Mas a investigação encontra:

```text
Aplicação vulnerável
Senha exposta
Usuário privilegiado
```

A erradicação precisa tratar todos esses pontos.

Caso contrário:

```text
Servidor limpo
   ↓
Mesmo vetor
   ↓
Novo comprometimento
```

---

# Remoção de malware

Quando o malware é identificado, a remoção pode envolver:

```text
Excluir arquivo malicioso
Remover processo
Remover persistência
Remover serviços
Remover tarefas
Remover alterações de configuração
```

Dependendo da gravidade, pode ser mais seguro:

```text
Reinstalar
Reimage
Restore
```

em vez de tentar limpar manualmente.

---

# Reimage

Reimage significa reconstruir o sistema a partir de uma imagem confiável.

Exemplo:

```text
Sistema comprometido
       ↓
Preservar evidências
       ↓
Reinstalar
       ↓
Aplicar patches
       ↓
Configurar
       ↓
Validar
```

É uma alternativa importante quando:

```text
Rootkit
Backdoor
Comprometimento profundo
Persistência desconhecida
Integridade do sistema não confiável
```

Quando não é possível garantir que o sistema foi completamente limpo, reconstruí-lo pode ser mais confiável.

---

# Persistência

Uma das principais preocupações durante a erradicação.

O atacante pode criar mecanismos para retornar ao ambiente.

Exemplos:

```text
Scheduled Task
Service
Registry Run Key
Startup Folder
Cron
Systemd Service
Webshell
SSH Key
API Token
OAuth Application
```

Remover o malware sem remover a persistência pode resultar em:

```text
Sistema aparentemente limpo
        ↓
Persistência continua ativa
        ↓
Recomprometimento
```

---

# Persistência em Windows

Exemplos que podem ser investigados:

```text
Services
Scheduled Tasks
Registry Run Keys
Startup Folder
WMI
PowerShell
Local Accounts
```

O objetivo não é apenas encontrar arquivos.

É descobrir:

> **Como o atacante conseguiria executar novamente o código?**

---

# Persistência em Linux

Possíveis locais de investigação:

```text
Cron
Systemd
SSH Authorized Keys
Shell Profiles
Startup Scripts
Services
Webshells
SUID/SGID
```

Exemplo:

```text
~/.ssh/authorized_keys
```

Uma chave adicionada pelo atacante pode permitir acesso mesmo depois da remoção do malware.

---

# Webshell

Em servidores web:

```text
Apache
Nginx
PHP
WordPress
Laravel
```

um atacante pode inserir um webshell.

Exemplo conceitual:

```text
Website
   ↓
Arquivo comprometido
   ↓
Webshell
   ↓
Remote Command Execution
```

A erradicação pode exigir:

```text
Remover webshell
Comparar arquivos
Validar código
Atualizar aplicação
Corrigir vulnerabilidade
Rotacionar credenciais
```

---

# WordPress comprometido

Em um WordPress comprometido, não basta procurar apenas um arquivo suspeito.

Investigar:

```text
Plugins
Themes
Uploads
wp-config.php
Usuários administrativos
Cron
Database
.htaccess
Arquivos PHP
```

E principalmente:

```text
Como o atacante conseguiu escrever no servidor?
```

---

# Vulnerabilidade explorada

Se a origem foi uma vulnerabilidade:

```text
Aplicação vulnerável
        ↓
Exploração
        ↓
Comprometimento
```

a erradicação deve incluir:

```text
Atualização
Patch
Correção de configuração
Remoção do componente vulnerável
```

Caso contrário:

```text
Malware removido
       ↓
Vulnerabilidade continua
       ↓
Novo ataque
```

---

# Patching

O patch deve ser aplicado de forma controlada.

Exemplo:

```text
Identificar vulnerabilidade
        ↓
Validar correção
        ↓
Testar
        ↓
Aplicar patch
        ↓
Validar serviço
```

Em ambientes críticos, considerar:

```text
Backup
Janela de manutenção
Rollback
Teste
Dependências
```

---

# Credenciais comprometidas

Se o atacante teve acesso a uma conta:

```text
user01
```

a erradicação pode exigir:

```text
Reset Password
Revoke Sessions
Revoke Tokens
Rotate API Keys
Rotate SSH Keys
Reconfigure MFA
Review Privileges
```

Trocar apenas a senha pode não ser suficiente.

---

# Privilégios

Uma conta comprometida com:

```text
Administrator
Root
Domain Admin
Cloud Admin
```

representa risco elevado.

Após o incidente:

```text
Revisar privilégios
Remover permissões desnecessárias
Rotacionar credenciais
Investigar uso da conta
```

A ideia é reduzir o acesso ao mínimo necessário.

---

# Contas criadas pelo atacante

Durante a investigação podem surgir contas inesperadas.

Exemplo:

```text
backup-admin
support2
svc-temp
```

A existência da conta não prova atividade maliciosa.

Investigar:

```text
Data de criação
Quem criou
Último login
Privilégios
Origem
Atividades
```

Se confirmada como maliciosa:

```text
Disable
Remove
Document
```

---

# Chaves SSH

Em ambientes Linux, uma chave SSH comprometida pode permitir acesso sem senha.

Investigar:

```text
~/.ssh/authorized_keys
```

e contas privilegiadas.

Se houver comprometimento:

```text
Remover chave
Gerar nova chave
Revogar chave antiga
Auditar acessos
```

---

# Tokens e API Keys

Em cloud e aplicações modernas, credenciais não são apenas senhas.

Podem existir:

```text
API Keys
Access Tokens
Refresh Tokens
Service Accounts
OAuth Credentials
Cloud Access Keys
```

Se comprometidos:

```text
Revoke
Rotate
Replace
Audit
```

---

# Malware em múltiplos hosts

Se o mesmo malware foi encontrado em:

```text
WS-01
WS-02
WS-03
WS-04
```

não basta limpar um único computador.

A erradicação precisa considerar:

```text
IOC
Hash
Filename
Persistence
Initial Access
Lateral Movement
```

O objetivo é impedir que o mesmo mecanismo continue espalhando o malware.

---

# Erradicação de ransomware

Em ransomware, a prioridade é:

```text
Conter
   ↓
Determinar escopo
   ↓
Preservar evidências
   ↓
Eliminar persistência
   ↓
Erradicar malware
   ↓
Corrigir vetor inicial
```

Não assumir que:

```text
Máquina descriptografada
=
Ambiente limpo
```

O atacante pode ter mantido:

```text
Backdoor
Conta
Credencial
Persistência
```

---

# Backdoors

Backdoor é qualquer mecanismo que permita acesso não autorizado ou persistência fora do fluxo esperado.

Pode aparecer como:

```text
Conta
Serviço
Webshell
SSH Key
Scheduled Task
Script
API Token
```

Durante a erradicação:

```text
Identificar
Validar
Remover
Testar
Monitorar
```

---

# Erradicação de phishing

Phishing não termina quando o e-mail é removido.

Se o usuário clicou e entregou credenciais:

```text
Remover e-mail
       ↓
Resetar senha
       ↓
Revogar sessões
       ↓
Validar MFA
       ↓
Investigar login
       ↓
Pesquisar atividade adicional
```

A erradicação precisa tratar o comprometimento causado pelo phishing.

---

# Account Compromise

Em um comprometimento de conta:

```text
Conta
 ↓
Credencial roubada
 ↓
Login atacante
```

A erradicação pode envolver:

```text
Reset Password
Revoke Sessions
Revoke Tokens
Rotate Keys
Review MFA
Review Privileges
Review Login History
```

---

# Exfiltração

Se houve exfiltração:

```text
Dados
 ↓
Destino externo
```

a erradicação deve considerar:

```text
Credencial utilizada
Processo responsável
Canal utilizado
Malware
Persistência
Conta comprometida
Vulnerabilidade
```

O objetivo é impedir novas transferências.

---

# Erradicação de C2

Quando um endpoint estava conectado a C2:

```text
Endpoint
   ↓
C2
```

não basta bloquear:

```text
IP
```

Também investigar:

```text
Processo
Persistência
Malware
Domínios relacionados
Credenciais
Outros hosts
```

O C2 pode mudar de endereço.

A causa precisa ser eliminada.

---

# Validação da erradicação

Depois da limpeza:

```text
A ameaça realmente desapareceu?
```

Verificar:

```text
IOC
Hash
Process
Network
DNS
Authentication
Persistence
Files
Services
Tasks
```

Exemplo:

```text
Antes:

Host → C2
Host → Malware

Depois:

Host → sem C2
Host → sem Malware
Host → sem persistência
```

---

# Revarredura

Após a erradicação:

```text
IOC Search
```

deve ser repetido.

Exemplo:

```text
IOC:
malicious-domain.com

Before:
23 hits

After:
0 hits
```

Isso não prova sozinho que o ambiente está seguro.

Mas fornece uma evidência importante de que a contenção e erradicação tiveram efeito.

---

# Threat Hunting após erradicação

Uma boa prática é continuar procurando sinais relacionados.

Exemplo:

```text
IOC removido
      ↓
Pesquisar comportamento relacionado
      ↓
Encontrar novo host
      ↓
Investigar
```

Isso evita declarar o incidente encerrado cedo demais.

---

# Validação por múltiplas fontes

Não depender de uma única ferramenta.

Exemplo:

```text
EDR
 ↓
0 ocorrências

SIEM
 ↓
0 ocorrências

DNS
 ↓
0 ocorrências

Firewall
 ↓
0 conexões
```

Quanto mais fontes confirmarem o resultado, maior a confiança.

---

# Erradicação e evidências

A limpeza deve ser documentada.

Registrar:

```text
Data
Hora
Host
Ação
Responsável
Evidência
Resultado
```

Exemplo:

```text
09/08/2026 15:20

Host:
WS-023

Action:
Removed malicious scheduled task.

Evidence:
EDR + Windows Event Logs.

Result:
No further execution detected.
```

---

# Erradicação e automação

Algumas ações podem ser automatizadas:

```text
IOC Blocking
Account Disable
Token Revocation
Endpoint Isolation
Malware Quarantine
```

Mas ações destrutivas devem exigir controle adequado.

Exemplo:

```text
Automatic:
Isolate Endpoint

Manual Approval:
Delete Production Server
```

O nível de automação deve acompanhar o risco.

---

# Quando reconstruir o sistema?

Pode ser preferível reconstruir quando:

```text
Integridade não pode ser garantida
Rootkit suspeito
Comprometimento profundo
Persistência desconhecida
Sistema crítico comprometido
Grande quantidade de alterações
```

Nesse cenário:

```text
Preservar evidências
       ↓
Rebuild
       ↓
Patch
       ↓
Hardening
       ↓
Restore
       ↓
Monitoramento
```

---

# Hardening após erradicação

A correção deve reduzir a possibilidade de reincidência.

Exemplos:

```text
MFA
Least Privilege
Firewall
Network Segmentation
Patch Management
EDR
Logging
Backup
Application Security
```

Erradicar sem melhorar a segurança pode deixar o ambiente vulnerável ao mesmo ataque.

---

# Root Cause

Ao final da erradicação, deve existir uma resposta para:

> **Como o atacante entrou?**

Exemplos:

```text
Phishing
Vulnerability
Weak Password
Exposed Service
Stolen Credential
Misconfiguration
Supply Chain
```

E:

> **Por que conseguiu avançar?**

Exemplo:

```text
Sem MFA
Privilégio excessivo
Rede plana
Logs insuficientes
Sistema sem patch
```

---

# Exemplo completo

## Cenário

```text
Servidor web comprometido.
```

Investigação:

```text
Vulnerabilidade
     ↓
Webshell
     ↓
Credential Theft
     ↓
C2
```

### Contenção

```text
Servidor isolado
C2 bloqueado
Credenciais desabilitadas
```

### Erradicação

```text
Webshell removido
Aplicação atualizada
Credenciais rotacionadas
Persistência removida
Servidor reconstruído
```

### Validação

```text
IOC Search
DNS Search
EDR
Authentication Logs
Web Logs
```

Resultado:

```text
Sem novas evidências de comprometimento.
```

---

# Critérios para considerar a erradicação concluída

```text
[✓] Malware removido
[✓] Persistência removida
[✓] Backdoors removidos
[✓] Credenciais comprometidas rotacionadas
[✓] Tokens revogados
[✓] Vulnerabilidade corrigida
[✓] Contas maliciosas removidas
[✓] IOC pesquisado novamente
[✓] Outros hosts investigados
[✓] Threat Hunting realizado
[✓] Sistema validado
[✓] Evidências documentadas
```

A conclusão da erradicação permite avançar para:

```text
RECUPERAÇÃO
```

---

# Checklist de Erradicação

```text
[ ] Causa raiz identificada?

[ ] Vetor inicial identificado?

[ ] Malware identificado?

[ ] Persistência identificada?

[ ] Backdoors identificados?

[ ] Contas comprometidas identificadas?

[ ] Credenciais rotacionadas?

[ ] Sessões revogadas?

[ ] Tokens revogados?

[ ] API Keys rotacionadas?

[ ] SSH Keys revisadas?

[ ] Vulnerabilidade corrigida?

[ ] Patches aplicados?

[ ] Configurações inseguras corrigidas?

[ ] Arquivos maliciosos removidos?

[ ] Processos maliciosos removidos?

[ ] Serviços maliciosos removidos?

[ ] Scheduled Tasks / Cron revisados?

[ ] Webshells removidos?

[ ] Outros hosts pesquisados?

[ ] IOC pesquisado novamente?

[ ] Threat Hunting realizado?

[ ] Sistema reconstruído quando necessário?

[ ] Evidências preservadas?

[ ] Ações documentadas?

[ ] Ambiente validado?
```

---

# Fluxo de Erradicação

```text
                    INCIDENTE CONTIDO
                           │
                           ▼
                    Identificar Causa
                           │
                           ▼
                    Identificar Escopo
                           │
                           ▼
                 Identificar Persistência
                           │
                           ▼
                  Preservar Evidências
                           │
                           ▼
                    Remover Ameaça
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
       Malware         Credenciais      Vulnerabilidade
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                       Hardening
                           │
                           ▼
                       Revarredura
                           │
                           ▼
                     Threat Hunting
                           │
                           ▼
                       Validação
                           │
                           ▼
                       Recuperação
```

---

# O objetivo final

Uma erradicação bem executada não termina quando:

```text
"o malware foi apagado."
```

Ela termina quando existe confiança razoável de que:

```text
A ameaça foi removida
+
A persistência foi eliminada
+
As credenciais foram protegidas
+
O vetor foi corrigido
+
O ambiente foi validado
```

O verdadeiro objetivo da erradicação é impedir que o incidente volte a acontecer pelo mesmo caminho.

> **Conter é parar o ataque. Erradicar é remover as condições que permitem que ele continue ou volte.**
