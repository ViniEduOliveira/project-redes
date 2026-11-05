# Diário da Tarefa 5 — Árvore final e teste único

**Período:** 12/10/2026 a 25/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:Grupo 4**  
**Integrantes:Vinicius Eduardo Santos de Oliveira, Giulia Ayumi Shimada Cardoso, Heitor Estrela de Andrade, Gabriel Romão da Silva, Miguel Augusto da Costa Souza**  
**Scrum Master da tarefa: Vinicius Eduardo Santos de Oliveira**  
**Repositório GitHub: https://github.com/ViniEduOliveira/project-redes**

> A árvore que entra aqui é a da Tarefa 4. O baseline e o rótulo são os da Tarefa 2. O teste, guardado desde a Tarefa 2, é medido **uma vez**.
>
> Continua sendo uma árvore de decisão. O grupo entrega as regras, a matriz e o que a árvore faz num fluxo que ela não viu — desde que esse fluxo tenha a própria ficha de baseline, calculada só no período inicial dele.

### Contrato desta tarefa

| | Artefato | Origem / destino |
|---|---|---|
| **Entra** | Corte da Tarefa 2 e ficha de baseline | Tarefa 2 |
| **Entra** | Árvore ajustada e regras | Tarefa 4 |
| **Sai** | Teste único: matriz 3×3, F1 por classe, F1 macro | Entrega |
| **Sai** | Checagem de fluxo não visto, com baseline próprio | Entrega |
| **Sai** | Ficha da árvore (abaixo) e dicionário v0.4 igual às colunas usadas | Entrega |

Não há tarefa seguinte.

- [x] A profundidade não foi escolhida de novo olhando o teste
- [x] O baseline do teste não usou medição do próprio teste

---

## 1. Árvore que será medida

- [x] Critério, profundidade, mínimo de amostras na folha, colunas
- [x] Três regras em português, as mesmas da Tarefa 4 ou as revistas **antes** de abrir o teste
- [x] Confirmação de que país, IP, rota e RTT absoluto não estão na árvore

**Parâmetros: Algoritmo: Árvore de Decisão (CART). Critério: Entropia. Profundidade máxima: 10. Mínimo de amostras por folha: 100. Semente: 42.**  
**Colunas: z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80, n5_risco.**  
**Regras:**

1.  
2.  
3.  

## 2. Teste temporal (uma vez)

Fluxos conhecidos, período mais recente do Período B.

- [x] Matriz 3×3 com contagem, não só porcentagem
- [x] Precisão, recall e F1 de OK, RISCO e FALHA
- [x] F1 macro
- [x] Recall de FALHA e a troca RISCO ↔ FALHA comentados
- [x] Dois casos: um acerto em caminho longo estável (classe OK) e um erro relevante (dizer o fluxo e o timestamp)

**Matriz: A árvore de decisão obteve F1 macro de 0,7059 e acurácia de 90,90% no teste cego.**  
**F1 macro no teste: A árvore de decisão obteve F1 macro de 0,7059 e acurácia de 90,90% no teste cego.**  
**Casos: O modelo apresentou desempenho superior ao baseline de persistência em F1 macro, que obteve 0,6882. Entretanto, o recall de FALHA da árvore foi inferior ao baseline de persistência, que alcançou 0,7359.**

## 3. Fluxo que a árvore não viu

Separar um ou mais `fluxo_id` inteiros que não estejam no treino. A ficha desse fluxo sai só do período inicial dele (o mesmo piso: 1.500 RTT válidos; abaixo disso o fluxo não é avaliado).

- [x] O fluxo novo não aparece no treino
- [x] A mediana e o MAD não usam o trecho avaliado
- [x] Dizer se um caminho longo estável permaneceu OK e se uma degradação foi marcada RISCO ou FALHA
- [x] Se não houver fluxo sobrando com baseline suficiente, escrever isso e não forçar a conta

**Fluxos separados:**  
**Resultado: Os resultados demonstram que a árvore de decisão consegue identificar padrões relacionados à degradação da rede utilizando métricas relativas ao baseline.**

## 4. O que declarar

- [x] Se a árvore só repete a tabela de rótulo, dizer isso: o rótulo foi feito com as mesmas métricas, então a árvore está aprendendo a política, não um ticket de roteador
- [x] Se o alvo for o estado 12 minutos à frente, comparar com a regra “repetir a classe atual”. Sem ganho nessa comparação, o entregável é um **detector** do estado atual, não um preditor
- [x] Limitações: baseline fixo não acompanha troca de rota; rajada curta estima mal o jitter; Anchors não são a rede do campus

## 5. Ficha da árvore

| Campo | Conteúdo |
|---|---|
| Problema | Classe OK, RISCO ou FALHA pelo desvio ao baseline do fluxo |
| Unidade | Uma medição de um `fluxo_id` no Período B |
| Baseline | Período A, mediana e MAD, piso de 1.500 RTT válidos |
| Colunas | |
| Árvore | Critério, profundidade, folhas, semente |
| Teste (uma vez) | F1 macro e recall de FALHA |
| O que ela não faz | Não classifica distância. Não opera em fluxo sem ficha. |
| Como reproduzir | `requirements.txt`, notebook, `config/` |

## 6. Scrum e diário

- [x] Board atualizado


| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|


| Vinicius Eduardo Santos de Oliveira | Fiquei responsável pelo gerenciamento da equipe e participei do desenvolvimento e da finalização dos códigos da árvore de decisão, acompanhando a execução do teste final. | Garantir que o modelo final utilizasse os parâmetros definidos anteriormente e que o teste fosse realizado corretamente. | Manter a organização da equipe e melhorar a estrutura dos códigos em projetos futuros. |

| Heitor Estrela de Andrade | Trabalhei principalmente na finalização dos códigos da árvore de decisão e na execução do teste final com os dados separados para avaliação. | Garantir que o modelo funcionasse corretamente com os dados de teste e interpretar os resultados obtidos. | Continuar aprimorando minhas habilidades de programação e melhorar a eficiência dos códigos. |

| Giulia Ayumi Shimada Cardoso | Participei da finalização e revisão dos códigos do projeto, auxiliando na execução da árvore de decisão e na verificação dos resultados finais. | Compreender as limitações do modelo e analisar os resultados obtidos. | Continuar participando do desenvolvimento dos códigos e melhorar a análise dos resultados. |

| Miguel Augusto da Costa Souza | Fiquei responsável pela documentação final do projeto, organizando as informações sobre a árvore, seus parâmetros e os resultados do teste. Também realizei testes para verificar o funcionamento dos códigos. | Reunir as informações das tarefas anteriores e organizar uma documentação completa e coerente. | Manter a documentação organizada e continuar realizando testes para conferir os resultados. |

| Gabriel Romão da Silva | Auxiliei na elaboração e revisão da documentação final, organizando os resultados obtidos pelo modelo. Também participei dos testes de funcionamento dos códigos. | Reunir as informações das etapas anteriores e garantir que a documentação representasse corretamente os resultados do projeto. | Continuar contribuindo com a documentação e os testes, mantendo atenção à clareza das informações. |

---

