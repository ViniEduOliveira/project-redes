# 🚀 Scrum Framework: Preditor de Falha e Risco em Dispositivos de Rede (t + 12 min)

Projeto interdisciplinar (Redes de Computadores, Estrutura de Dados II e Análise de Sistemas) que desenvolve um pipeline de Machine Learning capaz de antecipar a degradação de rede com **12 minutos de antecedência** ($t + 12\text{ min}$), comparando os algoritmos **Árvore de Decisão (CART)**, **Random Forest** e **XGBoost** (modelo campeão) a partir de telemetria pública do **RIPE Atlas**. O projeto inclui um **Artigo Web/Dashboard** para visualização da metodologia, arquitetura e métricas de desempenho.

---

## 👥 Equipe e Papéis

| Papel | Responsável | Responsabilidade |
| :--- | :--- | :--- |
| **Product Owner (PO)** | Vinicius Eduardo Santos de Oliveira | Visão do produto, arquitetura de ML e integração do projeto. |
| **Scrum Master (SM)** | Giulia Ayumi Shimada Cardoso | Facilita os eventos, remove impedimentos e garante as práticas do Scrum. |
| **Development Team** | Vinicius Eduardo Santos de Oliveira, Giulia Ayumi Shimada Cardoso, Heitor Estrela de Andrade, Gabriel Romão da Silva, Miguel Augusto da Costa Souza | Ingestão, engenharia de atributos, treino dos modelos, desenvolvimento do pipeline preditivo e documentação web. |

**Orientadoras/Orientador:** Andrea Sakai, Denise de Souza, Luis de Oliveira (stakeholders das Sprint Reviews).

---

## ⚙️ Parâmetros do Projeto

| Parâmetro | Valor |
| :--- | :--- |
| Duração da sprint | 1 semana |
| Velocity de referência | 10 pontos/sprint |
| Fonte de dados | API Pública do RIPE Atlas (telemetria de internet) |
| Horizonte Temporal | $t + 12\text{ min}$ (tolerância de $\pm 30$ segundos) |
| Vetor de Características ($X$) | `[z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80, n5_risco]` no instante $t$ |
| Alvo ($y$) | Classe predita no instante $t + 12\text{ min}$ (`OK`, `RISCO`, `FALHA`) |
| Divisão de Dados | Estritamente cronológica sem vazamento (70% Treino, 15% Validação, 15% Teste Cego) |
| Modelo Campeão | **XGBoost Classifier** (F1-Macro: 0.7525, Acurácia: 92.80%, Precisão na FALHA: 93.68%) |
| Artefato de Modelo | Pacote otimizado serializado em `.joblib` |
| Interface de Apresentação | Artigo Web responsivo (HTML5 / CSS3 / JavaScript) |
| Fuso local | `America/Sao_Paulo` |

---

## 🗂️ Arquitetura de Camadas

| Camada | Localização / Artefato | Conteúdo |
| :--- | :--- | :--- |
| **Bronze (Bruta)** | `/metadados/medicoes/` | Dados brutos de telemetria e registros brutos das medições. |
| **Silver (Processada)** | `/metadados/etapa 2/` | Dados limpos, tipados e com timestamps normalizados. |
| **Gold (Analítica)** | `/metadados/preditivo/` | Vetor $X$ de 8 atributos e o arquivo final `dataset_preditivo_t12_v1.csv`. |
| **Modelos Preditivos** | `/predicao_12min/modelos/` | Pacote do modelo campeão serializado (`.joblib`) e relatórios de métricas. |
| **Web Article** | `/artigo/` | Interface gráfica de apresentação (`index.html`, `style.css`, `script.js`). |

---

## 🔄 Rituais (Scrum Events)

* **Sprint Planning** (1h): Define a **Sprint Goal** e seleciona itens do backlog.
* **Daily Scrum** (15 min): *O que fiz ontem? O que farei hoje? Há impedimentos?*
* **Sprint Review & Demo:** Demonstração funcional das métricas e dos notebooks preditivos aos orientadores.
* **Sprint Retrospective:** Avaliação do fluxo de trabalho e ajustes no pipeline.

---

## 📌 Visão Geral das Sprints

```mermaid
flowchart LR
    S1["S1<br/>Fonte e ingestão<br/>(medicoes)"] --> S2["S2<br/>Tratamento<br/>(etapa 2)"]
    S2 --> S3["S3<br/>Janelas, RTT<br/>e jitter"]
    S3 --> S4["S4<br/>Atributos,<br/>vetor X e t+12"]
    S4 --> S5["S5<br/>Modelos ML<br/>e XGBoost"]
    S5 --> S6["S6<br/>Inferência e<br/>Simulação"]
    S6 --> S7["S7<br/>Artigo Web<br/>e Requisitos"]
```

