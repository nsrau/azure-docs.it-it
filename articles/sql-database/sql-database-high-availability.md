---
title: Disponibilità elevata
description: Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: 1c4ed77112e8c06db1946d756239e02cb187f3ef
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618466"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure

L'obiettivo dell'architettura a disponibilità elevata nel database SQL di Azure consiste nel garantire che il database sia aggiornato e in esecuzione almeno del 99,99% del tempo (per ulteriori informazioni sul contratto di servizio specifico per diversi livelli, fare riferimento al [contratto di servizio per](https://azure.microsoft.com/support/legal/sla/sql-database/)il database SQL di Azure), senza preoccuparsi dell'impatto delle operazioni di manutenzione e delle interruzioni. Azure gestisce automaticamente le attività di manutenzione critiche, ad esempio l'applicazione di patch, i backup, gli aggiornamenti di Windows e SQL, nonché gli eventi non pianificati, ad esempio errori hardware, software o di rete sottostanti.  Quando l'istanza SQL sottostante viene patchata o viene sottoposta a failover, il tempo di inattività non è evidente se si utilizza la logica di [ripetizione dei tentativi](sql-database-develop-overview.md#resiliency) nell'app. Database SQL di Azure può effettuare rapidamente il recupero anche nei casi più critici garantendo che i dati siano sempre disponibili.

La soluzione a disponibilità elevata è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi a causa di errori, che le operazioni di manutenzione non influiscano sul carico di lavoro e che il database non sia un singolo punto di errore nell'architettura software. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione. 

Esistono due modelli architetturali a disponibilità elevata usati nel database SQL di Azure:There are two high-availability architectural models that are used in Azure SQL Database:

- Modello di disponibilità standard basato su una separazione tra elaborazione e archiviazione.  Si basa su disponibilità elevata e affidabilità del livello di archiviazione remota. Questa architettura è destinata alle applicazioni aziendali orientate al budget che possono tollerare una riduzione delle prestazioni durante le attività di manutenzione.
- Modello di disponibilità Premium basato su un cluster di processi del motore di database. Si basa sul fatto che esiste sempre un quorum di nodi del motore di database disponibili. Questa architettura si rivolge alle applicazioni mission-critical con elevate prestazioni di I/O, elevata frequenza delle transazioni e garantisce un impatto minimo sulle prestazioni del carico di lavoro durante le attività di manutenzione.

Il database SQL di Azure viene eseguito sulla versione stabile più recente del Motore di database di SQL ServerSQL Server Database Engine e del sistema operativo Windows e la maggior parte degli utenti non noterebbe che gli aggiornamenti vengono eseguiti continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilità dei livelli di servizio Basic, Standard e Utilizzo generico

Questi livelli di servizio sfruttano l'architettura di disponibilità standard. Nella figura seguente vengono illustrati quattro nodi diversi con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-high-availability/general-purpose-service-tier.png)

Il modello di disponibilità standard include due livelli:The standard availability model includes two layers:

- Livello di calcolo senza `sqlservr.exe` stato che esegue il processo e contiene solo dati temporanei e memorizzati nella cache, ad esempio TempDB, database modello nell'SSD collegato e cache dei piani, pool di buffer e pool di archivi di colonne in memoria. Questo nodo senza stato è gestito da `sqlservr.exe`Azure Service Fabric che inizializza , controlla l'integrità del nodo ed esegue il failover su un altro nodo, se necessario.
- Livello di dati con stato con i file di database (con estensione mdf/ldf) archiviati nell'archivio BLOB di Azure.A stateful data layer with the database files (.mdf/.ldf) that are stored in Azure Blob storage. L'archiviazione BLOB di Azure include funzionalità di ridondanza e disponibilità dei dati incorporate. Garantisce che ogni record nel file di log o nella pagina nel file di dati verrà mantenuto anche se il processo di SQL Server si arresta in modo anomalo.

Ogni volta che il motore di database o il sistema operativo viene aggiornato o viene rilevato un errore, Azure Service Fabric sposterà il processo di SQL Server senza stato in un altro nodo di calcolo senza stato con capacità libera sufficiente. I dati nell'archiviazione BLOB di Azure non sono interessati dallo spostamento e i file di dati/log vengono collegati al processo di SQL Server appena inizializzato. Questo processo garantisce una disponibilità del 99,99%, ma un carico di lavoro intenso potrebbe verificarsi una riduzione delle prestazioni durante la transizione poiché la nuova istanza di SQL Server inizia con la cache a freddo.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilità dei livelli di servizio Premium e Business critical

I livelli di servizio Premium e Business Critical sfruttano il modello di disponibilità Premium, che integra le risorse di calcolo (processo motore di database di SQL Server) e l'archiviazione (SSD collegata localmente) in un singolo nodo. La disponibilità elevata si ottiene replicando sia l'elaborazione che l'archiviazione in nodi aggiuntivi creando un cluster da tre a quattro nodi. 

![Cluster di nodi di motore di database](media/sql-database-high-availability/business-critical-service-tier.png)

I file di database sottostanti (.mdf/.ldf) vengono inseriti nell'archiviazione SSD collegata per fornire un I/O a bassissima latenza al carico di lavoro. La disponibilità elevata viene implementata utilizzando una tecnologia simile a I gruppi di [disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)di SQL Server . Il cluster include una singola replica primaria (processo di SQL Server) accessibile per i carichi di lavoro dei clienti in lettura/scrittura e fino a tre repliche secondarie (calcolo e archiviazione) contenenti copie dei dati. Il nodo primario esegue costantemente il push delle modifiche ai nodi secondari nell'ordine e garantisce che i dati vengano sincronizzati in almeno una replica secondaria prima di eseguire il commit di ogni transazione. Questo processo garantisce che se il nodo primario si arresta in modo anomalo per qualsiasi motivo, è sempre presente un nodo completamente sincronizzato in cui eseguire il failover. The failover is initiated by the Azure Service Fabric. Quando la replica secondaria diventa il nuovo nodo primario, viene creata un'altra replica secondaria per garantire che il cluster disponga di nodi sufficienti (quorum impostato). Al termine del failover, le connessioni SQL vengono automaticamente reindirizzate al nuovo nodo primario.

Come vantaggio aggiuntivo, il modello di disponibilità Premium include la possibilità di reindirizzare connessioni SQL di sola lettura a una delle repliche secondarie. Questa funzionalità è denominata [scalabilità](sql-database-read-scale-out.md)in lettura . Fornisce il 100% di capacità di calcolo aggiuntiva senza costi aggiuntivi per disattivare le operazioni di sola lettura, ad esempio i carichi di lavoro analitici, dalla replica primaria.

## <a name="hyperscale-service-tier-availability"></a>Disponibilità del livello di servizio HyperscaleHyperscale service tier availability

L'architettura del livello di servizio Hyperscale è descritta in Architettura delle [funzioni distribuite.](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) 

![Architettura funzionale hyperscale](./media/sql-database-hyperscale/hyperscale-architecture.png)

Il modello di disponibilità in Hyperscale include quattro livelli:The availability model in Hyperscale includes four layers:

- Livello di calcolo senza `sqlservr.exe` stato che esegue i processi e contiene solo dati temporanei e memorizzati nella cache, ad esempio cache RBPEX non di copertura, TempDB, database modello e così via nell'SSD collegato e cache dei piani, pool di buffer e pool di archivio colonne in memoria. Questo livello senza stato include la replica di calcolo primaria e, facoltativamente, un numero di repliche di calcolo secondarie che possono essere considerate destinazioni di failover.
- Livello di archiviazione senza stato formato dai server di paging. Questo livello è il motore `sqlservr.exe` di archiviazione distribuita per i processi in esecuzione nelle repliche di calcolo. Ogni server di paging contiene solo dati temporanei e memorizzati nella cache, ad esempio la cache RBPEX nell'SSD collegato e le pagine di dati memorizzate nella cache in memoria. Ogni server di paging dispone di un server di paging associato in una configurazione attivo-attivo per fornire bilanciamento del carico, ridondanza e disponibilità elevata.
- Livello di archiviazione del log delle transazioni con stato formato dal nodo di calcolo che esegue il processo del servizio Log, dalla zona di destinazione del log delle transazioni e dall'archiviazione a lungo termine del log delle transazioni. La zona di destinazione e l'archiviazione a lungo termine usano Archiviazione di Azure, che offre disponibilità e [ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy) per il log delle transazioni, garantendo la durata dei dati per le transazioni di cui è stato eseguito il commit.
- Livello di archiviazione dati con stato con i file di database (con estensione mdf/ndf) archiviati in Archiviazione di Azure e aggiornati dai server di paging. Questo livello usa le funzionalità di disponibilità e [ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dei dati di Archiviazione di Azure.This layer uses data availability and redundancy features of Azure Storage. Garantisce che ogni pagina in un file di dati verrà mantenuta anche se i processi in altri livelli dell'architettura Hyperscale si arrestano in modo anomalo o se i nodi di calcolo hanno esito negativo.

I nodi di calcolo in tutti i livelli di Hyperscale vengono eseguiti in Azure Service Fabric, che controlla l'integrità di ogni nodo ed esegue il failover dei nodi integri disponibili in base alle esigenze.

Per ulteriori informazioni sulla disponibilità elevata in Hyperscale, vedere [Disponibilità elevata del database in Hyperscale](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configurazione con ridondanza della zona

Per impostazione predefinita, il cluster di nodi per il modello di disponibilità Premium viene creato nello stesso data center. Con l'introduzione delle zone di disponibilità di [Azure](../availability-zones/az-overview.md), il database SQL può inserire repliche diverse del database Business Critical in zone di disponibilità diverse nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché la configurazione ridondante della zona nei livelli di servizio Premium o Business Critical non crea ridondanza aggiuntiva del database, è possibile abilitarla senza costi aggiuntivi. Selezionando una configurazione ridondante di zona, è possibile rendere i database Premium o Business Critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del datacenter, senza alcuna modifica alla logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché i database ridondanti della zona dispongono di repliche in data center diversi con una certa distanza tra di essi, l'aumento della latenza di rete può aumentare il tempo di commit e quindi influire sulle prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione online simile all'aggiornamento del livello di servizio regolare. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> I database ridondanti e i pool elastici dell'area sono attualmente supportati solo nei livelli di servizio Premium e Business Critical in aree selezionate. Quando si utilizza il livello Business Critical, la configurazione ridondante di zona è disponibile solo quando è selezionato l'hardware di calcolo Gen5. Per informazioni aggiornate sulle aree che supportano i database ridondanti delle zone, vedere [Supporto dei servizi per regione](../availability-zones/az-overview.md#services-support-by-region).  
> Questa funzionalità non è disponibile nell'istanza gestita.

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) è una nuova funzionalità del motore di database SQL che migliora notevolmente la disponibilità del database, soprattutto in presenza di transazioni a esecuzione prolungata. Questa funzionalità è attualmente disponibile per database singoli, pool elastici e Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Test della resilienza degli errori delle applicazioniTesting application fault resiliency

La disponibilità elevata è una parte fondamentale della piattaforma di database SQL di Azure che funziona in modo trasparente per l'applicazione di database. Tuttavia, riconosciamo che è possibile testare il modo in cui le operazioni di failover automatico avviate durante eventi pianificati o non pianificati avrebbero un impatto sull'applicazione prima di distribuirla nell'ambiente di produzione. È possibile chiamare un'API speciale per riavviare un database o un pool elastico, che a sua volta attiverà un failover. Nel caso di un database ridondante di zona o di un pool elastico, la chiamata API comporterebbe il reindirizzamento delle connessioni client al nuovo database primario in una zona di disponibilità diversa dalla zona di disponibilità del database primario precedente. Pertanto, oltre a testare l'impatto del failover sulle sessioni di database esistenti, è anche possibile verificare se modifica le prestazioni end-to-end a causa di modifiche nella latenza di rete. Poiché l'operazione di riavvio è intrusiva e un numero elevato di essi potrebbe sottolineare la piattaforma, è consentita una sola chiamata di failover ogni 30 minuti per ogni database o pool elastico. 

Un failover può essere avviato usando l'API REST o PowerShell.A failover can be initiated using REST API or PowerShell. Per l'API REST, vedere [Failover del database](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e Failover del pool [elastico.](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover) Per PowerShell, vedere [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) e [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). Le chiamate all'API REST possono essere effettuate anche dall'interfaccia della riga di comando di Azure usando il comando [az rest.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> Il comando Failover non è attualmente disponibile nel livello di servizio Hyperscale e per l'istanza gestita.

## <a name="conclusion"></a>Conclusioni

Il database SQL di Azure offre una soluzione a disponibilità elevata incorporata, profondamente integrata con la piattaforma Azure.Azure SQL Database features a built-in high availability solution, that is deeply integrated with the Azure platform. Dipende da Service Fabric per il rilevamento e il ripristino degli errori, nell'archiviazione BLOB di Azure per la protezione dei dati e nelle zone di disponibilità per una maggiore tolleranza di errore. Inoltre, il database SQL di Azure sfrutta la tecnologia del gruppo di disponibilità AlwaysOn di SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di realizzare appieno i vantaggi di un modello di storage misto e di supportare i contratti di servizio più esigenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
