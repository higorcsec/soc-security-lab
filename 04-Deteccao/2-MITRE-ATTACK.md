# MITRE ATT&CK

## Introdução

O **MITRE ATT&CK** é uma base de conhecimento utilizada para representar o comportamento de adversários durante ataques cibernéticos.

Em vez de pensar apenas em:

> "Qual malware está sendo utilizado?"

o ATT&CK ajuda o analista a pensar:

> "O que o atacante está tentando fazer e como ele está fazendo isso?"

Essa mudança de perspectiva é importante para um SOC porque ferramentas, arquivos, IPs e domínios podem mudar rapidamente.

As técnicas utilizadas para atingir um objetivo, porém, podem permanecer semelhantes.

Por isso, o MITRE ATT&CK é especialmente útil para **detecção, investigação, threat hunting, avaliação de cobertura e resposta a incidentes**.

---

## Táticas, Técnicas e Subtécnicas

A estrutura do ATT&CK pode ser entendida em três níveis principais:

```text
Tática
   ↓
Técnica
   ↓
Subtécnica
```

### Tática

Representa o **objetivo** do atacante.

Exemplos:

* Initial Access
* Execution
* Persistence
* Privilege Escalation
* Defense Evasion
* Credential Access
* Discovery
* Lateral Movement
* Collection
* Command and Control
* Exfiltration
* Impact

A pergunta aqui é:

> **"O que o adversário quer alcançar?"**

---

### Técnica

Representa uma forma utilizada pelo atacante para atingir esse objetivo.

Por exemplo:

```text
Tática:
Credential Access

        ↓

Técnica:
OS Credential Dumping
```

Nesse caso, o objetivo é obter credenciais e a técnica descreve uma maneira de realizar isso.

---

### Subtécnica

É uma especialização de uma técnica.

Por exemplo:

```text
OS Credential Dumping
        ↓
LSASS Memory
```

Isso permite que a análise seja muito mais específica.

Em um SOC, essa granularidade é útil porque diferentes técnicas exigem diferentes fontes de log e estratégias de detecção.

---

# ATT&CK como linguagem do SOC

Uma das maiores vantagens do ATT&CK é criar uma linguagem comum entre diferentes áreas.

Imagine que um analista registre apenas:

```text
"Usuário executou PowerShell suspeito."
```

Essa informação é útil, mas limitada.

Agora imagine:

```text
Tática:
Execution

Técnica:
Command and Scripting Interpreter

Subtécnica:
PowerShell
```

A atividade passa a estar contextualizada dentro de uma técnica conhecida de comportamento adversário.

Isso facilita:

* documentação;
* investigação;
* criação de regras;
* threat hunting;
* comunicação entre equipes;
* análise de cobertura;
* classificação de incidentes.

---

# Exemplo de ataque

Considere um cenário simples:

```text
Phishing
   ↓
Documento malicioso
   ↓
PowerShell
   ↓
Download de payload
   ↓
Persistência
   ↓
Credential Access
   ↓
Lateral Movement
```

O atacante não está realizando uma única ação.

Ele está executando uma **cadeia de técnicas**.

Podemos representar essa cadeia de maneira conceitual:

```text
Initial Access
      ↓
Execution
      ↓
Persistence
      ↓
Credential Access
      ↓
Lateral Movement
      ↓
Command and Control
```

Esse modelo ajuda o analista a entender que um alerta isolado pode ser apenas uma parte de uma operação maior.

---

# ATT&CK e detecção

O ATT&CK não é simplesmente uma lista de ataques.

Para um SOC, seu maior valor está em relacionar:

```text
Comportamento
      ↓
Técnica ATT&CK
      ↓
Telemetria necessária
      ↓
Regra de detecção
      ↓
Alerta
      ↓
Investigação
```

Por exemplo:

```text
Comportamento:
PowerShell executado de maneira suspeita

        ↓

ATT&CK:
Command and Scripting Interpreter:
PowerShell

        ↓

Telemetria:
Process Creation
PowerShell Logs
Windows Event Logs

        ↓

Detecção:
PowerShell + comportamento suspeito

        ↓

Alerta:
Possível execução maliciosa
```

Esse raciocínio é muito mais útil do que simplesmente criar regras baseadas em palavras-chave.

---

# Telemetria é parte da detecção

Uma técnica só pode ser detectada se o ambiente possuir **telemetria suficiente**.

Por exemplo, uma organização pode querer detectar execução suspeita de PowerShell.

Mas se ela não coleta:

```text
Process Creation
PowerShell Events
Command Line
Parent Process
User
Host
Network Connections
```

a capacidade de investigação fica limitada.

