---
title: IBM Db2 pureScale in Azure
description: Questo articolo illustra un'architettura per l'esecuzione di un ambiente IBM Db2 pureScale in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978116"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM Db2 pureScale in Azure

L'ambiente IBM Db2 pureScale fornisce una soluzione cluster database per Azure con disponibilità e scalabilità elevate in sistemi operativi Linux. Questo articolo illustra un'architettura per l'esecuzione di Db2 pureScale in Azure.

## <a name="overview"></a>Panoramica

Le aziende si affidano da diverso tempo a piattaforme tradizionali di sistemi di gestione di database relazionali (RDBMS) per provvedere alle necessità di elaborazione delle transazioni online (OLTP). Ultimamente molte stanno eseguendo la migrazione dei loro ambienti di database basati su mainframe in Azure per espandere la capacità, ridurre i costi e mantenere una stabile struttura dei costi operativi.

La migrazione rappresenta spesso il primo passo nella direzione della modernizzazione di una piattaforma legacy. Recentemente, ad esempio, un'azienda ha riallocato il proprio ambiente IBM Db2 da z/OS a IBM Db2 pureScale in Azure. Benché non identico all'ambiente originale, IBM Db2 pureScale su Linux offre funzionalità di scalabilità e disponibilità elevate analoghe a IBM Db2 per z/OS in esecuzione in una configurazione Parallel Sysplex nel mainframe.

Questo articolo descrive l'architettura usata per questa migrazione ad Azure. Per testare la configurazione è stato usato Red Hat Linux 7.4. Questa versione è disponibile in Azure Marketplace. Prima di scegliere una distribuzione Linux, verificare le versioni attualmente supportate. Per informazioni dettagliate, vedere la documentazione relativa a [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Questo articolo vuole essere un semplice punto di partenza per il piano di implementazione di Db2. I requisiti varieranno da azienda ad azienda, ma lo stesso modello di base vale per tutte. Questo modello di architettura può essere utile anche per le applicazioni OLAP (Online Analytical Processing) in Azure.

Questo articolo non illustra le differenze e le possibili attività di migrazione per lo spostamento di un database IBM Db2 per z/OS in IBM Db2 pureScale in esecuzione su Linux, né fornisce stime delle dimensioni equivalenti e analisi del carico di lavoro per lo spostamento da Db2 z/OS a Db2 pureScale. Per stabilire qual è l'architettura Db2 pureScale migliore per il proprio ambiente, è consigliabile completare un esercizio sulla stima delle dimensioni e stabilire un'ipotesi. Insieme ad altri fattori, nel sistema di origine prendere in considerazione l'architettura Db2 z/OS Parallel Sysplex con condivisione dei dati, la configurazione della funzionalità Coupling Facility e le statistiche di utilizzo DDF.

> [!NOTE]
> Questo articolo descrive un approccio alla migrazione di Db2, ma ne esistono anche altri. Ad esempio, Db2 pureScale può essere eseguito anche in ambienti locali virtualizzati. IBM supporta Db2 su Microsoft Hyper-V in varie configurazioni. Per altre informazioni, vedere [Db2 pureScale virtualization architecture](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) (Architettura di virtualizzazione di Db2 pureScale) in IBM Knowledge Center.

## <a name="architecture"></a>Architettura

Per supportare livelli elevati di disponibilità e scalabilità in Azure, è possibile usare un'architettura scale-out di dati condivisi per Db2 pureScale. Per la migrazione illustrata in questo articolo è stata usata l'architettura di esempio seguente.

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "Db2 pureScale in macchine virtuali di Azure con le funzionalità di archiviazione e rete")


Il diagramma dell'architettura mostra i livelli logici necessari per un cluster Db2 pureScale. Questi livelli includono le macchine virtuali per un client, per la gestione, per la memorizzazione nella cache, per il motore di database e per lo spazio di archiviazione condiviso. Oltre ai nodi del motore di database, il diagramma include anche due nodi usati per le funzionalità di memorizzazione nella cache del cluster (CF). Per il motore di database stesso vengono usati almeno due nodi. Un server Db2 che appartiene a un cluster pureScale è detto membro. Il cluster è connesso tramite iSCSI a un cluster di archiviazione condivisa GlusterFS a tre nodi, per fornire spazio di archiviazione scale-out e disponibilità elevata. Db2 pureScale viene installato su macchine virtuali di Azure che eseguono Linux.

Questo approccio è un semplice modello che può essere modificato in base alle esigenze di dimensioni e scalabilità dell'organizzazione ed è basato sugli elementi seguenti:

-   Due o più membri di database sono combinati con almeno due nodi CF, che gestiscono un pool di buffer globale per i servizi di memoria condivisa e Gestione blocco globale per controllare l'accesso condiviso e i conflitti di blocco da più membri attivi. Un nodo CF funge da nodo primario e l'altro da nodo secondario, di failover. Per evitare di creare un ambiente con un singolo punto di guasto, sono necessari almeno quattro nodi per un cluster Db2 pureScale.

-   Spazio di archiviazione condiviso a prestazioni elevate (visualizzato nelle dimensioni P30 nella figura 1), usato da ognuno dei nodi di GlusterFS.

