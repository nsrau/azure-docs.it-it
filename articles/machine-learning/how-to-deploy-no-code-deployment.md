---
title: Nessuna distribuzione di codice (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire un modello senza uno script di immissione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.openlocfilehash: 9d6e234e1f4c8ac5199b92a09eb12bf7aa41b01b
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185486"
---
# <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

La distribuzione del modello senza codice è attualmente in anteprima e supporta i Framework di apprendimento automatico seguenti:

## <a name="tensorflow-savedmodel-format"></a>Formato TensorFlow SavedModel
I modelli TensorFlow devono essere registrati in **formato SavedModel** per funzionare con la distribuzione del modello senza codice.

Vedere [questo collegamento](https://www.tensorflow.org/guide/saved_model) per informazioni su come creare un SavedModel.

Sono supportate tutte le versioni di TensorFlow elencate in "Tags" al [TensorFlow che funge da DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>Modelli ONNX

La registrazione e la distribuzione del modello ONNX sono supportate per qualsiasi grafo di inferenza di ONNX. I passaggi di pre-elaborazione e postelaborazione non sono attualmente supportati.

Di seguito è riportato un esempio di come registrare e distribuire un modello ONNX di MNIST:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Per assegnare un punteggio a un modello, vedere [utilizzare un modello di Azure machine learning distribuito come servizio Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service). Molti progetti ONNX utilizzano i file protobuf per archiviare in modo compatto i dati di training e di convalida, il che può rendere difficile conoscere il formato dei dati previsto dal servizio. In qualità di sviluppatore di modelli, è consigliabile documentare gli sviluppatori:

* Formato di input (JSON o Binary)
* Forma e tipo di dati di input (ad esempio, una matrice di float di forma [100,100, 3])
* Informazioni di dominio (ad esempio, per un'immagine, lo spazio di colore, l'ordine dei componenti e se i valori vengono normalizzati)

Se si usa Pytorch, l' [esportazione di modelli da Pytorch a ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) include i dettagli sulla conversione e sulle limitazioni. 

## <a name="scikit-learn-models"></a>Scikit-informazioni sui modelli

Nessuna distribuzione del modello di codice è supportata per tutti i tipi di modello Scikit-learn predefiniti.

Di seguito è riportato un esempio di come registrare e distribuire un modello sklearn senza codice aggiuntivo:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Per impostazione predefinita, i modelli che supportano predict_proba utilizzeranno il metodo. Per eseguire l'override di questo per usare Predict, è possibile modificare il corpo del POST come indicato di seguito:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Queste dipendenze sono incluse nel contenitore di inferenza Scikit-learn predefinito:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger per gli eventi per le distribuzioni di modelli](how-to-use-event-grid.md)
