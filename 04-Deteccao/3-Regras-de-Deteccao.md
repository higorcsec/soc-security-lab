# Regras de Detecção

## Introdução

Uma regra de detecção transforma um comportamento observado no ambiente em um evento que pode ser analisado por um SOC.

Na prática, ela responde a uma pergunta:

> **"Que comportamento eu quero identificar e quais evidências indicam que ele está acontecendo?"**

Uma regra bem construída não deve apenas gerar alertas.

Ela deve gerar **alertas que façam sentido**.

Esse detalhe é importante porque um SOC pode possuir milhares de eventos por dia e, ainda assim, ter pouca capacidade real de detecção.

O objetivo não é gerar o maior número possível de alertas.

É identificar os eventos que realmente merecem a atenção do analista.

---

# Anatomia de uma regra

Uma regra de detecção normalmente possui alguns componentes:

```text
Fonte de dados
      ↓
Condição
      ↓
Contexto
      ↓
Correlação
      ↓
Severidade
      ↓
Alerta
      ↓
Investigação
```

Exemplo:

```text
Fonte:
Windows Event Logs

Condição:
PowerShell executando comando codificado

Contexto:
Processo iniciado pelo Word

Correlação:
Conexão externa logo após a execução

Severidade:
Alta

Resultado:
Alerta para investigação
```

A diferença entre uma regra simples e uma boa regra geralmente está no **contexto**.

---

# O que uma boa regra precisa responder

Antes de criar uma regra, o analista deve conseguir responder:

### O que quero detectar?

Definir claramente o comportamento.

### Por que isso é importante?

Relacionar a ameaça ao risco do ambiente.

### Quais dados preciso?

Identificar as fontes de telemetria.

### Como vou identificar?

Definir a lógica da detecção.

### Qual será a severidade?

Determinar a prioridade.

### O que o analista fará depois?

Criar um procedimento inicial de investigação.

Se essas perguntas não estiverem claras, provavelmente a regra ainda não está madura.

---

# Fontes de dados

Uma detecção depende diretamente da qualidade dos dados disponíveis.

Algumas fontes comuns em um SOC:

```text
Windows Event Logs
Linux Logs
Firewall
DNS
Proxy
VPN
EDR
Antivírus
Web Server
Cloud
Active Directory
IAM
Aplicações
Banco de dados
```

Cada fonte oferece uma perspectiva diferente.

Por exemplo:

```text
Firewall
   ↓
Quem conversou com quem?

DNS
   ↓
Qual domínio foi consultado?

EDR
   ↓
Qual processo executou?

Windows
   ↓
Qual usuário iniciou?

Proxy
   ↓
Qual URL foi acessada?
```

Quando essas fontes são correlacionadas, o analista consegue construir uma visão muito mais completa.

---

# Regra baseada apenas em IOC

Uma das regras mais simples é procurar um indicador conhecido.

Exemplo:

```text
IF destination_ip IN Threat_Intelligence_List
THEN generate_alert
```

Esse tipo de regra possui valor.

Porém, ela possui limitações.

Um atacante pode:

```text
Trocar o IP
Trocar o domínio
Alterar o hash
Utilizar infraestrutura comprometida
Utilizar serviços legítimos
```

Por isso, IoCs devem ser utilizados como **uma camada da detecção**, e não como a única estratégia.

---

# Regra baseada em comportamento

Uma abordagem mais interessante é detectar comportamento.

Exemplo:

```text
IF
Process = powershell.exe
AND
CommandLine contains "EncodedCommand"
AND
ParentProcess = winword.exe

THEN
generate_alert
```

Aqui não estamos procurando apenas um arquivo conhecido.

Estamos procurando uma combinação de comportamentos.

Isso aumenta a capacidade de identificar variações de um ataque.

---

# Contexto é o que transforma evento em alerta

Considere:

```text
powershell.exe executado
```

Isso, isoladamente, pode ser completamente normal.

Agora:

```text
winword.exe
    ↓
powershell.exe
    ↓
EncodedCommand
    ↓
Download externo
```

O contexto mudou completamente.

A regra deve tentar identificar essa diferença.

