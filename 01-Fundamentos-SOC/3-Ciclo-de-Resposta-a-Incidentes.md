#  Ciclo de Resposta a Incidentes

A resposta a incidentes é o processo utilizado por uma equipe de segurança para **identificar, analisar, conter e solucionar eventos que possam representar uma ameaça à organização**.

Em um ambiente real, não basta detectar que algo aconteceu. É necessário entender **o que aconteceu, qual foi o impacto, até onde o incidente chegou e quais ações devem ser tomadas para reduzir o risco**.

Um SOC utiliza esse processo para transformar um alerta inicial em uma resposta organizada e baseada em evidências.

---

## 🎯 Objetivo da resposta a incidentes

O principal objetivo é **reduzir o impacto de um incidente de segurança e restaurar o ambiente de forma segura**.

Uma resposta bem estruturada busca:

- Detectar o incidente rapidamente
- Confirmar se o alerta realmente representa uma ameaça
- Identificar sistemas e usuários afetados
- Conter a ameaça
- Remover a causa do incidente
- Restaurar os serviços afetados
- Registrar evidências e ações realizadas
- Evitar que o mesmo problema aconteça novamente

> Quanto mais rápido uma organização consegue detectar e responder a um incidente, menor tende a ser o impacto causado pelo ataque.

---

#  Etapas do ciclo de resposta

Embora diferentes organizações possam utilizar metodologias próprias, o processo normalmente pode ser dividido em algumas etapas principais:

```text
┌──────────────────────┐
│  1. Preparação       │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  2. Identificação    │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  3. Análise          │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  4. Contenção        │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  5. Erradicação      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  6. Recuperação      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  7. Lições aprendidas│
└──────────────────────┘
