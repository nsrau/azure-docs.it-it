---
title: Eseguire previsioni di batch su grandi quantità di dati
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire previsioni di batch in modo asincrono su grandi quantità di dati tramite il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4c38230c1d5a4dcad9a10b82d810f82515980076
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245872"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Eseguire previsioni di batch su set di dati di grandi dimensioni con il servizio Azure Machine Learning

Questo articolo illustra come eseguire previsioni su grandi quantità di dati in modo asincrono usando il servizio Azure Machine Learning.

La previsione di batch (o punteggio batch) offre inferenza conveniente con velocità effettiva senza precedenti per le applicazioni asincrone. Le pipeline di previsione di batch possono essere scalate per eseguire l'inferenza su terabyte di dati di produzione. La previsione di batch è ottimizzata per previsioni fire-and-forget a velocità effettiva elevata per un'ampia raccolta di dati.

>[!TIP]
> Se il sistema richiede l'elaborazione a bassa latenza (per elaborare rapidamente un singolo documento o un set di documenti di dimensioni ridotte), usare il [punteggio in tempo reale](how-to-consume-web-service.md) invece della previsione di batch.

Questa procedura illustra come creare una [pipeline di Machine Learning](concept-ml-pipelines.md) per registrare un modello di visione artificiale con training preliminare ([InceptionV3](https://arxiv.org/abs/1512.00567)). Usare il modello con training preliminare per assegnare il punteggio di batch alle immagini disponibili nell'account di archiviazione BLOB di Azure. Queste immagini usate per il punteggio sono immagini senza etichetta del set di dati [ImageNet](http://image-net.org/).

## <a name="prerequisites"></a>Prerequisiti

- Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

- Configurare un ambiente di sviluppo per installare l'SDK di Azure Machine Learning. Per altre informazioni, vedere [Configurare un ambiente di sviluppo per Azure Machine Learning](how-to-configure-environment.md).

- Creare un'area di lavoro di Azure Machine Learning che conterrà tutte le risorse di pipeline. È possibile usare il codice seguente. Per altre opzioni, vedere [Create a workspace configuration file](how-to-configure-environment.md#workspace) (Creare un file di configurazione dell'area di lavoro).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

La procedura seguente illustra come configurare le risorse necessarie per eseguire una pipeline:

- Accedere all'archivio dati che contiene già il modello con training preliminare, le etichette di input e le immagini da valutare (già impostati per l'utente).
- Configurare un archivio dati per archiviare l'output.
- Configurare gli oggetti  `DataReference`  affinché puntino ai dati degli archivi di dati precedenti.
- Configurare i computer di calcolo o i cluster in cui verrà eseguita la procedura di pipeline.

### <a name="access-the-datastores"></a>Accedere agli archivi dati

Accedere prima di tutto all'archivio dati che include il modello, le etichette e le immagini.

Si usa un contenitore BLOB pubblico denominato *sampledata* nell'account *pipelinedata* che contiene le immagini del set di valutazione ImageNet. Il nome dell'archivio dati per questo contenitore pubblico è *images_datastore*. Registrare questo archivio dati con l'area di lavoro:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Quindi effettuare la configurazione in modo da usare l'archivio dati predefinito per gli output.

Quando si crea la propria area di lavoro, per impostazione predefinita vengono collegati all'area [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [Archivio BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) . File di Azure è l'archivio dati predefinito per un'area di lavoro, ma come archivio dati è possibile usare anche la risorsa di archiviazione BLOB. Per altre informazioni,consultare [Azure storage options](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks) (Opzioni di archiviazione di Azure).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Configurare i riferimenti ai dati

Fare a questo punto riferimento ai dati nella pipeline come input per i passaggi della pipeline.

Un'origine dati in una pipeline è rappresentata da un oggetto [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) . L'oggetto  `DataReference`  punta ai dati che si trovano in un archivio dati o che sono accessibili da tale archivio. Gli oggetti `DataReference`  sono necessari per la directory usata per le immagini di input, la directory in cui è archiviato il modello con training preliminare, la directory per le etichette e la directory di output.

```python
input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")
                           
model_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")                          
                         
label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")                          
                         
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Configurare la destinazione di calcolo

In Azure Machine Learning il *calcolo* (o la *destinazione di calcolo*) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning. È ad esempio possibile creare un `Azure Machine Learning compute`.

```python
compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, 
                        compute_name, 
                        provisioning_config)
    
    compute_target.wait_for_completion(
                     show_output=True, 
                     min_node_count=None, 
                     timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Preparare il modello

Prima di poter usare il modello con training preliminare, è necessario scaricarlo e registrarlo con l'area di lavoro.

### <a name="download-the-pretrained-model"></a>Scaricare il modello con training preliminare

Scaricare il modello di visione artificiale con training preliminare (InceptionV3) da <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Quindi estrarre il file nella sottocartella `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registrare il modello

Come effettuare la registrazione del modello:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Scrivere lo script di assegnazione dei punteggi

>[!Warning]
>Il codice seguente è solo un esempio del contenuto del file [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) usato dal [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). È necessario creare lo script di assegnazione dei punteggi per il proprio scenario.

Lo script `batch_score.py` prende le immagini di input in *dataset_path*, i modelli con training preliminare in  *model_dir,* e genera *results-label.txt* in *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here
  
def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
            
        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Creare ed eseguire la pipeline di assegnazione punteggio batch

Ora che si dispone di tutti gli elementi necessari a creare la pipeline, occorre metterli tutti insieme.

### <a name="prepare-the-run-environment"></a>Preparare l'ambiente di esecuzione

Specificare le dipendenze conda per lo script. Questo oggetto sarà necessario in un secondo momento, durante la creazione del passaggio della pipeline.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Specificare il parametro per la pipeline

Creare un parametro della pipeline usando un oggetto  [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)  con un valore predefinito.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Creare il passaggio della pipeline

Creare il passaggio della pipeline usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già aggiunta all'area di lavoro come destinazione di esecuzione dello script. Usare [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) per creare il passaggio della pipeline.

```python
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Eseguire la pipeline

Adesso eseguire la pipeline ed esaminare l'output prodotto. L'output avrà un punteggio corrispondente a ogni immagine di input.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Pubblicare la pipeline

Se si è soddisfatti del risultato dell'esecuzione, pubblicare la pipeline in modo da poterla eseguire in un secondo momento con altri valori di input. Quando si pubblica una pipeline, si ottiene un endpoint REST. Questo endpoint accetta la chiamata della pipeline con il set di parametri già incorporati usando [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Eseguire di nuovo la pipeline usando l'endpoint REST

Per eseguire di nuovo la pipeline, è necessario un token di intestazione di autenticazione Azure Active Directory come descritto nell'articolo sulla classe [AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare questo end-to-end funzionante, provare il notebook di assegnazione punteggio batch di [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

