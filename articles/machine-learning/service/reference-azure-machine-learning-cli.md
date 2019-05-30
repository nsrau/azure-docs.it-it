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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 541ffe70ae5198e631568584a58d02ac283e89d3
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298250"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Usare l'estensione dell'interfaccia della riga di comando per il servizio Azure Machine Learning

L'interfaccia della riga di comando di Azure Machine Learning è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), un'interfaccia della riga di comando multipiattaforma per la piattaforma Azure. Questa estensione fornisce comandi per l'uso con il servizio di Azure Machine Learning. Consente di automatizzare le attività di apprendimento automatico. Nell'elenco seguente offre alcune azioni di esempio che è possibile eseguire con l'estensione dell'interfaccia della riga:

+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

L'interfaccia della riga di comando non sostituisce Azure Machine Learning SDK. È uno strumento complementare che è ottimizzato per gestire attività elevata con parametri in base a se stessi anche all'automazione.

## <a name="prerequisites"></a>Prerequisiti

* Per usare l'interfaccia della riga di comando, è necessario avere una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Documentazione di riferimento complete

Trovare il [completo di documenti di riferimento per l'estensione di azure-cli-ml di Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> È possibile trovare il file di esempio è possibile usare con i comandi seguenti permettono [qui](https://aka.ms/azml-deploy-cloud).

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

    Per altre informazioni, vedere [Crea area di lavoro ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Collegare una configurazione dell'area di lavoro in una cartella per abilitare la consapevolezza contestuale dell'interfaccia della riga.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando crea un `.azureml` sottodirectory che contiene file dell'ambiente conda e runconfig di esempio. Contiene inoltre un `config.json` file utilizzato per comunicare con l'area di lavoro di Azure Machine Learning.

    Per altre informazioni, vedere [cartella di az ml collegare](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Collegare un contenitore blob di Azure come un archivio dati.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Per altre informazioni, vedere [az ml datastore collegare-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ Collegare un cluster del servizio contenitore di AZURE come destinazione di calcolo.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Per altre informazioni, vedere [aks collegare computetarget az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Creare una nuova destinazione AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Per altre informazioni, vedere [computetarget az ml creare amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Eseguire gli esperimenti

* Avviare un'esecuzione dell'esperimento. Quando si utilizza questo comando, specificare il nome del file runconfig (il testo prima \*. runconfig se si sta esaminando il file system) con il parametro - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il `az ml folder attach` comando crea un `.azureml` sottodirectory, che contiene due file di esempio runconfig. 
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) salvarlo come file runconfig.
    >
    > Per più file runconfig di esempio, vedere [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Per altre informazioni, vedere [az ml eseguire script di inviare](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Visualizzare un elenco degli esperimenti:

    ```azurecli-interactive
    az ml experiment list
    ```

    Per altre informazioni, vedere [esperimento di ml az elenco](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Registrazione del modello, la profilatura, distribuzione

I comandi seguenti illustrano come registrare un modello con training e quindi distribuirlo come servizio di produzione:

+ Registrare un modello con Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Per altre informazioni, vedere [registrazione di modello ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **FACOLTATIVO** il modello per ottenere i valori ottimali di CPU e memoria per la distribuzione del profilo.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Per altre informazioni, vedere [profilo modello ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuire il modello al servizio contenitore di AZURE
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Di seguito è riportato un esempio `inferenceconfig.json` documento:
    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```
    Di seguito è riportato un esempio di documento 'deploymentconfig.json':
    ```json
    {
    "computeType": "aks",
    "ComputeTarget": "akscomputetarget"
    }
    ```

    Per altre informazioni, vedere [Distribuisci modello ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento per l'estensione di Machine Learning dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Eseguire il training e distribuire modelli di machine learning con le pipeline di Azure](/azure/devops/pipelines/targets/azure-machine-learning)