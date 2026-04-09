# Relatório da Atividade 5 — Avaliação de Performance MPI
 
**Disciplina:** PROGRAMAÇÃO CONCORRENTE E DISTRIBUÍDA
**Aluno(s):** João Gabriel Lucas Pinheiro de Lima
**Turma:** ADS Noturno
**Professor:** Rafael Marconi Ramos
**Data:** 08/04/2026
 
---
 
# 1. Descrição do Problema
 
O programa implementa um sistema de **detecção de perguntas duplicadas** sobre o dataset *Quora Question Pairs* (arquivo `nlp_features_train.csv`, disponível no Kaggle). O objetivo é encontrar, dentre um conjunto de perguntas, os pares com maior similaridade textual.
 
O algoritmo utilizado é a **similaridade do cosseno** baseada em representação TF-IDF (Term Frequency–Inverse Document Frequency), calculada com auxílio da biblioteca **pandas** para manipulação dos dados. Para cada par de perguntas possível, o programa calcula um valor de similaridade entre 0 e 1, sendo 1,0 o valor correspondente a perguntas idênticas.
 
A entrada utilizada nos testes contém **10.000 perguntas**, gerando **49.995.000 pares avaliados** na versão serial e **12.497.500 pares por processo** nas versões distribuídas.
 
**Questões respondidas:**
 
- **Objetivo do programa:** identificar os pares de perguntas mais semanticamente similares em um grande corpus de texto.
- **Volume de dados:** ~50 milhões de pares avaliados no total.
- **Algoritmo:** similaridade do cosseno com vetorização TF-IDF.
- **Complexidade aproximada:** O(n²) para a comparação dos pares, onde n é o número de perguntas.
- **Objetivo da paralelização:** distribuir a carga de comparação dos pares entre múltiplos processos MPI para reduzir o tempo de execução.
 
---
 
# 2. Ambiente Experimental
 
| Item                        | Descrição                          |
| --------------------------- | ---------------------------------- |
| Processador                 | Intel Core i7-12700                |
| Número de núcleos           | 12 núcleos físicos (20 threads)    |
| Memória RAM                 | 16 GB                              |
| Sistema Operacional         | Windows 11                         |
| Linguagem utilizada         | Python                             |
| Biblioteca de paralelização | mpi4py (MPI para Python)           |
| Compilador / Versão         | Microsoft MPI + Python 3.x / pandas|
 
---
 
# 3. Metodologia de Testes
 
O tempo de execução foi medido internamente pelo próprio programa, que registra o instante de início e fim do processamento e exibe o tempo total ao final da execução.
 
Foram realizadas execuções individuais para cada configuração (1 execução por configuração, sem cálculo de média), sendo os tempos reportados diretamente a partir da saída do programa.
 
O tamanho da entrada foi fixo em todas as execuções: **10.000 perguntas** extraídas do dataset `nlp_features_train.csv`.
 
### Configurações testadas
 
- 1 processo (versão serial — `python avaliador.py`)
- 2 processos (`mpiexec -n 2 python avaliador_mpi.py`)
- 4 processos (`mpiexec -n 4 python avaliador_mpi.py`)
- 8 processos (`mpiexec -n 8 python avaliador_mpi.py`)
- 12 processos (`mpiexec -n 12 python avaliador_mpi.py`)
 
### Procedimento experimental
 
- 1 execução por configuração
- Tempo extraído diretamente da saída do programa
- Execuções realizadas em máquina local com Windows 11, sem isolamento de carga do sistema operacional
 
---
 
# 4. Resultados Experimentais
 
Tempos de execução obtidos em cada configuração:
 
| Nº Processos | Tempo de Execução (s) |
| ------------ | --------------------- |
| 1            | 107,56                |
| 2            | 23,16                 |
| 4            | 15,54                 |
| 8            | 11,15                 |
| 12           | 10,48                 |
 
---
 
# 5. Cálculo de Speedup e Eficiência
 
### Speedup
 
```
Speedup(p) = T(1) / T(p)
```
 
Onde:
 
- **T(1)** = tempo da execução serial
- **T(p)** = tempo com p processos
 
### Eficiência
 
```
Eficiência(p) = Speedup(p) / p
```
 
Onde:
 
- **p** = número de processos
 
---
 
# 6. Tabela de Resultados
 
| Processos | Tempo (s) | Speedup | Eficiência |
| --------- | --------- | ------- | ---------- |
| 1         | 107,56    | 1,00    | 1,000      |
| 2         | 23,16     | 4,65    | 2,323      |
| 4         | 15,54     | 6,92    | 1,730      |
| 8         | 11,15     | 9,65    | 1,206      |
| 12        | 10,48     | 10,26   | 0,855      |
 
> **Nota:** Os valores de eficiência acima de 1,0 para 2, 4 e 8 processos indicam um **speedup superlinear**, discutido na seção de análise.
 
---
 
# 7. Gráfico de Tempo de Execução
 
