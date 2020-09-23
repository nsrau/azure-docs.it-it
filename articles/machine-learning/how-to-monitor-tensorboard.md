---
title: Visualizzare esperimenti con TensorBoard
titleSuffix: Azure Machine Learning
description: Avviare TensorBoard per visualizzare le cronologie di esecuzione degli esperimenti e identificare potenziali aree di ottimizzazione e ripetizione del training degli iperparametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: da5c128b9e0befd69e1ded6b47644a3c64b8f657
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905050"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizzare le esecuzioni e le metriche degli esperimenti con TensorBoard e Azure Machine Learning


Questo articolo illustra come visualizzare le esecuzioni e le metriche degli esperimenti in TensorBoard usando [il pacchetto `tensorboard`](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py&preserve-view=true) nella versione principale di Azure Machine Learning SDK. Una volta esaminate le esecuzioni degli esperimenti, è possibile ottimizzare e ripetere il training dei modelli di Machine Learning.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) è una famiglia di applicazioni Web che consente di ispezionare e comprendere la struttura e le prestazioni degli esperimenti.

Il modo in cui avviare TensorBoard con gli esperimenti di Azure Machine Learning varia in base al tipo di esperimento:
+ Se l'esperimento genera in modo nativo i file di log utilizzabili da TensorBoard, ad esempio esperimenti di PyTorch, Chainer e TensorFlow, è possibile [avviare TensorBoard direttamente](#direct) dalla cronologia di esecuzione dell'esperimento. 

