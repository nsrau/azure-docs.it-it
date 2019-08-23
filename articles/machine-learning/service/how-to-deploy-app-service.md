---
title: Distribuire modelli di Machine Learning in app Azure Service (anteprima)
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il servizio Azure Machine Learning per distribuire un modello in un'app Web nel servizio app Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897434"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuire un modello di machine learning nel servizio app Azure (anteprima)

Informazioni su come distribuire un modello dal servizio Azure Machine Learning come app Web nel servizio app Azure.

> [!IMPORTANT]
> Sebbene sia il servizio Azure Machine Learning sia il servizio app Azure sono disponibili a livello generale, la possibilità di distribuire un modello dal servizio Machine Learning al servizio app è in anteprima.

Con Azure Machine Learning servizio è possibile creare immagini Docker da modelli di apprendimento automatico sottoposti a training. Questa immagine contiene un servizio Web che riceve i dati, li invia al modello e quindi restituisce la risposta. App Azure servizio può essere usato per distribuire l'immagine e fornisce le funzionalità seguenti:

* [Autenticazione](/azure/app-service/configure-authentication-provider-aad) avanzata per la sicurezza avanzata. I metodi di autenticazione includono sia Azure Active Directory che l'autenticazione a più fattori.
* [Ridimensionamento](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) automatico senza dover ridistribuire.
* [Supporto SSL](/azure/app-service/app-service-web-ssl-cert-load) per le comunicazioni sicure tra i client e il servizio.

Per altre informazioni sulle funzionalità fornite dal servizio app Azure, vedere [Panoramica del servizio app](/azure/app-service/overview).

> [!IMPORTANT]
> Se è necessario avere la possibilità di registrare i dati di assegnazione dei punteggi usati con il modello distribuito o i risultati dell'assegnazione dei punteggi, è invece necessario eseguire la distribuzione nel servizio Azure Kubernetes. Per altre informazioni, vedere [raccogliere dati nei modelli di produzione](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .
* Un modello di apprendimento automatico sottoposto a training registrato nell'area di lavoro. Se non si dispone di un modello, usare l' [esercitazione relativa alla classificazione delle immagini: Train Model](tutorial-train-models-with-aml.md) per eseguire il training e la registrazione di un modello.

    > [!IMPORTANT]
    > I frammenti di codice in questo articolo presuppongono che siano state impostate le variabili seguenti:
    >
    > * `ws`-L'area di lavoro Azure Machine Learning.
    > * `model`: Modello registrato che verrà distribuito.
    > * `inference_config`-Configurazione dell'inferenza per il modello.
    >
    > Per altre informazioni sull'impostazione di queste variabili, vedere [distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

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

    Per ulteriori informazioni sugli script di immissione, vedere [distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

Queste entità sono incapsulate in una __configurazione__di inferenza. La configurazione dell'inferenza fa riferimento allo script di immissione e ad altre dipendenze.

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

Per ulteriori informazioni sulla configurazione dell'inferenza, vedere [distribuire modelli con il servizio Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Quando si esegue la distribuzione nel servizio app Azure, non è necessario creare una __configurazione di distribuzione__.

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita nel servizio app Azure, usare [Model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Il frammento di codice seguente illustra come compilare una nuova immagine dalla configurazione del modello e dell'inferenza:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Quando `show_output=True`viene visualizzato l'output del processo di compilazione docker. Al termine del processo, l'immagine è stata creata nel Container Registry di Azure per l'area di lavoro.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Dal [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro Azure Machine Learning. Nella sezione __Panoramica__ usare il collegamento al __Registro di sistema__ per accedere al container Registry di Azure per l'area di lavoro.

    [![Screenshot della panoramica dell'area di lavoro](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Dal Container Registry di Azure selezionare __repository__, quindi selezionare il __nome dell'immagine__ che si desidera distribuire. Per la versione che si vuole distribuire, selezionare la voce __...__ e quindi eseguire la __distribuzione nell'app Web__.

    [![Screenshot della distribuzione da ACR a un'app Web](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Per creare l'app Web, specificare un nome di sito, una sottoscrizione, un gruppo di risorse e selezionare il piano di servizio app/percorso. Infine, selezionare __Crea__.

    ![Screenshot della finestra di dialogo nuova app Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Usare l'app Web

Dal [portale di Azure](https://portal.azure.com)selezionare l'app Web creata nel passaggio precedente. Nella sezione __Panoramica__ copiare l' __URL__. Questo valore è l' __URL di base__ del servizio.

[![Screenshot della panoramica per l'app Web](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

Il servizio Web che passa le richieste al modello si trova in `{baseurl}/score`. Ad esempio `https://mywebapp.azurewebsites.net/score`. Il codice Python seguente illustra come inviare dati all'URL e visualizzare la risposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* Per ulteriori informazioni sulla configurazione dell'app Web, vedere la documentazione relativa al [servizio app in Linux](/azure/app-service/containers/) .
* Per altre informazioni sul ridimensionamento, vedere Introduzione a scalabilità automatica [in Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Per altre informazioni sul supporto SSL, vedere [usare un certificato SSL nel servizio app Azure](/azure/app-service/app-service-web-ssl-cert-load).
* Per altre informazioni sull'autenticazione, vedere [configurare l'app del servizio app per usare Azure Active Directory l'accesso](/azure/app-service/configure-authentication-provider-aad).
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)