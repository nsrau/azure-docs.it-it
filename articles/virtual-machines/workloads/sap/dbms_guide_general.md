---
title: Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP | Microsoft Docs
description: Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836120"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Questa guida fa parte della documentazione su come implementare e distribuire il software SAP in Microsoft Azure. Prima di leggere questa Guida, leggere il [Guida alla pianificazione e implementazione][planning-guide]. Questo documento illustra gli aspetti generico della distribuzione dei sistemi DBMS correlati a SAP in macchine virtuali di Microsoft Azure (VM) tramite l'infrastruttura di Azure come una funzionalità del servizio (IaaS).

Il documento è complementare alla documentazione sull'installazione di SAP e le note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

Questo documento introduce considerazioni sull'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. Al contrario, i sistemi DBMS specifici vengono gestiti all'interno di questo documento, dopo questo documento.

## <a name="definitions"></a>Definizioni
Questi termini vengono usati in tutto il documento:

* **IaaS**: Infrastruttura distribuita come servizio.
* **PaaS**: Piattaforma distribuita come servizio.
* **SaaS**: Software come un servizio.
* **Componente SAP**: Una singola applicazione SAP, ad esempio ERP Central Component (ECC), Business Warehouse (BW), Solution Manager o EP (Enterprise Portal). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o in un'applicazione non basata su NetWeaver, ad esempio gli oggetti Business.
* **Ambiente SAP sicuramente**: Uno o più componenti SAP raggruppati logicamente per eseguire una funzione di business, ad esempio sviluppo, controllo qualità, formazione, ripristino di emergenza o produzione.
* **Panorama applicativo SAP**: questo termine si riferisce a tutte le risorse SAP presenti nell'ambiente IT di un cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non di produzione.
* **Sistema SAP**: La combinazione di un livello DBMS e un'applicazione del livello, ad esempio, un sistema di sviluppo SAP ERP, un SAP Business Warehouse test di sistema o un sistema di produzione SAP CRM. Nelle distribuzioni di Azure non è supportata tra questi due livelli tra origini locali e Azure. Di conseguenza, un sistema SAP è di distribuire in locale o è distribuito in Azure. È possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. Ad esempio, si potrebbe distribuire lo sviluppo CRM SAP e testare sistemi in Azure ma distribuire SAP CRM produzione sistema locale.
* **Cross-premise**: Viene descritto uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con site-to-site, multisito o interfacce di connettività di Azure ExpressRoute tra i dati in locale e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. 

    La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Con questa estensione, le VM possono far parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server ed eseguire servizi su tali macchine virtuali, ad esempio servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo scenario è lo scenario più comune in uso per distribuire risorse SAP in Azure. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP sono quali macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale e sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede tali macchine virtuali facciano parte di un dominio locale e Active Directory o LDAP. 
