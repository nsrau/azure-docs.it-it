---
title: Novità
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Informazioni sulle nuove funzionalità e sui miglioramenti della documentazione per il database SQL di Azure & SQL Istanza gestita.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: ebbdd103350e1de36d45ecf84acf15d477fa34db
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058132"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Novità del database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo elenca le funzionalità del database SQL di Azure e di Azure SQL Istanza gestita attualmente disponibili in anteprima pubblica. Per gli aggiornamenti e i miglioramenti del database SQL e di SQL Istanza gestita, vedere SQL [database & sql istanza gestita Service Updates](https://azure.microsoft.com/updates/?product=sql-database). Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Novità

La documentazione per il database SQL di Azure e Istanza gestita SQL di Azure è stata suddivisa in sezioni separate. È stato anche aggiornato il modo in cui si fa riferimento a un'istanza gestita dall' *istanza gestita di database SQL di Azure* ad *Azure SQL istanza gestita*.

Questa operazione è stata eseguita perché alcune funzionalità e funzionalità variano molto tra un singolo database e un'istanza gestita e sono diventate sempre più complesse per spiegare le sfumature complesse tra il database SQL di Azure e Istanza gestita SQL di Azure in singoli articoli condivisi.

Questo chiarimento tra i diversi prodotti SQL di Azure dovrebbe semplificare e semplificare il processo di utilizzo del SQL Server motore di database in Azure, indipendentemente dal fatto che si tratti di un singolo database gestito nel database SQL di Azure, di un'istanza gestita completa che ospita più database in Azure SQL Istanza gestita o del familiare prodotto locale SQL Server ospitato in una macchina virtuale in Azure.

Si tenga presente che questo è un lavoro in corso e non tutti gli articoli sono stati ancora aggiornati. Ad esempio, la documentazione per le istruzioni Transact-SQL (T-SQL), le stored procedure e molte funzionalità condivise tra il database SQL di Azure e Istanza gestita SQL di Azure non sono ancora state completate, quindi ci ringraziamo per la pazienza mentre continuiamo a chiarire il contenuto. 

Questa tabella fornisce un confronto rapido per la modifica della terminologia: 


