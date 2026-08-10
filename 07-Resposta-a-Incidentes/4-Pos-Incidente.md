# Pós-Incidente

## Introdução

O incidente terminou.

O sistema foi recuperado, os serviços voltaram a funcionar e a ameaça foi removida.

Mas ainda existe uma pergunta importante:

> **O que precisa mudar para que isso não aconteça novamente?**

É nesse momento que começa o trabalho de pós-incidente.

O objetivo não é procurar culpados.

É transformar o incidente em:

```text
Evidência
   ↓
Aprendizado
   ↓
Melhoria
   ↓
Prevenção
```

Um SOC maduro não mede seu trabalho apenas pela quantidade de incidentes encerrados.

Ele também mede o quanto aprende com eles.

---

# O que é o pós-incidente?

O pós-incidente é a etapa realizada após a contenção, erradicação e recuperação.

Nela são analisados:

```text
Causa raiz
Linha do tempo
Ações realizadas
Evidências
Impacto
Falhas de segurança
Falhas de processo
Tempo de resposta
Efetividade das detecções
```

O resultado deve gerar ações concretas.

---

# O incidente realmente terminou?

Antes de iniciar o encerramento:

```text
[ ] A ameaça foi removida?
[ ] Os sistemas foram recuperados?
[ ] Não existem novos indicadores?
[ ] Persistência foi eliminada?
[ ] Credenciais foram protegidas?
[ ] Vulnerabilidades foram corrigidas?
[ ] Monitoramento está ativo?
```

Se ainda existir atividade suspeita:

```text
Incidente
   ↓
Investigação
```

O pós-incidente começa quando existe evidência suficiente de que a ameaça está controlada.

---

# Linha do tempo

Uma das entregas mais importantes do pós-incidente é a timeline.

Exemplo:

```text
08:42
Primeiro acesso suspeito

08:47
Execução de processo incomum

08:53
Conexão com domínio malicioso

09:01
SOC recebeu alerta

09:08
Analista iniciou triagem

09:17
Endpoint isolado

09:35
Conta comprometida identificada

10:20
Persistência removida

11:10
Sistema reconstruído

12:30
Serviço recuperado

13:00
Monitoramento reforçado
```

A timeline permite enxergar o incidente como uma sequência de eventos em vez de acontecimentos isolados.

---

# Por que a timeline importa?

Ela ajuda a responder:

```text
Quando começou?
Quando foi detectado?
Quando foi analisado?
Quando foi contido?
Quanto tempo ficou ativo?
```

Também permite identificar atrasos.

Exemplo:

```text
Ataque
  ↓
2h
  ↓
Detecção
  ↓
40min
  ↓
Contenção
```

Talvez o maior problema não tenha sido a tecnologia.

Talvez tenha sido o tempo de resposta.

---

# MTTD

MTTD significa:

> **Mean Time To Detect**

É o tempo médio necessário para detectar um incidente.

Exemplo:

```text
Ataque:
10:00

Detecção:
10:15

MTTD:
15 minutos
```

Quanto menor o MTTD, mais cedo a organização percebe que algo está errado.

---

# MTTR

MTTR pode representar:

> **Mean Time To Respond / Recover**

Dependendo da metodologia adotada pela organização, o significado exato pode variar.

No contexto operacional do SOC, o indicador é utilizado para acompanhar o tempo necessário para responder ou recuperar um incidente.

Exemplo:

```text
Detecção:
10:15

Contenção:
10:30

Recuperação:
12:00
```

O tempo deve ser analisado de acordo com o processo definido pela organização.

---

# O que aconteceu?

Uma análise pós-incidente deve reconstruir o ataque.

Exemplo:

```text
Phishing
   ↓
Credencial roubada
   ↓
Login suspeito
   ↓
Privilégio elevado
   ↓
Execução de malware
   ↓
C2
   ↓
Exfiltração
```

Essa visão é mais útil do que simplesmente registrar:

```text
"Malware detectado."
```

---

# Causa raiz

A pergunta mais importante é:

> **Por que o incidente foi possível?**

Exemplo:

```text
Phishing
   ↓
Usuário clicou
   ↓
Credencial foi enviada
   ↓
MFA não estava habilitado
```

A causa não é simplesmente:

```text
"Usuário clicou."
```

O cenário pode revelar uma combinação de fatores:

```text
Credencial exposta
+
Ausência de MFA
+
Treinamento insuficiente
+
Detecção tardia
```

---

# Não procurar culpados

Uma análise madura não deve transformar o pós-incidente em uma caça ao responsável.

