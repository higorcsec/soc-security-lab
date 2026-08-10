# Exfiltração de Dados

## Introdução

Exfiltração de dados é a transferência não autorizada de informações de dentro de um ambiente para um destino controlado pelo atacante ou para um local não autorizado.

Em um incidente, a exfiltração pode representar o objetivo final do ataque.

Um fluxo comum:

```text
Acesso inicial
      ↓
Comprometimento
      ↓
Discovery
      ↓
Acesso aos dados
      ↓
Coleta
      ↓
Preparação
      ↓
Exfiltração
```

Em ataques modernos, a exfiltração também pode acontecer antes de outras ações.

Por exemplo:

```text
Comprometimento
      ↓
Exfiltração
      ↓
Criptografia
      ↓
Extorsão
```

Por isso, identificar que dados foram retirados pode ser tão importante quanto descobrir como o atacante entrou.

---

# O que pode ser exfiltrado?

Nem todo dado possui o mesmo valor.

Um atacante pode procurar:

```text
Credenciais
Documentos
Dados financeiros
Dados de clientes
Informações pessoais
Código-fonte
Chaves de API
Tokens
Certificados
Configurações
Backups
Propriedade intelectual
Informações estratégicas
```

Em um ambiente corporativo:

```text
Servidor
   ↓
Banco de dados
   ↓
Dados de clientes
   ↓
Exfiltração
```

O impacto pode ser muito maior do que o comprometimento de apenas um endpoint.

---

# Exfiltração x Download legítimo

Um dos maiores desafios para o SOC é que exfiltração pode parecer uma atividade normal.

Imagine:

```text
Usuário
   ↓
Download de arquivo
```

Isso pode ser completamente legítimo.

Agora:

```text
Conta comprometida
   ↓
Acesso a centenas de arquivos
   ↓
Compressão
   ↓
Transferência externa
```

O comportamento muda completamente.

Por isso:

> **Volume sozinho não determina exfiltração. O contexto determina.**

---

# Como ocorre a exfiltração

Existem diversas possibilidades.

```text
Internet
Cloud Storage
E-mail
FTP
SFTP
HTTPS
DNS
Web Services
Mensageria
Dispositivos removíveis
```

Também pode acontecer através de serviços legítimos.

Exemplo:

```text
Endpoint
   ↓
Cloud Storage
   ↓
Servidor externo
```

O tráfego pode parecer legítimo.

Por isso, a análise precisa considerar:

```text
Quem
O quê
Quando
Para onde
Quanto
Como
Por quê
```

---

# Exfiltração por HTTPS

HTTPS é amplamente utilizado por aplicações legítimas.

Isso também significa que pode ser utilizado para transferência de dados.

Exemplo:

```text
Endpoint
   ↓
HTTPS
   ↓
Servidor externo
```

O SOC pode analisar:

```text
Domínio
IP
Processo
Volume
Horário
Periodicidade
Reputação
Usuário
```

O conteúdo pode não estar diretamente visível devido à criptografia, mas metadados ainda podem fornecer contexto.

---

# Exfiltração para Cloud Storage

Atacantes podem utilizar serviços de armazenamento para retirar dados.

Exemplo:

```text
Servidor
   ↓
Arquivos
   ↓
Compressão
   ↓
Cloud Storage
```

Isso pode ser difícil de distinguir de uma operação legítima.

Por isso, o SOC pode procurar:

```text
Novo destino
+
Grande volume
+
Conta incomum
+
Horário anormal
+
Processo desconhecido
```

---

# Exfiltração por e-mail

Uma conta comprometida pode ser utilizada para enviar informações para endereços externos.

Exemplo:

```text
Conta comprometida
      ↓
Arquivos anexados
      ↓
E-mail externo
```

Ou:

```text
Conta comprometida
      ↓
Forwarding
      ↓
Caixa externa
```

O SOC deve observar:

```text
Destinatário
Tamanho
Quantidade
Tipo de arquivo
Horário
Origem
Histórico do usuário
```

