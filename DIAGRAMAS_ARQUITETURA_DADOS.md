# DIAGRAMAS DE ARQUITETURA E MODELO DE DADOS
## Sistema Avançado de Treino, Performance e Evolução Física

---

## 1. MODELO ENTIDADE-RELACIONAMENTO (ER)

```mermaid
erDiagram
    USUARIO ||--o{ TREINO : cria
    USUARIO ||--o{ PERFIL_FISIOLOGICO : possui
    USUARIO ||--o{ WELLNESS_DIARY : registra
    USUARIO ||--o{ META : define
    USUARIO ||--o{ MACROCICLO : planeja
    
    TREINO ||--|{ EXERCICIO_TREINO : contem
    TREINO ||--o{ SESSAO : executa
    
    EXERCICIO_TREINO }o--|| EXERCICIO : referencia
    EXERCICIO_TREINO ||--o{ TECNICA_ESPECIAL : usa
    
    EXERCICIO }o--|| GRUPO_MUSCULAR : ativa
    EXERCICIO ||--o{ ATIVACAO_MUSCULAR : possui
    
    SESSAO ||--|{ SERIE : contem
    SESSAO ||--|| WELLNESS_DIARY : associa
    
    SERIE ||--o{ METRICA : gera
    
    MACROCICLO ||--|{ MESOCICLO : divide
    MESOCICLO ||--o{ SESSAO : inclui
    
    METRICA }o--|| TIPO_METRICA : classifica
    
    USUARIO {
        int id PK
        string nome
        date data_nascimento
        float peso_atual
        float altura
        int nivel_experiencia
        date created_at
    }
    
    PERFIL_FISIOLOGICO {
        int id PK
        int usuario_id FK
        int mev_peitoral
        int mav_peitoral
        int mrv_peitoral
        int tempo_recuperacao_peitoral
        float taxa_progressao_media
        date updated_at
    }
    
    TREINO {
        int id PK
        int usuario_id FK
        string nome
        string divisao
        string objetivo
        int dias_semana
        boolean ativo
        date created_at
    }
    
    EXERCICIO {
        int id PK
        string nome
        string tipo_equipamento
        string padrao_movimento
        string musculo_primario
        string musculo_secundario
    }
    
    EXERCICIO_TREINO {
        int id PK
        int treino_id FK
        int exercicio_id FK
        int ordem
        int series_planejadas
        string range_reps
        float carga_alvo
        int tempo_descanso
    }
    
    SESSAO {
        int id PK
        int treino_id FK
        int usuario_id FK
        datetime inicio
        datetime fim
        int duracao_minutos
        float session_rpe
        int score_prontidao
        date data
    }
    
    SERIE {
        int id PK
        int sessao_id FK
        int exercicio_treino_id FK
        int numero_serie
        float carga_kg
        int reps_realizadas
        float rpe
        int rir
        string tipo_falha
        int tempo_descanso_real
        datetime timestamp
    }
    
    METRICA {
        int id PK
        int serie_id FK
        int sessao_id FK
        int tipo_metrica_id FK
        float valor
        string unidade
        datetime calculado_em
    }
    
    WELLNESS_DIARY {
        int id PK
        int usuario_id FK
        date data
        float horas_sono
        int qualidade_sono
        int nivel_energia
        int nivel_estresse
        int doms_geral
        float hrv
    }
    
    META {
        int id PK
        int usuario_id FK
        int exercicio_id FK
        string tipo_meta
        float valor_alvo
        date prazo
        float progresso_atual
        boolean atingida
    }
    
    MACROCICLO {
        int id PK
        int usuario_id FK
        string nome
        string objetivo
        date inicio
        date fim
        int duracao_semanas
        boolean ativo
    }
    
    MESOCICLO {
        int id PK
        int macrociclo_id FK
        string tipo
        int semana_inicio
        int semana_fim
        int volume_alvo
        float intensidade_media
    }
```

---

## 2. DIAGRAMA DE CLASSES - CORE DOMAIN

