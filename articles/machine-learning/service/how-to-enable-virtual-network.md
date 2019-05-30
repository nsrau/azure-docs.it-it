---
title: Eseguire esperimenti e inferenze in una rete virtuale
titleSuffix: Azure Machine Learning service
description: Eseguire in modo sicuro esperimenti e inferenze di Machine Learning in una rete virtuale di Azure. Informazioni su come creare destinazioni di calcolo per il training dei modelli e su come eseguire interferenze in una rete virtuale. Informazioni sui requisiti per le reti virtuali sicure, ad esempio per quanto riguarda le porte in ingresso e in uscita.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 48c59ddc1e203030bd967911d536930cb94761d3
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356186"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>In modo sicuro eseguire esperimenti e inferenza all'interno di una rete virtuale di Azure

In questo articolo descrive come eseguire esperimenti e inferenza in una rete virtuale. Una rete virtuale funge da limite di sicurezza, isolando le risorse di Azure dalla rete Internet pubblica. È anche possibile aggiungere una rete virtuale di Azure alla rete locale. Consente di eseguire il training dei modelli in modo sicuro e accedere ai tuoi modelli distribuiti per l'inferenza. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione.

Il servizio Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. È ad esempio possibile usare un ambiente Microsoft Data Science Virtual Machine per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes. Per altre informazioni sulle reti virtuali, vedere [Panoramica di Rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Prerequisiti

Questo documento si presuppone che abbia familiarità con le reti virtuali di Azure e IP di rete in generale. Questo documento presuppone anche che sia stato creato una rete virtuale e una subnet da usare con le risorse di calcolo. Se non si ha familiarità con le reti virtuali di Azure, leggere gli articoli seguenti per informazioni sul servizio:

* [Indirizzamento IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Avvio rapido: Creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrare il traffico di rete](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Account di archiviazione per l'area di lavoro

> [!IMPORTANT]
> È possibile inserire l'account di archiviazione collegato all'area di lavoro del servizio Azure Machine Learning dietro la rete virtuale solo durante l'esecuzione di sperimentazione. Inferenza richiede l'accesso illimitato all'account di archiviazione. Se non si è certi che queste impostazioni siano state modificate, vedere la sezione __Modificare la regola predefinita di accesso alla rete__ in [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security). Usare la procedura per consentire l'accesso da tutte le reti durante l'inferenza o di un modello di punteggio.

Per usare le funzionalità di sperimentazione di Azure Machine Learning con archiviazione di Azure dietro una rete virtuale, attenersi alla procedura seguente:

1. Creare un calcolo di sperimentazione, ad esempio. Machine Learning calcolo dietro una rete virtuale o collegare un calcolo di sperimentazione all'area di lavoro, ad esempio. Cluster HDInsight o macchina virtuale. Per altre informazioni, vedere [calcolo di usare Machine Learning](#use-machine-learning-compute) e [usare una macchina virtuale o un cluster HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) sezioni in questo documento
2. Passare alla risorsa di archiviazione collegata all'area di lavoro. ![Immagine del portale di Azure con archiviazione di Azure collegata all'area di lavoro del servizio Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Nella pagina di archiviazione di Azure, selezionare __firewall e reti virtuali__. ![Immagine del portale di Azure che mostra i firewall e virtual networks sezione nella pagina archiviazione di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Nel __firewall e reti virtuali__ pagina selezionare le voci seguenti:
    - Selezionare __Reti selezionate__.
    - Sotto __reti virtuali__, selezionare __Aggiungi rete virtuale esistente__ per aggiungere la rete virtuale in cui si trova le risorse di calcolo di sperimentazione. (Vedere il passaggio 1).
    - Selezionare __Consenti attendibili i servizi Microsoft per accedere a questo account di archiviazione__.
![Immagine del portale di Azure che mostra i firewall e virtual networks pagina in archiviazione di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Quando si esegue l'esperimento, nel codice sperimentazione, modificare la configurazione di esecuzione per usare l'archiviazione blob:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Insieme di credenziali delle chiavi per l'area di lavoro
Istanza di Key Vault associato con l'area di lavoro viene usato dal servizio Azure Machine Learning per archiviare le credenziali di vari tipi:
* La stringa di connessione di account di archiviazione associato
* Password per le istanze di Azure Container Repository
* Stringhe di connessione ai dati punti vendita. 

Per l'uso di sperimentazione di Azure Machine Learning funzionalità con Key Vault protetto da una rete virtuale attenersi alla procedura seguente:
1. Passare all'insieme di credenziali chiave associate all'area di lavoro. ![Immagine del portale di Azure con Key Vault associato con l'area di lavoro del servizio di Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Nella pagina di Key Vault, selezionare __firewall e reti virtuali__ sezione. ![Immagine del portale di Azure che mostra i firewall e virtual networks sezione nella pagina di Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Nel __firewall e reti virtuali__ pagina selezionare le voci seguenti:
    - Selezionare __Reti selezionate__.
    - Sotto il __reti virtuali__, selezionare __aggiungere le reti virtuali esistenti__ per aggiungere la rete virtuale in cui si trova le risorse di calcolo di sperimentazione.
    - Selezionare __Consenti attendibili il servizio Microsoft per ignorare il firewall__.
![Immagine del portale di Azure che mostra i firewall e virtual networks pagina in Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Usare l'ambiente di calcolo di Machine Learning

Per usare l'ambiente di calcolo di Azure Machine Learning in una rete virtuale, fare riferimento alle informazioni seguenti sui requisiti di rete:

- La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro del servizio Azure Machine Learning.

- La subnet specificata per il cluster dell'ambiente di calcolo di Machine Learning deve avere indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione per il cluster. Se la subnet non ha abbastanza indirizzi IP non assegnati, il cluster verrà parzialmente allocato.

- Se si prevede di proteggere la rete virtuale limitando il traffico, è necessario lasciare aperte alcune porte per il servizio dell'ambiente di calcolo di Machine Learning. Per altre informazioni, vedere la sezione [Porte richieste](#mlcports).

- Controllare se i criteri di sicurezza o i blocchi nel gruppo di risorse o nella sottoscrizione della rete virtuale limitano le autorizzazioni per gestire la rete virtuale.

- Se si prevede di inserire più cluster dell'ambiente di calcolo di Machine Learning in una rete virtuale, potrebbe essere necessario richiedere un aumento di quota per una o più risorse.

    L'ambiente di calcolo di Machine Learning alloca automaticamente le risorse di rete aggiuntive nel gruppo di risorse contenente la rete virtuale. Per ogni cluster dell'ambiente di calcolo di Machine Learning, il servizio Azure Machine Learning alloca le risorse seguenti:

    - Un gruppo di sicurezza di rete (NSG)

    - Un indirizzo IP pubblico

    - Un bilanciamento del carico

  Queste risorse sono limitate in base alle [quote delle risorse](https://docs.microsoft.com/azure/azure-subscription-service-limits) della sottoscrizione.

### <a id="mlcports"></a> Porte richieste

L'ambiente di calcolo di Machine Learning attualmente usa il servizio Azure Batch per effettuare il provisioning delle machine virtuali nella rete virtuale specificata. La subnet deve consentire la comunicazione in ingresso dal servizio Batch. Questa comunicazione viene usata per pianificare le esecuzioni nei nodi dell'ambiente di calcolo di Machine Learning e per comunicare con Archiviazione di Azure e altre risorse. Batch aggiunge gruppi di sicurezza di rete a livello di interfacce di rete collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

- Connessioni in entrata il traffico TCP sulle porte 29876 e 29877 da un __Tag del servizio__ dei __BatchNodeManagement__.

    ![Immagine del portale di Azure che illustra una regola in ingresso usando il tag di servizio BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (facoltativo) Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto. Questa porta è necessario solo se si desidera connettersi tramite SSH su indirizzo IP pubblico.
 
- Traffico in uscita su qualsiasi porta verso la rete virtuale.

- Traffico in uscita su qualsiasi porta verso Internet. 

Prestare attenzione se si modificano o aggiungono regole in ingresso/uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio dell'ambiente di calcolo di Machine Learning imposta i nodi di calcolo come non utilizzabili.

Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet perché Batch configura gruppi di sicurezza di rete propri. Se tuttavia alla subnet specificata sono associati gruppi di sicurezza di rete (NSG) e/o un firewall, configurare le regole di sicurezza in ingresso e in uscita, come accennato in precedenza. 

Lo screenshot seguente mostra come la configurazione della regola NSG apparirà nel portale di Azure:

![Screenshot delle regole dei gruppi di sicurezza di rete in ingresso per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Screenshot delle regole dei gruppi di sicurezza di rete in uscita per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Limitazione della connettività in uscita dalla rete virtuale

Se non vuoi usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, attenersi alla procedura seguente:

- Negazione connessione internet in uscita usando le regole NSG 

- Limitare il traffico in uscita verso archiviazione di Azure (tramite __Tag del servizio__ dei __Storage.Region_Name__ es. Storage. eastus), registro contenitori di Azure (tramite __Tag del servizio__ dei __AzureContainerRegistry.Region_Name__ ex. AzureContainerRegistry.EastUS) e il servizio di Azure Machine Learning (tramite __Tag del servizio__ dei __AzureMachineLearning__)

Lo screenshot seguente mostra come la configurazione della regola NSG apparirà nel portale di Azure:

![Screenshot delle regole dei gruppi di sicurezza di rete in uscita per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Route definite dall'utente per il tunneling forzato

Se si usa il tunneling forzato con calcolo di Azure Machine Learning, è necessario aggiungere [route definite dall'utente (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) alla subnet contenente la risorsa di calcolo.

* Una route definita dall'utente per ogni indirizzo IP utilizzato dal servizio Azure Batch nell'area in cui sono presenti delle risorse. Queste route definite dall'utente consentono al servizio batch comunicare con nodi di calcolo per la pianificazione delle attività. Per ottenere un elenco degli indirizzi IP del servizio Batch, contattare il supporto di Azure.

* Il traffico in uscita ad archiviazione di Azure (in particolare, gli URL nel formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net`) non deve essere bloccato per l'appliance di rete locale.

Quando si aggiungono le route definite dall'utente, definire le route per ogni prefisso di indirizzo IP di Batch correlato e impostare __tipo hop successivo__ al __Internet__. L'immagine seguente mostra un esempio di questa route definita dall'utente nel portale di Azure:

![Esempio route definite dall'utente per un prefisso di indirizzo](./media/how-to-enable-virtual-network/user-defined-route.png)

Per altre informazioni, vedere la [creare un pool di Batch di Azure in una rete virtuale](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) articolo.

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Creare l'ambiente di calcolo di Machine Learning in una rete virtuale

Per creare un cluster dell'ambiente di calcolo di Machine Learning con il portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro del servizio Azure Machine Learning.

1. Nella sezione __Applicazione__ selezionare __Calcolo__. Selezionare quindi __Aggiungi ambiente di calcolo__. 

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, usare queste opzioni:

    - __Configurazione di rete__: Selezionare __Advanced__ (Avanzate).

    - __Gruppo di risorse__: selezionare il gruppo di risorse che contiene la rete virtuale.

    - __Rete virtuale__: selezionare la rete virtuale che contiene la subnet.

    - __Subnet__: selezionare la subnet da usare.

   ![Screenshot che illustra le impostazioni della rete virtuale per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Al termine del processo di creazione, è possibile eseguire il training del modello usando il cluster. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Usare una macchina virtuale o un cluster HDInsight

Per usare una macchina virtuale o un cluster Azure HDInsight in una rete virtuale con l'area di lavoro, seguire questa procedura:

> [!IMPORTANT]
> Il servizio Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu.

1. Creare una macchina virtuale o un cluster HDInsight usando il portale di Azure o l'interfaccia della riga di comando di Azure e inserirlo in una rete virtuale di Azure. Per altre informazioni, vedere i documenti seguenti:
    * [Creare e gestire reti virtuali di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Per consentire al servizio Azure Machine Learning di comunicare con la porta SSH nella macchina virtuale o nel cluster, è necessario configurare una voce di origine per il gruppo di sicurezza di rete. La porta SSH è in genere la porta 22. Per consentire il traffico da questa origine, usare le informazioni seguenti:

    * __Origine__: selezionare __Tag del servizio__.

    * __Tag del servizio di origine__: selezionare __AzureMachineLearning__.

    * __Intervalli di porte di origine__: Selezionare __*__ .

    * __Destinazione__: selezionare __Tutte__.

    * __Intervalli di porte di destinazione__: selezionare __22__.

    * __Protocollo__: selezionare __Tutti__.

    * __Azione__: selezionare __Consenti__.

   ![Screenshot delle regole in ingresso per la sperimentazione in una macchina virtuale o un cluster HDInsight in una rete virtuale](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se non si desidera usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, vedere [limitano la connettività in uscita dalla rete virtuale](#limiting-outbound-from-vnet)
    
1. Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Usare il servizio Azure Kubernetes

> [!IMPORTANT]
> Controllare i prerequisiti e pianificare l'indirizzamento IP per il cluster prima di eseguire i passaggi elencati sotto. Per altre informazioni, vedere [Configurare funzionalità di rete avanzate nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Il servizio Azure Kubernetes e la rete virtuale di Azure devono essere nella stessa area.

Per aggiungere il servizio Azure Kubernetes in una rete virtuale nell'area di lavoro, seguire questa procedura nel portale di Azure:

1. Gruppo NSG che assicurarsi che i controlli della rete virtuale è abilitata per l'uso del servizio di Azure Machine Learning la regola in ingresso __Tag del servizio__ di __AzureMachineLearning__

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro del servizio Azure Machine Learning.

1. Nella sezione __Applicazione__ selezionare __Calcolo__. Selezionare quindi __Aggiungi ambiente di calcolo__. 

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, usare queste opzioni:

    - __Configurazione di rete__: Selezionare __Advanced__ (Avanzate).

    - __Gruppo di risorse__: selezionare il gruppo di risorse che contiene la rete virtuale.

    - __Rete virtuale__: selezionare la rete virtuale che contiene la subnet.

    - __Subnet__: Selezionare la subnet.

    - __Intervallo di indirizzi del servizio Kubernetes__: selezionare l'intervallo di indirizzi del servizio Kubernetes. Questo intervallo di indirizzi usa un indirizzo IP in notazione CIDR per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi a nessun intervallo IP della subnet. Ad esempio: 10.0.0.0/16.

    - __Indirizzo IP del servizio DNS Kubernetes__: selezionare l'indirizzo IP del servizio DNS di Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes. Ad esempio: 10.0.0.10.

    - __Indirizzo del bridge Docker__: selezionare l'indirizzo del bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in nessun intervallo IP della subnet o nell'intervallo di indirizzi del servizio Kubernetes. Ad esempio:  172.17.0.1/16.

   ![Servizio Azure Machine Learning: impostazioni della rete virtuale dell'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Gruppo NSG che assicurarsi che i controlli che della rete virtuale è abilitata per l'endpoint di assegnazione dei punteggi in modo che possa essere chiamato all'esterno delle rete virtuale la regola in ingresso

    ![Come aggiungere un ambiente di calcolo nel servizio Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Se è già presente un cluster del servizio Azure Kubernetes in una rete virtuale, è possibile collegarlo all'area di lavoro. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-to-aks.md).

È anche possibile usare **Azure Machine Learning SDK** per aggiungere il servizio Azure Kubernetes in una rete virtuale. Il codice seguente crea una nuova istanza del servizio Azure Kubernetes nella subnet `default` di una rete virtuale denominata `mynetwork`:

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

Quando viene completato il processo di creazione, è possibile inferenza/punteggio in un cluster AKS dietro una rete virtuale. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare ambienti di training](how-to-set-up-training-targets.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Distribuire in modo sicuro modelli con SSL](how-to-secure-web-service.md)

