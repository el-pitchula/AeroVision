# AeroVision

### Sistema de Percepção Visual para Detecção, Segmentação e Rastreamento de Objetos em Imagens Aéreas

> **AeroVision** is a computer vision system designed to provide visual perception capabilities for UAVs through object detection, instance segmentation and multi-object tracking.

---

## Visão geral

O **AeroVision** é um projeto de visão computacional voltado à interpretação de imagens e vídeos obtidos por veículos aéreos não tripulados (UAVs).

O sistema tem como objetivo investigar uma cadeia de percepção visual composta por:

```text
Imagem / Vídeo UAV
        │
        ▼
Pré-processamento
        │
        ▼
Detecção de Objetos
        │
        ▼
Segmentação de Instâncias
        │
        ▼
Rastreamento
        │
        ▼
Informações sobre o Ambiente
```

A proposta é desenvolver um módulo de percepção que possa futuramente ser integrado a outros sistemas embarcados de estimação de estado e controle, contribuindo para uma arquitetura de **UAV autônomo**.

---

## Objetivos

### Objetivo geral

Desenvolver e avaliar um sistema de visão computacional capaz de detectar, segmentar e rastrear objetos presentes em imagens aéreas obtidas por UAVs.

### Objetivos específicos

* realizar análise exploratória de um dataset de imagens aéreas;
* preparar e padronizar os dados para treinamento;
* desenvolver um modelo de detecção de objetos;
* desenvolver um modelo de segmentação de instâncias;
* comparar o desempenho das tarefas de detecção e segmentação;
* avaliar os modelos utilizando métricas quantitativas;
* realizar análise qualitativa de erros;
* testar os modelos em imagens não utilizadas durante o treinamento;
* realizar inferência em vídeo;
* investigar rastreamento de múltiplos objetos;
* estruturar o sistema como um módulo reutilizável de percepção visual para UAVs.

---

## Arquitetura conceitual

O AeroVision foi concebido como um dos módulos de uma arquitetura maior de sistemas autônomos:

```text
                         SISTEMA AUTÔNOMO UAV
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
              ESTADO INERCIAL              PERCEPÇÃO
                    │                           │
                 IMU / INS                  CÂMERA
                    │                           │
                    ▼                           ▼
               GyroAI-SAT                  AeroVision
                    │                           │
                    │                    ┌──────┴──────┐
                    │                    │             │
                    │                 Detecção    Segmentação
                    │                    │             │
                    │                    └──────┬──────┘
                    │                           │
                    │                       Tracking
                    │                           │
                    └──────────────┬────────────┘
                                   ▼
                            FUSÃO SENSORIAL
                                   │
                                   ▼
                             ESTADO DO UAV
                                   │
                                   ▼
                           TOMADA DE DECISÃO
                                   │
                                   ▼
                            CONTROLE AUTÔNOMO
```

A integração com sistemas inerciais e de controle não faz parte do escopo inicial do projeto acadêmico. Ela representa uma possibilidade de evolução futura da pesquisa.

---

## Pipeline de visão computacional

O pipeline experimental será organizado em etapas:

```text
Dataset
   │
   ▼
Exploratory Data Analysis
   │
   ▼
Preprocessing
   │
   ▼
Train / Validation / Test
   │
   ├───────────────┐
   ▼               ▼
Detection      Segmentation
   │               │
   └───────┬───────┘
           ▼
       Evaluation
           │
           ▼
      Error Analysis
           │
           ▼
     Video Inference
           │
           ▼
        Tracking
```

---

## Tecnologias

O projeto será desenvolvido principalmente utilizando:

* Python
* PyTorch
* Ultralytics YOLO
* OpenCV
* NumPy
* Pandas
* Matplotlib
* Scikit-learn
* Google Colab / GPU

A arquitetura final de modelos será definida após a análise do dataset e dos requisitos experimentais.

---

## Dataset

O dataset definitivo ainda será selecionado durante a etapa inicial do projeto.

Os principais critérios de seleção serão:

* imagens obtidas por UAV;
* pelo menos 300 imagens anotadas;
* classes adequadas ao problema;
* disponibilidade de bounding boxes;
* disponibilidade de máscaras para segmentação;
* possibilidade de utilização acadêmica;
* documentação e origem verificáveis;
* divisão adequada entre treinamento, validação e teste.

Entre as fontes investigadas encontram-se datasets de visão aérea como **VisDrone** e **UVSD**.

O VisDrone fornece imagens e vídeos capturados por drones, com anotações para detecção e rastreamento. O dataset possui 10.209 imagens estáticas e 288 vídeos, totalizando 261.908 frames.

O UVSD é um dataset voltado especificamente para detecção e segmentação de veículos em imagens de UAV, contendo 5.874 imagens e 98.600 instâncias com anotações em nível de instância. Seu acesso é destinado a pesquisa e estudo privado mediante solicitação institucional.

