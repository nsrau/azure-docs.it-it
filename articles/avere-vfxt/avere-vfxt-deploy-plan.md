---
title: Pianificare il sistema Avere vFXT - Azure
description: Informazioni sulle attività di pianificazione da eseguire prima della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256229"
---
# <a name="plan-your-avere-vfxt-system"></a>Pianificare il sistema Avere vFXT

Questo articolo illustra come pianificare una nuova vFXT per il cluster di Azure, posizionata e ridimensionata in modo appropriato in base alle esigenze. 

Prima di passare ad Azure Marketplace o di creare macchine virtuali, valutare come interagirà il cluster con gli altri elementi in Azure. Pianificare la posizione delle risorse cluster nella rete privata e nelle subnet e stabilire la posizione dell'archiviazione back-end. Assicurarsi che i nodi del cluster creati siano sufficientemente potenti per supportare il flusso di lavoro. 

Per altre informazioni, continuare la lettura.

## <a name="resource-group-and-network-infrastructure"></a>Gruppo di risorse e infrastruttura di rete

Valutare la posizione degli elementi della distribuzione di Avere vFXT per Azure. Il diagramma seguente mostra una disposizione possibile dei componenti di Avere vFXT per Azure:

![Diagramma che mostra il controller del cluster e le macchine virtuali del cluster all'interno di una subnet. Intorno al limite della subnet c'è un limite di rete virtuale. All'interno della rete virtuale è presente un esagono che rappresenta l'endpoint di servizio di archiviazione ed è connesso con una freccia tratteggiata all'archiviazione BLOB al di fuori della rete virtuale.](media/avere-vfxt-components-option.png)

Seguire queste linee guida per la pianificazione dell'infrastruttura di rete del sistema Avere vFXT:

* Tutti gli elementi devono essere gestiti con una nuova sottoscrizione creata per la distribuzione di Avere vFXT. I vantaggi includono: 
  * Verifica semplificata dei costi: è possibile visualizzare e controllare tutti i costi relativi alle risorse, all'infrastruttura e ai calcoli in una sottoscrizione.
  * Pulizia più semplice: è possibile rimuovere l'intera sottoscrizione al termine del progetto.
  * Praticità di partizionamento delle quote di risorse: proteggere altri carichi di lavoro critici da possibili limitazioni delle risorse isolando i client e il cluster vFXT in una singola sottoscrizione. In questo modo si evitano conflitti quando si apre un numero elevato di client per un flusso di lavoro di calcolo ad alte prestazioni.

* Posizionare i sistemi di calcolo client vicino al cluster vFXT. L'archiviazione back-end può avere una posizione più remota.  

* Il cluster vFXT e la VM controller cluster devono trovarsi nella stessa rete virtuale (VNET), nello stesso gruppo di risorse e usare lo stesso account di archiviazione. Il modello di creazione automatica del cluster gestisce questa situazione per la maggior parte delle situazioni.

* Il cluster deve essere posizionato nella relativa subnet per evitare conflitti di indirizzi IP con i client o le risorse di calcolo. 

* Il modello di creazione del cluster può creare la maggior parte delle risorse di infrastruttura necessarie per il cluster, inclusi i gruppi di risorse, le reti virtuali, le subnet e gli account di archiviazione. Se si desidera utilizzare risorse già esistenti, verificare che soddisfino i requisiti indicati in questa tabella. 

  | Resource | Usare existing? | Requisiti |
  |----------|-----------|----------|
  | Gruppo di risorse | Sì, se vuoto | Deve essere vuoto| 
  | Account di archiviazione | Sì se si connette un contenitore BLOB esistente dopo la creazione del cluster <br/>  No se si crea un nuovo contenitore BLOB durante la creazione del cluster | Il contenitore BLOB esistente deve essere vuoto <br/> &nbsp; |
  | Rete virtuale | Yes | Deve includere un endpoint del servizio di archiviazione se si crea un nuovo contenitore BLOB di Azure | 
  | Subnet | Yes |   |

## <a name="ip-address-requirements"></a>Requisiti degli indirizzi IP 

Assicurarsi che la subnet del cluster disponga di un intervallo di indirizzi IP sufficiente per supportare il cluster. 

Il cluster Avere vFXT usa gli indirizzi IP seguenti:

* Un indirizzo IP di gestione del cluster. Questo indirizzo può passare da un nodo all'altro del cluster, ma è sempre disponibile, in modo che sia possibile connettersi allo strumento di configurazione del pannello di controllo di Avere.
* Per ogni nodo del cluster:
  * Almeno un indirizzo IP esposto al client. Tutti gli indirizzi esposti al client vengono gestiti tramite lo strumento *vserver* del cluster, che può spostarli tra i nodi in base alle esigenze.
  * Un indirizzo IP per le comunicazioni del cluster
  * Un indirizzo IP dell'istanza (assegnato alla macchina virtuale)

Se si usa l'archiviazione BLOB di Azure, potrebbero essere anche necessari indirizzi IP della rete virtuale del cluster:  

* Un account di archiviazione BLOB di Azure richiede almeno cinque indirizzi IP. Tenere presente questo requisito se si colloca l'archiviazione BLOB nella stessa rete virtuale del cluster.
* Se si usa l'archiviazione BLOB di Azure all'esterno della rete virtuale per il cluster, è necessario creare un endpoint di servizio di archiviazione all'interno della rete virtuale. Questo endpoint non usa un indirizzo IP.

È possibile posizionare le risorse di rete e l'archiviazione BLOB (se in uso) in gruppi di risorse diversi rispetto al cluster.

## <a name="vfxt-node-size"></a>dimensioni del nodo vFXT

Le macchine virtuali che fungono da nodi del cluster determinano la velocità effettiva delle richieste e la capacità di archiviazione della cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Ogni nodo vFXT sarà identico. Ciò significa che se si crea un cluster a tre nodi, si avranno tre macchine virtuali con tipo e dimensioni uguali. 

| Tipo di istanza | vCPU | Memoria  | Archiviazione SSD locale  | Numero massimo di dischi dati | Velocità effettiva del disco senza memorizzazione nella cache | Scheda di rete (conteggio) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 operazioni di I/O al secondo <br/> 768 MBps | 16.000 MBps (8)  |

La cache del disco per ogni nodo è configurabile e può andare da 1000 GB a 8000 GB. 4 TB per nodo è la dimensione della cache consigliata per i nodi Standard_E32s_v3.

Per ulteriori informazioni su queste macchine virtuali, leggere la documentazione Microsoft Azure:

* [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Quota account

Assicurarsi che la sottoscrizione abbia la capacità necessaria per eseguire il cluster Avere vFXT, nonché i sistemi client o di calcolo in uso. Per informazioni dettagliate, vedere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Archivio dati back-end

Specificare dove il cluster Avere vFXT deve archiviare i dati non presenti nella cache. Decidere se il working set verrà archiviato a lungo termine in un nuovo contenitore BLOB o in un sistema di archiviazione hardware o cloud esistente. 

Se si vuole usare l'archiviazione BLOB di Azure per il back-end, è necessario creare un nuovo contenitore durante la creazione del cluster vFXT. In questo modo il nuovo contenitore viene creato e configurato ed è pronto per l'uso non appena è pronto il cluster. 

Per informazioni dettagliate, vedere [Creare Avere vFXT per Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure).

> [!NOTE]
> Come sistema di archiviazione principale per Avere vFXT è possibile usare solo contenitori di archiviazione BLOB vuoti. vFXT deve essere in grado di gestire il proprio archivio oggetti senza necessità di conservare i dati esistenti. 
>
> Vedere [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md) per informazioni su come copiare i dati nel nuovo contenitore del cluster in modo efficiente usando computer client e la cache Avere vFXT.

Se si vuole usare un sistema di archiviazione locale esistente, aggiungerlo al cluster vFXT dopo la sua creazione. Vedere [Configurare l'archiviazione](avere-vfxt-add-storage.md) per istruzioni dettagliate sull'aggiunta di un sistema di archiviazione esistente al cluster Avere vFXT.

## <a name="cluster-access"></a>Accesso al cluster 

Il cluster Avere vFXT per Azure si trova in una subnet privata e non ha un indirizzo IP pubblico. È necessario disporre di un metodo per l'accesso alla subnet privata per l'amministrazione del cluster e le connessioni client. 

Le opzioni di accesso includono:

* Host di collegamento: assegnare un indirizzo IP pubblico a una macchina virtuale separata all'interno della rete privata e usarlo per creare un tunnel SSL per i nodi del cluster. 

  > [!TIP]
  > Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento. Per altre informazioni, vedere [Controller del cluster come host di collegamento](#cluster-controller-as-jump-host).

* Rete privata virtuale (VPN): configurare una VPN da punto a sito o da sito a sito per la rete privata.

* Azure ExpressRoute: configurare una connessione privata tramite un partner ExpressRoute. 

Per informazioni dettagliate su queste opzioni, vedere la [documentazione di Rete virtuale di Azure sulle comunicazioni Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controller del cluster come host di collegamento

Se si imposta un indirizzo IP pubblico nel controller del cluster, è possibile usarlo come host di collegamento per contattare il cluster Avere vFXT dall'esterno della subnet privata. Tuttavia, poiché il controller ha privilegi di accesso per la modifica dei nodi del cluster, si presenta un lieve rischio per la sicurezza.  

Per migliorare la sicurezza per un controller con un indirizzo IP pubblico, lo script di distribuzione crea automaticamente un gruppo di sicurezza di rete che limita l'accesso in ingresso alla porta 22. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

Quando si crea il cluster, è possibile scegliere se creare un indirizzo IP pubblico nel controller del cluster. 

* Se si crea una nuova rete virtuale o una nuova subnet, al controller del cluster verrà assegnato un indirizzo IP pubblico.
* Se si seleziona una rete virtuale e una subnet esistenti, il controller del cluster avrà solo indirizzi IP privati. 

## <a name="vm-access-roles"></a>Ruoli di accesso alla macchina virtuale 

Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/index.yml) (RBAC) per autorizzare le macchine virtuali del cluster a eseguire determinate attività. Il controller cluster, ad esempio, richiede l'autorizzazione per creare e configurare le macchine virtuali del nodo del cluster. I nodi del cluster devono essere in grado di assegnare o riassegnare gli indirizzi IP ad altri nodi del cluster.

Per le macchine virtuali vFXT, vengono usati due ruoli predefiniti di Azure: 

* Il controller del cluster usa il ruolo predefinito [collaboratore](../role-based-access-control/built-in-roles.md#avere-contributor). 
* I nodi del cluster usano l' [operatore](../role-based-access-control/built-in-roles.md#avere-operator) Role havee incorporato

Se è necessario personalizzare i ruoli di accesso per i componenti vFXT, è necessario definire il proprio ruolo e quindi assegnarlo alle VM al momento della creazione. Non è possibile usare il modello di distribuzione in Azure Marketplace. Consultare il servizio supporto tecnico clienti Microsoft aprendo un ticket nel portale di Azure come descritto in [ottenere assistenza con il sistema](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Passaggio successivo: Informazioni sul processo di distribuzione

In [Panoramica della distribuzione](avere-vfxt-deploy-overview.md) viene fornita una panoramica generale di tutti i passaggi necessari per creare un sistema Avere vFXT per Azure e prepararlo a fornire i dati.  