| Sprint | Objetivo | Pontos | Status |
| :---: | :--- | :---: | :---: |
| 1 | Fonte de dados e ingestão (`medicoes`) | 10 | ✅ Concluída |
| 2 | Tratamento e higienização (`etapa 2`) | 10 | ✅ Concluída |
| 3 | Janelas temporais, RTT e Jitter | 10 | ✅ Concluída |
| 4 | Vetor $X$ (8 atributos) e rotulagem $t + 12\text{ min}$ (`preditivo`) | 10 | ✅ Concluída |
| 5 | Modelagem ML (Árvore de Decisão, Random Forest e XGBoost) | 10 | ✅ Concluída |
| 6 | Testes de inferência, simulação e empacotamento do modelo (`modelos`) | 10 | ✅ Concluída |
| 7 | Artigo Web, especificação de requisitos e entrega final | 10 | ✅ Concluída |

---

## ✅ Definition of Done (DoD) Geral

1. Código versionado no Git (repositório `project-redes`) com commits descritivos seguindo Conventional Commits.
2. Notebooks e scripts reexecutáveis do início ao fim em ambiente limpo (Google Colab ou local).
3. Métricas de avaliação validadas no conjunto cego de validação sem vazamento temporal.
4. Modelo serializado em `.joblib` testado e salvo na pasta `/predicao_12min/modelos/`.
5. Latência de inferência do modelo validada e inferior a **2 ms** por amostra.
6. Artigo Web testado quanto à responsividade e correto funcionamento dos gráficos/diagramas.
7. Documentação de requisitos (`Requisitos_minimos.md`) e Scrum Framework atualizados.

---

## 🟢 Detalhamento das Sprints Executadas

### Sprint 1: Fonte de Dados e Ingestão (`medicoes`) — ✅ Concluída
* **US01 - Memorando de Decisão da Fonte:** Comparação técnica entre bases estáticas e a API pública do RIPE Atlas.
* **US02 - Seleção dos Alvos de Medição:** Mapeamento de sondas públicas e coleta de telemetria RTT/ICMP.
* **US03 - Integração com API REST do RIPE Atlas:** Ingestão automatizada com tratamento de exceções.
* **US04 - Persistência Bruta:** Armazenamento imutável dos dados coletados na pasta `/metadados/medicoes/`.

### 🟡 Sprint 2: Tratamento de Dados (`etapa 2`) — ✅ Concluída
* **US05 - Tratamento de Perda e Nulos:** Conversão de timeouts em instabilidades sinalizadas e remoção de duplicados.
* **US06 - Normalização Temporal:** Ajuste de timestamps para UTC e `America/Sao_Paulo`.
* **US07 - Tipagem e Higienização:** Estruturação de dados limpos na pasta `/metadados/etapa 2/`.

### 🔵 Sprint 3: Janelas, RTT e Jitter — ✅ Concluída
* **US08 - Janelas Deslizantes:** Agrupamento de medições temporais por sonda.
* **US09 - Cálculo de Jitter Relativo:** Medição das oscilações de RTT e variação estatística da latência.

### 🟠 Sprint 4: Vetor $X$ e Projeção $t + 12\text{ min}$ — ✅ Concluída
* **US10 - Engenharia de 8 Atributos:** Criação do vetor $X$ (`z_robusto`, `aumento_pct`, `jitter_relativo`, `perda_pct`, `timeout_atual`, `n5_timeout`, `n5_aumento80`, `n5_risco`).
* **US11 - Mapeamento do Alvo Futuro:** Rotulagem do estado da rede no horizonte $t + 12\text{ min}$ (`OK`, `RISCO`, `FALHA`).
* **US12 - Particionamento Temporal Estrito:** Divisão em 70% Treino, 15% Validação e 15% Teste Cego para evitar *data leakage*, gerando o `dataset_preditivo_t12_v1.csv`.

### 🟣 Sprint 5: Modelagem de Machine Learning — ✅ Concluída
* **US13 - Treinamento da Árvore de Decisão:** Baseline não-paramétrico (F1-Macro: 0.7430).
* **US14 - Treinamento do Random Forest:** Ensemble Bagging (F1-Macro: 0.7438, Precisão na FALHA: 93.59%).
* **US15 - Treinamento e Escolha do XGBoost:** Otimização por Gradient Boosting eleita como campeã (F1-Macro: 0.7525, Precisão na FALHA: 93.68%).
* **US16 - Exportação do Modelo:** Serialização do pacote `.joblib` otimizado.

