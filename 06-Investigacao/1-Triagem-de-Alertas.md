# Triagem de Alertas

## Introdução

Um SOC pode receber centenas ou milhares de alertas por dia.

O problema não é apenas detectar.

O problema é decidir:

> **O que precisa de atenção agora?**

A triagem é o processo de analisar um alerta, adicionar contexto, validar evidências e determinar o próximo passo.

Um alerta pode resultar em:

```text
Alerta
   ↓
Validação
   ↓
Contexto
   ↓
Classificação
   ↓
Prioridade
   ↓
Resposta
```

O objetivo não é investigar profundamente todos os alertas.

O objetivo é identificar rapidamente:

```text
O que aconteceu?
É real?
Qual é o risco?
Qual é o impacto?
Precisa de escalonamento?
```

---

# Alerta não é incidente

Essa distinção é fundamental.

Um **alerta** é uma sinalização gerada por uma regra, ferramenta ou mecanismo de segurança.

Um **incidente** é uma ocorrência confirmada ou altamente provável que representa uma violação ou ameaça à segurança.

Exemplo:

```text
Alerta:
Login de localização incomum
```

Isso não significa automaticamente:

```text
Conta comprometida
```

Pode ser:

```text
VPN
Viagem
Home Office
Proxy
```

Agora:

```text
Login incomum
+
novo dispositivo
+
MFA inesperado
+
download de dados
```

pode justificar a classificação como:

```text
Possível Account Compromise
```

A função da triagem é fazer essa separação.

---

# O primeiro objetivo: entender o alerta

Antes de abrir dezenas de ferramentas, entender o evento.

Perguntas iniciais:

```text
O que foi detectado?

Qual ferramenta gerou o alerta?

Quando aconteceu?

Qual usuário está envolvido?

Qual host?

Qual IP?

Qual domínio?

Qual processo?

Qual regra disparou?
```

Um alerta sem contexto pode parecer grave.

Depois de enriquecido, pode se revelar:

```text
Falso positivo
```

ou:

```text
Incidente real
```

---

# Estrutura básica de um alerta

Um alerta pode conter:

```text
Alert ID:
INC-2026-00041

Severity:
High

Time:
14:32:18

User:
usuario01

Host:
WS-023

Source IP:
185.x.x.x

Destination:
example.com

Detection:
Suspicious PowerShell Execution
```

O analista deve evitar olhar apenas para:

```text
Severity: High
```

A severidade ajuda a priorizar.

Ela não substitui investigação.

---

# Severidade x Prioridade

São conceitos diferentes.

### Severidade

Representa o potencial impacto ou risco associado ao evento.

Exemplo:

```text
Low
Medium
High
Critical
```

### Prioridade

Representa a urgência com que o SOC deve agir.

Uma ameaça de alta severidade em um servidor de teste pode ter prioridade menor que uma atividade suspeita em um Domain Controller.

Exemplo:

```text
High Severity
+
Lab Server
=
Prioridade moderada
```

Enquanto:

```text
Medium Severity
+
Domain Controller
=
Prioridade alta
```

O ativo e o contexto mudam a decisão.

---

# Critérios de prioridade

Uma forma simples de pensar:

```text
Prioridade =
Risco
+
Impacto
+
Confiança
+
Criticidade do ativo
```

Também considerar:

```text
Escopo
Urgência
Persistência
Privilégio
Sensibilidade dos dados
```

---

# Criticidade do ativo

Nem todo host possui o mesmo valor.

Exemplo:

```text
Workstation
Servidor Web
Servidor de Banco
Domain Controller
Firewall
SIEM
Backup Server
```

Um alerta semelhante pode possuir prioridades completamente diferentes.

Exemplo:

```text
PowerShell suspeito
```

em:

```text
WS-001
```

versus:

```text
DC-001
```

O segundo merece atenção imediata.

---

# Criticidade da identidade

O mesmo vale para usuários.

```text
Usuário comum
```

é diferente de:

```text
Administrador
```

ou:

```text
Domain Admin
```

Um alerta envolvendo uma conta privilegiada deve receber atenção especial.

---

# Confiança da detecção

Nem todo alerta possui o mesmo nível de confiança.

Exemplo:

```text
Regra:
IP suspeito detectado
```

pode gerar muitos falsos positivos.

Já:

```text
EDR:
Malware confirmado
+
processo executado
+
arquivo malicioso
```

possui uma confiança maior.

Uma boa triagem considera:

```text
Qual é a qualidade da evidência?
```

---

# Contexto é tudo

Considere:

```text
Evento:
PowerShell executado.
```

