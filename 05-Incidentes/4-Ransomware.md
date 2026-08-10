# Ransomware

## Introdução

Ransomware é um tipo de malware utilizado para impedir o acesso a dados ou sistemas, geralmente através da criptografia de arquivos.

Em ataques modernos, porém, o objetivo pode ir muito além da criptografia.

Um atacante pode:

```text id="m0f4qz"
Obter acesso inicial
        ↓
Roubar credenciais
        ↓
Escalar privilégios
        ↓
Desativar controles
        ↓
Movimentar-se pela rede
        ↓
Coletar dados
        ↓
Exfiltrar informações
        ↓
Criptografar sistemas
        ↓
Exigir pagamento
```

Por isso, ransomware deve ser tratado como um **incidente crítico**, principalmente quando envolve servidores, Active Directory, backups ou sistemas essenciais para o negócio.

A criptografia dos arquivos pode ser apenas a etapa mais visível de um comprometimento que começou muito antes.

---

# Como funciona um ataque de ransomware

Um ataque pode seguir diferentes caminhos.

Um exemplo:

```text id="y0v9si"
Phishing
   ↓
Credencial comprometida
   ↓
Acesso inicial
   ↓
Discovery
   ↓
Privilege Escalation
   ↓
Lateral Movement
   ↓
Credential Access
   ↓
Exfiltração
   ↓
Ransomware
```

Outro cenário:

```text id="z9h2pj"
Serviço exposto
   ↓
Exploração
   ↓
Acesso ao servidor
   ↓
Persistência
   ↓
Movimentação lateral
   ↓
Criptografia
```

O SOC precisa identificar a cadeia antes que ela chegue à etapa final.

---

# Ransomware não começa com a criptografia

Um dos erros mais perigosos é tratar o início do incidente como:

> "Os arquivos começaram a ser criptografados."

Nesse momento, o atacante pode já ter:

```text id="9t9gkp"
Comprometido contas
+
Criado persistência
+
Obtido privilégios
+
Acessado servidores
+
Comprometido backups
```

Por isso, os primeiros sinais podem aparecer muito antes da criptografia.

Exemplo:

```text id="8z8p7n"
Login anormal
   ↓
PowerShell
   ↓
Discovery
   ↓
Credencial
   ↓
Movimentação lateral
   ↓
Criptografia
```

Cada etapa pode gerar oportunidades de detecção.

---

# Ransomware e Double Extortion

Ataques modernos podem combinar:

```text id="3q9x4c"
Criptografia
+
Exfiltração
```

Nesse cenário, mesmo que a organização consiga restaurar os arquivos através de backups, o atacante pode ameaçar divulgar os dados roubados.

A cadeia fica:

```text id="r4f2w7"
Acesso
   ↓
Coleta
   ↓
Exfiltração
   ↓
Criptografia
   ↓
Extorsão
```

Isso aumenta o impacto do incidente.

---

# Impactos possíveis

Um ataque de ransomware pode afetar:

### Disponibilidade

```text id="n3j7c8"
Arquivos inacessíveis
Servidores indisponíveis
Sistemas paralisados
```

### Confidencialidade

```text id="k5v8p2"
Dados roubados
Informações de clientes
Documentos internos
Credenciais
```

### Integridade

```text id="x7m1q4"
Arquivos alterados
Configurações modificadas
Backups comprometidos
```

### Continuidade

```text id="s2c9v6"
Operação interrompida
Atendimento afetado
Sistemas críticos indisponíveis
```

Por isso, ransomware pode rapidamente deixar de ser apenas um incidente de segurança e se tornar uma **crise operacional**.

---

# Primeiros sinais

Alguns indicadores podem aparecer antes da criptografia.

Exemplos:

```text id="f8k3m1"
Login anormal
Conta privilegiada utilizada fora do padrão
Execução de ferramentas administrativas
Desativação de segurança
Criação de contas
Acesso incomum a servidores
Movimentação lateral
Volume anormal de arquivos
```

Durante a execução:

```text id="c7v2n9"
Grande quantidade de arquivos modificados
Extensões alteradas
Arquivos de instrução criados
Processos desconhecidos
Shadow Copies removidas
Backups acessados
```

Nenhum evento isolado confirma ransomware.

A combinação pode revelar a cadeia de ataque.

---

# File Encryption

A criptografia geralmente gera um comportamento diferente do uso normal do sistema.

Um processo pode:

