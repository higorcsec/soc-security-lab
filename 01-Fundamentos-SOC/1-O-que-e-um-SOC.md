# O que é um SOC?

## Security Operations Center

Um **SOC (Security Operations Center)** é o centro responsável por **monitorar, detectar, investigar e responder a eventos de segurança** dentro de uma organização.

Na prática, o SOC funciona como uma espécie de **torre de controle da segurança da empresa**.

Enquanto sistemas, servidores, endpoints, firewalls, aplicações e usuários geram milhares de eventos todos os dias, o SOC trabalha para identificar quais desses eventos representam um comportamento normal e quais podem indicar uma **ameaça ou incidente de segurança**.

> **O objetivo de um SOC não é apenas detectar ataques. É entender o que está acontecendo, determinar o risco e agir antes que um evento se transforme em um problema maior.**

---

## Por que um SOC é necessário?

Uma empresa moderna possui diversos pontos que precisam ser protegidos:

* Servidores
* Estações de trabalho
* Notebooks
* Dispositivos móveis
* Firewalls
* Aplicações web
* Bancos de dados
* Serviços em nuvem
* Contas corporativas
* Active Directory
* APIs
* Redes corporativas

Todos esses ambientes produzem informações constantemente.

Um login realizado às 9h pode ser completamente normal.

O mesmo login realizado às 3h da manhã, vindo de um país diferente e seguido por diversas tentativas de acesso a servidores internos, pode ser um **indicador de comprometimento**.

É nesse tipo de situação que o SOC entra em ação.

---

# Como funciona um SOC?

De forma simplificada, podemos representar o funcionamento de um SOC da seguinte maneira:

```text
       FONTES DE LOGS
             │
             ▼
 ┌───────────────────────┐
 │ Coleta de informações │
 └───────────┬───────────┘
             │
             ▼
 ┌───────────────────────┐
 │ SIEM / Monitoramento  │
 └───────────┬───────────┘
             │
             ▼
 ┌───────────────────────┐
 │ Detecção de eventos   │
 └───────────┬───────────┘
             │
             ▼
 ┌───────────────────────┐
 │ Análise e investigação│
 └───────────┬───────────┘
             │
             ▼
 ┌───────────────────────┐
 │ Resposta ao incidente │
 └───────────┬───────────┘
             │
             ▼
 ┌───────────────────────┐
 │ Contenção / Mitigação │
 └───────────────────────┘
```

O processo pode variar de acordo com a maturidade da empresa, mas normalmente envolve quatro grandes atividades:

**Monitorar → Detectar → Investigar → Responder**

---

# O que um SOC monitora?

Um SOC pode receber informações de praticamente qualquer ambiente relevante para a segurança da organização.

### Endpoints

Computadores e servidores podem gerar eventos relacionados a:

* Processos executados
* Criação de arquivos
* Alterações no sistema
* Tentativas de login
* Execução de scripts
* Instalação de softwares
* Alterações de privilégios

Ferramentas de **EDR/XDR** e agentes instalados nos endpoints ajudam a fornecer essa visibilidade.

---

### Firewalls e dispositivos de rede

Podem fornecer informações como:

* Conexões permitidas
* Conexões bloqueadas
* IPs de origem e destino
* Portas utilizadas
* Protocolos
* Tentativas de acesso suspeitas

Esses dados podem ajudar a identificar atividades como **port scanning, comunicação com IPs maliciosos ou tráfego fora do padrão**.

---

### Servidores

Logs de servidores podem revelar:

* Tentativas de autenticação
* Escalação de privilégios
* Execução de comandos
* Alterações de configuração
* Criação de usuários
* Erros de aplicação

Em ambientes Linux, por exemplo, logs de autenticação podem ser extremamente importantes durante uma investigação.

---

### Aplicações

Aplicações web e APIs também produzem eventos importantes.

Exemplos:

```text
Login realizado
Falha de autenticação
Alteração de senha
Criação de usuário
Requisição HTTP suspeita
Erro de aplicação
Tentativa de acesso a recurso inexistente
```

Quando analisados em conjunto, esses eventos podem revelar comportamentos que não seriam percebidos olhando apenas para um único log.

---

# O papel do SIEM

Um dos principais componentes de um SOC é o **SIEM (Security Information and Event Management)**.

O SIEM centraliza eventos provenientes de diferentes fontes e permite:

* Coletar logs
* Normalizar informações
* Correlacionar eventos
* Criar regras de detecção
* Gerar alertas
* Pesquisar históricos
* Apoiar investigações

Alguns exemplos de soluções utilizadas em ambientes de segurança são:

* Wazuh
* Splunk
* IBM QRadar
* Microsoft Sentinel

O SIEM, porém, não substitui o analista.

Ele pode indicar:

> "Existe algo suspeito acontecendo."

Mas cabe ao profissional de segurança entender:

> "Por que isso aconteceu, qual é o impacto e o que devemos fazer?"

Essa diferença é importante.

---

# Evento, alerta e incidente

Um dos conceitos fundamentais para quem está começando em SOC é entender que **evento, alerta e incidente não são a mesma coisa**.

### Evento

É uma atividade registrada por algum sistema.

Exemplo:

```text
Usuário Higor realizou login às 08:42.
```

Isso, isoladamente, não significa que existe um problema.

---

### Alerta

É quando uma ferramenta identifica um comportamento que merece atenção.

Exemplo:

```text
10 tentativas de login falharam
em menos de 1 minuto para o mesmo usuário.
```

Agora existe algo que precisa ser investigado.

---

### Incidente

É quando a investigação determina que existe uma situação de segurança que pode comprometer a organização.

Exemplo:

```text
As tentativas de login foram realizadas por um
IP externo e, posteriormente, houve um login
bem-sucedido utilizando a mesma conta.
```

Nesse cenário, pode existir uma tentativa de comprometimento de credenciais.

---

# O SOC trabalha com contexto

Um dos maiores desafios de um SOC é que **nem todo alerta representa um ataque**.

Imagine o seguinte alerta:

```text
PowerShell executado em um computador.
```

Isso pode parecer suspeito.

Mas PowerShell também é utilizado diariamente por administradores e equipes de TI.

Agora considere:

```text
PowerShell
      +
download de arquivo externo
      +
execução de script
      +
conexão com IP desconhecido
```

O contexto mudou completamente.

Por isso, um bom analista não deve simplesmente olhar para um alerta e perguntar:

> "É malicioso?"

Ele precisa perguntar:

> **"O que aconteceu antes, durante e depois desse evento?"**

Essa mentalidade é fundamental para trabalhar em SOC.

---

# Principais atividades de um SOC

Dependendo da estrutura da empresa, um SOC pode realizar diversas atividades.

### Monitoramento

Acompanhamento contínuo dos ambientes para identificar comportamentos suspeitos.

### Triagem

Análise inicial dos alertas para determinar:

* O que aconteceu?
* Qual ativo foi afetado?
* Qual usuário está envolvido?
* O evento é verdadeiro ou falso positivo?
* Existe risco imediato?

### Investigação

Busca por evidências adicionais para entender a origem e o impacto do evento.

### Resposta

Ações tomadas para controlar ou eliminar a ameaça.

Exemplos:

* Bloquear IP
* Desabilitar conta
* Isolar endpoint
* Encerrar processo
* Remover artefato malicioso
* Bloquear domínio
* Aplicar regra no firewall

### Threat Intelligence

Utilização de informações sobre ameaças conhecidas para melhorar a capacidade de detecção.

### Threat Hunting

Busca proativa por comportamentos suspeitos que ainda não necessariamente geraram um alerta.

---

# SOC e resposta a incidentes

O SOC possui uma relação direta com **Incident Response (IR)**.

Quando um alerta é confirmado como incidente, a equipe precisa seguir um processo organizado.

Uma visão simplificada seria:

```text
Detectar
   ↓
Analisar
   ↓
Classificar
   ↓
Conter
   ↓
Erradicar
   ↓
Recuperar
   ↓
Aprender
```

O objetivo não é apenas resolver o problema atual.

Depois de um incidente, a organização deve buscar entender:

* Como o ataque aconteceu?
* Por que ele foi possível?
* Por que os controles não impediram o ataque?
* O que poderia ter detectado o comportamento antes?
* Como evitar que aconteça novamente?

---

# O fator humano

Apesar de toda a tecnologia envolvida, um SOC não é simplesmente uma coleção de ferramentas.

