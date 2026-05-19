# MAPEAMENTO DE FLUXOS DE ESTADOS

## Sistema Avançado de Treino, Performance e Evolução Física

---

## 1. ESTADOS GLOBAIS DO SISTEMA

### 1.1 Estado do Usuário

```
[NÃO_CADASTRADO] → [ONBOARDING] → [ATIVO] → [INATIVO]
                                      ↓
                                 [PAUSADO]
```

**Estados:**

- `NÃO_CADASTRADO`: Primeira vez no app
- `ONBOARDING`: Coletando dados fisiológicos iniciais
- `ATIVO`: Usando sistema regularmente
- `PAUSADO`: Deload programado ou pausa voluntária
- `INATIVO`: Sem treinos por 14+ dias

**Transições:**

- NÃO_CADASTRADO → ONBOARDING: Primeiro acesso
- ONBOARDING → ATIVO: Completar perfil fisiológico
- ATIVO → PAUSADO: Iniciar deload ou marcar pausa
- PAUSADO → ATIVO: Retomar treinos
- ATIVO → INATIVO: 14 dias sem registro de treino
- INATIVO → ATIVO: Registrar novo treino

---

## 2. FLUXO DE CRIAÇÃO DE TREINO (M01)

### 2.1 Estados da Criação

```
[NOVA_DIVISÃO] → [SELECIONANDO_EXERCÍCIOS] → [CONFIGURANDO_PARÂMETROS] → [TREINO_SALVO]
       ↓                                              ↓
[USANDO_TEMPLATE]                         [AJUSTANDO_TÉCNICAS]
```

**Estados:**

- `NOVA_DIVISÃO`: Definindo estrutura (PPL, Upper/Lower, etc)
- `USANDO_TEMPLATE`: Carregando programa pronto
- `SELECIONANDO_EXERCÍCIOS`: Escolhendo exercícios da biblioteca
- `CONFIGURANDO_PARÂMETROS`: Definindo séries, reps, carga
- `AJUSTANDO_TÉCNICAS`: Vinculando técnicas especiais (Drop Set, Rest-Pause)
- `TREINO_SALVO`: Treino completo e persistido

**Transições:**

- NOVA_DIVISÃO → SELECIONANDO_EXERCÍCIOS: Escolher divisão
- USANDO_TEMPLATE → AJUSTANDO_TÉCNICAS: Personalizar template
- SELECIONANDO_EXERCÍCIOS → CONFIGURANDO_PARÂMETROS: Adicionar exercício
- CONFIGURANDO_PARÂMETROS → AJUSTANDO_TÉCNICAS: Aplicar técnica
- AJUSTANDO_TÉCNICAS → TREINO_SALVO: Salvar treino

---

## 3. FLUXO DE EXECUÇÃO DE TREINO (M02)

### 3.1 Estados da Sessão

```
[TREINO_PLANEJADO] → [INICIADO] → [EM_EXECUÇÃO] → [FINALIZADO] → [PROCESSADO]
                         ↓              ↓              ↓
                    [PRÉ_TREINO]   [DESCANSANDO]  [RESUMO]
```

**Estados:**

- `TREINO_PLANEJADO`: Treino configurado, não iniciado
- `PRÉ_TREINO`: Registro de contexto (sono, energia, DOMS)
- `INICIADO`: Timer geral iniciado
- `EM_EXECUÇÃO`: Executando séries
- `DESCANSANDO`: Entre séries ou exercícios
- `FINALIZADO`: Última série completada
- `RESUMO`: Visualizando métricas da sessão
- `PROCESSADO`: Dados calculados e armazenados

**Transições:**

- TREINO_PLANEJADO → PRÉ_TREINO: Tocar "Iniciar Treino"
- PRÉ_TREINO → INICIADO: Confirmar contexto
- INICIADO → EM_EXECUÇÃO: Começar primeira série
- EM_EXECUÇÃO → DESCANSANDO: Completar série
- DESCANSANDO → EM_EXECUÇÃO: Timer acabar + iniciar próxima
- EM_EXECUÇÃO → FINALIZADO: Última série completada
- FINALIZADO → RESUMO: Auto-transição
- RESUMO → PROCESSADO: Calcular métricas

