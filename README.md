# Classificação de Sinais EEG-SSVEP com Redes Neuronais MLP

## Visão Geral do Projeto

Este projeto demonstra a classificação de Potenciais Evocados Visuais de Estado Estacionário (SSVEP) a partir de dados de Eletroencefalografia (EEG). O objetivo é identificar em qual de duas frequências de estímulo visual (8 Hz ou 10 Hz) um indivíduo está a focar, utilizando uma rede neuronal do tipo Perceptron de Múltiplas Camadas (MLP).

A aplicação principal desta tecnologia está em Interfaces Cérebro-Computador (BCI), permitindo que um utilizador controle um sistema (como um teclado virtual ou um drone) apenas com o olhar.

## Principais Etapas do Projeto

* **Pré-processamento de Sinais EEG:** Limpeza e filtragem dos dados brutos para remoção de ruídos.
* **Extração de Características:** Utilização da Transformada Rápida de Fourier (FFT) para extrair características relevantes no domínio da frequência.
* **Seleção de Canais e Frequências:** Foco nos canais da região occipital e nas frequências de estímulo e seus harmónicos.
* **Implementação de MLP:** Construção e treino de uma rede neuronal para a tarefa de classificação.
* **Análise de Desempenho:** Avaliação do modelo em três cenários distintos para testar a robustez e a capacidade de generalização.

## Metodologia

O pipeline de processamento e classificação seguiu as seguintes etapas:

1.  **Carregamento dos Dados:** Os dados de 35 indivíduos foram carregados a partir de ficheiros `.mat`. Cada ficheiro contém registos de EEG de 5 segundos para 40 frequências de estímulo diferentes, com 6 repetições cada.

2.  **Pré-processamento:**
    * **Recorte Temporal:** 500ms do início e do fim de cada sinal foram removidos para eliminar possíveis artefactos de transição.
    * **Filtro CAR (Common Average Reference):** Um filtro espacial foi aplicado para reduzir o ruído comum a todos os canais, subtraindo a média dos sinais de todos os elétrodos de cada sinal individual.

3.  **Janelamento (Windowing):** Cada sinal de 5 segundos foi dividido em 5 janelas de 1 segundo (250 amostras). Isso aumentou o número de amostras de treino em 5 vezes, servindo como uma forma de *data augmentation*.

4.  **Extração de Características com FFT:**
    * A Transformada Rápida de Fourier (FFT) foi aplicada a cada janela de 1 segundo para converter o sinal do domínio do tempo para o domínio da frequência.
    * A principal característica extraída foi a **potência do sinal** nas frequências de estímulo (8 Hz e 10 Hz) e nos seus harmónicos (16 Hz, 20 Hz, 24 Hz, etc.), que é onde a resposta SSVEP se manifesta com maior intensidade.

5.  **Seleção de Canais e Frequências:**
    * Foram selecionados 9 canais da região occipital do cérebro (`Pz, PO5, PO3, POz, PO4, PO6, O1, Oz, O2`), que são os mais relevantes para o processamento de estímulos visuais.
    * Para a classificação, o foco foi na distinção entre os estímulos de **8 Hz** e **10 Hz**.

6.  **Normalização:** As características foram normalizadas (Z-score) para cada sujeito individualmente, garantindo que a escala dos dados não influenciasse negativamente o treino do modelo.

## Cenários de Avaliação e Resultados

Foram implementados três cenários para avaliar a performance do classificador MLP:

### Cenário 1: Modelo Individual (Subject-Dependent)

Um modelo MLP foi treinado e testado para cada um dos 35 indivíduos, utilizando apenas os seus próprios dados. Este cenário simula uma aplicação calibrada para um utilizador específico.

* **Acurácia Média:** `86.19% ± 14.22%`

### Cenário 2: Modelo Generalizado (Leave-One-Subject-Out - LOSO)

Um modelo único foi treinado com os dados de 34 indivíduos e testado no indivíduo que ficou de fora. O processo foi repetido 35 vezes, com cada indivíduo a servir como teste uma vez. Este cenário avalia a capacidade de generalização do modelo para novos utilizadores sem calibração.

* **Acurácia Média:** `88.81% ± 7.91%`

### Cenário 3: Modelo Generalizado com Exclusão de "Outliers"

Neste cenário, os 6 indivíduos com os piores resultados no Cenário 1 foram excluídos do conjunto de treino. Um novo modelo generalizado foi treinado com os 29 indivíduos restantes e testado nesses mesmos 29 indivíduos (usando a abordagem LOSO).

* **Acurácia Média (nos 29 indivíduos restantes):** `92.30% ± 6.01%`
* **Acurácia do modelo treinado nos 29 melhores ao ser testado nos 6 piores:** `77.22% ± 9.98%`

Este último cenário demonstra que é possível criar um modelo generalizado ainda mais robusto ao remover dados de utilizadores que são "outliers" (possivelmente devido a baixa qualidade do sinal ou baixa resposta SSVEP).

## Como Executar o Projeto

1.  **Pré-requisitos:** Certifique-se de ter o Python 3 e as seguintes bibliotecas instaladas:
    ```
    tensorflow
    scikit-learn
    numpy
    matplotlib
    seaborn
    scipy
    ```
    Pode instalá-las com o comando:
    `pip install tensorflow scikit-learn numpy matplotlib seaborn scipy`

2.  **Estrutura de Dados:**
    * Crie uma pasta para armazenar os dados (ex: `SSVEP_data`).
    * Descarregue os ficheiros `.mat` do dataset SSVEP e coloque-os dentro desta pasta.
    * No notebook, atualize a variável `FOLDER_PATH` na primeira célula de código para o caminho da sua pasta de dados.

3.  **Execução:**
    * Abra o notebook `Projeto_final_v3.ipynb` num ambiente como Jupyter Notebook ou JupyterLab.
    * Execute as células por ordem para reproduzir todo o pipeline de análise e os resultados.

## Tecnologias Utilizadas

* **Linguagem:** Python 3
* **Ambiente:** Jupyter Notebook
* **Bibliotecas Principais:**
    * TensorFlow (com Keras) para a construção da rede neuronal MLP.
    * Scikit-learn para pré-processamento, métricas de avaliação e normalização.
    * NumPy para manipulação de arrays multidimensionais.
    * SciPy para o cálculo da FFT e leitura dos ficheiros `.mat`.
    * Matplotlib e Seaborn para a visualização dos resultados.
