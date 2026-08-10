# Casos de Threat Hunting

Threat Hunting ganha valor quando deixa de ser apenas uma metodologia e passa a ser aplicado sobre situações reais.

Um bom caso de Hunting não precisa começar com um alerta.

Pode começar com:

```text id="4n7w3r"
Uma anomalia
Uma hipótese
Uma informação de Threat Intelligence
Um incidente anterior
Uma vulnerabilidade
Uma mudança de comportamento
```

A partir disso, o analista procura evidências no ambiente e tenta responder:

> **"Existe algum comportamento compatível com essa ameaça acontecendo aqui?"**

Os casos abaixo representam cenários comuns que podem ser utilizados para estruturar atividades de Threat Hunting em um SOC.

---

# 1. Caso — PowerShell suspeito

## Cenário

O SOC recebeu informações de Threat Intelligence indicando campanhas utilizando PowerShell para execução de comandos após o comprometimento inicial.

Não existe nenhum alerta específico relacionado à campanha.

O Threat Hunter cria a hipótese:

> **"Pode existir uso anormal de PowerShell em endpoints do ambiente que ainda não foi detectado."**

---

## Dados utilizados

```text id="m6e2pm"
Windows Event Logs
PowerShell Logs
Process Creation
Network Logs
SIEM
```

---

## Hunting

O primeiro passo é identificar a utilização de PowerShell:

```text id="jrlk8g"
powershell.exe
```

Depois, adicionar contexto:

```text id="s5f8aa"
Processo
+
Usuário
+
Endpoint
+
Command Line
+
Horário
```

Durante a investigação aparece:

```text id="y4otq8"
HOST-014

Usuário:
user01

Processo:
powershell.exe

Horário:
02:17

Processo pai:
winword.exe
```

Agora o comportamento merece investigação.

---

## Pivot

O Hunter investiga o que aconteceu depois:

```text id="6cvlxy"
winword.exe
      ↓
powershell.exe
      ↓
Conexão externa
      ↓
Download
```

O domínio acessado também é consultado em Threat Intelligence.

Resultado:

```text id="0r4w6y"
Domínio:
suspicious-domain[.]com

Classificação:
Malicioso
```

---

## Conclusão

A hipótese ganha evidências fortes.

```text id="q44u7q"
Documento
   ↓
PowerShell
   ↓
Download
   ↓
Domínio malicioso
```

O caso deve ser escalado para investigação de incidente.

---

## Melhorias geradas

O Hunting também pode gerar melhorias:

```text id="b50a0e"
Nova regra de detecção
        +
PowerShell Logging
        +
Monitoramento de processos
        +
Correlação com Threat Intelligence
```

---

# 2. Caso — Login administrativo fora do padrão

## Cenário

Uma conta administrativa normalmente é utilizada durante o horário comercial.

O Hunter cria a hipótese:

> **"Uma credencial administrativa pode ter sido comprometida e estar sendo utilizada fora do padrão."**

---

## Dados utilizados

```text id="2nwdm8"
Active Directory
VPN
Windows Security Logs
SIEM
```

---

## Hunting

O analista procura autenticações da conta:

```text id="e1ljx4"
admin01
```

Resultado:

```text id="72x4h6"
01:42 → Login
01:44 → Login em servidor
01:47 → Acesso administrativo
```

A origem também chama atenção:

```text id="3x0y4u"
IP de origem:
185.XX.XX.20
```

O IP não pertence ao range normalmente utilizado pela organização.

---

## Pivot

O Hunter pesquisa o mesmo IP:

```text id="h6df3w"
185.XX.XX.20
        ↓
admin01
        ↓
SRV-01
        ↓
SRV-03
        ↓
SRV-05
```

A mesma conta acessou vários servidores em poucos minutos.

---

## Investigação

Agora as perguntas são:

```text id="q3x6zq"
O usuário estava trabalhando?

O IP pertence à VPN?

O acesso foi autorizado?

Existem outros logins?

Houve execução de comandos?

Houve alterações nos servidores?
```

Se não houver justificativa operacional, a hipótese ganha força.

---

## Conclusão

O caso pode indicar:

```text id="5p4j19"
Credencial comprometida
        ↓
Acesso inicial
        ↓
Movimentação lateral
```

O caso deve ser encaminhado para Incident Response.

---

# 3. Caso — Possível Command and Control

## Cenário

Durante uma atividade de Hunting, o analista identifica um endpoint realizando conexões periódicas para o mesmo endereço externo.

Não existe alerta de malware.

Hipótese:

> **"Um endpoint pode estar realizando beaconing para uma infraestrutura de Command and Control."**