### 3.2 Estados da Série

```
[NÃO_EXECUTADA] → [EXECUTANDO] → [COMPLETA] → [CONFIRMADA]
                        ↓
                   [EDITANDO]
```

**Estados:**

- `NÃO_EXECUTADA`: Série planejada
- `EXECUTANDO`: Timer ativo, série em andamento
- `EDITANDO`: Ajustando parâmetros em tempo real
- `COMPLETA`: Série finalizada, aguardando confirmação
- `CONFIRMADA`: Dados salvos

**Dados capturados por transição:**

- NÃO_EXECUTADA → EXECUTANDO: Iniciar timer
- EXECUTANDO → COMPLETA: Registrar carga, reps, RPE, RIR, falha
- COMPLETA → CONFIRMADA: Salvar dados

---

## 4. FLUXO DE CÁLCULOS E MÉTRICAS (M03)

### 4.1 Pipeline de Processamento

```
[SÉRIE_CONFIRMADA] → [CALC_VOLUME] → [CALC_INTENSIDADE] → [CALC_ESTÍMULO] → [MÉTRICAS_ATUALIZADAS]
                          ↓                ↓                    ↓
                     [CALC_1RM]      [CALC_DENSIDADE]    [CALC_SFR]
```

**Estados:**

- `SÉRIE_CONFIRMADA`: Dados brutos disponíveis
- `CALC_VOLUME`: Calculando tonelagem (carga × reps × séries)
- `CALC_1RM`: Estimando 1RM por fórmulas múltiplas
- `CALC_INTENSIDADE`: Determinando % 1RM
- `CALC_DENSIDADE`: Tonelagem / tempo total
- `CALC_ESTÍMULO`: Estímulo por músculo (séries × ativação × intensidade)
- `CALC_SFR`: Relação estímulo/fadiga
- `MÉTRICAS_ATUALIZADAS`: Banco de dados atualizado

**Processamento em Pipeline:**

1. Cálculos por série (paralelo)
2. Agregação por exercício
3. Agregação por músculo
4. Agregação por sessão
5. Atualização de histórico semanal/mensal

---

## 5. FLUXO DE PROGRESSÃO (M08)

### 5.1 Estados do Modelo de Progressão

```
[SEM_PROGRESSÃO] → [PROGRESSÃO_LINEAR] → [PLATÔ_DETECTADO] → [PROGRESSÃO_ALTERNATIVA]
                          ↓                      ↓
                   [PROGREDINDO]          [ESTAGNADO]
```

**Estados:**

- `SEM_PROGRESSÃO`: Treino inicial, sem histórico
- `PROGRESSÃO_LINEAR`: +X kg por sessão funciona
- `PROGREDINDO`: Métricas crescentes
- `PLATÔ_DETECTADO`: 3+ sessões sem melhora
- `ESTAGNADO`: Estagnação confirmada
- `PROGRESSÃO_ALTERNATIVA`: Mudar para dupla progressão, volume, etc

**Transições:**

- SEM_PROGRESSÃO → PROGRESSÃO_LINEAR: Primeira progressão bem-sucedida
- PROGRESSÃO_LINEAR → PROGREDINDO: 3+ sessões com incremento
- PROGREDINDO → PLATÔ_DETECTADO: Falha de progressão
- PLATÔ_DETECTADO → ESTAGNADO: Confirmar com 3+ sessões
- ESTAGNADO → PROGRESSÃO_ALTERNATIVA: Sistema sugere mudança

### 5.2 Avaliação de Prontidão

```
[PRÉ_TREINO] → [CALC_PRONTIDÃO] → [VERDE] / [AMARELO] / [VERMELHO]
                                      ↓          ↓            ↓
                                [PROGREDIR] [MANTER]  [REDUZIR/CANCELAR]
```

**Estados de Prontidão:**

- `VERDE`: Score 75-100 → Progredir
- `AMARELO`: Score 50-74 → Manter carga
- `VERMELHO`: Score <50 → Reduzir ou cancelar

**Fatores:**

- Sono (horas + qualidade)
- HRV (se disponível)
- DOMS residual
- Dias desde último treino do músculo
- ACWR (carga aguda vs crônica)

---

## 6. FLUXO DE INSIGHTS (M05)

