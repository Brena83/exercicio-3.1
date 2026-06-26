# Relatório de Auditoria v2 — Revisão Crítica do Relatório Assistente v2

**Auditado:** B_relatorio_assistente_v2.md  
**Papel do auditor:** Assistente independente com postura adversarial  
**Data:** junho de 2026

---

## Sumário Executivo

A v2 representa avanço significativo em relação à v1: corrigiu a omissão da jornada de requerimento/habilitação, expandiu o backstage, adicionou dados do Consumidor.gov.br e reconheceu a ambiguidade sobre o Decreto 11.034/2022. Entretanto, **três problemas novos ou persistentes** limitam a utilidade do documento como base para o Blueprint AS-IS definitivo. O mais crítico é a **ausência de uma estrutura matricial funcional** — a v2 promete na introdução a "estrutura matricial etapa × camada" mas entrega apenas seções separadas. Além disso, dois conteúdos técnicos são afirmados com mais segurança do que as fontes sustentam.

---

## Problema A — Estrutura matricial prometida não foi entregue

**Achado:** A introdução da v2 afirma "Introdução de estrutura matricial (etapa × camada) como rascunho para o blueprint — Problema 5". Contudo, o documento mantém a mesma estrutura por seções sequenciais (jornada, frontstage, backstage, evidências, normativos, fail points) — que são seções independentes, não uma matriz cruzada.

**Por que isso importa:** Um Service Blueprint AS-IS precisa, para ser utilizável, relacionar cada etapa da jornada do cidadão com os elementos correspondentes em cada camada (frontstage, backstage, evidências, fail points). Se a jornada tem 6 etapas e existem 4 fail points, o leitor precisa saber: *em qual etapa cada fail point ocorre?* Atualmente, o FP1 (falha de autenticação) claramente pertence à Etapa 2.3, o FP2 (bloqueio sem causa) pode ocorrer nas Etapas 2.4 e 2.5 — mas o documento não estabelece essa correspondência explícita.

**Exigência para a v3:** Apresentar, ao menos como tabela, a matriz cruzando etapas da jornada (colunas) com as camadas do blueprint (linhas: evidências físicas | ações do cliente | processos frontstage | linha de visibilidade | processos backstage | processos de suporte | fail points). Não precisa ser um diagrama visual — uma tabela Markdown já resolve.

---

## Problema B — Afirmação sobre webservices SOAP/REST sem fonte primária adequada

**Achado:** A Seção 3.2 afirma: "Pesquisa nos documentos de licitação da Caixa e no Acórdão 135/2024-TCU indica que a integração Caixa-Dataprev para o Seguro-Desemprego ocorre via **webservices** (SOAP/REST)". O Acórdão 135/2024-TCU, no entanto, trata da cadeia de concessão/processamento (MTE/Strab-Dataprev), não da integração de canal de atendimento (Caixa-Dataprev para a URA). A afirmação mistura dois ecossistemas que o próprio exercício 2.1 (Mapa de Atores) identificou como separados.

**O problema específico:** O Acórdão 135/2024 não menciona a URA da Caixa — menciona a BGSD/Dataprev no contexto de processamento de concessão de parcelas. Extrapolar que a arquitetura de integração de concessão é a mesma da integração de atendimento/consulta é uma inferência não documentada.

**Exigência para a v3:** Distinguir claramente: (a) integração MTE-Dataprev para concessão do benefício (documentada no Acórdão 135/2024); (b) integração Caixa-Dataprev para consulta de status pela URA (não documentada em fonte primária). O item (b) deve continuar marcado como [EM ABERTO] e a afirmação sobre SOAP/REST deve ser apresentada como hipótese técnica, não como fato confirmado.

---

## Problema C — Estimativa de 38 mil ligações apresentada como dado, não como estimativa

**Achado:** A Seção 6, Fail Point FP2, afirma: "Estimativa conservadora: se 10% dos ~380 mil casos geram ligação, são ~38 mil ligações adicionais." A conta é razoável, mas o documento apresenta esse número sem deixar claro que é uma estimativa de ordem de grandeza construída pelo próprio redator, baseada em hipótese (10% de conversão) sem base empírica.

**Problema:** Em um blueprint AS-IS usado para diagnóstico, dados de volumetria estimados sem embasamento podem ser citados por stakeholders como se fossem dados reais, gerando decisões equivocadas. A honestidade metodológica exige que estimativas sejam claramente rotuladas.

**Exigência para a v3:** Rotular explicitamente todas as estimativas como "estimativa do redator" com a hipótese assumida. Buscar dado real de volumetria de chamadas na ouvidoria da Caixa ou Consumidor.gov.br para substituir a estimativa onde possível.

---

## O Que a v2 Resolveu Bem (confirmar na v3)

1. **Jornada com requerimento/habilitação:** a inclusão das Etapas 1.1–1.4 (Fase 1) corrige o erro estrutural mais grave da v1. A distinção entre fase de requerimento (gov.br/SINE) e fase de acompanhamento (URA) está agora clara.

2. **Normativos — Decreto 11.034/2022:** a v2 reconhece corretamente que a aplicabilidade do Decreto ao canal do Seguro-Desemprego é questionável e marca o ponto como [EM ABERTO]. Isso é mais honesto e tecnicamente correto que a especulação da v1.

3. **Evidências físicas por etapa:** a tabela Seção 4 está bem estruturada e é diretamente utilizável para o blueprint.

4. **Fail Point FP6 (falta de protocolo ao final da URA):** esse é um fail point novo e importante que a v1 não tinha identificado. É um achado relevante para o blueprint.

5. **Tabela de lacunas:** a Seção 7 está bem organizada e atualizada. Manter e expandir.

---

## Síntese das Exigências para a v3

| Problema | Exigência |
|----------|-----------|
| A — Estrutura matricial | Entregar a matriz etapa × camada (tabela Markdown), cruzando todas as etapas com todas as camadas do blueprint |
| B — Afirmação sobre SOAP/REST | Separar integração MTE-Dataprev (concessão) da integração Caixa-Dataprev (URA/consulta); marcar a segunda como [EM ABERTO] e a afirmação SOAP/REST como hipótese |
| C — Estimativa de ligações | Rotular todas as estimativas explicitamente; buscar dado real de volumetria |

A v3 deve ser a versão final utilizável pelo exercício C (grill-me + blueprint). Se os três problemas acima forem resolvidos, o documento estará pronto para servir de base ao Blueprint AS-IS.
