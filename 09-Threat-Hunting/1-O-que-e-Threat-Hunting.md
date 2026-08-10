# O que é Threat Hunting

Threat Hunting é o processo de **buscar ativamente por ameaças dentro de um ambiente**, mesmo quando nenhum alerta foi gerado.

Essa é a principal diferença entre uma abordagem tradicional de segurança e o Threat Hunting.

Em um SOC tradicional:

```text
Evento
  ↓
Regra de detecção
  ↓
Alerta
  ↓
Analista
  ↓
Investigação
```

No Threat Hunting, o processo começa de outra forma:

```text
Hipótese
   ↓
Busca
   ↓
Análise
   ↓
Correlação
   ↓
Evidências
   ↓
Conclusão
```

O analista não espera a ameaça aparecer.

Ele procura sinais de que ela **pode já estar acontecendo**.

---

# 1. Por que Threat Hunting existe?

Nenhum ambiente possui detecção perfeita.

Uma ameaça pode passar despercebida por diversos motivos:

* Regra de detecção inexistente
* IOC ainda desconhecido
* Técnica nova
* Configuração incorreta
* Logs insuficientes
* Falso negativo
* Ataque utilizando ferramentas legítimas
* Comportamento semelhante ao de um usuário normal

Por isso, depender exclusivamente de alertas significa assumir que:

> **Tudo que não gerou alerta está seguro.**

Essa premissa é perigosa.

Threat Hunting trabalha justamente sobre essa lacuna.

---

# 2. Detecção x Threat Hunting

Os dois processos trabalham juntos, mas possuem objetivos diferentes.

| Detecção                                  | Threat Hunting                            |
| ----------------------------------------- | ----------------------------------------- |
| Espera um evento corresponder a uma regra | Procura comportamentos suspeitos          |
| Trabalha com regras existentes            | Pode criar novas hipóteses                |
| Geralmente é reativo                      | É proativo                                |
| Responde a alertas                        | Investiga o ambiente                      |
| Busca padrões conhecidos                  | Também procura o desconhecido             |
| Automatização possui papel importante     | Raciocínio analítico possui papel central |

Um não substitui o outro.

Um SOC maduro utiliza ambos.

```text
                 SOC
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
    DETECÇÃO             HUNTING
        │                   │
        ▼                   ▼
    Alertas              Hipóteses
        │                   │
        └─────────┬─────────┘
                  ▼
             INVESTIGAÇÃO
                  │
                  ▼
               RESPOSTA
```

---

# 3. Threat Hunting não é procurar malware

Esse é um dos erros mais comuns.

Threat Hunting não significa simplesmente procurar arquivos maliciosos.

Uma ameaça pode não depender de malware.

Um atacante pode utilizar ferramentas legítimas do próprio sistema:

```text
PowerShell
WMI
PsExec
RDP
SSH
cmd.exe
Windows Management Tools
```

Esse tipo de atividade pode ser muito mais difícil de identificar.

Por isso, o Threat Hunter procura principalmente por **comportamentos, relações e anomalias**.

---

# 4. O que um Threat Hunter procura?

Um Threat Hunter pode procurar:

* Execuções incomuns
* Processos suspeitos
* Conexões externas inesperadas
* Logins fora do padrão
* Uso anormal de privilégios
* Persistência
* Movimentação lateral
* Execução de scripts
* Alterações no sistema
* Comunicação com infraestrutura suspeita
* Uso anormal de ferramentas administrativas
* Comportamentos associados a TTPs

Um exemplo:

```text
Usuário
   ↓
Login fora do horário habitual
   ↓
PowerShell
   ↓
Download externo
   ↓
Execução de script
   ↓
Conexão para IP externo
```

Nenhum evento isolado necessariamente prova um comprometimento.

Mas a sequência pode representar um comportamento altamente relevante.

---

# 5. O conceito de hipótese

Threat Hunting normalmente começa com uma hipótese.

Uma hipótese é uma afirmação que pode ser investigada utilizando dados disponíveis.

Exemplo:

> "Um atacante pode estar utilizando PowerShell para executar comandos em endpoints comprometidos."

A partir disso, o analista define o que precisa procurar.

```text
HIPÓTESE
   ↓
Quais evidências espero encontrar?
   ↓
Quais logs possuem essas evidências?
   ↓
Quais consultas posso executar?
   ↓
O comportamento existe no ambiente?
   ↓
Existe contexto suficiente para confirmar ou descartar?
```

Isso transforma uma investigação genérica em uma investigação direcionada.

---

# 6. Threat Hunting baseado em comportamento

Um dos principais conceitos de Hunting é procurar **comportamento**, e não apenas indicadores.

Considere:

```text
Arquivo.exe
```

Procurar somente o nome do arquivo pode ser pouco eficiente.

O atacante pode simplesmente alterar o nome.

Agora considere:

```text
winword.exe
     ↓
powershell.exe
     ↓
download externo
     ↓
arquivo temporário
     ↓
execução
```

O nome dos arquivos pode mudar.

O comportamento continua relevante.

Por isso, o Threat Hunter deve pensar:

> **"O que o atacante precisa fazer para alcançar o objetivo?"**

e não apenas:

> **"Qual arquivo o atacante vai utilizar?"**

---

# 7. Threat Hunting e MITRE ATT&CK

O MITRE ATT&CK é uma das principais referências utilizadas para estruturar atividades de Threat Hunting.

O framework organiza comportamentos relacionados a ataques reais em:

* Táticas
* Técnicas
* Subtécnicas

Por exemplo:

```text
Tática:
Execution

        ↓

Técnica:
Command and Scripting Interpreter

        ↓

Subtécnica:
PowerShell
```

Isso permite transformar uma técnica conhecida em uma hipótese de investigação.

Exemplo:

> "Quero verificar se PowerShell está sendo utilizado de maneira suspeita no ambiente."

A partir dessa hipótese, o analista procura eventos relacionados à execução de PowerShell.

---

# 8. Fontes de dados para Threat Hunting

Threat Hunting depende diretamente da qualidade dos dados disponíveis.

Algumas fontes importantes:

```text
Windows Event Logs
        │
        ├── Processos
        ├── Logons
        ├── PowerShell
        └── Eventos de segurança

Linux Logs
        │
        ├── SSH
        ├── sudo
        ├── processos
        └── autenticação

Network Logs
        │
        ├── Firewall
        ├── DNS
        ├── Proxy
        └── NetFlow

Endpoint
        │
        ├── Processos
        ├── Arquivos
        ├── Conexões
        └── Persistência
```

Quanto melhor a visibilidade, maior a capacidade de Hunting.

---

# 9. Threat Hunting e SIEM

O SIEM pode funcionar como uma das principais plataformas de apoio ao Threat Hunting.

O analista pode utilizar os dados centralizados para procurar padrões.

Um fluxo comum:

```text
Logs
 ↓
Coleta
 ↓
SIEM
 ↓
Query
 ↓
Filtragem
 ↓
Correlação
 ↓
Investigação
```

Por exemplo, o analista pode procurar:

```text
PowerShell
+
EncodedCommand
+
Conexão externa
```

ou:

```text
Login administrativo
+
Horário incomum
+
Endpoint desconhecido
```

O objetivo não é simplesmente encontrar eventos.

É encontrar **relações relevantes entre eventos**.

---

# 10. Exemplo de Hunting

Imagine que exista a seguinte hipótese:

> "Um atacante pode estar utilizando credenciais comprometidas para acessar servidores via RDP."

O Hunter pode procurar:

```text
Logins RDP
       ↓
Horário
       ↓
Usuário
       ↓
IP de origem
       ↓
Servidor acessado
       ↓
Histórico do usuário
```

Durante a investigação aparece:

