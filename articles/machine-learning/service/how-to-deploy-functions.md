---
title: Distribuire modelli ml in app di funzioni di Azure (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per distribuire un modello in un'app funzioni di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 9fba3221656405f2bf2b1654b43d687f1915cca6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74542394"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuire un modello di Machine Learning in funzioni di Azure (anteprima)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello da Azure Machine Learning come app per le funzioni in funzioni di Azure.

> [!IMPORTANT]
> Sebbene sia la Azure Machine Learning che funzioni di Azure siano disponibili a livello generale, la possibilità di creare un pacchetto di un modello dal servizio Machine Learning per le funzioni è in anteprima.

Con Azure Machine Learning, è possibile creare immagini Docker da modelli di apprendimento automatico sottoposti a training. Azure Machine Learning dispone ora della funzionalità di anteprima per compilare questi modelli di apprendimento automatico in app per le funzioni, che possono essere [distribuite in funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un modello di apprendimento automatico sottoposto a training registrato nell'area di lavoro. Se non si dispone di un modello, usare l' [esercitazione relativa alla classificazione delle immagini: Train Model](tutorial-train-models-with-aml.md) per eseguire il training e la registrazione di un modello.

    > [!IMPORTANT]
    > I frammenti di codice in questo articolo presuppongono che siano state impostate le variabili seguenti:
    >
    > * `ws`: area di lavoro del Azure Machine Learning.
    > * `model`: il modello registrato che verrà distribuito.
    > * `inference_config`: la configurazione dell'inferenza per il modello.
    >
    > Per altre informazioni sull'impostazione di queste variabili, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Prima di distribuire, è necessario definire gli elementi necessari per eseguire il modello come servizio Web. Nell'elenco seguente vengono descritti gli elementi di base necessari per una distribuzione:

* Uno __script di immissione__. Questo script accetta richieste, assegna punteggi alla richiesta utilizzando il modello e restituisce i risultati.

    > [!IMPORTANT]
    > Lo script di immissione è specifico del modello. deve comprendere il formato dei dati della richiesta in ingresso, il formato dei dati previsti dal modello e il formato dei dati restituiti ai client.
    >
    > Se i dati della richiesta sono in un formato non utilizzabile dal modello, lo script può trasformarlo in un formato accettabile. Può anche trasformare la risposta prima di restituirla al client.
    >
    > Per impostazione predefinita, quando si esegue il packaging per le funzioni, l'input viene considerato come testo. Se si è interessati a utilizzare i byte non elaborati dell'input (ad esempio per i trigger BLOB), è necessario utilizzare [AMLRequest per accettare dati non elaborati](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

Queste entità sono incapsulate in una __configurazione di inferenza__. La configurazione dell'inferenza fa riferimento allo script di immissione e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con funzioni di Azure, è necessario usare un oggetto [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e il relativo utilizzo con una configurazione di inferenza:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).

Per ulteriori informazioni sulla configurazione dell'inferenza, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Quando si esegue la distribuzione in funzioni, non è necessario creare una __configurazione di distribuzione__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installare il pacchetto di anteprima SDK per il supporto delle funzioni

Per compilare pacchetti per funzioni di Azure, è necessario installare il pacchetto di anteprima dell'SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita in funzioni di Azure, usare [azureml. contrib. Functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) o la funzione specifica del pacchetto per il trigger che si vuole usare. Il frammento di codice seguente illustra come creare un nuovo pacchetto con un trigger di BLOB dal modello e dalla configurazione dell'inferenza:

> [!NOTE]
> Il frammento di codice presuppone che `model` contenga un modello registrato e che `inference_config` contenga la configurazione per l'ambiente di inferenza. Per altre informazioni, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True`, viene visualizzato l'output del processo di compilazione docker. Al termine del processo, l'immagine è stata creata nel Container Registry di Azure per l'area di lavoro. Una volta compilata l'immagine, viene visualizzata la località nel Container Registry di Azure. Il percorso restituito è nel formato `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Il packaging per le funzioni supporta attualmente trigger HTTP, trigger di BLOB e trigger del bus di servizio. Per altre informazioni sui trigger, vedere [binding di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Salvare le informazioni sul percorso, così come vengono usate durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Container Registry di Azure che contiene l'immagine. Sostituire `<acrinstance>` con il valore restituito in precedenza da `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    L'output di questo comando è simile al documento JSON seguente:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salvare il valore per __username__ e una delle __password__.

1. Se non si dispone già di un gruppo di risorse o di un piano di servizio app per distribuire il servizio, i comandi seguenti illustrano come creare entrambi:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    In questo esempio viene usato un piano tariffario _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning usano Linux, quindi è necessario usare il parametro `--is-linux`.

1. Per creare l'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` e `<imagename>` con i valori restituiti `package.location` precedente:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > A questo punto è stata creata l'app per le funzioni. Tuttavia, poiché non è stata specificata la stringa di connessione per il trigger o le credenziali del BLOB al Container Registry di Azure che contiene l'immagine, l'app per le funzioni non è attiva. Nei passaggi successivi vengono fornite la stringa di connessione e le informazioni di autenticazione per il registro contenitori. 

1. Creare l'account di archiviazione da usare come trigger e ottenere la stringa di connessione.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Registrare la stringa di connessione da fornire all'app per le funzioni. Verrà usato in un secondo momento per richiedere `<triggerConnectionString>`

1. Creare i contenitori per l'input e l'output nell'account di archiviazione. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. È necessario recuperare il tag associato al contenitore creato usando il comando seguente:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Il tag più recente illustrato sarà `imagetag` riportato di seguito.

1. Per fornire all'app per le funzioni le credenziali necessarie per accedere al registro contenitori, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` e `<imagetag>` con i valori della chiamata AZ CLI nel passaggio precedente. Sostituire `<username>` e `<password>` con le informazioni di accesso di ACR recuperate in precedenza:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Questo comando restituisce informazioni simili al documento JSON seguente:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

A questo punto, l'app per le funzioni inizia a caricare l'immagine.

> [!IMPORTANT]
> Potrebbero essere necessari alcuni minuti prima che l'immagine venga caricata. È possibile monitorare lo stato di avanzamento tramite il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare l'app per le funzioni nella documentazione di [funzioni](https://docs.microsoft.com/azure/azure-functions/functions-create-function-linux-custom-imag) .
* Altre informazioni sull'archiviazione BLOB attiva i [binding dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Distribuire il modello nel servizio app Azure](how-to-deploy-app-service.md).
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)