```mermaid
classDiagram
    class Usuario {
        +int id
        +String nome
        +Date dataNascimento
        +PerfilFisiologico perfil
        +List~Treino~ treinos
        +List~Sessao~ historico
        +calcularIdade() int
        +obterNivelExperiencia() String
    }
    
    class PerfilFisiologico {
        +Map~String_VolumeParams~ volumePorMusculo
        +Map~String_int~ tempoRecuperacao
        +float taxaProgressaoMedia
        +calibrar(Sessao sessao)
        +obterMEV(String musculo) int
        +obterMAV(String musculo) int
        +obterMRV(String musculo) int
    }
    
    class Treino {
        +int id
        +String nome
        +String divisao
        +String objetivo
        +List~ExercicioTreino~ exercicios
        +clonar() Treino
        +adicionarExercicio(Exercicio ex)
        +calcularVolumePlanejado() Map
    }
    
    class ExercicioTreino {
        +Exercicio exercicio
        +int series
        +String rangeReps
        +float cargaAlvo
        +int tempoDescanso
        +List~TecnicaEspecial~ tecnicas
        +calcularVolumeEsperado() float
    }
    
    class Exercicio {
        +int id
        +String nome
        +String tipoEquipamento
        +String padraoMovimento
        +Map~String_float~ ativacaoMuscular
        +obterAtivacao(String musculo) float
        +ehBilateral() boolean
    }
    
    class Sessao {
        +int id
        +Treino treino
        +DateTime inicio
        +DateTime fim
        +List~Serie~ series
        +WellnessDiary wellness
        +float sessionRPE
        +calcularMetricas() Map
        +finalizar()
    }
    
    class Serie {
        +int numero
        +ExercicioTreino exercicio
        +float cargaKg
        +int repsRealizadas
        +float rpe
        +int rir
        +TipoFalha tipoFalha
        +DateTime timestamp
        +calcularVolume() float
        +estimar1RM() float
    }
    
    class MotorCalculos {
        +calcularVolume(Serie serie) float
        +estimar1RM(Serie serie) float
        +calcularIntensidade(Serie serie) float
        +calcularEstimulo(Sessao sessao) Map
        +calcularSFR(ExercicioTreino ex) float
        +calcularDensidade(Sessao sessao) float
    }
    
    class MotorInsights {
        +List~Sessao~ historico
        +detectarEstagnacao(Exercicio ex) boolean
        +detectarDesequilibrio() Map
        +detectarOverreaching() boolean
        +detectarAssimetria() Map
        +gerarInsights() List~Insight~
    }
    
    class MotorRecomendacoes {
        +Usuario usuario
        +gerarRecomendacaoProgressao(Exercicio ex) Recomendacao
        +gerarRecomendacaoDeload() Recomendacao
        +gerarRecomendacaoSubstituicao(Exercicio ex) List~Exercicio~
        +gerarRecomendacaoVolume(String musculo) Recomendacao
    }
    
    class MotorPredicao {
        +List~Sessao~ historico
        +projetar1RM(Exercicio ex, int semanas) ProjecaoFutura
        +projetarVolumeAcumulado(int semanas) Map
        +estimarTempoParaMeta(Meta meta) int
        +calcularDecaimento(int diasSemTreino) float
    }
    
    class MotorSimulacao {
        +Treino treino
        +simularAdicaoSerie(ExercicioTreino ex) ResultadoSimulacao
        +simularSubstituicao(Exercicio original, Exercicio novo) ResultadoSimulacao
        +simularMudancaDivisao(String novaDivisao) ResultadoSimulacao
        +simularBloco(Mesociclo meso) ResultadoSimulacao
    }
    
    Usuario "1" --> "1" PerfilFisiologico
    Usuario "1" --> "*" Treino
    Usuario "1" --> "*" Sessao
    Treino "1" --> "*" ExercicioTreino
    ExercicioTreino "*" --> "1" Exercicio
    Sessao "1" --> "*" Serie
    Serie "*" --> "1" ExercicioTreino
    
    MotorCalculos ..> Sessao : calcula
    MotorCalculos ..> Serie : calcula
    MotorInsights ..> Sessao : analisa
    MotorRecomendacoes ..> Usuario : gera para
    MotorPredicao ..> Sessao : projeta de
    MotorSimulacao ..> Treino : simula
```

---

## 3. ARQUITETURA DE CAMADAS

