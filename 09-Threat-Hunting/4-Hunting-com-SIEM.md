# Hunting com SIEM

O SIEM é uma das principais ferramentas utilizadas para centralizar e analisar os dados necessários para Threat Hunting.

Ele permite reunir informações que normalmente estariam espalhadas em diferentes sistemas:

```text
Windows
Linux
Firewall
DNS
Proxy
EDR
VPN
Cloud
Aplicações
        ↓
      SIEM
        ↓
   Investigação
        ↓
 Threat Hunting
```

O valor do SIEM no Hunting não está apenas em armazenar logs.

Está na capacidade de **pesquisar, correlacionar e contextualizar eventos** que, isoladamente, poderiam passar despercebidos.

---

# 1. Hunting começa antes da query

Um erro comum é abrir o SIEM e começar a procurar eventos sem saber o que está sendo investigado.

Threat Hunting deve seguir:

```text
Hipótese
   ↓
Evidências esperadas
   ↓
Fontes de dados
   ↓
Query
   ↓
Resultados
   ↓
Correlação
   ↓
Investigação
```

Exemplo:

> "Um atacante pode estar utilizando PowerShell para executar comandos em endpoints comprometidos."

A partir disso, o Hunter define:

```text
Processo
+
Usuário
+
Command Line
+
Processo Pai
+
Horário
+
Conexão de rede
```

Só então começa a busca no SIEM.

---

# 2. O SIEM como central de investigação

Imagine um ambiente com:

```text
100 Endpoints
20 Servidores
5 Firewalls
2 VPNs
1 Active Directory
```

Cada equipamento produz seus próprios logs.

Sem centralização:

```text
Endpoint → Log local
Servidor → Log local
Firewall → Log local
AD → Log local
```

O analista teria que investigar cada fonte separadamente.

Com um SIEM:

```text
Endpoint ──────┐
Servidor ──────┤
Firewall ──────┤
DNS ───────────┼──→ SIEM
AD ────────────┤
VPN ───────────┘
```

Agora é possível investigar o comportamento do ambiente em um único ponto.

---

# 3. Dados necessários para Hunting

O SIEM só consegue investigar aquilo que possui visibilidade.

Algumas fontes importantes:

| Fonte            | Exemplos de Hunting                |
| ---------------- | ---------------------------------- |
| Windows          | Processos, logons, PowerShell      |
| Linux            | SSH, sudo, processos               |
| Firewall         | Conexões, portas, destinos         |
| DNS              | Domínios consultados               |
| VPN              | Acessos remotos                    |
| Proxy            | URLs acessadas                     |
| EDR              | Processos, arquivos, comportamento |
| Active Directory | Autenticação e privilégios         |
| Cloud            | APIs, logins, alterações           |

Uma hipótese pode ser excelente, mas se os dados necessários não existirem no SIEM, a investigação ficará limitada.

---

# 4. Query baseada em hipótese

Considere:

```text
HIPÓTESE

"Existe uso anormal de PowerShell no ambiente."
```

Uma investigação pode começar procurando eventos relacionados ao processo:

```text
powershell.exe
```

Depois, o Hunter começa a adicionar contexto:

```text
powershell.exe
+
usuário
+
endpoint
+
horário
```

Depois:

```text
powershell.exe
+
command line
+
processo pai
```

E finalmente:

```text
powershell.exe
+
command line
+
processo pai
+
conexão externa
```

A query vai ficando mais específica conforme o contexto aparece.

---

# 5. Pesquisa ampla x pesquisa direcionada

Existem dois momentos diferentes durante uma investigação.

## Pesquisa ampla

Serve para entender o ambiente.

Exemplo:

```text
Todos os eventos de PowerShell
```

O objetivo é descobrir:

* Quantidade
* Usuários
* Endpoints
* Frequência
* Horários
* Padrões

---

## Pesquisa direcionada

Depois de entender o comportamento, o Hunter pode restringir:

```text
PowerShell
+
usuário específico
+
endpoint específico
+
janela de tempo
```

Essa abordagem evita analisar milhares de eventos desnecessários.

---

# 6. O papel do tempo

Tempo é uma das dimensões mais importantes em Hunting.

Um evento isolado pode parecer normal.

