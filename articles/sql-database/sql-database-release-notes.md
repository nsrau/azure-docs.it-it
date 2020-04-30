---
title: Note sulla versione
description: Informazioni sulle nuove funzionalità e miglioramenti nel servizio database SQL di Azure e nella documentazione del database SQL di Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sstein
ms.openlocfilehash: c3dc5b26435f6d876e5eaea943e359055018913b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201313"
---
# <a name="sql-database-release-notes"></a>Note sulla versione del database SQL

Questo articolo elenca le funzionalità del database SQL attualmente disponibili in anteprima pubblica. Per aggiornamenti e miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="single-database"></a>[Database singolo](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Nuove generazioni di hardware serie Fsv2 e serie M| Per informazioni, vedere [generazioni di hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| Recupero accelerato del database con singoli database e pool elastici | Per informazioni, vedere [accelerazione del ripristino del database](sql-database-accelerated-database-recovery.md).|
|Count Distinct approssimato|Per informazioni, vedere [conteggio approssimativo Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità batch su rowstore (con livello di compatibilità 150)|Per informazioni, vedere [modalità batch su rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](sql-database-data-discovery-and-classification.md).|
| Processi di database elastici | Per informazioni, vedere [creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Feedback delle concessioni di memoria (modalità riga) (con livello di compatibilità 150)|Per informazioni, vedere [feedback delle concessioni di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di Azure |Per informazioni, vedere [usare l'editor di query SQL di portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).|
| R Services/Machine Learning con database singoli e pool elastici |Per informazioni, vedere [Machine Learning Services nel database SQL di Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analitica SQL|Per informazioni, vedere [analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).|
|Compilazione posticipata della variabile di tabella (con livello di compatibilità 150)|Per informazioni, vedere [compilazione posticipata della variabile di tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Un modo pratico ed economicamente conveniente per eseguire la migrazione di istanze SQL più piccole al cloud. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entità del server Azure AD a livello di istanza (account di accesso)</a> | Creare account di accesso a livello di server utilizzando l'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login from External provider</a> . |
| [Replica transazionale](sql-database-managed-instance-transactional-replication.md) | Replicare le modifiche dalle tabelle in altri database posizionati in istanze gestite, database singoli o istanze di SQL Server o aggiornare le tabelle quando alcune righe vengono modificate in altre istanze gestite o SQL Server istanza. Per informazioni, vedere [configurare la replica in un database dell'istanza gestita di database SQL di Azure](replication-with-sql-database-managed-instance.md). |
| Introduzione al rilevamento delle minacce |Per informazioni, vedere [configurare il rilevamento delle minacce in istanza gestita di database SQL di Azure](sql-database-managed-instance-threat-detection.md).|
| Conservazione del backup a lungo termine | Per informazioni, vedere [configurare la conservazione del backup a lungo termine in istanza gestita di database SQL di Azure](sql-database-managed-instance-long-term-backup-retention-configure.md), attualmente disponibile in anteprima pubblica limitata. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Istanza gestita: nuove funzionalità e problemi noti

### <a name="managed-instance-h2-2019-updates"></a>Aggiornamenti dell'istanza gestita H2 2019

- [Configurazione della subnet assistita dal servizio](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Un modo sicuro e pratico per gestire la configurazione delle subnet in cui è possibile controllare il traffico dati mentre istanza gestita garantisce il flusso ininterrotto del traffico di gestione
- [Transparent Data Encryption (Transparent Data Encryption) con Bring your own key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) Abilita lo scenario Bring-your-own-key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di separare i compiti di gestione per chiavi e dati.
- I [gruppi di failover automatico](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- Configurare il comportamento dell'istanza gestita con i [flag di traccia globali](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aggiornamenti H1 2019 per istanza gestita

Le funzionalità seguenti sono abilitate nel modello di distribuzione dell'istanza gestita in H1 2019:
  - Supporto per le sottoscrizioni con <a href="https://aka.ms/sql-mi-visual-studio-subscribers">credito mensile di Azure per Sottoscrittori di Visual Studio</a> e maggiori [limiti a livello](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)di area.
  - Supporto per <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e per <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Creare istanze con <a href="https://aka.ms/managed-instance-collation">regole di confronto a livello di server</a> e il <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso orario</a> desiderato.
  - Le istanze gestite sono ora protette con il <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall incorporato</a>.
  - Configurare le istanze per l'uso di [endpoint pubblici](sql-database-managed-instance-public-endpoint-configure.md), la connessione per [l'override del proxy](sql-database-connectivity-architecture.md#connection-policy) per ottenere prestazioni di rete migliori, <a href="https://aka.ms/four-cores-sql-mi-update">4 vcore di generazione hardware quinta generazione</a> o <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurare la conservazione dei backup fino a 35 giorni per il</a> ripristino temporizzato. La [conservazione dei backup a lungo termine](sql-database-long-term-retention.md#managed-instance-support) (fino a 10 anni) è attualmente in anteprima pubblica limitata.  
  - Le nuove funzionalità consentono di eseguire <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">il ripristino geografico del database in un altro Data Center tramite PowerShell</a>, [Rename database](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [Delete Virtual cluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Il nuovo [ruolo Collaboratore istanza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) incorporato consente la conformità alla separazione dei compiti (SOD) con i principi di sicurezza e la conformità agli standard aziendali.
  - Istanza gestita è disponibile nelle aree di Azure per enti pubblici seguenti per la versione GA (US Gov Texas, US Gov Arizona), nonché in Cina settentrionale 2 e Cina orientale 2. È disponibile anche nelle aree pubbliche seguenti: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale, Sudafrica occidentale.

### <a name="known-issues"></a>Problemi noti

|Problema  |Data di individuazione  |Stato  |Data di risoluzione  |
|---------|---------|---------|---------|
|[Autorizzazioni per il gruppo di risorse non applicate a Istanza gestita](#permissions-on-resource-group-not-applied-to-managed-instance)|2020 feb|Con soluzione alternativa||
|[Limitazione del failover manuale tramite il portale per i gruppi di failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Gennaio 2020|Con soluzione alternativa||
|[Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli account di accesso non sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|2019 dicembre|Con soluzione alternativa||
|[I processi di SQL Agent possono essere interrotti dal riavvio del processo di Agent](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 dicembre|Risolto|Mar 2020|
|[Gli account di accesso e gli utenti di AAD non sono supportati in SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 novembre|Nessuna soluzione alternativa||
|[I limiti di memoria di OLTP in memoria non vengono applicati](#in-memory-oltp-memory-limits-are-not-applied)|2019 ottobre|Con soluzione alternativa||
|[Errore errato restituito durante il tentativo di rimozione di un file non vuoto](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 ottobre|Con soluzione alternativa||
|[Modificare il livello di servizio e le operazioni di creazione istanza sono bloccate dal ripristino del database in corso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 Sep|Con soluzione alternativa||
|[Potrebbe essere necessario riconfigurare Resource Governor business critical livello di servizio dopo il failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 Sep|Con soluzione alternativa||
|[È necessario inizializzare nuovamente le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 agosto|Con soluzione alternativa||
|[L'impersonificazione dei tipi di accesso Azure AD non è supportata](#impersonification-of-azure-ad-login-types-is-not-supported)|2019 luglio|Nessuna soluzione alternativa||
|[@queryil parametro non è supportato in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|2019 aprile|Nessuna soluzione alternativa||
|[La replica transazionale deve essere riconfigurata dopo il failover geografico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Nessuna soluzione alternativa||
|[Il database temporaneo viene usato durante l'operazione di ripristino](#temporary-database-is-used-during-restore-operation)||Con soluzione alternativa||
|[La struttura e il contenuto di TEMPDB vengono ricreati](#tempdb-structure-and-content-is-re-created)||Nessuna soluzione alternativa||
|[Superamento dello spazio di archiviazione con file di database di piccole dimensioni](#exceeding-storage-space-with-small-database-files)||Con soluzione alternativa||
|[Valori GUID visualizzati al posto dei nomi di database](#guid-values-shown-instead-of-database-names)||Con soluzione alternativa||
|[I log degli errori non sono salvati in stato permanente](#error-logs-arent-persisted)||Nessuna soluzione alternativa||
|[L'ambito della transazione su due database all'interno della stessa istanza non è supportato](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Con soluzione alternativa|Mar 2020|
|[I moduli CLR e i server collegati talvolta non possono fare riferimento a un indirizzo IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Con soluzione alternativa||
|Coerenza del database non verificata con DBCC CHECKDB dopo il ripristino del database dall'archiviazione BLOB di Azure.||Risolto|2019 novembre|
|Il ripristino temporizzato del database dal livello business critical al livello per utilizzo generico non riuscirà se il database di origine contiene oggetti OLTP in memoria.||Risolto|2019 ottobre|
|Posta elettronica database funzionalità con server di posta elettronica esterni (non Azure) tramite connessione protetta||Risolto|2019 ottobre|
|Database indipendenti non supportati nell'istanza gestita||Risolto|2019 agosto|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Autorizzazioni per il gruppo di risorse non applicate all'istanza gestita

Istanza gestita ruolo Collaboratore RBAC quando applicato a un gruppo di risorse (RG) non viene applicato al Istanza gestita e non ha alcun effetto.

**Soluzione alternativa**: configurare il ruolo di collaboratore istanza gestita per gli utenti a livello di sottoscrizione.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitazione del failover manuale tramite il portale per i gruppi di failover

Se il gruppo di failover si estende su più istanze in sottoscrizioni o gruppi di risorse di Azure diversi, non è possibile avviare il failover manuale dall'istanza primaria del gruppo di failover.

**Soluzione alternativa**: avviare il failover tramite il portale dall'istanza geografica secondaria.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli account di accesso non sysadmin

Se gli account di accesso non sysadmin vengono aggiunti a uno dei ruoli predefiniti del [database di SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), esiste un problema per cui le autorizzazioni Execute esplicite devono essere concesse alle stored procedure master per il funzionamento di tali account di accesso. Se si verifica questo problema, viene visualizzato il messaggio di errore "autorizzazione EXECUTE negata per l'oggetto <object_name> (Microsoft SQL Server, errore: 229)".

**Soluzione alternativa**: dopo aver aggiunto gli account di accesso a uno dei ruoli predefiniti del database di SQL Agent: SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole, per ognuno degli account di accesso aggiunti a questi ruoli eseguire lo script T-SQL seguente per concedere esplicitamente le autorizzazioni Execute alle stored procedure elencate.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>I processi di SQL Agent possono essere interrotti dal riavvio del processo di Agent

**(Risolto nel 2020 marzo)** SQL Agent crea una nuova sessione ogni volta che viene avviato il processo, aumentando gradualmente il consumo di memoria. Per evitare di raggiungere il limite di memoria interna che blocca l'esecuzione dei processi pianificati, il processo dell'agente verrà riavviato quando il consumo di memoria raggiunge la soglia. Potrebbe causare l'interruzione dell'esecuzione dei processi in esecuzione al momento del riavvio.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>I limiti di memoria di OLTP in memoria non vengono applicati

Business critical livello di servizio non applica correttamente [i limiti di memoria massima per gli oggetti ottimizzati per la memoria](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) in alcuni casi. L'istanza gestita può consentire al carico di lavoro di usare più memoria per le operazioni di OLTP in memoria, che potrebbero influire sulla disponibilità e sulla stabilità dell'istanza. Le query di OLTP in memoria che raggiungono i limiti potrebbero non riuscire immediatamente. Questo problema verrà risolto a breve. Le query che usano una quantità maggiore di memoria OLTP in memoria avranno esito negativo prima di raggiungere i [limiti](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Soluzione alternativa:** [monitorare l'utilizzo dell'archiviazione OLTP In memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) utilizzando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) per garantire che il carico di lavoro non utilizzi una quantità di memoria superiore a quella disponibile. Aumentare i limiti di memoria che dipendono dal numero di Vcore o ottimizzare il carico di lavoro per usare una quantità inferiore di memoria.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Errore errato restituito durante il tentativo di rimozione di un file non vuoto

SQL Server/Istanza gestita [non consentire all'utente di eliminare un file non vuoto](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se si tenta di rimuovere un file di dati non vuoto utilizzando `ALTER DATABASE REMOVE FILE` l'istruzione, l' `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` errore non verrà restituito immediatamente. Istanza gestita continuerà a eliminare il file e l'operazione avrà esito negativo dopo 30min `Internal server error`con.

**Soluzione temporanea**: rimuovere il contenuto del file tramite `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` il comando. Se si tratta dell'unico file del filegroup, è necessario eliminare i dati dalla tabella o dalla partizione associata a questo filegroup prima di compattare il file e, facoltativamente, caricare i dati in un'altra tabella o partizione.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Modificare il livello di servizio e le operazioni di creazione istanza sono bloccate dal ripristino del database in corso

L'istruzione `RESTORE` continuativa, il processo di migrazione del servizio migrazione dei dati e il ripristino temporizzato predefinito bloccano l'aggiornamento del livello di servizio o il ridimensionamento dell'istanza esistente e la creazione di nuove istanze fino al termine del processo di ripristino. Il processo di ripristino bloccherà queste operazioni sulle istanze gestite e sui pool di istanze nella stessa subnet in cui è in esecuzione il processo di ripristino. Le istanze nei pool di istanze non sono interessate. La creazione o la modifica delle operazioni del livello di servizio non avrà esito negativo o timeout. il processo verrà eseguito una volta completato o annullato il processo di ripristino.

**Soluzione temporanea**: attendere il completamento del processo di ripristino o annullare il processo di ripristino se l'operazione di creazione o aggiornamento del livello di servizio ha una priorità più elevata.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Potrebbe essere necessario riconfigurare Resource Governor business critical livello di servizio dopo il failover

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funzionalità che consente di limitare le risorse assegnate al carico di lavoro utente potrebbe erroneamente classificare un carico di lavoro dell'utente dopo il failover o la modifica avviata dall'utente del livello di servizio (ad esempio, la modifica delle dimensioni massime di archiviazione delle istanze di vCore o max).

**Soluzione temporanea**: `ALTER RESOURCE GOVERNOR RECONFIGURE` eseguire periodicamente o come parte del processo di SQL Agent che esegue l'attività SQL quando l'istanza viene avviata se si usa [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>È necessario inizializzare nuovamente le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio

Le finestre di dialogo Service Broker tra database interromperanno il recapito dei messaggi ai servizi di altri database dopo l'operazione di modifica del livello di servizio. I messaggi **non vengono persi** ed è possibile trovarli nella coda del mittente. Qualsiasi modifica delle dimensioni di archiviazione dell'istanza o di Vcore in Istanza gestita `service_broke_guid` , causerà la modifica del valore della vista [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) per tutti i database. Eventuali `DIALOG` istruzioni create utilizzando [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) che fanno riferimento a broker di servizi in un altro database arresteranno il recapito dei messaggi al servizio di destinazione.

**Soluzione alternativa:** Arrestare tutte le attività che usano le conversazioni di dialogo Service Broker tra database prima di aggiornare il livello di servizio e reinizializzarle dopo. Se sono presenti messaggi rimanenti che non vengono recapitati dopo la modifica del livello di servizio, leggere i messaggi dalla coda di origine e inviarli nuovamente alla coda di destinazione.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>L'impersonificazione dei tipi di accesso Azure AD non è supportata

La rappresentazione tramite `EXECUTE AS USER` o `EXECUTE AS LOGIN` delle entità AAD seguenti non è supportata:
-    Utenti di AAD con alias. In questo caso `15517`viene restituito l'errore seguente.
- Accessi e utenti di AAD basati su applicazioni o entità servizio di AAD. In questo caso `15517` vengono restituiti i seguenti errori e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryil parametro non è supportato in sp_send_db_mail

Il `@query` parametro nella procedura [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) non funziona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>La replica transazionale deve essere riconfigurata dopo il failover geografico

Se la replica transazionale è abilitata in un database in un gruppo di failover automatico, l'amministratore dell'istanza gestita deve eseguire la pulizia di tutte le pubblicazioni nel database primario precedente e riconfigurarle sul nuovo database primario dopo che si è verificato un failover in un'altra area. Per altri dettagli, vedere [replica](sql-database-managed-instance-transact-sql-information.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Gli account di accesso e gli utenti di AAD non sono supportati in SSDT

SQL Server Data Tools non supportano completamente gli account di accesso e gli utenti di Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Il database temporaneo viene usato durante l'operazione di ripristino

Quando un database viene ripristinato in Istanza gestita, il servizio di ripristino creerà prima di tutto un database vuoto con il nome desiderato per allocare il nome nell'istanza. Dopo un certo periodo di tempo, il database verrà eliminato e verrà avviato il ripristino del database effettivo. Il database in stato di *ripristino* creerà temporaneamente un valore GUID casuale anziché Name. Al termine del processo di ripristino, il nome temporaneo verrà modificato `RESTORE` con il nome desiderato specificato nell'istruzione. Nella fase iniziale, l'utente può accedere al database vuoto e persino creare tabelle o caricare dati in questo database. Questo database temporaneo verrà eliminato quando il servizio di ripristino avvierà la seconda fase.

**Soluzione temporanea**: non accedere al database che si sta ripristinando fino a quando non si noterà che il ripristino è stato completato.

### <a name="tempdb-structure-and-content-is-re-created"></a>La struttura e il contenuto di TEMPDB vengono ricreati

Il `tempdb` database viene sempre suddiviso in 12 file di dati e la struttura del file non può essere modificata. Non è possibile modificare le dimensioni massime per ogni file e non è possibile aggiungere `tempdb`nuovi file a. `Tempdb`viene sempre ricreato come database vuoto quando l'istanza viene avviata o sottoposta a failover e tutte le `tempdb` modifiche apportate in non verranno mantenute.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

`CREATE DATABASE`le `ALTER DATABASE ADD FILE`istruzioni, `RESTORE DATABASE` e potrebbero non riuscire perché l'istanza può raggiungere il limite di archiviazione di Azure.

Ogni istanza gestita di per utilizzo generico ha fino a 35 TB di spazio di archiviazione riservato per lo spazio su disco Premium di Azure. Ogni file di database si trova in un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato sul disco non viene addebitato, ma la somma totale delle dimensioni del disco Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni di archiviazione a causa della frammentazione interna.

Ad esempio, un'istanza gestita di per utilizzo generico potrebbe avere un file di grandi dimensioni di 1,2 TB in un disco da 4 TB. Potrebbero inoltre essere presenti 248 file con dimensioni di 1 GB, ognuno dei quali si trova in dischi 128 GB distinti. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio illustra che in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza gestita potrebbe raggiungere il limite di 35 TB riservato per un disco Premium di Azure collegato quando potrebbe non essere previsto.

In questo esempio, i database esistenti continuano a funzionare e possono crescere senza problemi, purché non vengano aggiunti nuovi file. Non è possibile creare o ripristinare nuovi database perché lo spazio disponibile non è sufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite delle dimensioni dell'istanza. L'errore restituito in questo caso non è chiaro.

È possibile [identificare il numero di file rimanenti usando le](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) visualizzazioni di sistema. Se si raggiunge questo limite, provare a [svuotare ed eliminare alcuni dei file più piccoli usando l'istruzione DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello business critical, che non ha questo limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valori GUID visualizzati al posto dei nomi di database

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID perché sono sostituiti con nomi di database effettivi in futuro.

**Soluzione temporanea**: utilizzare la vista sys. databases per risolvere il nome del database effettivo dal nome del database fisico, specificato sotto forma di identificatori del database GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>I log degli errori non sono salvati in stato permanente

I log degli errori disponibili nell'istanza gestita non sono salvati in modo permanente e la loro dimensione non è inclusa nel limite massimo di archiviazione. I log degli errori potrebbero essere cancellati automaticamente se si verifica il failover. Potrebbero esserci gap nella cronologia dei log degli errori perché Istanza gestita stato spostato più volte su diverse macchine virtuali.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L'ambito della transazione su due database all'interno della stessa istanza non è supportato

**(Risolto nel 2020 marzo)** La `TransactionScope` classe in .NET non funziona se due query vengono inviate a due database all'interno della stessa istanza nello stesso ambito di transazione:

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

**Soluzione alternativa (non necessaria dal 2020 marzo):** Utilizzare [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per utilizzare un altro database in un contesto di connessione anziché utilizzare due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>I moduli CLR e i server collegati talvolta non possono fare riferimento a un indirizzo IP locale

I moduli CLR posizionati in un'istanza gestita, i server collegati o le query distribuite che fanno riferimento a un'istanza corrente talvolta non riescono a risolvere l'indirizzo IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa:** Se possibile, utilizzare le connessioni di contesto in un modulo CLR.

## <a name="updates"></a>Aggiornamenti

Per un elenco degli aggiornamenti e dei miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per aggiornamenti e miglioramenti a tutti i servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Fornire il proprio contributo per i contenuti

Per contribuire alla documentazione del database SQL di Azure, vedere la guida per i [collaboratori di docs](https://docs.microsoft.com/contribute/).
