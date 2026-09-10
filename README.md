# Preditor de Falha e Risco em Dispositivos de Rede

## Integrantes do Grupo
* Vinicius Eduardo Santos de Oliveira
* Giulia Ayumi Shimada Cardoso
* Heitor Estrela de Andrade
* Gabriel Romão da Silva
* Miguel Augusto da Costa Souza 


## Objetivo
Desenvolver, de forma iterativa e incremental via metodologia Scrum, uma aplicação que treine um modelo preditivo de árvore de decisão a partir de logs históricos de rede. O sistema deve disponibilizar um dashboard capaz de classificar o status dos dispositivos em tempo real, realizando o monitoramento da rede local e permitindo a injeção de eventos simulados sob demanda.

## Descrição do Projeto
Trata-se de um projeto interdisciplinar integrando Redes de Computadores, Estrutura de Dados II e Análise de Sistemas. A aplicação coleta métricas reais de dispositivos (como latência, perda de pacotes e jitter) para treinar o algoritmo de árvore de decisão. 

Em produção, o sistema conta com um dashboard em tempo real que monitora os IPs via ping e classifica o estado de cada dispositivo em três categorias principais: **OK**, **RISCO** ou **FALHA**. Além do monitoramento ao vivo, o projeto inclui um modo de simulação para forçar picos de instabilidade, garantindo a validação confiável da reação da rede em cenários variados.