>
> Nelle versioni precedenti della documentazione, sono stati indicati scenari IT ibridi. Il termine *ibrida* è radicato nel fatto che vi sia una connettività cross-premise tra origini locali e Azure. In questo caso, ibride significa anche che le macchine virtuali in Azure fanno parte di Active Directory locale.
>
>

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni a disponibilità elevata DBMS. Nel caso dei documenti correlati a SAP, lo scenario cross-premise si riduce a site-to-site o privati [ExpressRoute](https://azure.microsoft.com/services/expressroute/) connettività e un panorama applicativo SAP che viene distribuito tra origini locali e Azure.

## <a name="resources"></a>Risorse
Sono disponibili altri articoli nel carico di lavoro SAP in Azure. Iniziare con [carico di lavoro SAP in Azure: Iniziare a usare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e quindi scegliere l'area di interesse.

Le note SAP seguenti sono correlate a SAP in Azure per quanto riguarda l'area coperta in questo documento.

| Numero della nota | Title |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: Prodotti e tipi di macchine virtuali di Azure supportati |
| [2015553] |SAP in Microsoft Azure: Prerequisiti di supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: Monitoraggio avanzato |
| [2191498] |SAP in Linux con Azure: Monitoraggio avanzato |
| [2039619] |Applicazioni SAP in Microsoft Azure che usano Oracle Database: prodotti e versioni supportate |
| [2233094] |DB6: Applicazioni SAP in Azure con IBM DB2 per Linux, UNIX e Windows: Informazioni aggiuntive |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [2002167] |Red Hat Enterprise Linux 7.x: Installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di swapping per Linux |
| [2171857] |Oracle Database 12C: Supporto per file system in Linux |
| [1114181] |Oracle Database 11g: Supporto per file system in Linux |


Per informazioni su tutte le note SAP per Linux, vedere la [community wiki SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

È necessario una conoscenza approfondita dell'architettura di Microsoft Azure e come le macchine virtuali di Microsoft Azure vengono distribuite e gestite. Per altre informazioni, vedere [documentazione di Azure](https://docs.microsoft.com/azure/).

In generale, l'installazione di Windows, Linux e DBMS e la configurazione sono essenzialmente lo stesso come qualsiasi macchina virtuale o computer bare metal è installato in locale. Esistono alcune decisioni implementazione gestione di architettura e di sistema che sono diversi quando si usa IaaS di Azure. Questo documento illustra le specifiche relative all'architettura e le differenze di gestione di sistema per essere pronti quando si usa IaaS di Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura di archiviazione di una macchina virtuale per le distribuzioni RDBMS
Per seguire questo capitolo, leggere e comprendere le informazioni visualizzate nelle [in questo capitolo] [ deployment-guide-3] del [Guida alla distribuzione][deployment-guide]. È necessario comprendere e conoscere le diverse serie di macchine Virtuali e le differenze tra archiviazione standard e premium, prima di leggere questo capitolo. 

Per altre informazioni sull'archiviazione di Azure per macchine virtuali di Azure, vedere:

- [Introduzione al servizio managed disks per le macchine virtuali Windows Azure](../../windows/managed-disks-overview.md).
- [Introduzione al servizio managed disks per le macchine virtuali Linux di Azure](../../linux/managed-disks-overview.md).

In una configurazione di base, è consigliabile in genere una struttura di distribuzione in cui il sistema operativo, DBMS ed eventuali file binari SAP siano separati dai file di database. È consigliabile che i sistemi SAP in esecuzione in macchine virtuali di Azure abbiano il base VHD, o il disco, installato con il sistema operativo, file eseguibili del sistema di gestione di database e file eseguibili SAP. 

I file di dati e di log DBMS vengono archiviati in archiviazione standard o premium. Archiviati in dischi separati e collegati come dischi logici all'immagine del sistema operativo Azure originale della macchina virtuale. Per le distribuzioni di Linux, sono documentati indicazioni diverse, in particolare per SAP HANA.

Quando si pianifica il layout dei dischi, trovare l'equilibrio ottimale tra questi elementi:

* Numero di file di dati.
* Numero di dischi contenenti i file.
* Quote di operazioni di I/O al secondo di un singolo disco.
* Velocità effettiva dei dati per disco.
* Numero di dischi dati aggiuntivi possibili per dimensioni di VM.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
* La latenza che i diversi tipi di Archiviazione di Azure possono offrire.
* Contratti di servizio della macchina virtuale.

Azure applica una quota di operazioni di I/O al secondo per disco dati. Queste quote sono diverse per i dischi ospitati in archiviazione standard e archiviazione premium. Anche la latenza delle operazioni di I/O è diversa tra i due tipi di archiviazione. Archiviazione Premium offre una migliore latenza dei / o. 

Ognuno dei diversi tipi di VM ha un numero limitato di dischi dati che è possibile collegare. Un'altra restrizione è che solo determinati tipi VM possono usare archiviazione premium. In genere, si decide di utilizzare un determinato tipo di macchina virtuale in base ai requisiti di CPU e memoria. È anche possibile considerare il numero di IOPS, latenza e i requisiti di velocità effettiva del disco che in genere vengono ridimensionati con il numero di dischi o il tipo di dischi di archiviazione premium. Il numero di IOPS e velocità effettiva per essere raggiunta da ogni disco può richiedere dimensioni del disco, in particolare con archiviazione premium.

> [!NOTE]
> Per le distribuzioni DBMS, si consiglia l'uso di archiviazione premium per tutti i dati, log delle transazioni, o ripristino i file. Non è importante se si desidera distribuire i sistemi non di produzione o di produzione.

> [!NOTE]
> Per sfruttare i vantaggi di Azure univoco del [single VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), tutti i dischi collegati devono essere il tipo di archiviazione premium, che include il VHD di base.

> [!NOTE]
> File principali del database di hosting, ad esempio file di dati e log dei database SAP su hardware di archiviazione che si trova nei centri dati di terze parti con percorso condiviso adiacenti al data center di Azure non è supportata. Per carichi di lavoro SAP, solo l'archiviazione che è rappresentato come servizio di Azure nativo è supportato per i file di log delle transazioni e i dati dei database SAP.

Il posizionamento dei file di database e file di log e rollforward e il tipo di archiviazione di Azure da utilizzare è definito dai requisiti di IOPS, latenza e velocità effettiva. Per consentire di IOPS sufficiente, potrebbe essere necessario usare più dischi o usare un disco di archiviazione premium più grande. Se si usano più dischi, creare una striscia software tra i dischi che contengono i file di dati o il registro e ripristino i file. In questi casi, il numero di IOPS e la velocità effettiva del disco i contratti di servizio di archiviazione premium sottostante dischi o il numero massimo di IOPS ottenibile dei dischi di archiviazione standard è cumulativi per il set di striping risultante.

Come già indicato, se i requisiti di IOPS superano a ciò che è possibile fornire un singolo disco rigido virtuale, bilanciare il numero di IOPS necessari per i file di database in un numero di dischi rigidi virtuali. Il modo più semplice per distribuire il carico IOPS nei dischi consiste nella compilazione di una striscia di software nei diversi dischi. Quindi inserire un numero di file di dati del sistema DBMS SAP nei LUN ricavati dallo striping software. Il numero di dischi in striping è basato sulle richieste IOPs, le richieste di velocità effettiva del disco e volume di richieste.


- - -
> ![Windows][Logo_Windows] Windows
>
> È consigliabile l'uso di spazi di archiviazione di Windows per creare set di striping tra più dischi rigidi virtuali di Azure. Usare almeno Windows Server 2012 R2 o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Per compilare un RAID software in Linux sono supportati solo MDADM e gestione di volumi logici (LVM). Per altre informazioni, vedere:
>
> - [Configurare RAID software in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) tramite MDADM
> - [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

- - -

> [!NOTE]
> Poiché archiviazione di Azure mantiene tre immagini dei dischi rigidi virtuali, può non essere opportuno configurare una ridondanza quando si esegue lo striping. È sufficiente configurare lo striping in modo che i / o vengono distribuiti nei diversi VHD.
>

### <a name="managed-or-nonmanaged-disks"></a>Dischi non gestiti o non gestito
Un account di archiviazione di Azure è un costrutto amministrativo e soggetto a limitazioni. Limitazioni differiscono tra account di archiviazione standard e premium. Per informazioni sulle funzionalità e le limitazioni, vedere [obiettivi di scalabilità e prestazioni di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Per l'archiviazione standard, tenere presente che è previsto un limite per il numero di IOPS per account di archiviazione. Visualizzare la riga che contiene **frequenza di richiesta totale** nell'articolo [obiettivi di scalabilità e prestazioni di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). È anche previsto un limite iniziale per il numero di account di archiviazione per ogni sottoscrizione di Azure. Bilanciare i dischi rigidi virtuali per il panorama applicativo SAP più grande tra diversi account di archiviazione per evitare di raggiungere i limiti di questi account di archiviazione. Questo è un lavoro noioso quando si sta parlando di poche centinaia macchine virtuali con dischi rigidi virtuali superiore a mille.

Poiché non è consigliabile usare l'archiviazione standard per le distribuzioni DBMS in combinazione con un carico di lavoro SAP, riferimenti e i consigli per archiviazione standard sono limitati a questo breve [articolo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Per evitare il lavoro amministrativo di pianificazione e distribuzione di dischi rigidi virtuali tra diversi account di archiviazione Azure, Microsoft ha introdotto [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) nel 2017. I dischi gestiti sono disponibili per l'archiviazione premium e archiviazione standard. I principali vantaggi dei dischi gestiti rispetto ai dischi non gestiti sono:

- Per i dischi gestiti, Azure distribuisce i vari dischi rigidi virtuali tra diversi account di archiviazione automaticamente in fase di distribuzione. In questo modo, limiti degli account di archiviazione per volume di dati, velocità effettiva dei / o e IOPS non raggiunto.
- Archiviazione di Azure usano dischi gestiti, rispetta i concetti dei set di disponibilità di Azure. Se la macchina virtuale fa parte di un set di disponibilità di Azure, il VHD di base e un disco collegato di una macchina virtuale vengono distribuiti in diversi domini di errore e di aggiornamento.


> [!IMPORTANT]
> Dato i vantaggi di Azure Managed Disks, è consigliabile usare Azure Managed Disks per le distribuzioni DBMS e le distribuzioni SAP in generale.
>

Per convertire da non gestiti a managed disks, vedere:

- [Convertire una macchina virtuale Windows da dischi non gestiti a managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Convertire una macchina virtuale Linux da dischi non gestiti a managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi dati
Durante il montaggio di dischi alle macchine virtuali, è possibile scegliere se il traffico dei / o tra la macchina virtuale e i dischi in archiviazione di Azure viene memorizzato nella cache. Archiviazione standard e premium usano due tecnologie diverse per questo tipo di cache.

I suggerimenti seguenti presuppongono queste caratteristiche dei / o per DBMS standard:

- È principalmente un carico di lavoro lettura da file di dati di un database. Queste letture vengono prestazioni critiche per il sistema DBMS.
- La scrittura su file di dati si verifica in base i checkpoint o da un flusso costante di burst. Scrive un numero minore rispetto alle letture calcolata la media su un giorno, sono disponibili. Vengono mantenuti legge dai file di dati, queste operazioni di scrittura sono asincrone e non contenere eventuali transazioni utente.
- Esistono pochissime operazioni di lettura dal file registro transazioni e dal file di rollforward. Le eccezioni sono i/o elevato quando si eseguono backup del log delle transazioni.
- Il carico principale con i file di log delle transazioni o di ripristino è di scrittura. A seconda della natura del carico di lavoro, è possibile disporre i/o con dimensioni di 4 KB o, in altri casi, le dimensioni dei / o di 1 MB o più.
- Tutte le operazioni di scrittura devono essere resi persistenti su disco in modo affidabile.

Per l'archiviazione standard, i tipi di cache possibili sono:

* Nessuna
* Lettura
* Lettura/Scrittura

Per ottenere prestazioni coerenti e deterministiche, impostare il caching in archiviazione standard per tutti i dischi che contengono i file di dati correlati a DBMS, accedere e ripristinare file e lo spazio di tabella per **NONE**. La memorizzazione nella cache del disco rigido virtuale di base può rimanere con l'impostazione predefinita.

Per l'archiviazione premium, sono disponibili le opzioni di memorizzazione nella cache seguenti:

* Nessuna
* Lettura
* Lettura/Scrittura
* Nessuna + acceleratore di scrittura, ovvero solo per le macchine virtuali serie M di Azure
* Lettura + acceleratore di scrittura, ovvero solo per le macchine virtuali serie M di Azure

Per l'archiviazione premium, è consigliabile usare **caching di lettura per i file di dati** di SAP, database e scegliere **nessuna memorizzazione nella cache per i dischi dei file di log**.

Per le distribuzioni di serie M, è consigliabile usare acceleratore di scrittura di Azure per la distribuzione di DBMS. Per informazioni dettagliate, restrizioni e distribuzione dell'acceleratore di scrittura di Azure, vedere [acceleratore di scrittura abilitare](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Dischi di Azure non persistenti
Macchine virtuali di Azure offrono i dischi persistenti dopo aver distribuita una macchina virtuale. Nel caso di un riavvio VM, tutto il contenuto in tali unità verrà cancellato. Si tratta di un dato che i file di dati e i file di log e il ripristino dei database in nessuna circostanza si trova in tali unità non persistente. Potrebbero esserci delle eccezioni per alcuni database, in cui queste unità non persistente potrebbe essere adatte per tempdb e spazi di tabella temporanea. Evitare di utilizzare queste unità per le macchine virtuali serie a, poiché queste unità non persistente è limitate della velocità effettiva con quella famiglia di VM. 

Per altre informazioni, vedere [comprendere le unità temporanee nelle macchine virtuali Windows in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> L'unità D in una VM di Azure è un'unità non persistente, che è supportata da alcuni dischi locali nel nodo di calcolo di Azure. Perché è non persistenti, tutte le modifiche apportate al contenuto sull'unità D vengono perse al riavvio della macchina virtuale. Le modifiche includono i file che sono stati archiviati, le directory che sono state create e le applicazioni installate.
>
> ![Linux][Logo_Linux] Linux
>
> VM Linux in Azure viene montata automaticamente un'unità in /mnt/resource un'unità non persistente supportata da dischi locali nel nodo di calcolo di Azure. Perché è non persistenti, tutte le modifiche apportate al contenuto in//mnt/Resource vengono perse al riavvio della macchina virtuale. Le modifiche includono i file che sono stati archiviati, le directory che sono state create e le applicazioni installate.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resilienza di Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure archivia il VHD di base, con sistema operativo e i dischi collegati o oggetti BLOB, in almeno tre nodi di archiviazione separato. Questo tipo di archiviazione è denominato archiviazione con ridondanza locale (LRS). Archiviazione con ridondanza locale è il valore predefinito per tutti i tipi di archiviazione in Azure.

Sono disponibili altri metodi di ridondanza. Per altre informazioni, vedere [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Archiviazione Premium è consigliabile il tipo di archiviazione per le macchine virtuali DBMS e i dischi che archiviano i database e log e ripristino i file. Il metodo di ridondanza è disponibile solo per l'archiviazione premium è di tipo archiviazione con ridondanza locale. Di conseguenza, è necessario configurare i metodi di database per abilitare la replica dei dati di database in un'altra zona disponibilità o area geografica di Azure. I metodi di database includono SQL Server AlwaysOn, Oracle Data Guard e replica di sistema HANA.


> [!NOTE]
> Per le distribuzioni DBMS, non è consigliabile l'uso di archiviazione con ridondanza geografica (GRS) per l'archiviazione standard. Archiviazione con ridondanza geografica gravemente sulle prestazioni e non rispetta l'ordine di scrittura tra diversi dischi rigidi virtuali collegati a una macchina virtuale. Non rispetta l'ordine di scrittura nei diversi dischi rigidi virtuali potenzialmente comporta database incoerente sul lato destinazione della replica. Questa situazione si verifica se i file di database i log e fase di rollforward vengono distribuiti tra più dischi rigidi virtuali, come avviene in genere, sul lato di macchina virtuale di origine.



## <a name="vm-node-resiliency"></a>Resilienza del nodo della VM
Azure offre diversi contratti di servizio diversi per le macchine virtuali. Per altre informazioni, vedere la versione più recente di [contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Poiché il livello DBMS viene in genere critico per la disponibilità in un sistema SAP, è necessario comprendere i set di disponibilità, zone di disponibilità e gli eventi di manutenzione. Per altre informazioni su questi concetti, vedere [gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gestire la disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minimo per gli scenari con un carico di lavoro SAP DBMS di produzione si consiglia:

- Distribuire due macchine virtuali in un set nella stessa area di Azure di disponibilità separato.
- Eseguire queste due macchine virtuali nella stessa rete virtuale di Azure e sono interfacce di rete associati uscita dalle subnet stessa.
- Usare i metodi di database per mantenere un hot standby con la seconda macchina virtuale. I metodi possono essere SQL Server Always On, Oracle Data Guard o replica di sistema HANA.

È anche possibile distribuire una terza macchina virtuale in un'altra area di Azure e usare gli stessi metodi di database per fornire una replica asincrona in un'altra area di Azure.

Per informazioni su come impostare il set di disponibilità di Azure, vedere [in questa esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerazioni sulla rete di Azure
Nelle distribuzioni di SAP su larga scala, usare il progetto del [Data Center virtuale di Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Usarlo per le assegnazioni di ruolo e le autorizzazioni e configurazione di rete virtuale a diverse parti dell'organizzazione.

Queste procedure consigliate sono il risultato di centinaia di clienti (distribuzioni):

- Le reti virtuali in che verrà distribuita l'applicazione SAP non hanno accesso a internet.
- Le macchine virtuali del database eseguiti nella stessa rete virtuale come livello di applicazione.
- Le macchine virtuali all'interno della rete virtuale hanno un'allocazione dell'indirizzo IP privato statica. Per altre informazioni, vedere [metodi di allocazione in Azure e i tipi di indirizzi IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Restrizioni relative al routing da e verso le macchine virtuali DBMS sono *non* impostata con i firewall installati nelle macchine virtuali DBMS locale. Al contrario, il routing del traffico è definito con [(Nsg) di gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Per separare e isolare il traffico alla VM DBMS, assegnare diverse schede di rete alla macchina virtuale. Tutte le schede NIC Ottiene un indirizzo IP diverso, e tutte le schede NIC viene assegnata a una subnet di rete virtuale diversa. Ogni subnet dispone di diverse regole di sicurezza di rete. L'isolamento o la separazione del traffico di rete è una misura per il routing. Non è utilizzato per impostare le quote per velocità effettiva della rete.

> [!NOTE]
> Assegnazione di indirizzi IP statici tramite Azure si intende assegnare gli utenti a singole interfacce di rete virtuale. Non assegnare indirizzi IP statici all'interno del sistema operativo guest a un'interfaccia di rete virtuale. Alcuni servizi di Azure come Backup di Azure si basano sul fatto che al minimo la scheda virtuale primaria è impostata su DHCP e non per gli indirizzi IP statici. Per altre informazioni, vedere [backup di macchine virtuali di Azure di risolvere i problemi](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Per assegnare più indirizzi IP statici a una macchina virtuale, assegnare più schede di rete virtuale a una macchina virtuale.
>


> [!IMPORTANT]
> La configurazione [Appliance virtuali di rete](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un SAP NetWeaver-, Hybris- o sistema basati su S/4HANA SAP non è supportata. Questa restrizione è per motivi di prestazioni e funzionalità. Il percorso di comunicazione tra il livello applicazione SAP e il livello DBMS deve essere uno diretto. Non include la restrizione [gruppo di sicurezza dell'applicazione (ASG) e le regole NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se tali regole ASG e sicurezza di rete consentano a un percorso di comunicazione diretta. 
>
> Altri scenari in cui non sono supportati Appliance virtuali di rete sono:
>
> * Percorsi di comunicazione tra macchine virtuali di Azure che rappresentano Linux Pacemaker nodi e cluster dispositivi SBD come descritto in [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure su SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Percorsi di comunicazione tra macchine virtuali di Azure e Windows Server Scale-Out File Server di scalabilità orizzontale (SOFS) impostata come descritto in [clustering di un'istanza di SAP ASCS/SCS in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra i partner di comunicazione due. È anche possibile limitare la velocità effettiva in percorsi critici tra il livello applicazione SAP e il livello DBMS. In alcuni scenari di clienti, Appliance virtuali di rete possono provocare cluster Linux Pacemaker esito negativo. Questi sono casi in cui comunicano le comunicazioni tra i nodi del cluster Pacemaker di Linux per il dispositivo SBD attraverso un'appliance virtuale di rete.
>

> [!IMPORTANT]
> Un altro di progettazione del *non* supportata è la separazione del livello applicazione SAP e il livello DBMS in diverse reti virtuali di Azure che non sono [eseguire il peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra loro. È consigliabile separare il livello applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure anziché tramite diverse reti virtuali di Azure. 
>
> Se si decide di non segue il suggerimento e invece separare i due livelli in diverse reti virtuali, le due reti virtuali devono trovarsi [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tenere presente che traffico di rete tra due [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) reti virtuali di Azure è soggetta a costi di trasferimento. Volume di dati molto grandi che è costituito da diversi terabyte verrà scambiati tra il livello applicazione SAP e il livello DBMS. Se il livello applicazione SAP e il livello DBMS sono separati tra due con peering reti virtuali di Azure, è possibile accumulare sostanziale dei costi.

Set di due macchine virtuali di utilizzo per la distribuzione di DBMS all'interno di una disponibilità di Azure di produzione. Usare anche il routing separato per il livello applicazione SAP e il traffico di gestione e operazioni alle due VM DBMS. Vedere l'immagine seguente:

![Diagramma di due macchine virtuali in due subnet](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usare Azure Load Balancer per reindirizzare il traffico
L'utilizzo di indirizzi IP virtuali privati usato nelle funzionalità, ad esempio SQL Server Always On o replica di sistema HANA richiede la configurazione di un servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico Usa le porte probe per determinare il nodo attivo di DBMS e instradare il traffico esclusivamente in tale nodo di database attivo. 

Se si verifica un failover del nodo di database, non è necessario per riconfigurare l'applicazione SAP. Al contrario, le architetture di applicazioni SAP più comune riconnettere l'indirizzo IP virtuale privato. Nel frattempo, il bilanciamento del carico reagisce per il failover a nodi, reindirizzando il traffico in base all'indirizzo IP virtuale privato per il secondo nodo.

Azure offre due diversi [SKU di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): uno SKU basic e uno SKU standard. A meno che non si desidera distribuire le zone di disponibilità di Azure, il servizio di bilanciamento del carico di base dello SKU avviene correttamente.

È il traffico tra le macchine virtuali DBMS e il livello applicazione SAP sempre indirizzate tramite il bilanciamento del carico di tutto il tempo? La risposta dipende da come si configura il bilanciamento del carico. 

A questo punto, il traffico in ingresso alla VM DBMS viene sempre instradato attraverso il servizio di bilanciamento del carico. Route del traffico in uscita dalla VM DBMS al livello di applicazione che della macchina virtuale dipende dalla configurazione del bilanciamento del carico. 

Il bilanciamento del carico offre un'opzione DirectServerReturn. Se tale opzione è configurata, il traffico indirizzato dalla VM DBMS per il livello applicazione SAP è *non* instradato attraverso il servizio di bilanciamento del carico. Al contrario, passa direttamente a livello dell'applicazione. Se non è configurato Direct server Return, il traffico di ritorno per il livello applicazione SAP viene instradato tramite il servizio di bilanciamento del carico.

È consigliabile configurare Direct server return in combinazione con servizi di bilanciamento del carico che vengono posizionate tra il livello applicazione SAP e il livello DBMS. Questa configurazione riduce la latenza di rete tra i due livelli.

Per un esempio di come impostare questa configurazione con SQL Server Always On, vedere [configurare un listener ILB per gruppi di disponibilità Always On in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se si usano i modelli JSON di GitHub pubblicati come riferimento per le distribuzioni di infrastruttura SAP in Azure, studiare [modello per un sistema SAP a 3 livelli](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). In questo modello, è anche possibile visualizzare le impostazioni corrette per il bilanciamento del carico.

### <a name="azure-accelerated-networking"></a>Rete accelerata di Azure
Per ridurre ulteriormente la latenza di rete tra VM di Azure, è consigliabile che si sceglie [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Usarlo quando si distribuiscono macchine virtuali di Azure per un carico di lavoro SAP, in particolare per il livello applicazione SAP e il livello DBMS di SAP.

> [!NOTE]
> Non tutti i tipi di macchine Virtuali supportano la rete accelerata. L'articolo precedente Elenca i tipi di VM che supportano la funzionalità rete accelerata.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Per informazioni su come distribuire le VM con Accelerated Networking per Windows, vedere [creare una macchina virtuale Windows con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Per altre informazioni sulla distribuzione di Linux, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> Nel caso di SUSE, Red Hat e Oracle Linux, la funzionalità di Rete accelerata è supportata nelle versioni recenti. Le versioni precedenti, ad esempio SLES 12 SP2 o RHEL 7.2 non supportano la rete accelerata di Azure.
>


## <a name="deployment-of-host-monitoring"></a>Distribuzione di host di monitoraggio
Per la produzione delle applicazioni SAP in macchine virtuali di Azure, SAP richiede la possibilità di ottenere i dati dagli host fisici che eseguono le macchine virtuali di Azure di monitoraggio dell'host. È necessario un livello di patch dell'agente host SAP specifico che abilita questa funzionalità in SAPOSCOL e nell'agente host SAP. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per ulteriori informazioni sulla distribuzione di componenti che forniscono dati host a SAPOSCOL e SAP Host Agent e la gestione del ciclo di vita di tali componenti, vedere la [Deployment guide][deployment-guide].


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su un determinato DBMS, vedere:

- [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sqlserver.md)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_oracle.md)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_ibm.md)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sapase.md)
- [Distribuzione di SAP MaxDB, liveCache e server di contenuti in Azure](dbms_guide_maxdb.md)
- [Guida operativa a SAP HANA in Azure](hana-vm-operations.md)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](sap-hana-availability-overview.md)
- [Guida del backup di SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md)

