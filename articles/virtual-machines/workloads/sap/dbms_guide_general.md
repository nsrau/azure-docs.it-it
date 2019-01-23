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
ms.openlocfilehash: 87d3a44b01dff81242f935c7737bd170fe744536
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246875"
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

Questa guida fa parte della documentazione relativa all'implementazione e alla distribuzione del software SAP in Microsoft Azure. Prima di leggere questa guida, vedere la [guida alla pianificazione e all'implementazione][planning-guide]. Questo documento illustra gli aspetti generici della distribuzione di sistemi DBMS correlati a SAP in macchine virtuali (VM) di Microsoft Azure tramite le funzionalità dell'infrastruttura distribuita come servizio (IaaS) di Azure.

Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP su piattaforme specifiche.

Questo documento introduce considerazioni sull'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. I sistemi DBMS specifici vengono trattati nei capitoli successivi di questo documento.

## <a name="definitions-upfront"></a>Definizioni
Nel documento vengono usati i termini seguenti:

* IaaS: Infrastructure as a Service, Infrastruttura distribuita come servizio.
* PaaS: Platform as a Service, Piattaforma distribuita come servizio.
* SaaS: Software as a Service, Software come un servizio.
* Componente SAP: singola applicazione SAP, ad esempio ECC, BW, Solution Manager o EP. I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* Ambiente SAP: raggruppamento logico di uno o più componenti SAP per una funzione aziendale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
* Panorama applicativo SAP: questo termine si riferisce a tutte le risorse SAP presenti nell'ambiente IT di un cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
* Sistema SAP: combinazione del livello DBMS e del livello applicazione, costituito ad esempio da un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Nelle distribuzioni di Azure non è supportata la divisione di questi due livelli tra ambiente locale e Azure. Di conseguenza, un sistema SAP deve essere distribuito o in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale.
* Cross-premise: indica uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è probabilmente lo scenario più comune per la distribuzione di risorse SAP in Azure. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che queste VM siano parte di un dominio locale e di AD/LDAP. Nelle versioni precedenti della documentazione sono stati menzionati scenari IT ibridi, in cui il termine *ibrido* indica la presenza di una connettività cross-premise tra l'ambiente locale e Azure. In questo caso *ibrido* indica anche che le macchine virtuali in Azure fanno parte dell'istanza locale di Active Directory.
>
>

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni DBMS a disponibilità elevata. Nel caso dei documenti correlati a SAP, lo scenario cross-premise è basato sulla presenza di connettività [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privata o da sito a sito e sul fatto che il panorama applicativo SAP è distribuito tra l'ambiente locale e Azure.

## <a name="resources"></a>Risorse
Sono pubblicati vari articoli sul carico di lavoro SAP in Azure. Si consiglia di iniziare con [SAP workload on Azure - Get Started](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) (Introduzione ai carichi di lavoro SAP in Azure) e quindi di selezionare l'area di interesse.

Le note SAP seguenti sono correlate a SAP in Azure relativamente all'area coperta in questo documento:

| Numero della nota | Title |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: prodotti e tipi di macchine virtuali di Azure supportati. |
| [2015553] |SAP in Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: Enhanced Monitoring |
| [2191498] |SAP in Linux con Azure: monitoraggio avanzato |
| [2039619] |Applicazioni SAP in Microsoft Azure che usano il Oracle Database: prodotti e versioni supportate |
| [2233094] |DB6: informazioni aggiuntive sulle applicazioni SAP in Azure che usano IBM DB2 per Linux, UNIX e Windows |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [2002167] |Red Hat Enterprise Linux 7.x: installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di swapping per Linux |
| [2171857] |Oracle Database 12c: supporto per file system in Linux |
| [1114181] |Oracle Database 11g: supporto per file system in Linux |


Vedere anche il [wiki in SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contenente tutte le note SAP per Linux.

È necessario conoscere l'architettura Microsoft Azure e il funzionamento delle macchine virtuali di Microsoft Azure e sapere come queste vengono distribuite. È possibile trovare altre informazioni nella pagina [Documentazione di Microsoft Azure](https://docs.microsoft.com/azure/).

Per quanto riguarda la tecnologia IaaS, in generale l'installazione e la configurazione di Windows, Linux e DBMS sono sostanzialmente uguali a quanto avviene in qualsiasi macchina virtuale o computer bare metal installato in locale. Alcune decisioni relative all'architettura e all'implementazione della gestione del sistema, tuttavia, presentano differenze in caso di tecnologia IaaS di Azure. Lo scopo di questo documento è spiegare le differenze specifiche relative all'architettura e alla gestione del sistema che è necessario conoscere quando si usa la tecnologia IaaS di Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS
Per seguire questo capitolo, è necessario conoscere il contenuto di [questo][deployment-guide-3] capitolo della [guida alla distribuzione][deployment-guide]. Prima di leggere questo capitolo è necessario conoscere le diverse serie di macchine virtuali e le relative differenze, nonché le differenze tra [Archiviazione Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) e Standard di Azure.

Per quanto riguarda Archiviazione di Azure per macchine virtuali di Azure, è necessario avere familiarità con gli articoli seguenti:

- [Informazioni sull'archiviazione su dischi per le VM Windows di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [Informazioni sull'archiviazione su disco per le VM Linux di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds)

In una configurazione di base, si consiglia di solito di usare una struttura di distribuzione in cui il sistema operativo, il sistema DBMS ed eventuali file binari SAP siano separati dai file di database. Nei sistemi SAP in esecuzione in macchine virtuali di Azure, il disco rigido virtuale di base deve preferibilmente essere installato con il sistema operativo, i file eseguibili del sistema di gestione di database e i file eseguibili SAP. I file di dati e di log di DBMS vengono archiviati in Archiviazione di Azure (Standard o Premium) in dischi separati e collegati come dischi logici alla VM di Azure dell'immagine del sistema operativo originale. Specialmente nelle distribuzioni Linux possono essere disponibili raccomandazioni diverse documentate, in particolare per quanto riguarda SAP HANA.

Nella pianificazione del layout dei dischi è necessario trovare l'equilibrio ottimale tra gli elementi seguenti:

* Numero di file di dati.
* Numero di dischi contenenti i file.
* Quote di operazioni di I/O al secondo di un singolo disco.
* Velocità effettiva dei dati per disco.
* Numero di dischi dati aggiuntivi possibili per dimensioni di VM.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
* La latenza che i diversi tipi di Archiviazione di Azure possono offrire.
* Contratti di servizio per macchine virtuali

Azure applica una quota di operazioni di I/O al secondo per disco dati. I dischi ospitati in Archiviazione Standard di Azure e quelli ospitati in Archiviazione Premium hanno quote diverse. Anche la latenza delle operazioni di I/O è diversa tra i due tipi di archiviazione. Archiviazione Premium offre una latenza migliore. A ognuno dei diversi tipi di VM è possibile collegare un numero limitato di dischi dati. Un'altra restrizione è data dal fatto che solo determinati tipi di macchine virtuali possono usare Archiviazione Premium di Azure. Di conseguenza, la scelta di un determinato tipo di VM potrebbe non basarsi soltanto sui requisiti di CPU e di memoria, ma anche sui requisiti in termini di operazioni di I/O al secondo, latenza e velocità effettiva dei dischi che vengono in genere ridimensionati in base al numero dei dischi o al tipo di dischi di Archiviazione Premium. In particolare con Archiviazione Premium, le dimensioni di un disco possono essere determinate anche dal numero di operazioni di I/O al secondo e dalla velocità effettiva che deve essere raggiunta da ogni disco.

> [!NOTE]
> Per le distribuzioni DBMS, l'utilizzo di Archiviazione Premium per file di dati, file registro transazioni o file di rollforward è fortemente consigliato. In tal modo non è importante se si vuole distribuire ambienti di produzione o di non produzione.

> [!NOTE]
> Per trarre vantaggio dall'unico [contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) tutti i dischi collegati devono essere di tipo Archiviazione Premium di Azure, incluso il disco rigido virtuale di base.


> [!NOTE]
> Non può ospitare i file principali di database (file di log e dati) dei database SAP su hardware di archiviazione situati in data center di terze parti con risorse condivise adiacenti ai data center di Azure. Per il carico di lavoro SAP solo l'archiviazione che è rappresentata come servizio nativo di Azure è supportata per i file di log delle transazioni e dei dati dei database SAP.
> 

La posizione dei file di database, dei file registro e di rollforward e il tipo di Archiviazione di Azure usati devono essere definiti in base ai requisiti di operazioni di I/O al secondo, latenza e velocità effettiva. Per avere un numero di operazioni di I/O al secondo sufficiente, potrebbe essere necessario sfruttare più dischi oppure usare un disco di Archiviazione Premium di dimensioni maggiori. In caso di uso di più dischi, si creerebbe una striscia software tra i dischi che contengono i file di dati o i file registro/di rollforward. In questi casi, i contratti di servizio per le operazioni di I/O al secondo e la velocità effettiva dei dischi di Archiviazione Premium sottostanti o il numero massimo raggiungibile di operazioni di I/O al secondo di Archiviazione Standard di Azure sono cumulativi per il set di striping risultante.

Come già indicato, se la richiesta di operazioni di I/O al secondo supera ciò che può sostenere un singolo disco rigido virtuale, è necessario bilanciare il numero di operazioni di I/O al secondo necessario per i file di database in più dischi rigidi virtuali. Il modo più semplice per distribuire il carico delle operazioni di I/O al secondo tra più dischi consiste nel creare una striscia software su dischi diversi. Memorizzare quindi una serie di file di dati del sistema DBMS SAP nei LUN ricavati dalla striscia software. Il numero di dischi nella striscia si basa sulle richieste di operazioni di I/O al secondo, sulle richieste di velocità effettiva dei dischi e sulle richieste di volume.


- - -
> ![ Windows][Logo_Windows]  Windows
>
> È consigliabile usare spazi di archiviazione di Windows per creare tali set di striping tra più dischi rigidi virtuali di Azure. È consigliabile usare almeno Windows Server 2012 R2 o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Per compilare un RAID software in Linux sono supportati solo MDADM e LVM (Logical Volume Manager). Per altre informazioni, leggere gli articoli seguenti:
>
> - [Configurare RAID software su Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

- - -

> [!NOTE]
> Considerato che Archiviazione di Azure conserva tre immagini dei dischi rigidi virtuali, non è utile configurare una ridondanza durante lo striping. È sufficiente configurare lo striping, in modo che le operazioni di I/O vengano distribuite su dischi rigidi virtuali diversi.
>

### <a name="managed-or-non-managed-disks"></a>Dischi gestiti e non gestiti
Un account di archiviazione di Azure non è soltanto un costrutto amministrativo, ma è anche soggetto a limitazioni. Le limitazioni variano tra gli account di archiviazione Standard e gli account di archiviazione Premium di Azure. Le funzionalità e le limitazioni precise sono elencate nell'articolo [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Per Archiviazione Standard di Azure è quindi importante ricordare che è previsto un limite di operazioni di I/O al secondo per account di archiviazione. Vedere in proposito la riga contenente la **frequenza di richiesta totale** nell'articolo [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Esiste inoltre un limite iniziale per il numero di account di archiviazione per ogni sottoscrizione di Azure. È pertanto necessario bilanciare i dischi rigidi virtuali per il panorama applicativo SAP di maggiori dimensioni tra i diversi account di archiviazione per evitare di raggiungere i limiti di questi account di archiviazione. Si tratta di un lavoro noioso quando si parla di alcune centinaia di macchine virtuali con più di mille dischi rigidi virtuali.

Poiché non è consigliabile usare Archiviazione Standard di Azure per le distribuzioni DBMS in combinazione con il carico di lavoro SAP, i riferimenti e i consigli per Archiviazione Standard di Azure sono limitati a questo breve [articolo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Per evitare il lavoro amministrativo di pianificazione e distribuzione di dischi rigidi virtuali nei diversi account di archiviazione di Azure, nel 2017 Microsoft ha introdotto il servizio [Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi gestiti sono disponibili per Archiviazione Standard di Azure, nonché per Archiviazione Premium di Azure. I vantaggi principali di Managed Disks rispetto all'elenco di dischi non gestiti sono i seguenti:

- Per Managed Disks, Azure distribuisce automaticamente i diversi dischi rigidi virtuali tra diversi account di archiviazione in fase di distribuzione e quindi evita di raggiungere i limiti di un account di archiviazione di Azure in termini di volume di dati, velocità effettiva delle operazioni di I/O e operazioni di I/O al secondo.
- Con Managed Disks, Archiviazione di Azure rispetta i concetti di set di disponibilità di Azure e distribuisce il disco rigido virtuale di base e il disco collegato di una VM in domini di errore e di aggiornamento diversi se la VM fa parte di un set di disponibilità di Azure.


> [!IMPORTANT]
> Dato i vantaggi di Azure Managed Disks, si consiglia di usare questo servizio in generale per le distribuzioni DBMS e le distribuzioni SAP.
>

Per convertire dischi non gestiti in dischi gestiti, vedere gli articoli seguenti:

- [Convertire i dischi non gestiti di una VM Windows in dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi dati
Quando si montano dischi su VM, è possibile scegliere se memorizzare nella cache il traffico di I/O tra la VM e i dischi posizionati nella risorsa di archiviazione di Azure. Archiviazione Premium e Archiviazione Standard di Azure usano due tecnologie diverse per questo tipo di cache.

Le raccomandazioni riportate di seguito presuppongono queste caratteristiche di I/O per il sistema DBMS standard:

- Si tratta principalmente di carico di lavoro di lettura su file di dati di un database. Queste operazioni di lettura sono cruciali per le prestazioni per il sistema DBMS
- La scrittura sui file di dati si verifica in burst in base ai checkpoint o a un flusso costante. Ciononostante, mediamente in un giorno, le operazioni di scrittura sono minori di quelle di lettura. Al contrario delle operazioni di lettura da file di dati, queste operazioni di scrittura sono asincrone e non contengono transazioni utente.
- Esistono pochissime operazioni di lettura dal file registro transazioni e dal file di rollforward. Fanno eccezione le operazioni di I/O di grandi dimensioni quando si eseguono i backup del log delle transazioni.
- Il carico principale nei file registro transazioni e di rollforward è costituito da operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

Per Archiviazione Standard di Azure le opzioni di memorizzazione nella cache sono:

* Nessuna
* Lettura
* Lettura/Scrittura

Per ottenere prestazioni coerenti e deterministiche, è consigliabile impostare la memorizzazione nella cache in Archiviazione Standard di Azure per tutti i dischi contenenti **file registro, di rollforward e di dati correlati a DBMS su "NONE"**. La memorizzazione nella cache del disco rigido virtuale di base può rimanere con l'impostazione predefinita.

Per Archiviazione Premium di Azure, le opzioni di memorizzazione nella cache sono:

* Nessuna
* Lettura
* Lettura/Scrittura
* Nessuna + acceleratore di scrittura (solo per macchine virtuali serie M di Azure)
* Lettura + acceleratore di scrittura (solo per macchine virtuali serie M di Azure)

Per Archiviazione Premium di Azure è consigliabile scegliere il **caching di lettura per i file di dati** del database SAP e **nessun caching per i dischi dei file di log**.

Per le distribuzioni di macchine serie M, si consiglia di usare l'acceleratore di scrittura di Azure per la distribuzione DBMS. Per i dettagli, le limitazioni e la distribuzione dell'acceleratore di scrittura di Azure, vedere il documento sull'[acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-non-persistent-disks"></a>Dischi non persistenti di Azure
Le macchine virtuali di Azure offrono dischi non persistenti dopo la distribuzione di una macchina virtuale. In caso di riavvio di una VM, tutto il contenuto delle unità viene cancellato. È di conseguenza importante che i file di dati e i file registro/di rollforward dei database non vengano mai posizionati in tali unità non persistenti. Potrebbero esserci delle eccezioni per alcuni database, dove queste unità non persistenti potrebbero essere adatte per gli spazi di tabella di tempdb e temp. Evitare invece di usare queste unità nelle VM serie A poiché, con questa famiglia di VM, le unità non persistenti sono limitate in termini di velocità effettiva. Per altre informazioni, leggere l'articolo [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Informazioni sull'unità temporanea nelle macchine virtuali Windows di Azure)

- - -
> ![ Windows][Logo_Windows]  Windows
>
> In una VM di Azure, D:\ è un'unità non persistente supportata da alcuni dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto dell'unità D:\ vengono perse al riavvio della macchina virtuale. Per "tutte le modifiche" si intendono i file memorizzati, le directory create, le applicazioni installate, ecc.
>
> ![Linux][Logo_Linux] Linux
>
> Nelle VM Linux di Azure viene montata automaticamente in /mnt/resource un'unità non persistente supportata da dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto in /mnt/resource vengono perse al riavvio della VM. Per "tutte le modifiche" si intendono i file memorizzati, le directory create, le applicazioni installate, ecc.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resilienza di Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure archivia il disco rigido virtuale di base e i dischi collegati o i BLOB in almeno tre nodi di archiviazione separati. Questo approccio è denominato archiviazione con ridondanza locale (LRS). L'archiviazione con ridondanza locale è l'impostazione predefinita per tutti i tipi di archiviazione in Azure.

Esistono molti altri metodi di ridondanza e sono tutti descritti nell'articolo [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>A partire da Archiviazione Premium di Azure, che è il tipo consigliato di archiviazione per le macchine virtuali DBMS e i dischi che archiviano file di database e registro/di rollforward, l'unico metodo disponibile è l'archiviazione con ridondanza locale. È quindi necessario configurare i metodi di database, ad esempio SQL Server Always On, Oracle Data Guard o replica di sistema HANA, per abilitare la replica dei dati di database in un'altra area di Azure o in un'altra zona di disponibilità di Azure.


> [!NOTE]
> Per le distribuzioni DBMS, l'utilizzo dell'archiviazione con ridondanza geografica disponibile con Archiviazione Standard di Azure non è consigliabile perché ha un forte impatto negativo sulle prestazioni e non rispetta l'ordine di scrittura tra i diversi dischi rigidi virtuali collegati a una macchina virtuale. Il fatto di non rispettare l'ordine di scrittura nei diversi dischi rigidi virtuali può facilmente portare a creare database incoerenti sul lato della destinazione della replica se i file di database e i file registro/di rollforward vengono distribuiti tra più dischi rigidi virtuali (come avviene nella maggior parte dei casi) sul lato della macchina virtuale di origine.



## <a name="vm-node-resiliency"></a>Resilienza del nodo della VM
La piattaforma Azure offre molti contratti di servizio per macchine virtuali. I dettagli esatti sono reperibili nella versione più recente di [Contratto di servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Poiché il livello DBMS è in genere una parte essenziale della disponibilità di un sistema SAP, è necessario acquisire familiarità con i concetti di set di disponibilità, zone di disponibilità ed eventi di manutenzione. Gli articoli che descrivono tutti questi concetti sono [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gestire la disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Il consiglio di base per gli scenari DBMS di produzione con carichi di lavoro SAP è di:

- Distribuire due macchine virtuali in un set di disponibilità separato nella stessa area di Azure.
- Queste due macchine virtuali vengono eseguite nella stessa rete virtuale di Azure e dispongono di schede di rete collegate fuori dalle stesse subnet.
- Usare i metodi di database per mantenere un hot standby con la seconda macchina virtuale. I metodi possono essere SQL Server Always On, Oracle Data Guard o replica di sistema HANA.

È inoltre possibile distribuire una terza macchina virtuale in un'altra area di Azure e usare gli stessi metodi di database per fornire una replica asincrona in un'altra area di Azure.

Il modo per configurare il set di disponibilità di Azure è illustrato in questa [esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerazioni sulla rete di Azure
Nelle distribuzioni di SAP su larga scala si consiglia di usare il progetto di [data center virtuale di Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) per la configurazione e le autorizzazioni e le assegnazioni di ruolo a parti diverse della propria organizzazione.

Esistono molte procedure consigliate nate come risultato di centinaia di distribuzioni dei clienti:

- Le reti virtuali in cui viene distribuita l'applicazione SAP non hanno accesso a Internet.
- Le macchine virtuali del database vengono eseguite nella stessa rete virtuale del livello applicazione.
- Le macchine virtuali all'interno della rete virtuale hanno un'allocazione statica dell'indirizzo IP privato. Vedere l'articolo [Tipi di indirizzi IP e metodi di allocazione in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) come riferimento.
- Le limitazioni del routing da e verso le macchine virtuali DBMS **NON** vengono impostate con i firewall installati nelle macchine virtuali DBMS locali. Il routing del traffico viene invece definito con i [Gruppi di sicurezza di rete (NSG) di Azure](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Al fine di separare e isolare il traffico verso la VM DBMS, si assegnano schede di rete diverse alla macchina virtuale. Ogni scheda di rete dispone di un indirizzo IP diverso ed è assegnata a una subnet diversa della rete virtuale. Le subnet hanno regole NSG diverse. L'isolamento o la separazione del traffico di rete è solo una misura di routing e non consente di impostare quote per la velocità effettiva della rete.

> [!NOTE]
> È consigliabile assegnare indirizzi IP statici tramite Azure alle singole schede di interfaccia di rete virtuali. Non è opportuno assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure, come Backup di Azure, si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria sia impostata su DHCP e non su indirizzi IP statici. Vedere anche il documento [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se è necessario assegnare più indirizzi IP statici a una macchina virtuale, occorre assegnare più schede di interfaccia di rete virtuali a una macchina virtuale.
>


> [!IMPORTANT]
> Se non per motivi di funzionalità, ma più importante se non per motivi di prestazioni, non è supportato configurare [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un sistema SAP basato su SAP NetWeaver, Hybris o S/4 HANA. La comunicazione tra il livello applicazione SAP e il livello DBMS deve essere diretta. La limitazione non include le [regole Azure ASG e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), a condizione che queste regole ASG e NSG consentano la comunicazione diretta. Altri scenari in cui non sono supportate appliance virtuali di rete sono nei percorsi di comunicazione tra macchine virtuali di Azure che rappresentano i nodi del cluster Pacemaker di Linux e i dispositivi SBD, come descritto in [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Oppure nei percorsi di comunicazione tra macchine virtuali di Azure e Windows Server SOFS configurati come descritto in [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Le appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra due partner di comunicazione e possono limitare la velocità effettiva nei percorsi critici tra il livello applicazione SAP e il livello DBMS. In alcuni scenari esaminati con i clienti, le appliance virtuali di rete possono causare errori dei cluster Linux Pacemaker nei casi in cui le comunicazioni tra i nodi del cluster Linux Pacemaker e il relativo dispositivo SBD avvengono tramite un'appliance virtuale di rete.
>

> [!IMPORTANT]
> Un'altra struttura **NON** supportata è la separazione del livello dell'applicazione SAP e del livello DBMS in reti virtuali Azure diverse non [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). È consigliabile separare il livello dell'applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure, anziché usare reti virtuali di Azure diverse. Se si decide di non seguire il consiglio e separare i due livelli in reti virtuali diverse, le due reti virtuali devono essere [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tenere presente che il traffico di rete tra due reti virtuali [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) di Azure è soggetto a costi di trasferimento. Se il livello dell'applicazione SAP e il livello DBMS vengono separati tra due reti virtuali Azure peer, i grandi volumi di dati (dell'ordine di molti terabyte) scambiati tra il livello dell'applicazione SAP e il livello DBMS possono originare costi sostanziali.

Se si usano due macchine virtuali per la distribuzione DBMS di produzione in un set di disponibilità di Azure più un routing separato per il livello dell'applicazione SAP e il traffico operativo e di gestione verso le due macchine virtuali DBMS, il diagramma appare indicativamente come segue:

![Diagramma di due macchine virtuali in due subnet](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Servizio di bilanciamento del carico di Azure per il reindirizzamento del traffico
L'uso di indirizzi IP virtuali privati usati in funzionalità quali SQL Server Always On o replica di sistema HANA richiede la configurazione di Azure Load Balancer. Azure Load Balancer è in grado, attraverso le porte probe, di determinare il nodo DBMS attivo e di instradare il traffico esclusivamente verso quel nodo di database attivo. In caso di failover del nodo del database, non è necessario riconfigurare l'applicazione SAP. Le architetture delle applicazioni SAP più comuni si riconnettono all'indirizzo IP virtuale privato. Nel frattempo il servizio di bilanciamento del carico di Azure reagisce al failover del nodo reindirizzando il traffico nell'indirizzo IP virtuale privato verso il secondo nodo.

Azure offre due [SKU di servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) diversi. Uno SKU basic e uno SKU standard. A meno che non si voglia distribuire in più zone di disponibilità di Azure, lo SKU basic del servizio di bilanciamento del carico è adeguato.

Il traffico tra le macchine virtuali DBMS e il livello dell'applicazione SAP viene sempre instradato tramite il servizio di bilanciamento del carico di Azure? La risposta dipende da come si configura il bilanciamento del carico. A questo punto il traffico in ingresso nella VM DBMS viene sempre instradato tramite il servizio di bilanciamento del carico di Azure. La route del traffico in uscita dalla VM DBMS alla VM del livello dell'applicazione dipende dalla configurazione del servizio di bilanciamento del traffico di Azure. Il bilanciamento del carico offre un'opzione DirectServerReturn. Se tale opzione è configurata, il traffico indirizzato dalla VM DBMS al livello dell'applicazione SAP **NON** viene instradato attraverso il servizio di bilanciamento del carico di Azure. Va direttamente al livello dell'applicazione. Se l'opzione DirectServerReturn non è configurata, il traffico di ritorno al livello dell'applicazione SAP viene instradato attraverso il servizio di bilanciamento del carico di Azure.

È consigliabile configurare DirectServerReturn in combinazione con i servizi di bilanciamento del carico di Azure che si trovano tra il livello dell'applicazione SAP e il livello DBMS per ridurre la latenza di rete tra i due livelli.

Un esempio di impostazione di questo tipo di configurazione è disponibile per SQL Server Always On in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se si sceglie di usare i modelli JSON di GitHub pubblicati come riferimento per le distribuzioni di infrastrutture SAP in Azure, è necessario studiare il [modello per un sistema SAP a 3 livelli](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). In questo modello è anche possibile studiare le impostazioni corrette del servizio di bilanciamento del carico di Azure.

### <a name="azure-accelerated-networking"></a>Rete accelerata di Azure
Per ridurre ulteriormente la latenza di rete tra le VM di Azure, è consigliabile scegliere l'opzione [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) durante la distribuzione delle macchine virtuali di Azure per carichi di lavoro SAP. Ciò vale specialmente per il livello dell'applicazione SAP e il livello DBMS SAP.

> [!NOTE]
> Non tutti i tipi di macchine virtuali supportano la Rete accelerata. L'articolo di riferimento elenca i tipi di macchine virtuali che supportano la funzionalità di Rete accelerata.
>

- - -
> ![ Windows][Logo_Windows]  Windows
>
> Per Windows, consultare l'articolo [Creare una macchina virtuale Windows con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) per comprendere i concetti e il modo in cui distribuire le VM con Rete accelerata.
>
> ![Linux][Logo_Linux] Linux
>
> Per Linux, vedere l'articolo [Creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) per ottenere informazioni dettagliate sulla distribuzione per Linux.
>
>

- - -

> [!NOTE]
> Nel caso di SUSE, Red Hat e Oracle Linux, la funzionalità di Rete accelerata è supportata nelle versioni recenti. Le versioni precedenti, ad esempio SLES 12 SP2 o RHEL 7.2, non supportano la Rete accelerata di Azure.
>


## <a name="deployment-of-host-monitoring"></a>Distribuzione del monitoraggio host
Per un uso in ambiente di produzione delle applicazioni SAP in macchine virtuali di Azure, SAP deve poter recuperare i dati di monitoraggio host dagli host fisici che eseguono le macchine virtuali di Azure. È necessario un livello di patch dell'agente host SAP specifico che abilita questa funzionalità in SAPOSCOL e nell'agente host SAP. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per informazioni dettagliate sulla distribuzione di componenti che forniscono dati host a SAPOSCOL e all'agente host SAP e sulla gestione del ciclo di vita di tali componenti, vedere la [guida alla distribuzione][deployment-guide]


## <a name="next-steps"></a>Passaggi successivi
Per informazioni su una distribuzione DBMS specifica, vedere questi articoli:

- [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sqlserver.md)
- [Distribuzione DBMS per Oracle di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_oracle.md)
- [Distribuzione DBMS per IBM DB2 di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_ibm.md)
- [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](dbms_guide_sapase.md)
- [Distribuzione di SAP MaxDB, liveCache e server di contenuti in Azure](dbms_guide_maxdb.md)
- [Guida operativa a SAP HANA in Azure](hana-vm-operations.md)
- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](sap-hana-availability-overview.md)
- [Guida del backup di SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md)

