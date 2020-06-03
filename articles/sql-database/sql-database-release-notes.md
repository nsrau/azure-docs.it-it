---
title: Note sulla versione
description: Informazioni sulle nuove funzionalità e miglioramenti nel servizio database SQL di Azure e nella documentazione del database SQL di Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: sstein
ms.openlocfilehash: 3e5069c779cee0700bff6b2236f3cd36547fd623
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659613"
---
# <a name="sql-database-release-notes"></a>Note sulla versione del database SQL

Questo articolo elenca le funzionalità del database SQL attualmente disponibili in anteprima pubblica. Per aggiornamenti e miglioramenti del database SQL, vedere [Aggiornamenti del servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="single-database"></a>[Database singolo](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Nuove generazioni di hardware serie Fsv2 e serie M| Per informazioni, vedere [Generazioni hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| Rescupero accelerato del database con database singoli e pool elastici | Per informazioni, vedere [Ripristino accelerato del database](sql-database-accelerated-database-recovery.md).|
|Count Distinct approssimato|Per informazioni, vedere [Count Distinct approssimato](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità batch per rowstore (con livello di compatibilità 150)|Per informazioni, vedere [Modalità batch per rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere[Individuazione dati e classificazione per SQL Data Warehouse e database SQL di Azure](sql-database-data-discovery-and-classification.md).|
| Processi di database elastici | Per informazioni, vedere [Creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Commenti sulla concessione di memoria (modalità riga) (con livello di compatibilità 150)|Per informazioni, vedere [Commenti sulla concessione di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di Azure |Per informazioni, vedere [Usare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).|
| R Services/Machine Learning con database singoli e pool elastici |Per informazioni, vedere [Machine Learning Services nel database SQL di Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analitica SQL|Per informazioni, vedere [Analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).|
|Compilazione posticipata delle variabili di tabella (con livello di compatibilità 150)|Per informazioni, vedere [Compilazione posticipata delle variabili di tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Soluzione comoda ed economica per eseguire la migrazione di istanze SQL di dimensioni ridotte nel cloud. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entità di sicurezza server (accessi) di Azure AD a livello di istanza</a> | Creare accessi a livello di server usando l'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>. |
| [Replica transazionale](sql-database-managed-instance-transactional-replication.md) | Replicare le modifiche dalle tabelle in altri database posizionati in Istanze gestite, Database singoli o Istanze di SQL Server o aggiornare le tabelle quando alcune righe vengono modificate in altre istanze gestite o in un'Istanza SQL Server. Per informazioni, vedere [Configurare la replica in un database di Istanza gestita del database SQL di Azure](replication-with-sql-database-managed-instance.md). |
| Introduzione al rilevamento delle minacce |Per informazioni, vedere [Configurare il rilevamento delle minacce in Istanza gestita del database SQL di Azure](sql-database-managed-instance-threat-detection.md).|
| Conservazione del backup a lungo termine | Per informazioni, vedere [Configurare la conservazione del backup a lungo termine in Istanza gestita del database SQL di Azure](sql-database-managed-instance-long-term-backup-retention-configure.md), attualmente in anteprima pubblica limitata. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Istanza gestita: nuove funzionalità e problemi noti

### <a name="managed-instance-h2-2019-updates"></a>Aggiornamenti di Istanza gestita H2 2019

- [Configurazione della subnet assistita dal servizio](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Un modo sicuro e pratico per gestire la configurazione delle subnet in cui è possibile controllare il traffico dei dati mentre Istanza gestita garantisce il flusso ininterrotto del traffico di gestione
- [Transparent Data Encryption (TDE) con Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) abilita lo scenario Bring your own key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di separare i compiti di gestione per chiavi e dati.
- I [Gruppi di failover automatico](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- Configurare il comportamento di Istanza gestita con [Flag di traccia globali](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aggiornamenti di Istanza gestita H1 2019

Le funzionalità seguenti sono abilitate nel modello di distribuzione di Istanza gestita in H1 2019:
  - Supporto per le sottoscrizioni con <a href="https://aka.ms/sql-mi-visual-studio-subscribers"> credito mensile di Azure per i sottoscrittori di Visual Studio </a> e più [limiti a livello di area](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Supporto per <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e per <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Creare istanze con <a href="https://aka.ms/managed-instance-collation">regole di confronto a livello di server</a> e <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso orario</a> di propria scelta.
  - Le istanze gestite ora sono protette con un <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall predefinito</a>.
  - Configurare le istanze per usare [endpoint pubblici](sql-database-managed-instance-public-endpoint-configure.md), connessione di [override di proxy](sql-database-connectivity-architecture.md#connection-policy) per ottenere prestazioni di rete migliori, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCore in hardware di quinta generazione</a> o <a href="https://aka.ms/managed-instance-configurable-backup-retention">Configurare la conservazione del backup fino a 35 giorni</a> per il ripristino temporizzato. La [Conservazione del backup a lungo termine](sql-database-long-term-retention.md#managed-instance-support) (fino a 10 anni) è attualmente in anteprima pubblica limitata.  
  - Le nuove funzionalità consentono di <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">eseguire il ripristino geografico del database in un altro data center tramite PowerShell</a>, [rinominare il database](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [eliminare il cluster virtuale](sql-database-managed-instance-delete-virtual-cluster.md).
  - Il nuovo [ruolo Collaboratore Istanza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) predefinito abilita la conformità della separazione dei compiti con i principi di sicurezza e la conformità agli standard aziendali.
  - Istanza gestita è disponibile nelle aree di Azure per enti pubblici seguenti: GA (US Gov Texas, US Gov Arizona), in Cina settentrionale 2 e Cina orientale 2. È disponibile anche nelle aree pubbliche seguenti: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale, Sudafrica occidentale.

### <a name="known-issues"></a>Problemi noti

|Problema  |Data individuata  |Stato  |Data risolta  |
|---------|---------|---------|---------|
|[Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo](#restoring-manual-backup-without-checksum-might-fail)|Maggio 2020|Ha una soluzione alternativa| |
|[L'agente non risponde dopo la modifica, la disabilitazione o l'abilitazione di processi esistenti](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maggio 2020|Attenuato automaticamente| |
|[Autorizzazioni per il gruppo di risorse non applicate a Istanza gestita](#permissions-on-resource-group-not-applied-to-managed-instance)|Febbraio 2020|Ha una soluzione alternativa| |
|[Limitazione del failover manuale tramite il portale per i gruppi di failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Gennaio 2020|Ha una soluzione alternativa| |
|[Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dicembre 2019|Ha una soluzione alternativa| |
|[I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dicembre 2019|Risolto|Marzo 2020|
|[Gli accessi e gli utenti di AAD non sono supportati in SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Novembre 2019|Nessuna soluzione alternativa| |
|[I limiti di memoria di OLTP in memoria non vengono applicati](#in-memory-oltp-memory-limits-are-not-applied)|Ottobre 2019|Ha una soluzione alternativa| |
|[Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Ottobre 2019|Ha una soluzione alternativa| |
|[Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Settembre 2019|Ha una soluzione alternativa| |
|[Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Settembre 2019|Ha una soluzione alternativa| |
|[È necessario inizializzare nuovamente le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Agosto 2019|Ha una soluzione alternativa| |
|[La rappresentazione dei tipi di accesso Azure AD non è supportata](#impersonification-of-azure-ad-login-types-is-not-supported)|Luglio 2019|Nessuna soluzione alternativa| |
|[Parametro @query non supportato in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Nessuna soluzione alternativa| |
|[È necessario riconfigurare la replica transazionale dopo il failover geografico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Marzo 2019|Nessuna soluzione alternativa| |
|[Il database temporaneo viene usato durante l'operazione RESTORE](#temporary-database-is-used-during-restore-operation)||Ha una soluzione alternativa| |
|[La struttura e il contenuto di TEMPDB vengono ricreati](#tempdb-structure-and-content-is-re-created)| |Nessuna soluzione alternativa| |
|[Superamento dello spazio di archiviazione con file di database di piccole dimensioni](#exceeding-storage-space-with-small-database-files)| |Ha una soluzione alternativa| |
|[Valori GUID visualizzati al posto dei nomi di database](#guid-values-shown-instead-of-database-names) ||Ha una soluzione alternativa| |
|[I log degli errori non sono permanenti](#error-logs-arent-persisted)||Nessuna soluzione alternativa| |
|[I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |Ha una soluzione alternativa| |
|[L'ambito della transazione in due database nella stessa istanza non è supportato](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |Risolto|Marzo 2020|
|Coerenza del database non verificata usando DBCC CHECKDB dopo il ripristino del database dall'Archiviazione BLOB di Azure.| |Risolto|Novembre 2019|
|Il ripristino temporizzato di database dal livello business critical al livello utilizzo generico non avrà esito positivo se il database di origine contiene oggetti OLTP in memoria.| |Risolto|Ottobre 2019|
|Funzionalità Posta elettronica database con server di posta elettronica esterni (non Azure) tramite connessione protetta| |Risolto|Ottobre 2019|
|Database indipendenti non supportati in Istanza gestita| |Risolto|Agosto 2019|

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo

In alcuni casi, il backup manuale dei database eseguito in Istanza gestita senza CHECKSUM potrebbe non essere ripristinato. In tal caso, riprovare a ripristinare il backup fino a quando non l'operazione non ha esito positivo.

**Soluzione alternativa**: eseguire backup manuali dei database in Istanza gestita con CHECKSUM abilitato.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>L'agente non risponde dopo la modifica, la disabilitazione o l'abilitazione di processi esistenti

In alcune circostanze, la modifica, la disabilitazione o l'abilitazione di un processo esistente può causare la mancata risposta da parte dell'agente. Il problema viene risolto automaticamente al momento del rilevamento, con conseguente riavvio del processo dell'agente.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Autorizzazioni per il gruppo di risorse non applicate a Istanza gestita

Il ruolo RBAC Collaboratore di Istanza gestita, quando applicato a un gruppo di risorse (RG), non viene applicato a Istanza gestita e non ha alcun effetto.

**Soluzione alternativa**: configurare il ruolo Collaboratore di Istanza gestita per gli utenti a livello di sottoscrizione.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitazione del failover manuale tramite il portale per i gruppi di failover

Se il gruppo di failover si estende su più istanze in sottoscrizioni o gruppi di risorse di Azure, non è possibile avviare il failover manuale dall'istanza primaria nel gruppo di failover.

**Soluzione alternativa**: avviare il failover tramite il portale dall'istanza geografica secondaria.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin

Se gli accessi non sysadmin vengono aggiunti a uno dei [ruoli predefiniti del database di SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), si verifica un problema per cui le autorizzazioni EXECUTE esplicite devono essere concesse alle stored procedure master per il corretto funzionamento di questi accessi. Se si verifica questo problema, si riceve l'errore "The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229) [L'autorizzazione EXECUTE è stata negata per l'oggetto <object_name> (Microsoft SQL Server, Error: 229)]".

**Soluzione alternativa**: dopo aver aggiunto accessi a uno dei ruoli predefiniti del database di SQL Agent: SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole, per ognuno degli accessi aggiunti a questi ruoli, eseguire lo script T-SQL seguente per concedere esplicitamente le autorizzazioni EXECUTE alle stored procedure elencate.

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

**(Risolto a marzo 2020)** SQL Agent crea una nuova sessione ogni volta che si avvia il processo, aumentando gradualmente il consumo di memoria. Per evitare di raggiungere il limite di memoria interna bloccando l'esecuzione dei processi pianificati, il processo dell'agente verrà riavviato quando il consumo di memoria raggiunge la soglia. Potrebbe causare l'interruzione dei processi in esecuzione al momento del riavvio.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>I limiti di memoria di OLTP in memoria non vengono applicati

In alcuni casi, il livello di servizio business critical non applicherà correttamente i [limiti di memoria massima per gli oggetti ottimizzati per la memoria](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space). Istanza gestita può consentire al carico di lavoro di usare più memoria per le operazioni di OLTP in memoria e questo potrebbe influire sulla disponibilità e sulla stabilità dell'istanza. Le query di OLTP in memoria che raggiungono i limiti potrebbero non avere subito esito negativo. Questo problema verrà risolto a breve. Le query che usano più memoria OLTP in memoria avranno esito negativo più velocemente se raggiungono i [limiti](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Soluzione alternativa:** [monitorare l'utilizzo della risorsa di archiviazione OLTP in memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) usando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) per garantire che il carico di lavoro non usi più memoria di quella disponibile. Aumentare i limiti di memoria che dipendono dal numero di vCore oppure ottimizzare il carico di lavoro per usare meno memoria.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto

SQL Server/Istanza gestita [non consentono all'utente di rimuovere un file non vuoto](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se si tenta di rimuovere un file di dati non vuoto usando l'istruzione `ALTER DATABASE REMOVE FILE`, l'errore `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` non verrà restituito immediatamente. Istanza gestita proverà a rimuovere il file e l'operazione avrà esito negativo dopo 30 minuti con `Internal server error`.

**Soluzione alternativa**: rimuovere il contenuto del file usando il comando `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Se si tratta dell'unico file del filegroup, è necessario eliminare i dati dalla tabella o dalla partizione associata a questo filegroup prima di compattare il file e, facoltativamente, caricare i dati in un'altra tabella o partizione.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso

L'istruzione `RESTORE` in corso, il processo di migrazione del servizio di migrazione dati e il ripristino temporizzato predefinito bloccheranno l'aggiornamento del livello di servizio o il ridimensionamento dell'istanza esistente e la creazione di nuove istanze fino al termine del processo di ripristino. Il processo di ripristino bloccherà queste operazioni sulle istanze gestite e sui pool di istanze nella stessa subnet in cui è in esecuzione il processo di ripristino. Le istanze nei pool di istanze non sono interessate. La creazione o la modifica delle operazioni del livello di servizio non avrà esito negativo e non scadrà per timeout. Queste operazioni procederanno dopo che il processo di ripristino è completato o annullato.

**Soluzione alternativa**: attendere il completamento del processo di ripristino o annullare il processo di ripristino se la creazione o l'aggiornamento del livello di servizio ha una priorità più elevata.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) è una funzionalità che consente di limitare le risorse assegnate al carico di lavoro dell'utente e potrebbe classificare erroneamente un carico di lavoro dell'utente dopo il failover o la modifica del livello di servizio avviata dall'utente (ad esempio, la modifica delle dimensioni massime di vCore o della risorsa di archiviazione delle istanze).

**Soluzione alternativa**: eseguire `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente o come parte del processo di SQL Agent che esegue l'attività SQL quando l'istanza viene avviata se si usa [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>È necessario inizializzare nuovamente le finestre di dialogo Service Broker tra database dopo l'aggiornamento del livello di servizio

Le finestre di dialogo Service Broker tra database interromperanno l'invio di messaggi ai servizi di altri database dopo l'operazione di modifica del livello di servizio. I messaggi **non vengono persi** e si trovano nella coda del mittente. Qualsiasi modifica di vCore o delle dimensioni della risorsa di archiviazione dell'istanza in Istanza gestita, causerà la modifica del valore `service_broke_guid` nella vista [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) per tutti i database. Qualsiasi `DIALOG` creata usando l'istruzione [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) che fa riferimento a Service Broker in un altro database interromperà l'invio dei messaggi al servizio di destinazione.

**Soluzione alternativa:** arrestare tutte le attività che usano conversazioni di dialogo Service Broker tra database prima di aggiornare il livello di servizio e reinizializzarle dopo. Se sono presenti messaggi che non vengono inviati dopo la modifica del livello di servizio, leggere i messaggi dalla coda di origine e inviarli nuovamente alla coda di destinazione.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>La rappresentazione dei tipi di accesso Azure AD non è supportata

La rappresentazione con `EXECUTE AS USER` o `EXECUTE AS LOGIN` delle entità AAD seguenti non è supportata:
-    Utenti di AAD con alias. In questo caso viene restituito l'errore seguente `15517`.
- Accessi e utenti di AAD basati su applicazioni o entità servizio di AAD. In questo caso vengono restituiti gli errori seguenti `15517` e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>Parametro @query non supportato in sp_send_db_mail

Il parametro `@query` nella procedura [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) non funziona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>È necessario riconfigurare la replica transazionale dopo il failover geografico

Se la replica transazionale è abilitata in un database in un gruppo di failover automatico, l'amministratore dell'istanza gestita deve eseguire la pulizia di tutte le pubblicazioni nel database primario precedente e riconfigurarle sul nuovo database primario dopo che si è verificato un failover in un'altra area. Per ulteriori informazioni, vedere [Replica](sql-database-managed-instance-transact-sql-information.md#replication).

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Gli accessi e gli utenti di AAD non sono supportati in SSDT

SQL Server Data Tools non supporta completamente gli accessi e gli utenti di Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Il database temporaneo viene usato durante l'operazione RESTORE

Quando un database viene ripristinato in Istanza gestita, il servizio di ripristino creerà prima di tutto un database vuoto con il nome desiderato per allocare il nome nell'istanza. Dopo un certo periodo di tempo, il database verrà rimosso e si avvierà il ripristino del database effettivo. Il database in stato di *Ripristino* avrà temporaneamente un valore GUID casuale anziché un nome. Al termine del processo di ripristino, il nome temporaneo verrà sostituito dal nome desiderato indicato nell'istruzione `RESTORE`. Nella fase iniziale, l'utente può accedere al database vuoto e anche creare tabelle o caricare dati in questo database. Questo database temporaneo verrà rimosso quando il servizio di ripristino avvierà la seconda fase.

**Soluzione alternativa**: non accedere al database in fase di ripristino fino al completamento dell'operazione.

### <a name="tempdb-structure-and-content-is-re-created"></a>La struttura e il contenuto di TEMPDB vengono ricreati

Il database `tempdb` viene sempre suddiviso in 12 file di dati e la struttura del file non può essere modificata. Le dimensioni massime per file non possono essere modificate e non è possibile aggiungere nuovi file a `tempdb`. `Tempdb` viene sempre ricreato come database vuoto quando l'istanza viene avviata o si effettua il failover e tutte le modifiche apportate in `tempdb` non verranno mantenute.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

Le istruzioni `CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` potrebbero avere esito negativo perché l'istanza può raggiungere il limite di Archiviazione di Azure.

Ogni istanza gestita per utilizzo generico ha fino a 35 TB di spazio di archiviazione riservati per lo spazio su disco Premium di Azure. Ogni file di database si trova in un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato sul disco non viene conteggiato, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni delle risorse di archiviazione a causa della frammentazione interna.

Ad esempio, un'istanza gestita di per utilizzo generico potrebbe avere un file di grandi dimensioni da 1,2 TB in un disco da 4 TB. Potrebbero inoltre essere presenti 248 file da 1 GB ognuno, divisi in dischi da 128 GB separati. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio dimostra che, in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza gestita può raggiungere il limite di 35 TB riservato a un disco Premium di Azure allegato anche quando non previsto.

In questo esempio, i database esistenti continuano a funzionare e possono crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. Non è possibile creare o ripristinare nuovi database a causa dello spazio insufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite di dimensioni dell'istanza. L'errore restituito in questo caso potrebbe non essere chiaro.

È possibile [identificare il numero di file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando le viste di sistema. Se si raggiunge questo limite, provare a [svuotare ed eliminare alcuni dei file più piccoli usando l'istruzione DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello di servizio business critical, che non ha questo limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valori GUID visualizzati al posto dei nomi di database

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID, in quanto vengono sostituiti dai nomi effettivi dei database in futuro.

**Soluzione alternativa**: usare la vista sys.databases per risolvere il nome del database effettivo dal nome del database fisico, indicato nel modulo degli identificatori del database GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>I log degli errori non sono permanenti

I log degli errori disponibili in Istanza gestita non sono permanenti e le relative dimensioni non sono incluse nel limite di archiviazione massimo. I log degli errori potrebbero essere cancellati automaticamente se si esegue il failover. Potrebbero esserci gap nella cronologia dei log degli errori perché Istanza gestita è stata spostata più volte su diverse macchine virtuali.

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

**Soluzione alternativa (non necessaria da marzo 2020):** usare [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per usare un altro database in un contesto della connessione invece di due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale

I moduli CLR inseriti in un'istanza gestita e i server collegati o le query distribuite che fanno riferimento all'istanza corrente talvolta non riescono a risolvere l'indirizzo IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa:** usare connessioni di contesto in un modulo CLR, se possibile.

## <a name="updates"></a>Aggiornamenti

Per un elenco di aggiornamenti e miglioramenti del database SQL, vedere [Aggiornamenti del servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per gli aggiornamenti e i miglioramenti apportati a tutti i servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuire al contenuto

Per contribuire alla documentazione del database SQL di Azure, vedere la [Guida per i collaboratori di Docs](https://docs.microsoft.com/contribute/).
