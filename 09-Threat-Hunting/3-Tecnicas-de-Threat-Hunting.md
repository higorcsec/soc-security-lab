# Técnicas de Threat Hunting

Threat Hunting não depende de uma única ferramenta ou técnica.

Um atacante pode deixar rastros em diferentes partes do ambiente:

```text
Endpoint
   ↓
Processos
   ↓
Autenticação
   ↓
Rede
   ↓
DNS
   ↓
Arquivos
   ↓
Persistência
```

Por isso, o Threat Hunter precisa trabalhar com diferentes fontes de evidência e, principalmente, saber **conectar esses sinais**.

A pergunta não é apenas:

> "Existe algo suspeito?"

Mas:

> **"Existe uma sequência de eventos que faça sentido dentro de um possível ataque?"**

---

# 1. Hunting baseado em comportamento

Uma das técnicas mais importantes é procurar comportamentos anormais em vez de depender exclusivamente de indicadores conhecidos.

Um IOC pode mudar.

O comportamento necessário para realizar um ataque muitas vezes permanece semelhante.

Exemplo:

```text
Usuário
   ↓
Documento
   ↓
PowerShell
   ↓
Download
   ↓
Execução
   ↓
Comunicação externa
```

Mesmo que o atacante altere:

```text
IP
Domínio
Hash
Nome do arquivo
```

a cadeia de comportamento pode continuar sendo identificável.

Por isso, o Hunting baseado em comportamento é uma das principais formas de procurar ameaças desconhecidas.

---

# 2. Hunting baseado em processos

Processos são uma das fontes mais importantes de evidência em endpoints.

O Hunter pode investigar:

* Processo executado
* Processo pai
* Usuário responsável
* Linha de comando
* Horário
* Caminho do executável
* Frequência
* Relacionamento com outros processos

Um processo isolado pode ser legítimo.

A relação entre processos pode ser muito mais interessante.

Exemplo:

```text
winword.exe
     ↓
powershell.exe
     ↓
cmd.exe
     ↓
arquivo.exe
```

Essa sequência merece investigação.

Outro exemplo:

```text
w3wp.exe
   ↓
cmd.exe
   ↓
powershell.exe
```

Em um servidor web, essa cadeia pode representar uma tentativa de exploração seguida de execução de comandos.

O contexto do servidor será fundamental para determinar se a atividade é legítima ou suspeita.

---

# 3. Parent-Child Process

Uma técnica muito útil é analisar a relação entre **processo pai e processo filho**.

Exemplo normal:

```text
explorer.exe
    ↓
notepad.exe
```

Agora:

```text
winword.exe
    ↓
powershell.exe
```

ou:

```text
w3wp.exe
    ↓
cmd.exe
```

A relação pode indicar comportamento relevante.

O Hunter deve perguntar:

```text
Esse processo normalmente cria esse filho?

O usuário possui permissão para executar essa ação?

O processo ocorreu em um servidor crítico?

A linha de comando é esperada?

Existe comunicação externa depois da execução?
```

A técnica não consiste em bloquear automaticamente uma relação.

Consiste em identificar relações que merecem investigação.

---

# 4. Hunting por Command Line

O nome do processo nem sempre é suficiente.

Considere:

```text
powershell.exe
```

O processo é legítimo.

Agora:

```text
powershell.exe -EncodedCommand ...
```

Existe um contexto adicional.

Command Line pode revelar:

* Scripts
* Argumentos
* URLs
* Caminhos
* Comandos
* Downloads
* Execução codificada
* Alterações no sistema

Por isso, quando possível, o Hunter deve analisar:

```text
Processo
+
Command Line
+
Usuário
+
Processo Pai
+
Horário
```

Essa combinação gera muito mais contexto.

---

# 5. Hunting de PowerShell

PowerShell é uma ferramenta legítima e extremamente poderosa.

Justamente por isso, pode ser utilizada por administradores e também por atacantes.

O objetivo do Hunting não deve ser:

> "PowerShell = ameaça."

A pergunta correta é:

> **"Existe utilização de PowerShell incompatível com o comportamento esperado daquele ambiente?"**

Exemplo:

```text
Servidor
   ↓
PowerShell
   ↓
Script administrativo conhecido
   ↓
Usuário autorizado
   ↓
Janela de manutenção
```

Provavelmente legítimo.

Agora:

```text
Endpoint de usuário
   ↓
PowerShell
   ↓
Execução codificada
   ↓
Download externo
   ↓
Conexão com IP suspeito
```

O contexto é completamente diferente.

---

# 6. Hunting de autenticação

Credenciais comprometidas são frequentemente utilizadas para acessar ambientes internos.

Por isso, eventos de autenticação são uma fonte importante de Hunting.

O Hunter pode procurar:

