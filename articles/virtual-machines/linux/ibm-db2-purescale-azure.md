---
title: IBM DB2 pureScale in Azure
description: Questo articolo illustra un'architettura per l'esecuzione di un ambiente IBM DB2 pureScale in Azure.
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
ms.author: edprice
ms.openlocfilehash: f893e417420b26dcb56e0d84551fbad3577b8fdb
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874867"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

L'ambiente IBM DB2 pureScale rappresenta un cluster database per Azure con disponibilità e scalabilità elevate in sistemi operativi Linux. Questo articolo illustra un'architettura per l'esecuzione di DB2 pureScale in Azure.

## <a name="overview"></a>Panoramica

Per soddisfare le proprie esigenze di elaborazione delle transazioni online (OLTP), le aziende si affidano da molto tempo a piattaforme di sistemi di gestione di database relazionali (RDBMS). Ultimamente molte stanno eseguendo la migrazione dei loro ambienti di database basati su mainframe in Azure per espandere la capacità, ridurre i costi e mantenere una stabile struttura dei costi operativi.

La migrazione rappresenta spesso il primo passo nella direzione della modernizzazione di una piattaforma non recente. Recentemente, ad esempio, un cliente aziendale ha trasferito il proprio ambiente IBM DB2 da z/OS a IBM DB2 pureScale in Azure. Anche se non è identico all'ambiente originale, IBM DB2 pureScale su Linux offre funzionalità di scalabilità e disponibilità elevate analoghe a IBM DB2 per z/OS in esecuzione in una configurazione Parallel Sysplex nel mainframe.

Questo articolo descrive l'architettura usata per questa migrazione ad Azure. Per testare la configurazione, il cliente ha usato Red Hat Linux 7.4. Questa versione è disponibile in Azure Marketplace. Prima di scegliere una distribuzione Linux, verificare le versioni attualmente supportate. Per informazioni dettagliate, vedere la documentazione relativa a [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e a [GlusterFS](https://docs.gluster.org/en/latest/).

Questo articolo rappresenta un punto di partenza per il piano di implementazione di DB2. I requisiti varieranno da azienda ad azienda, ma lo stesso modello di base vale per tutte. È possibile usare questo modello di architettura anche per le applicazioni OLAP (Online Analytical Processing) in Azure.

Questo articolo non tratta le differenze e le attività di migrazione possibili per lo spostamento di un database IBM DB2 per z/OS in IBM DB2 pureScale in esecuzione su Linux e non offre stime del ridimensionamento né del carico di lavoro per lo spostamento da DB2 z/OS a DB2 pureScale. 

Per decidere più facilmente l'architettura DB2 pureScale più adatta al proprio ambiente, è consigliabile eseguire una stima completa del ridimensionamento e creare un'ipotesi. Nel sistema di origine prendere in considerazione l'architettura DB2 z/OS Parallel Sysplex con condivisione dei dati, la configurazione della funzionalità Coupling Facility e le statistiche di utilizzo DDF (Distributed Data Facility).

> [!NOTE]
> Questo articolo descrive un solo approccio alla migrazione di DB2, ma ne esistono anche altri. È ad esempio possibile eseguire DB2 pureScale anche in ambienti locali virtualizzati. IBM supporta DB2 su Microsoft Hyper-V in varie configurazioni. Per altre informazioni, vedere [DB2 pureScale virtualization architecture](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) (Architettura di virtualizzazione di DB2 pureScale) in IBM Knowledge Center.

## <a name="architecture"></a>Architettura

Per supportare livelli elevati di disponibilità e scalabilità in Azure, è possibile usare un'architettura di dati condivisi ampliabile per DB2 pureScale. La migrazione eseguita dall'utente ha usato l'architettura di esempio seguente.

![Azure DB2 pureScale in macchine virtuali di Azure con archiviazione e rete](media/db2-purescale-on-azure/pureScaleArchitecture.png "Azure DB2 pureScale in macchine virtuali di Azure con archiviazione e rete")


Il diagramma illustra i livelli logici necessari per un cluster DB2 pureScale. Questi livelli includono le macchine virtuali per un client, per la gestione, per la memorizzazione nella cache, per il motore di database e per lo spazio di archiviazione condiviso. 

Oltre ai nodi del motore di database, il diagramma include due nodi usati per le funzionalità di memorizzazione nella cache del cluster (CF). Almeno due nodi vengono usati per il motore di database. Un server DB2 appartenente a un cluster pureScale è detto membro. 

Il cluster è connesso tramite iSCSI a un cluster di archiviazione condivisa GlusterFS a tre nodi, per fornire spazio di archiviazione scale-out e disponibilità elevata. DB2 pureScale viene installato in macchine virtuali di Azure che eseguono Linux.

Questo approccio è un modello che è possibile modificare in base alle dimensioni e alla scalabilità della propria organizzazione. Si basa sui principi seguenti:

-   Due o più membri del database vengono combinati con almeno due nodi CF. I nodi gestiscono un pool di buffer globale per i servizi di memoria condivisa e gestione del blocco globale per controllare l'accesso condiviso e le contese di blocco dei membri attivi. Un nodo CF funge da nodo primario e l'altro da nodo secondario, di failover. Per evitare un singolo punto di guasto nell'ambiente, un cluster pureScale DB2 richiede almeno quattro nodi.

