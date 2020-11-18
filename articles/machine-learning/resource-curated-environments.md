---
title: Ambienti dedicati
titleSuffix: Azure Machine Learning
description: Informazioni sugli ambienti Azure Machine Learning curati, un set di ambienti preconfigurati che consentono di ridurre i tempi di preparazione dell'esperimento e della distribuzione.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701399"
---
# <a name="azure-machine-learning-curated-environments"></a>Ambienti Azure Machine Learning curati

In questo articolo vengono elencati gli ambienti curati in Azure Machine Learning e i pacchetti e i canali pre-installati. Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Sono supportati da immagini Docker memorizzate nella cache, riducendo il costo di preparazione dell'esecuzione e consentendo tempi di distribuzione più rapidi. Usare questi ambienti per iniziare rapidamente a usare diversi framework di machine learning.

> [!NOTE]
> Questo elenco viene aggiornato a partire dal 2020 settembre. Usare Python SDK per ottenere l'elenco più aggiornato. Per ulteriori informazioni, vedere l' [articolo](./how-to-use-environments.md#use-a-curated-environment)relativo agli ambienti.

## <a name="azureml-automl"></a>AzureML-AutoML

**Canali del pacchetto:**

* Anaconda
* conda-Forge
* pytorch

**Pacchetti conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* Vacanze
* setuptools-git
* psutil

**Pacchetti PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-valori predefiniti
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferenza-schema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Canali del pacchetto:**

* Anaconda
* conda-Forge
* pytorch

**Pacchetti conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* Vacanze
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacchetti PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-valori predefiniti
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferenza-schema
* pytorch-trasformatori
* Spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Canali del pacchetto:**

* Anaconda
* conda-Forge
* pytorch

**Pacchetti conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* Vacanze
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacchetti PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-valori predefiniti
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferenza-schema
* horovod
* pytorch-trasformatori
* Spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-DataSet-Runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-valori predefiniti
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-DNN-Vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Canali del pacchetto:**

* Anaconda
* conda-Forge
* pytorch

**Pacchetti conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* Vacanze
* setuptools-git
* psutil

**Pacchetti PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-valori predefiniti
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferenza-schema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* Chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* Chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Canali del pacchetto:**

* conda-Forge
* pytorch
* valori predefiniti

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* adlfs
* azureml-core
* azureml-DataSet-Runtime
* dask [completa]
* dask-ml [completa]
* distribuite
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python
* matplotlib

**Pacchetti PIP:**

* azureml-valori predefiniti
* adlfs
* azureml-core
* dask [completa]
* dask-ml [completa]
* distribuite
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-iperpropulsore-ForecastDNN

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-valori predefiniti
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-DNN-Forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* torcia
* torchvision
* MKL
* horovod
* tensorboard
* futuro

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-0.20.3

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2-CPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2-GPU

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Canali del pacchetto:**

* Anaconda
* conda-Forge

**Pacchetti conda:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-telemetry
* azureml-Train-restclients-iperguida
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-Training
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-Pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Canali del pacchetto:**

* conda-Forge

**Pacchetti conda:**

* python

**Pacchetti PIP:**

* azureml-core
* azureml-valori predefiniti
* azureml-DataSet-Runtime [fuse, Pandas]
