---
title: Creare e usare destinazioni di calcolo per il training del modello
titleSuffix: Azure Machine Learning service
description: Configurare gli ambienti di training (destinazioni di calcolo) per il training del modello di Machine Learning. Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 07176fbe22e70658856dd266687a15d719e78e9f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231092"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurare e usare le destinazioni di calcolo per il training del modello 

Con il servizio Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, collettivamente definiti [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Machine Learning, Azure HDInsight o una macchina virtuale remota.  È possibile anche creare destinazioni di calcolo per la distribuzione del modello, come descritto in ["Dove e come distribuire i modelli"](how-to-deploy-and-where.md).

È possibile creare e gestire una destinazione di calcolo usando il Azure Machine Learning SDK, portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione di VS Code Azure Machine Learning. Se si dispone di destinazioni di calcolo create tramite un altro servizio, ad esempio un cluster HDInsight, è possibile usarle associandole all'area di lavoro del servizio Azure Machine Learning.
 
Questo articolo illustra come usare diverse destinazioni di calcolo per il training del modello.  I passaggi per tutte le destinazioni di calcolo seguono lo stesso flusso di lavoro:
1. __Creare__ una destinazione di calcolo se non ne esiste già una.
2. __Collegare__ la destinazione di calcolo all'area di lavoro.
3. __Configurare__ le destinazioni di calcolo affinché contengano l'ambiente Python e le dipendenze del pacchetto necessari per lo script.


>[!NOTE]
> Il codice di questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.39.

## <a name="compute-targets-for-training"></a>Destinazioni di calcolo per il training

Il servizio Azure Machine Learning offre un supporto variabile per le diverse destinazioni di calcolo. Un tipico ciclo di vita di sviluppo modello inizia con lo sviluppo e la sperimentazione su una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, ad esempio il computer locale o una macchina virtuale basata sul cloud. Quando il training viene eseguito su set di dati più grandi, o quando si esegue il training distribuito, è consigliabile usare l'ambiente di calcolo di Azure Machine Learning per creare un cluster a uno o più nodi che viene ridimensionato automaticamente ogni volta che viene inviata un'esecuzione. È possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato di seguito:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> L'ambiente di calcolo di Azure Machine Learning può essere creato come risorsa permanente o in modo dinamico quando si richiede un'esecuzione. Al termine del training, la creazione basata sull'esecuzione rimuove la destinazione di calcolo. Non è quindi possibile riutilizzare le destinazioni di calcolo create in questo modo.

## <a name="whats-a-run-configuration"></a>Che cos'è una configurazione di esecuzione?

Solitamente il training si avvia nel computer locale e in un secondo momento si esegue tale script di training in una destinazione di calcolo diversa. Con il servizio Azure Machine Learning è possibile eseguire lo script in diverse destinazioni di calcolo senza doverlo modificare. 

È sufficiente definire l'ambiente per ogni destinazione di calcolo all'interno di una configurazione di **esecuzione**.  Quindi, quando si vuole eseguire l'esperimento di training in una destinazione di calcolo diversa, specificare la configurazione di esecuzione per tale ambiente di calcolo. Per informazioni dettagliate su come specificare un ambiente e associarlo per eseguire la configurazione, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md)

Altre informazioni sull'[invio degli esperimenti](#submit) alla fine di questo articolo.

## <a name="whats-an-estimator"></a>Che cos'è un estimatore?

Per semplificare il training del modello usando i Framework più diffusi, il Azure Machine Learning Python SDK fornisce un'astrazione di livello superiore alternativa, la classe Estimator. Questa classe consente di creare facilmente configurazioni di esecuzione. È possibile creare e usare uno strumento di [stima](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training che usano qualsiasi framework di apprendimento scelto, ad esempio Scikit-learn.

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche i rispettivi estimatori [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) per semplificare l'uso di questi Framework.

Per altre informazioni, vedere [training di modelli ml con estimatori](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Che cos'è una pipeline ML?

Con le pipeline ML è possibile ottimizzare il flusso di lavoro con semplicità, velocità, portabilità e riutilizzo. Quando si compilano pipeline con Azure Machine Learning, è possibile concentrarsi sulla propria esperienza, Machine Learning, anziché sull'infrastruttura e l'automazione.

Le pipeline di ML sono costituite da più **passaggi**, ovvero unità di calcolo distinte nella pipeline. Ogni passaggio può essere eseguito in modo indipendente e usare risorse di calcolo isolate. In questo modo, più data scientist possono lavorare contemporaneamente sulla stessa pipeline senza dover sovraccaricare le risorse di calcolo e semplificare l'uso di diversi tipi/dimensioni di calcolo per ogni passaggio.

> [!TIP]
> Le pipeline di ML possono usare la configurazione di esecuzione o gli estimatori durante il training di modelli.

Mentre le pipeline di ML possono eseguire il training dei modelli, possono anche preparare i dati prima di eseguire il training e distribuire i modelli dopo il training. Uno dei casi d'uso principali per le pipeline è il Punteggio batch. Per ulteriori informazioni, vedere [pipeline: Ottimizzare i flussi di lavoro](concept-ml-pipelines.md)di machine learning.

## <a name="set-up-in-python"></a>Configurare in Python

Usare le sezioni seguenti per configurare queste destinazioni di calcolo:

* [Computer locale](#local)
* [Ambiente di calcolo di Machine Learning](#amlcompute)
* [Macchine virtuali remote](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Computer locale

1. **Creare e collegare**: Non è necessario creare o collegare una destinazione di calcolo per usare il computer locale come ambiente di training.  

1. **Configurare**:  Quando si usa il computer locale come destinazione di calcolo, il codice di training viene eseguito nell'[ambiente di sviluppo](how-to-configure-environment.md).  Se l'ambiente contiene già i pacchetti di Python necessari, usare l'ambiente gestito dall'utente.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Dopo aver collegato le risorse di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

### <a id="amlcompute"></a>Ambiente di calcolo di Machine Learning

L'ambiente di calcolo di Azure Machine Learning è un'infrastruttura di calcolo gestito che consente all'utente di creare facilmente un ambiente di calcolo a uno o più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

È possibile usare un ambiente di calcolo di Azure Machine Learning per distribuire il processo di training in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


È possibile creare l'ambiente di calcolo di Azure Machine Learning su richiesta quando si pianifica un'esecuzione, oppure come risorsa permanente.

#### <a name="run-based-creation"></a>Creazione basata su esecuzione

È possibile creare l'ambiente di calcolo di Machine Learning come destinazione di calcolo in fase di esecuzione. Il calcolo viene creato automaticamente per l'esecuzione. Il calcolo viene eliminato automaticamente dopo il completamento dell'esecuzione. 

> [!NOTE]
> Per specificare il numero massimo di nodi da usare, in genere si imposta `node_count` sul numero di nodi. Attualmente (04/04/2019) è presente un bug che impedisce il funzionamento di questo. Come soluzione alternativa, utilizzare la `amlcompute._cluster_max_node_count` proprietà della configurazione di esecuzione. Ad esempio `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> La creazione basata su esecuzione dell'ambiente di calcolo di Azure Machine Learning è attualmente disponibile in anteprima. Non usare la creazione basata su esecuzione se si usa l'ottimizzazione degli iperparametri o le funzionalità automatizzate di Machine Learning. Per usare l'ottimizzazione degli iperparametri o le funzionalità automatizzate di Machine Learning, creare una destinazione di [calcolo permanente](#persistent).

1.  **Creare, collegare e configurare**: La creazione basata su esecuzione esegue tutti i passaggi necessari per creare, collegare e configurare la destinazione di calcolo con la configurazione di esecuzione.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Dopo aver collegato le risorse di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

#### <a id="persistent"></a>Ambiente di calcolo permanente

Un ambiente di calcolo di Machine Learning permanente può essere usato nuovamente su più processi. Il calcolo può anche essere condiviso con altri utenti nell'area di lavoro e viene mantenuto tra i processi.

1. **Creare e collegare**: Per creare una risorsa permanente di ambiente di calcolo di Machine Learning in Python, specificare le proprietà **vm_size** e **max_nodes**. Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà. Il calcolo viene ridimensionato automaticamente fino a zero nodi quando non viene usato.   Le macchine virtuali dedicate vengono create per eseguire i processi in base alle esigenze.
    
    * **vm_size**: la famiglia di macchine virtuali dei nodi creati dall'ambiente di calcolo di Azure Machine Learning.
    * **max_nodes**: il numero massimo di nodi per la scalabilità automatica durante l'esecuzione di un processo in un ambiente di calcolo di Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Durante la creazione di un ambiente di calcolo di Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente di dimensione fissa o all'interno di una Rete virtuale di Azure esistente nella sottoscrizione.  Per informazioni dettagliate, consultare [AmlCompute class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) (Classe AmlCompute).
    
   Oppure è possibile creare e collegare una risorsa dell'ambiente di calcolo di Machine Learning permanente [nel portale di Azure](#portal-create).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo permanente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Dopo aver collegato le risorse di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).


### <a id="vm"></a>Macchine virtuali remote

Azure Machine Learning consente di usare la risorsa di calcolo personale e di associarla all'area di lavoro. Una risorsa di questo tipo è una macchina virtuale remota arbitraria, purché sia accessibile dal servizio Azure Machine Learning. La risorsa può essere una macchina virtuale di Azure, un server remoto nell'organizzazione o in locale. In particolare, se si dispone dell'indirizzo IP e delle credenziali (nome utente e password o chiave SSH), è possibile usare qualsiasi macchina virtuale accessibile per le esecuzioni remote.

È possibile usare un ambiente conda integrato nel sistema, un ambiente Python già esistente o un contenitore Docker. Per l'esecuzione in un contenitore Docker, è necessario che il motore Docker sia in esecuzione nella macchina virtuale. Questa funzionalità è particolarmente utile quando si preferisce un ambiente di sviluppo o sperimentazione basato sul cloud più flessibile rispetto al computer locale.

Per questo scenario usare Data Science Virtual Machine (DSVM) come macchina virtuale di Azure. Questa macchina virtuale è un ambiente preconfigurato di data science e di sviluppo per l'intelligenza artificiale in Azure. La macchina virtuale offre una scelta dettagliata di strumenti e framework per l'intero ciclo di vita dello sviluppo dell'apprendimento automatico. Per altre informazioni su come usare una Data Science Virtual Machine, consultare [Configurare un ambiente di sviluppo per Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Creazione**: Creare una Data Science Virtual Machine prima di usarla per eseguire il training del modello. Per creare questa risorsa consultare [Effettuare il provisioning della Data Science Virtual Machine per Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu. Quando si crea una macchina virtuale o se ne sceglie una esistente, è necessario selezionare una macchina virtuale che usa Ubuntu.

1. **Collegare**: Per associare una macchina virtuale esistente come destinazione di calcolo, è necessario specificare il nome di dominio completo (FQDN), il nome utente e la password per la macchina virtuale. Nell'esempio sostituire \<fqdn> con il nome di dominio completo pubblico della macchina virtuale o l'indirizzo IP pubblico. Nel comando seguente sostituire \<username> e \<password> con il nome utente e la password SSH per la macchina virtuale.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Oppure è possibile collegare la Data Science VM all'area di lavoro [usando il portale di Azure](#portal-reuse).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Dopo aver collegato le risorse di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight è una piattaforma comune per l'analisi dei Big Data. La piattaforma include Apache Spark, che può essere usato per il training del modello.

1. **Creazione**:  Creare il cluster HDInsight prima di usarlo per eseguire il training del modello. Per creare un cluster Spark in HDInsight, consultare [Creare un cluster Spark in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
    
    Dopo aver creato il cluster, collegarlo al nome host \<clustername>-ssh.azurehdinsight.net, dove \<clustername> è il nome fornito per il cluster. 

1. **Collegare**: Per collegare un cluster HDInsight come destinazione di calcolo, è necessario specificare il nome host, il nome utente e la password per il cluster HDInsight. Nell'esempio seguente viene usato l'SDK per associare un cluster all'area di lavoro. Nell'esempio sostituire \<clustername> con il nome del cluster. Sostituire \<username> e \<password> con un nuovo nome utente e una nuova password SSH il cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
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

   Oppure è possibile collegare il cluster HDInsight all'area di lavoro [usando il portale di Azure](#portal-reuse).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Dopo aver collegato le risorse di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch viene usato per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. AzureBatchStep può essere usato in una pipeline Azure Machine Learning per inviare processi a un pool di Azure Batch di computer.

Per aggiungere Azure Batch come destinazione di calcolo, è necessario usare Azure Machine Learning SDK e fornire le informazioni seguenti:

-   **Nome Azure batch Calcolo**: Nome descrittivo da usare per il calcolo nell'area di lavoro
-   **Nome account Azure batch**: Nome dell'account di Azure Batch
-   **Gruppo di risorse**: Il gruppo di risorse contenente l'account Azure Batch.

Il codice seguente illustra come aggiungere Azure Batch come destinazione di calcolo:

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

## <a name="set-up-in-azure-portal"></a>Configurare in portale di Azure

È possibile accedere alle destinazioni di calcolo associate all'area di lavoro nel portale di Azure.  Nel portale è possibile:

* [Visualizzare le destinazioni di calcolo](#portal-view) collegate all'area di lavoro
* [Creare una destinazione di calcolo](#portal-create) nell'area di lavoro
* [Collegare una destinazione di calcolo](#portal-reuse) creata esternamente all'area di lavoro

Dopo la creazione e il collegamento di una destinazione all'area di lavoro, questa verrà usata nella configurazione di esecuzione con un oggetto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Vista destinazioni di calcolo


Per visualizzare le destinazioni di calcolo dell'area di lavoro, usare la procedura seguente:

1. Andare nel [portale di Azure](https://portal.azure.com) e aprire l'area di lavoro. 
1. In __Applicazioni__ selezionare __Ambiente di calcolo__.

    [![Visualizza scheda calcolo](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per creare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo.

    ![Aggiungere una destinazione di calcolo](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Immettere un nome per la destinazione di calcolo. 

1. Selezionare l'**ambiente di calcolo di Machine Learning** come tipo di ambiente di calcolo da usare per il __training__. 

    >[!NOTE]
    >Ambiente di calcolo di Machine Learning è l'unica risorsa di calcolo gestito che è possibile creare nel portale di Azure.  Tutte le altre risorse di calcolo si possono collegare dopo averle create.

1. Compilare il modulo. Specificare i valori per le proprietà necessarie, in particolare la **famiglia di macchine virtuali**e il **numero massimo di nodi** da usare per creare rapidamente l'ambiente di calcolo.  

1. Selezionare __Create__.


1. Visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco:

    ![Selezionare una destinazione di calcolo per visualizzare lo stato dell'operazione di creazione](./media/how-to-set-up-training-targets/View_list.png)

1. Verranno mostrati i dettagli della destinazione di calcolo: 

    ![Visualizzare i dettagli della destinazione di calcolo](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Collegare destinazioni di calcolo

Per usare le destinazioni di calcolo create al di fuori dell'area di lavoro del servizio Azure Machine Learning, è necessario collegarle. Il collegamento di una destinazione di calcolo rende quest'ultima disponibile nell'area di lavoro.

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi i passaggi seguenti per collegare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo. 
1. Immettere un nome per la destinazione di calcolo. 
1. Selezionare il tipo di calcolo da associare per __Training__:

    > [!IMPORTANT]
    > Non tutti i tipi di calcolo possono essere creati usando il portale di Azure. I tipi di calcolo che possono essere allegati per il training sono:
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

È possibile accedere alle destinazioni di calcolo collegate all'area di lavoro usando l'[estensione dell'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) per il servizio Azure Machine Learning.  È possibile usare l'interfaccia della riga di comando per:

* Creare una destinazione di calcolo gestita
* Aggiornare una destinazione di calcolo gestita
* Collegare una destinazione di calcolo non gestita

Per altre informazioni consultare [Gestione delle risorse](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurare con VS Code

È possibile accedere, creare e gestire le destinazioni di calcolo associate all'area di lavoro usando l' [estensione vs code](how-to-vscode-tools.md#create-and-manage-compute-targets) per Azure Machine Learning servizio.

## <a id="submit"></a>Inviare l'esecuzione del training usando Azure Machine Learning SDK

Dopo aver creato una configurazione di esecuzione, questa si usa per l'esecuzione dell'esperimento.  Il criterio di codice per inviare l'esecuzione di un training è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un esperimento da eseguire
1. Inviare l'esecuzione.
1. Attendere il completamento dell'esecuzione.

> [!IMPORTANT]
> Quando si invia l'esecuzione del training, viene creato uno snapshot della directory che contiene gli script di training e viene inviata alla destinazione di calcolo. Viene inoltre archiviato come parte dell'esperimento nell'area di lavoro. Se si modificano i file e si invia di nuovo l'esecuzione, verranno caricati solo i file modificati.
>
> Per impedire che i file vengano inclusi nello snapshot, creare un file con [estensione gitignore](https://git-scm.com/docs/gitignore) o `.amlignore` nella directory e aggiungervi i file. Il `.amlignore` file usa la stessa sintassi e gli stessi criteri del file con [estensione gitignore](https://git-scm.com/docs/gitignore) . Se sono presenti entrambi i file `.amlignore` , il file avrà la precedenza.
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

In alternativa, è possibile:

* Inviare l'esperimento con un oggetto `Estimator` come illustrato in [Eseguire il training di modelli di Machine Learning con oggetti Estimator](how-to-train-ml-models.md).
* Inviare un'esecuzione iperguida per l' [ottimizzazione iperparametri](how-to-tune-hyperparameters.md).
* Inviare un esperimento tramite l' [estensione vs code](how-to-vscode-tools.md#train-and-tune-models).

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Creare una configurazione di esecuzione e inviare l'esecuzione usando Azure Machine Learning interfaccia della riga di comando

È possibile usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [Machine Learning estensione CLI](reference-azure-machine-learning-cli.md) per creare configurazioni di esecuzione e inviare esecuzioni su destinazioni di calcolo diverse. Gli esempi seguenti presuppongono che si disponga di un area di lavoro di Azure Machine Learning esistente ed è stato effettuato l' `az login` accesso ad Azure tramite il comando CLI. 

### <a name="create-run-configuration"></a>Crea configurazione di esecuzione

Il modo più semplice per creare la configurazione di esecuzione è esplorare la cartella che contiene gli script Python di machine learning e usare il comando dell'interfaccia della riga di comando

```azurecli
az ml folder attach
```

Questo comando crea una sottocartella `.azureml` che contiene i file di configurazione dell'esecuzione del modello per diverse destinazioni di calcolo. È possibile copiare e modificare questi file per personalizzare la configurazione, ad esempio per aggiungere pacchetti Python o modificare le impostazioni di Docker.  

### <a name="structure-of-run-configuration-file"></a>Struttura del file di configurazione di esecuzione

Il file di configurazione di esecuzione è YAML formattato, con le sezioni seguenti
 * Script da eseguire e relativi argomenti
 * Nome di destinazione di calcolo, ovvero "locale" o nome di un calcolo nell'area di lavoro.
 * Parametri per l'esecuzione dell'esecuzione: Framework, Communicator per le esecuzioni distribuite, durata massima e numero di nodi di calcolo.
 * Sezione Environment. Per informazioni dettagliate sui campi in questa sezione, vedere [creare e gestire gli ambienti per il training e la distribuzione](how-to-use-environments.md) .
   * Per specificare i pacchetti Python da installare per l'esecuzione, creare un [file di ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e impostare il campo __condaDependenciesFile__ .
 * Dettagli della cronologia di esecuzione per specificare la cartella del file di log e per abilitare o disabilitare gli snapshot della raccolta di output e della cronologia di esecuzione.
 * Dettagli di configurazione specifici del framework selezionato.
 * Informazioni di riferimento sui dati e archivio dati.
 * Dettagli di configurazione specifici per ambiente di calcolo di Machine Learning per la creazione di un nuovo cluster.

### <a name="create-an-experiment"></a>Creare un esperimento

Prima di tutto, creare un esperimento per le esecuzioni

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Esecuzione script

Per inviare un'esecuzione di script, eseguire un comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Esecuzione iperguida

È possibile usare l'iperguida con l'interfaccia della riga di comando di Azure per eseguire l'ottimizzazione dei parametri Per prima cosa, creare un file di configurazione di iperguida nel formato seguente. Per informazioni dettagliate sui parametri di ottimizzazione iperparametri, vedere [ottimizzare gli iperparametri per l'articolo del modello](how-to-tune-hyperparameters.md) .

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

Aggiungere questo file insieme ai file di configurazione di esecuzione. Inviare quindi un'esecuzione iperguida utilizzando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Prendere nota della sezione degli *argomenti* in runconfig e nello *spazio dei parametri* nella configurazione di iperguida. Contengono gli argomenti della riga di comando da passare allo script di training. Il valore in runconfig rimane invariato per ogni iterazione, mentre l'intervallo nella configurazione di iperguida viene iterato. Non specificare lo stesso argomento in entrambi i file.

Per ulteriori informazioni su questi ```az ml``` comandi dell'interfaccia della riga di comando e sul set completo di argomenti, vedere [la documentazione di riferimento](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Ad esempio, l'ID commit corrente per il repository viene registrato come parte della cronologia.

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Informazioni su come [ottimizzare in modo efficiente](how-to-tune-hyperparameters.md) gli iperparametri per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Consultare le informazioni sull'SDK di [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usare il servizio Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)