```text id="p2m8r5"
Abrir arquivo
   ↓
Criptografar
   ↓
Salvar
   ↓
Alterar extensão
```

E repetir isso milhares de vezes:

```text id="v6x3q1"
Arquivo 1
Arquivo 2
Arquivo 3
...
Arquivo 10.000
```

Esse padrão pode ser detectado através de comportamento.

---

# Detecção por volume

Um use case possível:

```text id="w9n4c7"
IF
file_modifications > baseline

AND

modifications happen rapidly

AND

same_process = true

THEN
possible_ransomware
```

A regra pode considerar:

```text id="d1s8k3"
Quantidade
Velocidade
Extensões
Processo
Usuário
Diretório
Tipo de arquivo
```

Um processo modificando milhares de documentos em poucos minutos merece atenção.

---

# Não depender da extensão

Um ransomware pode alterar extensões.

Exemplo:

```text id="q7m2v5"
documento.docx
       ↓
documento.locked
```

Mas a extensão pode mudar entre diferentes famílias.

Por isso, uma detecção mais resiliente deve procurar:

```text id="c5x9r1"
Comportamento de criptografia
+
Alto volume de alterações
+
Processo incomum
```

e não apenas:

```text id="n8k4p2"
*.locked
```

---

# Shadow Copies

Em ambientes Windows, atacantes podem tentar remover mecanismos de recuperação.

A remoção de Shadow Copies pode ser um indicador importante.

Um cenário:

```text id="u3v8m6"
Acesso privilegiado
   ↓
Comando administrativo
   ↓
Shadow Copies removidas
   ↓
Criptografia
```

Esse comportamento deve receber atenção especialmente quando combinado com outros sinais.

---

# Backups

Backups são um dos principais alvos em ataques de ransomware.

O atacante pode tentar:

```text id="h5q2z9"
Acessar backup
   ↓
Apagar backup
   ↓
Criptografar backup
   ↓
Impedir recuperação
```

Por isso, durante um incidente, o SOC deve verificar:

```text id="r7m4c2"
Backups estão acessíveis?
Backups foram modificados?
Credenciais de backup foram utilizadas?
Existe cópia offline?
Existe cópia imutável?
```

A existência de backup não significa automaticamente que a recuperação será possível.

É necessário saber se ele continua **íntegro e acessível**.

---

# Active Directory

Em muitos ambientes corporativos, o Active Directory é um alvo crítico.

Um atacante pode buscar:

```text id="b8x3q7"
Contas privilegiadas
Grupos administrativos
Domain Admin
Servidores
Trusts
Políticas
Credenciais
```

Se o domínio for comprometido, o ransomware pode atingir uma quantidade muito maior de sistemas.

Por isso, sinais de comprometimento de contas privilegiadas devem receber prioridade.

---

# Lateral Movement

Ransomware frequentemente precisa alcançar vários sistemas.

Um possível padrão:

```text id="m6v1x8"
Host A
   ↓
Credencial
   ↓
Host B
   ↓
Credencial
   ↓
Host C
   ↓
Servidor
```

O SOC pode procurar:

```text id="q2n7s4"
RDP
SMB
WinRM
PsExec
Admin Shares
Remote Services
```

O objetivo é identificar movimentação lateral antes da criptografia em massa.

---

# PowerShell e ferramentas administrativas

Atacantes podem utilizar ferramentas legítimas para realizar atividades maliciosas.

Isso pode dificultar a detecção.

Exemplo:

```text id="t8r3k6"
PowerShell
   ↓
Discovery
   ↓
Credential Access
   ↓
Lateral Movement
```

O problema não é simplesmente:

> "PowerShell foi executado."

O contexto é fundamental.

Exemplo:

```text id="s5m9v2"
PowerShell
+
usuário administrativo
+
host incomum
+
execução fora do horário
+
movimentação lateral
```

Esse conjunto possui muito mais relevância.

---

# Living off the Land

Uma característica importante de alguns ataques é utilizar ferramentas já existentes no sistema.

Exemplos:

```text id="w3c7n1"
PowerShell
CMD
WMI
RDP
PsExec
SMB
```

Isso reduz a necessidade de introduzir ferramentas novas no ambiente.

Consequentemente:

```text id="v8m2q5"
Detecção baseada somente em arquivos
```

pode ser insuficiente.

É necessário observar comportamento.

---

# Exfiltração antes da criptografia

Em ataques de extorsão dupla, o atacante pode coletar dados antes de criptografá-los.

