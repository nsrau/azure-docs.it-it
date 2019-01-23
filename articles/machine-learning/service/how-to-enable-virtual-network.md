---
title: Eseguire esperimenti e inferenze in una rete virtuale
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire in modo sicuro esperimenti e inferenze di Machine Learning in una rete virtuale di Azure. Questo articolo illustra come creare le destinazioni di calcolo usate per eseguire il training dei modelli e come elaborare un'inferenza in una rete virtuale di Azure. Descrive anche i requisiti per le reti virtuali protette, ad esempio le porte in ingresso e in uscita obbligatorie.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 1890f830f9b2a4b108328de056636335fd3746e3
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248603"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Eseguire esperimenti e inferenze in modo sicuro in una rete virtuale di Azure

In questo articolo viene illustrato come eseguire gli esperimenti e le inferenze in una rete virtuale. Una rete virtuale funge da limite di sicurezza, isolando le risorse di Azure dalla rete Internet pubblica. È anche possibile aggiungere una rete virtuale di Azure alla rete locale. Consente di eseguire il training dei modelli in modo sicuro e di accedere ai modelli distribuiti per l'inferenza.

Il servizio Azure Machine Learning si basa sugli altri servizi di Azure, ad esempio per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. È ad esempio possibile usare una Data Science Virtual Machine per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes. Per altre informazioni sulle reti virtuali, vedere il documento [Panoramica delle reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="storage-account-for-your-workspace"></a>Account di archiviazione per l'area di lavoro

Quando si crea un'area di lavoro per il servizio Azure Machine Learning, è necessario un account di archiviazione di Azure. Non attivare le regole del firewall per questo account di archiviazione. Il servizio Azure Machine Learning richiede accesso illimitato all'account di archiviazione.

Se non si è certi che queste impostazioni siano state modificate o meno, vedere la sezione __Modificare la regola predefinita di accesso alla rete__ del documento [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) e usare i passaggi per _consentire l'accesso_ da __Tutte le reti__.

## <a name="use-machine-learning-compute"></a>Usare l'ambiente di calcolo di Machine Learning

Per usare l'ambiente di calcolo di Machine Learning in una rete virtuale, usare le informazioni seguenti sui requisiti di rete:

- La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro del servizio Azure Machine Learning.

- La subnet specificata per il cluster dell'ambiente di calcolo di Machine Learning deve avere indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione per il cluster. Se la subnet non ha abbastanza indirizzi IP non assegnati, il cluster verrà parzialmente allocato.

- Se si prevede di proteggere la rete virtuale limitando il traffico, è necessario lasciare aperte alcune porte per il servizio dell'ambiente di calcolo di Machine Learning. Per altre informazioni, vedere la sezione [Porte richieste](#mlcports).

- Controllare se i criteri di sicurezza o i blocchi nel gruppo di risorse o nella sottoscrizione della rete virtuale limitano le autorizzazioni per gestire la rete virtuale.

- Se si inseriranno più cluster dell'ambiente di calcolo di Machine Learning in una sola rete virtuale, potrebbe essere necessario richiedere un aumento di quota per una o più risorse.

    L'ambiente di calcolo di Machine Learning alloca automaticamente le risorse di rete aggiuntive nel gruppo di risorse contenente la rete virtuale. Per ogni cluster dell'ambiente di calcolo di Machine Learning, il servizio Azure Machine Learning alloca le risorse seguenti: 

    - Un gruppo di sicurezza di rete (NSG)

    - Un indirizzo IP pubblico

    - Un bilanciamento del carico

    Queste risorse sono limitate in base alle [quote delle risorse](https://docs.microsoft.com/azure/azure-subscription-service-limits) della sottoscrizione. 

### <a id="mlcports"></a> Porte richieste

L'ambiente di calcolo di Machine Learning attualmente usa il servizio Azure Batch per effettuare il provisioning delle machine virtuali nella rete virtuale specificata. La subnet deve consentire la comunicazione in ingresso dal servizio Batch. Questa comunicazione viene usata per pianificare le esecuzioni nei nodi dell'ambiente di calcolo di Machine Learning e per comunicare con Archiviazione di Azure e altre risorse. Batch aggiunge gruppi di sicurezza di rete al livello di interfacce di rete collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

- Traffico TCP in ingresso sulle porte 29876 e 29877 da indirizzi IP del ruolo del servizio Batch. 
 
- Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto.
 
- Traffico in uscita su qualsiasi porta verso la rete virtuale.

- Traffico in uscita su qualsiasi porta verso Internet.

Prestare attenzione se si modificano o aggiungono regole in ingresso/uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio dell'ambiente di calcolo di Machine Learning imposta i nodi di calcolo come non utilizzabili.

Non occorre specificare i gruppi di sicurezza di rete a livello di subnet perché Batch configura gruppi di sicurezza di rete propri. Se tuttavia alla subnet specificata sono associati gruppi di sicurezza di rete (NSG) e/o un firewall, configurare le regole di sicurezza in ingresso e in uscita, come accennato in precedenza. Gli screenshot seguenti illustrano la configurazione delle regole nel portale di Azure:

![Screenshot delle regole dei gruppi di sicurezza di rete in ingresso per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Screenshot delle regole dei gruppi di sicurezza di rete in uscita per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Creare l'ambiente di calcolo di Machine Learning in una rete virtuale

Per creare un cluster dell'ambiente di calcolo di Machine Learning con il **portale di Azure**, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro del servizio Azure Machine Learning.

1. Nella sezione __Applicazione__ selezionare __Ambiente di calcolo__. Selezionare quindi __Aggiungi ambiente di calcolo__. 

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, usare queste opzioni:

    - __Configurazione di rete__: Selezionare __Advanced__ (Avanzate).

    - __Gruppo di risorse__: selezionare il gruppo di risorse che contiene la rete virtuale.

    - __Rete virtuale__: selezionare la rete virtuale che contiene la subnet.

    - __Subnet__: selezionare la subnet da usare.

    ![Screenshot che illustra le impostazioni della rete virtuale per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

È anche possibile creare un cluster dell'ambiente di calcolo di Machine Learning usando **Azure Machine Learning SDK**. Il codice seguente crea un nuovo cluster dell'ambiente di calcolo di Machine Learning nella subnet `default` di una rete virtuale denominata `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Al termine del processo di creazione, è possibile eseguire il training del modello usando il cluster. Per altre informazioni, vedere il documento [Selezionare e usare una destinazione di calcolo per il training](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight"></a>Usare una macchina virtuale o HDInsight

Per usare una macchina virtuale o un cluster HDInsight in una rete virtuale con l'area di lavoro, seguire questa procedura:

> [!IMPORTANT]
> Il servizio Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu.

1. Creare una macchina virtuale o un cluster HDInsight usando il portale di Azure, l'interfaccia della riga di comando di Azure e così via e inserirlo in una rete virtuale di Azure. Per altre informazioni, vedere i documenti seguenti:
    * [Creare e gestire reti virtuali di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estendere HDInsight usando le reti virtuali di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Per consentire al servizio Azure Machine Learning di comunicare con la porta SSH nella macchina virtuale o nel cluster, è necessario configurare una voce di origine per il gruppo di sicurezza di rete. La porta SSH è in genere la porta 22. Per consentire il traffico da questa origine, usare le informazioni seguenti:

    * __Origine__: Selezionare __Service Tag__ (Tag del servizio)

    * __Tag del servizio di origine__: selezionare __AzureMachineLearning__

    * __Intervalli di porte di origine__: selezionare __*__

    * __Destinazione__: selezionare __Qualsiasi__

    * __Intervalli di porte di destinazione__: selezionare __22__

    * __Protocollo__: selezionare __Qualsiasi__

    * __Azione__: selezionare __Consenti__

   ![Screenshot delle regole in ingresso per la sperimentazione sulla macchina virtuale o HDInsight in una rete virtuale](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere la sezione relativa alle regole di sicurezza predefinite della documentazione sui [gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
    
1. Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere il documento [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service-aks"></a>Usare il servizio Azure Kubernetes

> [!IMPORTANT]
> Controllare i prerequisiti e pianificare l'indirizzamento IP per il cluster prima di eseguire i passaggi elencati sotto. È possibile vedere [Configurare funzionalità di rete avanzate nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/configure-advanced-networking)
> 
> Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere la sezione relativa alle regole di sicurezza predefinite della documentazione sui [gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Il servizio Azure Kubernetes e la rete virtuale di Azure devono essere nella stessa area.

Per aggiungere il servizio Azure Kubernetes in una rete virtuale, seguire questa procedura nel __portale di Azure__:

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro del servizio Azure Machine Learning.

1. Nella sezione __Applicazione__ selezionare __Ambiente di calcolo__. Selezionare quindi __Aggiungi ambiente di calcolo__. 

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, usare queste opzioni:

    - __Configurazione di rete__: Selezionare __Advanced__ (Avanzate).

    - __Gruppo di risorse__: selezionare il gruppo di risorse che contiene la rete virtuale.

    - __Rete virtuale__: selezionare la rete virtuale che contiene la subnet.

    - __Subnet__: Selezionare la subnet.

    - __Intervallo di indirizzi del servizio Kubernetes__: selezionare l'intervallo di indirizzi del servizio Kubernetes. Questo intervallo di indirizzi usa un indirizzo IP in notazione CIDR per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi a nessun intervallo IP della subnet. Ad esempio:  10.0.0.0/16.

    - __Indirizzo IP del servizio DNS Kubernetes__: selezionare l'indirizzo IP del servizio DNS di Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes. Ad esempio:  10.0.0.10.

    - __Indirizzo del bridge Docker__: selezionare l'indirizzo del bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in nessun intervallo IP della subnet o nell'intervallo di indirizzi del servizio Kubernetes. Ad esempio:  172.17.0.1/16

   ![Servizio Azure Machine Learning: impostazioni della rete virtuale dell'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Se è già presente un cluster del servizio Azure Kubernetes in una rete virtuale, è possibile collegarlo all'area di lavoro. Per altre informazioni, vedere l'articolo su [come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-to-aks.md).

È anche possibile usare **Azure Machine Learning SDK** per aggiungere il servizio Azure Kubernetes in una rete virtuale. Il codice seguente crea un nuovo servizio Azure Kubernetes nella subnet `default` di una rete virtuale denominata `mynetwork`:

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Al termine del processo di creazione, è possibile eseguire l'inferenza in un cluster del servizio Azure Kubernetes dietro una rete virtuale. Per altre informazioni, vedere l'articolo su [come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare ambienti di training](how-to-set-up-training-targets.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Proteggere i modelli distribuiti con SSL](how-to-secure-web-service.md)