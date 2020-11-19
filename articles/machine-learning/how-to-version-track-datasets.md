---
title: Controllo delle versioni del set di dati
titleSuffix: Azure Machine Learning
description: Informazioni su come ottimizzare la versione dei set di impostazioni e sul funzionamento del controllo delle versioni con le pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 8c9beaca0fb3ee7881559ffcc955f171bc2ddd7b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886448"
---
# <a name="version-and-track-datasets-in-experiments"></a>Set di impostazioni di versione e di rilevamento negli esperimenti

Questo articolo illustra come eseguire la versione e tenere traccia dei set di impostazioni di Azure Machine Learning per la riproducibilità. Il controllo delle versioni del set di dati consente di contrassegnare lo stato dei dati in modo da poter applicare una versione specifica del set di dati per esperimenti futuri.

Scenari di controllo delle versioni tipiche:

* Quando sono disponibili nuovi dati per la ripetizione del training
* Quando si applicano approcci diversi alla preparazione dei dati o alla progettazione delle funzionalità

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

- [SDK di Azure Machine Learning per Python installato](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Questo SDK include il pacchetto [azureml-DataSets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py) .
    
- [Area di lavoro Azure Machine Learning](concept-workspace.md). Recuperarne uno esistente eseguendo il codice seguente oppure [creare una nuova area di lavoro](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Set di dati Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrare e recuperare le versioni del set di dati

Registrando un set di dati, è possibile riutilizzarlo e condividerlo tra diversi esperimenti e con i colleghi. È possibile registrare più set di impostazioni con lo stesso nome e recuperare una versione specifica in base al nome e al numero di versione.

### <a name="register-a-dataset-version"></a>Registrare una versione del set di dati

Il codice seguente registra una nuova versione del set di dati impostando `titanic_ds` il `create_new_version` parametro su `True` . Se non è stato `titanic_ds` registrato alcun set di dati esistente con l'area di lavoro, il codice crea un nuovo set di dati con il nome `titanic_ds` e ne imposta la versione su 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Recuperare un set di dati in base al nome

Per impostazione predefinita, il metodo [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) della `Dataset` classe restituisce la versione più recente del set di dati registrato con l'area di lavoro. 

Il codice seguente ottiene la versione 1 del `titanic_ds` set di dati.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Procedura consigliata per il controllo delle versioni

Quando si crea una versione del set di dati, *non* viene creata una copia aggiuntiva dei dati con l'area di lavoro. Poiché i set di dati sono riferimenti ai dati nel servizio di archiviazione, si ha un'unica origine di verità, gestita dal servizio di archiviazione.

>[!IMPORTANT]
> Se i dati a cui fa riferimento il set di dati vengono sovrascritti o eliminati, la chiamata a una versione specifica del set di dati *non comporta il* ripristino della modifica.

Quando si caricano dati da un set di dati, il contenuto dei dati corrente a cui fa riferimento il set di dati viene sempre caricato. Per assicurarsi che ogni versione del set di dati sia riproducibile, è consigliabile non modificare il contenuto dei dati a cui fa riferimento la versione del set di dati. Quando arrivano nuovi dati, Salva i nuovi file di dati in una cartella di dati separata e quindi crea una nuova versione del set di dati per includere i dati dalla nuova cartella.

Nell'immagine e nel codice di esempio seguenti viene illustrata la modalità consigliata per strutturare le cartelle di dati e creare versioni del set di dati che fanno riferimento a tali cartelle:

![Struttura di cartelle](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-an-ml-pipeline-output-dataset"></a>Versione di un set di dati di output della pipeline ML

È possibile usare un set di dati come input e output di ogni passaggio della [pipeline ml](concept-ml-pipelines.md) . Quando si eseguono nuovamente le pipeline, l'output di ogni passaggio della pipeline viene registrato come nuova versione del set di dati.

Le pipeline ML compilano l'output di ogni passaggio in una nuova cartella ogni volta che viene rieseguita la pipeline. Questo comportamento consente la riproducibilità dei set di impostazioni di output con versione. Altre informazioni sui [set di impostazioni nelle pipeline](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Tenere traccia dei dati negli esperimenti

Azure Machine Learning tiene traccia dei dati nell'esperimento come set di dati di input e di output.  

Di seguito sono riportati alcuni scenari in cui i dati vengono rilevati come un set di dati di **input**. 

* Come `DatasetConsumptionConfig` oggetto tramite il `inputs` `arguments` parametro o dell' `ScriptRunConfig` oggetto durante l'invio dell'esecuzione dell'esperimento. 

* Quando nello script vengono chiamati metodi come, get_by_name () o get_by_id (). Per questo scenario, il nome assegnato al set di dati quando è stato registrato nell'area di lavoro è il nome visualizzato. 

Di seguito sono riportati alcuni scenari in cui i dati vengono rilevati come set di dati di **output**.  

* Passare un `OutputFileDatasetConfig` oggetto tramite il `outputs` parametro o `arguments` quando si invia un'esecuzione dell'esperimento. `OutputFileDatasetConfig` gli oggetti possono essere utilizzati anche per salvare in modo permanente i dati tra i passaggi della pipeline. Vedere [spostare i dati tra i passaggi della pipeline ml.](how-to-move-data-in-out-of-pipelines.md)
    > [!TIP]
    > [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) è una classe di anteprima pubblica che contiene funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) che possono cambiare in qualsiasi momento.

* Registrare un set di dati nello script. Per questo scenario, il nome assegnato al set di dati quando è stato registrato nell'area di lavoro è il nome visualizzato. Nell'esempio seguente `training_ds` è il nome che verrebbe visualizzato.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Invia l'esecuzione figlio con un set di dati non registrato nello script. Viene restituito un set di dati salvato anonimo.

### <a name="trace-datasets-in-experiment-runs"></a>Set di elementi di traccia nelle esecuzioni dell'esperimento

Per ogni esperimento di Machine Learning, è possibile tracciare facilmente i set di dati usati come input con l' `Run` oggetto esperimento.

Il codice seguente usa il [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) metodo per tenere traccia dei set di dati di input usati con l'esecuzione dell'esperimento:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

È anche possibile trovare la `input_datasets` da esperimenti usando il [Azure Machine Learning Studio](). 

La figura seguente mostra dove trovare il set di dati di input di un esperimento in Azure Machine Learning Studio. Per questo esempio, passare al riquadro **Experiments (esperimenti** ) e aprire la scheda **Properties (proprietà** ) per un'esecuzione specifica dell'esperimento `keras-mnist` .

![Set di dati di input](./media/how-to-version-track-datasets/input-datasets.png)

Usare il codice seguente per registrare i modelli con i set di impostazioni:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Dopo la registrazione, è possibile visualizzare l'elenco dei modelli registrati con il set di dati usando Python o passare a [Studio](https://ml.azure.com/).

La vista seguente si trova nel riquadro **set di impostazioni** in **Asset**. Selezionare il set di dati e quindi selezionare la scheda **modelli** per un elenco dei modelli registrati con il set di dati. 

![Modelli di set di dati di input](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training con set di dati](how-to-train-with-datasets.md)
* [Altri notebook del set di dati di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)