```text
Usuário: admin01
Horário: 03:17
Origem: IP externo
Destino: SRV-FINANCEIRO
```

O horário sozinho não confirma um ataque.

Mas agora o analista pode investigar:

```text
Esse usuário costuma acessar nesse horário?

Esse IP já apareceu anteriormente?

O usuário estava trabalhando?

Existiram outros logins?

Houve execução de comandos após o acesso?

O mesmo IP acessou outros servidores?
```

É assim que Hunting transforma um evento aparentemente comum em uma investigação.

---

# 11. Hunting não significa encontrar um incidente

Uma investigação pode terminar de três maneiras:

```text
HIPÓTESE
   │
   ├── Confirmada
   │
   ├── Descartada
   │
   └── Inconclusiva
```

### Hipótese confirmada

Existem evidências suficientes indicando atividade maliciosa.

```text
Hunting
   ↓
Evidências
   ↓
Comportamento malicioso
   ↓
Incidente
```

### Hipótese descartada

Os eventos encontrados possuem uma explicação legítima.

Exemplo:

```text
PowerShell
   ↓
Script administrativo autorizado
   ↓
Atividade legítima
```

### Hipótese inconclusiva

Existem indícios, mas não existem dados suficientes para confirmar ou descartar.

Isso também é um resultado válido.

---

# 12. O valor do resultado negativo

Uma investigação que não encontrou uma ameaça não significa que foi inútil.

Imagine:

```text
Hipótese:
PowerShell está sendo utilizado para execução maliciosa.

Hunting:
5.000 eventos analisados.

Resultado:
Nenhuma evidência de comportamento malicioso.
```

Esse resultado pode ajudar o SOC a:

* Validar controles
* Identificar lacunas de logging
* Melhorar regras
* Criar baseline
* Documentar riscos
* Confirmar hipóteses de segurança

O importante é que a investigação tenha sido conduzida de forma estruturada.

---

# 13. Threat Hunting e Baseline

Para identificar comportamento anormal, muitas vezes é necessário conhecer o comportamento normal.

Imagine:

```text
Servidor WEB
 ↓
PowerShell
 ↓
1 vez por mês
```

Agora:

```text
Servidor WEB
 ↓
PowerShell
 ↓
300 vezes
 ↓
Durante a madrugada
```

O segundo comportamento merece investigação.

Por isso, Threat Hunting pode utilizar **baselines**.

O analista procura desvios em relação ao comportamento esperado.

```text
COMPORTAMENTO NORMAL
        ↓
      BASELINE
        ↓
COMPORTAMENTO ATUAL
        ↓
      DIFERENÇA
        ↓
    INVESTIGAÇÃO
```

---

# 14. Threat Hunting e Falso Negativo

Um dos principais objetivos do Hunting é encontrar situações que não foram detectadas pelas regras existentes.

Imagine:

```text
Atividade maliciosa
       ↓
Regra não detectou
       ↓
Nenhum alerta
       ↓
Threat Hunting
       ↓
Comportamento identificado
```

Depois da investigação, o SOC pode transformar a descoberta em uma nova detecção:

```text
Hunting
   ↓
Comportamento identificado
   ↓
Nova regra
   ↓
SIEM
   ↓
Detecção automática
```

Esse ciclo é extremamente importante.

O Hunting pode melhorar continuamente a capacidade de detecção do SOC.

---

# 15. O ciclo do Threat Hunting

Um processo de Hunting pode ser representado assim:

```text
             HIPÓTESE
                 ↓
          COLETA DE DADOS
                 ↓
             CONSULTA
                 ↓
             ANÁLISE
                 ↓
          CORRELAÇÃO
                 ↓
        EVIDÊNCIAS ENCONTRADAS?
             /           \
           SIM            NÃO
            ↓              ↓
       INVESTIGAÇÃO      DESCARTAR
            ↓
          AÇÃO
            ↓
       NOVA DETECÇÃO
            ↓
       NOVA HIPÓTESE
```

O processo é contínuo.

