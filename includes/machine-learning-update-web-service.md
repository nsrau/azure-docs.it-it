---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477256"
---
Per aggiornare un servizio `update` Web, utilizzare il metodo . È possibile aggiornare il servizio Web per utilizzare un nuovo modello, un nuovo script di voce o nuove dipendenze che possono essere specificate in una configurazione di inferenza. Per ulteriori informazioni, vedere la documentazione relativa a [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Quando si crea una nuova versione di un modello, è necessario aggiornare manualmente ogni servizio che si desidera utilizzare.
>
> Non è possibile usare l'SDK per aggiornare un servizio Web pubblicato dalla finestra di progettazione di Azure Machine Learning.You can not use the SDK to update a web service published from the Azure Machine Learning designer.

**Uso dell'SDK**

Il codice seguente mostra come usare l'SDK per aggiornare il modello, l'ambiente e lo script di ingresso per un servizio Web:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Uso dell'interfaccia della riga di comando**

È inoltre possibile aggiornare un servizio Web utilizzando l'interfaccia della riga di comando di ML. Nell'esempio seguente viene illustrata la registrazione di un nuovo modello e quindi l'aggiornamento di un servizio Web per l'utilizzo del nuovo modello:The following example demonstrates registering a new model and then updating a web service to use the new model:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In questo esempio, un documento JSON viene usato per passare le informazioni sul modello dal comando registration nel comando update.
>
> Per aggiornare il servizio in modo che utilizzi un nuovo script di `ic` voce o un nuovo ambiente, creare un file di configurazione dell'inferenza e specificarlo con il parametro . [inference configuration file](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema)

Per altre informazioni, vedere la documentazione relativa [all'aggiornamento del servizio az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)