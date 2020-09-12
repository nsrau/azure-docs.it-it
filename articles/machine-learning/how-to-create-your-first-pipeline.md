---
title: Creare ed eseguire pipeline ML
titleSuffix: Azure Machine Learning
description: Creare ed eseguire una pipeline di Machine Learning con l'SDK di Azure Machine Learning per Python. Usare le pipeline di ML per creare e gestire i flussi di lavoro che uniscono le fasi di Machine Learning (ML). Queste fasi includono la preparazione dei dati, il training del modello, la distribuzione del modello e l'inferenza e il punteggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 8/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 968685b64b1412751ca990842d95e9d2ffb251a6
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650563"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare ed eseguire una pipeline di [Machine Learning](concept-ml-pipelines.md) usando il [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Usare le **pipeline di ml** per creare un flusso di lavoro che unisce varie fasi di machine learning. Pubblicare quindi la pipeline per l'accesso successivo o la condivisione con altri utenti. Tenere traccia delle pipeline di ML per vedere le prestazioni del modello nel mondo reale e per rilevare la tendenza ai dati. Le pipeline di ML sono ideali per gli scenari di assegnazione dei punteggi in batch, l'uso di diversi calcoli, il riutilizzo dei passaggi anziché la loro esecuzione, nonché la condivisione di flussi di lavoro ML con altri utenti.

Sebbene sia possibile usare un tipo diverso di pipeline denominato [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) per l'automazione ci/CD di attività di Machine Learning, questo tipo di pipeline non è archiviato nell'area di lavoro. [Confrontare queste diverse pipeline](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Le pipeline di ML create sono visibili ai membri dell' [area di lavoro](how-to-manage-workspace.md)Azure Machine Learning. 

Le pipeline di ML vengono eseguite su destinazioni di calcolo. vedere [che cosa sono le destinazioni di calcolo in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)). Le pipeline possono leggere e scrivere i dati da e verso le posizioni di [archiviazione di Azure](https://docs.microsoft.com/azure/storage/) supportate.

Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) che conterrà tutte le risorse della pipeline.

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) con l'SDK già installato.

Per iniziare, aggiungere l'area di lavoro:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

Creare le risorse necessarie per eseguire una pipeline di ML:

* Configurare un archivio dati che verrà usato per accedere ai dati necessari nei passaggi della pipeline.

* Configurare un `Dataset` oggetto in modo che punti ai dati persistenti che si trovano in o è accessibile in un archivio dati. Configurare un `PipelineData` oggetto per i dati temporanei passati tra i passaggi della pipeline. 

    > [!TIP]
    > Un'esperienza migliorata per il passaggio di dati temporanei tra i passaggi della pipeline è disponibile nella classe di anteprima pubblica  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) .  Questa classe è una funzionalità di anteprima [sperimentale](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#&preserve-view=truestable-vs-experimental) e può cambiare in qualsiasi momento.

* Configurare le [destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) in cui verranno eseguiti i passaggi della pipeline.

### <a name="set-up-a-datastore"></a>Configurare un archivio dati

Un archivio dati contiene i dati a cui accede la pipeline. Ogni area di lavoro ha un archivio dati predefinito. È possibile registrare altri archivi dati. 

Quando si crea l'area di lavoro, [file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e l' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) sono collegati all'area di lavoro. Un archivio dati predefinito è registrato per la connessione all'archivio BLOB di Azure. Per altre informazioni, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

I passaggi utilizzano in genere i dati e producono dati di output. Un passaggio può creare dati, ad esempio un modello, una directory con modello e file dipendenti o dati temporanei. Questi dati sono quindi disponibili per altri passaggi successivi nella pipeline. Per altre informazioni sulla connessione della pipeline ai dati, vedere gli articoli [come accedere ai dati](how-to-access-data.md) e [come registrare i set](how-to-create-register-datasets.md)di dati. 

### <a name="configure-data-with-dataset-and-pipelinedata-objects"></a>Configurare i dati `Dataset` con `PipelineData` oggetti e