### 6.1 Detecção de Padrões

```
[COLETANDO_DADOS] → [ANALISANDO] → [PADRÃO_DETECTADO] → [INSIGHT_GERADO] → [NOTIFICADO]
                                          ↓
                                   [SEM_PADRÃO]
```

**Tipos de Padrões:**

- `ESTAGNAÇÃO`: 3+ sessões sem progressão
- `DESEQUILÍBRIO`: Ratio push/pull >1.3 ou <0.7
- `OVERREACHING`: ACWR >1.5 + RPE crescente
- `ASSIMETRIA`: Diferença >10% entre lados
- `SUB_VOLUME`: Músculo abaixo MEV por 2+ semanas

**Estados:**

- `COLETANDO_DADOS`: Acumulando histórico (mínimo 4-8 semanas)
- `ANALISANDO`: Rodando análise estatística
- `PADRÃO_DETECTADO`: Correlação ou anomalia encontrada
- `SEM_PADRÃO`: Dados insuficientes ou normais
- `INSIGHT_GERADO`: Mensagem estruturada criada
- `NOTIFICADO`: Usuário alertado

---

## 7. FLUXO DE RECOMENDAÇÕES (M06)

### 7.1 Geração de Recomendação

```
[TRIGGER] → [ANÁLISE_CONTEXTO] → [GERAÇÃO] → [VALIDAÇÃO] → [APRESENTADA] → [ACEITA/REJEITADA]
                                                                                  ↓
                                                                           [APLICADA]
```

**Triggers:**

- Estagnação detectada (M05)
- ACWR em zona de risco
- Desequilíbrio muscular
- Proximidade ao MRV
- Final de mesociclo

**Estados:**

- `TRIGGER`: Evento que dispara recomendação
- `ANÁLISE_CONTEXTO`: Coletar dados relevantes (histórico, perfil, objetivos)
- `GERAÇÃO`: Criar recomendação + justificativa
- `VALIDAÇÃO`: Verificar aplicabilidade (equipamento, restrições)
- `APRESENTADA`: Mostrar ao usuário
- `ACEITA`: Usuário aprova
- `REJEITADA`: Usuário recusa (feedbac registrado)
- `APLICADA`: Mudança implementada no programa

**Tipos de Recomendações:**

- Progressão de exercício (+carga, +reps, +série)
- Deload (tipo, duração)
- Substituição de exercício
- Ajuste de divisão
- Técnica de intensificação
- Periodização (próxima fase)

---

## 8. FLUXO DE PERIODIZAÇÃO (M13)

### 8.1 Estados do Ciclo

```
[SEM_CICLO] → [PLANEJANDO_MACRO] → [EM_MESOCICLO] → [DELOAD] → [PRÓXIMO_MESO]
                                           ↓
                                    [ACUMULAÇÃO] → [INTENSIFICAÇÃO] → [REALIZAÇÃO]
```

**Estados do Macrociclo:**

- `SEM_CICLO`: Treino ad-hoc sem periodização
- `PLANEJANDO_MACRO`: Definindo objetivo e divisão em mesociclos
- `EM_MESOCICLO`: Executando mesociclo ativo

**Estados do Mesociclo:**

- `ACUMULAÇÃO`: Semanas 1-4, volume crescente, intensidade moderada
- `INTENSIFICAÇÃO`: Semanas 5-7, volume decrescente, intensidade alta
- `REALIZAÇÃO`: Semanas 8-9, volume mínimo, intensidade máxima (peak)
- `DELOAD`: Semana de recuperação programada
- `PRÓXIMO_MESO`: Transição automática

**Transições:**

- SEM_CICLO → PLANEJANDO_MACRO: Criar programa estruturado
- PLANEJANDO_MACRO → EM_MESOCICLO: Iniciar primeiro meso
- ACUMULAÇÃO → INTENSIFICAÇÃO: Semana alvo ou fadiga acumulada
- INTENSIFICAÇÃO → REALIZAÇÃO: Preparação para pico
- REALIZAÇÃO → DELOAD: Fase completa
- DELOAD → PRÓXIMO_MESO: Recuperação completa

---

## 9. FLUXO DE RECUPERAÇÃO E FADIGA (M14)

