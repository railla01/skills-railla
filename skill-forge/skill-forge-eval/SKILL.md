---
name: skill-forge-eval
description: "Avalia se uma skill está funcionando corretamente rodando o mesmo prompt com e sem ela ativa, comparando os outputs. Use para confirmar que uma skill faz diferença, validar uma melhoria antes de adotar, ou testar casos específicos de uso. Ativar com: 'skill-forge eval', 'avalia essa skill', 'testa a skill', 'essa skill está funcionando', 'compara com e sem a skill', 'valida a skill', 'a skill está fazendo diferença'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Eval

Testa uma skill em condições controladas: roda o mesmo prompt com a skill ativa e sem ela, compara os outputs e gera um relatório de grading com evidências.

## Entrada Aceita

- Skill a avaliar (nome ou caminho)
- Conjunto de eval (JSON, prompt inline, ou auto-gerado a partir da skill)
- Modo: `completo` (padrão) ou `rápido` (apenas casos críticos)

## Processo

### 1. Definir o Conjunto de Eval

Se o usuário não fornecer casos de teste, gere automaticamente a partir do SKILL.md:
- 5 casos que devem disparar a skill (baseados nos triggers declarados)
- 3 casos fora do escopo (a skill não deve interferir)
- 2 edge cases (ambíguos)

Formato de cada caso:
```json
{
  "id": "caso-01",
  "prompt": "texto exato enviado ao Claude",
  "assertion": "o que o output deve conter ou fazer",
  "should_trigger": true
}
```

### 2. Execução Paralela

Para cada caso do conjunto:

**Run A — Com skill ativa:**
- Carregue a skill
- Execute o prompt exato
- Capture: output completo, tempo de resposta

**Run B — Baseline (sem skill):**
- Sem a skill carregada
- Execute o mesmo prompt exato
- Capture: output completo, tempo de resposta

### 3. Grading por Caso

Para cada par (Run A vs Run B), avalie:

| Critério | Pergunta |
|---|---|
| Assertion | O Run A atendeu ao critério definido? |
| Diferença | O Run A foi materialmente melhor que Run B? |
| Disparo correto | A skill disparou quando devia e não disparou quando não devia? |
| Regressão | O Run A foi pior que Run B em algum aspecto? |

Resultado por caso: `PASS` / `FAIL` / `PARCIAL`

### 4. Modo Blind Comparison

Quando ativado:
- Apresente Run A e Run B sem identificar qual é qual
- Peça ao usuário (ou a um agente comparador) para avaliar qual é melhor
- Registre o resultado sem revelar a origem

Útil para avaliar sem viés de expectativa.

### 5. Artefatos Gerados

**grading.json** — resultado por caso:
```json
{
  "caso-01": {
    "resultado": "PASS",
    "assertion_atendida": true,
    "skill_disparou": true,
    "diferenca_observada": "Run A estruturou em tópicos; Run B deu resposta genérica",
    "regressao": false
  }
}
```

**resumo.md** — relatório legível:
```
RESULTADO GERAL: X/Y casos passaram (X%)

CASOS QUE PASSARAM: [lista]
CASOS QUE FALHARAM: [lista com motivo]
CASOS PARCIAIS: [lista com observação]

OBSERVAÇÃO GERAL: [padrão observado nos resultados]
Próximo passo: [skill-forge evolve se falhas | skill-forge benchmark se passou]
```

## Interpretação dos Resultados

| Taxa de pass | Interpretação | Próximo passo |
|---|---|---|
| ≥ 80% | Skill funcionando bem | Ir para benchmark |
| 60–79% | Funcionando parcialmente | Evolve nos casos falhos |
| < 60% | Skill com problema estrutural | Review completo + evolve |

## Regras Críticas

- Nunca avalie um caso sem ter a assertion definida antes — avaliar "achismo" não tem valor
- Se um caso falha no baseline E na skill, o problema é do prompt, não da skill
- Reportar resultados parciais é melhor que não reportar — não bloqueie por casos incompletos