|**Nuovo termine**  | **Termine precedente**  |**Spiegazione** |
|---------|---------|---------|
|**Istanza gestita di database SQL di Azure** | *Istanza gestita* di database SQL di Azure| Azure SQL Istanza gestita è il proprio prodotto all'interno della famiglia SQL di Azure, anziché semplicemente un'opzione di distribuzione nel database SQL di Azure. | 
|**Database SQL di Azure**|Database *singolo* database SQL di Azure| Se non specificato diversamente in modo esplicito, il nome del prodotto database SQL di Azure include sia database singoli che database distribuiti in un pool elastico. |
|**Database SQL di Azure**|*Pool elastico* del database SQL di Azure| Se non specificato diversamente in modo esplicito, il nome del prodotto database SQL di Azure include sia database singoli che database distribuiti in un pool elastico.  |
|**Database SQL di Azure** |database SQL di Azure | Sebbene il termine rimanga invariato, ora si applica solo alle distribuzioni di database singolo e del pool elastico e non include l'istanza gestita. |
| **SQL di Azure**| N/D | Questo si riferisce alla famiglia di prodotti SQL Server motore di database disponibili in Azure: database SQL di Azure, Azure SQL Istanza gestita e SQL Server in macchine virtuali di Azure. | 

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Rescupero accelerato del database con database singoli e pool elastici | Per informazioni, vedere [Ripristino accelerato del database](../accelerated-database-recovery.md).|
| Individuazione dati e classificazione  |Per informazioni, vedere [database SQL di Azure e individuazione dei dati di analisi delle sinapsi di azure & classificazione](data-discovery-and-classification-overview.md).|
| Processi di database elastici (anteprima) | Per informazioni, vedere [Creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](elastic-transactions-overview.md). |
| Editor di query nel portale di Azure |Per informazioni, vedere [Usare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](connect-query-portal.md).|
| R Services/Machine Learning con database singoli e pool elastici |Per informazioni, vedere [Machine Learning Services nel database SQL di Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analitica SQL|Per informazioni, vedere [Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">Transazioni distribuite</a> | Transazioni distribuite tra istanze gestite. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Soluzione comoda ed economica per eseguire la migrazione di istanze SQL di dimensioni ridotte nel cloud. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entità di sicurezza server (accessi) di Azure AD a livello di istanza</a> | Creare account di accesso a livello di istanza usando un'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login from External provider</a> . |
| [Replica transazionale](../managed-instance/replication-transactional-overview.md) | Replicare le modifiche dalle tabelle in altri database di SQL Istanza gestita, database SQL o SQL Server. In alternativa, aggiornare le tabelle quando alcune righe vengono modificate in altre istanze di SQL Istanza gestita o SQL Server. Per informazioni, vedere [configurare la replica in Azure SQL istanza gestita](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Rilevamento delle minacce |Per informazioni, vedere [configurare il rilevamento delle minacce in Azure SQL istanza gestita](../managed-instance/threat-detection-configure.md).|
| Conservazione del backup a lungo termine | Per informazioni, vedere [configurare la conservazione del backup a lungo termine in Azure SQL istanza gestita](../managed-instance/long-term-backup-retention-configure.md), attualmente disponibile in anteprima pubblica limitata. | 

---

## <a name="new-features"></a>Nuove funzionalità

### <a name="sql-managed-instance-h2-2019-updates"></a>Aggiornamenti di SQL Istanza gestita H2 2019

- La [configurazione della subnet assistita da servizi](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) è un modo sicuro e pratico per gestire la configurazione delle subnet in cui è possibile controllare il traffico dei dati mentre SQL istanza gestita garantisce il flusso ininterrotto del traffico di gestione.
- [Transparent Data Encryption (Transparent Data Encryption) con Bring your own key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) consente uno scenario BYOK (Bring your own key) per la protezione dei dati inattivi e consente alle organizzazioni di separare i compiti di gestione per chiavi e dati.
- I [Gruppi di failover automatico](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- I [flag di traccia globali](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) consentono di configurare il comportamento di SQL istanza gestita.

### <a name="sql-managed-instance-h1-2019-updates"></a>Aggiornamenti di SQL Istanza gestita H1 2019

Le funzionalità seguenti sono abilitate nel modello di distribuzione di SQL Istanza gestita in H1 2019:
  - Supporto per le sottoscrizioni con <a href="https://aka.ms/sql-mi-visual-studio-subscribers"> credito mensile di Azure per i sottoscrittori di Visual Studio </a> e più [limiti a livello di area](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Supporto per <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 e sharepoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 business Central. </a>
  - Creare un'istanza gestita con <a href="https://aka.ms/managed-instance-collation">regole di confronto a livello di istanza</a> e un <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso orario</a> di propria scelta.
  - Le istanze gestite ora sono protette con un [firewall predefinito](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Configurare SQL Istanza gestita per usare gli [endpoint pubblici](../managed-instance/public-endpoint-configure.md), la connessione per [l'override del proxy](connectivity-architecture.md#connection-policy) per ottenere prestazioni di rete migliori, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vcore di generazione hardware quinta generazione</a> o <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurare la conservazione dei backup fino a 35 giorni per il</a> ripristino temporizzato. La [Conservazione del backup a lungo termine](long-term-retention-overview.md#sql-managed-instance-support) (fino a 10 anni) è attualmente in anteprima pubblica limitata.  
  - Le nuove funzionalità consentono di <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">eseguire il ripristino geografico del database in un altro data center tramite PowerShell</a>, [rinominare il database](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [eliminare il cluster virtuale](../managed-instance/virtual-cluster-delete.md).
  - Il nuovo [ruolo Collaboratore Istanza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) predefinito abilita la conformità della separazione dei compiti con i principi di sicurezza e la conformità agli standard aziendali.
  - SQL Istanza gestita è disponibile nelle aree di Azure per enti pubblici seguenti per la versione GA (US Gov Texas, US Gov Arizona) e in Cina settentrionale 2 e Cina orientale 2. È disponibile anche nelle aree pubbliche seguenti: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale, Sudafrica occidentale.

## <a name="known-issues"></a>Problemi noti

|Problema  |Data individuata  |Stato  |Data risolta  |
|---------|---------|---------|---------|
|[Le transazioni distribuite possono essere eseguite dopo aver rimosso Istanza gestita dal gruppo di trust del server](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020 Sep|Ha una soluzione alternativa||
|[Non è possibile eseguire le transazioni distribuite dopo Istanza gestita operazione di ridimensionamento](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020 Sep|Ha una soluzione alternativa||
|[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql) nell'istruzione SQL di Azure e `BACKUP` / `RESTORE` in istanza gestita non è possibile usare Azure ad Gestisci identità per l'autenticazione in archiviazione di Azure|2020 Sep|Ha una soluzione alternativa||
|[L'entità servizio non può accedere a Azure AD e AKV](#service-principal-cannot-access-azure-ad-and-akv)|2020 agosto|Ha una soluzione alternativa||
|[Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo](#restoring-manual-backup-without-checksum-might-fail)|Maggio 2020|Risolto|Giugno 2020|
|[Agent smette di rispondere durante la modifica, la disabilitazione o l'abilitazione di processi esistenti](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maggio 2020|Risolto|Giugno 2020|
|[Autorizzazioni per il gruppo di risorse non applicate a SQL Istanza gestita](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Febbraio 2020|Ha una soluzione alternativa||
|[Limitazione del failover manuale tramite il portale per i gruppi di failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Gennaio 2020|Ha una soluzione alternativa||
|[Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dicembre 2019|Ha una soluzione alternativa||
|[I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dicembre 2019|Risolto|Marzo 2020|
|[Gli account di accesso e gli utenti Azure AD non sono supportati in SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Novembre 2019|Nessuna soluzione alternativa||
|[I limiti di memoria di OLTP in memoria non vengono applicati](#in-memory-oltp-memory-limits-are-not-applied)|Ottobre 2019|Ha una soluzione alternativa||
|[Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Ottobre 2019|Ha una soluzione alternativa||
|[Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Settembre 2019|Ha una soluzione alternativa||
|[Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Settembre 2019|Ha una soluzione alternativa||
|[È necessario reinizializzare le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Agosto 2019|Ha una soluzione alternativa||
|[La rappresentazione dei tipi di account di accesso Azure AD non è supportata](#impersonation-of-azure-ad-login-types-is-not-supported)|Luglio 2019|Nessuna soluzione alternativa||
|[Parametro @query non supportato in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Nessuna soluzione alternativa||
|[È necessario riconfigurare la replica transazionale dopo il failover geografico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Marzo 2019|Nessuna soluzione alternativa||
|[Il database temporaneo viene usato durante l'operazione RESTORE](#temporary-database-is-used-during-restore-operation)||Ha una soluzione alternativa||
|[La struttura e il contenuto di TEMPDB vengono ricreati](#tempdb-structure-and-content-is-re-created)||Nessuna soluzione alternativa||
|[Superamento dello spazio di archiviazione con file di database di piccole dimensioni](#exceeding-storage-space-with-small-database-files)||Ha una soluzione alternativa||
|[Valori GUID visualizzati al posto dei nomi di database](#guid-values-shown-instead-of-database-names)||Ha una soluzione alternativa||
|[I log degli errori non sono permanenti](#error-logs-arent-persisted)||Nessuna soluzione alternativa||
|[L'ambito della transazione in due database nella stessa istanza non è supportato](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ha una soluzione alternativa|Marzo 2020|
|[I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ha una soluzione alternativa||
|Coerenza del database non verificata usando DBCC CHECKDB dopo il ripristino del database dall'Archiviazione BLOB di Azure.||Risolto|Novembre 2019|
|Il ripristino temporizzato di database dal livello business critical al livello utilizzo generico non avrà esito positivo se il database di origine contiene oggetti OLTP in memoria.||Risolto|Ottobre 2019|
|Funzionalità posta elettronica database con server di posta elettronica esterni (non Azure) tramite connessione protetta||Risolto|Ottobre 2019|
|Database indipendenti non supportati in SQL Istanza gestita||Risolto|Agosto 2019|

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Le transazioni distribuite possono essere eseguite dopo aver rimosso Istanza gestita dal gruppo di trust del server

I [gruppi di attendibilità del server](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) vengono utilizzati per stabilire una relazione di trust tra le istanze gestite prerequisito per l'esecuzione di [transazioni distribuite](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview). Dopo aver rimosso Istanza gestita dal gruppo di attendibilità del server o aver eliminato il gruppo, è ancora possibile eseguire transazioni distribuite. Esiste una soluzione alternativa che è possibile applicare per assicurarsi che le transazioni distribuite siano disabilitate e che il [failover manuale avviato dall'utente](https://docs.microsoft.com/azure/azure-sql/managed-instance/user-initiated-failover) in istanza gestita.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Non è possibile eseguire le transazioni distribuite dopo Istanza gestita operazione di ridimensionamento

Istanza gestita operazioni di ridimensionamento che includono la modifica del livello di servizio o il numero di Vcore Reimposta le impostazioni del gruppo di attendibilità del server nel back-end e Disabilita [le transazioni distribuite](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview) Per risolvere il problema, eliminare e creare un nuovo [gruppo di trust server](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) in portale di Azure.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT e le istruzioni BACKUP/RESTOre non possono usare identità gestite per accedere ad archiviazione di Azure

L'istruzione BULK INSERT non può usare `DATABASE SCOPED CREDENTIAL` with Managed Identity per l'autenticazione nell'archiviazione di Azure. Come soluzione alternativa, passare all'autenticazione della firma di accesso condiviso. L'esempio seguente non funzionerà in SQL di Azure (database e Istanza gestita):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Soluzione alternativa**: usare la [firma di accesso condiviso per l'autenticazione nella](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage)risorsa di archiviazione.

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>L'entità servizio non può accedere a Azure AD e AKV

In alcune circostanze potrebbe esistere un problema con l'entità servizio usata per accedere ai servizi Azure AD e Azure Key Vault (AKV). Questo problema influisca quindi sull'utilizzo dell'autenticazione Azure AD e della crittografia Transparent Database Encryption (Transparent Database Encryption) con SQL Istanza gestita. Questo potrebbe essere un problema di connettività intermittente o non essere in grado di eseguire istruzioni, ad esempio creare un account di accesso/utente da un PROVIDER esterno o eseguire come account di accesso/utente. La configurazione di Transparent Data Encryption con una chiave gestita dal cliente in un nuovo Istanza gestita SQL di Azure potrebbe anche non funzionare in alcune circostanze.

**Soluzione alternativa**: per evitare che questo problema si verifichi nel istanza gestita SQL prima di eseguire i comandi di aggiornamento o, nel caso in cui il problema sia già stato riscontrato dopo l'aggiornamento, passare a portale di Azure, accedere a sql istanza gestita [Active Directory admin](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#azure-portal). Verificare che venga visualizzato il messaggio di errore "Istanza gestita necessaria un'entità servizio per accedere Azure Active Directory. Fare clic qui per creare un'entità servizio ". Se è stato rilevato questo messaggio di errore, fare clic su di esso e seguire le istruzioni dettagliate fornite fino a quando l'errore non è stato risolto.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo

In alcuni casi, il backup manuale dei database eseguiti in un'istanza gestita senza CHECKSUM potrebbe non essere ripristinato. In questi casi, riprovare a ripristinare il backup fino a quando l'operazione non viene completata.

**Soluzione alternativa**: eseguire il backup manuale dei database nelle istanze gestite con checksum abilitato.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent smette di rispondere durante la modifica, la disabilitazione o l'abilitazione di processi esistenti

In alcuni casi, la modifica, la disabilitazione o l'abilitazione di un processo esistente può causare la mancata risposta da parte dell'agente. Il problema viene risolto automaticamente al momento del rilevamento, con conseguente riavvio del processo dell'agente.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Autorizzazioni per il gruppo di risorse non applicate a SQL Istanza gestita

Quando il ruolo Azure collaboratore SQL Istanza gestita viene applicato a un gruppo di risorse (RG), non viene applicato a SQL Istanza gestita e non ha alcun effetto.

**Soluzione temporanea**: configurare un ruolo di collaboratore di SQL istanza gestita per gli utenti a livello di sottoscrizione.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitazione del failover manuale tramite il portale per i gruppi di failover

Se un gruppo di failover si estende su più istanze in sottoscrizioni o gruppi di risorse di Azure diversi, non è possibile avviare il failover manuale dall'istanza primaria del gruppo di failover.

**Soluzione temporanea**: avviare il failover tramite il portale dall'istanza geografica secondaria.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin

Se gli account di accesso non sysadmin vengono aggiunti ai [ruoli predefiniti del database di SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), esiste un problema per cui le autorizzazioni Execute esplicite devono essere concesse alle stored procedure master per il funzionamento di tali account di accesso. Se si verifica questo problema, si riceve l'errore "The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229) [L'autorizzazione EXECUTE è stata negata per l'oggetto <object_name> (Microsoft SQL Server, Error: 229)]".

**Soluzione alternativa**: dopo aver aggiunto gli account di accesso a un ruolo predefinito del database di SQL Agent (SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole), per ognuno degli account di accesso aggiunti a questi ruoli, eseguire lo script T-SQL seguente per concedere esplicitamente le autorizzazioni Execute alle stored procedure elencate.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agente

**(Risolto nel 2020 marzo)** SQL Agent crea una nuova sessione ogni volta che viene avviato un processo, aumentando gradualmente il consumo di memoria. Per evitare di raggiungere il limite di memoria interno, che blocca l'esecuzione dei processi pianificati, il processo dell'agente verrà riavviato quando il consumo di memoria raggiunge la soglia. Potrebbe causare l'interruzione dei processi in esecuzione al momento del riavvio.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>I limiti di memoria di OLTP in memoria non vengono applicati

In alcuni casi, il livello di servizio business critical non applica correttamente i [limiti di memoria massima per gli oggetti ottimizzati per la memoria](../managed-instance/resource-limits.md#in-memory-oltp-available-space) . SQL Istanza gestita può consentire al carico di lavoro di usare più memoria per le operazioni di OLTP in memoria, che potrebbero influire sulla disponibilità e sulla stabilità dell'istanza. Le query di OLTP in memoria che raggiungono i limiti potrebbero non avere subito esito negativo. Questo problema verrà risolto a breve. Le query che usano una quantità maggiore di memoria OLTP in memoria avranno esito negativo prima di raggiungere i [limiti](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Soluzione alternativa**: [monitorare l'utilizzo dell'archiviazione OLTP in memoria](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space) utilizzando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) per garantire che il carico di lavoro non utilizzi una quantità di memoria superiore a quella disponibile. Aumentare i limiti di memoria che dipendono dal numero di vCore oppure ottimizzare il carico di lavoro per usare meno memoria.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto

SQL Server e SQL Istanza gestita [non consentono a un utente di eliminare un file non vuoto](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se si tenta di rimuovere un file di dati non vuoto utilizzando un' `ALTER DATABASE REMOVE FILE` istruzione, l'errore `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` non verrà restituito immediatamente. SQL Istanza gestita continuerà a provare a eliminare il file e l'operazione avrà esito negativo dopo 30 minuti con `Internal server error` .

**Soluzione temporanea**: rimuovere il contenuto del file utilizzando il `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` comando. Se si tratta dell'unico file del gruppo di file, è necessario eliminare i dati dalla tabella o dalla partizione associata a questo gruppo di file prima di compattare il file e, facoltativamente, caricare i dati in un'altra tabella o partizione.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso

`RESTORE`L'istruzione continuativa, il processo di migrazione del servizio migrazione dei dati e il ripristino temporizzato incorporato bloccano l'aggiornamento di un livello di servizio o il ridimensionamento dell'istanza esistente e la creazione di nuove istanze fino al termine del processo di ripristino. 

Il processo di ripristino bloccherà queste operazioni sulle istanze gestite e sui pool di istanze nella stessa subnet in cui è in esecuzione il processo di ripristino. Le istanze nei pool di istanze non sono interessate. La creazione o la modifica delle operazioni del livello di servizio non avrà esito negativo o il timeout. Verranno eseguiti dopo il completamento o l'annullamento del processo di ripristino.

**Soluzione temporanea**: attendere il completamento del processo di ripristino o annullare il processo di ripristino se l'operazione di creazione o aggiornamento del livello di servizio ha una priorità più alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover

La funzionalità [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) che consente di limitare le risorse assegnate al carico di lavoro dell'utente potrebbe classificare erroneamente un carico di lavoro dell'utente dopo il failover o una modifica avviata dall'utente del livello di servizio (ad esempio, la modifica delle dimensioni massime di archiviazione delle istanze di vCore o max).

**Soluzione temporanea**: eseguire `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente o come parte di un processo di SQL Agent che esegue l'attività SQL quando l'istanza viene avviata se si usa [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>È necessario reinizializzare le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio

Le finestre di dialogo Service Broker tra database interromperanno l'invio di messaggi ai servizi di altri database dopo l'operazione di modifica del livello di servizio. I messaggi *non vengono persi*ed è possibile trovarli nella coda del mittente. Qualsiasi modifica delle dimensioni di archiviazione dell'istanza o di Vcore in SQL Istanza gestita causerà la modifica di un `service_broke_guid` valore nella vista [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) per tutti i database. Qualsiasi `DIALOG` oggetto creato utilizzando un'istruzione [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) che fa riferimento a broker di servizio in un altro database arresterà il recapito dei messaggi al servizio di destinazione.

**Soluzione temporanea**: arrestare tutte le attività che usano le conversazioni di dialogo Service Broker tra database prima di aggiornare un livello di servizio e reinizializzarle successivamente. Se sono presenti messaggi rimanenti che non vengono recapitati dopo una modifica del livello di servizio, leggere i messaggi dalla coda di origine e inviarli nuovamente alla coda di destinazione.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>La rappresentazione dei tipi di account di accesso Azure AD non è supportata

La rappresentazione tramite `EXECUTE AS USER` o `EXECUTE AS LOGIN` delle seguenti entità di Azure Active Directory (Azure ad) non è supportata:
-   Utenti Azure AD con alias. In questo caso viene restituito l'errore seguente: `15517` .
- Azure AD gli account di accesso e gli utenti basati su applicazioni Azure AD o entità servizio. In questo caso vengono restituiti gli errori seguenti: `15517` e `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>Parametro @query non supportato in sp_send_db_mail

Il parametro `@query` nella procedura [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) non funziona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>È necessario riconfigurare la replica transazionale dopo il failover geografico

Se la replica transazionale è abilitata in un database in un gruppo di failover automatico, l'amministratore di SQL Istanza gestita deve eseguire la pulizia di tutte le pubblicazioni nel database primario precedente e riconfigurarle sul nuovo database primario dopo un failover in un'altra area. Per ulteriori informazioni, vedere [replica](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Gli account di accesso e gli utenti Azure AD non sono supportati in SSDT

SQL Server Data Tools non supportano completamente Azure AD account di accesso e utenti.

### <a name="temporary-database-is-used-during-restore-operation"></a>Il database temporaneo viene usato durante l'operazione RESTORE

Quando si esegue il ripristino di un database in SQL Istanza gestita, il servizio di ripristino creerà prima di tutto un database vuoto con il nome desiderato per allocare il nome nell'istanza. Dopo un certo periodo di tempo, il database verrà eliminato e verrà avviato il ripristino del database effettivo. 

Il database in stato di *ripristino* avrà temporaneamente un valore GUID casuale anziché Name. Al termine del processo di ripristino, il nome temporaneo verrà modificato con il nome desiderato specificato nell' `RESTORE` istruzione. 

Nella fase iniziale, un utente può accedere al database vuoto e persino creare tabelle o caricare dati in questo database. Questo database temporaneo verrà rimosso quando il servizio di ripristino avvierà la seconda fase.

**Soluzione alternativa**: non accedere al database in fase di ripristino fino al completamento dell'operazione.

### <a name="tempdb-structure-and-content-is-re-created"></a>La struttura e il contenuto di TEMPDB vengono ricreati

Il `tempdb` database viene sempre suddiviso in 12 file di dati e la struttura del file non può essere modificata. Le dimensioni massime per file non possono essere modificate e non è possibile aggiungere nuovi file a `tempdb`. `Tempdb` viene sempre ricreato come database vuoto quando l'istanza viene avviata o si effettua il failover e tutte le modifiche apportate in `tempdb` non verranno mantenute.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

Le istruzioni `CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` potrebbero avere esito negativo perché l'istanza può raggiungere il limite di Archiviazione di Azure.

Ogni istanza per utilizzo generico di SQL Istanza gestita ha fino a 35 TB di spazio di archiviazione riservato per lo spazio su disco Premium di Azure. Ogni file di database si trova in un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato sul disco non viene conteggiato, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni delle risorse di archiviazione a causa della frammentazione interna.

Ad esempio, un'istanza di per utilizzo generico di SQL Istanza gestita potrebbe avere un file di grandi dimensioni di 1,2 TB in un disco da 4 TB. Potrebbero inoltre essere presenti 248 file di 1 GB ciascuno e posizionati in dischi 128 GB distinti. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio illustra che in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza di SQL Istanza gestita potrebbe raggiungere il limite di 35 TB riservato per un disco Premium di Azure collegato quando potrebbe non essere previsto.

In questo esempio, i database esistenti continuano a funzionare e possono crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. Non è possibile creare o ripristinare nuovi database a causa dello spazio insufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite di dimensioni dell'istanza. L'errore restituito in questo caso potrebbe non essere chiaro.

È possibile [identificare il numero di file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando le viste di sistema. Se si raggiunge questo limite, provare a [svuotare ed eliminare alcuni dei file più piccoli usando l'istruzione DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello di servizio business critical, che non ha questo limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valori GUID visualizzati al posto dei nomi di database

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID, in quanto vengono sostituiti dai nomi effettivi dei database in futuro.

**Soluzione temporanea**: utilizzare la vista sys. databases per risolvere il nome del database effettivo dal nome del database fisico, specificato sotto forma di identificatori del database GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>I log degli errori non sono permanenti

I log degli errori disponibili in SQL Istanza gestita non sono salvati in modo permanente e la loro dimensione non è inclusa nel limite massimo di archiviazione. I log degli errori potrebbero essere cancellati automaticamente se si esegue il failover. Potrebbero esserci gap nella cronologia dei log degli errori perché SQL Istanza gestita è stato spostato più volte su diverse macchine virtuali.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L'ambito della transazione in due database nella stessa istanza non è supportato

**(Risolto a marzo 2020)** La classe `TransactionScope` in .NET non funziona se vengono inviate due query ai due database nella stessa istanza all'interno del medesimo ambito della transazione:

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

**Soluzione alternativa (non necessaria dal 2020 marzo)**: usare [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per usare un altro database in un contesto di connessione anziché usare due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale

I moduli CLR in SQL Istanza gestita e i server collegati o le query distribuite che fanno riferimento a un'istanza corrente talvolta non riescono a risolvere l'indirizzo IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione temporanea**: se possibile, usare connessioni di contesto in un modulo CLR.

## <a name="updates"></a>Aggiornamenti

Per un elenco di aggiornamenti e miglioramenti del database SQL, vedere [Aggiornamenti del servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per gli aggiornamenti e i miglioramenti apportati a tutti i servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuire al contenuto

Per contribuire alla documentazione di Azure SQL, vedere la [Guida](https://docs.microsoft.com/contribute/)per i collaboratori di docs.
