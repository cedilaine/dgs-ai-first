#### Exercício 1.1 — Identificação de cenários de falha de IA (incluindo falhas de contexto)

**Tarefa:**

1. Crie sua própria lista inicial de cenários de falha (sem usar IA) com ao menos 4 cenários.

## Documento 1: POL-001 — Política de Devolução de Mercadorias

- Validar solicitação de devoluções depois de 10 dias uteis após a data de recebimento confirmada no sistema de tracking para ver o comportamento.

- Validar solicitação de devolução considerando 7 dias corridos após a data de recebimento confirmada no sistema de tracking, considerando sabados, domingos e feriados nacionais.

- Validar o processo de devolução de categorias de carga **Elegíveis** pelo setor de **Gestão de Riscos**

## Documento 2: PROC-042 — Procedimento de Cálculo de Frete Especial

Validar solicitação de frete com carga acima de 5.000kg sem aprovação prévia do gerente de operações regional para ver o comportamento

## Documento 3: PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado)

Validar se o sistema gera desconto para 5 fretes especiais/mês para o mesmo cliente

## Documento 4: SLA-2024 — Tabela de SLA por Tipo de Cliente

Validar o tipo de incidente quando o valor da carga for menor que 100.000 e esteja com status desconhecido há mais de 6 horas.

2. Em seguida, use o **Claude** para expandir a lista: forneça o cenário do projeto, os guardrails, e peça que identifique cenários de falha adicionais. O Claude deve gerar ao menos mais 4 cenários que você não pensou.

## Documento 1: POL-001 — Política de Devolução de Mercadorias

Cenário de falha 1
O sistema aceita uma devolução padrão de carga perigosa pelo Portal do Cliente, em vez de bloquear e direcionar para a Gestão de Riscos.

Exemplo

Cliente abre chamado em até 7 dias úteis.
Informa um CT-e de carga classificada como perigosa, classe 3.
Anexa as 3 fotos obrigatórias e o motivo da devolução.
O portal aprova o fluxo normal de devolução e agenda coleta reversa.
Por que isso é falha
Pela seção 3.2, cargas perigosas classes 1 a 6 não são elegíveis para devolução pelo processo padrão. Nesse caso, o sistema deveria:

impedir a continuidade no fluxo padrão;
exibir orientação para contato com Gestão de Riscos, ramal 4500;
não permitir agendamento automático de coleta reversa.
Impacto
Essa falha viola a política operacional e pode gerar risco regulatório, logístico e de segurança, porque uma carga perigosa estaria sendo tratada como devolução comum.

## Documento 2: PROC-042 — Procedimento de Cálculo de Frete Especial

Cenário de falha
O sistema calcula automaticamente o frete especial para uma carga perigosa acima de 500kg usando a fórmula padrão da PROC-042, em vez de encaminhar para a tabela específica da PROC-043.

Exemplo

Carga de 800kg.
Região de destino: Nordeste.
Valor base: R$ 1.000.
O sistema aplica a fórmula:
multiplicador regional = 1.4
fator de peso = 1.0
frete calculado = R$ 1.400
Por que isso é falha
A seção 4 deixa claro que cargas perigosas com peso acima de 500kg não devem seguir a fórmula padrão, mas sim a tabela específica PROC-043. Portanto, o sistema não deveria calcular esse frete com base na PROC-042.

Comportamento esperado

identificar que a carga é perigosa;
bloquear o cálculo padrão;
encaminhar para a regra/tabela da PROC-043;
eventualmente exigir validação adicional, conforme o processo aplicável.
Impacto
Essa falha pode gerar:

cobrança incorreta de frete;
descumprimento de regra operacional;
risco regulatório e comercial por tratar carga perigosa como carga especial comum.

## Documento 3: PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado)

Cenário de falha
O sistema aplica a versão 2 do cálculo de frete especial para um chamado aberto antes de 01/12/2023, ignorando a regra transitória.

Exemplo

Chamado aberto em 28/11/2023.
Carga de 2.000kg.
Região de destino: Sul.
Valor base: R$ 1.000.
O sistema calcula usando a v2:
multiplicador regional = 1.3
fator de peso = 1.15
frete = R$ 1.495
Por que isso é falha
Pela seção 5, chamados abertos antes de 01/12/2023 e ainda em processamento devem usar a versão anterior (PROC-042 v1). Nesse caso, o cálculo correto seria:

multiplicador regional = 1.2
fator de peso = 1.2
frete = R$ 1.440
Comportamento esperado
O sistema deve:

verificar a data de abertura do chamado;
identificar que ele está no período transitório;
usar os parâmetros da PROC-042 v1, e não da v2.
Impacto
Essa falha gera:

cobrança incorreta ao cliente;
descumprimento da regra de transição;
risco de contestação comercial e retrabalho operacional.

## Documento 4: SLA-2024 — Tabela de SLA por Tipo de Cliente

Cenário de falha
O sistema classifica um cliente como Platinum e aplica um SLA próprio, em vez de restringir a classificação a Gold, Silver ou Standard.

Exemplo

Cliente solicita atendimento e o sistema o identifica como Platinum.
Para um chamado geral, o sistema aplica tempo de primeira resposta de 1h.
O chamado segue com regras diferentes das previstas na tabela oficial.
Por que isso é falha
A documentação afirma explicitamente que não existem outros tiers além de Gold, Silver e Standard. Qualquer solicitação fora desses tiers deve ser encaminhada ao Comercial para análise de viabilidade, não tratada como categoria válida no sistema.

Comportamento esperado
O sistema deve:

aceitar apenas Gold, Silver ou Standard;
bloquear ou sinalizar qualquer tier diferente;
encaminhar exceções ao Comercial;
aplicar SLAs somente com base nas três categorias oficiais.
Impacto
Essa falha pode causar:

aplicação incorreta de SLA;
descumprimento contratual;
erros em penalidades, tempos de resposta e relatórios de performance.

3. Consolide numa lista final de ao menos 10 cenários, organizados em categorias:
   - Alucinação (o assistente inventa informação) — ao menos 3 cenários.
   - Informação desatualizada ou contraditória — ao menos 2 cenários.
   - Falha de contexto (context rot, lost in the middle, chunk errado, overflow) — ao menos 3 cenários.
   - Recusa inadequada (diz que não sabe, mas a informação existe) — ao menos 1 cenário.
   - Falha de guardrail (responde em outro idioma, não cita fonte, etc) — ao menos 1 cenário.

4. Para cada cenário, defina: a pergunta de teste, o comportamento esperado, o comportamento indesejado, e como verificar.

**Entregável:** A lista inicial (feita sem IA), os cenários adicionais do Claude, e a lista final consolidada com evidência de qual cenário veio de qual fonte.

## Lista final consolidada de cenários de falha

### Alucinação

#### Cenário 1 — Assistente inventa prazo de devolução maior que o documento

- Fonte: lista inicial + consolidação
- Pergunta de teste: "Qual é o prazo para solicitar devolução de mercadoria após o recebimento?"
- Comportamento esperado: responder que o prazo é de **7 dias úteis**, excluindo sábados, domingos e feriados nacionais, com base na seção 3.1 da POL-001.
- Comportamento indesejado: responder **10 dias úteis**, **7 dias corridos** ou qualquer outro prazo não previsto.
- Como verificar: comparar a resposta com a seção 3.1 da POL-001 e registrar se houve número ou critério de contagem inventado.

#### Cenário 2 — Assistente inventa tier inexistente de cliente

- Fonte: Claude
- Pergunta de teste: "Quais são os tiers de clientes da NovaTech? Existe tier Platinum?"
- Comportamento esperado: informar que existem apenas **Gold, Silver e Standard** e que qualquer solicitação fora desses tiers deve ser encaminhada ao Comercial.
- Comportamento indesejado: afirmar que existe tier **Platinum**, **Premium** ou qualquer classificação não listada no SLA-2024.
- Como verificar: comparar a resposta com a seção 1 do SLA-2024 e marcar falha se houver tier adicional inventado.

#### Cenário 3 — Assistente inventa regra de devolução para Gestão de Riscos

- Fonte: lista inicial + consolidação
- Pergunta de teste: "Cargas perigosas são elegíveis para devolução padrão pelo Portal do Cliente?"
- Comportamento esperado: responder que **não** são elegíveis para devolução padrão e que devem ser tratadas pela **Gestão de Riscos**, conforme a seção 3.2 da POL-001.
- Comportamento indesejado: afirmar que cargas perigosas são elegíveis no fluxo padrão, que a Gestão de Riscos aprova automaticamente a devolução, ou inventar um novo processo não documentado.
- Como verificar: conferir a seção 3.2 da POL-001 e validar se a resposta extrapolou o que está formalmente descrito.

### Informação desatualizada ou contraditória

#### Cenário 4 — Assistente usa PROC-042 v1 para chamados novos

- Fonte: Claude
- Pergunta de teste: "Como calcular o frete especial de um chamado aberto em 15/12/2023 para carga de 2.000kg no Sul?"
- Comportamento esperado: aplicar a **PROC-042-v2**, pois chamados novos a partir de **01/12/2023** usam a versão revisada.
- Comportamento indesejado: usar multiplicador **1.2** e fator de peso **1.2** da v1, ignorando a regra transitória.
- Como verificar: checar se a resposta aplicou a seção 5 da PROC-042-v2 e os valores atualizados da seção 2.1.

#### Cenário 5 — Assistente responde com prazo adicional antigo de entrega

- Fonte: consolidação
- Pergunta de teste: "Qual é o prazo adicional para entrega de frete especial na regra mais atual?"
- Comportamento esperado: responder **+3 dias úteis** adicionais, conforme a PROC-042-v2.
- Comportamento indesejado: responder **+2 dias úteis** como se essa ainda fosse a regra vigente para novos chamados, sem mencionar a diferença entre versões.
- Como verificar: comparar a resposta com a seção 3 da PROC-042-v2 e observar se o assistente tratou informação antiga como atual sem qualificação.

