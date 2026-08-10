# OSINT

OSINT significa **Open Source Intelligence**, ou Inteligência de Fontes Abertas.

Dentro de um SOC, OSINT é utilizado para coletar, cruzar e analisar informações disponíveis publicamente com o objetivo de entender melhor uma ameaça, um indicador ou uma infraestrutura.

Isso pode envolver informações encontradas em:

* Sites públicos
* DNS
* WHOIS
* Certificados digitais
* Repositórios
* Fóruns
* Redes sociais
* Blogs técnicos
* Bases de reputação
* Relatórios de segurança
* Vazamentos publicados na internet
* Infraestrutura exposta

O ponto principal é entender que **OSINT não é simplesmente pesquisar no Google**.

É transformar informações públicas em contexto útil para uma investigação.

---

# 1. OSINT dentro de um SOC

Imagine que o SIEM gere um alerta:

```text
[ALERTA]

Endpoint: WEB-SRV-01
Destino: 185.XX.XX.10
Porta: 443
```

O endereço IP sozinho não explica muita coisa.

O analista pode utilizar OSINT para descobrir:

```text
IP
 ↓
WHOIS
 ↓
ASN
 ↓
DNS
 ↓
Domínios relacionados
 ↓
Certificados
 ↓
Reputação
 ↓
Infraestrutura relacionada
```

Depois da investigação, aquele IP pode revelar uma infraestrutura utilizada por phishing, malware ou C2.

O alerta que inicialmente parecia apenas uma conexão externa passa a possuir contexto.

---

# 2. O que pode ser investigado com OSINT?

OSINT pode ser aplicado a praticamente qualquer indicador encontrado durante uma investigação.

Entre os principais estão:

| Indicador   | Possíveis informações                                  |
| ----------- | ------------------------------------------------------ |
| IP          | ASN, localização aproximada, reputação, infraestrutura |
| Domínio     | DNS, registros, subdomínios, histórico                 |
| URL         | Reputação, redirecionamentos, malware                  |
| Hash        | Malware relacionado, família, detecções                |
| E-mail      | Domínios relacionados, exposição pública               |
| Certificado | Domínios associados                                    |
| Empresa     | Infraestrutura, tecnologias, ativos públicos           |
| Usuário     | Exposição pública e possíveis relações                 |
| Arquivo     | Metadados e indicadores                                |

O objetivo é criar conexões entre informações que inicialmente parecem isoladas.

---

# 3. OSINT e investigação de um domínio

Um dos cenários mais comuns em um SOC é investigar um domínio suspeito.

Imagine:

```text
secure-microsoft-login[.]com
```

O analista pode começar verificando:

```text
DNS
 ↓
WHOIS
 ↓
Nameservers
 ↓
Certificados
 ↓
Subdomínios
 ↓
Histórico
 ↓
Reputação
```

Algumas perguntas importantes:

```text
Quando o domínio foi registrado?

Quem é o registrador?

Quais nameservers estão sendo utilizados?

Existem outros domínios utilizando a mesma infraestrutura?

Existe certificado TLS associado?

O domínio aparece em campanhas de phishing?

Já foi identificado anteriormente por outras organizações?
```

Uma única informação pode levar a várias outras.

---

# 4. DNS como fonte de inteligência

DNS é uma das fontes mais úteis durante uma investigação.

Um domínio pode revelar:

```text
A Record
AAAA Record
MX Record
NS Record
TXT Record
CNAME
```

Por exemplo:

```text
example.com
      ↓
A Record
      ↓
185.XX.XX.20
      ↓
IP
      ↓
ASN
      ↓
Provedor
```

O mesmo domínio também pode possuir subdomínios:

```text
mail.example.com
vpn.example.com
dev.example.com
api.example.com
```

Essas informações ajudam o analista a compreender a infraestrutura relacionada ao alvo.

---

# 5. WHOIS e informações de registro

WHOIS pode fornecer informações relacionadas ao registro de um domínio ou endereço IP.

Durante uma investigação, alguns dados podem ser relevantes:

* Registrador
* Datas de criação e atualização
* Nameservers
* Organização
* ASN
* Blocos de IP

Nem sempre essas informações estarão disponíveis.

Muitos registros utilizam proteção de privacidade.

Mesmo assim, os dados técnicos restantes podem ajudar a conectar diferentes indicadores.

---

# 6. Certificados Digitais

Certificados TLS também podem ser utilizados como fonte de inteligência.

Imagine que um certificado contenha:

```text
CN: login-example.com

SAN:
login-example.com
secure-example.com
account-example.com
```

Agora o analista possui três possíveis indicadores relacionados.

Isso permite ampliar a investigação:

```text
Certificado
     ↓
Domínios
     ↓
IPs
     ↓
Infraestrutura
     ↓
Outros indicadores
```

Certificados podem ser especialmente interessantes durante investigações de phishing e infraestrutura maliciosa.

---

# 7. IP, ASN e infraestrutura