---

## Dados utilizados

```text id="bd2h6a"
Firewall
DNS
Endpoint Logs
Network Logs
Threat Intelligence
SIEM
```

---

## Hunting

O comportamento observado:

```text id="5hx19v"
10:00 → 185.XX.XX.10
10:05 → 185.XX.XX.10
10:10 → 185.XX.XX.10
10:15 → 185.XX.XX.10
10:20 → 185.XX.XX.10
```

A periodicidade chama atenção.

Mas periodicidade sozinha não prova C2.

---

## Pivot para o endpoint

O Hunter procura qual processo realizou a comunicação.

Resultado:

```text id="q5u6vd"
HOST-022
     ↓
powershell.exe
     ↓
185.XX.XX.10
```

Agora existe uma relação entre:

```text id="zj7p8n"
Processo
+
Rede
```

---

## Threat Intelligence

O IP é consultado.

Resultado:

```text id="o7j7lq"
Classificação:
C2

Confidence:
Alta
```

O Hunter também verifica o domínio relacionado.

---

## Conclusão

A cadeia encontrada:

```text id="e0g5xj"
Endpoint
   ↓
PowerShell
   ↓
Comunicação periódica
   ↓
IP conhecido como C2
```

representa evidência suficiente para uma investigação mais profunda.

---

# 4. Caso — Movimentação lateral

## Cenário

Um servidor normalmente recebe conexões de poucos sistemas.

Durante o Hunting, o analista percebe que ele começou a receber conexões de diversos endpoints.

Hipótese:

> **"Pode existir movimentação lateral dentro da rede."**

---

## Hunting

O Hunter analisa:

```text id="f1t9h8"
Origem
Destino
Usuário
Protocolo
Porta
Horário
```

Resultado:

```text id="4o1a2z"
WS-01 → SRV-01
WS-01 → SRV-02
WS-01 → SRV-03
WS-01 → SRV-04
```

Tudo ocorreu em poucos minutos.

---

## Pivot

O analista verifica o usuário:

```text id="oq5xq7"
user01
```

E descobre:

```text id="4cz0c5"
user01
não possui normalmente acesso administrativo
```

Também aparece:

```text id="z3v2j8"
PowerShell
+
WMI
+
Conexões internas
```

---

## Possível cadeia

```text id="0i0n8v"
Endpoint comprometido
       ↓
Credencial obtida
       ↓
Execução remota
       ↓
Movimentação lateral
```

---

## Ação

O caso deve ser investigado com prioridade.

Possíveis ações:

```text id="kdrgta"
Isolar endpoint
Validar credenciais
Investigar servidores acessados
Pesquisar o mesmo comportamento no SIEM
```

---

# 5. Caso — Persistência através de tarefa agendada

## Cenário

O SOC possui uma hipótese de que um atacante pode estar utilizando tarefas agendadas para manter persistência.

Hipótese:

> **"Existem tarefas agendadas criadas recentemente que podem estar sendo utilizadas para persistência."**

---

## Hunting

O analista procura:

```text id="m7u1s8"
Novas tarefas
Alterações
Usuário responsável
Comando executado
Horário
Endpoint
```

Encontra:

```text id="e4tv4g"
Task:
WindowsUpdateCheck

Criada:
03:14

Executa:
powershell.exe

Usuário:
user01
```

O nome parece legítimo.

Mas o conteúdo da tarefa precisa ser analisado.

---

## Pivot

O comando executado pela tarefa realiza:

```text id="c7l0kr"
Download externo
```

Depois:

```text id="x8hj2s"
Conexão para domínio suspeito
```

Agora existe uma cadeia:

```text id="k6v44y"
Scheduled Task
      ↓
PowerShell
      ↓
Download
      ↓
Comunicação externa
```

---

## Conclusão

O comportamento é compatível com um possível mecanismo de persistência.

A investigação deve verificar:

```text id="j4j0zn"
Quando a tarefa foi criada?

Quem criou?

Qual processo criou?

Existem tarefas semelhantes?

Outros endpoints possuem a mesma tarefa?

Qual arquivo é executado?
```

---

# 6. Caso — Phishing e credenciais comprometidas

## Cenário

O SOC recebe informações sobre uma campanha de phishing.

O objetivo do Hunting é verificar se algum usuário interno acessou a infraestrutura utilizada pela campanha.

Hipótese:

> **"Usuários da organização podem ter acessado domínios relacionados à campanha de phishing."**

---

## Hunting

O analista começa com os indicadores conhecidos:

```text id="2v6dne"
Domínio
URL
IP
Hash
```