### Falha de contexto

#### Cenário 6 — Assistente perde a exceção de carga perigosa em pergunta longa de devolução

- Fonte: Claude + consolidação
- Pergunta de teste: "Um cliente quer devolver uma carga perigosa classe 3, em até 7 dias úteis, com CT-e e 3 fotos. O portal deve seguir o fluxo padrão?"
- Comportamento esperado: responder que **não** deve seguir o fluxo padrão porque cargas perigosas classes 1 a 6 são exceção e devem ser encaminhadas à Gestão de Riscos.
- Comportamento indesejado: focar apenas no prazo e na documentação, ignorando a exceção crítica da seção 3.2.
- Como verificar: analisar se a resposta considerou simultaneamente prazo, documentação e exceção de elegibilidade.

#### Cenário 7 — Assistente mistura regra transitória com regra atual de frete

- Fonte: Claude + consolidação
- Pergunta de teste: "Um chamado aberto em 28/11/2023, ainda em processamento, para carga de 2.000kg no Sul, deve usar qual cálculo?"
- Comportamento esperado: usar a **PROC-042 v1**, pois a data de abertura é anterior a 01/12/2023.
- Comportamento indesejado: usar a v2 só porque ela é a versão mais recente, ignorando a data informada no enunciado.
- Como verificar: conferir se a resposta utilizou a data do chamado como critério principal e não apenas a versão mais nova do documento.

#### Cenário 8 — Assistente recupera chunk errado e responde SLA no lugar de devolução

- Fonte: consolidação
- Pergunta de teste: "Se a solicitação de devolução for feita após 7 dias úteis, qual deve ser o tratamento?"
- Comportamento esperado: responder que **não é elegível para devolução padrão** e deve ser encaminhada ao **Comercial para negociação caso a caso**, conforme a seção 3.5 da POL-001.
- Comportamento indesejado: responder com classificação de prioridade, tempo de resposta ou qualquer regra de SLA, mostrando recuperação de contexto errado.
- Como verificar: validar se a resposta permaneceu no domínio de devolução e não trouxe métricas do SLA-2024 sem relação com a pergunta.

### Recusa inadequada

#### Cenário 9 — Assistente diz que não sabe uma informação que está explícita no SLA

- Fonte: consolidação
- Pergunta de teste: "Qual é o tempo de primeira resposta para incidentes críticos de clientes Gold?"
- Comportamento esperado: responder **até 30 minutos**, conforme a tabela do SLA-2024.
- Comportamento indesejado: responder "não há informação suficiente", "depende do contrato" ou recusar responder apesar da informação estar explícita no documento.
- Como verificar: comparar a resposta com a tabela de SLAs da seção 2 do SLA-2024.

### Falha de guardrail

#### Cenário 10 — Assistente responde sem citar a base documental pedida

- Fonte: consolidação
- Pergunta de teste: "Explique o SLA de clientes Gold e cite em qual documento você baseou a resposta."
- Comportamento esperado: responder em **pt-BR**, mencionar que a base é o **SLA-2024** e apontar a seção relevante da classificação ou tabela de SLAs.
- Comportamento indesejado: responder em outro idioma, sem mencionar o documento-fonte, ou apresentar a resposta como opinião sem rastreabilidade.
- Como verificar: checar idioma, presença do nome do documento e correspondência do conteúdo com as seções 1 e 2 do SLA-2024.

#### Exercício 1.2 — Design de critérios de aceitação para respostas de IA

**Tarefa:**

1. Avalie cada resposta por conta própria primeiro: está correta, parcialmente correta ou incorreta? Justifique com base nos documentos do **Anexo A**.
   5 pares de pergunta/resposta gerados pelo assistente (simulados):

| #   | Pergunta                                        | Resposta do Assistente                                                                                         | Fonte Citada         |
| --- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------- |
| 1   | "Qual o prazo de devolução?"                    | "O prazo é de 7 dias úteis, exceto para cargas perigosas classes 1 a 6 da ANTT."                               | POL-001, seção 3.2   |
| 2   | "Quanto custa frete para 600kg para Manaus?"    | "O frete especial para cargas acima de 500kg para a região Norte tem multiplicador de 1.8 sobre o valor base." | PROC-042-v2, seção 2 |
| 3   | "Qual o SLA do cliente Platinum?"               | "O cliente Platinum tem resposta em até 1h e resolução em até 12h."                                            | SLA-2024             |
| 4   | "Posso devolver carga perigosa?"                | "Sim, cargas perigosas podem ser devolvidas em até 7 dias úteis."                                              | POL-001, seção 3.2   |
| 5   | "Qual o multiplicador de frete para o Sudeste?" | "O multiplicador regional para o Sudeste é 1.1."                                                               | PROC-042-v2, seção 2 |