Um endereço IP pode ser apenas o começo da investigação.

Exemplo:

```text
185.XX.XX.10
```

O analista pode descobrir:

```text
IP
 ↓
ASN
 ↓
Organização
 ↓
Range
 ↓
Outros IPs
 ↓
Domínios relacionados
```

Isso pode revelar que diferentes domínios suspeitos estão hospedados na mesma infraestrutura.

Por exemplo:

```text
login-company[.]com
secure-company[.]net
verify-company[.]org

          ↓

     Mesmo ASN
          ↓
     Mesmo range
          ↓
   Mesma infraestrutura
```

Uma relação como essa pode ser muito mais relevante do que analisar cada domínio isoladamente.

---

# 8. OSINT e Threat Intelligence

OSINT e Threat Intelligence estão diretamente relacionados, mas não são a mesma coisa.

Uma forma simples de visualizar:

```text
OSINT
 ↓
Coleta de informações públicas
 ↓
Análise
 ↓
Correlação
 ↓
Contexto
 ↓
Threat Intelligence
```

OSINT pode ser uma das fontes utilizadas para produzir inteligência.

Por exemplo:

```text
OSINT encontrou:
IP + domínio + certificado + ASN

             ↓

Analista correlaciona os dados

             ↓

Identifica infraestrutura relacionada

             ↓

Cria inteligência sobre a ameaça
```

Portanto:

> **OSINT coleta e investiga informações. Threat Intelligence transforma informações em conhecimento acionável sobre ameaças.**

---

# 9. Ferramentas utilizadas em OSINT

Um analista não precisa depender de uma única ferramenta.

Durante uma investigação, diferentes fontes podem ser combinadas.

Algumas categorias importantes são:

### DNS

Utilizado para descobrir registros e relações entre domínios.

Exemplos:

```text
nslookup
dig
```

---

### WHOIS

Utilizado para consultar informações de registro.

Exemplo:

```bash
whois example.com
```

---

### Certificados

Podem ser utilizados para descobrir domínios relacionados a certificados públicos.

---

### Reputação

Serviços de reputação podem indicar se um IP, domínio, URL ou arquivo já foi associado a atividades maliciosas.

---

### Busca na Internet

Mecanismos de busca também podem revelar:

* Documentação exposta
* Repositórios
* Credenciais publicadas
* Relatórios
* Infraestrutura
* Discussões técnicas
* Indicadores já investigados

O importante é saber **o que procurar e como relacionar os resultados**.

---

# 10. OSINT e Google Dorks

Operadores avançados de mecanismos de busca podem ajudar a encontrar informações públicas específicas.

Exemplo:

```text
site:example.com
```

Pode restringir resultados a um domínio.

Outro exemplo:

```text
filetype:pdf
```

Pode localizar arquivos PDF indexados.

Também podem ser combinados:

```text
site:example.com filetype:pdf
```

Em um contexto de segurança, essas técnicas podem ajudar a identificar informações que uma organização publicou inadvertidamente.

O objetivo de um SOC deve ser **identificar exposição e reduzir risco**, não explorar sistemas sem autorização.

---

# 11. OSINT em um caso de phishing

Imagine que o SOC receba o seguinte domínio:

```text
microsoft-login-security[.]com
```

A investigação pode seguir:

```text
DOMÍNIO
   ↓
WHOIS
   ↓
DATA DE REGISTRO
   ↓
DNS
   ↓
IP
   ↓
ASN
   ↓
CERTIFICADO
   ↓
OUTROS DOMÍNIOS
   ↓
REPUTAÇÃO
```

O analista descobre:

```text
Domínio criado recentemente
        +
Nome semelhante a marca legítima
        +
Infraestrutura compartilhada com outros domínios suspeitos
        +
Página de login falsa
```

Agora existe uma forte indicação de que o domínio faz parte de uma campanha de phishing.

O próximo passo pode envolver:

```text
Bloqueio do domínio
        ↓
Busca por outros usuários afetados
        ↓
Verificação de credenciais
        ↓
Investigação dos endpoints
        ↓
Resposta ao incidente
```

OSINT ajudou a transformar um domínio desconhecido em um indicador contextualizado.

---

# 12. OSINT + SIEM

OSINT também pode complementar diretamente o trabalho realizado no SIEM.

Um cenário:

```text
SIEM
 ↓
Detecta IP suspeito
 ↓
Analista coleta IOC
 ↓
OSINT
 ↓
Reputação + DNS + ASN + histórico
 ↓
Enriquecimento
 ↓
SIEM
 ↓
Nova correlação
 ↓
Prioridade do alerta
```

Isso cria um ciclo interessante:

```text
LOG → IOC → OSINT → CONTEXTO → DETECÇÃO → INVESTIGAÇÃO
```

O analista deixa de olhar apenas para eventos individuais e começa a enxergar relacionamentos.

---

# 13. Documentação da investigação

Uma investigação OSINT precisa ser reproduzível.

