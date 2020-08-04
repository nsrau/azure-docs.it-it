---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542789"
---
## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).
- [Software Development Kit di Azure Machine Learning (SDK) per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere la documentazione di [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

## <a name="register-your-model"></a><a id="registermodel"></a>Registrare il modello

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP]
> Quando si registra un modello, si specifica il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso consente di individuare solo i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per altre informazioni, vedere [individuare i file di modello nello script di immissione](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

I modelli di apprendimento automatico vengono registrati nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o da un'altra posizione. Quando si registra un modello, è possibile specificare facoltativamente i metadati relativi al modello. I `tags` `properties` dizionari e applicati a una registrazione del modello possono quindi essere utilizzati per filtrare i modelli.

Negli esempi seguenti viene illustrato come registrare un modello.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto Run o un oggetto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , a seconda di come è stato [eseguito](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) il training del modello. Ogni oggetto può essere usato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un modello da un `azureml.core.Run` oggetto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Il `model_path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare sul `model_path` percorso di una cartella che contiene i file. Per ulteriori informazioni, vedere la documentazione di [Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrare un modello da un `azureml.train.automl.run.AutoMLRun` oggetto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In questo esempio, i `metric` `iteration` parametri e non vengono specificati, quindi l'iterazione con la metrica primaria migliore verrà registrata. Il `model_id` valore restituito dall'esecuzione viene utilizzato al posto di un nome di modello.

    Per ulteriori informazioni, vedere la documentazione di [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

Per registrare un modello, è possibile fornire il percorso locale del modello. È possibile specificare il percorso di una cartella o di un singolo file. È possibile utilizzare questo metodo per registrare i modelli sottoposti a training con Azure Machine Learning e quindi scaricarli. È anche possibile usare questo metodo per registrare i modelli sottoposti a training al di fuori della Azure Machine Learning.

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

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

Per ulteriori informazioni, vedere la documentazione relativa alla [classe del modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Per ulteriori informazioni sull'utilizzo di modelli sottoposti a training all'esterno di Azure Machine Learning, vedere [come distribuire un modello esistente](../articles/machine-learning/how-to-deploy-existing-model.md).


## <a name="define-an-entry-script"></a>Definire uno script di immissione

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definire una configurazione di inferenza

Una configurazione di inferenza descrive come configurare il servizio Web che contiene il modello. Viene usato in un secondo momento, quando si distribuisce il modello.

La configurazione dell'inferenza USA ambienti Azure Machine Learning per definire le dipendenze software necessarie per la distribuzione. Gli ambienti consentono di creare, gestire e riutilizzare le dipendenze software necessarie per il training e la distribuzione. È possibile creare un ambiente da file di dipendenza personalizzati o usare uno degli ambienti Azure Machine Learning curati. Il YAML seguente è un esempio di file di dipendenze conda per l'inferenza. Si noti che è necessario indicare azureml-defaults con versione >= 1.0.45 come dipendenza PIP, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web. Se si desidera utilizzare la generazione automatica dello schema, è necessario che lo script di immissione importi anche i `inference-schema` pacchetti.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se la dipendenza è disponibile tramite conda e PIP (da PyPi), Microsoft consiglia di usare la versione conda, in quanto i pacchetti conda sono in genere dotati di binari predefiniti che rendono l'installazione più affidabile.
>
> Per ulteriori informazioni, vedere informazioni su [conda e PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Per verificare se la dipendenza è disponibile tramite conda, utilizzare il `conda search <package-name>` comando oppure utilizzare gli indici dei pacchetti in [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

È possibile usare il file delle dipendenze per creare un oggetto ambiente e salvarlo nell'area di lavoro per un uso futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Per informazioni dettagliate sull'uso e sulla personalizzazione degli ambienti Python con Azure Machine Learning, vedere [creare & usare gli ambienti software in Azure Machine Learning](../articles/machine-learning/how-to-use-environments.md)

Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


Nell'esempio seguente viene illustrato il caricamento di un ambiente dall'area di lavoro e la relativa utilizzo con la configurazione dell'inferenza:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](../articles/machine-learning/how-to-use-environments.md).

Per ulteriori informazioni sulla configurazione dell'inferenza, vedere la documentazione relativa alla classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>Definire una configurazione di distribuzione

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


## <a name="deploy-your-model"></a>Distribuire il modello

A questo punto è possibile distribuire il modello. Nell'esempio seguente viene illustrata una distribuzione locale. La sintassi può variare a seconda della destinazione di calcolo scelta nel passaggio precedente.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione relativa a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).


## <a name="delete-resources"></a>Eliminare le risorse

Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per ulteriori informazioni, vedere la documentazione relativa a [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

