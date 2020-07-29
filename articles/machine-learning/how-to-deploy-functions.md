---
title: Distribuire modelli ml in app di funzioni di Azure (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per distribuire un modello in un'app funzioni di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python
ms.openlocfilehash: 8d1ea9b0989a71268b98f0b2fd1d95d5671f996b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325798"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuire un modello di Machine Learning in funzioni di Azure (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello da Azure Machine Learning come app per le funzioni in funzioni di Azure.

> [!IMPORTANT]
> Sebbene sia la Azure Machine Learning che funzioni di Azure siano disponibili a livello generale, la possibilità di creare un pacchetto di un modello dal servizio Machine Learning per le funzioni è in anteprima.

Con Azure Machine Learning, è possibile creare immagini Docker da modelli di apprendimento automatico sottoposti a training. Azure Machine Learning dispone ora della funzionalità di anteprima per compilare questi modelli di apprendimento automatico in app per le funzioni, che possono essere [distribuite in funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un modello di apprendimento automatico sottoposto a training registrato nell'area di lavoro. Se non si dispone di un modello, usare l' [esercitazione relativa alla classificazione delle immagini: Train Model](tutorial-train-models-with-aml.md) per eseguire il training e la registrazione di un modello.

    > [!IMPORTANT]
    > I frammenti di codice in questo articolo presuppongono che siano state impostate le variabili seguenti:
    >
    > * `ws`-L'area di lavoro Azure Machine Learning.
    > * `model`: Modello registrato che verrà distribuito.
    > * `inference_config`-Configurazione dell'inferenza per il modello.
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
    > Per impostazione predefinita, quando si esegue il packaging per le funzioni, l'input viene considerato come testo. Se si è interessati a utilizzare i byte non elaborati dell'input (ad esempio per i trigger BLOB), è necessario utilizzare [AMLRequest per accettare dati non elaborati](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).

Per altre informazioni sullo script di immissione, vedere [definire il codice](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#script) di assegnazione dei punteggi

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

Queste entità sono incapsulate in una __configurazione di inferenza__. La configurazione di inferenza fa riferimento allo script di avvio e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con funzioni di Azure, è necessario usare un oggetto [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Si noti che se si definisce un ambiente personalizzato, è necessario aggiungere azureml-defaults con Version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e il relativo utilizzo con una configurazione di inferenza:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
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

Quando `show_output=True` viene visualizzato l'output del processo di compilazione docker. Al termine del processo, l'immagine è stata creata nel Container Registry di Azure per l'area di lavoro. Una volta compilata l'immagine, viene visualizzata la località nel Container Registry di Azure. Il percorso restituito è nel formato `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Il packaging per le funzioni supporta attualmente trigger HTTP, trigger di BLOB e trigger del bus di servizio. Per altre informazioni sui trigger, vedere [binding di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Salvare le informazioni sul percorso, così come vengono usate durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Container Registry di Azure che contiene l'immagine. Sostituire `<myacr>` con il valore restituito in precedenza da `package.location` : 

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
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In questo esempio viene usato un piano tariffario _Basic Linux_ ( `--sku B1` ).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning usano Linux, quindi è necessario usare il `--is-linux` parametro.

1. Creare l'account di archiviazione da usare per l'archiviazione dei processi Web e ottenere la relativa stringa di connessione. Sostituire `<webjobStorage>` con il nome che si vuole usare.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Per creare l'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome che si vuole usare. Sostituire `<acrinstance>` e `<imagename>` con i valori restituiti in `package.location` precedenza. Sostituire `<webjobStorage>` con il nome dell'account di archiviazione nel passaggio precedente:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > A questo punto è stata creata l'app per le funzioni. Tuttavia, poiché non è stata specificata la stringa di connessione per il trigger o le credenziali del BLOB al Container Registry di Azure che contiene l'immagine, l'app per le funzioni non è attiva. Nei passaggi successivi vengono fornite la stringa di connessione e le informazioni di autenticazione per il registro contenitori. 

1. Creare l'account di archiviazione da usare per l'archiviazione del trigger del BLOB e ottenere la relativa stringa di connessione. Sostituire `<triggerStorage>` con il nome che si vuole usare.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Registrare la stringa di connessione da fornire all'app per le funzioni. Verrà usato in un secondo momento per richiedere`<triggerConnectionString>`

1. Creare i contenitori per l'input e l'output nell'account di archiviazione. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Per associare la stringa di connessione del trigger all'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome dell'app per le funzioni. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. È necessario recuperare il tag associato al contenitore creato usando il comando seguente. Sostituire `<username>` con il nome utente restituito in precedenza dal registro contenitori:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Salvare il valore restituito, che verrà usato come `imagetag` nel passaggio successivo.

1. Per fornire all'app per le funzioni le credenziali necessarie per accedere al registro contenitori, usare il comando seguente. Sostituire `<app-name>` con il nome dell'app per le funzioni. Sostituire `<acrinstance>` e `<imagetag>` con i valori della chiamata AZ cli nel passaggio precedente. Sostituire `<username>` e `<password>` con le informazioni di accesso di ACR recuperate in precedenza:

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

## <a name="test-the-deployment"></a>Test della distribuzione

Dopo che l'immagine è stata caricata e l'app è disponibile, seguire questa procedura per attivare l'app:

1. Creare un file di testo contenente i dati previsti dal file score.py. L'esempio seguente funziona con un score.py che prevede una matrice di 10 numeri:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Il formato dei dati dipende da ciò che si aspetta il score.py e il modello.

2. Usare il comando seguente per caricare il file nel contenitore di input nel BLOB di archiviazione dei trigger creato in precedenza. Sostituire `<file>` con il nome del file contenente i dati. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza. In questo esempio, `input` è il nome del contenitore di input creato in precedenza. Se è stato usato un nome diverso, sostituire questo valore:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    L'output di questo comando è simile al codice JSON seguente:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Per visualizzare l'output prodotto dalla funzione, utilizzare il comando seguente per elencare i file di output generati. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza. In questo esempio, `output` è il nome del contenitore di output creato in precedenza. Se è stato usato un nome diverso, sostituire questo valore::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    L'output di questo comando è simile a `sample_input_out.json` .

4. Per scaricare il file ed esaminarne il contenuto, usare il comando seguente. Sostituire `<file>` con il nome file restituito dal comando precedente. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Al termine del comando, aprire il file. Contiene i dati restituiti dal modello.

Per altre informazioni sull'uso dei trigger BLOB, vedere l'articolo [creare una funzione attivata da archiviazione BLOB di Azure](/azure/azure-functions/functions-create-storage-blob-triggered-function) .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare l'app per le funzioni nella documentazione di [funzioni](/azure/azure-functions/functions-create-function-linux-custom-image) .
* Altre informazioni sull'archiviazione BLOB attiva i [binding dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Distribuire il modello nel servizio app Azure](how-to-deploy-app-service.md).
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Riferimento API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
