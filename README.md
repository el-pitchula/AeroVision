# AeroVision

### Sistema de Percepção Visual para Detecção e Segmentação de Objetos em Imagens Aéreas

[![Python](https://img.shields.io/badge/Python-3.x-blue)]()
[![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-orange)]()
[![YOLO](https://img.shields.io/badge/YOLO-Ultralytics-green)]()
[![License](https://img.shields.io/badge/Code-MIT-lightgrey)]()

---

## Visão geral

**AeroVision** é um sistema de visão computacional desenvolvido para a percepção de objetos em imagens aéreas obtidas por veículos aéreos não tripulados (UAVs).

O projeto investiga uma cadeia de processamento composta por:

```text
Imagem aérea
     │
     ▼
Pré-processamento
     │
     ▼
Detecção de objetos
     │
     ▼
Segmentação de instâncias
     │
     ▼
Avaliação
     │
     ▼
Inferência em vídeo
     │
     ▼
Tracking (extensão)
```

O sistema foi desenvolvido no contexto da disciplina de **Visão Computacional e Reconhecimento de Padrões**, com foco na aplicação prática de técnicas modernas de Deep Learning para percepção visual.

---

# Objetivo

## Objetivo geral

Desenvolver e avaliar um sistema de visão computacional capaz de detectar e segmentar veículos em imagens aéreas obtidas por UAVs.

## Objetivos específicos

* analisar exploratoriamente um dataset de imagens aéreas;
* preparar os dados para treinamento;
* desenvolver um modelo de detecção baseado em YOLO;
* desenvolver um modelo de segmentação de instâncias;
* avaliar quantitativamente os modelos;
* analisar erros de detecção e segmentação;
* realizar inferência em imagens não utilizadas no treinamento;
* realizar inferência em vídeo;
* investigar rastreamento de múltiplos objetos como extensão do sistema.

---

# Dataset

O projeto utiliza o dataset **Drone-Traffic**, disponibilizado através do Roboflow Universe.

O dataset contém:

* **1.332 imagens**;
* **4 classes**;
* anotações para **instance segmentation**;
* imagens de cenas de tráfego capturadas por drones.

### Classes

| ID | Classe  |
| -: | ------- |
|  0 | bicycle |
|  1 | bus     |
|  2 | car     |
|  3 | lorry   |

### Fonte

**Drone-Traffic Dataset — kaggleMTID**

Roboflow Universe:

https://universe.roboflow.com/kagglemtid/drone-traffic

### Licença

CC BY 4.0.

A fonte original e os termos de utilização do dataset serão mantidos na documentação do projeto.

---

# Metodologia

O pipeline experimental será dividido em cinco etapas principais:

```text
Dataset
   │
   ▼
EDA
   │
   ▼
Detection
   │
   ▼
Segmentation
   │
   ▼
Evaluation
   │
   ▼
Video Inference
```

---

## 1. Exploratory Data Analysis

Inicialmente serão investigados:

* quantidade de imagens;
* distribuição das classes;
* quantidade de objetos por imagem;
* resolução das imagens;
* distribuição das bounding boxes;
* distribuição das máscaras;
* possíveis desbalanceamentos;
* imagens representativas;
* possíveis problemas nas anotações.

---

## 2. Detecção

A primeira etapa de Deep Learning será a detecção dos objetos.

O modelo deverá produzir:

```text
classe
confiança
bounding box
```

Exemplo:

```text
Class: car
Confidence: 0.91

Bounding Box:
x1, y1, x2, y2
```

A implementação será realizada utilizando modelos da família YOLO através do framework Ultralytics.

---

## 3. Segmentação

Na segunda tarefa será utilizada segmentação de instâncias.

Para cada objeto detectado, o modelo deverá produzir:

```text
classe
confiança
bounding box
máscara
```

A segmentação permite representar a forma aproximada de cada objeto, indo além da representação retangular utilizada na detecção.

---

# Avaliação

## Detecção

Serão avaliadas:

* Precision;
* Recall;
* mAP@0.5;
* mAP@0.5:0.95;
* matriz de confusão.

## Segmentação

Serão avaliadas:

* IoU;
* métricas de máscara disponibilizadas pelo modelo;
* qualidade visual das máscaras;
* comparação entre previsão e ground truth.

---

# Análise de erros

Será realizada análise qualitativa dos resultados, buscando identificar:

* falsos positivos;
* falsos negativos;
* objetos pequenos;
* objetos parcialmente ocultos;
* sobreposição entre veículos;
* confusão entre classes;
* baixa qualidade de segmentação;
* falhas em regiões densamente povoadas.

A análise qualitativa será utilizada para complementar as métricas quantitativas.

---

# Inferência em vídeo

Após a validação dos modelos, será realizada inferência sobre um vídeo contendo uma cena compatível com o domínio do projeto.

O objetivo é demonstrar o comportamento do modelo fora do conjunto de treinamento.

O vídeo final terá duração mínima de **30 segundos**, conforme os requisitos da atividade.

---

# Tracking

Como extensão do projeto, será investigada a utilização de um algoritmo de rastreamento de múltiplos objetos, como **ByteTrack**.

A ideia é transformar:

```text
Frame 1
CAR
```

em:

```text
Frame 1 → CAR #01
Frame 2 → CAR #01
Frame 3 → CAR #01
Frame 4 → CAR #01
```

O tracking será tratado como uma extensão/bônus e não comprometerá a entrega das tarefas principais.

---

# Arquitetura conceitual

O AeroVision representa o módulo de percepção visual de uma arquitetura maior de UAV autônomo:

```text
                         SISTEMA AUTÔNOMO UAV
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
              ESTADO INERCIAL               PERCEPÇÃO
                    │                           │
                   IMU                        CÂMERA
                    │                           │
                    ▼                           ▼
               GyroAI-SAT                  AeroVision
                                                │
                                      ┌─────────┴─────────┐
                                      │                   │
                                      ▼                   ▼
                                  Detecção          Segmentação
                                      │                   │
                                      └─────────┬─────────┘
                                                ▼
                                             Tracking
                                                │
                                                ▼
                                        Percepção do ambiente
                                                │
                         ┌──────────────────────┘
                         ▼
                   Fusão Sensorial
                         │
                         ▼
                   Estado do UAV
                         │
                         ▼
                  Sistema Autônomo
```

A integração entre AeroVision e GyroAI-SAT **não faz parte do escopo desta entrega**. Ela representa uma possível evolução futura do projeto.

---

# Tecnologias

* Python
* PyTorch
* Ultralytics
* YOLO
* OpenCV
* NumPy
* Pandas
* Matplotlib
* Scikit-learn
* Google Colab

---

# Estrutura do projeto

```text
AeroVision/
│
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
│
├── data/
│   └── README.md
│
├── docs/
│   ├── proposal.md
│   ├── methodology.md
│   └── references.md
│
├── notebooks/
│   ├── 01_dataset_eda.ipynb
│   ├── 02_detection.ipynb
│   ├── 03_segmentation.ipynb
│   ├── 04_evaluation.ipynb
│   └── 05_video_tracking.ipynb
│
├── src/
│   ├── preprocessing/
│   ├── detection/
│   ├── segmentation/
│   ├── tracking/
│   └── evaluation/
│
├── configs/
├── models/
├── results/
└── videos/
```

---

# Status

**Em desenvolvimento**

### Progresso

* [x] Definição do problema
* [x] Definição do domínio
* [x] Seleção do dataset
* [x] Definição das classes
* [x] Definição da arquitetura
* [ ] Download/exportação do dataset
* [ ] EDA
* [ ] Preparação dos dados
* [ ] Treinamento da detecção
* [ ] Treinamento da segmentação
* [ ] Avaliação
* [ ] Análise de erros
* [ ] Inferência em vídeo
* [ ] Tracking
* [ ] Relatório

---

# Contexto acadêmico

Projeto desenvolvido para a disciplina de **Visão Computacional e Reconhecimento de Padrões**.

O projeto também integra uma linha de desenvolvimento pessoal envolvendo:

**Inteligência Artificial + Visão Computacional + Controle + Aeroespacial + Sistemas Autônomos.**

---

# Referências

As fontes utilizadas, artigos, datasets, frameworks e modelos serão documentados em:

```text
docs/references.md
```

Todos os recursos de terceiros utilizados no projeto serão devidamente identificados e citados.