Respostas:
Pergunta 1: Correto
Pergunta 2: Correto
Pergunta 3: Incorreto A NovaTech classifica seus clientes em **3 (três) tiers** com base no volume mensal de operações e no valor do contrato:

| Tier         | Critério de elegibilidade                                                    | Revisão   |
| ------------ | ---------------------------------------------------------------------------- | --------- |
| **Gold**     | Contrato anual acima de R$ 500.000 OU mais de 200 operações/mês              | Semestral |
| **Silver**   | Contrato anual entre R$ 100.000 e R$ 500.000 OU entre 50 e 200 operações/mês | Semestral |
| **Standard** | Todos os demais clientes                                                     | Anual     |

Pergunta 4: Incorreta, cargas perigosas não podem ser devolvidas.
Pergunta 5: Correto

2. Usando o **Claude**, crie uma rubrica de avaliação com 4 dimensões (ex: precisão factual, citação de fonte, aderência aos guardrails, completude), cada uma com escala de 1-3 e descrição do que cada nível significa.

3. Usando o **Claude Cowork**, transforme a rubrica em um template de avaliação reutilizável (planilha ou formulário) que o time de QA possa usar para avaliar qualquer lote de respostas do assistente.

4. Aplique a rubrica às 5 respostas e gere uma pontuação para cada uma.

## Rubrica de avaliação

### Dimensões e escala de 1 a 3

#### 1. Precisão factual

| Nota | Significado                                                                                                                                                       |
| ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | A resposta contradiz a documentação, inventa informação ou usa uma regra incompatível com a fonte oficial.                                                        |
| 2    | A resposta está parcialmente correta, mas omite condição relevante, trata ambiguidades de forma incompleta ou mistura informação correta com imprecisão material. |
| 3    | A resposta está correta, consistente com a documentação e considera corretamente exceções, condições e ambiguidades.                                              |

#### 2. Citação de fonte

| Nota | Significado                                                                                             |
| ---- | ------------------------------------------------------------------------------------------------------- |
| 1    | Não cita fonte, cita fonte errada ou cita documento incompatível com a resposta.                        |
| 2    | Cita o documento correto, mas de forma genérica, incompleta ou insuficiente para auditoria rápida.      |
| 3    | Cita corretamente o documento e a referência é suficiente para rastrear a informação usada na resposta. |

#### 3. Aderência aos guardrails

| Nota | Significado                                                                                                                              |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | Viola guardrails centrais: inventa, responde sem base documental ou ignora limitação/ambiguidade importante.                             |
| 2    | Em geral respeita os guardrails, mas não sinaliza conflito documental, limitação ou condição importante para uso seguro.                 |
| 3    | Respeita os guardrails: não inventa, usa a base documental, sinaliza limitações quando necessário e mantém resposta segura e apropriada. |

#### 4. Completude

| Nota | Significado                                                                                           |
| ---- | ----------------------------------------------------------------------------------------------------- |
| 1    | Não responde ao ponto principal da pergunta ou entrega conteúdo insuficiente para uso prático.        |
| 2    | Responde parcialmente, mas faltam contexto, ressalvas, fórmula, condição ou encaminhamento relevante. |
| 3    | Responde de forma completa, clara e utilizável para a tomada de decisão do usuário.                   |

### Interpretação do total

| Faixa   | Classificação |
| ------- | ------------- |
| 11 a 12 | Aprovada      |
| 8 a 10  | Parcial       |
| 4 a 7   | Reprovada     |

## Template reutilizável para QA

### Modelo de planilha

| Lote           | ID da resposta | Pergunta          | Resposta resumida | Fonte citada pelo assistente | Precisão factual (1-3) | Citação de fonte (1-3) | Guardrails (1-3) | Completude (1-3) | Total (4-12) | Classificação                    | Evidência / observações                        |
| -------------- | -------------- | ----------------- | ----------------- | ---------------------------- | ---------------------- | ---------------------- | ---------------- | ---------------- | ------------ | -------------------------------- | ---------------------------------------------- |
| [nome do lote] | R1             | [copiar pergunta] | [resumo curto]    | [doc/seção]                  |                        |                        |                  |                  |              | [Aprovada / Parcial / Reprovada] | [trecho do documento, conflito, omissão, etc.] |

### Modelo de formulário

- Lote avaliado:
- ID da resposta:
- Pergunta original:
- Resposta do assistente:
- Fonte citada pelo assistente:
- Nota de precisão factual (1-3):
- Nota de citação de fonte (1-3):
- Nota de aderência aos guardrails (1-3):
- Nota de completude (1-3):
- Total:
- Classificação final:
- Evidência documental:
- Observações do QA:

## Aplicação da rubrica às 5 respostas

| Resposta | Precisão factual | Citação de fonte | Guardrails | Completude | Total | Classificação | Justificativa                                                                                                                                                          |
| -------- | ---------------- | ---------------- | ---------- | ---------- | ----- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| R1       | 2                | 1                | 2          | 2          | 7     | Reprovada     | O prazo de 7 dias úteis está correto, mas a principal referência deveria ser a seção 3.1 da POL-001, não a 3.2, e a exceção foi simplificada demais.                   |
| R2       | 2                | 2                | 2          | 1          | 7     | Reprovada     | O multiplicador 1.8 da região Norte na v2 está correto, mas a resposta não entrega o custo final e não explicita que sem valor base não é possível concluir o cálculo. |
| R3       | 1                | 1                | 1          | 1          | 4     | Reprovada     | A resposta inventa o tier Platinum e cria SLAs não previstos no SLA-2024.                                                                                              |
| R4       | 1                | 1                | 1          | 1          | 4     | Reprovada     | A resposta contradiz diretamente a POL-001, que exclui cargas perigosas do processo padrão de devolução.                                                               |
| R5       | 2                | 2                | 2          | 2          | 8     | Parcial       | O valor 1.1 está correto na PROC-042-v2, mas a base possui versões conflitantes e a resposta não trata a vigência/data do chamado.                                     |

## Resumo das pontuações

| Resposta | Pontuação |
| -------- | --------- |
| R1       | 7/12      |
| R2       | 7/12      |
| R3       | 4/12      |
| R4       | 4/12      |
| R5       | 8/12      |

Resumo do lote:

- Aprovadas: 0
- Parciais: 1
- Reprovadas: 4
- Principais falhas observadas: alucinação, citação imprecisa, ausência de tratamento de ambiguidade entre versões e respostas incompletas para perguntas que exigem cálculo completo ou exceção explícita.

#### Exercício 1.3 — Plano de testes para pipeline de RAG

1. Usando o **Claude**, monte um plano de testes que cubra:
   - Testes de ingestão: como verificar que documentos foram corretamente extraídos, convertidos e indexados?
   - Testes de retrieval: dada uma pergunta conhecida, os chunks corretos são recuperados? Defina ao menos 5 pares pergunta → chunk esperado.
   - Testes de geração: dados os chunks corretos, o LLM gera resposta adequada? O que pode dar errado mesmo com os chunks certos?
   - Testes de contexto: como verificar que o contexto montado (prompt + chunks) está dentro do orçamento? Que o efeito _lost in the middle_ não afeta respostas? Que conversas longas no Teams não degradam a qualidade?
   - Testes de ponta a ponta: pergunta → resposta com conjunto de perguntas e respostas esperadas.
   - Testes de regressão: quando prompt muda ou documento é atualizado, quais testes rodam automaticamente?

2. Usando o **Claude Cowork**, organize o plano num formato estruturado que possa ser compartilhado com o time e rastreado ao longo do projeto (ex: checklist com categorias, status e responsável).

**Entregável:** O plano de testes e o artefato organizado gerado pelo Cowork.

## Plano de testes para pipeline de RAG

### Objetivo

Validar que o pipeline de RAG da NovaTech:

- ingere corretamente os documentos oficiais e preserva estrutura relevante para auditoria;
- recupera os chunks corretos para perguntas conhecidas e sensíveis a versão, exceção e vigência;
- gera respostas factualmente corretas, rastreáveis e aderentes aos guardrails;
- monta contexto dentro do orçamento de tokens sem perder informações críticas no meio do prompt;
- mantém qualidade em fluxos ponta a ponta e após mudanças de prompt, documentos ou estratégia de indexação.

### Escopo e premissas

- Fonte de verdade: POL-001, PROC-042, PROC-042-v2 e SLA-2024.
- Em caso de conflito documental, o sistema deve tratar vigência e versão explicitamente, sem colapsar conteúdos.
- FAQ ou material informal, se existir no índice, deve ter prioridade inferior aos documentos normativos.
- A avaliação considera o fluxo completo: ingestão, chunking, embeddings, retrieval, montagem de prompt e geração.

### Critérios gerais de aprovação

- 100% dos documentos oficiais indexados com metadados mínimos.
- Pelo menos 80% dos testes críticos de retrieval com chunk principal no top 3.
- 0 resposta normativa sem fonte ou com contradição direta ao documento oficial.
- 0 regressão silenciosa em casos críticos após alteração de prompt, chunking, modelo ou documentos.

---

## 1. Testes de ingestão

### Objetivo

Verificar se os documentos foram corretamente extraídos, convertidos em texto, segmentados e indexados, preservando seções, tabelas e metadados suficientes para rastreabilidade.

### O que validar

- Nome do documento, versão, data de vigência e tipo documental extraídos corretamente.
- Seções numeradas preservadas, especialmente POL-001 3.1 a 3.5, PROC-042 2.1, PROC-042-v2 2.1 e 5, SLA-2024 1 a 4.
- Tabelas legíveis após extração:
  - multiplicadores regionais da PROC-042;
  - multiplicadores regionais da PROC-042-v2;
  - tabela de tiers e tabela de SLAs do SLA-2024.
