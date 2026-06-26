# Diagrama AS-IS — Service Blueprint: URA da Caixa / Seguro-Desemprego

**Fonte:** C_blueprint_asis.md  
**Data:** junho de 2026

---

## Diagrama 1 — Fluxo da Jornada por Swim Lane (Mermaid)

O diagrama abaixo representa as interações entre atores ao longo das 10 etapas da jornada, com setas indicando fluxos de informação, ação e handoff entre swim lanes. As setas tracejadas (- ->) indicam fluxos de dados entre sistemas; as setas sólidas (-->) indicam ações ou transferências iniciadas pelo cidadão ou pela Caixa.

```mermaid
flowchart TD
    subgraph CIDADAO["LANE 1 — CIDADÃO"]
        C1["E1: Recebe TRCT\n(demissão)"]
        C2["E2: Faz requerimento\n(gov.br / SINE)"]
        C3["E3: Aguarda habilitação"]
        C4["E4: Lê carta de habilitação\n(e-mail / app)"]
        C5["E5: Identifica necessidade\n(parcela não creditada\nou benefício bloqueado)"]
        C6["E6: Disca 0800 726 0207\nnavega no menu"]
        C7["E7: Digita CPF +\ndata de nascimento"]
        C8["E8: Ouve resultado\nda URA"]
        C9a["E9a: Encerra ligação\n(consulta resolvida)"]
        C9b["E9b: Aguarda fila\n(atendente humano)"]
        C10a["E10: Aguarda crédito\nou age conforme\norientação"]
        C10b["E10b: Ciclo de\nretrabalho\n(liga de novo)"]
    end

    subgraph FRENTE["LANE 2 — URA / FRONTSTAGE CAIXA"]
        F6["URA: Menu principal\nem voz sintetizada"]
        F7["URA: Solicita CPF e\ndata de nascimento"]
        F8["URA: Apresenta status\n(parcelas, valor, data\nou 'benefício bloqueado')"]
        F9a["URA: Encerra\natendimento\n(sem protocolo ⚠️)"]
        F9b["URA: Transfere para\natendente humano\n(informa tempo de espera)"]
        F9c["Atendente: Orienta\nou registra ocorrência"]
        F10["Caixa Tem: SMS\nde crédito"]
    end

    subgraph BACK["LANE 3 — BACKSTAGE CAIXA"]
        B7["CIC: Valida CPF +\ndata de nascimento"]
        B8["URA consulta BGSD\nvia Integração B\n[protocolo EM ABERTO]"]
        B9["ACD: Roteia para\nfila do BPO\n[operador EM ABERTO]"]
        B10["Core bancário:\nExecuta crédito na\nconta do cidadão"]
    end

    subgraph MTE_DP["LANE 4 — MTE / DATAPREV (BGSD)"]
        M1["eSocial/CAGED:\nRegistro da rescisão\npelo empregador"]
        M2["BGSD: Recebe dados\ndo eSocial; abre\nprocesso de requerimento"]
        M3["Dataprev: Cruza dados\n(eSocial, INSS, RAIS)\ne decide elegibilidade"]
        M4["BGSD: Atualiza status\npara 'habilitado';\nagenda parcelas"]
        M8["BGSD: Responde consulta\ncom status do benefício\n(motivo de bloqueio\nnão é transmitido ⚠️)"]
        M10["BGSD: Atualiza parcela\ncomo 'paga'"]
    end

    subgraph SUPORTE["LANE 5 — SUPORTE / REGULAÇÃO"]
        S1["eSocial / CAGED"]
        S2["Portal gov.br / SINE"]
        S6["Plataforma URA\n+ PABX/VoIP\n[marca EM ABERTO]"]
        S_anatel["Anatel: regula 0800\n(gratuito, 24h)"]
        S_tcu["TCU/CGU:\nControle externo"]
    end

    %% Fase 1: Pré-URA
    C1 -->|"inicia requerimento"| C2
    C1 -.->|"dados de rescisão"| M1
    M1 -.->|"alimenta BGSD"| M2
    C2 -->|"aguarda"| C3
    C2 -.->|"requerimento"| M2
    M2 -.->|"cruza dados"| M3
    M3 -.->|"decisão de habilitação"| M4
    M4 -.->|"notificação"| C4
    C4 -->|"identifica necessidade"| C5

    %% Fase 2: URA
    C5 -->|"liga 0800"| C6
    C6 -->|"navega menu"| F6
    F6 -->|"solicita autenticação"| C7
    C7 -->|"digita CPF + data"| F7
    F7 -.->|"valida"| B7
    B7 -.->|"autenticado ✓"| F8
    B7 -.->|"falha ⚠️ FP1"| C9a

    F8 -.->|"consulta BGSD"| B8
    B8 -.->|"consulta"| M8
    M8 -.->|"retorna status\n(sem motivo de bloqueio ⚠️)"| B8
    B8 -.->|"resultado"| F8
    F8 -->|"ouve resultado"| C8

    C8 -->|"consulta resolvida"| C9a
    C8 -->|"pede atendente"| C9b
    C9a -->|"encerra\n(sem protocolo ⚠️ FP6)"| F9a
    C9b -->|"aguarda fila"| F9b
    F9b -.->|"roteamento"| B9
    B9 -->|"atendente atende"| F9c
    F9c -->|"orienta"| C10a
    F9c -->|"sem resolução ⚠️ FP5"| C10b
    C10b -->|"liga de novo"| C6

    C10a -.->|"crédito na conta"| B10
    B10 -.->|"atualiza BGSD"| M10
    B10 -.->|"notifica"| F10
    F10 -->|"SMS de crédito"| C10a

    %% Suporte
    S1 -.->|"infraestrutura"| M1
    S2 -.->|"canal de requerimento"| C2
    S6 -.->|"plataforma"| F6
    S_anatel -.->|"regula"| F6
    S_tcu -.->|"fiscaliza"| M3

    %% Estilo dos fail points
    classDef failpoint fill:#ff6b6b,color:#fff,stroke:#cc0000
    class B7,M8,F9a,F9c failpoint
```

