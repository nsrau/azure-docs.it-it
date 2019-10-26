---
title: Controllo delle versioni del set di dati
titleSuffix: Azure Machine Learning service
description: Informazioni sulla procedura consigliata per la versione dei set di impostazioni e sul funzionamento del controllo delle versioni con le pipeline di Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902002"
---
# <a name="version-and-track-datasets-in-experiments"></a>Set di impostazioni di versione e di rilevamento negli esperimenti

In questa procedura viene illustrato come eseguire la versione e tenere traccia dei set di impostazioni di Azure Machine Learning per la riproducibilità. Il controllo delle versioni del set di dati consente di contrassegnare lo stato dei dati in modo da poter applicare una versione specifica del set di dati per esperimenti futuri.

Scenari tipici da considerare per il controllo delle versioni:

* Quando sono disponibili nuovi dati per la ripetizione del training.
* Quando si applicano approcci diversi per la preparazione dei dati o la progettazione di funzionalità.

## <a name="prerequisites"></a>Prerequisiti

Per questa procedura, è necessario:

- [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto [azureml-DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- [Area di lavoro Azure Machine Learning](concept-workspace.md). Recuperarne uno esistente con il codice seguente o [creare una nuova area di lavoro](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Set di dati Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrare e recuperare le versioni del set di dati

La registrazione di un set di dati consente la versione, il riutilizzo e la condivisione tra gli esperimenti e i colleghi. È possibile registrare più set di impostazioni con lo stesso nome e recuperare una versione specifica in base al nome e al numero di versione.

### <a name="register-a-dataset-version"></a>Registrare una versione del set di dati

Il codice seguente registra una nuova versione del set di dati, `titanic_ds`, impostando il parametro `create_new_version` su `True`. Se non è presente alcuna `titanic_ds` registrata con l'area di lavoro, viene creato un nuovo set di dati con il nome `titanic_ds` e la relativa versione viene impostata su 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Recuperare un set di dati in base al nome

Per impostazione predefinita, il metodo [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) nella classe `Dataset` restituisce la versione più recente del set di dati registrato con l'area di lavoro. 

Il codice seguente ottiene la versione 1 del set di dati `titanic_ds`.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Procedura consigliata per il controllo delle versioni

Quando si crea una versione del set di dati, **non** si crea una copia aggiuntiva dei dati con l'area di lavoro. I set di dati sono riferimenti ai dati nel servizio di archiviazione, quindi è presente una sola origine di verità gestita dal servizio di archiviazione. 

>[!IMPORTANT]
> Se i dati a cui fa riferimento il set di dati vengono sovrascritti o eliminati, la chiamata a una versione specifica del set di dati non può annullare la modifica. 

Quando si caricano dati da un set di dati, caricherà sempre il contenuto dei dati corrente a cui fa riferimento il set di dati. Se si desidera garantire la riproducibilità di ogni versione del set di dati, è consigliabile non modificare il contenuto dei dati a cui fa riferimento la versione del set di dati. Quando arrivano nuovi dati, Salva i nuovi file di dati in una cartella di dati separata e crea una nuova versione del set di dati per includere i dati dalla nuova cartella dati.

L'immagine e il codice di esempio seguenti illustrano la modalità consigliata per strutturare le cartelle di dati e creare versioni del set di dati che fanno riferimento a tali cartelle.

![Struttura di cartelle](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Versione di un set di dati di output della pipeline

È possibile usare il set di dati come input e output di ogni passaggio della pipeline di Machine Learning (ML). Quando si eseguono nuovamente le pipeline, l'output di ogni passaggio della pipeline viene registrato come nuova versione del set di dati. 

Poiché le pipeline di ML compilano l'output di ogni passaggio in una nuova cartella ogni volta che viene rieseguita la pipeline, i set di risultati con versione sono riproducibili.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Tenere traccia dei set di impostazioni negli esperimenti

Per ogni esperimento di Machine Learning, è possibile tracciare facilmente i set di dati usati come input tramite l'oggetto `Run` del modello registrato.

Usare il codice seguente per registrare i modelli con i set di impostazioni.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Dopo la registrazione, è possibile visualizzare l'elenco dei modelli registrati con il set di dati usando Python o la [pagina di destinazione dell'area di lavoro](https://ml.azure.com/).

Il codice seguente usa il metodo [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) per tenere traccia dei set di dati di input usati con l'esecuzione dell'esperimento.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

È anche possibile trovare i `input_datasets` dagli esperimenti usando la [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com/). 

La figura seguente mostra dove trovare il set di dati di input di un esperimento nella pagina di destinazione dell'area di lavoro. Per questo esempio, passare al riquadro **Experiments (esperimenti** ) e aprire la scheda **Properties (proprietà** ) per un'esecuzione specifica dell'esperimento, `keras-mnist`. 

![Set di dati di input](media/how-to-version-datasets/input-datasets.png)

È anche possibile trovare i modelli che hanno usato il set di dati con la pagina di destinazione dell'area di lavoro. La vista seguente si trova nel pannello set di impostazioni in asset. Selezionare il set di dati e passare alla scheda modelli per visualizzare un elenco dei modelli che usano tale set di dati. 

![Modelli di set di dati di input](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il [training con i set di impostazioni](how-to-train-with-datasets.md).
* [Altri notebook di esempio di set di impostazioni](https://aka.ms/dataset-tutorial).
