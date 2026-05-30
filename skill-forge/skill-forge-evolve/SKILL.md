---
name: skill-forge-evolve
description: "Melhora uma skill existente com base em problemas identificados — triggers errados, execução incompleta, instruções vagas, ou arquitetura insuficiente. Use após um review com score baixo ou após casos de eval que falharam. Ativar com: 'skill-forge evolve', 'melhora essa skill', 'corrige a skill', 'a skill não está disparando', 'a skill dispara errado', 'a skill não entrega o que deveria', 'refina a skill', 'itera na skill'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Evolve

Melhora skills com base em falhas identificadas pelo review ou eval. Categoriza o tipo de problema e aplica o fix específico — sem refatorar o que já funciona.

## Entrada Necessária

- Skill com problema (nome, caminho ou conteúdo)
- Resultado do review e/ou eval (ou descrição do problema observado)

## Diagnóstico — 4 Tipos de Problema

### Tipo 1: Problema de Triggering

**Sintomas:**
- Skill não dispara quando o usuário usa frases do domínio
- Skill dispara em contextos errados (over-triggering)
- Usuário não sabe como chamar a skill

**Fixes:**
- Under-triggering → adicione palavras-chave do domínio na description, inclua variações de como o usuário realmente fala, adicione sinônimos
- Over-triggering → restrinja o escopo na description, adicione "não usar para X" explicitamente
- Triggers obscuros → reformule para frases que o usuário diria naturalmente (não termos técnicos)

**Teste rápido após fix:**
Gere 3 novas queries que deviam disparar e confirme que agora disparam.

### Tipo 2: Falha de Execução

**Sintomas:**
- Skill dispara mas entrega output incompleto
- Skill para no meio do workflow
- Output correto mas sem estrutura esperada

**Fixes:**
- Adicione checkpoint de validação entre etapas
- Especifique o formato exato do output esperado (com exemplo)
- Adicione instrução de fallback: "se [situação], então [alternativa]"
- Substitua instruções vagas por "faça X" com critério mensurável

**Exemplo de fix de instrução:**

Antes: "Analise o conteúdo e identifique os pontos principais."
Depois: "Liste exatamente 3 pontos fortes e 3 pontos fracos, cada um em uma linha, com exemplo concreto do texto para cada."

### Tipo 3: Gap de Arquitetura

**Sintomas:**
- Skill cobre casos A e B mas não C (que deveria cobrir)
- Skill faz duas coisas muito diferentes em um único arquivo
- Usuário precisa de roteamento que a skill não oferece

**Fixes:**
- Caso ausente → adicione seção específica ao SKILL.md (se simples) ou crie sub-skill (se workflow distinto)
- Dois workflows misturados → separe em sub-skills, promova o arquivo principal a orquestrador
- Falta de roteamento → adicione tabela de roteamento no orquestrador

**Quando promover de tier:**
- Tier 1 → 2: operação precisar de consistência que instrução não garante → adicione script
- Tier 2 → 3: segundo workflow distinto surgir → extraia em sub-skills
- Tier 3 → 4: workflows precisarem rodar em paralelo → adicione agentes

### Tipo 4: Problema de Qualidade

**Sintomas:**
- Outputs inconsistentes para o mesmo tipo de input
- Instrução ambígua que admite múltiplas interpretações
- Falta de exemplos concretos

**Fixes:**
- Substitua linguagem ambígua por "faça X, não Y" com exemplo de cada
- Adicione pelo menos um exemplo completo de input → output no SKILL.md
- Adicione seção "Regras Críticas" se não houver

## Protocolo de Iteração

1. Identifique o tipo de problema (use os 4 tipos acima)
2. Aplique o fix específico — não refatore o que não está quebrado
3. Rode `skill-forge eval` nos casos que falharam anteriormente
4. Compare resultado com iteração anterior
5. Se passou: documente o que mudou e o motivo
6. Se ainda falha: volte ao diagnóstico — pode ser outro tipo de problema combinado

**Loop de auto-correção rápida:**
Para um único caso falhando:
1. Aplique fix
2. Teste contra o caso original
3. Teste contra 3 casos que já funcionavam (check de regressão)
4. Se tudo passou: aceite a mudança

## Output do Evolve

```
PROBLEMA IDENTIFICADO: [Tipo 1/2/3/4 — descrição específica]

MUDANÇAS APLICADAS:
- [arquivo] linha [X]: [o que mudou e por quê]

SKILL.md ATUALIZADO:
[conteúdo completo atualizado]

QUERIES PARA VERIFICAR:
1. [query que deve disparar agora]
2. [query que não deve disparar]
3. [caso que falhava antes]

Próximo passo: rode skill-forge eval nesses 3 casos
```

## Regras Críticas

- Não refatore partes que funcionam — isole o fix no problema identificado
- Não empilhe múltiplos fixes de tipos diferentes em uma iteração — um problema por vez
- Sempre teste regressão: um fix de triggering pode quebrar instrução e vice-versa
