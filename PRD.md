1) Descritivo textual detalhado — Razão de ser do The Industrial Hunter

O The Industrial Hunter existe para resolver um problema simples e caro no mercado de válvulas industriais: a demanda não nasce no pedido — ela nasce antes, em sinais públicos e operacionais que quase sempre chegam tarde na mesa do vendedor.

Na prática, grandes indústrias (celulose, refinarias, petroquímica, siderurgia/mineração) geram demanda relevante por válvulas em dois momentos previsíveis:

CAPEX (projetos e expansão)
Quando uma empresa aprova um investimento, inicia licenciamento, contrata EPC, começa engenharia e entra em compras, existe uma sequência de decisões técnicas e comerciais que culmina na especificação e aquisição de válvulas. Quem entra cedo consegue:

influenciar especificação (materiais, classes, normas, fornecedores aprovados),

posicionar homologações,

travar relações com EPC/contratadas antes do “bolo” ser dividido.

OPEX (paradas de manutenção / turnarounds)
Paradas são eventos raros e caros. Quando uma planta anuncia (ou deixa rastros) de uma parada geral/unidade, o mercado reage: manutenção, inspeção, caldeiraria, automação, sobressalentes — e válvulas. O diferencial aqui é timing brutal:

quem antecipa janela, escopo e contratadas entra como solução e não como “cotação de última hora”,

quem chega tarde vira preço.

O problema é que esses sinais estão espalhados:

notícias, releases corporativos, portais setoriais,

diários oficiais, licenciamento ambiental,

licitações, contratações e atas,

vagas de emprego relacionadas à parada,

comunicados operacionais e notas públicas.

O vendedor técnico até consegue “garimpar” isso manualmente — mas paga com:

tempo perdido e atenção fragmentada,

ruído (parada de ônibus, greve, etc.),

erro de alvo (fala com a pessoa errada),

falta de evidência e falta de histórico.

O Industrial Hunter transforma esse caos em algo executável. Ele faz quatro coisas que um CRM não faz:

Descoberta ativa: procura sinais onde eles nascem (não espera indicação).

Estruturação com evidência: converte texto solto em entidades e eventos rastreáveis (com link e trecho).

Prioriza pelo que importa: urgência (janela), fit técnico para válvulas e clareza do alvo.

Entrega ação pronta: um card que diz “por que isso vale sua energia” + “qual próximo passo”.

O objetivo final não é “ter dados”. É gerar pipeline real:

reduzir tempo até o primeiro contato,

aumentar taxa de resposta,

e aumentar a chance de influenciar especificação/compra antes do concorrente.

O v0 tem uma razão de ser pragmática: provar que o sistema consegue, de forma consistente, identificar paradas e projetos reais, deduplicar, anexar evidências e produzir um lead acionável — com uma UI simples que permite operar isso todo




2) PRD — The Industrial Hunter (v0 plenamente funcional com UI)
1. Visão do produto

SaaS que detecta, estrutura e prioriza sinais públicos de demanda por válvulas industriais no Brasil, com foco em:

Industrial Projects (CAPEX)

Maintenance Shutdowns / Turnarounds (OPEX)

Setores: Papel & Celulose, Óleo & Gás, Petroquímica, Siderurgia/Mineração.

2. Objetivos do v0

Entregar um sistema que um usuário consegue usar diariamente para:

Rodar varreduras (automática 1x/dia e manual).

Visualizar oportunidades em dashboard com filtros.

Abrir detalhe com evidências e contexto (fato vs hipótese).

Marcar status/feedback e manter histórico.

Exportar resultados.

3. Escopo do v0 (o que entra)
3.1 Pipeline (backend)

Módulos obrigatórios

Query Planner: gera queries por setor/UF/tipo (project/shutdown) com orçamento diário.

Discovery: busca URLs relevantes (10–20 por query).

Fetch & Clean: URL → texto/markdown limpo, em camadas.

Classifier: project/shutdown/irrelevant, com justificativa curta.

Extractor: JSON schema rígido + evidências obrigatórias.

Validation: validação de tipos/datas e consistência.

Dedup/Merge: fingerprint robusto + ledger de eventos/evidências.

Scoring: score, confidence, time-to-act.

Persistence: banco + export.

Fetch em camadas (obrigatório)

Fetch simples + extractor tipo “reader”

Fallback para leitura limpa alternativa

Browser/headless para páginas dinâmicas

Proxy apenas se necessário (opcional no v0, mas preparado)

3.2 UI (web)

Funcionalidades mínimas

Login

Dashboard:

lista de sinais

filtros: tipo, setor, UF, score mínimo, time-to-act, data de descoberta

busca por operador/planta

ordenação: score, recência, urgência

Detalhe do sinal:

resumo + score/confidence/time-to-act

evidências (links + trechos)

facts vs hypotheses (com confidence nas hipóteses)

sinais comerciais (EPC/contratadas, compras, vagas) quando existirem

Ações:

status: Novo / Qualificado / Em abordagem / Reunião / Proposta / Ganho / Perdido / Ruído / Duplicado / Já conhecido

feedback: Útil / Ruído / Duplicado + nota opcional

Operação:

botão “Rodar varredura agora”

tela “Runs”: últimas execuções e métricas

Export:

CSV/JSON do conjunto filtrado

4. Fora de escopo (v0)

Enrichment obrigatório (Proxycurl/Apollo) — apenas “placeholder” e/ou botão futuro.

Outbound automation (cadências).

Integrações CRM completas.

Qualquer contorno de paywall/login/ToS.

5. Contrato de dados (schema v0)
IndustrialSignal

id

signal_type: industrial_project | maintenance_shutdown

operator: { name, industry_vertical }

asset: { plant_name?, unit_or_area?, location? }

timeline: { phase, start_date?, end_date?, window_text? }

facts[]: frases literais

valve_hypotheses[]: { hypothesis, rationale, confidence }

commercial_signals:

epc_or_contractors[]

procurement_mentions[]

job_postings[]

evidences[]: { url, excerpt, published_at? }

score (0–100)

confidence (0–1)

time_to_act_bucket: 0_30 | 30_90 | 90_180 | 180_plus | unknown

fingerprint

status + notes?

raw_trace: { query_used, fetch_method, content_hash }

created_at, updated_at

Ledger

SignalEvent (uma fonte/ocorrência por URL/extração)

Evidence (trechos e links)

6. Deduplicação e merge

Fingerprint recomendado
signal_type + operator_normalized + plant_normalized + window_bucket + topic_signature

Regras de merge

anexar novas evidências

preferir datas explícitas > texto solto

preferir fonte oficial > blog/agregador

nunca apagar histórico

7. Scoring e confiança (v0)
Score 0–100

Shutdown

time-to-act/urgência pesa mais

penaliza ausência de janela

Project

fase (EPC/procurement) pesa mais

contratada/EPC citada aumenta score

Confidence 0–1

qualidade da fonte × clareza da entidade × consistência entre fontes

8. Métricas e observabilidade

URLs processadas / válidos / rejeitados / schema failures

custo estimado por run

dedup rate (fontes→lead)

feedback útil vs ruído

9. Critérios de aceite do v0

Usuário consegue: login → rodar varredura → ver leads → abrir detalhe → marcar status/feedback → exportar.

Leads válidos sempre com evidência.

Ruído rejeitado com justificativa.

Dedup funciona com múltiplas fontes.