---
title: Risoluzione dei problemi di distribuzione del servizio Web in locale
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere, risolvere e risolvere i problemi relativi agli errori di distribuzione comuni di Azure Machine Learning Docker in locale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperfq2
ms.openlocfilehash: 18de43cb170ccdede8f7fe13dc0c6c0c2fa4e7a3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188633"
---
# <a name="troubleshoot-model-deployment-locally"></a>Risoluzione dei problemi di distribuzione del modello in locale

Informazioni su come risolvere e risolvere, o aggirare, i comuni errori di distribuzione del servizio Web Docker Azure Machine Learning in locale.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug localmente, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

    Per verificare l'installazione Docker, usare il comando `docker run hello-world` da un terminale o da un prompt dei comandi. Per informazioni sull'installazione Docker o sulla risoluzione dei problemi relativi agli errori Docker, vedere la [Documentazione di Docker](https://docs.docker.com/).

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi.

Per esplorare un esempio eseguibile, è possibile trovare un [notebook di distribuzione locale](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) di esempio nel repository  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) .

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione localmente, modificare il codice per usare `LocalWebservice.deploy_configuration()` per creare una configurazione della distribuzione. Usare quindi `Model.deploy()` per distribuire il servizio. Nell'esempio seguente viene distribuito un modello (contenuto nella variabile del modello) come servizio Web locale:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se si definisce una specifica conda specifica YAML, List azureml-defaults Version >= 1.0.45 come dipendenza PIP. Questo pacchetto è necessario per ospitare il modello come servizio Web.

A questo punto, è possibile usare il servizio come di consueto. Nel codice seguente viene illustrato l'invio di dati al servizio:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe essere necessario aggiornare il file `score.py` per aggiungere la registrazione o tentare di risolvere eventuali problemi individuati. Per ricaricare le modifiche apportate al file di `score.py`, usare `reload()`. Il codice seguente, ad esempio, consente di ricaricare lo script per il servizio e quindi di inviarvi dati. Il punteggio dei dati viene assegnato tramite il file `score.py` aggiornato:

> [!IMPORTANT]
> Il metodo `reload` è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione di a un'altra destinazione di calcolo, vedere [How to Update your webservice](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dalla posizione specificata dall'oggetto `InferenceConfig` usato dal servizio.

Per modificare il modello, le dipendenze Conda o la configurazione della distribuzione, usare [update()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). Nell'esempio seguente viene aggiornato il modello usato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, usare [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Esaminare il log Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. L'esempio seguente illustra come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Se la riga si `Booting worker with pid: <pid>` verifica più volte nei log, significa che non è disponibile memoria sufficiente per avviare il thread di lavoro.
È possibile risolvere l'errore aumentando il valore di `memory_gb` in `deployment_config`

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come risolvere i problemi delle distribuzioni remote](how-to-troubleshoot-deployment.md)
* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
* [Come eseguire ed eseguire il debug di esperimenti localmente](./how-to-debug-visual-studio-code.md)
