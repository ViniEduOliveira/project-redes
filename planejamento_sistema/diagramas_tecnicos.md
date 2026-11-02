# 📐 Diagramas Técnicos da Arquitetura do Sistema

Este documento reúne a modelagem UML do projeto **Preditor de Falha e Risco em Dispositivos de Rede ($t + 12\text{ min}$)**, cobrindo desde a ingestão de telemetria bruta até ao serviço de inferência na API REST.

---

## 1. Diagrama de Casos de Uso

Mapeia as interações entre os atores (API do RIPE Atlas, Desenvolvedor/Engenheiro e Cliente Consumidor) e os casos de uso do sistema.

```plantuml
@startuml
left to right direction
skinparam packageStyle rectangle

actor "Engenheiro /\nDesenvolvedor" as Dev
actor "API RIPE\nAtlas" as RIPE
actor "Cliente /\nAplicação\nConsumidora" as Client

rectangle "Sistema\nPreditivo de Redes (t +\n12 min)" {
   usecase "UC01 - Ingerir Telemetria\nBruta" as UC1
   usecase "UC02 - Tratar Dados e\nEliminar Colunas Irrelevantes" as UC2
   usecase "UC03 - Extrair Vetor de 8\nAtributos em t" as UC3
   usecase "UC04 - Rotular Alvo em t + 12\nmin" as UC4
   usecase "UC05 - Treinar e Validar\nModelos (Árvore, RF,\nXGBoost)" as UC5
   usecase "UC06 - Selecionar Campeão e\nSerializar em .joblib" as UC6
   usecase "UC07 - Disponibilizar\nEndpoint na API REST" as UC7
   usecase "UC08 - Requisitar Predição\nPreditiva (POST\n/predict)" as UC8
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
@endluml
```

![Diagrama de Casos de Uso](./img_diagramas/diagrama_caso_de_uso.png)

---

## 2. Diagrama de Atividades

Detalha o fluxo passo a passo do processamento de dados, fases de treino/validação dos modelos de Machine Learning e publicação da API.

```plantuml
@startuml
start

:Coletar telemetria bruta via
API do RIPE Atlas;
:Salvar dados brutos em
/metadados/medicoes/;

partition "Processamento
e Tratamento" {
    :Carregar dados brutos;
    :Eliminar colunas e registros
desnecessários;
    :Tratar valores ausentes e timeouts;
    :Calcular janelas temporais, RTT e Jitter
relativo;
    :Extrair Vetor X (8 Atributos
Estatísticos);
    :Mapear classe alvo y no horizonte t + 12
min;
    :Exportar dataset_preditivo_t12_v1.csv;
}

partition "Treinamento e
Avaliação de ML" {
    :Particionar dataset cronologicamente\n(70%
Treino / 15% Validação / 15% Teste Cego);
    
    fork
        :Treinar Árvore de Decisão (CART);
    fork again
        :Treinar Random Forest;
    fork again
        :Treinar
XGBoost Classifier;
    end fork

    :Calcular Acurácia, F1-Macro e Precisão da
classe FALHA;
    :Comparar métricas dos três algoritmos;
    :Selecionar XGBoost como Modelo Campeão;
}

partition "Empacotamento
e Serviço (API REST)" {
    :Serializar modelo otimizado em formato
.joblib;
    :Carregar .joblib no backend da API REST;
    :Expor endpoint POST /predict;
}

stop
@endluml
```

![Diagrama de Atividades](./img_diagramas/diagrama_atividade.png)

---

## 3. Diagrama de Atividades II: Cenário de Uso (Inferência Online em t + 12 min)

Detalha o fluxo de inferência em tempo real e ações tomadas no painel de monitoramento.

```plantuml
@startuml
title Diagrama de Atividades - 5.2: Cenário de Uso do Sistema (Inferência Online em t + 12 min)

skinparam ActivityBackgroundColor #FFFFFF
skinparam ActivityBorderColor #2B2B2B
skinparam PartitionBorderColor #4A90E2

|#E8F0FE|Sonda / Cliente (Packet Tracer)|
|#E6F4EA|API REST (FastAPI)|
|#FEF7E0|Modelo XGBoost (.joblib)|

|Sonda / Cliente (Packet Tracer)|
start
:Capturar métricas de RTT, Jitter e Perda no
instante t;
:Enviar requisição POST /api/v1/previsoes/t12
com dados JSON;

|API REST (FastAPI)|
:Receber e validar payload da requisição via
Pydantic;

if (Payload válido e baseline disponível?) then
(sim)
    :Calcular vetor de 8 atributos estatísticos (z_robusto via MAD, n5);
    :Enviar vetor formatado para o modelo em memória;
else (não)
    :Retornar
erro HTTP 422 (Unprocessable Entity);
    |Sonda
/ Cliente (Packet Tracer)|
    :Exibir alerta de dados inconsistentes / amostragem insuficiente;
    stop
endif

|Modelo XGBoost (.joblib)|
:Processar vetor pelas 200 árvores de decisão;
:Classificar pior estado previsto para o
horizonte t + 12 min (OK, RISCO, FALHA);

|API REST (FastAPI)|
:Montar resposta JSON com a classe predita e
nível de confiança;
:Retornar HTTP 200 OK;

|Sonda / Cliente (Packet Tracer)|
:Receber diagnóstico preditivo para o horizonte
t + 12 min;

if (Status predito em t+12 min?) is (FALHA ou
RISCO) then
    :Emitir alarme visual no painel de monitoramento;
    :Disparar comutação/reencaminhamento preventivo de rota;
else (OK)
    :Manter rotina normal de monitoramento da infraestrutura;
endif

stop
@endluml
```

