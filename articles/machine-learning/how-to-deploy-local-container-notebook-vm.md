---
title: Modelli HDeploy per le istanze di calcolo
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando le istanze di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 07afeba8ab481da6a23862dee187c8c72df19f3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84429577"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuire un modello per Azure Machine Learning istanze di calcolo

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web nell'istanza di calcolo Azure Machine Learning. Usare le istanze di calcolo se si verifica una delle condizioni seguenti:

- È necessario distribuire e convalidare rapidamente il modello.
- Si sta eseguendo il test di un modello in fase di sviluppo.

> [!TIP]
> La distribuzione di un modello da un Jupyter Notebook in un'istanza di calcolo a un servizio Web nella stessa VM è una _distribuzione locale_. In questo caso, il computer ' local ' è l'istanza di calcolo. Per altre informazioni sulle distribuzioni, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro Azure Machine Learning con un'istanza di calcolo in esecuzione. Per ulteriori informazioni, vedere [configurazione dell'ambiente e dell'area di lavoro](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Eseguire la distribuzione nelle istanze di calcolo

Un notebook di esempio che illustra le distribuzioni locali è incluso nell'istanza di calcolo. Usare la procedura seguente per caricare il notebook e distribuire il modello come servizio Web nella macchina virtuale:

1. Da [Azure Machine Learning Studio](https://ml.azure.com)selezionare le istanze di calcolo Azure Machine Learning.

1. Aprire la `samples-*` sottodirectory e quindi aprire `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` . Una volta aperto, Esegui il notebook.

    ![Screenshot del servizio locale in esecuzione nel notebook](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Il notebook Visualizza l'URL e la porta su cui è in esecuzione il servizio. Ad esempio: `https://localhost:6789`. È anche possibile eseguire la cella che contiene `print('Local service port: {}'.format(local_service.port))` per visualizzare la porta.

    ![Screenshot della porta del servizio locale in esecuzione](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Per testare il servizio da un'istanza di calcolo, usare l' `https://localhost:<local_service.port>` URL. Per eseguire il test da un client remoto, ottenere l'URL pubblico del servizio in esecuzione nell'istanza di calcolo. È possibile determinare l'URL pubblico utilizzando la formula seguente. 
    * VM notebook: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Istanza di calcolo: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Ad esempio, 
    * VM notebook:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Istanza di calcolo:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testare il servizio

Per inviare dati di esempio al servizio in esecuzione, usare il codice seguente. Sostituire il valore di `service_url` con l'URL di nel passaggio precedente:

> [!NOTE]
> Quando si esegue l'autenticazione a una distribuzione nell'istanza di calcolo, l'autenticazione viene eseguita utilizzando Azure Active Directory. La chiamata a `interactive_auth.get_authentication_header()` nel codice di esempio esegue l'autenticazione con AAD e restituisce un'intestazione che può essere usata per eseguire l'autenticazione nel servizio nell'istanza di calcolo. Per altre informazioni, vedere [Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine Learning](how-to-setup-authentication.md#interactive-authentication).
>
> Quando si esegue l'autenticazione a una distribuzione in Azure Kubernetes Service o in istanze di contenitore di Azure, viene usato un metodo di autenticazione diverso. Per altre informazioni su, vedere [configurare l'autenticazione per Azure Machine Learning risorse e flussi di lavoro](how-to-setup-authentication.md#web-service-authentication).

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

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)