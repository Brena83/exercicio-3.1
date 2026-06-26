# Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA da Caixa Econômica Federal

**Versão:** 1.0 | **Data:** junho de 2026
**Canal:** URA — 0800 726 0207 · Disponível 24h · Gratuito ao cidadão
**Órgão titular:** MTE | **Operador do canal:** Caixa Econômica Federal | **Base de dados:** Dataprev / BGSD

---

## 1. Swim Lanes (Atores)

| Swim Lane | Ator | Papel |
|-----------|------|-------|
| **Lane 1 — Cidadão** | Trabalhador demitido | Inicia e conduz a jornada; destinatário do serviço |
| **Lane 2 — URA / Frontstage Caixa** | Sistema de URA + BPO (atendente humano) | Interface de atendimento — o que o cidadão vê e ouve |
| **Lane 3 — Backstage Caixa** | CIC, core bancário, plataforma de telefonia | Autenticação, roteamento de chamadas, crédito bancário |
| **Lane 4 — MTE / Dataprev** | Secretaria do Trabalho + Dataprev (BGSD) | Concessão, habilitação e armazenamento do status do benefício |
| **Lane 5 — Suporte / Regulação** | Anatel, Senacon/Procon, TCU/CGU, eSocial/CAGED | Regulação, controle externo, infraestrutura de dados |

> **Linha de visibilidade:** separa as Lanes 1–2 (visíveis ao cidadão) das Lanes 3–5 (invisíveis ao cidadão).

---

## 2. Blueprint por Etapa

### ◼ FASE 1 — PRÉ-URA: Requerimento e Habilitação

---

#### E1 — Demissão e geração dos dados

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | TRCT, aviso prévio escrito, guia do FGTS, formulário SD/CD |
| **Ação do cidadão** | Recebe o TRCT; guarda documentação; aguarda período de carência (mín. 7 dias) |
| **Frontstage** | Empregador entrega a documentação de rescisão ao trabalhador |
| **Backstage Caixa** | — |
| **MTE / Dataprev** | Empregador registra rescisão no eSocial/CAGED; Dataprev recebe os dados e alimenta a BGSD |
| **Suporte** | eSocial (MTE/Receita Federal); CAGED |
| ⚠️ **Fail point** | **FP0** — Empregador não registra a rescisão no eSocial → cidadão fica sem dados de elegibilidade na BGSD · *Frequência: Baixa* |

---

#### E2 — Requerimento do Seguro-Desemprego

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | Protocolo de requerimento emitido pelo gov.br / SINE; formulário SD/CD (se presencial) |
| **Ação do cidadão** | Acessa gov.br (ou app Carteira de Trabalho Digital, ou vai ao SINE); preenche dados; recebe protocolo. **Prazo: entre 7 e 120 dias após a rescisão** |
| **Frontstage** | Portal gov.br / SINE exibe formulário de requerimento; emite protocolo |
| **Backstage Caixa** | — |
| **MTE / Dataprev** | MTE recebe o requerimento; Dataprev abre processo na BGSD |
| **Suporte** | Portal gov.br (MGI); SINE (rede de postos do MTE) |
| ⚠️ **Fail point** | **FP0b** — Cidadão perde prazo de 120 dias → perde o direito ao benefício sem recurso simples · *Impacto: muito alto* |

---

#### E3 — Análise e decisão de habilitação

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | — (processo interno) |
| **Ação do cidadão** | Aguarda a decisão; pode acompanhar o status no gov.br ou app |
| **Frontstage** | Portal gov.br exibe status "em análise" |
| **Backstage Caixa** | — |
| **MTE / Dataprev** | MTE cruza dados do eSocial/CAGED (tempo de emprego) + INSS (não acumulação) + RAIS; Dataprev processa e atualiza a BGSD com a decisão |
| **Suporte** | BGSD (Dataprev); base INSS; RAIS; eSocial |
| ⚠️ **Fail point** | **FP2a** — Dado inconsistente no eSocial/CAGED → benefício bloqueado desde a origem; cidadão não sabe o motivo · *Frequência: Alta* |