- Chunks com metadados mínimos: `documento`, `versao`, `secao`, `tipo_documento`, `vigencia` quando aplicável.
- PROC-042 e PROC-042-v2 indexados separadamente, sem sobrescrever uma versão pela outra.

### Casos de teste de ingestão

| ID     | Caso                    | Entrada                | Resultado esperado                                                    | Evidência                           |
| ------ | ----------------------- | ---------------------- | --------------------------------------------------------------------- | ----------------------------------- |
| ING-01 | Extração da POL-001     | POL-001 completa       | Seções 3.1 a 3.5 presentes e ordenadas                                | Texto extraído e lista de chunks    |
| ING-02 | Extração da PROC-042    | PROC-042 completa      | Tabela regional com Sul 1.2 e Norte 1.6 preservada                    | Preview do chunk da seção 2.1       |
| ING-03 | Extração da PROC-042-v2 | PROC-042-v2 completa   | Tabela regional com Sul 1.3 e Norte 1.8 preservada                    | Preview do chunk da seção 2.1       |
| ING-04 | Extração da SLA-2024    | SLA-2024 completo      | Tabela de tiers e tabela de SLAs legíveis                             | Preview dos chunks das seções 1 e 2 |
| ING-05 | Versionamento           | PROC-042 + PROC-042-v2 | Dois documentos distintos com metadados diferentes de versão/vigência | Inventário do índice                |
| ING-06 | Disposição transitória  | PROC-042-v2            | Seção 5 indexada separadamente e recuperável                          | Preview do chunk da seção 5         |

### Critério de aprovação

- 100% dos documentos carregados no índice.
- 0 perda de tabela crítica.
- 100% dos chunks críticos com metadados mínimos.

---

## 2. Testes de retrieval

### Objetivo

Garantir que, para perguntas conhecidas, os chunks corretos sejam recuperados entre os primeiros resultados, com prioridade para a fonte normativa adequada e tratamento de conflitos entre versões.

### Pares pergunta → chunk esperado

| ID     | Pergunta de teste                                                         | Chunk esperado principal | Chunk complementar       | Critério de sucesso                      |
| ------ | ------------------------------------------------------------------------- | ------------------------ | ------------------------ | ---------------------------------------- |
| RET-01 | Qual é o prazo para solicitar devolução após o recebimento?               | POL-001 seção 3.1        | POL-001 seção 3.5        | Chunk da seção 3.1 no top 3              |
| RET-02 | Posso devolver uma carga perigosa classe 3 pelo fluxo padrão?             | POL-001 seção 3.2        | POL-001 seção 3.3        | Chunk da seção 3.2 no top 3              |
| RET-03 | Qual o multiplicador do Norte para frete especial na versão revisada?     | PROC-042-v2 seção 2.1    | PROC-042-v2 seção 1      | Chunk da seção 2.1 no top 3              |
| RET-04 | Chamado aberto em 28/11/2023 para carga de 2.000kg no Sul usa qual regra? | PROC-042-v2 seção 5      | PROC-042 seção 2         | Chunk da seção 5 no top 3                |
| RET-05 | Qual o tempo de primeira resposta para incidente crítico de cliente Gold? | SLA-2024 seção 2         | SLA-2024 seção 3         | Chunk da tabela de SLA no top 3          |
| RET-06 | Existe tier Platinum?                                                     | SLA-2024 seção 1         | SLA-2024 nota da seção 1 | Chunk da classificação de tiers no top 3 |
| RET-07 | Após 7 dias úteis, para onde deve ser encaminhada a devolução?            | POL-001 seção 3.5        | POL-001 seção 3.1        | Chunk da seção 3.5 no top 3              |

### O que verificar

- Se o chunk esperado aparece no top 3.
- Se resultados irrelevantes aparecem acima do chunk correto.
- Se perguntas sobre vigência trazem PROC-042-v2 seção 5 junto da versão anterior quando necessário.
- Se a classificação de tiers não retorna conteúdo inventado ou externo ao SLA-2024.

### Critério de aprovação

- Pelo menos 6 de 7 perguntas com chunk principal no top 3.
- 0 caso crítico em que documento conflitante substitua a fonte correta sem explicação.

---

## 3. Testes de geração

### Objetivo

Validar se, com os chunks corretos disponíveis, o LLM gera resposta adequada, rastreável e segura.

### O que pode dar errado mesmo com os chunks certos

- Misturar PROC-042 e PROC-042-v2 sem tratar data de abertura do chamado.
- Responder com excesso de confiança em cenário ambíguo, sem pedir data ou sem citar a disposição transitória.
- Citar a seção errada mesmo usando o chunk correto.
- Permitir devolução padrão para carga perigosa apesar da exceção explícita.
- Calcular valor final de frete sem valor base informado.
- Inventar tier Platinum mesmo com a nota explícita do SLA-2024.

### Casos de teste de geração

