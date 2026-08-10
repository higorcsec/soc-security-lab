# Brute Force

## Introdução

Brute Force é uma técnica utilizada para tentar obter acesso a uma conta ou serviço através da tentativa repetida de credenciais.

A lógica é simples:

```text
Tentativa
   ↓
Falha
   ↓
Tentativa
   ↓
Falha
   ↓
Tentativa
   ↓
Sucesso?
```

Apesar de ser uma técnica conhecida há décadas, continua relevante porque serviços expostos à Internet, credenciais fracas e contas comprometidas ainda oferecem oportunidades para atacantes.

Em um SOC, porém, o desafio não é simplesmente detectar:

> "Muitas tentativas de login."

O desafio é determinar:

> **"Essas tentativas representam um ataque, uma automação legítima ou apenas um usuário errando a senha?"**

Essa diferença é o que transforma um evento de autenticação em uma investigação de segurança.

---

# Como funciona um ataque de Brute Force

Um cenário básico:

```text
Atacante
   ↓
Identifica serviço
   ↓
Obtém usuário
   ↓
Realiza tentativas
   ↓
Analisa respostas
   ↓
Obtém credencial
   ↓
Acessa o serviço
```

Os alvos podem incluir:

```text
SSH
RDP
VPN
Webmail
Painéis administrativos
Aplicações web
APIs
Active Directory
Cloud
Bancos de dados
```

O atacante normalmente procura algum ponto de entrada que permita autenticação.

---

# Brute Force x Password Spraying

Apesar de relacionados, os dois ataques possuem comportamentos diferentes.

## Brute Force tradicional

O atacante testa várias senhas contra uma conta.

```text
usuario01
   ↓
senha123
123456
qwerty
admin123
...
```

O volume de tentativas pode gerar muitos eventos para o mesmo usuário.

---

## Password Spraying

No Password Spraying, o atacante utiliza poucas senhas contra muitas contas.

```text
Senha123
   ↓
usuario01
usuario02
usuario03
usuario04
usuario05
```

Depois:

```text
OutraSenha
   ↓
usuario01
usuario02
usuario03
...
```

Isso pode dificultar a detecção quando existem políticas de bloqueio por conta.

---

# Credential Stuffing

Outro cenário importante é o **Credential Stuffing**.

Nesse caso, o atacante não tenta descobrir a senha.

Ele utiliza credenciais obtidas anteriormente em outro vazamento.

```text
Vazamento externo
       ↓
Usuário + senha
       ↓
Teste em outro serviço
       ↓
Login
```

Exemplo:

```text
Site A
   ↓
credencial vazada
   ↓
Site B
   ↓
login bem-sucedido
```

Por isso, um login suspeito nem sempre significa que houve brute force.

Pode ser reutilização de credenciais.

---

# Onde o SOC encontra os sinais

Tentativas de brute force podem aparecer em diferentes fontes.

### SSH

```text
Failed password
Invalid user
Authentication failure
```

### Windows

```text
Failed logon
Account lockout
Authentication failure
```

### VPN

```text
Multiple authentication failures
Repeated login attempts
Unusual source IP
```

### Aplicações Web

```text
HTTP 401
HTTP 403
Repeated login requests
```

### Cloud

```text
Authentication failures
Impossible travel
Unusual sign-in
MFA failures
```

A fonte do log muda, mas o comportamento pode ser semelhante.

---

# O que procurar

Durante uma investigação, alguns campos são especialmente importantes:

```text
Usuário
IP de origem
Destino
Horário
Quantidade de tentativas
Resultado
Método de autenticação
User-Agent
Geolocalização
Dispositivo
```

O objetivo é reconstruir o comportamento.

Exemplo:

```text
IP:
185.x.x.x

Usuário:
admin

Tentativas:
1.240

Período:
14 minutos

Resultado:
1 login bem-sucedido
```

Isso merece uma investigação muito diferente de:

```text
Usuário:
higor

Tentativas:
4

Período:
10 minutos

Origem:
Rede corporativa
```

