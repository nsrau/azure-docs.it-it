---
title: Eseguire l'inferenza batch su grandi quantità di dati
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere inferenze in modo asincrono su grandi quantità di dati tramite l'inferenza batch in Azure Machine Learning. L'inferenza batch offre funzionalità di elaborazione parallela predefinite ed è ottimizzata per la velocità effettiva elevata e per l'inferenza "fire-and-forget" per i casi d'uso relativi ai Big Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 4e7ddf7fc7b18d57b8251d4fb8293ed2f6e83d17
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929568"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Eseguire l'inferenza batch su grandi quantità di dati usando Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida pratica si apprenderà come ottenere inferenze su grandi quantità di dati in modo asincrono e in parallelo usando Azure Machine Learning. La funzionalità di inferenza batch descritta qui è disponibile in anteprima pubblica. Offre livelli elevati di prestazioni e velocità effettiva per generare inferenze ed elaborare i dati. Fornisce funzionalità asincrone predefinite.

Con l'inferenza di batch, è facile scalare le inferenze offline a grandi cluster di computer su terabyte di dati di produzione, ottenendo una maggiore produttività e ottimizzando i costi.

In questa guida pratica si apprenderà come eseguire le attività seguenti:

> * Creare una risorsa di calcolo remota.
> * Scrivere uno script di inferenza personalizzato.
> * Creare una [pipeline di Machine Learning](concept-ml-pipelines.md) per registrare un modello di classificazione delle immagini con training preliminare basato sul set di dati [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/). 
> * Usare il modello per eseguire l'inferenza batch sulle immagini di esempio disponibili nell'account di archiviazione BLOB di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per un avvio rapido guidato, completare l'[esercitazione relativa all'installazione](tutorial-1st-experiment-sdk-setup.md) se non si ha già un'area di lavoro di Azure Machine Learning o una macchina virtuale per notebook. 

* Per gestire l'ambiente in uso e le relative dipendenze, vedere la [guida pratica](how-to-configure-environment.md) sulla configurazione dell'ambiente. Eseguire `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` nell'ambiente per scaricare le dipendenze necessarie.

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

Le azioni seguenti configurano le risorse necessarie per eseguire una pipeline di inferenza batch:

- Creare un archivio dati che punti a un contenitore BLOB che include immagini di cui eseguire l'inferenza.
- Configurare i riferimenti ai dati come input e output per il passaggio della pipeline di inferenza batch.
- Configurare un cluster di elaborazione per eseguire il passaggio di inferenza batch.

### <a name="create-a-datastore-with-sample-images"></a>Creare un archivio dati con immagini di esempio

Ottenere il set di valutazione MNIST dal contenitore BLOB pubblico `sampledata` in un account denominato `pipelinedata`. Creare un archivio dati con il nome `mnist_datastore`, che punta a questo contenitore. Nella seguente chiamata a `register_azure_blob_container`, l'impostazione del flag `overwrite` su `True` sovrascrive eventuali archivi dati creati in precedenza con quel nome. 

È possibile modificare questo passaggio in modo da puntare al proprio contenitore BLOB fornendo valori personalizzati per `datastore_name`, `container_name` e `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

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

### <a name="configure-data-inputs-and-outputs"></a>Configurare gli input e gli output dei dati

A questo punto è necessario configurare gli input e gli output dei dati, tra cui:

- la directory che contiene le immagini di input.
- La directory in cui è archiviato il modello con training preliminare.
- La directory che contiene le etichette.
- La directory per l'output.

`Dataset` è una classe per l'esplorazione, la trasformazione e la gestione dei dati in Azure Machine Learning. Questa classe ha due tipi: `TabularDataset` e `FileDataset`. In questo esempio si userà `FileDataset` come input per il passaggio della pipeline di inferenza batch. 

> [!NOTE] 
> Attualmente il supporto di `FileDataset` nell'inferenza batch è limitato all'archivio BLOB di Azure. 

È anche possibile fare riferimento ad altri set di dati nello script di inferenza personalizzato. Ad esempio, è possibile usarlo per accedere alle etichette nello script per l'assegnazione di etichette alle immagini usando `Dataset.register` e `Dataset.get_by_name`.

Per altre informazioni sui set di dati di Azure Machine Learning, vedere [Creare e accedere ai set di dati (anteprima)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

Gli oggetti `PipelineData` vengono usati per trasferire i dati intermedi tra i passaggi della pipeline. In questo esempio l'oggetto viene usato per gli output di inferenza.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Configurare una destinazione di calcolo

In Azure Machine Learning il *calcolo* (o la *destinazione di calcolo*) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning. Eseguire il codice seguente per creare una destinazione [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) basata su CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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

Registrare quindi il modello con l'area di lavoro in modo che sia disponibile per la risorsa di calcolo remota.

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
- `init()`: usare questa funzione per qualsiasi preparazione dispendiosa o comune per un'inferenza successiva. Usarla ad esempio per caricare il modello in un oggetto globale.
-  `run(mini_batch)`: la funzione viene eseguita per ogni istanza di `mini_batch`.
    -  `mini_batch`: l'inferenza batch richiama il metodo Run e passa un elenco o un dataframe Pandas come argomento al metodo. Ogni voce in min_batch sarà un percorso file se l'input è un oggetto FileDataset oppure un dataframe Pandas se l'input è un oggetto TabularDataset.
    -  `response`: il metodo Run() deve restituire un dataframe Pandas o una matrice. Per append_row output_action, questi elementi restituiti vengono accodati nel file di output comune. Per summary_only, il contenuto degli elementi viene ignorato. Per tutte le azioni di output, ogni elemento di output restituito indica un'inferenza riuscita dell'elemento di input nel mini-batch di input. L'utente deve verificare che nel risultato dell'inferenza siano inclusi dati sufficienti per eseguire il mapping dell'input all'inferenza. L'output di inferenza verrà scritto nel file di output ma non sarà necessariamente in ordine, quindi l'utente dovrà usare una chiave nell'output per eseguirne il mapping all'input.

```python
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

## <a name="build-and-run-the-batch-inference-pipeline"></a>Compilare ed eseguire la pipeline di inferenza batch

Ora si dispone di tutti gli elementi necessari per creare la pipeline.

### <a name="prepare-the-run-environment"></a>Preparare l'ambiente di esecuzione

Specificare prima di tutto le dipendenze per lo script. Si userà questo oggetto in un secondo momento, durante la creazione del passaggio della pipeline.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_CPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Specificare i parametri per il passaggio della pipeline di inferenza batch

`ParallelRunConfig` è la configurazione principale per la nuova istanza `ParallelRunStep` di inferenza batch introdotta all'interno della pipeline di Azure Machine Learning. Si usa per eseguire il wrapping dello script e configurare i parametri necessari, inclusi tutti gli elementi seguenti:
- `entry_script`: script utente come percorso di file locale che verrà eseguito in parallelo su più nodi. Se `source_directly` è presente, usare un percorso relativo. In caso contrario, usare qualsiasi percorso accessibile nel computer.
- `mini_batch_size`: dimensioni del mini-batch passato a una singola chiamata di `run()` (facoltativo, il valore predefinito è `1`).
    - Per `FileDataset`, è il numero di file con un valore minimo di `1`. È possibile combinare più file in un solo mini-batch.
    - Per `TabularDataset`, sono le dimensioni dei dati. Valori di esempio sono `1024`, `1024KB`, `10MB` e `1GB`. Il valore consigliato è `1MB`. Si noti che il mini-batch di `TabularDataset` non supererà mai i limiti dei file. Supponiamo ad esempio di avere una serie di file CSV di varie dimensioni, comprese tra 100 KB e 10 MB. Se si imposta `mini_batch_size = 1MB`, i file di dimensioni inferiori a 1 MB verranno considerati come un mini-batch. I file di dimensioni maggiori di 1 MB verranno suddivisi in più mini-batch.
- `error_threshold`: numero di errori di record per `TabularDataset` e di errori di file per `FileDataset` che devono essere ignorati durante l'elaborazione. Se il numero di errori per l'intero input supera questo valore, il processo viene arrestato. La soglia di errore è per l'intero input e non per i singoli mini-batch inviati al metodo `run()`. L'intervallo è `[-1, int.max]`. La parte `-1` indica di ignorare tutti gli errori durante l'elaborazione.
- `output_action`: uno dei valori seguenti indica come verrà organizzato l'output:
    - `summary_only`: l'output viene archiviato dallo script utente. `ParallelRunStep` userà l'output solo per il calcolo della soglia di errore.
    - `append_row`: per tutti i file di input, nella cartella di output verrà creato un solo file per accodare tutti gli output separati per riga. Il nome file sarà parallel_run_step.txt.
- `source_directory`: percorsi delle cartelle che contengono tutti i file da eseguire nella destinazione di calcolo (facoltativo).
- `compute_target`: È supportato solo `AmlCompute`.
- `node_count`: numero di nodi di calcolo da usare per l'esecuzione dello script utente.
- `process_count_per_node`: numero di processi per nodo.
- `environment`: definizione dell'ambiente Python. È possibile configurarlo per usare un ambiente Python esistente o per configurare un ambiente temporaneo per l'esperimento. La definizione è anche responsabile dell'impostazione delle dipendenze dell'applicazione necessarie (facoltativo).
- `logging_level`: livello di dettaglio del log. I valori in ordine crescente di livello di dettaglio sono: `WARNING`, `INFO` e `DEBUG`. Il valore predefinito è `INFO` (facoltativo).
- `run_invocation_timeout`: timeout della chiamata del metodo `run()` in secondi. Il valore predefinito è `60`.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Creare il passaggio della pipeline

Creare il passaggio della pipeline usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già collegata all'area di lavoro come destinazione di esecuzione dello script. Usare `ParallelRunStep` per creare il passaggio della pipeline di inferenza batch, che accetta tutti i parametri seguenti:
- `name`: nome del passaggio, con le restrizioni di denominazione seguenti: univoco, 3-32 caratteri ed espressione regolare ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: zero o più nomi di modello già registrati nel registro dei modelli di Azure Machine Learning.
- `parallel_run_config`: oggetto `ParallelRunConfig`, come definito in precedenza.
- `inputs`: uno o più set di dati di Azure Machine Learning di tipo singolo.
- `output`: oggetto `PipelineData` che corrisponde alla directory di output.
- `arguments`: elenco di argomenti passati allo script utente (facoltativo).
- `allow_reuse`: indica se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito con le stesse impostazioni o gli stessi input. Se il parametro è `False`, durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio (facoltativo, il valore predefinito è `True`).

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>Eseguire la pipeline

Eseguire ora la pipeline Creare prima di tutto un oggetto `Pipeline` usando il riferimento all'area di lavoro e il passaggio della pipeline creato. Il parametro `steps` è una matrice di passaggi. In questo caso, è presente un solo passaggio per l'assegnazione di punteggi in batch. Per creare pipeline con più passaggi, inserire i passaggi nell'ordine corretto in questa matrice.

Usare quindi la funzione `Experiment.submit()` per inviare la pipeline per l'esecuzione.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorare il processo di inferenza batch

Il completamento di un processo di inferenza batch può richiedere molto tempo. Questo esempio ne monitora lo stato usando un widget Jupyter. È possibile gestire lo stato del processo anche usando:

* Azure Machine Learning Studio. 
* Output della console dall'oggetto [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Passaggi successivi

Per vedere questo processo in funzione dall'inizio alla fine, provare il [notebook relativo all'inferenza batch](https://aka.ms/batch-inference-notebooks). 

Per indicazioni sul debug e la risoluzione dei problemi per le pipeline, vedere la [guida pratica](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