Exemplo:

```text id="k9q4x6"
Discovery
   ↓
Collection
   ↓
Compressão
   ↓
Transferência
   ↓
Criptografia
```

Alguns sinais:

```text id="z4n8c1"
Grande volume de dados
Compressão incomum
Destino externo
Uso de contas privilegiadas
Acesso a diretórios sensíveis
```

A investigação precisa determinar se dados foram efetivamente exfiltrados.

---

# Correlação no SIEM

Considere:

```text id="s7m3v9"
Login privilegiado
      +
PowerShell
      +
Discovery
      +
RDP
      +
Volume anormal de arquivos
```

Individualmente:

```text id="a4k8p2"
Eventos suspeitos
```

Em conjunto:

```text id="r6x1m5"
Possível ataque de ransomware
```

Esse é um exemplo de como correlação pode antecipar o impacto.

---

# Detecção em camadas

Uma estratégia madura não espera a criptografia.

Podemos construir:

```text id="n5c8q2"
Camada 1
Anomalia de autenticação
        ↓
Camada 2
Privilege Escalation
        ↓
Camada 3
Lateral Movement
        ↓
Camada 4
Backup Tampering
        ↓
Camada 5
File Encryption
```

Quanto mais cedo o atacante for detectado, maior a possibilidade de limitar o impacto.

---

# MITRE ATT&CK

Ransomware não representa uma única técnica.

O ataque pode envolver diversas técnicas do MITRE ATT&CK:

```text id="p7v3k9"
Initial Access
Execution
Persistence
Privilege Escalation
Defense Evasion
Credential Access
Discovery
Lateral Movement
Collection
Command and Control
Exfiltration
Impact
```

Uma das técnicas diretamente relacionadas ao impacto é:

```text id="q4m8x1"
T1486
Data Encrypted for Impact
```

Outras atividades podem incluir:

```text id="y2c6v5"
T1490
Inhibit System Recovery
```

O mapeamento deve ser baseado no comportamento observado durante o incidente.

---

# Investigação

Quando um alerta de possível ransomware aparece, a primeira tarefa é determinar:

> **"O que está acontecendo agora?"**

Depois:

> **"Quando começou?"**

E:

> **"Até onde chegou?"**

Uma investigação pode seguir:

```text id="f3k7n2"
Alerta
   ↓
Identificar host
   ↓
Identificar usuário
   ↓
Identificar processo
   ↓
Verificar arquivos modificados
   ↓
Verificar rede
   ↓
Verificar autenticações
   ↓
Identificar outros hosts
   ↓
Determinar escopo
```

---

# Timeline

Uma timeline pode revelar a progressão.

Exemplo:

```text id="z8m4c1"
22:01
Login administrativo

22:04
PowerShell

22:06
RDP para servidor

22:10
Acesso a compartilhamento

22:15
Backup acessado

22:18
Shadow Copies removidas

22:20
Execução do payload

22:21
Criptografia iniciada
```

Agora o incidente possui uma narrativa.

O SOC consegue identificar que a criptografia não foi o começo.

Foi o resultado de uma sequência de ações.

---

# Determinando o escopo

Perguntas importantes:

```text id="x5v9q3"
Quantos endpoints foram afetados?

Quantos servidores?

Quais usuários foram comprometidos?

O Active Directory foi afetado?

Os backups foram acessados?

Houve exfiltração?

Quais dados foram acessados?

O ransomware ainda está ativo?
```

A resposta define a estratégia de contenção.

---

# Contenção

Em um ransomware ativo, velocidade é crítica.

Possíveis ações:

```text id="k7n2m8"
Isolar endpoints
Bloquear comunicação
Desabilitar contas comprometidas
Revogar sessões
Bloquear IOCs
Interromper processos maliciosos
Proteger backups
Restringir movimentação lateral
```

Mas as ações devem ser coordenadas.

Em um ambiente de produção, desligar servidores indiscriminadamente pode aumentar o impacto.

A decisão deve considerar:

```text id="v3q8c5"
Segurança
+
Criticidade
+
Continuidade
+
Preservação de evidências
```

---

# Isolamento

Quando existe criptografia ativa, o isolamento pode ser uma das ações mais importantes.

Exemplo:

```text id="m9x4r2"
Endpoint comprometido
       ↓
Rede
       ↓
Servidor
       ↓
Compartilhamento
```

