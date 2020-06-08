---
title: Isolamento rete e privacy
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata con Azure Machine Learning per proteggere i processi di sperimentazione/training e di inferenza/assegnazione dei punteggi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 05/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 17c6e10b213cb1f3d2b20433a5511c27960cdb06
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816302"
---
# <a name="secure-your-machine-learning-lifecycles-with-private-virtual-networks"></a>Proteggere i cicli di vita di Machine Learning con le reti virtuali private
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come isolare i processi di sperimentazione/training e i processi di inferenza/assegnazione dei punteggi in Azure Machine Learning all'interno di una rete virtuale di Azure. Verranno inoltre illustrate alcune *impostazioni di sicurezza avanzate*, informazioni non necessarie per i casi d'uso di base o sperimentali.

> [!WARNING]
> Se la risorsa di archiviazione sottostante si trova in una rete virtuale, gli utenti non saranno in grado di usare l'esperienza Web di Azure Machine Learning Studio, tra cui:
> - Finestra di progettazione con trascinamento della selezione
> - Interfaccia utente per Machine Learning automatizzato
> - Interfaccia utente per etichettatura dei dati
> - Interfaccia utente per set di dati
> - Notebook
> 
> Se si tenta, verrà visualizzato un messaggio simile all'errore seguente: `__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="what-is-a-vnet"></a>Informazioni sulla rete virtuale

Una **rete virtuale** funge da limite di sicurezza, isolando le risorse di Azure dalla rete Internet pubblica. È anche possibile aggiungere una rete virtuale di Azure alla rete locale. Aggiungendo le reti, si può eseguire il training dei modelli in modo sicuro e accedere ai modelli distribuiti per l'inferenza.

Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo, note anche come [destinazioni di calcolo](concept-compute-target.md), per eseguire il training e distribuire i modelli. Le destinazioni possono essere create all'interno di una rete virtuale. È ad esempio possibile usare l'ambiente di calcolo di Azure Machine Learning per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes. 


## <a name="prerequisites"></a>Prerequisiti

+ Un'[area di lavoro](how-to-manage-workspace.md) di Azure Machine Learning.

