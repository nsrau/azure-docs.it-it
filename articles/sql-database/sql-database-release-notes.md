---
title: Note sulla versione
description: Informazioni sulle nuove funzionalità e sui miglioramenti nel servizio Database SQL di Azure e nella documentazione del database SQL di AzureLearn about the new features and improvements in the Azure SQL Database service and in the Azure SQL Database documentation
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 7d922aa0727ad28054d050a29039951d3f04985f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383377"
---
# <a name="sql-database-release-notes"></a>Note sulla versione del database SQL

In questo articolo sono elencate le funzionalità del database SQL attualmente in anteprima pubblica. Per gli aggiornamenti e i miglioramenti del database SQL, vedere Aggiornamenti del [servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per aggiornamenti e miglioramenti ad altri servizi di Azure, vedere [Aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità nell'anteprima pubblica

### <a name="single-database"></a>[Database singolo](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Nuove generazioni di hardware serie Fsv2 e serie M| Per informazioni, consultate [Generazioni di hardware.](sql-database-service-tiers-vcore.md#hardware-generations)|
| Ripristino accelerato del database con database singoli e pool elastici | Per informazioni, consultate [Ripristino accelerato del database](sql-database-accelerated-database-recovery.md).|
|Count Distinct approssimato|Per informazioni, consultate [Conteggio approssimativo distinto](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità Batch in Rowstore (sotto il livello di compatibilità 150)Batch Mode on Rowstore (under compatibility level 150)|Per informazioni, vedere [Modalità batch in Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere Database SQL di Azure e individuazione dei dati di [SQL Data Warehouse & classificazione.](sql-database-data-discovery-and-classification.md)|
| Processi di database elastici | Per informazioni, consultate [Creare, configurare e gestire processi elastici.](elastic-jobs-overview.md) |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastice](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Commenti e suggerimenti sulla concessione di memoria (modalità riga) (nel livello di compatibilità 150)Memory Grant Feedback (Row Mode) (under compatibility level 150)|Per informazioni, vedere Feedback sulla [concessione di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di AzureQuery editor in the Azure portal |Per informazioni, vedere Usare l'editor di query SQL del portale di [Azure per connettersi ed eseguire query sui dati.](sql-database-connect-query-portal.md)|
| Servizi R / machine learning con singoli database e pool elastici |Per informazioni, vedere Servizi di Machine Learning nel database SQL di [Azure.For information,](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)see Machine Learning Services in Azure SQL Database .|
|Analitica SQL|Per informazioni, vedere Analisi SQL di Azure.For information, see [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Compilazione posticipata di variabili di tabella (nel livello di compatibilità 150)Table Variable Deferred Compilation (under compatibility level 150)|Per informazioni, vedere [Compilazione postista di variabili di tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Un modo pratico ed economico per eseguire la migrazione di istanze SQL più piccole nel cloud. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entità server di Azure AD a livello di istanza (account di accesso)Instance-level Azure SERVER principals (logins)</a> | Creare account di accesso a livello di server utilizzando l'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER.</a> |
| [Replica transazionale](sql-database-managed-instance-transactional-replication.md) | Replicare le modifiche dalle tabelle in altri database inseriti in istanze gestite, database singoli o istanze di SQL Server oppure aggiornare le tabelle quando alcune righe vengono modificate in altre istanze gestite o istanze di SQL Server.Replica the changes from your tables into other databases placed on Managed Instances, Single Databases, or SQL Server instances, or update your tables when some rows are changed in other Managed Instances or SQL Server instance. Per informazioni, vedere Configurare la [replica in un database dell'istanza gestita del database SQL](replication-with-sql-database-managed-instance.md)di Azure.For information, see Configure replication in an Azure SQL Database managed instance database . |
| Introduzione al rilevamento delle minacce |Per informazioni, vedere [Configurare il rilevamento delle minacce nell'istanza gestita del database SQL](sql-database-managed-instance-threat-detection.md)di Azure.For information, see Configure threat detection in Azure SQL Database managed instance .|
| Conservazione del backup a lungo termine | Per informazioni, vedere [Configurare la conservazione del backup a lungo termine nell'istanza gestita del database SQL](sql-database-managed-instance-long-term-backup-retention-configure.md)di Azure.For information, see Configure long-term back up retention in Azure SQL Database managed instance . | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Istanza gestita: nuove funzionalità e problemi noti

### <a name="managed-instance-h2-2019-updates"></a>Aggiornamento di H2 2019 dell'istanza gestita

- [Configurazione della subnet assistita dal servizioService-aided subnet configuration](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Un modo sicuro e conveniente per gestire la configurazione della subnet in cui si controlla il traffico dati mentre l'istanza gestita garantisce il flusso ininterrotto del traffico di gestione
- [Transparent Data Encryption (TDE) con Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) consente lo scenario BYOK (Bring Your Own Key) per la protezione dei dati inattivi e consente alle organizzazioni di separare i compiti di gestione per chiavi e dati.
- [I gruppi](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) di failover automatico consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- Configurare il comportamento dell'istanza gestita con i flag di [traccia globali](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aggiornamento di H1 2019 dell'istanza gestita

Le funzionalità seguenti sono abilitate nel modello di distribuzione dell'istanza gestita in H1 2019:The following features are enabled in Managed instance deployment model in H1 2019:
  - Supporto per le sottoscrizioni con <a href="https://aka.ms/sql-mi-visual-studio-subscribers">credito mensile di Azure per i sottoscrittori</a> di Visual Studio e [limiti regionali](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)aumentati.
  - Supporto per <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e per <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Creare istanze con <a href="https://aka.ms/managed-instance-collation">regole di confronto</a> a livello di server e fuso <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">orario</a> di propria scelta.
  - Le istanze gestite sono ora protette con <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall incorporato.</a>
  - Configurare le istanze per l'utilizzo di [endpoint pubblici](sql-database-managed-instance-public-endpoint-configure.md), Connessione di [sostituzione proxy](sql-database-connectivity-architecture.md#connection-policy) per ottenere prestazioni di rete migliori, 4 <a href="https://aka.ms/four-cores-sql-mi-update">vCore alla generazione dell'hardware Gen5</a> o Configurare la <a href="https://aka.ms/managed-instance-configurable-backup-retention">conservazione dei backup fino a 35 giorni</a> per il ripristino temporizzato. La conservazione dei backup a lungo termine (fino a 10 anni) non è ancora abilitata, pertanto è possibile utilizzare i backup di <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">sola copia</a> come alternativa.
  - Le nuove funzionalità consentono di <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">ripristinare geograficamente il database in un altro data center utilizzando PowerShell</a>, [rinominare il database,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [eliminare il cluster virtuale.](sql-database-managed-instance-delete-virtual-cluster.md)
  - Il nuovo [ruolo Collaboratore istanza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) integrato consente la separazione dei compiti (SoD) con i principi di sicurezza e la conformità agli standard aziendali.
  - L'istanza gestita è disponibile nelle seguenti aree di Azure per enti pubblici in GA (US Gov Texas, US Gov Arizona) e in Cina North 2 e China East 2. È disponibile anche nelle seguenti regioni pubbliche: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, UAE Central, Emirati Arabi Uniti Nord, Sud Africa Nord, Sud Africa Ovest.

### <a name="known-issues"></a>Problemi noti

|Problema  |Data di scoperta  |Stato  |Data risolta  |
|---------|---------|---------|---------|
|[Autorizzazioni per il gruppo di risorse non applicate all'istanza gestitaPermissions on resource group not applied to Managed Instance](#permissions-on-resource-group-not-applied-to-managed-instance)|Feb 2020|Ha soluzione alternativa||
|[Limitazione del failover manuale tramite portale per gruppi di failoverLimitation of manual failover via portal for failover groups](#limitation-of-manual-failover-via-portal-for-failover-groups)|gennaio 2020|Ha soluzione alternativa||
|[I ruoli di SQL Agent richiedono autorizzazioni EXECUTE esplicite per gli account di accesso non sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dicembre 2019|Ha soluzione alternativa||
|[I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agenteSQL Agent jobs can be interrupted by Agent process restart](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dicembre 2019|Nessuna soluzione alternativa|Mar 2020|
|[AAD logins and users are not supported in SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Nessuna soluzione alternativa||
|[I limiti di memoria OLTP in memoria non vengono applicati](#in-memory-oltp-memory-limits-are-not-applied)|Ottobre 2019|Ha soluzione alternativa||
|[Errore errato restituito durante il tentativo di rimuovere un file non vuoto](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Ottobre 2019|Ha soluzione alternativa||
|[Il livello di servizio delle modifiche e le operazioni di creazione dell'istanza sono bloccati dal ripristino del database in corsoChange service tier and create instance operations are blocked by ongoing database restore](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Settembre 2019|Ha soluzione alternativa||
|[Potrebbe essere necessario riconfigurare Resource Governor nel livello di servizio Business Critical dopo il failoverResource Governor on Business Critical service tier might need to be reconfigured after failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Settembre 2019|Ha soluzione alternativa||
|[Le finestre di dialogo di Service Broker tra database devono essere reinizializzate dopo l'aggiornamento del livello di servizioCross-database Service Broker dialogs must be re-initialized after service tier upgrade](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Agosto 2019|Ha soluzione alternativa||
|[L'impersonificazione dei tipi di accesso di Azure AD non è supportata](#impersonification-of-azure-ad-login-types-is-not-supported)|lug 2019|Nessuna soluzione alternativa||
|[@queryparametro non supportato in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Nessuna soluzione alternativa||
|[La replica transazionale deve essere riconfigurata dopo il failover geograficoTransactional Replication must be reconfigured after geo-failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Nessuna soluzione alternativa||
|[Il database temporaneo viene utilizzato durante l'operazione RESTORE](#temporary-database-is-used-during-restore-operation)||Ha soluzione alternativa||
|[La struttura e il contenuto TEMPDB vengono ricreati](#tempdb-structure-and-content-is-re-created)||Nessuna soluzione alternativa||
|[Superamento dello spazio di archiviazione con file di database di piccole dimensioni](#exceeding-storage-space-with-small-database-files)||Ha soluzione alternativa||
|[Valori GUID visualizzati al posto dei nomi di database](#guid-values-shown-instead-of-database-names)||Ha soluzione alternativa||
|[I log degli errori non sono persistenti](#error-logs-arent-persisted)||Nessuna soluzione alternativa||
|[L'ambito delle transazioni in due database all'interno della stessa istanza non è supportatoTransaction scope on two databases within the same instance isn't supported](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ha soluzione alternativa||
|[I moduli CLR e i server collegati a volte non possono fare riferimento a un indirizzo IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ha soluzione alternativa||
|Database consistency not verified using DBCC CHECKDB after restore database from Azure Blob Storage.||Risolto|Nov 2019|
|Il ripristino del database temporizzato dal livello Business Critical al livello Scopo generale non avrà esito positivo se il database di origine contiene oggetti OLTP in memoria.||Risolto|Ottobre 2019|
|Funzionalità Posta elettronica database con server di posta esterni (non Azure) che usano una connessione sicuraDatabase Mail feature with external (non-Azure) mail servers using secure connection||Risolto|Ottobre 2019|
|Database indipendenti non supportati nell'istanza gestitaContained databases not supported in managed instance||Risolto|Agosto 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Autorizzazioni per il gruppo di risorse non applicate all'istanza gestitaPermissions on resource group not applied to managed instance

Il ruolo RBAC Collaboratore istanza gestita quando viene applicato a un gruppo di risorse (RG) non viene applicato all'istanza gestita e non ha alcun effetto.

**Soluzione alternativa:** impostare il ruolo Collaboratore istanza gestita per gli utenti a livello di sottoscrizione.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitazione del failover manuale tramite portale per gruppi di failoverLimitation of manual failover via portal for failover groups

Se il gruppo di failover si estende tra istanze in sottoscrizioni o gruppi di risorse di Azure diversi, il failover manuale non può essere avviato dall'istanza primaria nel gruppo di failover.

**Soluzione alternativa:** avviare il failover tramite il portale dall'istanza geo-secondaria.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>I ruoli di SQL Agent richiedono autorizzazioni EXECUTE esplicite per gli account di accesso non sysadmin

Se gli account di accesso non sysadmin vengono aggiunti a uno dei ruoli predefiniti del database di [SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), esiste un problema in cui è necessario concedere autorizzazioni EXECUTE esplicite alle stored procedure master affinché questi account di accesso funzionino. Se si verifica questo problema, verrà visualizzato il messaggio di errore "L'autorizzazione EXECUTE è stata negata sull'oggetto <object_name> (Microsoft SQL Server, errore: 229)".

**Soluzione alternativa:** dopo aver aggiunto gli account di accesso a uno dei ruoli predefiniti del database di SQL Agent: SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole, per ognuno degli account di accesso aggiunti a questi ruoli eseguire lo script T-SQL seguente per concedere in modo esplicito le autorizzazioni EXECUTE alle stored procedure elencate.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agenteSQL Agent jobs can be interrupted by Agent process restart

SQL Agent crea una nuova sessione ogni volta che viene avviato il processo, aumentando gradualmente il consumo di memoria. Per evitare di raggiungere il limite di memoria interno che bloccherebbe l'esecuzione dei processi pianificati, il processo dell'agente verrà riavviato una volta che il consumo di memoria raggiunge la soglia. Può comportare l'interruzione dell'esecuzione dei processi in esecuzione al momento del riavvio.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>I limiti di memoria OLTP in memoria non vengono applicati

Il livello di servizio Business Critical non applicherà correttamente [i limiti di memoria massima per gli oggetti ottimizzati per](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) la memoria in alcuni casi. L'istanza gestita può consentire al carico di lavoro di utilizzare più memoria per le operazioni OLTP in memoria, che possono influire sulla disponibilità e la stabilità dell'istanza. Le query OLTP in memoria che raggiungono i limiti potrebbero non riuscire immediatamente. Questo problema verrà risolto a breve. Le query che utilizzano più memoria OLTP in memoria avranno esito negativo prima se raggiungono i [limiti](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Soluzione alternativa:** [monitorare l'utilizzo dello spazio di archiviazione OLTP in memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) utilizzando SQL Server Management [Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) per assicurarsi che il carico di lavoro non utilizzi più della memoria disponibile. Aumentare i limiti di memoria che dipendono dal numero di vCore o ottimizzare il carico di lavoro per usare meno memoria.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Errore errato restituito durante il tentativo di rimuovere un file non vuoto

SQL Server/Istanza gestita [non consente all'utente](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)di eliminare un file non vuoto. Se si tenta di rimuovere un `ALTER DATABASE REMOVE FILE` file di `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` dati non vuoto utilizzando l'istruzione, l'errore non verrà restituito immediatamente. Istanza gestita continuerà a tentare di eliminare il file `Internal server error`e l'operazione avrà esito negativo dopo 30min con .

**Soluzione alternativa**: Rimuovere il `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` contenuto del file utilizzando il comando. Se questo è l'unico file nel filegroup, è necessario eliminare i dati dalla tabella o dalla partizione associata a questo filegroup prima di compattarlo e, facoltativamente, caricarli in un'altra tabella/partizione.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Il livello di servizio delle modifiche e le operazioni di creazione dell'istanza sono bloccati dal ripristino del database in corsoChange service tier and create instance operations are blocked by ongoing database restore

L'istruzione in corso, `RESTORE` il processo di migrazione del servizio di migrazione dei dati e il ripristino temporale con un periodo di tempo predefinito bloccheranno l'aggiornamento del livello di servizio o il ridimensionamento dell'istanza esistente e la creazione di nuove istanze fino al termine del processo di ripristino. Il processo di ripristino bloccherà queste operazioni nelle istanze gestite e nei pool di istanze nella stessa subnet in cui è in esecuzione il processo di ripristino. Le istanze nei pool di istanze non sono interessate. Le operazioni di creazione o modifica del livello di servizio non avranno esito negativo o il timeout, ma si procederà dopo il completamento o l'annullamento del processo di ripristino.

**Soluzione alternativa:** attendere il completamento del processo di ripristino o annullare il processo di ripristino se l'operazione di creazione o aggiornamento del livello di servizio ha una priorità più alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Potrebbe essere necessario riconfigurare Resource Governor nel livello di servizio Business Critical dopo il failoverResource Governor on Business Critical service tier might need to be reconfigured after failover

[La](/sql/relational-databases/resource-governor/resource-governor) funzionalità Resource Governor che consente di limitare le risorse assegnate al carico di lavoro degli utenti potrebbe classificare in modo non corretto un carico di lavoro utente dopo il failover o la modifica del livello di servizio avviata dall'utente (ad esempio, la modifica della dimensione massima di archiviazione dell'istanza vCore o limite).

**Soluzione alternativa:** eseguire `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente o come parte del processo di SQL Agent che esegue l'attività SQL all'avvio dell'istanza se si utilizza [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Le finestre di dialogo di Service Broker tra database devono essere reinizializzate dopo l'aggiornamento del livello di servizioCross-database Service Broker dialogs must be re-initialized after service tier upgrade

Le finestre di dialogo di Service Broker tra database interromperanno il recapito dei messaggi ai servizi in altri database dopo l'operazione di modifica del livello di servizio. I messaggi non vengono **persi** e possono essere trovati nella coda del mittente. Qualsiasi modifica delle dimensioni di vCore o dell'archiviazione dell'istanza nell'istanza gestita causerà `service_broke_guid` la modifica del valore nella vista [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) per tutti i database. Qualsiasi `DIALOG` creato utilizzando l'istruzione [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) che fa riferimento a Service Broker in un altro database interromperà il recapito dei messaggi al servizio di destinazione.

**Soluzione alternativa:** Arrestare tutte le conversazioni della finestra di dialogo Service Broker tra database prima di aggiornare il livello di servizio e reinizializzarle dopo. Se sono presenti messaggi rimanenti che non vengono recapitati dopo la modifica del livello di servizio, leggere i messaggi dalla coda di origine e inviarli nuovamente alla coda di destinazione.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>L'impersonificazione dei tipi di accesso di Azure AD non è supportata

La rappresentazione `EXECUTE AS USER` `EXECUTE AS LOGIN` tramite o delle seguenti entità AAD non è supportata:
-    Utenti AAD con alias. In questo caso `15517`viene restituito il seguente errore.
- Account di accesso e utenti di AAD basati su applicazioni AAD o entità servizio. In questo caso `15517` vengono restituiti `15406`i seguenti errori e .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametro non supportato in sp_send_db_mail

Il `@query` parametro nella procedura [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) non funziona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>La replica transazionale deve essere riconfigurata dopo il failover geograficoTransactional Replication must be reconfigured after geo-failover

Se la replica transazionale è abilitata in un database in un gruppo di failover automatico, l'amministratore dell'istanza gestita deve pulire tutte le pubblicazioni nel database primario precedente e riconfigurarle nel nuovo database primario dopo un failover in un'altra area. Per ulteriori dettagli, vedere [Replica.](sql-database-managed-instance-transact-sql-information.md#replication)

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD logins and users are not supported in SSDT

SQL Server Data ToolsSQL Server Data Tools non supporta completamente gli account di accesso e gli utenti di Azure Active Directory.SQL Server Data Tools don't fully support Azure Active directory logins and users.

### <a name="temporary-database-is-used-during-restore-operation"></a>Il database temporaneo viene utilizzato durante l'operazione RESTORE

Quando un database viene ripristinato nell'istanza gestita, il servizio di ripristino crea innanzitutto un database vuoto con il nome desiderato per allocare il nome nell'istanza. Dopo qualche tempo, questo database verrà eliminato e verrà avviato il ripristino del database effettivo. Il database in stato *di ripristino* sarà temporaneo un valore GUID casuale anziché il nome. Il nome temporaneo verrà modificato con `RESTORE` il nome desiderato specificato nell'istruzione al termine del processo di ripristino. Nella fase iniziale, l'utente può accedere al database vuoto e persino creare tabelle o caricare dati in questo database. Questo database temporaneo verrà eliminato all'avvio della seconda fase del servizio di ripristino.

**Soluzione alternativa:** non accedere al database da ripristinare finché non viene visualizzato il completamento del ripristino.

### <a name="tempdb-structure-and-content-is-re-created"></a>La struttura e il contenuto TEMPDB vengono ricreati

Il `tempdb` database è sempre suddiviso in 12 file di dati e la struttura del file non può essere modificata. La dimensione massima per file non può essere modificata `tempdb`e non è possibile aggiungere nuovi file a . `Tempdb`viene sempre ricreato come database vuoto all'avvio o al `tempdb` failover dell'istanza e le modifiche apportate non verranno mantenute.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` le istruzioni potrebbero non riuscire perché l'istanza può raggiungere il limite di Archiviazione di Azure.

Ogni istanza gestita di General Purpose ha fino a 35 TB di spazio di archiviazione riservato allo spazio su disco Premium di Azure.Each General Purpose managed instance has up to 35 TB of storage reserved for Azure Premium Disk space. Ogni file di database viene inserito in un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato sul disco non viene addebitato, ma la somma totale delle dimensioni del disco Premium di Azure non può superare i 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale potrebbe superare il limite di 35 TB di Azure sulle dimensioni di archiviazione a causa della frammentazione interna.

Ad esempio, un'istanza gestita di General Purpose potrebbe avere un file di grandi dimensioni di 1,2 TB posizionato su un disco da 4 TB. Potrebbe anche avere 248 file con dimensioni di 1 GB ciascuno che vengono posizionati su dischi separati da 128 GB. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio illustra che in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza gestita potrebbe raggiungere il limite di 35 TB riservato per un disco Premium di Azure collegato quando potrebbe non essere previsto.

In questo esempio, i database esistenti continuano a funzionare e possono crescere senza problemi, purché non vengono aggiunti nuovi file. Non è possibile creare o ripristinare nuovi database perché non c'è spazio sufficiente per le nuove unità disco, anche se la dimensione totale di tutti i database non raggiunge il limite di dimensioni dell'istanza. L'errore restituito in questo caso non è chiaro.

È possibile [identificare il numero di file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizzando le viste di sistema. Se si raggiunge questo limite, provare a [svuotare ed eliminare alcuni dei file più piccoli utilizzando l'istruzione DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello Business Critical, che non presenta questo limite.](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>Valori GUID visualizzati al posto dei nomi di database

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID perché vengono sostituiti con nomi di database effettivi in futuro.

**Soluzione alternativa:** utilizzare la visualizzazione sys.databases per risolvere il nome effettivo del database dal nome del database fisico, specificato sotto forma di identificatori di database GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>I log degli errori non sono persistenti

I log degli errori disponibili nell'istanza gestita non sono persistenti e la relativa dimensione non è inclusa nel limite massimo di archiviazione. I log degli errori potrebbero essere cancellati automaticamente se si verifica il failover. Potrebbero esserci lacune nella cronologia del log degli errori perché l'istanza gestita è stata spostata più volte in diverse macchine virtuali.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L'ambito delle transazioni in due database all'interno della stessa istanza non è supportatoTransaction scope on two databases within the same instance isn't supported

La `TransactionScope` classe in .NET non funziona se due query vengono inviate a due database all'interno della stessa istanza nello stesso ambito di transazione:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Anche se questo codice funziona con i dati all'interno della stessa istanza, è necessario MSDTC.

**Soluzione alternativa:** Utilizzare [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per utilizzare un altro database in un contesto di connessione anziché utilizzare due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>I moduli CLR e i server collegati a volte non possono fare riferimento a un indirizzo IP locale

I moduli CLR inseriti in un'istanza gestita e in server collegati o query distribuite che fanno riferimento a un'istanza corrente a volte non sono in grado di risolvere l'IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa:** Se possibile, utilizzare le connessioni di contesto in un modulo CLR.

## <a name="updates"></a>Aggiornamenti

Per un elenco degli aggiornamenti e dei miglioramenti apportati al database SQL, vedere Aggiornamenti del [servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per aggiornamenti e miglioramenti a tutti i servizi di Azure, vedere [Aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Fornire il proprio contributo per i contenuti

Per contribuire alla documentazione del database SQL di Azure, vedere la Guida per i collaboratori di [Docs](https://docs.microsoft.com/contribute/).
