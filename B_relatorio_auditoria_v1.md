# Relatório de Auditoria v1 — Revisão Crítica do Relatório Assistente v1

**Auditado:** B_relatorio_assistente_v1.md  
**Papel do auditor:** Assistente independente com postura adversarial — busca falhas, omissões, afirmações não sustentadas e lacunas estruturais que comprometam a qualidade do Service Blueprint AS-IS.  
**Data:** junho de 2026

---

## Sumário Executivo

O Relatório v1 produz uma base útil, mas apresenta **quatro problemas estruturais sérios** e diversas afirmações de confiança injustificada. O documento é insuficiente como base para um Service Blueprint AS-IS por três razões principais: (1) a jornada do cidadão está incompleta — faltam etapas críticas do processo de habilitação anterior à ligação; (2) os processos de backstage são superficiais e especulativos onde deveriam ser factuais; (3) os fail points listados carecem de evidências de frequência/impacto e omitem falhas sistêmicas conhecidas. Detalho abaixo.

---

## Problema 1 — Jornada incompleta: o processo de habilitação está ausente

**Achado:** A v1 inicia a jornada na "Etapa 0 — Evento-gatilho" (demissão) e salta diretamente para a "Etapa 2 — Ligação à URA". Isso omite o processo mais crítico do ponto de vista do cidadão: **a habilitação ao Seguro-Desemprego**.

**O que falta:**
- O cidadão precisa **requerer** o Seguro-Desemprego — isso não acontece automaticamente com a demissão. O requerimento pode ser feito pelo portal gov.br, pelo app Carteira de Trabalho Digital, no SINE, ou em agências da Caixa. A URA não é o canal de requerimento — é o canal de **acompanhamento após o requerimento**.
- Existe um **prazo para requerimento** (entre 7 e 120 dias após a demissão, dependendo da data de rescisão — a v1 não menciona isso).
- O MTE analisa o requerimento e emite a decisão de habilitação. Somente após a habilitação o benefício aparece no sistema da Dataprev/Caixa para consulta pela URA.
- **Consequência da omissão:** o blueprint vai confundir o papel da URA. A URA não serve para requerer o benefício — serve para acompanhar o benefício já requerido. Essa distinção é fundamental para o blueprint AS-IS.

**Recomendação:** Adicionar as etapas de requerimento (gov.br/SINE/presencial) e habilitação (MTE/Dataprev) como etapas obrigatórias anteriores ao primeiro acesso à URA. A URA entra na jornada apenas na fase de acompanhamento.

---

## Problema 2 — Afirmação não sustentada sobre o Decreto 11.034/2022

**Achado:** A v1 afirma, na Seção 5 (Normativos) e na Seção 6 (Fail Points 3): "O Decreto 11.034/2022 exige atendimento em até 2 minutos para SAC" e depois especula que "a Caixa pode argumentar que a URA do Seguro-Desemprego tem natureza informativa, não de SAC regulado."

**Problema:**
- O Decreto 11.034/2022 regulamenta o SAC de **fornecedores de produtos e serviços ao consumidor**. A Caixa, como agente pagador de um benefício público, atua aqui em nome do Estado — não como empresa prestando serviço ao consumidor. A aplicabilidade do Decreto 11.034/2022 ao canal do Seguro-Desemprego é **juridicamente questionável** e a v1 não investiga isso.
- O prazo de 2 minutos do Decreto 11.034/2022 é para o **SAC humano** — não para a URA em si. A URA automática não está sujeita a esse limite.
- A v1 apresenta uma especulação ("pode argumentar") como se fosse um fato incerto — mas na verdade é uma questão jurídica que requer análise, não especulação.

**Recomendação:** Investigar se há regulamentação específica da Anatel ou do MTE sobre tempo de atendimento no canal do Seguro-Desemprego. Verificar se a Caixa, no papel de agente pagador de benefício público, está sujeita ao SAC ou a outro regime. Consultar decisões do TCU ou pareceres da AGU sobre o tema.

---

## Problema 3 — Backstage superficial: integração Dataprev descrita sem evidência

**Achado:** A Seção 3 descreve o processo de consulta à BGSD como se fosse em "tempo real", mas não apresenta fonte. A afirmação "A URA realiza uma consulta à BGSD mantida pela Dataprev" é plausível, mas não é evidenciada.

