# Account Compromise

## Introdução

Uma conta comprometida ocorre quando um terceiro obtém acesso às credenciais, sessão ou mecanismos de autenticação de um usuário sem autorização.

O comprometimento pode acontecer por diferentes caminhos:

```text
Phishing
Brute Force
Password Spraying
Credential Stuffing
Malware
Vazamento de credenciais
Roubo de sessão
MFA Fatigue
Engenharia Social
```

O problema para o SOC começa depois que o atacante consegue autenticar.

Um login válido pode parecer completamente legítimo para um sistema.

```text
Usuário legítimo
      ↓
Login válido
```

versus:

```text
Atacante
      ↓
Credencial válida
      ↓
Login válido
```

Para o sistema de autenticação, os dois eventos podem ser semelhantes.

Por isso, a investigação precisa sair da pergunta:

> **"A senha estava correta?"**

e passar para:

> **"Esse comportamento era esperado para essa identidade?"**

---

# Como uma conta é comprometida

Um cenário comum:

```text
Phishing
   ↓
Credencial capturada
   ↓
Login
   ↓
MFA
   ↓
Acesso
```

Outro:

```text
Vazamento externo
   ↓
Credencial reutilizada
   ↓
Credential Stuffing
   ↓
Login
```

Também existe:

```text
Malware
   ↓
Roubo de credenciais
   ↓
Cookies / Tokens
   ↓
Acesso
```

O método utilizado para obter a conta é importante porque pode indicar onde procurar outros sinais do incidente.

---

# Credencial válida não significa usuário legítimo

Esse é um dos principais conceitos para investigação de identidade.

Considere:

```text
08:00
Usuário → São Paulo
```

Depois:

```text
08:20
Mesma conta → outro país
```

Isso pode ser suspeito.

Mas não significa automaticamente comprometimento.

Pode existir:

```text
VPN
Proxy
Cloud service
Viagem
NAT
```

Por isso, geolocalização é apenas um indicador.

O SOC precisa correlacionar:

```text
Localização
+
Dispositivo
+
IP
+
Horário
+
MFA
+
Aplicação
+
Comportamento
```

---

# Indicadores de comprometimento

Alguns sinais comuns:

```text
Login de localização incomum
Login fora do horário habitual
Novo dispositivo
IP desconhecido
Múltiplas falhas antes do sucesso
MFA inesperado
Alteração de senha
Alteração de MFA
Criação de sessão suspeita
Acesso a recursos incomuns
Download anormal
Alteração de permissões
```

Um único indicador raramente é suficiente.

A combinação aumenta a confiança.

---

# Impossible Travel

Um cenário conhecido:

```text
10:00
Login → São Paulo

10:20
Login → Londres
```

Fisicamente, o intervalo pode ser incompatível com uma viagem normal.

Isso pode gerar:

```text
Impossible Travel
```

Mas o alerta precisa ser analisado.

Possíveis explicações:

```text
VPN
Proxy
Cloud
IP corporativo
Geolocalização incorreta
```

Portanto:

> **Impossible Travel é um indicador, não uma prova.**

---

# Login fora do padrão

Imagine:

```text
Usuário:
higor

Padrão:
08:00 → 18:00

Evento:
03:17
```

Isso merece investigação.

Mas novamente:

```text
03:17
```

não significa automaticamente ataque.

O usuário pode estar trabalhando.

A pergunta correta é:

> **"Esse acesso combina com o comportamento conhecido dessa identidade?"**

---

# Novo dispositivo

Um novo dispositivo pode representar:

```text
Novo computador
Novo navegador
Novo sistema operacional
Novo aplicativo
Novo fingerprint
```

Exemplo:

```text
Conta
   ↓
Dispositivo conhecido
   ↓
Login normal
```

Depois:

```text
Conta
   ↓
Dispositivo nunca visto
   ↓
IP desconhecido
   ↓
Acesso a recurso sensível
```

A combinação é muito mais relevante.

---

# MFA

Multi-Factor Authentication reduz significativamente o risco de comprometimento baseado apenas em senha.

Mas MFA não elimina o risco.

