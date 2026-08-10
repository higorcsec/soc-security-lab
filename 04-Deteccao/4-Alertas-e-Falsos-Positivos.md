# Alertas e Falsos Positivos

## Introdução

Um SOC pode ter as melhores ferramentas de segurança disponíveis e ainda assim falhar se não conseguir lidar com seus próprios alertas.

O problema não é apenas detectar ameaças.

É conseguir separar:

```text id="h7qv4d"
Evento
  ↓
Alerta
  ↓
Comportamento relevante?
  ↓
Investigação
```

Em um ambiente real, nem todo alerta representa um ataque.

Serviços legítimos podem executar comandos administrativos.

Usuários podem acessar recursos fora do padrão.

Sistemas podem gerar grandes quantidades de eventos.

Ferramentas de segurança podem interpretar comportamentos legítimos como suspeitos.

É nesse cenário que aparecem os **falsos positivos**.

---

# O que é um alerta?

Um alerta é uma notificação gerada quando determinada condição de detecção é atendida.

Por exemplo:

```text id="8k1vde"
Regra:

Mais de 10 falhas de login
em 5 minutos

        ↓

Condição atingida

        ↓

ALERTA
```

O alerta não significa necessariamente que um ataque aconteceu.

Ele significa:

> **"Algo aconteceu que corresponde ao comportamento que decidimos monitorar."**

Essa diferença é fundamental.

O alerta inicia a investigação.

Ele não encerra a investigação.

---

# O que é um falso positivo?

Um **falso positivo** acontece quando uma regra identifica uma atividade como suspeita, mas, após análise, ela é considerada legítima ou não representa uma ameaça.

Exemplo:

```text id="t3hm8j"
Regra:
Múltiplos logins falhos

        ↓

Alerta

        ↓

Analista investiga

        ↓

Usuário esqueceu a senha

        ↓

Falso positivo
```

O evento aconteceu.

A regra funcionou.

O problema foi a interpretação do comportamento.

Isso é importante porque um falso positivo não significa necessariamente que a regra está "errada".

Pode significar que ela precisa de **mais contexto**.

---

# Falso positivo x falso negativo

Os dois conceitos são importantes para entender uma operação de detecção.

### Falso positivo

A regra alerta, mas não existe ameaça relevante.

```text id="89ot5q"
Atividade legítima
      ↓
Alerta
      ↓
Falso positivo
```

### Falso negativo

Existe uma atividade maliciosa, mas a regra não detecta.

```text id="a1mtlq"
Ataque
      ↓
Sem alerta
      ↓
Falso negativo
```

Podemos representar:

```text id="c6v1dl"
                  Realidade

              Ameaça    Legítimo

Alerta        TP        FP

Sem alerta    FN        TN
```

Onde:

```text id="r0y9sx"
TP = True Positive
FP = False Positive
FN = False Negative
TN = True Negative
```

Em segurança, ambos são problemas.

Mas eles possuem impactos diferentes.

---

# O custo do falso positivo

Imagine um SOC recebendo:

```text id="2xw1u8"
2.000 alertas por dia
```

Se:

```text id="h7r6u3"
90% = falso positivo
```

o analista precisa filtrar:

```text id="v1p5v8"
2.000
 ↓
1.800 eventos sem relevância
 ↓
200 alertas para análise
```

Isso consome tempo e recursos.

Pior:

um alerta realmente importante pode estar no meio desse volume.

Esse fenômeno é conhecido como **alert fatigue**.

---

# Alert Fatigue

Alert fatigue acontece quando o excesso de alertas reduz a capacidade do analista de perceber eventos realmente importantes.

Um cenário comum:

```text id="y0z0la"
Alerta
Alerta
Alerta
Alerta
Alerta
Alerta
Alerta
Alerta
Alerta
Alerta
```

Depois de centenas de eventos semelhantes, o analista pode começar a tratá-los mecanicamente.

Isso cria um risco operacional:

> **quanto mais ruído existe, maior a chance de um sinal importante ser ignorado.**

Por isso, reduzir ruído não é apenas uma questão de produtividade.

Também é uma questão de segurança.

---

# Um alerta não deve ser avaliado isoladamente

Considere:

```text id="7d5f2q"
PowerShell executado
```

Isso pode ser normal.

Agora:

```text id="1e7k2p"
PowerShell
+
EncodedCommand
+
WINWORD.EXE
+
Download externo
```

A situação é diferente.

O analista precisa adicionar contexto.

Alguns pontos importantes:

```text id="w8s6f1"
Quem?
 ↓
Qual usuário?

Onde?
 ↓
Qual host?

Quando?
 ↓
Qual horário?

O quê?
 ↓
Qual processo?

Como?
 ↓
Qual comando?

Para onde?
 ↓
Qual destino?

Por quê?
 ↓
Existe justificativa?
```

Esse contexto ajuda a transformar um alerta genérico em uma decisão.

---

# Processo de triagem

Uma triagem simples pode seguir:

```text id="3z6f0q"
Alerta recebido
      ↓
Validar evento
      ↓
Identificar ativo
      ↓
Identificar usuário
      ↓
Analisar contexto
      ↓
Correlacionar eventos
      ↓
Consultar Threat Intelligence
      ↓
Classificar
      ↓
Encerrar ou escalar
```

O objetivo da triagem é responder:

> **"Isso precisa continuar sendo investigado?"**

---

# Classificação de alertas

Uma classificação simples:

### Informativo

Atividade esperada ou de baixo risco.

```text id="8d9s0u"
Login administrativo autorizado
```

### Suspeito

Existe comportamento fora do padrão, mas ainda não há evidência suficiente.

```text id="l9p7q0"
Login fora do horário
```

### Malicioso

Existem evidências suficientes para indicar atividade maliciosa.

```text id="e1v9c2"
Execução de malware confirmada
```

### Incidente

A atividade maliciosa possui impacto ou escopo que exige resposta formal.

```text id="q6x4mj"
Servidor comprometido
+
credenciais expostas
+
movimentação lateral
```

---

# Exemplo prático: múltiplos logins

Imagine a seguinte regra:

```text id="8r5m2k"
Mais de 10 falhas de login
em 5 minutos
```

O alerta aparece:

```text id="5m4x0n"
Usuário: suporte
Falhas: 35
```

A primeira hipótese pode ser:

```text id="y6d3m1"
Brute Force
```

Mas o analista precisa investigar.

### Pergunta 1

De onde vieram as tentativas?

```text id="4c3v8x"
Mesmo IP?
Vários IPs?
Rede interna?
Internet?
```

### Pergunta 2

Qual foi o resultado?

```text id="6v8t2a"
Todas falharam?
Houve login bem-sucedido?
```

### Pergunta 3

O usuário reconhece a atividade?

```text id="0d1z7k"
Usuário esqueceu a senha?
Aplicação está utilizando senha antiga?
```

### Pergunta 4

Existe padrão semelhante?

```text id="m8f1ce"
Outras contas
+
mesmo IP
+
mesmo período
```

Agora a hipótese de ataque ganha força.

---

# Quando um falso positivo revela um problema

Nem sempre um falso positivo deve simplesmente ser ignorado.

Imagine:

```text id="qv7z4n"
Regra:
Login administrativo fora do horário

Resultado:
Falso positivo
```

Após investigar:

```text id="f0d2ca"
Usuário:
Administrador

Motivo:
Rotina automatizada de backup
```

O problema não está necessariamente na regra.

Pode estar na falta de contexto sobre a infraestrutura.

Talvez o SOC precise conhecer:

```text id="a4r2s7"
Serviço de backup
Conta utilizada
Horário autorizado
Hosts envolvidos
```

A solução pode ser melhorar o contexto da regra.

---

# Tuning de regras

O tuning é o processo de ajustar uma detecção para melhorar sua qualidade.

Exemplo:

### Regra inicial

```text id="w6k4rp"
IF PowerShell
THEN Alert
```

Resultado:

```text id="j8m2q1"
500 alertas/dia
```

Após análise:

```text id="s5k9x0"
400 = administração legítima
70 = scripts corporativos
30 = suspeitos
```

A regra pode ser refinada:

```text id="r4v8n3"
PowerShell
+
EncodedCommand
+
processo pai suspeito
```

Resultado:

```text id="b2z6d1"
50 alertas/dia
```

O objetivo não foi simplesmente reduzir de 500 para 50.

O objetivo foi aumentar a proporção de eventos relevantes.

---

# Exceções

Exceções podem ser utilizadas quando existe um comportamento conhecido e autorizado.

Exemplo:

```text id="x3n7k2"
Regra:
Execução de PowerShell suspeita

Exceção:
Servidor de automação
+
conta de serviço autorizada
+
script conhecido
```

Isso pode reduzir ruído.

Mas exceções precisam ser tratadas com cuidado.

Uma exceção muito ampla pode criar um ponto cego.

Evite:

```text id="m4w9s2"
Excluir:
powershell.exe
```

Prefira algo mais específico:

```text id="j2c8v6"
Host:
SRV-AUTOMACAO-01

Conta:
svc_automation

Script:
C:\Scripts\backup.ps1
```

Quanto mais específica a exceção, menor a chance de esconder uma atividade maliciosa.

---

# Whitelist não significa confiança eterna

Uma atividade considerada legítima hoje pode deixar de ser legítima amanhã.

Por isso:

```text id="z5v1r8"
Whitelist
     ↓
Revisão periódica
     ↓
Validação
     ↓
Manutenção
```

Exceções antigas devem ser revisadas.

Caso contrário, uma regra criada para reduzir falsos positivos pode se transformar em uma brecha de detecção.

---

# Priorização

Nem todos os alertas precisam receber o mesmo tratamento.

Um SOC pode considerar:

```text id="q8f4m2"
Risco do ativo
+
Criticidade do usuário
+
Confiança da detecção
+
Impacto potencial
+
Contexto da ameaça
```

Exemplo:

```text id="r3n8p6"
Alerta:
PowerShell suspeito

Endpoint:
Computador de teste

Prioridade:
Média
```

Enquanto:

```text id="h2v7k9"
Alerta:
PowerShell suspeito

Endpoint:
Servidor de produção

Usuário:
Administrador

Destino:
IP associado a malware

Prioridade:
Alta/Critica
```

A mesma regra pode gerar prioridades diferentes dependendo do contexto.

---

# Confiança da detecção

Outra forma de pensar na priorização é utilizar o conceito de **confidence**.

Exemplo:

```text id="b7x4q2"
PowerShell
        ↓
Baixa confiança

PowerShell
+
EncodedCommand
        ↓
Média confiança

PowerShell
+
EncodedCommand
+
Word
+
IP malicioso
        ↓
Alta confiança
```

Não significa que uma detecção de alta confiança seja automaticamente um incidente.

Significa que existem mais evidências apontando para um comportamento malicioso.

---

# Correlação reduz ruído

Imagine que três regras diferentes disparem:

```text id="z9r1c4"
PowerShell suspeito
      +
Download suspeito
      +
Conexão C2
```

Em vez de tratar como três incidentes independentes, o SIEM pode correlacioná-los.

Resultado:

```text id="k5m8v2"
Possível cadeia de ataque
```

Isso melhora a investigação e reduz a fragmentação dos alertas.

---

# Alertas e contexto do ativo

Uma regra também deve considerar o ativo envolvido.

Exemplo:

```text id="a7p3x9"
Servidor DNS
Servidor de banco
Estação de trabalho
Notebook de administrador
Servidor web
```

O mesmo comportamento pode ter significados diferentes dependendo do equipamento.

Por exemplo:

```text id="j6w2n8"
Consulta DNS incomum
```

Em uma estação de trabalho, pode ser apenas um comportamento suspeito.

Em um servidor DNS, pode fazer parte da própria função do sistema.

Sem conhecer o ativo, a regra pode gerar ruído.

---

# O objetivo do tuning

O objetivo do tuning não é:

```text id="v2r6m8"
"Gerar menos alertas."
```

O objetivo é:

```text id="d4k9s1"
Gerar alertas melhores.
```

Uma regra pode gerar:

```text id="c8n3q5"
1000 alertas
```

e ser excelente.

Outra pode gerar:

```text id="u7p2w4"
10 alertas
```

e ser péssima.

A quantidade, sozinha, não mede qualidade.

O que importa é a relação entre:

```text id="x5j1a8"
Volume
+
Relevância
+
Cobertura
+
Tempo de investigação
```

---

# Métricas para acompanhar

Algumas métricas úteis:

| Métrica           | Pergunta                                 |
| ----------------- | ---------------------------------------- |
| Volume            | Quantos alertas foram gerados?           |
| Falso positivo    | Quantos eram benignos?                   |
| True Positive     | Quantos eram realmente relevantes?       |
| Tempo de triagem  | Quanto tempo o analista gastou?          |
| Tempo de resposta | Quanto tempo até a ação?                 |
| Escalonamento     | Quantos viraram incidentes?              |
| Cobertura         | Qual comportamento está sendo detectado? |

Esses indicadores ajudam o SOC a identificar quais regras precisam de revisão.

---

# Ciclo de melhoria

Uma detecção madura passa por um ciclo contínuo:

```text id="m2k7v4"
Criar regra
    ↓
Publicar
    ↓
Monitorar
    ↓
Analisar alertas
    ↓
Identificar ruído
    ↓
Ajustar
    ↓
Testar
    ↓
Monitorar novamente
```