**O que não está explicado e deveria estar:**
- **Protocolo de integração:** a URA consulta a Dataprev via webservice, API REST, mensageria ou cache local? Isso impacta diretamente o tempo de resposta e o comportamento em falhas.
- **Frequência de atualização:** se há cache, qual a defasagem? Um cidadão que teve o benefício desbloqueado hoje à tarde vê isso refletido imediatamente na próxima ligação?
- **Autenticação técnica:** a integração entre Caixa e Dataprev é coberta por qual instrumento contratual? O Acórdão 135/2024-TCU menciona "alterações no termo contratual com a Dataprev" — isso afeta a URA ou apenas o processamento de concessão?
- **SLA de disponibilidade da BGSD:** qual o tempo de indisponibilidade mensal tolerado? Esse dado é público (consta nos contratos ou relatórios de gestão)?

**Recomendação:** Pesquisar documentos de licitação/contratos entre Caixa e Dataprev, relatórios de gestão do MTE e eventuais acórdãos do TCU que descrevam a arquitetura de integração do canal de atendimento.

---

## Problema 4 — Fail Points sem dados de frequência ou impacto quantificado

**Achado:** Os seis fail points listados são qualitativamente corretos, mas a v1 não apresenta **nenhum dado de frequência ou escala**. Um Service Blueprint AS-IS precisa priorizar fail points — e para isso é preciso, ao menos, indicar ordens de grandeza.

**O que poderia ser levantado:**
- Consumidor.gov.br: as reclamações sobre Seguro-Desemprego da Caixa são públicas e permitem identificar os problemas mais frequentes com nomenclatura dos próprios cidadãos.
- Relatórios de ouvidoria da Caixa (publicados anualmente no Relatório de Sustentabilidade ou no portal da ouvidoria).
- Dados de volumetria de atendimento (ligações/mês à URA do Seguro-Desemprego) — estimativa a partir dos 7,44 milhões de solicitações/ano (MTE, 2024): se mesmo 10% ligam à URA, são 744 mil ligações/ano.
- O Acórdão 135/2024-TCU menciona 382 mil parcelas irregulares — parte dessas irregularidades pode ter gerado contato com a URA (benefício bloqueado = cidadão liga).

**Recomendação:** Consultar Consumidor.gov.br, relatórios de ouvidoria da Caixa e dados do MTE para quantificar ao menos por categoria de reclamação. Estimar volume de atendimento com base nos dados de solicitações.

---

## Problema 5 — Ausência de "linha de visibilidade" e "linha de interação interna" no blueprint

**Achado:** Um Service Blueprint tem, por definição, quatro linhas horizontais: (1) evidências físicas, (2) ações do cliente, (3) linha de visibilidade (frontstage/backstage), (4) processos de suporte. A v1 trata essas dimensões em seções separadas, mas não as organiza de forma cruzada com as **etapas da jornada**. Não é possível, lendo a v1, saber quais processos de backstage correspondem a qual etapa da jornada do cidadão.

**Recomendação:** Na próxima versão, organizar o conteúdo em uma matriz: etapas da jornada nas colunas, camadas do blueprint nas linhas. Isso tornará o documento diretamente utilizável para construir o diagrama da Parte D.

---

## Síntese das Exigências para a v2

| Item | Exigência |
|------|-----------|
| Jornada | Adicionar etapas de requerimento e habilitação (pré-URA) com prazos |
| Normativos | Investigar aplicabilidade real do Decreto 11.034/2022 ao canal do Seguro-Desemprego |
| Backstage | Detalhar protocolo de integração Caixa-Dataprev com evidência documental |
| Fail points | Quantificar frequência/impacto com dados do Consumidor.gov.br e ouvidoria da Caixa |
| Estrutura | Reorganizar o conteúdo em matriz etapa × camada para uso direto no blueprint |

---

## O Que a v1 Acerta (manter na v2)

- A estrutura de seções (jornada, frontstage, backstage, evidências, normativos, fail points) é adequada para o objetivo.
- A identificação dos atores principais (Caixa, Dataprev, MTE, BPO) está correta e alinhada ao exercício 2.1.
- A tabela de evidências físicas por etapa é útil.
- A lista de lacunas (Seção 7) é honesta e deve ser mantida e expandida.
- O reconhecimento de que a URA não resolve bloqueios — apenas os informa — é um insight importante para o blueprint.