### 9.1 Modelo SRA por Músculo

```
[REPOUSO] → [TREINO] → [STRESS] → [RECUPERANDO] → [SUPERCOMPENSAÇÃO] → [REPOUSO]
                          ↓             ↓                 ↓
                      [FADIGA]  [JANELA_ÓTIMA]    [DECAIMENTO]
```

**Estados:**

- `REPOUSO`: Músculo totalmente recuperado
- `TREINO`: Estímulo aplicado
- `STRESS`: Dano muscular + depleção energética
- `FADIGA`: Acúmulo de metabólitos, performance reduzida
- `RECUPERANDO`: Reparação em andamento
- `JANELA_ÓTIMA`: 24-72h pós-treino, melhor momento para próximo estímulo
- `SUPERCOMPENSAÇÃO`: Capacidade acima da baseline
- `DECAIMENTO`: Perda de adaptação por falta de estímulo

**Timers (individualizados por usuário):**

- Peitoral: 48-72h
- Costas: 48-72h
- Quadríceps: 72-96h
- Isquiotibiais: 48-72h
- Ombros: 48-60h
- Bíceps/Tríceps: 36-48h

### 9.2 Estado de Fadiga Sistêmica

```
[DESCANSADO] → [FADIGA_LEVE] → [FADIGA_MODERADA] → [FADIGA_ALTA] → [OVERREACHING]
                                                                           ↓
                                                                    [DELOAD_URGENTE]
```

**Estados:**

- `DESCANSADO`: ACWR 0.8-1.0, HRV normal
- `FADIGA_LEVE`: ACWR 1.0-1.2, performance mantida
- `FADIGA_MODERADA`: ACWR 1.2-1.4, leve queda de performance
- `FADIGA_ALTA`: ACWR 1.4-1.6, queda mensurável
- `OVERREACHING`: ACWR >1.6, múltiplos sinais (RPE↑, performance↓, HRV↓)
- `DELOAD_URGENTE`: Sistema recomenda pausa imediata

---

## 10. FLUXO DE SIMULAÇÃO (M11)

### 10.1 Modo What-If

```
[PROGRAMA_ATUAL] → [SIMULAR_MUDANÇA] → [CALCULAR_IMPACTO] → [COMPARAR] → [DECIDIR]
                                                                              ↓
                                                                    [APLICAR] / [DESCARTAR]
```

**Estados:**

- `PROGRAMA_ATUAL`: Estado baseline conhecido
- `SIMULAR_MUDANÇA`: Definir alteração (exercício, divisão, volume)
- `CALCULAR_IMPACTO`: Rodar modelo preditivo
- `COMPARAR`: Atual vs Projetado (estímulo, fadiga, tempo)
- `DECIDIR`: Usuário avalia
- `APLICAR`: Implementar mudança
- `DESCARTAR`: Manter atual

**Tipos de Simulação:**

- Adição de série: volume_novo = atual + 1
- Substituição de exercício: SFR_A vs SFR_B
- Mudança de divisão: frequência_por_músculo antes/depois
- Bloco completo: curva de fadiga projetada 4-8 semanas

---

## 11. FLUXO DE PREDIÇÃO (M10)

### 11.1 Projeção de 1RM

```
[HISTÓRICO_4_SEMANAS] → [REGRESSÃO_LINEAR] → [PROJEÇÃO_FUTURA] → [INTERVALO_CONFIANÇA]
                              ↓
                     [DETECÇÃO_TENDÊNCIA]
```

**Estados:**

- `HISTÓRICO_4_SEMANAS`: Dados mínimos para predição
- `REGRESSÃO_LINEAR`: Ajuste de curva aos dados
- `DETECÇÃO_TENDÊNCIA`: Crescente/Estável/Decrescente
- `PROJEÇÃO_FUTURA`: 4, 8, 12, 24 semanas
- `INTERVALO_CONFIANÇA`: ±X kg baseado em variabilidade histórica

**Outputs:**

- 1RM projetado em 4 semanas: 100kg ±5kg (95% confiança)
- Taxa de progressão: +1.2kg/semana (média 8 semanas)

---

## 12. FLUXO DE INTEGRAÇÕES (M15)

### 12.1 Sincronização com Apple Health

