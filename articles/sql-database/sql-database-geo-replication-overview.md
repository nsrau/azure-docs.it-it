---
title: Gruppi di failover e replica geografica attiva - Database SQL di Azure | Microsoft Docs
description: I gruppi di failover automatico con replica geografica attiva consentono di configurare 4 repliche del database in uno qualsiasi dei data center di Azure e di eseguire automaticamente il failover in caso di interruzione.
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 07/10/2017
ms.author: sashan
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: fc9f362d2506451748841e02dbae58f80cf278ba
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Panoramica: gruppi di failover e replica geografica attiva
La replica geografica attiva consente di configurare fino a quattro database secondari accessibili in lettura nella stessa posizione del data center o in posizioni (aree) diverse. Sono disponibili database secondari per l'esecuzione di query e per il failover in caso di interruzione di un data center o di impossibilità di connettersi al database primario. Il failover deve essere avviato manualmente dall'applicazione dell'utente. Dopo il failover, il nuovo database primario dispone di un endpoint di connessione diverso. 

> [!NOTE]
> La replica geografica attiva è disponibile per tutti i database in tutti i livelli di servizio in tutte le aree.
>  

I gruppi di failover automatico del database SQL di Azure (in anteprima) costituiscono una funzionalità del database SQL progettata per gestire automaticamente la relazione di replica geografica, la connettività e il failover su vasta scala. Grazie a questa funzionalità, i clienti possono ripristinare automaticamente più database correlati nell'area secondaria dopo errori irreversibili a livello di area o altri eventi imprevisti che comportano la perdita parziale o completa di disponibilità del servizio del database SQL nell'area primaria. È anche possibile usare i database secondari leggibili per l'offload dei carichi di lavoro di sola lettura.  Poiché coinvolgono più database, i gruppi di failover automatico devono essere configurati nel server primario. I server primario e secondario devono trovarsi nella stessa sottoscrizione. I gruppi di failover automatico supportano la replica di tutti i database nel gruppo in un solo server secondario in un'area diversa. La replica geografica attiva, senza gruppi di failover automatico, consente l'uso di un numero massimo di 4 database secondari in tutte le aree.

Se si sta usando la replica geografica attiva e per qualsiasi motivo il database primario restituisce un errore o deve essere portato offline, è possibile avviare il failover in uno dei database secondari. Una volta attivato il failover su uno dei database secondari, tutti gli altri database secondari vengono automaticamente collegati al nuovo database primario. Se si usano i gruppi di failover automatico (in anteprima) per gestire il ripristino del database ed eventuali interruzioni che influiscono su uno o più database nel gruppo, si otterrà un failover automatico. È possibile configurare i criteri di failover automatico che più soddisfano le esigenze dell'applicazione oppure è possibile rifiutarli esplicitamente e usare l'attivazione manuale. I gruppi di failover automatico (in anteprima) forniscono anche endpoint di listener di sola lettura e di sola scrittura che rimangono invariati durante i failover. Se si usa l'attivazione di failover manuale o automatica, il failover trasforma tutti i database secondari nel gruppo in database primari. Dopo aver completato il failover del database, viene aggiornato automaticamente il record DNS per reindirizzare gli endpoint nella nuova area.

È possibile gestire la replica e il failover di un singolo database o di un set di database in un server o in un pool elastico tramite la replica geografica attiva. È possibile farlo tramite il [portale di Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md). Si applica sia ai gruppi di replica geografica attiva che ai gruppi di failover automatico (in anteprima).

La replica geografica attiva sfrutta la tecnologia [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server per replicare in modo asincrono le transazioni di cui è stato eseguito il commit nel database primario nei database secondari tramite Read Committed Snapshot Isolation (RCSI). I gruppi di failover automatico forniscono la semantica del gruppo sulla replica geografica attiva, ma viene usato lo stesso meccanismo di replica asincrona. Anche se a un certo punto i dati del database secondario possono essere leggermente indietro rispetto al database primario, per tali dati sono sempre garantite transazioni complete. La ridondanza tra aree consente il ripristino rapido delle applicazioni dalla perdita definitiva di un intero data center o di parti di esso causata da calamità naturali, errori umani irreversibili o atti dolosi. È possibile consultare i dati RPO specifici in [Panoramica della continuità aziendale](sql-database-business-continuity.md).

La figura seguente illustra un esempio di replica geografica attiva configurata con il database primario nell'area Stati Uniti centro-settentrionali e il database secondario nell'area Stati Uniti centro-meridionali.

![relazione di replica geografica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Poiché i database secondari sono leggibili, possono essere usati per l'offload dei carichi di lavoro di sola lettura, ad esempio i processi di creazione di rapporti. Se si usa la replica geografica attiva, è possibile creare il database secondario nella stessa area del database primario, ma non si incrementa la resilienza dell'applicazione in caso di errori irreversibili. Se si usano i gruppi di failover automatico (in anteprima), i database secondari vengono sempre creati in un'area diversa.

Oltre al ripristino di emergenza, la replica geografica attiva può essere usata negli scenari seguenti:

* **Migrazione di un database**: è possibile usare la replica geografica attiva per eseguire la migrazione di un database da un server a un altro online con un tempo di inattività minimo.
* **Aggiornamenti dell'applicazione**: è possibile creare un database secondario aggiuntivo come copia di failback durante gli aggiornamenti dell'applicazione.

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza dei database tra i data center rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di applicazioni per il ripristino di emergenza cloud con la replica geografica attiva in database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funzionalità della replica geografica attiva
La funzionalità di replica geografica attiva fornisce i seguenti elementi essenziali:
* **Replica asincrona automatica**: è possibile creare un database secondario solo aggiungendolo a un database esistente. Il database secondario può essere creato in qualsiasi server di database SQL di Azure. Dopo avere creato il database secondario, è necessario popolarlo con i dati copiati dal database primario. Questo processo è noto come seeding. Dopo aver creato ed eseguito il seeding del database secondario, gli aggiornamenti al database primario vengono replicati automaticamente in modo asincrono nel database secondario. Replica asincrona significa che le transazioni vengono sottoposte a commit nel database primario prima di essere replicate nel database secondario. 
* **Database secondari accessibili in lettura**: un'applicazione può accedere a un database secondario per le operazioni di sola lettura usando le stesse entità di sicurezza usate per l'accesso al database primario. I database secondari operano in modalità di isolamento dello snapshot per garantire che la replica degli aggiornamenti del database primario (riesecuzione del log) non venga ritardata da query eseguite sul database secondario.

   > [!NOTE]
   > La riesecuzione del log viene ritardata nel database secondario se è in corso la ricezione di aggiornamenti dello schema dal database primario, perché richiede un blocco dello schema nel database secondario. 
   > 

* **Più database secondari leggibili**: due o più database secondari aumentano la ridondanza e il livello di protezione per il database primario e l'applicazione. Se sono presenti più database secondari, l'applicazione resta protetta anche se uno dei database secondari non funziona. Se è presente un solo database secondario e questo smette di funzionare, l'applicazione rimane esposta a maggiori rischi finché non viene creato un nuovo database secondario.

   > [!NOTE]
   > Se si usa la replica geografica attiva per compilare un'applicazione distribuita a livello globale ed è necessario fornire l'accesso in sola lettura ai dati in più di 4 aree, è possibile creare il database secondario di un database secondario. Questo processo è noto come concatenamento. In questo modo è possibile ottenere una scalabilità virtualmente illimitata della replica del database. Il concatenamento riduce anche il sovraccarico della replica dal database primario. Il compromesso è l'intervallo di replica aumentato nei database secondari a più strati. 
   >

* **Supporto dei database del pool elastico**: la replica geografica attiva può essere configurata per qualsiasi database in qualsiasi pool elastico. Il database secondario può trovarsi in un altro pool elastico. Per i database normali, il database secondario può essere un pool elastico e viceversa, purché i livelli di servizio restino invariati. 
* **Livello di prestazioni configurabile del database secondario**: un database secondario può essere creato con un livello di prestazioni inferiore rispetto al database primario. I database primari e secondari devono avere lo stesso livello di servizio. Questa opzione non è consigliata per le applicazioni con attività di scrittura nel database elevate perché il maggiore intervallo di replica aumenta il rischio di perdita di dati sostanziali dopo il failover. Inoltre, dopo il failover ci saranno delle ripercussioni sulle prestazioni dell'applicazione fino a quando il nuovo database primario non verrà aggiornato a un livello di prestazioni superiore. Il grafico della percentuale IO del log nel portale di Azure fornisce un buon metodo per stimare il livello di prestazioni minimo del database secondario che deve sostenere il carico della replica. Ad esempio, se il database primario è P6 (1000 DTU) e la percentuale IO del log è del 50%, il database secondario deve essere almeno P4 (500 DTU). È anche possibile recuperare i dati di I/O del log usando la vista di database [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) o [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).  Per altre informazioni sui livelli di prestazioni del database SQL, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md). 
* **Failover e failback controllati dall'utente**: un database secondario può essere impostato esplicitamente sul ruolo di database primario in qualsiasi momento dall'applicazione o dall'utente. Durante un'interruzione reale, deve essere usata l'opzione "non pianificato", che alza immediatamente il livello di un database secondario a primario. Quando il database primario viene ripristinato ed è nuovamente disponibile, il sistema lo contrassegna automaticamente come database secondario e lo aggiorna con il nuovo database primario. A causa della natura asincrona della replica, una piccola quantità di dati può andare perduta durante i failover non pianificati se il database primario si interrompe prima di replicare le modifiche più recenti al database secondario. Quando un database primario con più database secondari esegue il failover, il sistema riconfigura automaticamente le relazioni di replica e collega i database secondari rimanenti al nuovo database primario appena alzato di livello senza alcun intervento da parte dell'utente. Dopo aver risolto l'interruzione del servizio che ha causato il failover, è opportuno ripristinare l'applicazione nell'area primaria. A tale scopo, richiamare il comando di failover con l'opzione "pianificato". 
* **Mantenere sincronizzate le credenziali e le regole del firewall**: per i database con replica geografica è consigliabile usare le [regole del firewall a livello di database](sql-database-firewall-configure.md), in modo che tali regole possano essere replicate con il database, per garantire che tutti i database secondari abbiano le stesse regole del firewall del database primario. In questo modo non è più necessario configurare e gestire le regole del firewall manualmente nei server che ospitano sia il database primario che i secondari. Analogamente, l'accesso ai dati come [utenti di database indipendente](sql-database-manage-logins.md) fa sì che i database primari e secondari abbiano sempre le stesse credenziali utente. Durante un failover, quindi, non si verificano interruzioni dovute a una mancata corrispondenza tra account di accesso e password. Con l'aggiunta di [Azure Active Directory](../active-directory/active-directory-whatis.md) i clienti possono gestire l'accesso utente ai database primari e secondari, eliminando completamente la necessità di gestire le credenziali nei database.

## <a name="auto-failover-group-capabilities"></a>Funzionalità dei gruppi di failover automatico

La funzionalità dei gruppi di failover automatico fornisce un'astrazione potente della replica geografica attiva grazie al supporto della replica a livello di gruppo e al failover automatico. Rimuove anche la necessità di modificare la stringa di connessione SQL dopo il failover, fornendo gli endpoint di listener aggiuntivi. 

* **Gruppo di failover**: è possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria.  
* **Server primario**: un server che ospita i database primari nel gruppo di failover.
* **Server secondario**: un server che ospita i database secondari nel gruppo di failover. Il server secondario non può essere nella stessa area del server primario.
* **Aggiunta di database al gruppo di failover**: è possibile inserire diversi database all'interno di un server o di un pool elastico nello stesso gruppo di failover. Se si aggiunge un database autonomo al gruppo, viene creato automaticamente un database secondario usando la stessa edizione e lo stesso livello di prestazione. Se il database primario si trova in un pool elastico, il database secondario viene creato automaticamente nel pool elastico con lo stesso nome. Se si aggiunge un database che dispone già di un database secondario nel server secondario, tale replica geografica viene ereditata dal gruppo.

   > [!NOTE]
   > Quando si aggiunge un database che dispone già di un database secondario in un server che non fa parte del gruppo di failover, viene creato un nuovo database secondario nel server secondario. 
   >

* **Listener di lettura/scrittura del gruppo di failover**: record CNAME DNS in formato **&lt;nome-gruppo-failover&gt;.database.windows.net** che punta all'URL del server primario corrente. Consente alle applicazioni SQL di lettura/scrittura di riconnettersi al database primario in modo trasparente quando viene modificato il database primario dopo il failover. 
* **Listener di sola lettura del gruppo di failover**: record CNAME DNS in formato **&lt;nome-gruppo-failover&gt;.secondary.database.windows.net** che punta all'URL del server secondario. Consente alle applicazioni SQL di sola lettura di connettersi in modo trasparente nel database secondario usando le regole di bilanciamento del carico specificate. Facoltativamente, è possibile specificare se si desidera che il traffico di sola lettura sia reindirizzato automaticamente verso il server primario quando il server secondario non è disponibile.
* **Criteri di failover automatico**: per impostazione predefinita, il gruppo di failover è configurato con un criterio di failover automatico. Il sistema attiva il failover non appena viene rilevato l'errore. Se si desidera controllare il flusso di lavoro del failover dall'applicazione, è possibile disattivare il failover automatico. 
* **Failover manuale**: è possibile avviare manualmente il failover in qualsiasi momento, indipendentemente dalla configurazione del failover automatico. Se i criteri di failover automatico non sono configurati, è necessario eseguire il failover manuale per ripristinare i database nel gruppo di failover. È possibile avviare il failover forzato o semplice (con sincronizzazione completa dei dati). Quest'ultimo può essere usato per spostare il server attivo nell'area primaria. Quando il failover viene completato, i record DNS vengono aggiornati automaticamente per garantire la connettività al server corretto.
* **Periodo di tolleranza con perdita di dati**: poiché i database primario e secondario vengono sincronizzati tramite la replica asincrona, il failover può comportare la perdita di dati. È possibile personalizzare i criteri di failover automatico per riflettere la tolleranza dell'applicazione verso la perdita dei dati. Configurando **GracePeriodWithDataLossHours**, è possibile verificare la lunghezza del periodo di attesa del sistema prima che venga avviato il failover che comporterà probabilmente la perdita dei dati. 

   > [!NOTE]
   > Quando il sistema rileva che i database del gruppo sono ancora online (ad esempio, l'interruzione del servizio ha interessato solo il piano di controllo del servizio), attiva immediatamente il failover con la sincronizzazione completa dei dati (failover semplice) indipendentemente dal valore impostato con **GracePeriodWithDataLossHours**. Questo comportamento assicura che non avvenga alcuna perdita di dati durante il ripristino. Il periodo di tolleranza ha effetto solo quando non è possibile che si verifichi il failover semplice. Se l'interruzione del servizio è stata risolta prima che scada il periodo di tolleranza, il failover non viene attivato.
   >

* **Più gruppi di failover**: è possibile configurare più gruppi di failover per la stessa coppia di server per controllare la scalabilità di failover. Ogni gruppo esegue il failover in modo indipendente. Se l'applicazione multi-tenant fa uso di pool elastici, è possibile usare questa funzionalità per combinare i database primari e secondari in ogni pool. In questo modo è possibile ridurre l'impatto di un'interruzione a solo la metà dei tenant.

## <a name="best-practices-of-building-highly-available-service"></a>Procedure consigliate per la creazione di un servizio a disponibilità elevata

Per creare un servizio a disponibilità elevata che usa il database SQL di Azure, è consigliabile attenersi alle indicazioni seguenti:

- **Usare gruppi di failover**: è possibile creare uno o più gruppi di failover tra due server in aree diverse (server primario e secondario). Ogni gruppo può includere uno o più database che vengono ripristinati come unità nel caso in cui tutti o alcuni database primari diventino non disponibili a causa di un'interruzione nell'area primaria. Il gruppo di failover crea un database di replica geografica secondaria con lo stesso obiettivo di servizio di quello primario. Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso obiettivo di livello di servizio del database primario.
- **Usare il listener di lettura/scrittura per il carico di lavoro OLTP**: quando si eseguono operazioni OLTP, usare **&lt;nome-gruppo-failover&gt;.database.windows.net** come URL del server per indirizzare automaticamente le connessioni al database primario. Questo URL non cambia dopo il failover.  
Si noti che il failover comporta l'aggiornamento del record DNS in modo che le connessioni client vengano reindirizzate al nuovo database primario solo dopo l'aggiornamento della cache DNS del client.
- **Usare il listener di sola lettura per il carico di lavoro di sola lettura**: se è presente un carico di lavoro di sola lettura isolato logicamente che tollera un certo grado di obsolescenza dei dati, è possibile usare il database secondario nell'applicazione. Per le sessioni di sola lettura, usare **&lt;nome-gruppo-failover&gt;.secondary.database.windows.net** come URL del server per indirizzare automaticamente la connessione al database secondario. È consigliabile anche indicare la finalità di lettura nella stringa di connessione usando **ApplicationIntent=ReadOnly**. 
- **Prepararsi a un calo delle prestazioni**: la decisione di failover di SQL è indipendente dal resto dell'applicazione o dagli altri servizi usati. L'applicazione potrebbe essere "mista" con alcuni componenti in un'area e alcuni componenti in un'altra. Per evitare il calo, verificare la distribuzione dell'applicazione ridondante nell'area di ripristino di emergenza, quindi seguire le istruzioni in questo articolo.  
Si noti che l'applicazione nell'area di ripristino di emergenza non deve usare una stringa di connessione diversa.  
- **Prepararsi a una perdita di dati**: se viene rilevata un'interruzione, SQL attiva automaticamente il failover di lettura/scrittura se è prevedibile una perdita di dati nulla. In caso contrario, attende il periodo specificato da **GracePeriodWithDataLossHours**. Se è stato specificato **GracePeriodWithDataLossHours**, prepararsi a una perdita di dati. Durante le interruzioni del servizio, generalmente Azure predilige la disponibilità. Se non ci si può permettere una perdita di dati, assicurarsi di impostare **GracePeriodWithDataLossHours** su un numero sufficientemente elevato, ad esempio 24 ore. 


## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade di un database primario
È possibile eseguire l'aggiornamento o il downgrade di un database primario a un livello di prestazioni diverso entro lo stesso livello di servizio, senza disconnettere eventuali database secondari. Quando si esegue l'aggiornamento, è consigliabile aggiornare prima di tutto il database secondario e quindi il database primario. Quando si esegue il downgrade, seguire l'ordine inverso, ovvero eseguire prima di tutto il downgrade del database primario e quindi del database secondario. Quando si aggiorna o si effettua il downgrade del database a un livello di servizio diverso, viene applicata questa raccomandazione. 

> [!NOTE]
> Se è stato creato il database secondario come parte della configurazione del gruppo di failover, non è consigliabile eseguire il downgrade del database secondario. In questo modo si garantisce che il livello dei dati abbia una capacità sufficiente per elaborare il carico di lavoro normale dopo che il failover viene attivato. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Evitare la perdita di dati critici
A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. La replica asincrona rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) subito dopo il commit della transazione. La chiamata a **sp_wait_for_database_copy_sync** blocca il thread chiamante finché non viene completata la trasmissione dell'ultima transazione di cui è stato eseguito il commit nel database secondario. Tuttavia, non attende che le transazioni trasmesse vengano riprodotta e che ne venga eseguito il commit nel database secondario. **sp_wait_for_database_copy_sync** ha come ambito un collegamento di copia continua specifico. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impedisce la perdita di dati dopo il failover, ma non garantisce la sincronizzazione completa per l'accesso in lettura. Il ritardo causato da una chiamata di routine **sp_wait_for_database_copy_sync** può essere significativo e dipende dalle dimensioni del log delle transazioni al momento della chiamata. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Gestione a livello di codice dei gruppi di failover e della replica geografica attiva
Come indicato in precedenza, i gruppi di failover automatico (in anteprima) e la replica geografica attiva possono anche essere gestiti a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili.

**API di Azure Resource Manager e sicurezza basata sui ruoli**: la replica geografica attiva include un set di API di Azure Resource Manager per la gestione, compresa l'[API REST del Database SQL di Azure](https://docs.microsoft.com/rest/api/sql/) e i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Queste API richiedono l'uso di gruppi di risorse e supportano la sicurezza basata sui ruoli (Controllo degli accessi in base al ruolo). Per altre informazioni su come implementare i ruoli di accesso, vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md).

> [!NOTE]
> Molte nuove funzionalità della replica geografica attiva sono supportate solo con [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) e in particolare con le [API REST SQL di Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) e con i [cmdlet di PowerShell per il database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). L'[API REST (versione classica)](https://msdn.microsoft.com/library/azure/dn505719.aspx) e i [cmdlet del database SQL di Azure (versione classica)](https://msdn.microsoft.com/library/azure/dn546723.aspx) sono supportati per la compatibilità con le versioni precedenti, pertanto è consigliabile usare le API basate su Azure Resource Manager. 
> 

## <a name="manage-sql-database-failover-using-transact-sql"></a>Gestire il failover del database SQL con Transact-SQL

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Usare l'argomento ADD SECONDARY ON SERVER per creare un database secondario per un database esistente e avviare la replica dei dati |
| [ALTER DATABASE (database SQL di Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Usare FAILOVER o FORCE_FAILOVER_ALLOW_DATA_LOSS per passare un database secondario al ruolo di database primario per avviare il failover |
| [ALTER DATABASE (database SQL di Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Usare REMOVE SECONDARY ON SERVER per terminare la replica dei dati tra un database SQL e il database secondario specificato. |
| [sys.geo_replication_links (database SQL di Azure)](https://msdn.microsoft.com/library/mt575501.aspx) |Restituisce informazioni su tutti i collegamenti di replica esistenti per ogni database nel server logico del database SQL di Azure. |
| [sys.dm_geo_replication_link_status (database SQL di Azure)](https://msdn.microsoft.com/library/mt575504.aspx) |Ottiene l'ultima ora di replica, l'ultimo intervallo di replica e altre informazioni sul collegamento di replica per un database SQL specificato. |
| [sys.dm_operation_status (database SQL di Azure)](https://msdn.microsoft.com/library/dn270022.aspx) |Mostra lo stato per tutte le operazioni di database, incluso lo stato dei collegamenti di replica. |
| [sp_wait_for_database_copy_sync (database SQL di Azure)](https://msdn.microsoft.com/library/dn467644.aspx) |fa sì che l'applicazione rimanga in attesa finché tutte le transazioni vengono replicate e riconosciute dal database secondario attivo. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Gestire il failover del database SQL con PowerShell

| Cmdlet | Descrizione |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Ottiene uno o più database. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Crea un database secondario per un database esistente e avvia la replica dei dati. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Consente di passare un database secondario al ruolo di database primario per avviare il failover. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Termina la replica dei dati tra un database SQL e il database secondario specificato. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Ottiene i collegamenti di replica geografica tra un database di SQL Azure e un gruppo di risorse o SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Questo comando crea un gruppo di failover e lo registra nei server primario e secondario|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Rimuove il gruppo di failover dal server ed elimina tutti i database secondari incluso il gruppo |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Recupera la configurazione del gruppo di failover |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Modifica la configurazione del gruppo di failover |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Attiva il failover del gruppo di failover per il server secondario |
|  | |

> [!IMPORTANT]
> Per gli script di esempio, vedere [Configure and failover a single database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) (Configurare ed eseguire il failover di un singolo database usando la replica geografica attiva), [Configure and failover a pooled database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md) (Configurare ed eseguire il failover di un pool di database usando la replica geografica attiva) e [Configure and failover a failover group for a single database (preview) (Configurare ed eseguire il failover di un gruppo di failover per un singolo database](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md.
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Gestire il failover del database SQL con L'API REST
| API | Descrizione |
| --- | --- |
| [Creare o aggiornare database (createMode=Restore)](/rest/api/sql/Databases/CreateOrUpdate) |Crea, aggiorna o ripristina un database primario o secondario. |
| [Get Create or Update Database Status](/rest/api/sql/Databases/CreateOrUpdate) |Restituisce lo stato durante un'operazione di creazione. |
| [Impostazione del database secondario come primario (failover pianificato)](/rest/api/sql/replicationlinks/failover) |Imposta il database di replica primario eseguendo il failover dal database di replica primaria corrente. |
| [Set Secondary Database as Primary (Unplanned Failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks#failoverallowdataloss) |Imposta il database di replica primario eseguendo il failover dal database di replica primaria corrente. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Replication Link](/rest/api/sql/replicationlinks/get) |Ottiene tutti i collegamenti di replica specifici per un database SQL specificato in una relazione di replica geografica. Recupera le informazioni visibili nella vista del catalogo sys.geo_replication_links. |
| [Replication Links - List By Database](/rest/api/sql/replicationlinks/listbydatabase) | Ottiene tutti i collegamenti di replica per un database SQL specificato in una relazione di replica geografica. Recupera le informazioni visibili nella vista del catalogo sys.geo_replication_links. |
| [Delete Replication Link](/rest/api/sql/databases/delete) | Elimina un collegamento alla replica del database. Non può essere eseguito durante il failover. |
| [Create or Update Failover Group]/rest/api/sql/failovergroups/createorupdate) | Crea o aggiorna un gruppo di failover. |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | Rimuove il gruppo di failover dal server |
| [Failover (Planned)](/rest/api/sql/failovergroups/failover) | Viene eseguito il failover dal server primario corrente a questo server. |
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Viene eseguito il failover dal server primario corrente a questo server. Questa operazione potrebbe comportare la perdita di dati. |
| [Get Failover Group](/rest/api/sql/failovergroups/get) | Crea un gruppo di failover. |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | Elenca i gruppi di failover in un server. |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | Aggiorna un gruppo di failover. |
|  | |

## <a name="next-steps"></a>Passaggi successivi
* Per script di esempio, vedere:
   - [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Configure and failover a failover group for a single database (preview)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md) (Configurare ed eseguire il failover di un gruppo di failover per un singolo database (anteprima))
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per ulteriori informazioni sui requisiti di autenticazione per un nuovo database e server primario, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).