Isoladamente:

```text
Baixo contexto.
```

Agora:

```text
PowerShell
+
usuário desconhecido
+
script ofuscado
+
download externo
+
execução suspeita
```

O contexto muda completamente.

O analista deve buscar eventos relacionados antes de concluir.

---

# Timeline

Uma das ferramentas mais importantes na triagem é a timeline.

Exemplo:

```text
14:20
Login

14:22
PowerShell

14:23
Download

14:25
Execução

14:27
Conexão externa
```

Agora existe uma sequência.

Sem timeline:

```text
Eventos isolados
```

Com timeline:

```text
Possível cadeia de ataque
```

---

# Antes e depois do alerta

Não analisar apenas o evento que disparou.

Investigar:

```text
Antes
  ↓
Evento
  ↓
Depois
```

Exemplo:

```text
Antes:
Login anormal

Evento:
PowerShell

Depois:
Conexão externa
```

Isso pode revelar o verdadeiro significado do alerta.

---

# Escopo inicial

Durante a triagem:

```text
Esse evento aconteceu uma vez?
```

ou:

```text
Aconteceu em vários hosts?
```

Pesquisar:

```text
Mesmo IP
Mesmo domínio
Mesmo hash
Mesmo usuário
Mesmo processo
Mesmo comando
```

Se aparecer:

```text
1 host
```

pode ser um incidente isolado.

Se aparecer:

```text
25 hosts
```

pode existir uma campanha ou comprometimento amplo.

---

# Enriquecimento

O alerta inicial raramente contém todas as informações necessárias.

O analista pode adicionar:

```text
Threat Intelligence
WHOIS
DNS
Geolocalização
Reputação de IP
Reputação de domínio
EDR
Firewall
Proxy
Active Directory
Cloud Logs
```

Exemplo:

```text
IP suspeito
   ↓
Threat Intelligence
   ↓
Malicious
   ↓
Outros hosts?
```

Isso transforma um indicador isolado em contexto.

---

# IP suspeito

Imagine:

```text
Source IP:
185.x.x.x
```

A triagem deve perguntar:

```text
É interno ou externo?

O IP pertence à organização?

É VPN?

É provedor conhecido?

Foi observado anteriormente?

Possui reputação maliciosa?

Quais hosts falaram com ele?
```

Nunca classificar um IP como malicioso apenas porque:

```text
"parece estranho"
```

---

# Domínio suspeito

Para um domínio:

```text
example-suspicious.com
```

verificar:

```text
Data de criação
DNS
Nameservers
Certificado
Reputação
Histórico
Subdomínios
IPs associados
```

Também:

```text
Quais hosts acessaram?
Qual usuário?
Qual processo?
Quando?
```

---

# Hash suspeito

Se o alerta fornecer:

```text
SHA256:
abc123...
```

o analista pode pesquisar:

```text
Reputação
Detecções
Primeira observação
Família de malware
Hosts afetados
```

Mas um hash desconhecido não significa:

```text
Benigno
```

nem:

```text
Malicioso
```

Significa apenas:

```text
Sem contexto suficiente
```

---

# Usuário

O usuário pode ser um dos melhores elementos para contextualização.

Perguntas:

```text
É uma conta válida?

O usuário estava trabalhando?

O horário faz sentido?

O host pertence ao usuário?

O comportamento é comum?

A conta é privilegiada?

Houve autenticações recentes?
```

---

# Processo

Quando o alerta envolve execução:

```text
Processo
Parent Process
Command Line
Hash
Usuário
Host
```

são fundamentais.

Exemplo:

```text
winword.exe
   ↓
powershell.exe
   ↓
Download
```

Isso pode ser muito mais interessante do que simplesmente:

```text
PowerShell executado
```

---

# Command Line

A linha de comando pode revelar intenção.

Exemplo:

```text
powershell.exe -ExecutionPolicy Bypass ...
```

Não significa automaticamente malware.

Mas pode justificar investigação adicional.

Sempre considerar:

```text
Parent Process
Usuário
Arquivo
Destino
Contexto
```

---

# Triagem em camadas

Uma forma eficiente de trabalhar:

```text
Nível 1
↓
Validar alerta

Nível 2
↓
Enriquecer contexto

Nível 3
↓
Determinar risco

Nível 4
↓
Escalonar ou encerrar
```

O objetivo é evitar gastar 30 minutos em um alerta que poderia ser encerrado em 2 minutos.

---

# Primeira decisão

Após a análise inicial, normalmente existem três caminhos:

```text
Falso Positivo
```

```text
Benigno / Legítimo
```

