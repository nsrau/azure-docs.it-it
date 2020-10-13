---
title: Configurare le destinazioni di calcolo di training & inferenza
titleSuffix: Azure Machine Learning
description: Aggiungere risorse di calcolo (destinazioni di calcolo) all'area di lavoro da usare per il training e l'inferenza di Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc4256ae0591e9fc82dcdce7c66514710fad3f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711376"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Configurare le destinazioni di calcolo per il training e la distribuzione dei modelli

Informazioni su come aggiungere risorse di calcolo di Azure all'area di lavoro Azure Machine Learning.  È quindi possibile usare queste risorse come [destinazioni di calcolo](concept-compute-target.md) per il training e l'inferenza nelle attività di machine learning.

Questo articolo illustra come configurare l'area di lavoro per l'uso di queste risorse di calcolo:

* Il computer locale
* Macchine virtuali remote
* HDInsight di Azure
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics.
* Istanza di contenitore di Azure

Per usare le destinazioni di calcolo gestite da Azure Machine Learning, vedere:


* [Istanza di calcolo di Azure Machine Learning](how-to-create-manage-compute-instance.md)
* [Cluster di calcolo di Azure Machine Learning](how-to-create-attach-compute-cluster.md)
* [Cluster del servizio Azure Kubernetes](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Limitazioni

* **Non creare più allegati simultanei nello stesso calcolo** dall'area di lavoro. Ad esempio, se si connette un cluster del servizio Kubernetes di Azure a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

    Se si vuole ricollegare una destinazione di calcolo, ad esempio per modificare TLS o altre impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente.

## <a name="whats-a-compute-target"></a>Che cos'è una destinazione di calcolo?

Con Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, collettivamente definiti [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.  Si usano anche le destinazioni di calcolo per la distribuzione del modello, come descritto in ["dove e come distribuire i modelli"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Computer locale

Quando si usa il computer locale per il **Training**, non è necessario creare una destinazione di calcolo.  È sufficiente [inviare l'esecuzione del training](how-to-set-up-training-targets.md) dal computer locale.

Quando si usa il computer locale per l' **inferenza**, è necessario che Docker sia installato. Per eseguire la distribuzione, utilizzare [LocalWebservice.deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) per definire la porta che il servizio Web utilizzerà. Usare quindi il normale processo di distribuzione come descritto in [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

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

    > [!WARNING]
    > Non creare più allegati simultanei nello stesso DSVM dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

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

    > [!WARNING]
    > Non creare più allegati simultanei nello stesso HDInsight dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

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

> [!WARNING]
> Non creare più allegati simultanei per lo stesso Azure Batch dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

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

> [!WARNING]
> Non creare più allegati simultanei per lo stesso Azure Databricks dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

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

> [!WARNING]
> Non creare più allegati simultanei nello stesso Anna dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

> [!TIP]
> Le pipeline di Azure Machine Learning possono funzionare solo con i dati archiviati nell'archivio dati predefinito dell'account Data Lake Analytics. Se i dati che è necessario utilizzare si trova in un archivio non predefinito, è possibile utilizzare un oggetto [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) per copiare i dati prima del training.

## <a name="azure-container-instance"></a><a id="aci"></a>Istanza di contenitore di Azure

Le istanze di contenitore di Azure (ACI) vengono create dinamicamente quando si distribuisce un modello. Non è possibile creare o alporre l'associazione ACI all'area di lavoro in qualsiasi altro modo. Per altre informazioni, vedere [distribuire un modello in istanze di contenitore di Azure](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Azure Kubernetes Service (AKS) consente un'ampia gamma di opzioni di configurazione quando vengono usate con Azure Machine Learning. Per altre informazioni, vedere [come creare e alleghi il servizio Azure Kubernetes](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare la risorsa di calcolo per [configurare e inviare un'esecuzione di training](how-to-set-up-training-targets.md).
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* [Usare Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)