---

# Baseline é fundamental

Nem toda sequência de falhas é um ataque.

Imagine:

```text
07:58
Usuário inicia notebook

07:59
VPN tenta autenticar

07:59
Senha antiga configurada

08:00
Falha
08:00
Falha
08:01
Falha
```

Isso pode gerar um alerta.

Mas o contexto indica uma possível causa legítima.

Agora:

```text
02:17
IP externo
      ↓
50 usuários
      ↓
500 tentativas
      ↓
Múltiplos serviços
```

O cenário é completamente diferente.

Por isso:

> **Volume sem contexto não é suficiente para determinar um ataque.**

---

# Indicadores de um possível Brute Force

Alguns padrões aumentam a suspeita:

```text
Muitas tentativas em pouco tempo
+
IP externo
+
múltiplas contas
+
horário incomum
+
serviço exposto
+
padrão automatizado
```

Outro cenário:

```text
Mesmo IP
   ↓
admin
   ↓
administrator
   ↓
root
   ↓
test
   ↓
guest
```

Isso pode indicar tentativa de descoberta de contas válidas.

---

# Brute Force distribuído

Um atacante não precisa utilizar apenas um endereço IP.

Pode distribuir as tentativas:

```text
IP 1 → usuário01
IP 2 → usuário01
IP 3 → usuário01
IP 4 → usuário01
```

Ou:

```text
IP 1 → usuário01
IP 2 → usuário02
IP 3 → usuário03
IP 4 → usuário04
```

Nesse caso, uma regra simples baseada em:

```text
"100 tentativas do mesmo IP"
```

pode não funcionar.

A detecção precisa considerar outros elementos:

```text
Usuário
ASN
Geolocalização
Fingerprint
User-Agent
Janela temporal
Serviço
Padrão de comportamento
```

---

# Brute Force contra uma única conta

Exemplo:

```text
IP 200.10.10.20
        ↓
admin
        ↓
500 tentativas
        ↓
15 minutos
```

Uma detecção simples poderia ser:

```text
IF
failed_login >= threshold

AND

same_user = true

AND

time_window <= X

THEN
alert
```

O threshold deve ser ajustado ao ambiente.

Não existe um número universal que seja perfeito para todos os serviços.

---

# Password Spraying

Agora considere:

```text
IP 200.10.10.20

10:01 → user01 → falha
10:02 → user02 → falha
10:03 → user03 → falha
10:04 → user04 → falha
10:05 → user05 → sucesso
```

A quantidade de tentativas por usuário é pequena.

Mas existe um padrão:

```text
1 IP
+
muitos usuários
+
mesma janela temporal
+
mesmo serviço
```

Esse padrão pode ser muito mais relevante do que a quantidade total de falhas por conta.

---

# Credential Stuffing

Um cenário diferente:

```text
IP 1 → usuário01 → falha
IP 2 → usuário02 → falha
IP 3 → usuário03 → sucesso
IP 4 → usuário04 → falha
```

Aqui, o comportamento pode não parecer um brute force tradicional.

Pode ser:

```text
Credenciais obtidas externamente
        ↓
Tentativas automatizadas
        ↓
Algumas contas válidas
```

Por isso, o SOC deve analisar também:

```text
Login bem-sucedido
+
novo dispositivo
+
IP desconhecido
+
localização incomum
```

---

# Quando um Brute Force se torna um incidente?

Uma grande quantidade de falhas não significa necessariamente comprometimento.

O cenário muda quando existe evidência de sucesso ou impacto.

Exemplo:

```text
Falhas
   ↓
Falhas
   ↓
Falhas
   ↓
LOGIN BEM-SUCEDIDO
```

A investigação deve continuar imediatamente.

Perguntas importantes:

```text
A senha foi comprometida?

O login foi realmente legítimo?

Qual IP realizou o acesso?

Qual dispositivo?

O usuário reconhece?

O que aconteceu depois do login?
```

---