```
[DESCONECTADO] → [AUTORIZANDO] → [CONECTADO] → [SINCRONIZANDO] → [DADOS_ATUALIZADOS]
                                                      ↓
                                              [ERRO_SYNC] → [RETRY]
```

**Estados:**

- `DESCONECTADO`: Sem permissão
- `AUTORIZANDO`: Pedindo permissões iOS
- `CONECTADO`: Permissão concedida
- `SINCRONIZANDO`: Lendo HealthKit
- `DADOS_ATUALIZADOS`: Sono, HRV, passos importados
- `ERRO_SYNC`: Falha de leitura
- `RETRY`: Tentativa automática em 5min

**Frequência:**

- Manual: sob demanda
- Automática: diária às 6h

---

## 13. FLUXO DE NOTIFICAÇÕES

### 13.1 Estados de Alerta

```
[EVENTO] → [VERIFICAR_PRIORIDADE] → [ALTA/MÉDIA/BAIXA] → [NOTIFICAR] → [VISUALIZADA/IGNORADA]
                                            ↓
                                      [SILENCIADA]
```

**Tipos de Evento:**

- PR batido (ALTA)
- Deload recomendado (ALTA)
- Treino agendado (MÉDIA)
- Insight novo (MÉDIA)
- Mesociclo completado (BAIXA)

**Estados:**

- `EVENTO`: Trigger detectado
- `VERIFICAR_PRIORIDADE`: Classificar urgência
- `ALTA/MÉDIA/BAIXA`: Nível de prioridade
- `SILENCIADA`: Horário de silêncio ativo
- `NOTIFICAR`: Push enviado
- `VISUALIZADA`: Usuário abriu
- `IGNORADA`: Timeout sem ação

---

## 14. DIAGRAMA DE ESTADOS COMPLETO - VISÃO MACRO

```
                         ┌─────────────┐
                         │  USUÁRIO    │
                         │   ATIVO     │
                         └──────┬──────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
         ┌──────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
         │   PLANEJAR  │ │  EXECUTAR │ │  ANALISAR   │
         │   TREINO    │ │  TREINO   │ │  PROGRESSO  │
         └──────┬──────┘ └─────┬─────┘ └──────┬──────┘
                │               │               │
         ┌──────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
         │ Criar/Editar│ │ Registrar │ │ Visualizar  │
         │  Programa   │ │  Séries   │ │  Métricas   │
         └──────┬──────┘ └─────┬─────┘ └──────┬──────┘
                │               │               │
         ┌──────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
         │  Templates  │ │  Timer    │ │   Gráficos  │
         │  Divisões   │ │  RPE/RIR  │ │   Insights  │
         └──────┬──────┘ └─────┬─────┘ └──────┬──────┘
                │               │               │
                └───────────────┼───────────────┘
                                │
                         ┌──────▼──────┐
                         │   SISTEMA   │
                         │  APRENDE E  │
                         │  RECOMENDA  │
                         └─────────────┘
```

---

## 15. MATRIZ DE TRANSIÇÕES CRÍTICAS

| Estado Origem    | Evento                  | Estado Destino  | Validações                         |
| ---------------- | ----------------------- | --------------- | ---------------------------------- |
| NÃO_CADASTRADO   | Primeiro acesso         | ONBOARDING      | -                                  |
| ONBOARDING       | Perfil completo         | ATIVO           | Dados antropométricos obrigatórios |
| TREINO_PLANEJADO | Iniciar treino          | EM_EXECUÇÃO     | -                                  |
| EM_EXECUÇÃO      | Completar série         | DESCANSANDO     | Carga + reps + RPE obrigatórios    |
| DESCANSANDO      | Timer acabar            | EM_EXECUÇÃO     | Próxima série existe               |
| EM_EXECUÇÃO      | Última série            | FINALIZADO      | Todas séries completas             |
| FINALIZADO       | Auto                    | PROCESSADO      | Calcular métricas                  |
| PROGREDINDO      | 3+ sessões sem melhora  | PLATÔ_DETECTADO | Histórico suficiente               |
| PLATÔ_DETECTADO  | Confirmar               | ESTAGNADO       | 3+ sessões confirmadas             |
| ACUMULAÇÃO       | Fadiga alta ou semana N | DELOAD          | ACWR >1.4 ou semana programada     |
| DELOAD           | 5-7 dias                | INTENSIFICAÇÃO  | Fadiga normalizada                 |

