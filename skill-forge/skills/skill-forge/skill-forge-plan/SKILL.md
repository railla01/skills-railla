---
name: skill-forge-plan
description: "Projeta a arquitetura de uma skill antes de escrever qualquer linha. Use quando o usuário quiser planejar uma skill nova, entender que tier de complexidade precisa, definir sub-skills necessárias, ou mapear casos de uso antes de construir. Ativar com: 'skill-forge plan', 'quero planejar uma skill', 'como estruturo essa skill', 'que arquitetura usar para'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Plan

Projeta a arquitetura completa de uma skill antes de qualquer escrita. Evita retrabalho e skills genéricas que não disparam quando deveriam.

## Processo em 6 Etapas

### 1. Descoberta de Domínio

Pergunte ao usuário:
- Qual é o propósito central desta skill?
- Quem vai usar? Em que contexto?
- Quais ferramentas ou MCPs serão necessários?
- Quais são os 3–5 casos de uso principais?

### 2. Decomposição de Casos de Uso

Para cada caso de uso identificado:
- Liste as entradas esperadas (o que o usuário fornece)
- Liste as saídas esperadas (o que a skill entrega)
- Mapeie os passos intermediários

### 3. Avaliação de Complexidade

Determine o tier com base nas respostas:

| Critério | Tier |
|---|---|
| Um workflow linear, sem ramificações | 1 |
| Workflow + operações determinísticas | 2 |
| Múltiplos workflows distintos com roteamento | 3 |
| Execução paralela, múltiplos agentes | 4 |

**Regra:** escolha o tier mínimo que atende os requisitos. Não sobre-engenharie.

### 4. Design de Arquitetura

Gere a estrutura de arquivos adequada ao tier:

**Tier 1:**
```
nome-da-skill/
└── SKILL.md
```

**Tier 2:**
```
nome-da-skill/
├── SKILL.md
└── scripts/
    └── script-principal.py
```

**Tier 3:**
```
nome-da-skill/
├── SKILL.md          ← orquestrador com tabela de roteamento
├── sub-skill-a/
│   └── SKILL.md
└── sub-skill-b/
    └── SKILL.md
```

**Tier 4:**
```
nome-da-skill/
├── SKILL.md
├── agents/
│   └── agente.yml
├── sub-skill-a/
│   └── SKILL.md
└── scripts/
    └── executor.py
```

### 5. Decomposição de Sub-Skills (Tier 3–4)

Para cada sub-skill identificada:
- Nome em kebab-case, descritivo e focado
- Uma responsabilidade clara (não misturar workflows)
- Definir se é chamada pelo usuário ou internamente pelo orquestrador

### 6. Output do Plano

Entregue um documento com:
- Nome proposto da skill (kebab-case)
- Tier escolhido e justificativa
- Lista de casos de uso com entradas e saídas
- Estrutura de arquivos
- Rascunho das frases de trigger (5–10)
- Próximo passo: rodar `skill-forge build`

## Regras Críticas

- Não avance para build sem ter os triggers definidos — eles determinam se a skill vai disparar
- Não crie Tier 3 para um único workflow — use Tier 1 ou 2
- Nomes de skills: sem "claude" ou "anthropic", sem espaços, 1–64 caracteres
