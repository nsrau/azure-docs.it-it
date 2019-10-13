---
title: Disponibilità elevata - Servizio del database SQL di Azure | Documentazione Microsoft
description: Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/11/2019
ms.openlocfilehash: 0307a905c1d3d7d9bc707fbda87fb8f3fd6d2aee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299703"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure

L'obiettivo dell'architettura a disponibilità elevata nel database SQL di Azure è garantire che il database sia in esecuzione il 99,99% del tempo, senza doversi preoccupare dell'effetto delle operazioni di manutenzione e delle interruzioni. Azure gestisce automaticamente le attività di manutenzione critiche, ad esempio l'applicazione di patch, i backup, gli aggiornamenti di Windows e SQL, nonché gli eventi non pianificati, ad esempio l'hardware sottostante, software o errori di rete.  Quando l'istanza di SQL sottostante viene patchata o viene eseguito il failover, il tempo di inattività non è evidente se si [Usa la logica di ripetizione dei tentativi](sql-database-develop-overview.md#resiliency) nell'app. Database SQL di Azure può effettuare rapidamente il recupero anche nei casi più critici garantendo che i dati siano sempre disponibili.

La soluzione a disponibilità elevata è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi a causa di errori, che le operazioni di manutenzione non influiscano sul carico di lavoro e che il database non sia un singolo punto di guasto nell'architettura software. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione. 

Sono disponibili due modelli architetturali a disponibilità elevata usati nel database SQL di Azure:

- Modello di disponibilità standard basato su una separazione tra calcolo e archiviazione.  Si basa sull'affidabilità e sulla disponibilità elevata del livello di archiviazione remoto. Questa architettura è destinata alle applicazioni aziendali orientate al budget che possono tollerare un calo delle prestazioni durante le attività di manutenzione.
- Modello di disponibilità Premium basato su un cluster di processi del motore di database. Si basa sul fatto che esiste sempre un quorum dei nodi del motore di database disponibili. Questa architettura è destinata alle applicazioni mission-critical con prestazioni di i/o elevate, velocità elevata delle transazioni e garantisce un effetto minimo sulle prestazioni del carico di lavoro durante le attività

Il database SQL di Azure viene eseguito sulla versione stabile più recente di SQL Server motore di database e sul sistema operativo Windows e la maggior parte degli utenti non noterà che gli aggiornamenti vengono eseguiti in modo continuo.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilità dei livelli di servizio Basic, Standard e Utilizzo generico

Questi livelli di servizio sfruttano l'architettura di disponibilità standard. Nella figura seguente sono illustrati quattro nodi diversi con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-high-availability/general-purpose-service-tier.png)

Il modello di disponibilità standard include due livelli:

- Livello di calcolo senza stato che esegue il processo `sqlservr.exe` e contiene solo dati temporanei e memorizzati nella cache, ad esempio TempDB, i database modello nell'unità SSD collegata e la cache dei piani, il pool di buffer e il pool columnstore in memoria. Questo nodo senza stato viene gestito da Azure Service Fabric che Inizializza `sqlservr.exe`, controlla l'integrità del nodo ed esegue il failover in un altro nodo, se necessario.
- Un livello dati con stato con i file di database (con estensione MDF/ldf) archiviati nell'archivio BLOB di Azure. Archiviazione BLOB di Azure include funzionalità integrate di disponibilità e ridondanza dei dati. Garantisce che tutti i record nel file di log o nella pagina del file di dati verranno conservati anche se SQL Server processo si arresta in modo anomalo.

Ogni volta che il motore di database o il sistema operativo viene aggiornato o viene rilevato un errore, Azure Service Fabric sposterà il processo di SQL Server senza stato in un altro nodo di calcolo senza stato con capacità sufficiente. I dati nell'archivio BLOB di Azure non sono interessati dallo spostamento e i file di dati e di log vengono associati al processo di SQL Server appena inizializzato. Questo processo garantisce una disponibilità del 99,99%, ma un carico di lavoro elevato può subire un calo delle prestazioni durante la transizione, perché la nuova istanza di SQL Server inizia con la cache a freddo.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilità dei livelli di servizio Premium e Business critical

I livelli di servizio Premium e business critical sfruttano il modello di disponibilità Premium, che integra le risorse di calcolo (SQL Server processo di motore di database) e l'archiviazione (SSD collegato localmente) in un singolo nodo. Per ottenere la disponibilità elevata, è possibile eseguire la replica di risorse di calcolo e di archiviazione in nodi aggiuntivi creando un cluster da tre a quattro nodi. 

![Cluster di nodi di motore di database](media/sql-database-high-availability/business-critical-service-tier.png)

I file di database sottostanti (con estensione MDF/ldf) vengono inseriti nell'archivio SSD collegato per fornire i/o a latenza molto bassa per il carico di lavoro. La disponibilità elevata viene implementata utilizzando una tecnologia simile a SQL Server [Always on gruppi di disponibilità](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Il cluster include una singola replica primaria (SQL Server processo) accessibile per i carichi di lavoro dei clienti in lettura/scrittura e fino a tre repliche secondarie (calcolo e archiviazione) contenenti copie di dati. Il nodo primario esegue costantemente il push delle modifiche nei nodi secondari in ordine e garantisce che i dati vengano sincronizzati con almeno una replica secondaria prima di eseguire il commit di ogni transazione. Questo processo garantisce che in caso di arresto anomalo del nodo primario per qualsiasi motivo, è sempre presente un nodo completamente sincronizzato in cui eseguire il failover. Il failover viene avviato dal Service Fabric di Azure. Quando la replica secondaria diventa il nuovo nodo primario, viene creata un'altra replica secondaria per garantire che il cluster disponga di un numero sufficiente di nodi (set di quorum). Al termine del failover, le connessioni SQL vengono reindirizzate automaticamente al nuovo nodo primario.

Come vantaggio aggiuntivo, il modello di disponibilità Premium include la possibilità di reindirizzare le connessioni SQL di sola lettura a una delle repliche secondarie. Questa funzionalità è chiamata [scalabilità in lettura](sql-database-read-scale-out.md). Offre una capacità di calcolo aggiuntiva del 100% senza costi aggiuntivi per le operazioni di sola lettura non caricate, ad esempio i carichi di lavoro analitici, dalla replica primaria.

## <a name="hyperscale-service-tier-availability"></a>Disponibilità del livello di servizio con iperscalabilità

L'architettura del livello di servizio con iperscalabilità è descritta in [architettura di funzioni distribuite](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Architettura funzionale iperscalabile](./media/sql-database-hyperscale/hyperscale-architecture.png)

Il modello di disponibilità in iperscalabilità include quattro livelli:

- Un livello di calcolo senza stato che esegue i processi `sqlservr.exe` e contiene solo dati temporanei e memorizzati nella cache, ad esempio la cache RBPEX non coprente, TempDB, il database modello e così via, nell'unità SSD collegata e nella cache dei piani, nel pool di buffer e nel pool columnstore in memoria. Questo livello senza stato include la replica di calcolo primaria e, facoltativamente, un numero di repliche di calcolo secondarie che possono fungere da destinazioni di failover.
- Livello di archiviazione senza stato formato da server di pagine. Questo livello è il motore di archiviazione distribuito per i processi `sqlservr.exe` in esecuzione sulle repliche di calcolo. Ogni server della pagina contiene solo dati temporanei e memorizzati nella cache, ad esempio la copertura della cache RBPEX nell'unità SSD collegata e le pagine di dati memorizzate nella cache. Ogni server di pagina dispone di un server di paging associato in una configurazione Active-Active per fornire bilanciamento del carico, ridondanza e disponibilità elevata.
- Un livello di archiviazione del log delle transazioni con stato formato dal nodo di calcolo che esegue il processo del servizio di log, l'area di destinazione del log delle transazioni e l'archiviazione a lungo termine del log delle transazioni. La zona di destinazione e l'archiviazione a lungo termine usano archiviazione di Azure, che fornisce disponibilità e [ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy) per il log delle transazioni, garantendo la durabilità dei dati per le transazioni di cui
- Un livello di archiviazione dati con stato con i file di database (con estensione MDF/NDF) archiviati in archiviazione di Azure e aggiornati dai server delle pagine. Questo livello usa le funzionalità di disponibilità e [ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dei dati di archiviazione di Azure. Garantisce che ogni pagina di un file di dati venga mantenuta anche se i processi in altri livelli di architettura iperscalare si arrestano in modo anomalo o se i nodi di calcolo hanno esito negativo.

I nodi di calcolo in tutti i livelli di iperscalabilità vengono eseguiti in Service Fabric di Azure, che controlla l'integrità di ogni nodo ed esegue i failover ai nodi integri disponibili se necessario.

Per altre informazioni sulla disponibilità elevata in iperscalabilità, vedere [disponibilità elevata del database in iperscalabilità](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configurazione con ridondanza della zona

Per impostazione predefinita, il cluster di nodi per il modello di disponibilità Premium viene creato nello stesso data center. Con l'introduzione di [zone di disponibilità di Azure](../availability-zones/az-overview.md), il database SQL può inserire repliche diverse del database di business critical a diverse zone di disponibilità nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona nei livelli di servizio Premium o business critical non crea ridondanza aggiuntiva del database, è possibile abilitarla senza costi aggiuntivi. Selezionando una configurazione con ridondanza della zona, è possibile rendere i database Premium o business critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del Data Center, senza apportare modifiche alla logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché i database con ridondanza della zona hanno repliche in data center diversi con una certa distanza tra di essi, la latenza di rete aumentata può aumentare il tempo di commit e quindi influisca sulle prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione online simile al normale aggiornamento del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> I database con ridondanza della zona e i pool elastici sono attualmente supportati solo nei livelli di servizio Premium e business critical in aree selezionate. Quando si usa il livello di business critical, la configurazione con ridondanza della zona è disponibile solo quando è selezionato l'hardware di calcolo quinta generazione. Per informazioni aggiornate sulle aree che supportano i database con ridondanza della zona, vedere [supporto dei servizi in base all'area](../availability-zones/az-overview.md#services-support-by-region).  
> Questa funzionalità non è disponibile nell'istanza gestita.

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) è una nuova funzionalità del motore di database SQL che migliora notevolmente la disponibilità dei database, soprattutto in presenza di transazioni a esecuzione prolungata. Questa funzionalità è attualmente disponibile per database singoli, pool elastici e Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusione

Il database SQL di Azure offre una soluzione di disponibilità elevata incorporata che è strettamente integrata con la piattaforma Azure. Dipende da Service Fabric per il rilevamento e il ripristino degli errori, sull'archiviazione BLOB di Azure per la protezione dei dati e su zone di disponibilità per una maggiore tolleranza di errore. Inoltre, il database SQL di Azure sfrutta la Always On tecnologia del gruppo di disponibilità SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di realizzare in modo completo i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più complessi.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
