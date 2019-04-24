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
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392658"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure

L'obiettivo dell'architettura di disponibilità elevata in Database SQL di Azure è garanzia che il database sia attivo e in esecuzione il 99,99% del tempo, senza doversi preoccupare l'impatto delle operazioni di manutenzione e le interruzioni. Azure gestisce automaticamente le attività di manutenzione critiche, ad esempio l'applicazione di patch, backup, gli aggiornamenti di Windows e SQL, nonché eventi non pianificati, ad esempio errori hardware, software o di rete sottostanti.  Quando l'istanza SQL sottostante viene riparata o viene eseguito il failover, il tempo di inattività non è evidente se si [impiegare per la logica di ripetizione dei tentativi](sql-database-develop-overview.md#resiliency) nell'app. Database SQL di Azure può effettuare rapidamente il recupero anche nei casi più critici garantendo che i dati siano sempre disponibili.

La soluzione a disponibilità elevata è progettata per garantire che il commit dei dati non sono mai persi a causa di errori, che le operazioni di manutenzione non influiscono sul carico di lavoro, e che il database non sarà un singolo punto di guasto nell'architettura software. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione. 

Esistono due modelli di architettura a disponibilità elevata utilizzabili in Database SQL di Azure:

- Modello di disponibilità standard basato su una separazione tra calcolo e archiviazione.  Si basa su un'elevata disponibilità e affidabilità del livello di archiviazione remota. Questa architettura è destinato alle applicazioni di business orientate al budget che possono tollerare un peggioramento delle prestazioni durante le attività di manutenzione.
- Modello di disponibilità di Premium che si basa su un cluster dei processi del motore di database. Si basa sul fatto che ci sia sempre un quorum di nodi di motore di database disponibili. Questa architettura è destinato a applicazioni mission-critical con prestazioni dei / o elevate, elevata frequenza di transazioni e garanzie impatto minimo sulle prestazioni per il carico di lavoro durante le attività di manutenzione.

Database SQL di Azure esegue la versione stabile più recente del motore di Database di SQL Server e del sistema operativo Windows e la maggior parte degli utenti non noterà che gli aggiornamenti vengono eseguiti in modo continuo.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilità dei livelli di servizio Basic, Standard e Utilizzo generico

Questi livelli di servizio sfruttano l'architettura di disponibilità standard. La figura seguente illustra quattro nodi diversi con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-high-availability/general-purpose-service-tier.png)

Il modello di disponibilità standard include due livelli:

- Un livello di calcolo senza stato che viene eseguito il `sqlserver.exe` elaborare e contiene solo temporanei e memorizzati nella cache i dati su unità SSD collegata, ad esempio pool di archiviazione di TempDB, modello di database, cache dei piani, pool di buffer e colonna. Questo nodo senza stato è gestito da Azure Service Fabric che consente di inizializzare `sqlserver.exe`, controlla l'integrità del nodo ed esegue il failover a un altro nodo se necessario.
- Un livello di dati con stato con i file di database (.mdf/.ldf) che vengono archiviati in archiviazione Blob di Azure. Archivio blob di Azure include la disponibilità dei dati incorporate e la funzionalità di ridondanza. Garantisce che ogni record nel file di log o nella pagina nel file di dati verranno mantenuti anche se si blocca il processo di SQL Server.

Ogni volta che viene aggiornato il motore di database o del sistema operativo, o viene rilevato un errore, Azure Service Fabric sposta il processo di SQL Server senza stato in un altro nodo di calcolo senza stato con una capacità sufficiente disponibile. Dati in archiviazione Blob di Azure non sono interessati dallo spostamento e i file di dati o di log associati al processo di SQL Server appena inizializzato. Questo processo garantisce disponibilità al 99,99%, ma un carico di lavoro potrebbe notare un calo delle prestazioni durante la transizione poiché la nuova istanza di SQL Server viene avviato con la cache a freddo.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilità dei livelli di servizio Premium e Business critical

Premium e come sfruttare i livelli di servizio Business Critical al modello di disponibilità Premium, che si integra calcolo delle risorse (processo di SQL Server Database Engine) e archiviazione (SSD collegato al computer locale) in un singolo nodo. Disponibilità elevata si ottiene tramite la replica di archiviazione e calcolo in nodi aggiuntivi creando un cluster a quattro composto da tre nodi. 