### 🔴 Sprint 6: Inferência e Validação de Desempenho — ✅ Concluída
* **US17 - Pipeline de Inferência Preditiva:** Notebook `experimento_preditivo_12min.ipynb` configurado para recarregar o pacote `.joblib` e classificar novos vetores de atributos.
* **US18 - Testes de Latência de Inferência:** Medição e validação do tempo de processamento por predição ($< 2\text{ ms}$).
* **US19 - Simulação de Cenários de Degradação:** Testes com injeção sintética de picos de RTT e perda de pacotes para validar a resposta do XGBoost.

### ⚫ Sprint 7: Artigo Web, Requisitos e Entrega — ✅ Concluída
* **US20 - Artigo Web Interativo:** Desenvolvimento e ajuste da interface responsiva em `/artigo/` com os diagramas e tabelas comparativas.
* **US21 - Documentação de Requisitos:** Redação dos Requisitos Funcionais (RF), Não-Funcionais (RNF) e Regras de Negócio (RN) no arquivo `Requisitos_minimos.md`.
* **US22 - Consolidação do Repositório:** Integração no Git e atualização final da documentação.

---

## 🛠️ Matriz de Priorização (Sprint Backlog Final)

| ID | Descrição | Disciplina | Prioridade | Pontos | Sprint | Status |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: |
| **US01** | Memorando de decisão da fonte | Análise de Sistemas | Alta | 2 | 1 | ✅ Done |
| **US02** | Alvo Unicast e sondas RIPE | Redes | Alta | 2 | 1 | ✅ Done |
| **US03** | Ingestão via API REST RIPE | Redes | Alta | 3 | 1 | ✅ Done |
| **US04** | Persistência bruta em `/metadados/medicoes/` | Estrutura de Dados II | Alta | 3 | 1 | ✅ Done |
| **US05** | Perda e valores ausentes | Redes | Alta | 3 | 2 | ✅ Done |
| **US06** | Normalização temporal | Estrutura de Dados II | Média | 2 | 2 | ✅ Done |
| **US07** | Tipagem e higienização em `/etapa 2/` | Estrutura de Dados II | Média | 3 | 2 | ✅ Done |
| **US08** | Janelas e latência média | Redes | Alta | 5 | 3 | ✅ Done |
| **US09** | Cálculo de Jitter Relativo | Redes | Alta | 5 | 3 | ✅ Done |
| **US10** | Engenharia dos 8 Atributos | Estrutura de Dados II | Alta | 3 | 4 | ✅ Done |
| **US11** | Rotulagem no horizonte $t + 12\text{ min}$ | Redes / Análise | Alta | 5 | 4 | ✅ Done |
| **US12** | Divisão Temporal Cega (`dataset_preditivo`) | Estrutura de Dados II | Alta | 2 | 4 | ✅ Done |
| **US13** | Treino da Árvore de Decisão | Estrutura de Dados II | Alta | 5 | 5 | ✅ Done |
| **US14** | Treino do Random Forest | Estrutura de Dados II | Alta | 5 | 5 | ✅ Done |
| **US15** | Treino e Escolha do XGBoost | Estrutura de Dados II | Alta | 5 | 5 | ✅ Done |
| **US16** | Serialização do Modelo em `/modelos/` | Estrutura de Dados II | Média | 2 | 5 | ✅ Done |
| **US17** | Pipeline de Inferência Preditiva | Estrutura de Dados II | Alta | 5 | 6 | ✅ Done |
| **US18** | Testes de Latência de Inferência | Análise de Sistemas | Média | 2 | 6 | ✅ Done |
| **US19** | Simulação de Cenários de Degradação | Redes / Análise | Média | 3 | 6 | ✅ Done |
| **US20** | Artigo Web Responsivo em `/artigo/` | Análise / Front | Alta | 5 | 7 | ✅ Done |
| **US21** | Especificação em `Requisitos_minimos.md` | Análise de Sistemas | Alta | 3 | 7 | ✅ Done |
| **US22** | Relatório e Ajuste no Git | Análise de Sistemas | Alta | 2 | 7 | ✅ Done |

---

## ⚠️ Riscos Gerenciados e Mitigações

| Risco Mapeado | Impacto | Mitigação Aplicada no Projeto |
| :--- | :--- | :--- |
| **Vazamento Temporal (*Data Leakage*)** | Métricas irrealistas no teste | Divisão estritamente cronológica (70/15/15) por janelas de tempo sem misturar amostras futuras. |
| **Alarmes Falsos na Classe FALHA** | Desconfiança da operação de rede | Otimização do modelo visando alta precisão na classe `FALHA` (**93.68% no XGBoost**). |
| **Desbalanceamento de Classes** | Modelo ignora falhas raras | Utilização do Macro F1-Score como métrica principal de decisão e hiperparâmetros ajustados. |
| **Latência Elevada de Inferência** | Inviabilidade de uso em produção | Escolha de pacote `.joblib` otimizado garantindo inferência $< 1.5\text{ ms}$. |