Não basta encontrar uma informação e simplesmente colocá-la no relatório.

O analista deve registrar:

```text
IOC investigado:
185.XX.XX.10

Data da investigação:
2026-08-10

Fonte:
Fonte pública consultada

Resultado:
IP associado a infraestrutura suspeita

Evidências:
DNS
ASN
Certificado
Reputação

Conclusão:
Indicador apresenta características compatíveis
com infraestrutura maliciosa.
```

Isso ajuda outro analista a revisar a investigação posteriormente.

---

# 14. Cuidado com falsos positivos

Uma das maiores armadilhas do OSINT é assumir que uma informação isolada comprova uma ameaça.

Por exemplo:

```text
IP possui reputação ruim
```

Isso não significa automaticamente:

```text
"Esse IP está atacando minha empresa."
```

Pode existir:

* IP compartilhado
* CDN
* Hosting compartilhado
* Proxy
* NAT
* Infraestrutura comprometida
* Informação desatualizada

Por isso, OSINT deve ser utilizado para **enriquecer uma investigação**, e não substituir a análise dos eventos internos.

---

# 15. OSINT e OPSEC

Existe outro conceito importante:

**OPSEC — Operations Security.**

Ao realizar investigações OSINT, o analista deve pensar também na própria exposição.

Dependendo da investigação, acessar diretamente determinado recurso pode revelar:

```text
IP do analista
User-Agent
Horário de acesso
Origem da conexão
```

Em ambientes profissionais, investigações mais sensíveis podem utilizar ambientes controlados para reduzir exposição.

A regra é simples:

> **Investigar uma ameaça não significa revelar sua infraestrutura de investigação para ela.**

---

# 16. OSINT no fluxo de um SOC

Uma investigação pode ser representada assim:

```text
                    ALERTA
                       │
                       ▼
                    IOC
                       │
                       ▼
                 COLETA OSINT
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
         DNS          WHOIS      REPUTAÇÃO
          │            │            │
          └────────────┼────────────┘
                       ▼
                  CORRELAÇÃO
                       │
                       ▼
                   CONTEXTO
                       │
                       ▼
                DECISÃO DO SOC
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Monitorar     Bloquear    Investigar
```

O valor do processo está na correlação.

---

# 17. OSINT não é "achar informação"

Uma investigação OSINT eficiente precisa responder perguntas.

Por exemplo:

```text
Quem controla essa infraestrutura?

Quando ela surgiu?

Quais domínios estão relacionados?

Existe histórico de abuso?

Esse indicador aparece em outras campanhas?

Existe atividade semelhante dentro da organização?

O indicador está relacionado a algum malware conhecido?

Existe evidência suficiente para classificar o IOC como ameaça?
```

Essas perguntas direcionam a investigação.

Sem objetivo, OSINT rapidamente se transforma em uma coleção de informações sem utilidade operacional.

---

# 18. Boas práticas

Ao utilizar OSINT em um SOC:

* Sempre registre as fontes utilizadas.
* Valide informações em mais de uma fonte quando possível.
* Considere a data da informação.
* Não trate reputação como prova definitiva.
* Preserve evidências importantes.
* Documente a linha de raciocínio da investigação.
* Evite acessar recursos suspeitos diretamente sem necessidade.
* Utilize ambientes controlados quando apropriado.
* Diferencie informação pública de informação confirmada.
* Respeite leis, políticas internas e autorização.
* Transforme descobertas relevantes em inteligência reutilizável.

---

# 19. O que um analista deve procurar?

Durante uma investigação, o objetivo não deve ser simplesmente encontrar "algo suspeito".

O analista deve procurar **relações**.

Por exemplo:

```text
IOC
 ↓
Domínio
 ↓
IP
 ↓
ASN
 ↓
Certificado
 ↓
Outros domínios
 ↓
Malware
 ↓
Campanha
 ↓
Threat Actor
```

Quanto mais relações confiáveis forem encontradas, maior será a capacidade de compreender a ameaça.

---

# Conclusão

OSINT é uma das habilidades mais importantes para um analista de SOC porque permite transformar um indicador desconhecido em uma informação contextualizada.

Um IP, domínio ou hash isolado possui pouco significado.

Quando combinado com:

```text
DNS
WHOIS
Reputação
Certificados
ASN
Histórico
Relatórios
Infraestrutura
```

é possível construir uma visão muito mais completa da ameaça.

O analista de SOC não precisa apenas saber **qual alerta apareceu**.

Ele precisa conseguir responder:

> **"O que existe por trás desse alerta?"**

É essa capacidade de investigação que transforma dados em contexto e contexto em decisão.

---

## Resumo

```text
OSINT
 ↓
Coleta
 ↓
Validação
 ↓
Correlação
 ↓
Contextualização
 ↓
Threat Intelligence
 ↓
Detecção
 ↓
Investigação
 ↓
Resposta
```

> **OSINT encontra as peças.
> O analista conecta as peças.
> O SOC transforma essas conexões em ação.**
