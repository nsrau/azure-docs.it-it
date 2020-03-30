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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101354"
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

Questa guida fa parte della documentazione su come implementare e distribuire il software SAP in Microsoft Azure. Prima di leggere questa guida, leggere la [Guida alla pianificazione e all'implementazione][planning-guide]. Questo documento illustra gli aspetti di distribuzione generici dei sistemi DBMS correlati a SAP nelle macchine virtuali (VM) di Microsoft Azure usando le funzionalità dell'infrastruttura di Azure come servizio (IaaS).

Il documento integra la documentazione relativa all'installazione SAP e le note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP su determinate piattaforme.

Questo documento introduce considerazioni sull'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. Dopo questo documento, invece, i sistemi DBMS specifici vengono gestiti all'interno di questo documento.

## <a name="definitions"></a>Definizioni
In tutto il documento vengono utilizzati i seguenti termini:

* **IaaS**: Infrastrutture come servizio.
* **PaaS**: Piattaforma come servizio.
* **SaaS**: Software come servizio.
* Componente **SAP**: un'applicazione SAP specifica, ad esempio ERP Central Component (ECC), Business Warehouse (BW), Solution Manager o Enterprise Portal (EP). I componenti SAP possono essere basati sulle tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* **Ambiente SAP:** uno o più componenti SAP raggruppati logicamente per eseguire una funzione aziendale, ad esempio sviluppo, controllo qualità, formazione, ripristino di emergenza o produzione.
* **Panorama SAP:** questo termine si riferisce all'intero asset SAP nel panorama IT di un cliente. Il panorama SAP include tutti gli ambienti di produzione e non di produzione.
* **Sistema SAP:** combinazione di un livello DBMS e un livello di applicazione, ad esempio, di un sistema di sviluppo SAP ERP, di un sistema di test SAP Business Warehouse o di un sistema di produzione SAP CRM. Nelle distribuzioni di Azure, la divisione di questi due livelli tra l'ambiente locale e Azure non è supportata. Di conseguenza, un sistema SAP viene distribuito in locale o in Azure.As a result, an SAP system is either deployed on-premises or it's deployed in Azure. È possibile distribuire i diversi sistemi di un panorama SAP in Azure o in locale. Ad esempio, è possibile distribuire i sistemi di sviluppo e test di SAP CRM in Azure, ma distribuire il sistema di produzione SAP CRM in locale.
* **Cross-premise:** descrive uno scenario in cui le macchine virtuali vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure.Cross-premises : Describes a scenario where VMs are deployed to an Azure subscription that has site-to-site, multisite, or Azure ExpressRoute connectivity between the on-premises data centers and Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. 

    La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Con questa estensione, le VM possono far parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server ed eseguire servizi in tali macchine virtuali, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo scenario è lo scenario più comune in uso per distribuire gli asset SAP in Azure.This scenario is the most common scenario in use to deploy SAP assets on Azure. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP sono in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale e sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione dell'intero panorama SAP in Azure richiede che tali macchine virtuali facciano parte di un dominio locale e di Active Directory/LDAP. 
>
> Nelle versioni precedenti della documentazione sono stati menzionati scenari ibridi-IT. Il termine ibrido è radicato nel fatto che esiste una connettività cross-premise tra l'ambiente locale e Azure.The term *hybrid* is rooted in the fact that there's a cross-premises connectivity between on-premises and Azure. In questo caso, ibrido significa anche che le macchine virtuali in Azure fanno parte di Active Directory locale.
>
>

Alcuni documenti Microsoft descrivono gli scenari cross-premise in modo leggermente diverso, in particolare per le configurazioni a disponibilità elevata DBMS. Nel caso dei documenti correlati a SAP, lo scenario cross-premise si riduce alla connettività ExpressRoute da sito a sito o privata e a un panorama SAP distribuito tra l'ambiente locale e Azure.In the case of sap-related documents, the cross-premises scenario impils downto to site-to-site or private [ExpressRoute](https://azure.microsoft.com/services/expressroute/) connectivity and an SAP landscape that's distributed between on-premises and Azure.

## <a name="resources"></a>Risorse
Sono disponibili altri articoli sul carico di lavoro SAP in Azure.There are other articles available on SAP workload on Azure. Iniziare con il carico di [lavoro SAP in Azure: iniziare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e quindi scegliere l'area di interesse.

Le note SAP seguenti sono correlate a SAP in Azure per quanto riguarda l'area trattata in questo documento.

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di AzureSAP applications on Azure: Supported products and Azure VM types |
| [2015553] |SAP in Microsoft Azure: prerequisiti del supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: monitoraggio avanzato |
| [2191498] |SAP su Linux con Azure: monitoraggio avanzato |
| [2039619] |Applicazioni SAP in Microsoft Azure usando il database Oracle: prodotti e versioni supportati |
| [2233094] |DB6: Applicazioni SAP in Azure tramite IBM DB2 per Linux, UNIX e Windows: informazioni aggiuntive |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: note di installazione |
| [2002167] |Red Hat Enterprise Linux 7.x: installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di scambio per Linux |
| [2171857] |Oracle Database 12c: supporto del file system su Linux |
| [1114181] |Oracle Database 11g: Supporto del file system su Linux |


Per informazioni su tutte le note SAP per Linux, vedere il [wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

È necessaria una conoscenza di microsoft Azure dell'architettura di Microsoft Azure e del modo in cui vengono distribuite e gestite le macchine virtuali di Microsoft Azure.You need a working knowledge of Microsoft Azure architecture and how Microsoft Azure virtual machines are deployed and operated. Per altre informazioni, vedere [la documentazione](https://docs.microsoft.com/azure/)di Azure.For more information, see Azure documentation .

In generale, l'installazione e la configurazione di Windows, Linux e DBMS sono essenzialmente le stesse di qualsiasi macchina virtuale o macchina bare metal installata in locale. Esistono alcune decisioni relative all'architettura e all'implementazione della gestione del sistema che sono diverse quando si usa Azure IaaS.There are some architecture and system management implementation decisions that are different when you use Azure IaaS. Questo documento illustra le differenze specifiche di gestione dell'architettura e del sistema per cui essere preparati quando si usa Azure IaaS.This document explains the specific architectural and system management differences to be prepared for when you use Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura di archiviazione di una macchina virtuale per le distribuzioni RDBMSStorage structure of a VM for RDBMS deployments
Per seguire questo capitolo, leggere e comprendere le informazioni presentate in [questo capitolo][deployment-guide-3] della Guida alla [distribuzione.][deployment-guide] È necessario comprendere e conoscere le diverse serie VM e le differenze tra archiviazione standard e archiviazione Premium prima di leggere questo capitolo. 

Per altre informazioni su Archiviazione di Azure per le macchine virtuali di Azure, vedere:To learn about Azure Storage for Azure VMs, see:

- [Introduzione ai dischi gestiti per le macchine virtuali Windows](../../windows/managed-disks-overview.md)di Azure.
- [Introduzione ai dischi gestiti per le macchine virtuali di Azure Linux.](../../linux/managed-disks-overview.md)

In una configurazione di base è in genere consigliabile una struttura di distribuzione in cui il sistema operativo, il sistema DBMS e gli eventuali file binari SAP sono separati dai file di database. È consigliabile che nei sistemi SAP in esecuzione nelle macchine virtuali di Azure sia installato il disco rigido virtuale di base, o disco, con il sistema operativo, gli eseguibili del sistema di gestione di database e gli eseguibili SAP. 

I file di dati e di log DBMS vengono archiviati in un archivio standard o in uno spazio di archiviazione Premium. Vengono archiviati in dischi separati e collegati come dischi logici alla macchina virtuale dell'immagine del sistema operativo Azure originale. Per le distribuzioni Linux, sono documentati diversi consigli, in particolare per SAP HANA.

Quando si pianifica il layout del disco, trovare il miglior equilibrio tra questi elementi:

* Numero di file di dati.
* Numero di dischi contenenti i file.
* Quote di operazioni di I/O al secondo di un singolo disco.
* Velocità effettiva dei dati per disco.
* Numero di dischi dati aggiuntivi possibili per dimensioni di VM.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
* La latenza che i diversi tipi di Archiviazione di Azure possono offrire.
* Contratti di di rete VM.

Azure applica una quota di operazioni di I/O al secondo per disco dati. Queste quote sono diverse per i dischi ospitati nell'archiviazione standard e nell'archiviazione Premium.These quotas are different for disks hosted on standard storage and premium storage. Anche la latenza delle operazioni di I/O è diversa tra i due tipi di archiviazione. Lo storage Premium offre una migliore latenza di I/O.Premium storage delivers better I/O latency. 

Ognuno dei diversi tipi di VM ha un numero limitato di dischi dati che è possibile collegare. Un'altra restrizione è che solo determinati tipi di macchine virtuali possono usare l'archiviazione Premium.Another restriction is that only certain VM types can use premium storage. In genere, si decide di usare un determinato tipo di macchina virtuale in base ai requisiti di CPU e memoria. È inoltre possibile considerare i requisiti di IOPS, latenza e velocità effettiva del disco che in genere vengono scalati con il numero di dischi o il tipo di dischi di archiviazione Premium.You might also consider the IOPS, latency, and disk throughput requirements that usually are scaled with the number of disks or the type of premium storage disks. Il numero di operazioni di I/O al secondo e la velocità effettiva che deve essere raggiunta da ogni disco potrebbero determinare le dimensioni del disco, in particolare con l'archiviazione Premium.The number of IOPS and the throughput to be achieved by each disk might dictate disk size, especially with premium storage.

> [!NOTE]
> Per le distribuzioni DBMS, è consigliabile usare l'archiviazione Premium per tutti i file di dati, log delle transazioni o di ripristino. Non importa se si desidera distribuire sistemi di produzione o non di produzione.

> [!NOTE]
> Per trarre vantaggio dall'unico livello di [archiviazione per le macchine virtuali singole](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)di Azure, tutti i dischi collegati devono essere il tipo di archiviazione Premium, che include il disco rigido virtuale di base.

> [!NOTE]
> L'hosting di file di database principali, ad esempio file di dati e di log, di database SAP su hardware di archiviazione che si trova in data center di terze parti adiacenti ai data center di Azure non è supportato. Per i carichi di lavoro SAP, solo l'archiviazione rappresentata come servizio di Azure nativo è supportata per i file di log delle transazioni e dei dati dei database SAP.

Il posizionamento dei file di database e dei file di log e ripristino e il tipo di Archiviazione di Azure in uso sono definiti dai requisiti di IOPS, latenza e velocità effettiva. Per avere un numero sufficiente di operazioni di I/O al secondo, potrebbe essere necessario usare più dischi o un disco di archiviazione Premium più grande. Se si utilizzano più dischi, creare uno stripe software tra i dischi che contengono i file di dati o i file di log e di rieseguire. In questi casi, le operazioni di I/O al secondo e i contratti di servizio per la velocità effettiva del disco dei dischi di archiviazione Premium sottostanti o le operazioni di I/O al secondo massime raggiungibili dei dischi di archiviazione standard sono accumulate per il set di striping risultante.

Come già indicato, se il requisito di IOPS supera quello che un singolo disco rigido virtuale può fornire, bilanciare il numero di operazioni di I/O al secondo necessarie per i file di database in un numero di dischi rigidi virtuali. Il modo più semplice per distribuire il carico di operazioni di I/O al secondo tra i dischi consiste nel creare uno stripe software sui diversi dischi. Posizionare quindi un numero di file di dati del DBMS SAP nei LUN ricavati dallo stripe software. Il numero di dischi nello stripe è guidato da richieste di operazioni di I/O al secondo, velocità effettiva del disco e richieste di volume.


---
> ![WINDOWS][Logo_Windows] WINDOWS
>
> È consigliabile usare Spazi di archiviazione Windows per creare set di striping in più dischi rigidi virtuali di Azure.We recommend that you use Windows Storage Spaces to create stripe sets across multiple Azure VHDs. Utilizzare almeno Windows Server 2012 R2 o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Solo MDADM e Logical Volume Manager (LVM) sono supportati per creare un RAID software su Linux. Per altre informazioni, vedere:
>
> - [Configurare il RAID software su Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) utilizzando MDADM
> - [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Poiché Archiviazione di Azure mantiene tre immagini dei dischi rigidi virtuali, non ha senso configurare una ridondanza quando si esegue lo striping. È sufficiente configurare lo striping in modo che gli I/O vengano distribuiti sui diversi dischi rigidi virtuali.
>

### <a name="managed-or-nonmanaged-disks"></a>Dischi gestiti o non gestiti
Un account di archiviazione di Azure è un costrutto amministrativo e anche soggetto a limitazioni. Le limitazioni differiscono tra gli account di archiviazione standard e gli account di archiviazione premium. Per informazioni sulle funzionalità e le limitazioni, vedere Obiettivi di scalabilità e prestazioni di Archiviazione di Azure.For information on capabilities and limitations, see [Azure Storage scalability and performance targets.](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Per l'archiviazione standard, tenere presente che esiste un limite per le operazioni di I/O al secondo per account di archiviazione. Vedere la riga che contiene la **frequenza totale** delle richieste nell'articolo Obiettivi di scalabilità e prestazioni di Archiviazione di Azure.See the row that contains Total Request Rate in the article [Azure Storage scalability and performance targets.](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets) È inoltre disponibile un limite iniziale per il numero di account di archiviazione per ogni sottoscrizione di Azure.There's also an initial limit on the number of storage accounts per Azure subscription. Bilanciare i dischi rigidi virtuali per il panorama SAP più grande in account di archiviazione diversi per evitare di raggiungere i limiti di questi account di archiviazione. Questo è un lavoro noioso quando si parla di alcune centinaia di macchine virtuali con più di mille dischi rigidi virtuali.

Poiché l'utilizzo dell'archiviazione standard per le distribuzioni DBMS in combinazione con un carico di lavoro SAP non è consigliato, i riferimenti e i consigli per l'archiviazione standard sono limitati a questo breve [articolo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Per evitare il lavoro amministrativo di pianificazione e distribuzione dei dischi rigidi virtuali tra diversi account di archiviazione di Azure, Microsoft ha introdotto i [dischi gestiti](https://azure.microsoft.com/services/managed-disks/) di Azure nel 2017.To avoid the administrative work of planning and deploying VHDs across different Azure storage accounts, Microsoft introduced Azure Managed Disks in 2017. I dischi gestiti sono disponibili per l'archiviazione standard e l'archiviazione Premium. I principali vantaggi dei dischi gestiti rispetto ai dischi non gestiti sono:

- Per i dischi gestiti, Azure distribuisce automaticamente i diversi dischi rigidi virtuali tra diversi account di archiviazione in fase di distribuzione. In questo modo, i limiti dell'account di archiviazione per il volume di dati, la velocità effettiva di I/O e le operazioni di I/O al secondo non vengono raggiunti.
- Usando i dischi gestiti, Archiviazione di Azure rispetta i concetti dei set di disponibilità di Azure.Using managed disks, Azure Storage honors the concepts of Azure availability sets. Se la macchina virtuale fa parte di un set di disponibilità di Azure, il disco rigido virtuale di base e il disco collegato di una macchina virtuale vengono distribuiti in domini di errore e di aggiornamento diversi.


> [!IMPORTANT]
> Dati i vantaggi dei dischi gestiti di Azure, è consigliabile usare i dischi gestiti di Azure per le distribuzioni DBMS e SAP in generale.
>

Per eseguire la conversione da dischi non gestiti a dischi gestiti, vedere:

- [Convertire una macchina virtuale Windows da dischi non gestiti a dischi gestiti.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti.](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi dati
Quando si montano i dischi nelle macchine virtuali, è possibile scegliere se il traffico di I/O tra la macchina virtuale e i dischi che si trovano in Archiviazione di Azure viene memorizzato nella cache. Lo storage standard e premium utilizza due tecnologie diverse per questo tipo di cache.

Le raccomandazioni seguenti presuppongono queste caratteristiche di I/O per il DBMS standard:

- Si tratta principalmente di un carico di lavoro di lettura rispetto ai file di dati di un database. Queste letture sono fondamentali per le prestazioni del sistema DBMS.
- La scrittura sui file di dati avviene in burst in base a checkpoint o a un flusso costante. Nella media di un giorno, ci sono meno scritture rispetto alle letture. Di fronte alle letture dai file di dati, queste scritture sono asincrone e non contengono transazioni utente.
- Esistono pochissime operazioni di lettura dal file registro transazioni e dal file di rollforward. Le eccezioni sono operazioni di I/O di grandi dimensioni quando si eseguono backup del log delle transazioni.
- Il carico principale sui file di log di transazione o di ripetizione è la scrittura. A seconda della natura del carico di lavoro, è possibile avere I/O di dimensioni ridotte come 4 KB o, in altri casi, dimensioni di I/O pari o superiori a 1 MB.
- Tutte le scritture devono essere rese persistenti su disco in modo affidabile.

Per l'archiviazione standard, i tipi di cache possibili sono:

* nessuno
* Lettura
* Lettura/Scrittura

Per ottenere prestazioni coerenti e deterministiche, impostare la memorizzazione nella cache nell'archiviazione standard per tutti i dischi che contengono file di dati correlati a DBMS, file di log e di ripristino e tablespace su **NONE**. La memorizzazione nella cache del disco rigido virtuale di base può rimanere con l'impostazione predefinita.

Per l'archiviazione Premium, esistono le seguenti opzioni di memorizzazione nella cache:

* nessuno
* Lettura
* Lettura/Scrittura
* Nessuno: acceleratore di scrittura, che è solo per le macchine virtuali di serie M di Azure
* Acceleratore di lettura e scrittura, che è solo per le macchine virtuali di Azure M-SeriesRead - Write Accelerator, which is only for Azure M-Series VMs

Per l'archiviazione Premium, è consigliabile usare la **memorizzazione nella cache** in lettura per i file di dati del database SAP e scegliere **Nessuna memorizzazione nella cache per i dischi dei file di log**.

Per le distribuzioni di serie M, è consigliabile usare Acceleratore di scrittura di Azure per la distribuzione DBMS. Per informazioni dettagliate, restrizioni e distribuzione di Azure Write Accelerator, vedere [Abilitare L'acceleratore](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)di scrittura .


### <a name="azure-nonpersistent-disks"></a>Dischi non persistenti di AzureAzure nonpersistent disks
Le macchine virtuali di Azure offrono dischi non persistenti dopo la distribuzione di una macchina virtuale. Nel caso di riavvio di una macchina virtuale, tutto il contenuto in tali unità viene cancellato. Si tratta di un dato di fatto che i file di dati e file di log e di ripristino dei database non devono in nessun caso essere situati in quelle unità non persistenti. Potrebbero esserci eccezioni per alcuni database, in cui queste unità non persistenti potrebbero essere adatte per tabledb tempdb e tablespace temporanei. Evitare di usare queste unità per le macchine virtuali Serie A perché tali unità non persistenti sono limitate in velocità effettiva con tale famiglia di macchine virtuali. 

Per altre informazioni, vedere Informazioni sull'unità temporanea nelle macchine virtuali Windows in Azure.For more information, see [Understand the temporary drive on Windows VMs in Azure.](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![WINDOWS][Logo_Windows] WINDOWS
>
> L'unità D in una macchina virtuale di Azure è un'unità non persistente, supportata da alcuni dischi locali nel nodo di calcolo di Azure.Drive D in an Azure VM is a nonpersisted drive, which is backed by some local disks on the Azure compute node. Poiché non è persistente, tutte le modifiche apportate al contenuto nell'unità D vengono perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory create e le applicazioni installate.
>
> ![Linux][Logo_Linux] Linux
>
> Le macchine virtuali di Azure Linux montano automaticamente un'unità in /mnt/resource supportata da dischi locali nel nodo di calcolo di Azure.Linux Azure VMs automatically mount a drive at /mnt/resource that's a nonpersisted drive backed by local disks on the Azure compute node. Poiché non è persistente, tutte le modifiche apportate al contenuto in /mnt/resource vengono perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory create e le applicazioni installate.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resilienza di Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure archivia il disco rigido virtuale di base, con il sistema operativo e i dischi o i BLOB collegati, in almeno tre nodi di archiviazione separati. Questo tipo di archiviazione è denominato archiviazione con ridondanza locale (LRS). LRS is the default for all types of storage in Azure.

Esistono altri metodi di ridondanza. Per altre informazioni, vedere Replica di [Archiviazione di Azure.For](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)more information, see Azure Storage replication .

> [!NOTE]
>L'archiviazione Premium è il tipo di archiviazione consigliato per le macchine virtuali DBMS e i dischi in cui sono archiviati i file di log e di ripristino del database. L'unico metodo di ridondanza disponibile per l'archiviazione premium è LRS. Di conseguenza, è necessario configurare i metodi di database per abilitare la replica dei dati del database in un'altra area di Azure o in un'altra zona di disponibilità. I metodi di database includono SQL Server Always On, Oracle Data Guard e HANA System Replication.


> [!NOTE]
> Per le distribuzioni DBMS, l'uso di archiviazione con ridondanza geografica (GRS) non è consigliato per l'archiviazione standard. GRS influisce notevolmente sulle prestazioni e non rispetta l'ordine di scrittura tra diversi dischi rigidi virtuali collegati a una macchina virtuale. Se non si rispetta l'ordine di scrittura tra dischi rigidi virtuali diversi, è possibile che i database non siano coerenti sul lato di destinazione della replica. Questa situazione si verifica se i file di database e di log e di ripristino vengono distribuiti su più dischi rigidi virtuali, come avviene in genere sul lato VM di origine.



## <a name="vm-node-resiliency"></a>Resilienza del nodo della VM
Azure offre diversi contratti di servizio diversi per le macchine virtuali. Per ulteriori informazioni, vedere la versione più recente del [servizio directory virtuale per le macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Poiché il livello DBMS è in genere fondamentale per la disponibilità in un sistema SAP, è necessario comprendere i set di disponibilità, le zone di disponibilità e gli eventi di manutenzione. Per altre informazioni su questi concetti, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e Gestire la disponibilità delle macchine virtuali Linux in Azure.For more information on these concepts, see Manage the availability of Windows virtual machines in Azure and Manage the availability of Linux virtual machines in [Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)

La raccomandazione minima per gli scenari DBMS di produzione con un carico di lavoro SAP consiste nel:The minimum recommendation for production DBMS scenarios with an SAP workload is to:

- Distribuire due macchine virtuali in un set di disponibilità separato nella stessa area di Azure.Deploy two VMs in a separate availability set in the same Azure region.
- Eseguire queste due macchine virtuali nella stessa rete virtuale di Azure e disporre di schede di interfaccia di rete collegate dalle stesse subnet.
- Usare i metodi di database per mantenere un hot standby con la seconda macchina virtuale. I metodi possono essere SQL Server Always On, Oracle Data Guard o replica di sistema HANA.

È anche possibile distribuire una terza macchina virtuale in un'altra area di Azure e usare gli stessi metodi di database per fornire una replica asincrona in un'altra area di Azure.You also can deploy a third VM in another Azure region and use the same database methods to supply an asynchronous replica in another Azure region.

Per informazioni su come configurare i set di disponibilità di Azure, vedere [questa esercitazione.](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)



## <a name="azure-network-considerations"></a>Considerazioni sulla rete di AzureAzure network considerations
Nelle distribuzioni SAP su larga scala usare il blueprint del data center virtuale di [Azure.](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) Utilizzarlo per la configurazione della rete virtuale e le autorizzazioni e le assegnazioni di ruolo a diverse parti dell'organizzazione.

Queste procedure consigliate sono il risultato di centinaia di distribuzioni di clienti:These best practices are the result of hundreds of customer deployments:

- Le reti virtuali in cui viene distribuita l'applicazione SAP non hanno accesso a Internet.The virtual networks the SAP application is deployed into don't have access to the internet.
- Le macchine virtuali del database vengono eseguite nella stessa rete virtuale del livello applicazione.
- Le macchine virtuali all'interno della rete virtuale hanno un'allocazione statica dell'indirizzo IP privato. Per altre informazioni, vedere Tipi di indirizzi IP e metodi di allocazione in Azure.For more information, see [IP address types and allocation methods in Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
- Le restrizioni di routing da e verso le macchine virtuali DBMS *non* sono impostate con i firewall installati nelle macchine virtuali DBMS locali. Al contrario, il routing del traffico viene definito con i gruppi di sicurezza di [rete (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Per separare e isolare il traffico verso la macchina virtuale DBMS, assegnare schede di interfaccia di rete diverse alla macchina virtuale. Ogni scheda di interfaccia di rete ottiene un indirizzo IP diverso e ogni scheda di interfaccia di rete viene assegnata a una subnet di rete virtuale diversa. Ogni subnet ha regole del gruppo di sicurezza di rete diverse. L'isolamento o la separazione del traffico di rete è una misura per il routing. Non viene utilizzato per impostare le quote per la velocità effettiva della rete.

> [!NOTE]
> Assegnare indirizzi IP statici tramite Azure significa assegnarli a singole schede di interfaccia di rete virtuali. Non assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure, ad esempio Backup di Azure, si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria è impostata su DHCP e non su indirizzi IP statici. Per altre informazioni, vedere [Risolvere i problemi di backup delle macchine virtuali](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)di Azure.For more information, see Troubleshoot Azure virtual machine backup . Per assegnare più indirizzi IP statici a una macchina virtuale, assegnare più schede di interfaccia di rete virtuali a una macchina virtuale.
>


> [!IMPORTANT]
> La configurazione delle [appliance virtuali](https://azure.microsoft.com/solutions/network-appliances/) di rete nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un sistema SAP NetWeaver, Hybris o S/4HANA non è supportata. Questa restrizione è per motivi di funzionalità e prestazioni. Il percorso di comunicazione tra il livello dell'applicazione SAP e il livello DBMS deve essere diretto. La restrizione non include le regole del gruppo di [sicurezza dell'applicazione (ASG) e](https://docs.microsoft.com/azure/virtual-network/security-overview) del gruppo di sicurezza di rete se tali regole ASG e del gruppo di sicurezza di rete consentono un percorso di comunicazione diretto. 
>
> Altri scenari in cui le appliance virtuali di rete non sono supportate sono:Other scenarios where network virtual appliances aren't supported are in:
>
> * Percorsi di comunicazione tra macchine virtuali di Azure che rappresentano nodi del cluster Linux Pacemaker e dispositivi SBD come descritto in [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server per applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Percorsi di comunicazione tra le macchine virtuali di Azure e il server dei file di scalabilità orizzontale di Windows Server (SOFS) configurato come descritto in [Cluster an SAP ASCS/SCS instance in un cluster di failover di Windows utilizzando una condivisione file in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) 
>
> Le appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra due partner di comunicazione. Possono inoltre limitare la velocità effettiva nei percorsi critici tra il livello dell'applicazione SAP e il livello DBMS. In alcuni scenari dei clienti, le appliance virtuali di rete possono causare un errore nei cluster Linux pacemaker.In some customer scenarios, network virtual appliances can cause Pacemaker Linux clusters to fail. Si tratta di casi in cui le comunicazioni tra i nodi del cluster Linux Pacemaker comunicano al dispositivo SBD tramite un'appliance virtuale di rete.
>

> [!IMPORTANT]
> Un'altra progettazione *non* supportata è la separazione del livello dell'applicazione SAP e del livello DBMS in diverse reti virtuali di Azure non sottoposte a [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra loro. È consigliabile separare il livello applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure anziché reti virtuali di Azure diverse. 
>
> Se si decide di non seguire la raccomandazione e di separare invece i due livelli in reti virtuali diverse, è necessario eseguire il [peered delle](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)due reti virtuali. 
>
> Tenere presente che il traffico di rete tra due reti virtuali di Azure [con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) è soggetto a costi di trasferimento. Enorme volume di dati che consiste di molti terabyte viene scambiato tra il livello dell'applicazione SAP e il livello DBMS. È possibile accumulare costi notevoli se il livello dell'applicazione SAP e il livello DBMS sono separati tra due reti virtuali di Azure con peering.

Usare due macchine virtuali per la distribuzione di tipo DBMS di produzione all'interno di un set di disponibilità di Azure.Use two VMs for your production DBMS deployment within an Azure availability set. Utilizzare anche il routing separato per il livello dell'applicazione SAP e il traffico di gestione e operazioni verso le due macchine virtuali DBMS. Vedere l'immagine seguente:

![Diagramma di due macchine virtuali in due subnet](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usare Azure Load Balancer per reindirizzare il trafficoUse Azure Load Balancer to redirect traffic
L'uso di indirizzi IP virtuali privati usati in funzionalità come SQL Server Always On o HANA System Replication richiede la configurazione di un servizio di bilanciamento del carico di Azure.The use of private virtual IP addresses used in functionalities like SQL Server Always On or HANA System Replication requires the configuration of an Azure load balancer. Il servizio di bilanciamento del carico usa le porte probe per determinare il nodo DBMS attivo e instradare il traffico esclusivamente a tale nodo di database attivo. 

Se si verifica un failover del nodo del database, non è necessario riconfigurare l'applicazione SAP. Al contrario, le architetture delle applicazioni SAP più comuni si riconnettono all'indirizzo IP virtuale privato. Nel frattempo, il servizio di bilanciamento del carico reagisce al failover del nodo reindirizzando il traffico contro l'indirizzo IP virtuale privato al secondo nodo.

Azure offre due SKU di [bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)diversi: uno SKU di base e uno SKU standard. A meno che non si desideri eseguire la distribuzione tra le zone di disponibilità di Azure, lo SKU di bilanciamento del carico di base non funziona correttamente.

Il traffico tra le macchine virtuali DBMS e il livello dell'applicazione SAP viene sempre instradato attraverso il servizio di bilanciamento del carico? La risposta dipende da come si configura il bilanciamento del carico. 

A questo punto, il traffico in ingresso verso la macchina virtuale DBMS viene sempre instradato tramite il servizio di bilanciamento del carico. La route del traffico in uscita dalla macchina virtuale DBMS alla macchina virtuale a livello di applicazione dipende dalla configurazione del servizio di bilanciamento del carico. 

Il bilanciamento del carico offre un'opzione DirectServerReturn. Se questa opzione è configurata, il traffico diretto dalla macchina virtuale DBMS al livello dell'applicazione SAP *non* viene instradato tramite il servizio di bilanciamento del carico. Al contrario, va direttamente al livello dell'applicazione. Se DirectServerReturn non è configurato, il traffico restituito al livello dell'applicazione SAP viene instradato tramite il servizio di bilanciamento del carico.

È consigliabile configurare DirectServerReturn in combinazione con i servizi di bilanciamento del carico posizionati tra il livello dell'applicazione SAP e il livello DBMS. Questa configurazione riduce la latenza di rete tra i due livelli.

Per un esempio di come configurare questa configurazione con SQL Server Always On, vedere Configurare un listener ILB per i gruppi di disponibilità AlwaysOn in Azure.For an example of how to set up this configuration with SQL Server Always On, see [Configure an ILB listener for Always On availability groups in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)

Se si usano modelli JSON GitHub pubblicati come riferimento per le distribuzioni dell'infrastruttura SAP in Azure, studiare questo [modello per un sistema SAP a 3 livelli.](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md) In questo modello è inoltre possibile visualizzare le impostazioni corrette per il servizio di bilanciamento del carico.

### <a name="azure-accelerated-networking"></a>Rete accelerata di Azure
Per ridurre ulteriormente la latenza di rete tra macchine virtuali di Azure, è consigliabile scegliere Rete accelerata di Azure.To further reduce network latency between Azure VMs, we recommend that you choose [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Utilizzarlo quando si distribuiscono macchine virtuali di Azure per un carico di lavoro SAP, in particolare per il livello dell'applicazione SAP e il livello DBMS SAP.

> [!NOTE]
> Non tutti i tipi di VM supportano la rete accelerata. Nell'articolo precedente sono elencati i tipi di VM che supportano Accelerated Networking.
>

---
> ![WINDOWS][Logo_Windows] WINDOWS
>
> Per informazioni su come distribuire macchine virtuali con Accelerated Networking per Windows, vedere [Creare una macchina virtuale Windows con Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Per altre informazioni sulla distribuzione Linux, vedere [Creare una macchina virtuale Linux con Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> Nel caso di SUSE, Red Hat e Oracle Linux, la funzionalità di Rete accelerata è supportata nelle versioni recenti. Le versioni precedenti come SLES 12 SP2 o RHEL 7.2 non supportano la rete accelerata di Azure.
>


## <a name="deployment-of-host-monitoring"></a>Distribuzione del monitoraggio dell'host
Per l'uso in produzione di applicazioni SAP nelle macchine virtuali di Azure, SAP richiede la possibilità di ottenere i dati di monitoraggio dell'host dagli host fisici che eseguono le macchine virtuali di Azure.For production use of SAP applications in Azure virtual machines, SAP requires the ability to get host monitoring data from the physical hosts that run the Azure virtual machines. È necessario un livello di patch dell'agente host SAP specifico che abilita questa funzionalità in SAPOSCOL e nell'agente host SAP. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per ulteriori informazioni sulla distribuzione dei componenti che forniscono dati host a SAPOSCOL e SAP Host Agent e sulla gestione del ciclo di vita di tali componenti, vedere la Guida alla [distribuzione][deployment-guide].


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su un particolare DBMS, vedere:For more information on a particular DBMS, see:

- [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sqlserver.md)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_oracle.md)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_ibm.md)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sapase.md)
- [Distribuzione di SAP MaxDB, liveCache e server di contenuti in Azure](dbms_guide_maxdb.md)
- [Guida operativa a SAP HANA in Azure](hana-vm-operations.md)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](sap-hana-availability-overview.md)
- [Guida di backup per SAP HANA nelle macchine virtuali di AzureBackup guide for SAP HANA on Azure virtual machines](sap-hana-backup-guide.md)

