---
title: Distribuire modelli ml nel servizio app di Azure (anteprima)Deploy ml models to Azure App Service (preview)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per distribuire un modello in un'app Web nel servizio app di Azure.Learn how to use Azure Machine Learning to deploy a model to a Web App in Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282818"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuire un modello di Machine Learning nel servizio app di Azure (anteprima)Deploy a machine learning model to Azure App Service (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello da Azure Machine Learning come app Web nel servizio app di Azure.Learn how to deploy a model from Azure Machine Learning as a web app in Azure App Service.

> [!IMPORTANT]
> Mentre Azure Machine Learning e il servizio app di Azure sono generalmente disponibili, la possibilità di distribuire un modello dal servizio Machine Learning al servizio app è in anteprima.

Con Azure Machine Learning è possibile creare immagini Docker da modelli di apprendimento automatico addestrati. Questa immagine contiene un servizio Web che riceve i dati, li invia al modello e quindi restituisce la risposta. Il servizio app di Azure può essere usato per distribuire l'immagine e offre le funzionalità seguenti:Azure App Service can be used to deploy the image, and provides the following features:

* [Autenticazione](/azure/app-service/configure-authentication-provider-aad) avanzata per una maggiore sicurezza. I metodi di autenticazione includono sia Azure Active Directory che l'autenticazione a più fattori.
* [Scalabilità automatica](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) senza dover ridistribuire.
* [Supporto TLS](/azure/app-service/configure-ssl-certificate-in-code) per le comunicazioni protette tra i client e il servizio.

Per altre informazioni sulle funzionalità fornite dal servizio app di Azure, vedere Panoramica del [servizio app.](/azure/app-service/overview)

> [!IMPORTANT]
> Se è necessaria la possibilità di registrare i dati di punteggio usati con il modello distribuito o i risultati del punteggio, è invece necessario distribuire nel servizio Azure Kubernetes.If you need the ability to log the scoring data used with your deployed model, or the results of scoring, you should instead deploy to Azure Kubernetes Service. Per ulteriori informazioni, consultate Raccogliere dati sui modelli di [produzione.](how-to-enable-data-collection.md)

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

    > [!IMPORTANT]
    > Azure Machine Learning SDK non consente al servizio Web di accedere all'archivio dati o ai set di dati. Se è necessario il modello distribuito per accedere ai dati archiviati all'esterno della distribuzione, ad esempio in un account di archiviazione di Azure, è necessario sviluppare una soluzione di codice personalizzata usando l'SDK pertinente. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python).
    >
    > Un'altra alternativa che può funzionare per lo scenario è [le stime batch](how-to-use-parallel-run-step.md), che forniscono l'accesso ai datastore durante il punteggio.

    Per altre informazioni sugli script di immissione, vedere Distribuire modelli con Azure Machine Learning.For more information on entry scripts, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

* **Dipendenze**, ad esempio script helper o pacchetti Python/Conda necessari per eseguire lo script di ingresso o il modello

Queste entità sono incapsulate in una configurazione di __inferenza__. La configurazione di inferenza fa riferimento allo script di avvio e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con il servizio app di Azure, è necessario usare un oggetto [Ambiente.When](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) creating an inference configuration for use with Azure App Service, you must use an Environment object. Si noti che se si definisce un ambiente personalizzato, è necessario aggiungere impostazioni predefinite di azureml con la versione >1.0.45 come dipendenza pip. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e l'utilizzo con una configurazione di inferenza:The following example demonstrates creating an environment object and using it with an inference configuration:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> Quando si esegue la distribuzione nel servizio app di Azure, non è necessario creare una configurazione di __distribuzione.__

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita nel servizio app di Azure, usare [Model.package.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) Il frammento di codice seguente illustra come compilare una nuova immagine dalla configurazione del modello e dell'inferenza:The following code snippet demonstrates how to build a new image from the model and inference configuration:

> [!NOTE]
> Il frammento di `model` codice presuppone che `inference_config` contiene un modello registrato e che contiene la configurazione per l'ambiente di inferenza. Per altre informazioni, vedere Distribuire modelli con Azure Machine Learning.For more information, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando `show_output=True`è , viene visualizzato l'output del processo di compilazione Docker. Al termine del processo, l'immagine è stata creata nel Registro di sistema del contenitore di Azure per l'area di lavoro. Dopo aver compilato l'immagine, viene visualizzato il percorso nel Registro di sistema del contenitore di Azure.Once the image has been built, the location in your Azure Container Registry is displayed. Il percorso restituito è `<acrinstance>.azurecr.io/package:<imagename>`nel formato . Ad esempio: `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Salvare le informazioni sul percorso, come viene utilizzato durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Registro di sistema del contenitore di Azure che contiene l'immagine. Sostituire `<acrinstance>` con il valore restituito `package.location`in precedenza da:

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

    In questo esempio viene utilizzato`--sku B1`un piano __tariffario Basic__ ( ).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning `--is-linux` usano Linux, pertanto è necessario usare il parametro.

1. Per creare l'app Web, utilizzare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` `<imagename>` e con i `package.location` valori restituiti in precedenza:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Questo comando restituisce informazioni simili al seguente documento JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > A questo punto, l'app Web è stata creata. Tuttavia, poiché non sono state fornite le credenziali al Registro di sistema del contenitore di Azure che contiene l'immagine, l'app Web non è attiva. Nel passaggio successivo vengono fornite le informazioni di autenticazione per il Registro di sistema del contenitore.

1. Per fornire all'app Web le credenziali necessarie per accedere al Registro di sistema del contenitore, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` `<imagename>` e con i `package.location` valori restituiti in precedenza. Sostituire `<username>` `<password>` e con le informazioni di accesso ACR recuperate in precedenza:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

A questo punto, l'app Web inizia a caricare l'immagine.

> [!IMPORTANT]
> Potrebbero essere alcuni minuti prima che l'immagine sia caricata. Per monitorare lo stato di avanzamento, utilizzare il comando seguente:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Una volta che l'immagine è stata caricata e `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`il sito è attivo, il registro visualizza un messaggio che indica .

Una volta distribuita l'immagine, è possibile trovare il nome host utilizzando il comando seguente:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Questo comando restituisce informazioni simili `<app-name>.azurewebsites.net`al seguente nome host - . Utilizzare questo valore come parte __dell'URL__ di base per il servizio.

## <a name="use-the-web-app"></a>Usare l'app Web

Il servizio Web che passa le `{baseurl}/score`richieste al modello si trova in corrispondenza di . Ad esempio: `https://<app-name>.azurewebsites.net/score`. Il codice Python seguente illustra come inviare dati all'URL e visualizzare la risposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare l'app Web nella documentazione del [servizio app su Linux.Learn](/azure/app-service/containers/) to configure your Web App in the App Service on Linux documentation.
* Per altre informazioni sulla scalabilità, vedere [Introduzione alla scalabilità automatica in Azure.Learn](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)more about scaling in Get started with Autoscale in Azure .
* [Usare un certificato TLS/SSL nel servizio app di Azure](/azure/app-service/configure-ssl-certificate-in-code).
* [Configurare l'app del servizio app per l'uso dell'accesso](/azure/app-service/configure-authentication-provider-aad)ad Azure Active Directory.
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
