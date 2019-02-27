---
title: Estensione dell'interfaccia della riga di comando di Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni sull'estensione dell'interfaccia della riga di comando di Azure Machine Learning per l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di usare le risorse nel cloud di Azure. L'estensione di Machine Learning consente di usare il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c860aca538fcb2fbcff65aebecf062c4c428c84c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415788"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Usare l'estensione dell'interfaccia della riga di comando per il servizio Azure Machine Learning

L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce i comandi per usare il servizio Azure Machine Learning dalla riga di comando. Consente di creare script per automatizzare i flussi di lavoro di apprendimento automatico. È ad esempio possibile creare script che eseguono le azioni seguenti:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. È uno strumento complementare ottimizzato per gestire attività con un numero elevato di parametri, ad esempio:

* Creazione di risorse di calcolo

* Invio di un esperimento con parametri

* Registrazione del modello

* Creazione di immagini

* Distribuzione di servizi

## <a name="prerequisites"></a>Prerequisiti


* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Quando richiesto, selezionare `y` per installare l'estensione.

Per verificare che l'estensione sia stata installata, usare il comando seguente per visualizzare un elenco di sottocomandi specifici di ML:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Per aggiornare l'estensione, è necessario __rimuoverla__ e quindi __installarla__. In questo modo viene installata la versione più recente.

## <a name="remove-the-extension"></a>Rimuovere l'estensione

Per rimuovere l'estensione dell'interfaccia della riga di comando, usare il comando seguente:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resource management

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per gestire le risorse usate da Azure Machine Learning.


+ Creare un'area di lavoro del servizio Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Impostare un'area di lavoro predefinita:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
* Allegare un cluster del servizio Azure Kubernetes

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Sperimentazioni

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per lavorare con gli esperimenti:

* Allegare un progetto (eseguire la configurazione) prima di inviare un esperimento:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Avviare un'esecuzione dell'esperimento. Quando si usa questo comando, specificare il nome del file runconfig che contiene la configurazione di esecuzione. La destinazione di calcolo usa la configurazione di esecuzione per creare l'ambiente di training per il modello. In questo esempio, la configurazione di esecuzione viene caricata dal file `./aml_config/myrunconfig.runconfig`.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Per altre informazioni sul file runconfig, vedere la sezione [RunConfig](#runconfig).

* Visualizzare un elenco degli esperimenti inviati:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Registrazione del modello, creazione dell'immagine e distribuzione

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Creare un'immagine che contiene il modello di Machine Learning e le dipendenze: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Distribuire un'immagine in una destinazione di calcolo:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> File Runconfig

Si usa una configurazione di esecuzione per configurare l'ambiente di training usato per il training del modello. Questa configurazione può essere creata in memoria tramite SDK o può essere caricata da un file runconfig.

Il file runconfig è un documento di testo che descrive la configurazione per l'ambiente di training. Ad esempio, elenca il nome dello script di training e il file che contiene le dipendenze Conda necessarie per il training del modello.

L'interfaccia della riga di comando di Azure Machine Learning crea due file `.runconfig` predefiniti denominati `docker.runconfig` e `local.runconfig` quando si collega un progetto usando il comando `az ml project attach`. 

Se si dispone di codice che crea una configurazione di esecuzione usando la classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py), è possibile usare il metodo `save()` per salvarla in modo permanente in un file `.runconfig`.

Di seguito è riportato un esempio del contenuto di un file `.runconfig`:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.1
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```