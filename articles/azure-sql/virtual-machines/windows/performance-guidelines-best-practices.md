---
title: Linee guida relative alle prestazioni per SQL Server in Azure | Microsoft Docs
description: Vengono fornite linee guida per l'ottimizzazione delle prestazioni SQL Server in Macchine virtuali di Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cff67dde7cfe9e015cd25b26811410ce6e686e9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462534"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Linee guida sulle prestazioni per SQL Server sulle macchine virtuali di Microsoft Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce indicazioni per l'ottimizzazione delle prestazioni SQL Server in Macchine virtuali di Microsoft Azure.

## <a name="overview"></a>Panoramica

Durante l'esecuzione di SQL Server in macchine virtuali di Azure, è consigliabile continuare a usare le stesse opzioni di ottimizzazione delle prestazioni del database applicabili ai SQL Server negli ambienti server locali. Tuttavia, le prestazioni di un database relazionale in un cloud pubblico dipendono da molti fattori, ad esempio dalle dimensioni della macchina virtuale e dalla configurazione dei dischi dati.

[Le immagini SQL Server provisioning nel portale di Azure](sql-vm-create-portal-quickstart.md) seguono le [procedure consigliate](storage-configuration.md)per la configurazione dell'archiviazione generale. Dopo il provisioning, è possibile applicare le altre ottimizzazioni descritte in questo articolo. Basare le scelte sul carico di lavoro e verificarle tramite test.

> [!TIP]
> Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questo articolo è incentrato su come ottenere le *migliori* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è contenuto, potrebbero non essere necessarie tutte le ottimizzazione elencate di seguito. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

## <a name="quick-checklist"></a>Elenco di controllo rapido

Di seguito è riportato un elenco di controllo rapido per ottenere prestazioni ottimali di SQL Server in macchine virtuali di Azure:

| Area | Ottimizzazioni |
| --- | --- |
| [Dimensioni macchina virtuale](#vm-size-guidance) | -Usare le dimensioni delle macchine virtuali con 4 o più vCPU come [Standard_M8-4ms](/../../virtual-machines/m-series), il [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)o il [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) o superiore. <br/><br/> -Usare le dimensioni delle macchine virtuali con ottimizzazione per la [memoria](../../../virtual-machines/sizes-memory.md) per ottimizzare le prestazioni dei carichi di lavoro SQL Server. <br/><br/> -La serie [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)e [Mv2](../../../virtual-machines/mv2-series.md) offrono il rapporto ottimale tra memoria e vCore richiesto per i carichi di lavoro OLTP. Entrambe le VM serie M offrono il rapporto massimo tra memoria e vCore richiesto per i carichi di lavoro mission-critical ed è ideale anche per carichi di lavoro data warehouse. <br/><br/> -È possibile che sia necessario un rapporto tra memoria e vCore superiore per i carichi di lavoro mission-critical e data warehouse. <br/><br/> -Sfruttare le immagini del Marketplace per le macchine virtuali di Azure perché le impostazioni di SQL Server e le opzioni di archiviazione sono configurate per prestazioni SQL Server ottimali. <br/><br/> -Raccogliere le caratteristiche di prestazioni del carico di lavoro di destinazione e usarle per determinare le dimensioni della macchina virtuale appropriate per l'azienda.|
| [Storage](#storage-guidance) | -Per i test dettagliati delle prestazioni SQL Server nelle macchine virtuali di Azure con i benchmark TPC-E e TPC_C, vedere il Blog [ottimizzare le prestazioni OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Usare [unità SSD Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) per ottenere i migliori vantaggi in termini di prezzo/prestazioni. Configurare la [cache di sola lettura](../../../virtual-machines/premium-storage-performance.md#disk-caching) per i file di dati e nessuna cache per il file di log. <br/><br/> -Usare i [dischi Ultra](../../../virtual-machines/disks-types.md#ultra-disk) se il carico di lavoro richiede una latenza di archiviazione inferiore a 1-ms. Per altre informazioni, vedere [eseguire la migrazione ad ultra disk](storage-migrate-to-ultradisk.md) . <br/><br/> - Raccogliere i requisiti di latenza di archiviazione per file di dati, di log e TempDB di SQL Server [monitorando l'applicazione](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) prima di scegliere il tipo di disco. Se < 1-MS le latenze di archiviazione sono necessarie, usare i dischi Ultra, altrimenti usare l'unità SSD Premium. Se le latenze basse sono necessarie solo per il file di log e non per i file di dati, [effettuare il provisioning del disco Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) ai livelli di velocità effettiva e operazioni di I/O al secondo richiesti solo per il file di log. <br/><br/>  - L'archiviazione standard è consigliata solo a scopo di sviluppo e test o per i file di backup e non deve essere usata per i carichi di lavoro di produzione. <br/><br/> - Mantenere l'[account di archiviazione](../../../storage/common/storage-account-create.md) e la macchina virtuale di SQL Server nella stessa area.<br/><br/> - Disabilitare l'[archiviazione con ridondanza geografica](../../../storage/common/storage-redundancy.md) (replica geografica) di Azure nell'account di archiviazione.  |
| [Dischi](#disks-guidance) | - Usare almeno 2 [dischi SSD Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 per il file di log e 1 per i file di dati). <br/><br/> -Per i carichi di lavoro che richiedono < 1-MS le latenze di i/o, abilitare l'acceleratore di scrittura per la serie M e prendere in considerazione l'uso di dischi Ultra SSD per la serie es <br/><br/> - Abilitare la [memorizzazione nella cache di sola lettura](../../../virtual-machines/premium-storage-performance.md#disk-caching) sui dischi che ospitano i file di dati.<br/><br/> -Aggiungere una capacità aggiuntiva del 20% di IOPS/velocità effettiva superiore a quella richiesta dal carico di lavoro durante la [configurazione dell'archiviazione per i file di dati, log e tempdb di SQL Server](storage-configuration.md) <br/><br/> - Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br/><br/> - Non abilitare la memorizzazione nella cache sui dischi che ospitano il file di log.  **Importante**: arrestare il servizio SQL Server quando si modificano le impostazioni della cache per un disco di macchine virtuali di Azure.<br/><br/> - Eseguire lo striping di più dischi dati di Azure per ottenere una maggiore velocità effettiva di archiviazione.<br/><br/> - Formattare con dimensioni di allocazione documentate. <br/><br/> - Posizionare TempDB sul dico `D:\` dell'unità SSD locale per carichi di lavoro di SQL Server di importanza strategica (dopo aver scelto le dimensioni della macchina virtuale corrette). Se si crea la macchina virtuale dal portale di Azure o dai modelli di avvio rapido di Azure e si [posiziona il database temporaneo nel disco locale](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), non è necessaria alcuna azione aggiuntiva. per tutti gli altri casi, seguire i passaggi nel Blog per l'  [uso di unità SSD per archiviare tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) per evitare errori dopo il riavvio. Se la capacità dell'unità locale non è sufficiente per le dimensioni di TempDB, inserire TempDB in un pool di archiviazione con [striping](../../../virtual-machines/premium-storage-performance.md) su dischi SSD Premium per cui è abilitata la [memorizzazione nella cache di sola lettura](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |- Abilitare la compressione di pagina del database.<br/><br/> - Abilitare l'inizializzazione immediata per i file di dati.<br/><br/> - Limitare l'aumento automatico delle dimensioni per il database.<br/><br/> - Disabilitare la compattazione automatica del database.<br/><br/> - Spostare tutti i database su dischi dati, inclusi i database di sistema.<br/><br/> - Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.<br/><br/> - Configurare il percorso predefinito del file di backup e del file di database.<br/><br/> - [Abilitare pagine bloccate in memoria](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> -Valutare e applicare gli [aggiornamenti cumulativi più recenti](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) per la versione installata di SQL Server. |
| [Elementi specifici delle funzionalità](#feature-specific-guidance) | -Eseguire il backup direttamente nell'archivio BLOB di Azure.<br/><br/>- Usare [backup di snapshot di file](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) per i database di dimensioni superiori a 12 TB. <br/><br/>- Usare più file TempDB, 1 file per core, fino a 8 file.<br/><br/>- Impostare la memoria massima del server su 90% o fino a 50 GB rimasti per il sistema operativo. <br/><br/>- Abilitare soft-NUMA. |


<br/>
Per altre informazioni su *come* e *perché* eseguire queste ottimizzazioni, vedere i dettagli e le indicazioni riportate nelle sezioni seguenti.
<br/><br/>

## <a name="getting-started"></a>Introduzione

Se si sta creando un nuovo SQL Server in una macchina virtuale di Azure e non si esegue la migrazione di un sistema di origine corrente, creare la nuova VM SQL Server in base ai requisiti del fornitore.  I requisiti del fornitore per una macchina virtuale SQL Server sono gli stessi di quelli distribuiti in locale. 

Se si crea una nuova macchina virtuale SQL Server con una nuova applicazione compilata per il cloud, è possibile ridimensionare facilmente la macchina virtuale SQL Server Man mano che i dati e i requisiti di utilizzo si evolvono.
Avviare gli ambienti di sviluppo con la serie D di livello inferiore, la serie B o AV2 e aumentare l'ambiente nel tempo. 

Il valore minimo consigliato per un ambiente OLTP di produzione è 4 vCore, 32 GB di memoria e un rapporto tra memoria e vCore pari a 8. Per i nuovi ambienti, iniziare con 4 macchine vCore e scalare fino a 8, 16, 32 Vcore o più quando i dati e i requisiti di calcolo cambiano. Per la velocità effettiva OLTP, usare come destinazione SQL Server VM con 5000 IOPS per ogni vCore. 

Usare le immagini del Marketplace SQL Server VM con la configurazione di archiviazione nel portale. Questo renderà più semplice creare correttamente i pool di archiviazione necessari per ottenere le dimensioni, le operazioni di IOPS e la velocità effettiva necessarie per i carichi di lavoro. È importante scegliere SQL Server VM che supportano archiviazione Premium e la memorizzazione nella cache di archiviazione Premium. Per altre informazioni, vedere la sezione [archiviazione](#storage-guidance) . 

SQL Server data warehouse e gli ambienti mission-critical dovranno spesso essere ridimensionati oltre il rapporto 8 tra memoria e vCore. Per gli ambienti di medie dimensioni, è consigliabile scegliere un rapporto da 16 core a memoria e un rapporto da core a memoria 32 per ambienti di data warehouse più grandi. 

SQL Server ambienti data warehouse spesso traggono vantaggio dall'elaborazione parallela di computer più grandi. Per questo motivo, la serie M e la serie Mv2 sono opzioni complesse per ambienti data warehouse più grandi.

## <a name="vm-size-guidance"></a>Linee guida per le dimensioni delle VM

Usare la configurazione vCPU e Memory dal computer di origine come base per la migrazione di un database di SQL Server locale corrente per SQL Server in macchine virtuali di Azure. Usa la tua licenza Core in Azure per sfruttare i vantaggi del [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) e risparmiare sui costi SQL Server delle licenze.

**Microsoft consiglia un rapporto tra memoria e vCore di 8 come punto di partenza per i carichi di lavoro di produzione SQL Server.** I rapporti più piccoli sono accettabili per i carichi di lavoro non di produzione. 

Scegliere una dimensione di macchina virtuale con ottimizzazione per la [memoria](../../../virtual-machines/sizes-memory.md), [utilizzo generico](../../../virtual-machines/sizes-general.md), [ottimizzato](../../../virtual-machines/sizes-storage.md)per l'archiviazione o con [vCore vincolata](../../../virtual-machines/constrained-vcpu.md) più ottimale per le prestazioni SQL Server in base al carico di lavoro (OLTP o data warehouse). 

### <a name="memory-optimized"></a>Ottimizzate per la memoria

Le [dimensioni delle macchine virtuali con ottimizzazione](../../../virtual-machines/sizes-memory.md) per la memoria rappresentano una destinazione primaria per le macchine virtuali SQL Server e la scelta consigliata da Microsoft. Le macchine virtuali con ottimizzazione per la memoria offrono un rapporto più elevato tra memoria e CPU e opzioni di cache medio-grandi. 

#### <a name="m-and-mv2-series"></a>Serie M e Mv2

La [serie M](../../../virtual-machines/m-series.md) offre conteggi di vCore e memoria per alcuni dei più grandi SQL Server carichi di lavoro.  

La [serie Mv2](../../../virtual-machines/mv2-series.md) presenta i conteggi e la memoria vCore più elevati ed è consigliata per carichi di lavoro mission-critical e data warehouse. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database in memoria di grandi dimensioni e carichi di lavoro con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e analisi in memoria.

Il [Standard_M64ms](../../../virtual-machines/m-series.md) ha un rapporto da 28 a vCore, ad esempio.

Alcune delle funzionalità della serie M e Mv2 sono attraenti per le prestazioni SQL Server includono [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e supporto per la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching) , supporto di più [dischi](../../../virtual-machines/disks-enable-ultra-ssd.md) e [accelerazione in scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Serie Edsv4

La [serie Edsv4](../../../virtual-machines/edv4-edsv4-series.md) è progettata per applicazioni con utilizzo intensivo della memoria. Queste VM hanno una capacità SSD di archiviazione locale di grandi dimensioni, un numero elevato di IOPS del disco locale, fino a 504 GiB di RAM e un calcolo migliorato rispetto alle dimensioni EV3/Esv3 precedenti con le VM Gen2. Si tratta di un rapporto tra memoria e vCore quasi coerente con 8 tra queste macchine virtuali, ideale per carichi di lavoro SQL Server standard. 

Questa serie di VM è ideale per applicazioni aziendali con utilizzo intensivo di memoria e applicazioni che traggono vantaggio da archiviazione locale a bassa latenza e ad alta velocità.

Le macchine virtuali della serie Edsv4 supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md)e la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2-series 11-15

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) presenta le stesse configurazioni di memoria e disco della serie D precedente. Questa serie presenta un rapporto coerente tra memoria e CPU di 7 tra tutte le macchine virtuali. 

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) supporta l' [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), che è fortemente consigliata per prestazioni ottimali.

### <a name="general-purpose"></a>Utilizzo generico

Le [dimensioni delle macchine virtuali per utilizzo generico](../../../virtual-machines/sizes-general.md) sono progettate per fornire rapporti bilanciati tra memoria e vCore per carichi di lavoro a livello di voce più piccoli, ad esempio sviluppo e test, server Web e server di database più piccoli. 

Dato che i rapporti tra memoria e vCore sono inferiori rispetto alle macchine virtuali per utilizzo generico, è importante monitorare attentamente i contatori delle prestazioni basati sulla memoria per assicurarsi che SQL Server sia in grado di ottenere la memoria cache del buffer necessaria. Per ulteriori informazioni, vedere [baseline Performance Memory](#memory) . 

Poiché la raccomandazione iniziale per i carichi di lavoro di produzione è un rapporto tra memoria e vCore pari a 8, la configurazione minima consigliata per una macchina virtuale per utilizzo generico che esegue SQL Server è 4 vCPU e 32 GB di memoria. 

#### <a name="ddsv4-series"></a>Serie Ddsv4

La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) offre una combinazione equa di vCPU, memoria e disco temporaneo, ma con supporto più piccolo per la memoria a vCore. 

Le macchine virtuali Ddsv4 includono latenza più bassa e archiviazione locale ad alta velocità.

Questi computer sono ideali per le distribuzioni di applicazioni e SQL affiancate che richiedono l'accesso rapido ai database relazionali di archiviazione temporanea e di reparto. In tutte le macchine virtuali di questa serie è presente un rapporto tra memoria e vCore standard di 4. 

Per questo motivo, è consigliabile usare la D8ds_v4 come macchina virtuale iniziale in questa serie, con 8 Vcore e 32 GB di memoria. Il computer più grande è il D64ds_v4, che include 64 Vcore e 256 GB di memoria.

Le macchine virtuali della [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e [caching archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) non ha un rapporto tra memoria e vCore pari a 8 consigliato per i carichi di lavoro SQL Server. In questo modo, è possibile considerare l'uso di queste macchine virtuali solo per carichi di lavoro di sviluppo e applicazioni più piccoli.

#### <a name="b-series"></a>Serie B

Le dimensioni delle macchine virtuali [serie B](../../../virtual-machines/sizes-b-series-burstable.md) sono ideali per i carichi di lavoro che non necessitano di prestazioni coerenti, ad esempio il modello di verifica e i server di sviluppo e applicazioni molto piccoli. 

Per la maggior parte delle dimensioni delle macchine virtuali [serie B](../../../virtual-machines/sizes-b-series-burstable.md) è possibile ottenere un rapporto tra memoria e vCore pari a 4. Il più grande di questi computer è la [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) con 20 vcore e 80 GB di memoria.

Questa serie è univoca in quanto le app hanno la possibilità di **irrompere** durante l'orario di lavoro con crediti di espansione che variano in base alle dimensioni del computer. 

Quando i crediti sono esauriti, la macchina virtuale Torna alle prestazioni della macchina di base.

Il vantaggio della serie B è costituito dal risparmio di calcolo che è possibile ottenere rispetto alle altre dimensioni delle macchine virtuali in altre serie, soprattutto se è necessaria una potenza di elaborazione sporadica nel corso della giornata.

Questa serie supporta [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md), ma **non supporta la** [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> La [serie B](../../../virtual-machines/sizes-b-series-burstable.md) di espansione non ha il rapporto tra memoria e vCore di 8 consigliato per i carichi di lavoro SQL Server. In questo modo, è consigliabile usare queste macchine virtuali solo per le applicazioni più piccole, i server Web e i carichi di lavoro di sviluppo.

#### <a name="av2-series"></a>Serie Av2

Le macchine virtuali della [serie AV2](../../../virtual-machines/av2-series.md) sono più adatte per carichi di lavoro di livello superiore, ad esempio sviluppo e test, server Web con traffico ridotto, database di piccole e medie app e modelli di prova.

Solo i [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 Vcore e 16GBs di memoria), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 Vcore e 32GBs di memoria) e il [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 Vcore e 64GBs di memoria) hanno un rapporto di memoria-vCore ottimale pari a 8 per le prime tre macchine virtuali. 

Queste macchine virtuali sono opzioni valide per le macchine SQL Server di sviluppo e di test più piccole. 

Il [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 vCore può anche essere una soluzione ideale per piccoli server Web e applicazioni.

> [!NOTE] 
> La serie AV2 non supporta l'archiviazione Premium e, di conseguenza, non è consigliata per i carichi di lavoro di produzione SQL Server anche con le macchine virtuali con un rapporto da memoria a vCore pari a 8.

### <a name="storage-optimized"></a>Ottimizzate per l'archiviazione

Le [dimensioni delle macchine virtuali ottimizzate](../../../virtual-machines/sizes-storage.md) per l'archiviazione sono per casi d'uso specifici. Queste macchine virtuali sono progettate in modo specifico con velocità effettiva del disco ottimizzate e i/o. Questa serie di macchine virtuali è destinata a scenari di Big Data, data warehousing e database transazionali di grandi dimensioni. 

#### <a name="lsv2-series"></a>Serie Lsv2

La [serie Lsv2](../../../virtual-machines/lsv2-series.md) offre una velocità effettiva elevata, bassa latenza e archiviazione NVMe locale. Le macchine virtuali della serie Lsv2 sono ottimizzate per l'uso del disco locale nel nodo collegato direttamente alla macchina virtuale anziché usare dischi dati durevoli. 

Queste macchine virtuali sono opzioni complesse per i carichi di lavoro Big Data, data warehouse, Reporting e ETL. La velocità effettiva elevata e gli IOPs dell'archiviazione NVMe locale sono un caso d'uso valido per l'elaborazione di file che verranno caricati nel database e altri scenari in cui i dati di origine possono essere ricreati dal sistema di origine o da altri repository, ad esempio archiviazione BLOB di Azure o Azure Data Lake. [Serie Lsv2](../../../virtual-machines/lsv2-series.md) Le macchine virtuali possono anche incrementare le prestazioni del disco per un massimo di 30 minuti alla volta.

Queste macchine virtuali sono da 8 a 80 vCPU con 8 GiB di memoria per vCPU e ogni 8 vCPU è 1,92 TB di NVMe SSD. Questo significa che per la macchina virtuale più grande di questa serie, l' [L80s_v2](../../../virtual-machines/lsv2-series.md), sono presenti 80 vCPU e 640 Bib di memoria con 10 TB 1.92 di spazio di archiviazione NVMe.  Il rapporto tra memoria e vCore di 8 per tutte le macchine virtuali è coerente.

L'archiviazione NVMe è effimera, ovvero i dati andranno persi in questi dischi se si riavvia la macchina virtuale.

Le serie Lsv2 e LS supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md), ma non la memorizzazione nella cache di archiviazione Premium. La creazione di una cache locale per aumentare i IOPs non è supportata. 

> [!WARNING]
> L'archiviazione dei file di dati nell'archiviazione NVMe temporanea potrebbe causare la perdita di dati quando la macchina virtuale viene deallocata. 

### <a name="constrained-vcores"></a>VCore vincolato

Carichi di lavoro a prestazioni elevate SQL Server spesso richiedono quantità maggiori di memoria, i/o e velocità effettiva senza i conteggi vCore più elevati. 

La maggior parte dei carichi di lavoro OLTP sono database di applicazioni basati su un numero elevato di transazioni più piccole. Con i carichi di lavoro OLTP, viene letta o modificata solo una piccola quantità di dati, ma i volumi di transazioni basate sui conteggi degli utenti sono molto più elevati. È importante fare in modo che la memoria SQL Server disponibile per i piani della cache, archiviare i dati a cui si accede di recente per le prestazioni e assicurarsi che le letture fisiche possano essere lette rapidamente in memoria. 

Questi ambienti OLTP richiedono maggiori quantità di memoria, archiviazione veloce e la larghezza di banda di I/O necessaria per prestazioni ottimali. 

Per mantenere questo livello di prestazioni senza i costi di licenza SQL Server più elevati, Azure offre dimensioni di VM con [conteggi vCPU vincolati](../../../virtual-machines/constrained-vcpu.md). 

Questo consente di controllare i costi di licenza riducendo il vcore disponibile mantenendo la stessa memoria, l'archiviazione e la larghezza di banda di I/O della macchina virtuale padre.

Il numero di vCPU può essere limitato a una metà a un trimestre delle dimensioni originali della macchina virtuale. Riducendo la Vcore disponibile per la macchina virtuale, si otterranno rapporti più elevati tra memoria e vCore.

Queste nuove dimensioni di VM hanno un suffisso che specifica il numero di vCPU attive per facilitarne l'identificazione. 

Ad esempio, [M64-32ms standard](../../../virtual-machines/constrained-vcpu.md) richiede la gestione delle licenze solo 32 SQL Server Vcore con memoria, i/o e velocità effettiva di [M64ms](../../../virtual-machines/m-series.md) e [M64-16ms standard](../../../virtual-machines/constrained-vcpu.md) richiedono solo 16 Vcore per la gestione delle licenze.  Sebbene il [M64-16ms standard](../../../virtual-machines/constrained-vcpu.md) abbia un trimestre del costo della licenza SQL Server della M64ms, il costo di calcolo della macchina virtuale sarà lo stesso.

> [!NOTE] 
> - I carichi di lavoro di data warehouse medio-grandi possono comunque trarre vantaggio dalle [macchine virtuali vCore vincolate](../../../virtual-machines/constrained-vcpu.md), ma i carichi di lavoro data warehouse sono comunemente caratterizzati da un numero minore di utenti e processi che indirizzano grandi quantità di dati tramite piani di query eseguiti in parallelo. 
> - Il costo di calcolo, che include le licenze del sistema operativo, rimarrà uguale a quello della macchina virtuale padre. 

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Per un test dettagliato delle prestazioni SQL Server nelle macchine virtuali di Azure con benchmark TPC-E e TPC-C, vedere il Blog [ottimizzare le prestazioni di OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

La cache BLOB di Azure con unità SSD Premium è consigliata per tutti i carichi di lavoro di produzione. 

> [!WARNING]
> Le unità HDD e SSD Standard sono caratterizzate da latenze e larghezze di banda variabili e sono consigliate solo per i carichi di lavoro di sviluppo e test. Per i carichi di lavoro di produzione si consiglia di usare le unità SSD Premium.

È inoltre consigliabile creare account di archiviazione di Azure nello stesso data center delle macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disabilitare la replica geografica in quanto l'ordine di scrittura coerente su più dischi non è garantito. In alternativa, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due data center di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Linee guida per i dischi

Sono disponibili tre tipi di dischi principali nelle macchine virtuali di Azure:

* **Disco** del sistema operativo: quando si crea una macchina virtuale di Azure, la piattaforma collegherà almeno un disco (etichettato come unità **C** ) alla macchina virtuale per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.
* **Disco temporaneo**: le macchine virtuali di Azure contengono un altro disco, chiamato disco temporaneo, contrassegnato come unità **D**. Questo è un disco sul nodo che può essere usato come area scratch.
* **Dischi dati**: È possibile associare dischi aggiuntivi alla macchina virtuale come dischi dati che vengono memorizzati nell'archivio come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco del sistema operativo è un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C** .

I criteri predefiniti di caching per il disco del sistema operativo predefinito sono **Lettura/Scrittura**. Per le applicazioni sensibili alle prestazioni, è consigliabile usare dischi dati anziché il disco del sistema operativo. Vedere la sezione relativa ai dischi di dati riportata di seguito.

### <a name="temporary-disk"></a>Disco temporaneo

L'unità di archiviazione temporanea, etichettata come unità **D** , non viene salvata in modo permanente nell'archivio BLOB di Azure. Non archiviare i file del database utente o i file di log delle transazioni utente nell'unità **D**.

Posizionare TempDB sul dico `D:\` dell'unità SSD locale per carichi di lavoro di SQL Server di importanza strategica (dopo aver scelto le dimensioni della macchina virtuale corrette). Se si crea la macchina virtuale dal portale di Azure o dai modelli di avvio rapido di Azure e si [posiziona il database temporaneo nel disco locale](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), non è necessaria alcuna azione aggiuntiva. per tutti gli altri casi, seguire i passaggi nel Blog per l'  [uso di unità SSD per archiviare tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) per evitare errori dopo il riavvio. Se la capacità dell'unità locale non è sufficiente per le dimensioni di TempDB, inserire TempDB in un pool di archiviazione con [striping](../../../virtual-machines/premium-storage-performance.md) su dischi SSD Premium per cui è abilitata la [memorizzazione nella cache di sola lettura](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Per le macchine virtuali che supportano unità SSD Premium, è consigliabile archiviare TempDB su un disco che supporta le unità SSD Premium con memorizzazione nella cache di lettura abilitata.


### <a name="data-disks"></a>Dischi dati

* **Usare i dischi SSD Premium per i file di dati e di log**: se non si usa lo striping del disco, usare due dischi SSD Premium di cui uno contenente il file di log e l'altro contenente i file di dati. Ogni unità SSD Premium offre un numero di operazioni di I/O al secondo e larghezza di banda (MB/s) a seconda delle dimensioni, come illustrato nell'articolo [Selezionare un tipo di disco](../../../virtual-machines/disks-types.md). Se si utilizza una tecnica di striping del disco, come Spazi di archiviazione, si ottengono prestazioni ottimali con due pool, uno per il/i file di log e l'altro per i file di dati. Tuttavia, se si prevede di usare le istanze del cluster di failover di SQL Server, è necessario configurare un pool oppure usare le [condivisioni file Premium](failover-cluster-instance-premium-file-share-manually-configure.md).

   > [!TIP]
   > - Per i risultati dei test su diverse configurazioni di dischi e carichi di lavoro, vedere il post di Blog seguente: [linee guida per la configurazione dell'archiviazione per SQL Server in macchine virtuali di Azure](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - Per prestazioni di importanza strategica per istanze di SQL Server che necessitano di circa 50.000 operazioni di I/O al secondo, provare a sostituire 10 dischi -P30 con un'unità Ultra SSD. Per altre informazioni, vedere il post di blog seguente: [Mission critical performance with Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/) (Prestazioni di importanza strategica con un'unità Ultra SSD).

   > [!NOTE]
   > Quando si esegue il provisioning di una VM SQL Server nel portale, è possibile modificare la configurazione di archiviazione. A seconda della configurazione, Azure configura uno o più dischi. Più dischi sono combinati in un pool di archiviazione singolo con striping. Entrambi i file di dati e di log risiedono insieme in questa configurazione. Per altre informazioni, vedere [Configurazione dell'archiviazione per le VM di SQL Server](storage-configuration.md).

* **Striping del disco**: per aumentare la velocità effettiva, è possibile aggiungere altri dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati, è necessario analizzare il numero di IOPS e larghezza di banda necessari per i file di log e i file di dati e TempDB. Si noti che a seconda delle dimensioni delle VM i limiti nel numero di IOPs e larghezza di banda supportati cambiano. Vedere le tabelle relative agli IOPS per [dimensione di VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Usare le linee guida seguenti:

  * Per Windows 8 e Windows Server 2012 o versioni successive, usare [Spazi di archiviazione](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) applicando le indicazioni seguenti:

      1. Impostare l'interleave (dimensione di striping) su 64 kB (65.536 byte) per carichi di lavoro OLTP e su 256 kB (262.144 KB) per carichi di lavoro di data warehouse, in modo da evitare effetti sulle prestazioni a causa del mancato allineamento delle partizioni. Questo valore deve essere impostato con PowerShell.
      2. Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell (e non l'interfaccia utente di Server Manager) per configurare più di 8 dischi. 

    Ad esempio, il seguente PowerShell crea un nuovo pool di archiviazione con le dimensioni di interfoliazione a 64 KB e il numero di colonne uguale alla quantità di disco fisico nel pool di archiviazione:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Questa opzione è deprecata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio dischi virtuali all'API di gestione archiviazione di Windows](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Se si usa [Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con [Istanze del cluster di failover di SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md), è necessario configurare un singolo pool. Anche se si possono creare volumi diversi nel pool singolo, tutti condivideranno le stesse caratteristiche, ad esempio gli stessi criteri di memorizzazione nella cache.

  * Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per ulteriori informazioni, vedere [dimensioni per le macchine virtuali](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se non si usano unità SSD Premium (scenari di sviluppo/test), è consigliabile aggiungere il numero massimo di dischi dati supportati dalle dimensioni della [macchina virtuale](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usare lo striping del disco.

* **Memorizzazione dei criteri nella cache**: tenere presenti le raccomandazioni seguenti per la memorizzazione dei criteri nella cache a seconda della configurazione di archiviazione.

  * Se si usano dischi separati per i dati e file di log, abilitare la memorizzazione nella cache di lettura sui dischi dati che ospitano i propri file di dati e i file di dati di TempDB. Ciò può comportare un miglioramento significativo delle prestazioni. Non abilitare la memorizzazione nella cache sul disco che contiene il file di log, perché questo causa una riduzione delle prestazioni.

  * Se si usa lo striping del disco in un singolo pool di archiviazione, la maggior parte dei carichi di lavoro trarrà vantaggio dalla memorizzazione nella cache. Se si dispone di pool di archiviazione separato per i file di log e di dati, abilitare la cache di lettura solo nel pool di archiviazione per i file di dati. Per alcuni carichi di lavoro di scrittura pesanti, è possibile ottenere prestazioni migliori senza la memorizzazione nella cache. Ciò può essere determinato solo tramite test.

  * Le raccomandazioni precedenti si applicano alle unità SSD Premium. Se non si usano unità SSD Premium, non abilitare la memorizzazione nella cache per i dischi dati.

  * Per istruzioni sulla configurazione della memorizzazione nella cache su disco, vedere gli articoli seguenti. Per il modello di distribuzione (ASM) classica, vedere: [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) e [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Per il modello di distribuzione di Azure Resource Manager, vedere: [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Arrestare il servizio SQL Server quando si modifica l'impostazione della cache dei dischi delle macchine virtuali di Azure per evitare la possibilità di eventuali danneggiamenti del database.

* **Dimensioni unità di allocazione NTFS**: quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 kB per file di log e dati, nonché per TempDB. Se TempDB viene inserito nel disco temporaneo (D:\ unità) le prestazioni acquisite sfruttando questa unità superano la necessità di una dimensione dell'unità di allocazione di 64 KB. 

* **Procedure consigliate per la gestione del disco**: quando si rimuove un disco dati o si modifica il relativo tipo di cache, arrestare il servizio SQL Server durante la modifica. Quando vengono modificate le impostazioni del caching sul disco del sistema operativo, Azure arresta la VM, cambia il tipo di cache e riavvia la VM. Quando vengono modificate le impostazioni della cache di un disco dati, la VM non viene arrestata, ma il disco dati viene scollegato dalla VM durante la modifica e quindi ricollegato.

  > [!WARNING]
  > Il mancato arresto del servizio SQL Server durante queste operazioni può danneggiare il database.


## <a name="io-guidance"></a>Linee guida per l'I/O

* Quando l'applicazione e le richieste vengono eseguite in parallelo, si ottengono i migliori risultati con le unità SSD Premium. Le unità SSD Premium sono progettate per scenari in cui la profondità della coda I/O è maggiore di 1, pertanto si noteranno eventualmente impercettibili miglioramenti delle prestazioni per le richieste seriali a thread singolo (anche se usano in modo intensivo l'archiviazione). Ad esempio, ciò potrebbe influire sui risultati di test a thread singolo degli strumenti di analisi delle prestazioni, ad esempio SQLIO.

* È consigliabile usare la [compressione di pagina del database](/sql/relational-databases/data-compression/data-compression) in quanto consente di migliorare le prestazioni dei carichi di lavoro con utilizzo intensivo di I/O. Tuttavia, la compressione dei dati potrebbe incrementare l'utilizzo della CPU nel server di database.

* È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare l'inizializzazione immediata dei file, concedere SE_MANAGE_VOLUME_NAME all'account di servizio SQL Server (MSSQLSERVER) e aggiungerlo ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. Se si usa un'immagine della piattaforma SQL Server per Azure, l'account del servizio predefinito (NT Service\MSSQLSERVER) non viene aggiunto ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo aver aggiunto l'account del servizio SQL Server ai criteri di sicurezza **Esecuzione attività di manutenzione volume** , riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](/sql/relational-databases/databases/database-instant-file-initialization).

* Tenere presente che l' **aumento automatico delle dimensioni** viene considerato semplicemente una contingenza per una crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se si usa questa funzionalità, impostare la precrescita del file mediante l'opzione Dimensioni.

* Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.

* Spostare tutti i database su dischi dati, inclusi i database di sistema. Per altre informazioni vedere l'articolo [Spostare i database di sistema](/sql/relational-databases/databases/move-system-databases).

* Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati. È possibile farlo in Gestione configurazione SQL Server facendo clic con il pulsante destro del mouse sull'istanza di SQL Server e selezionando Proprietà. Le impostazioni per il log degli errori e il file di traccia possono essere modificate nella scheda **Parametri di avvio** . La Directory dump si specifica nella scheda **Avanzate** . La schermata illustra dove cercare il parametro di avvio del log degli errori.

    ![Schermata del log degli errori di SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Configurare i percorsi predefiniti per i file di backup e di database. Seguire i consigli elencati in questo articolo ed eseguire le modifiche nella finestra Proprietà server. Per le istruzioni, vedere l'articolo dedicato alla [Visualizzazione o modifica dei percorsi predefiniti per i file di dati e di log (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Le schermata seguente illustra come apportare tali modifiche.

    ![File di log e di backup di SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Attivare le pagine bloccate per ridurre le operazioni di I/O e le attività di paging. Per altre informazioni, vedere l'articolo [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Se si esegue SQL Server 2012, installare l'aggiornamento cumulativo 10 del Service Pack 1. Questo aggiornamento contiene la correzione per le prestazioni ridotte delle operazioni I/O quando si esegue select nell'istruzione di una tabella temporanea in SQL Server 2012. Per altre informazioni, vedere questo [articolo della Knowledge Base](https://support.microsoft.com/kb/2958012).

* È consigliabile comprimere i file di dati durante il trasferimento in entrata e in uscita di Azure.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Nell'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

### <a name="back-up-to-azure-storage"></a>Backup su Archiviazione di Azure
Quando si eseguono backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile usare [SQL Server backup nell'URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati. Quando si esegue il backup o il ripristino da e verso archiviazione di Azure, seguire le indicazioni fornite in [SQL Server procedure consigliate per il backup in URL e la risoluzione dei problemi e il ripristino da backup archiviati in archiviazione di Azure](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). È anche possibile automatizzare questi backup usando [il backup automatico per SQL Server in macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Prima di SQL Server 2012, usare lo [strumento di backup di SQL Server in Azure](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità effettiva di backup usando più destinazioni di backup in striping.

### <a name="sql-server-data-files-in-azure"></a>File di dati di SQL Server in Azure

questa nuova funzionalità ([File di dati di SQL Server in Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)) è disponibile a partire da SQL Server 2014. L'esecuzione di SQL Server con file di dati in Azure dimostra le caratteristiche di prestazioni paragonabili a quelle dei dischi dati di Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Istanza del cluster di failover e Spazi di archiviazione

Se si usa spazi di archiviazione, quando si aggiungono nodi al cluster nella pagina **conferma** , deselezionare la casella **di controllo Aggiungi tutte le archiviazioni idonee al cluster**. 

![Deselezionare le risorse di archiviazione idonee](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Se si usa Spazi di archiviazione e non si deseleziona **Aggiungi tutte le risorse di archiviazione idonee al cluster**, Windows rende non visibili i dischi virtuali durante il processo di clustering. Di conseguenza, tali dischi non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati usando PowerShell. Spazi di archiviazione consente di raggruppare più dischi in pool di archiviazione. Per altre informazioni, vedere [Spazi di archiviazione](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Istanze multiple 

Quando si distribuiscono più istanze di SQL Server in una singola macchina virtuale, tenere presenti le seguenti procedure consigliate: 

- Impostare la memoria massima del server per ogni istanza di SQL Server, assicurandosi che rimanga memoria rimanente per il sistema operativo. Assicurarsi di aggiornare le restrizioni di memoria per le istanze di SQL Server se si modifica la quantità di memoria allocata alla macchina virtuale. 
- Disporre di lun distinti per i dati, i log e TempDB poiché tutti hanno modelli di carico di lavoro diversi e non si vuole che influiscano tra loro. 
- Eseguire test approfonditi dell'ambiente in carichi di lavoro di produzione intensivi per garantire la massima capacità di carico di lavoro nei contratti di licenza dell'applicazione. 

I segnali di sistemi di overload possono includere, tra l'altro, l'esaurimento dei thread di lavoro, tempi di risposta lenti e/o memoria di sistema Dispatcher bloccata. 



## <a name="collect-performance-baseline"></a>Raccolta dati di riferimento per le prestazioni

Per un approccio più prescrittivo, raccogliere i contatori delle prestazioni usando PerfMon/LogMan e acquisire SQL Server le statistiche di attesa per comprendere meglio le pressioni generali e i potenziali colli di bottiglia dell'ambiente di origine. 

Per iniziare, raccogliere la CPU, la [memoria, le](../../../virtual-machines/premium-storage-performance.md#iops)operazioni di i/o al secondo, la [velocità effettiva](../../../virtual-machines/premium-storage-performance.md#throughput)e [la](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) [latenza](../../../virtual-machines/premium-storage-performance.md#latency) del carico di lavoro di origine in momenti di picco successivi a 

Raccogliere i dati durante le ore di picco, ad esempio i carichi di lavoro durante la giornata lavorativa tipica, ma anche altri processi di carico elevato, ad esempio l'elaborazione di fine giornata e i carichi di lavoro ETL del fine settimana. Prendere in considerazione la scalabilità verticale delle risorse per carichi di lavoro in modo atipico, ad esempio l'elaborazione di fine trimestre, quindi eseguire il ridimensionamento dopo il completamento del carico di lavoro. 

Usare l'analisi delle prestazioni per selezionare le [dimensioni della VM](../../../virtual-machines/sizes-memory.md) che possono essere ridimensionate in modo da soddisfare i requisiti di prestazioni del carico di lavoro


### <a name="iops-and-throughput"></a>IOPS e velocità effettiva

SQL Server prestazioni dipende molto dal sottosistema di I/O. A meno che il database non si inserisca nella memoria fisica, SQL Server porta costantemente le pagine del database all'interno e all'esterno del pool di buffer. I file di dati per SQL Server devono essere trattati in modo diverso. L'accesso ai file di log è sequenziale tranne quando è necessario eseguire il rollback di una transazione in cui è possibile accedere in modo casuale ai file di dati, incluso il database TempDB. Se si dispone di un sottosistema di I/O lento, gli utenti potrebbero riscontrare problemi di prestazioni, ad esempio tempi di risposta lenti e attività che non vengono completate a causa di timeout. 

Per impostazione predefinita, le macchine virtuali di Azure Marketplace hanno file di log in un disco fisico separato dai file di dati. Il numero e le dimensioni dei file di dati di TempDB soddisfano le procedure consigliate e sono assegnati all'effimero D:/ unità.. 

I contatori PerfMon seguenti consentono di convalidare la velocità effettiva di i/o richiesta dal SQL Server: 
* **\LogicalDisk\Disk letture/sec** (IOPS di lettura e scrittura)
* **Scritture \LogicalDisk\Disk/sec** (IOPS di lettura e scrittura) 
* **\LogicalDisk\Disk byte/sec** (requisiti di velocità effettiva per i file di dati, log e tempdb)

Usando i requisiti di IOPS e velocità effettiva ai livelli di picco, valutare le dimensioni delle VM che corrispondono alla capacità delle misurazioni. 

Se il carico di lavoro richiede 20 operazioni di i/o al secondo e 10.000 di scrittura, è possibile scegliere E16s_v3 (con un massimo di 32 K memorizzati nella cache e 25600 IOPS non memorizzati nella cache) o M16_s (con un massimo di 20 K memorizzati nella cache e 10.000 IOPS non memorizzati nella cache) con 2 dischi P30 con striping con spazi di archiviazione. 

Assicurarsi di comprendere i requisiti di velocità effettiva e IOPS del carico di lavoro perché le macchine virtuali hanno limiti di scalabilità diversi per IOPS e velocità effettiva.

### <a name="memory"></a>Memoria

Tenere traccia della memoria esterna usata dal sistema operativo e della memoria utilizzata internamente da SQL Server. L'identificazione della pressione per uno dei due componenti consente di ridimensionare le macchine virtuali e identificare le opportunità di ottimizzazione. 

I contatori PerfMon seguenti consentono di convalidare l'integrità della memoria di una macchina virtuale SQL Server: 
* [\Memoria\MByte disponibili](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memoria memoria server Manager\Target (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memoria memoria server Manager\Total (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy scritture/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\letture permanenza presunta](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Calcolo/elaborazione

Il calcolo in Azure viene gestito in modo diverso rispetto a quello locale. I server locali sono stati compilati per durare diversi anni senza un aggiornamento a causa del sovraccarico di gestione e dei costi di acquisizione di nuovi componenti hardware. La virtualizzazione mitiga alcuni di questi problemi, ma le applicazioni sono ottimizzate per sfruttare al meglio l'hardware sottostante, ovvero qualsiasi modifica significativa all'utilizzo delle risorse richiede il ribilanciamento dell'intero ambiente fisico. 

Non si tratta di una sfida in Azure, in cui una nuova macchina virtuale in una serie diversa di hardware e anche in un'area diversa è facile da realizzare. 

In Azure, si desidera sfruttare il maggior numero possibile di risorse delle macchine virtuali, pertanto le macchine virtuali di Azure devono essere configurate in modo da garantire la media della CPU più elevata possibile senza alcun effetto sul carico di lavoro. 

I contatori PerfMon seguenti consentono di convalidare l'integrità di calcolo di una macchina virtuale SQL Server:
* **Tempo processore informazioni \Processor (_Total) \%**
* **Tempo processore \Processo (sqlservr) \%**

> [!NOTE] 
> Idealmente, provare a puntare all'uso del 80% del calcolo, con picchi superiori al 90% ma che non raggiungano il 100% per un periodo di tempo prolungato. Fondamentalmente, è sufficiente eseguire il provisioning delle risorse di calcolo necessarie per l'applicazione e quindi pianificare la scalabilità verticale o verticale in base alle esigenze aziendali. 

## <a name="next-steps"></a>Passaggi successivi

Per le procedure di sicurezza consigliate, vedere [considerazioni sulla sicurezza per SQL Server in macchine virtuali di Azure](security-considerations-best-practices.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