Por isso, uma pergunta importante durante o desenvolvimento de uma detecção é:

> **"Qual evidência eu preciso enxergar para identificar essa técnica?"**

Esse pensamento evita criar regras que parecem boas no papel, mas não funcionam no ambiente real.

---

# Exemplo de detecção

Imagine o seguinte evento:

```text
Host: WS-014
User: usuario01

Parent Process:
WINWORD.EXE

Process:
powershell.exe

Command:
powershell.exe -EncodedCommand ...
```

O analista pode relacionar esse comportamento a:

```text
Tática:
Execution

Técnica:
Command and Scripting Interpreter

Subtécnica:
PowerShell
```

Mas o mapeamento ATT&CK não deve ser o fim da análise.

O próximo passo é investigar:

```text
Quem executou?
      ↓
Qual documento?
      ↓
De onde veio?
      ↓
Qual comando foi executado?
      ↓
Qual processo foi criado?
      ↓
Houve conexão externa?
      ↓
O payload foi baixado?
      ↓
Houve persistência?
```

O ATT&CK ajuda a organizar a investigação.

Ele não substitui a investigação.

---

# ATT&CK e Threat Hunting

O framework também pode ser utilizado de maneira proativa.

Em vez de esperar um alerta, o analista pode escolher uma técnica e perguntar:

> **"Consigo encontrar evidências dessa técnica no meu ambiente?"**

Por exemplo:

```text
Técnica:
PowerShell

Hunting:
Pesquisar execução de PowerShell
        ↓
Identificar usuários
        ↓
Identificar hosts
        ↓
Analisar comandos
        ↓
Correlacionar processos
        ↓
Pesquisar conexões externas
```

Isso transforma o ATT&CK em uma espécie de **mapa para investigação**.

O analista deixa de depender exclusivamente dos alertas gerados pelas ferramentas.

---

# Matriz de cobertura

Uma aplicação muito interessante do ATT&CK dentro de um SOC é avaliar a cobertura de detecção.

Exemplo simplificado:

| Tática              | Técnica                      | Existe detecção? | Telemetria         |
| ------------------- | ---------------------------- | ---------------: | ------------------ |
| Execution           | PowerShell                   |              Sim | Windows / EDR      |
| Persistence         | Scheduled Task               |              Sim | Windows            |
| Credential Access   | LSASS                        |          Parcial | EDR                |
| Discovery           | System Information Discovery |              Não | —                  |
| Lateral Movement    | SMB/Windows Admin Shares     |          Parcial | Firewall / Windows |
| Command and Control | Application Layer Protocol   |              Sim | Firewall / Proxy   |

Isso revela um problema que simplesmente olhar para a quantidade de regras não mostra.

Uma organização pode possuir centenas de regras e ainda ter **grandes áreas sem cobertura**.

---

# Gap de detecção

Imagine que o ambiente possua:

```text
100 regras de detecção
```

Isso não significa necessariamente que exista uma boa cobertura.

Após mapear as regras ao ATT&CK, podemos descobrir:

```text
Execution          ██████████
Persistence        ███████
Credential Access  ███
Discovery          ██
Lateral Movement   ████
Exfiltration       █
```

Agora existe uma visão muito mais clara de onde estão os gaps.

A pergunta deixa de ser:

> "Quantas regras temos?"

e passa a ser:

> "Quais comportamentos relevantes ainda não conseguimos detectar?"

Essa é uma pergunta muito mais útil para uma equipe de segurança.

---

# ATT&CK e SIEM

Ferramentas como SIEM e plataformas de segurança podem utilizar o ATT&CK como camada de organização das detecções.

Um alerta pode carregar informações como:

```text
Rule:
Suspicious PowerShell Execution

Severity:
High

MITRE ATT&CK:
T1059.001

Tactic:
Execution
```

Isso facilita a análise posterior.

O analista pode visualizar:

```text
Host
 ↓
Usuário
 ↓
Alerta
 ↓
Técnica ATT&CK
 ↓
Outros eventos relacionados
```

Com isso, diferentes alertas podem ser agrupados por comportamento.

---

# Correlação entre técnicas

Um dos pontos mais interessantes aparece quando diferentes técnicas começam a aparecer no mesmo host.

Exemplo:

```text
09:14
PowerShell
       ↓
Execution

09:16
Credential Dumping
       ↓
Credential Access

09:21
Remote Service
       ↓
Lateral Movement

09:25
C2 Connection
       ↓
Command and Control
```

Individualmente, cada evento pode exigir investigação.

Em conjunto, eles podem representar uma cadeia de ataque muito mais clara.

