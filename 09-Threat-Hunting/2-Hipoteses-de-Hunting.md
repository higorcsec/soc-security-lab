# Hipóteses de Hunting

Threat Hunting não começa com uma query.

Começa com uma **pergunta**.

Antes de consultar milhares de eventos no SIEM, o analista precisa definir o que está tentando descobrir e quais evidências poderiam confirmar ou descartar aquela possibilidade.

Essa pergunta estruturada é chamada de **hipótese de Hunting**.

Exemplo:

> "Um atacante pode estar utilizando PowerShell para executar comandos em endpoints comprometidos sem gerar uma detecção."

A partir dessa hipótese, o analista decide:

```text
O que preciso procurar?
        ↓
Onde esses dados estão?
        ↓
Quais eventos representam esse comportamento?
        ↓
Como diferenciar atividade legítima de suspeita?
        ↓
Quais evidências confirmariam a hipótese?
```

O objetivo não é provar que existe uma ameaça.

É descobrir **se existem evidências que sustentem ou derrubem a hipótese**.

---

# 1. O que torna uma boa hipótese?

Uma hipótese de Hunting precisa ser:

* Investigável
* Baseada em evidências
* Relacionada a um comportamento
* Compatível com os dados disponíveis
* Específica o suficiente para ser testada
* Aberta o suficiente para permitir descobertas

Uma hipótese ruim seria:

> "Quero procurar hackers na rede."

Não existe uma pergunta clara.

Uma hipótese melhor:

> "Um atacante pode estar utilizando credenciais comprometidas para realizar logins administrativos fora do horário habitual."

Agora existe algo que pode ser investigado.

---

# 2. Estrutura de uma hipótese

Uma forma simples de estruturar uma hipótese é:

```text
ATORES
   +
COMPORTAMENTO
   +
AMBIENTE
   +
OBJETIVO
   +
EVIDÊNCIA ESPERADA
```

Exemplo:

```text
Atacante
   +
Uso de PowerShell
   +
Endpoints Windows
   +
Execução de comandos
   +
Processos + command line + conexão externa
```

A hipótese poderia ser:

> "Um atacante pode estar utilizando PowerShell em endpoints Windows para executar comandos e estabelecer comunicação externa."

Agora a investigação possui direção.

---

# 3. De onde surgem as hipóteses?

Uma hipótese não precisa surgir do nada.

Existem várias fontes.

## Threat Intelligence

Um relatório informa que determinado grupo utiliza PowerShell para execução.

O Hunter pode transformar isso em:

> "Existe atividade compatível com esse comportamento no nosso ambiente?"

---

## MITRE ATT&CK

Uma técnica pode servir como ponto de partida.

Exemplo:

```text
Tática:
Execution

Técnica:
Command and Scripting Interpreter

Subtécnica:
PowerShell
```

A hipótese:

> "PowerShell pode estar sendo utilizado de forma anormal para execução de comandos."

---

## Incidentes anteriores

Um incidente antigo revelou que um atacante utilizou RDP para movimentação lateral.

O SOC pode criar uma nova hipótese:

> "Existe utilização anormal de RDP entre endpoints internos?"

---

## Alertas existentes

Uma regra detectou vários logins suspeitos.

O Hunter pode expandir a investigação:

> "O mesmo comportamento pode estar ocorrendo em outros usuários que ainda não geraram alertas?"

---

## Vulnerabilidades

Uma vulnerabilidade crítica foi identificada em determinado servidor.

Uma hipótese pode ser:

> "O servidor vulnerável apresenta sinais de exploração ou atividade pós-exploração?"

---

## Anomalias

Um servidor normalmente executa poucos processos administrativos.

De repente:

```text
PowerShell
cmd
WMI
PsExec
```

aparecem com frequência.

Isso pode gerar uma hipótese:

> "Existe atividade administrativa anormal nesse servidor?"

---

# 4. Hipótese baseada em TTP

Uma das formas mais eficientes de criar hipóteses é utilizar **TTPs — Táticas, Técnicas e Procedimentos**.

Em vez de procurar apenas:

```text
IP malicioso
```

o Hunter pode procurar:

```text
Técnica utilizada pelo atacante
```

Por exemplo:

```text
Tática:
Persistence

       ↓

Técnica:
Scheduled Task/Job

       ↓

Hipótese:
Um atacante pode estar criando tarefas agendadas
para manter persistência em endpoints.
```

Agora a investigação pode procurar:

```text
Criação de tarefas
+
Usuário responsável
+
Comando executado
+
Horário
+
Endpoint
```

Essa abordagem é mais resistente a mudanças de IOC.

---

# 5. IOC x Hipótese

É importante não confundir os dois.

Um IOC pode ser:

```text
185.XX.XX.10
```

A hipótese seria:

> "Endpoints podem estar estabelecendo comunicação com infraestrutura de Command and Control."

O IOC é uma evidência específica.

A hipótese representa uma **possibilidade de comportamento ou atividade maliciosa**.

Por isso, Hunting baseado somente em IOC pode ser limitado.

Se o atacante mudar o IP, a investigação pode perder valor.

Se a hipótese estiver baseada no comportamento, o Hunter ainda pode encontrar a atividade.

---

# 6. Hipóteses baseadas em comportamento

Considere o seguinte comportamento:

```text
winword.exe
    ↓
powershell.exe
    ↓
download
    ↓
arquivo temporário
    ↓
execução
```

Uma hipótese poderia ser:

> "Documentos do Office podem estar sendo utilizados como ponto inicial para execução de scripts maliciosos."

O analista não precisa saber antecipadamente:

```text
Qual será o arquivo?
Qual será o domínio?
Qual será o IP?
Qual será o malware?
```

Ele procura a cadeia comportamental.

Isso aumenta a capacidade de encontrar ameaças desconhecidas.

---

# 7. Transformando uma hipótese em investigação

Uma hipótese sozinha não é suficiente.

Ela precisa ser convertida em perguntas técnicas.

Exemplo:

### Hipótese

> "Um atacante pode estar utilizando PowerShell para executar comandos remotamente."

### Perguntas

```text
Quais endpoints executaram PowerShell?

Quais usuários executaram?

Quais comandos foram utilizados?

A execução ocorreu remotamente?

Qual processo iniciou o PowerShell?

Houve conexão externa?

Existem endpoints com comportamento semelhante?
```

Agora temos um plano de investigação.

---

# 8. Definindo evidências esperadas

Antes de executar a busca, o Hunter deve pensar:

> "Se essa hipótese for verdadeira, o que eu espero encontrar?"

Exemplo:

```text
HIPÓTESE

PowerShell utilizado para execução maliciosa.

EVIDÊNCIAS ESPERADAS:

- Execução de powershell.exe
- Command line suspeita
- Processo pai incomum
- Usuário inesperado
- Horário incomum
- Conexão externa
- Download de arquivo
- Persistência
```

Isso evita que o analista procure informações aleatoriamente.

---

# 9. Definindo evidências que descartariam a hipótese

Também é importante pensar no que poderia provar que a hipótese está errada.

Exemplo:

```text
HIPÓTESE:
PowerShell está sendo utilizado para atividade maliciosa.

EVIDÊNCIAS:

PowerShell executado por equipe administrativa
+
Script conhecido
+
Servidor autorizado
+
Janela de manutenção registrada
```

Nesse caso, a atividade pode ser legítima.

Um bom Hunter procura tanto evidências que **confirmam** quanto evidências que **contradizem** a hipótese.

---

# 10. Hipótese e contexto

O mesmo comportamento pode ser:

```text
Normal
```

ou:

```text
Malicioso
```

dependendo do contexto.

Exemplo:

```text
PowerShell
```

Por si só, não é necessariamente suspeito.

Agora:

```text
PowerShell
+
Usuário comum
+
Servidor crítico
+
03:00
+
Download externo
+
Execução codificada
```

O contexto muda completamente a investigação.

Por isso:

> **Threat Hunting não é procurar eventos ruins. É procurar combinações que façam sentido dentro de um cenário de ameaça.**

---

# 11. Priorizando hipóteses

Um SOC pode ter dezenas de hipóteses possíveis.

Não é necessário investigar todas ao mesmo tempo.

Uma forma simples de priorizar é considerar:

| Critério      | Pergunta                                      |
| ------------- | --------------------------------------------- |
| Impacto       | Se for verdadeiro, qual seria o impacto?      |
| Probabilidade | Esse comportamento é plausível no ambiente?   |
| Exposição     | Existe superfície vulnerável?                 |
| Evidência     | Temos dados suficientes para investigar?      |
| Relevância    | A hipótese está relacionada a ameaças atuais? |
| Esforço       | Quanto tempo será necessário para validar?    |

Uma hipótese de alto impacto e baixo esforço de investigação pode ter prioridade.

