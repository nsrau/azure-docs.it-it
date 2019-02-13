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
ms.openlocfilehash: 65936348dcb40c6ceb71ebf735da8bb2120af654
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694517"
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

* Avviare un'esecuzione dell'esperimento. Quando si usa questo comando, specificare il nome del file `.runconfig` che contiene la configurazione di esecuzione. La destinazione di calcolo usa la configurazione di esecuzione per creare l'ambiente di training per il modello. In questo esempio, la configurazione di esecuzione viene caricata dal file `./aml_config/myrunconfig.runconfig`.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Vengono creati file `.runconfig` predefiniti denominati `docker.runconfig` e `local.runconfig` quando si collega un progetto usando il comando `az ml project attach`. Potrebbe essere necessario modificarli prima di usarli per eseguire il training di un modello. 

    È anche possibile creare una configurazione di esecuzione a livello di codice usando la classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py). Dopo averla creata, è possibile usare il metodo `save()` per creare il file `.runconfig`.

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
