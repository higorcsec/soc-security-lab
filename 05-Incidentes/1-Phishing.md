# Phishing

## Introdução

Phishing é uma das formas mais utilizadas para obter acesso inicial a ambientes corporativos.

A técnica explora algo que nenhuma ferramenta de segurança consegue substituir completamente: **a interação humana**.

O atacante pode utilizar e-mails, mensagens, páginas falsas, documentos ou links maliciosos para induzir uma vítima a:

* fornecer credenciais;
* executar um arquivo;
* acessar um endereço controlado pelo atacante;
* instalar um software;
* aprovar uma autenticação;
* transferir informações;
* realizar alguma ação que favoreça o comprometimento.

Por isso, um incidente de phishing não deve ser tratado apenas como:

> "Um usuário recebeu um e-mail suspeito."

O objetivo da investigação é descobrir **até onde aquele e-mail conseguiu chegar**.

---

# Como funciona um ataque de phishing

Um ataque pode seguir diferentes caminhos, mas uma cadeia comum é:

```text
Atacante
   ↓
Cria campanha
   ↓
Envia mensagem
   ↓
Usuário recebe
   ↓
Usuário interage
   ↓
Credencial / execução / acesso
   ↓
Comprometimento
```

Em um cenário mais completo:

```text
Phishing
   ↓
Link malicioso
   ↓
Página falsa
   ↓
Roubo de credenciais
   ↓
Login utilizando credencial roubada
   ↓
Persistência
   ↓
Acesso a recursos
```

Em outro cenário:

```text
Phishing
   ↓
Documento malicioso
   ↓
Execução
   ↓
PowerShell
   ↓
Download de payload
   ↓
C2
```

A diferença entre os dois cenários é importante porque a resposta do SOC também será diferente.

---

# Tipos de phishing

## Phishing por e-mail

É o cenário mais conhecido.

O atacante envia uma mensagem tentando parecer legítima.

Exemplos de temas utilizados:

```text
Atualização de senha
Fatura
Documento compartilhado
Entrega
Conta bloqueada
Notificação de segurança
Processo seletivo
Cobrança
```

O conteúdo pode direcionar a vítima para um site falso ou induzir a execução de um arquivo.

---

## Spear Phishing

No spear phishing, a mensagem é direcionada para uma pessoa ou grupo específico.

O atacante pode utilizar informações disponíveis sobre:

```text
Cargo
Empresa
Equipe
Projetos
Fornecedores
Clientes
Redes sociais
```

Isso permite construir uma mensagem muito mais convincente.

Exemplo:

```text
Atacante
   ↓
Pesquisa funcionário
   ↓
Identifica gerente financeiro
   ↓
Descobre fornecedor
   ↓
Cria mensagem personalizada
   ↓
Envia documento falso
```

Quanto maior a personalização, maior pode ser a chance de a vítima acreditar na mensagem.

---

## Business Email Compromise

No **Business Email Compromise (BEC)**, o objetivo pode ser financeiro ou operacional.

O atacante pode comprometer ou falsificar uma identidade utilizada em comunicações corporativas.

Exemplo:

```text
Atacante
   ↓
Compromete conta
   ↓
Monitora conversas
   ↓
Identifica pagamento pendente
   ↓
Interfere na comunicação
   ↓
Solicita alteração bancária
```

Esse tipo de incidente mostra por que segurança de identidade é tão importante.

Nem sempre o objetivo do atacante é instalar malware.

Às vezes, **o acesso à conta já é suficiente**.

---

## Smishing

É o phishing realizado através de mensagens SMS ou aplicativos de mensagens.

Exemplo:

```text
Mensagem
   ↓
"Seu pacote não pôde ser entregue"
   ↓
Link
   ↓
Página falsa
   ↓
Credenciais / cartão / dados
```

A lógica permanece a mesma:

```text
Engenharia social
      +
Ação da vítima
      =
Acesso ou informação
```

---

## Quishing

É o uso de **QR Codes** para direcionar a vítima para conteúdo malicioso.

O problema é que o usuário pode não visualizar o destino real antes de escanear o código.

Exemplo:

```text
QR Code
   ↓
URL maliciosa
   ↓
Página falsa
   ↓
Roubo de credenciais
```

Para o SOC, isso reforça a importância de analisar também URLs e domínios, e não apenas anexos.

---

# Indicadores de phishing

Durante a investigação, alguns indicadores podem ser relevantes:

### Remetente

```text
Domínio desconhecido
Domínio parecido com o legítimo
Endereço incompatível
```

### URL

```text
Domínio recém-criado
URL encurtada
Redirecionamentos
HTTPS não significa legitimidade
```

### Anexo

```text
Arquivo executável
Documento inesperado
Arquivo compactado
Extensão suspeita
Macro
Script
```

### Conteúdo

```text
Urgência
Ameaça
Solicitação financeira
Solicitação de credenciais
Pedido incomum
Erro de linguagem
```

Nenhum desses indicadores, isoladamente, confirma um ataque.

O contexto é o que determina a relevância.

---

# O papel do SOC

Quando um usuário reporta:

> "Recebi um e-mail estranho."

o SOC precisa transformar essa informação em uma investigação.

Um fluxo possível:

```text
Usuário reporta
      ↓
Triagem
      ↓
Análise do e-mail
      ↓
Análise de remetente
      ↓
Análise de URL/anexo
      ↓
Threat Intelligence
      ↓
Verificação de outros destinatários
      ↓
Verificação de interação
      ↓
Avaliação de comprometimento
      ↓
Resposta
```

A pergunta principal não é apenas:

> "O e-mail é malicioso?"

Também precisamos descobrir:

> **"Quem recebeu, quem abriu, quem clicou e o que aconteceu depois?"**

---

# Análise do e-mail

Uma investigação pode começar pelos metadados.

Informações relevantes incluem:

```text
From
Reply-To
Return-Path
Received
Message-ID
SPF
DKIM
DMARC
Subject
Attachments
URLs
```

Os cabeçalhos podem revelar informações que não aparecem visualmente na mensagem.

Por exemplo:

```text
From:
financeiro@empresa.com

Reply-To:
contato@dominio-suspeito.com
```

Isso merece investigação.

---

# SPF, DKIM e DMARC

Esses mecanismos ajudam a avaliar a autenticidade de mensagens, mas não devem ser interpretados como uma prova absoluta de legitimidade.

### SPF

Ajuda a verificar se o servidor que enviou a mensagem está autorizado para determinado domínio.

### DKIM

Utiliza assinatura criptográfica para validar a integridade e associação da mensagem ao domínio.

### DMARC

Utiliza informações de SPF e DKIM para aplicar políticas relacionadas à autenticação do domínio.

Um resultado de autenticação positivo não significa automaticamente:

> "Esse e-mail é seguro."

Um atacante pode enviar uma mensagem utilizando um domínio legítimo comprometido ou uma infraestrutura legítima.

Por isso:

```text
Autenticação
+
Reputação
+
Conteúdo
+
Comportamento
+
Contexto
```

devem ser analisados em conjunto.

---

# Análise de URL

Uma URL suspeita deve ser analisada antes de ser acessada diretamente pelo analista.

Alguns pontos:

```text
Domínio
Subdomínio
Caminho
Parâmetros
Redirecionamentos
Reputação
Idade do domínio
Certificado
Infraestrutura
```

Exemplo:

```text
https://login.empresa-exemplo.com
```

pode parecer legítimo.

Mas:

```text
https://empresa-exemplo.login-verificacao.com
```

possui uma estrutura completamente diferente.

O domínio efetivamente controlado nesse segundo caso é:

```text
login-verificacao.com
```

Esse tipo de análise é básico, mas extremamente importante durante uma triagem.

---

# Não confiar apenas no HTTPS

Um erro comum é interpretar:

```text
HTTPS
🔒
```

como sinônimo de site legítimo.

HTTPS protege a comunicação entre cliente e servidor.

Ele não garante que o servidor seja confiável.

Um site de phishing também pode utilizar HTTPS.

Portanto:

```text
HTTPS ≠ Legítimo
```

O certificado protege a conexão.

A análise de reputação e legitimidade do domínio continua sendo necessária.

---

# Análise de anexos

Anexos devem ser tratados com cautela.

Exemplos:

```text
.exe
.dll
.js
.vbs
.ps1
.zip
.rar
.iso
.docm
.xlsm
```

Documentos aparentemente comuns também podem ser utilizados como vetor de execução.

Durante a análise, o SOC pode observar:

```text
Hash
Tipo real do arquivo
Metadados
Comportamento
Processos criados
Conexões de rede
Detecções de antivírus/EDR
```

O objetivo é descobrir:

> **"O arquivo apenas chegou ao ambiente ou chegou a ser executado?"**

Essa diferença muda completamente a gravidade do incidente.

---

# O clique é o começo da investigação

Imagine:

```text
E-mail malicioso
       ↓
Usuário clicou
```

Isso não significa necessariamente comprometimento.

Precisamos descobrir:

```text
Clicou?
   ↓
Página abriu?
   ↓
Credencial foi inserida?
   ↓
MFA foi solicitado?
   ↓
Login aconteceu?
   ↓
Arquivo foi baixado?
   ↓
Arquivo foi executado?
   ↓
Houve conexão externa?
```

Cada resposta muda a avaliação do incidente.

---

# Cenário 1 — Usuário recebeu, mas não interagiu

```text
E-mail malicioso
       ↓
Usuário reportou
       ↓
Não clicou
       ↓
Não abriu anexo
```

Nesse caso, a prioridade pode ser:

```text
Remover mensagem
Bloquear IOC
Pesquisar outros destinatários
Verificar campanha
```

Ainda existe trabalho.

Se o mesmo e-mail foi enviado para 500 usuários, o SOC precisa saber quantos ainda podem estar expostos.

---

# Cenário 2 — Usuário clicou

```text
E-mail
   ↓
Link
   ↓
Usuário clicou
```

Agora é necessário investigar:

```text
URL acessada
IP
Domínio
Horário
Endpoint
Navegador
Redirecionamentos
```

Também é importante procurar:

```text
Download
Execução
Login
Alteração de credenciais
Conexões posteriores
```

---

# Cenário 3 — Usuário informou credenciais

Esse cenário exige atenção especial.

```text
Phishing
   ↓
Página falsa
   ↓
Credencial inserida
```

A resposta deve considerar a possibilidade de comprometimento da conta.

Medidas podem incluir:

```text
Reset de senha
Revogação de sessões
Revogação de tokens
Verificação de MFA
Análise de autenticações
Verificação de regras de e-mail
Investigação de acessos
```

A simples troca de senha pode não ser suficiente se sessões ou tokens já tiverem sido comprometidos.

---

# Cenário 4 — Malware executado

Se um anexo foi executado:

```text
Phishing
   ↓
Anexo
   ↓
Execução
   ↓
Processo suspeito
```

o incidente passa a envolver também análise de endpoint.

O SOC deve investigar:

```text
Processo pai
Processo filho
Command Line
Arquivos criados
Persistência
Conexões de rede
DNS
Credenciais
Movimentação lateral
```

Nesse ponto, o phishing pode ser apenas o **vetor inicial** de um comprometimento maior.

---

# Correlação com SIEM

Um SIEM pode ajudar a conectar os eventos.

Exemplo:

```text
10:02
E-mail recebido

10:07
Usuário clicou no link

10:08
DNS para domínio suspeito

10:09
Conexão HTTPS

10:10
PowerShell executado

10:11
Arquivo criado

10:12
Conexão externa
```

Individualmente, os eventos possuem significados diferentes.

Em sequência:

```text
Phishing
   ↓
Initial Access
   ↓
Execution
   ↓
Command and Control
```

A investigação ganha uma narrativa.

---

# MITRE ATT&CK

Phishing pode estar relacionado a diferentes técnicas dependendo do cenário.

Um exemplo:

```text
T1566
Phishing
```

Com subcategorias como:

```text
T1566.001
Spearphishing Attachment

T1566.002
Spearphishing Link

T1566.003
Spearphishing via Service
```

O mapeamento deve refletir o comportamento observado.

Se o e-mail apenas foi recebido, isso é diferente de um cenário em que houve execução de código e comprometimento posterior.

---

# Detecções para phishing

Alguns use cases possíveis:

### Domínio suspeito

```text
E-mail
+
URL
+
Threat Intelligence match
```

### Campanha

```text
Mesmo domínio
+
múltiplos destinatários
+
mesmo assunto
```

### Credential Phishing

```text
Login
+
novo dispositivo
+
IP incomum
+
usuário acabou de acessar URL suspeita
```

### Malware

```text
Anexo
+
execução
+
processo suspeito
+
conexão externa
```

O objetivo é não depender de uma única regra.

---

# Investigação de campanha

Um dos pontos mais importantes em um incidente de phishing é determinar o **escopo**.

Se um usuário recebeu a mensagem, a pergunta seguinte deve ser:

> "Quem mais recebeu?"

Pesquisar:

```text
Remetente
Assunto
Message-ID
Hash do anexo
URL
Domínio
IP
```

pode revelar outros destinatários.

Exemplo:

```text
Campanha
   ↓
300 mensagens enviadas
   ↓
80 entregues
   ↓
25 abertas
   ↓
8 cliques
   ↓
2 usuários informaram credenciais
```

Agora o SOC possui uma visão real do impacto.

---

# Contenção

As ações dependem do estágio do incidente.

### E-mail não aberto

```text
Remover mensagens
Bloquear remetente/domínio
Bloquear URL
Pesquisar campanha
```

### Usuário clicou

```text
Analisar endpoint
Bloquear IOC
Verificar navegador
Pesquisar atividade posterior
```

### Credencial informada

```text
Reset de senha
Revogar sessões
Revogar tokens
Verificar MFA
Investigar autenticações
```

### Malware executado

```text
Isolar endpoint
Coletar evidências
Bloquear IOC
Analisar persistência
Investigar escopo
```

---

# Erradicação

Depois de conter o incidente, o SOC deve procurar eliminar os mecanismos utilizados pelo atacante.

Exemplos:

```text
Remover malware
Remover persistência
Bloquear domínio
Bloquear IP
Remover regras maliciosas
Revogar sessões
Rotacionar credenciais
Corrigir vulnerabilidade
```

A erradicação deve considerar não apenas o primeiro endpoint.

Se outros sistemas foram afetados, eles também precisam ser tratados.

---

# Recuperação

Após a contenção e erradicação:

```text
Restaurar sistemas
Validar credenciais
Monitorar endpoints
Acompanhar autenticações
Reativar serviços
Validar controles
```

O monitoramento pós-incidente é importante.

Um atacante pode tentar retornar utilizando outra conta ou outro mecanismo.

---

# Lições aprendidas

Depois do incidente, algumas perguntas devem ser feitas:

```text
Como o atacante conseguiu chegar ao usuário?

A mensagem passou pelos filtros?

O usuário clicou?

Havia autenticação MFA?

A conta foi comprometida?

O SIEM detectou a atividade?

O EDR detectou a execução?

Quanto tempo levou para responder?

Quantos usuários foram afetados?

Qual controle poderia ter impedido o incidente?
```

Essas respostas podem gerar novas ações.

Por exemplo:

```text
Incidente
   ↓
Gap identificado
   ↓
Nova regra
   ↓
Novo controle
   ↓
Melhor cobertura
```

---

# Playbook de resposta

