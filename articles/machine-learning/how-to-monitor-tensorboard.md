---
title: Visualizzare esperimenti con TensorBoard
titleSuffix: Azure Machine Learning
description: Avvia TensorBoard per visualizzare le cronologie di esecuzione degli esperimenti e identificare le aree potenziali per l'ottimizzazione e la riqualificazione degli iperparametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195380"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizzare le esecuzioni e le metriche dell'esperimento con TensorBoard e Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo imparerai a visualizzare le esecuzioni dell'esperimento e le metriche in TensorBoard usando il pacchetto nell'SDK principale di Azure Machine Learning.In this article, you learn how to view your experiment runs and metrics in TensorBoard using [the `tensorboard` package](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) in the main Azure Machine Learning SDK. Dopo aver esaminato le esecuzioni dell'esperimento, è possibile ottimizzare e riqualificare i modelli di apprendimento automatico.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) è una suite di applicazioni web per l'ispezione e la comprensione della struttura e delle prestazioni dell'esperimento.

La modalità di avvio di TensorBoard con gli esperimenti di Azure Machine Learning dipende dal tipo di esperimento:How you launch TensorBoard with Azure Machine Learning experiments depends on the type of experiment:
+ Se l'esperimento genera in modo nativo i file di log che sono consumabili da TensorBoard, ad esempio gli esperimenti PyTorch, Chainer e TensorFlow, è possibile [avviare TensorBoard direttamente](#direct) dalla cronologia di esecuzione dell'esperimento. 