Cada investigação pode gerar novas perguntas.

---

# 16. Threat Hunting como evolução do SOC

Um SOC pode começar dependendo principalmente de:

```text
Alertas
```

Com o amadurecimento, começa a trabalhar com:

```text
Alertas
+
Correlação
+
Threat Intelligence
+
Threat Hunting
```

Isso muda a postura de segurança.

O SOC deixa de apenas reagir ao que seus controles conseguem detectar e passa também a questionar:

> **"O que pode estar acontecendo no ambiente que ainda não estamos vendo?"**

Essa pergunta é a essência do Threat Hunting.

---

# 17. O papel do Threat Hunter

O Threat Hunter precisa combinar conhecimento técnico com capacidade investigativa.

Entre as habilidades importantes estão:

* Análise de logs
* Redes
* Sistemas operacionais
* SIEM
* MITRE ATT&CK
* Threat Intelligence
* Scripts e automação
* Investigação de processos
* Análise de comportamento
* Raciocínio analítico

Mas existe uma habilidade que conecta todas as outras:

**saber fazer perguntas.**

Um bom Hunter não olha apenas para um evento.

Ele pergunta:

```text
Por que isso aconteceu?

Isso é normal?

Quem executou?

De onde veio?

O que aconteceu antes?

O que aconteceu depois?

Existe outra máquina fazendo a mesma coisa?

Existe outro usuário relacionado?

Esse comportamento aparece em alguma técnica conhecida?
```

---

# 18. Threat Hunting em um SOC real

Um cenário pode ser:

```text
              SIEM
                │
        Eventos do ambiente
                │
                ▼
        Threat Hunter
                │
        ┌───────┴───────┐
        ▼               ▼
     Hipótese         Dados
        │               │
        └───────┬───────┘
                ▼
             Hunting
                │
        ┌───────┴────────┐
        ▼                ▼
   Sem evidência      Evidência
        │                │
        ▼                ▼
    Documentar        Investigar
                         │
                         ▼
                    Responder
                         │
                         ▼
                  Criar detecção
```

Esse último passo é importante.

O objetivo não é simplesmente encontrar ameaças.

O objetivo é **melhorar continuamente a capacidade do SOC de encontrá-las**.

---

# 19. Boas práticas

Ao realizar Threat Hunting:

* Comece com uma hipótese.
* Defina quais evidências seriam esperadas.
* Utilize dados confiáveis.
* Conheça o comportamento normal do ambiente.
* Procure relações entre eventos.
* Não dependa apenas de IOCs.
* Utilize MITRE ATT&CK para estruturar investigações.
* Documente consultas e resultados.
* Registre hipóteses descartadas.
* Transforme descobertas em novas regras quando possível.
* Revise continuamente as fontes de dados.
* Evite conclusões baseadas em um único evento.

---

# 20. Threat Hunting em uma frase

Threat Hunting pode ser resumido como:

```text
Não esperar o alerta.
Procurar a ameaça.
```

Mas, em um SOC profissional, o conceito vai além disso.

O objetivo é buscar evidências de comprometimento utilizando **hipóteses, dados, comportamento, contexto e investigação**, mesmo quando os mecanismos tradicionais de detecção ainda não encontraram nada.

---

# Conclusão

Threat Hunting representa uma mudança de postura dentro da segurança.

Em vez de depender exclusivamente de regras que dizem:

> **"Isso parece suspeito."**

o analista também passa a perguntar:

> **"E se já existir algo acontecendo que nossas regras ainda não conseguem enxergar?"**

Essa mudança transforma o SOC de uma operação puramente reativa em uma operação mais **proativa, investigativa e orientada por evidências**.

O Threat Hunter não procura apenas indicadores.

Ele procura **comportamentos, relações e anomalias que possam revelar uma ameaça antes que ela se torne um incidente maior**.

> **Detecção espera pelo sinal.
> Threat Hunting procura o que ainda não virou sinal.**
