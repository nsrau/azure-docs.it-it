---
title: Trasferimento di dati in pipeline ML
titleSuffix: Azure Machine Learning
description: Informazioni sull'output & di input dei dati in pipeline di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 6744bbf2e77fa0ec275350678e75ff094eec82e0
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650392"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Spostamento di dati in e tra i passaggi della pipeline ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo fornisce il codice per l'importazione, la trasformazione e lo stato di trasferimento dei dati tra i passaggi in una pipeline Azure Machine Learning. Per una panoramica del funzionamento dei dati in Azure Machine Learning, vedere [accedere ai dati nei servizi di archiviazione di Azure](how-to-access-data.md). Per i vantaggi e la struttura delle pipeline di Azure Machine Learning, vedere [che cosa sono le pipeline Azure Machine Learning?](concept-ml-pipelines.md).

In questo articolo verranno illustrate le attività seguenti:

- Usare `Dataset` gli oggetti per i dati preesistenti
- Accedere ai dati nei passaggi
- Suddividere `Dataset` i dati in subset, ad esempio subset di training e di convalida
- Creare `PipelineData` oggetti per trasferire i dati al passaggio successivo della pipeline
- USA `PipelineData` oggetti come input per i passaggi della pipeline
- Creare nuovi `Dataset` oggetti da `PipelineData` salvare in modo permanente

> [!TIP]
> Un'esperienza migliorata per passare dati temporanei tra i passaggi della pipeline e salvare in modo permanente i dati dopo l'esecuzione della pipeline è disponibile nelle classi di anteprima pubblica,  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) e [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) .  Queste classi sono funzionalità di anteprima [sperimentale](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#&preserve-view=truestable-vs-experimental) e possono cambiare in qualsiasi momento.


## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) o usarne una esistente tramite Python SDK. Importare le classi `Workspace` e `Datastore` e caricare le informazioni sulla sottoscrizione dal file `config.json` usando la funzione `from_config()`. Questa funzione Cerca il file JSON nella directory corrente per impostazione predefinita, ma è anche possibile specificare un parametro Path per puntare al file usando `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alcuni dati preesistenti. Questo articolo illustra brevemente l'uso di un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Facoltativo: una pipeline di Machine Learning esistente, ad esempio quella descritta in [creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Usare `Dataset` gli oggetti per i dati preesistenti 

Il modo migliore per inserire i dati in una pipeline consiste nell'usare un oggetto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) . `Dataset` gli oggetti rappresentano dati permanenti disponibili in un'area di lavoro.

Esistono diversi modi per creare e registrare `Dataset` oggetti. I set di dati tabulari sono per i dati delimitati disponibili in uno o più file. I set di dati di file sono per i dati binari (ad esempio immagini) o per i dati che verranno analizzati. Il modo più semplice per creare oggetti a livello di codice consiste nell' `Dataset` usare i BLOB esistenti nell'archiviazione dell'area di lavoro o negli URL pubblici:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Per altre opzioni di creazione di set di dati con opzioni diverse e da origini diverse, registrazione e revisione nell'interfaccia utente di Azure Machine Learning, informazioni sul modo in cui le dimensioni dei dati interagiscono con la capacità di calcolo e sul controllo delle versioni, vedere [creare set](how-to-create-register-datasets.md)di dati di Azure Machine Learning. 

### <a name="pass-datasets-to-your-script"></a>Passare i set di impostazioni allo script

Per passare il percorso del set di dati allo script, usare il `Dataset` metodo dell'oggetto `as_named_input()` . È possibile passare l'oggetto risultante `DatasetConsumptionConfig` allo script come argomento o, usando l'argomento dello script della `inputs` pipeline, è possibile recuperare il set di dati usando `Run.get_context().input_datasets[]` .

Una volta creato un input denominato, è possibile scegliere la modalità di accesso: `as_mount()` o `as_download()` . Se lo script elabora tutti i file nel set di dati e il disco della risorsa di calcolo è sufficientemente grande per il set di dati, la modalità di accesso al download è la scelta migliore. La modalità di accesso al download eviterà l'overhead di streaming dei dati in fase di esecuzione. Se lo script accede a un subset del set di dati o è troppo grande per il calcolo, usare la modalità di accesso di montaggio. Per altre informazioni, vedere [Mount vs. download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Per passare un set di dati al passaggio della pipeline:

1. Usare `TabularDataset.as_named_inputs()` o `FileDataset.as_named_input()` (No ' s'alla fine) per creare un `DatasetConsumptionConfig` oggetto
1. Usare `as_mount()` o `as_download()` per impostare la modalità di accesso
1. Passare i set di impostazioni ai passaggi della pipeline usando l' `arguments` argomento o `inputs`

Il frammento di codice seguente illustra il modello comune di combinazione di questi passaggi all'interno del `PythonScriptStep` costruttore: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

È anche possibile usare metodi come `random_split()` e `take_sample()` per creare più input o ridurre la quantità di dati passati al passaggio della pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Accedere ai set di impostazioni nello script

Gli input denominati per lo script del passaggio della pipeline sono disponibili come dizionario all'interno dell' `Run` oggetto. Recuperare l' `Run` oggetto attivo usando `Run.get_context()` e quindi recuperare il dizionario degli input denominati usando `input_datasets` . Se l'oggetto è stato passato `DatasetConsumptionConfig` utilizzando l' `arguments` argomento anziché l' `inputs` argomento, accedere ai dati utilizzando il `ArgParser` codice. Entrambe le tecniche sono illustrate nel frammento di codice seguente.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Il valore passato sarà il percorso dei file del set di dati.

È anche possibile accedere direttamente a un registrato `Dataset` . Poiché i set di elementi registrati sono persistenti e condivisi in un'area di lavoro, è possibile recuperarli direttamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>`PipelineData`Da usare per i dati intermedi

Mentre `Dataset` gli oggetti rappresentano dati permanenti, gli oggetti [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) vengono usati per i dati temporanei restituiti dai passaggi della pipeline. Poiché la durata di un `PipelineData` oggetto è più lunga di un singolo passaggio della pipeline, è necessario definirli nello script di definizione della pipeline. Quando si crea un `PipelineData` oggetto, è necessario specificare un nome e un archivio dati in cui risiederanno i dati. Passare gli `PipelineData` oggetti al `PythonScriptStep` usando gli _both_ `arguments` `outputs` argomenti e:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

È possibile scegliere di creare l' `PipelineData` oggetto usando una modalità di accesso che fornisce un caricamento immediato. In tal caso, quando si crea il `PipelineData` , impostare `upload_mode` su `"upload"` e usare l' `output_path_on_compute` argomento per specificare il percorso in cui verranno scritti i dati:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!TIP]
> Con la classe di anteprima pubblica è disponibile un'esperienza migliorata per il passaggio di dati intermedi tra i passaggi della pipeline [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Per un esempio di codice con `OutputFileDatasetConfig` , vedere How to [Build a Two Step ml pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Usare `PipelineData` come output di un passaggio di training
All'interno della pipeline è `PythonScriptStep` possibile recuperare i percorsi di output disponibili usando gli argomenti del programma. Se questo passaggio è il primo e inizializza i dati di output, sarà necessario creare la directory nel percorso specificato. È quindi possibile scrivere tutti i file che si desidera siano contenuti in `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se l'oggetto è stato creato `PipelineData` con l' `is_directory` argomento impostato su `True` , è sufficiente eseguire la chiamata, `os.makedirs()` quindi è possibile scrivere tutti i file desiderati. Per ulteriori informazioni, vedere la documentazione di riferimento di [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) .


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lettura `PipelineData` come input per i passaggi non iniziali

