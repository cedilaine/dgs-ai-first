#### Exercício 3.1 — Revisão crítica das respostas do assist

**Tarefa:**

1. Avalie cada resposta por conta própria: correta, parcialmente correta, ou incorreta? Justifique com base no Anexo A.

1 Correta
2 Correta
3 Correta
4 Incorreta
5 Correta
6 Incorreta

2. Depois, use o **Claude** como segundo avaliador e compare com a sua avaliação.
   Resultado por resposta

# Minha avaliação Justificativa (Anexo A)

1 Correta Prazo de 7 dias úteis está correto (anexo-a-documentacao-simulada-novatech.md:35); abrir chamado no portal e anexar fotos também está alinhado (anexo-a-documentacao-simulada-novatech.md:49, anexo-a-documentacao-simulada-novatech.md:50).
2 Correta SLA Silver de resolução em até 48h úteis está explícito na tabela (anexo-a-documentacao-simulada-novatech.md:183).
3 Correta Carga perigosa classe 3 (classe 1 a 6) não é elegível para devolução padrão (anexo-a-documentacao-simulada-novatech.md:41); deve escalar para Gestão de Riscos (anexo-a-documentacao-simulada-novatech.md:45).
4 Incorreta A resposta inventa política formal de danos sem fonte; o próprio Anexo aponta gap formal para danos (anexo-a-documentacao-simulada-novatech.md:260).
5 Correta Não existe tier Enterprise além de Gold/Silver/Standard (anexo-a-documentacao-simulada-novatech.md:176).
6 Incorreta Afirmativa vem de FAQ informal (anexo-a-documentacao-simulada-novatech.md:237, anexo-a-documentacao-simulada-novatech.md:238), que não é validado por Compliance (anexo-a-documentacao-simulada-novatech.md:216); e há nota explícita de ausência de base formal para esse processo (anexo-a-documentacao-simulada-novatech.md:257).
Comparação com sua avaliação (cenario-3.md exercício 1)
Sua avaliação em cenario-3.md:7 classifica 1,2,3,5 como corretas e 4,6 como incorretas (cenario-3.md:7-12).

Concordância: 100% (6 de 6).

3. Para as respostas com problema, classifique o tipo de erro (alucinação, fonte não confiável, informação incompleta) e proponha um ajuste de produto (prompt, interface, ou pipeline) para preveni-lo.

Respostas com problema: tipo de erro e ajuste de produto

# Tipo de erro Ajuste recomendado

4 Alucinação Pipeline: bloquear saída sem fonte formal obrigatória e retornar resposta segura padrão quando não houver documento normativo recuperado. Prompt: instrução explícita “não inferir política ausente”. Interface: badge “sem base documental formal” com ação de escalar.
6 Fonte não confiável Pipeline: score de confiabilidade por tipo de documento (POL/PROC/SLA > FAQ), com regra de bloqueio para temas críticos (carga perigosa) quando só houver FAQ. Interface: exibir classificação da fonte (formal/informal) e exigir HITL para envio ao usuário final quando a fonte for informal. Prompt: priorizar documentos normativos e, na ausência, responder com incerteza + encaminhamento.

#### Exercício 3.2 — Harness de produto para melhoria contínua

**Tarefa:**
Usando o **Claude**, projete um harness de produto que cubra:

1. **Processo de feedback:** como o feedback do atendente vira melhoria (novo documento? ajuste de prompt? reindexação?).

   (atendente -> melhoria em produção)
   Coleta estruturada no front/bot:
   Campos obrigatórios: query, answer, source_document, confidence_score, tipo_problema, impacto, comentario.
   tipo_problema padronizado: factual, fonte, completude, linguagem, guardrail, outro.
   Triagem diária (Product Specialist + QA):
   Classificar cada caso como: erro de conteúdo, erro de retrieval, erro de política/guardrail, erro de UX.
   Priorizar por risco: alto (segurança/compliance), médio (informação incorreta), baixo (clareza/formatação).
   Roteamento por causa raiz:
   Se faltou base documental: criar/atualizar documento oficial.
   Se documento existe mas não foi recuperado: ajustar chunking, metadados, embeddings, filtros, e executar reindexação.
   Se resposta veio incompleta/ambígua com fonte correta: ajustar prompt e exemplos.
   Se violou regra crítica: ajustar guardrail determinístico (bloqueio em código), não só prompt.
   Implementação e validação:
   Mudança vai para branch com ticket de rastreabilidade (feedback_id -> fix_id).
   Todo fix entra com teste de regressão do caso original (vira “golden case”).
   Fechamento do loop:
   Publicar status para atendente: recebido, em análise, corrigido, não reproduzido.
   Medir eficácia: taxa de reincidência do mesmo erro em 7/14/30 dias.

2. **Regression testing de produto:** antes de mudar o prompt ou adicionar documentos, como verificar que as respostas existentes não pioraram E que os guardrails do cenário 2 continuam sendo respeitados.

   (antes de produção)
   Suíte mínima obrigatória:
   Golden set com perguntas canônicas por domínio (devolução, SLA, frete especial, carga perigosa).
   Guardrail set com casos negativos (ex.: sem fonte, tema sensível, pergunta fora de escopo).
   Ambiguity set com perguntas incompletas para validar comportamento “QUANDO EM DÚVIDA”.
   Critérios de aprovação (gates):
   Zero regressão em casos críticos de guardrail.
   Não piorar precisão factual nos casos canônicos.
   Cobertura de fonte: respostas com source_document válido em 100% dos casos obrigatórios.
   Em dúvida, o assistente deve pedir clarificação ou escalar, nunca inventar.
   Verificações automáticas por release:
   Validar structured output (schema obrigatório).
   Validar fonte contra catálogo de documentos permitidos.
   Bloquear termos/combinações de alto risco definidos no NÃO DEVE.
   Comparar versão candidata vs baseline (A/B offline) com relatório de diffs.
   Estratégia de rollout:
   Staging -> canário (5-10%) -> produção total.
   Rollback automático se ultrapassar limiar de erro/escalação.

