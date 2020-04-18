---
title: Esperimenti sicuri e inferenza nella rete virtualeSecure experiments and inference in virtual network
titleSuffix: Azure Machine Learning
description: informazioni su come proteggere i processi di sperimentazione/formazione e i processi di inferenza/assegnazione di punteggi o punteggio in Azure Machine Learning all'interno di una rete virtuale di Azure.Learn how to secure experimentation/training jobs and inference/scoring jobs in Azure Machine Learning within an Azure Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/17/2020
ms.openlocfilehash: 1f3c9f86072eeedbc999946d0f846fbc3b41f94d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641746"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Proteggere i processi di sperimentazione e inferenza di Azure ML all'interno di una rete virtuale di AzureSecure Azure ML experimentation and inference jobs within an Azure Virtual Network
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo verrà illustrato come proteggere i processi di sperimentazione/formazione e i processi di inferenza/assegnazione in Azure Machine Learning all'interno di una rete virtuale di Azure (vnet).

Una **rete virtuale** funge da limite di sicurezza, isolando le risorse di Azure da Internet pubblico. È anche possibile aggiungere una rete virtuale di Azure alla rete locale. Unendo le reti, è possibile eseguire il training sicuro dei modelli e accedere ai modelli distribuiti per l'inferenza.

Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo, o destinazioni di [calcolo,](concept-compute-target.md)vengono utilizzate per eseguire il training e la distribuzione di modelli. Le destinazioni possono essere create all'interno di una rete virtuale. Ad esempio, è possibile usare Microsoft Data Science Virtual Machine per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes (AKS). Per altre informazioni sulle reti virtuali, vedere Panoramica della rete virtuale di Azure.For more information about virtual networks, see [Azure Virtual Network overview.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

In questo articolo vengono inoltre fornite informazioni dettagliate sulle impostazioni di *protezione avanzate,* informazioni non necessarie per i casi d'uso di base o sperimentali. Alcune sezioni di questo articolo forniscono informazioni di configurazione per una varietà di scenari. Non è necessario completare le istruzioni in ordine o nella loro interezza.

> [!TIP]
> A meno che non venga chiamato in modo specifico, l'uso di risorse come account di archiviazione o destinazioni di calcolo all'interno di una rete virtuale funzionerà sia con le pipeline di Machine Learning che con i flussi di lavoro non della pipeline, ad esempio l'esecuzione di script.

> [!WARNING]
> Microsoft non supporta l'utilizzo delle funzionalità di Azure Machine Learning Studio, ad esempio MachineL automatizzato, Set di dati, Etichettatura dati, Finestra di progettazione e Blocchi appunti se nell'archiviazione sottostante è abilitata la rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

+ Area di [lavoro](how-to-manage-workspace.md)di Azure Machine Learning .

