# 📐 Diagramas Técnicos da Arquitetura do Sistema

Este documento reúne a modelagem UML do projeto **Preditor de Falha e Risco em Dispositivos de Rede ($t + 12\text{ min}$)**, cobrindo desde a ingestão de telemetria bruta até ao serviço de inferência na API REST.

---

## 1. Diagrama de Casos de Uso

Mapeia as interações entre os atores (API do RIPE Atlas, Desenvolvedor/Engenheiro e Cliente Consumidor) e os casos de uso do sistema.

```plantuml
@startuml
left to right direction
skinparam packageStyle rectangle

actor "Engenheiro / Desenvolvedor" as Dev
actor "API RIPE Atlas" as RIPE
actor "Cliente / Aplicação Consumidora" as Client

rectangle "Sistema Preditivo de Redes (t + 12 min)" {
    usecase "UC01 - Ingerir Telemetria Bruta" as UC1
    usecase "UC02 - Tratar Dados e Eliminar Colunas Irrelevantes" as UC2
    usecase "UC03 - Extrair Vetor de 8 Atributos em t" as UC3
    usecase "UC04 - Rotular Alvo em t + 12 min" as UC4
    usecase "UC05 - Treinar e Validar Modelos (Árvore, RF, XGBoost)" as UC5
    usecase "UC06 - Selecionar Campeão e Serializar em .joblib" as UC6
    usecase "UC07 - Disponibilizar Endpoint na API REST" as UC7
    usecase "UC08 - Requisitar Predição Preditiva (POST /predict)" as UC8
}

RIPE --> UC1
Dev --> UC1
Dev --> UC2
Dev --> UC3
Dev --> UC4
Dev --> UC5
UC5 .> UC6 : <<include>>
Dev --> UC7
Client --> UC8
UC7 .> UC8 : <<serves>>
@enduml
```

![Diagrama de Casos de Uso](./diagramas/diagrama_casos_de_uso.png)

---

## 2. Diagrama de Atividades

Detalha o fluxo passo a passo do processamento de dados, fases de treino/validação dos modelos de Machine Learning e publicação da API.

```plantuml
@startuml
start

:Coletar telemetria bruta via API do RIPE Atlas;
:Salvar dados brutos em /metadados/medicoes/;

partition "Processamento e Tratamento" {
    :Carregar dados brutos;
    :Eliminar colunas e registros desnecessários;
    :Tratar valores ausentes e timeouts;
    :Calcular janelas temporais, RTT e Jitter relativo;
    :Extrair Vetor X (8 Atributos Estatísticos);
    :Mapear classe alvo y no horizonte t + 12 min;
    :Exportar dataset_preditivo_t12_v1.csv;
}

partition "Treinamento e Avaliação de ML" {
    :Particionar dataset cronologicamente\n(70% Treino / 15% Validação / 15% Teste Cego);
    
    fork
        :Treinar Árvore de Decisão (CART);
    fork again
        :Treinar Random Forest;
    fork again
        :Treinar XGBoost Classifier;
    end fork

    :Calcular Acurácia, F1-Macro e Precisão da classe FALHA;
    :Comparar métricas dos três algoritmos;
    :Selecionar XGBoost como Modelo Campeão;
}

partition "Empacotamento e Serviço (API REST)" {
    :Serializar modelo otimizado em formato .joblib;
    :Carregar .joblib no backend da API REST;
    :Expor endpoint POST /predict;
}

stop
@enduml
```

![Diagrama de Atividades](./diagramas/diagrama_atividades.png)

---

## 3. Diagrama de Sequência

Demonstra a ordem cronológica de eventos dividida em duas fases: o ciclo de Ingestão/Treino e o fluxo de consumo via API REST.

```plantuml
@startuml
autonumber
actor "Cliente / Consumidor" as Client
participant "API REST (Backend)" as API
participant "Modelo XGBoost (.joblib)" as Model
participant "Pipeline de Dados" as Pipeline
participant "API RIPE Atlas" as RIPE

== Fase 1: Ingestão, Tratamento e Treino ==
Pipeline -> RIPE: Requisita telemetria RTT / ICMP
RIPE --> Pipeline: Retorna JSON/CSV com dados brutos
Pipeline -> Pipeline: Elimina dados desnecessários e trata nulos
Pipeline -> Pipeline: Extrai vetor de 8 atributos e rotula alvo em t + 12 min
Pipeline -> Pipeline: Treina e avalia Árvore, Random Forest e XGBoost
Pipeline -> Model: Exporta o modelo XGBoost campeão (.joblib)

== Fase 2: Inferência Preditiva via API REST ==
Client -> API: POST /predict (JSON com vetor de 8 atributos em t)
API -> API: Valida esquema e tipos do JSON
API -> Model: Executa predicao(vetorX)
Model --> API: Retorna classe predita (OK / RISCO / FALHA) em t + 12 min
API --> Client: Resposta HTTP 200 OK (JSON com classe e confiança)
@enduml
```

![Diagrama de Sequência](./diagramas/diagrama_sequencia.png)