```text
Login fora do horário
Login de localização incomum
Múltiplas falhas
Sucesso após várias falhas
Conta administrativa
Acesso a servidores incomuns
Acesso simultâneo
```

Um exemplo:

```text
00:41 → Falha
00:42 → Falha
00:43 → Falha
00:44 → Sucesso
00:45 → Acesso administrativo
```

A sequência merece investigação.

O valor está na correlação dos eventos.

---

# 7. Hunting por Anomalia

Anomaly Hunting procura desvios em relação ao comportamento esperado.

Imagine que um usuário normalmente faça:

```text
09:00 → Login
09:10 → E-mail
09:30 → Sistema interno
```

De repente:

```text
02:47 → Login
02:50 → VPN
02:53 → Servidor de banco
02:55 → PowerShell
```

Nenhum evento isolado precisa ser malicioso.

A sequência, porém, representa uma mudança significativa de comportamento.

O Hunter pode utilizar:

```text
Baseline
+
Comportamento atual
=
Anomalia
```

---

# 8. Hunting de DNS

DNS é uma excelente fonte de dados para Hunting.

Mesmo quando o atacante utiliza HTTPS, o endpoint frequentemente precisa resolver um domínio.

O Hunter pode procurar:

* Domínios recém-observados
* Domínios incomuns
* Alto volume de consultas
* Subdomínios suspeitos
* Consultas para domínios associados a ameaças
* Padrões anormais de resolução

Um cenário:

```text
Endpoint
   ↓
Consulta DNS
   ↓
Domínio desconhecido
   ↓
Conexão externa
   ↓
Download
```

O domínio pode ser investigado posteriormente utilizando Threat Intelligence e OSINT.

---

# 9. Hunting de tráfego de rede

O tráfego de rede permite observar relações entre sistemas.

Alguns elementos interessantes:

```text
Origem
Destino
Porta
Protocolo
Horário
Volume
Frequência
```

Exemplo:

```text
WS-001
   ↓
SRV-DB-01
   ↓
Porta 3306
```

Se aquela estação nunca deveria acessar o banco diretamente, o evento pode justificar investigação.

Outro cenário:

```text
Endpoint
   ↓
IP externo
   ↓
443
   ↓
Conexão recorrente
```

A porta 443 não torna a comunicação legítima automaticamente.

O contexto precisa ser analisado.

---

# 10. Hunting de Command and Control

Uma infraestrutura de C2 permite que o atacante mantenha comunicação com sistemas comprometidos.

O Hunter pode procurar padrões como:

```text
Conexões periódicas
Destinos incomuns
Domínios suspeitos
Comunicações persistentes
Padrões de beaconing
```

Um exemplo simplificado:

```text
10:00 → conexão
10:05 → conexão
10:10 → conexão
10:15 → conexão
10:20 → conexão
```

Uma comunicação periódica pode ser relevante.

Porém, também existem aplicações legítimas que realizam comunicação periódica.

Por isso:

> **Periodicidade é um sinal de investigação, não uma prova de C2.**

---

# 11. Hunting de Beaconing

Beaconing é um comportamento no qual um sistema realiza comunicações periódicas com determinado destino.

Visualmente:

```text
Endpoint
   │
   ├── 10:00 → C2
   ├── 10:05 → C2
   ├── 10:10 → C2
   ├── 10:15 → C2
   └── 10:20 → C2
```

O Hunter pode analisar:

```text
Intervalo
Destino
Volume
Horário
Quantidade de endpoints
Processo responsável
```

Um dos pontos mais interessantes é relacionar o tráfego ao processo:

```text
Conexão externa
      ↓
Processo responsável
      ↓
Usuário
      ↓
Command Line
```

Isso pode transformar uma anomalia de rede em uma investigação de endpoint.

---

# 12. Hunting de movimentação lateral

Depois de comprometer um sistema, um atacante pode tentar acessar outros sistemas.

O Hunter pode procurar:

```text
RDP
SMB
WinRM
SSH
PsExec
WMI
```

Exemplo:

```text
WS-001
   ↓
SRV-01
   ↓
SRV-02
   ↓
SRV-03
```

Se um usuário ou endpoint normalmente acessa apenas um servidor e, repentinamente, começa a acessar vários sistemas, existe um comportamento que merece investigação.

O objetivo é identificar possíveis padrões de:

```text
Origem
   ↓
Destino
   ↓
Usuário
   ↓
Método
   ↓
Horário
```

---

# 13. Hunting de persistência

Um atacante pode tentar garantir que seu acesso continue disponível mesmo após reinicializações ou encerramento de sessões.

O Hunter pode investigar mecanismos como:

```text
Scheduled Tasks
Services
Startup
Registry
SSH Keys
Cron
Systemd
```

Exemplo:

```text
Tarefa agendada criada
        ↓
Usuário incomum
        ↓
Executa script
        ↓
Comunicação externa
```

