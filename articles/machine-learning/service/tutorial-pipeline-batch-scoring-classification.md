---
title: "Esercitazione: Pipeline di Machine Learning per l'assegnazione di punteggi in batch"
titleSuffix: Azure Machine Learning
description: Creare una pipeline di Machine Learning per l'assegnazione di punteggi in batch con un modello di classificazione delle immagini in Azure Machine Learning. Le pipeline di Machine Learning ottimizzano il flusso di lavoro offrendo velocità, portabilità e possibilità di riutilizzo e consentendo così di concentrarsi sulle proprie competenze di Machine Learning anziché sull'infrastruttura e l'automazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: f693a80726c9185bbd75d5fb99eb7e5f3ccad987
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493486"
---
# <a name="build--use-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Creare e usare una pipeline di Azure Machine Learning per l'assegnazione di punteggi in batch

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa esercitazione si usa una pipeline di Azure Machine Learning per eseguire un processo di assegnazione di punteggi in batch. Questo esempio usa il modello Tensorflow di rete neurale convoluzionale [Inception-V3](https://arxiv.org/abs/1512.00567), già sottoposto a training, per classificare le immagini senza etichetta. Dopo aver creato e pubblicato una pipeline, configurare un endpoint REST da usare per attivare la pipeline da qualsiasi libreria HTTP in qualsiasi piattaforma.

Le pipeline di Machine Learning ottimizzano il flusso di lavoro offrendo velocità, portabilità e possibilità di riutilizzo e consentendo così di concentrarsi sulle proprie competenze di Machine Learning anziché sull'infrastruttura e l'automazione. [Altre informazioni sulle pipeline di Machine Learning](concept-ml-pipelines.md).

In questa esercitazione si completano le attività seguenti:

> [!div class="checklist"]
> * Configurare l'area di lavoro e scaricare i dati di esempio
> * Creare oggetti dati per recuperare e restituire i dati
> * Scaricare, preparare e registrare il modello nell'area di lavoro
> * Effettuare il provisioning di destinazioni di calcolo e creare uno script di assegnazione del punteggio
> * Compilare, eseguire e pubblicare una pipeline
> * Abilitare un endpoint REST per la pipeline

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha già un'area di lavoro di Azure Machine Learning o una macchina virtuale per notebook, completare la [parte 1 dell'esercitazione sull'installazione](tutorial-1st-experiment-sdk-setup.md).
* Al termine dell'esercitazione sull'installazione, usare lo stesso server notebook per aprire il notebook *tutorials/tutorial-pipeline-batch-scoring-classification.ipynb*.

Se si vuole eseguire l'esercitazione sull'installazione nell'[ambiente locale](how-to-configure-environment.md#local), è possibile accedervi in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Eseguire `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` per ottenere i pacchetti necessari.

## <a name="configure-workspace-and-create-a-datastore"></a>Configurare l'area di lavoro e creare un archivio dati

Creare un oggetto area di lavoro dall'area di lavoro di Azure Machine Learning esistente.

- Un'[area di lavoro](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud che è possibile usare per monitorare le esecuzioni del modello e tenerne traccia. 
- `Workspace.from_config()` legge il file `config.json` e quindi carica i dettagli di autenticazione in un oggetto denominato `ws`. L'oggetto `ws` viene usato nel codice nell'intera esercitazione.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Creare un archivio dati per le immagini di esempio

Nell'account `pipelinedata` ottenere l'esempio di dati pubblici di valutazione di ImageNet dal contenitore BLOB pubblico `sampledata`. Chiamare `register_azure_blob_container()` per rendere i dati disponibili per l'area di lavoro con il nome `images_datastore`. Quindi impostare l'archivio dati predefinito dell'area di lavoro come archivio dati di output. Usare l'archivio dati di output per assegnare punteggi all'output nella pipeline.

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

Quando si crea una pipeline, un oggetto `DataReference` legge i dati dall'archivio dati dell'area di lavoro. Un oggetto `PipelineData` trasferisce i dati intermedi tra i passaggi della pipeline.

> [!Important]
> L'esempio di assegnazione di punteggi in batch di questa esercitazione usa un solo passaggio della pipeline. Nei casi d'uso con più passaggi, il flusso tipico includerà questi passaggi:
>
> 1. Usare gli oggetti `DataReference` come *input* per recuperare dati non elaborati, eseguire qualche trasformazione e quindi generare come *output* un oggetto `PipelineData`.
>
> 2. Usare l'*oggetto di output* `PipelineData` del passaggio precedente come *oggetto di input*. Ripetere questa operazione per i passaggi successivi.

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

Scaricare il modello Tensorflow già sottoposto a training per usarlo per l'assegnazione di punteggi in batch nella pipeline. Creare prima di tutto una directory locale in cui archiviare il modello. Quindi scaricarlo ed estrarlo.

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

## <a name="create-and-attach-the-remote-compute-target"></a>Creare e collegare la destinazione di calcolo remota

Le pipeline di Machine Learning non possono essere eseguite in locale, quindi è necessario eseguirle in risorse cloud o in *destinazioni di calcolo remote*. Una destinazione di calcolo remota è un ambiente di calcolo virtuale riutilizzabile in cui eseguire esperimenti e flussi di lavoro di Machine Learning. 

Eseguire il codice seguente per creare una destinazione [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) abilitata per la GPU e collegarla all'area di lavoro. Per altre informazioni sulle destinazioni di calcolo, vedere l'[articolo concettuale](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


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

Per l'assegnazione di punteggi, creare uno script di assegnazione di punteggi in batch, denominato `batch_scoring.py`, e scriverlo nella directory corrente. Lo script recupera le immagini di input, applica il modello di classificazione e restituisce le previsioni in un file di risultati.

Lo script `batch_scoring.py` accetta i parametri seguenti, che vengono passati dal passaggio della pipeline creato più avanti in questa esercitazione:

- `--model_name`: il nome del modello usato.
- `--label_dir`: la directory che contiene il file `labels.txt`.
- `--dataset_path`: la directory che contiene le immagini di input.
- `--output_dir`: la directory di output del file `results-label.txt` dopo che lo script avrà eseguito il modello sui dati.
- `--batch_size`: le dimensioni del batch usato nell'esecuzione del modello.

L'infrastruttura delle pipeline usa la classe `ArgumentParser` per passare i parametri nei passaggi della pipeline. Ad esempio, nel codice seguente al primo argomento `--model_name` viene assegnato l'identificatore della proprietà `model_name`. Nella funzione `main()` si usa `Model.get_model_path(args.model_name)` per accedere a questa proprietà.


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
> La pipeline di questa esercitazione include un solo passaggio e scrive l'output in un file. Per le pipeline con più passaggi, si usa anche `ArgumentParser` per definire una directory in cui scrivere i dati di output come input per i passaggi successivi. Per un esempio del passaggio di dati tra più passaggi della pipeline usando il modello di progettazione `ArgumentParser`, vedere il [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Compilare ed eseguire la pipeline

Prima di eseguire la pipeline, creare un oggetto che definisce l'ambiente Python e le dipendenze necessarie per lo script `batch_scoring.py`. La dipendenza principale necessaria è Tensorflow, ma occorre installare anche `azureml-defaults` dall'SDK per i processi in background. Creare un oggetto `RunConfiguration` usando le dipendenze. Specificare inoltre il supporto di Docker e della GPU Docker.

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

Definire un parametro personalizzato per la pipeline per controllare le dimensioni del batch. Una volta pubblicata la pipeline ed esposta tramite un endpoint REST, vengono esposti anche tutti i parametri configurati. È possibile specificare parametri personalizzati nel payload JSON quando si esegue di nuovo la pipeline tramite una richiesta HTTP.

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

Più classi ereditano dalla classe padre [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). È possibile scegliere le classi per usare specifici framework o stack per creare un passaggio. In questo esempio si usa la classe [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) per definire la logica del passaggio usando uno script Python personalizzato. Se un argomento dello script è un input per il passaggio o un output del passaggio, deve essere definito *sia* nella matrice `arguments` *sia* nel parametro `input` o `output` rispettivamente. 

Negli scenari in cui sono presenti più passaggi, un riferimento a un oggetto nella matrice `outputs` diventa disponibile come *input* per un passaggio successivo della pipeline.

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

Per un elenco di tutte le classi che è possibile usare per i diversi tipi di passaggio, vedere il [pacchetto dei passaggi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Eseguire la pipeline

Eseguire ora la pipeline Creare prima di tutto un oggetto `Pipeline` usando il riferimento all'area di lavoro e il passaggio della pipeline creato. Il parametro `steps` è una matrice di passaggi. In questo caso, è presente un solo passaggio per l'assegnazione di punteggi in batch. Per creare pipeline con più passaggi, inserire i passaggi nell'ordine corretto in questa matrice.

Usare quindi la funzione `Experiment.submit()` per inviare la pipeline per l'esecuzione. Specificare anche il parametro personalizzato `param_batch_size`. La funzione `wait_for_completion` genera log durante il processo di creazione della pipeline. È possibile usarli per visualizzare lo stato di avanzamento corrente.

> [!IMPORTANT]
> La prima esecuzione della pipeline richiede circa *15 minuti*. Devono essere scaricate tutte le dipendenze, quindi viene creata un'immagine Docker e viene effettuato il provisioning o la creazione dell'ambiente Python. Una seconda esecuzione della pipeline richiede molto meno tempo, perché queste risorse vengono riutilizzate e non create. Tuttavia, il tempo di esecuzione totale dipende dal carico di lavoro degli script e dai processi in esecuzione in ogni passaggio della pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Scaricare ed esaminare l'output

Eseguire il codice seguente per scaricare il file di output creato dallo script `batch_scoring.py`. Quindi esplorare i risultati dell'assegnazione di punteggi.

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
      <td>Rhodesian Ridgeback</td>
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

## <a name="publish-and-run-from-a-rest-endpoint"></a>Pubblicare ed eseguire la pipeline da un endpoint REST

Eseguire il codice seguente per pubblicare la pipeline nell'area di lavoro. Nell'area di lavoro in Azure Machine Learning Studio è possibile visualizzare i metadati della pipeline, incluse la cronologia di esecuzione e le durate. È anche possibile eseguire la pipeline manualmente da Machine Learning Studio.

La pubblicazione della pipeline consente a un endpoint REST di eseguire di nuovo la pipeline da qualsiasi libreria HTTP in qualsiasi piattaforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Per eseguire la pipeline dall'endpoint REST, è necessaria un'intestazione di autenticazione di tipo connessione OAuth2. L'esempio seguente usa l'autenticazione interattiva a scopo illustrativo, ma per la maggior parte degli scenari di produzione che richiedono l'autenticazione automatica o headless, usare l'autenticazione basata su entità servizio, come [descritto in questo notebook](https://aka.ms/pl-restep-auth).

L'autenticazione basata su entità servizio implica la creazione di una *registrazione dell'app* in *Azure Active Directory*. Generare prima di tutto un segreto client, quindi concedere al ruolo dell'entità servizio l'*accesso* all'area di lavoro di Machine Learning. Usare la classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) per gestire il flusso di autenticazione. 

`InteractiveLoginAuthentication` e `ServicePrincipalAuthentication` ereditano da `AbstractAuthentication`. In entrambi i casi, usare la funzione `get_authentication_header()` nello stesso modo per recuperare l'intestazione:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Ottenere l'URL REST dalla proprietà `endpoint` dell'oggetto pipeline pubblicato. È possibile trovare l'URL REST anche nell'area di lavoro in Azure Machine Learning Studio. 

Creare una richiesta HTTP POST all'endpoint. Specificare l'intestazione di autenticazione nella richiesta. Aggiungere un oggetto payload JSON con il nome dell'esperimento e il parametro batch_size. Come accennato in precedenza nell'esercitazione, `param_batch_size` viene passato allo script `batch_scoring.py` perché è stato definito come oggetto `PipelineParameter` nella configurazione del passaggio.

Eseguire la richiesta per attivare l'esecuzione. Includere il codice per l'accesso alla chiave `Id` dal dizionario delle risposte per ottenere il valore dell'ID esecuzione.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Usare l'ID esecuzione per monitorare lo stato della nuova esecuzione. La nuova esecuzione richiede altri 10-15 minuti. 

Avrà un aspetto simile alla pipeline eseguita in precedenza nell'esercitazione. È possibile scegliere di non visualizzare l'output completo.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende eseguire altre esercitazioni su Azure Machine Learning, non completare questa sezione.

### <a name="stop-the-compute-instance"></a>Arrestare l'istanza di calcolo

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminarle per non incorrere in alcun addebito:

1. Nel menu sinistro del portale di Azure selezionare **Gruppi di risorse**.
1. Nell'elenco di gruppi di risorse selezionare quello creato.
1. Selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse. Quindi, selezionare **Elimina**.

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sulle pipeline di Machine Learning sono state eseguite le attività seguenti:

> [!div class="checklist"]
> * È stata creata una pipeline con dipendenze di ambiente da eseguire in una risorsa di calcolo GPU remota.
> * È stato creato uno script di assegnazione di punteggi per l'esecuzione di previsioni in batch con un modello Tensorflow già sottoposto a training.
> * È stata pubblicata una pipeline, che è stata abilitata per l'esecuzione da un endpoint REST.

Per altri esempi sulla creazione di pipeline tramite l'SDK di Machine Learning, vedere il [repository di notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