+ Per gli esperimenti che non generano in modo nativo file consumabili TensorBoard, ad esempio Scikit-learn o esperimenti di Azure Machine Learning, usare [il `export_to_tensorboard()` metodo](#export) per esportare le cronologie di esecuzione come log TensorBoard e avviare TensorBoard da lì. 

> [!TIP]
> Le informazioni contenute in questo documento sono principalmente per i data scientist e gli sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato a monitorare l'utilizzo delle risorse e gli eventi di Azure Machine Learning, ad esempio quote, esecuzioni di training completate o distribuzioni di modelli completate, vedere Monitoraggio di Azure Machine Learning.If you are an administrator interested in monitoring resource usage and events from Azure Machine learning, such as quotas, completed training runs, or completed model deployments, see [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

* Per avviare TensorBoard e visualizzare le cronologie di esecuzione dell'esperimento, gli esperimenti devono aver abilitato in precedenza la registrazione per tenere traccia delle metriche e delle prestazioni.  

* Il codice in questo documento può essere eseguito in uno degli ambienti seguenti:The code in this document can be run in either of the following environments: 

    * Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

        * Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.

        * Nella cartella degli esempi nel server di blocco appunti, trovare due blocchi appunti completati ed espansi passando a queste directory:
            * **how-to-use-azureml > training-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard.ipynb**

    * Il tuo server notebook Juptyer
       * [Installare Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) `tensorboard` SDK con l'extraInstall the Azure Machine Learning SDK with the extra
        * [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.  
        * Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opzione 1: visualizzare direttamente la cronologia delle esecuzione in TensorBoardOption 1: Directly view run history in TensorBoard

Questa opzione funziona per gli esperimenti che generano in modo nativo i file di log utilizzati da TensorBoard, ad esempio gli esperimenti PyTorch, Chainer e TensorFlow. Se questo non è il caso dell'esperimento, usa [invece `export_to_tensorboard()` il metodo.](#export)

Il codice di esempio seguente usa [l'esperimento demo di MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) dal repository di TensorFlow in una destinazione di calcolo remota, Calcolo di Azure Machine Learning.The following example code uses the MNIST demo experiment from TensorFlow's repository in a remote compute target, Azure Machine Learning Compute. Successivamente, viene eseguito il training del modello con lo [stimatore TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)personalizzato dell'SDK e quindi viene avviato TensorBoard rispetto a questo esperimento TensorFlow, ovvero un esperimento che genera in modo nativo i file degli eventi TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Impostare il nome dell'esperimento e creare la cartella del progetto

Qui diamo un nome all'esperimento e ne creiamo la cartella. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Scarica il codice dell'esperimento demo TensorFlow

Il repository di TensorFlow ha una demo MNIST con ampia strumentazione TensorBoard. Non è necessario modificare il codice di questa demo affinché funzioni con Azure Machine Learning.We do not, nor need to, alter of any of this demo's code for it to work with Azure Machine Learning. Nel codice seguente, scarichiamo il codice MNIST e lo salviamo nella cartella dell'esperimento appena creata.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
In tutto il file di codice MNIST, mnist_with_summaries.py, si noti che ci sono linee che chiamano `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` e così via. Questi metodi raggruppano, registrano e contrassegnano le metriche chiave degli esperimenti nella cronologia di esecuzione. Il `tf.summary.FileWriter()` metodo è particolarmente importante in quanto serializza i dati delle metriche dell'esperimento registrate, il che consente a TensorBoard di generare visualizzazioni al di fuori di esse.

 ### <a name="configure-experiment"></a>Configurare l'esperimento

Di seguito, configuriamo l'esperimento e configuriamo le directory per i log e i dati. Questi log verranno caricati nel servizio Artefatto, a cui tensorBoard accede in un secondo momento.

>[!Note]
> Per questo esempio TensorFlow, è necessario installare TensorFlow nel computer locale. Inoltre, il modulo TensorBoard (ovvero quello incluso in TensorFlow) deve essere accessibile al kernel di questo notebook, in quanto il computer locale è ciò che esegue TensorBoard.

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

### <a name="create-a-cluster-for-your-experiment"></a>Creare un cluster per l'esperimentoCreate a cluster for your experiment
Creiamo un cluster AmlCompute per questo esperimento, tuttavia gli esperimenti possono essere creati in qualsiasi ambiente e sei ancora in grado di avviare TensorBoard sulla cronologia di esecuzione dell'esperimento. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Invia esecuzione con lo stimatore TensorFlow

Lo stimatore TensorFlow fornisce un modo semplice per avviare un lavoro di training TensorFlow su un obiettivo di calcolo. Viene implementato tramite [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) la classe generica, che può essere utilizzata per supportare qualsiasi framework. Per altre informazioni sui modelli di training usando lo stimatore generico, vedere Eseguire il training dei [modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Lancia TensorBoard

È possibile avviare TensorBoard durante la corsa o dopo il completamento. Di seguito viene creata un'istanza dell'oggetto TensorBoard, `tb`, `run`che accetta la cronologia di esecuzione `start()` dell'esperimento caricata in , quindi avvia TensorBoard con il metodo . 
  
Il [TensorBoard costruttore](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) accetta una matrice di esecuzioni, quindi assicurarsi e passarlo come una matrice a elemento singolo.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Mentre questo esempio utilizzato TensorFlow, TensorBoard può essere utilizzato con la stessa facilità con i modelli PyTorch o Chainer. TensorFlow deve essere disponibile nel computer che esegue TensorBoard, ma non è necessario sulla macchina che esegue i calcoli PyTorch o Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opzione 2: Esporta la cronologia come registro da visualizzare in TensorBoard

Il codice seguente imposta un esperimento di esempio, inizia il processo di registrazione usando le API della cronologia delle corse di Azure Machine Learning ed esporta la cronologia di esecuzione dell'esperimento in log utilizzabili da TensorBoard per la visualizzazione. 

### <a name="set-up-experiment"></a>Configurare l'esperimento

Il codice seguente imposta un nuovo esperimento e denomina la directory `root_run`di esecuzione. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Qui carichiamo il set di dati sul diabete, un set di dati di piccole dimensioni integrato fornito con scikit-learn, e lo dividiamo in set di test e training.

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

### <a name="run-experiment-and-log-metrics"></a>Eseguire metriche di esperimenti e log

Per questo codice, viene eseguito il training di un `alpha`modello di regressione `mse`lineare e delle metriche chiave di log, il coefficiente alfa, e l'errore medio al quadrato, , nella cronologia di esecuzione.

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

### <a name="export-runs-to-tensorboard"></a>Esportazione di esecuzioni in TensorBoard

Con il metodo [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) dell'SDK, è possibile esportare la cronologia di esecuzione dell'esperimento di apprendimento automatico di Azure nei log di TensorBoard, in modo da poterli visualizzare tramite TensorBoard.  

Nel codice seguente viene creata `logdir` la cartella nella directory di lavoro corrente. Questa cartella è dove esporteremo la `root_run` cronologia di esecuzione dell'esperimento e i log da e quindi contrassegnare l'esecuzione come completata. 

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
 È inoltre possibile esportare una particolare conduzione in TensorBoard specificando il nome`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Avviare e arrestare TensorBoard
Una volta che la nostra cronologia di esecuzione per questo esperimento viene esportata, possiamo lanciare TensorBoard con il metodo [start().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Al termine, assicuratevi di chiamare il metodo [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) dell'oggetto TensorBoard. In caso contrario, TensorBoard continuerà a essere eseguito fino a quando non si arresta il kernel del notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passaggi successivi

In questo how-to, creato due esperimenti e imparato come lanciare TensorBoard contro le loro storie di corsa per identificare le aree per la potenziale messa a punto e riqualificazione. 

* Se sei soddisfatto del tuo modello, vai al nostro articolo [Come distribuire un modello.](how-to-deploy-and-where.md) 
* Ulteriori informazioni [sull'ottimizzazione dei parametri iperparametri](how-to-tune-hyperparameters.md). 