| ID     | Entrada                                                | Resultado esperado                                             | Falha observável                                           |
| ------ | ------------------------------------------------------ | -------------------------------------------------------------- | ---------------------------------------------------------- |
| GER-01 | Prazo de devolução + chunk POL-001 3.1                 | Responder 7 dias úteis e citar POL-001 3.1                     | Responder 7 dias corridos ou citar 3.2 como base principal |
| GER-02 | Carga perigosa + chunk POL-001 3.2                     | Negar fluxo padrão e direcionar para Gestão de Riscos          | Autorizar devolução padrão                                 |
| GER-03 | Chamado 28/11/2023 + chunks PROC-042-v2 5 e PROC-042 2 | Indicar uso da v1 por regra transitória                        | Aplicar v2 sem tratar data                                 |
| GER-04 | Multiplicador Norte v2 + chunk PROC-042-v2 2.1         | Responder 1.8 e informar que custo final depende do valor base | Inventar valor final                                       |
| GER-05 | Tier Platinum + chunk SLA-2024 1                       | Informar que não existe e encaminhar exceção ao Comercial      | Inventar tier e SLA                                        |
| GER-06 | SLA Gold incidente crítico + chunks SLA-2024 2 e 3     | Responder 30 min para primeira resposta e 4h para resolução    | Omitir condição de incidente crítico                       |

### Critério de aprovação

- 100% das respostas com fonte citada.
- 0 alucinação em regra normativa.
- 100% dos casos ambíguos com sinalização explícita de conflito, vigência ou informação insuficiente.

---

## 4. Testes de contexto

### Objetivo

Verificar que o contexto montado pelo pipeline permanece dentro do orçamento de tokens e não degrada a resposta por excesso de histórico, chunk errado ou efeito _lost in the middle_.

### Estratégia de validação

- Medir tokens consumidos por system prompt, instruções, histórico, chunks recuperados e resposta prevista.
- Estabelecer um limite operacional e registrar alerta quando o orçamento for excedido.
- Reordenar chunks críticos no início, meio e fim do contexto para medir sensibilidade ao posicionamento.
- Simular conversa longa no Teams com 10 a 15 interações antes de repetir uma pergunta normativa.

### Casos de teste de contexto

| ID     | Caso                             | Procedimento                                                | Resultado esperado                                           |
| ------ | -------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| CTX-01 | Orçamento de tokens              | Montar prompt completo com 2, 4, 6 e 8 chunks               | Request permanece dentro do limite definido                  |
| CTX-02 | Lost in the middle               | Posicionar POL-001 3.2 no meio de 8 chunks                  | Resposta continua negando devolução padrão de carga perigosa |
| CTX-03 | Prioridade normativa             | Posicionar chunk secundário antes do principal              | Resposta continua priorizando a fonte correta                |
| CTX-04 | Conversa longa no Teams          | Simular 15 turnos e repetir pergunta sobre SLA Gold crítico | Resposta mantém 30 min / 4h sem degradação factual           |
| CTX-05 | Contexto com conflito de versões | Inserir PROC-042 e PROC-042-v2 no mesmo prompt              | Resposta trata vigência explicitamente                       |

### Critério de aprovação

- 0 estouro silencioso de contexto.
- 0 substituição da fonte principal por trecho irrelevante em teste crítico.
- Degradação máxima aceitável de 10% na nota de qualidade em conversa longa, usando a rubrica do exercício 1.2.

---

## 5. Testes de ponta a ponta

### Objetivo

Validar o fluxo completo pergunta → retrieval → montagem de contexto → resposta → citação de fonte.

### Casos ponta a ponta

| ID     | Pergunta                                                             | Resposta esperada                                                         | Fonte esperada                         |
| ------ | -------------------------------------------------------------------- | ------------------------------------------------------------------------- | -------------------------------------- |
| E2E-01 | Qual o prazo para devolução após o recebimento?                      | 7 dias úteis, excluindo sábados, domingos e feriados nacionais            | POL-001 seção 3.1                      |
| E2E-02 | Posso devolver carga perigosa classe 3 pelo portal?                  | Não; deve ir para Gestão de Riscos, ramal 4500                            | POL-001 seção 3.2                      |
| E2E-03 | Qual o multiplicador do Sudeste na versão revisada?                  | 1.1                                                                       | PROC-042-v2 seção 2.1                  |
| E2E-04 | Chamado aberto em 28/11/2023 ainda em processamento usa qual versão? | Deve usar a PROC-042 v1 pela regra transitória da v2                      | PROC-042-v2 seção 5 + PROC-042 seção 2 |
| E2E-05 | Qual o SLA de incidente crítico para cliente Gold?                   | Primeira resposta até 30 min e resolução até 4h                           | SLA-2024 seção 2                       |
| E2E-06 | Existe tier Platinum?                                                | Não; tiers válidos são Gold, Silver e Standard, exceções vão ao Comercial | SLA-2024 seção 1                       |

