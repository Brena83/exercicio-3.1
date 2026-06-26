# Transcript do Grill-Me — Service Blueprint AS-IS: URA da Caixa / Seguro-Desemprego

**Objetivo:** Destilação do Service Blueprint AS-IS a partir da pesquisa adversarial (v1–v3 + auditorias).  
**Método:** Série de perguntas focadas em cada camada e etapa do blueprint, com o aluno respondendo com base nos relatórios da Parte B.  
**Sessão:** junho de 2026

---

## Rodada 1 — Contexto e Escopo do Serviço

**Grill-me:** Antes de entrar no blueprint, preciso que você delimite com precisão o serviço que está sendo mapeado. Me diga: (a) qual é o serviço, (b) qual é o canal específico, (c) qual é o evento que inicia a jornada do cidadão neste canal, e (d) o que este canal **não** faz — ou seja, qual é o limite do escopo.

**Aluno:** O serviço é o **Seguro-Desemprego** — benefício pago ao trabalhador formal demitido sem justa causa. O canal específico é a **URA (Unidade de Resposta Audível) da Caixa Econômica Federal**, acessada pelo número 0800 726 0207, disponível 24 horas. O evento que inicia a jornada **neste canal** é o cidadão já ter o benefício habilitado (ou suspeitar que está habilitado) e precisar verificar o status, o valor ou a data de crédito da parcela. O canal **não** serve para requerer o Seguro-Desemprego — isso é feito no gov.br, no app Carteira de Trabalho Digital ou no SINE presencial. A URA também não desbloqueia benefícios — apenas informa o bloqueio. Não processa saques — o cidadão precisa da conta Caixa ativa para isso.

**Grill-me:** Boa delimitação. Mas você disse "cidadão já ter o benefício habilitado ou suspeitar". Isso é impreciso. O que acontece quando o cidadão liga antes da habilitação? O sistema simplesmente informa que não há benefício, ou há uma mensagem específica? Essa diferença importa para o blueprint.

**Aluno:** Boa observação. Quando o cidadão liga antes da habilitação — ou seja, antes de o MTE processar o requerimento — a URA não encontra benefício vinculado ao CPF. A mensagem é genérica: "Não localizamos benefício do Seguro-Desemprego para o CPF informado." O cidadão pode interpretar isso como (a) ainda não foi processado, (b) foi negado, ou (c) erro no sistema — e a URA não diferencia esses três casos. Isso gera um fail point logo no início da jornada de acompanhamento: o cidadão que ligou cedo demais recebe a mesma mensagem do cidadão que foi negado.

---

## Rodada 2 — Jornada Pré-URA: o que o cidadão precisa fazer antes de ligar

**Grill-me:** Você disse que a URA é para acompanhamento pós-habilitação. Me descreva as etapas que o cidadão percorre **antes** de chegar à URA, incluindo prazos e atores envolvidos. Seja específico sobre quem faz o quê.

**Aluno:** A jornada pré-URA tem quatro etapas. **Etapa 1 — Demissão:** o empregador registra a rescisão no eSocial. A Dataprev recebe esses dados e alimenta a BGSD (Base de Gestão do Seguro-Desemprego). O trabalhador recebe o TRCT (Termo de Rescisão do Contrato de Trabalho). **Etapa 2 — Requerimento:** o trabalhador tem entre 7 e 120 dias após a rescisão para requerer o benefício — se perder esse prazo, perde o direito. O requerimento pode ser feito pelo gov.br, app Carteira de Trabalho Digital, SINE ou agência da Caixa. **Etapa 3 — Análise pelo MTE/Dataprev:** o MTE analisa o requerimento cruzando dados do eSocial/CAGED (tempo de emprego), INSS (não acumulação) e RAIS. A Dataprev processa o cruzamento e devolve a decisão ao MTE. O prazo típico é alguns dias para casos sem pendências. **Etapa 4 — Habilitação e notificação:** o MTE emite a decisão. Se aprovado, o cidadão recebe notificação pelo canal de requerimento (e-mail, app). A carta de habilitação informa o número de parcelas e as datas previstas. **Somente após a Etapa 4 a URA tem informação útil para o cidadão.**