O atacante pode tentar:

```text
Roubar sessão
Roubar token
Phishing de MFA
MFA Fatigue
Social Engineering
```

---

# MFA Fatigue

Um atacante pode provocar várias solicitações de MFA:

```text
Tentativa
   ↓
MFA
   ↓
Negado

Tentativa
   ↓
MFA
   ↓
Negado

Tentativa
   ↓
MFA
   ↓
Usuário aceita
```

O atacante pode então obter acesso.

Por isso, múltiplas solicitações de MFA inesperadas são um indicador relevante.

---

# Roubo de sessão

Nem todo comprometimento exige descobrir a senha.

Um atacante pode obter:

```text
Session Cookie
Token
Access Token
Refresh Token
```

e utilizar a sessão existente.

Um possível cenário:

```text
Malware
   ↓
Roubo de sessão
   ↓
Token
   ↓
Acesso ao serviço
```

Nesse caso:

```text
Login
```

pode nem apresentar uma falha de autenticação tradicional.

---

# OAuth e aplicações autorizadas

Em ambientes cloud, uma conta pode ser comprometida através da autorização de uma aplicação maliciosa.

Exemplo:

```text
Usuário
   ↓
Autoriza aplicação
   ↓
Aplicação recebe permissões
   ↓
Acesso a dados
```

Durante a investigação, o SOC deve verificar:

```text
Aplicações autorizadas
Permissões concedidas
Tokens
Consentimentos
Data da autorização
Usuário responsável
```

---

# Account Compromise em Active Directory

Em ambientes Windows, uma conta comprometida pode ser utilizada para:

```text
RDP
SMB
WinRM
PowerShell
Serviços
Aplicações internas
```

O risco aumenta quando a conta possui privilégios elevados.

Exemplo:

```text
Usuário comum
   ↓
Credencial comprometida
   ↓
Acesso inicial
```

versus:

```text
Domain Admin
   ↓
Credencial comprometida
   ↓
Controle do ambiente
```

Por isso, a criticidade da identidade deve fazer parte da triagem.

---

# Conta privilegiada

Contas administrativas exigem atenção especial.

Exemplos:

```text
Administrator
Domain Admin
Root
Cloud Admin
Security Admin
Database Admin
```

Uma atividade anormal realizada por uma conta privilegiada pode ter impacto muito maior.

Um alerta pode considerar:

```text
Conta privilegiada
+
IP novo
+
horário incomum
+
novo dispositivo
```

e receber prioridade elevada.

---

# Account Compromise e Brute Force

Os dois incidentes podem estar relacionados.

Exemplo:

```text
Falhas
   ↓
Falhas
   ↓
Falhas
   ↓
Login bem-sucedido
```

Isso pode indicar:

```text
Brute Force
     ↓
Account Compromise
```

Mas também pode acontecer:

```text
Credential Leak
     ↓
Login direto
     ↓
Account Compromise
```

Nesse caso, não haverá necessariamente uma grande quantidade de falhas.

---

# Investigação baseada em timeline

Uma investigação eficiente deve reconstruir a sequência.

Exemplo:

```text
08:12
Phishing recebido

08:18
Usuário acessa link

08:20
Credencial utilizada

08:21
Login de novo IP

08:23
MFA aprovado

08:27
Nova sessão criada

08:35
Arquivos acessados

08:41
Download de dados
```

Agora existe uma narrativa.

O SOC consegue identificar:

```text
Vetor inicial
        ↓
Comprometimento
        ↓
Acesso
        ↓
Atividade pós-comprometimento
```

---

# O que investigar

Quando uma conta é suspeita, analisar:

```text
Identidade
IP
Dispositivo
Localização
Horário
MFA
Sessões
Aplicações
Recursos acessados
Alterações realizadas
Downloads
Permissões
```

Depois expandir:

```text
Outras contas
Outros dispositivos
Outros IPs
Outros serviços
```

---

# O que aconteceu depois do login?

Essa é uma das perguntas mais importantes.

Imagine:

```text
Login suspeito
```

O alerta sozinho não determina o impacto.

Agora:

