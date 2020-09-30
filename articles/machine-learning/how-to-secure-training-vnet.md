---
title: Ambienti di training protetti con reti virtuali
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata per proteggere l'ambiente di training Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python, contperfq1
ms.openlocfilehash: 7953573d5e82c6393a1dc1f0407835cb307638e5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533078"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Proteggere un ambiente di training Azure Machine Learning con reti virtuali

Questo articolo illustra come proteggere gli ambienti di training con una rete virtuale in Azure Machine Learning.

Questo articolo è la terza parte di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. È consigliabile leggere prima di tutto la [parte 1: Panoramica di VNet](how-to-network-security-overview.md) per comprendere prima l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica](how-to-network-security-overview.md)  >  [di VNet proteggere l'area di lavoro](how-to-secure-workspace-vnet.md)  >  **3. Proteggere l'ambiente di training**  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)   >  [5. Abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)

Questo articolo illustra come proteggere le risorse di calcolo di training seguenti in una rete virtuale:
> [!div class="checklist"]
> - Cluster di calcolo di Azure Machine Learning
> - Istanza di calcolo di Azure Machine Learning
> - Azure Databricks
> - Macchina virtuale
> - Cluster HDInsight

## <a name="prerequisites"></a>Prerequisiti

+ Vedere l'articolo [Panoramica della sicurezza di rete](how-to-network-security-overview.md) per comprendere gli scenari di rete virtuale comuni e l'architettura complessiva della rete virtuale.

