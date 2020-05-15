---
title: "Esercitazione: Pipeline di Machine Learning per l'assegnazione di punteggi in batch"
titleSuffix: Azure Machine Learning
description: In questa esercitazione verrà creata una pipeline di Machine Learning per l'assegnazione di punteggi batch in un modello di classificazione delle immagini. Azure Machine Learning consente di concentrarsi su Machine Learning anziché preoccuparsi dell'infrastruttura e dell'automazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 5b6b58cb205c769feeed011c0a2ba2ec569d667a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857758"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Esercitazione: Creare una pipeline di Azure Machine Learning per l'assegnazione di punteggi batch

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa esercitazione avanzata viene illustrato come creare una pipeline di Azure Machine Learning per eseguire un processo di assegnazione di punteggi batch. Le pipeline di Machine Learning ottimizzano il flusso di lavoro offrendo velocità, portabilità e possibilità di riutilizzo per consentire così di concentrarsi su Machine Learning anziché sull'infrastruttura e l'automazione. Dopo aver creato e pubblicato una pipeline, configurare un endpoint REST da usare per attivare la pipeline da qualsiasi libreria HTTP in qualsiasi piattaforma. 

Questo esempio usa il modello di rete neurale convoluzionale [Inception-V3](https://arxiv.org/abs/1512.00567) già sottoposto a training implementato in Tensorflow per classificare le immagini senza etichetta. [Altre informazioni sulle pipeline di Machine Learning](concept-ml-pipelines.md).

In questa esercitazione si completano le attività seguenti:

> [!div class="checklist"]
> * Configurare l'area di lavoro 
> * Scaricare e archiviare i dati di esempio
> * Creare oggetti set di dati per recuperare e restituire i dati
> * Scaricare, preparare e registrare il modello nell'area di lavoro
> * Effettuare il provisioning di destinazioni di calcolo e creare uno script di assegnazione del punteggio
> * Usare la classe `ParallelRunStep` per l'assegnazione di punteggi batch asincrona
> * Compilare, eseguire e pubblicare una pipeline
> * Abilitare un endpoint REST per la pipeline

Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha già un'area di lavoro di Azure Machine Learning o una macchina virtuale per notebook, completare la [parte 1 dell'esercitazione sull'installazione](tutorial-1st-experiment-sdk-setup.md).
* Al termine dell'esercitazione sull'installazione, usare lo stesso server notebook per aprire il notebook *tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb*.

Se si vuole eseguire l'esercitazione sull'installazione nell'[ambiente locale](how-to-configure-environment.md#local), è possibile accedervi in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Eseguire `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` per ottenere i pacchetti necessari.

## <a name="configure-workspace-and-create-a-datastore"></a>Configurare l'area di lavoro e creare un archivio dati

Creare un oggetto area di lavoro dall'area di lavoro di Azure Machine Learning esistente.

- Un'[area di lavoro](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud che è possibile usare per monitorare le esecuzioni del modello e tenerne traccia. 
- `Workspace.from_config()` legge il file `config.json` e quindi carica i dettagli di autenticazione in un oggetto denominato `ws`. L'oggetto `ws` viene usato nel codice nell'intera esercitazione.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Creare un archivio dati per le immagini di esempio

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

## <a name="create-dataset-objects"></a>Creare oggetti set di dati

Durante la compilazione delle pipeline vengono usati oggetti `Dataset` per leggere i dati dagli archivi dati dell'area di lavoro e oggetti `PipelineData` per trasferire i dati intermedi tra i passaggi della pipeline.

> [!Important]
> L'esempio di assegnazione di punteggi in batch di questa esercitazione usa un solo passaggio della pipeline. Nei casi d'uso con più passaggi, il flusso tipico includerà questi passaggi:
>
> 1. Usare gli oggetti `Dataset` come *input* per recuperare dati non elaborati, eseguire qualche trasformazione e quindi generare come *output* un oggetto `PipelineData`.
>
> 2. Usare l'`PipelineData` *oggetto di output* del passaggio precedente come *oggetto di input*. Ripetere questa operazione per i passaggi successivi.

Per questo scenario vengono creati oggetti `Dataset` corrispondenti alle directory dell'archivio dati per le immagini di input e le etichette di classificazione (valori y-test). Viene anche creato un oggetto `PipelineData` per i dati di output del punteggio batch.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registrare quindi i set di dati nell'area di lavoro.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
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

Eseguire il codice seguente per creare una destinazione [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) abilitata per la GPU e collegarla all'area di lavoro. Per altre informazioni sulle destinazioni di calcolo, vedere l'[articolo concettuale](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


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

Lo script `batch_scoring.py` accetta i parametri seguenti, che vengono passati dal passaggio `ParallelRunStep` che verrà creato più avanti in questa esercitazione:

- `--model_name`: il nome del modello usato.
- `--labels_name`: Nome del `Dataset` che include il file `labels.txt`.

L'infrastruttura delle pipeline usa la classe `ArgumentParser` per passare i parametri nei passaggi della pipeline. Ad esempio, nel codice seguente al primo argomento `--model_name` viene assegnato l'identificatore della proprietà `model_name`. Nella funzione `init()` si usa `Model.get_model_path(args.model_name)` per accedere a questa proprietà.


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

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> La pipeline di questa esercitazione include un solo passaggio e scrive l'output in un file. Per le pipeline con più passaggi, si usa anche `ArgumentParser` per definire una directory in cui scrivere i dati di output come input per i passaggi successivi. Per un esempio del passaggio di dati tra più passaggi della pipeline usando il modello di progettazione `ArgumentParser`, vedere il [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Creare la pipeline

Prima di eseguire la pipeline, creare un oggetto che definisce l'ambiente Python e le dipendenze necessarie per lo script `batch_scoring.py`. La dipendenza principale necessaria è Tensorflow, ma occorre installare anche `azureml-defaults` per i processi in background. Creare un oggetto `RunConfiguration` usando le dipendenze. Specificare inoltre il supporto di Docker e della GPU Docker.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Creare la configurazione per il wrapping dello script

Creare il passaggio della pipeline usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già collegata all'area di lavoro.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Creare il passaggio della pipeline

Un passaggio della pipeline è un oggetto che incapsula tutti gli elementi necessari per l'esecuzione di una pipeline, tra cui:

* Impostazioni dell'ambiente e delle dipendenze
* Risorsa di calcolo in cui eseguire la pipeline
* Dati di input e di output ed eventuali parametri personalizzati
* Riferimento a uno script o alla logica SDK da eseguire durante il passaggio

Più classi ereditano dalla classe padre [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). È possibile scegliere le classi per usare specifici framework o stack per creare un passaggio. In questo esempio si usa la classe `ParallelRunStep` per definire la logica del passaggio usando uno script Python personalizzato. Se un argomento dello script è un input per il passaggio o un output del passaggio, deve essere definito *sia* nella matrice `arguments`*sia* nel parametro `input` o `output` rispettivamente. 

Negli scenari in cui sono presenti più passaggi, un riferimento a un oggetto nella matrice `outputs` diventa disponibile come *input* per un passaggio successivo della pipeline.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Per un elenco di tutte le classi che è possibile usare per i diversi tipi di passaggio, vedere il [pacchetto dei passaggi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Inviare la pipeline

Eseguire ora la pipeline Creare prima di tutto un oggetto `Pipeline` usando il riferimento all'area di lavoro e il passaggio della pipeline creato. Il parametro `steps` è una matrice di passaggi. In questo caso, è presente un solo passaggio per l'assegnazione di punteggi in batch. Per creare pipeline con più passaggi, inserire i passaggi nell'ordine corretto in questa matrice.

Usare quindi la funzione `Experiment.submit()` per inviare la pipeline per l'esecuzione. Specificare anche il parametro personalizzato `param_batch_size`. La funzione `wait_for_completion` genera log durante il processo di creazione della pipeline. È possibile usarli per visualizzare lo stato di avanzamento corrente.

> [!IMPORTANT]
> La prima esecuzione della pipeline richiede circa *15 minuti*. Devono essere scaricate tutte le dipendenze, quindi viene creata un'immagine Docker e viene effettuato il provisioning o la creazione dell'ambiente Python. Una seconda esecuzione della pipeline richiede molto meno tempo, perché queste risorse vengono riutilizzate e non create. Tuttavia, il tempo di esecuzione totale dipende dal carico di lavoro degli script e dai processi in esecuzione in ogni passaggio della pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Scaricare ed esaminare l'output

Eseguire il codice seguente per scaricare il file di output creato dallo script `batch_scoring.py`. Quindi esplorare i risultati dell'assegnazione di punteggi.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Pubblicare ed eseguire la pipeline da un endpoint REST

Eseguire il codice seguente per pubblicare la pipeline nell'area di lavoro. Nell'area di lavoro in Azure Machine Learning Studio è possibile visualizzare i metadati della pipeline, incluse la cronologia di esecuzione e le durate. È anche possibile eseguire la pipeline manualmente da Machine Learning Studio.

La pubblicazione della pipeline consente a un endpoint REST di eseguire di nuovo la pipeline da qualsiasi libreria HTTP in qualsiasi piattaforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Per eseguire la pipeline dall'endpoint REST, è necessaria un'intestazione di autenticazione di tipo connessione OAuth2. L'esempio seguente usa l'autenticazione interattiva a scopo illustrativo, ma per la maggior parte degli scenari di produzione che richiedono l'autenticazione automatica o headless, usare l'autenticazione basata su entità servizio, come [descritto in questo articolo](how-to-setup-authentication.md).

L'autenticazione basata su entità servizio implica la creazione di una *registrazione dell'app* in *Azure Active Directory*. Generare prima di tutto un segreto client, quindi concedere al ruolo dell'entità servizio l'*accesso* all'area di lavoro di Machine Learning. Usare la classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) per gestire il flusso di autenticazione. 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) e `ServicePrincipalAuthentication` ereditano da `AbstractAuthentication`. In entrambi i casi, usare la funzione [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) nello stesso modo per recuperare l'intestazione:

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

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

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
