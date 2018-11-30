---
title: Come usare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning
description: Informazioni sull'estensione dell'interfaccia della riga di comando di Azure Machine Learning per l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di usare le risorse nel cloud di Azure. L'estensione di Machine Learning consente di usare il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 13d09471191deed670db97a9f18e15bc9577dd1a
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713419"
---
# <a name="use-the-azure-machine-learning-cli-extension"></a>Usare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning

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

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!NOTE]
> Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLfree) prima di iniziare.

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
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

+ Creare una macchina virtuale data science. È anche possibile creare cluster BatchAI per il training distribuito o cluster AKS per la distribuzione.


  ```azurecli-interactive
  az ml computetarget setup dsvm -n mydsvm
  ```

## <a name="experiments"></a>Sperimentazioni

I comandi seguenti illustrano come usare l'interfaccia della riga di comando per lavorare con gli esperimenti:

* Allegare un progetto (eseguire la configurazione) prima di inviare un esperimento:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Avviare un'esecuzione dell'esperimento. Quando si usa questo comando, specificare una destinazione di calcolo. In questo esempio `local` usa il computer locale per eseguire il training del modello con lo script `train.py`:

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

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
