---
name: skill-forge-build
description: "Escreve uma skill completa do zero com estrutura correta, frontmatter válido e instruções acionáveis. Use quando o usuário já tem um plano de skill (ou ideia clara) e quer gerar o arquivo SKILL.md. Ativar com: 'skill-forge build', 'escreve a skill', 'gera o SKILL.md', 'constrói a skill', 'cria o arquivo da skill'. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  adapted_from: "https://github.com/AgriciDaniel/skill-forge"
  version: 1.0.0-ptbr
---

# Skill Forge — Build

Gera o SKILL.md completo com frontmatter válido, triggers otimizados e instruções acionáveis. Segue o padrão Agent Skills.

## Processo em 10 Etapas

### 1. Coletar Insumos

Aceite qualquer uma destas entradas:
- Documento de plano gerado pelo `skill-forge plan`
- Descrição livre do usuário
- Skill existente para refatorar

Se a entrada for descrição livre, faça no máximo 3 perguntas antes de escrever.

### 2. Gerar Frontmatter

Estrutura obrigatória:

```yaml
---
name: nome-em-kebab-case
description: "[capacidade principal]. Use [quando/para quê]. Ativar com: [lista de 5–10 frases de trigger]. SEMPRE responde em português do Brasil (PT-BR)."
license: MIT
metadata:
  version: 1.0.0
---
```

**Regras do frontmatter:**
- `name`: kebab-case, 1–64 chars, sem "claude" ou "anthropic"
- `description`: máximo 1024 caracteres, sem tags XML, com 5–10 triggers explícitas
- Sem campos extras não documentados no padrão

### 3. Framework da Description

Use a estrutura: **capacidade + contexto de uso + triggers**

```
"[O que a skill faz de forma concreta]. Use quando [situação/intenção do usuário]. 
Ativar com: '[frase 1]', '[frase 2]', '[frase 3]', [+ mais]. 
[Comportamento especial se houver]. SEMPRE responde em PT-BR."
```

### 4. Escrever o SKILL.md Principal

Estrutura do corpo:

```markdown
# Nome da Skill — Subtítulo Descritivo

[1–2 frases explicando o que a skill entrega.]

## Gatilhos de Ativação
- Lista das frases que ativam a skill

## Processo
[Passos numerados, acionáveis, com exemplos de input/output]

## Regras Críticas
[O que nunca fazer, limites, comportamentos obrigatórios]
```

**Regras do corpo:**
- Máximo 500 linhas
- Instruções acionáveis — não vague ("faça uma análise boa" ❌ → "liste 3 pontos fortes e 3 fracos com exemplo" ✓)
- Sem tags XML
- Inclua pelo menos um exemplo concreto de input → output

### 5. Criar Sub-Skills (Tier 3–4)

Para cada sub-skill do plano:
- Arquivo próprio: `sub-skill-nome/SKILL.md`
- Frontmatter próprio com triggers específicos
- Corpo focado em um único workflow
- Orquestrador principal tem tabela de roteamento

### 6. Gerar Scripts (Tier 2+)

Se a skill precisa de operações determinísticas:
- Um arquivo por responsabilidade
- Interface CLI com `argparse`
- Output em JSON estruturado
- Sem credenciais hardcoded
- Docstring explicando o propósito

### 7. Criar Arquivos de Referência

Para conhecimento que não é instrução (dados, benchmarks, exemplos):
- Arquivo separado em `references/`
- Um tópico por arquivo
- Linkado no SKILL.md, não embutido

### 8. Definir Agentes (Tier 4)

Se precisar de agentes paralelos:
- Arquivo YAML separado em `agents/`
- Nome: 3–50 caracteres
- Description usa bloco `<example>` (única exceção ao no-XML)
- Body vira system prompt do agente

### 9. Validar

Antes de entregar, confirme:
- [ ] `name` é kebab-case sem palavras proibidas
- [ ] `description` tem 5–10 triggers e cabe em 1024 chars
- [ ] Corpo tem menos de 500 linhas
- [ ] Pelo menos um exemplo concreto
- [ ] Sem tags XML no corpo
- [ ] Nenhum `README.md` — apenas `SKILL.md`

### 10. Entregar

Output final:
- Arquivo(s) SKILL.md prontos para copiar
- Árvore de arquivos gerados
- 3 queries de teste para verificar disparo

## Regras Críticas

- Não entregue instruções vagas — toda etapa deve ter input e output definidos
- Não misture múltiplos workflows em um único SKILL.md — separe em sub-skills
- Não gere código com dependências externas desnecessárias
