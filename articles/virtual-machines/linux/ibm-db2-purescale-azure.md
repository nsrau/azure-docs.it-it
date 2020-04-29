---
title: IBM DB2 pureScale in Azure
description: Questo articolo illustra un'architettura per l'esecuzione di un ambiente IBM DB2 pureScale in Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945047"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

L'ambiente IBM DB2 pureScale rappresenta un cluster database per Azure con disponibilità e scalabilità elevate in sistemi operativi Linux. Questo articolo illustra un'architettura per l'esecuzione di DB2 pureScale in Azure.

## <a name="overview"></a>Panoramica

Le aziende dispongono di piattaforme RDBMS (database Relational Database Management System) tradizionali utilizzate a lungo per soddisfare le proprie esigenze di elaborazione delle transazioni online (OLTP). Ultimamente molte stanno eseguendo la migrazione dei loro ambienti di database basati su mainframe in Azure per espandere la capacità, ridurre i costi e mantenere una stabile struttura dei costi operativi. La migrazione rappresenta spesso il primo passo nella direzione della modernizzazione di una piattaforma legacy. 

Recentemente, un cliente aziendale ha riallocato l'ambiente IBM DB2 in esecuzione su z/OS a IBM DB2 pureScale in Azure. La soluzione cluster di database DB2 pureScale offre disponibilità elevata e scalabilità nei sistemi operativi Linux. Il cliente ha eseguito correttamente DB2 come istanza autonoma di scalabilità verticale in una singola macchina virtuale (VM) in un sistema di grandi dimensioni in Azure prima di installare DB2 pureScale. 

Anche se non è identico all'ambiente originale, IBM DB2 pureScale su Linux offre funzionalità di scalabilità e disponibilità elevate analoghe a IBM DB2 per z/OS in esecuzione in una configurazione Parallel Sysplex nel mainframe. In questo scenario, il cluster viene connesso tramite iSCSI a un cluster di archiviazione condivisa. Abbiamo usato il file system GlusterFS, una file system distribuita, disponibile, scalabile e open source, ottimizzata in modo specifico per l'archiviazione cloud. Tuttavia, IBM non supporta più questa soluzione. Per mantenere il supporto tecnico da IBM, è necessario usare un file system supportato compatibile con iSCSI. Microsoft offre Spazi di archiviazione diretta (S2D) come opzione

Questo articolo descrive l'architettura usata per questa migrazione ad Azure. Per testare la configurazione, il cliente ha usato Red Hat Linux 7.4. Questa versione è disponibile in Azure Marketplace. Prima di scegliere una distribuzione Linux, verificare le versioni attualmente supportate. Per informazioni dettagliate, vedere la documentazione per [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Questo articolo rappresenta un punto di partenza per il piano di implementazione di DB2. I requisiti varieranno da azienda ad azienda, ma lo stesso modello di base vale per tutte. È possibile usare questo modello di architettura anche per le applicazioni OLAP (Online Analytical Processing) in Azure.

Questo articolo non tratta le differenze e le attività di migrazione possibili per lo spostamento di un database IBM DB2 per z/OS in IBM DB2 pureScale in esecuzione su Linux e non offre stime del ridimensionamento né del carico di lavoro per lo spostamento da DB2 z/OS a DB2 pureScale. 

Per decidere più facilmente l'architettura DB2 pureScale più adatta al proprio ambiente, è consigliabile eseguire una stima completa del ridimensionamento e creare un'ipotesi. Nel sistema di origine prendere in considerazione l'architettura DB2 z/OS Parallel Sysplex con condivisione dei dati, la configurazione della funzionalità Coupling Facility e le statistiche di utilizzo DDF (Distributed Data Facility).

> [!NOTE]
> Questo articolo descrive un solo approccio alla migrazione di DB2, ma ne esistono anche altri. È ad esempio possibile eseguire DB2 pureScale anche in ambienti locali virtualizzati. IBM supporta DB2 su Microsoft Hyper-V in varie configurazioni. Per ulteriori informazioni, vedere l'articolo relativo all' [architettura di virtualizzazione DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) in IBM Knowledge Center.

## <a name="architecture"></a>Architecture

Per supportare livelli elevati di disponibilità e scalabilità in Azure, è possibile usare un'architettura di dati condivisi ampliabile per DB2 pureScale. La migrazione eseguita dall'utente ha usato l'architettura di esempio seguente.