```text
Login suspeito
   ↓
Acesso a e-mail
   ↓
Download de documentos
   ↓
Criação de regra de encaminhamento
```

O risco aumenta significativamente.

Outro cenário:

```text
Login
   ↓
RDP
   ↓
PowerShell
   ↓
Discovery
```

Agora existe possibilidade de comprometimento do endpoint.

---

# Regras de encaminhamento de e-mail

Em ambientes corporativos, uma conta comprometida pode ser utilizada para criar regras automáticas.

Exemplo:

```text
E-mail recebido
      ↓
Regra criada
      ↓
Encaminhamento externo
```

Isso pode permitir que o atacante acompanhe comunicações mesmo depois que o login suspeito desapareça.

Durante a investigação, verificar:

```text
Regras criadas
Regras alteradas
Encaminhamentos
Delegações
Caixas compartilhadas
```

---

# Alteração de MFA

Uma conta comprometida pode ser usada para alterar os próprios mecanismos de autenticação.

Exemplo:

```text
Atacante
   ↓
Acessa conta
   ↓
Adiciona novo método MFA
   ↓
Mantém acesso
```

Esse comportamento pode criar persistência.

Por isso:

```text
Novo método MFA
+
login anormal
```

deve ser investigado.

---

# Alteração de senha

Outro indicador importante:

```text
Login suspeito
   ↓
Alteração de senha
```

ou:

```text
Alteração de senha
   ↓
Novo login
```

Pode ser legítimo.

Mas em uma conta já considerada suspeita, aumenta a necessidade de investigação.

---

# Persistência em identidade

O atacante pode tentar manter acesso através de:

```text
Tokens
Sessões
Aplicações autorizadas
MFA
Regras de e-mail
Delegações
Contas adicionais
Chaves
Credenciais
```

Por isso, simplesmente alterar a senha pode não ser suficiente.

Uma resposta completa deve revisar os mecanismos de persistência associados à identidade.

---

# SIEM e correlação

Um SIEM pode correlacionar:

```text
Login
+
IP
+
Device
+
MFA
+
Application
+
File Access
```

Exemplo:

```text
Login de IP desconhecido
        +
Novo dispositivo
        +
MFA inesperado
        +
Download anormal
```

Resultado:

```text
Possible Account Compromise
```

---

# Exemplo de regra de detecção

Uma lógica simples:

```text
IF

new_device = true

AND

new_ip = true

AND

sensitive_resource_access = true

THEN

generate_alert
```

Pode ser enriquecida:

```text
+
user_risk
+
geo_anomaly
+
mfa_anomaly
+
time_anomaly
```

Quanto mais contexto, menor tende a ser o ruído.

---

# Detecção de MFA Fatigue

Uma regra conceitual:

```text
IF

mfa_denied >= threshold

AND

time_window <= X

THEN

possible_mfa_fatigue
```

Se posteriormente ocorrer:

```text
MFA denied
+
MFA denied
+
MFA approved
+
new device
```

a prioridade pode aumentar.

---

# Detecção de sessão suspeita

Um exemplo:

```text
Conta
   ↓
Dispositivo A
   ↓
Sessão normal
```

Depois:

```text
Mesma conta
   ↓
Dispositivo B
   ↓
IP diferente
   ↓
Acesso simultâneo
```

Esse comportamento pode indicar:

```text
Session Hijacking
ou
Account Compromise
```

Mas precisa ser validado contra VPNs, proxies e outros fatores legítimos.

---

# Investigação de privilégio

Uma conta comprometida pode tentar aumentar seus privilégios.

Exemplo:

```text
Usuário comum
   ↓
Acesso inicial
   ↓
Grupo privilegiado
   ↓
Admin
```

Ou:

```text
Conta comprometida
   ↓
Criação de nova conta
   ↓
Adição a grupo administrativo
```

Essas alterações devem ser investigadas imediatamente.

---

# Contenção

Quando o comprometimento é confirmado ou altamente provável:

```text
Desabilitar conta
Revogar sessões
Revogar tokens
Resetar senha
Remover métodos MFA desconhecidos
Remover aplicações suspeitas
Remover regras de e-mail
```

