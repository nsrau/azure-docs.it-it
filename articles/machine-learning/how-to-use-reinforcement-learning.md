---
title: Eseguire il training e distribuire un modello di apprendimento per rinforzo (anteprima).
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning Learning di rinforzo (anteprima) per eseguire il training di un agente RL per giocare a pong.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a7fdb370847e72657829d53df019203b0a5b211b
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302568"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Apprendimento per rinforzo con Azure Machine Learning (anteprima)



> [!NOTE]
> Reinforcement Learning di Azure Machine Learning è attualmente una funzionalità di anteprima. Al momento sono supportati solo i framework Ray e RLlib.

L'articolo descrive come eseguire il training di un agente di apprendimento per rinforzo (RL) in modo che giochi al videogioco Pong. Vengono usate la libreria open source di Python e [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) con Azure Machine Learning, per gestire la complessità dei processi RL distribuiti.

In questo articolo verrà spiegato come:
> [!div class="checklist"]
> * Configurare un esperimento
> * Definire i nodi head e i nodi di lavoro
> * Creare un oggetto di stima RL
> * Inviare un esperimento per avviare un'esecuzione
> * Visualizzazione dei risultati

Questo articolo si basa sull'[esempio RLlib Pong](https://aka.ms/azureml-rl-pong) disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md) del notebook di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti. Si consiglia di provare l'istanza di calcolo di Azure Machine Learning per un'esperienza di avvio più rapida. I notebook di esempio per il rinforzo sono disponibili per una rapida clonazione ed esecuzione in istanze di calcolo di Azure Machine Learning.

 - Istanza di calcolo di Azure Machine Learning

     - Informazioni su come clonare notebook di esempio in [Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md).
         - Clonare la cartella **How-to-use-azureml** invece delle **esercitazioni**
     - Eseguire il notebook di configurazione della rete virtuale disponibile in `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` per aprire le porte di rete usate per l'apprendimento per rinforzo distribuito.
     - Eseguire il notebook di esempio `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Server Jupyter Notebook personale

    - Installare [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
    - Installare [Azure Machine Learning SDK](/python/api/azureml-contrib-reinforcementlearning/?preserve-view=true&view=azure-ml-py): `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Creare un [file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Eseguire la rete virtuale per aprire le porte di rete usate per l'apprendimento di rinforzo distribuito.


## <a name="how-to-train-a-pong-playing-agent"></a>Procedura per eseguire il training di un agente che giochi a Pong

L'apprendimento per rinforzo (RL) è un approccio al Machine Learning che apprende facendo. Mentre altre tecniche di Machine Learning apprendono mediante l'acquisizione passiva dei dati di input e tramite ricerca di modelli al suo interno, RL usa **agenti di training** per prendere decisioni e apprendere i risultati in modo attivo.

Gli agenti di training imparano a giocare a Pong in un ambiente **simulato**. Gli agenti di training decidono in ogni frame del gioco di spostare la racchetta verso l'alto, verso il basso o di lasciarla al proprio posto. Lo stato del gioco (un'immagine RGB dello schermo) viene esaminato per prendere una decisione.

RL usa **ricompense** per indicare all'agente se le sue decisioni hanno avuto esito positivo. In questo ambiente, l'agente riceve una ricompensa positiva quando realizza un punteggio e una ricompensa negativa quando subisce un punteggio. In molte iterazioni e in base al suo stato corrente, l'agente di training impara a scegliere l'azione che ottimizza la somma dei premi futuri previsti.

Per eseguire questa ottimizzazione in RL di solito si usa un modello di **rete neurale profonda** (DNN). Inizialmente, l'agente di formazione eseguirà prestazioni ridotte, tuttavia ogni gioco genererà altri esempi per migliorare ulteriormente il modello.

Il training termina quando l'agente raggiunge un punteggio medio di ricompensa pari a 18, in un epoch di training. Ciò significa che l'agente ha battuto il proprio avversario con una media di almeno 18 punti nelle corrispondenze fino a 21.

Il processo di iterazione della simulazione e la ripetizione del training di un DNN risultano costosi dal punto di vista del calcolo e richiedono grandi quantità di dati. Un modo per migliorare le prestazioni dei processi RL è il **lavoro di parallelizzazione** fatto in modo che più agenti di training possano agire e apprendere simultaneamente. Tuttavia, la gestione di un ambiente RL distribuito può rappresentare un'impresa complessa.