Il modo migliore per fornire i dati a una pipeline è un oggetto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) . L' `Dataset` oggetto punta ai dati presenti in o è accessibile da un archivio dati o da un URL Web. La `Dataset` classe è astratta, quindi si creerà un'istanza di un `FileDataset` (che fa riferimento a uno o più file) o di un oggetto `TabularDataset` creato da da uno o più file con colonne di dati delimitate.


Si crea un oggetto `Dataset` usando metodi come [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) o [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#&preserve-view=truefrom-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```
I dati intermedi (o output di un passaggio) sono rappresentati da un oggetto [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true). `output_data1` viene generato come output di un passaggio e usato come input di uno o più passaggi successivi. `PipelineData` introduce una dipendenza dei dati tra i vari passaggi e crea un ordine di esecuzione implicito nella pipeline. Questo oggetto verrà usato in un secondo momento durante la creazione di passaggi della pipeline.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")

```

> [!TIP]
> La conservazione dei dati intermedi tra i passaggi della pipeline è inoltre possibile con la classe di anteprima pubblica [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Per un esempio di codice che usa la `OutputFileDatasetConfig` classe, vedere How to [Build a Two Step ml pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="set-up-a-compute-target"></a>Configurare una destinazione di calcolo


In Azure Machine Learning il termine __ambiente di calcolo__ (o __destinazione di calcolo__) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning.   Vedere [destinazioni di calcolo per il training del modello](concept-compute-target.md#train) per un elenco completo delle destinazioni di calcolo e [creare destinazioni di calcolo](how-to-create-attach-compute-sdk.md) per la creazione e la connessione all'area di lavoro.   Il processo per la creazione e la connessione di una destinazione di calcolo è lo stesso sia per il training di un modello che per l'esecuzione di un passaggio della pipeline. Dopo aver creato e collegato la destinazione di calcolo, usare l'oggetto `ComputeTarget` nel [passaggio pipeline](#steps).

> [!IMPORTANT]
> L'esecuzione di operazioni di gestione su destinazioni di calcolo non è supportata all'interno di processi remoti. Poiché le pipeline di Machine Learning vengono inviate come processo remoto, non usare le operazioni di gestione in destinazioni di calcolo all'interno della pipeline.

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning

È possibile creare un ambiente di calcolo di Azure Machine Learning per eseguire i passaggi. Il codice per altre destinazioni di calcolo è molto simile, con parametri leggermente diversi, a seconda del tipo. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>Configurare l'ambiente dell'esecuzione di training

Il passaggio successivo consiste nel verificare che l'esecuzione del training remoto includa tutte le dipendenze necessarie per i passaggi di training. Le dipendenze e il contesto di runtime vengono impostati tramite la creazione e la configurazione di un `RunConfiguration` oggetto. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Il codice precedente mostra due opzioni per la gestione delle dipendenze. Come illustrato, con `USE_CURATED_ENV = True` la configurazione si basa su un ambiente curato. Gli ambienti curati sono "precotti" con librerie interdipendenti comuni e possono essere notevolmente più veloci da portare online. Gli ambienti curati hanno immagini Docker predefinite in [Microsoft container Registry](https://hub.docker.com/publishers/microsoftowner). Per ulteriori informazioni, vedere [Azure Machine Learning ambienti curati](resource-curated-environments.md).

Il percorso adottato se si modifica `USE_CURATED_ENV` per `False` visualizzare il modello per l'impostazione esplicita delle dipendenze. In questo scenario verrà creata e registrata una nuova immagine Docker personalizzata in un Container Registry di Azure all'interno del gruppo di risorse. vedere [Introduzione ai registri di contenitori Docker privati in Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro). La compilazione e la registrazione di questa immagine possono richiedere alcuni minuti.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Creare i passaggi della pipeline

Dopo aver creato la risorsa di calcolo e l'ambiente, è possibile definire i passaggi della pipeline. Sono disponibili molti passaggi predefiniti tramite il Azure Machine Learning SDK, come si può vedere nella [documentazione di riferimento per il `azureml.pipeline.steps` pacchetto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true). La classe più flessibile è [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py&preserve-view=true), che esegue uno script Python.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Il codice precedente mostra un tipico passaggio iniziale della pipeline. Il codice di preparazione dei dati si trova in una sottodirectory (in questo esempio, `"prepare.py"` nella directory `"./dataprep.src"` ). Come parte del processo di creazione della pipeline, questa directory viene compressa e caricata in `compute_target` e il passaggio esegue lo script specificato come valore per `script_name` .

I `arguments` `inputs` valori, e `outputs` specificano gli input e gli output del passaggio. Nell'esempio precedente, i dati di base sono il set di dati `my_dataset` . I dati corrispondenti verranno scaricati nella risorsa di calcolo, perché il codice lo specifica come `as_download()` . Lo script `prepare.py` esegue tutte le attività di trasformazione dei dati appropriate per l'attività a disposizione e restituisce i dati a `output_data1` , di tipo `PipelineData` . Per ulteriori informazioni, vedere la pagina relativa allo stato di [trasferimento dei dati in e tra i passaggi della pipeline ml (Python)](how-to-move-data-in-out-of-pipelines.md). 

Il passaggio viene eseguito nel computer definito da `compute_target` usando la configurazione `aml_run_config` . 

Il riutilizzo dei risultati precedenti ( `allow_reuse` ) è fondamentale quando si usano le pipeline in un ambiente di collaborazione, poiché l'eliminazione di riesecuzioni non necessarie offre flessibilità. Il riutilizzo è il comportamento predefinito quando il script_name, gli input e i parametri di un passaggio rimangono invariati. Quando il riutilizzo è consentito, i risultati dell'esecuzione precedente vengono inviati immediatamente al passaggio successivo. Se `allow_reuse` è impostato su `False` , durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio.

È possibile creare una pipeline con un solo passaggio, ma quasi sempre si sceglie di suddividere il processo complessivo in diversi passaggi. Ad esempio, è possibile che si disponga di passaggi per la preparazione, il training, il confronto dei modelli e la distribuzione dei dati. Si supponga, ad esempio, che, dopo la `data_prep_step` specifica precedente, il passaggio successivo potrebbe essere il training:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = PipelineData(name = "training_results", 
                                datastore=def_blob_store,
                                output_name="training_results")

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Il codice precedente è molto simile a quello per la fase di preparazione dei dati. Il codice di training si trova in una directory separata da quella del codice di preparazione dei dati. L' `PipelineData` output del passaggio di preparazione dei dati `output_data1` viene usato come _input_ per il passaggio di training. `PipelineData`Viene creato un nuovo oggetto `training_results` che consente di conservare i risultati per un passaggio di confronto o di distribuzione successivo. 


> [!TIP]
> Per un'esperienza migliorata e la possibilità di scrivere dati intermedi negli archivi dati alla fine dell'esecuzione della pipeline, usare la classe di anteprima pubblica [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Per esempi di codice, vedere How to [Build a Two Step ml pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) e [How to Write Data to back Stores after run complete](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

Dopo la definizione dei passaggi, si crea la pipeline usando alcuni o tutti i passaggi definiti.

> [!NOTE]
> Nessun file o dati viene caricato in Azure Machine Learning quando si definiscono i passaggi o si compila la pipeline. I file vengono caricati quando si chiama [Experiment. Submit ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-).

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>Uso di un set di dati 

I set di dati creati da archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL possono essere usati come input per qualsiasi passaggio della pipeline. È possibile scrivere l'output in [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true)o se si desidera scrivere dati in un archivio dati specifico, utilizzare [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true). 

> [!IMPORTANT]
> La scrittura di dati di output in un archivio dati usando PipelineData è supportata solo per gli archivi dati di Azure BLOB e condivisione file di Azure. 
>
> Per scrivere i dati di output nel BLOB di Azure, nella condivisione file di Azure, in ADLS gen 1 e ADLS gen 2 datastores usare la classe di anteprima pubblica [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Il set di dati viene quindi recuperato nella pipeline utilizzando il dizionario [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueinput-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

`Run.get_context()`È opportuno evidenziare la riga. Questa funzione recupera un oggetto `Run` che rappresenta l'esecuzione sperimentale corrente. Nell'esempio precedente viene usato per recuperare un set di dati registrato. Un altro uso comune dell' `Run` oggetto consiste nel recuperare sia l'esperimento che l'area di lavoro in cui si trova l'esperimento: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Per informazioni più dettagliate, incluse le modalità alternative per passare e accedere ai dati, vedere lo stato di [trasferimento dei dati in e tra i passaggi della pipeline di ml (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Memorizzazione nella cache & riutilizzo  

Per ottimizzare e personalizzare il comportamento delle pipeline, è possibile eseguire alcune operazioni per la memorizzazione nella cache e il riutilizzo. Ad esempio, è possibile scegliere di:
+ **Disattivare il riutilizzo predefinito del passaggio Esegui output** impostando `allow_reuse=False` durante la [definizione del passaggio](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true). Il riutilizzo è fondamentale quando si utilizzano pipeline in un ambiente di collaborazione, perché l'eliminazione di esecuzioni non necessarie offre flessibilità. Tuttavia, è possibile rifiutare esplicitamente il riutilizzo.
+ **Forzare la rigenerazione dell'output per tutti i passaggi di un'esecuzione** con `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Per impostazione predefinita, `allow_reuse` per i passaggi è abilitato e viene eseguito l' `source_directory` hashing dell'oggetto specificato nella definizione del passaggio. Quindi, se lo script per un determinato passaggio rimane lo stesso ( `script_name` , gli input e i parametri) e nessun altro elemento in ` source_directory` è stato modificato, viene riutilizzato l'output di un'esecuzione del passaggio precedente, il processo non viene inviato al calcolo e i risultati dell'esecuzione precedente sono immediatamente disponibili al passaggio successivo.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>Inviare la pipeline

Quando si invia la pipeline, Azure Machine Learning controlla le dipendenze per ogni passaggio e carica uno snapshot della directory di origine specificata. Se la directory di origine non è specificata, viene caricata la directory locale corrente. Lo snapshot viene inoltre archiviato come parte dell'esperimento nell'area di lavoro.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Per altre informazioni, vedere [Snapshot](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando si esegue una pipeline per la prima volta, Azure Machine Learning:

* Scarica lo snapshot del progetto nella destinazione di calcolo dalla risorsa di archiviazione BLOB associata all'area di lavoro.
* Crea un'immagine Docker corrispondente a ogni passaggio nella pipeline.
* Scarica l'immagine Docker per ogni passaggio nella destinazione di calcolo dal registro contenitori.
* Configura l'accesso agli `Dataset` `PipelineData` oggetti e. Per come `as_mount()` modalità di accesso, il fusibile viene usato per fornire l'accesso virtuale. Se il montaggio non è supportato o se l'utente ha specificato l'accesso come `as_download()` , i dati vengono invece copiati nella destinazione di calcolo.

* Esegue il passaggio nella destinazione di calcolo specificata nella definizione del passaggio. 
* Crea gli artefatti, ad esempio i log, stdout e stderr, le metriche e l'output specificati dal passaggio. Questi artefatti vengono quindi caricati e conservati nell'archivio dati predefinito dell'utente.

![Diagramma dell'esecuzione di un esperimento come pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Per ulteriori informazioni, vedere la Guida di riferimento alla [classe Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) .

## <a name="view-results-of-a-pipeline"></a>Visualizzare i risultati di una pipeline

Vedere l'elenco di tutte le pipeline e i relativi dettagli di esecuzione in studio:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visualizzare l'area di lavoro](how-to-manage-workspace.md#view).

1. A sinistra selezionare **pipeline** per visualizzare tutte le esecuzioni della pipeline.
 ![elenco delle pipeline di Machine Learning](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selezionare una pipeline specifica per visualizzare i risultati dell'esecuzione.

### <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Passaggi successivi

- Per condividere la pipeline con colleghi o clienti, vedere [pubblicare pipeline di Machine Learning](how-to-deploy-pipelines.md)
- Usare [i notebook di Jupyter su GitHub](https://aka.ms/aml-pipeline-readme) per esplorare ulteriormente le pipeline di Machine Learning
- Vedere la Guida di riferimento per l'SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true)
- Vedere le [procedure](how-to-debug-pipelines.md) per suggerimenti sul debug e la risoluzione dei problemi relativi alle pipeline =
- Per informazioni su come eseguire i notebook, vedere l'articolo [Esplorare Azure Machine Learning con notebook Jupyter](samples-notebooks.md).
