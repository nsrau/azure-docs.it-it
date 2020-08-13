---
title: Eseguire previsioni batch su Big Data
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere inferenze in modo asincrono su grandi quantità di dati tramite la funzionalità ParallelRunStep in Azure Machine Learning. ParallelRunStep offre funzionalità di elaborazione parallela predefinite ed è ottimizzata per la velocità effettiva elevata e per l'inferenza "fire-and-forget" per i casi d'uso relativi ai Big Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 07/16/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 960b59275885efd547df63febab37d2403c1c7cf
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847705"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Eseguire l'inferenza batch su grandi quantità di dati usando Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come eseguire l'inferenza batch su grandi quantità di dati in modo asincrono e in parallelo usando Azure Machine Learning. ParallelRunStep offre funzionalità di elaborazione parallela predefinite.

Con ParallelRunStep è facile aumentare le dimensioni delle inferenze offline fino a grandi cluster di computer su terabyte di dati strutturati o non strutturati, ottenendo una maggiore produttività e ottimizzando i costi.

In questo articolo si apprenderà come eseguire le attività seguenti:

> 1. Configurare le risorse di Machine Learning.
> 1. Configurare gli input e l'output dei dati di inferenza batch.
> 1. Preparare il modello di classificazione delle immagini con training preliminare in base al set di dati [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/). 
> 1.  Scrivere lo script di inferenza.
> 1. Creare una [pipeline di Machine Learning](concept-ml-pipelines.md) contenente ParallelRunStep ed eseguire l'inferenza batch su immagini di test MNIST. 
> 1. Ripetere l'esecuzione dell'inferenza batch con nuovi input di dati e parametri. 
> 1. Visualizzare i risultati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per un avvio rapido guidato, completare l'[esercitazione relativa all'installazione](tutorial-1st-experiment-sdk-setup.md) se non si ha già un'area di lavoro di Azure Machine Learning. 

* Per gestire l'ambiente in uso e le relative dipendenze, vedere la [guida pratica](how-to-configure-environment.md) sulla configurazione dell'ambiente locale.

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

Le azioni seguenti configurano le risorse di Machine Learning necessarie per eseguire una pipeline di inferenza batch:

- Connettersi a un'area di lavoro.
- Creare una risorsa di calcolo o collegarne una esistente.

### <a name="configure-workspace"></a>Configurare l'area di lavoro

Creare un oggetto area di lavoro dall'area di lavoro esistente. `Workspace.from_config()` legge il file config.json e carica i dettagli in un oggetto denominato ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Questo frammento di codice prevede che la configurazione dell'area di lavoro sia salvata nella directory corrente o nella relativa directory padre. Per altre informazioni sulla creazione di un'area di lavoro, vedere [Creare e gestire le aree di lavoro di Azure Machine Learning](how-to-manage-workspace.md). Per altre informazioni sul salvataggio della configurazione in un file, vedere [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

In Azure Machine Learning il *calcolo* (o la *destinazione di calcolo*) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning. Eseguire il codice seguente per creare una destinazione [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) basata su CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Configurare gli input e l'output

### <a name="create-a-datastore-with-sample-images"></a>Creare un archivio dati con immagini di esempio

Ottenere il set di valutazione MNIST dal contenitore BLOB pubblico `sampledata` in un account denominato `pipelinedata`. Creare un archivio dati con il nome `mnist_datastore`, che punta a questo contenitore. Nella seguente chiamata a `register_azure_blob_container`, l'impostazione del flag `overwrite` su `True` sovrascrive eventuali archivi dati creati in precedenza con quel nome. 