Azure Machine Learning fornisce il framework per gestire queste complessità così da scalare i carichi di lavoro RL.

## <a name="set-up-the-environment"></a>Configurare l'ambiente

Configurare l'ambiente RL locale caricando i pacchetti Python necessari, con l'inizializzazione dell'area di lavoro, la creazione di un esperimento e la specifica di una rete virtuale configurata.

### <a name="import-libraries"></a>Importare le librerie

Importare i pacchetti Python necessari a eseguire il resto di questo esempio.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L'[area di lavoro di Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati.

Inizializzare un oggetto dell'area di lavoro dal file `config.json` creato nella [sezione dei prerequisiti](#prerequisites). Se si esegue questo codice in un'istanza di calcolo di Azure Machine Learning, il file di configurazione viene creato automaticamente.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Creare un esperimento di apprendimento per rinforzo

Creare un [esperimento](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) per tenere traccia dell'esecuzione dell'apprendimento per rinforzo. In Azure Machine Learning, gli esperimenti sono raccolte logiche di versioni di valutazione correlate per organizzare log di esecuzione, cronologia, output e altro ancora.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Specificare una rete virtuale

Per i processi RL che usano più destinazioni di calcolo è necessario specificare una rete virtuale con porte aperte che consentano la comunicazione tra i nodi di lavoro e i nodi head. La rete virtuale può trovarsi in qualsiasi gruppo di risorse, tuttavia deve trovarsi nella stessa area dell'area di lavoro. Per altre informazioni sulla configurazione della rete virtuale, vedere il notebook per la configurazione dell'area di lavoro disponibile nella sezione prerequisiti. Qui è possibile specificare il nome della rete virtuale nel gruppo di risorse.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definire le destinazioni di calcolo head e ruolo di lavoro

Questo esempio usa destinazioni di calcolo separate per i nodi Ray head e di lavoro. Queste impostazioni consentono di ridimensionare le risorse di calcolo in base al carico di lavoro previsto. Impostare il numero e le dimensioni di ogni nodo, in base alle esigenze dell'esperimento.

### <a name="head-computing-target"></a>Destinazione di elaborazione head

Questo esempio usa un cluster head dotato di GPU per ottimizzare le prestazioni di Deep Learning. Il nodo head esegue il training della rete neurale usata dall'agente per prendere decisioni. Il nodo head raccoglie anche i punti dati dai nodi di lavoro per eseguire un altro training della rete neurale.

Il calcolo head usa una singola macchina virtuale [`STANDARD_NC6` ](../virtual-machines/nc-series.md) (VM). Dispone di 6 CPU virtuali, il che significa che può distribuire il lavoro tra 6 CPU operative.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Cluster di calcolo del ruolo di lavoro

Questo esempio usa quattro VM [`STANDARD_D2_V2` ](../virtual-machines/nc-series.md) per la destinazione di calcolo del ruolo di lavoro. Ogni nodo di lavoro dispone di 2 CPU disponibili per un totale di 8 CPU disponibili per parallelizzare il lavoro.

Le GPU non sono necessarie per i nodi di lavoro poiché non eseguono Deep Learning. I ruoli di lavoro eseguono le simulazioni di gioco e raccolgono i dati.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Creare un oggetto di stima per l'apprendimento per rinforzo

In questa sezione si apprenderà come usare il [ReinforcementLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?preserve-view=true&view=azure-ml-py) per inviare un processo di training ad Azure Machine Learning.

Azure Machine Learning usa le classi di stima per incapsulare le informazioni di configurazione di esecuzione. Ciò consente di specificare facilmente come configurare un'esecuzione di script. 

### <a name="define-a-worker-configuration"></a>Definire una configurazione ruolo di lavoro condiviso

L'oggetto WorkerConfiguration indica ad Azure Machine Learning come inizializzare il cluster di lavoro che eseguirà lo script di immissione.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definire i parametri degli script

Lo script di immissione `pong_rllib.py` accetta un elenco di parametri che definisce la modalità di esecuzione del processo di training. Il passaggio di questi parametri tramite l'oggetto di stima come un livello di incapsulamento semplifica la modifica dei parametri di script e l'esecuzione delle configurazioni indipendentemente fra loro.

