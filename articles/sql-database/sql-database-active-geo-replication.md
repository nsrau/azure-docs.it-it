---
title: Replica geografica attiva - Database SQL di Azure | Microsoft Docs
description: Usare la replica geografica attiva per creare database secondari leggibili di singoli database nello stesso data center oppure in uno diverso (area).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 21b036763a1e8a7480d11fef4c36599f92fc3657
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853189"
---
# <a name="creating-and-using-active-geo-replication"></a>Creazione e uso di replica geografica attiva

La replica geografica attiva è una funzionalità del database SQL di Azure che consente di creare database secondari leggibili di singoli database in un server di database SQL nello stesso data center o in uno diverso (area).

> [!NOTE]
> La replica geografica attiva non è supportata dall'istanza gestita. Per il failover geografico di istanze gestite, usare [gruppi di failover automatico](sql-database-auto-failover-group.md).

La replica geografica attiva è progettata come una soluzione di continuità aziendale che consente all'applicazione di eseguire un rapido ripristino di emergenza di singoli database in caso di emergenza a livello di area o di un guasto su larga scala. Se la replica geografica è abilitata, l'applicazione può avviare il failover in un database secondario in un'altra area di Azure. Sono supportati al massimo quattro database secondari nella stessa area o in aree geografiche diverse ed è possibile usare i database secondari anche per le query di accesso in sola lettura. Il failover deve essere avviato manualmente dall'utente o dall'applicazione. Dopo il failover, il nuovo database primario dispone di un endpoint di connessione diverso. Il diagramma seguente illustra una configurazione tipica di un'applicazione cloud con ridondanza geografica tramite la replica geografica attiva.