3. **Ponto de human-in-the-loop:** quais mudanças no assistente exigem aprovação humana antes de ir a produção, e quem aprova.

   (HITL)
   Mudanças que exigem aprovação humana antes de produção:

Alteração de system prompt ou regras de guardrail.
Mudança de modelo/base model.
Mudança em política de priorização de fontes (ex.: formal vs FAQ).
Reindexação ampla ou inclusão de documentos normativos sensíveis.
Qualquer mudança que afete temas críticos (carga perigosa, compliance, SLA contratual).
Aprovadores (quem aprova):

Product Specialist: aderência de produto e experiência.
Tech Lead: segurança técnica, regressão e observabilidade.
Compliance/Operações: temas regulatórios/políticas formais.
Delivery Manager: go/no-go final em janela de release.
Regra prática de decisão:

Se impacta guardrail DEVE/NÃO DEVE: aprovação conjunta Product Specialist + Tech Lead + Compliance.
Se impacta somente UX/completude sem risco regulatório: Product Specialist + Tech Lead

**Entregável:** O documento do harness de produto.

Template Operacional — Exercício 3.2 (Harness de Produto)
Preencha os campos entre [ ].
Versão: [vX.Y] | Data: [dd/mm/aaaa] | Responsável: [nome]

1. Objetivo e Escopo
   Objetivo: garantir evolução do assistente sem regressão de qualidade, preservando guardrails DEVE / NÃO DEVE / QUANDO EM DÚVIDA.
   Escopo desta release: [prompt | documentos | retrieval | guardrails | modelo].

2. Processo de Feedback (atendente -> melhoria)
   Coleta estruturada
   Campos mínimos: pergunta, resposta, source_document, confidence_score, tipo_erro, impacto, comentário.
   Tipos de erro: factual, fonte, incompleta, guardrail, linguagem, outro.
   Triagem e causa raiz
   Dono: [Product Specialist/QA].
   SLA de triagem: [ex: 24h úteis].
   Classificação de causa: documentação, prompt, retrieval/chunking, guardrail determinístico, UX/interface.
   Ação corretiva
   Se faltou fonte formal: [criar/atualizar documento oficial].
   Se retrieval falhou: [ajustar chunking/metadados/filtros + reindexar].
   Se resposta incompleta: [ajuste de prompt + exemplos].
   Se violação crítica: [bloqueio por regra determinística].
   Fechamento do loop
   Status ao atendente: recebido, em análise, corrigido, descartado.
   Métrica de eficácia: reincidência em [7/14/30] dias.
3. Regression Testing de Produto (pré-release)
   Suítes obrigatórias
   Golden set: perguntas canônicas por domínio.
   Guardrail set: casos negativos e temas sensíveis.
   Ambiguity set: perguntas incompletas para validar “QUANDO EM DÚVIDA”.
   Gates de aprovação
   Zero regressão em guardrails críticos.
   Não piorar precisão factual vs baseline.
   100% das respostas obrigatórias com source_document válido.
   Em dúvida, resposta deve pedir clarificação ou escalar (não inventar).
   Comparação de versão
   Rodar baseline vs candidata.
   Registrar diffs: [quais respostas mudaram, risco, decisão].
4. HITL (Human-in-the-Loop)
   Mudanças que exigem aprovação humana pré-produção:

Alteração de system prompt ou regras de guardrail.
Mudança de modelo.
Mudança na priorização de fontes (formal x informal).
Reindexação ampla ou inclusão de documento normativo sensível.
Mudança em fluxos de tema crítico (ex: carga perigosa, SLA contratual).
Aprovadores obrigatórios:

Product Specialist.
Tech Lead.
Compliance/Operações (quando tema regulatório).
Delivery Manager (go/no-go final). 5) Checklist de Release (Bloqueante vs Desejável)
Item Critério Tipo Status (OK/NOK) Evidência Responsável
1 Structured output válido (schema) Bloqueante [ ] [link/execução] [ ]
2 Guardrails críticos sem regressão Bloqueante [ ] [relatório testes] [ ]
3 Fonte formal obrigatória em respostas críticas Bloqueante [ ] [amostra/metric] [ ]
4 HITL ativo para baixa confiança/tema sensível Bloqueante [ ] [fluxo + print/log] [ ]
5 Rollback testado (procedimento executável) Bloqueante [ ] [runbook + teste] [ ]
6 Dashboard de métricas atualizado Desejável [ ] [link dashboard] [ ]
7 Relatório semanal preparado Desejável [ ] [template] [ ]
8 Backlog de melhorias priorizado Desejável [ ] [board] [ ] 6) Decisão de Go/No-Go
Decisão: [GO | NO-GO]
Justificativa objetiva: [2-4 linhas com base no checklist]
Riscos residuais aceitos: [lista curta]
Plano de rollback (trigger -> responsável -> ação): [texto curto]