```mermaid
graph TB
    subgraph Apresentacao[Camada de Apresentação - iOS]
        UI1[Telas SwiftUI]
        UI2[View Models]
        UI3[Navegação]
    end
    
    subgraph Aplicacao[Camada de Aplicação]
        APP1[Use Cases]
        APP2[Serviços]
        APP3[Coordinators]
    end
    
    subgraph Dominio[Camada de Domínio]
        DOM1[Entidades]
        DOM2[Value Objects]
        DOM3[Regras de Negócio]
        DOM4[Motores de Cálculo]
        DOM5[Motores de Análise]
    end
    
    subgraph Infraestrutura[Camada de Infraestrutura]
        INF1[Repositórios]
        INF2[Core Data]
        INF3[HealthKit Integration]
        INF4[CloudKit Sync]
        INF5[Local Storage]
    end
    
    UI1 --> UI2
    UI2 --> APP1
    UI3 --> APP2
    
    APP1 --> DOM1
    APP1 --> DOM3
    APP2 --> DOM4
    APP2 --> DOM5
    
    DOM1 --> INF1
    DOM3 --> INF1
    INF1 --> INF2
    INF1 --> INF5
    
    APP3 --> INF3
    APP3 --> INF4
    
    style Apresentacao fill:#E3F2FD,stroke:#1976D2
    style Aplicacao fill:#E8F5E9,stroke:#388E3C
    style Dominio fill:#FFF3E0,stroke:#F57C00
    style Infraestrutura fill:#F3E5F5,stroke:#7B1FA2
```

---

## 4. FLUXO DE DADOS - EXECUÇÃO DE TREINO

```mermaid
sequenceDiagram
    actor U as Usuário
    participant V as View
    participant VM as ViewModel
    participant UC as UseCase
    participant M as Motor Cálculos
    participant R as Repositório
    participant DB as Core Data
    
    U->>V: Iniciar Treino
    V->>VM: startWorkout()
    VM->>UC: ExecutarTreino(treinoId)
    
    UC->>R: buscarTreino(id)
    R->>DB: fetch
    DB-->>R: Treino
    R-->>UC: Treino
    
    UC-->>VM: TreinoIniciado
    VM-->>V: Estado: EmExecucao
    
    loop Para cada série
        U->>V: Completar Série
        V->>VM: completeSerie(dados)
        VM->>UC: RegistrarSerie(dados)
        
        UC->>M: calcularMetricasSerie(serie)
        M-->>UC: Métricas
        
        UC->>R: salvarSerie(serie, metricas)
        R->>DB: save
        DB-->>R: OK
        R-->>UC: Salvo
        
        UC-->>VM: SerieRegistrada
        VM-->>V: Atualizar UI
    end
    
    U->>V: Finalizar Treino
    V->>VM: finalizarTreino()
    VM->>UC: FinalizarSessao()
    
    UC->>M: calcularMetricasSessao(sessao)
    M->>M: calcularVolume()
    M->>M: calcularIntensidade()
    M->>M: calcularEstimulo()
    M->>M: calcularSFR()
    M-->>UC: MetricasCompletas
    
    UC->>R: salvarSessao(sessao, metricas)
    R->>DB: save
    DB-->>R: OK
    
    UC-->>VM: SessaoFinalizada
    VM-->>V: Mostrar Resumo
    V-->>U: Resumo da Sessão
```

---

## 5. PIPELINE DE PROCESSAMENTO ASSÍNCRONO