Depois pesquisa no SIEM:

```text id="2g2b5m"
DNS
+
Proxy
+
Firewall
```

Resultado:

```text id="v3xw2c"
user01
 ↓
phishing-domain[.]com
 ↓
14:32
```

---

## Pivot

Agora o Hunter investiga:

```text id="v48wuj"
O usuário acessou a página?

Houve autenticação?

Qual endpoint foi utilizado?

Existem outros usuários?

O domínio foi acessado anteriormente?
```

Também pode verificar:

```text id="9zjz07"
Login suspeito
+
VPN
+
Acesso a recursos internos
```

---

## Possível impacto

O cenário pode evoluir para:

```text id="pyy6jc"
Phishing
 ↓
Credencial capturada
 ↓
Login externo
 ↓
Acesso interno
```

Esse tipo de caso demonstra como Threat Hunting pode conectar diferentes etapas de um ataque.

---

# 7. Caso — Servidor comprometido sem malware conhecido

## Cenário

Um servidor web apresenta comportamento estranho.

Não existe hash conhecido de malware.

Nenhum antivírus detectou ameaça.

Hipótese:

> **"O servidor pode ter sido comprometido utilizando ferramentas legítimas do sistema."**

Esse é um cenário importante porque mostra a limitação de depender somente de malware conhecido.

---

## Hunting

O Hunter investiga processos:

```text id="ezm5qj"
nginx
 ↓
bash
 ↓
curl
 ↓
python
```

O servidor normalmente não executa Python durante sua operação.

Agora o comportamento merece investigação.

---

## Pivot

O analista verifica:

```text id="n0s5tc"
Quem iniciou o processo?

Qual comando foi executado?

Qual arquivo foi criado?

Houve conexão externa?

Existe persistência?

Qual usuário estava envolvido?
```

Resultado:

```text id="k3j1xw"
www-data
 ↓
bash
 ↓
curl
 ↓
download
```

---

## Conclusão

Mesmo sem encontrar um malware conhecido, existe uma cadeia de comportamento suspeito.

Esse é um dos motivos pelos quais Threat Hunting deve considerar:

```text id="8b3r5z"
Comportamento
+
Processos
+
Rede
+
Contexto
```

e não somente assinaturas.

---

# 8. Caso — PowerShell em servidor que normalmente não utiliza PowerShell

## Cenário

Um servidor possui função específica e não deveria utilizar PowerShell regularmente.

Durante um Hunting de baseline, aparece:

```text id="2l0h2v"
SRV-WEB-01
↓
powershell.exe
```

Hipótese:

> **"Existe execução não esperada de PowerShell no servidor web."**

---

## Investigação

O Hunter verifica:

```text id="7w6u3s"
Usuário
Processo pai
Command Line
Horário
Conexões
Arquivo criado
```

Resultado:

```text id="x3p0o4"
Processo pai:
w3wp.exe

Filho:
powershell.exe
```

Esse relacionamento merece atenção.

---

## Possível cenário

```text id="e5x4td"
Aplicação Web
     ↓
Exploração
     ↓
Execução de comando
     ↓
PowerShell
```

O Hunter agora precisa verificar se houve exploração da aplicação.

Esse tipo de investigação demonstra como o contexto da função do servidor muda completamente a interpretação de um processo.

---

# 9. Caso — Password Spraying

## Cenário

O SOC suspeita que um atacante esteja tentando descobrir credenciais utilizando poucas senhas contra muitas contas.

Hipótese:

> **"Pode estar ocorrendo Password Spraying contra contas corporativas."**

---

## Hunting

O analista procura:

```text id="q66w4f"
Falhas de autenticação
+
IP de origem
+
Quantidade de usuários
+
Intervalo de tempo
```

Resultado:

```text id="u3c0w9"
IP: 185.XX.XX.30

user01 → falha
user02 → falha
user03 → falha
user04 → falha
user05 → sucesso
```

O comportamento é muito mais relevante do que uma única falha de login.

---

## Pivot

O Hunter verifica:

```text id="ef7k6k"
O usuário que teve sucesso acessou sistemas?

Houve VPN?

Houve MFA?

O mesmo IP tentou outras contas?

Existem outros IPs realizando o mesmo comportamento?
```

---

## Possível cadeia

```text id="5n2b7v"
Password Spraying
       ↓
Credencial válida
       ↓
Acesso
       ↓
Possível comprometimento
```

---

# 10. Caso — DNS como indicador inicial

## Cenário

O SOC não possui um IOC conhecido.

Durante um Hunting de DNS, um endpoint apresenta milhares de consultas para subdomínios aleatórios.

