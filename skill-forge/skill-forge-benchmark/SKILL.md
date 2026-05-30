---
name: skill-forge-benchmark
description: "Mede e compara performance de uma skill ao longo de múltiplas iterações, identificando regressões e melhorias com dados. Use quando quiser saber se uma skill melhorou entre versões, comparar duas versões de uma skill, monitorar estabilidade de um conjunto de skills, ou ter dados para tomar decisão de aceitar ou rejeitar uma mudança. Ativar com: 'skill-forge benchmark', 'benchmarka a skill', 'compara versões da skill', 'a skill regrediu', 'métricas da skill', 'performance da skill entre versões'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Benchmark

Mede performance de skills ao longo de iterações com consistência estatística. Transforma "parece melhor" em "é X% melhor nos casos Y e Z".

## Quando Usar

- Após `skill-forge evolve` para confirmar que a melhoria foi real
- Para comparar duas versões de uma skill antes de decidir qual manter
- Para detectar regressão depois de uma mudança aparentemente não relacionada

**Não use** para avaliação de uma única execução — use `skill-forge eval` para isso. Benchmark é para comparação entre versões.

## Configuração

Defina antes de rodar:

```json
{
  "skill": "nome-ou-caminho-da-skill",
  "versao_anterior": "opcional — caminho ou descrição da versão anterior",
  "dataset_eval": "caminho do JSON ou 'auto' para gerar automaticamente",
  "trials": 3,
  "thresholds": {
    "pass_rate_minima": 0.75,
    "tokens_maximo": 2000
  }
}
```

## Processo

### 1. Execução por Trials

Para cada caso do dataset, rode `trials` vezes (padrão: 3):
- Captura: resultado (PASS/FAIL), tempo de resposta, tokens usados
- Motivo: 1 execução pode ter variância — 3 dá média confiável

### 2. Agregação de Métricas

Por caso e por versão:

| Métrica | Como calcular |
|---|---|
| Pass rate | passes / total de trials |
| Tempo médio | média dos tempos de resposta |
| Tokens médios | média dos tokens usados |
| Desvio padrão | variância entre trials |

### 3. Comparação Entre Versões

Se houver versão anterior para comparar:

| Dimensão | Versão Anterior | Versão Atual | Delta |
|---|---|---|---|
| Pass rate geral | X% | Y% | +/- Z% |
| Tempo médio | Xs | Ys | +/- Zs |
| Tokens médios | X | Y | +/- Z |

Flags automáticas:
- 🔴 **Regressão**: caso que passava antes agora falha
- 🟢 **Melhoria**: caso que falhava antes agora passa
- 🟡 **Variância alta**: desvio padrão > 30% da média (resultado instável)

### 4. Relatório Final

```
BENCHMARK — [nome da skill] — [data]

RESULTADO GERAL
Versão atual: X/Y casos passaram (X%)
Versão anterior: X/Y casos passaram (X%) [se disponível]
Delta: +/- X%

THRESHOLDS
Pass rate mínima (75%): ✅ ATENDIDA / ❌ NÃO ATENDIDA
Tokens máximos (2000): ✅ ATENDIDA / ❌ NÃO ATENDIDA

MELHORIAS DETECTADAS
- [caso]: FAIL → PASS

REGRESSÕES DETECTADAS
- [caso]: PASS → FAIL  ← ATENÇÃO

INSTABILIDADE (alta variância)
- [caso]: desvio padrão de X%

DECISÃO RECOMENDADA
✅ Aceitar versão atual / ❌ Reverter para versão anterior / ⚠️ Investigar regressões antes de decidir

Próximo passo: [skill-forge evolve nas regressões | pronto para uso se aprovado]
```

### 5. Gate de Aprovação

A versão atual só é considerada aprovada se:
- Pass rate ≥ threshold configurado (padrão: 75%)
- Sem regressões em casos que passavam antes
- Sem flags de instabilidade alta

Se não aprovada: recomende `skill-forge evolve` nos casos específicos antes de re-benchmarkar.

## Regras Críticas

- Não aceite uma versão com regressão sem investigar — um caso piorando pode revelar problema estrutural
- Variância alta em um caso indica instrução ambígua — o evolve resolve na maioria das vezes
- Benchmark sem versão anterior tem valor limitado — é uma foto, não uma comparação. Use como linha de base para próximas iterações