---

## 16. DEPENDÊNCIAS ENTRE MÓDULOS

```
M01 (Construtor) ──────────────────┐
                                    │
                                    ▼
M02 (Execução) ────────────▶ M03 (Cálculos) ────────┐
       │                            │                 │
       │                            ▼                 │
       │                    M04 (Analytics)           │
       │                            │                 │
       │                            ▼                 │
       │                    M05 (Insights) ◀──────────┘
       │                            │
       │                            ▼
       └──────────────────▶ M06 (Recomendações)
                                    │
                                    ▼
                           M08 (Progressão)
                                    │
                                    ▼
                           M13 (Periodização)
                                    │
                                    ▼
                           M14 (Recuperação)

M11 (Simulação) ◀─── Leitura de todos os módulos
M10 (Predição)  ◀─── Histórico de M03, M08
M15 (Integrações) ──▶ M14 (Recuperação) [HRV, Sono]
```

---

## 17. PONTOS DE PERSISTÊNCIA DE DADOS

### 17.1 Quando os Dados São Salvos

| Evento              | Dados Persistidos                         | Módulo |
| ------------------- | ----------------------------------------- | ------ |
| Série confirmada    | carga, reps, RPE, RIR, tempo, falha       | M02    |
| Treino finalizado   | duração_total, Session RPE, contexto      | M02    |
| Cálculos completos  | Volume, Intensidade, 1RM estimado, SFR    | M03    |
| Insight gerado      | tipo, severidade, dados_base, timestamp   | M05    |
| Recomendação aceita | tipo, parâmetros, aplicada_em             | M06    |
| PR batido           | exercício, métrica, valor, data, contexto | M08    |
| Mesociclo iniciado  | objetivo, volume_alvo, duração            | M13    |
| Wellness diário     | sono, energia, DOMS, estresse             | M14    |

---

## 18. CONDIÇÕES DE ERRO E ESTADOS DE EXCEÇÃO

### 18.1 Falhas de Validação

```
[ENTRADA_INVÁLIDA] → [VALIDAR] → [ERRO] → [FEEDBACK] → [CORRIGIR]
                                     ↓
                              [LOG_ERRO]
```

**Exemplos:**

- Carga negativa: Bloquear + mensagem
- RPE fora de 1-10: Bloquear + mensagem
- Exercício sem músculo alvo: Aviso + sugerir correção
- Volume semanal > 50 séries/músculo: Alerta de MRV

### 18.2 Estados de Conflito

```
[MUDANÇA_PLANEJADA] → [CONFLITO_DETECTADO] → [RESOLVER] → [APLICAR]
                              ↓
                       [AVISAR_USUÁRIO]
```

**Exemplos:**

- Adicionar exercício que requer equipamento não disponível
- Marcar deload enquanto em mesociclo de intensificação
- Progredir exercício com estagnação detectada

---

## RESUMO EXECUTIVO

O sistema opera em **5 camadas de estados**:

1. **Camada de Usuário**: Onboarding → Ativo → Pausado/Inativo
2. **Camada de Treino**: Planejado → Executando → Finalizado → Processado
3. **Camada de Análise**: Coletando → Analisando → Insight/Recomendação
4. **Camada de Ciclo**: Mesociclo (Acumulação → Intensificação → Deload)
5. **Camada de Recuperação**: Stress → Recuperando → Supercompensação

**Transições críticas** são disparadas por:

- **Eventos temporais**: Timer de descanso, dias desde último treino
- **Limiares de métricas**: ACWR >1.5, 3+ sessões sem progressão
- **Ações do usuário**: Confirmar série, aceitar recomendação
- **Triggers automáticos**: Fim de mesociclo, detecção de padrão

**Persistência** ocorre em:

- Confirmação de série (imediata)
- Finalização de treino (batch)
- Cálculos completos (assíncrono)
- Eventos críticos (PR, insight, recomendação)

**Recuperação de falhas**:

- Auto-save a cada série confirmada
- Backup iCloud automático diário
- Log de auditoria para debugging