-   Funzionalità di rete a prestazioni elevate per i membri dati e lo spazio di archiviazione condiviso.

### <a name="compute-considerations"></a>Considerazioni sulle risorse di calcolo

Questa architettura esegue i livelli applicazione, archiviazione e dati sulle macchine virtuali di Azure. Gli [script di configurazione della distribuzione](http://aka.ms/db2onazure) creano quanto segue:

-   Un cluster Db2 pureScale. Il tipo di risorse di calcolo necessario in Azure dipende dalla configurazione. In generale esistono due approcci possibili:

    -   Usare una rete HPC (High Performance Computing) a più nodi, dove più istanze di piccole e medie dimensioni accedono allo spazio di archiviazione condiviso. Per questa configurazione HPC, le [macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) di Azure della serie E ottimizzata per la memoria o della serie L ottimizzata per l'archiviazione forniscono la potenza di calcolo necessaria.

    -   Usare meno istanze di macchine virtuali di grandi dimensioni per i motori di dati. Per le istanze di grandi dimensioni, le macchine virtuali più grandi, della [serie M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) ottimizzata per la memoria, sono ideali per carichi di lavoro in memoria pesanti, ma potrebbe essere necessaria un'istanza dedicata, a seconda delle dimensioni della partizione logica (LPAR) usata per eseguire Db2.

-   Db2 CF usa macchine virtuali ottimizzate per la memoria, ad esempio della serie E o L.

-   L'archivio GlusterFS usa macchine virtuali Standard\_DS4\_v2 che eseguono Linux.

-   Un jumpbox GlusterFS è una macchina virtuale Standard\_DS2\_v2 che esegue Linux.

-   Il client è una macchina virtuale Standard\_DS3\_v2 che esegue Windows (usata per il testing).

-   Un server di controllo è una macchina virtuale Standard\_DS3\_v2 che esegue Linux (usata per Db2 pureScale).

> [!NOTE]
> Sono necessarie almeno due istanze Db2 in un cluster Db2 pureScale, oltre a un'istanza della cache e a un'istanza di Gestione blocchi.

### <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

Analogamente a Oracle RAC, Db2 pureScale è un database di scale-out con I/O a blocchi a prestazioni elevate. È consigliabile usare l'opzione di [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) con le massime dimensioni disponibili per soddisfare le proprie esigenze. Ad esempio, opzioni di archiviazione di dimensioni inferiori possono essere adatte per gli ambienti di sviluppo e test, mentre gli ambienti di produzione necessitano spesso di una capacità di archiviazione maggiore. L'architettura di esempio usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) per il suo rapporto tra operazioni di I/O al secondo e dimensioni e prezzo. Indipendentemente dalle dimensioni, usare Archiviazione Premium per ottenere prestazioni ottimali.

Db2 pureScale usa un'architettura di condivisione totale, in cui tutti i dati sono accessibili da tutti i nodi del cluster. Lo spazio di archiviazione Premium deve essere condiviso tra più istanze, che si tratti di istanze su richiesta o dedicate.

Un cluster Db2 pureScale di grandi dimensioni può richiedere uno spazio di archiviazione Premium condiviso di 200 terabyte (TB) o superiore, con 100.000 operazioni di I/O al secondo. Db2 pureScale supporta un'interfaccia a blocchi iSCSI che può essere usata in Azure. L'interfaccia iSCSI richiede un cluster di archiviazione condivisa che può essere implementato con GlusterFS, S2D o un altro strumento. Questo tipo di soluzione crea un dispositivo di rete di archiviazione virtuale (vSAN) in Azure. Db2 pureScale usa la vSAN per installare il file system a cluster usato per condividere i dati tra più macchine virtuali.

Per questa architettura è stato usato il file system di GlusterFS, un file system distribuito gratuito, scalabile e open source ottimizzato in modo specifico per l'archiviazione cloud.

### <a name="networking-considerations"></a>Considerazioni sulla rete

IBM consiglia le funzionalità di rete InfiniBand per tutti i membri di un cluster Db2 pureScale. Db2 pureScale usa anche Accesso diretto a memoria remota (RDMA), laddove disponibile, per le funzionalità di memorizzazione nella cache usate.

Durante la configurazione, viene creato un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) di Azure per contenere tutte le macchine virtuali. Generalmente le risorse vengono raggruppate in base alla loro durata e alle persone che le gestiranno. Le macchine virtuali in questa architettura richiedono la [rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), una funzionalità di Azure che fornisce a una macchina virtuale una latenza di rete coerente ed estremamente bassa tramite Single Root I/O Virtualization (SR-IOV).

Ogni macchina virtuale di Azure viene distribuita in una rete virtuale segmentata in più subnet: principale, GlusterFS front-end (gfsfe), GlusterFS back-end (bfsbe), Db2 pureScale (db2be) e Db2 purescale front end (db2fe). Lo script di installazione crea anche le [schede di interfaccia di rete](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) principali nelle macchine virtuali della subnet principale.

I [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) vengono usati per limitare il traffico di rete nella rete virtuale e per isolare le subnet.

In Azure, Db2 pureScale deve usare TCP/IP come connessione di rete per l'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

-   [Distribuire questa architettura in Azure](deploy-ibm-db2-purescale-azure.md)