```mermaid
graph LR
    A[Série Confirmada] --> B{Fila de Processamento}
    
    B --> C1[Worker 1:<br/>Cálculo de Métricas]
    B --> C2[Worker 2:<br/>Atualização 1RM]
    B --> C3[Worker 3:<br/>Agregação Volume]
    
    C1 --> D1[Volume, Intensidade,<br/>Densidade]
    C2 --> D2[1RM Estimado<br/>por Fórmulas]
    C3 --> D3[Volume Semanal<br/>por Músculo]
    
    D1 --> E[Merge Results]
    D2 --> E
    D3 --> E
    
    E --> F{Triggers Automáticos}
    
    F -->|Volume > MRV| G1[Alerta MRV]
    F -->|Estagnação 3+| G2[Insight Estagnação]
    F -->|ACWR > 1.5| G3[Recomendação Deload]
    F -->|PR Detectado| G4[Notificação PR]
    
    G1 --> H[Fila de Notificações]
    G2 --> H
    G3 --> H
    G4 --> H
    
    H --> I[Enviar Push]
    
    style B fill:#4CAF50,stroke:#2E7D32,color:#fff
    style E fill:#FF9800,stroke:#E65100,color:#fff
    style F fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 6. MODELO DE CACHE E SINCRONIZAÇÃO

```mermaid
graph TD
    A[App] --> B{Cache Local}
    
    B --> C1[Treinos Recentes<br/>30 dias]
    B --> C2[Métricas Agregadas<br/>12 semanas]
    B --> C3[1RM Estimados<br/>Todos exercícios]
    B --> C4[Perfil Fisiológico<br/>Atual]
    
    C1 --> D[Core Data]
    C2 --> D
    C3 --> D
    C4 --> D
    
    D --> E{Sync Engine}
    
    E -->|Mudanças| F[iCloud CloudKit]
    F -->|Merge| E
    
    E --> G{Conflict Resolution}
    G -->|Server Wins| H1[Atualizar Local]
    G -->|Client Wins| H2[Atualizar Server]
    G -->|Merge| H3[Combinar]
    
    H1 --> D
    H2 --> F
    H3 --> D
    H3 --> F
    
    E -->|Background| I[Sincronização<br/>Automática]
    E -->|Manual| J[Pull to Refresh]
    
    style B fill:#4CAF50,stroke:#2E7D32,color:#fff
    style E fill:#FF9800,stroke:#E65100,color:#fff
    style G fill:#F44336,stroke:#C62828,color:#fff
```

---

## 7. ARQUITETURA DE ANÁLISE E INSIGHTS

```mermaid
flowchart TB
    subgraph Input[Fontes de Dados]
        I1[Sessões Históricas]
        I2[Séries Registradas]
        I3[Wellness Diary]
        I4[HealthKit Data]
    end
    
    subgraph Agregacao[Agregação]
        A1[Agregador Temporal<br/>Dia/Semana/Mês]
        A2[Agregador por Músculo]
        A3[Agregador por Exercício]
    end
    
    subgraph Analise[Análise Estatística]
        AN1[Regressão Linear]
        AN2[Detecção de Anomalias]
        AN3[Correlação Pearson]
        AN4[Clustering K-Means]
    end
    
    subgraph Motores[Motores Especializados]
        M1[Motor Insights]
        M2[Motor Recomendações]
        M3[Motor Predição]
        M4[Motor Simulação]
    end
    
    subgraph Output[Saídas]
        O1[Insights Gerados]
        O2[Recomendações]
        O3[Projeções Futuras]
        O4[Simulações What-If]
        O5[Notificações]
    end
    
    I1 --> A1
    I2 --> A2
    I3 --> A1
    I4 --> A1
    
    A1 --> AN1
    A2 --> AN2
    A3 --> AN3
    A1 --> AN4
    
    AN1 --> M3
    AN2 --> M1
    AN3 --> M1
    AN4 --> M2
    
    M1 --> O1
    M2 --> O2
    M3 --> O3
    M4 --> O4
    
    O1 --> O5
    O2 --> O5
    O3 --> O5
    
    style Agregacao fill:#E3F2FD,stroke:#1976D2
    style Analise fill:#E8F5E9,stroke:#388E3C
    style Motores fill:#FFF3E0,stroke:#F57C00
    style Output fill:#F3E5F5,stroke:#7B1FA2
