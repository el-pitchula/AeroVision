# AeroVision

## Sistema de Percepção Visual para Detecção e Segmentação de Objetos em Imagens Aéreas

[![Python](https://img.shields.io/badge/Python-3.x-blue)]()
[![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-orange)]()
[![YOLO](https://img.shields.io/badge/YOLO-Ultralytics-green)]()
[![License](https://img.shields.io/badge/Code-MIT-lightgrey)]()

O **AeroVision** é um sistema de visão computacional desenvolvido para detecção e segmentação de veículos em imagens aéreas obtidas por drones.

O projeto utiliza modelos YOLO para realizar:

- detecção de objetos;
- segmentação de instâncias;
- avaliação quantitativa;
- análise qualitativa;
- inferência em vídeo.

O sistema foi desenvolvido no contexto da disciplina de **Visão Computacional e Reconhecimento de Padrões** e foi estruturado de forma modular para permitir futuras extensões relacionadas à percepção autônoma de UAVs.

---

## Objetivo

O objetivo do projeto é investigar a aplicação de modelos modernos de visão computacional na percepção de veículos a partir de imagens aéreas.

A solução foi desenvolvida utilizando o dataset **Drone-Traffic**, contendo quatro categorias:

- `bicycle`
- `bus`
- `car`
- `lorry`

---

## Arquitetura conceitual

O AeroVision constitui o módulo visual de uma arquitetura maior de sistema autônomo de UAV planejada para trabalhos futuros:

```text
             SISTEMA AUTÔNOMO UAV
                      │
          ┌───────────┴───────────┐
          ↓                       ↓
     ESTADO INERCIAL          PERCEPÇÃO
          │                       │
         IMU                    CÂMERA
          │                       │
     GyroAI-SAT               AeroVision
                                  │
                     ┌────────────┼────────────┐
                     ↓            ↓            ↓
                 Detecção    Segmentação   Tracking
                     └────────────┬────────────┘
                                  ↓
                           FUSÃO SENSORIAL
                                  ↓
                            ESTADO DO UAV
                                  ↓
                         TOMADA DE DECISÃO
                                  ↓
                         CONTROLE AUTÔNOMO
````

A integração entre percepção visual e sensores inerciais não faz parte da avaliação atual do AeroVision e representa uma direção de desenvolvimento futuro.

---

# Dataset

Foi utilizado o dataset **Drone-Traffic**, disponibilizado pelo Roboflow Universe.

**Fonte:**
[https://universe.roboflow.com/kagglemtid/drone-traffic](https://universe.roboflow.com/kagglemtid/drone-traffic)

**Licença:** CC BY 4.0

A versão utilizada no projeto contém:

| Divisão   |   Imagens |  Anotações |
| --------- | --------: | ---------: |
| Treino    |       950 |     14.229 |
| Validação |       251 |      3.839 |
| Teste     |       118 |      1.697 |
| **Total** | **1.319** | **19.765** |

Todas as imagens possuem informações de segmentação.

---

# Modelos

## Detecção

Para detecção de objetos foi utilizado o **YOLO11n** através da biblioteca Ultralytics.

Configuração final:

```text
Modelo: YOLO11n
Imagem: 416 × 416
Batch: 4
Épocas adicionais: 30
GPU: NVIDIA GTX 1050 Ti
```

O modelo final de detecção foi obtido através de uma etapa adicional de treinamento a partir de um checkpoint produzido em experimento anterior.

---

## Segmentação

Para segmentação de instâncias foi utilizado o **YOLO11n-seg**.

Configuração final:

```text
Modelo: YOLO11n-seg
Imagem: 640 × 640
Batch: 4
Épocas: 50
GPU: NVIDIA GTX 1050 Ti
```

O experimento final encontra-se em:

```text
results/segmentation/experiment_640/
```

---

# Resultados

A avaliação final foi realizada sobre o conjunto de teste.

## Detecção

| Métrica      | Resultado |
| ------------ | --------: |
| Precision    |    0,9265 |
| Recall       |    0,9143 |
| mAP@0.5      |    0,9472 |
| mAP@0.5:0.95 |    0,7599 |
| IoU médio*   |    0,9082 |

* IoU médio calculado sobre as instâncias pareadas entre predições e ground truth.

---

## Segmentação

### Bounding boxes

| Métrica      | Resultado |
| ------------ | --------: |
| Precision    |    0,9913 |
| Recall       |    0,9623 |
| mAP@0.5      |    0,9880 |
| mAP@0.5:0.95 |    0,8672 |

### Máscaras

| Métrica      | Resultado |
| ------------ | --------: |
| Precision    |    0,9861 |
| Recall       |    0,9573 |
| mAP@0.5      |    0,9813 |
| mAP@0.5:0.95 |    0,7250 |
| IoU médio*   |    0,8611 |

* IoU médio calculado sobre as instâncias de máscara pareadas.

---

# IoU por classe

## Detecção

| Classe    |  IoU médio |
| --------- | ---------: |
| bicycle   |     0,7249 |
| bus       |     0,9630 |
| car       |     0,9107 |
| lorry     |     0,9328 |
| **Média** | **0,9082** |

## Segmentação

| Classe    | IoU médio das máscaras |
| --------- | ---------------------: |
| bicycle   |                 0,6822 |
| bus       |                 0,9222 |
| car       |                 0,8592 |
| lorry     |                 0,8955 |
| **Média** |             **0,8611** |

A classe `bicycle` apresentou os menores valores de IoU, principalmente devido à menor dimensão de algumas instâncias nas imagens aéreas.

---

# Inferência em vídeo

O sistema também foi avaliado em uma sequência de vídeo de tráfego aéreo.

Características do vídeo original:

```text
Frames:       726
Duração:      aproximadamente 30,28 s
FPS:          23,98
Resolução:    1920 × 1080
```

O vídeo original está disponível em:

```text
videos/traffic_drone.mp4
```

As versões processadas estão organizadas em:

```text
results/video/
```

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
│   ├── references.md
│   └── report.pdf
│
├── notebooks/
│   ├── 01_dataset_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_detection.ipynb
│   ├── 04_segmentation.ipynb
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
│
├── models/
│   └── README.md
│
├── results/
│   ├── detection/
│   ├── segmentation/
│   ├── evaluation/
│   └── tracking/
│
└── videos/
    └── README.md
```

---

# Avaliação

A avaliação final utiliza:

* Precision;
* Recall;
* mAP@0.5;
* mAP@0.5:0.95;
* IoU;
* matrizes de confusão;
* análise qualitativa de erros;
* inferência em vídeo.

Os scripts de avaliação encontram-se em:

```text
src/evaluation/
```

Os resultados gerados encontram-se em:

```text
results/evaluation/
```

---

# Documentação

A documentação detalhada está disponível em:

- [`docs/methodology.md`](docs/methodology.md) — metodologia completa;
- [`docs/references.md`](docs/references.md) — referências utilizadas.

O relatório acadêmico final será disponibilizado em:

```text
docs/report.pdf
```

---

# Instalação

Clone o repositório:

```bash
git clone https://github.com/el-pitchula/AeroVision.git
cd AeroVision
```

Crie um ambiente virtual:

```bash
python -m venv .venv
```

Ative o ambiente virtual no Windows:

```bash
.venv\Scripts\activate
```

Instale as dependências:

```bash
pip install -r requirements.txt
```

---

# Execução

Os notebooks fornecem o fluxo de experimentação do projeto:

```text
01_dataset_eda.ipynb
02_preprocessing.ipynb
03_detection.ipynb
04_segmentation.ipynb
05_video_tracking.ipynb
```

A estrutura também permite executar os módulos individualmente através dos diretórios em `src/`.

---

# Tecnologias

* Python
* PyTorch
* Ultralytics YOLO
* OpenCV
* NumPy
* Pandas
* Matplotlib
* Scikit-learn
* CUDA

---

# Licença e atribuição

O código deste projeto é disponibilizado para fins acadêmicos e de pesquisa.

O dataset utilizado é de terceiros e está sujeito à sua própria licença:

**Drone-Traffic — CC BY 4.0**

Fonte:

[https://universe.roboflow.com/kagglemtid/drone-traffic](https://universe.roboflow.com/kagglemtid/drone-traffic)

---

# Trabalhos futuros

Entre as principais extensões planejadas estão:

* tracking de veículos;
* avaliação em diferentes condições ambientais;
* técnicas específicas para objetos pequenos;
* ampliação do dataset;
* fusão de dados visuais e inerciais;
* integração com o projeto GyroAI-SAT;
* desenvolvimento de percepção multimodal;
* tomada de decisão autônoma;
* aplicação do AeroVision como componente de um sistema autônomo de UAV.
