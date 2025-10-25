# Segmentação de Objetos em Ambientes Naturais (Dataset CamVid)

Este repositório contém o projeto final da **Equipe 5**, desenvolvido para o programa **Atlântico Avanti**. O objetivo deste projeto é treinar e avaliar um modelo de Deep Learning (U-Net) para a tarefa de segmentação semântica em cenas de trânsito, utilizando o dataset CamVid.

A segmentação semântica classifica cada pixel de uma imagem em uma categoria pré-definida (ex: Carro, Estrada, Pedestre, Céu), uma tarefa crucial para veículos autônomos e análise de cenas urbanas.




## 📋 Tabela de Conteúdos
1.  [Sobre o Dataset (CamVid)](#-sobre-o-dataset-camvid)
2.  [Metodologia](#-metodologia)
    * [Arquitetura do Modelo (U-Net)](#arquitetura-do-modelo-u-net)
    * [Transfer Learning (ResNet-50)](#transfer-learning-resnet-50)
    * [Aumento de Dados](#aumento-de-dados)
3.  [Tecnologias Utilizadas](#-tecnologias-utilizadas)
4.  [Como Executar](#-como-executar)
5.  [Resultados](#-resultados)
    * [Curvas de Treinamento](#curvas-de-treinamento)
    * [Métricas de Teste](#métricas-de-teste)
    * [Matriz de Confusão](#matriz-de-confusão)
    * [Análise Qualitativa](#análise-qualitativa)
6.  [Conclusões e Trabalhos Futuros](#-conclusões-e-trabalhos-futuros)
7.  [Equipe](#-equipe)
8.  [Referências](#-referências)

---

## 💾 Sobre o Dataset (CamVid)

Utilizamos o **CamVid (Cambridge-Driving Labeled Video Database)**, um dataset popular para segmentação semântica focado em cenas de trânsito.

Após a Análise Exploratória de Dados (EDA), o dataset foi dividido da seguinte forma:
* **Treino:** 369 imagens e 369 máscaras
* **Validação:** 100 imagens e 100 máscaras
* **Teste:** 232 imagens e 232 máscaras

A análise (slides 6-8) confirmou que todas as imagens são `.png` com dimensão de 960x720 pixels e que as classes são **altamente desbalanceadas**. Classes como `Road` e `Sky` são muito frequentes, enquanto `Bicyclist` e `Animal` são raras. Esta descoberta foi crucial para a escolha da nossa função de perda (Loss).

---

## 🔬 Metodologia

O projeto foi estruturado em três fases: 1) Aquisição e Análise de Dados, 2) Pré-processamento e Aumento de Dados, e 3) Modelagem e Avaliação.

### Arquitetura do Modelo (U-Net)

A arquitetura escolhida foi a **U-Net**, famosa por sua eficácia em tarefas de segmentação biomédica e, posteriormente, em cenas urbanas.
* **Encoder (Codificador):** Analisa a imagem, extrai características e reduz a resolução.
* **Decoder (Decodificador):** Reconstrói a imagem, aumentando a resolução para criar a máscara de segmentação.
* **Skip Connections:** Conectam o Encoder ao Decoder para preservar detalhes finos e informações de localização precisas.

### Transfer Learning (ResNet-50)

Para acelerar o treinamento e melhorar a precisão, utilizamos **Transfer Learning**.
* Usamos um encoder **ResNet-50**, pré-treinado no gigantesco banco de dados **ImageNet**.
* Isso significa que nosso modelo já começa o treinamento com uma "inteligência visual" avançada, capaz de reconhecer características básicas de objetos.

### Aumento de Dados

Para evitar *overfitting* e tornar o modelo mais robusto a diferentes condições (luz, ângulo), utilizamos a biblioteca `albumentations` para aplicar transformações aleatórias apenas nos dados de treino:
* `A.HorizontalFlip`: Inversão horizontal.
* `A.ColorJitter`: Alterações de cor e brilho.
* `A.GaussianBlur`: Desfoque.
* E outras (Rotação, `RandomBrightnessContrast`, etc.)

---

## 🚀 Tecnologias Utilizadas

Este projeto foi construído inteiramente em **Python**. As principais bibliotecas e frameworks utilizados são:

* **Framework Principal:** PyTorch
* **Biblioteca de Segmentação:** `segmentation-models-pytorch (smp)`
* **Análise de Dados:** NumPy e Pandas
* **Manipulação de Imagem:** OpenCV e Pillow
* **Aumento de Dados:** Albumentations
* **Visualização:** Matplotlib e Seaborn

---

## ⚙️ Como Executar

O projeto está contido em um notebook (`.ipynb`) que deve ser executado no Google Colab para aproveitar a aceleração de GPU.

1.  **Clone o repositório:**
    ```bash
    git clone [(https://github.com/eloisamarin/projeto-segmentacao-camvid)]((https://github.com/eloisamarin/projeto-segmentacao-camvid))
    ```
2.  **Abra o Notebook no Google Colab:**
    * Faça o upload do arquivo `seu_notebook.ipynb` para o Colab.
3.  **Habilite a GPU:**
    * No menu, vá em `Ambiente de execução` -> `Alterar o tipo de ambiente de execução` -> `Acelerador de hardware` -> `GPU`.
4.  **Instale as Dependências:**
    * A primeira célula do notebook contém os comandos `!pip install` necessários.
5.  **Execute as Células:**
    * Execute todas as células em ordem. O notebook fará o download do dataset (via Kaggle API), fará a análise, definirá o modelo, treinará e, ao final, exibirá os resultados da avaliação.

---

## 📊 Resultados

O modelo foi treinado por 25 épocas usando o otimizador **Adam**, **ReduceLROnPlateau** (Scheduler) e **EarlyStopping** para salvar o melhor modelo e evitar *overfitting*. As métricas de avaliação principais foram **IoU (Intersection over Union)**, **Coeficiente Dice** e **Acurácia**.

### Curvas de Treinamento

O gráfico de perdas (Loss) demonstra a convergência bem-sucedida do modelo. A perda de treino (`Train Loss`) e a perda de validação (`Val Loss`) diminuem de forma estável, indicando que o modelo aprendeu sem *overfitting* significativo.
*(Baseado no gráfico de Loss do slide 17)*



## 🔮 Conclusões e Trabalhos Futuros

### Conclusões
O estudo validou com sucesso o uso da arquitetura U-Net com um encoder ResNet-50 pré-treinado para segmentação semântica no CamVid. A validação de um pipeline de *Transfer Learning* e uma análise (EDA) detalhada foram contribuições chave, guiando a escolha do modelo e da função de perda combinada (Dice + Focal) para lidar com o desbalanceamento de classes.

### Possibilidades de Novos Estudos
* **Testar Encoders Mais Modernos:** Substituir o `ENCODER` por `EfficientNet-B5`, que já está previsto no código.
* **Ajuste de Hiperparâmetros:** Otimizar o `LAMBDA` (peso entre Dice e Focal) ou a Taxa de Aprendizado (`LR`).
* **Aumento de Dados Agressivo:** Aplicar técnicas mais avançadas para melhorar o desempenho em classes raras.

---

## 👥 Equipe

* **Eloisa Marin da Silva Pessoa**
* **Lucas dos Santos Fileto**
* **Victor Leandro da Silva**