Esse ciclo nunca termina completamente.

Novos sistemas entram no ambiente.

Novos comportamentos aparecem.

Novas ameaças surgem.

As regras precisam acompanhar essas mudanças.

---

# Exemplo de análise de um alerta

## Alerta

```text id="f6s8d2"
Título:
Suspicious PowerShell Execution

Host:
WS-034

Usuário:
usuario01

Processo:
powershell.exe

Parent:
WINWORD.EXE
```

### Primeira hipótese

Possível execução de código através de documento malicioso.

### Investigação

```text id="t4n1c7"
1. Verificar usuário
2. Identificar documento aberto
3. Analisar command line
4. Verificar hash
5. Consultar Threat Intelligence
6. Analisar conexões
7. Procurar persistência
8. Verificar outros hosts
```

### Resultado A

```text id="q9m2w5"
Documento corporativo legítimo
+
script autorizado
+
usuário esperado
```

Classificação:

```text id="n5v8c3"
Falso positivo
```

Ação:

```text id="s1x4j7"
Avaliar ajuste da regra.
```

### Resultado B

```text id="k7p2d9"
Documento recebido por e-mail
+
PowerShell codificado
+
download externo
+
IP malicioso
```

Classificação:

```text id="w3r6m1"
Possível incidente
```

Ação:

```text id="h8q5v2"
Escalonar
+
isolar endpoint
+
coletar evidências
+
investigar escopo
```

O alerta foi o mesmo.

O contexto mudou a decisão.

---

# Quando encerrar um alerta

Um alerta pode ser encerrado quando houver evidência suficiente para classificá-lo.

Exemplos:

```text id="u4c8z2"
Benigno
Atividade autorizada
Falso positivo
Duplicado
Teste controlado
IOC sem impacto
Incidente escalado
```

O encerramento deve registrar o motivo.

Evite:

```text id="n6x2p9"
"Resolvido."
```

Prefira algo como:

```text id="r8m4v1"
Falso positivo.

Atividade executada pelo servidor de automação
SRV-AUTOMACAO-01 utilizando conta de serviço autorizada.
Comportamento validado com a equipe responsável.
```

Esse histórico ajuda na melhoria futura das regras.

---

# O analista não deve lutar contra o alerta

Um erro comum é tratar a quantidade de alertas como um problema exclusivamente operacional.

Na realidade, excesso de falsos positivos pode indicar problemas em diferentes pontos:

```text id="c3v7k1"
Regra ruim
     ↓
Telemetria insuficiente
     ↓
Contexto ausente
     ↓
Baseline inadequado
     ↓
Exceções mal definidas
```

Por isso, quando uma regra gera muito ruído, a pergunta não deveria ser apenas:

> "Como podemos desligar isso?"

A pergunta correta é:

> **"Por que essa regra não consegue diferenciar o comportamento legítimo do comportamento suspeito?"**

Essa mudança de pensamento é fundamental para a maturidade de um programa de detecção.

---

# Checklist de triagem

Ao receber um alerta:

```text id="a9k4s6"
[ ] O evento realmente aconteceu?

[ ] Qual usuário está envolvido?

[ ] Qual ativo foi afetado?

[ ] O comportamento é esperado?

[ ] Existe justificativa operacional?

[ ] Existem eventos relacionados?

[ ] Existe IOC associado?

[ ] Existe técnica MITRE ATT&CK relacionada?

[ ] Outros hosts apresentaram o mesmo comportamento?

[ ] Existe impacto?

[ ] O alerta deve ser encerrado ou escalado?
```

---

# Conclusão

Um SOC eficiente não é aquele que possui mais alertas.

É aquele que consegue **separar sinal de ruído**.

Falsos positivos fazem parte da operação de segurança. O problema surge quando eles não são medidos, investigados e utilizados para melhorar as detecções.

O ciclo ideal é:

```text id="p6r2m8"
Detectar
   ↓
Investigar
   ↓
Classificar
   ↓
Aprender
   ↓
Ajustar
   ↓
Detectar melhor
```

Cada falso positivo bem analisado pode revelar uma oportunidade de melhorar a regra.

Cada falso negativo descoberto pode revelar uma lacuna de cobertura.

E cada alerta bem contextualizado reduz o tempo necessário para o analista entender o que realmente está acontecendo.

No final, o objetivo não é eliminar o ruído completamente — isso é praticamente impossível em um ambiente real.

O objetivo é fazer com que, quando um alerta chegar ao analista, **exista uma boa razão para ele olhar para aquilo**.