A ordem pode variar de acordo com o ambiente.

O objetivo é impedir que o atacante continue utilizando a identidade.

---

# Não basta trocar a senha

Esse é um erro comum.

Imagine:

```text
Senha comprometida
      ↓
Senha alterada
```

Mas:

```text
Token roubado
```

continua válido.

Ou:

```text
Aplicação maliciosa
```

continua autorizada.

Ou:

```text
Regra de encaminhamento
```

continua ativa.

Por isso, a contenção deve considerar toda a superfície da identidade.

---

# Revogação de sessões

Depois de um comprometimento:

```text
Sessões existentes
      ↓
Revogar
```

Isso reduz a possibilidade de o atacante continuar utilizando sessões já estabelecidas.

Também pode ser necessário revogar tokens associados.

---

# Investigação pós-contenção

Depois de bloquear a conta:

```text
Quem acessou?
Quando?
De onde?
O que visualizou?
O que baixou?
O que alterou?
Quais sistemas acessou?
```

Pesquisar:

```text
E-mail
Arquivos
Cloud
VPN
RDP
Aplicações
Active Directory
```

O objetivo é determinar o impacto real.

---

# Exemplo de incidente

## Situação

O SIEM gera:

```text
Suspicious Sign-In

User:
usuario01

IP:
185.x.x.x

Device:
Unknown

Location:
Unexpected
```

Poucos minutos depois:

```text
MFA approved
```

E então:

```text
Download de grande volume de arquivos
```

### Investigação

O analista encontra:

```text
Novo dispositivo
+
IP desconhecido
+
MFA inesperado
+
Acesso a arquivos sensíveis
```

O caso é escalado.

---

# Resposta

### 1. Conter

```text
Bloquear conta
Revogar sessões
Revogar tokens
Resetar senha
Revisar MFA
```

### 2. Investigar

```text
Timeline
IP
Device
Applications
Files
E-mail
```

### 3. Procurar persistência

```text
Apps autorizadas
Regras de e-mail
Delegações
MFA
Tokens
```

### 4. Determinar impacto

```text
Quais dados foram acessados?
Quais foram baixados?
Quais sistemas foram acessados?
```

---

# Falso positivo

Nem todo login incomum significa comprometimento.

Exemplos:

```text
Usuário viajando
VPN
Home Office
Novo notebook
Troca de celular
Proxy corporativo
```

Por isso:

```text
IP desconhecido
```

sozinho possui pouco contexto.

Já:

```text
IP desconhecido
+
novo dispositivo
+
MFA inesperado
+
download anormal
```

possui muito mais relevância.

---

# Account Compromise em Cloud

Ambientes cloud possuem uma superfície adicional.

A identidade pode ter acesso a:

```text
E-mail
Arquivos
Aplicações
Cloud Storage
APIs
VMs
Databases
Secrets
```

Uma conta comprometida pode permitir acesso a diversos recursos sem que o atacante precise comprometer diretamente cada sistema.

Por isso, o SOC deve conhecer:

```text
Identity
Permissions
Applications
Tokens
Sessions
Resources
```

---

# Princípio do menor privilégio

Quanto maior o privilégio da conta, maior o impacto potencial.

Exemplo:

```text
Conta de leitura
   ↓
Acesso limitado
```

versus:

```text
Conta administrativa
   ↓
Acesso amplo
```

O princípio de menor privilégio reduz o impacto potencial de uma conta comprometida.

---

# Threat Hunting

Depois de confirmar uma conta comprometida, podemos pesquisar:

```text
Mesmo IP
Mesmo dispositivo
Mesmo token
Mesmo domínio
Mesma aplicação
Outras contas
```

Exemplo:

```text
Conta comprometida
      ↓
IP 185.x.x.x
      ↓
Pesquisar ambiente
      ↓
Outras contas acessadas?
```

Se houver:

```text
usuario01
usuario02
usuario03
```

o incidente pode ser maior do que inicialmente identificado.

---

# MITRE ATT&CK

Account Compromise pode estar relacionado a diferentes técnicas, dependendo de como o acesso foi obtido e utilizado.

