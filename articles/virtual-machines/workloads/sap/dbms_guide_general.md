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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101354"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Questa guida fa parte della documentazione relativa all'implementazione e alla distribuzione del software SAP in Microsoft Azure. Prima di leggere questa guida, leggere la [Guida alla pianificazione e all'implementazione][planning-guide]. Questo documento illustra gli aspetti di distribuzione generici dei sistemi DBMS correlati a SAP in Microsoft Azure macchine virtuali (VM) usando le funzionalità di infrastruttura distribuita come servizio (IaaS) di Azure.

Il documento è complementare alla documentazione sull'installazione di SAP e alle note su SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP su determinate piattaforme.

Questo documento introduce considerazioni sull'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. Al contrario, i sistemi DBMS specifici vengono gestiti all'interno di questo articolo, dopo questo documento.

## <a name="definitions"></a>Definizioni
Nel documento vengono usati i termini seguenti:

* **IaaS**: (Infrastructure as a Service) infrastruttura distribuita come servizio.
* **PaaS**: (Platform as a Service) piattaforma distribuita come servizio.
* **SaaS**: (Software as a Service) software come servizio.
* **Componente SAP**: singola applicazione SAP, ad esempio ERP Central Component (ecc), Business Warehouse (BW), Solution Manager o Enterprise Portal (EP). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio gli oggetti business.
* **Ambiente SAP**: uno o più componenti SAP raggruppati in modo logico per eseguire una funzione aziendale, ad esempio sviluppo, controllo di qualità, formazione, ripristino di emergenza o produzione.
* **Panorama**applicativo SAP: questo termine si riferisce all'intero asset SAP nel panorama IT di un cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non di produzione.
* **Sistema SAP**: combinazione del livello DBMS e del livello dell'applicazione, ad esempio, un sistema di sviluppo SAP ERP, un sistema di test di SAP Business Warehouse o un sistema di produzione SAP CRM. Nelle distribuzioni di Azure, la divisione di questi due livelli tra l'ambiente locale e Azure non è supportata. Di conseguenza, un sistema SAP viene distribuito in locale o distribuito in Azure. È possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. Ad esempio, è possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure, ma distribuire il sistema di produzione SAP CRM in locale.
* **Cross-premise**: descrive uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. 

    La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Con questa estensione, le VM possono far parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server ed eseguire servizi in tali macchine virtuali, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo scenario è lo scenario più comune usato per distribuire asset SAP in Azure. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP sono le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale e sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che tali macchine virtuali facciano parte di un dominio locale e Active Directory/LDAP. 