# O login bem-sucedido é apenas o começo

Considere:

```text
Brute Force
   ↓
Credencial obtida
   ↓
Login
   ↓
Acesso ao sistema
```

Agora precisamos investigar:

```text
Comandos executados
Arquivos acessados
Alterações realizadas
Novos usuários
Permissões
Conexões externas
Movimentação lateral
Exfiltração
Persistência
```

O incidente pode ter evoluído de:

```text
Initial Access
```

para:

```text
Execution
Persistence
Privilege Escalation
Lateral Movement
Collection
Exfiltration
```

Por isso, o SOC nunca deve encerrar a investigação apenas porque identificou o método inicial de acesso.

---

# Exemplo: SSH

Um servidor Linux apresenta:

```text
01:12
Failed password for root

01:12
Failed password for root

01:13
Failed password for root

01:13
Failed password for root

01:14
Accepted password for root
```

A sequência é altamente relevante.

O SOC deve investigar:

```text
IP de origem
Método de autenticação
Comandos executados
Sessão criada
Arquivos modificados
Novos usuários
Chaves SSH
Processos
Conexões de saída
```

Um ponto importante:

```text
Failed login
```

é um evento.

```text
Failed login
+
Successful login
+
atividade suspeita
```

é uma história.

É essa história que o analista precisa reconstruir.

---

# Exemplo: RDP

Em ambientes Windows, RDP pode ser um alvo frequente.

Um possível padrão:

```text
Internet
   ↓
RDP
   ↓
Múltiplas falhas
   ↓
Login bem-sucedido
```

Após o sucesso:

```text
Login
   ↓
PowerShell
   ↓
Download
   ↓
Execução
```

Nesse ponto, o evento de autenticação deixou de ser apenas um problema de credenciais.

Pode ser o início de um comprometimento do endpoint.

---

# Correlação no SIEM

O SIEM pode correlacionar eventos para aumentar a qualidade da detecção.

Exemplo:

```text
20 falhas de login
       +
1 login bem-sucedido
       +
IP externo
       +
usuário privilegiado
```

Resultado:

```text
ALERTA DE ALTO RISCO
```

Outro exemplo:

```text
Falhas de login
       +
novo dispositivo
       +
MFA rejeitado
       +
login bem-sucedido
```

Esse conjunto pode representar uma tentativa de comprometimento de identidade.

---

# Detecção baseada em contexto

Uma regra simples:

```text
IF
failed_logins > 20

THEN
alert
```

pode gerar muito ruído.

Uma regra mais contextualizada:

```text
IF
failed_logins > threshold

AND
source_ip = external

AND
target_account = privileged

AND
time_window = short

THEN
high_priority_alert
```

Podemos adicionar:

```text
+
IP reputation
+
geolocation
+
asset criticality
+
successful login
```

A qualidade da detecção aumenta.

---

# Exemplo de regra de detecção

## Caso

Detectar possível brute force contra SSH.

### Dados necessários

```text
timestamp
source_ip
username
destination
authentication_result
```

### Lógica

```text
Falhas de autenticação
        ↓
Mesmo serviço
        ↓
Mesmo usuário
        ↓
Mesma origem
        ↓
Janela de tempo
        ↓
Threshold atingido
```

### Resultado

```text
Alerta:
Possible SSH Brute Force
```

### Enriquecimento

```text
IP reputation
GeoIP
ASN
Asset criticality
User privilege
Previous activity
```

---

# MITRE ATT&CK

Brute Force está associado ao MITRE ATT&CK através da técnica:

```text
T1110 - Brute Force
```

Ela possui diferentes sub-técnicas, incluindo:

```text
T1110.001 - Password Guessing
T1110.002 - Password Cracking
T1110.003 - Password Spraying
T1110.004 - Credential Stuffing
```

Esse mapeamento ajuda o SOC a relacionar a atividade observada com uma técnica conhecida.

Também permite avaliar cobertura de detecção.

---

# Investigação

Uma investigação pode seguir:

```text
Alerta
   ↓
Identificar origem
   ↓
Identificar alvo
   ↓
Identificar conta
   ↓
Determinar volume
   ↓
Determinar janela temporal
   ↓
Verificar sucesso
   ↓
Correlacionar eventos
   ↓
Avaliar impacto
   ↓
Conter
```

---

# Perguntas essenciais

O analista deve conseguir responder:

```text
Quem tentou acessar?

De onde veio a tentativa?

Qual serviço foi atacado?

Qual conta foi alvo?

Quantas tentativas aconteceram?

Em quanto tempo?

Existiu login bem-sucedido?

A conta é privilegiada?

O IP possui histórico malicioso?

Outras contas foram atacadas?

Outros serviços foram testados?

O acesso foi seguido por comportamento suspeito?
```

Essas respostas transformam o alerta em evidência.

---

# Contenção

As ações dependem do cenário.

### Ataque em andamento

Possíveis medidas:

```text
Bloquear IP
Rate limiting
Firewall
WAF
Bloqueio geográfico quando aplicável
MFA
Account lockout
```

### Conta comprometida

```text
Reset de senha
Revogar sessões
Revogar tokens
Revisar MFA
Desabilitar conta quando necessário
Investigar acessos
```

### Servidor comprometido

```text
Isolar host
Preservar evidências
Bloquear origem
Analisar processos
Analisar persistência
Verificar outros hosts
```

A contenção deve considerar o risco de bloquear usuários legítimos ou interromper serviços importantes.

---

# Evitando bloqueios cegos

Bloquear automaticamente qualquer IP com várias falhas pode causar problemas.

Exemplo:

```text
Empresa
   ↓
NAT
   ↓
500 usuários
```

Um único IP pode representar centenas de pessoas.

Bloqueá-lo sem contexto pode causar impacto operacional.

Outro exemplo:

```text
VPN corporativa
   ↓
IP compartilhado
```

Por isso, a decisão deve considerar:

```text
Origem
+
Contexto
+
Volume
+
Usuários afetados
+
Criticidade
```

---

# Medidas preventivas

Alguns controles podem reduzir o risco:

### MFA

Mesmo que a senha seja descoberta:

```text
Senha
   ↓
MFA
   ↓
Segundo fator necessário
```

Isso reduz a probabilidade de acesso somente com a senha.

### Rate Limiting

Limita a velocidade das tentativas.

### Account Lockout

Pode bloquear temporariamente uma conta após determinado número de falhas.

Deve ser configurado com cuidado para evitar abuso como mecanismo de negação de serviço contra contas.

### Senhas fortes

Reduzem a eficácia de ataques baseados em adivinhação.

### Password Managers

Ajudam a reduzir reutilização de senhas.

### Remoção de serviços desnecessários

Se um serviço não precisa estar exposto:

```text
Internet
   X
Serviço
```

menos superfície de ataque significa menos oportunidades.

---

# Investigando um possível comprometimento

Imagine:

```text
100 falhas
       ↓
Login bem-sucedido
       ↓
Conta admin
```

O SOC deve ampliar o escopo.

Pesquisar:

```text
Autenticações
Sessões
Processos
Comandos
Arquivos
Alterações de configuração
Novos usuários
Permissões
Conexões de rede
```

Também é importante verificar se a mesma credencial foi utilizada em outros serviços.

---

# Exemplo de incidente

## Situação

O SIEM gera:

```text
Possible Brute Force

Source:
185.x.x.x

Target:
VPN

User:
admin

Failed attempts:
87

Window:
6 minutos
```

### Investigação

O analista verifica:

```text
IP externo
+
usuário privilegiado
+
87 falhas
+
1 login bem-sucedido
```

A prioridade aumenta.

Depois:

```text
Login
   ↓
VPN
   ↓
Acesso ao servidor
   ↓
PowerShell
```

Agora existe evidência de que o brute force pode ter sido apenas o início do ataque.

---

# Resposta ao incidente

### 1. Conter