O objetivo é descobrir:

```text
O que falhou?
Por que falhou?
Como melhorar?
```

Em vez de:

```text
Quem errou?
```

Isso favorece uma cultura de segurança em que problemas são reportados antes que se transformem em incidentes maiores.

---

# Root Cause Analysis

A análise da causa raiz pode utilizar perguntas simples:

```text
O que aconteceu?

Como aconteceu?

Por que aconteceu?

Por que não foi detectado antes?

Por que os controles existentes não impediram?

O que precisa mudar?
```

---

# Five Whys

Uma técnica simples é o:

> **5 Whys**

Exemplo:

```text
Por que o servidor foi comprometido?
→ Aplicação vulnerável.

Por que estava vulnerável?
→ Não recebeu atualização.

Por que não recebeu atualização?
→ Não estava no inventário de patch.

Por que não estava no inventário?
→ Processo de gestão de ativos incompleto.

Por que o processo era incompleto?
→ Não existia responsável definido.
```

A causa final pode estar muito distante do primeiro sintoma observado.

---

# Falhas técnicas

Registrar problemas técnicos encontrados.

Exemplos:

```text
Sistema sem patch
Firewall mal configurado
MFA ausente
Logs insuficientes
EDR desativado
Privilégio excessivo
Backup inadequado
Segmentação inexistente
```

Cada falha deve gerar uma ação quando aplicável.

---

# Falhas de processo

Nem todo problema é técnico.

Exemplos:

```text
Alerta sem responsável
Escalonamento indefinido
Documentação desatualizada
Backup sem teste
Inventário incompleto
Playbook inexistente
Comunicação lenta
```

Um SOC pode ter ferramentas excelentes e ainda responder mal por falta de processos.

---

# Falhas de detecção

Pergunta importante:

> **O SOC deveria ter detectado isso antes?**

Exemplo:

```text
Atacante
   ↓
Login anômalo
   ↓
PowerShell
   ↓
C2
   ↓
Exfiltração
```

Se o alerta só apareceu na exfiltração, talvez existam oportunidades de detecção anteriores.

---

# Melhorando regras de detecção

O incidente pode gerar uma nova regra.

Exemplo:

```text
Incidente:
Uso suspeito de PowerShell

Ação:
Criar regra de detecção

Depois:
Executar teste

Resultado:
Alerta gerado
```

O incidente passa a melhorar diretamente o SOC.

---

# IOC para inteligência

Os indicadores encontrados podem alimentar processos de segurança:

```text
IP
Domínio
Hash
URL
E-mail
Arquivo
User-Agent
```

Exemplo:

```text
IOC
   ↓
Validação
   ↓
SIEM
   ↓
EDR
   ↓
Firewall
   ↓
DNS
```

Mas IOC não deve ser tratado como verdade permanente.

Indicadores mudam.

O contexto continua sendo essencial.

---

# Atualização do SIEM

Depois de um incidente, revisar:

```text
Logs disponíveis
Fontes de logs
Parsing
Correlação
Retenção
Alertas
Dashboards
```

Pergunta importante:

> **Tínhamos os dados necessários para investigar o incidente?**

Se a resposta for não, existe uma melhoria clara.

---

# Exemplo de melhoria

Durante a investigação:

```text
Precisávamos de:
DNS Logs
```

Mas:

```text
DNS Logs:
Não coletados
```

Ação:

```text
Configurar coleta
   ↓
Normalizar
   ↓
Enviar ao SIEM
   ↓
Criar regra
   ↓
Testar
```

Esse é um resultado concreto do pós-incidente.

---

# Atualização de Playbooks

Se o SOC demorou porque não sabia qual ação executar:

```text
Problema:
Processo indefinido
```

Ação:

```text
Criar / Atualizar Playbook
```

Exemplo:

```text
Phishing
   ↓
Triagem
   ↓
Validar URL
   ↓
Investigar usuário
   ↓
Revogar sessão
   ↓
Resetar credencial
   ↓
Hunting
   ↓
Encerrar
```

---

# O que funcionou?

Nem toda análise deve procurar apenas problemas.

Registrar também:

```text
Detecção rápida
Automação eficiente
Boa comunicação
Backup funcional
Resposta rápida
Boa visibilidade
Playbook eficiente
```

Isso ajuda a identificar controles que devem ser mantidos ou ampliados.

---

# O que não funcionou?

Registrar:

```text
Alerta atrasado
Log ausente
Regra muito ruidosa
Escalonamento lento
Backup não testado
Documentação desatualizada
```

