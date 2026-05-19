# DIAGRAMAS VISUAIS - FLUXOS DE ESTADOS
## Sistema Avançado de Treino, Performance e Evolução Física

> **Nota**: Estes diagramas usam sintaxe Mermaid e podem ser visualizados em GitHub, GitLab, VS Code (com extensão), Obsidian, ou em [mermaid.live](https://mermaid.live)

---

## 1. VISÃO GERAL DO SISTEMA

```mermaid
graph TD
    A[Usuário] --> B{Estado do Usuário}
    B --> C[Não Cadastrado]
    B --> D[Onboarding]
    B --> E[Ativo]
    B --> F[Pausado]
    B --> G[Inativo]
    
    C -->|Primeiro acesso| D
    D -->|Completar perfil| E
    E -->|Deload/Pausa| F
    F -->|Retomar| E
    E -->|14 dias sem treino| G
    G -->|Novo treino| E
    
    E --> H[Planejar Treino]
    E --> I[Executar Treino]
    E --> J[Analisar Progresso]
    
    H --> K[M01: Construtor]
    I --> L[M02: Execução]
    J --> M[M04: Analytics]
    
    L --> N[M03: Cálculos]
    N --> O[M05: Insights]
    O --> P[M06: Recomendações]
    P --> Q[M08: Progressão]
    
    style E fill:#4CAF50,stroke:#2E7D32,color:#fff
    style L fill:#2196F3,stroke:#1565C0,color:#fff
    style O fill:#FF9800,stroke:#E65100,color:#fff
```

---

## 2. FLUXO DE CRIAÇÃO DE TREINO (M01)

```mermaid
stateDiagram-v2
    [*] --> SemTreino
    SemTreino --> NovaDivisao: Criar novo
    SemTreino --> UsandoTemplate: Usar template
    
    NovaDivisao --> SelecionandoExercicios: Escolher divisão
    UsandoTemplate --> SelecionandoExercicios: Carregar template
    
    SelecionandoExercicios --> ConfigurandoParametros: Adicionar exercício
    ConfigurandoParametros --> AjustandoTecnicas: Aplicar técnica especial
    ConfigurandoParametros --> TreinoSalvo: Salvar sem técnicas
    AjustandoTecnicas --> TreinoSalvo: Salvar
    
    TreinoSalvo --> [*]
    
    note right of AjustandoTecnicas
        Técnicas: Drop Set,
        Rest-Pause, Myo-Reps,
        Cluster Sets, etc.
    end note
```

---

## 3. FLUXO DE EXECUÇÃO DE TREINO (M02)

```mermaid
stateDiagram-v2
    [*] --> TreinoPlanejado
    TreinoPlanejado --> PreTreino: Iniciar treino
    
    PreTreino --> Iniciado: Confirmar contexto
    note right of PreTreino
        Registrar: sono, energia,
        DOMS, estresse
    end note
    
    Iniciado --> EmExecucao: Começar 1ª série
    EmExecucao --> Descansando: Completar série
    Descansando --> EmExecucao: Timer + próxima série
    
    EmExecucao --> Finalizado: Última série
    Finalizado --> Resumo: Auto-transição
    Resumo --> Processado: Calcular métricas
    
    Processado --> [*]
    
    state EmExecucao {
        [*] --> NaoExecutada
        NaoExecutada --> Executando: Iniciar timer
        Executando --> Editando: Ajustar parâmetros
        Editando --> Executando: Confirmar
        Executando --> Completa: Finalizar
        Completa --> Confirmada: Salvar dados
        Confirmada --> [*]
    }
```

---

## 4. FLUXO DETALHADO DA SÉRIE

```mermaid
graph TD
    A[Série Não Executada] --> B{Iniciar?}
    B -->|Sim| C[Executando]
    B -->|Não| A
    
    C --> D{Durante Execução}
    D --> E[Registrar Carga]
    D --> F[Contar Reps]
    D --> G[Timer Ativo]
    
    C --> H{Ajustar?}
    H -->|Sim| I[Modo Edição]
    I --> C
    H -->|Não| J[Completar Série]
    
    J --> K[Capturar Dados]
    K --> L[RPE: 1-10]
    K --> M[RIR: 0-5]
    K --> N[Tipo de Falha]
    K --> O[Tempo de Descanso]
    
    L --> P[Série Completa]
    M --> P
    N --> P
    O --> P
    
    P --> Q{Confirmar?}
    Q -->|Sim| R[Série Confirmada]
    Q -->|Não| I
    
    R --> S[Salvar no BD]
    S --> T[Iniciar Cronômetro<br/>de Descanso]
    
    style C fill:#2196F3,stroke:#1565C0,color:#fff
    style R fill:#4CAF50,stroke:#2E7D32,color:#fff
    style K fill:#FF9800,stroke:#E65100,color:#fff
```

---

## 5. PIPELINE DE CÁLCULOS (M03)

```mermaid
flowchart TD
    A[Série Confirmada] --> B{Pipeline de Cálculo}
    
    B --> C1[Calc Volume]
    B --> C2[Calc 1RM]
    B --> C3[Calc Intensidade]
    B --> C4[Calc Densidade]
    B --> C5[Calc Estímulo]
    B --> C6[Calc SFR]
    
    C1 --> D1[Tonelagem:<br/>carga × reps × séries]
    C2 --> D2[Fórmulas Múltiplas:<br/>Epley, Brzycki, etc]
    C3 --> D3[% 1RM por série]
    C4 --> D4[Tonelagem /<br/>tempo total]
    C5 --> D5[Séries × Ativação<br/>× Intensidade]
    C6 --> D6[Estímulo /<br/>Fadiga Gerada]
    
    D1 --> E[Agregação]
    D2 --> E
    D3 --> E
    D4 --> E
    D5 --> E
    D6 --> E
    
    E --> F1[Por Exercício]
    E --> F2[Por Músculo]
    E --> F3[Por Sessão]
    
    F1 --> G[Métricas Atualizadas]
    F2 --> G
    F3 --> G
    
    G --> H[Histórico Semanal/Mensal]
    
    style A fill:#4CAF50,stroke:#2E7D32,color:#fff
    style E fill:#FF9800,stroke:#E65100,color:#fff
    style G fill:#9C27B0,stroke:#6A1B9A,color:#fff
```

---

## 6. FLUXO DE PROGRESSÃO (M08)

```mermaid
stateDiagram-v2
    [*] --> SemProgressao
    
    SemProgressao --> ProgressaoLinear: 1ª progressão bem-sucedida
    
    ProgressaoLinear --> Progredindo: 3+ sessões com incremento
    
    Progredindo --> PlateauDetectado: Falha de progressão
    Progredindo --> Progredindo: Continuar progredindo
    
    PlateauDetectado --> Estagnado: Confirmar com 3+ sessões
    PlateauDetectado --> Progredindo: Retomar progressão
    
    Estagnado --> ProgressaoAlternativa: Sistema sugere mudança
    
    ProgressaoAlternativa --> Progredindo: Novo método funciona
    
    note right of ProgressaoLinear
        +X kg por sessão
    end note
    
    note right of ProgressaoAlternativa
        Dupla progressão,
        Volume, RPE-Target,
        Ondulada
    end note
```

---

## 7. AVALIAÇÃO DE PRONTIDÃO PARA TREINO

```mermaid
graph TD
    A[Pré-Treino] --> B[Calcular Prontidão]
    
    B --> C{Coletar Fatores}
    C --> D[Sono: horas + qualidade]
    C --> E[HRV: se disponível]
    C --> F[DOMS residual]
    C --> G[Dias desde último treino]
    C --> H[ACWR: agudo/crônico]
    
    D --> I{Score: 0-100}
    E --> I
    F --> I
    G --> I
    H --> I
    
    I -->|75-100| J[🟢 VERDE<br/>Progredir]
    I -->|50-74| K[🟡 AMARELO<br/>Manter Carga]
    I -->|0-49| L[🔴 VERMELHO<br/>Reduzir ou Cancelar]
    
    J --> M[Adicionar carga/reps/série]
    K --> N[Executar como planejado]
    L --> O[Reduzir volume/intensidade]
    L --> P[Cancelar e descansar]
    
    style J fill:#4CAF50,stroke:#2E7D32,color:#fff
    style K fill:#FFC107,stroke:#F57F17,color:#000
    style L fill:#F44336,stroke:#C62828,color:#fff
```

---

## 8. FLUXO DE INSIGHTS (M05)

```mermaid
flowchart TD
    A[Sistema Ativo] --> B[Coletando Dados]
    
    B --> C{Dados Suficientes?}
    C -->|Não: < 4 semanas| B
    C -->|Sim: ≥ 4-8 semanas| D[Analisando]
    
    D --> E{Detectar Padrões}
    
    E -->|Estagnação| F1[3+ sessões sem progressão]
    E -->|Desequilíbrio| F2[Ratio push/pull anormal]
    E -->|Overreaching| F3[ACWR > 1.5 + RPE↑]
    E -->|Assimetria| F4[Diferença > 10% entre lados]
    E -->|Sub-volume| F5[Abaixo MEV por 2+ semanas]
    E -->|Normal| F6[Sem Padrão]
    
    F1 --> G[Padrão Detectado]
    F2 --> G
    F3 --> G
    F4 --> G
    F5 --> G
    F6 --> H[Continuar Monitorando]
    
    G --> I[Gerar Insight]
    I --> J{Estruturar Mensagem}
    
    J --> K[Tipo de Insight]
    J --> L[Severidade: Baixa/Média/Alta]
    J --> M[Dados Base]
    J --> N[Recomendação Vinculada]
    
    K --> O[Insight Gerado]
    L --> O
    M --> O
    N --> O
    
    O --> P[Notificar Usuário]
    
    P --> Q{Usuário Visualiza?}
    Q -->|Sim| R[Visualizada]
    Q -->|Não: timeout| S[Ignorada]
    
    H --> B
    
    style G fill:#FF9800,stroke:#E65100,color:#fff
    style O fill:#F44336,stroke:#C62828,color:#fff
    style R fill:#4CAF50,stroke:#2E7D32,color:#fff
```

---

## 9. FLUXO DE RECOMENDAÇÕES (M06)

```mermaid
stateDiagram-v2
    [*] --> Monitorando
    
    Monitorando --> Trigger: Evento detectado
    
    state Trigger {
        [*] --> EstagnacaoDetectada
        [*] --> ACWRRisco
        [*] --> DesequilibrioMuscular
        [*] --> ProximidadeMRV
        [*] --> FinalMesociclo
    }
    
    Trigger --> AnaliseContexto
    
    AnaliseContexto --> Geracao: Dados coletados
    
    state Geracao {
        [*] --> CriarRecomendacao
        CriarRecomendacao --> AdicionarJustificativa
        AdicionarJustificativa --> CalcularConfianca
    }
    
    Geracao --> Validacao
    
    Validacao --> Apresentada: Válida
    Validacao --> Monitorando: Inválida
    
    Apresentada --> Aceita: Usuário aceita
    Apresentada --> Rejeitada: Usuário rejeita
    
    Aceita --> Aplicada: Implementar mudança
    Rejeitada --> Monitorando: Registrar feedback
    
    Aplicada --> [*]
    
    note right of Geracao
        Tipos: Progressão, Deload,
        Substituição, Divisão,
        Técnica, Periodização
    end note
```

---

## 10. PERIODIZAÇÃO - CICLO COMPLETO (M13)

```mermaid
graph TD
    A[Sem Ciclo] --> B{Criar Programa?}
    B -->|Sim| C[Planejando Macrociclo]
    B -->|Não| A
    
    C --> D[Definir Objetivo]
    D --> E{Objetivo}
    E -->|Hipertrofia| F1[Bulk: 12-16 semanas]
    E -->|Força| F2[Peak: 8-12 semanas]
    E -->|Recomposição| F3[Recomp: 16-24 semanas]
    E -->|Manutenção| F4[Maint: Indefinido]
    
    F1 --> G[Dividir em Mesociclos]
    F2 --> G
    F3 --> G
    F4 --> G
    
    G --> H[Mesociclo 1: Acumulação]
    H --> I{Semana 1-4}
    I --> J[Volume Crescente]
    I --> K[Intensidade Moderada]
    
    J --> L{Fadiga OK?}
    K --> L
    L -->|Sim| M[Continuar Acumulação]
    L -->|Não| N[Avançar para Deload]
    
    M --> O[Mesociclo 2: Intensificação]
    N --> O
    
    O --> P{Semana 5-7}
    P --> Q[Volume Decrescente]
    P --> R[Intensidade Crescente]
    
    Q --> S[Mesociclo 3: Realização]
    R --> S
    
    S --> T{Semana 8-9}
    T --> U[Volume Mínimo]
    T --> V[Intensidade Máxima]
    
    U --> W[Deload Programado]
    V --> W
    
    W --> X{Continuar?}
    X -->|Sim| H
    X -->|Não| Y[Fim do Macrociclo]
    
    Y --> Z[Análise de Resultados]
    
    style H fill:#4CAF50,stroke:#2E7D32,color:#fff
    style O fill:#FF9800,stroke:#E65100,color:#fff
    style S fill:#F44336,stroke:#C62828,color:#fff
    style W fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 11. MODELO SRA (Stress, Recovery, Adaptation)

```mermaid
graph TD
    A[Músculo em Repouso] --> B[Treino Executado]
    B --> C[Stress Gerado]
    
    C --> D{Quantificar Stress}
    D --> E[Volume × Intensidade]
    D --> F[Séries Efetivas]
    D --> G[Ativação Muscular]
    
    E --> H[Stress Score]
    F --> H
    G --> H
    
    H --> I[Fadiga Aguda]
    I --> J{Processo de Recuperação}
    
    J --> K[Horas 0-24:<br/>Reparação Inicial]
    J --> L[Horas 24-48:<br/>Síntese Proteica]
    J --> M[Horas 48-72:<br/>Supercompensação]
    J --> N[Horas 72-96:<br/>Janela Ótima]
    
    N --> O{Próximo Estímulo?}
    O -->|Sim: 24-72h| P[Novo Treino<br/>Timing Ideal]
    O -->|Sim: > 96h| Q[Novo Treino<br/>Decaimento Iniciado]
    O -->|Não: > 120h| R[Decaimento de Adaptação]
    
    P --> B
    Q --> B
    R --> S[Retorno ao Baseline]
    S --> A
    
    style C fill:#F44336,stroke:#C62828,color:#fff
    style M fill:#4CAF50,stroke:#2E7D32,color:#fff
    style N fill:#FF9800,stroke:#E65100,color:#fff
    style P fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 12. FADIGA SISTÊMICA E ACWR

```mermaid
flowchart TD
    A[Sessão de Treino] --> B[Calcular Carga da Sessão]
    
    B --> C[Session RPE × Duração min]
    C --> D[Foster's Load]
    
    D --> E{Agregar Cargas}
    E --> F[Última Semana:<br/>Carga Aguda]
    E --> G[Últimas 4 Semanas:<br/>Carga Crônica Média]
    
    F --> H[ACWR = Aguda / Crônica]
    G --> H
    
    H --> I{Classificar ACWR}
    
    I -->|< 0.8| J[🔵 Sub-treinamento]
    I -->|0.8 - 1.3| K[🟢 Zona Ótima]
    I -->|1.3 - 1.5| L[🟡 Zona de Atenção]
    I -->|> 1.5| M[🔴 Zona de Risco]
    
    J --> N[Recomendação:<br/>Aumentar carga]
    K --> O[Recomendação:<br/>Manter]
    L --> P[Recomendação:<br/>Monitorar sinais]
    M --> Q[Recomendação:<br/>Deload urgente]
    
    N --> R[Atualizar Histórico]
    O --> R
    P --> R
    Q --> R
    
    R --> S[Próxima Sessão]
    S --> A
    
    style K fill:#4CAF50,stroke:#2E7D32,color:#fff
    style L fill:#FFC107,stroke:#F57F17,color:#000
    style M fill:#F44336,stroke:#C62828,color:#fff
    style Q fill:#D32F2F,stroke:#B71C1C,color:#fff
```

---

## 13. FLUXO DE SIMULAÇÃO (M11)

```mermaid
stateDiagram-v2
    [*] --> ProgramaAtual
    
    ProgramaAtual --> SimularMudanca: Usuário propõe mudança
    
    state SimularMudanca {
        [*] --> TipoMudanca
        TipoMudanca --> AdicionarSerie
        TipoMudanca --> SubstituirExercicio
        TipoMudanca --> MudarDivisao
        TipoMudanca --> AjustarVolume
    }
    
    SimularMudanca --> CalcularImpacto
    
    state CalcularImpacto {
        [*] --> ModeloPreditivo
        ModeloPreditivo --> EstimuloProjetado
        ModeloPreditivo --> FadigaProjetada
        ModeloPreditivo --> TempoProjetado
        ModeloPreditivo --> RecuperacaoProjetada
    }
    
    CalcularImpacto --> Comparar
    
    state Comparar {
        [*] --> AtualVsProjetado
        AtualVsProjetado --> DeltaEstimulo
        AtualVsProjetado --> DeltaFadiga
        AtualVsProjetado --> DeltaTempo
        AtualVsProjetado --> ScoreComparativo
    }
    
    Comparar --> Decidir: Mostrar comparação
    
    Decidir --> Aplicar: Usuário aceita
    Decidir --> Descartar: Usuário rejeita
    
    Aplicar --> ProgramaAtualizado
    Descartar --> ProgramaAtual
    
    ProgramaAtualizado --> [*]
    
    note right of CalcularImpacto
        Simulação sem executar
        mudanças reais
    end note
```

---

## 14. FLUXO DE PREDIÇÃO (M10)

```mermaid
graph TD
    A[Histórico ≥ 4 Semanas] --> B{Dados Suficientes?}
    B -->|Não| C[Aguardar Mais Dados]
    B -->|Sim| D[Iniciar Predição]
    
    D --> E[Regressão Linear]
    E --> F[Ajustar Curva aos Dados]
    
    F --> G{Detecção de Tendência}
    G -->|Crescente| H1[Taxa: +X kg/semana]
    G -->|Estável| H2[Taxa: ~0 kg/semana]
    G -->|Decrescente| H3[Taxa: -X kg/semana]
    
    H1 --> I[Projetar Futuro]
    H2 --> I
    H3 --> I
    
    I --> J{Horizonte de Projeção}
    J --> K1[4 semanas]
    J --> K2[8 semanas]
    J --> K3[12 semanas]
    J --> K4[24 semanas]
    
    K1 --> L[Calcular Intervalo de Confiança]
    K2 --> L
    K3 --> L
    K4 --> L
    
    L --> M[Variabilidade Histórica]
    M --> N[1RM Projetado ± Δ]
    
    N --> O{Apresentar Cenários}
    O --> P1[Otimista: +20%]
    O --> P2[Realista: Tendência]
    O --> P3[Conservador: -20%]
    
    P1 --> Q[Visualização Gráfica]
    P2 --> Q
    P3 --> Q
    
    Q --> R[Projeção Completa]
    
    C --> S[Aguardar]
    S --> A
    
    style H1 fill:#4CAF50,stroke:#2E7D32,color:#fff
    style H3 fill:#F44336,stroke:#C62828,color:#fff
    style R fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 15. INTEGRAÇÃO COM APPLE HEALTH

```mermaid
sequenceDiagram
    participant U as Usuário
    participant A as App
    participant H as HealthKit
    participant S as Sistema
    
    U->>A: Abrir App
    A->>A: Verificar Conexão Health
    
    alt Não Conectado
        A->>U: Solicitar Permissões
        U->>A: Conceder Permissões
        A->>H: Autorizar Acesso
        H-->>A: Autorização OK
    end
    
    A->>H: Solicitar Dados
    
    par Leitura Paralela
        H-->>A: Horas de Sono
        H-->>A: Qualidade do Sono
        H-->>A: HRV Diário
        H-->>A: Frequência Cardíaca Repouso
        H-->>A: Passos Diários
        H-->>A: Peso Corporal
    end
    
    A->>S: Processar Dados Importados
    
    S->>S: Calcular Score de Recuperação
    S->>S: Atualizar Modelo de Prontidão
    S->>S: Ajustar Recomendações
    
    S-->>A: Dados Processados
    A-->>U: Atualizar Dashboard
    
    Note over A,S: Sincronização<br/>Automática Diária<br/>às 6h
```

---

## 16. FLUXO COMPLETO - DA CRIAÇÃO À ANÁLISE

```mermaid
flowchart TB
    subgraph Planejamento
        A1[Criar Divisão] --> A2[Selecionar Exercícios]
        A2 --> A3[Configurar Parâmetros]
        A3 --> A4[Treino Salvo]
    end
    
    subgraph Execucao[Execução]
        B1[Iniciar Treino] --> B2[Registrar Contexto]
        B2 --> B3[Executar Séries]
        B3 --> B4[Log Completo]
        B4 --> B5[Finalizar Treino]
    end
    
    subgraph Processamento
        C1[Calcular Volume] --> C2[Calcular Intensidade]
        C2 --> C3[Calcular Estímulo]
        C3 --> C4[Calcular SFR]
        C4 --> C5[Atualizar 1RM]
    end
    
    subgraph Analise[Análise]
        D1[Gráficos de Evolução] --> D2[Detectar Padrões]
        D2 --> D3[Gerar Insights]
        D3 --> D4[Criar Recomendações]
    end
    
    subgraph Progressao[Progressão]
        E1[Avaliar Prontidão] --> E2[Ajustar Programa]
        E2 --> E3[Simular Mudanças]
        E3 --> E4[Aplicar Otimizações]
    end
    
    A4 --> B1
    B5 --> C1
    C5 --> D1
    D4 --> E1
    E4 --> A1
    
    style Planejamento fill:#E3F2FD,stroke:#1976D2
    style Execucao fill:#E8F5E9,stroke:#388E3C
    style Processamento fill:#FFF3E0,stroke:#F57C00
    style Analise fill:#F3E5F5,stroke:#7B1FA2
    style Progressao fill:#FCE4EC,stroke:#C2185B
```

---

## 17. MATRIZ DE ESTADOS - VISÃO CONSOLIDADA

```mermaid
graph TB
    subgraph Usuario[Estados do Usuário]
        U1[Não Cadastrado]
        U2[Onboarding]
        U3[Ativo]
        U4[Pausado]
        U5[Inativo]
    end
    
    subgraph Treino[Estados do Treino]
        T1[Planejado]
        T2[Em Execução]
        T3[Finalizado]
        T4[Processado]
    end
    
    subgraph Serie[Estados da Série]
        S1[Não Executada]
        S2[Executando]
        S3[Completa]
        S4[Confirmada]
    end
    
    subgraph Progressao[Estados de Progressão]
        P1[Sem Progressão]
        P2[Progredindo]
        P3[Platô]
        P4[Estagnado]
    end
    
    subgraph Ciclo[Estados do Ciclo]
        C1[Sem Ciclo]
        C2[Acumulação]
        C3[Intensificação]
        C4[Realização]
        C5[Deload]
    end
    
    subgraph Recuperacao[Estados de Recuperação]
        R1[Descansado]
        R2[Fadiga Leve]
        R3[Fadiga Moderada]
        R4[Fadiga Alta]
        R5[Overreaching]
    end
    
    U3 --> T1
    T1 --> T2
    T2 --> S2
    S2 --> S4
    T2 --> T3
    T3 --> T4
    
    T4 --> P2
    P2 --> P3
    
    U3 --> C2
    C2 --> C3
    C3 --> C4
    C4 --> C5
    
    T4 --> R2
    R2 --> R3
    R3 --> R4
    R4 --> R5
    R5 --> C5
    
    C5 --> R1
    R1 --> C2
```

---

## 18. TIMELINE - LINHA DO TEMPO DE UMA SEMANA

```mermaid
gantt
    title Ciclo Semanal de Treino com SRA
    dateFormat  YYYY-MM-DD
    section Segunda
    Treino Push     :active, t1, 2024-01-01, 1h
    Stress Peitoral :crit, s1, 2024-01-01, 6h
    section Terça
    Recuperação     :r1, 2024-01-02, 1d
    section Quarta
    Treino Pull     :active, t2, 2024-01-03, 1h
    Stress Costas   :crit, s2, 2024-01-03, 6h
    section Quinta
    Recuperação     :r2, 2024-01-04, 1d
    section Sexta
    Treino Legs     :active, t3, 2024-01-05, 1h
    Stress Pernas   :crit, s3, 2024-01-05, 6h
    section Sábado
    Recuperação     :r3, 2024-01-06, 1d
    section Domingo
    Descanso Total  :done, d1, 2024-01-07, 1d
    section Análise
    Calcular ACWR   :milestone, m1, 2024-01-07, 0d
    Gerar Insights  :milestone, m2, 2024-01-07, 0d
```

---

## 19. ARQUITETURA DE DECISÃO - PRONTIDÃO

```mermaid
graph TD
    A[Manhã do Treino] --> B{Coletar Dados}
    
    B --> C1[Sono: 7.5h]
    B --> C2[Qualidade: 4/5]
    B --> C3[HRV: 65ms]
    B --> C4[DOMS: 2/5]
    B --> C5[Dias Descanso: 2]
    B --> C6[ACWR: 1.1]
    
    C1 --> D{Calcular Pesos}
    C2 --> D
    C3 --> D
    C4 --> D
    C5 --> D
    C6 --> D
    
    D --> E[Sono: 25%]
    D --> F[HRV: 20%]
    D --> G[DOMS: 15%]
    D --> H[Descanso: 20%]
    D --> I[ACWR: 20%]
    
    E --> J[Score Ponderado]
    F --> J
    G --> J
    H --> J
    I --> J
    
    J --> K{Score Total}
    
    K -->|85/100| L[🟢 VERDE]
    L --> M[Decisão: PROGREDIR]
    M --> N[+2.5kg no Supino]
    M --> O[+1 rep no Agachamento]
    
    N --> P[Executar Treino]
    O --> P
    
    style L fill:#4CAF50,stroke:#2E7D32,color:#fff
    style M fill:#2196F3,stroke:#1565C0,color:#fff
    style P fill:#FF9800,stroke:#E65100,color:#fff
```

---

## 20. FLUXO DE NOTIFICAÇÕES

```mermaid
stateDiagram-v2
    [*] --> SistemaAtivo
    
    SistemaAtivo --> EventoDetectado: Trigger
    
    state EventoDetectado {
        [*] --> PRBatido
        [*] --> DeloadRecomendado
        [*] --> TreinoAgendado
        [*] --> InsightNovo
        [*] --> MesocicloCompleto
    }
    
    EventoDetectado --> VerificarPrioridade
    
    VerificarPrioridade --> Alta: PR / Deload
    VerificarPrioridade --> Media: Treino / Insight
    VerificarPrioridade --> Baixa: Mesociclo
    
    Alta --> VerificarHorario
    Media --> VerificarHorario
    Baixa --> VerificarHorario
    
    VerificarHorario --> Silenciado: 22h-7h
    VerificarHorario --> Notificar: 7h-22h
    
    Silenciado --> Enfileirada: Aguardar
    Enfileirada --> Notificar: 7h
    
    Notificar --> Enviada
    
    Enviada --> Visualizada: Usuário abre
    Enviada --> Ignorada: Timeout 24h
    
    Visualizada --> [*]
    Ignorada --> [*]
    
    note right of Notificar
        Push Notification
        + Badge no Ícone
        + In-App Alert
    end note
```

---

## LEGENDA DE CORES

- 🟢 **Verde** - Estados positivos, progressão, sucesso
- 🟡 **Amarelo** - Estados de atenção, manutenção
- 🔴 **Vermelho** - Estados críticos, fadiga, risco
- 🔵 **Azul** - Estados de processamento, análise
- 🟣 **Roxo** - Estados de insights, recomendações
- 🟠 **Laranja** - Estados de transição, intermediários

---

## COMO USAR ESTES DIAGRAMAS

1. **Visualização Online**: Copie o código Mermaid e cole em [mermaid.live](https://mermaid.live)
2. **GitHub/GitLab**: Os diagramas renderizam automaticamente em arquivos `.md`
3. **VS Code**: Instale a extensão "Markdown Preview Mermaid Support"
4. **Obsidian**: Suporte nativo a Mermaid
5. **Export**: Use mermaid.live para exportar como PNG/SVG

---

## ÍNDICE RÁPIDO DE DIAGRAMAS

1. Visão Geral do Sistema
2. Criação de Treino (M01)
3. Execução de Treino (M02)
4. Fluxo Detalhado da Série
5. Pipeline de Cálculos (M03)
6. Progressão (M08)
7. Avaliação de Prontidão
8. Insights (M05)
9. Recomendações (M06)
10. Periodização Completa (M13)
11. Modelo SRA
12. Fadiga Sistêmica e ACWR
13. Simulação (M11)
14. Predição (M10)
15. Integração Apple Health
16. Fluxo Completo
17. Matriz de Estados
18. Timeline Semanal
19. Arquitetura de Decisão
20. Notificações

---

*Última atualização: 2026-05-19*
