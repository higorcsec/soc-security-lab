# Feeds de Threat Intelligence

Threat Intelligence só é útil quando consegue chegar ao SOC de forma **rápida, confiável e utilizável**.

É aí que entram os **Threat Intelligence Feeds**.

Um feed é uma fonte que fornece informações relacionadas a ameaças, como:

* Endereços IP maliciosos
* Domínios suspeitos
* URLs utilizadas em campanhas
* Hashes de arquivos maliciosos
* Indicadores associados a phishing
* Infraestrutura utilizada por grupos criminosos
* Táticas, técnicas e procedimentos (TTPs)
* Informações sobre campanhas e famílias de malware

Na prática, o feed funciona como uma camada de inteligência que ajuda o SOC a responder uma pergunta simples:

> **"Esse indicador que apareceu no ambiente já é conhecido como ameaça?"**

---

## 1. Como um Feed é utilizado no SOC

Imagine que um endpoint faça uma conexão para:

```text
185.XX.XX.XX
```

O SIEM registra a conexão e gera um evento.

Sozinho, o endereço IP pode não significar muita coisa.

Agora imagine que esse IP esteja presente em um Threat Intelligence Feed e tenha sido associado anteriormente a:

```text
C2 - Command and Control
Malware
Botnet
Credential Theft
```

A situação muda completamente.

O SOC pode correlacionar:

```text
Log do Endpoint
      ↓
SIEM
      ↓
IOC detectado
      ↓
Threat Intelligence Feed
      ↓
Indicador conhecido como malicioso
      ↓
Alerta
      ↓
Investigação
```

Esse processo reduz o tempo necessário para transformar um evento aparentemente comum em um **alerta contextualizado**.

---

# 2. Tipos de Threat Intelligence Feeds

Nem todo feed possui o mesmo objetivo.

Alguns são extremamente focados em indicadores técnicos, enquanto outros fornecem contexto sobre campanhas e grupos de ameaça.

## 2.1 IPs Maliciosos

Fornecem endereços IP associados a atividades suspeitas ou maliciosas.

Exemplo:

```text
185.XX.XX.10
45.XX.XX.73
91.XX.XX.142
```

Podem estar relacionados a:

* C2
* Botnets
* Scanners
* Brute Force
* Malware
* Proxy malicioso
* Infraestrutura de phishing

Um SOC pode utilizar esses indicadores para verificar conexões de saída dos endpoints.

---

## 2.2 Domínios Maliciosos

Contêm domínios identificados como suspeitos ou maliciosos.

Exemplo:

```text
login-microsoft-security[.]com
account-verification[.]net
secure-office365-login[.]xyz
```

São particularmente úteis contra campanhas de:

* Phishing
* Credential Theft
* Malware
* Fake Login Pages
* Command and Control

Um domínio recém-registrado e semelhante ao de uma empresa legítima pode receber atenção especial durante uma investigação.

---

## 2.3 URLs Maliciosas

Alguns feeds trabalham diretamente com URLs.

Exemplo:

```text
hxxps://example[.]com/update/login.php
```

Esse tipo de inteligência pode ajudar a identificar:

* Links de phishing
* Downloads maliciosos
* Exploit Kits
* Malware Delivery
* Redirecionamentos suspeitos

É comum combinar esse tipo de feed com logs de proxy, DNS e firewall.

---

## 2.4 File Hashes

Outro indicador comum são hashes de arquivos.

Exemplo:

```text
SHA256:
9f86d081884c7d659a2feaa0c55ad015...
```

Quando um arquivo é detectado em um endpoint, seu hash pode ser comparado com bases de inteligência.

O SOC pode então descobrir se aquele arquivo já foi associado a:

```text
Trojan
Ransomware
Stealer
Backdoor
Loader
```

Hash é extremamente útil porque permite identificar um arquivo sem depender apenas do nome dele.

---

# 3. Feeds Públicos, Comerciais e Internos

Uma das diferenças importantes em Threat Intelligence está na origem da informação.

## Feeds Públicos

São fontes disponibilizadas publicamente.

Podem ser utilizados para:

* Laboratórios
* Estudos
* Homelabs
* Pequenos ambientes
* Testes de detecção

São uma excelente forma de começar a trabalhar com Threat Intelligence sem depender de uma solução comercial.

---

## Feeds Comerciais

São fornecidos por empresas especializadas em segurança.

Normalmente oferecem:

