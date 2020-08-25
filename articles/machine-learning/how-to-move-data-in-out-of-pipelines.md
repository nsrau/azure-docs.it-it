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
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756963"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Spostamento di dati in e tra i passaggi della pipeline ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo fornisce il codice per l'importazione, la trasformazione e lo stato di trasferimento dei dati tra i passaggi in una pipeline Azure Machine Learning. Per una panoramica del funzionamento dei dati in Azure Machine Learning, vedere [accedere ai dati nei servizi di archiviazione di Azure](how-to-access-data.md). Per i vantaggi e la struttura delle pipeline di Azure Machine Learning, vedere [che cosa sono le pipeline Azure Machine Learning?](concept-ml-pipelines.md).

In questo articolo verranno illustrate le attività seguenti:

- Usare `Dataset` gli oggetti per i dati preesistenti
- Accedere ai dati nei passaggi
- Suddividere `Dataset` i dati in subset, ad esempio subset di training e di convalida
- Creare `OutputFileDatasetConfig` oggetti per trasferire i dati al passaggio successivo della pipeline
- USA `OutputFileDatasetConfig` oggetti come input per i passaggi della pipeline
- Creare nuovi `Dataset` oggetti da `OutputFileDatasetConfig` salvare in modo permanente

> [!NOTE]
>Le `OutputFileDatasetConfig` `OutputTabularDatasetConfig` classi e sono funzionalità di anteprima sperimentale e possono cambiare in qualsiasi momento.
>
>Per altre informazioni, vedere https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

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

Il modo migliore per inserire i dati in una pipeline consiste nell'usare un oggetto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) . `Dataset` gli oggetti rappresentano dati permanenti disponibili in un'area di lavoro.

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

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>`OutputFileDatasetConfig`Da usare per i dati intermedi

Sebbene `Dataset` gli oggetti rappresentino solo dati persistenti, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) è possibile utilizzare gli oggetti per l'output di dati temporanei da passaggi della pipeline **e** dati di output permanenti. 

 `OutputFileDatasetConfig` il comportamento predefinito dell'oggetto consiste nel scrivere nell'archivio dati predefinito dell'area di lavoro. Passare gli `OutputFileDatasetConfig` oggetti all'oggetto `PythonScriptStep` con il `arguments` parametro.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

È possibile scegliere di caricare il contenuto dell' `OutputFileDatasetConfig` oggetto alla fine di un'esecuzione. In tal caso, usare la `as_upload()` funzione insieme all' `OutputFileDatasetConfig` oggetto e specificare se sovrascrivere i file esistenti nella destinazione. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Usare `OutputFileDatasetConfig` come output di un passaggio di training

All'interno della pipeline è `PythonScriptStep` possibile recuperare i percorsi di output disponibili usando gli argomenti del programma. Se questo passaggio è il primo e inizializza i dati di output, sarà necessario creare la directory nel percorso specificato. È quindi possibile scrivere tutti i file che si desidera siano contenuti in `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Lettura `OutputFileDatasetConfig` come input per i passaggi non iniziali

Quando il passaggio iniziale della pipeline scrive alcuni dati nel `OutputFileDatasetConfig` percorso e diventa un output del passaggio iniziale, può essere usato come input per un passaggio successivo. 

Nel codice seguente, 

* `step1_output_data` indica che l'output di PythonScriptStep `step1` viene scritto nell'archivio dati di generazione 2 ADLS `my_adlsgen2` in modalità di caricamento. Altre informazioni su come [configurare le autorizzazioni](how-to-access-data.md#azure-data-lake-storage-generation-2) per i ruoli per la scrittura di dati negli archivi dati ADLS di generazione 2. 

* Dopo `step1` il completamento di e l'output viene scritto nella destinazione indicata da `step1_output_data` , il passaggio 2 è pronto per essere usato `step1_output_data` come input. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registrare `OutputFileDatasetConfig` oggetti da riutilizzare

Se si vuole rendere la `OutputFileDatasetConfig` disponibilità più lunga della durata dell'esperimento, registrarla nell'area di lavoro per condividerla e riutilizzarla in diversi esperimenti.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md)
* [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
