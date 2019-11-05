---
title: Distribuire modelli di Machine Learning in app Azure Service (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per distribuire un modello in un'app Web nel servizio app Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: b0d7286d96d2fbfa35eb7ce9079413dfd186288c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496959"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuire un modello di machine learning nel servizio app Azure (anteprima)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello da Azure Machine Learning come app Web nel servizio app Azure.

> [!IMPORTANT]
> Sebbene sia Azure Machine Learning che app Azure servizio siano disponibili a livello generale, la possibilità di distribuire un modello dal servizio Machine Learning al servizio app è in anteprima.

Con Azure Machine Learning, è possibile creare immagini Docker da modelli di apprendimento automatico sottoposti a training. Questa immagine contiene un servizio Web che riceve i dati, li invia al modello e quindi restituisce la risposta. App Azure servizio può essere usato per distribuire l'immagine e fornisce le funzionalità seguenti:

* [Autenticazione](/azure/app-service/configure-authentication-provider-aad) avanzata per la sicurezza avanzata. I metodi di autenticazione includono sia Azure Active Directory che l'autenticazione a più fattori.
* [Ridimensionamento](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) automatico senza dover ridistribuire.
* [Supporto SSL](/azure/app-service/configure-ssl-certificate-in-code) per le comunicazioni sicure tra i client e il servizio.

Per altre informazioni sulle funzionalità fornite dal servizio app Azure, vedere [Panoramica del servizio app](/azure/app-service/overview).

> [!IMPORTANT]
> Se è necessario avere la possibilità di registrare i dati di assegnazione dei punteggi usati con il modello distribuito o i risultati dell'assegnazione dei punteggi, è invece necessario eseguire la distribuzione nel servizio Azure Kubernetes. Per altre informazioni, vedere [raccogliere dati nei modelli di produzione](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Prerequisiti

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

    > [!IMPORTANT]
    > Il Azure Machine Learning SDK non fornisce un modo per il servizio Web di accedere all'archivio dati o ai set di dati. Se è necessario che il modello distribuito acceda ai dati archiviati all'esterno della distribuzione, ad esempio in un account di archiviazione di Azure, è necessario sviluppare una soluzione di codice personalizzata usando l'SDK pertinente. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python).
    >
    > Un'altra alternativa che può funzionare per lo scenario è la [stima in batch](how-to-run-batch-predictions.md), che fornisce l'accesso agli archivi dati quando si esegue il punteggio.

    Per ulteriori informazioni sugli script di immissione, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

Queste entità sono incapsulate in una __configurazione di inferenza__. La configurazione dell'inferenza fa riferimento allo script di immissione e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con app Azure Service, è necessario usare un oggetto [Environment](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e il relativo utilizzo con una configurazione di inferenza:
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
> Quando si esegue la distribuzione nel servizio app Azure, non è necessario creare una __configurazione di distribuzione__.

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita nel servizio app Azure, usare [Model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Il frammento di codice seguente illustra come compilare una nuova immagine dalla configurazione del modello e dell'inferenza:

> [!NOTE]
> Il frammento di codice presuppone che `model` contenga un modello registrato e che `inference_config` contenga la configurazione per l'ambiente di inferenza. Per altre informazioni, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando `show_output=True`, viene visualizzato l'output del processo di compilazione docker. Al termine del processo, l'immagine è stata creata nel Container Registry di Azure per l'area di lavoro. Una volta compilata l'immagine, viene visualizzata la località nel Container Registry di Azure. Il percorso restituito è nel formato `<acrinstance>.azurecr.io/package:<imagename>`. Ad esempio, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Salvare le informazioni sul percorso, così come vengono usate durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Container Registry di Azure che contiene l'immagine. Sostituire `<acrinstance>` con il valore e restituito in precedenza da `package.location`: 

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

    In questo esempio viene usato un piano tariffario __Basic__ (`--sku B1`).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning usano Linux, quindi è necessario usare il parametro `--is-linux`.

1. Per creare l'app Web, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` e `<imagename>` con i valori restituiti `package.location` precedente:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Questo comando restituisce informazioni simili al documento JSON seguente:

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
    > A questo punto, l'app Web è stata creata. Tuttavia, poiché non sono state fornite le credenziali per il Container Registry di Azure che contiene l'immagine, l'app Web non è attiva. Nel passaggio successivo vengono fornite le informazioni di autenticazione per il registro contenitori.

1. Per fornire all'app Web le credenziali necessarie per accedere al registro contenitori, usare il comando seguente. Sostituire `<app-name>` con il nome che si desidera utilizzare. Sostituire `<acrinstance>` e `<imagename>` con i valori restituiti `package.location` precedente. Sostituire `<username>` e `<password>` con le informazioni di accesso di ACR recuperate in precedenza:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

A questo punto, l'app Web inizia a caricare l'immagine.

> [!IMPORTANT]
> Potrebbero essere necessari alcuni minuti prima che l'immagine venga caricata. Per monitorare lo stato di avanzamento, usare il comando seguente:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Una volta che l'immagine è stata caricata e il sito è attivo, il log Visualizza un messaggio che indica `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`.

Una volta distribuita l'immagine, è possibile trovare il nome host usando il comando seguente:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Questo comando restituisce informazioni simili al nome host `<app-name>.azurewebsites.net`seguente. Utilizzare questo valore come parte dell' __URL di base__ per il servizio.

## <a name="use-the-web-app"></a>Usare l'app Web

Il servizio Web che passa le richieste al modello si trova in `{baseurl}/score`. Ad esempio, `https://<app-name>.azurewebsites.net/score`. Il codice Python seguente illustra come inviare dati all'URL e visualizzare la risposta:

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

* Informazioni su come configurare l'app Web nel [servizio app](/azure/app-service/containers/) nella documentazione di Linux.
* Per altre informazioni sul ridimensionamento, [vedere Introduzione alla scalabilità automatica in Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Usare un certificato SSL nel servizio app Azure](/azure/app-service/configure-ssl-certificate-in-code).
* [Configurare l'app del servizio app per usare Azure Active Directory l'accesso](/azure/app-service/configure-authentication-provider-aad).
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
