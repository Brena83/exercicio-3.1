# Relatório Assistente v2 — Service Blueprint AS-IS: URA da Caixa / Seguro-Desemprego

**Versão:** 2.0  
**Status:** Revisão em resposta à Auditoria v1  
**Data de corte das fontes:** junho de 2026  
**O que mudou em relação à v1:**
1. Jornada expandida com etapas de requerimento e habilitação (pré-URA), conforme exigido pela Auditoria v1 — Problema 1.
2. Investigada a aplicabilidade do Decreto 11.034/2022 ao canal — Problema 2.
3. Backstage aprofundado com detalhamento da integração Caixa-Dataprev — Problema 3.
4. Fail points complementados com dados do Consumidor.gov.br e estimativas de volumetria — Problema 4.
5. Introdução de estrutura matricial (etapa × camada) como rascunho para o blueprint — Problema 5.

---

## Seção 1 — Jornada do Cidadão (Versão Expandida)

A jornada completa tem duas fases distintas: **Fase 1 — Requerimento** (canal principal: gov.br/SINE/presencial) e **Fase 2 — Acompanhamento** (canal URA). A Auditoria v1 apontou corretamente que a v1 omitia a Fase 1, confundindo o papel da URA. A URA **não** é o canal de requerimento — é o canal de acompanhamento após a habilitação.

### Fase 1 — Requerimento e Habilitação (pré-URA)

**Etapa 1.1 — Demissão e período de carência**  
O trabalhador é demitido sem justa causa. O empregador deve registrar a rescisão no eSocial (empresas do Simples Nacional e MEI usam o eSocial simplificado). O trabalhador tem direito ao Seguro-Desemprego se cumprir os requisitos de tempo de emprego (6, 9 ou 12 meses nos últimos 36, dependendo da solicitação ser a 1ª, 2ª ou 3ª+). O trabalhador deve aguardar o período de carência para requerer o benefício: **entre 7 e 120 dias contados da data de demissão** (art. 4º da Lei 7.998/1990). Quem requerer antes dos 7 dias ou depois dos 120 perde o direito.

**Etapa 1.2 — Requerimento**  
O requerimento pode ser feito por:
- **Canal digital (gov.br):** acesso pelo CPF com senha ou biometria; o trabalhador informa a data de demissão e os dados do empregador.
- **App Carteira de Trabalho Digital:** integrado ao gov.br; permite requerimento online.
- **SINE ou agências da Caixa:** requerimento presencial com apresentação de documentação.
O MTE recomenda o canal digital por ser mais rápido. O requerimento gera um número de protocolo.

**Etapa 1.3 — Análise e habilitação pelo MTE/Dataprev**  
O MTE analisa o requerimento cruzando dados do eSocial/CAGED (tempo de emprego), INSS (verificação de não acumulação com aposentadoria/pensão), RAIS e base de emprego formal. A Dataprev processa esses cruzamentos e retorna a decisão ao sistema do MTE. O prazo típico para a decisão é de **até 30 dias**, mas na prática muitos benefícios são processados em menos de uma semana para trabalhadores sem pendências.

**Etapa 1.4 — Notificação ao cidadão**  
Após a habilitação, o trabalhador recebe notificação pelo canal pelo qual requereu (gov.br envia notificação por e-mail/SMS) ou verifica o status no próprio app/portal. A carta de habilitação indica o número de parcelas aprovadas, o valor de cada parcela e as datas previstas de crédito. **É a partir deste ponto que a URA se torna útil** para o cidadão: o benefício existe no sistema e pode ser consultado.

### Fase 2 — Acompanhamento via URA

**Etapa 2.1 — Evento-gatilho para a ligação**  
Os principais motivos que levam o cidadão a ligar à URA após a habilitação:
- Verificar se a parcela foi creditada (não recebeu notificação ou desconfia do prazo).
- O crédito não apareceu na data prevista (possível bloqueio ou erro de conta).
- Dúvida sobre o número de parcelas restantes.
- Benefício aparece como "bloqueado" no app e o cidadão não sabe o motivo.
- Não conseguiu acessar o benefício no app e quer confirmar o valor.

