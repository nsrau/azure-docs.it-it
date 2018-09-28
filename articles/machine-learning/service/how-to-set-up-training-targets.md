---
title: Configurare le destinazioni di calcolo per il training del modello con il servizio Azure Machine Learning | Microsoft Docs
description: Informazioni su come selezionare e configurare gli ambienti di training (destinazioni di calcolo) usati per il training dei modelli di Machine Learning. Il servizio Azure Machine Learning consente di passare facilmente da un ambiente di training all'altro. È possibile avviare il training in locale e, se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 4af2e570b498e496e80b6aeee2b8aeae23c582cc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952410"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Selezionare e usare una destinazione di calcolo per il training del modello

Con il servizio Azure Machine Learning, è possibile eseguire il training del modello in diversi ambienti. Questi ambienti, denominati __destinazioni di calcolo__, possono essere locali o nel cloud. In questo documento saranno illustrate le destinazioni di calcolo supportate e verrà descritto come usarle.

Una destinazione di calcolo è la risorsa che esegue lo script di training o ospita il modello quando viene distribuito come servizio Web. Le destinazioni di calcolo possono essere create e gestite usando Azure Machine Learning SDK o l'interfaccia della riga di comando. Se si dispone di destinazioni di calcolo che sono state create da un altro processo (ad esempio, il portale di Azure o l'interfaccia della riga di comando di Azure), è possibile usarle associandole all'area di lavoro del servizio Azure Machine Learning.

È possibile iniziare con esecuzioni locali nel computer e quindi aumentare le prestazioni e il numero di istanze in altri ambienti, come Data Science Virtual Machine in remoto con GPU o Azure Batch per intelligenza artificiale. 

## <a name="supported-compute-targets"></a>Destinazioni di calcolo supportate

Azure Machine Learning supporta le seguenti destinazioni di calcolo:

|Destinazione del calcolo| Accelerazione GPU | Ottimizzazione degli iperparametri automatizzata | Selezione del modello automatizzata | Può essere usato nelle pipeline|
|----|:----:|:----:|:----:|:----:|
|[Computer locale](#local)| È possibile | &nbsp; | ✓ | &nbsp; |
|[Data Science Virtual Machine (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch per intelligenza artificiale](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

È anche possibile usare __[Istanze di contenitore di Azure](#aci)__ per il training dei modelli. Si tratta di un'offerta cloud serverless conveniente e facile da creare e usare. Istanze di contenitore di Azure non supporta l'accelerazione GPU, l'ottimizzazione degli iperparametri automatizzata o la selezione del modello automatizzata. Inoltre, non può essere usato in una pipeline.

Di seguito sono riportate le principali differenze tra le destinazioni di calcolo:
* __Accelerazione GPU__: le GPU sono disponibili con Data Science Virtual Machine e Azure Batch per intelligenza artificiale. È possibile accedere a una GPU nel computer locale, a seconda dell'hardware, dei driver e dei framework installati.
* __Ottimizzazione degli iperparametri automatizzata__: l'ottimizzazione degli iperparametri automatizzata di Azure Machine Learning consente di individuare gli iperparametri migliori per il modello.
* __Selezione del modello automatizzata__: Azure Machine Learning può consigliare in modo intelligente la selezione dell'algoritmo e degli iperparametri durante la creazione di un modello. La selezione del modello automatizzata consente la convergenza verso un modello di alta qualità più rapidamente rispetto ai tentativi manuali con diverse combinazioni. Per altre informazioni, vedere il documento [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning).
* __Pipeline__: Azure Machine Learning consente di combinare in una pipeline diverse attività come il training e la distribuzione. Le pipeline possono essere eseguite in parallelo o in sequenza e forniscono un affidabile meccanismo di automazione. Per altre informazioni, vedere il documento [Build machine learning pipelines with Azure Machine Learning service](concept-ml-pipelines.md) (Creare pipeline di Machine Learning con il servizio Azure Machine Learning).

È possibile usare Azure Machine Learning SDK, l'interfaccia della riga di comando di Azure o il portale di Azure per creare le destinazioni di calcolo. È anche possibile usare destinazioni di calcolo esistenti, aggiungendole (associandole) all'area di lavoro.

> [!IMPORTANT]
> Non è possibile associare un'istanza esistente di Istanza di contenitore di Azure all'area di lavoro. È necessario creare una nuova istanza.
>
> Non è possibile creare un cluster Azure HDInsight all'interno di un'area di lavoro. È necessario associare un cluster esistente.

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro per lo sviluppo e la distribuzione di un modello con Azure Machine Learning comprende i passaggi seguenti:

1. Sviluppare script di training di Machine Learning in Python.
1. Creare e configurare o associare una destinazione di calcolo esistente.
1. Inviare gli script di training alla destinazione di calcolo.
1. Esaminare i risultati per individuare il modello migliore.
1. Registrare il modello nel registro dei modelli.
1. Distribuire il modello.

> [!IMPORTANT]
> Lo script di training non è associato a una destinazione di calcolo specifica. È possibile eseguire inizialmente il training nel computer locale, quindi passare a destinazioni di calcolo diverse senza dover riscrivere lo script di training.

Il passaggio da una destinazione di calcolo all'altra richiede la creazione di una [configurazione di esecuzione](concept-azure-machine-learning-architecture.md#run-configuration). La configurazione di esecuzione definisce come eseguire lo script nella destinazione di calcolo.

## <a name="training-scripts"></a>Script di training

Quando si avvia un'esecuzione di training, viene inviata l'intera directory che contiene gli script di training. Viene creato uno snapshot, che è quindi inviato alla destinazione di calcolo. Per altre informazioni, vedere [Snapshot](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Computer locale

Durante il training in locale, è necessario usare l'SDK per inviare l'operazione di training. È possibile eseguire il training usando un ambiente gestito dall'utente o dal sistema.

### <a name="user-managed-environment"></a>Ambiente gestito dall'utente

In un ambiente gestito dall'utente, quest'ultimo deve verificare che siano disponibili tutti i pacchetti necessari nell'ambiente Python in cui sceglie di eseguire lo script. Il frammento di codice seguente è un esempio di configurazione di training per un ambiente gestito dall'utente:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```
  
### <a name="system-managed-environment"></a>Ambiente gestito dal sistema

Gli ambienti gestiti dal sistema si basano su Conda per gestire le dipendenze. Conda crea un file denominato `conda_dependencies.yml` che contiene un elenco delle dipendenze. È quindi possibile richiedere al sistema di creare un nuovo ambiente Conda ed eseguire gli script al suo interno. Gli ambienti gestiti dal sistema possono essere riutilizzati in un secondo momento, a condizione che il file `conda_dependencies.yml` resti invariato. 

La configurazione iniziale di un nuovo ambiente può richiedere diversi minuti, a seconda delle dimensioni delle dipendenze richieste. Il frammento di codice seguente illustra la creazione di un ambiente gestito dal sistema che dipende da scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```
## <a id="dsvm"></a>Data Science Virtual Machine

Il computer locale potrebbe non disporre delle risorse di calcolo o GPU necessarie per il training del modello. In questo caso, è possibile aumentare le prestazioni o il numero di istanze per il processo di training aggiungendo altre destinazioni di calcolo, ad esempio tramite Data Science Virtual Machine (DSVM).

> [!WARNING]
> Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu. Quando si crea una macchina virtuale o se ne seleziona una esistente, è necessario selezionare una macchina virtuale con Ubuntu.

La procedura seguente usa l'SDK per configurare Data Science Virtual Machine (DSVM) come destinazione di training:

1. Creare o associare una macchina virtuale
    
    * Per creare una nuova Data Science Virtual Machine, verificare innanzitutto se è disponibile una Data Science Virtual Machine con lo stesso nome. In caso contrario, creare una nuova macchina virtuale:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Per associare una macchina virtuale esistente come destinazione di calcolo, è necessario specificare il nome di dominio completo, il nome dell'account di accesso e la password per la macchina virtuale.  Nell'esempio sostituire ```<fqdn>``` con il nome di dominio completo pubblico della macchina virtuale o l'indirizzo IP pubblico. Sostituire ```<username>``` e ```<password>``` con l'utente e la password SSH per la macchina virtuale:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Per eliminare le risorse di calcolo al termine, usare il codice seguente:

    ```python
    dsvm_compute.delete()
    ```

## <a id="batch"></a>Azure Batch per intelligenza artificiale

Se il training del modello richiede molto tempo, è possibile usare Azure Batch per intelligenza artificiale per distribuire il training in un cluster di risorse di calcolo nel cloud. Batch per intelligenza artificiale può anche essere configurato per consentire una risorsa GPU.

Nell'esempio seguente viene eseguita una ricerca di un cluster Batch per intelligenza artificiale esistente in base al nome. Se non viene trovato, viene creato:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget

# choose a name for your cluster
batchai_cluster_name = ws.name + "cpu"

found = False
# see if this compute target already exists in the workspace
for ct in ws.compute_targets():
    print(ct.name, ct.type)
    if (ct.name == batchai_cluster_name and ct.type == 'BatchAI'):
        found = True
        print('found compute target. just use it.')
        compute_target = ct
        break
        
if not found:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", # for GPU, use "STANDARD_NC6"
                                                                #vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = True,
                                                                cluster_min_nodes = 1, 
                                                                cluster_max_nodes = 4)

    # create the cluster
    compute_target = ComputeTarget.create(ws,batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Per associare un cluster Batch per intelligenza artificiale esistente come destinazione di calcolo, è necessario specificare l'ID risorsa di Azure. Per ottenere l'ID risorsa nel portale di Azure, è necessario:
1. Cercare il servizio `Batch AI` in **Tutti i servizi**
1. Fare clic sul nome dell'area di lavoro a cui appartiene il cluster
1. Selezionare il cluster
1. Fare clic su **Proprietà**
1. Copiare l'**ID**

Nell'esempio seguente viene usato l'SDK per associare un cluster all'area di lavoro. Nell'esempio sostituire `<name>` con il nome delle risorsa di calcolo. Questo nome non deve corrispondere al nome del cluster. Sostituire `<resource-id>` con l'ID risorsa di Azure indicato in precedenza:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

È anche possibile controllare lo stato del cluster e dei processi di Batch per intelligenza artificiale usando i comandi seguenti dell'interfaccia della riga di comando di Azure:

- Controllare lo stato del cluster. È possibile determinare quanti nodi sono in esecuzione con `az batchai cluster list`.
- Controllare lo stato dei processi. È possibile determinare quanti processi sono in esecuzione con `az batchai job list`.

La creazione di un cluster Batch per intelligenza artificiale richiede circa 5 minuti

## <a name='aci'></a>Istanza di contenitore di Azure

Istanza di contenitore di Azure offre contenitori isolati con tempi di avvio più rapidi e che non richiedono la gestione di macchine virtuali da parte dell'utente. Il servizio Azure Machine Learning usa contenitori Linux, che sono disponibili nelle aree westus, eastus, westeurope, northeurope, westus2 e southeastasia. Per altre informazioni, vedere [Disponibilità a livello di area](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

Nell'esempio seguente viene illustrato come usare l'SDK per creare una destinazione di calcolo Istanza di contenitore di Azure e usarla per eseguire il training di un modello: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

La creazione di una destinazione di calcolo Istanza di contenitore di Azure può richiedere da qualche secondo ad alcuni minuti.

## <a id="hdinsight"></a>Associare un cluster HDInsight 

HDInsight è una piattaforma comune per l'analisi dei Big Data. Include Apache Spark, che può essere usato per il training del modello.

> [!IMPORTANT]
> È necessario creare il cluster HDInsight prima di usarlo per eseguire il training del modello. Per creare un cluster Spark in HDInsight, vedere il documento [Creare un cluster Spark in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
>
> Dopo aver creato il cluster, questo ha il nome di dominio completo (FQDN) `<clustername>.azurehdinsight.net`, dove `<clustername>` è il nome specificato per il cluster. È necessario questo indirizzo (o l'indirizzo IP pubblico del cluster) per usarlo come destinazione di calcolo

Per configurare HDInsight come destinazione di calcolo, è necessario specificare il nome di dominio completo, il nome dell'account di accesso del cluster e la password per il cluster HDInsight. Nell'esempio seguente viene usato l'SDK per associare un cluster all'area di lavoro. Nell'esempio sostituire `<fqdn>` con il nome di dominio completo pubblico del cluster o l'indirizzo IP pubblico. Sostituire `<username>` e `<password>` con l'utente e la password SSH per il cluster:

> [!NOTE]
> Per trovare il nome FQDN per il cluster, visitare il portale di Azure e selezionare il cluster HDInsight. Nella sezione __Panoramica__, il nome FQDN fa parte della voce __URL__. È sufficiente rimuovere `https://` dall'inizio del valore.
>
> ![Screenshot della panoramica del cluster HDInsight con la voce URL evidenziata](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Inviare l'esecuzione di training
    
Il codice per l'invio di un'esecuzione di training è identico indipendentemente dalla destinazione di calcolo:

* Creare un oggetto `ScriptRunConfig` usando la configurazione di esecuzione per la destinazione di calcolo.
* Inviare l'esecuzione.
* Attendere il completamento dell'esecuzione.

Nell'esempio seguente viene usata la configurazione per la destinazione di calcolo locale gestita dal sistema creata in precedenza in questo documento:

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Visualizzare e configurare le destinazioni di calcolo tramite il portale di Azure

È possibile visualizzare le destinazioni di calcolo associate all'area di lavoro dal portale di Azure. Per ottenere l'elenco, eseguire la procedura seguente:

1. Visitare il [portale di Azure](https://portal.azure.com) e passare all'area di lavoro.
2. Fare clic sul collegamento __Calcolo__ nella sezione __Applicazioni__.

    ![Visualizzare la scheda Calcolo](./media/how-to-set-up-training-targets/compute_tab.png)

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo e quindi usare la procedura seguente per creare una destinazione di calcolo:

1. Fare clic sul segno __+__ per aggiungere una destinazione di calcolo.

    ![Aggiungere una destinazione di calcolo ](./media/how-to-set-up-training-targets/add_compute.png)

1. Immettere un nome per la destinazione di calcolo.
1. Selezionare il tipo di calcolo da associare per __Training__. 
1. Selezionare __Crea nuovo__ e compilare il modulo richiesto. 
1. Selezionare __Crea__
1. È possibile visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco.

    ![Visualizzare l'elenco delle destinazioni di calcolo](./media/how-to-set-up-training-targets/View_list.png) Verranno visualizzati i dettagli per la destinazione di calcolo.
    ![Visualizzare i dettagli](./media/how-to-set-up-training-targets/vm_view.PNG)
1. È ora possibile inviare un'esecuzione su queste destinazioni come descritto in precedenza.

### <a name="reuse-existing-compute-in-your-workspace"></a>Riutilizzare destinazioni di calcolo esistenti nell'area di lavoro

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo e quindi usare la procedura seguente per riutilizzare una destinazione di calcolo:

1. Fare clic sul segno **+** per aggiungere una destinazione di calcolo.
2. Immettere un nome per la destinazione di calcolo.
3. Selezionare il tipo di calcolo da associare per Training. Batch per intelligenza artificiale e le macchine virtuali sono attualmente supportati nel portale per il training.
4. Selezionare "Usa esistente".
    - Per associare cluster Batch per intelligenza artificiale, selezionare la destinazione di calcolo nel menu a discesa, selezionare l'area di lavoro di Batch per intelligenza artificiale e il cluster di Batch per intelligenza artificiale, quindi fare clic su **Crea**.
    - Per associare una macchina virtuale, immettere l'indirizzo IP, la combinazione di nome utente e password, le chiavi pubblica/privata e la porta, quindi fare clic su Crea.

    > [!NOTE]
    > Microsoft consiglia di usare chiavi SSH, perché sono più sicure rispetto alle password. Le password sono vulnerabili agli attacchi di forza bruta, mentre le chiavi SSH si basano sulle firme di crittografia. Per informazioni sulla creazione di chiavi SSH per l'uso con Macchine virtuali di Azure, vedere i documenti seguenti:
    >
    > * [Create and use SSH keys on Linux or macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Creare e usare chiavi SSH in Linux o macOS)
    > * [Create and use SSH keys on Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Creare e usare chiavi SSH in Windows)

5. È possibile visualizzare lo stato del provisioning selezionando la destinazione di calcolo nell'elenco delle risorse di calcolo.
6. È ora possibile inviare un'esecuzione su queste destinazioni.

## <a name="examples"></a>Esempi
I notebook seguenti illustrano i concetti descritti in questo articolo:
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`
* `01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb`
* `01.getting-started/05.train-in-spark/05.train-in-spark.ipynb`
* `01.getting-started/07.hyperdrive-with-sklearn/07.hyperdrive-with-sklearn.ipynb`

Ottenere questi notebook: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento su Azure Machine Learning SDK](http://aka.ms/aml-sdk)
* [Esercitazione sul training di un modello](tutorial-train-models-with-aml.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Build machine learning pipelines with Azure Machine Learning service](concept-ml-pipelines.md) (Creare pipeline di Machine Learning con il servizio Azure Machine Learning)