```

---

## 8. MODELO DE PERMISSÕES E INTEGRAÇÕES

```mermaid
graph TB
    A[App] --> B{Gerenciador de Permissões}
    
    B --> C1[HealthKit]
    B --> C2[Notificações]
    B --> C3[CloudKit]
    B --> C4[Apple Watch]
    
    C1 --> D1{Permissões Health}
    D1 --> E1[Ler Sono]
    D1 --> E2[Ler HRV]
    D1 --> E3[Ler FC Repouso]
    D1 --> E4[Ler Passos]
    D1 --> E5[Ler Peso]
    
    C2 --> D2{Permissões Push}
    D2 --> F1[Alertas]
    D2 --> F2[Badges]
    D2 --> F3[Sons]
    
    C3 --> D3{Permissões iCloud}
    D3 --> G1[CloudKit DB]
    D3 --> G2[Documents]
    
    C4 --> D4{Permissões Watch}
    D4 --> H1[WorkoutKit]
    D4 --> H2[HealthKit]
    D4 --> H3[Complications]
    
    E1 --> I[Estado de Autorização]
    E2 --> I
    E3 --> I
    E4 --> I
    E5 --> I
    F1 --> I
    F2 --> I
    F3 --> I
    G1 --> I
    G2 --> I
    H1 --> I
    H2 --> I
    H3 --> I
    
    I --> J{Status}
    J -->|Autorizado| K[Sync Ativo]
    J -->|Negado| L[Modo Offline]
    J -->|Não Determinado| M[Solicitar]
    
    style B fill:#4CAF50,stroke:#2E7D32,color:#fff
    style I fill:#FF9800,stroke:#E65100,color:#fff
    style K fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 9. DIAGRAMA DE DEPENDÊNCIAS - MÓDULOS

```mermaid
graph TB
    subgraph Core[Core Domain]
        C1[Entities]
        C2[Value Objects]
        C3[Business Rules]
    end
    
    subgraph Motors[Calculation Engines]
        M1[Motor Cálculos<br/>M03]
        M2[Motor Insights<br/>M05]
        M3[Motor Recomendações<br/>M06]
        M4[Motor Progressão<br/>M08]
        M5[Motor Predição<br/>M10]
        M6[Motor Simulação<br/>M11]
    end
    
    subgraph Features[Feature Modules]
        F1[Construtor Treino<br/>M01]
        F2[Execução Treino<br/>M02]
        F3[Analytics<br/>M04]
        F4[Periodização<br/>M13]
        F5[Recuperação<br/>M14]
        F6[Integrações<br/>M15]
    end
    
    subgraph Infrastructure[Infrastructure]
        I1[Repositories]
        I2[Core Data]
        I3[Network]
        I4[HealthKit]
    end
    
    C1 --> M1
    C2 --> M1
    C3 --> M1
    
    M1 --> M2
    M1 --> M3
    M1 --> M4
    M1 --> M5
    M1 --> M6
    
    M2 --> M3
    M4 --> M3
    
    F1 --> C1
    F2 --> C1
    F2 --> M1
    F3 --> M1
    F3 --> M2
    F4 --> C1
    F5 --> M1
    F6 --> I4
    
    M3 --> I1
    M5 --> I1
    F2 --> I1
    
    I1 --> I2
    I1 --> I3
    
    style Core fill:#E3F2FD,stroke:#1976D2
    style Motors fill:#FFF3E0,stroke:#F57C00
    style Features fill:#E8F5E9,stroke:#388E3C
    style Infrastructure fill:#F3E5F5,stroke:#7B1FA2
```

---

## 10. FLUXO DE BACKUP E RESTAURAÇÃO

```mermaid
sequenceDiagram
    participant U as Usuário
    participant A as App
    participant CD as Core Data
    participant iC as iCloud
    participant E as Exportador
    
    Note over U,E: Cenário 1: Backup Automático
    
    A->>CD: Detectar mudanças
    CD-->>A: Delta de dados
    A->>iC: Sync incremental
    iC-->>A: Confirmação
    A->>A: Atualizar último backup
    
    Note over U,E: Cenário 2: Backup Manual
    
    U->>A: Solicitar backup
    A->>CD: Exportar todos os dados
    CD-->>A: JSON completo
    A->>iC: Upload backup completo
    iC-->>A: URL do backup
    A-->>U: Backup realizado
    
    Note over U,E: Cenário 3: Restauração
    
    U->>A: Solicitar restauração
    A->>iC: Listar backups disponíveis
    iC-->>A: Lista com timestamps
    A-->>U: Escolher backup
    U->>A: Selecionar data
    A->>iC: Download backup
    iC-->>A: Dados
    A->>CD: Limpar dados locais
    CD-->>A: OK
    A->>CD: Importar backup
    CD-->>A: Importação completa
    A-->>U: Restauração completa
    
    Note over U,E: Cenário 4: Exportação CSV
    
    U->>A: Exportar para CSV
    A->>CD: Query dados
    CD-->>A: Resultados
    A->>E: Converter para CSV
    E-->>A: Arquivo CSV
    A-->>U: Compartilhar arquivo
```