---

#### E4 — Notificação de habilitação ao cidadão

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | E-mail / notificação no app gov.br com carta de habilitação: nº de parcelas, valor, datas previstas de crédito |
| **Ação do cidadão** | Lê a notificação; anota dados de parcelas e datas |
| **Frontstage** | gov.br / app envia notificação; exibe a carta de habilitação |
| **Backstage Caixa** | — |
| **MTE / Dataprev** | MTE emite decisão de habilitação; Dataprev atualiza BGSD com status "habilitado" e agenda das parcelas |
| **Suporte** | Sistema de notificações do gov.br (MGI) |
| ⚠️ **Fail point** | Notificação cai no spam ou e-mail desatualizado no cadastro gov.br → cidadão não sabe que foi habilitado e pode perder o prazo de saque |

---

### 🔵 FASE 2 — URA: Acompanhamento do Benefício

---

#### E5 — Evento-gatilho para a ligação à URA

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | — |
| **Ação do cidadão** | Identifica necessidade de consulta: parcela não creditada, dúvida sobre valor, suspeita de bloqueio, não recebeu notificação |
| **Frontstage** | — (o cidadão ainda não entrou em contato) |
| **Backstage Caixa** | — |
| **MTE / Dataprev** | Benefício existe (ou deveria existir) na BGSD |
| **Suporte** | — |
| ⚠️ **Fail point** | Cidadão tenta primeiro outros canais (gov.br, app Caixa Tem) e não resolve → frustração acumulada antes de ligar |

---

#### E6 — Ligação e navegação no menu da URA

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | — (canal de voz) |
| **Ação do cidadão** | Disca 0800 726 0207; ouve o menu; pressiona opções numéricas para chegar ao Seguro-Desemprego (2–3 toques de tecla) |
| **Frontstage** | URA apresenta saudação automática e menu principal em voz sintetizada; opções numéricas por tipo de serviço |
| **Backstage Caixa** | Sistema de URA recebe a ligação; plataforma de telefonia (PABX/VoIP) roteia para o módulo de Seguro-Desemprego |
| **MTE / Dataprev** | — |
| **Suporte** | Plataforma de URA (marca **[EM ABERTO]**); sistema de telefonia do BPO; Anatel (regula 0800) |
| ⚠️ **Fail point** | Menu não é intuitivo para cidadãos com baixa escolaridade digital; opção de Seguro-Desemprego pode estar enterrada em submenus |

---

#### E7 — Autenticação (CPF + data de nascimento)

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | — |
| **Ação do cidadão** | Digita CPF (11 dígitos) e data de nascimento (8 dígitos) no teclado do telefone; confirma os dados |
| **Frontstage** | URA solicita CPF; confirma ("O CPF informado é XXX.XXX.XXX-XX. Pressione 1 para confirmar"); solicita data de nascimento |
| **Backstage Caixa** | Sistema de URA valida CPF + data de nascimento contra o **CIC (Cadastro de Identificação do Cliente)** da Caixa |
| **MTE / Dataprev** | — (autenticação é feita pelo CIC da Caixa, não pela BGSD) |
| **Suporte** | CIC — sistema cadastral da Caixa |
| ⚠️ **Fail point** | **FP1** — CPF existe na BGSD mas não está cadastrado corretamente no CIC da Caixa (cidadão nunca foi cliente da Caixa; data de nascimento diverge entre sistemas) → autenticação falha; cidadão encaminhado ao presencial · *Frequência: Alta* |

---

#### E8 — Consulta e resposta da URA

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | — |
| **Ação do cidadão** | Ouve o resultado em voz sintetizada; anota informações manualmente (sem protocolo automático) |
| **Frontstage** | URA apresenta: (a) status do benefício (habilitado / bloqueado / inexistente); (b) nº de parcelas disponíveis; (c) valor da parcela; (d) data prevista de crédito. **Se bloqueado: informa apenas "benefício bloqueado" — sem detalhar a causa** |
| **Backstage Caixa** | Sistema de URA faz consulta à **BGSD/Dataprev** via **Integração B** (protocolo técnico **[EM ABERTO]**); aplica regras de negócio (status das parcelas, datas de competência, bloqueio ativo); formata o resultado em voz sintetizada |
| **MTE / Dataprev** | BGSD responde à consulta com o status do benefício; o **motivo de bloqueio está na BGSD mas não é transmitido para a URA** |
| **Suporte** | BGSD (Dataprev); Integração B (Caixa-Dataprev) |
| ⚠️ **Fail points** | **FP2b** — URA informa "bloqueado" mas não informa o motivo; causa raiz: fronteira institucional Caixa/Dataprev · *Frequência: Muito alta* <br>**FP4** — Integração B indisponível → URA retorna erro genérico sem prazo de normalização · *Frequência: Baixa, impacto alto* |

---

#### E9 — Encerramento ou transferência para atendimento humano

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | Protocolo de atendimento emitido pelo atendente humano — **não garantido**; não emitido automaticamente pela URA |
| **Ação do cidadão** | Se consulta resolveu: encerra a ligação. Se não: pressiona opção para atendente; aguarda fila de espera |
| **Frontstage** | URA oferece opção de atendente (tecla \*); informa tempo estimado de espera; reproduz música de espera. Atendente humano atende, acessa o sistema, orienta ou registra ocorrência |
| **Backstage Caixa** | Sistema de URA encaminha para fila de ACD (Automatic Call Distribution); BPO/contact center recebe a chamada; atendente acessa interface com dados da BGSD (status visível, mas motivo de bloqueio frequentemente inacessível) |
| **MTE / Dataprev** | BGSD disponível para consulta pelo atendente, mas acesso ao detalhe de bloqueio pode estar restrito ao sistema do MTE |
| **Suporte** | ACD / plataforma do BPO (operador **[EM ABERTO]**); interface de atendente; BGSD |
| ⚠️ **Fail points** | **FP3** — Tempo de espera >30 min em períodos de pico · *Frequência: Alta em picos* <br>**FP5** — Atendente não tem acesso ao motivo do bloqueio; sistemas Caixa e MTE/Dataprev não integrados em nível de detalhe · *Frequência: Alta* <br>**FP6** — URA não emite protocolo automático ao encerrar sem transferência → cidadão sem registro do atendimento · *Frequência: Muito alta* |

---

#### E10 — Pós-atendimento: ação do cidadão ou aguardo

| Camada | Conteúdo |
|--------|----------|
| **Evidência física** | SMS de crédito (Caixa Tem), extrato bancário, comprovante de saque — ou **nenhuma** (se o problema não foi resolvido) |
| **Ação do cidadão** | Age conforme orientação: aguarda o crédito; vai ao SINE ou agência presencial; aciona a Ouvidoria; ou **liga de novo** (ciclo de retrabalho) |
| **Frontstage** | Caixa Tem envia notificação de crédito (push/SMS) quando o valor é creditado na conta |
| **Backstage Caixa** | Core bancário da Caixa executa o crédito na conta vinculada ao CPF do cidadão |
| **MTE / Dataprev** | BGSD atualiza o status da parcela como "paga" |
| **Suporte** | Core bancário Caixa; Caixa Tem |
| ⚠️ **Fail points** | **FP7** — Conta Caixa inativa ou inexistente → crédito disponível mas inacessível; cidadão precisa ir à agência · *Frequência: Média* <br>**FP8** — Cidadão sem clareza sobre qual canal reclamar (Ouvidoria Caixa? Consumidor.gov.br? Portal MTE? Anatel?) → reclamação dispersa, baixa resolução · *Frequência: Sistêmica* |

---

## 3. Síntese dos Fail Points

