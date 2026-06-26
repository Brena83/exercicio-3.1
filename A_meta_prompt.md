# Meta-Prompt: Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA da Caixa Econômica Federal

## Serviço, Canal e Órgão

**Serviço:** Atendimento ao Seguro-Desemprego — consulta de parcelas, situação do benefício, orientação sobre requerimento e desbloqueio de benefício.  
**Canal:** URA (Unidade de Resposta Audível) telefônica — número 0800 726 0207 (Caixa Econômica Federal), acesso 24 h.  
**Órgão titular da política:** Ministério do Trabalho e Emprego (MTE) — Secretaria do Trabalho (Strab).  
**Órgão operador do canal:** Caixa Econômica Federal (agente pagador e titular do canal 0800).  
**Órgão de processamento de dados:** Dataprev — responsável pela Base de Gestão do Seguro-Desemprego (BGSD).

> Este é o mesmo serviço mapeado no exercício 2.1 (Mapa de Atores). O objetivo agora é aprofundar a pesquisa para sustentar um **Service Blueprint AS-IS**: como o serviço **opera hoje**, na prática, na ótica do cidadão e dos processos de bastidor.

---

## Objetivo da Pesquisa

Produzir um conjunto de informações estruturadas que permitam montar um Service Blueprint AS-IS completo do atendimento ao Seguro-Desemprego via URA da Caixa, cobrindo:

1. **Jornada do cidadão** (etapas sequenciais, do evento-gatilho até a resolução ou encaminhamento), com tempos típicos e evidências físicas em cada etapa.  
2. **Processos de linha de frente (frontstage)** — o que o cidadão vê e ouve na interação com a URA e, eventualmente, com o atendente humano.  
3. **Processos de bastidor (backstage)** — o que acontece nos sistemas e entre os atores institucionais para que a URA entregue uma resposta ao cidadão: consulta à BGSD/Dataprev, autenticação, regras de elegibilidade, integração bancária.  
4. **Evidências físicas** — elementos tangíveis que o cidadão encontra em cada ponto de contato (SMS de confirmação, extrato Caixa Tem, carta de habilitação, protocolo de atendimento).  
5. **Normativos aplicáveis** — leis, decretos, portarias e resoluções que regem o serviço e o canal (Lei 7.998/1990, Decreto 11.034/2022-SAC, resoluções CODEFAT, normas Anatel sobre 0800).  
6. **Fail points e gargalos conhecidos** — pontos onde o serviço falha ou gera filas, reclamações e retrabalho (autenticação negada, sistema Dataprev indisponível, transferência para humano sem resolução, cidadão sem conta Caixa ativa).

---

## Perguntas de Pesquisa

### Jornada do cidadão (etapas)
1. Qual o **evento-gatilho** que leva o cidadão a ligar para a URA? (rescisão, prazo de carência, dúvida sobre parcela, bloqueio do benefício)
2. Quais são as **etapas sequenciais** de uma ligação típica à URA? (menu principal → autenticação → consulta → resultado → encerramento ou transferência)
3. Quanto tempo leva cada etapa? Qual o tempo médio de atendimento (TMA) na URA e no atendimento humano?
4. Quais **canais alternativos** o cidadão costuma tentar antes ou depois da URA? (gov.br, app Caixa Tem, SINE presencial, app Carteira de Trabalho Digital)

### Processos de bastidor
5. Como a URA **autentica** o cidadão? Quais dados são solicitados (CPF, data de nascimento, número do benefício)?
6. Como a URA **consulta** o status do benefício? Há integração em tempo real com a BGSD/Dataprev ou há cache? Qual a latência típica?
7. Quais **decisões automáticas** a URA toma? (confirmar parcela disponível, informar data de crédito, sinalizar bloqueio, redirecionar para MTE)
8. Quando e como ocorre a **transferência para atendente humano**? Quais filas existem? Há diferenciação por tipo de demanda?

### Normativos e controles
9. Quais normas do **SAC** (Decreto 11.034/2022) se aplicam à URA da Caixa para o Seguro-Desemprego? Há obrigação de tempo máximo de espera?
10. Como a **Anatel** regula o serviço 0800? Há requisitos de disponibilidade?
11. Quais controles do **TCU e da CGU** incidem sobre o canal de atendimento (além dos já identificados no 2.1 sobre concessão/pagamento)?

### Fail points e gargalos
12. Quais são os **principais motivos de insucesso** em uma ligação à URA? (dados inconsistentes, sistema fora do ar, menu inadequado para a demanda)
13. Qual o volume de ligações que **terminam sem resolução** e exigem retorno ou atendimento presencial?
14. Há **picos de demanda** conhecidos? (logo após publicação de tabela de valores, final do mês, início do ano)

---

## Formato do Resultado Esperado

O relatório deve ser organizado nas seguintes seções:

**Seção 1 — Jornada do Cidadão:** narrativa sequencial com etapas numeradas, tempo estimado e evidência física associada a cada etapa.  
**Seção 2 — Processos Frontstage:** descrição do que o cidadão ouve/vê em cada etapa (script da URA, mensagens de erro, opções de menu).  
**Seção 3 — Processos Backstage:** descrição dos sistemas, integrações e atores institucionais que sustentam cada etapa da jornada (mapeando quem faz o quê nos bastidores).  
**Seção 4 — Evidências Físicas:** listagem dos artefatos tangíveis por etapa.  
**Seção 5 — Normativos Aplicáveis:** tabela com norma, conteúdo relevante e impacto no serviço.  
**Seção 6 — Fail Points e Gargalos:** lista priorizada dos pontos críticos, com descrição do problema, frequência estimada e impacto no cidadão.  
**Seção 7 — Lacunas:** o que não foi possível confirmar com fonte primária e onde buscar.

---

## Fontes a Priorizar

- Portal gov.br (MTE): guias do Seguro-Desemprego, orientações de atendimento.
- Site da Caixa Econômica Federal: informações sobre o 0800 e canais de atendimento.
- Decreto nº 11.034/2022 (SAC — atendimento ao consumidor).
- Relatórios de ouvidoria da Caixa e do MTE.
- Acórdãos do TCU que mencionem o atendimento ao Seguro-Desemprego (além do Acórdão 135/2024 já mapeado).
- Reclamações no Consumidor.gov.br (padrões de falha identificados pelos próprios cidadãos).
- Documentos de licitação da Caixa (PNCP, licitacoes.caixa.gov.br) sobre o serviço de contact center / URA.
- Portarias e resoluções da Anatel sobre serviço 0800.

---

## Escopo e Limites

- **Foco:** canal URA telefônica (0800 726 0207). Outros canais (app, presencial, gov.br) são mencionados apenas como comparação ou ponto de transferência.
- **Benefício:** Seguro-Desemprego formal (trabalhador com carteira assinada demitido sem justa causa). Seguro-Defeso e modalidades especiais são mencionados apenas quando afetam a operação da URA.
- **Corte temporal:** situação vigente em 2025–2026.
- **Perspectiva:** dupla — cidadão (jornada) e sistema (processos). Não inclui análise de política pública ou propostas de melhoria (isso é o TO-BE).