Se o endpoint tiver acesso a compartilhamentos, o ransomware pode atingir recursos adicionais.

O isolamento reduz essa capacidade.

---

# Proteção dos backups

Durante um incidente, os backups devem ser tratados como ativos críticos.

A equipe deve verificar:

```text id="p8c3v7"
Acesso
Integridade
Imutabilidade
Credenciais
Conectividade
Última cópia válida
```

Sempre que possível, mecanismos de backup devem possuir proteção contra alteração ou exclusão pelo mesmo conjunto de credenciais utilizado pelos sistemas de produção.

---

# Erradicação

Depois de conter o ataque:

```text id="h2m7q9"
Identificar vetor inicial
       ↓
Eliminar persistência
       ↓
Remover malware
       ↓
Revogar credenciais
       ↓
Corrigir vulnerabilidades
       ↓
Reforçar controles
```

Não é suficiente restaurar os arquivos se o ponto de entrada continuar vulnerável.

Caso contrário:

```text id="n4x8c6"
Restaurar
   ↓
Atacante retorna
   ↓
Novo comprometimento
```

---

# Recuperação

A recuperação deve ser gradual.

Exemplo:

```text id="r6m2v9"
Validar backup
   ↓
Restaurar sistema crítico
   ↓
Validar integridade
   ↓
Aplicar patches
   ↓
Reforçar autenticação
   ↓
Monitorar
   ↓
Restaurar próximo sistema
```

A prioridade deve considerar a criticidade dos serviços.

---

# Não pagar não significa "não fazer nada"

A decisão sobre pagamento de resgate envolve aspectos jurídicos, financeiros, regulatórios e estratégicos.

Do ponto de vista técnico do SOC, independentemente da decisão:

```text id="b7q3m5"
Investigar
Conter
Preservar evidências
Determinar escopo
Proteger backups
Recuperar
Monitorar
```

continuam sendo necessários.

O pagamento também não garante:

```text id="d8v1k4"
Recuperação completa
Exclusão dos dados
Ausência de persistência
Não divulgação
```

Por isso, a capacidade de recuperação deve existir antes do incidente.

---

# Exemplo de incidente

## Situação

O SOC recebe vários alertas:

```text id="s3x9q2"
PowerShell suspeito
+
Login administrativo
+
RDP
+
Alto volume de arquivos modificados
```

Poucos minutos depois:

```text id="c6m4v8"
Extensões alteradas
+
arquivos de instrução criados
```

### Investigação

O analista identifica:

```text id="t7k2n5"
Host inicial
   ↓
Conta comprometida
   ↓
RDP
   ↓
Servidor de arquivos
   ↓
Acesso ao backup
```

Existe evidência de movimentação lateral.

---

# Resposta

### Contenção imediata

```text id="w5r8c3"
Isolar hosts afetados
Bloquear comunicação maliciosa
Revogar credenciais comprometidas
Interromper processos maliciosos
Restringir movimentação lateral
```

### Proteção

```text id="j2m7v9"
Proteger backups
Bloquear contas suspeitas
Preservar evidências
```

### Investigação

```text id="q8x3k6"
Identificar vetor inicial
Determinar escopo
Pesquisar IOCs
Pesquisar TTPs
Verificar exfiltração
```

---

# Falso positivo

Nem toda alteração em massa de arquivos é ransomware.

Exemplo:

```text id="n6p2v8"
Software de backup
   ↓
Modifica milhares de arquivos
```

Ou:

```text id="y4m9c3"
Sistema de processamento
   ↓
Cria arquivos em massa
```

Por isso, uma regra baseada somente em:

```text id="k8x5q1"
"muitos arquivos modificados"
```

pode gerar ruído.

É melhor combinar:

```text id="f3v7m2"
Volume
+
Processo
+
Extensões
+
Usuário
+
Local
+
Comportamento
```

---

# Detection Engineering para Ransomware

Uma detecção pode ser criada em diferentes estágios.

### Pré-impacto

```text id="q1n6x8"
Login anormal
+
Privilege Escalation
+
Lateral Movement
```

### Durante preparação

```text id="v4m8c2"
Backup tampering
+
Shadow Copy deletion
```

### Durante impacto

```text id="z7r3k5"
Alto volume de alterações
+
Processo incomum
+
Extensões alteradas
```

A vantagem é conseguir identificar o ataque antes da etapa final.

---

# Threat Hunting

Após detectar um ransomware, o SOC deve procurar sinais relacionados em todo o ambiente.