**Grill-me:** Certo. Agora me diga: quais documentos o cidadão tem em mãos ao chegar à Etapa 2 (requerimento) e quais desses documentos ele precisa ter em mãos ao ligar para a URA (Etapa 6 da jornada)?

**Aluno:** Para o requerimento (Etapa 2), o cidadão precisa do TRCT, de dados do empregador (CNPJ, data de admissão e demissão) e do próprio CPF. Para ligar à URA, o cidadão precisa apenas do **CPF** e da **data de nascimento** — não precisa do número do benefício nem de nenhum outro documento. Isso é ao mesmo tempo um ponto positivo (baixa barreira de acesso) e um ponto de risco (autenticação fraca: CPF + data de nascimento são dados de conhecimento amplo, não há segundo fator de autenticação).

---

## Rodada 3 — Frontstage: o que o cidadão experimenta na ligação

**Grill-me:** Descreva o fluxo de uma ligação típica à URA do ponto de vista do cidadão — o que ele ouve, o que ele faz, e quanto tempo leva cada etapa. Seja específico sobre os menus e as mensagens.

**Aluno:** O cidadão disca 0800 726 0207. A URA atende imediatamente com saudação automática. O menu principal oferece opções por tipo de serviço — para Seguro-Desemprego, o cidadão navega até a opção de benefícios/Seguro-Desemprego (tipicamente 2 a 3 toques de tecla). A URA então solicita o CPF (11 dígitos, digitados no teclado do telefone) e, em seguida, a data de nascimento (8 dígitos). Após confirmar os dados, a URA processa a consulta (alguns segundos de silêncio ou música de espera) e apresenta o resultado em voz sintetizada: número de parcelas disponíveis, valor e data prevista de crédito. O tempo total de uma consulta resolvida pela URA é de 3 a 5 minutos. Se o benefício estiver bloqueado, a URA informa apenas "benefício bloqueado" e oferece a opção de transferência para atendente humano. Se o cidadão aceitar a transferência, entra em fila de espera — que em períodos de pico pode ultrapassar 30 minutos.

**Grill-me:** Você mencionou "alguns segundos de silêncio" durante a consulta. Isso é um detalhe pequeno, mas importa para a experiência do cidadão. O que acontece se a integração com a Dataprev falhar nesse momento? O cidadão recebe alguma indicação de que houve problema técnico?

**Aluno:** Não. Quando a integração com a Dataprev está indisponível, a URA retorna uma mensagem genérica de erro — algo como "Não foi possível processar sua solicitação. Tente novamente mais tarde." O cidadão não sabe se é uma falha temporária (que se resolverá em minutos) ou uma manutenção programada (que durará horas). Não há informação sobre prazo de normalização. Esse é o Fail Point FP4 — baixa frequência, mas alto impacto porque atinge todos os usuários simultaneamente durante a janela de indisponibilidade.

---

## Rodada 4 — Backstage: o que acontece nos bastidores da consulta

**Grill-me:** Me explique o que acontece nos sistemas e entre os atores institucionais durante a Etapa E8 (consulta e resposta da URA) — especificamente: qual sistema a URA consulta, quem mantém esse sistema, e como os dados chegam até ele.

**Aluno:** Durante a E8, a URA realiza uma consulta à **BGSD (Base de Gestão do Seguro-Desemprego)**, mantida pela **Dataprev**. Os dados que chegam à BGSD foram alimentados em etapas anteriores: pelo empregador via eSocial/CAGED (dados de rescisão), pelo MTE (decisão de habilitação) e pela própria Caixa (dados de conta bancária vinculada). A consulta da URA à BGSD ocorre via integração Caixa-Dataprev. A natureza técnica dessa integração — se é em tempo real ou com cache, se usa API REST ou outro protocolo — não está disponível em fonte pública primária. É plausível que seja uma consulta síncrona via webservice, dada a necessidade de informação atualizada ao cidadão, mas isso permanece como hipótese técnica, não fato confirmado.