+ Conoscenza tecnica generale del [servizio Rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e delle [reti IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Una rete virtuale e una subnet preesistenti da usare con le risorse di calcolo.

## <a name="private-endpoints"></a>Endpoint privati

È anche possibile [abilitare il collegamento privato di Azure](how-to-configure-private-link.md) per connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. [Informazioni su come configurare questo endpoint privato.](how-to-configure-private-link.md)



> [!TIP]
> È possibile combinare la rete virtuale e il collegamento privato per proteggere le comunicazioni tra l'area di lavoro e altre risorse di Azure. Tuttavia, alcune combinazioni richiedono un'area di lavoro Enterprise. Usare la tabella seguente per capire quali scenari richiedono l'edizione Enterprise:
>
> | Scenario | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | Nessuna rete virtuale o collegamento privato | ✔ | ✔ |
> | Area di lavoro senza collegamento privato. Altre risorse (ad eccezione di Registro Azure Container) in una rete virtuale | ✔ | ✔ |
> | Area di lavoro senza collegamento privato. Altre risorse con collegamento privato | ✔ | |
> | Area di lavoro con collegamento privato. Altre risorse (ad eccezione di Registro Azure Container) in una rete virtuale | ✔ | ✔ |
> | Area di lavoro e qualsiasi altra risorsa con collegamento privato | ✔ | |
> | Area di lavoro con collegamento privato. Altre risorse senza collegamento privato o rete virtuale | ✔ | ✔ |
> | Registro Azure Container in una rete virtuale | ✔ | |
> | Chiavi gestite dal cliente per l'area di lavoro | ✔ | |
> 

> [!WARNING]
> L'anteprima delle istanze di calcolo di Azure Machine Learning non è supportata in un'area di lavoro in cui è abilitato il collegamento privato.
> 
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con collegamento privato abilitato. In alternativa, è possibile usare il servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere [Proteggere i processi di sperimentazione e inferenza di Azure ML in una rete virtuale di Azure](how-to-enable-virtual-network.md).


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Cluster di elaborazione e istanze di calcolo

Per usare una [**destinazione di calcolo** gestita di Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) o un'[istanza **di calcolo di Azure Machine Learning**](concept-compute-instance.md) in una rete virtuale, è necessario che siano soddisfatti i seguenti requisiti di rete:

> [!div class="checklist"]
> * La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro di Azure Machine Learning.
> * La subnet specificata per il cluster di elaborazione o l'istanza di calcolo deve avere indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione. Se la subnet non ha abbastanza indirizzi IP non assegnati, verrà parzialmente allocato un cluster di elaborazione.
> * Controllare se i criteri di sicurezza o i blocchi nel gruppo di risorse o nella sottoscrizione della rete virtuale limitano le autorizzazioni per gestire la rete virtuale. Se si prevede di proteggere la rete virtuale limitando il traffico, lasciare aperte alcune porte per il servizio dell'ambiente di calcolo. Per altre informazioni, vedere la sezione [Porte richieste](#mlcports).
> * Se si prevede di inserire più istanze di calcolo o cluster di elaborazione in una sola rete virtuale, potrebbe essere necessario richiedere un aumento di quota per una o più risorse.
> * Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale dell'istanza di calcolo o del cluster di elaborazione di Azure Machine Learning. 
> * Per il corretto funzionamento della funzionalità Jupyter dell'istanza di calcolo, assicurarsi che la comunicazione con il Websocket non sia disabilitata.

> [!TIP]
> L'istanza di calcolo o il cluster di elaborazione di Machine Learning alloca automaticamente le risorse di rete aggiuntive __nel gruppo di risorse contenente la rete virtuale__. Per ogni istanza o cluster, il servizio alloca le risorse seguenti:
> 
> * Un gruppo di sicurezza di rete
> * Un indirizzo IP pubblico
> * Un bilanciamento del carico
> 
> Nel caso dei cluster queste risorse vengono eliminate e ricreate ogni volta che il cluster si ridimensiona fino a 0 nodi, tuttavia, per un'istanza le risorse vengono mantenute finché l'istanza viene eliminata completamente (l'arresto non comporta la rimozione delle risorse). 
> Queste risorse sono limitate in base alle [quote delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) della sottoscrizione.


### <a name="required-ports"></a><a id="mlcports"></a> Porte richieste

L'ambiente di calcolo di Machine Learning attualmente usa il servizio Azure Batch per effettuare il provisioning delle machine virtuali nella rete virtuale specificata. La subnet deve consentire la comunicazione in ingresso dal servizio Batch. Usare questa comunicazione per pianificare le esecuzioni nei nodi dell'ambiente di calcolo di Machine Learning e per comunicare con Archiviazione di Azure e altre risorse. Il servizio Batch aggiunge gruppi di sicurezza di rete a livello di interfacce di rete collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

- Traffico TCP in ingresso sulle porte 29876 e 29877 da un __tag del servizio__ di __BatchNodeManagement__.

    ![Una regola in ingresso che usa il tag del servizio BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Facoltativo) Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto. Usare questa porta solo per effettuare la connessione usando SSH sull'indirizzo IP pubblico.

- Traffico in uscita su qualsiasi porta verso la rete virtuale.

- Traffico in uscita su qualsiasi porta verso Internet.

- Per il traffico TCP in ingresso dell'istanza di calcolo sulla porta 44224 da un __tag del servizio__ di __AzureMachineLearning__.

Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio dell'ambiente di calcolo imposta i nodi di calcolo come non utilizzabili.

Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet perché il servizio Azure Batch configura gruppi di sicurezza di rete propri. Se tuttavia alla subnet specificata sono associati gruppi di sicurezza di rete o un firewall, configurare le regole di sicurezza in ingresso e in uscita, come accennato in precedenza.

La configurazione della regola del gruppo di sicurezza di rete nel portale di Azure è illustrata nelle immagini seguenti:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Regole dei gruppi di sicurezza di rete in ingresso per l'ambiente di calcolo di Machine Learning" border="true":::



![Regole dei gruppi di sicurezza di rete in uscita per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Limitare la connettività in uscita dalla rete virtuale

Se non si vogliono usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, seguire questa procedura:

- Negare la connessione Internet in uscita usando le regole del gruppo di sicurezza di rete.

- Per un'__istanza di calcolo__ o un __cluster di elaborazione__, limitare il traffico in uscita agli elementi seguenti:
   - Archiviazione di Azure, usando __tag del servizio__ di __Storage.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.
   - Registro Azure Container, usando il __tag del servizio__ di __AzureContainerRegistry.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.
   - Azure Machine Learning, usando il __tag del servizio__ di __AzureMachineLearning__
   - Azure Resource Manager, usando il __tag del servizio__ di __AzureResourceManager__
   - Azure Active Directory, usando il __tag del servizio__ di __AzureActiveDirectory__

La configurazione della regola del gruppo di sicurezza di rete nel portale di Azure è illustrata nell'immagine seguente:

[![Regole dei gruppi di sicurezza di rete in uscita per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se si prevede di usare le immagini Docker predefinite di Microsoft e di abilitare le dipendenze gestite dall'utente, è necessario usare anche un __tag del servizio__ di __MicrosoftContainerRegistry.Region_Name__, ad esempio MicrosoftContainerRegistry.EastUS.
>
> Questa configurazione è necessaria quando si ha un codice simile ai frammenti di codice seguenti come parte degli script di training:
>
> __Training RunConfig__
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
> __Training strumento di valutazione__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Route definite dall'utente per il tunneling forzato

Se si usa il tunneling forzato con il ambiente di calcolo di Machine Learning, aggiungere [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) alla subnet che contiene la risorsa di calcolo.

* Stabilire una route definita dall'utente per ogni indirizzo IP usato dal servizio Azure Batch nell'area in cui si trovano le risorse. Queste route consentono al servizio Batch di comunicare con i nodi di calcolo per la pianificazione delle attività. Aggiungere anche l'indirizzo IP per il servizio Azure Machine Learning in cui si trovano le risorse, poiché è necessario per l'accesso alle istanze di calcolo. Per ottenere un elenco di indirizzi IP del servizio Batch e del servizio Azure Machine Learning, usare uno dei metodi seguenti:

    * Scaricare [gli intervalli IP e i tag del servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) e cercare `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` nel file, dove `<region>` è l'area di Azure.

    * Usare l['interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per scaricare le informazioni. L'esempio seguente scarica le informazioni relative all'indirizzo IP e filtra le informazioni per l'area Stati Uniti orientali 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* Il traffico in uscita verso Archiviazione di Azure non deve essere bloccato dall'appliance di rete locale. In particolare, gli URL sono in formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`.

Quando si aggiungono le route definite dall'utente, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare __Tipo hop successivo__ su __Internet__. La figura seguente illustra un esempio di route definita dall'utente nel portale di Azure:

![Esempio di route definita dall'utente per un prefisso di indirizzo](./media/how-to-enable-virtual-network/user-defined-route.png)

Per altre informazioni, vedere [Creare un pool di Azure Batch in una rete virtuale](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Creare un cluster di elaborazione in una rete virtuale

Per creare un cluster dell'ambiente di calcolo di Machine Learning seguire questa procedura:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/), quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare __Cluster di training__ dal centro, quindi selezionare __+__ .

1. Nella finestra di dialogo di __creazione di un cluster di training__ espandere la sezione __Impostazioni avanzate__.

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, eseguire le azioni seguenti nella sezione __Configura rete virtuale__:

    1. Nell'elenco a discesa __Gruppo di risorse__ selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Selezionare la rete virtuale che contiene la subnet nell'elenco a discesa __Rete virtuale__.
    1. Nell'elenco a discesa __Subnet__ selezionare la subnet da usare.

   ![Impostazioni della rete virtuale per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

Al termine del processo di creazione, eseguire il training del modello usando il cluster in un esperimento. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="use-a-storage-account-for-your-workspace"></a>Usare un account di archiviazione per l'area di lavoro

Per usare un account di archiviazione di Azure per l'area di lavoro in una rete virtuale, seguire questa procedura:

1. Creare una risorsa di calcolo, ad esempio un'istanza di calcolo o un cluster di elaborazione di Machine Learning, in una rete virtuale oppure collegare una risorsa di calcolo all'area di lavoro, ad esempio, un cluster HDInsight, una macchina virtuale o un cluster del servizio Azure Kubernetes. La risorsa di calcolo può essere per la sperimentazione o la distribuzione del modello.

   Per altre informazioni, vedere le sezioni [Usare un ambiente di calcolo di Machine Learning](#amlcompute), [Usare una macchina virtuale o un cluster HDInsight](#vmorhdi) e [Usare il servizio Azure Kubernetes](#aksvnet) in questo articolo.

1. Nel portale di Azure passare alla risorsa di archiviazione collegata all'area di lavoro.

   [![Archiviazione collegata all'area di lavoro di Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Selezionare **Firewall e reti virtuali** nella pagina __Archiviazione di Azure__.

   ![L'area "Firewall e reti virtuali" della pagina Archiviazione di Azure nel portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Nella pagina __Firewall e reti virtuali__ eseguire le operazioni seguenti:
    - Selezionare __Reti selezionate__.
    - In __Reti virtuali__ selezionare il collegamento __Aggiungi rete virtuale esistente__. Questa azione aggiunge la rete virtuale in cui risiede il calcolo (vedere il passaggio 1).

        > [!IMPORTANT]
        > L'account di archiviazione deve trovarsi nella stessa rete virtuale e nella stessa subnet delle istanze di calcolo o dei cluster di elaborazione usati per il training o l'inferenza.

    - Selezionare la casella di controllo __Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione__.

    > [!IMPORTANT]
    > Quando si usa Azure Machine Learning SDK, l'ambiente di sviluppo deve essere in grado di connettersi all'account di archiviazione di Azure. Se l'account di archiviazione si trova all'interno di una rete virtuale, il firewall deve consentire l'accesso dall'indirizzo IP dell'ambiente di sviluppo.
    >
    > Per abilitare l'accesso all'account di archiviazione, vedere i __firewall e le reti virtuali__ per l'account di archiviazione *da un Web browser nel client di sviluppo*. Usare quindi la casella di controllo __Aggiungere l'indirizzo IP client__ per aggiungere l'indirizzo IP del client all'__intervallo di indirizzi__. È anche possibile usare il campo __INTERVALLO DI INDIRIZZI__ per immettere manualmente l'indirizzo IP dell'ambiente di sviluppo. Dopo aver aggiunto l'indirizzo IP per il client, è possibile accedere all'account di archiviazione usando l'SDK.

   [![Il riquadro "Firewall e reti virtuali" nel portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> È possibile inserire sia l'_account di archiviazione predefinito_ per Azure Machine Learning, sia _gli account di archiviazione non predefiniti_ in una rete virtuale.
>
> Quando si crea un'area di lavoro, viene eseguito automaticamente il provisioning dell'account di archiviazione predefinito.
>
> Per gli account di archiviazione non predefiniti, il parametro `storage_account` nella funzione [`Workspace.create()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)consente di specificare un account di archiviazione personalizzato in base all'ID risorsa di Azure.


<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Per aggiungere il servizio Azure Kubernetes all'area di lavoro in una rete virtuale, seguire questa procedura:

> [!IMPORTANT]
> Prima di iniziare la procedura seguente, verificare i prerequisiti in [Configurare funzionalità di rete avanzate nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) e pianificare gli indirizzi IP per il cluster.
>
> L'istanza del servizio Azure Kubernetes e la rete virtuale di Azure devono trovarsi nella stessa area. Se si proteggono gli account di archiviazione di Azure usati dall'area di lavoro in una rete virtuale, è necessario che siano nella stessa rete virtuale dell'istanza del servizio Azure Kubernetes.

> [!WARNING]
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con collegamento privato abilitato.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/), quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare __Inference clusters__ (Cluster di inferenza) dal centro, quindi selezionare __+__ .

1. Nella finestra di dialogo __New Inference Cluster__ (Nuovo cluster di inferenza) selezionare __Avanzata__ in __Configurazione di rete__.

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, eseguire le operazioni seguenti:

    1. Nell'elenco a discesa __Gruppo di risorse__ selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Selezionare la rete virtuale che contiene la subnet nell'elenco a discesa __Rete virtuale__.
    1. Selezionare la subnet nell'elenco a discesa __Subnet__.
    1. Immettere l'intervallo di indirizzi del servizio Kubernetes nella casella __Intervallo di indirizzi del servizio Kubernetes__. Questo intervallo di indirizzi usa un indirizzo IP in notazione CIDR (Classless Inter-Domain Routing) per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi a nessun intervallo IP della subnet, ad esempio 10.0.0.0/16.
    1. Nella casella __Indirizzo IP del servizio DNS di Kubernetes__ immettere l'indirizzo IP del servizio DNS di Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 10.0.0.10.
    1. Nella casella __Indirizzo del bridge Docker__ immettere l'indirizzo del Bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in nessun intervallo IP della subnet o nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 172.17.0.1/16.

   ![Azure Machine Learning: impostazioni della rete virtuale dell'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verificare che per il gruppo di sicurezza di rete che controlla la rete virtuale sia abilitata una regola di sicurezza in ingresso per l'endpoint di punteggio, in modo che possa essere chiamata dall'esterno della rete virtuale.
   > [!IMPORTANT]
   > Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Aggiungere una regola di sicurezza in ingresso](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

È anche possibile usare Azure Machine Learning SDK per aggiungere il servizio Azure Kubernetes in una rete virtuale. Se è già presente un cluster del servizio Azure Kubernetes in una rete virtuale, collegarlo all'area di lavoro come descritto nell'articolo relativo alla [distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md). Il codice seguente crea una nuova istanza del servizio Azure Kubernetes nella subnet `default` di una rete virtuale denominata `mynetwork`:

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

Al termine del processo di creazione, è possibile eseguire l'inferenza o il calcolo del modello in un cluster del servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Usare gli indirizzi IP privati con il servizio Azure Kubernetes

Per impostazione predefinita, viene assegnato un indirizzo IP pubblico alle distribuzioni del servizio Azure Kubernetes. Quando si usa il servizio Azure Kubernetes all'interno di una rete virtuale, è invece possibile usare un indirizzo IP privato. Gli indirizzi IP privati sono accessibili solo dall'interno della rete virtuale o da reti unite in join.

Un indirizzo IP privato viene abilitato configurando il servizio Azure Kubernetes per l'uso di un _bilanciamento del carico interno_. 

> [!IMPORTANT]
> Non è possibile abilitare l'indirizzo IP privato durante la creazione del cluster del servizio Azure Kubernetes. Deve essere abilitato come aggiornamento a un cluster esistente.

Il frammento di codice seguente illustra come **creare un nuovo cluster del servizio Azure Kubernetes** e quindi aggiornarlo per usare un bilanciamento del carico interno/IP privato:

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
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Il contenuto del file `body.json` a cui fa riferimento il comando è simile al documento JSON seguente:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
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
> Attualmente non è possibile configurare il bilanciamento del carico durante l'esecuzione di un'operazione di __associazione__ in un cluster esistente. Per prima cosa è necessario associare il cluster, quindi eseguire un'operazione di aggiornamento per modificare il bilanciamento del carico.

Per altre informazioni sull'uso del bilanciamento del carico interno con il servizio Azure Kubernetes, vedere l'articolo sull'[uso del bilanciamento del carico interno con il servizio Azure Kubernetes](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Usare Istanze di Azure Container

Le Istanze di Azure Container vengono create dinamicamente quando si distribuisce un modello. Per abilitare Azure Machine Learning per la creazione di Istanze di Azure Container all'interno della rete virtuale, è necessario abilitare la __delega subnet__ per la subnet usata dalla distribuzione.

Per usare Istanze di Azure Container all'area di lavoro, seguire questa procedura:

1. Per abilitare la delega subnet nella rete virtuale, usare le informazioni contenute nell'articolo relativo ad [aggiunta e rimozione di una delega subnet](../virtual-network/manage-subnet-delegation.md). È possibile abilitare la delega quando si crea una rete virtuale o aggiungerla a una rete esistente.

    > [!IMPORTANT]
    > Quando si abilita la delega, usare `Microsoft.ContainerInstance/containerGroups` come valore di __Delega subnet a un servizio__.

2. Distribuire il modello usando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), usare i parametri `vnet_name` e `subnet_name`. Impostare questi parametri sul nome e sulla subnet della rete virtuale in cui è stata abilitata la delega.

## <a name="azure-firewall"></a>Firewall di Azure

Per informazioni sull'uso di Azure Machine Learning con Firewall di Azure, vedere l'articolo sull'[uso dell'area di lavoro di Azure Machine Learning area di lavoro dietro Firewall di Azure](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Registro Azure Container

> [!IMPORTANT]
> Il Registro Azure Container può essere inserito in una rete virtuale, ma è necessario soddisfare i prerequisiti seguenti:
>
> * L'area di lavoro di Azure Machine Learning deve essere di tipo Enterprise. Per informazioni sull'aggiornamento, vedere le istruzioni per [l'aggiornamento a Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * La versione di Registro Azure Container deve essere Premium. Per altre informazioni sull'aggiornamento, vedere [Cambiare SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Registro Azure Container deve essere nella stessa rete virtuale e nella stessa subnet dell'account di archiviazione e delle destinazioni di calcolo usati per il training o l'inferenza.
> * L'area di lavoro di Azure Machine Learning deve contenere un [cluster di elaborazione di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Quando Registro Azure Container è in una rete virtuale, Azure Machine Learning non può usarlo per compilare direttamente le immagini Docker. In questo caso viene usato il cluster di elaborazione per compilare le immagini.

1. Per trovare il nome del Registro Azure Container per la propria area di lavoro, usare uno dei metodi seguenti:

    __Azure portal__

    Nella sezione Panoramica dell'area di lavoro il valore __Registro__ è collegato al Registro Azure Container.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro Azure Container per l'area di lavoro" border="true":::

    __Interfaccia della riga di comando di Azure__

    Se è stata installata [l'estensione Machine Learning per l'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md), è possibile usare il comando `az ml workspace show` per visualizzare le informazioni sull'area di lavoro.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Il comando restituisce un valore analogo a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. L'ultima parte della stringa è il nome del Registro Azure Container per l'area di lavoro.

1. Per limitare l'accesso alla rete virtuale, seguire la procedura relativa alla [configurazione dell'accesso alla rete per il registro](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Quando si aggiunge la rete virtuale, selezionare la rete virtuale e la subnet per le risorse di Azure Machine Learning.

1. Usare Python SDK di Azure Machine Learning per configurare un cluster di elaborazione per compilare immagini Docker. Nel frammento di codice riportato di seguito viene illustrato come effettuare questa operazione:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > L'account di archiviazione, il cluster di elaborazione e Registro Azure Container devono essere tutti nella stessa subnet della rete virtuale.
    
    Per altre informazioni, vedere il riferimento per il metodo [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

1. Deve essere applicato il seguente modello di Azure Resource Manager, che consente all'area di lavoro di comunicare con Registro Azure Container.

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
    
## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage Gen2 è un set di funzionalità per l'analisi dei Big Data integrato in Archiviazione BLOB di Azure. Può essere usato per archiviare i dati usati per il training dei modelli con Azure Machine Learning. 

Per usare Data Lake Storage Gen 2 nella rete virtuale dell'area di lavoro di Azure Machine Learning, seguire questa procedura:

1. Creare un account di Azure Data Lake Storage Gen 2. Per altre informazioni, vedere [Creare un account di archiviazione di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Usare i passaggi 2-4 della sezione precedente, [Usare un account di archiviazione per l'area di lavoro](#use-a-storage-account-for-your-workspace) per inserire l'account nella rete virtuale.

Per usare Azure Machine Learning con Data Lake Storage Gen 2 all'interno di una rete virtuale, seguire questa procedura:

* Se si usa l'__SDK per creare un set di dati__e il sistema che esegue il codice __non è nella rete virtuale__, usare il parametro `validate=False`. Questo parametro ignora la convalida, che ha esito negativo se il sistema non è nella stessa rete virtuale dell'account di archiviazione. Per altre informazioni, vedere il metodo [from_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

* Quando si usa un'istanza di calcolo o un cluster di elaborazione di Azure Machine Learning per eseguire il training di un modello usando il set di dati, questo deve trovarsi nella stessa rete virtuale dell'account di archiviazione.

## <a name="key-vault-instance"></a>Istanza dell'insieme di credenziali delle chiavi 

L'istanza dell'insieme di credenziali delle chiavi associata all'area di lavoro viene usata da Azure Machine Learning per archiviare le credenziali seguenti:
* La stringa di connessione all'account di archiviazione associato
* Le password per le istanze del repository del contenitore di Azure
* Le stringhe di connessione agli archivi dati

Per usare le funzionalità di sperimentazione di Azure Machine Learning con Azure Key Vault in una rete virtuale, seguire questa procedura:

1. Passare all'insieme di credenziali delle chiavi associato all'area di lavoro.

   [![Insieme di credenziali delle chiavi associato all'area di lavoro di Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Selezionare **Firewall e reti virtuali** nel riquadro a sinistra nella pagina __Key Vault__.

   ![Sezione "Firewall e reti virtuali" nel riquadro Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Nella pagina __Firewall e reti virtuali__ eseguire le operazioni seguenti:
    - Selezionare __Reti selezionate__ in __Consenti l'accesso da__.
    - In __Reti virtuali__ selezionare __Aggiungi reti virtuali esistenti__ per aggiungere la rete virtuale in cui risiede il calcolo della sperimentazione.
    - In __Consentire ai servizi Microsoft attendibili di ignorare il firewall__ scegliere __Sì__.

   [![Sezione "Firewall e reti virtuali" nel riquadro Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Per usare Azure Databricks in una rete virtuale con l'area di lavoro, è necessario soddisfare i requisiti seguenti:

> [!div class="checklist"]
> * La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro di Azure Machine Learning.
> * Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale del cluster di Azure Databricks.
> * Oltre alle subnet __databricks-private__ e __databricks-public__ usate da Azure Databricks, è necessaria anche la subnet __predefinita__ creata per la rete virtuale.

Per informazioni specifiche sull'uso di Azure Databricks con una rete virtuale, vedere [Distribuire Azure Databricks nella rete virtuale di Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Macchina virtuale o cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu.

Per usare una macchina virtuale o un cluster Azure HDInsight in una rete virtuale con l'area di lavoro, seguire questa procedura:

1. Creare una macchina virtuale o un cluster HDInsight usando il portale di Azure o l'interfaccia della riga di comando di Azure e inserire il cluster in una rete virtuale di Azure. Per altre informazioni, vedere gli articoli seguenti:
    * [Creare e gestire reti virtuali di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Per consentire ad Azure Machine Learning di comunicare con la porta SSH nella macchina virtuale o nel cluster, configurare una voce di origine per il gruppo di sicurezza di rete. La porta SSH è in genere la porta 22. Per consentire il traffico da questa origine, eseguire queste operazioni:

    * Nell'elenco a discesa __Origine__ selezionare __Tag del servizio__.

    * Nell'elenco a discesa __Tag del servizio di origine__ selezionare __AzureMachineLearning__.

    * Nell'elenco a discesa __Intervalli di porte di origine__ selezionare __*__ .

    * Nell'elenco a discesa __Destinazione__ selezionare __Qualsiasi__.

    * Nell'elenco a discesa __Intervalli di porte di destinazione__ selezionare __22__.

    * In __Protocollo__ selezionare __Qualsiasi__.

    * In __Azione__ selezionare __Consenti__.

   ![Regole in ingresso per la sperimentazione in una macchina virtuale o un cluster HDInsight in una rete virtuale](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenere le regole in ingresso per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se non si vogliono usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, vedere la sezione [Limitare la connettività in uscita dalla rete virtuale](#limiting-outbound-from-vnet).

1. Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Passaggi successivi

* [Configurare ambienti di training](how-to-set-up-training-targets.md)
* [Configurare endpoint privati](how-to-configure-private-link.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
