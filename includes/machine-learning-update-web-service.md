---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: fef6225812980900ad55944644310433a6105de2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556843"
---
Quando si crea un nuovo modello, è necessario aggiornare manualmente ogni servizio per il quale si desidera utilizzare il nuovo modello. Per aggiornare il servizio Web, usare il metodo `update`. Il codice seguente illustra come usare l'SDK per aggiornare il modello per un servizio Web:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

È anche possibile aggiornare un servizio Web usando l'interfaccia della riga di comando di ML. Nell'esempio seguente viene illustrata la registrazione di un nuovo modello e l'aggiornamento del servizio Web per l'utilizzo del nuovo modello:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In questo esempio viene usato un documento JSON per passare le informazioni sul modello dal comando di registrazione nel comando Update.