**Grill-me:** Você distinguiu duas integrações — uma entre MTE e Dataprev (para concessão) e outra entre Caixa e Dataprev (para consulta pela URA). Isso é importante. Agora me diga: o que é diferente entre o que a Dataprev enxerga na integração de concessão e o que a URA consegue consultar? O cidadão que foi negado e o cidadão cujo benefício está bloqueado por irregularidade recebem a mesma informação da URA?

**Aluno:** Essa é uma distinção crítica para o blueprint. A BGSD contém o status detalhado do benefício — incluindo o motivo do bloqueio (cruzamento com INSS, irregularidade no eSocial, pendência documental). Porém, a URA **não apresenta o motivo do bloqueio ao cidadão** — apresenta apenas o status ("bloqueado"). Isso significa que: (a) o cidadão negado recebe "benefício não localizado" (sem CPF vinculado a nenhum processo); (b) o cidadão com benefício habilitado mas bloqueado recebe "benefício bloqueado" (sem causa); (c) o cidadão habilitado e com parcela disponível recebe o valor e a data. Os três cenários têm respostas distintas na URA, mas o cenário (b) — que é o mais frequente e o mais frustrante — não oferece informação suficiente para que o cidadão saiba o que fazer. O atendente humano, no caso (b), frequentemente também não tem acesso ao detalhe do motivo do bloqueio — essa informação está na BGSD/Dataprev, não nos sistemas da Caixa.

---

## Rodada 5 — Fail Points: priorização e causa raiz

**Grill-me:** Você identificou ao menos 7 fail points (FP0 a FP7). Para o blueprint AS-IS ser útil como diagnóstico, preciso que você priorize: quais são os **três fail points mais críticos** do ponto de vista do cidadão, e qual é a **causa raiz** de cada um — não o sintoma, a causa.

**Aluno:** Os três fail points mais críticos, com suas causas raiz:

**1º — FP2b: Status "bloqueado" sem causa explicada (E8)**  
Sintoma: cidadão recebe "benefício bloqueado" e não sabe o que fazer.  
Causa raiz: **fragmentação de sistemas e responsabilidades**. O motivo do bloqueio está na BGSD/Dataprev (sistema do MTE), mas o canal de atendimento é da Caixa. A Caixa não tem acesso ao detalhe do motivo do bloqueio — apenas ao status. Isso não é falha tecnológica isolada — é resultado de uma **fronteira institucional** entre MTE/Dataprev e Caixa que não foi projetada para o atendimento ao cidadão. A consequência: o cidadão fica em um limbo entre dois órgãos.

**2º — FP6: Ausência de protocolo ao encerrar a URA sem transferência (E9)**  
Sintoma: cidadão não tem registro da consulta e não consegue referenciar o atendimento em contatos futuros.  
Causa raiz: **a URA foi projetada como canal informativo, não como canal de atendimento com responsabilidade**. Sistemas de SAC modernos geram número de protocolo automaticamente. A URA da Caixa para o Seguro-Desemprego trata a consulta como uma transação efêmera — quando deveria gerar um registro rastreável, especialmente quando o resultado é negativo (bloqueio, erro).

**3º — FP1: Falha de autenticação (E7)**  
Sintoma: cidadão informa CPF + data de nascimento corretamente, mas não é reconhecido.  
Causa raiz: **inconsistência cadastral entre sistemas**. O CIC (cadastro da Caixa) e a BGSD (Dataprev) podem ter dados divergentes — especialmente para cidadãos que nunca foram clientes da Caixa ou que têm CPF com dados inconsistentes no cadastro do empregador. A autenticação valida contra o CIC da Caixa, mas o benefício está na BGSD. Se o CPF existe na BGSD mas não está no CIC com os dados corretos, a autenticação falha mesmo que o cidadão seja elegível.

**Grill-me:** Ótima análise. Uma última pergunta desta rodada: os três fail points têm solução dentro do escopo do canal (URA da Caixa) ou exigem mudança fora dele?