Uma sequência temporal pode revelar um ataque.

Exemplo:

```text
02:31 → Login
02:33 → PowerShell
02:34 → Download
02:35 → Arquivo executado
02:36 → Conexão externa
02:40 → Novo login em outro servidor
```

Quando colocados em uma timeline:

```text
Login
  ↓
Execução
  ↓
Download
  ↓
Comunicação
  ↓
Movimentação lateral
```

o comportamento se torna muito mais fácil de compreender.

---

# 7. Timeline de investigação

Uma boa prática é construir uma timeline.

Exemplo:

```text
[01:42] Login via VPN
[01:44] Acesso ao endpoint
[01:47] PowerShell executado
[01:48] Download de arquivo
[01:49] Novo processo criado
[01:50] DNS para domínio externo
[01:51] Conexão HTTPS
[01:56] Login em servidor interno
```

Agora o Hunter possui uma narrativa.

A investigação deixa de ser:

```text
"Existem 7 eventos suspeitos."
```

e passa a ser:

```text
"Existe uma sequência de eventos compatível
com acesso inicial, execução e possível
movimentação lateral."
```

Essa diferença é fundamental.

---

# 8. Correlação no SIEM

Correlação é um dos principais recursos para Threat Hunting.

Imagine:

```text
Evento 1:
Login administrativo

       +

Evento 2:
PowerShell

       +

Evento 3:
Conexão externa

       +

Evento 4:
Acesso a servidor crítico
```

Cada evento isolado pode ser legítimo.

A combinação pode ser relevante.

O SIEM permite relacionar:

```text
Usuário
+
IP
+
Endpoint
+
Processo
+
Horário
+
Destino
```

Isso gera contexto.

---

# 9. Hunting por usuário

Usuários podem ser utilizados como ponto de partida.

Exemplo:

```text
Usuário:
admin01
```

O Hunter pode investigar:

```text
Onde fez login?

Quando?

De qual IP?

Em quais servidores?

Quais processos executou?

Quais recursos acessou?

Existe comportamento diferente do habitual?
```

Uma investigação pode revelar:

```text
admin01
   ↓
VPN
   ↓
WS-010
   ↓
PowerShell
   ↓
SRV-DB-01
```

Agora existe uma sequência que pode ser analisada.

---

# 10. Hunting por endpoint

O mesmo raciocínio pode ser aplicado a uma máquina.

Exemplo:

```text
HOST-023
```

O Hunter pode reconstruir:

```text
Usuários
Processos
Conexões
DNS
Arquivos
Logons
Alterações
```

A pergunta passa a ser:

> "O que aconteceu nesse endpoint durante determinado período?"

Isso é especialmente útil quando existe suspeita de comprometimento.

---

# 11. Hunting por IP

Um IP também pode servir como pivô de investigação.

Exemplo:

```text
185.XX.XX.10
```

O Hunter pode procurar:

```text
Quais endpoints se conectaram?

Quando?

Quantas vezes?

Qual processo realizou a conexão?

Qual usuário estava conectado?

Quais domínios foram associados?

O IP possui reputação maliciosa?
```

O resultado pode ser:

```text
185.XX.XX.10
       ↓
HOST-01
HOST-04
HOST-09
       ↓
Mesmo destino
       ↓
Mesmo período
```

Isso pode indicar uma campanha afetando múltiplos endpoints.

---

# 12. Hunting por domínio

O mesmo processo pode ser utilizado para DNS.

Exemplo:

```text
example-suspicious[.]com
```

Investigue:

```text
Quem consultou?

Quando?

Quantas vezes?

Qual IP recebeu?

Houve conexão depois da resolução?

Quais processos estavam ativos?
```

Um fluxo interessante:

```text
DNS
 ↓
Domínio
 ↓
IP
 ↓
Conexão
 ↓
Processo
 ↓
Usuário
```

Essa cadeia permite transformar um simples evento DNS em uma investigação completa.

---

# 13. Hunting por processo

Processos também funcionam como pivôs.

Exemplo:

```text
powershell.exe
```

O Hunter pode descobrir:

```text
Quem executou?

Em quais máquinas?

Com quais argumentos?

Qual processo iniciou?

O que foi executado depois?

Existem conexões externas?
```

