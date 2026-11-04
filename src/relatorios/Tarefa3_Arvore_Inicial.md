# Diário da Tarefa 3 — Primeira árvore de decisão

**Período:** 28/09/2026 a 04/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:Grupo 4**  
**Integrantes:Vinicius Eduardo Santos de Oliveira, Giulia Ayumi Shimada Cardoso, Heitor Estrela de Andrade, Gabriel Romão da Silva, Miguel Augusto da Costa Souza**  
**Scrum Master da tarefa: Vinicius Eduardo Santos de Oliveira**  
**Repositório GitHub: https://github.com/ViniEduOliveira/project-redes**

> Tarefa 1 coletou. Tarefa 2 fez o baseline e o rótulo. Esta tarefa **não refaz** a ficha nem a tabela de classes.
>
> O modelo é uma **árvore de decisão**. O entregável é a árvore: atributos de cada divisão, profundidade, folhas e as regras lidas em português. Não é um desfile de algoritmos.
>
> A árvore oficial usa só métricas relativas ao baseline do fluxo. Uma segunda árvore, de contraste, pode ver a rota ou o RTT absoluto — só para mostrar que ela aprende distância. Essa árvore de contraste **não** é o modelo do projeto.

### Contrato desta tarefa

| | Artefato | Origem / destino |
|---|---|---|
| **Entra** | Ficha de baseline, dataset rotulado, corte temporal | Tarefa 2 — **os mesmos** |
| **Sai** | Árvore treinada só no treino, com profundidade e critério registrados | Tarefa 4 mexe nesta árvore, não no rótulo |
| **Sai** | Regras da árvore em texto e matriz 3×3 (OK, RISCO, FALHA) | Tarefa 4, para atacar o erro |
| **Sai** | Árvore de contraste (com rota ou RTT absoluto) e a frase do que ela aprendeu | Prova de que o modelo oficial não segue esse atalho |

**Não sai daqui:** troca de algoritmo (floresta, boosting, redes), limiar fino, model card.

- [x] Treino, validação e teste são os da Tarefa 2
- [x] A ficha de baseline não foi recalculada com o Período B

---

## 1. Colunas que a árvore oficial pode usar

Entram: `z_robusto`, `aumento_pct`, `jitter_relativo`, `perda_pct`, `timeout_atual`, `n5_timeout`, `n5_aumento80`, `n5_risco`.

Não entram: país, IP, `rota_id`, `fluxo_id`, nome do destino, RTT em milissegundos no lugar das métricas relativas.

- [x] Lista de colunas do treino colada no diário
- [x] Conferido que a classe (`OK` / `RISCO` / `FALHA`) é o alvo, não uma feature

**Colunas usadas: z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80, n5_risco.**

## 2. A árvore

- [x] Uma árvore de decisão (CART: Gini ou entropia — declarar qual)
- [x] `max_depth` e `min_samples_leaf` escolhidos olhando **só a validação**, não o teste
- [x] Semente registrada se o treino for estocástico
- [x] Ajuste (`fit`) só no treino
- [ ] Profundidade real, número de folhas e as primeiras divisões descritas
- [ ] Pelo menos três regras no formato “se métrica ≤ limiar e … então classe”, copiadas da árvore (não inventadas)

**Critério, profundidade máxima pedida, profundidade obtida, folhas: Árvore de decisão: critério entropia. Profundidade máxima: 10. Mínimo de amostras por folha: 100.**  
**Regras lidas da árvore: Foi utilizado o algoritmo DecisionTreeClassifier (CART), com critério de entropia (entropy), profundidade máxima de 10, mínimo de 100 amostras por folha e semente aleatória 42. O treinamento foi realizado somente com os dados de treino, enquanto a escolha dos parâmetros foi feita utilizando a validação.**

1.  
2.  
3.  

## 3. Leitura do erro

No bloco de **validação** (o teste fica fechado até a Tarefa 5; se o grupo já mediu o teste aqui, registra e **não** escolhe profundidade de novo olhando esse número):

- [ ] Matriz 3×3 com contagem
- [ ] Precisão, recall e F1 de OK, RISCO e FALHA
- [ ] F1 macro
- [ ] Acurácia sozinha não decide: com maioria OK, ela fica alta mesmo errando FALHA
- [ ] Dois erros concretos: uma medição de caminho longo estável que tenha caído em FALHA, ou uma FALHA de caminho curto que tenha caído em OK. Se não houver, dizer que o caso não apareceu na validação

**Matriz e F1 macro (validação): A árvore de decisão obteve F1 macro de 0,7436 e acurácia de 92,09% na validação. Para a classe FALHA, a precisão foi de 0,9276 e o recall foi de 0,6621. Os resultados demonstram que a árvore apresenta boa capacidade de classificação geral.**  
**Erros concretos (fluxo, timestamp, métricas, classe verdadeira, classe da árvore):**

## 4. Árvore de contraste (não é o modelo)

Treinar outra árvore, no mesmo treino, acrescentando a rota ou o RTT absoluto.

- [ ] Dizer qual divisão apareceu perto da raiz (em geral a rota ou um corte fixo de milissegundos)
- [ ] Declarar que essa árvore fica fora da entrega final

**O que a árvore de contraste usou na raiz: A árvore de contraste utiliza informações de rota ou RTT absoluto para demonstrar como uma árvore pode aprender padrões relacionados à distância e às características específicas dos caminhos de rede. Essa árvore possui finalidade exclusivamente comparativa e não faz parte do modelo oficial do projeto, pois o objetivo é classificar a degradação utilizando métricas relativas ao baseline de cada fluxo.**

## 5. Scrum e diário
,
- [x] Board atualizado

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
| | | | |

---
