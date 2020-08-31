---
title: Creare risorse di calcolo con Python SDK
titleSuffix: Azure Machine Learning
description: Usare il Azure Machine Learning Python SDK per creare risorse di calcolo di training e distribuzione (destinazioni di calcolo) per Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 96aa6839fe51bb8a8c26f411c1a1f9df6b8c5a7f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147501"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Creare destinazioni di calcolo per il training e la distribuzione di modelli con Python SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene usato il Azure Machine Learning Python SDK per creare e gestire le destinazioni di calcolo. È anche possibile creare e gestire le destinazioni di calcolo con:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md), 
* [Estensione dell'interfaccia](reference-azure-machine-learning-cli.md#resource-management) della riga di comando per Azure Machine Learning
* [Estensione vs code](how-to-manage-resources-vscode.md#compute-clusters) per Azure Machine Learning.


## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)
* [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Che cos'è una destinazione di calcolo?

Con Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, collettivamente definiti [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.  È possibile anche creare destinazioni di calcolo per la distribuzione del modello, come descritto in ["Dove e come distribuire i modelli"](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Destinazioni di calcolo per il training

Azure Machine Learning offre un supporto variabile per le diverse destinazioni di calcolo. Un tipico ciclo di vita di sviluppo modello inizia con lo sviluppo e la sperimentazione su una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, ad esempio il computer locale o una macchina virtuale basata sul cloud. Quando il training viene eseguito su set di dati più grandi, o quando si esegue il training distribuito, è consigliabile usare l'ambiente di calcolo di Azure Machine Learning per creare un cluster a uno o più nodi che viene ridimensionato automaticamente ogni volta che viene inviata un'esecuzione. È possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato di seguito:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning cluster di calcolo possono essere creati come risorsa persistente o creati dinamicamente quando si richiede un'esecuzione. Al termine del training, la creazione basata sull'esecuzione rimuove la destinazione di calcolo. Non è quindi possibile riutilizzare le destinazioni di calcolo create in questo modo.

Usare le sezioni seguenti per configurare queste destinazioni di calcolo:

* [Computer locale](#local)
* [Cluster di calcolo di Azure Machine Learning](#amlcompute)
* [Istanza di calcolo di Azure Machine Learning](#instance)
* [Macchine virtuali remote](#vm)
* [Azure HDInsight](#hdinsight)


## <a name="local-computer"></a><a id="local"></a>Computer locale

Quando si usa il computer locale per il training, non è necessario creare una destinazione di calcolo.  È sufficiente [inviare l'esecuzione del training](how-to-set-up-training-targets.md) dal computer locale.

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning cluster di calcolo

Azure Machine Learning cluster di calcolo è un'infrastruttura di calcolo gestito che consente di creare facilmente un calcolo a nodo singolo o a più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

È possibile usare un ambiente di calcolo di Azure Machine Learning per distribuire il processo di training in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](how-to-manage-quotas.md).

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

    In alternativa, è possibile creare e collegare una risorsa permanente dell'ambiente di calcolo di Azure Machine Learning in [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#portal-create).

Ora che è stato collegato il calcolo, il passaggio successivo consiste nell' [inviare l'esecuzione del training](how-to-set-up-training-targets.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Abbassare il costo del cluster di calcolo

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

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurare l'identità gestita

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Configurare l'identità gestita nella configurazione del provisioning:  
    
* Identità gestita assegnata dal sistema:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Identità gestita assegnata dall'utente: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Aggiungere identità gestita a un cluster di calcolo esistente:  
    
* Identità gestita assegnata dal sistema:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Identità gestita assegnata dall'utente:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Utilizzo identità gestite

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Istanza di calcolo di Azure Machine Learning

[Azure Machine Learning istanza di calcolo](concept-compute-instance.md) è un'infrastruttura di calcolo gestito che consente di creare facilmente una singola macchina virtuale. Il calcolo viene creato all'interno dell'area dell'area di lavoro, ma a differenza di un cluster di calcolo, un'istanza non può essere condivisa con altri utenti nell'area di lavoro. Inoltre, l'istanza non viene ridimensionata automaticamente.  Per evitare addebiti continui, è necessario arrestare la risorsa.

Un'istanza di calcolo può eseguire più processi in parallelo e dispone di una coda di processi. 

Le istanze di calcolo possono eseguire processi in modo sicuro in un [ambiente di rete virtuale](how-to-enable-virtual-network.md#compute-instance), senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker. 

1. **Creare e collegare**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Ora che è stato collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste nell' [inviare l'esecuzione del training](how-to-set-up-training-targets.md)


## <a name="remote-virtual-machines"></a><a id="vm"></a>Macchine virtuali remote

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

   In alternativa, è possibile collegare la Data Science VM all'area di lavoro [usando Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

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

   In alternativa, è possibile collegare il cluster HDInsight all'area di lavoro [usando Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Dopo aver collegato l'ambiente di calcolo e aver configurato l'esecuzione, il passaggio successivo consiste nell'[invio dell'esecuzione di training](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

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

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

Creare un'area di lavoro di Azure Databricks prima di usarlo. Per creare una risorsa dell'area di lavoro, vedere il documento [eseguire un processo Spark in Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Per collegare Azure Databricks come destinazione di calcolo, fornire le informazioni seguenti:

* __Databricks nome di calcolo__: il nome che si vuole assegnare a questa risorsa di calcolo.
* __Nome area di lavoro databricks__: nome dell'area di lavoro Azure Databricks.
* __Token di accesso di databricks__: il token di accesso usato per l'autenticazione Azure Databricks. Per generare un token di accesso vedere il documento [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) (Autenticazione).

Il codice seguente illustra come associare Azure Databricks come destinazione di calcolo con Azure Machine Learning SDK (__l'area di lavoro databricks deve essere presente nella stessa sottoscrizione dell'area di lavoro di AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-databricks) in GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics.

Azure Data Lake Analytics è una piattaforma di analisi dei Big Data nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

Creare un account di Azure Data Lake Analytics prima di usarla. Per creare questa risorsa vedere il documento [Introduzione ad Azure Data Lake Analytics con il portale di Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Per connettere Data Lake Analytics come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

* __Nome del calcolo__: il nome da assegnare a questa risorsa di calcolo.
* __Gruppo di risorse__: il gruppo di risorse contenente l'account data Lake Analytics.
* __Nome account__: il nome dell'account data Lake Analytics.

Il codice seguente illustra in che modo connettere Data Lake Analytics come destinazione di calcolo:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-adla) in GitHub.

> [!TIP]
> Le pipeline di Azure Machine Learning possono funzionare solo con i dati archiviati nell'archivio dati predefinito dell'account Data Lake Analytics. Se i dati che è necessario utilizzare si trova in un archivio non predefinito, è possibile utilizzare un oggetto [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) per copiare i dati prima del training.

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare la risorsa di calcolo per [inviare un'esecuzione di training](how-to-set-up-training-targets.md).
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* [Usare Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)