Isso pode revelar que o mesmo comportamento ocorreu em:

```text
HOST-01
HOST-07
HOST-13
HOST-21
```

Agora o Hunter precisa perguntar:

> "Por que quatro endpoints diferentes apresentaram o mesmo comportamento?"

---

# 14. Hunting por frequência

O SIEM permite identificar comportamentos que fogem da frequência normal.

Exemplo:

```text
Login administrativo

Média:
20/dia

Hoje:
320
```

Ou:

```text
DNS para determinado domínio

Média:
5 consultas/dia

Hoje:
8.000
```

Isso não confirma um ataque.

Mas representa uma anomalia que merece investigação.

---

# 15. Hunting por raridade

Além da frequência, a raridade pode ser útil.

Exemplo:

```text
Processo:
powershell.exe

Uso no ambiente:
10.000 eventos

Servidor WEB-03:
1 evento
```

A ocorrência é rara naquele contexto.

O Hunter pode investigar:

```text
Quem executou?

Por que esse servidor executou?

Qual foi o processo pai?

Qual command line?

O que aconteceu depois?
```

Raridade ajuda a encontrar eventos que poderiam passar despercebidos em grandes volumes de dados.

---

# 16. Hunting por sequência

O SIEM também pode ser utilizado para procurar sequências.

Exemplo:

```text
Login
 ↓
PowerShell
 ↓
Download
 ↓
Execução
 ↓
DNS
 ↓
Conexão externa
```

Esse tipo de investigação é muito mais poderoso do que procurar apenas:

```text
"powershell.exe"
```

O Hunter passa a procurar **cadeias de comportamento**.

---

# 17. Hunting com Threat Intelligence

Threat Intelligence pode enriquecer os resultados do SIEM.

Imagine:

```text
SIEM
 ↓
IP encontrado
 ↓
Threat Intelligence
 ↓
IP associado a C2
```

Agora o Hunter pode expandir:

```text
IP
 ↓
Quais endpoints acessaram?
 ↓
Quais usuários?
 ↓
Qual processo?
 ↓
Quando?
 ↓
Existe comportamento semelhante?
```

O IOC vira um ponto de partida para o Hunting.

---

# 18. Hunting com MITRE ATT&CK

MITRE ATT&CK pode ajudar a estruturar as buscas.

Exemplo:

```text
Tática:
Execution

Técnica:
PowerShell

        ↓

Dados:
Process Creation
PowerShell Logs

        ↓

Hunting:
Executions suspeitas
```

Outro:

```text
Tática:
Lateral Movement

Técnica:
Remote Services

        ↓

Dados:
Logons
Firewall
RDP
SMB
SSH

        ↓

Hunting:
Acessos incomuns entre endpoints
```

O framework funciona como uma ponte entre:

```text
Comportamento do atacante
        ↓
Dados disponíveis
        ↓
Query
```

---

# 19. Hunting com Wazuh

O Wazuh pode atuar como plataforma de monitoramento e análise em ambientes onde os agentes e fontes de log estão devidamente configurados.

Um fluxo possível:

```text
Endpoint
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Eventos
   ↓
Indexação
   ↓
Busca
   ↓
Hunting
```

Durante uma investigação, o analista pode utilizar informações relacionadas a:

```text
Processos
Autenticação
Integridade de arquivos
Vulnerabilidades
Logs
Comandos
Eventos do sistema
```

O ponto importante é que o Wazuh não substitui o raciocínio do Hunter.

A ferramenta fornece visibilidade.

O analista transforma essa visibilidade em investigação.

---

# 20. Hunting com Splunk

No Splunk, o Hunting pode ser realizado utilizando consultas sobre os dados coletados.

Um exemplo conceitual:

```text
index=windows
process="powershell.exe"
```

A partir daí, o Hunter pode adicionar contexto:

```text
index=windows
process="powershell.exe"
user="admin01"
```

Depois:

```text
index=windows
process="powershell.exe"
host="HOST-01"
```

E finalmente:

```text
index=windows
process="powershell.exe"
host="HOST-01"
user="admin01"
```

O objetivo não é decorar consultas.

É entender como reduzir o espaço de investigação utilizando os dados disponíveis.