![replica geografica attiva](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> Il database SQL supporta anche i gruppi di failover automatico. Per altre informazioni, vedere l'uso dei [gruppi di failover automatico](sql-database-auto-failover-group.md). La replica geografica attiva non è supportata per i database creati in un'istanza gestita. Con le istanze gestite è consigliabile usare i [gruppi di failover](sql-database-auto-failover-group.md).

Se per qualsiasi motivo il database primario restituisce un errore o deve essere portato offline, è possibile avviare il failover in uno dei database secondari. Una volta attivato il failover su uno dei database secondari, tutti gli altri database secondari vengono automaticamente collegati al nuovo database primario.

È possibile gestire la replica e il failover di un singolo database o di un set di database in un server o in un pool elastico tramite la replica geografica attiva. A questo scopo, usare:

- Il[portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell: database singolo](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: pool elastico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: database singolo o pool elastico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST: database singolo](https://docs.microsoft.com/rest/api/sql/replicationlinks)

Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

La replica geografica attiva sfrutta la tecnologia [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server per replicare in modo asincrono in un database secondario le transazioni di cui è stato eseguito il commit nel database primario usando l'isolamento dello snapshot. I gruppi di failover automatico forniscono la semantica del gruppo sulla replica geografica attiva, ma viene usato lo stesso meccanismo di replica asincrona. Anche se a un certo punto i dati del database secondario possono essere leggermente indietro rispetto al database primario, per tali dati sono sempre garantite transazioni complete. La ridondanza tra aree consente il ripristino rapido delle applicazioni dalla perdita definitiva di un intero data center o di parti di esso causata da calamità naturali, errori umani irreversibili o atti dolosi. È possibile consultare i dati RPO specifici in [Panoramica della continuità aziendale](sql-database-business-continuity.md).

> [!NOTE]
> Se si verifica un errore di rete tra due aree, viene eseguito un tentativo ogni 10 secondi per ristabilire le connessioni.
> [!IMPORTANT]
> Per garantire che una modifica importante al database primario venga replicata in un database secondario prima del failover, è possibile forzare la sincronizzazione per ottenere la replica delle modifiche importanti (ad esempio, gli aggiornamenti della password). La sincronizzazione forzata si riflette sulle prestazioni poiché blocca il thread delle chiamante fino a quando non vengono replicate tutte le transazioni. Per informazioni dettagliate, vedere [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Per monitorare l'intervallo di replica tra il database primario e la replica geografica secondaria, vedere [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

La figura seguente illustra un esempio di replica geografica attiva configurata con il database primario nell'area Stati Uniti centro-settentrionali e il database secondario nell'area Stati Uniti centro-meridionali.

![relazione di replica geografica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Poiché i database secondari sono leggibili, possono essere usati per l'offload dei carichi di lavoro di sola lettura, ad esempio i processi di creazione di rapporti. Se si usa la replica geografica attiva, è possibile creare il database secondario nella stessa area del database primario, ma non si incrementa la resilienza dell'applicazione in caso di errori irreversibili. Se si usano i gruppi di failover automatico, il database secondario viene sempre creato in un'area diversa.

Oltre al ripristino di emergenza, la replica geografica attiva può essere usata negli scenari seguenti:

- **Migrazione di database**: è possibile usare la replica geografica attiva per eseguire la migrazione di un database da un server a un altro online con un tempo di inattività minimo.
- **Aggiornamenti dell'applicazione**: è possibile creare un database secondario aggiuntivo come copia di failback durante gli aggiornamenti dell'applicazione.

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Funzionalità e terminologia della replica geografica attiva

- **Replica asincrona automatica**

  È possibile creare un database secondario solo aggiungendolo a un database esistente. Il database secondario può essere creato in qualsiasi server di database SQL di Azure. Dopo avere creato il database secondario, è necessario popolarlo con i dati copiati dal database primario. Questo processo è noto come seeding. Dopo aver creato ed eseguito il seeding del database secondario, gli aggiornamenti al database primario vengono replicati automaticamente in modo asincrono nel database secondario. Replica asincrona significa che le transazioni vengono sottoposte a commit nel database primario prima di essere replicate nel database secondario.

- **Database secondari accessibili in lettura**

  Un'applicazione può accedere a un database secondario per le operazioni di sola lettura usando le stesse entità di sicurezza usate per l'accesso al database primario. I database secondari operano in modalità di isolamento dello snapshot per garantire che la replica degli aggiornamenti del database primario (riesecuzione del log) non venga ritardata da query eseguite sul database secondario.

  > [!NOTE]
  > La riesecuzione del log viene ritardata nel database secondario se è in corso la ricezione di aggiornamenti dello schema dal database primario perché richiede un blocco dello schema nel database secondario.

- **Failover pianificato**

  Il failover pianificato esegue la sincronizzazione completa tra il database primario e il database secondario prima che il database secondario passi al ruolo primario. In questo modo si esclude la perdita di dati. Il failover pianificato viene usato negli scenari seguenti: (a) per eseguire analisi di ripristino di emergenza in fase di produzione quando la perdita di dati non è accettabile. (b) per spostare il database in un'area diversa; e (c) per restituire il database nell'area primaria dopo che l'interruzione del servizio è stata risolta (failback).

- **Failover non pianificato**

  Con il failover non pianificato o forzato il database secondario passa immediatamente al ruolo primario senza alcuna sincronizzazione. Questa operazione comporta la perdita di dati. Un failover non pianificato viene usato come metodo di ripristino durante le interruzioni quando non è accessibile il database primario. Quando il database primario originale torna disponibile, si riconnette automaticamente senza sincronizzazione e diventa un nuovo database secondario.

- **Più database secondari**

  È possibile creare fino a 4 database secondari per ogni database primario. Se è presente un solo database secondario e questo smette di funzionare, l'applicazione rimane esposta a maggiori rischi finché non viene creato un nuovo database secondario. Se sono presenti più database secondari, l'applicazione resta protetta anche se uno dei database secondari non funziona. I database secondari aggiuntivi possono anche essere usati per applicare la scalabilità orizzontale a carichi di lavoro di sola lettura

  > [!NOTE]
  > Se si usa la replica geografica attiva per compilare un'applicazione distribuita a livello globale ed è necessario fornire l'accesso in sola lettura ai dati in più di quattro aree, è possibile creare il database secondario di un database secondario. Questo processo è noto come concatenamento. In questo modo è possibile ottenere una scalabilità virtualmente illimitata della replica del database. Il concatenamento riduce anche il sovraccarico della replica dal database primario. Il compromesso è l'intervallo di replica aumentato nei database secondari a più strati.

- **Replica geografica di database in un pool elastico**

  Ogni database secondario può partecipare separatamente in un pool elastico o non essere presente in alcun pool elastico. La scelta del pool per ogni database secondario è separata e non dipende dalla configurazione di alcuna altro database secondario (sia primario che secondario). Ogni pool elastico è contenuto all'interno di una singola area, pertanto più database secondari nella stessa topologia non possono mai condividere un pool elastico.

- **Dimensioni di calcolo configurabili del database secondario**

  I database primari e secondari devono avere lo stesso livello di servizio. È anche consigliabile creare tale database secondario con le stesse dimensioni dell'ambiente di calcolo (DTU o vCore) del database primario. Per un database secondario con una dimensione dell'ambiente di calcolo inferiore sussiste il rischio di un intervallo di replica maggiore e di una potenziale indisponibilità e, di conseguenza, il rischio di una perdita di dati consistente dopo un failover. Di conseguenza, il valore RPO = 5 secondi pubblicato non può essere garantito. Un altro rischio è che dopo il failover le prestazioni dell'applicazione risentano di una mancanza capacità di calcolo del nuovo database primario finché quest'ultimo non viene aggiornato a una dimensione dell'ambiente di calcolo superiore. Il momento dell'aggiornamento dipende dalle dimensioni del database. Inoltre, attualmente tale aggiornamento richiede che i database primari e secondari siano online e, di conseguenza, non possono essere completati fino a quando non viene risolta l'interruzione del servizio. Se si decide di creare il database secondario con una dimensione dell'ambiente di calcolo inferiore, il grafico della percentuale IO del log nel portale di Azure costituisce un buon metodo per stimare la dimensione dell'ambiente di calcolo minima del database secondario che deve sostenere il carico della replica. Ad esempio, se il database primario è P6 (1000 DTU) e la percentuale IO del log è del 50%, il database secondario deve essere almeno P4 (500 DTU). È anche possibile recuperare i dati di I/O del log usando la vista di database [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) o [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).  Per altre informazioni sulla dimensione dell'ambiente di calcolo del database SQL, vedere [Quali sono i livelli di servizio del database SQL di Azure?](sql-database-purchase-models.md).

- **Failover e failback controllati dall'utente**

  Un database secondario può essere impostato esplicitamente sul ruolo di database primario in qualsiasi momento dall'applicazione o dall'utente. Durante un'interruzione reale, deve essere usata l'opzione "non pianificato", che alza immediatamente il livello di un database secondario a primario. Quando il database primario viene ripristinato ed è nuovamente disponibile, il sistema lo contrassegna automaticamente come database secondario e lo aggiorna con il nuovo database primario. A causa della natura asincrona della replica, una piccola quantità di dati può andare perduta durante i failover non pianificati se il database primario si interrompe prima di replicare le modifiche più recenti al database secondario. Quando un database primario con più database secondari esegue il failover, il sistema riconfigura automaticamente le relazioni di replica e collega i database secondari rimanenti al nuovo database primario appena alzato di livello senza alcun intervento da parte dell'utente. Dopo aver risolto l'interruzione del servizio che ha causato il failover, è opportuno ripristinare l'applicazione nell'area primaria. A tale scopo, richiamare il comando di failover con l'opzione "pianificato".

- **Mantenere sincronizzate le credenziali e le regole del firewall**

È consigliabile usare [a livello di database le regole del firewall IP](sql-database-firewall-configure.md) per i database con replica geografica in modo che queste regole possono essere replicate con il database per assicurarsi che tutti i database secondari abbiano le stesse regole di firewall IP del database primario. In questo modo non è più necessario configurare e gestire le regole del firewall manualmente nei server che ospitano sia il database primario che i secondari. Analogamente, l'accesso ai dati come [utenti di database indipendente](sql-database-manage-logins.md) fa sì che i database primari e secondari abbiano sempre le stesse credenziali utente. Durante un failover, quindi, non si verificano interruzioni dovute a una mancata corrispondenza tra account di accesso e password. Con l'aggiunta di [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i clienti possono gestire l'accesso utente ai database primari e secondari, eliminando completamente la necessità di gestire le credenziali nei database.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario

È possibile eseguire l'aggiornamento o il downgrade di un database primario a una dimensione dell'ambiente di calcolo diversa (entro lo stesso livello di servizio e non tra il livello per utilizzo generico e business critical) senza disconnettere eventuali database secondari. Quando si esegue l'aggiornamento, è consigliabile aggiornare prima di tutto il database secondario e quindi il database primario. Quando si esegue il downgrade, seguire l'ordine inverso, ovvero eseguire prima di tutto il downgrade del database primario e quindi del database secondario. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione.

> [!NOTE]
> Se è stato creato il database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato.

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici

A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) subito dopo il commit della transazione. La chiamata a **sp_wait_for_database_copy_sync** blocca il thread chiamante finché non viene completata la trasmissione dell'ultima transazione di cui è stato eseguito il commit nel database secondario. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. **sp_wait_for_database_copy_sync** ha come ambito un collegamento di copia continua specifico. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impedisce la perdita di dati dopo il failover, ma non garantisce la sincronizzazione completa per l'accesso in lettura. Il ritardo causato da una chiamata di routine **sp_wait_for_database_copy_sync** può essere significativo e dipende dalle dimensioni del log delle transazioni al momento della chiamata.

## <a name="monitoring-geo-replication-lag"></a>Monitoraggio intervallo di replica geografica

Per monitorare ritardo rispetto al valore RPO, utilizzare *replication_lag_sec* della colonna della [DM geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) nel database primario. Mostra intervallo in secondi tra le transazioni sottoposte a commit nel database primario e resi persistenti nel database secondario. ad esempio Se il valore del ritardo è 1 secondo, significa che se il database primario è stato interessato da un'interruzione del servizio per il momento e il failover viene avviata, non verranno salvate 1 secondo di transtions il più recente. 

Per misurare il ritardo rispetto alle modifiche nel database primario che sono stati applicati per la replica secondaria, vale a dire disponibile per la lettura dall'area secondaria, confrontare *last_commit* tempo nel database secondario con lo stesso valore nel database primario database.

> [!NOTE]
> In alcuni casi *replication_lag_sec* nel database primario ha un valore NULL, il che significa che il database primario non attualmente a conoscenza di quanto il database secondario è.   Ciò accade solitamente dopo il processo viene riavviato e deve essere una condizione temporanea. Prendere in considerazione l'applicazione di avviso se il *replication_lag_sec* restituisce NULL per un lungo periodo di tempo. Ciò potrebbe indicare che il database secondario non può comunicare con il database primario a causa di un errore di connettività permanente. Sono inoltre presenti le condizioni che potrebbero causare la differenza tra *last_commit* tempo nel database secondario e nel database primario per diventare grandi. ad esempio Se un commit viene effettuato nella replica primaria dopo un lungo periodo di alcuna modifica, la differenza passerà fino a un valore di grandi dimensioni prima di tornare rapidamente su 0. Considerarlo come una condizione di errore quando la differenza tra questi due valori rimane grande per molto tempo.


## <a name="programmatically-managing-active-geo-replication"></a>Gestione a livello di codice della replica geografica attiva

Come indicato in precedenza, la replica geografica attiva può essere gestita a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili. La replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](https://docs.microsoft.com/rest/api/sql/) e i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Queste API richiedono l'uso di gruppi di risorse e supportano la sicurezza basata sui ruoli (Controllo degli accessi in base al ruolo). Per altre informazioni su come implementare i ruoli di accesso, vedere [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: gestire il failover di database singoli e in pool

> [!IMPORTANT]
> Questi comandi Transact-SQL si applicano solo alla replica geografica attiva e non ai gruppi di failover. Di conseguenza potrebbero anche non applicarsi a istanze gestite, perché supportano solo i gruppi di failover.

| Comando | DESCRIZIONE |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usare l'argomento ADD SECONDARY ON SERVER per creare un database secondario per un database esistente e avviare la replica dei dati |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usare FAILOVER o FORCE_FAILOVER_ALLOW_DATA_LOSS per passare un database secondario al ruolo di database primario per avviare il failover |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usare REMOVE SECONDARY ON SERVER per terminare la replica dei dati tra un database SQL e il database secondario specificato. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Restituisce informazioni su tutti i collegamenti di replica esistenti per ogni database nel server di database SQL di Azure. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Ottiene l'ultima ora di replica, l'ultimo intervallo di replica e altre informazioni sul collegamento di replica per un database SQL specificato. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra lo stato per tutte le operazioni di database, incluso lo stato dei collegamenti di replica. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |fa sì che l'applicazione rimanga in attesa finché tutte le transazioni vengono replicate e riconosciute dal database secondario attivo. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: gestire il failover di database singoli e in pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

| Cmdlet | DESCRIZIONE |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Crea un database secondario per un database esistente e avvia la replica dei dati. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Consente di passare un database secondario al ruolo di database primario per avviare il failover. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Termina la replica dei dati tra un database SQL e il database secondario specificato. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Ottiene i collegamenti di replica geografica tra un database di SQL Azure e un gruppo di risorse o SQL Server. |
|  | |

> [!IMPORTANT]
> Per script di esempio, vedere [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) e [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>API REST: gestire il failover di database singoli e in pool

| API | DESCRIZIONE |
| --- | --- |
| [Creare o aggiornare database (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Crea, aggiorna o ripristina un database primario o secondario. |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Restituisce lo stato durante un'operazione di creazione. |
| [Impostazione del database secondario come primario (failover pianificato)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Stabilisce quale database secondario deve essere primario eseguendo il failover dal database primario corrente. **Questa opzione non è supportata per Istanza gestita.**|
| [Set Secondary Database as Primary (Unplanned Failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Stabilisce quale database secondario deve essere primario eseguendo il failover dal database primario corrente. Questa operazione potrebbe comportare la perdita di dati. **Questa opzione non è supportata per Istanza gestita.**|
| [Get Replication Link](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Ottiene tutti i collegamenti di replica specifici per un database SQL specificato in una relazione di replica geografica. Recupera le informazioni visibili nella vista del catalogo sys.geo_replication_links. **Questa opzione non è supportata per Istanza gestita.**|
| [Replication Links - List By Database](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Ottiene tutti i collegamenti di replica per un database SQL specificato in una relazione di replica geografica. Recupera le informazioni visibili nella vista del catalogo sys.geo_replication_links. |
| [Delete Replication Link](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Elimina un collegamento alla replica del database. Non può essere eseguito durante il failover. |
|  | |

## <a name="next-steps"></a>Passaggi successivi

- Per script di esempio, vedere:
  - [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- Il database SQL supporta anche i gruppi di failover automatico. Per altre informazioni, vedere l'uso dei [gruppi di failover automatico](sql-database-auto-failover-group.md).
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
- Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