ou:

```text
Suspeito / Incidente
```

Exemplo:

```text
Alerta
   ↓
Investigação
   ↓
Legítimo?
 ┌───────┐
Sim     Não
 ↓       ↓
Fechar   Escalar
```

---

# Falso positivo

Falso positivo acontece quando a regra identifica uma atividade que parece maliciosa, mas não representa uma ameaça real.

Exemplo:

```text
Backup
   ↓
Grande transferência
```

Uma regra de exfiltração pode disparar.

Mas:

```text
Destino conhecido
+
processo autorizado
+
horário esperado
```

pode indicar:

```text
Falso Positivo
```

---

# Benigno

Nem todo evento benigno precisa ser considerado um erro da ferramenta.

Exemplo:

```text
Administrador
   ↓
PowerShell
   ↓
Script autorizado
```

A detecção funcionou.

O comportamento apenas era legítimo.

Isso é diferente de:

```text
Regra mal configurada
```

---

# Quando o falso positivo é problema da regra

Imagine:

```text
Regra:
Alerta toda vez que PowerShell executar.
```

Em um ambiente corporativo:

```text
PowerShell
```

pode ser extremamente comum.

Resultado:

```text
1000 alertas
```

e:

```text
2 incidentes reais
```

Nesse caso, a regra precisa de melhoria.

---

# Redução de ruído

Uma boa regra deve utilizar contexto.

Em vez de:

```text
PowerShell executado
```

considerar:

```text
PowerShell
+
script ofuscado
+
download externo
+
parent process incomum
```

Isso reduz:

```text
Alert Fatigue
```

---

# Alert Fatigue

Quando o analista recebe alertas demais:

```text
1000 alertas
   ↓
500 relevantes
   ↓
100 importantes
   ↓
10 críticos
```

existe risco de:

```text
Cansaço
Atraso
Erros
Priorização ruim
Incidentes ignorados
```

Um SOC maduro trabalha continuamente para reduzir esse problema.

---

# Triagem e automação

Automação pode ajudar a enriquecer alertas.

Exemplo:

```text
Alerta
   ↓
SOAR
   ↓
Consulta IP
   ↓
Consulta domínio
   ↓
Consulta hash
   ↓
Consulta ativo
   ↓
Retorna contexto
```

O analista recebe:

```text
Alerta + Contexto
```

em vez de:

```text
Alerta isolado
```

---

# Exemplo de triagem automatizada

```text
IF alert_type = suspicious_ip

THEN

lookup reputation
lookup geo
lookup previous sightings
lookup affected_hosts
```

Resultado:

```text
IP:
185.x.x.x

Reputation:
Malicious

First Seen:
Today

Affected Hosts:
3

Users:
2
```

Agora a decisão pode ser muito mais rápida.

---

# SLA

A triagem deve respeitar o SLA definido pelo SOC.

Exemplo:

```text
Critical → imediato
High     → prioridade alta
Medium   → análise programada
Low      → fila normal
```

Os tempos reais dependem da organização.

O importante é:

> **O SLA transforma prioridade em tempo de resposta esperado.**

---

# Escalonamento

Nem todo alerta deve permanecer com o analista N1.

Escalonar quando:

```text
Existe evidência de comprometimento
+
Impacto potencial elevado
```

ou:

```text
A investigação exige conhecimento especializado.
```

Exemplo:

```text
N1
 ↓
Detecta possível malware
 ↓
N2
 ↓
Analisa comportamento
 ↓
Threat Hunter
 ↓
Investiga campanha
```

---

# Quando fechar um alerta

Pode ser apropriado fechar quando:

```text
Atividade legítima confirmada
```

ou:

```text
Falso positivo validado
```

ou:

```text
Sem evidências suficientes após investigação
```

Mas nunca simplesmente:

```text
"Não parece nada."
```

O fechamento precisa possuir justificativa.

---

# Evidência mínima

Uma boa anotação pode ser:

```text
"Login originado de VPN corporativa conhecida.
Usuário confirmado em atividade no período.
Dispositivo autorizado.
Nenhum comportamento anômalo identificado.
Alerta encerrado como atividade legítima."
```

Isso é muito melhor que:

```text
"FP."
```

---

# Registro da investigação

Cada alerta deve deixar uma trilha.

Exemplo:

```text
Alert ID:
INC-2026-0041

Analista:
SOC-N1

Data:
09/08/2026

Classificação:
Benigno

Motivo:
Atividade administrativa autorizada.

Evidências:
Host conhecido
Usuário validado
IP corporativo
Script autorizado

Ação:
Encerrado
```