-   Risorsa di archiviazione condivisa con prestazioni elevate (illustrata nella dimensione P30 nel diagramma). Ognuno dei nodi Gluster FS usa questa risorsa di archiviazione.

-   Funzionalità di rete a prestazioni elevate per i membri dati e lo spazio di archiviazione condiviso.

### <a name="compute-considerations"></a>Considerazioni sulle risorse di calcolo

Questa architettura esegue i livelli applicazione, archiviazione e dati sulle macchine virtuali di Azure. Gli [script di configurazione della distribuzione](https://aka.ms/db2onazure) creano quanto segue:

-   Un cluster DB2 pureScale. Il tipo di risorse di calcolo necessario in Azure dipende dalla configurazione. È in genere possibile usare due approcci:

    -   Usare una rete HPC (High Performance Computing) a più nodi, dove alcune istanze di piccole e medie dimensioni accedono allo spazio di archiviazione condiviso. Per questa configurazione HPC, le [macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) di Azure della serie E ottimizzata per la memoria o della serie L ottimizzata per l'archiviazione offrono la potenza di calcolo necessaria.

    -   Usare meno istanze di macchine virtuali di grandi dimensioni per i motori di dati. Per le istanze di grandi dimensioni, le macchine virtuali [serie M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) ottimizzate per la memoria sono ideali per carichi di lavoro con uso intensivo della memoria. Può essere necessaria un'istanza dedicata, a seconda delle dimensioni della partizione logica (LPAR) usata per eseguire DB2.

-   La funzionalità CF di DB2 usa macchine virtuali ottimizzate per la memoria, ad esempio della serie E o L.

-   L'archivio GlusterFS usa macchine virtuali Standard\_DS4\_v2 che eseguono Linux.

-   Un jumpbox GlusterFS è una macchina virtuale Standard\_DS2\_v2 che esegue Linux.

-   Il client è una macchina virtuale Standard\_DS3\_v2 che esegue Windows (usata per il testing).

-   Un server di controllo è una macchina virtuale Standard \_DS3\_v2 che esegue Linux (usata per DB2 pureScale).

> [!NOTE]
> Un cluster DB2 pureScale richiede almeno due istanze di DB2, oltre a un'istanza di cache e a un'istanza di Gestione blocchi.

### <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

Analogamente a Oracle RAC, DB2 pureScale è un database ampliabile con I/O a blocchi a prestazioni elevate. È consigliabile usare l'opzione [SSD Premium di Azure](disks-types.md) con le dimensioni maggiori in grado di soddisfare le proprie esigenze. Le opzioni di archiviazione di dimensioni inferiori possono essere adatte per gli ambienti di sviluppo e test, mentre gli ambienti di produzione necessitano spesso di una capacità di archiviazione maggiore. L'architettura di esempio usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) per il suo rapporto tra operazioni di I/O al secondo e dimensioni e prezzo. Indipendentemente dalle dimensioni, usare Archiviazione Premium per ottenere prestazioni ottimali.

DB2 pureScale usa un'architettura di condivisione totale, in cui tutti i dati sono accessibili da tutti i nodi del cluster. Lo spazio di archiviazione Premium deve essere condiviso tra le istanze, che si tratti di istanze su richiesta o dedicate.

Un cluster DB2 pureScale di grandi dimensioni può richiedere uno spazio di archiviazione Premium condiviso di 200 TB (terabyte) o superiore, con 100.000 operazioni di I/O al secondo. DB2 pureScale supporta un'interfaccia a blocchi iSCSI che è possibile usare in Azure. L'interfaccia iSCSI richiede un cluster di archiviazione condivisa che può essere implementato con GlusterFS, S2D o un altro strumento. Questo tipo di soluzione crea un dispositivo di rete di archiviazione virtuale (vSAN) in Azure. DB2 pureScale usa la vSAN per installare il file system a cluster usato per condividere i dati tra le macchine virtuali.

L'architettura di esempio usa GlusterFS, un file system distribuito gratuito, scalabile e open source ottimizzato per l'archiviazione nel cloud.

### <a name="networking-considerations"></a>Considerazioni sulla rete

In un cluster DB2 pureScale IBM consiglia una rete InfiniBand per tutti i membri. Per le funzionalità CF, DB2 pureScale usa anche l'Accesso diretto a memoria remota (RDMA), ove disponibile.

Durante la configurazione si crea un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) di Azure che contenga tutte le macchine virtuali. In genere si raggruppano le risorse in base alla loro durata e alle persone che le gestiranno. Le macchine virtuali in questa architettura richiedono la [rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Si tratta di una funzionalità di Azure che garantisce alle macchine virtuali una latenza di rete coerente ed estremamente bassa tramite Single Root I/O Virtualization (SR-IOV).

Ogni macchina virtuale di Azure viene distribuita in una rete virtuale con le subnet seguenti: principale, GlusterFS front-end (gfsfe), GlusterFS back-end (bfsbe), DB2 pureScale (db2be) e DB2 purescale front-end (db2fe). Lo script di installazione crea anche le [schede di interfaccia di rete](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) principali nelle macchine virtuali della subnet principale.

Usare [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) per limitare il traffico di rete nella rete virtuale e per isolare le subnet.

In Azure, DB2 pureScale deve usare TCP/IP come connessione di rete per l'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

-   [Distribuire questa architettura in Azure](deploy-ibm-db2-purescale-azure.md)
