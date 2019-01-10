---
title: Destinazioni di calcolo per il training del modello
titleSuffix: Azure Machine Learning service
description: Configurare gli ambienti di training (destinazioni di calcolo) per il training del modello di Machine Learning. Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794465"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurare le destinazioni di calcolo per il training del modello

Con il servizio Azure Machine Learning è possibile eseguire il training del modello su diverse risorse di calcolo. Queste risorse di calcolo, denominate __destinazioni di calcolo__, possono essere locali o nel cloud. Questo documento illustra le destinazioni di calcolo supportate e come usarle.

Una destinazione di calcolo è una risorsa in cui si esegue lo script di training o si ospita il modello quando viene distribuito come servizio Web. È possibile creare e gestire una destinazione di calcolo usando Azure Machine Learning SDK, il portale di Azure o l'interfaccia della riga di comando di Azure. Se si dispone di destinazioni di calcolo create tramite un altro servizio (ad esempio, un cluster Azure HDInsight) è possibile usarle associandole all'area di lavoro del servizio Azure Machine Learning.

Azure Machine Learning supporta tre ampie categorie di destinazioni di calcolo:

* __Locale__: il computer locale o la macchina virtuale basata sul cloud che è usata come ambiente di sviluppo/sperimentazione. 
* __Ambiente di calcolo gestito__: l'ambiente di calcolo di Azure Machine Learning è un'offerta per ambiente di calcolo gestita dal servizio Azure Machine Learning. Consente di creare facilmente ambienti di calcolo a uno o più nodi per le attività di training, test e inferenza dei batch.
* __Ambiente di calcolo associato__: è anche possibile usare i propri ambienti di calcolo nel cloud di Azure e collegarli ad Azure Machine Learning. Altre informazioni sui tipi di calcolo supportati e su come usarli nelle sezioni seguenti.


## <a name="supported-compute-targets"></a>Destinazioni di calcolo supportate

Il servizio Azure Machine Learning offre un supporto variabile per le varie destinazioni di calcolo. Un tipico ciclo di vita di sviluppo del modello inizia con lo sviluppo e la sperimentazione su una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, come il computer locale o una macchina virtuale basata su cloud. Quando si aumenta il training su un set di dati più ampio, o si esegue il training distribuito, usare un ambiente di calcolo di Azure Machine Learning per creare un cluster singolo o a più nodi che esegue il ridimensionamento automatico ogni volta che si invia un'esecuzione. Inoltre, è possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato nelle tabelle seguenti:

