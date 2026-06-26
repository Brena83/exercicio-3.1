# Relatório Assistente v1 — Service Blueprint AS-IS: URA da Caixa / Seguro-Desemprego

**Versão:** 1.0 (inicial)  
**Data de corte das fontes:** junho de 2026  
**Escopo:** Jornada do cidadão e processos operacionais do atendimento ao Seguro-Desemprego via URA da Caixa (0800 726 0207).

---

## Seção 1 — Jornada do Cidadão (Etapas Sequenciais)

A jornada do cidadão que acessa o Seguro-Desemprego via URA da Caixa começa, na maioria dos casos, **após a demissão sem justa causa**. O evento-gatilho é a rescisão do contrato de trabalho, que gera o direito ao benefício conforme a Lei nº 7.998/1990.

**Etapa 0 — Evento-gatilho (pré-canal)**  
O trabalhador é demitido. O empregador tem obrigação legal de comunicar a rescisão ao eSocial/CAGED, e a Dataprev recebe esses dados para processar a elegibilidade do trabalhador. O cidadão recebe o TRCT (Termo de Rescisão do Contrato de Trabalho) e, normalmente, orientações básicas do RH sobre como solicitar o Seguro-Desemprego. Muitos trabalhadores, especialmente de empresas menores, chegam ao canal sem essa orientação prévia.

**Etapa 1 — Busca por informação (pré-ligação)**  
Antes de ligar, o cidadão geralmente tenta outros canais: consulta o portal gov.br, tenta o app Caixa Tem, ou pergunta a conhecidos. Muitas vezes, a dúvida não é resolvida por esses canais — seja por dificuldade de navegação, seja porque o benefício ainda não foi habilitado no sistema — e o cidadão parte para a ligação à URA.

**Etapa 2 — Ligação e atendimento pela URA**  
O cidadão disca 0800 726 0207. A URA atende automaticamente e apresenta o menu principal com as opções disponíveis. As opções mais utilizadas para o Seguro-Desemprego são: (1) consulta ao status do benefício/parcelas e (2) informações sobre habilitação e requerimento. O cidadão deve informar o CPF e a data de nascimento para autenticação.

**Etapa 3 — Autenticação e consulta**  
Após a autenticação (CPF + data de nascimento), a URA consulta a base de dados integrada com a Dataprev e apresenta as informações disponíveis: status do benefício (habilitado, pendente, bloqueado), número de parcelas disponíveis, valor e data prevista de crédito.

**Etapa 4 — Resolução ou transferência**  
Se a consulta resolve a dúvida, o cidadão encerra a ligação. Se não, é transferido para atendente humano. A fila de espera para atendimento humano é variável — pode ser de alguns minutos a mais de 30 minutos em períodos de pico.

**Etapa 5 — Pós-atendimento**  
Após o crédito da parcela, o cidadão recebe uma notificação (SMS ou push no app Caixa Tem, se cadastrado). O cidadão acessa o valor na agência, caixa eletrônico ou pelo Caixa Tem.

---

## Seção 2 — Processos Frontstage (o que o cidadão vê/ouve)

A URA da Caixa utiliza um sistema de voz automatizado que guia o cidadão por menus de opções numéricas. O script típico inclui:

- Saudação automática com identificação do canal ("Caixa, bom dia. Para continuar em português, pressione 1.")
- Menu principal com opções por tipo de serviço (Seguro-Desemprego, conta bancária, cartão, etc.)
- Solicitação do CPF (digitado pelo teclado numérico do telefone)
- Solicitação da data de nascimento (confirmação de identidade)
- Leitura do status do benefício (voz sintetizada)
- Opção de transferência para atendente humano a qualquer momento (geralmente tecla 0 ou *9)

O cidadão não tem visibilidade dos processos de bastidor — não sabe se o sistema está consultando a Dataprev, se há uma fila de processamento, ou por que o benefício aparece como "pendente". As mensagens de erro da URA são genéricas ("Não foi possível processar sua solicitação. Tente novamente.") e não orientam o cidadão sobre a causa nem a solução.

---

## Seção 3 — Processos Backstage

**Autenticação:** A URA captura o CPF e a data de nascimento digitados e os envia para validação. A validação é feita contra a base cadastral da Caixa e/ou a BGSD/Dataprev. Não há, até onde se sabe por fontes públicas, uso de biometria de voz neste canal para o Seguro-Desemprego.

**Consulta de status:** Após autenticação, a URA realiza uma consulta à BGSD (Base de Gestão do Seguro-Desemprego) mantida pela Dataprev. Essa consulta verifica: elegibilidade do cidadão, número de parcelas disponíveis, valor de cada parcela (calculado com base na tabela vigente — Decreto 12.797/2025 e Resolução CODEFAT 957/2022), status de bloqueios ou pendências.

**Processamento de bloqueios:** Bloqueios podem ter origem em diferentes sistemas: cruzamento com INSS (auxílio-reclusão, pensão por morte), irregularidades cadastrais identificadas pela Dataprev, ou pendências no próprio sistema MTE. A URA apenas informa o bloqueio, mas não tem acesso para desbloqueá-lo — essa ação exige intervenção humana ou atualização na BGSD.