---

## 11. ESTRUTURA DE PASTAS - CÓDIGO

```
TrainingApp/
├── App/
│   ├── TrainingApp.swift
│   ├── AppDelegate.swift
│   └── SceneDelegate.swift
│
├── Core/
│   ├── Domain/
│   │   ├── Entities/
│   │   │   ├── Usuario.swift
│   │   │   ├── Treino.swift
│   │   │   ├── Exercicio.swift
│   │   │   ├── Sessao.swift
│   │   │   └── Serie.swift
│   │   ├── ValueObjects/
│   │   │   ├── RPE.swift
│   │   │   ├── RIR.swift
│   │   │   └── VolumeParams.swift
│   │   └── BusinessRules/
│   │       ├── ProgressionRules.swift
│   │       └── ValidationRules.swift
│   │
│   ├── Application/
│   │   ├── UseCases/
│   │   │   ├── ExecutarTreino.swift
│   │   │   ├── RegistrarSerie.swift
│   │   │   └── GerarRecomendacao.swift
│   │   └── Services/
│   │       ├── CalculoService.swift
│   │       └── InsightService.swift
│   │
│   └── Infrastructure/
│       ├── Persistence/
│       │   ├── CoreData/
│       │   │   ├── TrainingModel.xcdatamodeld
│       │   │   └── CoreDataStack.swift
│       │   └── Repositories/
│       │       ├── TreinoRepository.swift
│       │       └── SessaoRepository.swift
│       └── External/
│           ├── HealthKit/
│           │   └── HealthKitManager.swift
│           └── CloudKit/
│               └── CloudSyncManager.swift
│
├── Features/
│   ├── M01_ConstrucaoTreino/
│   │   ├── Views/
│   │   ├── ViewModels/
│   │   └── Models/
│   ├── M02_ExecucaoTreino/
│   │   ├── Views/
│   │   ├── ViewModels/
│   │   └── Components/
│   ├── M03_Calculos/
│   │   ├── MotorCalculos.swift
│   │   └── Formulas/
│   ├── M04_Analytics/
│   │   ├── Views/
│   │   └── Charts/
│   ├── M05_Insights/
│   │   └── MotorInsights.swift
│   └── M06_Recomendacoes/
│       └── MotorRecomendacoes.swift
│
├── Shared/
│   ├── Extensions/
│   ├── Utilities/
│   └── Constants/
│
└── Resources/
    ├── Assets.xcassets
    ├── Localizable.strings
    └── Info.plist
```

---

## 12. DIAGRAMA DE DEPLOY - INFRAESTRUTURA

```mermaid
graph TB
    subgraph Cliente[Dispositivo do Usuário]
        C1[iPhone App]
        C2[Apple Watch App]
    end
    
    subgraph Apple[Serviços Apple]
        A1[iCloud CloudKit<br/>Database]
        A2[iCloud Drive<br/>Documents]
        A3[HealthKit<br/>Local]
        A4[APNs<br/>Push Notifications]
    end
    
    subgraph Backend[Backend Opcional]
        B1[API Gateway]
        B2[Lambda Functions]
        B3[Analytics DB]
    end
    
    C1 <-->|Sync| A1
    C1 <-->|Backup| A2
    C1 <-->|Read/Write| A3
    C1 <-->|Receive| A4
    
    C2 <-->|Mirror| C1
    C2 <-->|Read| A3
    
    C1 -.->|Telemetry| B1
    B1 -.-> B2
    B2 -.-> B3
    
    style Cliente fill:#E3F2FD,stroke:#1976D2
    style Apple fill:#E8F5E9,stroke:#388E3C
    style Backend fill:#FFF3E0,stroke:#F57C00,stroke-dasharray: 5 5
```

---

## 13. MODELO DE SEGURANÇA E PRIVACIDADE