---

# 21. Hunting com Microsoft Sentinel

No Microsoft Sentinel, o mesmo conceito pode ser aplicado utilizando consultas sobre os dados ingeridos.

Um cenário conceitual:

```text
Windows Events
       +
Entra ID
       +
Defender
       +
Firewall
       +
Cloud Logs
       ↓
Microsoft Sentinel
       ↓
Hunting
```

Uma investigação pode começar com um usuário e expandir:

```text
Usuário
 ↓
IP
 ↓
Endpoint
 ↓
Processo
 ↓
Domínio
 ↓
Arquivo
```

A grande vantagem está na possibilidade de correlacionar diferentes fontes dentro do mesmo ambiente de análise.

---

# 22. O conceito de Pivot

**Pivot** é uma das habilidades mais importantes durante uma investigação.

Imagine que o Hunter encontre:

```text
IP suspeito
```

Ele pode fazer um pivot para:

```text
IP
 ↓
Endpoint
```

Depois:

```text
Endpoint
 ↓
Usuário
```

Depois:

```text
Usuário
 ↓
Processos
```

Depois:

```text
Processo
 ↓
Domínio
```

Depois:

```text
Domínio
 ↓
Threat Intelligence
```

A investigação cresce a partir das evidências encontradas.

Visualmente:

```text
                  IP
                  │
                  ▼
              Endpoint
                  │
          ┌───────┴───────┐
          ▼               ▼
       Usuário          Processo
          │               │
          ▼               ▼
       Logins          Command Line
                          │
                          ▼
                        Rede
                          │
                          ▼
                       Domínio
```

Essa capacidade de pivotar rapidamente é essencial em investigações reais.

---

# 23. Hunting e janela de tempo

Definir uma janela de tempo adequada é fundamental.

Uma busca muito curta pode perder eventos.

Uma busca muito ampla pode gerar:

```text
Milhões de eventos
```

Uma estratégia comum é começar pequeno:

```text
Últimas 24 horas
```

Se necessário:

```text
7 dias
```

Depois:

```text
30 dias
```

Ou utilizar uma janela baseada no contexto do incidente.

Exemplo:

```text
Primeiro evento:
10:32

Investigação:
10:00 → 12:00
```

Depois, se necessário, expandir.

---

# 24. Hunting e redução de ruído

SIEMs podem gerar grandes volumes de dados.

Uma query muito genérica:

```text
powershell.exe
```

pode retornar milhares de eventos.

O Hunter pode reduzir o ruído utilizando:

```text
Usuário
Host
Horário
Processo Pai
Command Line
Destino
Reputação
Frequência
```

Por exemplo:

```text
PowerShell
+
servidor crítico
+
usuário comum
+
madrugada
+
conexão externa
```

Agora o resultado é muito mais interessante.

---

# 25. Hunting baseado em correlação

Um modelo simples de correlação seria:

```text
[LOGIN ANORMAL]
       +
[PROCESSO SUSPEITO]
       +
[CONEXÃO EXTERNA]
       +
[IOC MALICIOSO]
       =
[ALTA PRIORIDADE]
```

Isso não significa que qualquer combinação será um incidente.

Significa que múltiplos sinais podem aumentar a prioridade da investigação.

O contexto continua sendo essencial.

---

# 26. Hunting e False Positive

O SIEM pode encontrar atividades que parecem suspeitas, mas são legítimas.

Exemplo:

```text
PowerShell
+
Download
+
Conexão externa
```

Pode ser:

```text
Atualização legítima
```

ou:

```text
Atividade maliciosa
```

Por isso, o Hunter deve verificar:

```text
Usuário
Processo
Origem
Destino
Janela de manutenção
Histórico
Baseline
Threat Intelligence
```

A função do Hunting não é gerar o máximo de suspeitas.

É gerar **investigações de qualidade**.

---

# 27. Hunting e criação de detecção

Uma investigação bem-sucedida pode gerar uma nova regra.

Exemplo:

```text
Hunting
   ↓
Identifica comportamento
   ↓
Valida como malicioso
   ↓
Cria regra
   ↓
SIEM
   ↓
Detecção automática
```

Antes:

```text
Atividade
   ↓
Sem alerta
```

