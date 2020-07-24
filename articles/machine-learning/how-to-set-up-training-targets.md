---
title: Usare destinazioni di calcolo per il training del modello
titleSuffix: Azure Machine Learning
description: Configurare gli ambienti di training (destinazioni di calcolo) per il training del modello di Machine Learning. Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 0f3682338c9373f3ba30c8b32ea5cf4132c18949
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048279"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurare e usare destinazioni di calcolo per il training del modello 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Con Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, collettivamente definiti [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.  È possibile anche creare destinazioni di calcolo per la distribuzione del modello, come descritto in ["Dove e come distribuire i modelli"](how-to-deploy-and-where.md).

È possibile creare e gestire una destinazione di calcolo usando Azure Machine Learning SDK, Azure Machine Learning Studio, l'interfaccia della riga di comando di Azure o l'estensione di VS Code per Azure Machine Learning. Se si dispone di destinazioni di calcolo create tramite un altro servizio, ad esempio un cluster HDInsight, è possibile usarle associandole all'area di lavoro di Azure Machine Learning.
 
Questo articolo illustra come usare diverse destinazioni di calcolo per il training del modello.  I passaggi per tutte le destinazioni di calcolo seguono lo stesso flusso di lavoro:
1. __Creare__ una destinazione di calcolo se non ne esiste già una.
2. __Collegare__ la destinazione di calcolo all'area di lavoro.
3. __Configurare__ le destinazioni di calcolo affinché contengano l'ambiente Python e le dipendenze del pacchetto necessari per lo script.


>[!NOTE]
> Il codice di questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.74.

## <a name="compute-targets-for-training"></a>Destinazioni di calcolo per il training

Azure Machine Learning offre un supporto variabile per le diverse destinazioni di calcolo. Un tipico ciclo di vita di sviluppo modello inizia con lo sviluppo e la sperimentazione su una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, ad esempio il computer locale o una macchina virtuale basata sul cloud. Quando il training viene eseguito su set di dati più grandi, o quando si esegue il training distribuito, è consigliabile usare l'ambiente di calcolo di Azure Machine Learning per creare un cluster a uno o più nodi che viene ridimensionato automaticamente ogni volta che viene inviata un'esecuzione. È possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato di seguito:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning cluster di calcolo possono essere creati come risorsa persistente o creati dinamicamente quando si richiede un'esecuzione. Al termine del training, la creazione basata sull'esecuzione rimuove la destinazione di calcolo. Non è quindi possibile riutilizzare le destinazioni di calcolo create in questo modo.

## <a name="whats-a-run-configuration"></a>Che cos'è una configurazione di esecuzione?

Solitamente il training si avvia nel computer locale e in un secondo momento si esegue tale script di training in una destinazione di calcolo diversa. Con Azure Machine Learning è possibile eseguire lo script in diverse destinazioni di calcolo senza doverlo modificare.

È sufficiente definire l'ambiente per ogni destinazione di calcolo in una **configurazione di esecuzione**.  Quindi, quando si vuole eseguire l'esperimento di training in una destinazione di calcolo diversa, specificare la configurazione di esecuzione per tale ambiente di calcolo. Per informazioni dettagliate su come specificare un ambiente e associarlo alla configurazione di esecuzione, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).

Altre informazioni sull'[invio degli esperimenti](#submit) alla fine di questo articolo.

## <a name="whats-an-estimator"></a>Che cos'è un oggetto Estimator?

Per semplificare il training dei modelli con i framework più diffusi, Python SDK di Azure Machine Learning offre un'astrazione alternativa di più alto livello, la classe Estimator,  che consente agli utenti di creare con facilità configurazioni di esecuzione. È possibile creare e usare un oggetto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training che usano qualsiasi framework di apprendimento scelto, ad esempio scikit-learn. È consigliabile usare un oggetto Estimator per il training perché crea automaticamente oggetti incorporati come un ambiente oppure oggetti RunConfiguration. Per avere un maggiore controllo sulla modalità di creazione di questi oggetti e specificare i pacchetti da installare per l'esecuzione dell'esperimento, seguire [questa procedura](#amlcompute) per inviare gli esperimenti di training usando un oggetto RunConfiguration in un ambiente di calcolo di Azure Machine Learning.