Specificando il `num_workers` corretto, si ottiene il massimo dagli sforzi di parallelizzazione. Impostare il numero di ruoli di lavoro in un numero pari alle CPU disponibili. Per questo esempio è possibile calcolarlo come segue:

Il nodo head è uno [Standard_NC6](../virtual-machines/nc-series.md) con 6 vCPU. Il cluster di lavoro è 4 [Standard_D2_V2 VM](../cloud-services/cloud-services-sizes-specs.md#dv2-series) con 2 CPU per ognuno, per un totale di 8 CPU. Tuttavia, è necessario sottrarre 1 CPU dal numero di ruoli di lavoro poiché 1 deve essere dedicata al ruolo del nodo head. 6 CPU + 8 CPU - 1 CPU head = 13 ruoli di lavoro simultanei. Azure Machine Learning usa i cluster head e i cluster di lavoro per distinguere le risorse di calcolo. Tuttavia, il Ray non distingue tra head e ruoli di lavoro, inoltre tutte le CPU sono CPU disponibili per l'esecuzione del thread di lavoro.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definire l'oggetto di stima per l'apprendimento per rinforzo

Usare l'elenco di parametri e l'oggetto di configurazione del ruolo di lavoro per creare l'oggetto di stima.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Script di immissione

Lo [script di immissione](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` esegue il training di una rete neurale usando l'[ambiente OpenAI Gym](https://github.com/openai/gym/) `PongNoFrameSkip-v4`. Le OpenAI Gym sono interfacce standardizzate per testare algoritmi di apprendimento per rinforzo nei giochi Atari classici.

Questo esempio usa un algoritmo di training noto come [IMPALA](https://arxiv.org/abs/1802.01561) (Importance Weighted Actor-Learner Architecture). IMPALA parallelizza ogni singolo attore di apprendimento per ridimensionare tra molti nodi di calcolo senza sacrificare velocità o stabilità.

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) coordina le attività di lavoro IMPALA.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Registrazione della funzione di callback


Lo script di immissione usa una funzione di utilità per definire una [funzione di callback RLlib personalizzata](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) per registrare le metriche nell'area di lavoro Azure Machine Learning. Informazioni su come visualizzare queste metriche nella sezione [Monitorare e visualizzare i risultati](#monitor-and-view-results).

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Inviare un'esecuzione

L'oggetto [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) gestisce la cronologia di esecuzione dei processi in corso o completati. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Il completamento dell'esecuzione può richiedere da 30 a 45 minuti.

## <a name="monitor-and-view-results"></a>Monitorare e visualizzare i risultati

Usare il widget Azure Machine Learning Jupyter per visualizzare lo stato delle esecuzioni in tempo reale. In questo esempio il widget mostra due esecuzioni figlio: una per head e una per i ruoli di lavoro. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Attendere il caricamento del widget.
1. Selezionare l'esecuzione head nell'elenco di esecuzioni.

Selezionare **Click here to see the run in Azure Machine Learning studio** (Fare clic qui per visualizzare l'esecuzione in Azure Machine Learning Studio) per informazioni aggiuntive sull'esecuzione in studio. È possibile accedere a queste informazioni mentre l'esecuzione è in corso o dopo il completamento.

![Grafico lineare che mostra la modalità di esecuzione del widget di dettagli](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

Il tracciato **episode_reward_mean** indica la media dei punti realizzati per ogni epoch di training. Si può notare che l'agente di training consegue inizialmente un risultato scarso, perdendo le proprie corrispondenze senza assegnare un punteggio a un singolo punto (visualizzato da un reward_mean pari a -21). All'interno di 100 iterazioni, l'agente di training impara a battere il computer avversario con una media di 18 punti.

Esplorando i log dell'esecuzione figlio è possibile visualizzare i risultati della valutazione registrati nel file driver_log.txt. Potrebbe essere necessario attendere alcuni minuti prima che le metriche risultino disponibili nella pagina di esecuzione.

In breve, si è appreso come configurare più risorse di calcolo per eseguire il training di un agente di apprendimento per rinforzo, per giocare molto bene a Pong.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire il training di un agente di apprendimento per rinforzo usando un agente di apprendimento IMPALA. Per altri esempi, vedere il [repository GitHub Azure Machine Learning Reinforcement Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).