---

# Compressão antes da exfiltração

Um atacante pode agrupar vários arquivos antes de transferi-los.

Exemplo:

```text
Documentos
   ↓
ZIP
   ↓
Transferência
```

A compressão pode reduzir o volume da transferência e facilitar a movimentação dos dados.

Um comportamento suspeito pode ser:

```text
Grande quantidade de documentos
       ↓
Compressão repentina
       ↓
Arquivo grande criado
       ↓
Conexão externa
```

O contexto é fundamental.

Backups e processos administrativos também podem produzir o mesmo comportamento.

---

# Exfiltração por dispositivo removível

Nem toda exfiltração depende da Internet.

Um atacante com acesso físico ou lógico ao endpoint pode utilizar:

```text
USB
HD externo
Dispositivo removível
```

Um cenário:

```text
Endpoint
   ↓
Acesso a documentos
   ↓
USB conectado
   ↓
Grande volume de cópia
```

Dependendo da infraestrutura, o SOC pode utilizar logs de endpoint para investigar:

```text
Dispositivo
Usuário
Horário
Arquivos acessados
Volume transferido
```

---

# DNS e exfiltração

DNS normalmente é utilizado para resolução de nomes.

Porém, pode ser abusado para transportar pequenas quantidades de informação.

Conceitualmente:

```text
Endpoint
   ↓
DNS Query
   ↓
Domínio controlado
```

O SOC pode observar:

```text
Subdomínios muito longos
Alta frequência de consultas
Padrões incomuns
Entropia elevada
Domínios recém-observados
```

Um padrão isolado não confirma exfiltração.

Mas pode ser um excelente indicador para investigação.

---

# Exfiltração por canais legítimos

Um dos desafios modernos é o uso de serviços legítimos.

Por exemplo:

```text
Atacante
   ↓
Conta comprometida
   ↓
Serviço legítimo
   ↓
Dados
```

Isso pode reduzir a eficácia de bloqueios simples baseados em reputação.

Por isso, o SOC deve observar:

```text
Identidade
+
Aplicação
+
Destino
+
Volume
+
Comportamento
```

---

# Dados em movimento

Uma forma de pensar sobre exfiltração é observar:

```text
Data at Rest
      ↓
Data Access
      ↓
Data in Use
      ↓
Data in Motion
```

A exfiltração acontece principalmente quando os dados entram em movimento para fora do controle esperado da organização.

Exemplo:

```text
Banco de dados
      ↓
Aplicação
      ↓
Usuário
      ↓
Internet
```

Cada etapa pode gerar telemetria.

---

# Data Discovery

Antes de exfiltrar, o atacante precisa descobrir onde estão os dados.

Pode procurar:

```text
Pastas
Servidores
Compartilhamentos
Bancos
Cloud Storage
Documentos
Backups
Credenciais
```

Um fluxo:

```text
Discovery
   ↓
Localização dos dados
   ↓
Collection
   ↓
Exfiltration
```

Por isso, detectar comportamento de Discovery pode impedir que o ataque chegue à exfiltração.

---

# Collection

Depois de encontrar os dados, o atacante precisa coletá-los.

Exemplo:

```text
Servidor
   ↓
Arquivos
   ↓
Coleta
   ↓
Diretório temporário
   ↓
Compressão
```

Esse diretório temporário pode se tornar um indicador importante.

O SOC pode investigar:

```text
Quem criou?
Quando?
Qual processo?
Quais arquivos foram copiados?
Qual foi o destino?
```

---

# Staging

O atacante pode preparar os dados antes da transferência.

Exemplo:

```text
Servidor
   ↓
Dados espalhados
   ↓
Coleta
   ↓
Staging Directory
   ↓
Compressão
   ↓
Exfiltração
```

Um comportamento suspeito:

```text
/tmp/export.zip
```

ou:

```text
C:\Temp\data.zip
```

não é necessariamente malicioso.

Mas:

