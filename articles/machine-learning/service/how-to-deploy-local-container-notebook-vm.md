---
title: Come distribuire i modelli nelle istanze di calcolo
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando le istanze di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: bb187826250b3edc9ac3d9e36a243d75819a45b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496884"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuire un modello per Azure Machine Learning istanze di calcolo

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Le istanze di calcolo sono disponibili solo per le aree di lavoro con un'area **Stati Uniti centro-settentrionali** o **Regno Unito meridionale**.
>Se l'area di lavoro si trova in un'altra area, è possibile continuare a creare e usare una [macchina virtuale del notebook](concept-compute-instance.md#notebookvm) .  È possibile distribuire un modello in un'istanza di calcolo o in una VM notebook usando la procedura descritta in questo articolo.

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

1. Aprire la sottodirectory `samples-*`, quindi aprire `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Una volta aperto, Esegui il notebook.

    ![Screenshot del servizio locale in esecuzione nel notebook](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Il notebook Visualizza l'URL e la porta su cui è in esecuzione il servizio. Ad esempio, `https://localhost:6789`. È anche possibile eseguire la cella contenente `print('Local service port: {}'.format(local_service.port))` per visualizzare la porta.

    ![Screenshot della porta del servizio locale in esecuzione](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Per testare il servizio da un'istanza di calcolo, usare l'URL del `https://localhost:<local_service.port>`. Per eseguire il test da un client remoto, ottenere l'URL pubblico del servizio in esecuzione nell'istanza di calcolo. È possibile determinare l'URL pubblico utilizzando la formula seguente. 
    * VM notebook: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Istanza di calcolo: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 
    
    Ad esempio, 
    * VM notebook: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Istanza di calcolo: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testare il servizio

Per inviare dati di esempio al servizio in esecuzione, usare il codice seguente. Sostituire il valore di `service_url` con l'URL di nel passaggio precedente:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)