![Cluster di nodi di motore di database](media/sql-database-high-availability/business-critical-service-tier.png)

I file di database sottostante (.mdf/.ldf) vengono inseriti nello spazio di archiviazione unità SSD associata per offrire una latenza molto bassa i/o al carico di lavoro. Disponibilità elevata viene implementata usando una tecnologia simile a SQL Server [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Il cluster include una singola replica primaria (processo di SQL Server) che è accessibile per i carichi di lavoro dei clienti di lettura / scrittura e fino a tre repliche secondarie (calcolo e archiviazione) che contiene copie dei dati. Il nodo primario costantemente effettua il push delle modifiche ai nodi secondari in ordine e assicura che i dati vengono sincronizzati per almeno una replica secondaria prima del commit di ogni transazione. Questo processo garantisce che se il nodo primario si blocca per qualche motivo, è sempre un nodo completamente sincronizzato per eseguire il failover. Il failover viene avviato da Azure Service Fabric. Dopo la replica secondaria diventa il nuovo nodo primario, viene creata un'altra replica secondaria per assicurarsi che il cluster dispone di sufficienti nodi (set di quorum). Al termine del failover, le connessioni SQL verranno reindirizzate automaticamente al nuovo nodo primario.

Come vantaggio aggiuntivo, il modello di disponibilità premium include la possibilità di reindirizzare le connessioni di SQL di sola lettura a una delle repliche secondarie. Questa funzionalità è detta [scalabilità in lettura](sql-database-read-scale-out.md). Fornisce 100% ulteriori capacità di calcolo senza alcun costo aggiuntivo a ricaricamenti operazioni di sola lettura, ad esempio carichi di lavoro analitici, dalla replica primaria.

## <a name="zone-redundant-configuration"></a>Configurazione con ridondanza della zona

Per impostazione predefinita, viene creato il cluster di nodi per il modello di disponibilità premium nello stesso datacenter. Con l'introduzione del [zone di disponibilità di Azure](../availability-zones/az-overview.md), Database SQL può posizionare repliche diverse nel cluster di diverse zone di disponibilità nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché configurazione nei livelli di servizio Premium o Business Critical con ridondanza della zona non crea ridondanza aggiuntiva del database, è possibile abilitarlo senza costi aggiuntivi. Se si seleziona una configurazione con ridondanza della zona, è possibile rendere i database Premium o Business Critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del Data Center, senza apportare modifiche alla logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché i database con ridondanza della zona hanno repliche in diversi Data Center distanti tra loro, la maggiore latenza di rete può aumentare il tempo di commit e pertanto compromettere le prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione online simile per l'aggiornamento del livello servizio regolari. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> Database con ridondanza della zona e i pool elastici sono attualmente supportati solo nei livelli di servizio Premium e Business Critical. Per impostazione predefinita, i backup e i record di controllo vengono archiviati nell'archiviazione con ridondanza geografica e accesso in lettura. È quindi possibile che non siano disponibili automaticamente in caso di un'interruzione a livello di zona. 

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

[Accelerated ripristino Database (ADR)](sql-database-accelerated-database-recovery.md) è una nuova funzionalità di motore di database SQL che consente di migliorare notevolmente la disponibilità del database, in particolare in presenza di lunga esecuzione di transazioni. Questa funzionalità è attualmente disponibile per database singoli, pool elastici e Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusioni

Database SQL di Azure offre una soluzione a disponibilità elevata incorporata, che è perfettamente integrata con la piattaforma Azure. È dipendente in Service Fabric per il ripristino e il rilevamento degli errori, in archiviazione Blob di Azure per la protezione dati e sulle zone di disponibilità per la tolleranza di errore. Inoltre, database SQL di Azure sfrutta la tecnologia di gruppo di disponibilità AlwaysOn di SQL Server per la replica e failover. La combinazione di queste tecnologie consente alle applicazioni di sfruttare pienamente i vantaggi di una risorsa di archiviazione mista modellare e supportano i contratti di servizio più esigenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
