---
name: skill-forge
description: "Sistema completo para criar, revisar, evoluir e avaliar skills do Claude Code com qualidade de produção. Use quando o usuário quiser criar uma skill nova, planejar arquitetura de skill, revisar uma skill existente, avaliar se a skill está funcionando, melhorar uma skill com base em falhas, ou medir performance de skill. Ativar com: 'cria uma skill', 'quero uma skill para', 'revisa minha skill', 'essa skill não está disparando', 'como melhoro essa skill', 'avalia essa skill', 'skill-forge plan', 'skill-forge build', 'skill-forge review', 'skill-forge eval', 'skill-forge evolve', 'skill-forge benchmark'. Cobre o ciclo completo: planejamento → construção → revisão → evolução → benchmark. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
  adapted_by: "Railla Lopes — adaptação PT-BR, sem publish e convert"
---

# Skill Forge — Criador de Skills para Claude Code

Sistema completo para projetar e construir skills de produção, seguindo o padrão Agent Skills com arquitetura em 3 camadas.

> **Idioma:** Sempre responde em português do Brasil (PT-BR).

## Roteamento de Comandos

| Comando | Sub-skill | O que faz |
|---|---|---|
| `/skill-forge plan` | [skill-forge-plan](./skill-forge-plan/SKILL.md) | Projeta arquitetura antes de escrever |
| `/skill-forge build` | [skill-forge-build](./skill-forge-build/SKILL.md) | Escreve a skill com estrutura correta |
| `/skill-forge review` | [skill-forge-review](./skill-forge-review/SKILL.md) | Audita com health score em 6 dimensões |
| `/skill-forge eval` | [skill-forge-eval](./skill-forge-eval/SKILL.md) | Testa a skill com e sem ela ativa |
| `/skill-forge evolve` | [skill-forge-evolve](./skill-forge-evolve/SKILL.md) | Melhora com base em falhas identificadas |
| `/skill-forge benchmark` | [skill-forge-benchmark](./skill-forge-benchmark/SKILL.md) | Mede performance ao longo do tempo |

## Fluxo Recomendado

```
plan → build → review → [eval → evolve] → benchmark
```

- Para skill nova: comece pelo `plan`
- Para skill existente com problema: comece pelo `review`
- Para confirmar melhoria: rode `eval` antes e depois do `evolve`

## Padrões de Qualidade

Toda skill criada por este sistema deve atender:

- Frontmatter YAML válido com `name` e `description`
- Nome em kebab-case (1–64 caracteres, sem "claude" ou "anthropic")
- Descrição com 5–10 frases de trigger, máximo 1024 caracteres
- Corpo com instruções acionáveis (não vagas), máximo 500 linhas
- Sem tags XML no corpo
- Sem README.md — apenas SKILL.md

## Tiers de Complexidade

| Tier | Estrutura | Quando usar |
|---|---|---|
| 1 | Arquivo único SKILL.md | Workflow simples, um caso de uso |
| 2 | SKILL.md + scripts | Operações que precisam de determinismo |
| 3 | Orquestrador + sub-skills | Múltiplos workflows distintos |
| 4 | Ecossistema com agentes | Execução paralela, alta complexidade |

## Sem Essas Funcionalidades

Esta versão não inclui:
- `publish` — empacotamento para distribuição pública
- `convert` — conversão para OpenAI, Cursor ou outros formatos
