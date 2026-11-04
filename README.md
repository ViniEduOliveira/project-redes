# 🌐 Preditor de Falha e Risco em Dispositivos de Rede (t + 12 min)

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![XGBoost](https://img.shields.io/badge/ML-XGBoost%201.7+-green.svg)](https://xgboost.readthedocs.io/)
[![Metodologia](https://img.shields.io/badge/Scrum-Sprint%207%2F7-brightgreen.svg)](#)

Projeto multidisciplinar de Bacharelado em Ciência da Computação integrando as disciplinas de **Redes de Computadores**, **Estrutura de Dados II** e **Análise e Projeto de Sistemas**. Desenvolvido pelo Grupo 4 através da metodologia ágil **Scrum**.

**Página do Projeto (Artigo Web):** https://projeto-redes-swart.vercel.app/
**Página do Projeto (Youtube):** https://www.youtube.com/watch?v=UV8yu7Ttres

---

## 👥 Integrantes do Grupo e Papéis

| Integrante | Papel Scrum | Responsabilidades Principais |
| :--- | :--- | :--- |
| **Vinicius Eduardo Santos de Oliveira** | Product Owner (PO) | Visão do produto, arquitetura de ML e integração do projeto |
| **Giulia Ayumi Shimada Cardoso** | Scrum Master (SM) | Gestão de sprints, facilitação ágil e revisão de código |
| **Heitor Estrela de Andrade** | Developer | Engenharia de atributos e otimização de dataset |
| **Gabriel Romão da Silva** | Developer | Processamento de séries temporais e tratamento de nulos |
| **Miguel Augusto da Costa Souza** | Developer | Documentação, testes e interface do Artigo Web |

**Docentes Orientadores (Stakeholders):** Andrea Sakai, Denise de Souza e Luis de Oliveira.

---

## 🎯 Objetivo do Projeto

Desenvolver um pipeline de Machine Learning capaz de diagnosticar o estado da rede e **antecipar instabilidades e falhas com 12 minutos de antecedência** ($t + 12\text{ min}$).

O sistema consome dados públicos de telemetria da internet (**RIPE Atlas**), aplica tratamento de séries temporais e engenharia de atributos estatísticos, classificando os enlaces entre três estados operacionais: **`OK`**, **`RISCO`** e **`FALHA`**. Os resultados e a metodologia são apresentados de forma interativa através de um **Artigo Web**.

---

## 🧩 Divisão dos Módulos do Projeto

O repositório está dividido em duas abordagens complementares de análise:

### 1. Classificação Instantânea (`classificacao_instantanea/`)
- **Foco:** Diagnóstico em tempo real ($t_0$).
- **Objetivo:** Identificar se o enlace de rede já se encontra degradado no momento exato da medição. Funciona como a linha de base (*baseline*) para validação das métricas brutas de latência, perda de pacotes e jitter.

### 2. Predição Preditiva Temporal (`predicao_12min/`) — Core do Projeto
- **Foco:** Projeção futura no horizonte de $t + 12\text{ min}$ (com tolerância de $\pm 30$ segundos).
- **Objetivo:** Analisar o comportamento da rede no instante $t$ através de um **vetor de 8 atributos estatísticos** e prever o estado operacional futuro, permitindo ações preventivas de gestão de infraestrutura.
- **Vetor de Entrada ($X$):** `z_robusto`, `aumento_pct`, `jitter_relativo`, `perda_pct`, `timeout_atual`, `n5_timeout`, `n5_aumento80`, `n5_risco`.
- **Validação:** Divisão temporal estrita (70% Treino, 15% Validação, 15% Teste Cego) garantindo ausência de vazamento de dados (*data leakage*).

---

## 📊 Comparativo de Desempenho dos Modelos ($t + 12\text{ min}$)

Avaliação realizada sobre o **conjunto cego de validação**:

| Algoritmo | Acurácia Global | F1-Macro | Precisão (Classe FALHA) | Tempo de Treino |
| :--- | :---: | :---: | :---: | :---: |
| **Árvore de Decisão (CART)** | $91.50\%$ | $0.7430$ | $91.20\%$ | $< 0.5\text{ s}$ |
| **Random Forest** | $92.30\%$ | $0.7438$ | $93.59\%$ | $< 2.0\text{ s}$ |
| **XGBoost Classifier (Campeão)** | **$92.80\%$** | **$0.7525$** | **$93.68\%$** | **$< 1.2\text{ s}$** |

> **Destaque:** O modelo XGBoost foi selecionado como campeão devido à sua superioridade na precisão da classe `FALHA` ($93.68\%$), minimizando significativamente a emissão de alarmes falsos.

---

## 🚀 Como Reproduzir os Experimentos

### 1. Pré-requisitos e Ambiente

Recomenda-se o uso do **Python 3.10+** ou execução direta no **Google Colab**. Para ambiente local, instale as dependências:

```bash
pip install pandas numpy scikit-learn xgboost joblib matplotlib seaborn requests
```

### 2. Execução dos Notebooks

1. **Classificação Instantânea ($t_0$):** Navegue até `classificacao_instantanea/notebooks/` para visualizar a análise exploratória e baselines do comportamento imediato da rede.
2. **Predição Futura ($t + 12\text{ min}$):** Acesse `predicao_12min/` e execute o notebook `experimento_preditivo_12min.ipynb` para rodar o pipeline completo do XGBoost (carregamento dos 8 atributos da camada Gold, treino, avaliação temporal e exportação do arquivo `.joblib`).

---

## 📚 Créditos e Referências

- RIPE NCC. **RIPE Atlas API v2 Manual**. Amsterdã: RIPE NCC. Disponível em: <https://atlas.ripe.net/docs/apis/rest-api-manual/>.
- CHEN, Tianqi; GUESTRIN, Carlos. **XGBoost: A Scalable Tree Boosting System**. In: ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, 2016.
- PEDREGOSA, F. et al. **Scikit-learn: Machine Learning in Python**. Journal of Machine Learning Research, v. 12, p. 2825-2830, 2011.
- SAKAI, Andrea Ono; SOUZA, Denise de; OLIVEIRA, Luis de. **Orientações do Projeto Interdisciplinar**. Universidade Braz Cubas, 2026.