O gráfico de linhas abaixo ilustra a redução do tempo de execução conforme o número de processos aumenta. Os dados plotados são:
 
| Processos | Tempo (s) |
|-----------|-----------|
| 1         | 107,56    |
| 2         | 23,16     |
| 4         | 15,54     |
| 8         | 11,15     |
| 12        | 10,48     |
 
Eixo X: número de processos MPI. Eixo Y: tempo de execução em segundos. A curva apresenta queda acentuada entre 1 e 2 processos e estabilização a partir de 8 processos.
 
![Gráfico Tempo de Execução](graficos/tempo_execucao.png)
 
---
 
# 8. Gráfico de Speedup
 
O gráfico de linhas abaixo compara o speedup obtido com o speedup ideal (linear). Os dados plotados são:
 
| Processos | Speedup obtido | Speedup ideal |
|-----------|---------------|---------------|
| 1         | 1,00          | 1             |
| 2         | 4,65          | 2             |
| 4         | 6,92          | 4             |
| 8         | 9,65          | 8             |
| 12        | 10,26         | 12            |
 
Eixo X: número de processos MPI. Eixo Y: speedup. A linha tracejada representa o speedup ideal linear; a linha sólida representa o speedup obtido.
 
![Gráfico Speedup](graficos/speedup.png)
 
---
 
# 9. Gráfico de Eficiência
 
O gráfico de linhas abaixo mostra a eficiência da paralelização. Os dados plotados são:
 
| Processos | Eficiência |
|-----------|-----------|
| 1         | 1,000     |
| 2         | 2,323     |
| 4         | 1,730     |
| 8         | 1,206     |
| 12        | 0,855     |
 
Eixo X: número de processos MPI. Eixo Y: eficiência. A linha tracejada em 1,0 representa a eficiência ideal. Valores acima de 1,0 indicam speedup superlinear; abaixo de 1,0 indicam overhead de paralelização.
 
![Gráfico Eficiência](graficos/eficiencia.png)
 
---
 
# 10. Análise dos Resultados
 
### Speedup superlinear
 
O resultado mais notável deste experimento é o **speedup superlinear** observado em 2, 4 e 8 processos. Teoricamente, o speedup máximo esperado com `p` processos é igual a `p` (speedup ideal). No entanto, os valores obtidos — 4,65x com 2 processos e 9,65x com 8 — superam esse limite.
 
A causa mais provável é o **efeito de cache**: quando a versão serial processa ~50 milhões de pares sequencialmente, os dados não cabem no cache do processador, causando muitos *cache misses*. Na versão distribuída, cada processo trabalha com apenas ~12,5 milhões de pares, volume que cabe melhor na hierarquia de cache do i7-12700, reduzindo acessos à memória RAM e acelerando proporcionalmente o processamento.
 
### Escalabilidade
 
A aplicação apresentou **boa escalabilidade até 8 processos**, com ganhos expressivos de desempenho. Entre 8 e 12 processos, o ganho foi mínimo (de 11,15s para 10,48s), indicando que o programa está se aproximando do limite de saturação para essa carga de trabalho.
 
### Overhead e saturação
 
O processador i7-12700 possui 12 núcleos físicos. Com 12 processos, cada núcleo é ocupado por um processo, o que em teoria deveria ser ideal. Porém, o ganho marginal entre 8 e 12 processos sugere que outros fatores limitam o desempenho, como:
 
- **Overhead de comunicação MPI** na fase de coleta e consolidação dos resultados (operação de *gather*);
- **Contenção de memória e barramento**, já que múltiplos processos competem pelo acesso à RAM;
- **Overhead do sistema operacional** no gerenciamento dos processos.
 
### Eficiência
 
A eficiência caiu progressivamente com o aumento de processos. Com 12 processos, a eficiência foi de 0,855, indicando que cerca de 14,5% do potencial computacional foi perdido em overhead. Isso é um resultado razoável para uma aplicação de processamento de texto com fase de consolidação de resultados.
 
---
 
# 11. Conclusão
 
O experimento demonstrou que a paralelização com MPI trouxe ganhos de desempenho muito significativos para o problema de comparação de similaridade entre perguntas. O tempo de execução foi reduzido de **107,56 segundos** (serial) para **10,48 segundos** com 12 processos, representando uma redução de aproximadamente **90% no tempo total**.
 
O fenômeno de speedup superlinear observado reforça a importância dos efeitos de cache em aplicações com grande volume de dados, um aspecto nem sempre considerado na análise teórica de desempenho paralelo.
 
O melhor custo-benefício foi obtido com **8 processos**, que entregou speedup de 9,65x com eficiência de 1,206. A partir desse ponto, os ganhos são decrescentes e o overhead de coordenação começa a pesar mais.
 
Como melhorias futuras, seria interessante avaliar o uso de **threads híbridas (MPI + OpenMP)**, otimizar a fase de consolidação dos resultados com operações de *reduce* mais eficientes, e testar com entradas maiores para verificar se a escalabilidade se mantém.