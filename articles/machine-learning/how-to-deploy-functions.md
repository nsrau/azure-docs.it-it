---
title: Distribuire modelli ml in App funzioni di Azure (anteprima)Deploy ml models to Azure Functions Apps (preview)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per distribuire un modello in un'app Funzioni di Azure.Learn how to use Azure Machine Learning to deploy a model to an Azure Functions App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927449"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuire un modello di Machine Learning in Funzioni di Azure (anteprima)Deploy a machine learning model to Azure Functions (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello da Azure Machine Learning come app per le funzioni in Funzioni di Azure.Learn how to deploy a model from Azure Machine Learning as a function app in Azure Functions.

> [!IMPORTANT]
> Mentre sono generalmente disponibili sia Azure Machine Learning che Funzioni di Azure, la possibilità di creare un pacchetto di un modello dal servizio Machine Learning per Funzioni è in anteprima.

Con Azure Machine Learning è possibile creare immagini Docker da modelli di apprendimento automatico addestrati. Azure Machine Learning include ora la funzionalità di anteprima per compilare questi modelli di Machine Learning in app per le funzioni, che possono essere distribuite in Funzioni di Azure.Azure Machine Learning now has the preview functionality to build these Machine Learning models into function apps, which can be [deployed into Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [Creare un'area di lavoro.](how-to-manage-workspace.md)
* [L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure .
* Un modello di apprendimento automatico con training registrato nell'area di lavoro. Se non si dispone di un modello, usare [l'esercitazione Classificazione immagini: modello di training](tutorial-train-models-with-aml.md) per eseguire il training e registrarne uno.

    > [!IMPORTANT]
    > I frammenti di codice in questo articolo presuppongono che siano state impostate le variabili seguenti:The code snippets in this article assume that you have set the following variables:
    >
    > * `ws`- L'area di lavoro di Azure Machine Learning.- Your Azure Machine Learning workspace.
    > * `model`- Il modello registrato che verrà distribuito.
    > * `inference_config`- La configurazione di inferenza per il modello.
    >
    > Per altre informazioni sull'impostazione di queste variabili, vedere Distribuire modelli con Azure Machine Learning.For more information on setting these variables, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Prima della distribuzione, è necessario definire gli elementi necessari per eseguire il modello come servizio Web. Nell'elenco seguente vengono descritti gli elementi di base necessari per una distribuzione:The following list describes the basic items needed for a deployment:

* Uno __script di immissione__. Questo script accetta le richieste, la richiesta viene valutata utilizzando il modello e restituisce i risultati.

    > [!IMPORTANT]
    > Lo script di immissione è specifico del modello; deve comprendere il formato dei dati della richiesta in ingresso, il formato dei dati previsti dal modello e il formato dei dati restituiti ai client.
    >
    > Se i dati della richiesta sono in un formato non utilizzabile dal modello, lo script può trasformarlo in un formato accettabile. Può anche trasformare la risposta prima di tornare a esso al client.
    >
    > Per impostazione predefinita durante la creazione di pacchetti per le funzioni, l'input viene considerato come testo. Se si è interessati a utilizzare i byte non elaborati dell'input, ad esempio per i trigger BLOB, è necessario usare [AMLRequest per accettare dati non elaborati.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)


* **Dipendenze**, ad esempio script helper o pacchetti Python/Conda necessari per eseguire lo script di ingresso o il modello

Queste entità sono incapsulate in una configurazione di __inferenza__. La configurazione di inferenza fa riferimento allo script di avvio e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con Funzioni di Azure, è necessario usare un oggetto [Environment.When](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) creating an inference configuration for use with Azure Functions, you must use an Environment object. Si noti che se si definisce un ambiente personalizzato, è necessario aggiungere impostazioni predefinite di azureml con la versione >1.0.45 come dipendenza pip. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e l'utilizzo con una configurazione di inferenza:The following example demonstrates creating an environment object and using it with an inference configuration:
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

Per ulteriori informazioni sugli ambienti, vedere Creare e gestire ambienti per la formazione e la [distribuzione.](how-to-use-environments.md)

Per altre informazioni sulla configurazione dell'inferenza, vedere Distribuire modelli con Azure Machine Learning.For more information on inference configuration, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

> [!IMPORTANT]
> Quando si esegue la distribuzione in Funzioni, non è necessario creare una configurazione di __distribuzione.__

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installare il pacchetto di anteprima SDK per il supporto delle funzioniInstall the SDK preview package for functions support

Per creare pacchetti per Funzioni di Azure, è necessario installare il pacchetto di anteprima SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita in Funzioni di Azure, usare [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) o la funzione del pacchetto specifica per il trigger che si desidera usare. Il frammento di codice seguente illustra come creare un nuovo pacchetto con un trigger BLOB dalla configurazione del modello e dell'inferenza:The following code snippet demonstrates how to create a new package with a blob trigger from the model and inference configuration:

> [!NOTE]
> Il frammento di `model` codice presuppone che `inference_config` contiene un modello registrato e che contiene la configurazione per l'ambiente di inferenza. Per altre informazioni, vedere Distribuire modelli con Azure Machine Learning.For more information, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True`è , viene visualizzato l'output del processo di compilazione Docker. Al termine del processo, l'immagine è stata creata nel Registro di sistema del contenitore di Azure per l'area di lavoro. Dopo aver compilato l'immagine, viene visualizzato il percorso nel Registro di sistema del contenitore di Azure.Once the image has been built, the location in your Azure Container Registry is displayed. Il percorso restituito è `<acrinstance>.azurecr.io/package@sha256:<hash>`nel formato .

> [!NOTE]
> La creazione del pacchetto per le funzioni supporta attualmente trigger HTTP, trigger BLOB e trigger del bus di servizio. Per altre informazioni sui trigger, vedere Associazioni di Funzioni di Azure.For more information on triggers, see [Azure Functions bindings.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)

> [!IMPORTANT]
> Salvare le informazioni sul percorso, come viene utilizzato durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Registro di sistema del contenitore di Azure che contiene l'immagine. Sostituire `<myacr>` con il valore `package.location`restituito in precedenza da : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    L'output di questo comando è simile al seguente documento JSON:

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

    Salvare il valore di __username__ e di una delle __password__.

1. Se non si dispone già di un gruppo di risorse o di un piano di servizio app per distribuire il servizio, i comandi seguenti illustrano come creare entrambi:If you do not already have a resource group or app service plan to deploy the service, the following commands demonstrate how to create both:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In questo esempio viene utilizzato`--sku B1`un piano tariffario di base di _Linux_ ( ).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning `--is-linux` usano Linux, pertanto è necessario usare il parametro.

1. Creare l'account di archiviazione da usare per l'archiviazione dei processi Web e ottenere la relativa stringa di connessione. Sostituire `<webjobStorage>` con il nome che si desidera utilizzare.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Per creare l'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` `<imagename>` e con i `package.location` valori restituiti in precedenza. Sostituire `<webjobStorage>` con il nome dell'account di archiviazione del passaggio precedente:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > A questo punto, l'app per le funzioni è stata creata. Tuttavia, poiché non è stata fornita la stringa di connessione per il trigger BLOB o le credenziali al Registro di sistema del contenitore di Azure che contiene l'immagine, l'app per le funzioni non è attiva. Nei passaggi successivi vengono fornite la stringa di connessione e le informazioni di autenticazione per il Registro di sistema del contenitore. 

1. Creare l'account di archiviazione da usare per l'archiviazione dei trigger BLOB e ottenere la relativa stringa di connessione. Sostituire `<triggerStorage>` con il nome che si desidera utilizzare.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Registrare questa stringa di connessione da fornire all'app per le funzioni. Lo useremo più tardi quando chiederemo`<triggerConnectionString>`

1. Creare i contenitori per l'input e l'output nell'account di archiviazione. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza:Replace with the connection string returned earlier:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Per associare la stringa di connessione del trigger all'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome dell'app per le funzioni. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza:Replace with the connection string returned earlier:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Sarà necessario recuperare il tag associato al contenitore creato utilizzando il comando seguente. Sostituire `<username>` con il nome utente restituito in precedenza dal Registro contenitori:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Salvare il valore restituito, verrà utilizzato `imagetag` come nel passaggio successivo.

1. Per fornire all'app per le funzioni le credenziali necessarie per accedere al Registro di sistema del contenitore, usare il comando seguente. Sostituire `<app-name>` con il nome dell'app per le funzioni. Sostituire `<acrinstance>` `<imagetag>` e con i valori della chiamata a interfaccia di comando di A . Sostituire `<username>` `<password>` e con le informazioni di accesso ACR recuperate in precedenza:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Questo comando restituisce informazioni simili al seguente documento JSON:

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
> Potrebbero essere alcuni minuti prima che l'immagine sia caricata. È possibile monitorare lo stato di avanzamento usando il portale di Azure.You can monitor progress using the Azure Portal.

## <a name="test-the-deployment"></a>Test della distribuzione

Dopo aver caricato l'immagine e aver a disposizione l'app, segui questi passaggi per attivare l'app:

1. Creare un file di testo contenente i dati previsti dal file score.py. L'esempio seguente funziona con un score.py che prevede una matrice di 10 numeri:The following example would work with a following that expects an array of 10 numbers:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Il formato dei dati dipende dal tipo di score.py e del modello.

2. Usare il comando seguente per caricare il file nel contenitore di input nel BLOB di archiviazione trigger creato in precedenza. Sostituire `<file>` con il nome del file contenente i dati. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza. In questo `input` esempio, è il nome del contenitore di input creato in precedenza. Se è stato utilizzato un nome diverso, sostituire questo valore:If you used a different name, replace this value:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    L'output di questo comando è simile al seguente JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Per visualizzare l'output prodotto dalla funzione, utilizzare il comando seguente per elencare i file di output generati. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza. In questo `output` esempio, è il nome del contenitore di output creato in precedenza. Se è stato utilizzato un nome diverso, sostituire questo valore:If you used a different name, replace this value::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    L'output di questo `sample_input_out.json`comando è simile a .

4. Per scaricare il file ed esaminarne il contenuto, utilizzare il comando seguente. Sostituire `<file>` con il nome del file restituito dal comando precedente. Sostituire `<triggerConnectionString>` con la stringa di connessione restituita in precedenza:Replace with the connection string returned earlier: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Al termine del comando, aprire il file. Contiene i dati restituiti dal modello.

Per altre informazioni sull'uso dei trigger BLOB, vedere l'articolo [Creare una funzione attivata dall'archiviazione BLOB](/azure/azure-functions/functions-create-storage-blob-triggered-function) di Azure.For more information on using blob triggers, see the Create a function triggered by Azure Blob storage article.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione dell'app Funzioni, vedere la documentazione relativa alle [funzioni.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Altre informazioni sull'archiviazione BLOB attiva le associazioni di archiviazione BLOB di Azure.Learn more about Blob storage triggers [Azure Blob storage bindings](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Distribuire il modello nel](how-to-deploy-app-service.md)servizio app di Azure .
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Riferimento API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