Por isso, quanto mais contexto relevante estiver disponível, melhor será a capacidade de priorização.

---

# Exemplo: criação suspeita de usuário

Imagine uma organização onde usuários administrativos são controlados.

Um evento registra:

```text
New User Created
```

Uma regra extremamente simples poderia gerar alerta para qualquer criação de usuário.

Resultado:

```text
Centenas de alertas
```

Uma regra mais contextualizada poderia considerar:

```text
Novo usuário criado
+
Conta criada fora do horário esperado
+
Criador não pertence ao grupo administrativo
```

Ou:

```text
Novo usuário criado
+
Usuário adicionado ao grupo Administrators
```

Nesse caso, a relevância aumenta significativamente.

---

# Correlação de eventos

Uma das funções mais importantes de um SIEM é permitir correlação.

Imagine:

```text
Evento 1
Login bem-sucedido

        ↓ 2 minutos

Evento 2
PowerShell executado

        ↓ 1 minuto

Evento 3
Download de arquivo

        ↓ 30 segundos

Evento 4
Conexão com IP externo suspeito
```

Cada evento isoladamente pode parecer aceitável.

Juntos, podem indicar uma atividade maliciosa.

Uma lógica de correlação poderia ser:

```text
Login
+
Execução suspeita
+
Download
+
Conexão suspeita
=
Alerta de alta prioridade
```

Esse é um dos principais motivos para centralizar logs em um SIEM.

---

# Exemplo de regra com Sigma

O formato Sigma permite representar regras de detecção de forma independente da plataforma.

Um exemplo simplificado:

```yaml
title: Suspicious PowerShell From Microsoft Word
id: example-001
status: experimental

logsource:
    category: process_creation
    product: windows

detection:
    selection:
        ParentImage|endswith: '\WINWORD.EXE'
        Image|endswith: '\powershell.exe'

    condition: selection

level: high

tags:
    - attack.execution
    - attack.t1059.001
```

A ideia não é simplesmente escrever YAML.

É criar uma lógica que possa ser compreendida, revisada e, quando necessário, convertida para diferentes plataformas.

---

# Exemplo de regra no Wazuh

Em um ambiente utilizando Wazuh, uma regra pode utilizar eventos recebidos pelos agentes e aplicar condições específicas.

Exemplo conceitual:

```xml
<rule id="100001" level="12">
    <if_group>windows</if_group>
    <match>powershell</match>
    <description>
        Suspicious PowerShell execution detected
    </description>
</rule>
```

Na prática, regras de produção devem ser construídas de acordo com os campos e eventos realmente disponíveis no ambiente.

O ponto principal é entender que o mecanismo de detecção depende da **qualidade da telemetria coletada**.

---

# Severidade

Nem todo alerta deve receber a mesma prioridade.

Uma classificação simples poderia ser:

```text
LOW
Informação ou atividade de baixo risco

MEDIUM
Comportamento anômalo que precisa de análise

HIGH
Comportamento potencialmente malicioso

CRITICAL
Indícios fortes de comprometimento ou impacto significativo
```

A severidade não deve ser baseada apenas no nome da regra.

O contexto também importa.

Por exemplo:

```text
PowerShell executado
```

pode ser normal.

Enquanto:

```text
PowerShell
+
EncodedCommand
+
Word
+
Conexão externa
+
Endpoint crítico
```

merece uma prioridade muito maior.

---

# Prioridade não é a mesma coisa que severidade

Esses conceitos podem parecer iguais, mas não necessariamente são.

### Severidade

Representa o potencial de risco do evento.

### Prioridade

Representa a urgência com que o SOC deve tratar aquele alerta.

Exemplo:

```text
Severidade: High

Mas:
Host de laboratório
Atividade autorizada
Usuário conhecido

Prioridade: Baixa
```

Outro cenário:

```text
Severidade: High

Host:
Servidor de produção

Usuário:
Conta administrativa

Atividade:
Não autorizada

Prioridade: Crítica
```

Esse tipo de contexto deve ser considerado durante o processo de triagem.

---

# Regras muito genéricas

Um dos maiores problemas em ambientes de SOC é criar regras genéricas demais.

Exemplo:

```text
IF PowerShell
THEN Alert
```