Pesquisar:

```text id="p5k9v3"
Hashes
Domínios
IPs
Processos
Usuários
Command Lines
Scheduled Tasks
RDP
SMB
```

Também pode procurar técnicas:

```text id="m8x2q6"
Credential Access
Lateral Movement
Defense Evasion
Impact
```

A pergunta passa a ser:

> **"Existe outro host apresentando o mesmo comportamento?"**

---

# Checklist do analista

```text id="r7c3m9"
[ ] O alerta foi validado?

[ ] Existe criptografia ativa?

[ ] Qual host iniciou a atividade?

[ ] Qual usuário está envolvido?

[ ] Qual processo está modificando os arquivos?

[ ] Quantos arquivos foram afetados?

[ ] Quais diretórios foram atingidos?

[ ] Existem servidores afetados?

[ ] Existem compartilhamentos de rede afetados?

[ ] Houve movimentação lateral?

[ ] Houve acesso a contas privilegiadas?

[ ] Shadow Copies foram afetadas?

[ ] Backups foram acessados?

[ ] Backups continuam íntegros?

[ ] Houve exfiltração?

[ ] Qual foi o vetor inicial?

[ ] Existem outros endpoints comprometidos?

[ ] As contas comprometidas foram contidas?

[ ] Os IOCs foram bloqueados?

[ ] O ambiente foi monitorado após a recuperação?
```

---

# Métricas

Algumas métricas importantes:

```text id="c4m8x7"
Tempo até detecção
Tempo até contenção
Quantidade de endpoints afetados
Quantidade de servidores afetados
Quantidade de dados afetados
Tempo de indisponibilidade
Tempo de recuperação
Quantidade de contas comprometidas
Quantidade de backups disponíveis
```

Uma métrica crítica é:

```text id="y9k3q5"
Tempo entre:
Primeiro sinal
       ↓
Detecção
       ↓
Contenção
```

Em ransomware, minutos podem representar milhares de arquivos adicionais comprometidos.

---

# Lições aprendidas

Após o incidente:

```text id="v6m2q8"
Como o atacante entrou?

Por que não foi detectado antes?

Qual controle falhou?

A conta estava protegida por MFA?

Havia segmentação?

Os backups estavam isolados?

O SIEM recebeu os eventos?

O EDR detectou o comportamento?

Havia detecção de movimentação lateral?

Houve exfiltração?

Quanto tempo levou para conter?
```

As respostas devem gerar melhorias concretas.

Exemplo:

```text id="q3x8n5"
Gap de detecção
      ↓
Nova regra
      ↓
Teste
      ↓
Purple Team
      ↓
Validação
```

---

# O que diferencia uma resposta madura

Uma resposta fraca:

```text id="u7m4c2"
Ransomware detectado
↓
Desligar computadores
↓
Restaurar backup
```

Uma resposta madura:

```text id="x9k2v6"
Detectar
   ↓
Determinar escopo
   ↓
Conter propagação
   ↓
Proteger backups
   ↓
Preservar evidências
   ↓
Identificar vetor inicial
   ↓
Erradicar persistência
   ↓
Validar backups
   ↓
Recuperar
   ↓
Monitorar
   ↓
Melhorar controles
```

A diferença está na capacidade de controlar o incidente sem perder a visão do ambiente inteiro.

---

# Conclusão

Ransomware não deve ser tratado como:

> **"Um vírus que criptografa arquivos."**

Essa definição é pequena demais para representar um ataque moderno.

Um incidente de ransomware pode envolver:

```text id="h5r8m2"
Initial Access
        ↓
Credential Access
        ↓
Privilege Escalation
        ↓
Discovery
        ↓
Lateral Movement
        ↓
Collection
        ↓
Exfiltration
        ↓
Impact
```

A criptografia é apenas uma das etapas.

Para um SOC, o objetivo é detectar o ataque **antes que ele chegue ao impacto máximo**.

Uma boa estratégia combina:

```text id="p8v3q6"
EDR
+
SIEM
+
Identity
+
Network
+
Threat Intelligence
+
MITRE ATT&CK
+
Threat Hunting
+
Backups
+
Incident Response
```

E existe uma pergunta que deve guiar toda investigação:

> **"Se o ransomware começou a criptografar arquivos agora, o que o atacante precisou fazer antes disso?"**

Encontrar essa resposta pode ser a diferença entre um endpoint comprometido e uma paralisação completa do ambiente.
