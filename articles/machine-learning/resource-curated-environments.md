---
title: Ambienti curati
titleSuffix: Azure Machine Learning
description: Raccolta di ambienti curati disponibili in Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012213"
---
# <a name="azure-machine-learning-curated-environments"></a>Ambienti Azure Machine Learning curati

In questo articolo vengono elencati gli ambienti curati in Azure Machine Learning e i pacchetti e i canali pre-installati. Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Sono supportati da immagini Docker memorizzate nella cache, riducendo il costo di preparazione dell'esecuzione e consentendo tempi di distribuzione più rapidi. Usare questi ambienti per iniziare rapidamente a usare diversi framework di machine learning.

> [!NOTE]
> Questo elenco viene aggiornato a partire dal 2020 giugno. Usare Python SDK per ottenere l'elenco più aggiornato. Per ulteriori informazioni, vedere l' [articolo](./how-to-use-environments.md#use-a-curated-environment)relativo agli ambienti.

## <a name="azure-automl"></a>Azure AutoML

- [AutoML AzureML](#azureml-automl)
- [GPU AzureML AutoML](#azureml-automl-gpu)
- [AzureML AutoML DNN](#azureml-automl-dnn)
- [GPU AzureML AutoML DNN](#azureml-automl-dnn-gpu)
- [GPU di Azure AutoML DNN Vision](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AutoML AzureML

canali dei pacchetti:
- Anaconda
- conda-Forge
- pytorch

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-explain-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferenza-schema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- py-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>GPU AzureML AutoML

canali dei pacchetti:
- Anaconda
- conda-Forge
- pytorch

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-explain-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferenza-schema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- fbprophet = = 0,5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AzureML AutoML DNN

canali dei pacchetti:
- Anaconda
- conda-Forge
- pytorch

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-explain-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferenza-schema
  - pytorch-transformatori = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- py-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>GPU AzureML AutoML DNN

canali dei pacchetti:
- Anaconda
- conda-Forge
- pytorch

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-explain-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferenza-schema
  - horovod = = 0.19.4
  - pytorch-transformatori = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- fbprophet = = 0,5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>AzureML AutoML DNN Vision GPU

dipendenze
- Python = 3.7
- PIP
  - azureml-automl-Core = = 1.8.0
  - azureml-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-explain-Model = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-Train-automl = = 1.8.0
  - azureml-contrib-DataSet = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-pipeline-passaggi = = 1.8.0
  - azureml-pipeline = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-contrib-automl-DNN-Vision = = 1.8.0

## <a name="azure-ml-designer"></a>Progettazione Azure ML

- [AzureML designer](#azureml-designer)
- [CV di AzureML designer](#azureml-designer-cv)
- [Trasformazione CV di AzureML designer](#azureml-designer-cv-transform)
- [IO AzureML designer](#azureml-designer-io)
- [AzureML designer NLP](#azureml-designer-nlp)
- [AzureML designer PyTorch](#azureml-designer-pytorch)
- [AzureML designer PyTorch Train](#azureml-designer-pytorch-train)
- [AzureML Designer R](#azureml-designer-r)
- [AzureML designer Recommender](#azureml-designer-recommender)
- [Punteggio di AzureML designer](#azureml-designer-score)
- [Trasformazione AzureML designer](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML designer

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.8 tramite
- Scikit-Surprise = 1.0.6
- PIP
  - azureml-Designer-Classic-Modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>CV di AzureML designer

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-CV-Modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>Trasformazione CV di AzureML designer

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-CV-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>IO AzureML designer

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-dataprep>= 1,6
  - azureml-designer-dataio-Modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>AzureML designer NLP

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-Designer-Classic-Modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>AzureML designer PyTorch

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-pytorch-Modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>AzureML designer PyTorch Train

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-pytorch-Modules = = 0.0.8

### <a name="azureml-designer-r"></a>AzureML Designer R

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.8 tramite
- r-accento circonflesso = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r-Forecast = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- matrice r = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nLME = 3.1
- r-nnet = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticolare = 1.12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-stringr = 1.4.0
- r-tidyverse = 1.2.1
- r-Timeout = 3043.102
- r-Tseries = 0.10
- r = 3.5.1
- PIP
  - azureml-Designer-Classic-Modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>AzureML designer Recommender

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-Recommender-Modules = = 0.0.5

### <a name="azureml-designer-score"></a>Punteggio di AzureML designer

canali dei pacchetti:
- valori predefiniti

dipendenze
- conda
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-Score-Modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Trasformazione AzureML designer

canali dei pacchetti:
- valori predefiniti

dipendenze
- Python = 3.6.8 tramite
- PIP
  - azureml-designer-DataTransform-Modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>ForecastDNN iperAzureMLa

dipendenze
- Python = 3.7
- PIP
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-contrib-automl-DNN-Forecasting = = 1.8.0

## <a name="azureml-minimal"></a>AzureML minimo

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0

## <a name="azureml-sidecar"></a>Sidecar AzureML

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2

## <a name="azureml-tutorial"></a>Esercitazione su AzureML

canali dei pacchetti:
- Anaconda
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-Widget = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-pipeline-passaggi = = 1.8.0
  - azureml-opendatasets = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1  
  - azureml-Train-automl = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-interpreta = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - sklearn-Pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML VowpalWabbit 8.8.0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-dataprep [fuse, Pandas]

## <a name="dask"></a>Dask

- [CPU Dask AzureML](#azureml-dask-cpu)
- [GPU AzureML Dask](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>CPU Dask AzureML

canali dei pacchetti:
- conda-Forge
- pytorch
- valori predefiniti

dipendenze
- Python = 3.6.9
- PIP
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [completa]
  - dask-ml [completa]
  - distribuite
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow

### <a name="azureml-dask-gpu"></a>GPU AzureML Dask

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.9
- PIP
  - azureml-defaults = = 1.8.0
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [completa]
  - dask-ml [completa]
  - distribuite
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [AzureML Chainer 5.1.0 CPU](#azureml-chainer-510-cpu)
- [GPU AzureML Chainer 5.1.0](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>AzureML Chainer 5.1.0 CPU

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainer = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>GPU AzureML Chainer 5.1.0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainer = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [CPU AzureML PyTorch 1,0](#azureml-pytorch-10-cpu)
- [GPU AzureML PyTorch 1,0](#azureml-pytorch-10-gpu)
- [CPU AzureML PyTorch 1,1](#azureml-pytorch-11-cpu)
- [GPU AzureML PyTorch 1,1](#azureml-pytorch-11-gpu)
- [CPU AzureML PyTorch 1,2](#azureml-pytorch-12-cpu)
- [GPU AzureML PyTorch 1,2](#azureml-pytorch-12-gpu)
- [CPU AzureML PyTorch 1,3](#azureml-pytorch-13-cpu)
- [GPU AzureML PyTorch 1,3](#azureml-pytorch-13-gpu)
- [CPU AzureML PyTorch 1,4](#azureml-pytorch-14-cpu)
- [GPU AzureML PyTorch 1,4](#azureml-pytorch-14-gpu)
- [CPU AzureML PyTorch 1,5](#azureml-pytorch-15-cpu)
- [GPU AzureML PyTorch 1,5](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>CPU AzureML PyTorch 1,0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>GPU AzureML PyTorch 1,0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch 1,1 CPU

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>GPU AzureML PyTorch 1,1

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>CPU AzureML PyTorch 1,2

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>GPU AzureML PyTorch 1,2

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>CPU AzureML PyTorch 1,3

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>GPU AzureML PyTorch 1,3

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>CPU AzureML PyTorch 1,4

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>GPU AzureML PyTorch 1,4

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>CPU AzureML PyTorch 1,5

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>GPU AzureML PyTorch 1,5

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - torcia = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

## <a name="scikit-learn"></a>Scikit informazioni

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit-learn 0.20.3

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - scikit-learn==0.20.3
  - SciPy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [CPU Azure ML TensorFlow 1,10](#azureml-tensorflow-110-cpu)
- [GPU di Azure ML TensorFlow 1,10](#azureml-tensorflow-110-gpu)
- [CPU Azure ML TensorFlow 1,12](#azureml-tensorflow-112-cpu)
- [GPU di Azure ML TensorFlow 1,12](#azureml-tensorflow-112-gpu)
- [CPU Azure ML TensorFlow 1,13](#azureml-tensorflow-113-cpu)
- [GPU di Azure ML TensorFlow 1,13](#azureml-tensorflow-113-gpu)
- [CPU Azure ML TensorFlow 2,0](#azureml-tensorflow-20-cpu)
- [GPU di Azure ML TensorFlow 2,0](#azureml-tensorflow-20-gpu)
- [CPU Azure ML TensorFlow 2,1](#azureml-tensorflow-21-cpu)
- [GPU di Azure ML TensorFlow 2,1](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>CPU AzureML TensorFlow 1,10

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>GPU AzureML TensorFlow 1,10

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>CPU AzureML TensorFlow 1,12

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>GPU AzureML TensorFlow 1,12

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow 1,13 CPU

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>GPU AzureML TensorFlow 1,13

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>CPU AzureML TensorFlow 2,0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>GPU AzureML TensorFlow 2,0

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>CPU AzureML TensorFlow 2,1

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>GPU AzureML TensorFlow 2,1

canali dei pacchetti:
- conda-Forge

dipendenze
- Python = 3.6.2
- PIP
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-iperpropulsore = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 2.1.0
  - horovod = = 0.19.1