![Diagrama de Atividades II](./img_diagramas/diagrama_atividade_II.png)

---

## 4. Diagrama de Sequência

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
Pipeline -> RIPE: Requisita telemetria RTT /
ICMP
RIPE --> Pipeline: Retorna JSON/CSV com dados
brutos
Pipeline -> Pipeline: Elimina dados
desnecessários e trata nulos
Pipeline -> Pipeline: Extrai vetor de 8
atributos e rotula alvo em t + 12 min
Pipeline -> Pipeline: Treina e avalia Árvore,
Random Forest e XGBoost
Pipeline -> Model: Exporta o modelo XGBoost
campeão (.joblib)

== Fase 2: Inferência Preditiva via API REST ==
Client -> API: POST /predict (JSON com vetor
de 8 atributos em t)
API -> API: Valida esquema e tipos do JSON
API -> Model: Executa predicao(vetorX)
Model --> API: Retorna classe predita (OK /
RISCO / FALHA) em t + 12 min
API --> Client: Resposta HTTP 200 OK (JSON
com classe e confiança)
@endluml
```

![Diagrama de Sequência](./img_diagramas/diagrama_sequencia.png)

---

## 5. Diagrama de Classes

Apresenta a estrutura estática de classes do sistema, seus atributos, métodos e relacionamentos.

```plantuml
@startuml
skinparam classAttributeIconSize 0

class ColetorTelemetriaRIPE {
- measurementId : int
- startTimestamp : long
- stopTimestamp : long
- timeoutSec : int
  + requisitarTelemetriaBruta() : JSON
  + validarStatusHttp() : boolean
}

class ProcessadorTratamento {
- colunasIrrelevantes : List<String>
- tratarNulos : boolean
  + carregarDadosBrutos() : DataFrame
  + removerColunas() : DataFrame
  + tratarTimeouts() : DataFrame
}

class ExtratorAtributos {
- janelaObservacao : int
- madBaseline : double
  + calcularZRobusto(rtt : double) : double
  + calcularJitterRelativo() : double
  + extrairVetor8Atributos() : VetorAtributos
}

class RotuladorTarget {
- horizonteMinutos : int = 12
  + calcularPiorEstadoJanela() : String
  + atribuirRotuloTarget() : void
}

class TreinadorModelos {
- splitTreino : double = 0.70
- splitTeste : double = 0.15
  + treinarDecisionTree() : void
  + treinarRandomForest() : void
  + treinarXGBoost() : void
  + avaliarF1Macro() : double
}

class SerializadorModelo {
- caminhoArtefato : String = "modelo_xgboost.joblib"
  + selecionarCampeao() : void
  + exportarJoblib() : File
}

class ServicoRestFastAPI {
- host : String
- porta : int
- modeloEmMemoria : XGBoostModel
  + carregarModeloJoblib() : void
  + postPredict(vetor : VetorAtributos) : ResultadoPredicao
}

class ClienteConsumidor {
- ipOrigem : String
- idSonda : String
  + capturarMetricasInstanteT() : VetorAtributos
  + enviarRequisicaoPostPredict() : JSON
}

class VetorAtributos {
- zRobusto : double
- aumentoPct : double
- jitterRelativo : double
- perdaPct : double
- timeoutAtual : int
- n5Timeout : int
- n5Aumento80 : int
- n5Risco : int
  + toArray() : double[]
  + toJson() : String
}

class ResultadoPredicao {
- statusPredito : String
- confianca : double
- horizonteMinutos : int = 12
  + formatarRespostaHttp() : JSON
}

ColetorTelemetriaRIPE "1" --> "1..*" API_RIPE_Atlas : consulta dados de
ProcessadorTratamento "1" --> "1" ColetorTelemetriaRIPE : processa telemetria de
ExtratorAtributos "1" --> "1" ProcessadorTratamento : extrai atributos de
ExtratorAtributos "1" *-- "1..*" VetorAtributos : gera
RotuladorTarget "1" --> "1..*" VetorAtributos : rotula amostras de
TreinadorModelos "1" --> "1..*" VetorAtributos : treina com
TreinadorModelos ..> SerializadorModelo : aciona
ServicoRestFastAPI "1" --> "1" SerializadorModelo : carrega modelo de
ServicoRestFastAPI "1" --> "0..*" ClienteConsumidor : recebe requisição de
ServicoRestFastAPI "1" *-- "0..*" ResultadoPredicao : produz
@endluml
```

![Diagrama de Classes](./img_diagramas/diagrama_classe.png)