Isso ajuda:

```text
Auditoria
Handoff
Métricas
Revisão
Lições aprendidas
```

---

# Handoff

Quando um alerta é escalado, o próximo analista não deveria começar do zero.

Um bom handoff contém:

```text
O que aconteceu?
Quando?
Qual ativo?
Qual usuário?
Quais evidências?
O que já foi investigado?
O que ainda falta?
Qual hipótese?
Qual prioridade?
```

Exemplo:

```text
Hipótese:
Possible Account Compromise

Evidências:
Novo IP
Novo dispositivo
MFA inesperado

Já verificado:
Reputação do IP
Logs de autenticação

Pendente:
Análise de sessão
Verificação de downloads
```

Isso economiza tempo.

---

# Caso prático

## Alerta

```text
Detection:
Suspicious Login

User:
usuario01

Source:
185.x.x.x

Time:
03:12
```

### Primeira análise

```text
Horário incomum
```

Ainda não é suficiente.

### Enriquecimento

```text
IP:
Não conhecido

Device:
Novo

MFA:
Aprovado

Location:
Incomum
```

Agora:

```text
Risco aumentado
```

### Próxima etapa

Pesquisar:

```text
Acesso a arquivos
E-mails
Aplicações
Sessões
Downloads
```

Encontrado:

```text
Download de 4 GB
```

Agora a hipótese passa a ser:

```text
Account Compromise
+
Possible Data Exfiltration
```

O alerta deve ser escalado.

---

# Matriz de decisão

Uma matriz simples:

| Evidência                      |   Risco |
| ------------------------------ | ------: |
| Evento isolado                 |   Baixo |
| Evento + comportamento anormal |   Médio |
| Múltiplos indicadores          |    Alto |
| Comprometimento confirmado     | Crítico |

Outro modelo:

```text
Baixo
↓
Monitorar

Médio
↓
Investigar

Alto
↓
Escalonar

Crítico
↓
Conter + Escalonar
```

---

# Triagem de Malware

Exemplo:

```text
EDR
↓
Malware detected
```

Perguntas:

```text
Arquivo?
Hash?
Host?
Usuário?
Processo?
Parent Process?
Persistência?
Conexões?
```

Depois:

```text
Outros hosts possuem o mesmo hash?
```

---

# Triagem de Phishing

Exemplo:

```text
E-mail suspeito
```

Verificar:

```text
Remetente
Domínio
URL
Anexos
SPF
DKIM
DMARC
Reputação
Quantidade de destinatários
```

Depois:

```text
Alguém clicou?
Alguém enviou credenciais?
Houve download?
```

---

# Triagem de Brute Force

Verificar:

```text
Quantidade de falhas
Origem
Destino
Usuário
Janela de tempo
Sucesso posterior
```

Exemplo:

```text
50 falhas
   ↓
1 sucesso
```

é muito mais preocupante que:

```text
2 falhas
```

---

# Triagem de Exfiltração

Verificar:

```text
Volume
Destino
Usuário
Host
Processo
Dados
Horário
Histórico
```

Exemplo:

```text
Backup conhecido
+
destino conhecido
+
horário esperado
```

pode ser legítimo.

Já:

```text
Novo destino
+
usuário incomum
+
dados sensíveis
+
transferência em massa
```

merece alta prioridade.

---

# Threat Intelligence na triagem

Threat Intelligence pode responder rapidamente:

```text
Esse IP já foi associado a ataques?

Esse domínio possui histórico malicioso?

Esse hash pertence a uma família conhecida?

Esse indicador foi observado recentemente?
```

Mas inteligência externa não deve substituir contexto interno.

O cenário ideal:

```text
Threat Intelligence
+
Telemetria interna
=
Melhor decisão
```

---

# MITRE ATT&CK

O mapeamento para MITRE ATT&CK pode ajudar a entender o comportamento.

Exemplo:

```text
Alerta:
PowerShell suspeito
```

Pode estar relacionado a:

```text
T1059.001
PowerShell
```

Outro:

```text
Login com credencial válida
```

pode estar relacionado a:

```text
T1078
Valid Accounts
```

O objetivo não é decorar técnicas.

É entender:

```text
O que o atacante está tentando fazer?
```

---

# Perguntas que o analista deve fazer

Durante a triagem:

```text
O que aconteceu?

Quando aconteceu?

Quem está envolvido?

Qual ativo?

Isso é esperado?

Existe contexto legítimo?

Existe evidência de comprometimento?

Qual é o impacto potencial?

Esse comportamento ocorreu antes?

Existem outros ativos afetados?

Qual é a próxima ação?
```

