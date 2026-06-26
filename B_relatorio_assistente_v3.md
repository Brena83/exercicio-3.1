# Relatório Assistente v3 — Service Blueprint AS-IS: URA da Caixa / Seguro-Desemprego

**Versão:** 3.0 (final)  
**Status:** Revisão em resposta à Auditoria v2  
**Data de corte das fontes:** junho de 2026

**O que mudou em relação à v2:**
1. Entregue a matriz etapa × camada (tabela cruzada) — exigência A da Auditoria v2.
2. Separadas as duas integrações Caixa-Dataprev; afirmação SOAP/REST rebaixada a hipótese — exigência B.
3. Estimativas de volumetria rotuladas explicitamente — exigência C.

---

## Seção 1 — Jornada do Cidadão (consolidada da v2, sem alterações estruturais)

*(Jornada mantida conforme v2: Fase 1 — Requerimento/Habilitação [Etapas 1.1 a 1.4] + Fase 2 — Acompanhamento via URA [Etapas 2.1 a 2.6]. Não reproduzida por extenso para evitar redundância — referência à v2, Seção 1.)*

**Resumo da jornada para a matriz:**

| Cód. | Etapa | Fase |
|------|-------|------|
| E1 | Demissão e período de carência | Pré-URA |
| E2 | Requerimento (gov.br / SINE / presencial) | Pré-URA |
| E3 | Análise e habilitação (MTE/Dataprev) | Pré-URA |
| E4 | Notificação ao cidadão | Pré-URA |
| E5 | Evento-gatilho para ligação à URA | URA |
| E6 | Ligação e navegação no menu | URA |
| E7 | Autenticação (CPF + data de nascimento) | URA |
| E8 | Consulta e resposta da URA | URA |
| E9 | Encerramento ou transferência para humano | URA |
| E10 | Pós-atendimento (ação do cidadão ou aguardo) | Pós-URA |

---

## Seção 2 — Matriz Etapa × Camada do Blueprint (NOVA — exigência A da Auditoria v2)

Esta matriz é o núcleo do Service Blueprint AS-IS. Leia as colunas como as etapas da jornada (E1–E10) e as linhas como as camadas do blueprint.

### Linha 1 — Evidências Físicas

| E1 | E2 | E3 | E4 | E5 | E6 | E7 | E8 | E9 | E10 |
|----|----|----|----|----|----|----|----|----|-----|
| TRCT, aviso prévio, formulário FGTS | Protocolo de requerimento (gov.br), formulário SD/CD (presencial) | Nenhuma | Carta/e-mail de habilitação, notificação no app | Nenhuma | Nenhuma (voz) | Nenhuma | Nenhuma | Protocolo de atendimento (se emitido pelo humano — não garantido) | SMS de crédito (Caixa Tem), extrato bancário |

### Linha 2 — Ações do Cidadão (Frontstage)

| E1 | E2 | E3 | E4 | E5 | E6 | E7 | E8 | E9 | E10 |
|----|----|----|----|----|----|----|----|----|-----|
| Recebe TRCT, guarda documentação | Acessa gov.br ou vai ao SINE; preenche formulário; confirma dados | Aguarda resposta | Lê notificação; anota número de parcelas e datas | Identifica necessidade de consulta; disca 0800 726 0207 | Ouve menu, pressiona opções numéricas | Digita CPF + data de nascimento no teclado do telefone | Ouve resultado da URA; anota informações | Pressiona opção para humano OU encerra a ligação | Age conforme instruído (aguarda crédito, vai ao SINE, liga de novo) |

### Linha 3 — Processos Frontstage (o que a Caixa/URA apresenta ao cidadão)

| E1 | E2 | E3 | E4 | E5 | E6 | E7 | E8 | E9 | E10 |
|----|----|----|----|----|----|----|----|----|-----|
| (Empregador) Registra rescisão; entrega documentação | Portal gov.br / SINE recebe o requerimento e exibe protocolo | (MTE) Analisa e emite decisão no sistema | (MTE/gov.br) Envia notificação ao cidadão | Nenhuma | URA apresenta menu em voz sintetizada | URA solicita CPF e data de nascimento; confirma identidade | URA lê status do benefício, número de parcelas, valor e data | URA oferece opção de transferência; informa tempo de espera (estimado) | Caixa Tem envia SMS de crédito |

### Linha 4 — Linha de Visibilidade (separação frontstage / backstage)

*(Tudo abaixo desta linha é invisível ao cidadão)*

### Linha 5 — Processos Backstage

| E1 | E2 | E3 | E4 | E5 | E6 | E7 | E8 | E9 | E10 |
|----|----|----|----|----|----|----|----|----|-----|
| Empregador registra no eSocial/CAGED; Dataprev recebe e processa os dados | MTE recebe requerimento; Dataprev cruza com INSS, RAIS, CAGED para verificar elegibilidade | MTE/Dataprev processa a decisão; atualiza a BGSD com status do benefício | MTE atualiza gov.br; envia notificação | Nenhum processo backstage | URA recebe a ligação; roteamento interno no sistema de telefonia (PABX/VoIP — plataforma do BPO) | Sistema de URA valida CPF/data de nascimento contra o CIC (cadastro Caixa) | **URA consulta a BGSD/Dataprev** [ver nota técnica abaixo]; aplica regras de negócio; formata resposta em voz | Sistema de URA encaminha para fila de ACD (Automatic Call Distribution) do BPO; atendente acessa interface com dados da BGSD | Caixa executa crédito na conta vinculada ao CPF; Caixa Tem processa notificação |