Cada problema deve ser convertido em uma melhoria quando possível.

---

# Lessons Learned

Uma seção simples pode registrar:

```text
O que aprendemos?

O que devemos manter?

O que devemos mudar?

O que devemos automatizar?

O que devemos monitorar melhor?
```

Exemplo:

```text
Aprendizado:
Credenciais comprometidas foram utilizadas
antes da detecção.

Melhoria:
Implementar detecção de login anômalo
e reforçar MFA.
```

---

# Plano de ação

O pós-incidente só tem valor se produzir mudanças.

Exemplo:

| Problema           | Ação                    | Prioridade | Responsável | Prazo   |
| ------------------ | ----------------------- | ---------- | ----------- | ------- |
| MFA ausente        | Habilitar MFA           | Alta       | IAM         | 7 dias  |
| Logs insuficientes | Adicionar coleta        | Alta       | SOC         | 14 dias |
| Regra inexistente  | Criar detecção          | Alta       | Detection   | 5 dias  |
| Backup não testado | Criar rotina de restore | Média      | Infra       | 30 dias |

A tabela transforma aprendizado em execução.

---

# Priorização das melhorias

Nem toda melhoria possui a mesma urgência.

Uma forma simples:

```text
Crítica
Alta
Média
Baixa
```

Ou:

```text
Impacto × Probabilidade
```

Exemplo:

```text
MFA ausente
Impacto: Alto
Probabilidade: Alta

Prioridade:
Crítica
```

---

# Reteste

Depois de implementar uma correção:

```text
Corrigir
   ↓
Testar
   ↓
Simular
   ↓
Validar
```

Exemplo:

```text
Nova regra de detecção
       ↓
Simular ataque
       ↓
SIEM recebe evento
       ↓
Regra dispara
       ↓
Alerta gerado
```

Uma regra não deve ser considerada concluída apenas porque foi criada.

---

# Purple Team

Algumas melhorias podem ser validadas através de exercícios controlados.

Exemplo:

```text
Red Team
   ↓
Simulação
   ↓
Blue Team
   ↓
Detecção
   ↓
Resposta
```

O objetivo é descobrir se:

```text
Detecção
+
Investigação
+
Resposta
```

funcionam na prática.

---

# Atualização da documentação

O incidente pode revelar que a documentação estava incompleta.

Atualizar:

```text
Playbooks
Procedimentos
Diagramas
Contatos
Inventário
Runbooks
Regras
```

Documentação de segurança deve acompanhar o ambiente real.

---

# Comunicação pós-incidente

Dependendo do incidente, diferentes áreas podem precisar receber informações.

Exemplos:

```text
SOC
Infraestrutura
TI
DevOps
Cloud
Gestão
Jurídico
Compliance
DPO
```

A comunicação deve respeitar:

```text
Necessidade
Confidencialidade
Políticas internas
Requisitos legais
```

---

# Evidências

As evidências do incidente devem ser preservadas conforme os procedimentos da organização.

Exemplos:

```text
Logs
Alertas
Screenshots
Hashes
Arquivos
Timeline
Tickets
Comandos executados
Resultados de investigação
```

Registrar:

```text
Quando?
Quem?
Onde?
O quê?
Como foi obtido?
```

---

# Documentação do incidente

Um relatório pode seguir:

```text
1. Resumo Executivo
2. Data e Hora
3. Sistemas Afetados
4. Detecção
5. Escopo
6. Timeline
7. Indicadores
8. Impacto
9. Contenção
10. Erradicação
11. Recuperação
12. Causa Raiz
13. Lições Aprendidas
14. Plano de Ação
```

---

# Resumo executivo

O relatório técnico pode ser detalhado.

Mas gestores normalmente precisam de uma visão objetiva:

```text
O que aconteceu?
Qual foi o impacto?
O incidente foi contido?
O ambiente foi recuperado?
Existe risco residual?
O que será feito?
```

Exemplo:

```text
Foi identificado comprometimento de uma estação
após execução de arquivo malicioso recebido por e-mail.

O endpoint foi isolado e reconstruído.

Não foram identificadas evidências de movimentação lateral.

Como medida preventiva, foram implementadas novas
regras de detecção e reforçado o controle de execução.
```

---

# Impacto

Avaliar:

```text
Confidencialidade
Integridade
Disponibilidade
```

### Confidencialidade

```text
Dados foram acessados?
```

### Integridade

```text
Dados foram alterados?
```

### Disponibilidade