---

## Diagrama 2 — Handoffs Críticos entre Atores (Mermaid — sequência)

Este diagrama de sequência detalha os handoffs mais críticos na Fase 2 (URA), mostrando explicitamente onde as responsabilidades passam de um ator para outro e onde ocorrem os fail points sistêmicos.

```mermaid
sequenceDiagram
    actor Cidadão
    participant URA as URA da Caixa<br/>(Frontstage)
    participant CIC as CIC — Cadastro<br/>Caixa (Backstage)
    participant BGSD as BGSD/Dataprev<br/>(MTE/Dataprev)
    participant BPO as Atendente Humano<br/>(BPO)

    Cidadão->>URA: Liga para 0800 726 0207
    URA->>Cidadão: Apresenta menu de opções

    Cidadão->>URA: Seleciona Seguro-Desemprego
    URA->>Cidadão: Solicita CPF + data de nascimento

    Cidadão->>URA: Informa CPF e data de nascimento
    URA->>CIC: Valida credenciais (CPF + data nasc.)

    alt Autenticação com sucesso
        CIC-->>URA: Credenciais válidas ✓
        URA->>BGSD: Consulta status do benefício (Integração B)
        
        alt BGSD disponível
            BGSD-->>URA: Retorna status (parcelas, valor, data)<br/>⚠️ Motivo de bloqueio NÃO é transmitido
            URA->>Cidadão: Informa status em voz sintetizada

            alt Benefício disponível
                Cidadão->>URA: Encerra ligação
                Note over URA,Cidadão: ⚠️ FP6: Sem protocolo de atendimento gerado
            else Benefício bloqueado
                URA->>Cidadão: "Benefício bloqueado. Deseja falar com atendente?"
                Cidadão->>URA: Solicita atendente humano
                URA->>BPO: Transfere para fila de espera (ACD)
                Note over URA,BPO: ⚠️ FP3: Espera pode exceder 30 min em picos
                BPO->>BGSD: Consulta status (mesma visão da URA)
                Note over BPO,BGSD: ⚠️ FP5: Atendente não acessa motivo do bloqueio<br/>(informação está no sistema do MTE, não na Caixa)
                BPO->>Cidadão: Orienta a procurar SINE/MTE<br/>ou registra ocorrência
            end
        else BGSD indisponível
            BGSD-->>URA: Timeout / erro de integração
            URA->>Cidadão: "Não foi possível processar.\nTente novamente mais tarde."
            Note over URA,Cidadão: ⚠️ FP4: Sem prazo de normalização informado
        end
    else Autenticação falha
        CIC-->>URA: Credenciais não reconhecidas
        URA->>Cidadão: "Dados não localizados.\nDirija-se a uma agência."
        Note over URA,Cidadão: ⚠️ FP1: CPF pode existir na BGSD<br/>mas não no CIC da Caixa
    end
```