```mermaid
graph TD
    A[Dados do Usuário] --> B{Classificação}
    
    B --> C1[Dados Sensíveis]
    B --> C2[Dados Normais]
    B --> C3[Dados Públicos]
    
    C1 --> D1[Saúde: Sono, HRV]
    C1 --> D2[Perfil: Peso, Idade]
    
    C2 --> E1[Treinos]
    C2 --> E2[Séries]
    C2 --> E3[Métricas]
    
    C3 --> F1[Templates Públicos]
    C3 --> F2[Exercícios Biblioteca]
    
    D1 --> G1[Armazenamento]
    D2 --> G1
    E1 --> G2[Armazenamento]
    E2 --> G2
    E3 --> G2
    F1 --> G3[Armazenamento]
    F2 --> G3
    
    G1[Keychain<br/>Criptografado]
    G2[Core Data<br/>Encriptado em Repouso]
    G3[Core Data<br/>Não Encriptado]
    
    G1 --> H1[iCloud<br/>End-to-End Encrypted]
    G2 --> H2[iCloud<br/>Encrypted in Transit]
    G3 --> H3[iCloud<br/>Standard Encryption]
    
    style C1 fill:#F44336,stroke:#C62828,color:#fff
    style C2 fill:#FF9800,stroke:#E65100,color:#fff
    style C3 fill:#4CAF50,stroke:#2E7D32,color:#fff
    style G1 fill:#D32F2F,stroke:#B71C1C,color:#fff
```

---

## 14. MÉTRICAS DE PERFORMANCE DO SISTEMA

```mermaid
graph LR
    A[Operação] --> B{Tipo}
    
    B --> C1[Leitura]
    B --> C2[Escrita]
    B --> C3[Cálculo]
    B --> C4[Sync]
    
    C1 --> D1[< 50ms<br/>Treino Ativo]
    C1 --> D2[< 100ms<br/>Histórico Recente]
    C1 --> D3[< 500ms<br/>Analytics]
    
    C2 --> E1[< 100ms<br/>Confirmar Série]
    C2 --> E2[< 200ms<br/>Salvar Sessão]
    
    C3 --> F1[< 50ms<br/>Métricas Série]
    C3 --> F2[< 200ms<br/>Métricas Sessão]
    C3 --> F3[< 1s<br/>Insights]
    
    C4 --> G1[Background<br/>Não Bloqueia UI]
    
    D1 --> H[SLA: 95%]
    D2 --> H
    D3 --> H
    E1 --> H
    E2 --> H
    F1 --> H
    F2 --> H
    F3 --> H
    G1 --> H
    
    style D1 fill:#4CAF50,stroke:#2E7D32,color:#fff
    style E1 fill:#4CAF50,stroke:#2E7D32,color:#fff
    style F1 fill:#4CAF50,stroke:#2E7D32,color:#fff
    style H fill:#2196F3,stroke:#1565C0,color:#fff
```

---

## 15. ESTRATÉGIA DE TESTES

```mermaid
graph TB
    subgraph Unit[Testes Unitários - 70%]
        U1[Entities]
        U2[Value Objects]
        U3[Business Rules]
        U4[Motores de Cálculo]
        U5[Fórmulas]
    end
    
    subgraph Integration[Testes de Integração - 20%]
        I1[Repositories + Core Data]
        I2[HealthKit Manager]
        I3[CloudKit Sync]
        I4[Use Cases]
    end
    
    subgraph UI_Tests[Testes de UI - 10%]
        UI1[Fluxo de Login]
        UI2[Criação de Treino]
        UI3[Execução de Treino]
        UI4[Visualização Analytics]
    end
    
    subgraph Performance[Testes de Performance]
        P1[Load Testing]
        P2[Stress Testing]
        P3[Latency Testing]
    end
    
    U1 --> CI[CI/CD Pipeline]
    U2 --> CI
    U3 --> CI
    U4 --> CI
    U5 --> CI
    I1 --> CI
    I2 --> CI
    I3 --> CI
    I4 --> CI
    UI1 --> CI
    UI2 --> CI
    UI3 --> CI
    UI4 --> CI
    P1 --> CI
    P2 --> CI
    P3 --> CI
    
    CI --> Deploy{Deploy?}
    Deploy -->|Pass| Prod[Production]
    Deploy -->|Fail| Block[Bloqueado]
    
    style Unit fill:#E8F5E9,stroke:#388E3C
    style Integration fill:#FFF3E0,stroke:#F57C00
    style UI_Tests fill:#E3F2FD,stroke:#1976D2
    style Performance fill:#F3E5F5,stroke:#7B1FA2
```

---

*Última atualização: 2026-05-19*
