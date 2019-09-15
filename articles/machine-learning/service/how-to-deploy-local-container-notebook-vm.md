---
title: Come distribuire i modelli nelle VM notebook
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando le macchine virtuali del notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: 046f998038c47a48a8528bf36d87ac836395eec2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002819"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Distribuire un modello in macchine virtuali notebook

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web nella macchina virtuale del notebook. Usare le macchine virtuali del notebook se si verifica una delle condizioni seguenti:

- È necessario distribuire e convalidare rapidamente il modello.
- Si sta eseguendo il test di un modello in fase di sviluppo.

> [!TIP]
> La distribuzione di un modello da un Jupyter Notebook in una macchina virtuale notebook a un servizio Web nella stessa VM è una _distribuzione locale_. In questo caso, il computer locale è la VM del notebook. Per altre informazioni sulle distribuzioni, vedere [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro Azure Machine Learning con una VM notebook in esecuzione. Per ulteriori informazioni, vedere [configurazione dell'ambiente e dell'area di lavoro](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Eseguire la distribuzione nelle VM notebook

Un notebook di esempio che illustra le distribuzioni locali è incluso nella macchina virtuale del notebook. Usare la procedura seguente per caricare il notebook e distribuire il modello come servizio Web nella macchina virtuale:

1. Dal [portale di Azure](https://portal.azure.com)selezionare le VM Azure Machine Learning notebook.

1. Aprire la `samples-*` sottodirectory e quindi aprire `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Una volta aperto, Esegui il notebook.

    ![Screenshot del servizio locale in esecuzione nel notebook](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Il notebook Visualizza l'URL e la porta su cui è in esecuzione il servizio. Ad esempio `https://localhost:6789`. È anche possibile eseguire la cella che `print('Local service port: {}'.format(local_service.port))` contiene per visualizzare la porta.

    ![Screenshot della porta del servizio locale in esecuzione](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Per testare il servizio dalla VM notebook, usare l' `https://localhost:<local_service.port>` URL. Per eseguire il test da un client remoto, ottenere l'URL pubblico del servizio in esecuzione nella macchina virtuale del notebook. è possibile determinare l'URL pubblico usando la formula seguente. `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Ad esempio `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

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
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
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