Quando il passaggio iniziale della pipeline scrive alcuni dati nel `PipelineData` percorso e diventa un output del passaggio iniziale, può essere usato come input per un passaggio successivo:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Il valore di un `PipelineData` input è il percorso dell'output precedente. 

> [!TIP]
> Con la classe di anteprima pubblica è disponibile un'esperienza migliorata per il passaggio di dati intermedi tra i passaggi della pipeline [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Per un esempio di codice con `OutputFileDatasetConfig` , vedere How to [Build a Two Step ml pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

Se, come illustrato in precedenza, il primo passaggio ha scritto un singolo file, l'utilizzo potrebbe essere simile al seguente: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Converti `PipelineData` oggetti in `Dataset` s

Se si vuole rendere la `PipelineData` disponibilità più lunga della durata di un'esecuzione, usare la relativa `as_dataset()` funzione per convertirla in un oggetto `Dataset` . È quindi possibile registrare `Dataset` , rendendolo un cittadino di prima classe nell'area di lavoro. Poiché l' `PipelineData` oggetto avrà un percorso diverso ogni volta che viene eseguita la pipeline, è consigliabile impostare `create_new_version` su `True` quando si registra un `Dataset` creato da un `PipelineData` oggetto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Con la classe di anteprima pubblica è disponibile un'esperienza migliorata per il salvataggio permanente dei dati intermedi al di fuori delle esecuzioni della pipeline [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Per un esempio di codice con `OutputFileDatasetConfig` , vedere How to [Build a Two Step ml pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md)
* [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