**Aluno:** Nenhum dos três tem solução completa dentro do escopo da URA sozinha. FP2b exige que a Caixa tenha acesso ao motivo do bloqueio na BGSD/Dataprev — isso é uma questão de governança e contrato entre os órgãos, não de tecnologia da URA. FP6 pode ser resolvido com uma mudança na URA (geração automática de protocolo), mas é uma decisão da Caixa que precisa de aprovação e investimento. FP1 exige sincronização entre o CIC da Caixa e a BGSD/Dataprev — novamente, integração entre sistemas de dois órgãos diferentes. Isso ilustra a natureza sistêmica dos problemas: a URA é um ponto de contato, mas as causas raiz estão na arquitetura institucional e de dados do ecossistema mais amplo.

---

## Rodada 6 — Normativos e Limites Regulatórios

**Grill-me:** Quais normas regulam especificamente o atendimento ao cidadão neste canal? E existe alguma norma que o serviço deveria cumprir mas que, com base no que você pesquisou, provavelmente não cumpre?

**Aluno:** As normas que se aplicam mais claramente são: a **Resolução ANATEL** sobre serviços 0800 (gratuidade e disponibilidade 24h — essa a Caixa cumpre); as normas da **Lei 7.998/1990** sobre o programa em si; e o **Decreto 11.034/2022** (SAC). Quanto ao Decreto 11.034/2022, há uma ambiguidade: ele regulamenta o SAC de fornecedores de produtos e serviços ao consumidor. A Caixa, como agente pagador do Estado, não é claramente uma "fornecedora de serviço ao consumidor" neste contexto — é uma executora de política pública. Não há decisão publicada que imponha formalmente o limite de 2 minutos de espera ao canal do Seguro-Desemprego. Se o Decreto se aplicasse, o serviço provavelmente não cumpriria — os dados de reclamações indicam esperas de 30+ minutos em picos. Mas essa aplicabilidade permanece juridicamente indefinida. Isso, em si, é um problema de governança: o cidadão não tem clareza sobre qual regulador reclamar quando o atendimento falha.

**Grill-me:** Você disse "o cidadão não tem clareza sobre qual regulador reclamar". Isso é um fail point regulatório. Como isso aparece na prática? Onde o cidadão vai reclamar hoje?

**Aluno:** Na prática, o cidadão tenta múltiplos canais de reclamação de forma não coordenada: (a) **Consumidor.gov.br** (plataforma da Senacon — indicado para reclamações de empresas privadas, uso questionável para banco estatal em papel de agente público); (b) **Ouvidoria da Caixa** (canal interno, resposta lenta, sem poder regulatório); (c) **Portal de Reclamações do MTE** (quando o problema é de concessão, não de atendimento); (d) **Anatel** (quando o problema é o próprio serviço de telefonia, não o conteúdo da resposta). A fragmentação de canais de reclamação reflete a fragmentação de responsabilidades no serviço — nenhum órgão é claramente o responsável pelo atendimento como um todo. Isso pode ser adicionado ao blueprint como um fail point regulatório/sistêmico (FP8).

---

## Rodada 7 — Síntese: o Blueprint AS-IS em uma imagem

**Grill-me:** Última rodada. Com base em tudo o que discutimos, se você tivesse que descrever o Service Blueprint AS-IS deste serviço em **três frases** — capturando a essência do funcionamento atual e o principal problema estrutural — o que você diria?

**Aluno:** O Seguro-Desemprego via URA da Caixa funciona como um **serviço de consulta em múltiplas camadas desconectadas**: o MTE/Dataprev decide e processa o benefício, a Caixa opera o canal de atendimento, e o cidadão fica no meio sem visibilidade do que cada camada enxerga. O principal problema estrutural não é tecnológico — é **a fronteira institucional mal projetada**: a URA pode dizer "bloqueado" mas não pode dizer "por quê", porque o porquê está na Dataprev, não na Caixa. O resultado é um canal de atendimento que **informa sem resolver**: o cidadão sai da ligação sabendo que tem um problema, mas sem saber o que fazer para resolvê-lo — e sem protocolo, sem responsável, sem prazo.

**Grill-me:** Essa síntese é precisa e vai direto ao ponto. Está pronto para construir o Blueprint AS-IS formal a partir do que destilamos aqui. Pode prosseguir.