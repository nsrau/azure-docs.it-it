---
title: Disponibilità elevata
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Informazioni sulle funzionalità e le funzionalità di disponibilità elevata del database SQL di Azure e del servizio SQL Istanza gestita
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: sstein, sashan
ms.date: 10/28/2020
ms.openlocfilehash: e5e58f8592fcf8627870c3a574335bbe34394064
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452465"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Disponibilità elevata per database SQL di Azure e SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

L'obiettivo dell'architettura a disponibilità elevata nel database SQL di Azure e in SQL Istanza gestita è garantire che il database sia operativo per almeno il 99,99% del tempo (per altre informazioni su un contratto di sicurezza specifico per diversi livelli, vedere il [contratto di contratto per il database SQL di Azure e sql istanza gestita](https://azure.microsoft.com/support/legal/sla/sql-database/)), senza preoccuparsi dell'effetto delle operazioni di manutenzione e delle interruzioni. Azure gestisce automaticamente le attività di manutenzione critiche, ad esempio l'applicazione di patch, i backup, gli aggiornamenti di Windows e SQL di Azure, nonché gli eventi non pianificati, ad esempio l'hardware, il software o gli errori di rete sottostanti.  Quando si applica una patch al database sottostante nel database SQL di Azure o si esegue il failover, il tempo di inattività non è evidente se si [Usa la logica di ripetizione dei tentativi](develop-overview.md#resiliency) nell'app. Il database SQL e il Istanza gestita SQL possono essere ripristinati rapidamente anche nelle circostanze più critiche, garantendo che i dati siano sempre disponibili.

La soluzione a disponibilità elevata è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi a causa di errori, che le operazioni di manutenzione non influiscano sul carico di lavoro e che il database non sia un singolo punto di guasto nell'architettura software. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione.

Sono disponibili due modelli di architettura a disponibilità elevata:

- **Modello di disponibilità standard** basato su una separazione tra calcolo e archiviazione.  Si basa sull'affidabilità e sulla disponibilità elevata del livello di archiviazione remoto. Questa architettura è destinata alle applicazioni aziendali orientate al budget che possono tollerare un calo delle prestazioni durante le attività di manutenzione.
- **Modello di disponibilità Premium** basato su un cluster di processi del motore di database. Si basa sul fatto che esiste sempre un quorum dei nodi del motore di database disponibili. Questa architettura è destinata alle applicazioni mission-critical con prestazioni di i/o elevate, velocità elevata delle transazioni e garantisce un effetto minimo sulle prestazioni del carico di lavoro durante le attività

Il database SQL e SQL Istanza gestita entrambi eseguiti sulla versione stabile più recente del motore di database SQL Server e del sistema operativo Windows e la maggior parte degli utenti non noterà che gli aggiornamenti vengono eseguiti in modo continuo.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Disponibilità con ridondanza locale del livello di servizio Basic, standard e per utilizzo generico

I livelli di servizio Basic, standard e per utilizzo generico sfruttano l'architettura di disponibilità standard per le risorse di calcolo senza server e con provisioning. Nella figura seguente sono illustrati quattro nodi diversi con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](./media/high-availability-sla/general-purpose-service-tier.png)

Il modello di disponibilità standard include due livelli:

- Livello di calcolo senza stato che esegue il `sqlservr.exe` processo e contiene solo dati temporanei e memorizzati nella cache, ad esempio tempdb, i database modello nell'unità SSD collegata e la cache dei piani, il pool di buffer e il pool columnstore in memoria. Questo nodo senza stato viene gestito da Azure Service Fabric che inizializza `sqlservr.exe` , controlla l'integrità del nodo ed esegue il failover in un altro nodo, se necessario.
- Un livello dati con stato con i file di database (con estensione MDF/ldf) archiviati nell'archivio BLOB di Azure. Archiviazione BLOB di Azure include funzionalità integrate di disponibilità e ridondanza dei dati. Garantisce che tutti i record nel file di log o nella pagina del file di dati verranno conservati anche se si verifica un `sqlservr.exe` arresto anomalo del processo.

Ogni volta che il motore di database o il sistema operativo viene aggiornato o viene rilevato un errore, Azure Service Fabric sposterà il `sqlservr.exe` processo senza stato in un altro nodo di calcolo senza stato con capacità disponibile sufficiente. I dati nell'archivio BLOB di Azure non sono interessati dallo spostamento e i file di dati/log sono collegati al processo appena inizializzato `sqlservr.exe` . Questo processo garantisce una disponibilità del 99,99%, ma un carico di lavoro elevato può subire un calo delle prestazioni durante la transizione, dal momento che il nuovo `sqlservr.exe` processo inizia con la cache a freddo.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Per utilizzo generico disponibilità con ridondanza della zona del livello di servizio (anteprima)

La configurazione con ridondanza della zona per il livello di servizio per utilizzo generico Usa [zone di disponibilità di Azure](../../availability-zones/az-overview.md)   per replicare i database in più posizioni fisiche all'interno di un'area di Azure.Selezionando ridondanza della zona, è possibile fare in modo che i database singoli e i pool elastici nuovi ed esistenti siano resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del Data Center, senza apportare modifiche alla logica dell'applicazione.

La configurazione con ridondanza della zona per il livello utilizzo generico è costituita da due livelli:  

- Livello dati con stato con i file di database (con estensione MDF/ldf) archiviati in ZRS PFS ( [condivisione file Premium di archiviazione](../../storage/files/storage-how-to-create-premium-fileshare.md)con ridondanza della zona. Usando l' [archiviazione con ridondanza della zona](../../storage/common/storage-redundancy.md) i dati e i file di log vengono copiati in modo sincrono in tre zone di disponibilità di Azure isolate fisicamente.
- Livello di calcolo senza stato che esegue il processo di sqlservr.exe e contiene solo dati temporanei e memorizzati nella cache, ad esempio TempDB, i database modello nell'unità SSD collegata e la cache dei piani, il pool di buffer e il pool columnstore in memoria. Questo nodo senza stato viene gestito da Azure Service Fabric che Inizializza sqlservr.exe, controlla l'integrità del nodo ed esegue il failover in un altro nodo, se necessario. Per i database per utilizzo generico con ridondanza della zona, i nodi con capacità di riserva sono immediatamente disponibili in altri zone di disponibilità per il failover.

La versione con ridondanza della zona dell'architettura a disponibilità elevata per il livello di servizio utilizzo generico è illustrata nel diagramma seguente:

![Configurazione con ridondanza della zona per utilizzo generico](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Per informazioni aggiornate sulle aree che supportano i database con ridondanza della zona, vedere [supporto dei servizi in base all'area](../../availability-zones/az-region.md). La configurazione con ridondanza della zona è disponibile solo quando è selezionato l'hardware di calcolo quinta generazione. Questa funzionalità non è disponibile in SQL Istanza gestita.

> [!NOTE]
> Per utilizzo generico database con dimensioni pari a 80 Vcore può comportare un calo delle prestazioni con la configurazione con ridondanza della zona. Inoltre, le operazioni come il backup, il ripristino, la copia del database e la configurazione delle relazioni di ripristino di emergenza geografico possono comportare prestazioni più lente per i singoli database di dimensioni superiori a 1 TB. 

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Disponibilità con ridondanza locale del livello di servizio Premium e business critical

I livelli di servizio Premium e business critical sfruttano il modello di disponibilità Premium, che integra risorse di calcolo ( `sqlservr.exe` processo) e archiviazione (SSD collegato localmente) in un singolo nodo. Per ottenere la disponibilità elevata, è possibile eseguire la replica di risorse di calcolo e di archiviazione in nodi aggiuntivi creando un cluster da tre a quattro nodi.

![Cluster di nodi di motore di database](./media/high-availability-sla/business-critical-service-tier.png)

I file di database sottostanti (con estensione MDF/ldf) vengono inseriti nell'archivio SSD collegato per fornire i/o a latenza molto bassa per il carico di lavoro. La disponibilità elevata viene implementata utilizzando una tecnologia simile a SQL Server [gruppi di disponibilità always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Il cluster include una singola replica primaria accessibile per i carichi di lavoro dei clienti in lettura/scrittura e fino a tre repliche secondarie (calcolo e archiviazione) contenenti copie di dati. Il nodo primario esegue costantemente il push delle modifiche nei nodi secondari in ordine e garantisce che i dati vengano sincronizzati con almeno una replica secondaria prima di eseguire il commit di ogni transazione. Questo processo garantisce che in caso di arresto anomalo del nodo primario per qualsiasi motivo, è sempre presente un nodo completamente sincronizzato in cui eseguire il failover. Il failover viene avviato dal Service Fabric di Azure. Quando la replica secondaria diventa il nuovo nodo primario, viene creata un'altra replica secondaria per garantire che il cluster disponga di un numero sufficiente di nodi (set di quorum). Al termine del failover, le connessioni SQL di Azure vengono reindirizzate automaticamente al nuovo nodo primario.

Come vantaggio aggiuntivo, il modello di disponibilità Premium include la possibilità di reindirizzare le connessioni SQL di Azure di sola lettura a una delle repliche secondarie. Questa funzionalità è chiamata [scalabilità in lettura](read-scale-out.md). Offre una capacità di calcolo aggiuntiva del 100% senza costi aggiuntivi per le operazioni di sola lettura non caricate, ad esempio i carichi di lavoro analitici, dalla replica primaria.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Disponibilità ridondante della zona del livello di servizio Premium e business critical 

Per impostazione predefinita, il cluster di nodi per il modello di disponibilità Premium viene creato nello stesso data center. Con l'introduzione di [zone di disponibilità di Azure](../../availability-zones/az-overview.md), il database SQL può inserire repliche diverse del database di business critical a diverse zone di disponibilità nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona nei livelli di servizio Premium o business critical non crea ridondanza aggiuntiva del database, è possibile abilitarla senza costi aggiuntivi. Selezionando una configurazione con ridondanza della zona, è possibile rendere i database Premium o business critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del Data Center, senza apportare modifiche alla logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché i database con ridondanza della zona hanno repliche in data center diversi con una certa distanza tra di essi, la latenza di rete aumentata può aumentare il tempo di commit e quindi influisca sulle prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione online simile al normale aggiornamento del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> Quando si usa il livello di business critical, la configurazione con ridondanza della zona è disponibile solo quando è selezionato l'hardware di calcolo quinta generazione. Per informazioni aggiornate sulle aree che supportano i database con ridondanza della zona, vedere [supporto dei servizi in base all'area](../../availability-zones/az-region.md).

> [!NOTE]
> Questa funzionalità non è disponibile in SQL Istanza gestita.

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Disponibilità del livello di servizio con iperscalabilità

L'architettura del livello di servizio con iperscalabilità è descritta in [architettura di funzioni distribuite](./service-tier-hyperscale.md#distributed-functions-architecture) ed è attualmente disponibile solo per il database SQL, non per SQL istanza gestita.

![Architettura funzionale iperscalabile](./media/high-availability-sla/hyperscale-architecture.png)

Il modello di disponibilità in iperscalabilità include quattro livelli:

- Livello di calcolo senza stato che esegue i `sqlservr.exe` processi e contiene solo dati temporanei e memorizzati nella cache, ad esempio cache RBPEX non coprente, tempdb, database modello e così via, nell'unità SSD collegata e nella cache dei piani, nel pool di buffer e nel pool columnstore in memoria. Questo livello senza stato include la replica di calcolo primaria e, facoltativamente, un numero di repliche di calcolo secondarie che possono fungere da destinazioni di failover.
- Livello di archiviazione senza stato formato da server di pagine. Questo livello è il motore di archiviazione distribuito per i `sqlservr.exe` processi in esecuzione nelle repliche di calcolo. Ogni server della pagina contiene solo dati temporanei e memorizzati nella cache, ad esempio la copertura della cache RBPEX nell'unità SSD collegata e le pagine di dati memorizzate nella cache. Ogni server di pagina dispone di un server di paging associato in una configurazione Active-Active per fornire bilanciamento del carico, ridondanza e disponibilità elevata.
- Un livello di archiviazione del log delle transazioni con stato formato dal nodo di calcolo che esegue il processo del servizio di log, l'area di destinazione del log delle transazioni e l'archiviazione a lungo termine del log delle transazioni. La zona di destinazione e l'archiviazione a lungo termine usano archiviazione di Azure, che fornisce disponibilità e [ridondanza](../../storage/common/storage-redundancy.md) per il log delle transazioni, garantendo la durabilità dei dati per le transazioni di cui
- Un livello di archiviazione dati con stato con i file di database (con estensione MDF/NDF) archiviati in archiviazione di Azure e aggiornati dai server delle pagine. Questo livello usa le funzionalità di disponibilità e [ridondanza](../../storage/common/storage-redundancy.md) dei dati di archiviazione di Azure. Garantisce che ogni pagina di un file di dati venga mantenuta anche se i processi in altri livelli di architettura iperscalare si arrestano in modo anomalo o se i nodi di calcolo hanno esito negativo.

I nodi di calcolo in tutti i livelli di iperscalabilità vengono eseguiti in Service Fabric di Azure, che controlla l'integrità di ogni nodo ed esegue i failover ai nodi integri disponibili se necessario.

Per altre informazioni sulla disponibilità elevata in iperscalabilità, vedere [disponibilità elevata del database in iperscalabilità](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

[Accelerated Database Recovery (ADR)](../accelerated-database-recovery.md) è una nuova funzionalità del motore di database che migliora notevolmente la disponibilità dei database, soprattutto in presenza di transazioni a esecuzione prolungata. ADR è attualmente disponibile per il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics.

## <a name="testing-application-fault-resiliency"></a>Test della resilienza degli errori delle applicazioni

La disponibilità elevata è una parte fondamentale del database SQL e della piattaforma Istanza gestita di SQL che funziona in modo trasparente per l'applicazione del database. Tuttavia, è possibile che si desideri testare il modo in cui le operazioni di failover automatico avviate durante gli eventi pianificati o non pianificati influiranno su un'applicazione prima di distribuirla nella produzione. È possibile attivare manualmente un failover chiamando un'API speciale per riavviare un database, un pool elastico o un'istanza gestita. Nel caso di un database con ridondanza della zona o di un pool elastico, la chiamata API comporterebbe il reindirizzamento delle connessioni client al nuovo primario in una zona di disponibilità diversa dalla zona di disponibilità della replica primaria precedente. Quindi, oltre a testare il modo in cui il failover influisca sulle sessioni di database esistenti, è anche possibile verificare se le prestazioni end-to-end vengono modificate a causa di modifiche alla latenza di rete. Poiché l'operazione di riavvio è intrusiva e un numero elevato di questi potrebbe sottolineare la piattaforma, viene consentita una sola chiamata di failover ogni 15 minuti per ogni database, pool elastico o istanza gestita.

È possibile avviare un failover usando PowerShell, l'API REST o l'interfaccia della riga di comando di Azure:

|Tipo di distribuzione|PowerShell|API REST| Interfaccia della riga di comando di Azure|
|:---|:---|:---|:---|
|Database|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Failover del database](/rest/api/sql/databases(failover)/failover/)|[AZ Rest](/cli/azure/reference-index#az-rest) può essere usato per richiamare una chiamata API REST dall'interfaccia della riga di comando di Azure|
|Pool elastico|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover del pool elastico](/rest/api/sql/elasticpools(failover)/failover/)|[AZ Rest](/cli/azure/reference-index#az-rest) può essere usato per richiamare una chiamata API REST dall'interfaccia della riga di comando di Azure|
|database SQL|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Istanze gestite-failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[failover AZ SQL mi](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> Il comando di failover non è disponibile per le repliche secondarie leggibili dei database con iperscalabilità.

## <a name="conclusion"></a>Conclusione

Il database SQL di Azure e Azure SQL Istanza gestita offrono una soluzione di disponibilità elevata incorporata che è strettamente integrata con la piattaforma Azure. Dipende da Service Fabric per il rilevamento e il ripristino degli errori, sull'archiviazione BLOB di Azure per la protezione dei dati e su zone di disponibilità per una maggiore tolleranza di errore (come indicato in precedenza nel documento non applicabile al Istanza gestita SQL di Azure). Inoltre, il database SQL e SQL Istanza gestita sfruttano la tecnologia del gruppo di disponibilità Always On dall'istanza di SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di realizzare in modo completo i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più complessi.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../../availability-zones/az-overview.md)
- Informazioni sulle [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md)
- Informazioni [su come avviare un failover manuale in SQL istanza gestita](../managed-instance/user-initiated-failover.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md).