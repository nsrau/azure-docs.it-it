---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning
description: Informazioni su come e dove distribuire i modelli di Azure Machine Learning, tra cui istanze di contenitore di Azure, servizio Azure Kubernetes, Azure IoT Edge e FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 11/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: fa8d40e4817b6adb42da6daa3035bd1c4a67c5d8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325268"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuire modelli con Azure Machine Learning

Informazioni su come distribuire un modello di machine learning come servizio Web nel cloud di Azure o per Azure IoT Edge dispositivi.

Il flusso di lavoro è simile indipendentemente dal punto in cui si distribuisce il modello:

1. Registrare il modello (facoltativo, vedere di seguito).
1. Preparare una configurazione di inferenza (a meno che non si utilizzi la [distribuzione senza codice](./how-to-deploy-no-code-deployment.md)).
1. Preparare uno script di immissione (a meno che non si usi la [distribuzione senza codice](./how-to-deploy-no-code-deployment.md)).
1. Scegliere una destinazione di calcolo.
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il servizio Web risultante.

Per ulteriori informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [gestire, distribuire e monitorare i modelli con Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).
- [Estensione dell'interfaccia della riga di comando di Azure (CLI) per il servizio Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).
- Il [Azure Machine Learning Software Development Kit (SDK) per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Seguire le istruzioni nella documentazione dell'interfaccia della riga di comando di Azure per [impostare il contesto della sottoscrizione](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Eseguire quindi le operazioni seguenti:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

per visualizzare le aree di lavoro a cui si ha accesso.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere la documentazione di [Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#workspace) .


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrare il modello (facoltativo)

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP] 
> La registrazione di un modello per il rilevamento della versione è consigliata ma non obbligatoria. Se si preferisce procedere senza registrare un modello, è necessario specificare una directory di origine in [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) o [inferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) e assicurarsi che il modello si trovi all'interno della directory di origine.

> [!TIP]
> Quando si registra un modello, si specifica il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso consente di individuare solo i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per altre informazioni, vedere [individuare i file di modello nello script di immissione](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Quando si usa `Tags` l'opzione Filtra per nella pagina modelli di Azure Machine Learning Studio, anziché usare `TagName : TagValue` i clienti `TagName=TagValue` (senza spazio)

Negli esempi seguenti viene illustrato come registrare un modello.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Il `--asset-path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare sul `--asset-path` percorso di una cartella che contiene i file.

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Per includere più file nella registrazione del modello, impostare sul `-p` percorso di una cartella che contiene i file.

Per ulteriori informazioni su `az ml model register` , consultare la [documentazione di riferimento](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto Run o un oggetto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , a seconda di come è stato [eseguito](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py) il training del modello. Ogni oggetto può essere usato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un modello da un `azureml.core.Run` oggetto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Il `model_path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare sul `model_path` percorso di una cartella che contiene i file. Per ulteriori informazioni, vedere la documentazione [Run.register_model](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrare un modello da un `azureml.train.automl.run.AutoMLRun` oggetto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In questo esempio, i `metric` `iteration` parametri e non vengono specificati, quindi l'iterazione con la metrica primaria migliore verrà registrata. Il `model_id` valore restituito dall'esecuzione viene utilizzato al posto di un nome di modello.

    Per ulteriori informazioni, vedere la documentazione [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

Per registrare un modello, è possibile fornire il percorso locale del modello. È possibile specificare il percorso di una cartella o di un singolo file. È possibile utilizzare questo metodo per registrare i modelli sottoposti a training con Azure Machine Learning e quindi scaricarli. È anche possibile usare questo metodo per registrare i modelli sottoposti a training al di fuori della Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Uso di SDK e ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Per includere più file nella registrazione del modello, impostare sul `model_path` percorso di una cartella che contiene i file.

Per ulteriori informazioni, vedere la documentazione relativa alla [classe del modello](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

Per ulteriori informazioni sull'utilizzo di modelli sottoposti a training all'esterno di Azure Machine Learning, vedere [come distribuire un modello esistente](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definire uno script di immissione

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definire una configurazione di inferenza


Una configurazione di inferenza descrive come configurare il servizio Web che contiene il modello. Viene usato in un secondo momento, quando si distribuisce il modello.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Una configurazione di inferenza minima può essere scritta come segue:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Questo specifica che la distribuzione userà il file `score.py` nella `./working_dir` Directory per elaborare le richieste in ingresso.

Per una descrizione più approfondita delle configurazioni di inferenza, [vedere questo articolo](./reference-azure-machine-learning-cli.md#inference-configuration-schema) . 

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato:

1. caricamento di un [ambiente curato](resource-curated-environments.md) dall'area di lavoro
1. Clonazione dell'ambiente
1. Specificando `scikit-learn` come dipendenza.
1. Uso dell'ambiente per la creazione di un InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).

Per ulteriori informazioni sulla configurazione dell'inferenza, vedere la documentazione relativa alla classe [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) .

---

> [!TIP] 
> Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definire una configurazione di distribuzione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Le opzioni disponibili per una configurazione di distribuzione variano a seconda della destinazione di calcolo scelta.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Per ulteriori informazioni, vedere [il riferimento](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Prima di distribuire il modello, è necessario definire la configurazione della distribuzione. *La configurazione della distribuzione è specifica per la destinazione di calcolo in cui verrà ospitato il servizio Web.* Ad esempio, quando si distribuisce un modello localmente, è necessario specificare la porta in cui il servizio accetta le richieste. La configurazione della distribuzione non fa parte dello script di immissione. Viene usato per definire le caratteristiche della destinazione di calcolo che ospiterà lo script del modello e della voce.

Potrebbe anche essere necessario creare la risorsa di calcolo, se, ad esempio, non si dispone già di un'istanza di Azure Kubernetes Service (AKS) associata all'area di lavoro.

La tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Locale | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanze di Azure Container | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Le classi per local, istanze di contenitore di Azure e servizi Web AKS possono essere importate da `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-model"></a>Distribuire il modello

A questo punto è possibile distribuire il modello. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Uso di un modello registrato

Se il modello è stato registrato nell'area di lavoro Azure Machine Learning, sostituire "modello: 1" con il nome del modello e il relativo numero di versione.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Uso di un modello locale

Se si preferisce non registrare il modello, è possibile passare il parametro "sourceDirectory" nella inferenceconfig.jssu per specificare una directory locale da cui distribuire il modello.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrata una distribuzione locale. La sintassi può variare a seconda della destinazione di calcolo scelta nel passaggio precedente.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione relativa a [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py), [Model. Deploy ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](/python/api/azureml-core/azureml.core.webservice.webservice?preserve-view=true&view=azure-ml-py).

---

### <a name="understanding-service-state"></a>Informazioni sullo stato del servizio

Durante la distribuzione del modello, è possibile che venga visualizzata la modifica dello stato del servizio durante la distribuzione completa.

Nella tabella seguente vengono descritti i diversi Stati del servizio:

| Stato WebService | Descrizione | Stato finale?
| ----- | ----- | ----- |
| Transizione | Il servizio è in fase di distribuzione. | No |
| Unhealthy | Il servizio è stato distribuito ma non è attualmente raggiungibile.  | No |
| Non pianificabile | Non è possibile distribuire il servizio in questo momento a causa di risorse insufficienti. | No |
| Non riuscito | La distribuzione del servizio non è riuscita a causa di un errore o di un arresto anomalo. | Sì |
| Healthy | Il servizio è integro e l'endpoint è disponibile. | Sì |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> Inferenza batch
Azure Machine Learning le destinazioni di calcolo vengono create e gestite da Azure Machine Learning. Possono essere usati per la stima in batch da Azure Machine Learning pipeline.

Per una procedura dettagliata di inferenza batch con Azure Machine Learning calcolo, vedere [How to Run batch Predictions](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> Inferenza IoT Edge
Il supporto per la distribuzione in Edge è in anteprima. Per altre informazioni, vedere [Deploy Azure Machine Learning As an IOT Edge Module](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Eliminare le risorse

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Per eliminare un WebService distribuito, usare `az ml service <name of webservice>` .

Per eliminare un modello registrato dall'area di lavoro, usare `az ml model delete <model id>`

Altre informazioni sull' [eliminazione di un](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) servizio Web e sull' [eliminazione di un modello](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per ulteriori informazioni, vedere la documentazione relativa a [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) e [Model. Delete ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

---


## <a name="next-steps"></a>Passaggi successivi

* [Risolvere una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger per gli eventi per le distribuzioni di modelli](how-to-use-event-grid.md)