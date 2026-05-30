---
name: skill-forge-review
description: "Audita uma skill existente com health score em 6 dimensões e lista problemas críticos com recomendações. Use para revisar qualidade de skills, identificar por que uma skill não dispara quando deveria, verificar se triggers estão corretos, ou avaliar se as instruções são acionáveis o suficiente. Ativar com: 'skill-forge review', 'revisa essa skill', 'audita a skill', 'por que essa skill não dispara', 'saúde da skill', 'health score da skill', 'o que está errado nessa skill'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Review

Audita skills existentes com health score ponderado em 6 dimensões. Identifica problemas silenciosos — triggers sobrepostos, instruções vagas, descrições que nunca disparam.

## Entrada Aceita

- Caminho do arquivo SKILL.md
- Conteúdo colado diretamente no chat
- Nome da skill (se já carregada)

## Health Score — 6 Dimensões

| Dimensão | Peso | O que avalia |
|---|---|---|
| Frontmatter | 25% | Nome, descrição, triggers, formatação |
| Instructions | 25% | Especificidade, exemplos, fluxo completo |
| Triggering | 20% | Cobertura de casos, riscos de over/under |
| Structure | 15% | Convenções de pasta, nomenclatura |
| Scripts | 10% | Qualidade do código auxiliar (se houver) |
| Disclosure | 5% | Clareza sobre o que a skill faz e não faz |

Score final: soma ponderada de 0–100. Abaixo de 70 = precisa de evolve antes de usar.

## Processo de Auditoria

### 1. Validação de Estrutura

Confirme:
- [ ] Arquivo se chama exatamente `SKILL.md` (case sensitive)
- [ ] Sem `README.md` na pasta
- [ ] Nome da pasta em kebab-case (1–64 chars)
- [ ] Sem "claude" ou "anthropic" no nome

### 2. Auditoria de Frontmatter (25%)

Verifique:
- [ ] `name` presente e em kebab-case
- [ ] `description` com 5–10 frases de trigger explícitas
- [ ] `description` com menos de 1024 caracteres
- [ ] Sem tags XML na description
- [ ] Linguagem da description é acionável, não vaga

Pontuação:
- Todos os itens OK → 25 pontos
- Triggers insuficientes (< 5) → -10
- Description acima de 1024 chars → -8
- XML na description → -5

### 3. Análise de Triggering (20%)

Gere automaticamente e avalie:

**5 queries que DEVEM disparar a skill** (casos centrais do propósito)
**5 queries que NÃO devem disparar** (fora do escopo)
**3 edge cases** (ambíguos — a skill deve lidar graciosamente)

Identifique:
- **Under-triggering**: descrição muito genérica ou sem termos do domínio → usuário não sabe como chamar a skill
- **Over-triggering**: descrição ampla demais → skill dispara em contextos errados

### 4. Qualidade das Instructions (25%)

Para cada seção do corpo:
- [ ] Cada etapa tem input e output definidos?
- [ ] Há pelo menos um exemplo concreto de entrada → saída?
- [ ] O fluxo cobre o que fazer quando algo dá errado?
- [ ] Instruções são "faça X" ou "liste Y" — não "analise bem" ou "considere"?
- [ ] Menos de 500 linhas?

### 5. Revisão de Arquitetura (15%)

Para skills multi-arquivo:
- [ ] Orquestrador tem tabela de roteamento clara?
- [ ] Cada sub-skill tem uma responsabilidade única?
- [ ] Referências cruzadas são válidas?
- [ ] Nomes de sub-skills seguem convenção?

Para skills simples (Tier 1): essa dimensão recebe nota cheia automaticamente.

### 6. Qualidade de Scripts (10%)

Se não houver scripts: nota cheia automaticamente.

Se houver:
- [ ] Docstring explicando propósito
- [ ] Interface CLI com argparse
- [ ] Output em JSON estruturado
- [ ] Sem credenciais hardcoded
- [ ] Uma responsabilidade por script

### 7. Disclosure (5%)

- [ ] A skill deixa claro o que faz E o que não faz?
- [ ] Há seção de "Regras Críticas" ou equivalente?

## Output do Review

Entregue:

```
HEALTH SCORE: [X]/100

✅ PONTOS FORTES
- [item 1]
- [item 2]

🔴 PROBLEMAS CRÍTICOS (bloqueiam uso)
- [problema] → [fix sugerido]

🟡 ALTA PRIORIDADE
- [problema] → [fix sugerido]

📋 RECOMENDAÇÕES
- [melhoria opcional]

🧪 QUERIES DE TESTE
Devem disparar:
1. "[query]"
2. "[query]"
3. "[query]"

Não devem disparar:
1. "[query]"
2. "[query]"

Próximo passo: [skill-forge evolve se score < 70 | pronta para uso se ≥ 70]
```

## Regras Críticas

- Não classifique como "pronta" com score abaixo de 70
- Problemas críticos são os que impedem a skill de funcionar — liste-os primeiro, sempre
- Não gere queries de teste genéricas — use o domínio real da skill