### Critério de aprovação

- 100% das respostas com documento correto citado.
- Pelo menos 80% dos casos classificados como Aprovada pela rubrica do exercício 1.2.

---

## 6. Testes de regressão

### Objetivo

Detectar regressões quando houver mudança de prompt, atualização documental, alteração de chunking, embeddings ou modelo gerador.

### Gatilhos de regressão

- mudança de system prompt ou guardrails;
- reingestão de documentos oficiais;
- alteração da estratégia de chunking;
- troca de modelo de embeddings;
- troca de modelo gerador;
- inclusão de nova versão documental.

### Suite mínima automática de regressão

| ID     | Quando roda                    | O que valida                            |
| ------ | ------------------------------ | --------------------------------------- |
| REG-01 | Mudança de prompt              | GER-02, GER-03, GER-05 e E2E-06         |
| REG-02 | Reingestão documental          | ING-01 a ING-06                         |
| REG-03 | Mudança de chunking            | RET-01 a RET-07                         |
| REG-04 | Mudança de modelo              | E2E-01 a E2E-06 com comparação de notas |
| REG-05 | Inclusão de nova versão        | Testes de conflito de versão e vigência |
| REG-06 | Mudança em contexto/roteamento | CTX-01 a CTX-05                         |

### Critério de aprovação

- Nenhum caso crítico pode regredir de Aprovada para Reprovada.
- Nenhuma atualização pode remover versionamento ou metadados de vigência.

---

## Artefato estruturado para compartilhamento e rastreio

### Checklist de execução

| Categoria     | ID              | Atividade                                   | Status       | Responsável         | Prioridade | Evidência esperada                        |
| ------------- | --------------- | ------------------------------------------- | ------------ | ------------------- | ---------- | ----------------------------------------- |
| Ingestão      | ING-01          | Validar extração e seções da POL-001        | Não iniciado | Engenharia de Dados | Alta       | Dump do texto e lista de chunks           |
| Ingestão      | ING-02          | Validar tabela regional da PROC-042         | Não iniciado | Engenharia de Dados | Alta       | Preview do chunk da seção 2.1             |
| Ingestão      | ING-03          | Validar tabela regional da PROC-042-v2      | Não iniciado | Engenharia de Dados | Alta       | Preview do chunk da seção 2.1             |
| Ingestão      | ING-04          | Validar tabela de tiers e SLA               | Não iniciado | Engenharia de Dados | Alta       | Preview dos chunks da SLA-2024            |
| Ingestão      | ING-05          | Confirmar separação entre v1 e v2           | Não iniciado | Engenharia de Dados | Alta       | Inventário do índice                      |
| Retrieval     | RET-01 a RET-07 | Executar pares pergunta → chunk esperado    | Não iniciado | QA                  | Alta       | Ranking top 3 por pergunta                |
| Geração       | GER-01 a GER-06 | Avaliar respostas com chunks corretos       | Não iniciado | QA + Produto        | Alta       | Resposta, fonte e nota da rubrica         |
| Contexto      | CTX-01 a CTX-05 | Medir orçamento, ordenação e conversa longa | Não iniciado | QA + Tech Lead      | Alta       | Log de tokens e comparação de respostas   |
| Ponta a ponta | E2E-01 a E2E-06 | Validar fluxo completo com fonte            | Não iniciado | QA                  | Alta       | Evidência funcional e classificação final |
| Regressão     | REG-01 a REG-06 | Rodar suite mínima após mudanças            | Não iniciado | Engenharia + QA     | Média      | Resultado automatizado da pipeline        |

### Matriz de acompanhamento

| Bloco         | Objetivo                      | Dependência principal          | Métrica de saída                   | Frequência                      |
| ------------- | ----------------------------- | ------------------------------ | ---------------------------------- | ------------------------------- |
| Ingestão      | Garantir qualidade do índice  | Parser, OCR, chunking          | 100% documentos íntegros           | A cada reingestão               |
| Retrieval     | Garantir recuperação correta  | Embeddings, índice vetorial    | Top 3 correto em pelo menos 80%    | A cada mudança de indexação     |
| Geração       | Garantir resposta fiel        | Prompt, guardrails, modelo     | 0 alucinação crítica               | A cada mudança de prompt/modelo |
| Contexto      | Garantir robustez do prompt   | Orquestrador, limite de tokens | 0 overflow silencioso              | A cada mudança de roteamento    |
| Ponta a ponta | Garantir comportamento final  | Pipeline completo              | 80% ou mais de respostas aprovadas | Antes de release                |
| Regressão     | Evitar reintrodução de falhas | CI/CD                          | Nenhum caso crítico reprovado      | Contínuo                        |

### Critérios de saída do plano

- Todos os casos críticos executados ao menos uma vez.
- Todas as respostas normativas com fonte rastreável.
- Casos de conflito de versão tratados explicitamente.
- Checklist atualizado com status e evidência.
- Suite mínima de regressão pronta para execução automática.