```text
Serviços ficaram indisponíveis?
```

---

# Classificação do impacto

Pode ser documentado como:

```text
Baixo
Médio
Alto
Crítico
```

Considerando:

```text
Quantidade de sistemas
Dados envolvidos
Tempo de indisponibilidade
Impacto financeiro
Impacto operacional
Impacto reputacional
Requisitos regulatórios
```

---

# Métricas do incidente

Alguns indicadores úteis:

```text
MTTD
MTTR
Tempo de contenção
Tempo de erradicação
Tempo de recuperação
Quantidade de hosts afetados
Quantidade de usuários afetados
Quantidade de alertas
Quantidade de falsos positivos
```

---

# Exemplo de métricas

```text
Incidente:
Phishing

MTTD:
12 minutos

Contenção:
18 minutos

Erradicação:
45 minutos

Recuperação:
1h20

Hosts afetados:
2

Contas comprometidas:
1
```

Esses números permitem comparar desempenho ao longo do tempo.

---

# Indicadores de melhoria

Após alguns incidentes, o SOC pode comparar:

```text
Antes
↓
MTTD: 30 min

Depois
↓
MTTD: 12 min
```

Isso demonstra evolução operacional.

O objetivo não é apenas produzir métricas.

É utilizar métricas para tomar decisões.

---

# Incidente como fonte de melhoria

Um incidente pode gerar:

```text
Nova regra
+
Novo playbook
+
Novo IOC
+
Novo controle
+
Novo treinamento
+
Nova configuração
```

Por isso:

> **Cada incidente deveria deixar o ambiente um pouco mais difícil de comprometer novamente.**

---

# Atualização do Threat Model

Se o incidente revelou um caminho de ataque que não estava sendo considerado:

```text
Threat Model
   ↓
Atualização
   ↓
Novo risco
   ↓
Novo controle
```

Exemplo:

```text
Antes:
Phishing não considerado vetor crítico.

Depois:
Phishing → Credential Theft → Cloud Access
```

O modelo de ameaça precisa acompanhar a realidade observada.

---

# Atualização do MITRE ATT&CK

As técnicas observadas durante o incidente podem ser mapeadas novamente.

Exemplo:

```text
Initial Access
     ↓
Execution
     ↓
Persistence
     ↓
Credential Access
     ↓
Command and Control
```

Isso ajuda a identificar:

```text
Quais técnicas foram observadas?
Quais foram detectadas?
Quais passaram despercebidas?
```

---

# Gap Analysis

Uma análise simples:

```text
Controle esperado
        ↓
Controle existente
        ↓
Diferença
        ↓
Plano de melhoria
```

Exemplo:

```text
Esperado:
MFA

Existente:
MFA apenas para administradores

Gap:
Usuários comuns sem MFA

Ação:
Expandir MFA
```

---

# Atualização das regras

Depois do incidente:

```text
Regra antiga
     ↓
Análise
     ↓
Melhoria
     ↓
Nova regra
     ↓
Teste
```

Mas cuidado:

Uma regra extremamente sensível pode gerar tantos alertas que acaba prejudicando o SOC.

A melhoria precisa considerar:

```text
Precisão
Cobertura
Contexto
Volume
```

---

# Falsos positivos

O pós-incidente também é um bom momento para revisar alertas.

Perguntar:

```text
Esse alerta ajudou?

Gerou ruído?

Faltou contexto?

Deveria ter prioridade maior?

Deveria ser automatizado?
```

O objetivo não é ter mais alertas.

É ter **alertas úteis**.

---

# Automação

Se uma ação foi repetida várias vezes durante o incidente:

```text
Isolar endpoint
Bloquear IP
Desabilitar conta
Adicionar IOC
```

pode existir oportunidade de automação.

Exemplo:

```text
Alerta crítico
   ↓
SOAR
   ↓
Isolar endpoint
   ↓
Abrir incidente
   ↓
Notificar SOC
```

Automação deve reduzir tempo sem remover controles necessários.

---

# Revisão de acessos

Depois de um incidente envolvendo credenciais:

```text
IAM
 ↓
Usuários
 ↓
Grupos
 ↓
Permissões
 ↓
Tokens
 ↓
Service Accounts
```

Revisar principalmente:

```text
Privilégios excessivos
Contas antigas
Contas sem MFA
Contas compartilhadas
Credenciais nunca rotacionadas
```

---

# Revisão de backups

Depois de um ransomware:

```text
Backup
 ↓
Restore
 ↓
Validação
```

Se o processo apresentou problemas:

```text
Melhoria
 ↓
Novo teste
 ↓
Novo restore
```

Backup só pode ser considerado parte confiável da estratégia quando sua recuperação é conhecida e testada.

---

# Treinamento

Alguns incidentes revelam necessidade de capacitação.

Exemplo:

```text
Phishing
 ↓
Usuários reportaram tarde
```

Ação:

```text
Treinamento
+
Simulação
+
Canal de reporte
```

Treinamento não deve ser usado para substituir controles técnicos.

Deve complementar a arquitetura de segurança.

---

# Encerramento formal

O incidente pode ser encerrado quando:

```text
[✓] Ameaça controlada
[✓] Sistemas recuperados
[✓] Investigação concluída
[✓] Evidências preservadas
[✓] Impacto avaliado
[✓] Causa raiz identificada
[✓] Melhorias definidas
[✓] Responsáveis definidos
[✓] Documentação concluída
```

---

# O incidente terminou. O trabalho não.

Essa é uma das ideias mais importantes do pós-incidente.

O alerta pode ser fechado.

O ticket pode ser encerrado.

O sistema pode estar funcionando.

Mas ainda existe valor em perguntar:

```text
O que aprendemos?
```

E principalmente:

```text
O que vamos mudar?
```

---

# Exemplo de Pós-Incidente

## Cenário

```text
Usuário recebeu e-mail de phishing.

Credencial foi comprometida.

Atacante realizou login.

Conta foi utilizada para acessar recursos internos.
```

### Detecção

```text
Login anômalo
```

### Contenção

```text
Conta desabilitada
Sessões revogadas
```

### Erradicação

```text
Senha alterada
Tokens revogados
MFA configurado
```

### Recuperação

```text
Conta restaurada
Acesso validado
Monitoramento reforçado
```

### Pós-Incidente

```text
Causa:
Credencial comprometida + ausência de MFA.

Falha de detecção:
Login inicial não gerou alerta.

Ações:
Implementar MFA
Criar regra de login anômalo
Atualizar playbook
Treinar usuários
```

Resultado:

```text
Incidente
   ↓
Análise
   ↓
Melhoria
   ↓
Controle
   ↓
Maior maturidade
```

---

# Checklist de Pós-Incidente

```text
[ ] Incidente oficialmente encerrado?

[ ] Timeline criada?

[ ] Evidências preservadas?

[ ] Escopo documentado?

[ ] Impacto documentado?

[ ] Causa raiz identificada?

[ ] Falhas técnicas identificadas?

[ ] Falhas de processo identificadas?

[ ] Detecções avaliadas?

[ ] IOCs documentados?

[ ] MITRE ATT&CK atualizado?

[ ] Playbooks revisados?

[ ] Regras de detecção revisadas?

[ ] SIEM revisado?

[ ] Backups avaliados?

[ ] Controles de acesso revisados?

[ ] Threat Model atualizado?

[ ] Gap Analysis realizado?

[ ] Lições aprendidas registradas?

[ ] Plano de ação criado?

[ ] Responsáveis definidos?

[ ] Prazos definidos?

[ ] Melhorias testadas?

[ ] Documentação atualizada?

[ ] Métricas registradas?

[ ] Relatório final concluído?
```

---

# Fluxo Pós-Incidente

```text
                    INCIDENTE ENCERRADO
                           │
                           ▼
                    Reconstruir Timeline
                           │
                           ▼
                    Analisar Causa Raiz
                           │
                           ▼
                     Avaliar Impacto
                           │
                           ▼
                   Identificar Gaps
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
        Detecção        Processo       Tecnologia
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                  Lições Aprendidas
                           │
                           ▼
                    Plano de Ação
                           │
                           ▼
                  Implementar Melhorias
                           │
                           ▼
                       Retestar
                           │
                           ▼
                    Atualizar SOC
```

---

# O objetivo final

O pós-incidente não deve produzir apenas um relatório.

Deve produzir mudanças.

```text
Incidente
    ↓
Evidências
    ↓
Investigação
    ↓
Causa
    ↓
Gap
    ↓
Melhoria
    ↓
Teste
    ↓
Novo controle
```

Se o mesmo ataque acontecer novamente, o SOC deveria estar:

```text
Mais rápido
Mais preparado
Mais visível
Mais automatizado
Mais preciso
```

Esse é o verdadeiro indicador de maturidade.

> **Um SOC maduro não é aquele que nunca sofre incidentes. É aquele que aprende com cada incidente e transforma esse aprendizado em capacidade defensiva.**