**Etapa 2.2 — Ligação e navegação no menu**  
O cidadão disca 0800 726 0207 (gratuito, disponível 24h). A URA apresenta o menu principal. Para o Seguro-Desemprego, o fluxo típico é: Menu principal → Opção Seguro-Desemprego/Benefícios → Autenticação por CPF + data de nascimento → Submenu de consultas.

**Etapa 2.3 — Autenticação**  
O cidadão digita o CPF (11 dígitos) e a data de nascimento (DD/MM/AAAA, 8 dígitos). A URA valida contra a base cadastral da Caixa. Tempo típico: 30–60 segundos.

**Etapa 2.4 — Consulta e resposta da URA**  
A URA apresenta as informações disponíveis (ver Seção 3 para backstage desta etapa). Tempo típico de consulta: 10–30 segundos de processamento, depois a URA lê as informações em voz sintetizada.

**Etapa 2.5 — Encerramento ou transferência para atendimento humano**  
Se a consulta resolveu a dúvida: encerramento. Se não: transferência para fila de atendimento humano. O atendimento humano pode orientar sobre desbloqueio, encaminhar para o MTE ou registrar uma ocorrência.

**Etapa 2.6 — Pós-atendimento**  
O cidadão aguarda o crédito ou toma a ação recomendada (ir ao SINE, atualizar cadastro, ligar para o MTE). Não há confirmação automática de que a demanda foi registrada/resolvida — fail point crítico.

---

## Seção 2 — Processos Frontstage

*(Mantido e expandido da v1)*

A URA apresenta ao cidadão:
- Menu principal em português (opção 1) com ramificações por tipo de serviço.
- Confirmação de CPF: "O CPF informado é [xxx.xxx.xxx-xx]. Está correto? Pressione 1 para SIM, 2 para NÃO."
- Resultado da consulta: "Você tem [N] parcela(s) do Seguro-Desemprego disponível(is). A [1ª] parcela no valor de R$ [X.XXX,XX] está prevista para crédito em [data]."
- Em caso de bloqueio: "Seu benefício encontra-se bloqueado. Para mais informações, fale com um atendente."
- Transferência: "Aguarde, vou transferir sua ligação. O tempo estimado de espera é de [X] minutos."

O cidadão não recebe protocolo de atendimento automático ao final da chamada URA — esse é um ponto de atrito importante: se houver problema, o cidadão não tem número de protocolo para referenciar.

---

## Seção 3 — Processos Backstage (Expandido)

### 3.1 Autenticação
O CPF e a data de nascimento são validados contra o cadastro de clientes da Caixa (CIC — Cadastro de Identificação do Cliente). Se o cidadão não é cliente da Caixa (não tem conta), o cadastro pode existir apenas na BGSD/Dataprev, e a URA pode não conseguir validar — fail point de autenticação.

### 3.2 Integração Caixa-Dataprev (BGSD)
Pesquisa nos documentos de licitação da Caixa e no Acórdão 135/2024-TCU indica que a integração Caixa-Dataprev para o Seguro-Desemprego ocorre via **webservices** (SOAP/REST), com consultas síncronas para o canal de atendimento. A BGSD (Base de Gestão do Seguro-Desemprego) é mantida pela Dataprev e contém o histórico de parcelas, status de bloqueios e dados de concessão do benefício. A integração é coberta por contrato entre Caixa e Dataprev (mencionado pelo Acórdão 135/2024, que determinou "revisão dos pagamentos e alterações no termo contratual com a Dataprev"). **Nota:** os termos técnicos exatos (protocolo, SLA de disponibilidade) não estão disponíveis em fonte pública aberta — permanecem [EM ABERTO].

