---
title: Controllo delle versioni del set di dati
titleSuffix: Azure Machine Learning
description: Informazioni su come versione ottimale dei set di dati e sul funzionamento del controllo delle versioni con le pipeline di Apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528473"
---
# <a name="version-and-track-datasets-in-experiments"></a>È necessario creare e tenere traccia dei set di dati negli esperimenti
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo verrà illustrato come versione e tenere traccia dei set di dati di Azure Machine Learning per la riproducibilità. Il controllo delle versioni del set di dati consente di aggiungere un segnalibro allo stato dei dati in modo da poter applicare una versione specifica del set di dati per esperimenti futuri.

Scenari tipici di controllo delle versioni:Typical versioning scenarios:

* Quando sono disponibili nuovi dati per la riqualificazione
* Quando si applicano diversi approcci di preparazione dei dati o di progettazione delle funzionalità

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

- [Azure Machine Learning SDK per Python installato.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Questo SDK include il pacchetto [azureml-datasets.This SDK includes the azureml-datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) package.
    
- Area di lavoro di [Azure Machine Learning](concept-workspace.md). Recuperarne uno esistente eseguendo il codice seguente [oppure creare una nuova area di lavoro.](how-to-manage-workspace.md)

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Un set di dati di [Azure Machine Learning.](how-to-create-register-datasets.md)

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrare e recuperare le versioni del set di datiRegister and retrieve dataset versions

Registrando un set di dati, è possibile versione, riutilizzarlo e condividerlo tra esperimenti e colleghi. È possibile registrare più set di dati con lo stesso nome e recuperare una versione specifica in base al nome e al numero di versione.

### <a name="register-a-dataset-version"></a>Registrare una versione del set di datiRegister a dataset version

Il codice seguente registra una `titanic_ds` nuova versione `create_new_version` del `True`dataset impostando il parametro su . Se non è `titanic_ds` presente alcun dataset esistente registrato con l'area `titanic_ds` di lavoro, il codice crea un nuovo set di dati con il nome e ne imposta la versione su 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
È anche possibile registrare una nuova versione di un set di dati 

### <a name="retrieve-a-dataset-by-name"></a>Recuperare un set di dati in base al nomeRetrieve a dataset by name

Per impostazione predefinita, il `Dataset` metodo [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) sulla classe restituisce la versione più recente del set di dati registrato nell'area di lavoro. 

Il codice seguente ottiene `titanic_ds` la versione 1 del set di dati.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Procedure consigliate per il controllo delle versioni

Quando si crea una versione del set di dati, *non* si crea una copia aggiuntiva dei dati con l'area di lavoro. Poiché i set di dati sono riferimenti ai dati nel servizio di archiviazione, si dispone di un'unica fonte di verità, gestita dal servizio di archiviazione.

>[!IMPORTANT]
> Se i dati a cui fa riferimento il set di dati vengono sovrascritti o eliminati, la chiamata a una versione specifica del set di dati *non* annulla la modifica.

Quando si caricano dati da un set di dati, il contenuto di dati corrente a cui fa riferimento il set di dati viene sempre caricato. Se si desidera assicurarsi che ogni versione del set di dati sia riproducibile, è consigliabile non modificare il contenuto dei dati a cui fa riferimento la versione del set di dati. Quando arrivano nuovi dati, salvare i nuovi file di dati in una cartella di dati separata e quindi creare una nuova versione del set di dati per includere i dati dalla nuova cartella.

L'immagine e il codice di esempio seguenti illustrano il modo consigliato per strutturare le cartelle di dati e creare versioni del set di dati che fanno riferimento a tali cartelle:The following image and sample code show the recommended way to structure your data folders and to create dataset versions that reference those folders:

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

## <a name="version-a-pipeline-output-dataset"></a>Versione di un set di dati di output della pipelineVersion a pipeline output dataset

È possibile usare un set di dati come input e output di ogni passaggio della pipeline di Machine Learning.You can use a dataset as the input and output of each Machine Learning pipeline step. Quando si rieseguono le pipeline, l'output di ogni passaggio della pipeline viene registrato come nuova versione del set di dati.

Poiché le pipeline di Machine Learning popolano l'output di ogni passaggio in una nuova cartella ogni volta che la pipeline viene rieseguita, i set di dati di output con controllo delle versioni sono riproducibili. Ulteriori informazioni sui [set di dati nelle pipeline](how-to-create-your-first-pipeline.md#steps).

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

## <a name="track-datasets-in-experiments"></a>Tenere traccia dei set di dati negli esperimenti

Per ogni esperimento di Machine Learning, è possibile tracciare `Run` facilmente i set di dati usati come input tramite l'oggetto esperimento.

Il codice seguente [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) usa il metodo per tenere traccia dei set di dati di input usati con l'esecuzione dell'esperimento:The following code uses the method to track which input datasets were used with the experiment run:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

È inoltre possibile `input_datasets` trovare gli https://ml.azure.com/esperimenti da utilizzando . 

L'immagine seguente mostra dove trovare il set di dati di input di un esperimento in Azure Machine Learning Studio.The following image shows where to find the input dataset of an experiment on Azure Machine Learning studio. Per questo esempio, passare al riquadro **Esperimenti** e aprire la `keras-mnist`scheda **Proprietà** per un'esecuzione specifica dell'esperimento, .

![Set di dati di inputInput datasets](./media/how-to-version-track-datasets/input-datasets.png)

Usare il codice seguente per registrare i modelli con i dataset:Use the following code to register models with datasets:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Dopo la registrazione, è possibile visualizzare l'elenco dei modelli https://ml.azure.com/registrati con il set di dati utilizzando Python o andare a .

La visualizzazione seguente è riportata dal riquadro **Set di dati** in **Asset**. Selezionare il set di dati e quindi selezionare la scheda **Modelli** per un elenco dei modelli registrati con il set di dati. 

![Modelli di set di dati di inputInput datasets models](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training con set di dati](how-to-train-with-datasets.md)
* [Altri blocchi appunti di set di dati di esempio](https://aka.ms/dataset-tutorial)
