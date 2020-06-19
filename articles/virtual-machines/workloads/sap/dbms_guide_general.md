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
ms.openlocfilehash: 581efde3128294a326bdfd08e622a8dcabe5784d
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84232642"
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

Questa guida fa parte della documentazione su come implementare e distribuire software SAP in Microsoft Azure. Prima di leggere questa guida, vedere la [Guida alla pianificazione e all'implementazione][planning-guide]. Questo documento illustra gli aspetti generici della distribuzione di sistemi DBMS correlati a SAP in macchine virtuali (VM) di Microsoft Azure tramite le funzionalità dell'infrastruttura distribuita come servizio (IaaS) di Azure.

Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP su piattaforme specifiche.

Questo documento introduce considerazioni sull'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. I sistemi DBMS specifici vengono trattati nei capitoli successivi di questo documento.

## <a name="definitions"></a>Definizioni
Nel documento vengono usati i termini seguenti:

* **IaaS**: Infrastructure as a Service, Infrastruttura distribuita come servizio.
* **PaaS**: Platform as a Service, Piattaforma distribuita come servizio.
* **SaaS**: Software as a Service, Software come un servizio.
* **Componente SAP**: singola applicazione SAP, ad esempio ECC (ERP Central Component), BW (Business Warehouse), Solution Manager o EP (Enterprise Portal). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* **Ambiente SAP**: raggruppamento logico di uno o più componenti SAP per l'esecuzione di una funzione aziendale, ad esempio sviluppo, controllo della qualità, training, ripristino di emergenza o produzione.
* **Panorama applicativo SAP**: questo termine si riferisce a tutte le risorse SAP presenti nell'ambiente IT di un cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non di produzione.
* **Sistema SAP**: combinazione di un livello DBMS e di un livello applicazione, ad esempio di un sistema di sviluppo SAP ERP, un sistema di test SAP Business Warehouse o un sistema di produzione SAP CRM. Nelle distribuzioni di Azure, la divisione di questi due livelli tra l'istanza locale e Azure non è supportata. Di conseguenza, un sistema SAP deve essere distribuito o in locale o in Azure. È possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale.
* **Cross-premise**: indica uno scenario in cui le macchine virtuali vengono distribuite a una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. 

    La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Con questa estensione, le VM possono far parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server ed eseguire servizi in queste macchine virtuali, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è lo scenario più comune in uso per la distribuzione di risorse SAP in Azure. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale e sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che queste macchine virtuali siano parte di un dominio locale e di Active Directory/LDAP. 
>
> Nelle versioni precedenti della documentazione sono stati citati gli scenari IT ibridi. Il termine *ibrido* indica la presenza di una connettività cross-premise tra l'istanza locale e Azure. In questo caso, ibrido indica anche che le macchine virtuali in Azure fanno parte dell'istanza locale di Active Directory.
>
>

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni DBMS a disponibilità elevata. Nel caso dei documenti correlati a SAP, lo scenario cross-premise è basato sulla connettività [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privata o da sito a sito e sul panorama applicativo SAP distribuito tra l'istanza locale e Azure.

## <a name="resources"></a>Risorse
Sono disponibili altri articoli sul carico di lavoro SAP in Azure. Iniziare da [Introduzione a un carico di lavoro SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e scegliere l'area di interesse.

Le note SAP seguenti sono correlate a SAP in Azure riguardo all'area coperta in questo documento.

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: Prodotti e tipi di macchine virtuali di Azure supportati |
| [2015553] |SAP in Microsoft Azure: Prerequisiti di supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: Monitoraggio avanzato |
| [2191498] |SAP in Linux con Azure: Monitoraggio avanzato |
| [2039619] |Applicazioni SAP in Microsoft Azure che usano Oracle Database: prodotti e versioni supportate |
| [2233094] |DB6: applicazioni SAP in Azure che usano IBM DB2 per Linux, UNIX e Windows: Informazioni aggiuntive |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [2002167] |Red Hat Enterprise Linux 7.x: Installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di scambio per Linux |
| [2171857] |Oracle Database 12c: supporto per file system in Linux |
| [1114181] |Oracle Database 11g: supporto per file system in Linux |


Per informazioni su tutte le note SAP per Linux, vedere la [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

È necessaria una conoscenza pratica dell'architettura Microsoft Azure e di come le macchine virtuali di Microsoft Azure siano state distribuite e gestite. Per altre informazioni, vedere la [documentazione di Azure](https://docs.microsoft.com/azure/).

In generale, l'installazione e la configurazione di Windows, Linux e DBMS sono sostanzialmente uguali a quanto avviene in qualsiasi macchina virtuale o computer bare metal installato in locale. Alcune decisioni relative all'architettura e all'implementazione della gestione del sistema presentano differenze in caso di tecnologia IaaS di Azure. Questo documento illustra le differenze specifiche relative all'architettura e alla gestione del sistema che è necessario conoscere quando si usa la tecnologia IaaS di Azure.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS
Per seguire questo capitolo, è necessario leggere e conoscere le informazioni presenti in [questo capitolo][deployment-guide-3] della [Guida alla distribuzione][deployment-guide]. Prima di leggere questo capitolo, è necessario comprendere e conoscere le diverse serie di macchine virtuali e le differenze tra archiviazione Standard e Premium. 

Per informazioni sull'archiviazione di Azure per le macchine virtuali di Azure, vedere:

- [Introduzione ai dischi gestiti per le macchine virtuali Windows di Azure](../../windows/managed-disks-overview.md).
- [Introduzione ai dischi gestiti per le macchine virtuali Linux di Azure](../../linux/managed-disks-overview.md).

In una configurazione di base, è consigliabile di solito usare una struttura di distribuzione in cui il sistema operativo, il sistema DBMS ed eventuali file binari SAP siano separati dai file di database. Nei sistemi SAP in esecuzione in macchine virtuali di Azure, il disco rigido virtuale di base, o disco, deve preferibilmente essere installato con il sistema operativo, i file eseguibili del sistema di gestione di database e i file eseguibili SAP. 

I file di dati e di log DBMS vengono archiviati in un'archiviazione Standard o Premium. Vengono archiviati in dischi separati e collegati come dischi logici all'immagine della macchina virtuale del sistema operativo Azure originale. Per le distribuzioni di Linux, sono documentate diverse raccomandazioni, specialmente per SAP HANA.

Quando si pianifica il layout del disco, è necessario trovare il migliore equilibrio tra gli elementi seguenti:

* Numero di file di dati.
* Numero di dischi contenenti i file.
* Quote di operazioni di I/O al secondo di un singolo disco.
* Velocità effettiva dei dati per disco.
* Numero di dischi dati aggiuntivi possibili per dimensioni di VM.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
* La latenza che i diversi tipi di Archiviazione di Azure possono offrire.
* Contratti di servizio per macchine virtuali.

Azure applica una quota di operazioni di I/O al secondo per disco dati. I dischi ospitati in archiviazione Standard e quelli ospitati in archiviazione Premium hanno quote diverse. Anche la latenza delle operazioni di I/O è diversa tra i due tipi di archiviazione. L'archiviazione Premium offre una migliore latenza delle operazioni di I/O. 

A ognuno dei diversi tipi di macchine virtuali è possibile collegare un numero limitato di dischi dati. Un'altra restrizione è data dal fatto che solo determinati tipi di macchine virtuali possono usare Archiviazione Premium di Azure. In genere, si decide di usare un determinato tipo di macchina virtuale in base ai requisiti di CPU e memoria. È anche possibile considerare i requisiti di operazioni di I/O al secondo, latenza e velocità effettiva del disco che in genere vengono ridimensionati con il numero di dischi o il tipo di dischi di Archiviazione Premium. Il numero di operazioni di I/O al secondo e la velocità effettiva che deve essere raggiunta da ogni disco possono determinare le dimensioni del disco, soprattutto con Archiviazione Premium.

> [!NOTE]
> Per le distribuzioni DBMS, è consigliabile l'uso di Archiviazione Premium per file di dati, file registro transazioni o file di rollforward. Non è importante se si vuole distribuire sistemi di produzione o di non produzione.

> [!NOTE]
> Per trarre vantaggio dall'unico [contratto di servizio per macchina virtuale singola](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), tutti i dischi collegati devono essere di tipo Archiviazione Premium, che include il disco rigido virtuale di base.

> [!NOTE]
> Non è possibile ospitare i file principali di database, ad esempio file di log e dati, dei database SAP su hardware di archiviazione situati in data center di terze parti con risorse condivise adiacenti ai data center di Azure. Per i carichi di lavoro SAP solo l'archiviazione che è rappresentata come servizio nativo di Azure è supportata per i file di dati e i file registro transazioni dei database SAP.

La posizione dei file di database, dei file registro e di rollforward e il tipo di archiviazione di Azure usati sono definiti in base ai requisiti di operazioni di I/O al secondo, latenza e velocità effettiva. Per avere un numero di operazioni di I/O al secondo sufficiente, potrebbe essere necessario usare più dischi oppure usare un disco di archiviazione Premium di dimensioni maggiori. Se si usano più dischi, si creerebbe una striscia software tra i dischi che contengono i file di dati o i file registro e di rollforward. In questi casi, i contratti di servizio per le operazioni di I/O al secondo e la velocità effettiva dei dischi di archiviazione Premium sottostanti o il numero massimo raggiungibile di operazioni di I/O al secondo dei dischi di archiviazione Standard sono cumulativi per il set di striping risultante.

Come già indicato, se la richiesta di operazioni di I/O al secondo supera ciò che può sostenere un singolo disco rigido virtuale, è possibile bilanciare il numero di operazioni di I/O al secondo necessarie per i file di database in più dischi rigidi virtuali. Il modo più semplice per distribuire il carico delle operazioni di I/O al secondo tra più dischi consiste nel creare una striscia software su dischi diversi. Memorizzare quindi una serie di file di dati del sistema DBMS SAP nei LUN ricavati dalla striscia software. Il numero di dischi nella striscia si basa sulle richieste di operazioni di I/O al secondo, sulle richieste di velocità effettiva dei dischi e sulle richieste di volume.


---
> ![Windows][Logo_Windows] Windows
>
> È consigliabile usare spazi di archiviazione di Windows per creare set di striping tra più dischi rigidi virtuali di Azure. Usare almeno Windows Server 2012 R2 o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Per compilare un RAID software in Linux sono supportati solo MDADM e Logical Volume Manager (LVM). Per altre informazioni, vedere:
>
> - [Configurare RAID software su Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Poiché Archiviazione di Azure conserva tre immagini dei dischi rigidi virtuali, non è utile configurare una ridondanza durante lo striping. È sufficiente configurare lo striping in modo che le operazioni di I/O vengano distribuite su dischi rigidi virtuali diversi.
>

### <a name="managed-or-nonmanaged-disks"></a>Dischi gestiti o non gestiti
Un account di archiviazione di Azure è un costrutto amministrativo ed è anche soggetto a limitazioni. Le limitazioni sono diverse tra gli account di archiviazione Standard e gli account di archiviazione Premium. Per altre informazioni su funzionalità e limitazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Per l'archiviazione Standard, tenere presente che è previsto un limite per il numero di operazioni di I/O al secondo per ogni account di archiviazione. Vedere la riga che contiene **Total Request Rate** (Frequenza di richiesta totale) nell'articolo [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Esiste inoltre un limite iniziale per il numero di account di archiviazione per ogni sottoscrizione di Azure. Bilanciare i dischi rigidi virtuali per il panorama applicativo SAP di maggiori dimensioni tra i diversi account di archiviazione per evitare di raggiungere i limiti di questi account di archiviazione. Si tratta di un lavoro noioso quando si parla di alcune centinaia di macchine virtuali con più di mille dischi rigidi virtuali.

Poiché non è consigliabile usare l'archiviazione Standard per le distribuzioni DBMS in combinazione con un carico di lavoro SAP, i riferimenti e le raccomandazioni per l'archiviazione Standard sono limitati a questo breve [articolo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Per evitare il lavoro amministrativo di pianificazione e distribuzione di dischi rigidi virtuali nei diversi account di archiviazione di Azure, nel 2017 Microsoft ha introdotto il servizio [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi gestiti sono disponibili per l'archiviazione Standard e per l'archiviazione Premium. I vantaggi principali dei dischi gestiti rispetto ai dischi non gestiti sono i seguenti:

- Per i dischi gestiti, Azure distribuisce automaticamente i diversi dischi rigidi virtuali tra diversi account di archiviazione in fase di distribuzione. In questo modo, si evita di raggiungere i limiti di un account di archiviazione in termini di volume di dati, velocità effettiva di I/O e operazioni di I/O al secondo.
- Con il servizio Managed Disks, Archiviazione di Azure rispetta i concetti di set di disponibilità di Azure. Se la macchina virtuale fa parte di un set di disponibilità di Azure, il disco rigido virtuale di base e il disco collegato di una macchina virtuale vengono distribuiti in domini di errore e di aggiornamento diversi.


> [!IMPORTANT]
> Dati i vantaggi di Azure Managed Disks, è consigliabile usare questo servizio in generale per le distribuzioni DBMS e le distribuzioni SAP.
>

Per eseguire la conversione da dischi non gestiti a dischi gestiti, vedere:

- [Convertire i dischi non gestiti di una macchina virtuale Windows in dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi dati
Quando si montano dischi su macchina virtuale, è possibile scegliere se memorizzare nella cache il traffico di I/O tra la macchina virtuale e i dischi posizionati nella risorsa di archiviazione di Azure. Archiviazione Premium e Archiviazione Standard usano due tecnologie diverse per questo tipo di cache.

Le raccomandazioni seguenti presuppongono queste caratteristiche di I/O per il sistema DBMS standard:

- Si tratta principalmente di un carico di lavoro di lettura su file di dati di un database. Queste operazioni di lettura sono cruciali per le prestazioni per il sistema DBMS.
- La scrittura sui file di dati si verifica in burst in base ai checkpoint o a un flusso costante. In media in un giorno, si verificano meno operazioni di scrittura rispetto alle operazioni di lettura. Al contrario delle operazioni di lettura da file di dati, queste operazioni di scrittura sono asincrone e non contengono transazioni utente.
- Esistono pochissime operazioni di lettura dal file registro transazioni e dal file di rollforward. Fanno eccezione le operazioni di I/O di grandi dimensioni quando si eseguono i backup del log delle transazioni.
- Il carico principale nei file registro transazioni e di rollforward è costituito da operazioni di scrittura. A seconda della natura del carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB.
- Tutte le scritture devono essere salvate in modo permanente su un disco in modo affidabile.

Per l'archiviazione Standard, le opzioni di memorizzazione nella cache sono:

* nessuno
* Lettura
* Lettura/Scrittura

Per ottenere prestazioni coerenti e deterministiche, è consigliabile impostare la memorizzazione nella cache in Archiviazione Standard per tutti i dischi che contengono file di dati, file registro e di rollforward correlati a DBMS e spazi di tabella impostati su **NONE**. La memorizzazione nella cache del disco rigido virtuale di base può rimanere con l'impostazione predefinita.

Per l'archiviazione Premium, le opzioni di memorizzazione nella cache sono:

* nessuno
* Lettura
* Lettura/Scrittura
* Nessuna + Acceleratore di scrittura, disponibile solo per macchine virtuali serie M di Azure
* Lettura + Acceleratore di scrittura, disponibile solo per macchine virtuali serie M di Azure

Per l'archiviazione Premium, è consigliabile usare **Read caching for data files** (Caching di lettura per i file di dati) del database SAP e scegliere **No caching for the disks of log file(s)** (Nessun caching per i dischi dei file di log).

Per le distribuzioni di macchine serie M, è consigliabile usare Acceleratore di scrittura di Azure per la distribuzione DBMS. Per i dettagli, le limitazioni e la distribuzione dell'acceleratore di scrittura di Azure, vedere [Abilitare l'acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Dischi non persistenti di Azure
Le macchine virtuali di Azure offrono dischi non persistenti dopo la distribuzione di una macchina virtuale. In caso di riavvio di una macchina virtuale, tutto il contenuto delle unità viene cancellato. È un dato di fatto che i file di dati, i file registro e di rollforward dei database non devono mai essere posizionati in tali unità non persistenti. Potrebbero esserci delle eccezioni per alcuni database, dove queste unità non persistenti potrebbero essere adatte per gli spazi di tabella di tempdb e temp. Evitare di usare queste unità nelle macchine virtuali serie A poiché, con questa famiglia di macchine virtuali, le unità non persistenti sono limitate in termini di velocità effettiva. 

Per altre informazioni, vedere [Informazioni sull'unità temporanea per le macchine virtuali Windows in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> L'unità D in una macchina virtuale di Azure è un'unità non persistente supportata da alcuni dischi locali nel nodo di calcolo di Azure. Poiché non è persistente, tutte le modifiche apportate al contenuto nell'unità D andranno perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory create e le applicazioni installate.
>
> ![Linux][Logo_Linux] Linux
>
> Nelle macchine virtuali Linux di Azure viene montata automaticamente in /mnt/resource un'unità non persistente supportata da dischi locali nel nodo di calcolo di Azure. Poiché non è persistente, tutte le modifiche apportate al contenuto in /mnt/resource andranno perse al riavvio della macchina virtuale. Le modifiche includono i file archiviati, le directory create e le applicazioni installate.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resilienza di Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure archivia il disco rigido virtuale di base con il sistema operativo e i dischi collegati o i BLOB in almeno tre nodi di archiviazione separati. Questo tipo di archiviazione è denominato archiviazione con ridondanza locale (LRS). L'archiviazione con ridondanza locale è l'impostazione predefinita per tutti i tipi di archiviazione in Azure.

Sono disponibili altri metodi di ridondanza. Per altre informazioni, vedere [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>L'archiviazione Premium è il tipo di archiviazione consigliato per le macchine virtuali DBMS e i dischi che archiviano file di database, file registro e di rollforward. L'unico metodo di ridondanza disponibile per l'archiviazione Premium è l'archiviazione con ridondanza locale. Di conseguenza, è necessario configurare i metodi di database per abilitare la replica dei dati di database in un'altra area di Azure o in un'altra zona di disponibilità. I metodi di database includono SQL Server Always On, Oracle Data Guard e replica di sistema HANA.


> [!NOTE]
> Per le distribuzioni DBMS, non è consigliabile usare l'archiviazione con ridondanza geografica (GRS) per l'archiviazione Standard. L'archiviazione con ridondanza geografica influisce fortemente sulle prestazioni e non rispetta l'ordine di scrittura tra dischi rigidi virtuali diversi collegati a una macchina virtuale. Il fatto di non rispettare l'ordine di scrittura tra dischi rigidi virtuali diversi può facilmente portare alla creazione di database incoerenti sul lato della destinazione della replica. Questa situazione si verifica se i file di database, i file registro e di rollforward vengono distribuiti tra più dischi rigidi virtuali, come avviene in genere, sul lato della macchina virtuale di origine.



## <a name="vm-node-resiliency"></a>Resilienza del nodo della VM
Azure offre molti contratti di servizio per macchine virtuali. Per altre informazioni, vedere la versione più recente del [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Poiché il livello DBMS è in genere essenziale per la disponibilità in un sistema SAP, è necessario comprendere i set di disponibilità, le zone di disponibilità e gli eventi di manutenzione. Per altre informazioni su questi concetti, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gestire la disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

La raccomandazione di base per gli scenari DBMS di produzione con un carico di lavoro SAP è di:

- Distribuire due macchine virtuali in un set di disponibilità separato nella stessa area di Azure.
- Eseguire queste due macchine virtuali nella stessa rete virtuale di Azure e disporre di schede di interfaccia di rete collegate fuori dalle stesse subnet.
- Usare i metodi di database per mantenere un hot standby con la seconda macchina virtuale. I metodi possono essere SQL Server Always On, Oracle Data Guard o replica di sistema HANA.

È anche possibile distribuire una terza macchina virtuale in un'altra area di Azure e usare gli stessi metodi di database per offrire una replica asincrona in un'altra area di Azure.

Per informazioni su come configurare i set di disponibilità di Azure, vedere [questa esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerazioni sulla rete di Azure
Nelle distribuzioni di SAP su larga scala, usare il progetto di [data center virtuale di Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). È possibile usarlo per la configurazione della rete virtuale e le autorizzazioni e le assegnazioni di ruolo a diverse parti della propria organizzazione.

Queste procedure consigliate sono il risultato di centinaia di distribuzioni dei clienti:

- Le reti virtuali in cui viene distribuita l'applicazione SAP non hanno accesso a Internet.
- Le macchine virtuali del database vengono eseguite nella stessa rete virtuale del livello applicazione.
- Le macchine virtuali all'interno della rete virtuale hanno un'allocazione statica dell'indirizzo IP privato. Per altre informazioni, vedere [Tipi di indirizzi IP e metodi di allocazione in Azure](../../../virtual-network/public-ip-addresses.md).
- Le limitazioni del routing da e verso le macchine virtuali DBMS *non* vengono impostate con i firewall installati nelle macchine virtuali DBMS locali. Il routing del traffico viene invece definito con i [Gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Per separare e isolare il traffico verso la macchina virtuale DBMS, assegnare schede di interfaccia di rete diverse alla macchina virtuale. Ogni scheda di interfaccia di rete riceve un indirizzo IP diverso e viene assegnata a una subnet rete virtuale diversa. Ogni subnet ha regole NSG diverse. L'isolamento o la separazione del traffico di rete è una misura per il routing. Non consente di impostare quote per la velocità effettiva della rete.

> [!NOTE]
> Assegnare indirizzi IP statici tramite Azure significa assegnarli alle singole schede di interfaccia di rete virtuali. Non assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure, come Backup di Azure, si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria sia impostata su DHCP e non su indirizzi IP statici. Per altre informazioni, vedere [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Per assegnare più indirizzi IP statici a una macchina virtuale, occorre assegnare più schede di interfaccia di rete virtuali a una macchina virtuale.
>


> [!IMPORTANT]
> La configurazione di [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra il livello applicazione SAP e il livello DBMS di un sistema SAP NetWeaver, Hybris o basato su S/4HANA non è supportata. Questa restrizione si applica per motivi di funzionalità e prestazioni. Il percorso di comunicazione tra il livello dell'applicazione SAP e il livello DBMS deve essere diretto. La restrizione non include le [regole del gruppo di sicurezza delle applicazioni e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se queste regole consentono un percorso di comunicazione diretta. 
>
> Altri scenari in cui le appliance virtuali di rete non sono supportate sono:
>
> * Percorsi di comunicazione tra macchine virtuali di Azure che rappresentano i nodi del cluster Pacemaker di Linux e i dispositivi SBD, come descritto in [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Percorsi di comunicazione tra macchine virtuali di Azure e File server di scalabilità orizzontale (SOFS) di Windows Server configurati come descritto in [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Le appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra due partner di comunicazione. Possono anche limitare la velocità effettiva nei percorsi critici tra il livello dell'applicazione SAP e il livello DBMS. In alcuni scenari dei clienti, le appliance virtuali di rete possono causare errori dei cluster Linux Pacemaker. Si tratta di casi in cui le comunicazioni tra i nodi del cluster Linux Pacemaker e il relativo dispositivo SBD avvengono tramite un'appliance virtuale di rete.
>

> [!IMPORTANT]
> Un'altra struttura *non* supportata è la separazione del livello dell'applicazione SAP e del livello DBMS in reti virtuali Azure diverse non connesse tramite [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). È consigliabile separare il livello dell'applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure, anziché usare reti virtuali di Azure diverse. 
>
> Se si decide di non seguire la raccomandazione e separare i due livelli in reti virtuali diverse, le due reti virtuali devono essere connesse tramite [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tenere presente che il traffico di rete tra due reti virtuali di Azure connesse tramite [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) è soggetto a costi di trasferimento. Grandi volumi di dati dell'ordine di molti terabyte vengono scambiati tra il livello dell'applicazione SAP e il livello DBMS. Si possono originare costi sostanziali se il livello dell'applicazione SAP e il livello DBMS sono separati tra due reti virtuali di Azure connesse tramite peering.

Usare due macchine virtuali per la distribuzione DBMS di produzione all'interno di un set di disponibilità di Azure. Usare anche un routing separato per il livello dell'applicazione SAP e il traffico operativo e di gestione verso le due macchine virtuali DBMS. Vedere l'immagine seguente:

![Diagramma di due macchine virtuali in due subnet](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usare Azure Load Balancer per reindirizzare il traffico
L'uso di indirizzi IP virtuali privati usati in funzionalità quali SQL Server Always On o replica di sistema HANA richiede la configurazione di un'istanza di Azure Load Balancer. Il servizio di bilanciamento del carico usa le porte probe per determinare il nodo DBMS attivo e instradare il traffico esclusivamente verso quel nodo del database attivo. 

Se si verifica un failover del nodo del database, non è necessario riconfigurare l'applicazione SAP. Le architetture delle applicazioni SAP più comuni invece si riconnettono all'indirizzo IP virtuale privato. Nel frattempo, il servizio di bilanciamento del carico reagisce al failover del nodo reindirizzando il traffico nell'indirizzo IP virtuale privato verso il secondo nodo.

Azure offre due diversi [SKU di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): SKU Basic e SKU Standard. A meno che non si voglia distribuire in più zone di disponibilità di Azure, lo SKU Basic del servizio di bilanciamento del carico è adeguato.

Il traffico tra le macchine virtuali DBMS e il livello dell'applicazione SAP viene sempre instradato tramite il servizio di bilanciamento del carico? La risposta dipende da come si configura il bilanciamento del carico. 

A questo punto, il traffico in ingresso verso la macchina virtuale DBMS viene sempre instradato tramite il servizio di bilanciamento del carico. La route del traffico in uscita dalla macchina virtuale DBMS alla macchina virtuale del livello dell'applicazione dipende dalla configurazione del servizio di bilanciamento del traffico. 

Il bilanciamento del carico offre un'opzione DirectServerReturn. Se tale opzione è configurata, il traffico indirizzato dalla macchina virtuale DBMS al livello dell'applicazione SAP *non* viene instradato attraverso il servizio di bilanciamento del carico. Va direttamente al livello dell'applicazione. Se l'opzione DirectServerReturn non è configurata, il traffico di ritorno al livello dell'applicazione SAP viene instradato attraverso il servizio di bilanciamento del carico.

È consigliabile configurare l'opzione DirectServerReturn in combinazione con i servizi di bilanciamento del carico che si trovano tra il livello dell'applicazione SAP e il livello DBMS. Questa configurazione riduce la latenza di rete tra i due livelli.

Per un esempio di come configurare questa configurazione con SQL Server Always On, vedere [Configurare un listener ILB per gruppi di disponibilità Always On in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se si usano i modelli JSON di GitHub pubblicati come riferimento per le distribuzioni di infrastrutture SAP in Azure, studiare questo [modello per un sistema SAP a 3 livelli](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). In questo modello è anche possibile visualizzare le impostazioni corrette per il servizio di bilanciamento del carico.

### <a name="azure-accelerated-networking"></a>Rete accelerata di Azure
Per ridurre ulteriormente la latenza di rete tra le macchine virtuali di Azure, è consigliabile scegliere [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Usarla quando si distribuiscono macchine virtuali di Azure per un carico di lavoro SAP, in particolare per il livello dell'applicazione SAP e il livello DBMS di SAP.

> [!NOTE]
> Non tutti i tipi di macchine virtuali supportano la rete accelerata. L'articolo precedente elenca i tipi di macchine virtuali che supportano la rete accelerata.
>

---
> ![Windows][Logo_Windows] Windows
>
> Per informazioni su come distribuire le macchine virtuali con Rete accelerata in Windows, vedere l'articolo [Creare una macchina virtuale Windows con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Per altre informazioni sulla distribuzione Linux, vedere [Creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> Nel caso di SUSE, Red Hat e Oracle Linux, la funzionalità di Rete accelerata è supportata nelle versioni recenti. Le versioni precedenti, ad esempio SLES 12 SP2 o RHEL 7.2, non supportano la rete accelerata di Azure.
>


## <a name="deployment-of-host-monitoring"></a>Distribuzione del monitoraggio host
Per un uso in ambiente di produzione delle applicazioni SAP in macchine virtuali di Azure, SAP deve poter recuperare i dati di monitoraggio host dagli host fisici che eseguono le macchine virtuali di Azure. È necessario un livello di patch dell'agente host SAP specifico che abilita questa funzionalità in SAPOSCOL e nell'agente host SAP. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per altre informazioni sulla distribuzione di componenti che forniscono dati host a SAPOSCOL e all'agente host SAP e sulla gestione del ciclo di vita di tali componenti, vedere la [Guida alla distribuzione][deployment-guide].


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su un particolare DBMS, vedere:

- [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sqlserver.md)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_oracle.md)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_ibm.md)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sapase.md)
- [Distribuzione di SAP MaxDB, liveCache e server di contenuti in Azure](dbms_guide_maxdb.md)
- [Guida operativa a SAP HANA in Azure](hana-vm-operations.md)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](sap-hana-availability-overview.md)
- [Guida del backup di SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md)