---

## Diagrama 3 — Fronteira Institucional e Fluxo de Dados (relação estrutural)

Este diagrama mostra as relações estruturais entre os sistemas de dados, destacando a **fronteira institucional** que é a causa raiz dos fail points FP2b e FP5.

```mermaid
graph LR
    subgraph MTE_SIDE["Ecossistema MTE / Dataprev"]
        ESOCIAL["eSocial / CAGED\n(dados de rescisão)"]
        BGSD["BGSD\n(Base de Gestão\ndo Seguro-Desemprego)\n— Dataprev"]
        MTE_SIS["Sistema MTE\n(concessão e\nhabilitação)"]
        INSS["Base INSS\n(cruzamento)"]
        RAIS["RAIS\n(cruzamento)"]
    end

    subgraph CAIXA_SIDE["Ecossistema Caixa"]
        CIC["CIC\n(Cadastro de\nIdentificação do Cliente)"]
        URA["Plataforma URA\n(atendimento ao cidadão)"]
        CORE["Core bancário\n(crédito das parcelas)"]
        BPO["BPO / Contact Center\n(atendimento humano)"]
    end

    ESOCIAL -->|"dados de rescisão"| BGSD
    INSS -->|"cruzamento previdenciário"| BGSD
    RAIS -->|"histórico de vínculo"| BGSD
    MTE_SIS -->|"decisão de habilitação"| BGSD

    BGSD -->|"Integração A\n(concessão — documentada\nAcórdão TCU 135/2024)"| CORE
    BGSD -->|"Integração B\n(consulta — protocolo\n[EM ABERTO])"| URA
    BGSD -.->|"motivo de bloqueio\nNÃO é transmitido\npara URA ⚠️"| URA

    URA --> CIC
    URA --> BPO

    FRONTIER["◄─── FRONTEIRA INSTITUCIONAL ───►\nCaixa não acessa detalhe do bloqueio\nMTE/Dataprev não atende o cidadão diretamente\n⚠️ Cidadão fica sem resposta útil"]

    style FRONTIER fill:#ffe0e0,stroke:#cc0000,color:#cc0000
    style MTE_SIDE fill:#e8f4f8,stroke:#0066cc
    style CAIXA_SIDE fill:#e8f8e8,stroke:#006600
```

---

## Legenda dos Fail Points no Diagrama

| Símbolo | Fail Point | Etapa | Tipo |
|---------|-----------|-------|------|
| ⚠️ FP0b | Perda do prazo de 120 dias | E2 | Processo/regulatório |
| ⚠️ FP1 | Falha de autenticação (CIC ≠ BGSD) | E7 | Integração de dados |
| ⚠️ FP2b | Bloqueio sem causa explicada | E8 | Fronteira institucional |
| ⚠️ FP3 | Espera >30 min para atendente | E9 | Capacidade operacional |
| ⚠️ FP4 | BGSD indisponível → erro genérico | E8 | Disponibilidade de sistema |
| ⚠️ FP5 | Atendente sem acesso ao motivo do bloqueio | E9 | Fronteira institucional |
| ⚠️ FP6 | URA não emite protocolo de atendimento | E9 | Projeto de sistema |
| ⚠️ FP7 | Conta Caixa inativa → crédito inacessível | E10 | Acesso bancário |
| ⚠️ FP8 | Fragmentação dos canais de reclamação | E10 | Governança regulatória |