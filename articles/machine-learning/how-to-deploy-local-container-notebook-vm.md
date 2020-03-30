---
title: Come distribuire modelli in istanze di calcoloHow to deploy models to compute instances
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando le istanze di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398183"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuire un modello nelle istanze di calcolo di Azure Machine LearningDeploy a model to Azure Machine Learning compute instances

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web nell'istanza di calcolo di Azure Machine Learning.Learn how to use Azure Machine Learning to deploy a model as a web service on your Azure Machine Learning compute instance. Utilizzare le istanze di calcolo se si verifica una delle seguenti condizioni:

- È necessario distribuire e convalidare rapidamente il modello.
- Si sta eseguendo il test di un modello in fase di sviluppo.

> [!TIP]
> La distribuzione di un modello da un blocco appunti di Jupyter in un'istanza di calcolo a un servizio Web nella stessa macchina virtuale è una _distribuzione locale._ In questo caso, il computer 'local' è l'istanza di calcolo. Per altre informazioni sulle distribuzioni, vedere Distribuire modelli con Azure Machine Learning.For more information on deployments, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning con un'istanza di calcolo in esecuzione. Per ulteriori informazioni, consultate Ambiente di [installazione e area di lavoro.](tutorial-1st-experiment-sdk-setup.md)

## <a name="deploy-to-the-compute-instances"></a>Eseguire la distribuzione nelle istanze di calcoloDeploy to the compute instances

Un blocco appunti di esempio che illustra le distribuzioni locali è incluso nell'istanza di calcolo. Usare la procedura seguente per caricare il blocco appunti e distribuire il modello come servizio Web nella macchina virtuale:Use the following steps to load the notebook and deploy the model as a web service on the VM:

1. In Azure Machine Learning Studio selezionare le istanze di calcolo di Azure Machine Learning.From [Azure Machine Learning studio,](https://ml.azure.com)select your Azure Machine Learning compute instances.

1. Aprire `samples-*` la sottodirectory, `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`quindi aprire . Una volta aperto, eseguire il blocco appunti.

    ![Screenshot del servizio locale in esecuzione nel blocco appunti](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Nel blocco appunti vengono visualizzati l'URL e la porta su cui è in esecuzione il servizio. Ad esempio: `https://localhost:6789`. È inoltre possibile eseguire `print('Local service port: {}'.format(local_service.port))` la cella contenente per visualizzare la porta.

    ![Screenshot della porta del servizio locale in esecuzione](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Per testare il servizio da `https://localhost:<local_service.port>` un'istanza di calcolo, usare l'URL. Per eseguire il test da un client remoto, ottenere l'URL pubblico del servizio in esecuzione nell'istanza di calcolo. L'URL pubblico può essere determinato utilizzare la seguente formula; 
    * VM del `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`notebook: . 
    * Istanza di `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`calcolo: . 

    Ad esempio, 
    * Macchina virtuale del notebook:Notebook VM:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Istanza di calcolo:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testare il servizio

Per inviare dati di esempio al servizio in esecuzione, usare il codice seguente. Sostituire il `service_url` valore di con l'URL del passaggio precedente:

> [!NOTE]
> Quando si esegue l'autenticazione a una distribuzione nell'istanza di calcolo, l'autenticazione viene effettuata usando Azure Active Directory.When authenticating to a deployment on the compute instance, the authentication is made using Azure Active Directory. La chiamata `interactive_auth.get_authentication_header()` al codice di esempio autentica l'utilizzo di AAD e restituisce un'intestazione che può quindi essere utilizzata per l'autenticazione al servizio nell'istanza di calcolo. Per altre informazioni, vedere [Configurare l'autenticazione per](how-to-setup-authentication.md#interactive-authentication)le risorse e i flussi di lavoro di Azure Machine Learning.
>
> Quando si esegue l'autenticazione a una distribuzione nel servizio Azure Kubernetes o nelle istanze del contenitore di Azure, viene usato un metodo di autenticazione diverso. Per altre informazioni, vedere [Configurare l'autenticazione per](how-to-setup-authentication.md#web-service-authentication)le risorse e i flussi di lavoro di Azure Machine Learning.

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzataHow to deploy a model using a custom Docker image](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)