Azure Machine Learning offre oggetti Estimator specifici per [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) e [Ray RLlib](how-to-use-reinforcement-learning.md).

Per altre informazioni, vedere [Eseguire il training di modelli di Machine Learning usando l'oggetto Estimator](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Che cos'è una pipeline di Machine Learning?

Con le pipeline di Machine Learning, è possibile ottimizzare il flusso di lavoro con maggiore semplicità, velocità, portabilità e riutilizzo. Quando si creano pipeline con Azure Machine Learning è possibile concentrarsi sulle proprie competenze in Machine Learning invece che sull'infrastruttura e sull'automazione.

Le pipeline di Machine Learning sono costituite da più **passaggi**, che corrispondono a unità di calcolo distinte nella pipeline. Ogni passaggio può essere eseguito in modo indipendente e può usare risorse di calcolo isolate. Questo approccio consente a più data scientist di lavorare contemporaneamente sulla stessa pipeline senza sovraccaricare le risorse di calcolo e semplifica l'uso di diversi tipi/dimensioni di ambienti di calcolo per ogni passaggio.

> [!TIP]
> Le pipeline di Machine Learning possono usare la configurazione di esecuzione o gli oggetti Estimator per il training di modelli.

Le pipeline di Machine Learning possono eseguire il training dei modelli, ma possono anche preparare i dati prima del training e distribuire i modelli dopo il training. Uno dei casi d'uso principali per le pipeline è l'assegnazione di punteggi batch. Per altre informazioni, vedere l'articolo [Pipeline: ottimizzazione dei flussi di lavoro di Machine Learning](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Configurare in Python

Usare le sezioni seguenti per configurare queste destinazioni di calcolo:

* [Computer locale](#local)
* [Cluster di calcolo di Azure Machine Learning](#amlcompute)
* [Istanza di calcolo di Azure Machine Learning](#instance)
* [Macchine virtuali remote](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Computer locale

1. **Creare e collegare**: Non è necessario creare o collegare una destinazione di calcolo per usare il computer locale come ambiente di training.  

1. **Configurare**:  Quando si usa il computer locale come destinazione di calcolo, il codice di training viene eseguito nell'[ambiente di sviluppo](how-to-configure-environment.md).  Se l'ambiente contiene già i pacchetti di Python necessari, usare l'ambiente gestito dall'utente.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning cluster di calcolo

Azure Machine Learning cluster di calcolo è un'infrastruttura di calcolo gestito che consente di creare facilmente un calcolo a nodo singolo o a più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

È possibile usare un ambiente di calcolo di Azure Machine Learning per distribuire il processo di training in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](/how-to-manage-quotas.md).


> [!TIP]
> I cluster possono in genere essere ridimensionati fino a 100 nodi, purché si disponga di una quota sufficiente per il numero di core necessari. Per impostazione predefinita, i cluster sono impostati con la comunicazione tra i nodi abilitata, ad esempio per supportare i processi MPI. È tuttavia possibile ridimensionare i cluster a migliaia di nodi semplicemente [generando un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)e richiedendo di consentire l'elenco della sottoscrizione o dell'area di lavoro o di un cluster specifico per la disabilitazione della comunicazione tra nodi. 

Un ambiente di calcolo di Azure Machine Learning può essere usato su più esecuzioni. L'ambiente di calcolo può essere condiviso con altri utenti nell'area di lavoro e mantenuto da un'esecuzione all'altra, ridimensionando automaticamente i nodi in base al numero di esecuzioni inviate e all'impostazione max_nodes definita nel cluster. L'impostazione min_nodes controlla il numero minimo di nodi disponibili.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Creare e collegare**: Per creare una risorsa permanente di ambiente di calcolo di Machine Learning in Python, specificare le proprietà **vm_size** e **max_nodes**. Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà. Il calcolo viene ridimensionato automaticamente fino a zero nodi quando non viene usato.   Le macchine virtuali dedicate vengono create per eseguire i processi in base alle esigenze.
    
    * **vm_size**: la famiglia di macchine virtuali dei nodi creati dall'ambiente di calcolo di Azure Machine Learning.
    * **max_nodes**: il numero massimo di nodi per la scalabilità automatica durante l'esecuzione di un processo in un ambiente di calcolo di Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Durante la creazione di un ambiente di calcolo di Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente di dimensione fissa o all'interno di una Rete virtuale di Azure esistente nella sottoscrizione.  Per informazioni dettagliate, consultare [AmlCompute class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) (Classe AmlCompute).

    In alternativa, è possibile creare e collegare una risorsa permanente dell'ambiente di calcolo di Azure Machine Learning in [Azure Machine Learning Studio](#portal-create).

   
1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo permanente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a>Abbassare il costo del cluster di calcolo

È anche possibile scegliere di usare [macchine virtuali con priorità bassa](concept-plan-manage-cost.md#low-pri-vm) per eseguire alcuni o tutti i carichi di lavoro. Queste macchine virtuali non hanno una disponibilità garantita e possono essere terminate durante l'uso. Un processo interrotto viene riavviato, non ripreso. 

Usare uno di questi modi per specificare una macchina virtuale con priorità bassa:
    
* In Studio scegliere **priorità bassa** quando si crea una macchina virtuale.
    
* Con Python SDK, impostare l' `vm_priority` attributo nella configurazione del provisioning.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Usando l'interfaccia della riga di comando, impostare `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```



### <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Istanza di calcolo di Azure Machine Learning

[Azure Machine Learning istanza di calcolo](concept-compute-instance.md) è un'infrastruttura di calcolo gestito che consente di creare facilmente una singola macchina virtuale. Il calcolo viene creato all'interno dell'area dell'area di lavoro, ma a differenza di un cluster di calcolo, un'istanza non può essere condivisa con altri utenti nell'area di lavoro. Inoltre, l'istanza non viene ridimensionata automaticamente.  Per evitare addebiti continui, è necessario arrestare la risorsa.

Un'istanza di calcolo può eseguire più processi in parallelo e dispone di una coda di processi. 

Le istanze di calcolo possono eseguire processi in modo sicuro in un [ambiente di rete virtuale](how-to-enable-virtual-network.md#compute-instance), senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker. 

1. **Creare e collegare**: 
    
    [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb? Name = create_instance)]

1. **Configure**: creare una configurazione di esecuzione.
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

Per altri comandi utili per l'istanza di calcolo, vedere il notebook [Train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Questo notebook è disponibile anche nella cartella **esempi** di studio in *Training/Train-on-computeinstance*.

Ora che è stato collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste nell' [inviare l'esecuzione del training](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Macchine virtuali remote

Azure Machine Learning consente di usare la risorsa di calcolo personale e di associarla all'area di lavoro. Una risorsa di questo tipo è una macchina virtuale remota arbitraria, purché sia accessibile da Azure Machine Learning. La risorsa può essere una macchina virtuale di Azure, un server remoto nell'organizzazione o in locale. In particolare, se si dispone dell'indirizzo IP e delle credenziali (nome utente e password o chiave SSH), è possibile usare qualsiasi macchina virtuale accessibile per le esecuzioni remote.

È possibile usare un ambiente conda integrato nel sistema, un ambiente Python già esistente o un contenitore Docker. Per l'esecuzione in un contenitore Docker, è necessario che il motore Docker sia in esecuzione nella macchina virtuale. Questa funzionalità è particolarmente utile quando si preferisce un ambiente di sviluppo o sperimentazione basato sul cloud più flessibile rispetto al computer locale.

Usare la Data Science Virtual Machine di Azure (DSVM) come macchina virtuale di Azure preferita per questo scenario. Questa macchina virtuale è un ambiente preconfigurato di data science e di sviluppo per l'intelligenza artificiale in Azure. La macchina virtuale offre una scelta dettagliata di strumenti e framework per l'intero ciclo di vita dello sviluppo dell'apprendimento automatico. Per altre informazioni su come usare una Data Science Virtual Machine, consultare [Configurare un ambiente di sviluppo per Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Creazione**: Creare una Data Science Virtual Machine prima di usarla per eseguire il training del modello. Per creare questa risorsa consultare [Effettuare il provisioning della Data Science Virtual Machine per Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning supporta solo le macchine virtuali che eseguono **Ubuntu**. Quando si crea una macchina virtuale o se ne sceglie una esistente, è necessario selezionare una macchina virtuale che usa Ubuntu.
    > 
    > Azure Machine Learning richiede anche che la macchina virtuale disponga di un __indirizzo IP pubblico__.

1. **Collegare**: Per associare una macchina virtuale esistente come destinazione di calcolo, è necessario specificare l'ID risorsa, il nome utente e la password per la macchina virtuale. L'ID risorsa della macchina virtuale può essere creato usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome della macchina virtuale nel formato stringa seguente: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   In alternativa, è possibile collegare la Data Science VM all'area di lavoro [usando Azure Machine Learning Studio](#portal-reuse).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight è una piattaforma comune per l'analisi dei Big Data. La piattaforma include Apache Spark, che può essere usato per il training del modello.

1. **Creazione**:  Creare il cluster HDInsight prima di usarlo per eseguire il training del modello. Per creare un cluster Spark in HDInsight, consultare [Creare un cluster Spark in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning richiede che il cluster HDInsight disponga di un __indirizzo IP pubblico__.

    Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
    
    Dopo la creazione del cluster, connettersi con il \<clustername> nome host-SSH.azurehdinsight.NET, dove \<clustername> è il nome fornito per il cluster. 

1. **Collegare**: Per collegare un cluster HDInsight come destinazione di calcolo, è necessario specificare l'ID risorsa, il nome utente e la password per il cluster HDInsight. L'ID risorsa del cluster HDInsight può essere creato usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome del cluster HDInsight nel formato stringa seguente: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   In alternativa, è possibile collegare il cluster HDInsight all'area di lavoro [usando Azure Machine Learning Studio](#portal-reuse).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch viene usato per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. AzureBatchStep può essere usato in una pipeline di Azure Machine Learning per inviare processi a un pool di computer di Azure Batch.

Per collegare Azure Batch come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

-    **Nome dell'ambiente di calcolo di Azure Batch**: un nome descrittivo da usare per l'ambiente di calcolo nell'area di lavoro.
-    **Nome dell'account Azure Batch**: il nome dell'account Azure Batch.
-    **Gruppo di risorse**: il gruppo di risorse che contiene l'account Azure Batch.

Il codice seguente illustra come collegare Azure Batch come destinazione di calcolo:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Configurare in Azure Machine Learning Studio

In Azure Machine Learning Studio è possibile accedere alle destinazioni di calcolo associate all'area di lavoro.  È possibile usare Studio per:

* [Visualizzare le destinazioni di calcolo](#portal-view) collegate all'area di lavoro
* [Creare una destinazione di calcolo](#portal-create) nell'area di lavoro
* [Collegare una destinazione di calcolo](#portal-reuse) creata esternamente all'area di lavoro


Dopo la creazione e il collegamento di una destinazione all'area di lavoro, questa verrà usata nella configurazione di esecuzione con un oggetto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Vista destinazioni di calcolo


Per visualizzare le destinazioni di calcolo dell'area di lavoro, usare la procedura seguente:

1. Passare ad [Azure Machine Learning Studio](https://ml.azure.com).
 
1. In __Applicazioni__ selezionare __Ambiente di calcolo__.

    [![Scheda per la visualizzazione dell'ambiente di calcolo](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per creare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo.

    ![Aggiungere una destinazione di calcolo](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Immettere un nome per la destinazione di calcolo. 

1. Selezionare l'**ambiente di calcolo di Machine Learning** come tipo di ambiente di calcolo da usare per il __training__. 

    >[!NOTE]
    >L'ambiente di calcolo di Azure Machine Learning è l'unica risorsa di calcolo gestito che è possibile creare in Azure Machine Learning Studio.  Tutte le altre risorse di calcolo si possono collegare dopo averle create.

1. Compilare il modulo. Specificare i valori per le proprietà necessarie, in particolare la **famiglia di macchine virtuali**e il **numero massimo di nodi** da usare per creare rapidamente l'ambiente di calcolo.  

1. Selezionare __Create__ (Crea).


1. Visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco:

    ![Selezionare una destinazione di calcolo per visualizzare lo stato dell'operazione di creazione](./media/how-to-set-up-training-targets/View_list.png)

1. Verranno mostrati i dettagli della destinazione di calcolo: 

    ![Visualizzare i dettagli della destinazione di calcolo](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Collegare destinazioni di calcolo

Per usare le destinazioni di calcolo create al di fuori dell'area di lavoro di Azure Machine Learning, è necessario collegarle. Il collegamento di una destinazione di calcolo rende quest'ultima disponibile nell'area di lavoro.

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi i passaggi seguenti per collegare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo. 
1. Immettere un nome per la destinazione di calcolo. 
1. Selezionare il tipo di calcolo da associare per __Training__:

    > [!IMPORTANT]
    > Non tutti i tipi di calcolo possono essere collegati usando Azure Machine Learning Studio. I tipi di calcolo che possono essere allegati per il training sono:
    >
    > * Macchina virtuale remota
    > * Azure Databricks (per l'uso nelle pipeline di machine learning)
    > * Azure Data Lake Analytics (per l'uso nelle pipeline di machine learning)
    > * HDInsight di Azure

1. Compilare il modulo e fornire i valori per le proprietà necessarie.

    > [!NOTE]
    > Microsoft consiglia di usare le chiavi SSH, che sono più sicure rispetto alle password. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. Le chiavi SSH si basano sulle firme di crittografia. Per informazioni sulla creazione di chiavi SSH per l'uso con Macchine virtuali di Azure, vedere i documenti seguenti:
    >
    > * [Create and use SSH keys on Linux or macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Creare e usare chiavi SSH in Linux o macOS)
    > * [Create and use SSH keys on Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Creare e usare chiavi SSH in Windows)

1. Selezionare __Allega__. 
1. Visualizzare lo stato dell'operazione di collegamento selezionando la destinazione di calcolo dall'elenco.

## <a name="set-up-with-cli"></a>Configurare con l'interfaccia della riga di comando

È possibile accedere alle destinazioni di calcolo associate all'area di lavoro usando l'[estensione dell'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) per Azure Machine Learning.  È possibile usare l'interfaccia della riga di comando per:

* Creare una destinazione di calcolo gestita
* Aggiornare una destinazione di calcolo gestita
* Collegare una destinazione di calcolo non gestita

Per altre informazioni consultare [Gestione delle risorse](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurare con VS Code

È possibile accedere, creare e gestire le destinazioni di calcolo collegate all'area di lavoro usando l'[estensione di VS Code](how-to-manage-resources-vscode.md#compute-clusters) per Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Inviare l'esecuzione di training usando Azure Machine Learning SDK

Dopo aver creato una configurazione di esecuzione, questa si usa per l'esecuzione dell'esperimento.  Il criterio di codice per inviare l'esecuzione di un training è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un esperimento da eseguire
1. Inviare l'esecuzione.
1. Attendere il completamento dell'esecuzione.

> [!IMPORTANT]
> Quando si invia l'esecuzione di training, viene creato uno snapshot della directory contenente gli script di training che viene quindi inviato alla destinazione di calcolo. Lo snapshot viene inoltre archiviato come parte dell'esperimento nell'area di lavoro. Se si modificano i file e si invia di nuovo l'esecuzione, verranno caricati solo i file modificati.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Per altre informazioni, vedere [Snapshot](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento nell'area di lavoro.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Inviare l'esperimento

Inviare l'esperimento con un oggetto `ScriptRunConfig`.  Questo oggetto include:

* **source_directory**: la directory di origine che contiene lo script di training
* **script**: identifica lo script di training
* **run_config**: la configurazione di esecuzione, che a sua volta definisce dove verrà eseguito il training.

Ad esempio, per usare la configurazione [destinazione locale](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Eseguire lo stesso esperimento in una destinazione di calcolo diversa tramite una configurazione di esecuzione differente, come la [destinazione amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Questo esempio usa per impostazione predefinita un solo nodo della destinazione di calcolo per il training. Per usare più nodi, specificare il numero di nodi desiderato per l'impostazione `node_count` della configurazione di esecuzione. Il codice seguente, ad esempio, imposta su quattro il numero di nodi usati per il training:
>
> ```python
> src.run_config.node_count = 4
> ```

In alternativa, è possibile:

* Inviare l'esperimento con un oggetto `Estimator` come illustrato in [Eseguire il training di modelli di Machine Learning con oggetti Estimator](how-to-train-ml-models.md).
* Inviare un'esecuzione di HyperDrive per l'[ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md).
* Inviare un esperimento tramite l'[estensione di VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Per altre informazioni, vedere la documentazione di [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py).

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Creare una configurazione di esecuzione e inviare l'esecuzione usando l'interfaccia della riga di comando di Azure Machine Learning

È possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e l'[estensione dell'interfaccia della riga di comando di Machine Learning](reference-azure-machine-learning-cli.md) per creare configurazioni di esecuzione e inviare esecuzioni su destinazioni di calcolo diverse. Gli esempi seguenti presuppongono che sia disponibile un'area di lavoro di Azure Machine Learning e che sia stato effettuato l'accesso ad Azure tramite il comando `az login` dell'interfaccia della riga di comando. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Creare la configurazione di esecuzione

Il modo più semplice per creare la configurazione di esecuzione è quello di esplorare la cartella che contiene gli script Python di Machine Learning e usare il comando dell'interfaccia della riga di comando

```azurecli
az ml folder attach
```

Questo comando crea una sottocartella `.azureml` che contiene i file modello della configurazione di esecuzione per diverse destinazioni di calcolo. È possibile copiare e modificare questi file per personalizzare la configurazione, ad esempio per aggiungere pacchetti Python o modificare le impostazioni di Docker.  

### <a name="structure-of-run-configuration-file"></a>Struttura del file della configurazione di esecuzione

Il file della configurazione di esecuzione è in formato YAML ed è costituito dalle sezioni seguenti
 * Lo script da eseguire e i relativi argomenti
 * Il nome della destinazione di calcolo, ovvero "local" o il nome di un ambiente di calcolo nell'area di lavoro.
 * I parametri per l'esecuzione: framework, communicator per le esecuzioni distribuite, durata massima e numero dei nodi di calcolo.
 * La sezione relativa all'ambiente. Per informazioni dettagliate sui campi di questa sezione, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).
   * Per specificare i pacchetti Python da installare per l'esecuzione, creare un [file di ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually) e impostare il campo __condaDependenciesFile__.
 * Dettagli della cronologia di esecuzione per specificare la cartella del file di log e per abilitare o disabilitare gli snapshot della raccolta di output e della cronologia di esecuzione.
 * Dettagli di configurazione specifici del framework selezionato.
 * Informazioni di riferimento sui dati e archivio dati.
 * Dettagli di configurazione specifici per l'ambiente di calcolo di Machine Learning per la creazione di un nuovo cluster.

Vedere il [file JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) di esempio per uno schema runconfig completo.

### <a name="create-an-experiment"></a>Creare un esperimento

Prima di tutto, creare un esperimento per le esecuzioni

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Esecuzione di script

Per inviare un'esecuzione di script, eseguire un comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Esecuzione di HyperDrive

È possibile usare HyperDrive con l'interfaccia della riga di comando di Azure per le esecuzioni di ottimizzazione dei parametri. Prima di tutto, creare un file di configurazione di HyperDrive nel formato seguente. Per informazioni dettagliate sui parametri di ottimizzazione degli iperparametri, vedere l'articolo [Ottimizzare gli iperparametri per il modello](how-to-tune-hyperparameters.md).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Aggiungere questo file insieme ai file della configurazione di esecuzione. Inviare quindi un'esecuzione HyperDrive usando il codice seguente:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Prendere nota della sezione *arguments* in runconfig e dello *spazio dei parametri* nella configurazione di HyperDrive. Contengono gli argomenti della riga di comando da passare allo script di training. Il valore in runconfig rimane invariato per ogni iterazione, mentre l'intervallo nella configurazione di HyperDrive viene iterato. Non specificare lo stesso argomento in entrambi i file.

Per altri dettagli su questi comandi dell'interfaccia della riga di comando ```az ml```, vedere la [documentazione di riferimento](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Consultare le informazioni sull'SDK di [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usare Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)