![DB2 pureScale in macchine virtuali di Azure che mostrano archiviazione e rete](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale in macchine virtuali di Azure che mostrano archiviazione e rete")


Il diagramma illustra i livelli logici necessari per un cluster DB2 pureScale. Questi livelli includono le macchine virtuali per un client, per la gestione, per la memorizzazione nella cache, per il motore di database e per lo spazio di archiviazione condiviso. 

Oltre ai nodi del motore di database, il diagramma include due nodi usati per le funzionalità di memorizzazione nella cache del cluster (CF). Per il motore di database viene utilizzato un minimo di due nodi. Un server DB2 appartenente a un cluster pureScale è detto membro. 

Il cluster è connesso tramite iSCSI a un cluster di archiviazione condiviso a tre nodi per offrire archiviazione con scalabilità orizzontale e disponibilità elevata. DB2 pureScale viene installato in macchine virtuali di Azure che eseguono Linux.

Questo approccio è un modello che è possibile modificare in base alle dimensioni e alla scalabilità della propria organizzazione. Si basa sui principi seguenti:

-   Due o più membri del database vengono combinati con almeno due nodi CF. I nodi gestiscono un pool di buffer globale per i servizi di memoria condivisa e gestione del blocco globale per controllare l'accesso condiviso e le contese di blocco dei membri attivi. Un nodo CF funge da nodo primario e l'altro da nodo secondario, di failover. Per evitare un singolo punto di guasto nell'ambiente, un cluster pureScale DB2 richiede almeno quattro nodi.

-   Risorsa di archiviazione condivisa con prestazioni elevate (illustrata nella dimensione P30 nel diagramma). Ogni nodo usa questa risorsa di archiviazione.

-   Funzionalità di rete a prestazioni elevate per i membri dati e lo spazio di archiviazione condiviso.

### <a name="compute-considerations"></a>Considerazioni sulle risorse di calcolo

Questa architettura esegue i livelli applicazione, archiviazione e dati sulle macchine virtuali di Azure. Gli [script di configurazione della distribuzione](https://aka.ms/db2onazure) creano quanto segue:

-   Un cluster DB2 pureScale. Il tipo di risorse di calcolo necessario in Azure dipende dalla configurazione. È in genere possibile usare due approcci:

    -   Usare una rete HPC (High Performance Computing) a più nodi, dove alcune istanze di piccole e medie dimensioni accedono allo spazio di archiviazione condiviso. Per questa configurazione HPC, le [macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) di Azure della serie E ottimizzata per la memoria o della serie L ottimizzata per l'archiviazione offrono la potenza di calcolo necessaria.

    -   Usare meno istanze di macchine virtuali di grandi dimensioni per i motori di dati. Per le istanze di grandi dimensioni, le macchine virtuali [serie M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) ottimizzate per la memoria sono ideali per carichi di lavoro con uso intensivo della memoria. Può essere necessaria un'istanza dedicata, a seconda delle dimensioni della partizione logica (LPAR) usata per eseguire DB2.

-   La funzionalità CF di DB2 usa macchine virtuali ottimizzate per la memoria, ad esempio della serie E o L.

-   Un cluster di archiviazione condivisa che usa\_macchine\_virtuali DS4 V2 standard che eseguono Linux.

-   Il JumpBox di gestione è una\_macchina\_virtuale DS2 V2 standard che esegue Linux.  Un'alternativa è Azure Bastion, un servizio che fornisce un'esperienza RDP/SSH sicura per tutte le macchine virtuali nella rete virtuale.

-   Il client è una macchina virtuale Standard\_DS3\_v2 che esegue Windows (usata per il testing).

-   *Facoltativo*. Server di controllo del mirroring. Questa operazione è necessaria solo con alcune versioni precedenti di DB2 pureScale. Questo esempio usa una macchina\_virtuale\_standard DS3 V2 che esegue Linux (usata per DB2 pureScale).

> [!NOTE]
> Un cluster DB2 pureScale richiede almeno due istanze di DB2, oltre a un'istanza di cache e a un'istanza di Gestione blocchi.

### <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

Analogamente a Oracle RAC, DB2 pureScale è un database ampliabile con I/O a blocchi a prestazioni elevate. È consigliabile usare l'opzione [SSD Premium di Azure](disks-types.md) con le dimensioni maggiori in grado di soddisfare le proprie esigenze. Le opzioni di archiviazione di dimensioni inferiori possono essere adatte per gli ambienti di sviluppo e test, mentre gli ambienti di produzione necessitano spesso di una capacità di archiviazione maggiore. L'architettura di esempio usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) per il suo rapporto tra operazioni di I/O al secondo e dimensioni e prezzo. Indipendentemente dalle dimensioni, usare Archiviazione Premium per ottenere prestazioni ottimali.

DB2 pureScale usa un'architettura di condivisione totale, in cui tutti i dati sono accessibili da tutti i nodi del cluster. Archiviazione Premium deve essere condivisa tra più istanze, sia su richiesta che su istanze dedicate.

Un cluster DB2 pureScale di grandi dimensioni può richiedere uno spazio di archiviazione Premium condiviso di 200 TB (terabyte) o superiore, con 100.000 operazioni di I/O al secondo. DB2 pureScale supporta un'interfaccia a blocchi iSCSI che è possibile usare in Azure. L'interfaccia iSCSI richiede un cluster di archiviazione condivisa che è possibile implementare con S2D o un altro strumento. Questo tipo di soluzione crea un dispositivo di rete di archiviazione virtuale (vSAN) in Azure. DB2 pureScale usa la vSAN per installare il file system a cluster usato per condividere i dati tra le macchine virtuali.

### <a name="networking-considerations"></a>Considerazioni sulla rete

In un cluster DB2 pureScale IBM consiglia una rete InfiniBand per tutti i membri. Per le funzionalità CF, DB2 pureScale usa anche l'Accesso diretto a memoria remota (RDMA), ove disponibile.

Durante la configurazione si crea un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) di Azure che contenga tutte le macchine virtuali. In genere si raggruppano le risorse in base alla loro durata e alle persone che le gestiranno. Le macchine virtuali in questa architettura richiedono la [rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Si tratta di una funzionalità di Azure che garantisce alle macchine virtuali una latenza di rete coerente ed estremamente bassa tramite Single Root I/O Virtualization (SR-IOV).

Ogni macchina virtuale di Azure viene distribuita in una rete virtuale con le subnet seguenti: principale, GlusterFS front-end (gfsfe), GlusterFS back-end (bfsbe), DB2 pureScale (db2be) e DB2 purescale front-end (db2fe). Lo script di installazione crea anche le [schede di interfaccia di rete](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) principali nelle macchine virtuali della subnet principale.

Usare [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) per limitare il traffico di rete nella rete virtuale e per isolare le subnet.

In Azure, DB2 pureScale deve usare TCP/IP come connessione di rete per l'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

-   [Distribuire questa architettura in Azure](deploy-ibm-db2-purescale-azure.md)
