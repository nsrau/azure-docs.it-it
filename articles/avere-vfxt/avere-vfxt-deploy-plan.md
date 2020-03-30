---
title: Pianificare il sistema Avere vFXT - Azure
description: Informazioni sulle attività di pianificazione da eseguire prima della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: cd0c74c8aa40b3e96716ef37aa27b08b5f6aece1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547541"
---
# <a name="plan-your-avere-vfxt-system"></a>Pianificare il sistema Avere vFXT

Questo articolo illustra come pianificare un nuovo cluster Avere vFXT per Azure posizionato e dimensionato in modo appropriato in base alle proprie esigenze.

Prima di passare ad Azure Marketplace o creare macchine virtuali, considerare i dettagli seguenti:Before going to the Azure Marketplace or creating any VMs, consider these details:

* In che modo il cluster interagirà con altre risorse di Azure?
* Dove devono trovarsi gli elementi del cluster nelle reti private e nelle subnet?
* Che tipo di archiviazione back-end verrà utilizzata e come verrà vi accederà il cluster?
* Quanto devono essere potenti i nodi del cluster per supportare il flusso di lavoro?

Per altre informazioni, continuare la lettura.

## <a name="learn-the-components-of-the-system"></a>Imparare i componenti del sistema

Può essere utile comprendere i componenti del sistema Avere vFXT per Azure quando si inizia la pianificazione.

* Nodi del cluster: il cluster è costituito da tre o più macchine virtuali configurate come nodi del cluster. Più nodi offrono al sistema una maggiore velocità effettiva e una cache più grande.

* Cache: la capacità della cache viene suddivisa equamente tra i nodi del cluster. Impostare la dimensione della cache per nodo quando si crea il cluster; le dimensioni dei nodi vengono aggiunte per diventare la dimensione totale della cache.

* Controller cluster: il controller del cluster è una macchina virtuale aggiuntiva situata all'interno della stessa subnet dei nodi del cluster. Il controller è necessario per creare il cluster e per le attività di gestione in corso.

* Archiviazione back-end: i dati che si desidera memorizzare nella cache vengono archiviati a lungo termine in un sistema di archiviazione hardware o in un contenitore BLOB di Azure.Back-end storage - The data that you want to have cached to cached term in a hardware storage system or an Azure Blob container. È possibile aggiungere spazio di archiviazione dopo aver creato il cluster Avere vFXT per Azure oppure, se si usa l'archiviazione BLOB, è possibile aggiungere e configurare il contenitore durante la creazione del cluster.

* Client: i computer client che utilizzano i file memorizzati nella cache si connettono al cluster utilizzando un percorso di file virtuale anziché accedere direttamente ai sistemi di archiviazione. (Per saperne di più in [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Sottoscrizione, gruppo di risorse e infrastruttura di reteSubscription, resource group, and network infrastructure

Valutare la posizione degli elementi della distribuzione di Avere vFXT per Azure. Il diagramma seguente mostra una disposizione possibile dei componenti di Avere vFXT per Azure:

![Diagramma che mostra il controller del cluster e le macchine virtuali del cluster all'interno di una subnet. Intorno al limite della subnet c'è un limite di rete virtuale. All'interno della rete virtuale è presente un esagono che rappresenta l'endpoint di servizio di archiviazione ed è connesso con una freccia tratteggiata all'archiviazione BLOB al di fuori della rete virtuale.](media/avere-vfxt-components-option.png)

Seguire queste linee guida quando si pianifica l'infrastruttura di rete del cluster Avere vFXT:Follow these guidelines when planning your Avere vFXT cluster's network infrastructure:

* Creare una nuova sottoscrizione per ogni distribuzione di Avere vFXT per Azure.Create a new subscription for each Avere vFXT for Azure deployment. Gestire tutti i componenti in questa sottoscrizione.

  I vantaggi dell'utilizzo di una nuova sottoscrizione per ogni distribuzione includono:Benefits of using a new subscription for each deployment include:
  * Verifica semplificata dei costi: è possibile visualizzare e controllare tutti i costi relativi alle risorse, all'infrastruttura e ai calcoli in una sottoscrizione.
  * Pulizia più semplice: è possibile rimuovere l'intera sottoscrizione al termine del progetto.
  * Comodo partizionamento delle quote di risorse: isolare i client E vFXT e il cluster in un'unica sottoscrizione per proteggere altri carichi di lavoro critici da possibili limitazioni delle risorse. Questa separazione impedisce i conflitti quando si crea un numero elevato di client per un flusso di lavoro di elaborazione ad alte prestazioni.

* Posizionare i sistemi di calcolo client vicino al cluster vFXT. L'archiviazione back-end può avere una posizione più remota.  

* Individuare il cluster vFXT e la macchina virtuale del controller del cluster insieme, in particolare devono essere:Locate the vFXT cluster and the cluster controller VM together - specifically, they should be:

  * Nella stessa rete virtuale
  * Nello stesso gruppo di risorse
  * Uso dello stesso account di archiviazioneUsing the same storage account
  
  Il modello di creazione cluster gestisce questa configurazione per la maggior parte delle situazioni.

* Il cluster deve trovarsi nella propria subnet per evitare conflitti di indirizzi IP con i client o altre risorse di calcolo.

* Usare il modello di creazione del cluster per creare la maggior parte delle risorse dell'infrastruttura necessarie per il cluster, inclusi gruppi di risorse, reti virtuali, subnet e account di archiviazione.

  Se si desidera utilizzare risorse già esistenti, assicurarsi che soddisfino i requisiti di questa tabella.

  | Risorsa | Utilizzare esistente? | Requisiti |
  |----------|-----------|----------|
  | Resource group | Sì, se vuoto | Deve essere vuoto|
  | Account di archiviazione | **Sì** se si connette un contenitore BLOB esistente dopo la creazione del clusterYes if connecting an existing Blob container after cluster creation <br/>  **No** se si crea un nuovo contenitore BLOB durante la creazione del cluster | Il contenitore BLOB esistente deve essere vuotoExisting Blob container must be empty <br/> &nbsp; |
  | Rete virtuale | Sì | Deve includere un endpoint del servizio di archiviazione se si crea un nuovo contenitore BLOB di AzureMust include a storage service endpoint if creating a new Azure Blob container |
  | Subnet | Sì | Non può contenere altre risorse |

## <a name="ip-address-requirements"></a>Requisiti degli indirizzi IP

Assicurarsi che la subnet del cluster disponga di un intervallo di indirizzi IP sufficiente per supportare il cluster.

Il cluster Avere vFXT usa gli indirizzi IP seguenti:

* Un indirizzo IP di gestione del cluster. Questo indirizzo può spostarsi da un nodo all'altro del cluster in base alle esigenze in modo che sia sempre disponibile. Utilizzare questo indirizzo per connettersi allo strumento di configurazione del Pannello di controllo Avere.
* Per ogni nodo del cluster:
  * Almeno un indirizzo IP esposto al client. Tutti gli indirizzi rivolti al client sono gestiti dal *server virtuale*del cluster , che consente di spostare gli indirizzi IP tra i nodi in base alle esigenze.
  * Un indirizzo IP per le comunicazioni del cluster
  * Un indirizzo IP dell'istanza (assegnato alla macchina virtuale)

Se si usa l'archiviazione BLOB di Azure, potrebbe anche richiedere indirizzi IP dalla rete virtuale del cluster:If you use Azure Blob storage, it might also require IP addresses from your cluster's virtual network:  

* Un account di archiviazione BLOB di Azure richiede almeno cinque indirizzi IP. Tenere presente questo requisito se si trova l'archiviazione BLOB nella stessa rete virtuale del cluster.
* Se si usa l'archiviazione BLOB di Azure esterna alla rete virtuale del cluster, creare un endpoint del servizio di archiviazione all'interno della rete virtuale. L'endpoint non utilizza un indirizzo IP.

È possibile posizionare le risorse di rete e l'archiviazione BLOB (se in uso) in gruppi di risorse diversi rispetto al cluster.

## <a name="vfxt-node-size"></a>Dimensione nodo vFXT

Le macchine virtuali che fungono da nodi del cluster determinano la velocità effettiva delle richieste e la capacità di archiviazione della cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Ogni nodo vFXT sarà identico. Ciò significa che se si crea un cluster a tre nodi, si avranno tre macchine virtuali con tipo e dimensioni uguali.

| Tipo di istanza | vCPU | Memoria  | Archiviazione SSD locale  | Numero massimo di dischi dati | Velocità effettiva del disco senza memorizzazione nella cache | Scheda di rete (conteggio) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 operazioni di I/O al secondo <br/> 768 MBps | 16.000 MBps (8)  |

La cache del disco per ogni nodo è configurabile e può andare da 1000 GB a 8000 GB. 4 TB per nodo è la dimensione consigliata della cache per Standard_E32s_v3 nodi.

Per altre informazioni su queste macchine virtuali, leggere la documentazione di Microsoft Azure: [Dimensioni delle macchine virtuali ottimizzate per](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) la memoriaFor additional information about these VMs, read the Microsoft Azure documentation: Memory optimized virtual machine sizes

## <a name="account-quota"></a>Quota account

Assicurarsi che la sottoscrizione abbia la capacità necessaria per eseguire il cluster Avere vFXT, nonché i sistemi client o di calcolo in uso. Per informazioni dettagliate, vedere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Archivio dati back-end

I sistemi di archiviazione back-end forniscono i file alla cache del cluster e ricevono anche i dati modificati dalla cache. Decidere se il working set verrà archiviato a lungo termine in un nuovo contenitore BLOB o in un sistema di archiviazione esistente (cloud o hardware). Questi sistemi di archiviazione back-end sono *denominati filer di base*.

### <a name="hardware-core-filers"></a>Filer di base hardware

Aggiungere sistemi di archiviazione hardware al cluster vFXT dopo aver creato il cluster. È possibile utilizzare una varietà di sistemi hardware più diffusi, inclusi i sistemi locali, purché il sistema di archiviazione sia raggiungibile dalla subnet del cluster.

Vedere [Configurare l'archiviazione](avere-vfxt-add-storage.md) per istruzioni dettagliate sull'aggiunta di un sistema di archiviazione esistente al cluster Avere vFXT.

### <a name="cloud-core-filers"></a>Filer di base cloud

Il sistema Avere vFXT per Azure può usare contenitori BLOB vuoti per l'archiviazione back-end. I contenitori devono essere vuoti quando vengono aggiunti al cluster: il sistema vFXT deve essere in grado di gestire il relativo archivio oggetti senza dover conservare i dati esistenti.

> [!TIP]
> Se si vuole usare l'archiviazione BLOB di Azure per il back-end, creare un nuovo contenitore come parte della creazione del cluster vFXT. Il modello di creazione cluster può creare e configurare un nuovo contenitore BLOB in modo che sia pronto per l'uso non appena il cluster è disponibile. L'aggiunta di un contenitore in un secondo momento è più complessa.
>
> Per informazioni dettagliate, vedere [Creare Avere vFXT per Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure).

Dopo aver aggiunto il contenitore di archiviazione BLOB vuoto come filer principale, è possibile copiarvi i dati tramite il cluster. Utilizzare un meccanismo di copia parallela e multithread. Vedere [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md) per informazioni su come copiare i dati nel nuovo contenitore del cluster in modo efficiente usando computer client e la cache Avere vFXT.

## <a name="cluster-access"></a>Accesso al cluster

Il cluster Avere vFXT per Azure si trova in una subnet privata e non ha un indirizzo IP pubblico. È necessario disporre di un modo per accedere alla subnet privata per l'amministrazione del cluster e le connessioni client.

Le opzioni di accesso includono:

* Host di collegamento: assegnare un indirizzo IP pubblico a una macchina virtuale separata all'interno della rete privata e usarlo per creare un tunnel SSL per i nodi del cluster.

  > [!TIP]
  > Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento. Per altre informazioni, vedere [Controller del cluster come host di collegamento](#cluster-controller-as-jump-host).

* Rete privata virtuale (VPN) - Configurare una VPN da punto a sito o da sito a sito tra la rete privata in Azure e le reti aziendali.

* Azure ExpressRoute: configurare una connessione privata tramite un partner ExpressRoute.

Per informazioni dettagliate su queste opzioni, vedere la [documentazione di Rete virtuale di Azure sulle comunicazioni Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controller del cluster come host di collegamento

Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento per contattare il cluster Avere vFXT dall'esterno della subnet privata. Tuttavia, poiché il controller ha privilegi di accesso per la modifica dei nodi del cluster, si presenta un lieve rischio per la sicurezza.

Per migliorare la sicurezza per un controller con un indirizzo IP pubblico, lo script di distribuzione crea automaticamente un gruppo di sicurezza di rete che limita l'accesso in ingresso solo alla porta 22.To improve security for a controller with a public IP address, the deployment script automatically creates a network security group that restricts inbound access to port 22 only. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

Quando si crea il cluster, è possibile scegliere se creare un indirizzo IP pubblico nel controller del cluster.

* Se si crea una **nuova rete virtuale** o una nuova **subnet,** al controller del cluster verrà assegnato un indirizzo IP **pubblico.**
* Se si seleziona una rete virtuale e una subnet esistenti, il controller del cluster diverrà solo indirizzi IP **privati.**

## <a name="vm-access-roles"></a>Ruoli di accesso ALLE macchine virtualiVM access roles

Azure usa il controllo degli [accessi in base](../role-based-access-control/index.yml) al ruolo per autorizzare le macchine virtuali del cluster a eseguire determinate attività. Ad esempio, il controller del cluster deve disporre dell'autorizzazione per creare e configurare le macchine virtuali del nodo del cluster. I nodi del cluster devono essere in grado di assegnare o riassegnare gli indirizzi IP ad altri nodi del cluster.

Per le macchine virtuali Avere vFXT vengono usati due ruoli predefiniti di Azure:Two built-in Azure roles are used for the Avere vFXT virtual machines:

* Il controller del cluster utilizza il ruolo predefinito [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* I nodi del cluster utilizzano il ruolo predefinito [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Se è necessario personalizzare i ruoli di accesso per i componenti di Avere vFXT, è necessario definire un ruolo personalizzato e quindi assegnarlo alle macchine virtuali al momento della creazione. Non è possibile usare il modello di distribuzione in Azure Marketplace.You cannot use the deployment template in the Azure Marketplace. Consultare il Servizio Supporto Tecnico Clienti Microsoft aprendo un ticket nel portale di Azure come descritto in [Ottenere assistenza per il sistema.](avere-vfxt-open-ticket.md)

## <a name="next-steps"></a>Passaggi successivi

[La panoramica](avere-vfxt-deploy-overview.md) della distribuzione offre la visualizzazione generale dei passaggi necessari per creare un sistema Avere vFXT per Azure e prepararlo a gestire i dati.