* Maior volume de indicadores
* Enriquecimento dos IOCs
* Contexto adicional
* Reputação dos indicadores
* Classificação de ameaças
* Informações sobre campanhas
* APIs
* Integração com SIEM e SOAR

Em ambientes corporativos, a escolha de um feed deve considerar **qualidade e relevância**, e não apenas quantidade.

---

## Feeds Internos

Essa é uma parte frequentemente esquecida.

Uma empresa também pode criar sua própria inteligência.

Exemplo:

Durante uma investigação, o SOC identifica:

```text
IP → 10.10.10.50
Domínio → example-malware.com
Hash → abc123...
```

Depois de confirmar que os indicadores estão relacionados ao incidente, eles podem ser adicionados a uma base interna.

Isso transforma incidentes anteriores em **inteligência reutilizável**.

---

# 4. STIX e TAXII

Quando diferentes ferramentas precisam compartilhar Threat Intelligence, é importante utilizar formatos e protocolos padronizados.

Dois nomes aparecem com frequência:

**STIX** e **TAXII**.

### STIX

STIX é um padrão utilizado para representar informações de Threat Intelligence de maneira estruturada.

Ele permite descrever relacionamentos entre objetos como:

```text
Threat Actor
      ↓
Malware
      ↓
Infrastructure
      ↓
Domain
      ↓
IP Address
```

Isso é muito mais útil do que simplesmente possuir uma lista com milhares de IPs.

---

### TAXII

TAXII é utilizado para transportar e compartilhar informações de Threat Intelligence.

De forma simplificada:

```text
STIX
↓
Representa a inteligência

TAXII
↓
Transporta a inteligência
```

Essa padronização facilita a integração entre diferentes ferramentas de segurança.

---

# 5. Integração com o SIEM

Um dos principais objetivos de um Threat Intelligence Feed dentro de um SOC é alimentar o processo de detecção.

Um fluxo comum seria:

```text
Threat Intelligence Feed
          ↓
      Integração/API
          ↓
         SIEM
          ↓
      Correlação
          ↓
     Evento + IOC
          ↓
        Alerta
```

Por exemplo:

Um firewall registra:

```text
SRC: 10.0.0.25
DST: 185.XX.XX.10
PORT: 443
```

O SIEM verifica o destino contra uma base de Threat Intelligence.

Se o IP estiver classificado como infraestrutura de C2:

```text
[ALERTA]

Endpoint: 10.0.0.25
Destino: 185.XX.XX.10
Tipo: C2
Severidade: Alta
```

Agora o analista possui contexto suficiente para iniciar a investigação.

---

# 6. Threat Intelligence + Wazuh

Em um ambiente utilizando Wazuh, os indicadores podem ser utilizados para aumentar a capacidade de detecção.

Um possível cenário:

```text
Endpoint
   ↓
Wazuh Agent
   ↓
Evento
   ↓
Wazuh Manager
   ↓
IOC identificado
   ↓
Threat Intelligence
   ↓
Correlação
   ↓
Alerta
```

Isso permite combinar informações provenientes do próprio endpoint com inteligência externa.

Por exemplo:

```text
Processo suspeito
       +
Hash conhecido
       +
Comunicação com IP malicioso
       =
Incidente com alta prioridade
```

O valor não está apenas no IOC.

Está na **correlação entre os eventos**.

---

# 7. O problema dos Feeds

Mais inteligência não significa necessariamente mais segurança.

Um feed pode fornecer milhares ou milhões de indicadores.

Se todos forem tratados como ameaça crítica, o resultado pode ser:

```text
Muito IOC
↓
Muito alerta
↓
Muito ruído
↓
Analista sobrecarregado
↓
Alert Fatigue
```

Por isso, um SOC precisa avaliar a qualidade dos feeds.

Alguns critérios importantes:

| Critério       | Pergunta                                    |
| -------------- | ------------------------------------------- |
| Atualização    | Com que frequência os IOCs são atualizados? |
| Confiabilidade | Qual é a qualidade da fonte?                |
| Contexto       | O indicador possui informações adicionais?  |
| False Positive | Existe histórico de falsos positivos?       |
| Expiração      | Indicadores antigos são removidos?          |
| Integração     | Existe API ou formato automatizado?         |
| Relevância     | O feed faz sentido para o ambiente?         |

Um feed ruim pode aumentar o trabalho do SOC em vez de diminuir.

---

# 8. IOC sem contexto não é inteligência

Essa é uma diferença importante.

Imagine receber:

```text
IP: 45.XX.XX.10
```

