# 📋 Especificação de Requisitos Mínimos (Projeto Preditivo de Redes t + 12 min)

## 1. Requisitos Funcionais (RF)

| ID | Nome do Requisito | Descrição |
| :---: | :--- | :--- |
| **RF001** | **Consumo da API RIPE Atlas** | O sistema deve realizar requisições HTTP GET à API REST pública do RIPE Atlas para coletar dados contínuos de medições de rede (ex.: medição Ping IPv4 ID 1001). |
| **RF002** | **Parametrização da Consulta** | O sistema deve permitir a configuração dos parâmetros de consulta da API RIPE Atlas, incluindo o identificador da medição (`measurement_id`), janela temporal de início e fim em timestamp UTC (`start`, `stop`) e tempo limite de requisição (`timeout`). |
| **RF003** | **Armazenamento e Exportação em CSV e JSON** | O sistema deve salvar e exportar os dados brutos recebidos da API em formato JSON e os dados normalizados/tabelados em formato CSV na camada de armazenamento (`raw`). |
| **RF004** | **Geração de Metadados de Execução** | O sistema deve gerar um arquivo JSON de metadados para cada coleta realizada, registrando a URL da requisição, parâmetros utilizados, timestamp UTC da execução, código de status HTTP e quantidade total de registros coletados. |
| **RF005** | **Extração e Cálculo de Métricas de Rede** | O sistema deve processar o *payload* das medições para extrair e calcular as variáveis de latência (RTT), taxa percentual de perda de pacotes (*packet loss*) e variação do atraso (*jitter*). |
| **RF006** | **Janelamento e Vetorização de Variáveis** | O sistema deve estruturar as métricas temporais em janelas deslizantes de observação para compor o vetor de atributos preditivos $X = [\text{latência}, \text{perda}, \text{jitter}]$. |
| **RF007** | **Treinamento da Árvore de Decisão** | O sistema deve treinar um modelo preditivo baseado no algoritmo de Árvore de Decisão (*Decision Tree*) a partir dos registros históricos de métricas para reconhecer padrões de anomalia. |
| **RF008** | **Classificação de Estado dos Dispositivos** | O sistema deve aplicar o modelo treinado para classificar o estado operacional de cada dispositivo monitorado em três níveis: `OK`, `RISCO` ou `FALHA`. |
| **RF009** | **Monitoramento Ativo via ICMP** | O sistema deve monitorar dispositivos da rede local em tempo real por meio de sondagens periódicas de ICMP (*ping*) disparadas para uma lista de endereços IP configurados. |
| **RF010** | **Visualização em Dashboard em Tempo Real** | O sistema deve fornecer um painel gráfico (*dashboard*) em tempo real que exiba o status de integridade de cada IP monitorado e gráficos temporais de latência, perda e *jitter*. |
| **RF011** | **Emissão de Alertas Visuais** | O sistema deve sinalizar alertas visuais imediatos na tela sempre que um dispositivo transitar para o estado de `RISCO` ou `FALHA`. |
| **RF012** | **Simulação e Injeção de Falhas** | O sistema deve fornecer um modo de simulação sob demanda capaz de injetar picos artificiais de instabilidade (aumento de latência e descarte de pacotes) para validar a resposta do modelo preditivo. |
| **RF013** | **Gerenciamento de Hosts Monitorados** | O sistema deve permitir o cadastro, consulta, edição e remoção dos endereços IP e identificadores dos dispositivos que compõem a topologia monitorada. |
| **RF014** | **Exportação de Relatórios de Incidentes** | O sistema deve disponibilizar a exportação de relatórios em formato CSV contendo o histórico consolidado de métricas, alterações de status e *logs* de ocorrências de falhas. |

---

## 2. Requisitos Não-Funcionais (RNF)

| ID | Categoria | Descrição do Requisito |
| :---: | :--- | :--- |
| **RNF001** | Desempenho e Tempo de Resposta | O endpoint de predição em tempo real (`POST /api/v1/previsoes/t12`) deve processar o vetor de atributos e retornar a resposta JSON em tempo inferior a **200 ms**. |
| **RNF002** | Disponibilidade e Redundância | A arquitetura física de rede deve garantir disponibilidade de **99,9%** no gateway da matriz por meio do protocolo HSRP (*Hot Standby Router Protocol*) entre os roteadores de borda. |
| **RNF003** | Métrica de Qualidade (F1-Macro) | O modelo preditivo XGBoost deve atingir um valor mínimo de **0,85** no indicador F1-Macro durante a avaliação no conjunto de teste cego. |
| **RNF004** | Portabilidade e Interoperabilidade | A API REST deve ser desenvolvida em Python (FastAPI) com suporte a padrões abertos JSON/HTTP, permitindo integração transparente com sondas RIPE Atlas e o Cisco Packet Tracer. |
| **RNF005** | Resiliência contra Outliers | O cálculo do indicador de anomalia de latência deve empregar o Desvio Absoluto Mediano (MAD) para evitar alarmes falsos por ruídos pontuais. |

---

## 3. Regras de Negócio (RN)

| ID | Nome | Descrição |
| :---: | :--- | :--- |
| **RN01** | **Independência da Camada de Apresentação** | O pipeline de Inteligência Artificial e a inferência preditiva devem funcionar autonomamente em ambiente de script/backend, sem dependência exclusiva da interface gráfica. |
| **RN02** | **Isolamento de Atributos Futuros** | É estritamente proibido utilizar qualquer medição recolhida após o instante $t$ para prever a classe do estado no horizonte futuro ($t + 12\text{ min}$). |
| **RN03** | **Priorização Contra Alarmes Falsos** | Em cenários de ajuste de limiar (*trade-off*), a precisão da classe `FALHA` deve ter prioridade sobre a taxa de cobertura (*recall*), garantindo confiabilidade aos operadores de rede. |