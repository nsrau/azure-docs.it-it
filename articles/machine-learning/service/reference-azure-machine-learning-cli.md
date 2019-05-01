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
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 2992ec9f43aac9e0d80c5e42873d26ac3a9c3fd1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916984"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Usare l'estensione dell'interfaccia della riga di comando per il servizio Azure Machine Learning

L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce i comandi per usare il servizio Azure Machine Learning dalla riga di comando. Consente di automatizzare i flussi di lavoro di apprendimento automatico. Ad esempio, è possibile eseguire le azioni seguenti:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. È uno strumento complementare che è ottimizzato per gestire attività elevata con parametri in base a se stessi anche all'automazione.

## <a name="prerequisites"></a>Prerequisiti

* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> È possibile trovare il file di esempio è possibile usare con i comandi seguenti permettono [qui](http://aka.ms/azml-deploy-cloud).

Quando richiesto, selezionare `y` per installare l'estensione.

Per verificare che l'estensione sia stata installata, usare il comando seguente per visualizzare un elenco di sottocomandi specifici di ML:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Rimuovere l'estensione

Per rimuovere l'estensione dell'interfaccia della riga di comando, usare il comando seguente:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resource management

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per gestire le risorse usate da Azure Machine Learning.

+ Se non hai già uno, creare un gruppo di risorse:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Creare un'area di lavoro del servizio Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ Collegare una configurazione dell'area di lavoro in una cartella per abilitare la consapevolezza contestuale dell'interfaccia della riga.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Collegare un contenitore blob di Azure come un archivio dati.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

+ Collegare un cluster del servizio contenitore di AZURE come destinazione di calcolo.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

+ Creare una nuova destinazione AMLcompute

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```
    
## <a id="experiments"></a>Eseguire gli esperimenti

+ Collegare una configurazione dell'area di lavoro in una cartella per abilitare la consapevolezza contestuale dell'interfaccia della riga.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

* Avviare un'esecuzione dell'esperimento. Quando si utilizza questo comando, specificare il nome del file runconfig (il testo prima \*. runconfig se si sta esaminando il file system) con il parametro - c.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Visualizzare un elenco degli esperimenti:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling-deployment"></a>Registrazione del modello, la profilatura, distribuzione

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Distribuire il modello al servizio contenitore di AZURE

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
