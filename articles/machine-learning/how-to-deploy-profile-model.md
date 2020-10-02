---
title: Utilizzo della CPU e della memoria del modello di profilo
titleSuffix: Azure Machine Learning
description: Scopri come profilare la memoria del modello e l'utilizzo della CPU
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: aac5fba68c43892216cbd16dd99b0c6a9bf70217
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660990"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Profilare il modello per determinare l'utilizzo delle risorse

Questo articolo illustra come profilare un apprendimento automatico al modello per determinare la quantità di CPU e memoria che è necessario allocare per il modello durante la distribuzione come servizio Web.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia stato eseguito il training e la registrazione di un modello con Azure Machine Learning. Vedere l' [esercitazione di esempio qui](how-to-train-scikit-learn.md) per un esempio di formazione e registrazione di un modello Scikit-learn con Azure Machine Learning.

## <a name="limitations"></a>Limitazioni

* La profilatura non funziona quando il Container Registry di Azure (ACR) per l'area di lavoro è dietro una rete virtuale.

## <a name="run-the-profiler"></a>Eseguire il profiler

Dopo aver registrato il modello e preparato gli altri componenti necessari per la distribuzione, è possibile determinare la CPU e la memoria necessarie per il servizio distribuito. Il profiling testa il servizio che esegue il modello e restituisce informazioni quali l'utilizzo della CPU, l'utilizzo della memoria e la latenza della risposta. Fornisce inoltre una raccomandazione per la CPU e la memoria in base all'utilizzo delle risorse.

Per profilare il modello, è necessario:
* Modello registrato.
* Una configurazione di inferenza basata sullo script di immissione e la definizione dell'ambiente di inferenza.
* Set di dati tabulare a colonna singola, in cui ogni riga contiene una stringa che rappresenta i dati della richiesta di esempio.

> [!IMPORTANT]
> A questo punto è supportata solo la profilatura dei servizi che prevedono che i dati della richiesta siano una stringa, ad esempio: JSON serializzato di stringa, testo, stringa serializzata immagine e così via. Il contenuto di ogni riga del set di dati (stringa) verrà inserito nel corpo della richiesta HTTP e inviato al servizio incapsulando il modello per l'assegnazione dei punteggi.

> [!IMPORTANT]
> È supportata solo la profilatura di un massimo di 2 CPU nell'area ChinaEast2 e USGovArizona.

Di seguito è riportato un esempio di come è possibile costruire un set di dati di input per profilare un servizio che prevede che i dati della richiesta in ingresso contengano JSON serializzato. In questo caso, è stato creato un set di dati basato su 100 istanze dello stesso contenuto della richiesta. Negli scenari reali è consigliabile usare set di dati più grandi contenenti diversi input, soprattutto se l'utilizzo o il comportamento delle risorse del modello è dipendente dall'input.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Quando il set di dati contenente i dati di richiesta di esempio è pronto, creare una configurazione di inferenza. La configurazione dell'inferenza si basa su score.py e sulla definizione dell'ambiente. Nell'esempio seguente viene illustrato come creare la configurazione dell'inferenza ed eseguire la profilatura:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Il comando seguente illustra come profilare un modello usando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Per salvare in modo permanente le informazioni restituite dalla profilatura, usare tag o proprietà per il modello. L'utilizzo di tag o proprietà consente di archiviare i dati con il modello nel registro di sistema del modello. Gli esempi seguenti illustrano l'aggiunta di un nuovo tag contenente le `requestedCpu` `requestedMemoryInGb` informazioni e:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

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