Depois:

```text
Atividade
   ↓
Regra
   ↓
Alerta
```

Esse é um dos maiores ganhos do Threat Hunting.

---

# 28. Hunting como processo contínuo

Threat Hunting não deve acontecer somente quando existe um incidente.

O SOC pode manter uma rotina:

```text
Segunda
↓
Hunting de autenticação

Terça
↓
Hunting de PowerShell

Quarta
↓
Hunting de DNS

Quinta
↓
Hunting de movimentação lateral

Sexta
↓
Revisão e melhoria de detecções
```

O calendário pode variar.

O conceito importante é transformar Hunting em uma atividade contínua.

---

# 29. Exemplo completo

Imagine que o SIEM contenha:

```text
Windows Logs
DNS Logs
Firewall Logs
```

O Hunter cria a hipótese:

> "Pode existir um endpoint realizando comunicação periódica com uma infraestrutura de C2."

### Etapa 1 — Rede

Encontrar conexões periódicas.

```text
HOST-07
↓
185.XX.XX.10
```

### Etapa 2 — DNS

Verificar se existe domínio associado.

```text
HOST-07
↓
suspicious-domain[.]com
```

### Etapa 3 — Endpoint

Descobrir qual processo realizou a conexão.

```text
HOST-07
↓
powershell.exe
```

### Etapa 4 — Usuário

Descobrir quem executou.

```text
USER-01
```

### Etapa 5 — Command Line

Investigar os argumentos.

```text
powershell.exe
+
script suspeito
```

### Etapa 6 — Threat Intelligence

Consultar os indicadores.

```text
IP → suspeito
Domínio → malicioso
```

### Etapa 7 — Conclusão

Agora existe uma cadeia:

```text
Usuário
 ↓
PowerShell
 ↓
Script
 ↓
DNS
 ↓
IP malicioso
 ↓
Comunicação periódica
```

A hipótese ganha evidências suficientes para ser escalada para investigação de incidente.

---

# 30. Checklist de Hunting no SIEM

```text
[ ] Defini a hipótese?

[ ] Sei quais evidências estou procurando?

[ ] Tenho os logs necessários?

[ ] Defini uma janela de tempo?

[ ] Comecei com uma busca ampla?

[ ] Reduzi o resultado conforme encontrei contexto?

[ ] Analisei usuário e endpoint?

[ ] Analisei processos?

[ ] Analisei rede e DNS?

[ ] Consultei Threat Intelligence quando necessário?

[ ] Fiz pivots a partir dos indicadores?

[ ] Considerei possíveis falsos positivos?

[ ] Documentei os resultados?

[ ] Existe necessidade de criar uma nova detecção?

[ ] Existe alguma falha de logging?
```

---

# 31. O verdadeiro valor do SIEM no Hunting

O SIEM não é apenas um lugar onde os logs ficam armazenados.

Ele funciona como um **ponto de investigação**.

Um bom Hunter consegue partir de:

```text
IP
```

e chegar a:

```text
IP
 ↓
Endpoint
 ↓
Usuário
 ↓
Processo
 ↓
Command Line
 ↓
DNS
 ↓
Arquivo
 ↓
Threat Intelligence
 ↓
MITRE ATT&CK
 ↓
Possível incidente
```

Essa capacidade de conectar eventos é o que transforma uma grande quantidade de logs em uma investigação utilizável.

---

# Conclusão

Hunting com SIEM é a combinação entre **visibilidade, investigação e raciocínio analítico**.

O SIEM fornece os dados.

As queries encontram os eventos.

A correlação conecta os eventos.

O Threat Hunter interpreta o contexto.

E a investigação determina o próximo passo.

```text
DADOS
  ↓
QUERY
  ↓
EVENTOS
  ↓
CORRELAÇÃO
  ↓
CONTEXTO
  ↓
EVIDÊNCIAS
  ↓
DECISÃO
```

O objetivo não é simplesmente encontrar um evento suspeito.

É conseguir construir uma história baseada em evidências.

> **O SIEM mostra o que aconteceu.
> O Hunting pergunta por que aconteceu.
> A correlação mostra como os eventos se conectam.
> A investigação decide o que fazer com isso.**