+ Conoscenza generale del [servizio Rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) di Azure e della [rete IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Una rete virtuale e una subnet preesistenti da usare con le risorse di elaborazione.

## <a name="use-a-storage-account-for-your-workspace"></a>Usare un account di archiviazione per l'area di lavoroUse a storage account for your workspace

> [!WARNING]
> Se si dispone di data scientist che usano la finestra di progettazione di Azure Machine Learning, riceveranno un errore durante la visualizzazione dei dati da un account di archiviazione all'interno di una rete virtuale. Il testo seguente è l'errore che ricevono:
>
> __Errore: impossibile profilare il set di dati. Ciò potrebbe essere dovuto al fatto che i dati vengono archiviati dietro una rete virtuale o che i dati non supportano il profilo.__

Per usare un account di archiviazione di Azure per l'area di lavoro in una rete virtuale, eseguire la procedura seguente:To use an Azure storage account for the workspace in a virtual network, use the following steps:

1. Creare una risorsa di calcolo (ad esempio, un'istanza di calcolo di Machine Learning o un cluster) dietro una rete virtuale o collegare una risorsa di calcolo all'area di lavoro (ad esempio, un cluster HDInsight, una macchina virtuale o un cluster del servizio Azure Kubernetes). La risorsa di calcolo può essere per la sperimentazione o la distribuzione del modello.

   Per altre informazioni, vedere le sezioni [Usare un calcolo](#amlcompute)di Machine Learning, Usare una macchina virtuale o un cluster [HDInsight](#vmorhdi)e Usare il [servizio Azure Kubernetes](#aksvnet) in questo articolo.

1. Nel portale di Azure passare all'archiviazione collegata all'area di lavoro.

   [![Archiviazione collegata all'area di lavoro di Azure Machine LearningThe storage that's attached to the Azure Machine Learning workspace](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Nella pagina **Archiviazione di Azure** selezionare Firewall e reti __virtuali.__

   ![Area "Firewall e reti virtuali" nella pagina Archiviazione di Azure nel portale di AzureThe "Firewalls and virtual networks" area on the Azure Storage page in the Azure portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Nella pagina Firewall e reti virtuali eseguire le azioni seguenti:On the __Firewalls__ and virtual networks page, do the following actions:
    - Selezionare __Reti selezionate__.
    - In __Reti virtuali__selezionare il collegamento Aggiungi rete virtuale __esistente.__ Questa azione aggiunge la rete virtuale in cui risiede il calcolo (vedere il passaggio 1).

        > [!IMPORTANT]
        > L'account di archiviazione deve trovarsi nella stessa rete virtuale e nella stessa subnet delle istanze di calcolo o dei cluster usati per il training o l'inferenza.

    - Selezionare la casella di controllo __Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione.__

    > [!IMPORTANT]
    > Quando si utilizza Azure Machine Learning SDK, l'ambiente di sviluppo deve essere in grado di connettersi all'account di archiviazione di Azure.When working with the Azure Machine Learning SDK, your development environment must be able to connect to the Azure Storage Account. Quando l'account di archiviazione si trova all'interno di una rete virtuale, il firewall deve consentire l'accesso dall'indirizzo IP dell'ambiente di sviluppo.
    >
    > Per abilitare l'accesso all'account di archiviazione, visitare firewall __e reti virtuali__ per l'account di archiviazione da un Web browser nel client di *sviluppo*. Utilizzare quindi la casella di controllo __Aggiungi indirizzo IP client__ per aggiungere l'indirizzo IP del client a ADDRESS __RANGE__. È inoltre possibile utilizzare il campo __ADDRESS RANGE__ per immettere manualmente l'indirizzo IP dell'ambiente di sviluppo. Dopo aver aggiunto l'indirizzo IP per il client, è possibile accedere all'account di archiviazione usando l'SDK.

   [![Riquadro "Firewall e reti virtuali" nel portale di AzureThe "Firewalls and virtual networks" pane in the Azure portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> È possibile inserire sia _l'account di archiviazione predefinito_ per Azure Machine Learning che gli account di archiviazione non _predefiniti_ in una rete virtuale.
>
> Il provisioning dell'account di archiviazione predefinito viene eseguito automaticamente quando si crea un'area di lavoro.
>
> Per gli account di `storage_account` archiviazione non predefiniti, il parametro nella [ `Workspace.create()` funzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) consente di specificare un account di archiviazione personalizzato in base all'ID risorsa di Azure.For non-default storage accounts, the parameter in the function allows you to specify a custom storage account by Azure resource ID.

## <a name="use-azure-data-lake-storage-gen-2"></a>Usare Azure Data Lake Storage Gen 2Use Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 è un set di funzionalità per l'analisi dei Big Data, basate sull'archiviazione BLOB di Azure.Azure Data Lake Storage Gen 2 is a set of capabilities for big data analytics, built on Azure Blob storage. Può essere usato per archiviare i dati usati per eseguire il training dei modelli con Azure Machine Learning.It can be used to store data used to train models with Azure Machine Learning. 

Per usare Data Lake Storage Gen 2 all'interno della rete virtuale dell'area di lavoro di Azure Machine Learning, eseguire la procedura seguente:To use Data Lake Storage Gen 2 inside the virtual network of your Azure Machine Learning workspace, use the following steps:

1. Creare un account di Azure Data Lake Storage gen 2.Create an Azure Data Lake Storage gen 2 account. Per altre informazioni, vedere Creare un account di archiviazione Gen2 di [Azure Data Lake Storage.](../storage/blobs/data-lake-storage-quickstart-create-account.md)

1. Usare i passaggi da 2 a 4 della sezione precedente [Usare un account di archiviazione per l'area di lavoro](#use-a-storage-account-for-your-workspace)per inserire l'account nella rete virtuale.

Quando si usa Azure Machine Learning con Data Lake Storage Gen 2 in una rete virtuale, usare le indicazioni seguenti:When using Azure Machine Learning with Data Lake Storage Gen 2 inside a virtual network, use the following guidance:

* Se si utilizza l'SDK __per creare un set di dati__e il sistema che esegue il codice non si trova nella rete __virtuale,__ utilizzare il `validate=False` parametro . Questo parametro ignora la convalida, che ha esito negativo se il sistema non si trova nella stessa rete virtuale dell'account di archiviazione. Per ulteriori informazioni, consultate il metodo [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Quando si usa Azure Machine Learning Compute Instance o compute cluster per eseguire il training di un modello usando il set di dati, deve trovarsi nella stessa rete virtuale dell'account di archiviazione.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Usare un'istanza dell'insieme di credenziali delle chiavi con l'area di lavoroUse a key vault instance with your workspace

L'istanza dell'insieme di credenziali della chiave associata all'area di lavoro viene usata da Azure Machine Learning per archiviare le credenziali seguenti:The key vault instance that's associated with the workspace is used by Azure Machine Learning to store the following credentials:
* Stringa di connessione dell'account di archiviazione associata
* Password per le istanze di Azure Container Repository
* Stringhe di connessione agli archivi datiConnection strings to data stores

Per usare le funzionalità di sperimentazione di Azure Machine Learning con l'insieme delle chiavi di Azure dietro una rete virtuale, usare la procedura seguente:To use Azure Machine Learning experimentation capabilities with Azure Key Vault behind a virtual network, use the following steps:

1. Passare all'insieme di credenziali delle chiavi associato all'area di lavoro.

   [![L'insieme di credenziali delle chiavi associato all'area di lavoro di Azure Machine LearningThe key vault that's associated with the Azure Machine Learning workspace](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Nel riquadro sinistro della pagina **Key Vault** selezionare Firewall e __reti virtuali.__

   ![La sezione "Firewall e reti virtuali" nel riquadro Dell'insieme di credenziali delle chiavi](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Nella pagina Firewall e reti virtuali eseguire le azioni seguenti:On the __Firewalls__ and virtual networks page, do the following actions:
    - Selezionare __Reti selezionate__ in __Consenti l'accesso da__.
    - In __Reti virtuali__selezionare Aggiungi reti virtuali __esistenti__ per aggiungere la rete virtuale in cui si trova il calcolo della sperimentazione.
    - In __Consenti ai servizi Microsoft attendibili__di ignorare questo firewall selezionare __Sì__.

   [![La sezione "Firewall e reti virtuali" nel riquadro Dell'insieme di credenziali delle chiavi](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Usare un calcolo di Machine LearningUse a Machine Learning Compute

Per usare un'istanza di calcolo di Azure Machine Learning o un cluster di calcolo in una rete virtuale, è necessario soddisfare i requisiti di rete seguenti:To use an Azure Machine Learning compute instance or compute cluster in a virtual network, the following network requirements must be met:

> [!div class="checklist"]
> * La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area geografica dell'area di lavoro di Azure Machine Learning.The virtual network must be in the same subscription and region as the Azure Machine Learning workspace.
> * La subnet specificata per l'istanza di calcolo o il cluster deve disporre di un numero sufficiente di indirizzi IP non assegnati per contenere il numero di macchine virtuali di destinazione. Se la subnet non dispone di un numero sufficiente di indirizzi IP non assegnati, verrà allocato parzialmente un cluster di calcolo.
> * Verificare se i criteri di sicurezza o i blocchi nella sottoscrizione della rete virtuale o il gruppo di risorse limitano le autorizzazioni per gestire la rete virtuale. Se si prevede di proteggere la rete virtuale limitando il traffico, lasciare alcune porte aperte per il servizio di calcolo. Per altre informazioni, vedere la sezione [Porte richieste](#mlcports).
> * Se si intende inserire più istanze di calcolo o cluster in una rete virtuale, potrebbe essere necessario richiedere un aumento della quota per una o più risorse.
> * Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale dell'istanza di calcolo o del cluster di Azure Machine Learning.If the Azure Storage Account(s) for the workspace are also secured in a virtual network, they must be in the same virtual network as the Azure Machine Learning compute instance or cluster. 

> [!TIP]
> L'istanza di calcolo di Machine Learning o il cluster alloca automaticamente risorse di rete aggiuntive nel gruppo di risorse che contiene la rete virtuale. Per ogni istanza di calcolo o cluster, il servizio alloca le risorse seguenti:For each compute instance or cluster, the service allocates the following resources:
> 
> * Un gruppo di sicurezza di rete
> * Un indirizzo IP pubblico
> * Un bilanciamento del carico
> 
> Queste risorse sono limitate in base alle [quote delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) della sottoscrizione.


### <a name="required-ports"></a><a id="mlcports"></a> Porte richieste

L'ambiente di calcolo di Machine Learning attualmente usa il servizio Azure Batch per effettuare il provisioning delle machine virtuali nella rete virtuale specificata. La subnet deve consentire la comunicazione in ingresso dal servizio Batch. Questa comunicazione viene usata per pianificare le esecuzioni nei nodi di Machine Learning Compute e per comunicare con Archiviazione di Azure e altre risorse. Il servizio Batch aggiunge gruppi di sicurezza di rete (NSG) a livello di interfacce di rete (NIC) collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

- Traffico TCP in ingresso sulle porte 29876 e 29877 da un tag di __servizio__ __batchNodeManagement__.

    ![Una regola in ingresso che utilizza il tag di servizio BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Facoltativo) Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto. Utilizzare questa porta solo se si desidera connettersi utilizzando SSH sull'IP pubblico.

- Traffico in uscita su qualsiasi porta verso la rete virtuale.

- Traffico in uscita su qualsiasi porta verso Internet.

- Per il traffico TCP in ingresso dell'istanza di calcolo sulla porta 44224 da un tag di __servizio__ di __AzureMachineLearning__.

Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio di calcolo imposta lo stato dei nodi di calcolo su inutilizzabile.

Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet, perché il servizio Azure Batch configura i propri gruppi di sicurezza di rete. Tuttavia, se alla subnet specificata sono associati gruppi di sicurezza di rete o un firewall, configurare le regole di sicurezza in ingresso e in uscita come indicato in precedenza.

La configurazione della regola del gruppo di sicurezza di rete nel portale di Azure è illustrata nelle immagini seguenti:The NSG rule configuration in the Azure portal is shown in the following images:

[![Regole del gruppo di sicurezza di gruppo in ingresso per Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Le regole del gruppo di sicurezza di gruppo in uscita per Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Limitare la connettività in uscita dalla rete virtuale

Se non si desidera utilizzare le regole in uscita predefinite e si desidera limitare l'accesso in uscita alla rete virtuale, eseguire la procedura seguente:

- Negare la connessione Internet in uscita utilizzando le regole del gruppo di sicurezza di rete.

- Per __un'istanza__ di calcolo o un cluster di __calcolo,__ limitare il traffico in uscita agli elementi seguenti:For a compute instance or a compute cluster , limit outbound traffic to the following items:
   - Archiviazione di Azure, usando __il tag__ di servizio __di Storage.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.Where is the name of an Azure region.
   - Registro di sistema del contenitore di Azure, usando __il tag di servizio__ di __AzureContainerRegistry.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.Where is the name of an Azure region.
   - Azure Machine Learning, usando __il tag di servizio__ di __AzureMachineLearningAzure__ Machine Learning, by using Service Tag of AzureMachineLearning
   - Azure Resource Manager, usando il __tag di servizio__ di __AzureResourceManagerAzure__ Resource Manager, by using Service Tag of AzureResourceManager
   - Azure Active Directory, usando __il tag di servizio__ di __AzureActiveDirectoryAzure__ Active Directory, by using Service Tag of AzureActiveDirectory

La configurazione della regola del gruppo di sicurezza di rete nel portale di Azure è illustrata nell'immagine seguente:The NSG rule configuration in the Azure portal is shown in the following image:

[![Le regole del gruppo di sicurezza di gruppo in uscita per Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se si prevede di utilizzare le immagini Docker predefinite fornite da Microsoft e di abilitare le dipendenze gestite dall'utente, è necessario utilizzare anche un __tag di servizio__ di __MicrosoftContainerRegistry.Region_Name__ (ad esempio, MicrosoftContainerRegistry.EastUS).
>
> Questa configurazione è necessaria quando si dispone di codice simile ai frammenti seguenti come parte degli script di training:This configuration is needed when you have code similar to the following snippets as part of your training scripts:
>
> __Formazione su RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Formazione dello stimatore__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Route definite dall'utente per il tunneling forzato

Se si usa il tunneling forzato con Machine Learning Compute, aggiungere [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) alla subnet che contiene la risorsa di calcolo.

* Stabilire un UDR per ogni indirizzo IP usato dal servizio Azure Batch nell'area in cui sono presenti le risorse. Questi UDR consentono al servizio Batch di comunicare con i nodi di calcolo per la pianificazione delle attività. Per ottenere un elenco di indirizzi IP del servizio Batch, utilizzare uno dei metodi seguenti:

    * Scaricare gli [intervalli IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519) e cercare il file, dove è l'area di Azure.Download the Azure IP Ranges and Service Tags and search the file for `BatchNodeManagement.<region>`, where `<region>` is your Azure region.

    * Usare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure per scaricare le informazioni. L'esempio seguente scarica le informazioni sull'indirizzo IP e filtra le informazioni per l'area East US 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Il traffico in uscita verso Archiviazione di Azure non deve essere bloccato dall'appliance di rete locale. In particolare, gli URL sono `<account>.table.core.windows.net` `<account>.queue.core.windows.net`nel `<account>.blob.core.windows.net`formato , , e .

Quando si aggiungono le UDR, definire la route per ogni prefisso dell'indirizzo IP Batch correlato e impostare il tipo di __hop successivo__ su __Internet__. L'immagine seguente mostra un esempio di questo UDR nel portale di Azure:The following image shows an example of this UDR in the Azure portal:

![Esempio di un UDR per un prefisso di indirizzo](./media/how-to-enable-virtual-network/user-defined-route.png)

Per altre informazioni, vedere Creare un pool batch di [Azure in una rete virtuale.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Creare un cluster di calcolo in una rete virtualeCreate a compute cluster in a virtual network

Per creare un cluster di Machine Learning Compute, eseguire la procedura seguente:To create a Machine Learning Compute cluster, use the following steps:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/)e quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare __Gruppi di formazione__ al __+__ centro, quindi selezionare .

1. Nella finestra di dialogo __Nuovo gruppo di formazione__ espandere la sezione Impostazioni __avanzate.__

1. Per configurare questa risorsa di calcolo per l'utilizzo di una rete virtuale, eseguire le azioni seguenti nella sezione __Configurare__ la rete virtuale:To configure this compute resource to use a virtual network, perform the following actions in the Configure virtual network section:

    1. Nell'elenco a discesa __Gruppo di__ risorse selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Nell'elenco a discesa __Rete virtuale__ selezionare la rete virtuale che contiene la subnet.
    1. Nell'elenco a discesa __Subnet__ selezionare la subnet da utilizzare.

   ![Impostazioni della rete virtuale per Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

È anche possibile creare un cluster dell'ambiente di calcolo di Machine Learning usando Azure Machine Learning SDK. Il codice seguente crea un nuovo cluster dell'ambiente di calcolo di Machine Learning nella subnet `default` di una rete virtuale denominata `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Al termine del processo di creazione, si esegue il training del modello usando il cluster in un esperimento. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Usare Azure DatabricksUse Azure Databricks

Per usare Azure Databricks in una rete virtuale con l'area di lavoro, è necessario soddisfare i requisiti seguenti:To use Azure Databricks in a virtual network with your workspace, the following requirements must be met:

> [!div class="checklist"]
> * La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area geografica dell'area di lavoro di Azure Machine Learning.The virtual network must be in the same subscription and region as the Azure Machine Learning workspace.
> * Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale del cluster Di Azure Databricks.If the Azure Storage Account(s) for the workspace are also secured in a virtual network, they must be in the same virtual network as the Azure Databricks cluster.
> * Oltre alle subnet __databricks-private__ e __databricks-public__ usate da Azure Databricks, è necessaria anche la subnet __predefinita__ creata per la rete virtuale.

Per informazioni specifiche sull'uso di Azure Databricks con una rete virtuale, vedere Distribuire Azure Databricks nella rete virtuale di Azure.For specific information on using Azure Databricks with a virtual network, see [Deploy Azure Databricks in your Azure Virtual Network.](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Usare una macchina virtuale o un cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning supports only virtual machines that are running Ubuntu.

Per usare una macchina virtuale o un cluster Azure HDInsight in una rete virtuale con l'area di lavoro, eseguire la procedura seguente:To use a virtual machine or Azure HDInsight cluster in a virtual network with your workspace, use the following steps:

1. Creare un cluster VM o HDInsight usando il portale di Azure o l'interfaccia della riga di comando di Azure e inserire il cluster in una rete virtuale di Azure.Create a VM or HDInsight cluster by using the Azure portal or the Azure CLI, and put the cluster in an Azure virtual network. Per altre informazioni, vedere gli articoli seguenti:
    * [Creare e gestire reti virtuali di Azure per macchine virtuali LinuxCreate and manage Azure virtual networks for Linux VMs](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Per consentire ad Azure Machine Learning di comunicare con la porta SSH nella macchina virtuale o nel cluster, configurare una voce di origine per il gruppo di sicurezza di rete. La porta SSH è in genere la porta 22. Per consentire il traffico da questa origine, eseguire le azioni seguenti:To allow traffic from this source, do the following actions:

    * Nell'elenco a discesa __Origine__ selezionare __Tag servizio.__

    * Nell'elenco a discesa __Tag servizio__ di origine selezionare __AzureMachineLearning__.

    * Nell'elenco a discesa __Intervalli__ __*__ di porte di origine selezionare .

    * Nell'elenco a discesa __Destinazione__ selezionare __Qualsiasi__.

    * Nell'elenco a discesa __Intervalli di porte__ di destinazione selezionare __22__.

    * In __Protocollo__selezionare __Qualsiasi__.

    * In __Azione__selezionare __Consenti__.

   ![Regole in ingresso per la sperimentazione in una macchina virtuale o in un cluster HDInsight all'interno di una rete virtualeInbound rules for doing experimentation on a VM or HDInsight cluster within a virtual network](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se non si desidera utilizzare le regole in uscita predefinite e si desidera limitare l'accesso in uscita alla rete virtuale, vedere la sezione [Limitare la connettività in uscita dalla rete virtuale.](#limiting-outbound-from-vnet)

1. Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro di Azure Machine Learning.Attach the VM or HDInsight cluster to your Azure Machine Learning workspace. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Usare il servizio Azure Kubernetes

Per aggiungere AKS in una rete virtuale all'area di lavoro, attenersi alla seguente procedura:

> [!IMPORTANT]
> Prima di iniziare la procedura seguente, seguire i prerequisiti in Configurare la [rete avanzata in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) procedure e pianificare l'indirizzamento IP per il cluster.
>
> L'istanza AKS e la rete virtuale di Azure devono trovarsi nella stessa area. Se si secure gli account di archiviazione di Azure usati dall'area di lavoro in una rete virtuale, devono trovarsi nella stessa rete virtuale dell'istanza AKS.

> [!WARNING]
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con collegamento privato abilitato.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/)e quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare Gruppi di __inferenza__ dal __+__ centro, quindi selezionare .

1. Nella finestra di dialogo __Nuovo cluster di inferenza__ selezionare __Avanzate__ in Configurazione __di rete__.

1. Per configurare questa risorsa di calcolo per l'utilizzo di una rete virtuale, eseguire le azioni seguenti:To configure this compute resource to use a virtual network, perform the following actions:

    1. Nell'elenco a discesa __Gruppo di__ risorse selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Nell'elenco a discesa __Rete virtuale__ selezionare la rete virtuale che contiene la subnet.
    1. Nell'elenco a discesa __Subnet__ selezionare la subnet.
    1. Nella casella Intervallo di indirizzi __del servizio Kubernetes__ immettere l'intervallo di indirizzi del servizio Kubernetes. Questo intervallo di indirizzi utilizza un intervallo IP di notazione CIDR (Classless Inter-Domain Routing) per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi ad alcun intervallo IP di subnet (ad esempio, 10.0.0.0/16).
    1. Nella casella __Indirizzo IP servizio DNS Kubernetes__ immettere l'indirizzo IP del servizio DNS Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes (ad esempio, 10.0.0.10).
    1. Nella casella __Indirizzo bridge Docker__ immettere l'indirizzo del bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in alcun intervallo IP di subnet o nell'intervallo di indirizzi del servizio Kubernetes (ad esempio, 172.17.0.1/16).

   ![Impostazioni della rete virtuale di Azure Machine Learning: Machine Learning Calcolo](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Assicurarsi che il gruppo di gruppi di sicurezza di rete che controlla la rete virtuale disponga di una regola di sicurezza in ingresso abilitata per l'endpoint di assegnazione del punteggio in modo che possa essere chiamato dall'esterno della rete virtuale.
   > [!IMPORTANT]
   > Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Una regola di sicurezza in ingressoAn inbound security rule](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

È anche possibile usare Azure Machine Learning SDK per aggiungere il servizio Azure Kubernetes in una rete virtuale. Se si dispone già di un cluster AKS in una rete virtuale, collegarlo all'area di lavoro come descritto in Come eseguire la [distribuzione in AKS](how-to-deploy-and-where.md). Il codice seguente crea una nuova `default` istanza AKS `mynetwork`nella subnet di una rete virtuale denominata :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Al termine del processo di creazione, è possibile eseguire l'inferenza, o assegnazione di un punteggio di modello, in un cluster AKS dietro una rete virtuale. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Usare gli indirizzi IP privati con il servizio Azure KubernetesUse private IPs with Azure Kubernetes Service

Per impostazione predefinita, un indirizzo IP pubblico viene assegnato alle distribuzioni AKS. Quando si usa AKS all'interno di una rete virtuale, è possibile usare un indirizzo IP privato. Gli indirizzi IP privati sono accessibili solo dall'interno della rete virtuale o delle reti unite.

Un indirizzo IP privato viene abilitato configurando AKS per l'utilizzo di un servizio di _bilanciamento del carico interno._ 

> [!IMPORTANT]
> Non è possibile abilitare l'IP privato durante la creazione del cluster di servizi Azure Kubernetes.You cannot enable private IP when creating the Azure Kubernetes Service cluster. Deve essere abilitato come aggiornamento a un cluster esistente.

Nel frammento di codice seguente viene illustrato come **creare un nuovo cluster AKS**e quindi aggiornarlo per l'utilizzo di un servizio di bilanciamento del carico IP/interno privato:The following code snippet demonstrates how to create a new AKS cluster , and then update it to use a private IP/internal load balancer:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Interfaccia della riga di comando di Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Il contenuto `body.json` del file a cui fa riferimento il comando è simile al seguente documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Attualmente, non è possibile configurare il servizio di bilanciamento del carico quando si esegue un'operazione di __collegamento__ in un cluster esistente. È innanzitutto necessario collegare il cluster e quindi eseguire un'operazione di aggiornamento per modificare il servizio di bilanciamento del carico.

Per altre informazioni sull'uso del servizio di bilanciamento del carico interno con AKS, vedere Usare il servizio di bilanciamento del carico interno con il servizio [Azure Kubernetes.](/azure/aks/internal-lb)

## <a name="use-azure-container-instances-aci"></a>Usare le istanze del contenitore di AzureUse Azure Container Instances (ACI)

Le istanze del contenitore di Azure vengono create dinamicamente quando si distribuisce un modello. Per abilitare Azure Machine Learning per creare ACI all'interno della rete virtuale, è necessario abilitare la delega della __subnet__ per la subnet usata dalla distribuzione.

Per usare ACI in una rete virtuale nell'area di lavoro, eseguire la procedura seguente:To use ACI in a virtual network to your workspace, use the following steps:

1. Per abilitare la delega della subnet nella rete virtuale, usare le informazioni contenute nell'articolo [Aggiungere o rimuovere una delega di subnet.](../virtual-network/manage-subnet-delegation.md) È possibile abilitare la delega durante la creazione di una rete virtuale o aggiungerla a una rete esistente.

    > [!IMPORTANT]
    > Quando si abilita `Microsoft.ContainerInstance/containerGroups` la delega, utilizzare come __subnet delegata per il__ valore del servizio.

2. Distribuire il modello utilizzando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), utilizzare i `vnet_name` parametri e `subnet_name` . Impostare questi parametri sul nome della rete virtuale e sulla subnet in cui è stata abilitata la delega.



## <a name="use-azure-firewall"></a>Usare Firewall di AzureUse Azure Firewall

Quando si usa Firewall di Azure, è necessario configurare una regola di rete per consentire il traffico da e verso gli indirizzi seguenti:When using Azure Firewall, you must configure a network rule to to and from the following addresses:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Quando si aggiunge la __Protocol__ regola, impostare il `*`protocollo su any e le porte su .

Per altre informazioni sulla configurazione di una regola di rete, vedere Distribuire e configurare Firewall di Azure.For more information on configuring a network rule, see [Deploy and configure Azure Firewall.](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)

## <a name="use-azure-container-registry"></a>Usare Registro Azure Container

> [!IMPORTANT]
> Il Registro di sistema del contenitore di Azure (ACR) può essere inserito in una rete virtuale, tuttavia è necessario soddisfare i prerequisiti seguenti:Azure Container Registry (ACR) can be put inside a virtual network, however you must meet the following prerequisites:
>
> * L'area di lavoro di Azure Machine Learning deve essere Enterprise Edition.Your Azure Machine Learning workspace must be Enterprise edition. Per informazioni sull'aggiornamento, vedere [Aggiornamento all'edizione Enterprise](how-to-manage-workspace.md#upgrade).
> * Il Registro di sistema del contenitore di Azure deve essere versione Premium . Per ulteriori informazioni sull'aggiornamento, vedere [Modifica degli SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Il Registro di sistema del contenitore di Azure deve trovarsi nella stessa rete virtuale e subnet dell'account di archiviazione e delle destinazioni di calcolo usate per il training o l'inferenza.
> * L'area di lavoro di Azure Machine Learning deve contenere un cluster di [calcolo](how-to-set-up-training-targets.md#amlcompute)di Azure Machine Learning.Your Azure Machine Learning workspace must contain an Azure Machine Learning compute cluster .
>
>     Quando ACR è dietro una rete virtuale, Azure Machine Learning non può usarlo per creare direttamente immagini Docker.When ACR is behind a virtual network, Azure Machine Learning cannot use it to directly build Docker images. Al contrario, il cluster di calcolo viene utilizzato per compilare le immagini.

1. Per trovare il nome del Registro di sistema del contenitore di Azure per l'area di lavoro, usare uno dei metodi seguenti:

    __Azure portal__

    Nella sezione Panoramica dell'area di lavoro, il valore __del Registro__ di sistema è collegato al Registro di sistema del contenitore di Azure.From the overview section of your workspace, the Registry value links to the Azure Container Registry.

    ![Registro dei contenitori di Azure per l'area di lavoroAzure Container Registry for the workspace](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Interfaccia della riga di comando di Azure__

    Se è stata [installata l'estensione di Machine Learning per l'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando di Azure, è possibile usare il `az ml workspace show` comando per visualizzare le informazioni sull'area di lavoro.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Il comando restituisce un valore analogo a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. L'ultima parte della stringa è il nome del Registro di sistema del contenitore di Azure per l'area di lavoro.

1. Per limitare l'accesso alla rete virtuale, attenersi alla procedura descritta in [Configurare l'accesso](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)alla rete per il Registro di sistema . Quando si aggiunge la rete virtuale, selezionare la rete virtuale e la subnet per le risorse di Azure Machine Learning.When adding the virtual network, select the virtual network and subnet for your Azure Machine Learning resources.

1. Usare Azure Machine Learning Python SDK per configurare un cluster di calcolo per creare immagini docker. Il frammento di codice seguente illustra come eseguire questa operazione:The following code snippet demonstrates how to do this:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > L'account di archiviazione, il cluster di calcolo e il Registro contenitori di Azure devono trovarsi tutti nella stessa subnet della rete virtuale.
    
    Per ulteriori informazioni, consultate il riferimento al metodo [update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

1. Se si usa Private Link per l'area di lavoro di Azure Machine Learning e si inserisce il Registro di sistema del contenitore di Azure per l'area di lavoro in una rete virtuale, è necessario applicare anche il modello di Azure Resource Manager seguente. Questo modello consente all'area di lavoro di comunicare con ACR tramite il collegamento privato.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare ambienti di training](how-to-set-up-training-targets.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine LearningUse TLS to secure a web service through Azure Machine Learning](how-to-secure-web-service.md)