È possibile modificare questo passaggio in modo da puntare al proprio contenitore BLOB fornendo valori personalizzati per `datastore_name`, `container_name` e `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Specificare quindi l'archivio dati predefinito dell'area di lavoro come archivio dati di output. Verrà usato per l'output dell'inferenza.

Quando si crea la propria area di lavoro, per impostazione predefinita vengono collegati all'area [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [Archivio BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) . File di Azure è l'archivio dati predefinito per un'area di lavoro, ma come archivio dati è possibile usare anche la risorsa di archiviazione BLOB. Per altre informazioni,consultare [Azure storage options](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks) (Opzioni di archiviazione di Azure).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Creare gli input di dati

Gli input per l'inferenza batch sono i dati che si vogliono partizionare per l'elaborazione parallela. Una pipeline di inferenza batch accetta gli input di dati tramite [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py).

`Dataset` è una classe per l'esplorazione, la trasformazione e la gestione dei dati in Azure Machine Learning. Ne esistono due tipi: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). In questo esempio si userà `FileDataset` come input. `FileDataset` offre la possibilità di scaricare o montare i file nel contesto di calcolo. Creando un set di dati, si crea un riferimento alla posizione dell'origine dati. Anche le eventuali trasformazioni di subset applicate al set di dati verranno archiviate nel set di dati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione.

Per altre informazioni sui set di dati di Azure Machine Learning, vedere [Creare e accedere ai set di dati (anteprima)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Per usare gli input di dati dinamici quando si esegue la pipeline di inferenza batch, è possibile definire il `Dataset` degli input come [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py). È possibile specificare il set di dati di input ogni volta che si ripete l'esecuzione della pipeline di inferenza batch.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Creare l'output

Gli oggetti [`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vengono usati per trasferire i dati intermedi tra i passaggi della pipeline. In questo esempio l'oggetto viene usato per l'output di inferenza.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>Preparare il modello

[Scaricare il modello di classificazione delle immagini con training preliminare](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz) e quindi estrarlo nella directory `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Registrare quindi il modello con l'area di lavoro in modo che sia disponibile per la risorsa di calcolo.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Scrivere lo script di inferenza

>[!Warning]
>Il codice seguente è solo un esempio usato dal [notebook di esempio](https://aka.ms/batch-inference-notebooks). È necessario creare uno script personalizzato per lo scenario in uso.

Lo script *deve contenere* due funzioni:
- `init()`: usare questa funzione per qualsiasi preparazione dispendiosa o comune per un'inferenza successiva. Usarla ad esempio per caricare il modello in un oggetto globale. Questa funzione verrà chiamata solo una volta all'inizio del processo.
-  `run(mini_batch)`: la funzione viene eseguita per ogni istanza di `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` richiama il metodo run e passa un elenco o un `DataFrame` Pandas come argomento al metodo. Ogni voce in mini_batch sarà un percorso file se l'input è un oggetto `FileDataset` oppure un `DataFrame` Pandas se l'input è un oggetto `TabularDataset`.
    -  `response`: il metodo run() deve restituire un `DataFrame` Pandas o una matrice. Per append_row output_action, questi elementi restituiti vengono accodati nel file di output comune. Per summary_only, il contenuto degli elementi viene ignorato. Per tutte le azioni di output, ogni elemento di output restituito indica un'esecuzione riuscita dell'elemento di input nel mini-batch di input. Verificare che nel risultato dell'esecuzione siano inclusi dati sufficienti per eseguire il mapping dell'input al risultato dell'output dell'esecuzione. L'output dell'esecuzione verrà scritto nel file di output ma non sarà necessariamente in ordine, quindi sarà necessario usare una chiave nell'output per eseguirne il mapping all'input.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se è presente un altro file o un'altra cartella nella stessa directory dello script di inferenza, è possibile farvi riferimento individuando la directory di lavoro corrente.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Compilare ed eseguire la pipeline contenente ParallelRunStep

A questo punto si ha tutto ciò che serve: gli input dei dati, il modello, l'output e lo script di inferenza. Ora si creerà la pipeline di inferenza batch contenente ParallelRunStep.

### <a name="prepare-the-environment"></a>Preparare l'ambiente

Specificare prima di tutto le dipendenze per lo script. In questo modo è possibile installare i pacchetti pip e configurare l'ambiente.

Includere sempre **azureml-core** e **azureml-dataset-runtime[pandas, fuse]** nell'elenco di pacchetti pip. Se si usa un'immagine Docker personalizzata (user_managed_dependencies=True), è necessario che sia installato anche Conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Specificare i parametri tramite ParallelRunConfig

`ParallelRunConfig` è la configurazione principale per l'istanza di `ParallelRunStep` all'interno della pipeline di Azure Machine Learning. Si usa per eseguire il wrapping dello script e configurare i parametri necessari, incluse tutte le voci seguenti:
- `entry_script`: script utente come percorso di file locale che verrà eseguito in parallelo su più nodi. Se `source_directory` è presente, usare un percorso relativo. In caso contrario, usare qualsiasi percorso accessibile nel computer.
- `mini_batch_size`: dimensioni del mini-batch passato a una singola chiamata di `run()` (Facoltativo; il valore predefinito è `10` file per `FileDataset` e `1MB` per `TabularDataset`.)
    - Per `FileDataset`, è il numero di file con un valore minimo di `1`. È possibile combinare più file in un solo mini-batch.
    - Per `TabularDataset`, sono le dimensioni dei dati. Valori di esempio sono `1024`, `1024KB`, `10MB` e `1GB`. Il valore consigliato è `1MB`. Il mini-batch di `TabularDataset` non supererà mai i limiti dei file. Supponiamo ad esempio di avere una serie di file CSV di varie dimensioni, comprese tra 100 KB e 10 MB. Se si imposta `mini_batch_size = 1MB`, i file di dimensioni inferiori a 1 MB verranno considerati come un mini-batch. I file di dimensioni maggiori di 1 MB verranno suddivisi in più mini-batch.
- `error_threshold`: numero di errori di record per `TabularDataset` e di errori di file per `FileDataset` che devono essere ignorati durante l'elaborazione. Se il numero di errori per l'intero input supera questo valore, il processo viene interrotto. La soglia di errore è per l'intero input e non per il singolo mini-batch inviato al metodo `run()`. L'intervallo è `[-1, int.max]`. La parte `-1` indica di ignorare tutti gli errori durante l'elaborazione.
- `output_action`: uno dei valori seguenti indica come verrà organizzato l'output:
    - `summary_only`: l'output viene archiviato dallo script utente. `ParallelRunStep` userà l'output solo per il calcolo della soglia di errore.
    - `append_row`: per tutti gli input, nella cartella di output verrà creato un solo file in cui accodare tutti gli output separati per riga.
- `append_row_file_name`: per personalizzare il nome del file di output per append_row output_action (facoltativo; il valore predefinito è `parallel_run_step.txt`).
- `source_directory`: percorsi delle cartelle che contengono tutti i file da eseguire nella destinazione di calcolo (facoltativo).
- `compute_target`: È supportato solo `AmlCompute`.
- `node_count`: numero di nodi di calcolo da usare per l'esecuzione dello script utente.
- `process_count_per_node`: numero di processi per nodo. È consigliabile impostare questo valore sul numero di GPU o CPU di un nodo (facoltativo; il valore predefinito è `1`).
- `environment`: definizione dell'ambiente Python. È possibile configurarlo per usare un ambiente Python esistente o per configurare un ambiente temporaneo. La definizione è anche responsabile dell'impostazione delle dipendenze dell'applicazione necessarie (facoltativo).
- `logging_level`: livello di dettaglio del log. I valori in ordine crescente di livello di dettaglio sono: `WARNING`, `INFO` e `DEBUG`. (Facoltativo. Il valore predefinito è `INFO`)
- `run_invocation_timeout`: timeout della chiamata del metodo `run()` in secondi. (Facoltativo. Il valore predefinito è`60`)
- `run_max_try`: Numero massimo di tentativi di `run()` per un mini-batch. Una `run()` viene considerata non riuscita se viene generata un'eccezione o se non viene restituito nulla quando viene raggiunto `run_invocation_timeout` (facoltativo; il valore predefinito è `3`). 

È possibile specificare `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` e `run_max_try` come `PipelineParameter` in modo che, quando si ripete l'esecuzione della pipeline, sia possibile ottimizzare i valori dei parametri. In questo esempio si usa `PipelineParameter` per `mini_batch_size` e `Process_count_per_node` e questi valori verranno cambiati quando si ripete l'esecuzione in un secondo momento. 

In questo esempio si presuppone l'uso dello script `digit_identification.py` descritto in precedenza. Se si usa uno script personalizzato, modificare di conseguenza i parametri `source_directory` e `entry_script`.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Creare ParallelRunStep

Creare ParallelRunStep usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già collegata all'area di lavoro come destinazione di esecuzione dello script di inferenza. Usare `ParallelRunStep` per creare il passaggio della pipeline di inferenza batch, che accetta tutti i parametri seguenti:
- `name`: nome del passaggio, con le restrizioni di denominazione seguenti: univoco, 3-32 caratteri ed espressione regolare ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: oggetto `ParallelRunConfig`, come definito in precedenza.
- `inputs`: uno o più set di dati di Azure Machine Learning di tipo singolo da partizionare per l'elaborazione parallela.
- `side_inputs`: uno o più set di dati o dati di riferimento usati come input secondari senza necessità di partizionamento.
- `output`: oggetto `PipelineData` che corrisponde alla directory di output.
- `arguments`: elenco di argomenti passati allo script utente. Usare unknown_args per recuperarli nello script di immissione (facoltativo).
- `allow_reuse`: indica se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito con le stesse impostazioni o gli stessi input. Se il parametro è `False`, durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio (Facoltativo. Il valore predefinito è `True`).

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>Creare ed eseguire la pipeline

Eseguire ora la pipeline Creare prima di tutto un oggetto [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) usando il riferimento all'area di lavoro e il passaggio della pipeline creato. Il parametro `steps` è una matrice di passaggi. In questo caso, è presente un solo passaggio per l'inferenza batch. Per creare pipeline con più passaggi, inserire i passaggi nell'ordine corretto in questa matrice.

Usare quindi la funzione `Experiment.submit()` per inviare la pipeline per l'esecuzione.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorare il processo di inferenza batch

Il completamento di un processo di inferenza batch può richiedere molto tempo. Questo esempio ne monitora lo stato usando un widget Jupyter. È possibile monitorare lo stato del processo anche usando:

* Azure Machine Learning Studio. 
* Output della console dall'oggetto [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Ripetere l'esecuzione con nuovi parametri e input di dati

Dal momento che gli input e diverse configurazioni sono stati specificati come `PipelineParameter`, è possibile ripetere l'esecuzione dell'inferenza batch con un input di set di dati diverso e ottimizzare i parametri senza dover creare una pipeline completamente nuova. Si userà lo stesso archivio dati ma una sola immagine come input di dati.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>View the results

I risultati dell'esecuzione precedente vengono scritti nel `DataStore` specificato nell'oggetto `PipelineData` come dati di output, che in questo caso sono detti *inferenze*. I risultati vengono archiviati nel contenitore BLOB predefinito ed è possibile passare all'account di archiviazione e visualizzarli tramite Storage Explorer. Il percorso del file è azureml-blobstore-*GUID*/azureml/*RunId*/*output_dir*.

È anche possibile scaricare questi dati per visualizzare i risultati. Il codice di esempio seguente consente di visualizzare le prime 10 righe.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Passaggi successivi

Per vedere questo processo in funzione dall'inizio alla fine, provare il [notebook relativo all'inferenza batch](https://aka.ms/batch-inference-notebooks). 

Per indicazioni sul debug e la risoluzione dei problemi per le ParallelRunStep, vedere la [guida pratica](how-to-debug-parallel-run-step.md).

Per indicazioni sul debug e la risoluzione dei problemi per le pipeline, vedere la [guida pratica](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

