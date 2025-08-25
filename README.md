# Classificação de Sinais EEG-SSVEP com Redes Neurais MLP
Visão Geral do Projeto
Este projeto demonstra a classificação de Potenciais Evocados Visuais de Estado Estacionário (SSVEP) a partir de dados de Eletroencefalografia (EEG). O objetivo é identificar em qual de duas frequências de estímulo visual (8 Hz ou 10 Hz) um indivíduo está focando, utilizando uma rede neural do tipo Perceptron de Múltiplas Camadas (MLP).

A aplicação principal dessa tecnologia está em Interfaces Cérebro-Computador (BCI), permitindo que um usuário controle um sistema (como um teclado virtual) apenas com o olhar.

Principais Etapas do Projeto
Pré-processamento de Sinais EEG: Limpeza e filtragem dos dados brutos para remoção de ruídos.

Extração de Características: Utilização da Transformada Rápida de Fourier (FFT) para extrair características relevantes no domínio da frequência.

Seleção de Canais e Frequências: Foco nos canais da região occipital e nas frequências de estímulo e seus harmônicos.

Implementação de MLP: Construção e treinamento de uma rede neural para a tarefa de classificação.

Análise de Desempenho: Avaliação do modelo em três cenários distintos para testar a robustez e a capacidade de generalização.

Metodologia
O pipeline de processamento e classificação seguiu as seguintes etapas:

Carregamento dos Dados: Os dados de 35 indivíduos foram carregados a partir de arquivos .mat. Cada arquivo contém registros de EEG de 5 segundos para 40 frequências de estímulo diferentes, com 6 repetições cada.

Pré-processamento:

Recorte Temporal: 500ms do início e do fim de cada sinal foram removidos para eliminar artefatos de transição.

Filtro CAR (Common Average Reference): Um filtro espacial foi aplicado para reduzir o ruído comum a todos os canais, subtraindo a média dos sinais de todos os eletrodos de cada sinal individual.

Janelamento (Windowing): Cada sinal de 5 segundos foi dividido em 5 janelas de 1 segundo (250 amostras). Isso aumentou o número de amostras de treinamento em 5 vezes, servindo como uma forma de data augmentation.

Extração de Características com FFT:

A Transformada Rápida de Fourier (FFT) foi aplicada a cada janela de 1 segundo para converter o sinal do domínio do tempo para o domínio da frequência.

A principal característica extraída foi a potência do sinal nas frequências de estímulo (8 Hz e 10 Hz) e em seus harmônicos (16 Hz, 20 Hz, 24 Hz, etc.), que é onde a resposta SSVEP se manifesta com maior intensidade.

Seleção de Canais e Frequências:

Foram selecionados 9 canais da região occipital do cérebro (Pz, PO5, PO3, POz, PO4, PO6, O1, Oz, O2), que são os mais relevantes para o processamento de estímulos visuais.

Para a classificação, o foco foi na distinção entre os estímulos de 8 Hz e 10 Hz.

Normalização: As características foram normalizadas para cada sujeito individualmente, garantindo que a escala dos dados não influenciasse negativamente o treinamento do modelo.

Cenários de Avaliação e Resultados
Foram implementados três cenários para avaliar a performance do classificador MLP:

Cenário 1: Modelo Individual (Subject-Dependent)
Um modelo MLP foi treinado e testado para cada um dos 35 indivíduos, utilizando apenas os seus próprios dados. Este cenário simula uma aplicação calibrada para um usuário específico.

Acurácia Média: 86.19% ± 14.22%

Cenário 2: Modelo Generalizado (Leave-One-Subject-Out - LOSO)
Um modelo único foi treinado com os dados de 34 indivíduos e testado no indivíduo que ficou de fora. O processo foi repetido 35 vezes, com cada indivíduo servindo como teste uma vez. Este cenário avalia a capacidade de generalização do modelo para novos usuários sem calibração.

Acurácia Média: 88.81% ± 7.91%

Cenário 3: Modelo Generalizado com Exclusão de "Outliers"
Neste cenário, os 6 indivíduos com os piores resultados no Cenário 1 foram excluídos do conjunto de treinamento. Um novo modelo generalizado foi treinado com os 29 indivíduos restantes e testado nesses mesmos 29 indivíduos (usando a abordagem LOSO).

Acurácia Média (nos 29 indivíduos restantes): 89.30% ± 8.01%

Este último cenário demonstra que é possível criar um modelo generalizado ainda mais robusto ao remover dados de usuários que são "outliers" (possivelmente devido a baixa qualidade do sinal ou baixa resposta SSVEP).

Como Executar o Projeto
Pré-requisitos: Certifique-se de ter o Python 3 e as seguintes bibliotecas instaladas:

tensorflow
scikit-learn
numpy
matplotlib
seaborn
scipy
Você pode instalá-las com o comando:
pip install tensorflow scikit-learn numpy matplotlib seaborn scipy

Estrutura de Dados:

Crie uma pasta para armazenar os dados (ex: SSVEP_data).

Baixe os arquivos .mat do dataset SSVEP e coloque-os dentro desta pasta.

No notebook, atualize a variável FOLDER_PATH na primeira célula de código para o caminho da sua pasta de dados.

Ajuste os nomes dos arquivos para seguirem uma ordem que consiga acompanhar e identificar cada individuo.

Execução:

Abra o notebook Projeto_final_v3.ipynb em um ambiente como Jupyter Notebook ou JupyterLab.

Execute as células em ordem para reproduzir todo o pipeline de análise e os resultados.

Tecnologias Utilizadas
Linguagem: Python 3

Ambiente: Jupyter Notebook

Bibliotecas Principais:

TensorFlow (com Keras) para a construção da rede neural MLP.

Scikit-learn para pré-processamento, métricas de avaliação e normalização.

NumPy para manipulação de arrays multidimensionais.

SciPy para o cálculo da FFT e leitura dos arquivos .mat.

Matplotlib e Seaborn para a visualização dos resultados.