---

# O erro de investigar demais cedo demais

Um problema comum é começar uma investigação completa antes de validar o alerta.

Exemplo:

```text
Alerta
↓
Abrir 15 ferramentas
↓
Consultar 30 fontes
↓
Pesquisar durante 40 minutos
↓
Descobrir que era backup
```

Uma abordagem melhor:

```text
Alerta
↓
Validar contexto
↓
Enriquecer
↓
Decidir
```

Se houver evidências:

```text
Escalar para investigação profunda.
```

---

# O erro de investigar de menos

O extremo oposto também é perigoso.

```text
Alerta:
Login suspeito

Analista:
"Usuário deve estar viajando."

Fechado.
```

Sem validar:

```text
VPN
IP
Device
MFA
Atividade posterior
```

Isso pode transformar um incidente real em falso positivo.

---

# Regra prática

Uma boa triagem busca equilíbrio:

```text
Rápido
+
Contextualizado
+
Baseado em evidências
```

Não é:

```text
Rápido demais
```

nem:

```text
Investigação completa para todo alerta.
```

---

# Checklist de Triagem

```text
[ ] Identifiquei o alerta?

[ ] Entendi o que disparou a regra?

[ ] Verifiquei horário?

[ ] Identifiquei usuário?

[ ] Identifiquei host?

[ ] Identifiquei IP?

[ ] Identifiquei domínio?

[ ] Identifiquei processo?

[ ] Analisei a timeline?

[ ] Verifiquei eventos anteriores?

[ ] Verifiquei eventos posteriores?

[ ] Consultei reputação quando necessário?

[ ] Verifiquei se existe contexto legítimo?

[ ] Pesquisei outros hosts?

[ ] Pesquisei outros usuários?

[ ] Avaliei criticidade do ativo?

[ ] Avaliei criticidade da identidade?

[ ] Determinei severidade?

[ ] Determinei prioridade?

[ ] Classifiquei o alerta?

[ ] Registrei as evidências?

[ ] Precisa de escalonamento?

[ ] Existe alguma ação imediata?
```

---

# Métricas de Triagem

O SOC pode acompanhar:

```text
MTTA
Mean Time to Acknowledge

MTTR
Mean Time to Respond/Resolve

Alert Volume
Quantidade de alertas

False Positive Rate
Taxa de falsos positivos

Escalation Rate
Taxa de escalonamento

Closure Rate
Taxa de encerramento
```

Exemplo:

```text
10.000 alertas
      ↓
6.000 falsos positivos
      ↓
3.500 benignos
      ↓
450 suspeitos
      ↓
50 incidentes
```

Esses números ajudam a identificar problemas de qualidade nas detecções.

---

# Melhorando a detecção através da triagem

A triagem também alimenta o ciclo de melhoria.

```text
Detecção
   ↓
Triagem
   ↓
Investigação
   ↓
Resultado
   ↓
Feedback
   ↓
Nova regra
```

Exemplo:

```text
100 alertas de PowerShell
      ↓
90 legítimos
      ↓
10 suspeitos
```

O SOC pode analisar os 90 legítimos e ajustar a regra.

Resultado:

```text
Menos ruído
+
Mais contexto
+
Melhor prioridade
```

---

# Triagem não é apenas fechar alertas

Esse é um ponto importante.

Um bom analista não mede seu trabalho apenas por:

```text
"Quantos alertas eu fechei?"
```

Também deve considerar:

```text
Quantos incidentes foram identificados?

Quanto tempo levou?

Quantos falsos positivos foram reduzidos?

Quantos padrões novos foram encontrados?

Quantas melhorias foram geradas?
```

O valor da triagem está na **qualidade das decisões**.

---

# Conclusão

Triagem é o ponto onde o SOC começa a transformar telemetria em resposta.

O processo pode ser resumido em:

```text
ALERTA
  ↓
VALIDAR
  ↓
CONTEXTUALIZAR
  ↓
ENRIQUECER
  ↓
PRIORIZAR
  ↓
CLASSIFICAR
  ↓
ESCALAR OU ENCERRAR
```

Um alerta isolado é apenas um sinal.

O trabalho do analista é descobrir se existe uma história por trás dele.

A pergunta mais importante durante uma triagem não é:

> **"A ferramenta disse que isso é suspeito?"**

É:

> **"Quais evidências sustentam essa hipótese?"**

Essa mudança de mentalidade transforma o analista de alguém que apenas reage a notificações em alguém capaz de **investigar, priorizar risco e tomar decisões baseadas em evidências**.