```text
Criação repentina
+
grande volume
+
dados sensíveis
+
processo desconhecido
+
conexão externa
```

merece investigação.

---

# Exfiltração em pequenos volumes

Nem toda exfiltração acontece através de grandes transferências.

Um atacante pode retirar dados lentamente:

```text
100 MB
↓
50 MB
↓
200 MB
↓
100 MB
```

Ou:

```text
Pequenos volumes
   ↓
Periodicamente
```

Isso pode dificultar detecções baseadas apenas em volume.

Por isso, é importante observar:

```text
Volume
+
Periodicidade
+
Destino
+
Processo
+
Usuário
```

---

# Low and Slow

Uma estratégia de exfiltração pode tentar permanecer abaixo dos limites normais.

Exemplo:

```text
Transferência normal:
100 MB/h

Atacante:
10 MB/h
```

Ao longo de dias:

```text
10 MB
×
24 horas
×
7 dias
```

o volume acumulado pode ser significativo.

Essa estratégia pode evitar alertas simples de alto volume.

---

# Exfiltração e comportamento do usuário

UEBA pode ajudar a identificar desvios.

Exemplo:

```text
Usuário normalmente:
10 arquivos/dia
```

Agora:

```text
Usuário:
2.000 arquivos/dia
```

Isso não significa automaticamente exfiltração.

Mas pode gerar:

```text
Anomalia de comportamento
```

Se combinado com:

```text
Novo IP
+
Novo dispositivo
+
Acesso a dados sensíveis
+
Transferência externa
```

a confiança aumenta.

---

# DLP

Data Loss Prevention pode ajudar a identificar movimentação de informações sensíveis.

Uma solução DLP pode considerar:

```text
Tipo de dado
Destino
Usuário
Aplicação
Dispositivo
Ação
```

Exemplo:

```text
Documento sensível
       ↓
Upload externo
       ↓
DLP Alert
```

O DLP complementa o SOC.

```text
DLP
+
SIEM
+
EDR
+
Network
```

produzem uma visão mais completa.

---

# SIEM e correlação

Imagine:

```text
Login anormal
      +
Acesso a diretório sensível
      +
Compressão
      +
Transferência externa
```

Individualmente:

```text
Eventos suspeitos
```

Correlacionados:

```text
Possible Data Exfiltration
```

Essa é uma situação ideal para um SIEM.

---

# Exemplo de correlação

Uma regra conceitual:

```text
IF

user_anomaly = true

AND

sensitive_data_access = true

AND

large_external_transfer = true

THEN

generate_exfiltration_alert
```

Pode ser enriquecida com:

```text
+
new_device
+
new_destination
+
rare_process
+
threat_intelligence
```

---

# Detecção baseada em baseline

Uma organização pode estabelecer um comportamento esperado.

Exemplo:

```text
Servidor
   ↓
Normalmente:
20 GB/dia
```

Um dia:

```text
Servidor
   ↓
180 GB
   ↓
Destino externo incomum
```

Isso pode gerar:

```text
Volume Anomaly
```

Mas é importante considerar:

```text
Backup
Atualização
Migração
Replicação
Projeto
```

antes de classificar como incidente.

---

# Análise de destino

O destino da transferência é uma das informações mais importantes.

Perguntas:

```text
O domínio é conhecido?

O IP pertence a um fornecedor?

É um serviço corporativo?

É um destino novo?

Foi observado anteriormente?

Possui reputação negativa?

Outros hosts acessam o mesmo destino?
```

Um destino desconhecido não é automaticamente malicioso.

Mas um destino desconhecido + comportamento anormal merece investigação.

---

# Análise do processo

Descobrir qual processo realizou a transferência pode mudar completamente o contexto.

Exemplo:

```text
BackupAgent.exe
      ↓
Transferência grande
```

pode ser legítimo.

Agora:

```text
powershell.exe
      ↓
Compressão
      ↓
Transferência externa
```

merece investigação.

A análise deve considerar:

```text
Processo
Parent Process
Command Line
Usuário
Host
Destino
```

