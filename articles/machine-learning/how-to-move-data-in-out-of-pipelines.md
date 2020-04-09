---
title: Dati di input e output da pipeline ML
titleSuffix: Azure Machine Learning
description: Preparare, usare e generare dati nelle pipeline di Azure Machine LearningPrepare, consume, and generate data in Azure Machine Learning pipelines
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879765"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Spostamento di dati tra e tra i passaggi della pipeline di ML (Python)Moving data into and between ML pipeline steps (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I dati sono fondamentali per le pipeline di apprendimento automatico. Questo articolo fornisce codice per l'importazione, la trasformazione e lo spostamento dei dati tra i passaggi in una pipeline di Azure Machine Learning.This article provides code for importing, transforming, and moving data between steps in an Azure Machine Learning pipeline. Per una panoramica del funzionamento dei dati in Azure Machine Learning, vedere [Accesso ai dati nei servizi di archiviazione](how-to-access-data.md)di Azure.For an overview of how data works in Azure Machine Learning, see Access data in Azure storage services. Per i vantaggi e la struttura delle pipeline di Azure Machine Learning, vedere Che cosa sono le pipeline di [Azure Machine Learning.](concept-ml-pipelines.md)

In questo articolo verranno illustrate le attività seguenti:

- Usare `Dataset` gli oggetti per i dati preesistentiUse objects for pre-existing data
- Accedi ai dati all'interno dei tuoi passi
- Dividere `Dataset` i dati in sottoinsiemi, ad esempio sottoinsiemi di training e convalida
- Creare `PipelineData` oggetti per trasferire i dati al passaggio successivo della pipelineCreate objects to transfer data to the next pipeline step
- Usare `PipelineData` gli oggetti come input per i passaggi della pipelineUse objects as input to pipeline steps
- Creare `Dataset` nuovi `PipelineData` oggetti da si desidera rendere persistenti

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso allo studio di Azure Machine [Learning.](https://ml.azure.com/)

- Un'area di lavoro di Azure Machine Learning.
  
  Creare [un'area](how-to-manage-workspace.md) di lavoro di Azure Machine Learning o usarne una esistente tramite Python SDK. Importare `Workspace` la `Datastore` classe e caricare le informazioni `config.json` sulla `from_config()`sottoscrizione dal file utilizzando la funzione . Questa funzione cerca il file JSON nella directory corrente per impostazione predefinita, ma `from_config(path="your/file/path")`è anche possibile specificare un parametro di percorso in modo che punti al file utilizzando .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alcuni dati preesistenti. Questo articolo illustra brevemente l'uso di un [contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)di Azure.This article details briefly the use of an Azure blob container .

- Facoltativo: una pipeline di Machine Learning esistente, ad esempio quella descritta in Creare ed eseguire pipeline di [Machine Learning con Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="use-dataset-objects-for-pre-existing-data"></a>Usare `Dataset` gli oggetti per i dati preesistentiUse objects for pre-existing data 

Il modo migliore per inserire dati in una pipeline consiste [nell'utilizzare](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) un Dataset oggetto. `Dataset`rappresentano dati persistenti disponibili in un workspace.

Esistono molti modi per `Dataset` creare e registrare oggetti. I set di dati tabulari sono per i dati delimitati disponibili in uno o più file. I set di dati dei file sono per i dati binari (ad esempio le immagini) o per i dati che verranno analizzati. I modi più semplici `Dataset` a livello di codice per creare oggetti sono l'uso di BLOB esistenti nell'archivio dell'area di lavoro o negli URL pubblici:The simplest programmatic ways to create objects are to use existing blobs in workspace storage or public URLs:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Per altre opzioni sulla creazione di set di dati con opzioni diverse e da origini diverse, per registrarli e rivederli nell'interfaccia utente di Azure Machine Learning, comprendere in che modo le dimensioni dei dati interagiscono con la capacità di calcolo e il controllo delle versioni, vedere Creare set di dati di Azure Machine Learning.For more options on creating datasets with different options and from different sources, registering it and reviewing them in the Azure Machine Learning UI, understanding how data size interacts with compute capacity, and versioning them, see [Create Azure Machine Learning datasets](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Passare set di dati allo scriptPass datasets to your script

Per passare il percorso del set di `Dataset` dati `as_named_input()` allo script, usare il metodo dell'oggetto. È possibile passare `DatasetConsumptionConfig` l'oggetto risultante allo script come `inputs` argomento oppure, utilizzando l'argomento `Run.get_context().input_datasets[]`per lo script della pipeline, è possibile recuperare il dataset utilizzando .

Dopo aver creato un input denominato, è possibile `as_mount()` `as_download()`scegliere la modalità di accesso: o . Se lo script elabora tutti i file nel set di dati e il disco nella risorsa di calcolo è sufficientemente grande per il set di dati, la modalità di accesso al download è la scelta migliore. La modalità di accesso al download eviterà l'overhead del flusso dei dati in fase di esecuzione. Se lo script accede a un sottoinsieme del set di dati o è troppo grande per il calcolo, usare la modalità di accesso di montaggio. Per ulteriori informazioni, leggere [Mount vs.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Per passare un set di dati al passaggio della pipeline:To pass a dataset to your pipeline step:

1. Utilizzare `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` o (nessuna 's' alla `DatasetConsumptionConfig` fine) per creare un oggetto
1. Utilizzare `as_mount()` `as_download()` o per impostare la modalità di accesso
1. Passare i set di dati ai `inputs` passaggi della pipeline usando l'argomento `arguments` o

Il frammento di codice seguente mostra `PythonScriptStep` il modello comune di combinazione di questi passaggi all'interno del costruttore:The following snippet shows the common pattern of combining these steps within the constructor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

È anche possibile usare `random_split()` `take_sample()` metodi come e per creare più input o ridurre la quantità di dati passati al passaggio della pipeline:You can also use methods such as and to create multiple inputs or reduce the amount of data passed to your pipeline step:

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

### <a name="access-datasets-within-your-script"></a>Accedere ai set di dati all'interno dello scriptAccess datasets within your script

Gli input denominati per lo script di `Run` passaggio della pipeline sono disponibili come dizionario all'interno dell'oggetto. Recuperare l'oggetto attivo `Run` utilizzando `Run.get_context()` e quindi recuperare `input_datasets`il dizionario degli input denominati utilizzando . `DatasetConsumptionConfig` Se l'oggetto è `arguments` stato passato `inputs` utilizzando l'argomento `ArgParser` anziché l'argomento , accedere ai dati utilizzando il codice. Entrambe le tecniche sono illustrate nel frammento di codice seguente.

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

Il valore passato sarà il percorso dei file del dataset.

E 'anche possibile accedere `Dataset` a un registrato direttamente. Poiché i set di dati registrati sono persistenti e condivisi in un'area di lavoro, è possibile recuperarli direttamente:Since registered datasets are persistent and shared across a workspace, you can retrieve them directly:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Utilizzare `PipelineData` per i dati intermedi

Mentre `Dataset` gli oggetti rappresentano dati persistenti, [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) oggetti vengono utilizzati per i dati temporanei che viene restituito da passaggi della pipeline. Poiché la durata `PipelineData` di un oggetto è più lunga di un singolo passaggio della pipeline, è necessario definirli nello script di definizione della pipeline. Quando si `PipelineData` crea un oggetto, è necessario fornire un nome e un archivio dati in cui risiederanno i dati. Passare `PipelineData` gli oggetti agli `PythonScriptStep` `outputs` oggetti utilizzando `arguments` _gli_ argomenti :

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

È possibile scegliere `PipelineData` di creare l'oggetto utilizzando una modalità di accesso che fornisce un caricamento immediato. In tal caso, quando `PipelineData`si `upload_mode` crea `"upload"` , `output_path_on_compute` impostare l'argomento su e utilizzare l'argomento per specificare il percorso in cui scrivere i dati:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Utilizzare `PipelineData` come output di un passaggio di allenamento

All'interno della `PythonScriptStep`pipeline, è possibile recuperare i percorsi di output disponibili utilizzando gli argomenti del programma. Se questo passaggio è il primo e i dati di output vengono inizializzati, è necessario creare la directory nel percorso specificato. È quindi possibile scrivere qualsiasi file che `PipelineData`si desidera essere contenuti nel file .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se è `PipelineData` stato `is_directory` creato il `True`con l'argomento impostato `os.makedirs()` su , sarebbe sufficiente eseguire la chiamata e quindi si sarebbe liberi di scrivere qualsiasi file che si desidera per il percorso. Per altre informazioni, vedere la documentazione di riferimento [di PipelineData.For](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) more details, see the PipelineData reference documentation.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lettura `PipelineData` come input per passaggi non iniziali

Dopo che il passaggio iniziale della pipeline scrive alcuni dati nel `PipelineData` percorso e diventa un output di tale passaggio iniziale, può essere utilizzato come input per un passaggio successivo:After the initial pipeline step writes some data to the path and it becomes an output of that initial step, it can be used as a input to a later step:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

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

Il valore `PipelineData` di un input è il percorso dell'output precedente. Se, come illustrato in precedenza, il primo passaggio ha scritto un singolo file, consumandopotrebbe essere simile al seguente:If, as shown previously, the first step wrote a single file, consuming it might look like: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Convertire `PipelineData` gli `Dataset`oggetti in s

Se si desidera rendere `PipelineData` disponibile per un periodo di tempo `as_dataset()` superiore alla durata `Dataset`di una corsa, utilizzare la relativa funzione per convertirlo in un file . È quindi possibile `Dataset`registrare , rendendolo un cittadino di prima classe nell'area di lavoro. Poiché `PipelineData` l'oggetto avrà un percorso diverso ogni volta che `create_new_version` viene `True` eseguita `Dataset` la pipeline, è consigliabile impostare su quando si registra un oggetto creato da un `PipelineData` oggetto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure di Machine LearningCreate an Azure machine learning dataset](how-to-create-register-datasets.md)
* [Creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDKCreate and run machine learning pipelines with Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
