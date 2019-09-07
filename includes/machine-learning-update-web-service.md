---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 11cc93e0eb11d0422fae4fbbbf0a549df067daca
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390669"
---
Per aggiornare un servizio Web, utilizzare il `update` metodo. È possibile aggiornare il servizio Web per usare un nuovo modello, un nuovo script di immissione o nuove dipendenze che possono essere specificate in una configurazione di inferenza. Per ulteriori informazioni, vedere la documentazione relativa a [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Quando si crea una nuova versione di un modello, è necessario aggiornare manualmente ogni servizio che si desidera utilizzare.

**Uso dell'SDK**

Il codice seguente illustra come usare l'SDK per aggiornare il modello, l'ambiente e lo script di immissione per un servizio Web:

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

È anche possibile aggiornare un servizio Web usando l'interfaccia della riga di comando di ML. Nell'esempio seguente viene illustrata la registrazione di un nuovo modello e l'aggiornamento di un servizio Web per l'utilizzo del nuovo modello:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In questo esempio viene usato un documento JSON per passare le informazioni sul modello dal comando di registrazione nel comando Update.
>
> Per aggiornare il servizio per l'utilizzo di un nuovo script di immissione o di un ambiente, creare un file di configurazione `ic` dell' [inferenza](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) e specificarlo con il parametro.

Per ulteriori informazioni, vedere la documentazione relativa all' [aggiornamento del servizio AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .