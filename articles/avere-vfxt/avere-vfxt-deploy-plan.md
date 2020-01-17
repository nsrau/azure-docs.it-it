---
title: Pianificare il sistema Avere vFXT - Azure
description: Informazioni sulle attività di pianificazione da eseguire prima della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 5ffa28a0f6080b94bd47519df578fd15309dbab5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153641"
---
# <a name="plan-your-avere-vfxt-system"></a>Pianificare il sistema Avere vFXT

Questo articolo illustra come pianificare una nuova vFXT per il cluster di Azure, posizionata e ridimensionata in modo appropriato in base alle esigenze.

Prima di passare ad Azure Marketplace o creare macchine virtuali, prendere in considerazione i seguenti dettagli:

* Come si interagisce il cluster con altre risorse di Azure?
* Dove devono trovarsi gli elementi cluster in reti private e subnet?
* Quale tipo di archiviazione back-end verrà usato e in che modo il cluster li accede?
* Quanto è potente eseguire i nodi del cluster per supportare il flusso di lavoro?

Continua a leggere per scoprire di più.

## <a name="learn-the-components-of-the-system"></a>Informazioni sui componenti del sistema

Quando si avvia la pianificazione, può essere utile comprendere i componenti del sistema vFXT per Azure.

* Nodi del cluster: il cluster è costituito da tre o più macchine virtuali configurate come nodi del cluster. Un maggior numero di nodi fornisce al sistema velocità effettiva più elevata e una cache più ampia.

* Cache: la capacità della cache è divisa equamente tra i nodi del cluster. Impostare la dimensione della cache per nodo quando si crea il cluster. le dimensioni del nodo vengono aggiunte per diventare la dimensione totale della cache.

* Controller cluster: il controller del cluster è una macchina virtuale aggiuntiva che si trova all'interno della stessa subnet dei nodi del cluster. Il controller è necessario per creare il cluster e per le attività di gestione in corso.

* Archiviazione back-end: i dati che si desidera memorizzare nella cache vengono archiviati a lungo termine in un sistema di archiviazione hardware o in un contenitore BLOB di Azure. È possibile aggiungere spazio di archiviazione dopo aver creato il vFXT per il cluster di Azure o se si usa l'archiviazione BLOB è possibile aggiungere e configurare il contenitore durante la creazione del cluster.

* Client: i computer client che usano i file memorizzati nella cache si connettono al cluster usando un percorso di file virtuale invece di accedere direttamente ai sistemi di archiviazione. Per altre informazioni, vedere [montare il cluster vFXT](avere-vfxt-mount-clients.md).

## <a name="subscription-resource-group-and-network-infrastructure"></a>Sottoscrizione, gruppo di risorse e infrastruttura di rete

Valutare la posizione degli elementi della distribuzione di Avere vFXT per Azure. Il diagramma seguente mostra una disposizione possibile dei componenti di Avere vFXT per Azure:

![Diagramma che mostra il controller del cluster e le macchine virtuali del cluster all'interno di una subnet. Intorno al limite della subnet c'è un limite di rete virtuale. All'interno della rete virtuale è presente un esagono che rappresenta l'endpoint di servizio di archiviazione ed è connesso con una freccia tratteggiata all'archiviazione BLOB al di fuori della rete virtuale.](media/avere-vfxt-components-option.png)

Per la pianificazione dell'infrastruttura di rete del cluster vFXT, seguire queste linee guida:

* Creare una nuova sottoscrizione per ogni vFXT per la distribuzione di Azure. Gestisci tutti i componenti in questa sottoscrizione.

  I vantaggi dell'utilizzo di una nuova sottoscrizione per ogni distribuzione includono:
  * Verifica semplificata dei costi: è possibile visualizzare e controllare tutti i costi relativi alle risorse, all'infrastruttura e ai calcoli in una sottoscrizione.
  * Pulizia più semplice: è possibile rimuovere l'intera sottoscrizione al termine del progetto.
  * Semplice partizionamento delle quote di risorse: isolare i client e il cluster vFXT in una singola sottoscrizione per proteggere altri carichi di lavoro critici da possibili limitazioni delle risorse. Questa separazione impedisce i conflitti quando si apre un numero elevato di client per un flusso di lavoro di calcolo ad alte prestazioni.

* Posizionare i sistemi di calcolo client vicino al cluster vFXT. L'archiviazione back-end può avere una posizione più remota.  

* Individuare il cluster vFXT e la macchina virtuale del controller cluster insieme. in particolare, devono essere:

  * Nella stessa rete virtuale
  * Nello stesso gruppo di risorse
  * Uso dello stesso account di archiviazione
  
  Il modello di creazione del cluster gestisce questa configurazione per la maggior parte delle situazioni.

* Il cluster deve trovarsi nella propria subnet per evitare conflitti di indirizzi IP con client o altre risorse di calcolo.

* Usare il modello di creazione del cluster per creare la maggior parte delle risorse di infrastruttura necessarie per il cluster, inclusi i gruppi di risorse, le reti virtuali, le subnet e gli account di archiviazione.

  Se si desidera utilizzare risorse già esistenti, verificare che soddisfino i requisiti indicati in questa tabella.

  | Gruppi | Usare existing? | Requisiti |
  |----------|-----------|----------|
  | Gruppo di risorse | Sì, se vuoto | Deve essere vuoto|
  | Account di archiviazione | **Sì se si** connette un contenitore BLOB esistente dopo la creazione del cluster <br/>  **No** se si crea un nuovo contenitore BLOB durante la creazione del cluster | Il contenitore BLOB esistente deve essere vuoto <br/> &nbsp; |
  | Rete virtuale | Sì | Deve includere un endpoint del servizio di archiviazione se si crea un nuovo contenitore BLOB di Azure |
  | Subnet | Sì | Non può contenere altre risorse |

## <a name="ip-address-requirements"></a>Requisiti degli indirizzi IP

Assicurarsi che la subnet del cluster disponga di un intervallo di indirizzi IP sufficiente per supportare il cluster.

Il cluster Avere vFXT usa gli indirizzi IP seguenti:

* Un indirizzo IP di gestione del cluster. Questo indirizzo può essere spostato dal nodo al nodo del cluster, in base alle esigenze, in modo che sia sempre disponibile. Usare questo indirizzo per connettersi allo strumento di configurazione del pannello di controllo.
* Per ogni nodo del cluster:
  * Almeno un indirizzo IP esposto al client. Tutti gli indirizzi client vengono gestiti dal *vserver*del cluster, che può spostare gli indirizzi IP tra i nodi in base alle esigenze.
  * Un indirizzo IP per le comunicazioni del cluster
  * Un indirizzo IP dell'istanza (assegnato alla macchina virtuale)

Se si usa l'archiviazione BLOB di Azure, potrebbero essere necessari anche indirizzi IP dalla rete virtuale del cluster:  

* Un account di archiviazione BLOB di Azure richiede almeno cinque indirizzi IP. Tenere presente questo requisito se si individua l'archiviazione BLOB nella stessa rete virtuale del cluster.
* Se si usa l'archiviazione BLOB di Azure che si trova all'esterno della rete virtuale del cluster, creare un endpoint del servizio di archiviazione all'interno della rete virtuale. L'endpoint non utilizza un indirizzo IP.

È possibile posizionare le risorse di rete e l'archiviazione BLOB (se in uso) in gruppi di risorse diversi rispetto al cluster.

## <a name="vfxt-node-size"></a>dimensioni del nodo vFXT

Le macchine virtuali che fungono da nodi del cluster determinano la velocità effettiva delle richieste e la capacità di archiviazione della cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Ogni nodo vFXT sarà identico. Ciò significa che se si crea un cluster a tre nodi, si avranno tre macchine virtuali con tipo e dimensioni uguali.

| Tipo di istanza | vCPU | Memoria  | Archiviazione SSD locale  | Numero massimo di dischi dati | Velocità effettiva del disco senza memorizzazione nella cache | Scheda di rete (conteggio) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 operazioni di I/O al secondo <br/> 768 MBps | 16.000 MBps (8)  |

La cache del disco per ogni nodo è configurabile e può andare da 1000 GB a 8000 GB. 4 TB per nodo è la dimensione della cache consigliata per i nodi Standard_E32s_v3.

Per altre informazioni su queste macchine virtuali, vedere la documentazione Microsoft Azure: [dimensioni delle macchine virtuali con ottimizzazione](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) per la memoria

## <a name="account-quota"></a>Quota account

Assicurarsi che la sottoscrizione abbia la capacità necessaria per eseguire il cluster Avere vFXT, nonché i sistemi client o di calcolo in uso. Per informazioni dettagliate, vedere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Archivio dati back-end

I sistemi di archiviazione back-end forniscono i file alla cache del cluster e ricevono anche i dati modificati dalla cache. Decidere se i working set verranno archiviati a lungo termine in un nuovo contenitore BLOB o in un sistema di archiviazione esistente (cloud o hardware). Questi sistemi di archiviazione back-end sono detti *Core filer*.

### <a name="hardware-core-filers"></a>Componenti di base hardware

Aggiungere i sistemi di archiviazione hardware al cluster vFXT dopo aver creato il cluster. È possibile usare un'ampia gamma di sistemi hardware diffusi, inclusi i sistemi locali, purché sia possibile raggiungere il sistema di archiviazione dalla subnet del cluster.

Vedere [Configurare l'archiviazione](avere-vfxt-add-storage.md) per istruzioni dettagliate sull'aggiunta di un sistema di archiviazione esistente al cluster Avere vFXT.

### <a name="cloud-core-filers"></a>Filer Core Cloud

Il vFXT per il sistema di Azure può usare contenitori BLOB vuoti per l'archiviazione back-end. I contenitori devono essere vuoti quando vengono aggiunti al cluster. il sistema vFXT deve essere in grado di gestire l'archivio oggetti senza dover mantenere i dati esistenti.

> [!TIP]
> Se si vuole usare l'archiviazione BLOB di Azure per il back-end, creare un nuovo contenitore come parte della creazione del cluster vFXT. Il modello di creazione del cluster può creare e configurare un nuovo contenitore BLOB in modo che sia pronto per l'uso non appena il cluster è disponibile. L'aggiunta di un contenitore in un secondo momento è più complessa.
>
> Per informazioni dettagliate, vedere [Creare Avere vFXT per Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure).

Dopo aver aggiunto il contenitore di archiviazione BLOB vuoto come file principale, è possibile copiarvi i dati tramite il cluster. Usare un meccanismo di copia parallela multithread. Vedere [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md) per informazioni su come copiare i dati nel nuovo contenitore del cluster in modo efficiente usando computer client e la cache Avere vFXT.

## <a name="cluster-access"></a>Accesso al cluster

Il cluster Avere vFXT per Azure si trova in una subnet privata e non ha un indirizzo IP pubblico. È necessario disporre di un modo per accedere alla subnet privata per l'amministrazione del cluster e le connessioni client.

Le opzioni di accesso includono:

* Host di collegamento: assegnare un indirizzo IP pubblico a una macchina virtuale separata all'interno della rete privata e usarlo per creare un tunnel SSL per i nodi del cluster.

  > [!TIP]
  > Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento. Per altre informazioni, vedere [Controller del cluster come host di collegamento](#cluster-controller-as-jump-host).

* Rete privata virtuale (VPN): configurare una VPN da punto a sito o da sito a sito tra la rete privata in Azure e le reti aziendali.

* Azure ExpressRoute: configurare una connessione privata tramite un partner ExpressRoute.

Per informazioni dettagliate su queste opzioni, vedere la [documentazione di Rete virtuale di Azure sulle comunicazioni Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controller del cluster come host di collegamento

Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento per contattare il cluster Avere vFXT dall'esterno della subnet privata. Tuttavia, poiché il controller ha privilegi di accesso per la modifica dei nodi del cluster, si presenta un lieve rischio per la sicurezza.

Per migliorare la sicurezza per un controller con un indirizzo IP pubblico, lo script di distribuzione crea automaticamente un gruppo di sicurezza di rete che limita l'accesso in ingresso alla porta 22. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

Quando si crea il cluster, è possibile scegliere se creare un indirizzo IP pubblico nel controller del cluster.

* Se si crea una **nuova rete virtuale** o una **Nuova Subnet**, al controller del cluster verrà assegnato un indirizzo IP **pubblico** .
* Se si seleziona una rete virtuale e una subnet esistenti, il controller del cluster avrà solo indirizzi IP **privati** .

## <a name="vm-access-roles"></a>Ruoli di accesso alla macchina virtuale

Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/index.yml) (RBAC) per autorizzare le macchine virtuali del cluster a eseguire determinate attività. Il controller cluster, ad esempio, richiede l'autorizzazione per creare e configurare le macchine virtuali del nodo del cluster. I nodi del cluster devono essere in grado di assegnare o riassegnare gli indirizzi IP ad altri nodi del cluster.

Per le macchine virtuali vFXT, vengono usati due ruoli predefiniti di Azure:

* Il controller del cluster usa il ruolo predefinito [collaboratore](../role-based-access-control/built-in-roles.md#avere-contributor).
* I nodi del cluster usano l' [operatore](../role-based-access-control/built-in-roles.md#avere-operator)Role havee incorporato.

Se è necessario personalizzare i ruoli di accesso per i componenti vFXT, è necessario definire il proprio ruolo e quindi assegnarlo alle VM al momento della creazione. Non è possibile usare il modello di distribuzione in Azure Marketplace. Consultare il servizio supporto tecnico clienti Microsoft aprendo un ticket nel portale di Azure come descritto in [ottenere assistenza con il sistema](avere-vfxt-open-ticket.md).

## <a name="next-step-understand-the-deployment-process"></a>Passaggio successivo: Informazioni sul processo di distribuzione

[Panoramica della distribuzione](avere-vfxt-deploy-overview.md) offre la visualizzazione di grandi dimensioni dei passaggi necessari per creare un vFXT di ricerca per il sistema di Azure e prepararlo a gestire i dati.