---

# 12. Exemplo de priorização

Imagine três hipóteses:

```text
H1
Possível uso de PowerShell para execução maliciosa.

H2
Possível movimentação lateral via RDP.

H3
Possível persistência através de tarefas agendadas.
```

O SOC pode avaliar:

```text
H1 → Alta prioridade
H2 → Média prioridade
H3 → Alta prioridade
```

A prioridade pode mudar conforme:

* Incidentes recentes
* Threat Intelligence
* Vulnerabilidades
* Perfil do ambiente
* Táticas observadas
* Novos alertas

Não existe uma lista universal de hipóteses.

O Hunting deve refletir **o risco real do ambiente**.

---

# 13. Hipóteses orientadas por incidentes

Um dos melhores pontos de partida para Hunting é um incidente real.

Imagine que um endpoint foi comprometido.

Durante a investigação foi descoberto:

```text
Phishing
   ↓
PowerShell
   ↓
Download
   ↓
Credenciais
   ↓
RDP
```

Depois que o incidente é encerrado, o SOC pode criar novas hipóteses:

```text
H1:
Existem outros endpoints que receberam o mesmo phishing?

H2:
Outros usuários executaram PowerShell semelhante?

H3:
O atacante utilizou as mesmas credenciais em outros sistemas?

H4:
Existem outros acessos RDP relacionados?
```

Assim, um incidente deixa de ser apenas um problema resolvido.

Ele gera **novas oportunidades de Hunting**.

---

# 14. Hipóteses orientadas por Threat Intelligence

Imagine que uma campanha conhecida esteja utilizando:

```text
PowerShell
+
Scheduled Tasks
+
DNS
+
C2
```

O SOC pode transformar essas informações em hipóteses:

```text
H1:
Existe execução anormal de PowerShell?

H2:
Existem tarefas agendadas criadas recentemente?

H3:
Existem consultas DNS relacionadas à infraestrutura?

H4:
Existem endpoints apresentando comunicação semelhante?
```

Isso permite trazer inteligência externa para dentro do ambiente.

---

# 15. Hipótese → Query

Somente depois de definir a hipótese chega o momento de criar a consulta.

Exemplo:

```text
HIPÓTESE
   ↓
PowerShell está sendo utilizado de forma suspeita.
   ↓
EVIDÊNCIAS
   ↓
Processo + usuário + command line + horário
   ↓
QUERY
   ↓
RESULTADOS
   ↓
ANÁLISE
```

Esse processo é importante porque evita o famoso:

```text
"Vou jogar uma query no SIEM e ver o que aparece."
```

Isso não é Hunting estruturado.

---

# 16. Exemplo prático com logs

Hipótese:

> "Um atacante pode estar utilizando uma conta administrativa comprometida para acessar servidores fora do padrão."

O Hunter pode procurar:

```text
Evento de autenticação
+
Conta administrativa
+
Origem
+
Horário
+
Servidor destino
```

Resultado:

```text
Usuário: admin01
Origem: 185.XX.XX.20
Destino: SRV-DB-01
Horário: 02:47
```

Agora começa a investigação.

Perguntas:

```text
O IP é conhecido?

O usuário normalmente acessa esse servidor?

O horário é esperado?

Existiram outros acessos?

Houve execução de comandos?

Outros servidores foram acessados?

A conta apresentou atividade semelhante anteriormente?
```

A hipótese pode ser:

```text
Confirmada
```

ou:

```text
Descartada
```

ou:

```text
Inconclusiva
```

---

# 17. Hipóteses encadeadas

Uma investigação pode gerar novas hipóteses.

Exemplo:

```text
H1
Existe acesso administrativo anormal?
       ↓
SIM
       ↓
H2
A conta foi comprometida?
       ↓
SIM
       ↓
H3
A conta foi utilizada para movimentação lateral?
       ↓
SIM
       ↓
H4
Outros endpoints foram comprometidos?
```

Isso transforma o Hunting em uma investigação progressiva.

Uma resposta gera a próxima pergunta.

---

# 18. Documentando uma hipótese

Uma hipótese de Hunting deve ser documentada.

Um modelo simples:

```text
# Hipótese H-001

## Hipótese

Um atacante pode estar utilizando PowerShell
para executar comandos em endpoints Windows.

## Motivação

Threat Intelligence identificou campanhas
recentes utilizando PowerShell como mecanismo
de execução.

## Dados necessários

- Windows Event Logs
- PowerShell Logs
- Process Events
- Network Logs

## Evidências esperadas

- powershell.exe
- Command Line suspeita
- Processos pais incomuns
- Conexões externas
- Execução fora do padrão

## Resultado

A confirmar.

## Próximos passos

Realizar busca nos últimos 30 dias e comparar
com o comportamento esperado dos endpoints.
```

Esse tipo de documentação demonstra maturidade operacional.

---

# 19. O resultado do Hunting

Ao terminar uma investigação, o resultado deve ser registrado.

```text
Hipótese:
H-001

Resultado:
Confirmada

Evidências:
- PowerShell
- Execução codificada
- Conexão externa
- Usuário comprometido

Impacto:
Endpoint potencialmente comprometido.

Ação:
Escalonamento para Incident Response.

Melhoria:
Criar regra de detecção para o comportamento identificado.
```

Se a hipótese for descartada:

```text
Resultado:
Descartada

Motivo:
Atividade realizada por equipe administrativa
durante janela de manutenção autorizada.
```

Se não houver dados suficientes:

```text
Resultado:
Inconclusiva

Motivo:
Logs de PowerShell não estavam habilitados
em parte dos endpoints.
```

Esse último resultado também revela uma oportunidade de melhoria.

---

# 20. Hipótese como ferramenta de melhoria

Uma hipótese pode revelar problemas na própria infraestrutura de segurança.

Exemplo:

```text
HIPÓTESE
Existe execução suspeita de PowerShell.

        ↓

INVESTIGAÇÃO

        ↓

Não existem logs suficientes.

        ↓

PROBLEMA DE VISIBILIDADE

        ↓

AÇÃO

Habilitar PowerShell Logging.
```

Nesse cenário, o Hunting não encontrou necessariamente um atacante.

Mas encontrou uma **falha de monitoramento**.

Isso também é um resultado importante para o SOC.

---

# 21. O ciclo das hipóteses

O processo pode ser representado assim:

```text
        THREAT INTELLIGENCE
                │
                ▼
             HIPÓTESE
                │
                ▼
          DADOS NECESSÁRIOS
                │
                ▼
             QUERY
                │
                ▼
             ANÁLISE
                │
          ┌─────┴─────┐
          ▼           ▼
      EVIDÊNCIA     SEM EVIDÊNCIA
          │           │
          ▼           ▼
     INVESTIGAR     DESCARTAR
          │
          ▼
       RESULTADO
          │
     ┌────┴────┐
     ▼         ▼
 INCIDENTE   MELHORIA
     │         │
     ▼         ▼
 RESPOSTA   DETECÇÃO
               │
               ▼
         NOVA HIPÓTESE
```

Esse ciclo faz o Threat Hunting evoluir continuamente.

---

# 22. Boas práticas

Ao criar hipóteses de Hunting:

* Comece com uma pergunta clara.
* Baseie a hipótese em evidências ou inteligência.
* Pense no comportamento do atacante.
* Defina quais evidências seriam esperadas.
* Defina também o que poderia descartar a hipótese.
* Verifique se os dados necessários estão disponíveis.
* Priorize hipóteses de acordo com o risco.
* Evite depender exclusivamente de IOCs.
* Utilize MITRE ATT&CK para estruturar comportamentos.
* Documente consultas e resultados.
* Registre hipóteses descartadas.
* Transforme descobertas relevantes em novas detecções.

---

# Conclusão

Uma boa investigação de Threat Hunting não começa com:

```text
"Qual query eu vou executar?"
```

Ela começa com:

```text
"O que eu acredito que pode estar acontecendo
e quais evidências poderiam provar isso?"
```

A hipótese dá direção ao Hunting.

Ela conecta:

```text
Threat Intelligence
        ↓
MITRE ATT&CK
        ↓
Incidentes
        ↓
Risco
        ↓
Hipótese
        ↓
Dados
        ↓
Investigação
        ↓
Resultado
```

E existe um ponto importante:

> **Uma hipótese descartada também gera conhecimento.**

Se a investigação mostrar que determinado comportamento é legítimo, o SOC aprende o baseline daquele ambiente.

Se encontrar atividade maliciosa, ganha uma nova detecção.

Se não conseguir investigar por falta de logs, descobre uma falha de visibilidade.

Em todos os três cenários, o SOC sai da investigação **mais preparado do que entrou**.

> **Threat Hunting começa com uma hipótese.
> A evidência decide o resultado.
> O resultado melhora o SOC.**