| Cód. | Etapa | Descrição | Causa raiz | Frequência |
|------|-------|-----------|------------|------------|
| FP0 | E1 | Empregador não registra no eSocial | Descumprimento da obrigação pelo empregador | Baixa |
| FP0b | E2 | Cidadão perde prazo de 120 dias | Falta de orientação prévia; baixa escolaridade | Baixa (impacto alto) |
| FP2a | E3 | Inconsistência no eSocial/CAGED → bloqueio na origem | Dado incorreto do empregador no eSocial | Alta |
| FP1 | E7 | Falha de autenticação na URA | Divergência CIC Caixa × BGSD Dataprev | Alta |
| **FP2b** | **E8** | **"Benefício bloqueado" sem causa explicada** | **Fronteira institucional Caixa/Dataprev — motivo de bloqueio não é transmitido à URA** | **Muito alta** |
| FP4 | E8 | Integração BGSD indisponível → erro genérico | Manutenção ou falha da Integração B | Baixa (alto impacto) |
| FP3 | E9 | Tempo de espera >30 min para atendente humano | Subdimensionamento da equipe em picos de demanda | Alta em picos |
| FP5 | E9 | Atendente humano sem acesso ao motivo do bloqueio | Sistemas Caixa e MTE/Dataprev não integrados em nível de detalhe | Alta |
| **FP6** | **E9** | **URA não emite protocolo automático** | **Sistema projetado como canal informativo, sem responsabilidade rastreável** | **Muito alta** |
| FP7 | E10 | Conta Caixa inativa → crédito inacessível | Cidadão sem relacionamento bancário ativo com a Caixa | Média |
| FP8 | E10 | Fragmentação dos canais de reclamação | Ausência de responsável único pelo atendimento integrado | Sistêmica |

> **Diagnóstico central:** O canal informa sem resolver. O cidadão sai da ligação sabendo que tem um problema, mas sem saber o que fazer — e sem protocolo, sem responsável, sem prazo. A causa raiz não é tecnológica: é a **fronteira institucional mal projetada** entre a Caixa (operadora do canal) e o MTE/Dataprev (detentores da informação).

---

## 4. Normativos Aplicáveis

| Norma | Relevância para o Blueprint |
|-------|----------------------------|
| Lei nº 7.998/1990 | Define o benefício: parcelas, elegibilidade, valores mínimo e máximo |
| Decreto nº 12.797/2025 | Piso do benefício = salário mínimo R$ 1.621,00 em 2026 |
| Resolução CODEFAT nº 957/2022 | Metodologia de reajuste anual pelo INPC |
| Resolução ANATEL nº 714/2019 | 0800 gratuito e disponível 24h |
| Decreto nº 11.034/2022 (SAC) | Aplicabilidade ao canal **[EM ABERTO]** — não há decisão formal impondo o limite de 2 min de espera |
| Lei nº 13.303/2016 | Estatuto das Estatais — regula contratações da Caixa (BPO e plataforma URA) |
| Acórdão 135/2024-TCU | Determinou revisão do contrato Caixa-Dataprev — pode afetar disponibilidade da BGSD |

---

## 5. Lacunas AS-IS

As seguintes informações são necessárias para completar o blueprint mas não estão disponíveis em fonte pública:

| Lacuna | Onde buscar |
|--------|-------------|
| Operador atual do BPO/contact center da Caixa (pós-CTIS Tecnologia, PE 59/2017) | PNCP; licitacoes.caixa.gov.br; LAI |
| Marca e versão da plataforma de URA em uso | Idem |
| Protocolo técnico da Integração B (Caixa-Dataprev para consulta pela URA) | Contrato Caixa-Dataprev via LAI; acórdãos TCU |
| SLA de disponibilidade da BGSD para o canal de atendimento | Contrato via LAI; relatórios de gestão Dataprev |
| TMA (tempo médio de atendimento) e FCR (taxa de resolução no primeiro contato) | Relatórios de ouvidoria da Caixa |
| Volumetria de chamadas mensais à URA do Seguro-Desemprego | Relatórios de gestão Caixa; pedido LAI |
| Aplicabilidade formal do Decreto 11.034/2022 ao canal | Consultar Senacon, Bacen, normativos MTE |