Um playbook simplificado para phishing:

```text
[1] Receber denúncia
        ↓
[2] Preservar evidências
        ↓
[3] Analisar e-mail
        ↓
[4] Identificar IOCs
        ↓
[5] Pesquisar destinatários
        ↓
[6] Verificar interação
        ↓
[7] Avaliar comprometimento
        ↓
[8] Conter
        ↓
[9] Erradicar
        ↓
[10] Recuperar
        ↓
[11] Monitorar
        ↓
[12] Documentar
```

---

# Checklist do analista

```text
[ ] E-mail foi realmente recebido?

[ ] Quem enviou?

[ ] Quem recebeu?

[ ] Qual domínio foi utilizado?

[ ] SPF/DKIM/DMARC foram analisados?

[ ] Existe URL?

[ ] Existe anexo?

[ ] URL foi acessada?

[ ] Anexo foi executado?

[ ] Credenciais foram informadas?

[ ] Houve autenticação posterior?

[ ] Houve download?

[ ] Houve execução?

[ ] Existem outros destinatários?

[ ] Existem IOCs?

[ ] Existe persistência?

[ ] Existe movimentação lateral?

[ ] A conta precisa ser contida?

[ ] O endpoint precisa ser isolado?

[ ] A campanha precisa ser bloqueada?

[ ] O incidente foi documentado?
```

---

# Exemplo de incidente

## Situação

Um funcionário reporta:

```text
"Recebi um e-mail do banco pedindo atualização cadastral."
```

### Análise

O SOC identifica:

```text
Remetente:
financeiro@dominio-suspeito.com

URL:
https://login.dominio-suspeito.com

Destinatários:
42 funcionários

Threat Intelligence:
Domínio suspeito
```

### Investigação

Dos 42 usuários:

```text
42 receberam
18 abriram
7 clicaram
2 informaram credenciais
```

### Resposta

Para os dois usuários:

```text
Reset de senha
Revogação de sessões
Verificação de MFA
Análise de autenticações
```

Para todos:

```text
Remoção do e-mail
Bloqueio do domínio
Bloqueio da URL
Pesquisa de IOC
```

### Resultado

A investigação não terminou no primeiro usuário.

O SOC identificou uma **campanha de phishing** e conseguiu determinar seu alcance.

---

# O que diferencia uma boa investigação

Uma investigação fraca poderia terminar assim:

```text
"E-mail malicioso identificado e bloqueado."
```

Uma investigação mais madura documentaria:

```text
Campanha identificada
↓
42 destinatários
↓
7 interações
↓
2 possíveis comprometimentos
↓
Contas contidas
↓
IOCs bloqueados
↓
Endpoints analisados
↓
Nenhuma evidência de movimentação lateral
```

A segunda abordagem fornece uma visão muito mais clara do incidente.

Ela permite responder:

> **O que aconteceu?**

> **Quem foi afetado?**

> **Até onde chegou?**

> **O que foi feito para conter?**

> **Existe risco residual?**

---

# Conclusão

Phishing não deve ser tratado apenas como um problema de e-mail.

Ele pode ser o primeiro passo de uma cadeia muito maior:

```text
Phishing
   ↓
Roubo de credenciais
   ↓
Comprometimento de conta
   ↓
Acesso inicial
   ↓
Persistência
   ↓
Movimentação lateral
   ↓
Exfiltração
```

Por isso, a investigação precisa acompanhar o incidente **além da mensagem original**.

O trabalho do SOC não termina quando o e-mail é bloqueado.

Termina quando existe evidência suficiente para responder:

```text
O que aconteceu?
Quem foi afetado?
O atacante conseguiu acesso?
Qual foi o impacto?
O ambiente está novamente sob controle?
O que podemos melhorar para impedir que aconteça novamente?
```

A principal habilidade em um incidente de phishing não é simplesmente reconhecer um e-mail falso.

É conseguir transformar **uma mensagem suspeita em uma investigação completa de segurança**.