**Transferência para humano:** Quando o cidadão solicita atendimento humano, a URA encaminha a ligação para a fila de atendentes do contact center. O BPO operador do contact center (historicamente CTIS Tecnologia, conforme PE 59/7066-2017 da Caixa; operador atual não confirmado em fonte primária) recebe a ligação e o atendente acessa um sistema de visualização do status do cidadão.

---

## Seção 4 — Evidências Físicas

| Etapa | Evidência Física |
|-------|-----------------|
| Pré-canal | TRCT, carta de habilitação do Seguro-Desemprego (emitida pelo empregador ou MTE), formulário SD/CD |
| Durante a ligação | Nenhuma (canal de voz) |
| Pós-consulta | Anotações manuais do cidadão; protocolo de atendimento (se fornecido pelo atendente humano) |
| Pós-crédito | SMS de confirmação de crédito (Caixa Tem), extrato bancário, comprovante de saque |

---

## Seção 5 — Normativos Aplicáveis

| Norma | Conteúdo Relevante | Impacto no Serviço |
|-------|-------------------|-------------------|
| Lei nº 7.998/1990 | Regula o Programa do Seguro-Desemprego e o FAT | Define elegibilidade, número de parcelas, valores |
| Decreto nº 11.034/2022 | Regulamenta o SAC (Serviço de Atendimento ao Consumidor) | Impõe obrigação de atendimento humano em até 2 minutos para serviços essenciais |
| Resolução CODEFAT nº 957/2022 | Metodologia de reajuste do benefício pelo INPC | Define fórmula de cálculo das parcelas |
| Decreto nº 12.797/2025 | Salário mínimo 2026 (R$ 1.621,00) | Define o piso do benefício |
| Regulamentação Anatel | Serviços 0800 devem ser gratuitos ao cidadão e disponíveis 24h | Garante gratuidade e disponibilidade do canal |

---

## Seção 6 — Fail Points e Gargalos

**Fail Point 1 — Falha de autenticação**  
O cidadão informa CPF e data de nascimento corretamente, mas a URA não reconhece os dados. Isso ocorre quando o cadastro na Dataprev está desatualizado ou quando há inconsistência entre o CPF cadastrado e o registrado pelo empregador no eSocial. Impacto: cidadão não consegue acessar as informações e é instruído a procurar um posto de atendimento presencial.

**Fail Point 2 — Benefício "pendente" sem explicação**  
O sistema informa que o benefício está "pendente" ou "em análise" sem detalhar o motivo. O cidadão não sabe se é um problema de documentação, cruzamento de dados (INSS, CAGED) ou simplesmente prazo de processamento. A URA não consegue fornecer essa informação — o atendente humano também frequentemente não tem acesso à causa raiz.

**Fail Point 3 — Fila de atendimento humano**  
Em períodos de pico (início do mês, após reajuste da tabela, após comunicados do MTE), o tempo de espera para atendimento humano excede 30 minutos. O Decreto 11.034/2022 exige atendimento em até 2 minutos para SAC, mas a Caixa pode argumentar que a URA do Seguro-Desemprego tem natureza informativa, não de SAC regulado.

**Fail Point 4 — Indisponibilidade da integração Dataprev**  
Quando a BGSD/Dataprev está indisponível (manutenções programadas ou falhas), a URA não consegue consultar o status do benefício e retorna mensagem genérica de erro. O cidadão não é informado sobre o prazo para normalização.

**Fail Point 5 — Cidadão sem conta Caixa ativa**  
Parte dos cidadãos elegíveis não tem conta na Caixa ou tem conta bloqueada. A URA informa o crédito disponível, mas o cidadão não consegue sacar. Esse problema não é resolvido pelo canal telefônico — exige comparecimento presencial.

---

## Seção 7 — Lacunas

- **Operador atual do BPO/contact center:** não confirmado em fonte primária (ver exercício 2.1, item 2.1 — permanece [EM ABERTO]).
- **Marca e versão da plataforma de URA:** não confirmada (Genesys, Avaya, Cisco, Mutant?).
- **TMA (Tempo Médio de Atendimento):** não há dados públicos sobre o tempo médio de resolução por tipo de demanda na URA.
- **Taxa de resolução no primeiro contato (FCR):** não há dados públicos sobre quantas ligações são resolvidas sem necessidade de atendimento humano ou segundo contato.
- **Cumprimento do Decreto 11.034/2022:** não há dados públicos sobre multas ou notificações à Caixa por descumprimento dos limites de tempo de espera.

---

## Fontes Consultadas

- Portal gov.br/trabalho-e-emprego: Seguro-Desemprego — orientações ao trabalhador.
- Site da Caixa Econômica Federal: informações sobre 0800 e canais de atendimento.
- Lei nº 7.998/1990 — Planalto.gov.br.
- Decreto nº 11.034/2022 — Planalto.gov.br.
- Decreto nº 12.797/2025 — Planalto.gov.br.
- Resolução CODEFAT nº 957/2022 — Portal FAT/MTE.
- Acórdão 135/2024-TCU-Plenário (contexto do ecossistema Dataprev/BGSD).
- Exercício 2.1 — Mapa de Atores (referência de contexto).