+ Una rete virtuale e una subnet esistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve avere le autorizzazioni per le azioni seguenti nei controlli degli accessi in base al ruolo (RBAC) di Azure:

    - "Microsoft. Network/virtualNetworks/join/Action" sulla risorsa di rete virtuale.
    - "Microsoft. Network/virtualNetworks/subnet/join/Action" sulla risorsa della subnet.

    Per ulteriori informazioni sul controllo degli accessi in base al ruolo con la rete, vedere [ruoli predefiniti di rete](/azure/role-based-access-control/built-in-roles#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Cluster di elaborazione e istanze di calcolo 

Per usare una [__destinazione di calcolo__ gestita di Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) o un'[istanza __di calcolo di Azure Machine Learning__](concept-compute-instance.md) in una rete virtuale, è necessario che siano soddisfatti i seguenti requisiti di rete:

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

Se si prevede di proteggere la rete virtuale limitando il traffico di rete da e verso la rete Internet pubblica, è necessario consentire le comunicazioni in ingresso dal servizio Azure Batch.

Il servizio Batch aggiunge gruppi di sicurezza di rete a livello di interfacce di rete collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

- Traffico TCP in ingresso sulle porte 29876 e 29877 da un __tag del servizio__ di __BatchNodeManagement__.

    ![Una regola in ingresso che usa il tag del servizio BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Facoltativo) Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto. Usare questa porta solo per effettuare la connessione usando SSH sull'indirizzo IP pubblico.

- Traffico in uscita su qualsiasi porta verso la rete virtuale.

- Traffico in uscita su qualsiasi porta verso Internet.

- Per il traffico TCP in ingresso dell'istanza di calcolo sulla porta 44224 da un __tag del servizio__ di __AzureMachineLearning__.

> [!IMPORTANT]
> Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio dell'ambiente di calcolo imposta i nodi di calcolo come non utilizzabili.
>
> Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet perché il servizio Azure Batch configura gruppi di sicurezza di rete propri. Tuttavia, se la subnet che contiene il Azure Machine Learning calcolo ha associato gruppi o un firewall, è necessario consentire anche il traffico indicato in precedenza.

La configurazione della regola del gruppo di sicurezza di rete nel portale di Azure è illustrata nelle immagini seguenti:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Regole dei gruppi di sicurezza di rete in ingresso per l'ambiente di calcolo di Machine Learning" border="true":::



![Regole NSG in ingresso per ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

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
> Se si prevede di usare le immagini Docker predefinite fornite da Microsoft e di abilitare le dipendenze gestite dall'utente, è necessario usare anche i __tag di servizio__seguenti:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
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

### <a name="forced-tunneling"></a>Tunneling forzato

Se si usa il [tunneling forzato](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) con Azure Machine Learning calcolo, è necessario consentire la comunicazione con la rete Internet pubblica dalla subnet che contiene la risorsa di calcolo. Questa comunicazione viene usata per la pianificazione delle attività e l'accesso ad archiviazione di Azure.

È possibile eseguire questa operazione in due modi:

* Usare una [rete virtuale NAT](../virtual-network/nat-overview.md). Un gateway NAT fornisce la connettività Internet in uscita per una o più subnet nella rete virtuale. Per informazioni, vedere [progettazione di reti virtuali con risorse del gateway NAT](../virtual-network/nat-gateway-resource.md).

* Aggiungere [route definite dall'utente (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) alla subnet che contiene la risorsa di calcolo. Stabilire una route definita dall'utente per ogni indirizzo IP usato dal servizio Azure Batch nell'area in cui si trovano le risorse. Queste route consentono al servizio Batch di comunicare con i nodi di calcolo per la pianificazione delle attività. Aggiungere anche l'indirizzo IP per il servizio Azure Machine Learning in cui si trovano le risorse, poiché è necessario per l'accesso alle istanze di calcolo. Per ottenere un elenco di indirizzi IP del servizio Batch e del servizio Azure Machine Learning, usare uno dei metodi seguenti:

    * Scaricare [gli intervalli IP e i tag del servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) e cercare `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` nel file, dove `<region>` è l'area di Azure.

    * Usare l['interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) per scaricare le informazioni. L'esempio seguente scarica le informazioni relative all'indirizzo IP e filtra le informazioni per l'area Stati Uniti orientali 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > Se si usano le aree Stati Uniti-Virginia, Stati Uniti-Arizona o Cina-Est-2, questi comandi non restituiscono indirizzi IP. Usare invece uno dei collegamenti seguenti per scaricare un elenco di indirizzi IP:
        >
        > * [Intervalli IP di Azure e tag dei servizi per Azure per enti pubblici](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Intervalli IP di Azure e tag dei servizi per Azure Cina](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Quando si aggiungono le route definite dall'utente, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare __Tipo hop successivo__ su __Internet__. La figura seguente illustra un esempio di route definita dall'utente nel portale di Azure:

    ![Esempio di route definita dall'utente per un prefisso di indirizzo](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > Gli indirizzi IP possono cambiare nel tempo.

    Oltre ai UdR definiti, il traffico in uscita verso archiviazione di Azure deve essere consentito tramite l'appliance di rete locale. In particolare, gli URL per questo traffico si trovano nei formati seguenti: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` . 

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Accedere ai dati in un notebook dell'istanza di calcolo

Se si usano notebook in un'istanza di calcolo di Azure, è necessario assicurarsi che il notebook sia in esecuzione in una risorsa di calcolo dietro la stessa rete virtuale e la stessa subnet dei dati. 

È necessario configurare l'istanza di calcolo in modo che si trovi nella stessa rete virtuale durante la creazione in **Impostazioni avanzate**  >  **Configura rete virtuale**. Non è possibile aggiungere un'istanza di calcolo esistente a una rete virtuale.

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

Questa sezione illustra come usare una macchina virtuale o un cluster Azure HDInsight in una rete virtuale con l'area di lavoro.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Creare la macchina virtuale o il cluster HDInsight

Creare una macchina virtuale o un cluster HDInsight usando il portale di Azure o l'interfaccia della riga di comando di Azure e inserire il cluster in una rete virtuale di Azure. Per altre informazioni, vedere gli articoli seguenti:
* [Creare e gestire reti virtuali di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

* [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

### <a name="configure-network-ports"></a>Configurare le porte di rete 

Consentire a Azure Machine Learning di comunicare con la porta SSH nella macchina virtuale o nel cluster, configurare una voce di origine per il gruppo di sicurezza di rete. La porta SSH è in genere la porta 22. Per consentire il traffico da questa origine, eseguire queste operazioni:

1. Nell'elenco a discesa __Origine__ selezionare __Tag del servizio__.

1. Nell'elenco a discesa __Tag del servizio di origine__ selezionare __AzureMachineLearning__.

    ![Regole in ingresso per la sperimentazione in una macchina virtuale o un cluster HDInsight in una rete virtuale](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Nell'elenco a discesa __Intervalli di porte di origine__ selezionare __*__ .

1. Nell'elenco a discesa __Destinazione__ selezionare __Qualsiasi__.

1. Nell'elenco a discesa __Intervalli di porte di destinazione__ selezionare __22__.

1. In __Protocollo__ selezionare __Qualsiasi__.

1. In __Azione__ selezionare __Consenti__.

Mantenere le regole in ingresso per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

Se non si vogliono usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, vedere la sezione [Limitare la connettività in uscita dalla rete virtuale](#limiting-outbound-from-vnet).

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Connessione della macchina virtuale o del cluster HDInsight

Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è la terza parte di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica di rete virtuale](how-to-network-security-overview.md)
* [Parte 2: proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Parte 4: proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)
* [Parte 5: abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)