> **Nota técnica (E8 — integração Caixa-Dataprev para a URA):**  
> Existem **duas integrações distintas** que a v2 confundia. Conforme correção exigida pela Auditoria v2:
> - **Integração A (MTE-Dataprev, concessão):** documentada no Acórdão 135/2024-TCU. Cobre o processamento de elegibilidade e concessão do benefício. Não envolve diretamente a URA da Caixa.
> - **Integração B (Caixa-Dataprev, consulta via URA):** coberta por contrato Caixa-Dataprev (mencionado genericamente no Acórdão 135/2024, mas sem detalhes técnicos). O protocolo técnico desta integração (REST/SOAP/mensageria; cache ou tempo real; SLA de disponibilidade) **não está disponível em fonte pública**. A hipótese de uso de webservice síncrono é tecnicamente plausível para um canal de consulta em tempo real, mas permanece **[EM ABERTO]** até obtenção de fonte primária (LAI ou contrato público).

### Linha 6 — Processos de Suporte (sistemas e atores de infraestrutura)

| E1–E4 (Pré-URA) | E5–E9 (URA) | E10 (Pós-URA) |
|-----------------|-------------|---------------|
| eSocial/CAGED (MTE); BGSD (Dataprev); Sistema de requerimento do MTE (gov.br) | Plataforma de URA (marca [EM ABERTO]); CIC — Cadastro Caixa; BPO/contact center (operador atual [EM ABERTO] — histórico: CTIS Tecnologia via PE 59/2017); Anatel (regulação 0800) | Core bancário da Caixa; Caixa Tem |

### Linha 7 — Fail Points por Etapa

| Etapa | Fail Point | Frequência estimada |
|-------|-----------|---------------------|
| E2 — Requerimento | FP0: Prazo perdido (antes de 7 dias ou após 120 dias) — cidadão perde direito ao benefício | Baixa (mas impacto muito alto) |
| E3 — Análise/Habilitação | FP2: Dado inconsistente no eSocial/CAGED → benefício bloqueado desde a origem | Alta |
| E7 — Autenticação URA | FP1: Falha de autenticação (CPF/data de nascimento não reconhecidos) | Alta |
| E8 — Consulta URA | FP2b: Status "bloqueado" sem informação de causa | Muito alta |
| E8 — Consulta URA | FP4: Indisponibilidade da integração BGSD/Dataprev → erro genérico | Baixa, alto impacto |
| E9 — Transferência | FP3: Tempo de espera excessivo (>30 min em picos) | Alta em picos |
| E9 — Atendimento humano | FP5: Atendente não tem acesso à causa do bloqueio (sistemas separados) | Alta |
| E9/E10 — Pós-atendimento | FP6: Ausência de protocolo ao encerrar URA sem transferência | Muito alta |
| E10 — Saque | FP7: Conta Caixa inativa/inexistente — crédito disponível mas inacessível | Média |

> **Nota sobre volumetria (exigência C da Auditoria v2):** Os dados de frequência acima são classificações qualitativas baseadas em padrões de reclamações no Consumidor.gov.br (categorias de Seguro-Desemprego/Caixa, junho 2026) e na inferência do Acórdão 135/2024-TCU (382 mil parcelas irregulares → potencial de ligações adicionais). **Não há dado público de volumetria de chamadas** (ligações/mês à URA do Seguro-Desemprego). Qualquer número absoluto seria estimativa do redator, não dado confirmado. A classificação Alta/Média/Baixa reflete a recorrência relativa nas reclamações públicas, não um percentual de chamadas.

---

## Seção 3 — Processos Backstage Detalhados (consolidação)

*(Conteúdo mantido da v2, com correção da nota técnica sobre a integração, já incorporada na matriz acima. Não reproduzido por extenso — referência à v2 e à nota técnica na Seção 2.)*

---

## Seção 4 — Normativos Aplicáveis (consolidado, sem alterações da v2)

*(Tabela da v2 mantida. Decreto 11.034/2022 permanece [EM ABERTO] quanto à aplicabilidade ao canal do Seguro-Desemprego.)*

---

## Seção 5 — Fail Points Consolidados (com rotulagem de estimativas)

*(Consolidados na matriz da Seção 2, Linha 7. A tabela da v2, Seção 6, está incorporada e atualizada.)*

---

## Seção 6 — Lacunas (atualizada)

| Lacuna | Status | Onde buscar |
|--------|--------|-------------|
| Operador atual do BPO/contact center | [EM ABERTO] | PNCP, licitacoes.caixa.gov.br, LAI |
| Marca da plataforma de URA | [EM ABERTO] | Idem |
| Protocolo técnico da Integração B (Caixa-Dataprev/URA) | [EM ABERTO] | Contrato Caixa-Dataprev via LAI; acórdãos TCU |
| SLA de disponibilidade da BGSD (Dataprev) | [EM ABERTO] | Contrato via LAI; relatórios de gestão Dataprev |
| TMA na URA do Seguro-Desemprego | [EM ABERTO] | Relatórios ouvidoria Caixa |
| Taxa de resolução no primeiro contato (FCR) | [EM ABERTO] | Idem |
| Aplicabilidade do Decreto 11.034/2022 ao canal | [EM ABERTO] | Consultar Senacon, Bacen, normativos MTE |
| Volumetria de chamadas (ligações/mês) | [EM ABERTO] | Relatórios de gestão Caixa; pedido LAI |
