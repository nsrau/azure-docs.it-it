---
title: Pianificare il sistema Avere vFXT - Azure
description: Informazioni sulle attività di pianificazione da eseguire prima della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f0e5523565dc561ed457dbc340835ad1889cb876
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669868"
---
# <a name="plan-your-avere-vfxt-system"></a>Pianificare il sistema Avere vFXT

Questo articolo illustra come pianificare un nuovo cluster Avere vFXT per Azure così da assicurarsi che il cluster creato sia posizionato e dimensionato in modo appropriato per le esigenze specifiche. 

Prima di passare ad Azure Marketplace o di creare macchine virtuali, valutare come interagirà il cluster con gli altri elementi in Azure. Pianificare la posizione delle risorse cluster nella rete privata e nelle subnet e stabilire la posizione dell'archiviazione back-end. Assicurarsi che i nodi del cluster creati siano sufficientemente potenti per supportare il flusso di lavoro. 

Per altre informazioni, continuare la lettura.

## <a name="resource-group-and-network-infrastructure"></a>Gruppo di risorse e infrastruttura di rete

Valutare la posizione degli elementi della distribuzione di Avere vFXT per Azure. Il diagramma seguente mostra una disposizione possibile dei componenti di Avere vFXT per Azure:

![Diagramma che mostra il controller del cluster e le macchine virtuali del cluster all'interno di una subnet. Intorno al limite della subnet c'è un limite di rete virtuale. All'interno della rete virtuale è presente un esagono che rappresenta l'endpoint di servizio di archiviazione ed è connesso con una freccia tratteggiata all'archiviazione BLOB al di fuori della rete virtuale.](media/avere-vfxt-components-option.png)

Seguire queste linee guida per la pianificazione dell'infrastruttura di rete del sistema Avere vFXT:

* Tutti gli elementi devono essere gestiti con una nuova sottoscrizione creata per la distribuzione di Avere vFXT. Questa strategia semplifica il monitoraggio dei costi e la pulizia e aiuta anche a partizionare le quote delle risorse. Poiché Avere vFXT viene usato con un numero elevato di client, l'isolamento dei client e del cluster in una sottoscrizione singola consente di proteggere altri carichi di lavoro critici dalla possibile limitazione delle risorse durante il provisioning dei client.

* Posizionare i sistemi di calcolo client vicino al cluster vFXT. L'archiviazione back-end può avere una posizione più remota.  

* Per semplicità, posizionare il cluster vFXT e la macchina virtuale controller del cluster nella stessa rete virtuale e nello stesso gruppo di risorse. Devono usare anche lo stesso account di archiviazione. 

* Il cluster deve essere posizionato nella relativa subnet per evitare conflitti di indirizzi IP con i client o le risorse di calcolo. 

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

## <a name="vfxt-node-sizes"></a>Dimensioni dei nodi vFXT 

Le macchine virtuali che fungono da nodi del cluster determinano la velocità effettiva delle richieste e la capacità di archiviazione della cache. È possibile scegliere tra due tipi di istanze, con caratteristiche di memoria, processore e archiviazione locale diverse. 

Ogni nodo vFXT sarà identico. Ciò significa che se si crea un cluster a tre nodi, si avranno tre macchine virtuali con tipo e dimensioni uguali. 

| Tipo di istanza | vCPU | Memoria  | Archiviazione SSD locale  | Valore massimo per dischi di dati | Velocità effettiva del disco senza memorizzazione nella cache | Scheda di rete (conteggio) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25.600 operazioni di I/O al secondo <br/> 384 MBps | 8.000 MBps (8) |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 operazioni di I/O al secondo <br/> 768 MBps | 16.000 MBps (8)  |

La cache del disco per ogni nodo è configurabile e può andare da 1000 GB a 8000 GB. La dimensione della cache consigliata per i nodi Standard_D16s_v3 è di 1 TB per nodo, mentre per i nodi Standard_E32s_v3 è di 4 TB per nodo.

Per altre informazioni su queste macchine virtuali, leggere i documenti di Microsoft Azure seguenti:

* [Dimensioni delle macchine virtuali per utilizzo generico](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Quota account

Assicurarsi che la sottoscrizione abbia la capacità necessaria per eseguire il cluster Avere vFXT, nonché i sistemi client o di calcolo in uso. Per informazioni dettagliate, vedere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Archivio dati back-end

Quando non è nella cache, il working set verrà archiviato in un nuovo contenitore BLOB o in un sistema di archiviazione hardware o cloud esistente?

Se si vuole usare l'archiviazione BLOB di Azure per il back-end, è necessario creare un nuovo contenitore durante la creazione del cluster vFXT. Usare lo script di distribuzione ``create-cloud-backed-container`` e specificare l'account di archiviazione per il nuovo contenitore BLOB. In questo modo il nuovo contenitore viene creato e configurato ed è pronto per l'uso non appena è pronto il cluster. Per informazioni dettagliate, vedere [Creare i nodi e configurare il cluster](avere-vfxt-deploy.md#create-nodes-and-configure-the-cluster).

> [!NOTE]
> Come sistema di archiviazione principale per Avere vFXT è possibile usare solo contenitori di archiviazione BLOB vuoti. vFXT deve essere in grado di gestire il proprio archivio oggetti senza necessità di conservare i dati esistenti. 
>
> Vedere [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md) per informazioni su come copiare i dati nel nuovo contenitore del cluster in modo efficiente usando computer client e la cache Avere vFXT.

Se si vuole usare un sistema di archiviazione locale esistente, aggiungerlo al cluster vFXT dopo la sua creazione. Lo script di distribuzione ``create-minimal-cluster`` crea un cluster vFXT senza archiviazione back-end. Vedere [Configurare l'archiviazione](avere-vfxt-add-storage.md) per istruzioni dettagliate sull'aggiunta di un sistema di archiviazione esistente al cluster Avere vFXT. 

## <a name="next-step-understand-the-deployment-process"></a>Passaggio successivo: Informazioni sul processo di distribuzione

In [Panoramica della distribuzione](avere-vfxt-deploy-overview.md) viene fornita una panoramica generale di tutti i passaggi necessari per creare un sistema Avere vFXT per Azure e prepararlo a fornire i dati.  