Esse é um dos motivos pelos quais o ATT&CK é útil para análise de incidentes: ele ajuda a transformar eventos técnicos em **comportamentos relacionados**.

---

# ATT&CK não é uma checklist

Um erro comum é tratar o ATT&CK como uma lista que precisa ser completamente preenchida.

Isso pode gerar uma falsa sensação de segurança.

Ter uma técnica marcada como:

```text
Detectada
```

não significa necessariamente que a detecção seja boa.

É preciso avaliar:

* Qual é a fonte de dados?
* Qual é a cobertura?
* Qual é a taxa de falsos positivos?
* Qual é o tempo de resposta?
* A regra consegue detectar diferentes variações?
* O atacante consegue facilmente contornar a detecção?
* O alerta possui contexto suficiente?

Uma detecção precisa ser **operacionalmente útil**, não apenas existir na matriz.

---

# ATT&CK e engenharia de detecção

O framework também pode orientar o desenvolvimento de novas regras.

Um fluxo possível:

```text
Escolher técnica
      ↓
Entender o comportamento
      ↓
Identificar telemetria
      ↓
Definir lógica
      ↓
Criar regra
      ↓
Testar
      ↓
Medir falsos positivos
      ↓
Ajustar
      ↓
Mapear para ATT&CK
      ↓
Monitorar
```

Esse processo aproxima o ATT&CK da realidade de um Detection Engineer.

A técnica deixa de ser apenas documentação e passa a servir como **requisito para engenharia de detecção**.

---

# Exemplo de documentação de uma regra

Uma regra pode ser documentada desta forma:

```text
Nome:
Suspicious PowerShell Execution

Objetivo:
Identificar execução de PowerShell associada a comportamentos suspeitos.

MITRE ATT&CK:
T1059.001 - PowerShell

Tática:
Execution

Fonte:
Windows Event Logs
EDR

Lógica:
PowerShell
+
CommandLine suspeita
+
Parent Process incomum

Severidade:
High

Resposta inicial:
1. Identificar usuário
2. Identificar endpoint
3. Analisar processo pai
4. Analisar command line
5. Verificar conexões externas
6. Pesquisar recorrência no ambiente
```

Esse tipo de documentação é muito mais útil para uma equipe do que uma regra sem contexto.

---

# Limitações do ATT&CK

O MITRE ATT&CK é extremamente útil, mas não deve ser tratado como uma solução completa de segurança.

Ele não informa sozinho:

* quais logs sua organização precisa coletar;
* qual regra deve ser criada;
* qual severidade um alerta deve receber;
* se determinado evento é benigno;
* como responder a um incidente específico.

Essas decisões dependem do ambiente.

Uma técnica pode ser extremamente relevante em uma empresa e pouco relevante em outra.

Por isso, o ATT&CK deve ser utilizado como **referência para raciocínio e organização**, e não como substituto da análise técnica.

---

# Como um analista deve pensar

Um bom exercício ao analisar uma técnica é fazer cinco perguntas:

### 1. O que o atacante quer fazer?

Identificar a tática.

### 2. Como ele pode fazer isso?

Identificar a técnica.

### 3. Que evidência ele deixa?

Identificar a telemetria.

### 4. Como podemos detectar?

Criar a lógica.

### 5. O que faremos quando detectar?

Definir investigação e resposta.

Em resumo:

```text
Objetivo
   ↓
Técnica
   ↓
Evidência
   ↓
Detecção
   ↓
Investigação
   ↓
Resposta
```

Esse é o caminho entre o conhecimento do atacante e uma detecção operacional.

---

# Conclusão

O MITRE ATT&CK é mais valioso quando deixa de ser apenas uma matriz visual e passa a fazer parte do processo de trabalho do SOC.

Ele pode ajudar a:

* entender o comportamento adversário;
* classificar incidentes;
* criar regras de detecção;
* orientar threat hunting;
* identificar gaps;
* medir cobertura;
* organizar investigações;
* melhorar a comunicação entre equipes.

A principal ideia é simples:

```text
MITRE ATT&CK
      ↓
Entender o comportamento
      ↓
Descobrir quais evidências procurar
      ↓
Construir a detecção
      ↓
Investigar o alerta
```

Uma boa equipe de SOC não utiliza o ATT&CK apenas para dizer **"qual técnica aconteceu"**.

Utiliza o framework para responder:

> **"Se essa técnica fosse utilizada contra o nosso ambiente hoje, nós conseguiríamos enxergá-la?"**

Essa pergunta transforma o ATT&CK de uma matriz de conhecimento em uma ferramenta de **engenharia de defesa**.