Hipótese:

> **"O comportamento DNS pode indicar comunicação anormal ou mecanismo de exfiltração."**

---

## Hunting

O analista observa:

```text id="i7h3u6"
HOST-09
 ↓
abc123.domain.com
 ↓
x92kd1.domain.com
 ↓
8dj21a.domain.com
 ↓
k29sd8.domain.com
```

Os subdomínios possuem padrões incomuns.

---

## Investigação

O Hunter verifica:

```text id="y1t1z1"
Quantidade de consultas
Intervalo
Processo responsável
Domínio principal
Outros endpoints
```

Também compara com o comportamento normal da aplicação.

Se apenas um endpoint apresentar o padrão, o caso se torna mais interessante.

---

# 11. Caso — Hunting pós-incidente

## Cenário

Um endpoint foi confirmado como comprometido.

O incidente foi contido.

Mas existe uma pergunta importante:

> **"O atacante comprometeu apenas esse endpoint?"**

O SOC cria novas hipóteses:

```text id="byz6j5"
H1:
Outros endpoints possuem os mesmos IOCs?

H2:
Outros usuários apresentaram comportamento semelhante?

H3:
A mesma técnica foi utilizada em outros sistemas?

H4:
Existe persistência em outras máquinas?

H5:
Existiu movimentação lateral?
```

---

## Hunting

O SOC pesquisa os indicadores e comportamentos em todo o ambiente.

```text id="c5d3po"
IOC
 ↓
SIEM
 ↓
Todos os endpoints
 ↓
Todos os usuários
 ↓
Todos os servidores
```

Esse processo pode revelar que o incidente era maior do que inicialmente imaginado.

---

# 12. Caso — Hunting baseado em vulnerabilidade

## Cenário

Uma vulnerabilidade crítica é descoberta em um servidor exposto à internet.

O servidor foi corrigido.

Mesmo assim, surge a pergunta:

> **"Esse servidor foi explorado antes da correção?"**

---

## Hipótese

> "O servidor pode ter sido explorado durante o período em que estava vulnerável."

---

## Hunting

O analista investiga:

```text id="87c84v"
Web Logs
Firewall
Processos
DNS
Autenticação
Arquivos
```

Procura por:

```text id="ez1k0u"
Requisições anormais
+
Processos inesperados
+
Arquivos novos
+
Conexões externas
```

---

## Possível resultado

```text id="1i4a2m"
Exploit
 ↓
Processo inesperado
 ↓
Download
 ↓
Execução
 ↓
C2
```

Nesse caso, a correção da vulnerabilidade não encerra a investigação.

O objetivo é verificar se houve exploração antes da correção.

---

# 13. O que esses casos têm em comum?

Apesar de serem ataques diferentes, o processo de Hunting permanece semelhante.

```text id="v6v8kh"
HIPÓTESE
   ↓
DADOS
   ↓
QUERY
   ↓
EVIDÊNCIA
   ↓
PIVOT
   ↓
CORRELAÇÃO
   ↓
CONTEXTO
   ↓
CONCLUSÃO
```

A diferença está nos comportamentos investigados.

---

# 14. O verdadeiro trabalho do Hunter

Em todos os casos anteriores, o Hunter não ficou limitado a uma única pergunta.

Ele começou com:

```text id="r9t9yi"
"Existe esse comportamento?"
```

Depois passou para:

```text id="j37w4j"
"Quem fez?"
```

Depois:

```text id="9ys7gq"
"Quando?"
```

Depois:

```text id="7g8d1s"
"De onde?"
```

Depois:

```text id="a9m2z4"
"O que aconteceu depois?"
```

E finalmente:

```text id="1m5g4u"
"Isso faz parte de uma atividade maior?"
```

Essa progressão é o coração de uma investigação de Threat Hunting.

---

# 15. Transformando um caso em detecção

Um caso de Hunting não deveria simplesmente terminar no relatório.

Se o comportamento for confirmado como malicioso, o SOC pode transformá-lo em uma nova detecção.

Exemplo:

```text id="9u7t1k"
Hunting
   ↓
PowerShell suspeito
   ↓
Comportamento confirmado
   ↓
Nova regra
   ↓
SIEM
   ↓
Alerta automático
```

O próximo atacante que tentar reproduzir o mesmo comportamento pode ser detectado sem depender de um novo Hunting manual.

---

# 16. Transformando um caso em inteligência

O caso também pode gerar Threat Intelligence.

Exemplo:

```text id="wq4z7v"
Incidente
   ↓
IP
   ↓
Domínio
   ↓
Hash
   ↓
Técnica
   ↓
Infraestrutura
```

