---
title: "Esercitazione: Pipeline di Azure Machine Learning per l'assegnazione del punteggio batch"
titleSuffix: Azure Machine Learning
description: Compilare una pipeline di Machine Learning per l'assegnazione del punteggio batch in un modello di classificazione delle immagini. Le pipeline di Machine Learning ottimizzano il flusso di lavoro offrendo velocità, portabilità e possibilità di riutilizzo e consentendo così di concentrarsi sulle proprie competenze di apprendimento automatico, anziché sull'infrastruttura e l'automazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 15a11ba74262ec5a354f0cb3fe22c09167c8d5a6
ms.sourcegitcommit: d15b23e23328ce7502dd3d2846b49fd2d6d8209c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005385"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch

In questa esercitazione si usano le pipeline di Azure Machine Learning per eseguire un processo di assegnazione del punteggio batch. Questo esempio usa il modello Tensorflow di rete neurale convoluzionale [Inception-V3](https://arxiv.org/abs/1512.00567), già sottoposto a training, per classificare le immagini senza etichetta. Dopo la compilazione e la pubblicazione di una pipeline, si configura un endpoint REST in modo da poter attivare la pipeline da qualsiasi libreria HTTP su qualsiasi piattaforma.

Le pipeline di Machine Learning ottimizzano il flusso di lavoro offrendo velocità, portabilità e possibilità di riutilizzo e consentendo così di concentrarsi sulle proprie competenze di apprendimento automatico, anziché sull'infrastruttura e l'automazione. [Altre informazioni sulle pipeline di Machine Learning](concept-ml-pipelines.md).

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Configurare l'area di lavoro e scaricare i dati di esempio
> * Creare oggetti dati per recuperare e restituire i dati
> * Scaricare, preparare e registrare il modello nell'area di lavoro
> * Effettuare il provisioning di destinazioni di calcolo e creare uno script di assegnazione del punteggio
> * Compilare, eseguire e pubblicare una pipeline
> * Abilitare un endpoint REST per la pipeline

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Completare la [parte 1 dell'esercitazione relativa all'installazione](tutorial-1st-experiment-sdk-setup.md) se non si ha già un'area di lavoro di Azure Machine Learning o una macchina virtuale per notebook.
* Al termine dell'esercitazione sull'installazione, aprire il notebook **tutorials/tutorial-pipeline-batch-scoring-classification.ipynb** usando lo stesso server notebook.

Questa esercitazione è disponibile anche in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se si vuole eseguirla nel proprio [ambiente locale](how-to-configure-environment.md#local). Eseguire `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` per ottenere i pacchetti necessari.

## <a name="configure-workspace-and-create-datastore"></a>Configurare l'area di lavoro e creare l'archivio dati

Creare un oggetto area di lavoro dall'area di lavoro di Azure Machine Learning esistente. 
+ Un'[area di lavoro](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud per monitorare le esecuzioni del modello e tenerne traccia. 

+ `Workspace.from_config()` legge il file **config.json** e carica i dettagli di autenticazione in un oggetto denominato `ws`. `ws` viene usato in tutta la parte restante del codice in questa esercitazione.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Creare un archivio dati per le immagini di esempio

Ottenere l'esempio di dati pubblici di valutazione di ImageNet dal contenitore BLOB pubblico `sampledata` nell'account `pipelinedata`. La chiamata a `register_azure_blob_container()` rende i dati disponibili per l'area di lavoro con il nome `images_datastore`. Specificare quindi l'archivio dati predefinito dell'area di lavoro come archivio dati di output, che verrà usato per assegnare un punteggio all'output nella pipeline.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Creare gli oggetti dati

Durante la compilazione delle pipeline vengono usati oggetti `DataReference` per leggere i dati dagli archivi dati dell'area di lavoro e oggetti `PipelineData` per trasferire i dati intermedi tra i passaggi della pipeline.

> [!Important]
> Questo esempio di assegnazione del punteggio batch usa solo un passaggio della pipeline, ma nei casi d'uso con più passaggi il flusso tipico include:
>
> 1. Uso di oggetti `DataReference` come **input** per recuperare i dati non elaborati, esecuzione di alcune trasformazioni e quindi restituzione di un oggetto `PipelineData` come **output**.
>
> 2. Uso dell'**oggetto di output** `PipelineData` del passaggio precedente come *oggetto di input*, ripetuto per i passaggi successivi.

Per questo scenario vengono creati oggetti `DataReference` corrispondenti alle directory dell'archivio dati per le immagini di input e le etichette di classificazione (valori y-test). Viene anche creato un oggetto `PipelineData` per i dati di output del punteggio batch.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Scaricare e registrare il modello

Scaricare il modello Tensorflow con training preliminare per usarlo per l'assegnazione del punteggio batch nella pipeline. Creare prima di tutto una directory locale in cui archiviare il modello, quindi scaricarlo ed estrarlo.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registrare ora il modello nell'area di lavoro, in modo da poterlo recuperare facilmente nel processo della pipeline. Nella funzione statica `register()` il parametro `model_name` è la chiave usata per individuare il modello nell'SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Creare e collegare la destinazione di calcolo remota

Poiché le pipeline di Machine Learning non possono essere eseguite localmente, è necessario eseguirle in risorse cloud. Queste risorse sono le destinazioni di calcolo remote, ovvero ambienti di calcolo virtuali riutilizzabili in cui si eseguono esperimenti e flussi di lavoro di Machine Learning. Eseguire il codice seguente per creare una destinazione [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) abilitata per la GPU e collegarla all'area di lavoro. Per altre informazioni sulle destinazioni di calcolo, vedere l'[articolo concettuale](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Scrivere uno script di assegnazione del punteggio

Per eseguire l'assegnazione del punteggio, occorre creare lo script `batch_scoring.py` e scriverlo nella directory corrente. Lo script recupera le immagini di input, applica il modello di classificazione e restituisce le stime in un file di risultati.

Lo script `batch_scoring.py` accetta i parametri seguenti, che vengono passati dal passaggio della pipeline creato più avanti in questa esercitazione:

- `--model_name`: nome del modello usato
- `--label_dir`: directory che contiene il file `labels.txt` 
- `--dataset_path`: directory che contiene le immagini di input
- `--output_dir`: lo script eseguirà il modello sui dati e restituirà un `results-label.txt` in questa directory
- `--batch_size`: dimensioni del batch usate nell'esecuzione del modello

L'infrastruttura delle pipeline usa la classe `ArgumentParser` per passare i parametri nei passaggi della pipeline. Ad esempio, nel codice seguente al primo argomento `--model_name` viene assegnato l'identificatore della proprietà `model_name`. Nella funzione `main()` si accede a questa proprietà usando `Model.get_model_path(args.model_name)`.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

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
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> La pipeline in questa esercitazione ha un solo passaggio e scrive l'output in un file, ma per le pipeline con più passaggi si usa anche `ArgumentParser` per definire una directory in cui scrivere i dati di output per l'input dei passaggi successivi. Vedere il [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) per un esempio di come passare i dati tra più passaggi della pipeline tramite il modello di progettazione `ArgumentParser`.

## <a name="build-and-run-the-pipeline"></a>Compilare ed eseguire la pipeline

Prima di eseguire la pipeline occorre creare un oggetto che definisce l'ambiente Python e le dipendenze necessarie per lo script `batch_scoring.py`. La dipendenza principale necessaria è Tensorflow, ma occorre installare anche `azureml-defaults` per i processi in background dall'SDK. Creare un oggetto `RunConfiguration` usando le dipendenze e specificare anche il supporto di Docker e Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Impostare parametri per la pipeline

Definire un parametro personalizzato per la pipeline per controllare le dimensioni del batch. Dopo che la pipeline è stata pubblicata ed esposta tramite un endpoint REST, anche tutti i parametri configurati vengono esposti e possono essere specificati nel payload JSON durante la riesecuzione della pipeline con una richiesta HTTP.

Creare un oggetto `PipelineParameter` per abilitare questo comportamento e definire un nome e un valore predefinito.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Creare il passaggio della pipeline

Un passaggio della pipeline è un oggetto che incapsula tutti gli elementi necessari per l'esecuzione di una pipeline, tra cui:

* Impostazioni dell'ambiente e delle dipendenze
* Risorsa di calcolo in cui eseguire la pipeline
* Dati di input e di output ed eventuali parametri personalizzati
* Riferimento a uno script o alla logica SDK da eseguire durante il passaggio

Sono disponibili diverse classi che ereditano dalla classe padre [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) che possono essere usate per facilitare la compilazione di un passaggio con determinati framework e stack. In questo esempio si usa la classe [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) per definire la logica del passaggio usando uno script Python personalizzato. Tenere presente che se un argomento dello script è un input per il passaggio o l'output del passaggio, deve essere definito **sia** nella matrice `arguments` **sia** nel parametro `input` o `output` rispettivamente. 

Un riferimento a un oggetto nella matrice `outputs` diventa disponibile come **input** per un passaggio successivo della pipeline, per gli scenari in cui sono presenti più passaggi.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Per un elenco di tutte le classi per i diversi tipi di passaggio, vedere il [pacchetto dei passaggi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Eseguire la pipeline

A questo punto si può eseguire la pipeline. Creare prima di tutto un oggetto `Pipeline` con il riferimento all'area di lavoro e il passaggio della pipeline creato. Il parametro `steps` è una matrice di passaggi e in questo caso è presente un solo passaggio per il punteggio batch. Per compilare pipeline con più passaggi, inserire i passaggi nell'ordine corretto in questa matrice.

Usare quindi la funzione `Experiment.submit()` per inviare la pipeline per l'esecuzione. Specificare anche il parametro personalizzato `param_batch_size`. La funzione `wait_for_completion` restituirà i log durante il processo di compilazione della pipeline, in modo da poter visualizzare lo stato di avanzamento corrente.

> [!IMPORTANT]
> La prima esecuzione della pipeline impiega circa **15 minuti**, perché devono essere scaricate tutte le dipendenze, quindi viene creata un'immagine Docker e viene effettuato il provisioning o la creazione dell'ambiente Python. La riesecuzione della pipeline richiede molto meno tempo, in quanto le risorse vengono riutilizzate. Il tempo di esecuzione totale dipende tuttavia dal carico di lavoro degli script e dai processi in esecuzione in ogni passaggio della pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Scaricare ed esaminare l'output

Eseguire il codice seguente per scaricare il file di output creato dallo script `batch_scoring.py`, quindi esplorare i risultati dei punteggi.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Nome file</th>
      <th>Previsione</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>Rhodesian ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>Treppiede</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>Tastiera di macchina da scrivere</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>Silky terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>Cravatta Windsor</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>Opilionide</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>Violino</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>Altoparlante</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>Grembiule</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>Aragosta americana</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-rest-endpoint"></a>Pubblicare ed eseguire la pipeline dall'endpoint REST

Eseguire il codice seguente per pubblicare la pipeline nell'area di lavoro. Nell'area di lavoro nel portale è possibile visualizzare i metadati della pipeline, incluse la cronologia di esecuzione e le durate. È anche possibile eseguire la pipeline manualmente dal portale.

Inoltre, la pubblicazione della pipeline consente a un endpoint REST di eseguire di nuovo la pipeline da qualsiasi libreria HTTP su qualsiasi piattaforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Per eseguire la pipeline dall'endpoint REST occorre prima di tutto un'intestazione di autenticazione di tipo connessione OAuth2. Questo esempio usa l'autenticazione interattiva a scopo illustrativo, ma per la maggior parte degli scenari di produzione che richiedono l'autenticazione automatica o headless, usare l'autenticazione basata su entità servizio, come [descritto in questo notebook](https://aka.ms/pl-restep-auth).

L'autenticazione basata su entità servizio prevede la creazione di una **registrazione dell'app** in **Azure Active Directory**, la generazione di un segreto client e la concessione all'entità servizio dell'**accesso in base al ruolo** all'area di lavoro di Machine Learning. Si usa quindi la classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) per gestire il flusso di autenticazione. 

Sia `InteractiveLoginAuthentication` che `ServicePrincipalAuthentication` ereditano da `AbstractAuthentication` e in entrambi i casi si usa la funzione `get_authentication_header()` nello stesso modo per recuperare l'intestazione.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Ottenere l'URL REST dalla proprietà `endpoint` dell'oggetto pipeline pubblicato. È possibile trovare l'URL REST anche nell'area di lavoro nel portale. Compilare una richiesta HTTP POST all'endpoint, specificando l'intestazione di autenticazione. Aggiungere inoltre un oggetto payload JSON con il nome dell'esperimento e il parametro batch_size. Tenere presente che `param_batch_size` viene passato allo script `batch_scoring.py` perché è stato definito come oggetto `PipelineParameter` nella configurazione del passaggio.

Eseguire la richiesta per attivare l'esecuzione. Accedere alla chiave `Id` dal dizionario delle risposte per ottenere il valore dell'ID esecuzione.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Usare l'ID esecuzione per monitorare lo stato della nuova esecuzione. L'esecuzione richiederà altri 10-15 minuti e sarà simile all'esecuzione precedente della pipeline, quindi, se non è necessario visualizzare un'altra esecuzione della pipeline, è possibile evitare di esaminare l'output completo.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende eseguire altre esercitazioni su Azure Machine Learning, non completare questa sezione.

### <a name="stop-the-notebook-vm"></a>Arrestare la macchina virtuale per notebook

Se si è usato un server notebook cloud, per ridurre i costi arrestare la macchina virtuale per notebook quando non viene usata.

1. Nell'area di lavoro selezionare **Macchine virtuali per notebook**.
1. Selezionare la macchina virtuale dall'elenco.
1. Selezionare **Arresta**.
1. Quando si è pronti a usare di nuovo il server, selezionare **Avvia**.

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminarle per evitare addebiti.

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.
1. Nell'elenco selezionare il gruppo di risorse creato.
1. Selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sulle pipeline di Machine Learning sono state eseguite le attività seguenti:

> [!div class="checklist"]
> * È stata compilata una pipeline con dipendenze di ambiente da eseguire in una risorsa di calcolo GPU remota
> * È stato creato uno script di assegnazione dei punteggi per l'esecuzione di stime batch con un modello Tensorflow con training preliminare
> * È stata pubblicata una pipeline e la si è abilitata per l'esecuzione da un endpoint REST

Per altri esempi di creazione di pipeline con Machine Learning SDK, vedere il [repository dei notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