|Destinazione del calcolo| Accelerazione GPU | Automatizzata<br/> Ottimizzazione degli iperparametri | Automatizzata</br> Machine Learning | Pipeline descrittivo|
|----|:----:|:----:|:----:|:----:|
|[Computer locale](#local)| È possibile | &nbsp; | ✓ | &nbsp; |
|[Ambiente di calcolo di Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Macchina virtuale remota](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics.](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks e Azure Data Lake Analytics possono essere usati solo in una pipeline._<br/>
> Per altre informazioni sulle pipeline, consultare [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).
>
> L'ambiente di calcolo di Machine Learning deve essere creato da un'area di lavoro. Non è possibile collegare le istanze esistenti a un'area di lavoro.
>
> Altre destinazioni di calcolo devono essere create all'esterno di Azure Machine Learning e quindi collegate all'area di lavoro.
>
> Quando si esegue il training di un modello, alcune destinazioni di calcolo si basano sulle immagini del contenitore Docker. L'immagine di base di GPU deve essere usata solo nei servizi di Microsoft Azure. Per il training del modello, i servizi includono:
> * Ambiente di calcolo di Azure Machine Learning
> * Azure Kubernetes Service
> * Windows Data Science Virtual Machine (DSVM)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro per lo sviluppo e la distribuzione di un modello con Azure Machine Learning comprende i passaggi seguenti:

1. Sviluppare gli script per il training di apprendimento automatico in Python.
1. Creare e configurare una destinazione di calcolo o associarne una esistente.
1. Inviare gli script di training alla destinazione di calcolo.
1. Esaminare i risultati per individuare il modello migliore.
1. Registrare il modello nel registro dei modelli.
1. Distribuire il modello.

> [!NOTE]
> Lo script di training non è associato a una destinazione di calcolo specifica. È possibile eseguire inizialmente il training nel computer locale, quindi passare a destinazioni di calcolo diverse senza dover riscrivere lo script di training.
> 
> Quando si associa una destinazione di calcolo all'area di lavoro creando ambienti di calcolo gestiti o collegando un calcolo esistente, è necessario specificare un nome per il calcolo. Il nome deve avere una lunghezza compresa tra i 16 e i 63 caratteri.

Per passare da una destinazione di calcolo all'altra è necessaria una [configurazione di esecuzione](concept-azure-machine-learning-architecture.md#run-configuration). La configurazione di esecuzione definisce come eseguire lo script nella destinazione di calcolo.

## <a name="training-scripts"></a>Script di training

Quando si avvia l'esecuzione di un training, si crea una snapshot della directory contenente gli script di training, che viene inviata alla destinazione di calcolo. Per altre informazioni, vedere [Snapshot](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Computer locale

Durante il training in locale, è necessario usare l'SDK per inviare l'operazione di training. È possibile eseguire il training usando un ambiente gestito dall'utente o dal sistema.

### <a name="user-managed-environment"></a>Ambiente gestito dall'utente

In un ambiente gestito dall'utente, assicurarsi che tutti i pacchetti necessari siano disponibili nell'ambiente di Python in cui si esegue lo script. Il frammento di codice seguente è un esempio di come configurare il training per un ambiente gestito dall'utente:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Ambiente gestito dal sistema

Gli ambienti gestiti dal sistema si basano su Conda per gestire le dipendenze. Conda crea un file denominato **conda_dependencies.yml** che contiene un elenco delle dipendenze. È possibile richiedere al sistema di creare un nuovo ambiente Conda ed eseguire gli script al suo interno. Gli ambienti gestiti dal sistema possono essere usati nuovamente in un secondo momento, a condizione che il file conda_dependencies.yml resti invariato. 

La configurazione iniziale di un nuovo ambiente può richiedere diversi minuti, a seconda delle dimensioni delle dipendenze richieste. Il frammento di codice seguente illustra la creazione di un ambiente gestito dal sistema che dipende da scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Ambiente di calcolo di Machine Learning

L'ambiente di calcolo di Azure Machine Learning è un'infrastruttura di calcolo gestito che consente all'utente di creare facilmente un ambiente di calcolo a uno o più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un contenitore Docker.

È possibile usare un ambiente di calcolo di Azure Machine Learning per distribuire il processo di training in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

È possibile creare l'ambiente di calcolo di Azure Machine Learning su richiesta quando si pianifica un'esecuzione, oppure come risorsa permanente.

### <a name="run-based-creation"></a>Creazione basata su esecuzione

È possibile creare l'ambiente di calcolo di Azure Machine Learning come destinazione di calcolo in fase di esecuzione. Il calcolo per l'esecuzione viene creato automaticamente e aumenta il numero di **max_nodes** specificato nella configurazione di esecuzione. Il calcolo viene eliminato automaticamente dopo il completamento dell'esecuzione.

> [!IMPORTANT]
> La creazione basata su esecuzione di un ambiente di calcolo di Azure Machine Learning è attualmente disponibile in stato di anteprima. Non usare la creazione basata su esecuzione se si usa l'ottimizzazione degli iperparametri o le funzionalità automatizzate di Machine Learning. Per usare l'ottimizzazione degli iperparametri o l'apprendimento automatico, creare l'ambiente di calcolo di Azure Machine Learning prima di inviare un'esecuzione.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Ambiente di calcolo permanente: Basic

Un ambiente di calcolo di Azure Machine Learning permanente può essere usato nuovamente su più processi. Il calcolo può anche essere condiviso con altri utenti nell'area di lavoro e viene mantenuto tra i processi.

Per creare un ambiente di calcolo di Azure Machine Learning permanente, è necessario specificare le proprietà **vm_size** e **max_nodes**. Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà. L'ambiente di calcolo si ridimensiona automaticamente a zero nodi quando non è in uso e crea macchine virtuali dedicate per l'esecuzione dei processi secondo necessità. 

* **vm_size**: la famiglia di macchine virtuali dei nodi creati dall'ambiente di calcolo di Azure Machine Learning.
* **max_nodes**: il numero massimo di nodi per la scalabilità automatica durante l'esecuzione di un processo in un ambiente di calcolo di Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Ambiente di calcolo permanente: Avanzate

Durante la creazione di un ambiente di calcolo di Azure Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente con dimensione fissa o all'interno di una rete virtuale di Azure esistente nella sottoscrizione.

Insieme a **vm_size** e **max_nodes** è anche possibile usare le proprietà seguenti:

* **min_nodes**: il numero minimo di nodi per il ridimensionamento durante l'esecuzione di un processo in un ambiente di calcolo di Azure Machine Learning. Il valore minimo predefinito è di zero (0) nodi.
* **vm_priority**: il tipo di macchina virtuale da usare quando si crea una risorsa di ambiente di calcolo di Azure Machine Learning. Scegliere tra **dedicato** (impostazione predefinita) e **a bassa priorità**. Le macchine virtuali con priorità bassa usano la capacità in eccesso in Azure. Queste macchine virtuali sono più economiche, ma quando vengono usate le esecuzioni possono essere annullate.
* **idle_seconds_before_scaledown**: la quantità di tempo di inattività da attendere affinché un'esecuzione venga completata e prima che si ridimensioni automaticamente fino al numero di **min_nodes**. Il tempo di inattività predefinito è di 120 secondi.
* **vnet_resourcegroup_name**: gruppo di risorse della rete virtuale __esistente__. L'ambiente di calcolo di Azure Machine Learning è creato all'interno di questa rete virtuale.
* **vnet_name**: nome della rete virtuale. La rete virtuale deve trovarsi nella stessa area dell'area di lavoro di Azure Machine Learning.
* **subnet_name**: nome della subnet all'interno della rete virtuale. Alle risorse dell'ambiente di calcolo di Machine Learning di Azure sono assegnati gli indirizzi IP di questo intervallo di subnet.

> [!TIP]
> Quando si crea una risorsa permanente dell'ambiente di calcolo di Azure Machine Learning, è possibile aggiornare le proprietà come il numero di **min_nodes** oppure **max_nodes**. Per aggiornare una proprietà, chiamare la funzione `update()` per la proprietà.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Macchina virtuale remota

Azure Machine Learning consente di usare la risorsa di calcolo personale e di associarla all'area di lavoro. Una risorsa di questo tipo è una macchina virtuale remota arbitraria, purché sia accessibile dal servizio Azure Machine Learning. La risorsa può essere una macchina virtuale di Azure, un server remoto nell'organizzazione o in locale. In particolare, se si dispone dell'indirizzo IP e delle credenziali (nome utente e password o chiave SSH), è possibile usare qualsiasi macchina virtuale accessibile per le esecuzioni remote.
È possibile usare un ambiente conda integrato nel sistema, un ambiente Python esistente o un contenitore Docker. Quando avviene un esecuzione usando un contenitore Docker, è necessario che il motore Docker sia in esecuzione nella macchina virtuale. Questa funzionalità remota della macchina virtuale è particolarmente utile quando si preferisce un ambiente di sviluppo o sperimentazione basato sul cloud più flessibile rispetto al computer locale.

> [!TIP]
> Usare la Data Science Virtual Machine come macchina virtuale di Azure preferita per questo scenario. Si tratta di un ambiente preconfigurato di data science e sviluppo per intelligenza artificiale, dotato di una gamma dettagliata di strumenti e framework per l'intero ciclo di vita di sviluppo dell'apprendimento automatico. Per altre informazioni su come usare una Data Science Virtual Machine, consultare [Configurare un ambiente di sviluppo per Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu. Quando si crea una macchina virtuale o se ne sceglie una esistente, è necessario selezionare una macchina virtuale che usa Ubuntu.

La procedura seguente usa l'SDK per configurare una Data Science Virtual Machine come destinazione di training:

1. Per associare una macchina virtuale esistente come destinazione di calcolo, è necessario specificare il nome di dominio completo (FQDN), il nome utente e la password per la macchina virtuale. Nell'esempio sostituire \<fqdn> con il nome di dominio completo pubblico della macchina virtuale o l'indirizzo IP pubblico. Nel comando seguente sostituire \<username> e \<password> con il nome utente e la password SSH per la macchina virtuale.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Creare una configurazione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. L'ambiente può essere usato come una destinazione di calcolo durante il training di modelli con una pipeline di Azure Machine Learning.

> [!IMPORTANT]
> Una destinazione di calcolo di Azure Databricks può essere usata solo in una pipeline di Machine Learning.
>
> È necessario creare uno spazio di lavoro Azure Databricks prima di usarla per eseguire il training del modello. Per creare queste risorse consultare [Eseguire un processo Spark in Azure Databricks mediante il portale di Azure](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Per connettere Azure Databricks come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

* __Nome dell'ambiente di calcolo__: Il nome da assegnare a questa risorsa di calcolo.
* __Nome dell'area di lavoro di Azure Databricks__: il nome dell'area di lavoro di Azure Databricks.
* __Token di accesso__: il token di accesso usato per eseguire l'autenticazione in Azure Databricks. Per generare un token di accesso consultare [Autenticazione](https://docs.azuredatabricks.net/api/latest/authentication.html).

Il codice seguente illustra come connettere Azure Databricks come destinazione di calcolo:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics è una piattaforma di analisi dei Big Data nel cloud di Azure. La piattaforma può essere usata come una destinazione di calcolo durante il training di modelli con una pipeline di Azure Machine Learning.

> [!IMPORTANT]
> Una destinazione di calcolo di Azure Data Lake Analytics può essere usata solo in una pipeline di Machine Learning.
>
> È necessario creare un account di Azure Data Lake Analytics prima di usarla per eseguire il training del modello. Per creare questa risorsa, consultare [Introduzione ad Azure Data Lake Analytics con il portale di Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Per connettere Data Lake Analytics come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

* __Nome dell'ambiente di calcolo__: Il nome da assegnare a questa risorsa di calcolo.
* __Gruppo di risorse__: il gruppo di risorse che contiene l'account Data Lake Analytics.
* __Nome account__: Nome dell'account Data Lake Analytics.

Il codice seguente illustra in che modo connettere Data Lake Analytics come destinazione di calcolo:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Le pipeline di Azure Machine Learning funzionano solo con i dati archiviati nell'archivio dati predefinito dell'account Data Lake Analytics. Se i dati necessari sono in un archivio non predefinito, è possibile usare un'operazione di [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) per copiare i dati prima di eseguire il training del modello.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight è una piattaforma comune per l'analisi dei Big Data. La piattaforma include Apache Spark, che può essere usato per il training del modello.

> [!IMPORTANT]
> È necessario creare il cluster HDInsight prima di usarlo per eseguire il training del modello. Per creare un cluster Spark in HDInsight, consultare [Creare un cluster Spark in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
>
> Dopo aver creato il cluster, questo ha il nome di dominio completo \<clustername>.azurehdinsight.net, dove \<clustername> è il nome fornito per il cluster. È necessario l'indirizzo del nome di dominio completo (o l'indirizzo IP pubblico del cluster) per usare il cluster come destinazione di calcolo.

Per configurare HDInsight come destinazione di calcolo, è necessario specificare il nome di dominio completo, il nome utente e la password per il cluster HDInsight. Nell'esempio seguente viene usato l'SDK per associare un cluster all'area di lavoro. Nell'esempio sostituire \<fqdn> con il nome di dominio completo pubblico del cluster o l'indirizzo IP pubblico. Sostituire \<username> e \<password> con un nuovo nome utente e una nuova password SSH il cluster.

> [!NOTE]
> Per trovare il nome di dominio completo per il cluster, andare sul portale di Azure e selezionare il cluster HDInsight. In __Panoramica__ è possibile visualizzare il nome di dominio completo nella voce __URL__. Per ottenere il nome di dominio completo, rimuovere il prefisso https:\// dall'inizio della voce.

![Nel portale di Azure ottenere il nome di dominio completo per un cluster HDInsight](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Inviare l'esecuzione di training

Esistono due modi per inviare una sessione di training:

* Inviare un esecuzione di training usando un oggetto `ScriptRunConfig`.
* Inviare un esecuzione di training usando un oggetto `Pipeline`.

> [!IMPORTANT]
> Le destinazioni di calcolo di Azure Databricks e Azure Datalake Analytics sono utilizzabili solo in una pipeline.
>
> La destinazione di calcolo locale non può essere usata in una pipeline.

### <a name="scriptrunconfig-object"></a>Oggetto ScriptRunConfig

Il criterio di codice per inviare l'esecuzione di un training con l'oggetto `ScriptRunConfig` è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un oggetto `ScriptRunConfig` usando la configurazione di esecuzione per la destinazione di calcolo.
1. Inviare l'esecuzione.
1. Attendere il completamento dell'esecuzione.

L'esempio seguente illustra la configurazione per la destinazione di calcolo locale gestita dal sistema creata in precedenza:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Oggetto della pipeline

Il criterio di codice per inviare l'esecuzione di un training con un oggetto `Pipeline` è uguale per tutti i tipi di destinazioni di calcolo:

1. Aggiungere un passaggio all'oggetto `Pipeline` per la risorsa di calcolo.
1. Inviare un'esecuzione tramite la pipeline.
1. Attendere il completamento dell'esecuzione.

L'esempio seguente mostra come usare la destinazione di calcolo di Azure Databricks creata in precedenza:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Per altre informazioni sulle pipeline di apprendimento automatico consultare [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

Ad esempio i Jupyter Notebook che illustrano come eseguire il training di un modello usando una pipeline, vedere [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Accedere agli ambienti di calcolo nel portale di Azure

È possibile accedere alle destinazioni di calcolo associate all'area di lavoro nel portale di Azure. 

### <a name="view-compute-targets"></a>Vista destinazioni di calcolo

Per visualizzare le destinazioni di calcolo dell'area di lavoro, usare la procedura seguente:

1. Andare nel [portale di Azure](https://portal.azure.com) e aprire l'area di lavoro.
1. In __Applicazioni__ selezionare __Ambiente di calcolo__.

    ![Vista destinazioni di calcolo](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per creare una destinazione di calcolo:

1. Selezionare il segno (+) per aggiungere una destinazione di calcolo.

    ![Aggiungere una destinazione di calcolo](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Immettere un nome per la destinazione di calcolo.
1. Selezionare l'**ambiente di calcolo di Machine Learning** come tipo di ambiente di calcolo da usare per il __training__.

    > [!IMPORTANT]
    > È possibile creare un ambiente di calcolo di Azure Machine Learning solo come ambiente di calcolo gestito per il training.

1. Compilare il modulo. Specificare i valori per le proprietà necessarie, in particolare la **famiglia di macchine virtuali**e il **numero massimo di nodi** da usare per creare rapidamente l'ambiente di calcolo. 
1. Selezionare __Create__.
1. Visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco:

    ![Selezionare una destinazione di calcolo per visualizzare lo stato dell'operazione di creazione](./media/how-to-set-up-training-targets/View_list.png)

1. Verranno mostrati i dettagli della destinazione di calcolo:

    ![Visualizzare i dettagli della destinazione di calcolo](./media/how-to-set-up-training-targets/compute-target-details.png)

È ora possibile inviare un'esecuzione in base alle destinazioni di calcolo come descritto in precedenza.


### <a name="reuse-existing-compute-targets"></a>Usare nuovamente le destinazioni di calcolo esistenti

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per usare nuovamente una destinazione di calcolo:

1. Selezionare il segno (+) per aggiungere una destinazione di calcolo.
1. Immettere un nome per la destinazione di calcolo.
1. Selezionare il tipo di calcolo da associare per __Training__:

    > [!IMPORTANT]
    > Non tutti i tipi di calcolo possono essere creati usando il portale di Azure.
    > I tipi di calcolo che possono essere allegati per il training sono:
    >
    > * Macchina virtuale remota
    > * Azure Databricks
    > * Azure Data Lake Analytics.
    > * HDInsight di Azure

1. Compilare il modulo e fornire i valori per le proprietà necessarie.

    > [!NOTE]
    > Microsoft consiglia di usare le chiavi SSH, che sono più sicure rispetto alle password. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. Le chiavi SSH si basano sulle firme di crittografia. Per informazioni sulla creazione di chiavi SSH per l'uso con Macchine virtuali di Azure, vedere i documenti seguenti:
    >
    > * [Create and use SSH keys on Linux or macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Creare e usare chiavi SSH in Linux o macOS)
    > * [Create and use SSH keys on Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Creare e usare chiavi SSH in Windows)

1. Selezionare __Allega__.
1. Visualizzare lo stato dell'operazione di collegamento selezionando la destinazione di calcolo dall'elenco.

È ora possibile inviare un'esecuzione in base alle destinazioni di calcolo come descritto in precedenza.

## <a name="notebook-examples"></a>Esempi di notebook

Ad esempio, consultare i notebook nelle posizioni seguenti:

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento su Azure Machine Learning SDK](https://aka.ms/aml-sdk)
* [Esercitazione: eseguire il training di un modello](tutorial-train-models-with-aml.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Build machine learning pipelines with Azure Machine Learning service](concept-ml-pipelines.md) (Creare pipeline di Machine Learning con il servizio Azure Machine Learning)