Isso provavelmente gera muito ruído.

Outra regra:

```text
IF login failed
THEN Alert
```

Também pode gerar milhares de eventos legítimos.

O problema não está necessariamente no evento.

Está na **falta de contexto**.

Uma abordagem melhor seria identificar padrões.

Por exemplo:

```text
50 falhas de login
+
mesma conta
+
múltiplos hosts
+
intervalo curto
```

Agora existe um comportamento que merece investigação.

---

# Threshold e comportamento

Thresholds podem ser utilizados para detectar repetição de eventos.

Exemplo:

```text
Mais de 20 falhas de autenticação
em 5 minutos
para a mesma conta
```

Ou:

```text
Mais de 10 hosts
acessando o mesmo domínio
em 2 minutos
```

Ou:

```text
Mais de 30 conexões
para portas incomuns
em poucos segundos
```

O threshold precisa ser baseado no comportamento real do ambiente.

Um número escolhido arbitrariamente pode gerar:

```text
Falsos positivos
```

ou:

```text
Falsos negativos
```

Por isso, regras devem ser testadas e ajustadas.

---

# Baseline

Em alguns casos, o melhor indicador de comportamento suspeito é aquilo que foge do padrão normal.

Exemplo:

```text
Servidor normalmente acessa:

443
80
53
```

De repente:

```text
Servidor
      ↓
Conexão para porta 4444
      ↓
IP externo
```

O evento merece investigação.

Outro exemplo:

```text
Usuário normalmente trabalha:

08:00 → 18:00
```

E aparece:

```text
Login administrativo
03:17
```

Isso não significa automaticamente um ataque.

Mas pode ser um desvio de comportamento relevante.

A detecção baseada em baseline tenta encontrar exatamente esse tipo de anomalia.

---

# Detecção em camadas

Uma estratégia eficiente é combinar diferentes níveis de detecção.

```text
Camada 1
IOC

       ↓

Camada 2
Comportamento

       ↓

Camada 3
Correlação

       ↓

Camada 4
Anomalia

       ↓

Camada 5
Contexto do ativo
```

Exemplo:

```text
IP suspeito
+
PowerShell
+
usuário administrativo
+
servidor crítico
+
atividade fora do horário
```

Quanto mais evidências independentes apontarem para o mesmo comportamento, maior pode ser a confiança da detecção.

---

# MITRE ATT&CK na regra

Uma regra também pode ser associada a uma técnica do MITRE ATT&CK.

Exemplo:

```text
Nome:
Suspicious PowerShell Execution

Tática:
Execution

Técnica:
T1059.001 - PowerShell

Fonte:
Windows Event Logs / EDR

Severidade:
High
```

Isso permite que o SOC acompanhe quais comportamentos estão sendo cobertos.

Também facilita identificar gaps.

---

# Exemplo completo

## Detecção: PowerShell suspeito

### Objetivo

Identificar execução de PowerShell associada a comportamento potencialmente malicioso.

### Telemetria

```text
Process Creation
PowerShell Logs
Windows Event Logs
EDR
Network Connections
```

### Condições

```text
PowerShell
+
Parent Process suspeito
+
EncodedCommand
```

### Enriquecimento

```text
Usuário
Host
IP
Hash
Domínio
Threat Intelligence
```

### MITRE ATT&CK

```text
T1059.001
PowerShell
```

### Severidade

```text
High
```

### Investigação inicial

```text
1. Identificar o usuário.
2. Identificar o endpoint.
3. Analisar o processo pai.
4. Analisar a command line.
5. Verificar conexões externas.
6. Pesquisar o hash.
7. Verificar recorrência.
8. Procurar eventos semelhantes no ambiente.
```

### Possível resposta

```text
Se confirmado:

→ Isolar endpoint
→ Bloquear IOC
→ Coletar evidências
→ Identificar escopo
→ Verificar persistência
→ Escalar incidente
```

---

# Testando uma regra

Uma regra não deve entrar diretamente em produção apenas porque parece correta.

Um processo básico de validação pode ser:

```text
Criar regra
   ↓
Executar teste controlado
   ↓
Verificar se detectou
   ↓
Verificar eventos legítimos
   ↓
Medir falsos positivos
   ↓
Ajustar
   ↓
Testar novamente
   ↓
Publicar
```

Em ambientes mais maduros, regras podem ser testadas contra conjuntos de eventos conhecidos ou simulações controladas.

O objetivo é descobrir duas coisas:

```text
A regra detecta o que deveria?

A regra alerta para coisas que não deveria?
```

---

# Tuning

Depois de publicada, uma regra não deve ser considerada "final".

O ambiente muda.

Aplicações mudam.

Usuários mudam.

Processos mudam.

Infraestrutura muda.

Consequentemente, as regras também precisam evoluir.

Exemplo:

```text
Regra inicial
↓
1000 alertas/dia
↓
900 falsos positivos
↓
Análise
↓
Adição de contexto
↓
80 alertas/dia
↓
60 relevantes
```

Esse processo é chamado de **tuning**.

O objetivo não é simplesmente diminuir a quantidade de alertas.

É aumentar a proporção de alertas que realmente merecem investigação.

---

# Métricas de uma regra

Algumas métricas úteis:

### Volume

Quantos alertas a regra gera?

### Taxa de falsos positivos

Quantos alertas são classificados como benignos?

### Taxa de verdadeiro positivo

Quantos alertas realmente indicam comportamento relevante?

### Tempo de triagem

Quanto tempo o analista leva para entender o alerta?

### Tempo de resposta

Quanto tempo leva até uma ação ser tomada?

### Cobertura

Qual comportamento ou técnica a regra consegue detectar?

Uma regra que gera 10.000 alertas por dia não é necessariamente melhor que uma que gera 50.

O valor está na **qualidade do sinal**.

---

# O alerta precisa contar uma história

Uma regra ruim pode produzir:

```text
ALERT:
Suspicious activity detected.
```

O analista agora precisa descobrir tudo sozinho.

Uma regra melhor pode entregar:

```text
ALERT:
Suspicious PowerShell Execution

Host:
WS-014

User:
usuario01

Parent:
WINWORD.EXE

Process:
powershell.exe

Command:
EncodedCommand

Destination:
185.x.x.x

MITRE:
T1059.001

Severity:
High
```

A segunda abordagem reduz o tempo necessário para iniciar a investigação.

Esse é um dos objetivos da engenharia de detecção:

> **Não apenas detectar o evento, mas entregar contexto suficiente para o analista tomar uma decisão.**

---

# Checklist para criação de regras

Antes de publicar uma regra:

```text
[ ] O objetivo da regra está claro?

[ ] A fonte de dados é confiável?

[ ] Os campos necessários existem?

[ ] A condição realmente representa o comportamento?

[ ] Existe contexto suficiente?

[ ] A regra possui MITRE ATT&CK quando aplicável?

[ ] A severidade foi definida?

[ ] O volume esperado foi analisado?

[ ] Falsos positivos foram considerados?

[ ] A regra foi testada?

[ ] Existe procedimento de investigação?

[ ] A regra possui documentação?

[ ] Existe processo de tuning?
```

---

# O princípio mais importante

Uma regra de detecção não deve ser criada porque:

> "Seria interessante detectar isso."

Ela deve existir porque:

> **"Se esse comportamento acontecer no nosso ambiente, precisamos saber."**

Essa diferença parece pequena, mas muda completamente a qualidade de um programa de detecção.

---

# Conclusão

Criar regras de detecção é uma combinação de:

```text
Conhecimento de ameaças
+
Conhecimento do ambiente
+
Telemetria
+
Lógica
+
Contexto
+
Testes
+
Tuning
```

O objetivo final não é construir uma coleção de regras.

É construir uma capacidade de **enxergar comportamentos relevantes dentro de uma grande quantidade de eventos**.

Uma regra eficiente consegue transformar:

```text
Milhares de eventos
        ↓
Contexto
        ↓
Sinal
        ↓
Alerta
        ↓
Investigação
```

No fim, a qualidade de uma detecção não é medida pela quantidade de alertas que ela produz.

É medida pela capacidade de **encontrar o que realmente importa sem esconder o analista em uma avalanche de ruído**.