A criação da tarefa não significa automaticamente comprometimento.

Mas a combinação dos eventos pode gerar uma hipótese forte.

---

# 14. Hunting de arquivos

Arquivos podem fornecer evidências importantes.

O Hunter pode procurar:

* Executáveis recém-criados
* Scripts
* Arquivos temporários
* Alterações em diretórios críticos
* Arquivos com extensões incomuns
* Hashes conhecidos
* Arquivos executados recentemente

Um exemplo:

```text
Download
   ↓
Arquivo novo
   ↓
Execução
   ↓
Processo filho
   ↓
Conexão externa
```

Essa cadeia é muito mais relevante do que simplesmente encontrar um arquivo desconhecido.

---

# 15. Hunting de PowerShell + Rede

Uma técnica poderosa é combinar fontes diferentes.

Por exemplo:

```text
PowerShell
     +
Conexão externa
```

O Hunter pode procurar:

```text
powershell.exe
       ↓
Processo executado
       ↓
Conexão externa
       ↓
Destino
```

Agora é possível investigar:

```text
O destino é conhecido?

O domínio possui má reputação?

O processo foi iniciado por um usuário comum?

O comando contém download?

Existe arquivo criado depois?
```

A correlação reduz o número de eventos irrelevantes.

---

# 16. Hunting por sequência de eventos

Uma ameaça raramente aparece como um único evento.

Muitas vezes existe uma sequência:

```text
Phishing
   ↓
Execução
   ↓
Persistência
   ↓
C2
   ↓
Credential Access
   ↓
Movimentação lateral
```

O Hunter pode tentar identificar partes dessa cadeia.

Exemplo:

```text
E-mail
 ↓
Word
 ↓
PowerShell
 ↓
Download
 ↓
Execução
 ↓
Conexão externa
```

Cada evento isolado pode ter uma explicação legítima.

A sequência pode contar outra história.

---

# 17. Hunting baseado em MITRE ATT&CK

MITRE ATT&CK pode ser utilizado para transformar técnicas conhecidas de ataque em buscas.

Exemplo:

```text
Tática:
Persistence

        ↓

Técnica:
Scheduled Task/Job

        ↓

Hunting:
Buscar criação de tarefas
e alterações recentes.

        ↓

Evidências:
Usuário + comando + horário + endpoint
```

Outro exemplo:

```text
Tática:
Credential Access

        ↓

Técnica:
OS Credential Dumping

        ↓

Hunting:
Investigar processos e comportamentos
associados à obtenção de credenciais.
```

O framework ajuda a organizar o raciocínio.

---

# 18. Hunting por raridade

Outra técnica é procurar eventos raros.

Imagine:

```text
PowerShell executado:
10.000 vezes

Em um servidor específico:
1 ocorrência
```

A ocorrência isolada pode ser interessante porque foge do padrão daquele ambiente.

O mesmo conceito pode ser aplicado a:

```text
Usuário raro
Processo raro
Destino raro
Porta rara
Comando raro
Endpoint raro
```

Raridade não significa malícia.

Mas pode ajudar o Hunter a encontrar **pontos fora da curva**.

---

# 19. Hunting baseado em frequência

Também é possível procurar alterações bruscas de frequência.

Exemplo:

```text
DNS Queries

Segunda:
200

Terça:
220

Quarta:
190

Quinta:
4.800
```

O aumento pode ter uma explicação legítima.

Mas representa uma mudança que merece investigação.

Outro exemplo:

```text
Falhas de login:
Normal → 5/dia

Atual:
1.200/dia
```

Isso pode indicar:

* Brute Force
* Password Spraying
* Erro de configuração
* Aplicação com problema

O Hunter precisa investigar o contexto antes de concluir.

---

# 20. Hunting baseado em baseline

Baseline representa o comportamento esperado.

Exemplo:

```text
Servidor Web

Processos normais:
nginx
php-fpm
systemd
```

Agora aparece:

```text
bash
curl
wget
python
```

Isso não significa automaticamente comprometimento.

Mas é uma alteração relevante em relação ao baseline.

O processo pode ser:

```text
Baseline
   ↓
Comportamento atual
   ↓
Desvio
   ↓
Investigação
```

---

# 21. Hunting com múltiplas fontes

As melhores investigações normalmente não dependem de apenas um log.

Imagine:

```text
Windows Event
       +
DNS
       +
Firewall
       +
Endpoint
       +
Threat Intelligence
```

O resultado pode ser:

```text
Usuário
   ↓
Executou PowerShell
   ↓
Consultou domínio suspeito
   ↓
Conectou ao IP
   ↓
IP possui reputação maliciosa
```

A confiança da investigação aumenta porque existem múltiplas evidências independentes apontando para o mesmo comportamento.

---

# 22. O princípio da correlação

Um dos conceitos mais importantes do Threat Hunting é:

> **Um evento pode ser normal. Uma sequência de eventos pode ser anormal.**

Exemplo:

```text
PowerShell
```

Pode ser normal.

```text
PowerShell
+
Download
```

Mais interessante.

```text
PowerShell
+
Download
+
Execução
+
Conexão externa
```

Muito mais relevante.

A investigação cresce conforme o contexto aumenta.

---

# 23. Hunting em Windows

Em ambientes Windows, algumas fontes importantes incluem:

```text
Security Events
PowerShell Logs
Sysmon
Process Creation
Logon Events
Windows Defender
DNS Client
Task Scheduler
```

Esses dados podem ser utilizados para investigar:

```text
Execução
Persistência
Credenciais
Movimentação lateral
Comunicação
Alterações no sistema
```

Quanto maior a visibilidade do endpoint, maior a capacidade de Hunting.

---

# 24. Hunting em Linux

Em Linux, o Hunter pode trabalhar com:

```text
auth.log
secure
journalctl
auditd
SSH logs
Processos
Cron
Systemd
Shell history
Network connections
```

Exemplo:

```text
SSH login
   ↓
sudo
   ↓
wget
   ↓
chmod
   ↓
execução
```

Essa sequência pode justificar uma investigação dependendo do servidor e do usuário envolvidos.

---

# 25. Hunting com Wazuh

Em um ambiente com Wazuh, diferentes fontes podem ser centralizadas e analisadas.

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
Hunting
   ↓
Query / Filtros
   ↓
Investigação
```

O Wazuh pode fornecer visibilidade sobre:

```text
Processos
Integridade de arquivos
Autenticação
Vulnerabilidades
Logs
Comandos
Eventos de segurança
```

O Hunter pode utilizar essas informações para validar hipóteses.

---

# 26. Da técnica à detecção

Uma das maiores vantagens do Threat Hunting é transformar descobertas em detecções.

Exemplo:

```text
HUNTING

PowerShell
+
Processo pai incomum
+
Download
+
Conexão externa

        ↓

Comportamento confirmado

        ↓

Nova regra de detecção

        ↓

SIEM

        ↓

Próximas ocorrências
geram alerta automaticamente
```

Isso cria um ciclo de melhoria:

```text
Hunting
   ↓
Descoberta
   ↓
Detecção
   ↓
Alerta
   ↓
Investigação
   ↓
Novo Hunting
```

---

# 27. O que não fazer

Threat Hunting também possui armadilhas.

Evite:

```text
Executar queries sem hipótese
```

```text
Procurar apenas IOCs conhecidos
```

```text
Tratar qualquer anomalia como ataque
```

```text
Ignorar atividade legítima
```

```text
Investigar sem documentar
```

```text
Confiar em apenas uma fonte de dados
```

```text
Criar regras baseadas em um único caso
```

O objetivo é produzir **evidência**, não simplesmente gerar suspeitas.

---

# 28. Checklist de uma investigação

Antes de finalizar um Hunting, pergunte:

```text
[ ] Qual era a hipótese?

[ ] Qual comportamento estava sendo investigado?

[ ] Quais dados foram utilizados?

[ ] Quais eventos foram encontrados?

[ ] Existe contexto suficiente?

[ ] A atividade é legítima ou suspeita?

[ ] Existem evidências adicionais?

[ ] A hipótese foi confirmada, descartada ou ficou inconclusiva?

[ ] Existe necessidade de resposta a incidente?

[ ] Uma nova regra de detecção deve ser criada?

[ ] Existe alguma melhoria de logging necessária?
```

---

# 29. Resumo das principais técnicas

```text
Comportamento
      ↓
Processos
      ↓
Parent-Child
      ↓
Command Line
      ↓
Autenticação
      ↓
DNS
      ↓
Rede
      ↓
C2 / Beaconing
      ↓
Movimentação lateral
      ↓
Persistência
      ↓
Arquivos
      ↓
Anomalias
      ↓
Baseline
      ↓
Correlação
```

Nenhuma dessas técnicas precisa funcionar isoladamente.

O maior valor aparece quando várias delas são combinadas.

---

# Conclusão

Threat Hunting é menos sobre encontrar um "indicador mágico" e mais sobre **entender como uma ameaça se comportaria dentro do ambiente**.

Um Hunter pode começar com:

```text
Processo
```

e chegar a:

```text
Processo
 ↓
Usuário
 ↓
Command Line
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
Possível incidente
```

Essa capacidade de conectar diferentes fontes é o que transforma logs em investigação.

O objetivo final não é apenas encontrar ameaças.

É descobrir **como elas poderiam passar pelos controles existentes e transformar esse conhecimento em novas formas de detecção**.

> **O IOC mostra uma pista.
> O comportamento mostra o caminho.
> A correlação mostra a história.
> O Hunting procura essa história antes que ela termine em um incidente.**