+ Per gli esperimenti che non restituiscono in modo nativo file utilizzabili da TensorBoard, ad esempio esperimenti di Scikit-learn o Azure Machine Learning, usare [il metodo `export_to_tensorboard()`](#export) per esportare le cronologie di esecuzione come log di TensorBoard e quindi avviare TensorBoard. 

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a scienziati dei dati e sviluppatori che vogliono monitorare il processo di training del modello. Gli amministratori interessati al monitoraggio dell'utilizzo di risorse e degli eventi di Azure Machine Learning, come quote, esecuzioni di training o distribuzioni del modello completate, possono consultare la sezione [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

* Per avviare TensorBoard e visualizzare le cronologie di esecuzione degli esperimenti, è necessario aver abilitato in precedenza la registrazione per tenere traccia delle metriche e delle prestazioni.  

* Il codice di questo documento può essere eseguito in uno degli ambienti seguenti: 

    * Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

        * Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.

        * Nella cartella di esempi del server di notebook sono disponibili due notebook completati ed espansi in queste directory:
            * **how-to-use-azureml > training-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard.ipynb**

    * Server di notebook Jupyter personale
       * [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) con l'aggiunta di `tensorboard`
        * [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).  
        * [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opzione 1: Visualizzare direttamente la cronologia di esecuzione in TensorBoard

Questa opzione è valida per gli esperimenti che restituiscono in modo nativo i file di log utilizzabili da TensorBoard, ad esempio gli esperimenti di PyTorch, Chainer e TensorFlow. Se questo non è il caso dell'esperimento, usare invece [il metodo `export_to_tensorboard()`](#export).

Il codice di esempio seguente usa l'[esperimento demo MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) del repository di TensorFlow in una destinazione di calcolo remota, l'ambiente di calcolo di Machine Learning. Successivamente, si eseguirà il training del modello con lo [strumento di stima TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true) personalizzato dell'SDK, quindi si avvierà TensorBoard per questo esperimento di TensorFlow, ovvero un esperimento che restituisce in modo nativo i file evento di TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Impostare il nome dell'esperimento e creare la cartella di progetto

Assegnare un nome all'esperimento e creare la relativa cartella. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Scaricare il codice dell'esperimento demo di TensorFlow

Il repository di TensorFlow include una demo MNIST con una vasta strumentazione di TensorBoard. Non è necessario modificare il codice di questa demo per l'uso con Azure Machine Learning. Nel codice seguente il codice MNIST viene scaricato e salvato nella cartella dell'esperimento appena creata.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Si noti che il file di codice MNIST, mnist_with_summaries.py, include righe che chiamano `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` e così via. Questi metodi raggruppano, registrano e contrassegnano le metriche chiave degli esperimenti nella cronologia di esecuzione. `tf.summary.FileWriter()` è particolarmente importante in quanto serializza i dati delle metriche registrate dell'esperimento, consentendo a TensorBoard di generare le relative visualizzazioni.

 ### <a name="configure-experiment"></a>Configurare l'esperimento

Di seguito si configura l'esperimento e si impostano le directory per i log e i dati. Questi log verranno caricati nel servizio Artifact, a cui TensorBoard accede in un secondo momento.

>[!Note]
> Per questo esempio di TensorFlow, sarà necessario installare TensorFlow nel computer locale. Inoltre, il modulo TensorBoard, ovvero quello incluso in TensorFlow, deve essere accessibile al kernel di questo notebook, perché è il computer locale che esegue TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
script_params = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Creare un cluster per l'esperimento
Per questo esperimento viene creato un cluster AmlCompute, tuttavia gli esperimenti possono essere creati in qualsiasi ambiente ed è comunque possibile avviare TensorBoard per la cronologia di esecuzione degli esperimenti. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="submit-run-with-tensorflow-estimator"></a>Inviare l'esecuzione con lo strumento di stima di TensorFlow

Lo strumento di stima di TensorFlow offre la possibilità di avviare facilmente un processo di training di TensorFlow in una destinazione di calcolo. Viene implementato tramite la classe generica [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true), che può essere usata per supportare qualsiasi framework. Per altre informazioni sui modelli di training che usano lo strumento di stima generico, vedere [Eseguire il training dei modelli con Azure Machine Learning usando lo strumento di stima](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Avviare TensorBoard

È possibile avviare TensorBoard durante o dopo il completamento dell'esecuzione. Nell'esempio seguente viene creata un'istanza dell'oggetto TensorBoard, `tb`, che accetta la cronologia di esecuzione dell'esperimento caricata in `run`, quindi avvia TensorBoard con il metodo `start()`. 
  
Il [costruttore di TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true) accetta una matrice di esecuzioni, quindi assicurarsi di passarla come matrice a elemento singolo.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Anche se in questo esempio si usa TensorFlow, TensorBoard può essere usato facilmente con i modelli PyTorch o Chainer. TensorFlow deve essere disponibile nel computer in cui è in esecuzione TensorBoard, ma non è necessario nel computer che esegue i calcoli PyTorch o Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opzione 2: Esportare la cronologia come log per la visualizzazione in TensorBoard

Il codice seguente configura un esperimento di esempio, avvia il processo di registrazione usando le API della cronologia di esecuzione di Azure Machine Learning ed esporta la cronologia di esecuzione dell'esperimento nei log utilizzabili da TensorBoard per la visualizzazione. 

### <a name="set-up-experiment"></a>Configurare l'esperimento

Il codice seguente configura un nuovo esperimento e assegna alla directory di esecuzione il nome `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Qui viene caricato il set di dati sul diabete, ovvero un piccolo set di dati predefinito incluso in Scikit-learn, e lo divide in set di test e di training.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Eseguire l'esperimento e registrare le metriche

Per questo codice, viene eseguito il training di un modello di regressione lineare e vengono registrate le metriche chiave, ovvero il coefficiente alfa, `alpha`, e l'errore quadratico medio, `mse`, nella cronologia di esecuzione.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Esportare le esecuzioni in TensorBoard

Con il metodo [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py&preserve-view=true) dell'SDK, è possibile esportare la cronologia di esecuzione dell'esperimento di Azure Machine Learning nei log di TensorBoard, che sarà poi possibile visualizzare tramite TensorBoard.  

Nel codice seguente viene creata la cartella `logdir` nella directory di lavoro corrente. Questa cartella è quella in cui verranno esportati la cronologia di esecuzione dell'esperimento e i log di `root_run`. Quindi l'esecuzione verrà contrassegnata come completata. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 È anche possibile esportare una determinata esecuzione in TensorBoard specificandone il nome `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Avviare e arrestare TensorBoard
Una volta esportata la cronologia di esecuzione di questo esperimento, è possibile avviare TensorBoard con il metodo [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#&preserve-view=truestart-start-browser-false-). 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Al termine, assicurarsi di chiamare il metodo [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#&preserve-view=truestop--) dell'oggetto TensorBoard. In caso contrario, TensorBoard continuerà a essere eseguito fino a quando non si arresta il kernel del notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passaggi successivi

In questa procedura sono stati creati due esperimenti e si è appreso come avviare TensorBoard per le relative cronologie di esecuzione per identificare le potenziali aree di ottimizzazione e ripetizione del training. 

* Se si è soddisfatti del modello, procedere con l'articolo [Come distribuire un modello](how-to-deploy-and-where.md). 
* Altre informazioni sull'[ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md). 
