---
title: Eseguire il training di modelli di Machine Learning con una classe Estimator di Azure Machine Learning
description: Informazioni su come eseguire il training su nodo singolo e distribuito di modelli di Machine Learning tradizionale e di apprendimento avanzato usando la classe Estimator del servizio Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: c47761c184d0e6c091ff49b3eca2fdf89574b49d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114860"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Come eseguire il training di modelli con Azure Machine Learning

Il training di modelli di machine learning, in particolare di reti neurali avanzate, è un'attività che richiede spesso molto tempo e risorse di calcolo. Dopo aver completato la scrittura dello script di training e la sua esecuzione su un subset ridotto di dati nel computer locale, si aumenterà il carico di lavoro.

Per facilitare il training, Python SDK di Azure Machine Learning fornisce un'astrazione di alto livello, la classe Estimator, che consente agli utenti di eseguire con facilità il training dei propri modelli nell'ecosistema di Azure. È possibile creare e usare un oggetto `Estimator` per inviare qualsiasi codice di training da eseguire su risorse di calcolo remote, che si tratti di un'esecuzione su nodo singolo o di training distribuito attraverso un cluster GPU. Per i processi PyTorch e TensorFlow, Azure Machine Learning fornisce anche oggetti Estimator personalizzati rispettivamente per `PyTorch` e `TensorFlow`, che facilitano l'uso di questi framework.

## <a name="train-with-an-estimator"></a>Eseguire il training con un Estimator

Dopo aver creato l'[area di lavoro](concept-azure-machine-learning-architecture.md#workspace) e configurato l'[ambiente di sviluppo](how-to-configure-environment.md), il training di un modello in Azure Machine Learning prevede i passaggi seguenti:  
1. Creare una [destinazione di calcolo remoto](how-to-set-up-training-targets.md)
2. Caricare i [dati di training](how-to-access-data.md) (facoltativo)
3. Creare lo [script di training](tutorial-train-models-with-aml.md#create-a-training-script)
4. Crea un oggetto `Estimator`
5. Inviare il processo di training

Questo articolo è incentrato sui passaggi 4-5. Per i passaggi da 1 a 3, fare riferimento all'[esercitazione Eseguire il training di un modello](tutorial-train-models-with-aml.md) per un esempio.

### <a name="single-node-training"></a>Training su nodo singolo

Usare un `Estimator` per un training su nodo singolo su risorse di calcolo remote in Azure per un modello scikit-learn. È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#batch) `compute_target` e l'oggetto [datastore](how-to-access-data.md) `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Questo frammento di codice specifica i parametri seguenti per il costruttore di `Estimator`.

Parametro | DESCRIZIONE
--|--
`source_directory`| Directory locale contenente tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale nelle risorse di calcolo remote 
`script_params`| Dizionario che specifica gli argomenti della riga di comando per lo script di training `entry_script`, sotto forma di coppie <argomento riga di comando, valore>
`compute_target`| Risorse di calcolo remote in cui verrà eseguito lo script di training, in questo caso un cluster [Batch per intelligenza artificiale](how-to-set-up-training-targets.md#batch)
`entry_script`| Percorso file (relativo a `source_directory`) dello script di training da eseguire nelle risorse di calcolo remote. Questo file e gli eventuali file aggiuntivi da cui dipende, devono trovarsi in questa cartella
`conda_packages`| Elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training.  
Il costruttore ha un altro parametro chiamato `pip_packages` che è possibile usare per gli eventuali pacchetti pip necessari

Ora che è stato creato l'oggetto `Estimator`, inviare il processo di training per l'esecuzione sulle risorse di calcolo remote tramite una chiamata alla funzione `submit` sull'oggetto [Experiment](concept-azure-machine-learning-architecture.md#experiment)`experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Cartelle speciali** Due cartelle *outputs* e *logs*, hanno un trattamento speciale in Azure Machine Learning. Durante il training, se si scrivono file in cartelle denominate *outputs* e *logs* relative alla directory radice (rispettivamente `./outputs` e `./logs`), i file verranno caricati automaticamente nella cronologia di esecuzione e vi si potrà accedere al termine dell'esecuzione.
>
> Per creare artefatti creati durante il training (ad esempio file modello, checkpoint, file di dati o immagini tracciate), scriverli nella cartella `./outputs`.
>
> Analogamente, è possibile scrivere tutti i log del training eseguito nella cartella `./logs`. Per usare l'[integrazione TensorBoard](https://aka.ms/aml-notebook-tb) di Azure Machine Learning, assicurarsi di scrivere i log di TensorBoard in questa cartella. Mentre è in corso l'esecuzione, sarà possibile avviare TensorBoard e riprodurre in streaming questi log.  In seguito sarà anche possibile ripristinare i log di una qualsiasi delle esecuzioni precedenti.
>
> Ad esempio, per scaricare un file scritto nella cartella *outputs* del computer locale dopo l'esecuzione del training remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Training distribuito e immagini Docker personalizzate

Esistono due scenari di training aggiuntivi che è possibile eseguire con `Estimator`:
* Uso di un'immagine Docker personalizzata
* Training distribuito in un cluster con più nodi

Il codice seguente mostra come eseguire il training distribuito per un modello CNTK. Presume inoltre che, anziché usare le immagini di Azure Machine Learning predefinite, si disponga di un'immagine Docker personalizzata per il training.

È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#batch) `compute_target`. È possibile creare l'oggetto Estimator come segue:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

Il codice precedente espone i nuovi parametri seguenti al costruttore di `Estimator`:

Parametro | DESCRIZIONE | Predefinito
--|--|--
`custom_docker_base_image`| Nome dell'immagine da usare. Specificare solo immagini disponibili in repository Docker pubblici (in questo caso Hub Docker). Per usare un'immagine da un repository Docker privato, usare invece il parametro `environment_definition` del costruttore | `None`
`node_count`| Numero di nodi da usare per il processo di training. | `1`
`process_count_per_node`| Numero di processi (o "ruoli di lavoro") da eseguire in ogni nodo. In questo caso si usano i `2` GPU disponibili in ogni nodo.| `1`
`distributed_backend`| Back-end per l'avvio del training distribuito, offerto da Estimator tramite MPI.  Per eseguire il training parallelo o distribuito (ad esempio `node_count`> 1 o `process_count_per_node`> 1 o entrambi), impostare `distributed_backend='mpi'`. L'implementazione MPI usata da Azure Machine Learning è [Open MPI](https://www.open-mpi.org/).| `None`

Infine, inviare il processo di training:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Esempi
Per un'esercitazione sul training di un modello sklearn, vedere:
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Per un'esercitazione su CNTK distribuito con un'immagine Docker personalizzata, vedere:
* [training/06.distributed-cntk-with-custom-docker](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/06.distributed-cntk-with-custom-docker)

Ottenere questi notebook:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Eseguire il training di modelli di PyTorch](how-to-train-pytorch.md)
* [Eseguire il training di modelli di TensorFlow](how-to-train-tensorflow.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