O dataset final somente será definido após a verificação de disponibilidade, formato das anotações e condições de uso.

---

## Detecção

A primeira tarefa será a detecção de objetos.

O modelo será treinado para produzir:

```text
classe
confiança
bounding box
```

Exemplo conceitual:

```text
Object #17

Class: car
Confidence: 0.94
BBox: (x1, y1, x2, y2)
```

A avaliação utilizará, entre outras métricas:

* Precision
* Recall
* mAP@0.5
* mAP@0.5:0.95
* matriz de confusão

---

## Segmentação

A segunda etapa será a segmentação de instâncias.

Diferentemente da detecção, que representa o objeto por uma bounding box, a segmentação deverá identificar os pixels pertencentes a cada instância.

Exemplo:

```text
Imagem
   │
   ▼
Objeto detectado
   │
   ▼
Máscara da instância
```

As métricas incluirão IoU e métricas apropriadas ao modelo de segmentação.

---

## Rastreamento

Como extensão do sistema, será investigado o rastreamento de múltiplos objetos em vídeo.

A ideia é associar uma identidade persistente às detecções:

```text
Frame 001       Frame 002       Frame 003

CAR #17   ───►  CAR #17   ───►  CAR #17
CAR #23   ───►  CAR #23   ───►  CAR #23
```

Uma possível abordagem será o **ByteTrack**, dependendo do desempenho e da compatibilidade com o detector selecionado.

---

## Avaliação

A avaliação será dividida em:

### Avaliação quantitativa

* mAP@0.5
* mAP@0.5:0.95
* Precision
* Recall
* IoU
* matriz de confusão

### Avaliação qualitativa

Serão analisados casos de:

* falsos positivos;
* falsos negativos;
* objetos pequenos;
* objetos parcialmente ocultos;
* baixa resolução;
* mudanças de iluminação;
* alta densidade de objetos;
* confusão entre classes;
* falhas de segmentação.

---

## Inferência em vídeo

O sistema será testado em vídeos contendo cenas aéreas.

A etapa deverá demonstrar:

```text
Vídeo
  │
  ▼
Detecção
  │
  ▼
Segmentação
  │
  ▼
Tracking
  │
  ▼
Vídeo anotado
```

O vídeo final deverá representar um cenário próximo da aplicação pretendida.

---

## Estrutura do projeto

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
│   └── 05_video_inference.ipynb
│
├── src/
│   ├── preprocessing/
│   ├── detection/
│   ├── segmentation/
│   ├── tracking/
│   └── evaluation/
│
├── configs/
│
├── models/
│
├── results/
│   ├── detection/
│   ├── segmentation/
│   ├── tracking/
│   ├── confusion_matrix/
│   └── error_analysis/
│
└── videos/
```

---

## Relação com outros projetos

O AeroVision faz parte de uma linha de desenvolvimento voltada a sistemas inteligentes aplicados ao setor aeroespacial.

### GyroAI-SAT

Sistema de inteligência artificial relacionado à estimação e monitoramento de atitude e à detecção de condições associadas ao gimbal lock.

### AeroVision

Módulo de percepção visual responsável por interpretar o ambiente externo através de câmeras.

### Futuro Sistema Autônomo UAV

A integração dos dois conceitos poderá futuramente resultar em uma arquitetura multimodal:

```text
              ┌───────────────┐
              │     UAV       │
              └───────┬───────┘
                      │
            ┌─────────┴─────────┐
            ▼                   ▼
          Sensores            Câmera
            │                   │
            ▼                   ▼
       GyroAI-SAT          AeroVision
            │                   │
            └─────────┬─────────┘
                      ▼
                Sensor Fusion
                      │
                      ▼
                Estado do UAV
                      │
                      ▼
              Autonomous System
```

Essa integração é uma direção futura e não representa o escopo obrigatório da versão acadêmica atual.

---

## Status

🚧 **Em desenvolvimento**

### Fase atual

* [x] Definição conceitual
* [x] Definição do domínio: visão aérea / UAV
* [x] Arquitetura inicial
* [ ] Seleção definitiva do dataset
* [ ] Análise exploratória dos dados
* [ ] Pipeline de preprocessing
* [ ] Treinamento do detector
* [ ] Treinamento do segmentador
* [ ] Avaliação
* [ ] Análise de erros
* [ ] Inferência em vídeo
* [ ] Rastreamento
* [ ] Documentação final

---

## Contexto acadêmico

Projeto desenvolvido no contexto da disciplina de **Visão Computacional e Reconhecimento de Padrões**, como aplicação prática de técnicas de detecção, segmentação e interpretação de imagens.

O projeto também serve como etapa experimental para uma linha de pesquisa pessoal envolvendo:

**Inteligência Artificial + Visão Computacional + Sistemas de Controle + Aeroespacial + Sistemas Autônomos.**

---

## Referências

As referências completas dos datasets, artigos, modelos e ferramentas utilizados serão mantidas em:

```text
docs/references.md
```