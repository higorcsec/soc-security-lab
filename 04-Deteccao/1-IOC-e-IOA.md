# IOC e IOA

## Introdução

Detectar um ataque não significa apenas procurar por arquivos maliciosos ou endereços IP conhecidos.

Em um SOC, o desafio é identificar **evidências de que algo fora do comportamento esperado está acontecendo**.

É nesse ponto que entram os **Indicators of Compromise (IoC)** e os **Indicators of Attack (IoA)**.

Os dois conceitos são importantes, mas respondem a perguntas diferentes:

> **IoC:** existe alguma evidência de que o ambiente foi comprometido?

> **IoA:** existe algum comportamento indicando que um ataque está acontecendo?

Essa diferença parece simples, mas muda completamente a forma como uma equipe de segurança constrói suas detecções.

---

## IoC — Indicator of Compromise

Um **Indicator of Compromise (IoC)** é um artefato que pode indicar que um sistema, usuário ou rede foi comprometido.

São evidências observáveis que podem ser pesquisadas em logs, endpoints, firewalls, DNS, proxies, EDRs e outras fontes de telemetria.

Alguns exemplos:

* Endereço IP associado a uma infraestrutura maliciosa;
* Domínio utilizado por um atacante;
* Hash de um arquivo malicioso;
* URL utilizada em uma campanha de phishing;
* Nome de arquivo suspeito;
* Chave de registro criada por malware;
* E-mail utilizado em uma campanha;
* Certificado ou artefato associado a uma infraestrutura maliciosa.

Um exemplo simples:

```text
IP: 185.x.x.x
Hash SHA256: 8f3c...a91d
Domínio: exemplo-malicioso[.]com
```

Se um desses indicadores aparecer nos logs de um ambiente corporativo, existe um motivo para investigação.

Mas existe uma limitação importante:

**um IoC normalmente representa uma evidência específica e pode ser alterado pelo atacante.**

Um domínio pode ser substituído.

Um IP pode mudar.

Um arquivo pode receber outro hash.

Uma infraestrutura pode ser descartada e outra criada.

Por isso, detectar somente IoCs conhecidos pode fazer com que o SOC enxergue apenas uma parte do ataque.

O RFC 9424, publicado pelo IETF, aborda justamente o papel dos IoCs na defesa e destaca tanto sua utilidade quanto suas limitações operacionais.

---

## IoA — Indicator of Attack

O **Indicator of Attack (IoA)** muda o foco da análise.

Em vez de perguntar:

> "Esse arquivo é conhecido como malicioso?"

o analista passa a perguntar:

> "O que esse processo está tentando fazer?"

IoAs representam **comportamentos associados à execução de um ataque**.

Exemplos:

* PowerShell executando código codificado;
* Word iniciando PowerShell;
* Processo tentando acessar LSASS de maneira incomum;
* Criação de uma conta administrativa fora do padrão;
* Execução de comandos administrativos por uma conta inesperada;
* Alteração de mecanismos de persistência;
* Conexões periódicas para um destino externo;
* Ferramentas administrativas sendo utilizadas de maneira anormal.

Considere o seguinte cenário:

```text
winword.exe
    └── powershell.exe
            └── comando codificado
                    └── conexão externa
```

Nenhum desses elementos, isoladamente, necessariamente significa comprometimento.

Porém, quando aparecem juntos, o contexto muda.

É justamente esse contexto que torna IoAs importantes para um SOC.

---

## IoC x IoA

| Característica                     | IoC                                          | IoA                                    |
| ---------------------------------- | -------------------------------------------- | -------------------------------------- |
| Foco                               | Evidência                                    | Comportamento                          |
| Momento                            | Geralmente após ou durante o comprometimento | Durante a execução do ataque           |
| Exemplo                            | Hash malicioso                               | PowerShell executando payload suspeito |
| Dependência de conhecimento prévio | Alta                                         | Menor                                  |
| Facilidade para o atacante alterar | Alta                                         | Menor                                  |
| Uso no SOC                         | Busca, enriquecimento e bloqueio             | Detecção e investigação                |

Isso não significa que IoAs sejam "melhores" que IoCs.

Os dois devem trabalhar juntos.

Um IoC pode confirmar uma suspeita.

Um IoA pode levantar a suspeita antes que exista um IoC conhecido.

---

## Exemplo de investigação

Imagine que o SIEM gere um alerta:

```text
ALERTA: Execução suspeita de PowerShell
Host: WS-023
Usuário: joao.silva
Processo pai: winword.exe
Destino: 185.x.x.x
```

O alerta sozinho não confirma um incidente.

O analista começa a enriquecer a informação.

### 1. Analisar o processo

```text
winword.exe
    ↓
powershell.exe
    ↓
EncodedCommand
```

Existe um comportamento suspeito.

### 2. Verificar o destino

```text
185.x.x.x
```

O IP é consultado em fontes de Threat Intelligence.

Caso exista associação com infraestrutura maliciosa, temos um IoC adicional.

### 3. Correlacionar com outros eventos

O analista procura:

```text
Login do usuário
        ↓
Abertura do documento
        ↓
PowerShell
        ↓
Conexão externa
        ↓
Criação de arquivo
        ↓
Persistência
```

Agora não estamos mais olhando para um evento isolado.

Estamos reconstruindo uma **cadeia de comportamento**.

Esse é um dos pontos em que um SIEM deixa de ser apenas um lugar para armazenar logs e passa a funcionar como ferramenta de investigação.

---

## A Pirâmide da Dor

Uma maneira útil de entender a diferença entre indicadores é pensar na **Pyramid of Pain**.

De forma simplificada:

```text
             TTPs
              ▲
             / \
            /   \
         Tools
          / \
       Network
       Artifacts
        /   \
      Domains
       / \
      IPs
       / \
     Hashes
```

Na parte inferior estão indicadores relativamente fáceis de alterar.

Um atacante pode trocar:

```text
Hash
   ↓
Arquivo recompilado
   ↓
Novo hash
```

Também pode trocar:

```text
IP
   ↓
Novo servidor
```

Já alterar uma técnica ou procedimento utilizado durante uma operação pode ser muito mais difícil.

Por isso, uma estratégia madura de detecção não deve depender exclusivamente de listas de IPs, domínios e hashes.

O objetivo é avançar progressivamente para a detecção de **comportamentos e TTPs**.

O RFC 9424 utiliza justamente a ideia da Pyramid of Pain para discutir a utilidade e o custo operacional dos diferentes tipos de IoCs.

---

## Transformando IoCs em detecção

Receber um IoC não significa simplesmente colocá-lo em uma blacklist.

O processo pode seguir uma sequência:

```text
Threat Intelligence
        ↓
Identificação do IoC
        ↓
Validação
        ↓
Enriquecimento
        ↓
Busca no ambiente
        ↓
Correlação com outros eventos
        ↓
Detecção
        ↓
Alerta
        ↓
Investigação
```

Por exemplo:

```text
IoC:
malicious-domain.com
```

O SOC pode pesquisar:

```text
DNS Logs
Proxy Logs
Firewall Logs
EDR
Web Server Logs
```

Se o domínio apareceu apenas uma vez em uma máquina isolada, o contexto é diferente de:

```text
50 endpoints
      ↓
mesmo domínio
      ↓
mesmo período
      ↓
mesmo processo
```

O segundo cenário merece uma prioridade muito maior.

---

## IoC não é sinônimo de incidente

Esse é um ponto importante para quem trabalha com SOC.

Encontrar um IoC não significa automaticamente que existe um incidente confirmado.

Por exemplo:

```text
IP malicioso encontrado no log
```

pode representar:

* tentativa bloqueada pelo firewall;
* conexão legítima através de infraestrutura compartilhada;
* scanner externo;
* tráfego de uma aplicação comprometida;
* verdadeiro comprometimento do endpoint.

Por isso, o indicador precisa ser analisado dentro do contexto.

Uma boa detecção não responde apenas:

> "O indicador apareceu?"

Ela tenta responder:

> "O que aconteceu antes, durante e depois da aparição desse indicador?"

---

## IoA e comportamento

Uma detecção baseada em comportamento pode continuar funcionando mesmo quando o atacante muda seus artefatos.

Exemplo:

```text
Atacante
   ↓
troca o malware
   ↓
troca o hash
   ↓
troca o domínio
   ↓
mantém a mesma técnica
```

Se a detecção estiver baseada somente no hash, ela pode deixar de funcionar.

Se estiver baseada no comportamento, ainda existe uma possibilidade de identificar a atividade.

Por isso, uma regra como:

```text
powershell.exe
```

é extremamente genérica.

Enquanto uma lógica como:

```text
winword.exe
    ↓
powershell.exe
    ↓
EncodedCommand
    ↓
conexão externa
```

possui muito mais contexto.

O objetivo não é criar a regra mais complexa possível.

É criar a regra que consiga diferenciar **atividade relevante de ruído operacional**.

---

## O papel do analista

Na prática, o analista de SOC precisa combinar os dois tipos de indicadores.

Um fluxo de investigação pode ser:

```text
IoA identificado
      ↓
Gerar alerta
      ↓
Investigar comportamento
      ↓
Encontrar IoCs
      ↓
Enriquecer evidências
      ↓
Correlacionar eventos
      ↓
Determinar impacto
      ↓
Classificar incidente
```

Essa abordagem transforma um simples alerta em uma investigação.

O objetivo não é acumular indicadores.

É **transformar indicadores em contexto**.

---

## Exemplo prático no SIEM

Uma consulta hipotética poderia procurar por execução suspeita de PowerShell:

```text
ProcessName = "powershell.exe"
AND
CommandLine contains "EncodedCommand"
AND
ParentProcess = "winword.exe"
```

A detecção pode então ser enriquecida com:

```text
+ Usuário
+ Host
+ IP de origem
+ IP de destino
+ Hash do processo
+ Domínio
+ Evento DNS
+ Evento de autenticação
+ Informação de Threat Intelligence
```

O resultado é muito mais útil para o analista do que simplesmente:

```text
PowerShell detected.
```

Uma boa detecção deve entregar **evidência suficiente para começar a investigação**.

---

## Checklist de análise

Ao encontrar um IoC ou IoA, algumas perguntas devem ser feitas:

### Identificação

* O que foi detectado?
* Qual é a origem do indicador?
* O indicador é confiável?
* Ele ainda está ativo?

### Contexto

* Qual usuário estava envolvido?
* Qual endpoint?
* Qual processo originou o evento?
* Qual foi o processo pai?
* Houve conexão externa?
* Existem eventos relacionados?

### Escopo

* A atividade ocorreu em apenas um host?
* Outros usuários foram afetados?
* O mesmo indicador apareceu em outros endpoints?
* Existe movimentação lateral?

### Impacto

* Houve execução de código?
* Houve elevação de privilégio?
* Houve persistência?
* Houve acesso a credenciais?
* Houve exfiltração?

### Resposta

* O evento deve ser escalado?
* O host precisa ser isolado?
* O indicador deve ser bloqueado?
* É necessário criar ou ajustar uma regra de detecção?

---

## O objetivo da detecção

Um SOC maduro não tenta detectar absolutamente tudo.

Isso seria impraticável e geraria uma quantidade enorme de ruído.

O objetivo é construir detecções que sejam:

* relevantes;
* contextualizadas;
* investigáveis;
* mensuráveis;
* ajustáveis;
* alinhadas ao risco do ambiente.

MITRE ATT&CK, por exemplo, disponibiliza analytics que representam a lógica específica utilizada para implementar estratégias de detecção. Isso reforça uma ideia importante: **uma técnica identificada precisa ser traduzida em uma lógica observável no ambiente**.

---

## Conclusão

IoCs ajudam o SOC a reconhecer **evidências conhecidas**.

IoAs ajudam o SOC a reconhecer **comportamentos suspeitos**.

A combinação dos dois permite construir uma defesa menos dependente de indicadores estáticos e mais orientada ao comportamento do adversário.

Na prática:

```text
IoC = "Eu encontrei algo conhecido."

IoA = "Eu encontrei algo suspeito acontecendo."

Detecção madura =
IoC + IoA + contexto + correlação + investigação
```

O valor de um SOC não está em gerar milhares de alertas.

Está em conseguir transformar eventos aparentemente desconectados em uma história que faça sentido para o analista.

**Detectar é encontrar o sinal. Investigar é entender o contexto.**