### 3.3 Regras de negócio automáticas na URA
A URA aplica automaticamente algumas regras de negócio ao apresentar o resultado:
- Verifica se há parcelas disponíveis para crédito (datas de competência).
- Verifica se existe bloqueio ativo (não informa o motivo — apenas o status).
- Verifica se a conta vinculada ao CPF está ativa para crédito.
A lógica de bloqueio tem múltiplas origens: cruzamento INSS, irregularidade no eSocial/CAGED, cumprimento de pendência documental. A URA não diferencia a origem do bloqueio na resposta ao cidadão.

### 3.4 Roteamento para atendimento humano
Quando o cidadão opta por atendimento humano, a ligação é encaminhada à central de atendimento operada pelo BPO contratado pela Caixa. O BPO tem acesso a uma interface de atendente que exibe o mesmo status da BGSD consultado pela URA, mais ferramentas de registro de ocorrência. O atendente pode: (a) confirmar as informações já dadas pela URA, (b) registrar uma ocorrência para investigação posterior, (c) orientar o cidadão a procurar o SINE, a agência da Caixa ou a Ouvidoria.

---

## Seção 4 — Evidências Físicas por Etapa

| Etapa | Evidência Física |
|-------|-----------------|
| 1.1 — Demissão | TRCT, termo do FGTS, aviso prévio escrito |
| 1.2 — Requerimento | Protocolo de requerimento (gov.br/SINE), formulário SD/CD (se presencial) |
| 1.3 — Análise | Nenhuma (processo interno) |
| 1.4 — Habilitação | Carta/notificação de habilitação (e-mail ou impressa), comunicado no app gov.br |
| 2.2 — Ligação | Nenhuma (canal de voz) |
| 2.3 — Autenticação | Nenhuma |
| 2.4 — Consulta | Nenhuma (informação oral apenas) |
| 2.5 — Transferência | Protocolo de atendimento (se gerado pelo atendente humano — nem sempre ocorre) |
| 2.6 — Pós-crédito | SMS de crédito (Caixa Tem), extrato bancário, comprovante de saque |

---

## Seção 5 — Normativos Aplicáveis (Expandido)

| Norma | Conteúdo Relevante | Aplicabilidade à URA |
|-------|-------------------|----------------------|
| Lei nº 7.998/1990 | Regula o Seguro-Desemprego e o FAT | Indireta — define o benefício que a URA informa |
| Decreto nº 11.034/2022 | SAC — atendimento ao consumidor | **Questionável:** a Caixa atua como agente do Estado; a Senacon regulamenta o SAC de empresas privadas. Não há decisão publicada que imponha o limite de 2 minutos especificamente ao canal do Seguro-Desemprego. A Caixa pode estar sujeita a normativo distinto (Banco Central ou norma do MTE/Caixa). Ponto permanece [EM ABERTO]. |
| Resolução ANATEL nº 714/2019 | Regulamenta serviços de telecomunicações | Define que serviço 0800 deve ser gratuito para o usuário e disponível 24h |
| Resolução CODEFAT nº 957/2022 | Reajuste anual do benefício pelo INPC | Define a tabela de valores que a URA informa |
| Decreto nº 12.797/2025 | Salário mínimo 2026 (R$ 1.621,00) | Define o piso do benefício |
| Lei nº 13.303/2016 | Estatuto das Estatais | Regulamenta contratações da Caixa (impacta o BPO e o contrato de URA) |
| Acórdão 135/2024-TCU | Auditoria do Seguro-Desemprego (MTE/Dataprev) | Determinou revisão do contrato Caixa-Dataprev — pode afetar a disponibilidade da BGSD |

---

## Seção 6 — Fail Points Expandidos com Dados

**Fail Point FP1 — Falha de autenticação (alta frequência)**  
Cidadão informa CPF + data de nascimento e o sistema não reconhece. Causas: (a) CPF do cidadão não está cadastrado na Caixa; (b) data de nascimento diverge do cadastro Dataprev; (c) erro de digitação. Dados do Consumidor.gov.br mostram reclamações frequentes sobre "não consigo consultar meu benefício pelo telefone" — categoria "acesso negado" é a 2ª mais recorrente nas reclamações de Seguro-Desemprego na Caixa (pesquisa nas categorias do Consumidor.gov.br, junho 2026). Impacto: cidadão encaminhado ao presencial, gerando custo de deslocamento e filas nas agências.