---

# Exfiltração via ferramentas administrativas

Atacantes podem utilizar ferramentas disponíveis no próprio sistema.

Exemplo:

```text
PowerShell
CLI de cloud
FTP client
SSH
Rclone
```

O problema não é simplesmente a ferramenta.

Por exemplo:

```text
Rclone
```

pode ser legítimo em determinados ambientes.

O SOC deve perguntar:

```text
Quem executou?
Em qual host?
Em qual horário?
Para qual destino?
Qual volume?
Qual comando?
```

---

# Investigação

Quando um alerta de exfiltração aparece:

```text
Alerta
   ↓
Identificar origem
   ↓
Identificar usuário
   ↓
Identificar processo
   ↓
Identificar dados
   ↓
Identificar destino
   ↓
Determinar volume
   ↓
Determinar período
```

Depois:

```text
Pesquisar ambiente
```

para descobrir se o comportamento aconteceu em outros hosts.

---

# Timeline

Uma timeline pode mostrar:

```text
09:00
Login suspeito

09:05
Acesso ao servidor

09:12
Consulta ao diretório

09:20
Arquivos copiados

09:25
ZIP criado

09:28
Conexão externa

09:32
Upload iniciado
```

Agora existe uma cadeia:

```text
Account Compromise
       ↓
Discovery
       ↓
Collection
       ↓
Staging
       ↓
Exfiltration
```

---

# Determinando o impacto

A pergunta não é apenas:

> "Houve transferência?"

É necessário descobrir:

```text
Quais dados?
Quanto?
De onde?
Para onde?
Quando?
Por quem?
```

Também:

```text
Os dados eram sensíveis?

Os dados pertenciam a clientes?

Existiam credenciais?

Existiam informações pessoais?

Existia propriedade intelectual?
```

A classificação do dado influencia diretamente a gravidade do incidente.

---

# Contenção

Dependendo do caso:

```text
Bloquear destino
Bloquear domínio
Bloquear IP
Isolar endpoint
Desabilitar conta
Revogar sessões
Interromper processo
Suspender aplicação comprometida
```

A contenção deve impedir novas transferências sem destruir evidências desnecessariamente.

---

# Preservação de evidências

Antes de remover tudo:

```text
Logs
Processos
Arquivos
Hashes
Command Line
Network Connections
Authentication Logs
Cloud Logs
```

podem ser importantes para reconstruir o incidente.

Exemplo:

```text
Processo
   ↓
Command Line
   ↓
Destino
   ↓
Arquivo
```

Isso pode ajudar a determinar como a exfiltração ocorreu.

---

# Bloqueio de IOCs

Depois de identificar indicadores:

```text
IP
Domínio
URL
Hash
Conta
```

eles podem ser utilizados nos controles disponíveis:

```text
Firewall
DNS
Proxy
EDR
SIEM
Identity
DLP
```

Mas o bloqueio deve ser baseado em evidências e contexto.

---

# Revogação de credenciais

Se a exfiltração estiver associada a uma conta comprometida:

```text
Conta
   ↓
Revogar sessões
   ↓
Resetar credencial
   ↓
Revisar MFA
   ↓
Revisar permissões
```

Também é necessário verificar:

```text
Tokens
Aplicações autorizadas
Delegações
Regras
```

---

# Exfiltração e ransomware

Os dois incidentes podem estar diretamente relacionados.

Exemplo:

```text
Account Compromise
        ↓
Discovery
        ↓
Collection
        ↓
Exfiltration
        ↓
Ransomware
```

Nesse cenário:

```text
Criptografia
+
Dados roubados
```

aumentam significativamente o impacto.

Mesmo que os sistemas sejam restaurados, os dados podem continuar nas mãos do atacante.

---

# Exfiltração e insider threat

Nem toda exfiltração é causada por um atacante externo.

Um funcionário ou terceiro com acesso legítimo também pode retirar informações.

Exemplo:

```text
Usuário legítimo
   ↓
Acesso autorizado
   ↓
Cópia em massa
   ↓
Dispositivo externo
```

A diferença pode estar na intenção.

Por isso, o SOC deve trabalhar com:

```text
Comportamento
+
Contexto
+
Política
+
Evidências
```

sem assumir automaticamente que o usuário é malicioso.

---

# Falso positivo

Exfiltração é uma categoria que pode gerar muitos falsos positivos.

Exemplos:

```text
Backup
Migração
Replicação
Cloud Sync
Atualização
Exportação de banco
Transferência entre servidores
```

Por isso, uma regra baseada somente em:

```text
"Grande volume de dados"
```

não é suficiente.

Melhor:

```text
Grande volume
+
destino incomum
+
usuário incomum
+
horário incomum
+
processo incomum
```

---

# Exemplo de incidente

## Situação

O SIEM detecta:

```text
Usuário:
usuario01

Host:
WS-023

Transferência:
12 GB

Destino:
Novo domínio
```

O volume é muito acima do padrão.

### Investigação

O analista encontra:

```text
Login de IP desconhecido
        ↓
Novo dispositivo
        ↓
Acesso a diretório financeiro
        ↓
Compressão de arquivos
        ↓
Upload externo
```

O incidente deixa de ser:

```text
Large Transfer
```

e passa a ser:

```text
Possible Data Exfiltration
```

---

# Resposta

### Contenção

```text
Isolar endpoint
Bloquear destino
Revogar sessão
Bloquear conta
```

### Investigação

```text
Determinar arquivos
Determinar volume
Determinar período
Identificar destino
Identificar processo
```

### Escopo

```text
Pesquisar IP
Pesquisar domínio
Pesquisar usuário
Pesquisar hash
Pesquisar outros hosts
```

---

# Threat Hunting

Após confirmar a exfiltração, procurar:

```text
Mesmo destino
Mesmo IP
Mesmo domínio
Mesmo processo
Mesmo usuário
Mesmos arquivos
Mesmo padrão de transferência
```

Exemplo:

```text
Domínio suspeito
       ↓
Pesquisar SIEM
       ↓
12 hosts encontrados
```

Agora existe uma possibilidade de campanha.

---

# MITRE ATT&CK

A exfiltração pode ser relacionada a diferentes técnicas do MITRE ATT&CK.

Um exemplo central:

```text
T1041
Exfiltration Over C2 Channel
```

Outras possibilidades dependem do método utilizado:

```text
Exfiltration Over Web Service
Exfiltration Over Alternative Protocol
Exfiltration Over Physical Medium
Automated Exfiltration
```

O mapeamento deve refletir o comportamento observado.

Não basta classificar o incidente como:

```text
"Exfiltração"
```

É mais útil identificar:

```text
Como?
Para onde?
Por qual canal?
Qual técnica?
```

---

# Checklist do analista

```text
[ ] O alerta foi validado?

[ ] Qual usuário realizou a atividade?

[ ] Qual host iniciou a transferência?

[ ] Qual processo realizou a transferência?

[ ] Qual foi o Parent Process?

[ ] Qual era a Command Line?

[ ] Qual foi o destino?

[ ] O destino é conhecido?

[ ] O destino é corporativo?

[ ] Qual foi o volume transferido?

[ ] Qual foi o período?

[ ] Quais dados foram acessados?

[ ] Os dados eram sensíveis?

[ ] Houve compressão?

[ ] Houve staging?

[ ] Houve acesso anormal a diretórios?

[ ] Houve comprometimento de conta?

[ ] Houve malware?

[ ] Houve movimentação lateral?

[ ] Outros hosts acessaram o mesmo destino?

[ ] O destino foi bloqueado?

[ ] A conta foi contida?

[ ] Sessões e tokens foram revogados?

[ ] As evidências foram preservadas?

[ ] O impacto foi determinado?
```

---

# Métricas

Algumas métricas úteis:

```text
Volume de dados exfiltrados
Número de incidentes
Tempo até detecção
Tempo até contenção
Número de contas comprometidas
Número de endpoints envolvidos
Número de destinos externos
Quantidade de dados sensíveis envolvidos
```

Também é importante acompanhar:

```text
Primeiro acesso
      ↓
Coleta
      ↓
Exfiltração
      ↓
Detecção
      ↓
Contenção
```

Essa timeline mostra quanto tempo o atacante permaneceu dentro do ambiente antes de ser identificado.

---

# Lições aprendidas

Depois do incidente:

```text
Como o atacante obteve acesso?

Qual dado foi procurado?

Como o dado foi coletado?

Houve staging?

Qual canal foi utilizado?

Por que a transferência não foi detectada?

Existia DLP?

Os logs estavam disponíveis?

A conta possuía privilégios excessivos?

O destino era conhecido?

O mesmo comportamento ocorreu anteriormente?
```

Cada resposta pode gerar uma melhoria.

Exemplo:

```text
Falha de visibilidade
       ↓
Nova fonte de logs
       ↓
Nova regra
       ↓
Teste
       ↓
Melhor cobertura
```

---

# O que diferencia uma investigação superficial

Investigação superficial:

```text
Grande transferência detectada
       ↓
Bloquear IP
       ↓
Encerrar alerta
```

Investigação madura:

```text
Grande transferência
       ↓
Quem?
       ↓
Qual host?
       ↓
Qual processo?
       ↓
Quais dados?
       ↓
Qual destino?
       ↓
Como conseguiu acesso?
       ↓
Houve staging?
       ↓
Outros hosts?
       ↓
Outras contas?
       ↓
Qual foi o impacto?
```

A diferença está em descobrir **a história por trás do tráfego**.

---

# Threat Detection

Alguns use cases podem ser desenvolvidos:

### Transferência anormal

```text
Usuário
   ↓
Volume muito acima do baseline
   ↓
Destino incomum
```

### Compressão suspeita

```text
Grande quantidade de arquivos
   ↓
ZIP/RAR
   ↓
Transferência externa
```

### Exfiltração via Cloud

```text
Servidor
   ↓
Novo serviço cloud
   ↓
Upload em massa
```

### Conta comprometida + exfiltração

```text
Login anormal
   ↓
Acesso a dados sensíveis
   ↓
Download
   ↓
Transferência externa
```

Essa última combinação possui alto valor para correlação no SIEM.

---

# Defesa em profundidade

Uma boa arquitetura não depende de uma única ferramenta.

```text
Identity
    ↓
Detecta comportamento de login

EDR
    ↓
Detecta processos

DLP
    ↓
Detecta movimentação de dados

Network
    ↓
Detecta comunicação

SIEM
    ↓
Correlaciona eventos

SOC
    ↓
Investiga e responde
```

Cada camada cobre uma parte diferente do problema.

---

# Conclusão

Exfiltração de dados não deve ser tratada simplesmente como:

> **"Alguém transferiu muitos arquivos."**

O verdadeiro problema é:

> **"Informações que deveriam permanecer dentro do ambiente foram acessadas e transferidas para um destino não autorizado."**

A investigação precisa responder:

```text
Quem?
O quê?
Quando?
Onde?
Como?
Para onde?
Quanto?
Por quê?
```

Uma análise madura combina:

```text
SIEM
+
EDR
+
DLP
+
Network Logs
+
Identity
+
Threat Intelligence
+
UEBA
+
MITRE ATT&CK
```

O objetivo não é apenas bloquear a transferência.

É descobrir **como o atacante chegou aos dados, quais informações foram comprometidas, se outros sistemas foram envolvidos e se ainda existe capacidade de acesso ao ambiente**.

Em um SOC, a diferença entre:

```text
"Detectamos uma transferência."
```

e:

```text
"Identificamos o usuário, o processo, os dados, o destino,
o vetor inicial e o escopo do comprometimento."
```

é justamente a diferença entre **monitorar eventos e investigar incidentes**.