Essas informações podem ser adicionadas à base de inteligência interna.

Depois:

```text id="0c5h4q"
Threat Intelligence
        ↓
SIEM
        ↓
Detecção
        ↓
Novo Hunting
```

Assim, um incidente alimenta todo o ciclo de segurança.

---

# 17. Modelo para documentar casos

Uma boa documentação de Hunting pode seguir este modelo:

```text id="n0m6xk"
# Caso H-001 — PowerShell suspeito

## Hipótese

Um atacante pode estar utilizando PowerShell
para executar comandos em endpoints.

## Motivação

Threat Intelligence indicou campanhas utilizando
PowerShell como mecanismo de execução.

## Fontes

- Windows Events
- PowerShell Logs
- Endpoint
- DNS
- Firewall

## Período analisado

2026-08-01 até 2026-08-10

## Evidências

- Processo PowerShell
- Processo pai incomum
- Download externo
- Comunicação com domínio suspeito

## Resultado

Hipótese confirmada.

## Impacto

Endpoint potencialmente comprometido.

## Ações

- Isolamento do endpoint
- Investigação de credenciais
- Busca por comportamento semelhante
- Criação de nova detecção

## MITRE ATT&CK

Execution
Command and Scripting Interpreter: PowerShell
```

Esse formato facilita a revisão por outros analistas.

---

# 18. Métricas de Threat Hunting

Casos de Hunting também podem gerar métricas.

Algumas métricas úteis:

```text id="m0g4v8"
Quantidade de hipóteses investigadas
Quantidade confirmada
Quantidade descartada
Quantidade inconclusiva
Novos IOCs encontrados
Novas regras criadas
Falhas de logging encontradas
Incidentes descobertos
Tempo de investigação
```

Exemplo:

```text id="q7y4i0"
10 hipóteses
 ↓
6 descartadas
2 inconclusivas
2 confirmadas
 ↓
4 novos IOCs
3 novas regras
1 incidente
```

Esses números ajudam a demonstrar o valor operacional do Hunting.

---

# 19. O ciclo completo

Um programa de Threat Hunting pode funcionar assim:

```text id="q8m2r7"
THREAT INTELLIGENCE
       ↓
    HIPÓTESE
       ↓
      SIEM
       ↓
     HUNTING
       ↓
    EVIDÊNCIAS
       ↓
   INVESTIGAÇÃO
       ↓
    INCIDENTE?
      /     \
    SIM      NÃO
    ↓         ↓
RESPOSTA   DOCUMENTAÇÃO
    ↓         ↓
NOVOS IOCs  BASELINE
    ↓         ↓
NOVA DETECÇÃO
       ↓
  NOVO HUNTING
```

O processo é contínuo.

---

# 20. Checklist de um caso de Hunting

```text id="w5b1yo"
[ ] Hipótese definida

[ ] Motivação documentada

[ ] Dados necessários identificados

[ ] Janela de tempo definida

[ ] Query criada

[ ] Evidências analisadas

[ ] Pivots realizados

[ ] Threat Intelligence consultada

[ ] Falsos positivos considerados

[ ] Hipótese confirmada, descartada ou inconclusiva

[ ] Impacto avaliado

[ ] Ações documentadas

[ ] Novos IOCs registrados

[ ] Nova regra de detecção avaliada

[ ] Falhas de logging registradas
```

---

# Conclusão

Casos de Threat Hunting mostram a diferença entre **saber utilizar uma ferramenta** e saber **investigar uma ameaça**.

O SIEM pode mostrar:

```text id="y0x5y0"
PowerShell
Login
DNS
Firewall
Processo
```

Mas cabe ao Hunter descobrir como esses eventos se relacionam.

Uma investigação bem conduzida pode revelar:

```text id="h4w4oc"
Usuário
 ↓
Endpoint
 ↓
Processo
 ↓
Comando
 ↓
Arquivo
 ↓
DNS
 ↓
IP
 ↓
Threat Intelligence
 ↓
Técnica MITRE ATT&CK
 ↓
Incidente
```

E o trabalho não termina quando o incidente é encerrado.

O conhecimento adquirido pode voltar para o SOC como:

```text id="3w2f9m"
Novo IOC
+
Nova regra
+
Novo caso de Hunting
+
Melhor logging
+
Novo conhecimento
```

É assim que o Threat Hunting gera evolução contínua.

> **Um bom Hunter não procura apenas ameaças.
> Ele procura aquilo que o SOC ainda não sabe detectar.**

E quando encontra, transforma a descoberta em uma nova capacidade de defesa.