Exemplos:

```text
Credential Access
Valid Accounts
Phishing
Brute Force
Credential Stuffing
Steal Web Session Cookie
```

Uma técnica especialmente importante é:

```text
T1078 - Valid Accounts
```

Ela representa o uso de credenciais válidas para obter acesso.

Esse conceito é fundamental:

> **O atacante não precisa parecer um atacante se estiver usando uma identidade legítima.**

---

# Checklist do analista

```text
[ ] Qual conta está envolvida?

[ ] A conta é privilegiada?

[ ] O login foi esperado?

[ ] Qual IP foi utilizado?

[ ] O IP é conhecido?

[ ] Qual dispositivo foi utilizado?

[ ] O dispositivo é conhecido?

[ ] A localização faz sentido?

[ ] O horário é compatível?

[ ] Houve falhas antes do login?

[ ] Houve MFA?

[ ] O MFA foi esperado?

[ ] Houve alteração de senha?

[ ] Houve alteração de MFA?

[ ] Existem sessões suspeitas?

[ ] Existem tokens suspeitos?

[ ] Existem aplicações autorizadas recentemente?

[ ] Foram criadas regras de e-mail?

[ ] Houve acesso a arquivos?

[ ] Houve downloads?

[ ] Houve alteração de permissões?

[ ] Outros sistemas foram acessados?

[ ] Outras contas foram envolvidas?

[ ] A sessão foi revogada?

[ ] A credencial foi resetada?

[ ] O impacto foi determinado?
```

---

# Métricas

Algumas métricas úteis:

```text
Número de contas comprometidas
Tempo até detecção
Tempo até contenção
Tempo até revogação de sessões
Quantidade de recursos acessados
Quantidade de dados acessados
Quantidade de contas afetadas
Taxa de falsos positivos
```

Também pode ser acompanhado:

```text
Login
   ↓
Detecção
   ↓
Contenção
```

Quanto menor esse intervalo, menor tende a ser a janela de oportunidade do atacante.

---

# Lições aprendidas

Depois do incidente:

```text
Como a credencial foi obtida?

O MFA estava habilitado?

O usuário recebeu treinamento?

Houve reutilização de senha?

Existia aplicação suspeita autorizada?

O atacante conseguiu persistência?

A detecção funcionou?

Quais recursos foram acessados?

A conta possuía privilégios excessivos?
```

Cada resposta pode gerar uma melhoria.

Exemplo:

```text
Privilégio excessivo
      ↓
Revisão de acesso
      ↓
Menor privilégio
      ↓
Menor impacto futuro
```

---

# O que diferencia um alerta de um incidente

Um alerta:

```text
Login de localização incomum
```

não significa necessariamente:

```text
Conta comprometida
```

Agora:

```text
Login incomum
+
novo dispositivo
+
MFA inesperado
+
acesso a dados sensíveis
+
download anormal
```

apresenta uma hipótese muito mais forte de comprometimento.

O SOC precisa transformar:

```text
Evento
   ↓
Contexto
   ↓
Evidência
   ↓
Incidente
```

---

# Conclusão

Account Compromise é um dos incidentes mais importantes para um SOC porque a atividade maliciosa pode ocorrer utilizando ferramentas e credenciais legítimas.

O atacante pode não precisar:

```text
Explorar servidor
```

nem:

```text
Executar malware
```

Pode simplesmente entrar utilizando:

```text
Usuário
+
Senha
+
Token
+
Sessão
```

Por isso, segurança de identidade precisa ir além da autenticação.

Uma investigação madura combina:

```text
Identity
+
SIEM
+
EDR
+
MFA
+
UEBA
+
Threat Intelligence
+
Logs de aplicação
+
Cloud
```

O objetivo não é apenas descobrir que uma conta foi acessada.

É descobrir:

> **como ela foi comprometida, o que o atacante fez com ela, quais recursos foram alcançados e se algum mecanismo de acesso permaneceu ativo.**

No final, uma conta comprometida não deve ser vista apenas como uma credencial roubada.

Ela deve ser tratada como uma **possível porta de entrada para todo o ambiente**.