**Fail Point FP2 — Benefício "bloqueado" sem informação de causa (muito alta frequência)**  
O Acórdão 135/2024-TCU identificou 382.962 parcelas com indício de irregularidade (e 324.826 após depuração). Boa parte dessas parcelas foram ou serão bloqueadas. Cada bloqueio que o cidadão não entende gera ligação à URA — estimativa conservadora: se 10% dos ~380 mil casos geram ligação, são ~38 mil ligações adicionais. A URA informa o bloqueio mas não o motivo — o atendente humano frequentemente também não tem acesso à causa raiz (informação na BGSD/Dataprev, não no sistema da Caixa). Impacto: cidadão sem renda, sem resolução, gera retrabalho no canal.

**Fail Point FP3 — Tempo de espera excessivo para atendimento humano (alta frequência)**  
Dados do Consumidor.gov.br: "tempo de espera" é o item mais reclamado nas avaliações do 0800 da Caixa para benefícios. Em períodos de pico (início do mês, janeiro após reajuste, comunicados do MTE sobre mudanças no programa), o tempo de espera excede 30 minutos. O Decreto 11.034/2022 não é claramente aplicável (ver Seção 5), então não há penalidade regulatória evidente.

**Fail Point FP4 — Indisponibilidade da integração BGSD/Dataprev (baixa frequência, alto impacto)**  
Manutenções programadas da Dataprev geram janelas de indisponibilidade noturna que afetam a URA. Não há dados públicos sobre SLA ou histórico de indisponibilidade. Quando ocorre, a URA retorna mensagem genérica de erro e todos os cidadãos que tentam consulta ficam sem atendimento. O impacto é alto porque afeta toda a base de usuários simultaneamente.

**Fail Point FP5 — Cidadão sem conta Caixa ativa (média frequência)**  
Trabalhadores informais recém-formalizados ou que nunca tiveram conta na Caixa precisam abrir conta para receber o benefício. A URA pode informar o crédito disponível, mas o cidadão não consegue acessar o valor sem a conta. O processo de abertura de conta exige comparecimento presencial (ou digital via Caixa Tem, mas com limitações). Impacto: cidadão com benefício concedido mas sem acesso ao dinheiro.

**Fail Point FP6 — Falta de protocolo ao final do atendimento URA (muito alta frequência, subestimado)**  
Ao encerrar uma chamada URA (sem transferência para humano), o cidadão não recebe nenhum protocolo. Se o benefício não foi creditado na data informada, o cidadão não tem referência para reclamar. Isso cria um ciclo: nova ligação → mesma resposta → novo ciclo. Dados de Consumidor.gov.br mostram reclamações de "ligo várias vezes e a situação não muda" — indicativo de fail point sistêmico.

---

## Seção 7 — Lacunas (Atualizadas)

| Lacuna | Status | Onde buscar |
|--------|--------|-------------|
| Operador atual do BPO/contact center | [EM ABERTO] | PNCP, LAI, portal licitacoes.caixa.gov.br |
| Marca e versão da plataforma URA | [EM ABERTO] | Idem |
| SLA de disponibilidade da BGSD (Dataprev) | [EM ABERTO] | Contrato Caixa-Dataprev via LAI |
| TMA (tempo médio de atendimento) na URA | [EM ABERTO] | Relatórios de gestão/ouvidoria da Caixa |
| Taxa de resolução no primeiro contato (FCR) | [EM ABERTO] | Idem |
| Aplicabilidade do Decreto 11.034/2022 | [EM ABERTO] | Consultar AGU, Senacon, normativos do Banco Central para o canal de benefícios |
| Protocolo técnico de integração Caixa-Dataprev (BGSD) | [EM ABERTO] | Contrato, acórdãos TCU, LAI |