Um ambiente pode possuir:

```text
SIEM
EDR
Firewall
IDS/IPS
Threat Intelligence
SOAR
```

e ainda assim possuir uma segurança ruim se os profissionais não souberem interpretar os dados.

O conhecimento do analista é fundamental para:

* Interpretar eventos
* Correlacionar informações
* Identificar anomalias
* Fazer perguntas durante a investigação
* Separar falso positivo de incidente real
* Tomar decisões
* Documentar evidências

Por isso, trabalhar em SOC exige muito mais do que conhecer uma ferramenta específica.

---

# SOC não é apenas "ficar olhando alertas"

Existe uma visão equivocada de que trabalhar em SOC significa apenas receber alertas e encerrá-los.

Na realidade, o trabalho envolve bastante investigação.

Um alerta pode levar o analista a consultar:

```text
SIEM
   ↓
Logs do firewall
   ↓
Endpoint
   ↓
Active Directory
   ↓
DNS
   ↓
Proxy
   ↓
Threat Intelligence
   ↓
MITRE ATT&CK
```

O objetivo é construir uma visão completa do que aconteceu.

É quase como montar um quebra-cabeça utilizando evidências técnicas.

---

# Exemplo prático

Imagine que o SIEM gere o seguinte alerta:

```text
Multiple Failed Login Attempts
```

O analista começa a investigação.

### 1. Identificar o usuário

```text
usuario: higor
```

### 2. Verificar origem

```text
IP: 185.xxx.xxx.xxx
País: desconhecido
```

### 3. Analisar quantidade de tentativas

```text
37 tentativas
em aproximadamente 2 minutos
```

### 4. Verificar se houve sucesso

```text
Falhas: 37
Sucesso: 1
```

### 5. Correlacionar eventos

O analista verifica se depois do login ocorreram:

* Acesso a arquivos sensíveis
* Criação de usuários
* Alteração de privilégios
* Execução de comandos
* Acesso a servidores

Nesse ponto, o alerta deixou de ser apenas uma sequência de tentativas de login.

Existe um possível comprometimento de conta que precisa ser investigado.

---

# O que diferencia um bom SOC?

Um SOC eficiente não deve medir seu sucesso apenas pela quantidade de alertas encerrados.

Também é importante avaliar:

* Tempo para detectar uma ameaça
* Tempo para responder
* Qualidade das investigações
* Taxa de falsos positivos
* Cobertura de logs
* Qualidade das regras de detecção
* Capacidade de identificar novas ameaças
* Tempo de contenção de incidentes

Alguns indicadores importantes são conhecidos como **MTTD (Mean Time to Detect)** e **MTTR (Mean Time to Respond/Remediate)**.

Quanto mais rapidamente uma organização consegue detectar e responder a uma ameaça, menor tende a ser o impacto potencial do incidente.

---

# O que estou buscando demonstrar neste laboratório

Este repositório não tem como objetivo apenas listar comandos ou explicar ferramentas.

A proposta é construir uma visão prática de como funciona uma operação de segurança.

Ao longo dos próximos tópicos, serão abordados conceitos como:

```text
SOC
 ├── Fundamentos
 ├── N1 / N2 / N3
 ├── Monitoramento
 ├── SIEM
 ├── Logs
 ├── Detecção
 ├── Investigação
 ├── Resposta a Incidentes
 ├── Threat Intelligence
 ├── Threat Hunting
 └── Indicadores de Segurança
```

A ideia é conectar **conceito + ferramenta + investigação + tomada de decisão**.

---

## Conclusão

Um SOC é muito mais do que uma equipe responsável por acompanhar alertas.

Ele representa uma operação contínua de **visibilidade, detecção, investigação e resposta**.

Para trabalhar nessa área, não basta saber que uma ferramenta gerou um alerta. É necessário entender **o que o alerta representa, qual contexto existe ao redor dele e qual ação deve ser tomada**.

É justamente essa capacidade de transformar dados em decisões de segurança que torna o trabalho de um analista de SOC tão importante dentro de uma organização.

> **Segurança não é impedir todos os eventos. É ter visibilidade suficiente para identificar rapidamente o que importa e capacidade para responder quando algo realmente acontece.**