```text
Bloquear origem
Revogar sessão
Resetar credencial
Revisar MFA
```

### 2. Investigar

```text
O que foi acessado?
Quais comandos foram executados?
Houve persistência?
Outras contas foram utilizadas?
```

### 3. Procurar expansão

```text
Outros hosts
Outras contas
Outros IPs
Outros serviços
```

### 4. Erradicar

```text
Remover persistência
Revogar credenciais
Corrigir exposição
Ajustar controles
```

### 5. Monitorar

```text
Tentativas futuras
Novos logins
Novas origens
Comportamentos relacionados
```

---

# Falso positivo

Um SOC precisa reconhecer que nem todo brute force aparente é um ataque.

Exemplo:

```text
Usuário
   ↓
Troca de senha
   ↓
Celular continua com senha antiga
   ↓
Tentativas automáticas
```

O resultado pode ser:

```text
20 falhas
```

Mas não existe atacante.

Outro cenário:

```text
Serviço automatizado
   ↓
Credencial expirada
   ↓
Tentativas repetidas
```

Também pode gerar alerta.

Por isso:

```text
Alerta ≠ Incidente
```

O alerta inicia a investigação.

---

# Métricas

Algumas métricas importantes:

```text
Número de tentativas
Número de contas atacadas
Número de IPs envolvidos
Taxa de sucesso
Tempo até detecção
Tempo até contenção
Número de incidentes confirmados
Taxa de falsos positivos
```

Uma métrica especialmente importante:

```text
Tentativas
    ↓
Sucesso
```

Quando existe sucesso após uma sequência anormal de falhas, a prioridade da investigação deve aumentar significativamente.

---

# Checklist do analista

```text
[ ] Qual serviço está sendo atacado?

[ ] Qual usuário está sendo alvo?

[ ] Qual IP iniciou as tentativas?

[ ] O IP é externo ou interno?

[ ] Quantas tentativas ocorreram?

[ ] Qual foi a janela de tempo?

[ ] Existem múltiplas contas?

[ ] Existe padrão de Password Spraying?

[ ] Existe indício de Credential Stuffing?

[ ] Houve login bem-sucedido?

[ ] A conta é privilegiada?

[ ] O login foi legítimo?

[ ] O que aconteceu após o login?

[ ] Existem outros hosts envolvidos?

[ ] O IP deve ser bloqueado?

[ ] A credencial precisa ser revogada?

[ ] Existe persistência?

[ ] O incidente precisa ser escalado?
```

---

# O que diferencia um alerta de um incidente

Considere:

```text
50 falhas
+
IP externo
```

Isso pode ser:

```text
Tentativa de ataque
```

Agora:

```text
50 falhas
+
IP externo
+
login bem-sucedido
+
conta privilegiada
+
atividade suspeita
```

Temos um cenário muito mais grave.

O SOC deve buscar essa evolução:

```text
Evento
   ↓
Padrão
   ↓
Alerta
   ↓
Evidência
   ↓
Incidente
```

---

# Conclusão

Brute Force parece uma técnica simples porque seu comportamento básico é conhecido:

```text
Tentar
   ↓
Falhar
   ↓
Tentar novamente
```

Mas a investigação de um possível brute force é muito mais complexa.

O SOC precisa diferenciar:

```text
Erro legítimo
      X
Automação legítima
      X
Brute Force
      X
Password Spraying
      X
Credential Stuffing
```

E, principalmente, precisa descobrir se alguma tentativa resultou em acesso.

O ponto mais importante da investigação é:

> **Não olhar apenas para as falhas. Procurar o que aconteceu depois delas.**

Uma sequência de autenticações pode parecer apenas ruído até que uma delas seja bem-sucedida.

A partir desse momento, a pergunta deixa de ser:

> "Alguém tentou entrar?"

e passa a ser:

> **"O que esse acesso permitiu que o atacante fizesse?"**

É essa mudança de perspectiva que transforma monitoramento de autenticação em resposta a incidentes.
