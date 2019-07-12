---
title: Visualizzare esperimenti con TensorBoard
titleSuffix: Azure Machine Learning service
description: Avviare TensorBoard per visualizzare le cronologie di esecuzione dell'esperimento e identificare potenziali aree utili per iperparametri ottimizzazione e la ripetizione del training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707060"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizzare le esecuzioni dell'esperimento e le metriche con TensorBoard e Azure Machine Learning

In questo articolo descrive come visualizzare le esecuzioni dell'esperimento e le metriche con TensorBoard [il `tensorboard` pacchetto](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) in Azure Machine Learning principale del servizio SDK. Dopo che è stato controllato dell'esecuzione dell'esperimento, per poter ottimizzare e ripetere il training dei modelli di machine learning.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) è una suite di applicazioni web permette di analizzare e comprendere la struttura di esperimento e delle prestazioni.

Come si avvia TensorBoard con gli esperimenti Azure Machine Learning dipende dal tipo di esperimento:
+ Se l'esperimento in modo nativo i file di log che possono essere utilizzati da TensorBoard, ad esempio gli esperimenti di PyTorch, Chainer e TensorFlow generati, è quindi possibile [avviare direttamente TensorBoard](#direct) dall'esperimento cronologia di esecuzione. 

+ Per gli esperimenti che non in modo nativo TensorBoard utilizzabile i file di output, ad esempio come Scikit-learn o Azure esperimenti di Machine Learning, usare [il `export_to_tensorboard()` metodo](#export) per esportare le cronologie di esecuzione sotto forma di log TensorBoard e avviare TensorBoard da tale posizione. 

## <a name="prerequisites"></a>Prerequisiti

* Per avviare TensorBoard e visualizzare l'esperimento le cronologie di esecuzione, è necessario che gli esperimenti stata precedentemente abilitata la registrazione tenere traccia delle relative metriche e le prestazioni.  

* Il codice in questa procedura può essere eseguito in uno degli ambienti seguenti: 

    * Azure Machine Learning Notebook VM - alcun download o installazione necessaria

        * Completare la [Guida introduttiva di notebook basato su cloud](quickstart-run-cloud-notebook.md#create-notebook) per creare un server notebook dedicato precaricato con il SDK e il repository di esempio.

        * Nella cartella samples nel server notebook, trovare due completato ed espanso notebook, passare a questa directory: **how-to-uso-azureml > formazione con deep learning**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Il server notebook Juptyer
      * Usare la [creare un articolo dell'area di lavoro](setup-create-workspace.md) a
          * [Installare il SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) con il `tensorboard` extra
          * Creare un'area di lavoro e il relativo file di configurazione (config. JSON)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opzione 1: Direttamente visualizzazione della cronologia di esecuzione in TensorBoard

Questa opzione funziona per gli esperimenti che in modalità nativa gli output file di log di utilizzabili da TensorBoard, ad esempio PyTorch, Chainer e TensorFlow esperimenti. Se non è il caso dell'esperimento, usare [il `export_to_tensorboard()` metodo](#export) invece.

Il codice di esempio seguente usa il [esperimento demo MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) dal repository di TensorFlow in una destinazione di calcolo remoto, calcolo di Azure Machine Learning. Successivamente, abbiamo il training del modello con personalizzata del SDK [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), e quindi avviare TensorBoard su questo esperimento TensorFlow, vale a dire un esperimento che i file di evento TensorBoard vengono generati in modo nativo.

### <a name="set-experiment-name-and-create-project-folder"></a>Impostare nome dell'esperimento e creare la cartella di progetto

Qui è assegnare un nome dell'esperimento e quindi creare la cartella. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Download del codice TensorFlow demo esperimento

Repository di TensorFlow ha una demo MNIST e fornisce strumentazione TensorBoard estesa. Microsoft non li supportano, né necessario, modificare il codice di questa demo per farli funzionare con il servizio di Azure Machine Learning. Nel codice seguente, si scarica il codice MNIST e salvarlo nella cartella esperimento appena creato.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
In tutto il file del codice MNIST mnist_with_summaries.py, si noti che sono presenti righe che chiamano `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` e così via. Questi metodi di gruppo, di log e metriche chiave dei propri esperimenti di tag nella cronologia di esecuzione. Il `tf.summary.FileWriter()` è particolarmente importante quando serializza i dati di metriche esperimento connesso, che consente di TensorBoard generare visualizzazioni di fuori di essi.

 ### <a name="configure-experiment"></a>Configurare l'esperimento

Nell'esempio seguente, configurare l'esperimento e impostare le directory per i log e dati. Questi log vengono caricati al servizio dell'elemento, quale TensorBoard accede in un secondo momento.

>[!Note]
> Per questo esempio TensorFlow, dovrai installare TensorFlow in locale. Inoltre, il modulo TensorBoard (vale a dire quella inclusa con TensorFlow) deve essere accessibile al kernel del notebook, come il computer locale è ciò che viene eseguito TensorBoard.

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
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Creare un cluster per l'esperimento
Viene creato un cluster AmlCompute per questo esperimento, tuttavia, è possibile creare esperimenti in qualsiasi ambiente e si è ancora in grado di avviare TensorBoard contro l'esperimento di cronologia di esecuzione. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Invia l'esecuzione con la stima di TensorFlow

Lo strumento di stima TensorFlow fornisce un modo semplice per l'avvio di un processo di training TensorFlow in una destinazione di calcolo. Viene implementato tramite il tipo generico [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), che può essere usato per supportare qualsiasi framework. Per altre informazioni sui training dei modelli utilizzando lo strumento di stima generico, vedere [il training dei modelli con Azure Machine Learning con strumento di stima](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Avviare TensorBoard

È possibile avviare TensorBoard durante l'esecuzione o dopo il completamento. In quanto segue, creiamo un'istanza di oggetto, TensorBoard `tb`, che ha eseguito l'esperimento cronologia caricata nel `run`e quindi avvia TensorBoard con il `start()` (metodo). 
  
Il [TensorBoard costruttore](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) accetta una matrice di esecuzioni, quindi vi consiglio di passarlo come una matrice a elemento singolo.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opzione 2: Esporta la cronologia di log per visualizzare in TensorBoard

Il codice seguente consente di impostare un esperimento di esempio, avvia il processo di registrazione usando l'API della cronologia di esecuzione di Azure Machine Learning e consente di esportare l'esperimento di cronologia di esecuzione nei registri utilizzabili da TensorBoard per la visualizzazione. 

### <a name="set-up-experiment"></a>Configurare l'esperimento

Il codice seguente configura un nuovo esperimento e i nomi di directory di esecuzione `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

In questo caso è caricare il set di dati diabetes: un set di dati piccolo predefinito fornito con scikit-informazioni su e dividerlo in set di training e test.

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

### <a name="run-experiment-and-log-metrics"></a>Eseguire l'esperimento e registrare metriche

Per questo codice, eseguire il training di un modello di regressione lineare e le metriche chiave, il coefficiente alfa, di log `alpha` e l'errore quadratico medio, `mse`, nella cronologia di esecuzione.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Esecuzioni di esportazione da TensorBoard

Con il SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metodo, possiamo inoltre esportare la cronologia di esecuzione dell'esperimento di apprendimento di Azure in log TensorBoard, pertanto è possibile visualizzarle tramite TensorBoard.  

Nel codice seguente, creiamo la cartella `logdir` nella directory di lavoro corrente. Questa cartella in cui verranno esportati esperimento della cronologia di esecuzione e i log da `root_run` e contrassegnare quindi eseguiti come completato. 

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
 È anche possibile esportare una determinata esecuzione in TensorBoard specificando il nome dell'esecuzione  `export_to_tensorboard(run_name, logdir)`

Avviare e arrestare una volta TensorBoard viene esportata la cronologia di esecuzione per questo esperimento, possiamo avviare TensorBoard con il [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) (metodo). 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Al termine, assicurarsi di chiamare il [Stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metodo dell'oggetto TensorBoard. In caso contrario, TensorBoard continuerà a eseguire fino a quando non si arresta il kernel del notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passaggi successivi

In questo sulle procedure, creati due esperimenti e imparato a avviare TensorBoard contro i cronologie di esecuzione per identificare le aree per l'ottimizzazione potenziale e ripetizione del training. 

* Se si è soddisfatti con il modello, passare a nostro [come distribuire un modello](how-to-deploy-and-where.md) articolo. 
* Altre informazioni sulle [ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md). 