>
> Nelle versioni precedenti della documentazione sono stati citati gli scenari IT ibridi. Il termine *ibrido* è radicato nel fatto che esiste una connettività cross-premise tra l'ambiente locale e Azure. In questo caso ibrido significa anche che le macchine virtuali in Azure fanno parte del Active Directory locale.
>
>

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni DBMS a disponibilità elevata. Nel caso dei documenti correlati a SAP, lo scenario cross-premise si riduce alla connettività [ExpressRoute](https://azure.microsoft.com/services/expressroute/) da sito a sito o privata e da un landscape SAP distribuito tra l'istanza locale e Azure.

## <a name="resources"></a>Risorse
Sono disponibili altri articoli sul carico di lavoro SAP in Azure. Iniziare a [usare il carico di lavoro SAP in Azure: per](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) iniziare, scegliere l'area di interesse.

Le note SAP seguenti sono correlate a SAP in Azure in relazione all'area illustrata in questo documento.

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure |
| [2015553] |SAP on Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: monitoraggio avanzato |
| [2191498] |SAP in Linux con Azure: monitoraggio avanzato |
| [2039619] |Applicazioni SAP su Microsoft Azure con il database Oracle: versioni e prodotti supportati |
| [2233094] |DB6: applicazioni SAP in Azure con IBM DB2 per Linux, UNIX e Windows: informazioni aggiuntive |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: note di installazione |
| [2002167] |Red Hat Enterprise Linux 7. x: installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di swapping per Linux |
| [2171857] |Oracle Database 12c: supporto del file System in Linux |
| [1114181] |Oracle Database 11g: supporto del file System in Linux |


Per informazioni su tutte le note SAP per Linux, vedere il [wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

È necessaria una conoscenza approfondita dell'architettura Microsoft Azure e del modo in cui vengono distribuite e gestite Microsoft Azure macchine virtuali. Per ulteriori informazioni, vedere la [documentazione di Azure](https://docs.microsoft.com/azure/).

In generale, l'installazione e la configurazione di Windows, Linux e DBMS sono essenzialmente le stesse delle macchine virtuali o dei computer bare metal installati in locale. Esistono alcune decisioni di implementazione di architettura e gestione del sistema diverse quando si usa Azure IaaS. In questo documento vengono illustrate le differenze specifiche relative a architettura e gestione del sistema da preparare quando si usa Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura di archiviazione di una macchina virtuale per le distribuzioni RDBMS
Per seguire questo capitolo, leggere e comprendere le informazioni presentate in [questo capitolo][deployment-guide-3] della [Guida alla distribuzione][deployment-guide]. Prima di leggere questo capitolo, è necessario comprendere e conoscere le diverse serie di VM e le differenze tra archiviazione standard e Premium. 

Per informazioni sull'archiviazione di Azure per le macchine virtuali di Azure, vedere:

- [Introduzione a Managed disks per le macchine virtuali Windows di Azure](../../windows/managed-disks-overview.md).
- [Introduzione a Managed disks per macchine virtuali Linux di Azure](../../linux/managed-disks-overview.md).

In una configurazione di base, in genere è consigliabile una struttura di distribuzione in cui il sistema operativo, DBMS e i file binari SAP finali sono separati dai file di database. È consigliabile che i sistemi SAP in esecuzione nelle macchine virtuali di Azure dispongano del disco rigido virtuale o del disco di base, installato con il sistema operativo, i file eseguibili del sistema di gestione di database e gli eseguibili SAP. 

I dati e i file di log di DBMS vengono archiviati in archiviazione standard o Premium. Vengono archiviati in dischi separati e collegati come dischi logici alla macchina virtuale dell'immagine del sistema operativo di Azure originale. Per le distribuzioni di Linux, sono documentate diverse raccomandazioni, specialmente per SAP HANA.

Quando si pianifica il layout del disco, trovare il migliore equilibrio tra gli elementi seguenti:

* Numero di file di dati.
* Numero di dischi contenenti i file.
* Quote di operazioni di I/O al secondo di un singolo disco.
* Velocità effettiva dei dati per disco.
* Numero di dischi dati aggiuntivi possibili per dimensioni di VM.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
* La latenza che i diversi tipi di Archiviazione di Azure possono offrire.
* Contratti di Service VM.

Azure applica una quota di operazioni di I/O al secondo per disco dati. Queste quote sono diverse per i dischi ospitati nell'archiviazione standard e nell'archiviazione Premium. Anche la latenza delle operazioni di I/O è diversa tra i due tipi di archiviazione. Archiviazione Premium offre una migliore latenza di I/O. 

Ognuno dei diversi tipi di VM ha un numero limitato di dischi dati che è possibile alleghiare. Un'altra restrizione è che solo determinati tipi di VM possono usare l'archiviazione Premium. In genere, si decide di usare un determinato tipo di macchina virtuale in base ai requisiti di CPU e memoria. È anche possibile considerare i requisiti di IOPS, latenza e velocità effettiva del disco che in genere vengono ridimensionati con il numero di dischi o il tipo di dischi di archiviazione Premium. Il numero di IOPS e la velocità effettiva da ottenere da ogni disco possono determinare le dimensioni del disco, soprattutto con archiviazione Premium.

> [!NOTE]
> Per le distribuzioni DBMS, è consigliabile usare l'archiviazione Premium per i file di dati, di log delle transazioni o di rollforward. Non è importante se si desidera distribuire sistemi di produzione o di non produzione.

> [!NOTE]
> Per sfruttare i vantaggi offerti dal contratto di base per le [singole macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)di Azure, tutti i dischi collegati devono essere il tipo di archiviazione Premium, che include il disco rigido virtuale di base.

> [!NOTE]
> L'hosting dei file di database principali, ad esempio i file di dati e di log, di database SAP nell'hardware di archiviazione che si trova in centri dati di terze parti con percorso condiviso adiacente ai Data Center di Azure non è supportato. Per i carichi di lavoro SAP, solo lo spazio di archiviazione rappresentato come servizio nativo di Azure è supportato per i file di dati e di log delle transazioni dei database SAP.

Il posizionamento dei file di database e dei file di log e di rollforward e il tipo di archiviazione di Azure usato vengono definiti in base ai requisiti di IOPS, latenza e velocità effettiva. Per avere un numero di operazioni di i/o al secondo, potrebbe essere necessario usare più dischi o usare un disco di archiviazione Premium di dimensioni maggiori. Se si usano più dischi, compilare uno striping software tra i dischi che contengono i file di dati o i file di log e ripristino. In questi casi, le operazioni di i/o al secondo e la velocità effettiva del disco per i dischi di archiviazione Premium sottostanti o il numero massimo di IOPS ottenibili di dischi di archiviazione standard sono cumulative per il set di striping risultante.

Come già indicato, se il requisito di IOPS supera quello che può essere fornito da un singolo disco rigido virtuale, bilanciare il numero di operazioni di i/o al secondo necessarie per i file di database in diversi VHD. Il modo più semplice per distribuire il carico di IOPS tra i dischi consiste nel creare una striscia software sui diversi dischi. Inserire quindi un numero di file di dati di SAP DBMS nei lun ricavati dal Stripe software. Il numero di dischi nello striping è determinato dalle richieste di IOPs, dalle richieste di velocità effettiva del disco e dalle richieste di volume.


---
> ![Windows][Logo_Windows] Windows
>
> Si consiglia di usare spazi di archiviazione Windows per creare set di striping in più dischi rigidi virtuali di Azure. Usare almeno Windows Server 2012 R2 o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Per la creazione di un RAID software in Linux sono supportati solo MDADM e Logical Volume Manager (LVM). Per altre informazioni, vedere:
>
> - [Configurare RAID software in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) con mdadm
> - [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Poiché archiviazione di Azure mantiene tre immagini dei dischi rigidi virtuali, non ha senso configurare una ridondanza quando si esegue lo striping. È sufficiente configurare lo striping in modo che le le I/o vengano distribuite su dischi rigidi virtuali diversi.
>

### <a name="managed-or-nonmanaged-disks"></a>Dischi gestiti o non gestiti
Un account di archiviazione di Azure è un costrutto amministrativo, oltre a un soggetto di limitazioni. Le limitazioni sono diverse tra gli account di archiviazione standard e gli account di archiviazione Premium. Per informazioni sulle funzionalità e sulle limitazioni, vedere [obiettivi di scalabilità e prestazioni per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Per l'archiviazione standard, tenere presente che è previsto un limite per gli IOPS per ogni account di archiviazione. Vedere la riga che contiene la **frequenza di richiesta totale** nell'articolo [obiettivi di scalabilità e prestazioni per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Esiste anche un limite iniziale per il numero di account di archiviazione per ogni sottoscrizione di Azure. Bilanciare i dischi rigidi virtuali per il panorama SAP più ampio in account di archiviazione diversi per evitare di raggiungere i limiti di questi account di archiviazione. Si tratta di un lavoro noioso quando si parla di un centinaio di macchine virtuali con più di mille VHD.

Poiché l'uso dell'archiviazione standard per le distribuzioni DBMS insieme a un carico di lavoro SAP non è consigliato, i riferimenti e le raccomandazioni per l'archiviazione standard sono limitati a questo breve [articolo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Per evitare il lavoro amministrativo di pianificazione e distribuzione di dischi rigidi virtuali in diversi account di archiviazione di Azure, Microsoft ha introdotto [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) in 2017. I dischi gestiti sono disponibili per archiviazione standard e archiviazione Premium. I principali vantaggi dei dischi gestiti rispetto ai dischi non gestiti sono:

- Per i dischi gestiti, Azure distribuisce automaticamente i vari dischi rigidi virtuali in account di archiviazione diversi in fase di distribuzione. In questo modo, non vengono raggiunti i limiti dell'account di archiviazione per il volume di dati, la velocità effettiva di I/O e IOPS.
- Con Managed disks, archiviazione di Azure rispetta i concetti dei set di disponibilità di Azure. Se la macchina virtuale fa parte di un set di disponibilità di Azure, il disco rigido virtuale di base e il disco collegato di una macchina virtuale vengono distribuiti in domini di errore e di aggiornamento diversi.


> [!IMPORTANT]
> Considerati i vantaggi di Azure Managed Disks, è consigliabile usare Azure Managed Disks per le distribuzioni DBMS e le distribuzioni di SAP in generale.
>

Per eseguire la conversione da dischi non gestiti a gestiti, vedere:

- [Convertire una macchina virtuale Windows da dischi non gestiti a Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Convertire una macchina virtuale Linux da dischi non gestiti a Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi dati
Quando si montano i dischi nelle macchine virtuali, è possibile scegliere se memorizzare nella cache il traffico di I/O tra la macchina virtuale e i dischi che si trovano in archiviazione di Azure. Archiviazione standard e Premium usano due tecnologie diverse per questo tipo di cache.

Le indicazioni seguenti presuppongono queste caratteristiche di I/O per il sistema DBMS standard:

- Si tratta principalmente di un carico di lavoro di lettura per i file di dati di un database. Queste letture sono critiche per le prestazioni del sistema DBMS.
- La scrittura nei file di dati si verifica in picchi in base ai checkpoint o a un flusso costante. Media di più di un giorno, il numero di Scritture è inferiore a quello delle letture. Opposto alle letture dei file di dati, queste Scritture sono asincrone e non tengono alcuna transazione utente.
- Esistono pochissime operazioni di lettura dal file registro transazioni e dal file di rollforward. Quando si eseguono I backup del log delle transazioni, le eccezioni sono I/o di grandi dimensioni.
- Il carico principale per i file di log di transazione o di rollforward è Scritture. A seconda della natura del carico di lavoro, è possibile avere un I/o minimo di 4 KB o, in altri casi, dimensioni di I/O pari a 1 MB o superiore.
- Tutte le scritture devono essere rese permanente sul disco in modo affidabile.

Per l'archiviazione standard, i tipi di cache possibili sono:

* nessuno
* Read
* Lettura/Scrittura

Per ottenere prestazioni coerenti e deterministiche, impostare la memorizzazione nella cache sull'archiviazione standard per tutti i dischi che contengono file di dati correlati a DBMS, file di log e ripristino e spazio tabella su **nessuno**. La memorizzazione nella cache del disco rigido virtuale di base può rimanere con l'impostazione predefinita.

Per archiviazione Premium sono disponibili le seguenti opzioni di memorizzazione nella cache:

* nessuno
* Read
* Lettura/Scrittura
* None + acceleratore di scrittura, che è solo per le VM serie M di Azure
* Read + acceleratore di scrittura, che è solo per le VM serie M di Azure

Per archiviazione Premium, è consigliabile usare la **memorizzazione nella cache di lettura per i file di dati** del database SAP e scegliere **nessuna memorizzazione nella cache per i dischi dei file di log**.

Per le distribuzioni di serie M, è consigliabile usare acceleratore di scrittura di Azure per la distribuzione DBMS. Per informazioni dettagliate, restrizioni e distribuzione di acceleratore di scrittura di Azure, vedere [Enable acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Dischi non permanenti di Azure
Le macchine virtuali di Azure offrono dischi non permanenti dopo la distribuzione di una macchina virtuale. Nel caso di un riavvio della macchina virtuale, tutto il contenuto in tali unità viene eliminato. Si tratta di un dato che i file di dati e i file di log e di ripristino dei database non devono trovarsi in nessuna circostanza nelle unità non salvate. Potrebbero essere presenti eccezioni per alcuni database, in cui queste unità non permanente potrebbero essere adatte per gli spazi di tabella tempdb e Temp. Evitare di usare queste unità per le macchine virtuali di serie A perché tali unità non salvate sono limitate alla velocità effettiva con tale famiglia di macchine virtuali. 

Per altre informazioni, vedere informazioni [sull'unità temporanea in macchine virtuali Windows in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> L'unità D in una macchina virtuale di Azure è un'unità non permanente, supportata da alcuni dischi locali nel nodo di calcolo di Azure. Poiché non è permanente, tutte le modifiche apportate al contenuto nell'unità D andranno perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory che sono state create e le applicazioni installate.
>
> ![Linux][Logo_Linux] Linux
>
> Le macchine virtuali Linux di Azure montano automaticamente un'unità in/mnt/Resource che è un'unità non permanente supportata da dischi locali nel nodo di calcolo di Azure. Poiché non è permanente, tutte le modifiche apportate al contenuto in/mnt/Resource vengono perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory che sono state create e le applicazioni installate.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resilienza di Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure archivia il disco rigido virtuale di base, con il sistema operativo e i dischi o i BLOB collegati, in almeno tre nodi di archiviazione distinti. Questo tipo di archiviazione è denominato archiviazione con ridondanza locale (con ridondanza locale). CON ridondanza locale è l'impostazione predefinita per tutti i tipi di archiviazione in Azure.

Sono disponibili altri metodi di ridondanza. Per altre informazioni, vedere [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Archiviazione Premium è il tipo di archiviazione consigliato per le macchine virtuali DBMS e i dischi in cui sono archiviati i file di database e di log e di rollforward. L'unico metodo di ridondanza disponibile per archiviazione Premium è con ridondanza locale. Di conseguenza, è necessario configurare i metodi di database per abilitare la replica dei dati del database in un'altra area di Azure o in una zona di disponibilità. I metodi di database includono SQL Server Always On, Oracle Data Guard e la replica di sistema HANA.


> [!NOTE]
> Per le distribuzioni DBMS, l'uso dell'archiviazione con ridondanza geografica (GRS) non è consigliato per l'archiviazione standard. Il GRS influiscono gravemente sulle prestazioni e non rispetta l'ordine di scrittura nei diversi dischi rigidi virtuali collegati a una macchina virtuale. Se non si rispetta l'ordine di scrittura in VHD diversi, è possibile che si ottengano database incoerenti sul lato della destinazione di replica. Questa situazione si verifica se i file di database e di log e di rollforward sono distribuiti in più dischi rigidi virtuali, come avviene in genere nel lato della VM di origine.



## <a name="vm-node-resiliency"></a>Resilienza del nodo della VM
Azure offre diversi contratti di contratto per le macchine virtuali. Per ulteriori informazioni, vedere la versione più recente di [SLA per le macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Poiché il livello DBMS è in genere cruciale per la disponibilità in un sistema SAP, è necessario comprendere i set di disponibilità, le zone di disponibilità e gli eventi di manutenzione. Per altre informazioni su questi concetti, vedere [gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gestire la disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

La raccomandazione minima per gli scenari DBMS di produzione con un carico di lavoro SAP è:

- Distribuire due macchine virtuali in un set di disponibilità separato nella stessa area di Azure.
- Eseguire queste due macchine virtuali nella stessa rete virtuale di Azure e disporre di schede di rete collegate dalle stesse subnet.
- Usare i metodi di database per mantenere un hot standby con la seconda macchina virtuale. I metodi possono essere SQL Server Always On, Oracle Data Guard o replica di sistema HANA.

È anche possibile distribuire una terza macchina virtuale in un'altra area di Azure e usare gli stessi metodi di database per fornire una replica asincrona in un'altra area di Azure.

Per informazioni su come configurare i set di disponibilità di Azure, vedere [questa esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerazioni sulla rete di Azure
Nelle distribuzioni di SAP su larga scala, usare il progetto di [data center virtuale di Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Usarlo per la configurazione della rete virtuale e le autorizzazioni e le assegnazioni di ruolo a diverse parti dell'organizzazione.

Queste procedure consigliate sono il risultato di centinaia di distribuzioni dei clienti:

- Le reti virtuali in cui è distribuita l'applicazione SAP non hanno accesso a Internet.
- Le macchine virtuali del database vengono eseguite nella stessa rete virtuale del livello applicazione.
- Le macchine virtuali all'interno della rete virtuale hanno un'allocazione statica dell'indirizzo IP privato. Per altre informazioni, vedere [tipi di indirizzi IP e metodi di allocazione in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Le restrizioni di routing da e verso le macchine virtuali DBMS *non* vengono impostate con i firewall installati nelle macchine virtuali DBMS locali. Il routing del traffico viene invece definito con i [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Per separare e isolare il traffico verso la macchina virtuale DBMS, assegnare schede di rete diverse alla macchina virtuale. Ogni scheda di interfaccia di rete riceve un indirizzo IP diverso e ogni scheda di interfaccia di rete viene assegnata a una subnet di rete virtuale diversa. Ogni subnet ha regole NSG diverse. L'isolamento o la separazione del traffico di rete è una misura per il routing. Non viene usato per impostare le quote per la velocità effettiva della rete.

> [!NOTE]
> L'assegnazione di indirizzi IP statici tramite Azure significa assegnarli a singole schede di rete virtuali. Non assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure come backup di Azure si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria è impostata su DHCP e non su indirizzi IP statici. Per altre informazioni, vedere [risolvere i problemi di backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Per assegnare più indirizzi IP statici a una macchina virtuale, assegnare più schede di rete virtuali a una macchina virtuale.
>


> [!IMPORTANT]
> La configurazione di [appliance virtuali di rete](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un sistema SAP basato su SAP NetWeaver, hybris o S/4HANA non è supportata. Questa restrizione è per motivi di funzionalità e di prestazioni. Il percorso di comunicazione tra il livello applicazione SAP e il livello DBMS deve essere uno diretto. La restrizione non include il [gruppo di sicurezza delle applicazioni (ASG) e le regole NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se le regole ASG e NSG consentono un percorso di comunicazione diretta. 
>
> Gli altri scenari in cui le appliance virtuali di rete non sono supportate sono:
>
> * Percorsi di comunicazione tra le macchine virtuali di Azure che rappresentano i nodi del cluster Pacemaker Linux e i dispositivi SBD, come descritto in [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Percorsi di comunicazione tra macchine virtuali di Azure e Windows Server File server di scalabilità orizzontale (SOFS) impostati come descritto in [cluster di un'istanza di SAP ASC/SCS in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Le appliance virtuali di rete nei percorsi di comunicazione possono raddoppiare facilmente la latenza di rete tra due partner di comunicazione. Possono anche limitare la velocità effettiva nei percorsi critici tra il livello applicazione SAP e il livello DBMS. In alcuni scenari dei clienti, le appliance virtuali di rete possono causare errori nei cluster di pacemaker Linux. Si tratta di casi in cui le comunicazioni tra i nodi del cluster Pacemaker Linux comunicano con il dispositivo SBD tramite un'appliance virtuale di rete.
>

> [!IMPORTANT]
> Un'altra progettazione *non* supportata è la separazione tra il livello dell'applicazione SAP e il livello DBMS in reti virtuali di Azure diverse senza [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) reciproco. È consigliabile separare il livello dell'applicazione SAP e il livello DBMS usando le subnet all'interno di una rete virtuale di Azure anziché usando reti virtuali di Azure diverse. 
>
> Se si decide di non seguire la raccomandazione e invece di separare i due livelli in reti virtuali diverse, è necessario eseguire il [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)delle due reti virtuali. 
>
> Tenere presente che il traffico di rete tra due reti virtuali di Azure con [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) è soggetto ai costi di trasferimento. Un volume di dati enorme costituito da molti terabyte viene scambiato tra il livello applicazione SAP e il livello DBMS. È possibile accumulare costi sostanziali se il livello applicazione SAP e il livello DBMS sono separati tra due reti virtuali di Azure con peering.

Usare due macchine virtuali per la distribuzione DBMS di produzione all'interno di un set di disponibilità di Azure. Usare anche il routing separato per il livello dell'applicazione SAP e il traffico di gestione e operazioni verso le due macchine virtuali DBMS. Vedere l'immagine seguente:

![Diagramma di due macchine virtuali in due subnet](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usare Azure Load Balancer per reindirizzare il traffico
L'uso di indirizzi IP virtuali privati usati in funzionalità come SQL Server Always On o la replica di sistema HANA richiede la configurazione di un servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico usa le porte Probe per determinare il nodo DBMS attivo e indirizzare il traffico esclusivamente al nodo del database attivo. 

Se si verifica un failover del nodo del database, non è necessario riconfigurare l'applicazione SAP. Al contrario, le architetture di applicazioni SAP più comuni si riconnettono a fronte dell'indirizzo IP virtuale privato. Nel frattempo, il servizio di bilanciamento del carico reagisce al failover del nodo reindirizzando il traffico verso l'indirizzo IP virtuale privato al secondo nodo.

Azure offre due [SKU di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)diversi: uno SKU Basic e uno SKU standard. A meno che non si voglia eseguire la distribuzione tra le zone di disponibilità di Azure, lo SKU di base del servizio di bilanciamento del carico funziona correttamente.

Il traffico tra le macchine virtuali DBMS e il livello dell'applicazione SAP viene sempre instradato attraverso il servizio di bilanciamento del carico? La risposta dipende da come si configura il bilanciamento del carico. 

Al momento, il traffico in ingresso verso la macchina virtuale DBMS viene sempre indirizzato tramite il servizio di bilanciamento del carico. Il routing del traffico in uscita dalla macchina virtuale DBMS alla macchina virtuale a livello di applicazione dipende dalla configurazione del servizio di bilanciamento del carico. 

Il bilanciamento del carico offre un'opzione DirectServerReturn. Se questa opzione è configurata, il traffico diretto dalla macchina virtuale DBMS al livello dell'applicazione SAP *non* viene instradato tramite il servizio di bilanciamento del carico. Al contrario, passa direttamente al livello dell'applicazione. Se DirectServerReturn non è configurato, il traffico di ritorno al livello dell'applicazione SAP viene indirizzato attraverso il servizio di bilanciamento del carico.

Si consiglia di configurare DirectServerReturn in combinazione con i bilanciamenti del carico posizionati tra il livello applicazione SAP e il livello DBMS. Questa configurazione riduce la latenza di rete tra i due livelli.

Per un esempio di come configurare questa configurazione con SQL Server Always On, vedere [configurare un listener ILB per gruppi di disponibilità always on in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se si usano i modelli JSON di GitHub pubblicati come riferimento per le distribuzioni dell'infrastruttura SAP in Azure, studiare questo [modello per un sistema SAP a 3 livelli](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Questo modello consente anche di visualizzare le impostazioni corrette per il servizio di bilanciamento del carico.

### <a name="azure-accelerated-networking"></a>Rete accelerata di Azure
Per ridurre ulteriormente la latenza di rete tra le macchine virtuali di Azure, è consigliabile scegliere [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Usarlo quando si distribuiscono macchine virtuali di Azure per un carico di lavoro SAP, in particolare per il livello dell'applicazione SAP e il livello DBMS di SAP.

> [!NOTE]
> Non tutti i tipi di VM supportano la rete accelerata. Nell'articolo precedente sono elencati i tipi di VM che supportano la rete accelerata.
>

---
> ![Windows][Logo_Windows] Windows
>
> Per informazioni su come distribuire VM con rete accelerata per Windows, vedere [creare una macchina virtuale Windows con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Per altre informazioni sulla distribuzione di Linux, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> Nel caso di SUSE, Red Hat e Oracle Linux, la funzionalità di Rete accelerata è supportata nelle versioni recenti. Le versioni precedenti, ad esempio SLES 12 SP2 o RHEL 7,2, non supportano la rete accelerata di Azure.
>


## <a name="deployment-of-host-monitoring"></a>Distribuzione del monitoraggio host
Per l'uso in produzione di applicazioni SAP in macchine virtuali di Azure, SAP richiede la possibilità di ottenere i dati di monitoraggio dell'host dagli host fisici che eseguono le macchine virtuali di Azure. È necessario un livello di patch dell'agente host SAP specifico che abilita questa funzionalità in SAPOSCOL e nell'agente host SAP. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per ulteriori informazioni sulla distribuzione di componenti che forniscono dati host a SAPOSCOL e all'agente host SAP e alla gestione del ciclo di vita di tali componenti, vedere la [Guida alla distribuzione][deployment-guide].


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su un particolare DBMS, vedere:

- [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sqlserver.md)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_oracle.md)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_ibm.md)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sapase.md)
- [Distribuzione di SAP MaxDB, liveCache e server di contenuti in Azure](dbms_guide_maxdb.md)
- [Guida operativa a SAP HANA in Azure](hana-vm-operations.md)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](sap-hana-availability-overview.md)
- [Guida al backup per SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md)