Isso é apenas um indicador.

Agora imagine receber:

```text
IP: 45.XX.XX.10

Reputação: Malicioso
Categoria: Command and Control
Malware: Trojan
Primeira observação: 2026-07-14
Última observação: 2026-08-10
Confidence: Alta
Campanha: XYZ
```

Agora existe contexto.

É justamente esse contexto que transforma um simples IOC em **Threat Intelligence utilizável**.

---

# 9. Ciclo de um Threat Intelligence Feed

Dentro de um SOC, o processo pode ser representado da seguinte forma:

```text
COLETA
   ↓
VALIDAÇÃO
   ↓
ENRIQUECIMENTO
   ↓
CORRELAÇÃO
   ↓
DETECÇÃO
   ↓
INVESTIGAÇÃO
   ↓
RESPOSTA
   ↓
FEEDBACK
   ↓
NOVA INTELIGÊNCIA
```

O último estágio é importante.

Após um incidente, novos indicadores podem ser descobertos e retornar para o ciclo:

```text
Incidente
   ↓
Novos IOCs
   ↓
Base interna
   ↓
Novas regras
   ↓
Novos alertas
```

Dessa forma, o SOC não apenas reage às ameaças.

Ele **aprende com os próprios incidentes**.

---

# 10. Exemplo prático

Considere o seguinte cenário:

Um usuário recebe um e-mail de phishing.

O link direciona para:

```text
hxxps://microsoft-verification[.]com/login
```

O usuário acessa a página e insere suas credenciais.

O SOC recebe:

```text
DNS Log
Firewall Log
Proxy Log
Endpoint Log
```

O domínio é consultado em uma fonte de Threat Intelligence.

Resultado:

```text
Domain: microsoft-verification[.]com
Category: Phishing
Confidence: High
First Seen: Recent
```

O SIEM correlaciona o domínio com o usuário que realizou a conexão.

O analista então consegue investigar:

```text
Usuário afetado
       ↓
Endpoint
       ↓
Domínio acessado
       ↓
IOC confirmado
       ↓
Credenciais potencialmente comprometidas
       ↓
Resposta ao incidente
```

Sem Threat Intelligence, o SOC teria apenas uma conexão HTTPS.

Com Threat Intelligence, existe **contexto para transformar o evento em investigação**.

---

# 11. Boas práticas

Ao trabalhar com Threat Intelligence Feeds:

* Não confie cegamente em qualquer feed.
* Priorize fontes com boa reputação.
* Valide indicadores antes de automatizar bloqueios.
* Utilize confidence score quando disponível.
* Defina tempo de expiração para IOCs.
* Evite bloquear indicadores apenas por coincidência.
* Combine diferentes fontes.
* Mantenha uma base interna de inteligência.
* Integre feeds ao SIEM quando possível.
* Documente a origem dos indicadores.
* Monitore falsos positivos.
* Revise regularmente os feeds utilizados.

---

# 12. Threat Intelligence na prática de um SOC

O objetivo de um Threat Intelligence Feed não é simplesmente fornecer uma lista de IPs, domínios e hashes.

O verdadeiro objetivo é **diminuir o tempo entre a descoberta de um evento e a compreensão do que aquele evento representa**.

Um SOC maduro não pergunta apenas:

> "Esse IP é malicioso?"

Ele pergunta:

```text
Quem está utilizando esse IP?
↓
O que aconteceu?
↓
Quando aconteceu?
↓
Qual endpoint foi afetado?
↓
Existe outro IOC relacionado?
↓
Qual técnica foi utilizada?
↓
Existe atividade semelhante no ambiente?
```

É nesse ponto que Threat Intelligence deixa de ser apenas uma base de indicadores e passa a fazer parte do processo de **detecção, investigação e resposta a incidentes**.

---

## Resumo

Threat Intelligence Feeds fornecem informações que podem ser utilizadas para enriquecer eventos e melhorar a capacidade de detecção de um SOC.

Os principais indicadores incluem:

```text
IP
Domínio
URL
Hash
E-mail
Infraestrutura
TTPs
```

Quando integrados corretamente ao SIEM, esses dados podem transformar eventos isolados em alertas contextualizados.

A diferença entre simplesmente possuir um feed e realmente utilizar Threat Intelligence está na capacidade de **correlacionar, validar e transformar os dados em decisões de segurança**.

> **Feed fornece dados.
> Inteligência fornece contexto.
> O SOC transforma contexto em ação.**
