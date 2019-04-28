---
title: Differenze T-SQL di istanza gestita di Database SQL di Azure | Microsoft Docs
description: Questo articolo illustra le differenze T-SQL tra un'istanza gestita di database SQL di Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5f476aa571ba2827cbe6f4e4f258545b5e9d3ba1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106366"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server

Questo articolo vengono riepilogati e vengono illustrate le differenze nella sintassi e il comportamento tra istanza gestita di Azure SQL Database e motore di Database locale SQL Server. <a name="Differences"></a>

- [Disponibilità](#availability) incluse le differenze relative a [Always On](#always-on-availability) e [Backup](#backup),
- [Sicurezza](#security) incluse le differenze relative a [Controllo](#auditing), [Certificati](#certificates), [Credenziali](#credential), [Provider del servizio di crittografia](#cryptographic-providers), [Accessi/utenti](#logins--users), [Chiave del servizio e chiave master del servizio](#service-key-and-service-master-key),
- [Configurazione](#configuration) incluse le differenze relative a [Estensione del pool di buffer](#buffer-pool-extension), [Regole di confronto](#collation), [Livelli di compatibilità](#compatibility-levels), [Mirroring del database](#database-mirroring), [Opzioni di database](#database-options), [SQL Server Agent](#sql-server-agent), [Opzioni tabella](#tables),
- [Funzionalità](#functionalities) tra cui [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [Transazioni distribuite](#distributed-transactions), [Eventi estesi](#extended-events), [Librerie esterne](#external-libraries), [FileStream e FileTable](#filestream-and-filetable), [Ricerca semantica full-text](#full-text-semantic-search), [Server collegati](#linked-servers), [PolyBase](#polybase), [Replica](#replication), [RIPRISTINO](#restore-statement), [Service Broker](#service-broker), [Stored procedure, funzioni e trigger](#stored-procedures-functions-triggers),
- [Funzionalità con un comportamento diverso nelle istanze gestite](#Changes)
- [Limitazioni temporanee e problemi noti](#Issues)

L'opzione di distribuzione Istanza gestita assicura una compatibilità elevata con il motore di database di SQL Server locale. La maggior parte delle funzionalità del motore di database di SQL Server è supportata in un'istanza gestita.

![migrazione](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilità

### <a name="always-on-availability"></a>Always On

La [disponibilità elevata](sql-database-high-availability.md) è incorporata in Istanza gestita e non può essere controllata dagli utenti. Le istruzioni seguenti non sono supportate:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Clausola [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) dell'istruzione [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Le istanze gestite hanno backup automatici e consentono agli utenti di creare backup `COPY_ONLY` di database completi. I backup differenziali, di log e dello snapshot dei file non sono supportati.

- Con un'istanza gestita è possibile eseguire il backup di un database dell'istanza solo in un account di Archiviazione BLOB di Azure:
  - È supportato solo `BACKUP TO URL`
  - I dispositivi di backup, `FILE` e `TAPE` non sono supportati  
- La maggior parte delle opzioni `WITH` generali è supportata
  - `COPY_ONLY` è obbligatorio
  - `FILE_SNAPSHOT` non è supportato
  - Le opzioni nastro `REWIND`, `NOREWIND`, `UNLOAD` e `NOUNLOAD` non sono supportate
  - Le opzioni specifiche dei log `NORECOVERY`, `STANDBY` e `NO_TRUNCATE` non sono supportate

 Limitazioni:  

- Con un'istanza gestita è possibile eseguire il backup di un database dell'istanza in un supporto di backup con un massimo di 32 set di stripe, sufficienti per database fino a 4 TB se si usa la compressione del backup.
- Dimensioni di striping backup max usando il `BACKUP` comando in un'istanza gestita corrisponde a 195 GB (dimensione massima dei blob). Aumentare il numero di set di stripe nel comando backup per ridurre le dimensioni dei singoli set di stripe e restare nel limite consentito.

    > [!TIP]
    > Per aggirare questa limitazione durante il backup di un database da SQL Server in un ambiente locale o in una macchina virtuale, è possibile eseguire le operazioni seguenti:
    >
    > - Eseguire il backup del `DISK` invece di backup `URL`
    > - Caricare i file di backup nell'archiviazione Blob
    > - Eseguire il ripristino in istanza gestita
    >
    > Il `Restore` comando in un'istanza gestita supporta le dimensioni del blob più grande nei file di backup poiché viene utilizzato un tipo di blob diverso per l'archiviazione dei file di backup caricati.

Per informazioni sui backup con T-SQL, vedere [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Security

### <a name="auditing"></a>Controllo

Le principali differenze tra il controllo nei database nel database SQL di Azure e quello in SQL Server sono le seguenti:

- Con l'opzione di distribuzione Istanza gestita nel database SQL di Azure, il controllo viene eseguito a livello del server e archivia i file di log `.xel` in Archiviazione BLOB di Azure.
- Con le opzioni di distribuzione dei database singoli e dei pool elastici nel database SQL di Azure, il controllo viene eseguito a livello del database.
- In SQL Server locale o nelle macchine virtuali SQL Server il controllo viene eseguito a livello del server, ma archivia gli eventi nei log eventi del file system o di Windows.
  
Il controllo XEvent in Istanza gestita supporta le destinazioni di Archiviazione BLOB di Azure. I log di file e di Windows non sono supportati.

Di seguito sono illustrate le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archiviazione BLOB di Azure:

- È disponibile una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui verranno inseriti i file di `.xel`.
- La sintassi `TO FILE` non è supportata perché un'istanza gestita non può accedere alle condivisioni file di Windows.

Per altre informazioni, vedere:  

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificati

Un'istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows e pertanto vengono applicati i vincoli seguenti:

- La sintassi `CREATE FROM`/`BACKUP TO` relativa ai file non è supportata per i certificati.
- La sintassi `CREATE`/`BACKUP` da `FILE`/`ASSEMBLY` per i certificati non è supportata. I file di chiavi private non possono essere usati.  

Vedere [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Soluzione alternativa**: creare uno script per il certificato/chiave privata, archiviare come file con estensione sql e creare il certificato dal file binario:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credenziali

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Un'istanza gestita non può accedere ai file e pertanto non è possibile creare provider del servizio di crittografia:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Account di accesso/utenti

- Gli account di accesso SQL creati `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` e `FROM SID` sono supportati. Vedere [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Le entità server (account di accesso) di Azure Active Directory (Azure AD) create con la sintassi [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) o con la sintassi [CREATE USER FROM LOGIN [account di accesso Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sono supportate (**anteprima pubblica**). Si tratta di account di accesso creati a livello di server.

    Istanza gestita supporta le entità di database di Azure AD con la sintassi `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Queste entità sono note anche come utenti di database indipendenti di Azure AD.

- Gli account di accesso di Windows creati con la sintassi `CREATE LOGIN ... FROM WINDOWS` non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- L'utente di Azure AD che ha creato l'istanza ha [privilegi amministrativi illimitati](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Gli utenti non amministratori di Azure Active Directory (Azure AD) a livello di database possono essere creati con la sintassi `CREATE USER ... FROM EXTERNAL PROVIDER`. Vedere [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Le entità server (account di accesso) di Azure AD supportano le funzionalità di SQL all'interno di una sola istanza gestita. Le funzionalità che richiedono l'interazione tra istanze, all'interno dello stesso tenant di Azure AD o in un tenant diverso, non sono supportate per gli utenti di Azure AD. Tra queste funzionalità sono incluse:

  - Replica transazionale SQL e
  - Server di collegamento

- L'impostazione di un account di accesso di Azure AD mappato a un gruppo di Azure AD come proprietario del database non è supportata.
- Le rappresentazione delle entità di livello server di Azure AD con altre entità di Azure AD è supportata, ad esempio la clausola [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). Limitazione di EXECUTE AS:

  - EXECUTE AS USER non è supportata per gli utenti di Azure AD quando il nome è diverso dal nome di accesso. Ad esempio, quando l'utente è stato creato tramite la sintassi CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] e si prova a eseguire la rappresentazione tramite EXEC AS USER = _myAadUser_. Quando si crea un utente **USER** da un'entità server (account di accesso) di Azure AD, specificare per user_name lo stesso valore di login_name indicato in **LOGIN**.
  - Solo le entità di livello server SQL (account di accesso) che fanno parte del ruolo `sysadmin` possono eseguire le operazioni seguenti destinate a entità di sicurezza di Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitazioni dell'**anteprima pubblica** per le entità server (account di accesso) di Azure AD:

  - Limitazioni dell'amministratore di Active Directory per Istanza gestita:

    - L'amministratore di Azure AD usato per configurare l'istanza gestita non può essere usato per creare un'entità server (account di accesso) di Azure AD all'interno dell'istanza gestita. È necessario creare la prima entità server (account di accesso) di Azure AD usando un account di SQL Server che è un `sysadmin`. Si tratta di una limitazione temporanea che verrà rimossa quando le entità server (account di accesso) di Azure AD saranno disponibili a livello generale. Se si prova a usare un account amministratore di Azure AD per creare l'account di accesso, viene visualizzato l'errore seguente: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Attualmente, il primo account di accesso di Azure AD nel database master deve essere creato dall'account di SQL Server standard (non Azure AD) che è un `sysadmin` usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. Non appena verrà rilasciata la versione disponibile a livello generale, questa limitazione verrà rimossa e l'account di accesso iniziale di Azure AD potrà essere creato dall'amministratore di Active Directory per Istanza gestita.
    - Il framework DacFx (esportazione/importazione) usato con SQL Server Management Studio (SSMS) o SqlPackage non è supportato per gli account di accesso di Azure AD. Questa limitazione verrà rimossa quando le entità server (account di accesso) di Azure AD saranno disponibili a livello generale.
    - Uso di entità server (account di accesso) di Azure AD con SSMS

      - La creazione di script per gli account di accesso di Azure AD (con un qualsiasi account di accesso autenticato) non è supportata.
      - IntelliSense non riconosce l'istruzione **CREATE LOGIN FROM EXTERNAL PROVIDER** e mostrerà una sottolineatura rossa.

- Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning di Istanza gestita), i membri dei ruoli server (`securityadmin` o `sysadmin`) o altri account di accesso con autorizzazione ALTER ANY LOGIN a livello di server possono creare entità server (account di accesso) di Azure AD nel database master per Istanza gestita.
- Se l'account di accesso è un'entità di sicurezza SQL, solo gli account di accesso che fanno parte del ruolo `sysadmin` possono usare il comando per creare gli account di accesso per un account Azure AD.
- L'account di accesso di Azure AD deve essere un membro di un'istanza di Azure AD all'interno della stessa directory usata per Istanza gestita di database SQL di Azure.
- Le entità server (account di accesso) di Azure AD sono visibili in Esplora oggetti a partire da SSMS 18.0, anteprima 5.
- È consentita la sovrapposizione di entità server (account di accesso) di Azure AD con un account amministratore di Azure AD. Le entità server (account di accesso) di Azure AD hanno la precedenza sull'amministratore di Azure AD quando viene risolta l'entità e vengono applicate le autorizzazioni all'istanza gestita.
- Durante l'autenticazione, per risolvere l'entità che esegue l'autenticazione viene applicata la sequenza seguente:

    1. Se l'account di Azure AD è mappato direttamente all'entità server (account di accesso) di Azure AD (presente in sys.server_principals come tipo "E"), concedere l'accesso e applicare le autorizzazioni dell'entità del server (account di accesso) di Azure AD.
    2. Se l'account di Azure AD è membro di un gruppo di Azure AD mappato all'entità server (account di accesso) di Azure AD (presente in sys.server_principals come tipo "X"), concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    3. Se l'account di Azure AD è uno specifico amministratore di Azure AD configurato nel portale per Istanza gestita (non è presente nelle viste di sistema di Istanza gestita), applicare le speciali autorizzazioni dell'amministratore di Azure AD per Istanza gestita (modalità legacy).
    4. Se l'account di Azure AD è mappato direttamente a un utente di Azure AD in un database (presente in sys.database_principals come tipo "E"), concedere l'accesso e applicare le autorizzazioni dell'utente del database di Azure AD.
    5. Se l'account di Azure AD è membro di un gruppo di Azure AD mappato a un utente di Azure AD in un database (presente in sys.database_principals come tipo "X"), concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    6. Se è presente un account di accesso di Azure AD mappato a un account utente di Azure AD o a un account di gruppo di Azure AD, quando viene risolto l'utente che esegue l'autenticazione, vengono applicate tutte le autorizzazioni di questo account di accesso di Azure AD.

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- Il [backup della chiave master](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [ripristino della chiave master](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [backup della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [ripristino della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)

## <a name="configuration"></a>Configurazione

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- L'[estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) non è supportata.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportata. Vedere [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- I livelli di compatibilità supportati sono: 100, 110, 120, 130, 140  
- I livelli di compatibilità inferiori a 100 non sono supportati.
- Il livello di compatibilità predefinito per i nuovi database è 140. Per i database ripristinati, il livello di compatibilità rimane invariato se era uguale o superiore a 100.

Vedere [Livello di compatibilità ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mirroring del database

Il mirroring del database non è supportato.

- Le opzioni `ALTER DATABASE SET PARTNER` e `SET WITNESS` non sono supportate.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` non è supportata.

Per altre informazioni, vedere [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opzioni di database

- I file di log multipli non sono supportati.
- Gli oggetti in memoria non sono supportati nel livello di servizio per utilizzo generico.  
- È previsto un limite di 280 file per ogni istanza di uso generale che implichi massimo di 280 file per ogni database. In generale i dati e log file scopo livello vengono conteggiati per il limite. [Livello Business Critical supporta 32.767 file per ogni database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- I database non possono contenere filegroup che contengono a loro volta dati FileStream.  Se il file con estensione bak contiene dati `FILESTREAM`, il ripristino non riesce.  
- Ogni file viene inserito in Archiviazione BLOB di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file.  

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le limitazioni di `CREATE DATABASE` sono le seguenti:

- Non possono essere definiti file e filegroup.  
- L'opzione `CONTAINMENT` non è supportata.  
- Le opzioni `WITH` non sono supportate.  
   > [!TIP]
   > Come soluzione alternativa, usare `ALTER DATABASE` dopo `CREATE DATABASE` per impostare le opzioni di database per l'aggiunta di file o l'impostazione del contenimento.  

- L'opzione `FOR ATTACH` non è supportata.
- L'opzione `AS SNAPSHOT OF` non è supportata.

Per altre informazioni, vedere [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Istruzione ALTER DATABASE

Alcune proprietà di file non possono essere impostate o modificate:

- Il percorso file non può essere specificato nell'istruzione T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Rimuovere `FILENAME` dallo script perché un'istanza gestita inserisce i file automaticamente.  
- Il nome file non può essere modificato tramite l'istruzione `ALTER DATABASE`.

Le opzioni seguenti sono attive per impostazione predefinita e non possono essere modificate:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Le opzioni seguenti non possono essere modificate:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Per altre informazioni, vedere [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Agente SQL Server

- Le impostazioni dell'agente SQL sono di sola lettura. La routine `sp_set_agent_properties` non è supportata in Istanza gestita.  
- Processi
  - I passaggi dei processi T-SQL sono supportati.
  - I processi di replica seguenti sono supportati:
    - Lettore di log delle transazioni
    - Snapshot
    - Database di distribuzione
  - I passaggi dei processi SSIS sono supportati.
  - Altri tipi di passaggi dei processi non sono attualmente supportati, tra cui:
    - Il passaggio del processo di replica di tipo merge non è supportato.  
    - La lettura della coda non è supportata.  
    - La shell dei comandi non è ancora supportata.
  - Le istanze gestite non possono accedere a risorse esterne, ad esempio a condivisioni di rete tramite robocopy.  
  - Analysis Services non è supportato.
- Le notifiche sono supportate in modo parziale.
- La notifica tramite posta elettronica è supportata e richiede la configurazione di un profilo di posta elettronica database. SQL Agent può utilizzare il profilo di posta elettronica di un solo database, che deve essere denominato `AzureManagedInstance_dbmail_profile`.  
  - Pager non è supportato.  
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.  
- EventLog non è supportato.

Le funzionalità seguenti non sono attualmente supportate ma saranno abilitate in futuro:

- Proxy
- Pianificazione di processi su una CPU inattiva
- Abilitazione/disabilitazione dell'agente
- Avvisi

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

Le opzioni seguenti non sono supportate:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Per informazioni sulla creazione e la modifica di tabelle, vedere [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funzionalità

### <a name="bulk-insert--openrowset"></a>Inserimento bulk/openrowset

Un'istanza gestita non può accedere a condivisioni file e cartelle di Windows e pertanto i file devono essere importati da Archiviazione BLOB di Azure:

- `DATASOURCE` è obbligatorio nel comando `BULK INSERT` durante l'importazione dei file da Archiviazione BLOB di Azure. Vedere [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` è obbligatorio nella funzione `OPENROWSET` quando si legge il contenuto di un file da Archiviazione BLOB di Azure. Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Un'istanza gestita non può accedere a condivisioni file e cartelle di Windows e pertanto vengono applicati i vincoli seguenti:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` non è supportata. Vedere [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Le istruzioni DBCC non documentate abilitate in SQL Server non sono supportate nelle istanze gestite.

- I `Trace Flags` non sono supportati. Vedere [Flag di traccia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` non è supportata. Vedere [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` non è supportata. Vedere [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transazioni distribuite

Né MSDTC né le [transazioni elastiche](sql-database-elastic-transactions-overview.md) sono attualmente supportate nelle istanze gestite.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per XEvents non sono supportate:

- `etw_classic_sync target` non è supportata. Archiviare i file `.xel` nell'archivio BLOB di Azure. Vedere [Destinazione etw_classic_sync_target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` non è supportata. Archiviare i file `.xel` nell'archivio BLOB di Azure. Vedere [Destinazione event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Librerie esterne

Le librerie esterne In-database R e Python non sono ancora supportate. Vedere [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- I dati FileStream non sono supportati.
- Il database non può contenere filegroup con dati `FILESTREAM`.
- `FILETABLE` non è supportata.
- Le tabelle non possono contenere tipi `FILESTREAM`.
- Le funzioni seguenti non sono supportate:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Per altre informazioni, vedere [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) e [FileTable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Ricerca semantica full-text

La [ricerca semantica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) non è supportata.

### <a name="linked-servers"></a>Server collegati

I server collegati nelle istanze gestite supportano un numero limitato di destinazioni:

- Destinazioni supportate: SQL Server e Database SQL
- Destinazioni non supportate: file, Analysis Services e altri sistemi di gestione di database relazionali (RDBMS).

Operazioni

- Le transazioni di scrittura tra istanze non sono supportate.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La funzione `OPENROWSET` può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La funzione `OPENDATASOURCE` può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Sono supportati come provider solo i valori `SQLNCLI`, `SQLNCLI11` e `SQLOLEDB`. Ad esempio: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Le tabelle esterne che fanno riferimento ai file in Hadoop Distributed File System o in Archiviazione BLOB di Azure non sono supportate. Per informazioni su Polybase, vedere [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

Per l'anteprima pubblica di Istanza gestita è disponibile la replica. Per informazioni sulla replica, vedere [Replica di SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Istruzione RESTORE

- Sintassi supportata
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintassi non supportata
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Source (Sorgente)  
  - `FROM URL` (Archiviazione BLOB di Azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportata.
  - I set di backup non sono supportati.
- Le opzioni `WITH` (`DIFFERENTIAL`, `STATS` e così via) non sono supportate.
- `ASYNC RESTORE` - il ripristino continua anche se la connessione del client si interrompe. Se la connessione viene interrotta, è possibile controllare nella vista `sys.dm_operation_status` lo stato di un'operazione di ripristino (oltre che di CREATE e DROP DATABASE). Vedere [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Le opzioni di database seguenti sono impostate/sottoposte a override e non possono essere modificate in seguito:  

- `NEW_BROKER` (se il broker non è abilitato nel file con estensione bak)  
- `ENABLE_BROKER` (se il broker non è abilitato nel file con estensione bak)  
- `AUTO_CLOSE=OFF` (se un database nel file con estensione bak ha l'opzione `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (se un database nel file con estensione bak è in modalità di ripristino `SIMPLE` o `BULK_LOGGED`)
- Un filegroup ottimizzato per la memoria viene aggiunto e denominato XTP se non era incluso nel file con estensione bak di origine  
- Qualsiasi filegroup ottimizzato per la memoria esistente viene rinominato in XTP  
- Le opzioni `SINGLE_USER` e `RESTRICTED_USER` vengono convertite in `MULTI_USER`

 Limitazioni:  

- I file `.BAK` che contengono più set di backup non possono essere ripristinati.
- I file `.BAK` che contengono più file di log non possono essere ripristinati.
- Se il file con estensione bak contiene dati `FILESTREAM`, il ripristino non riesce.
- Impossibile ripristinare i backup contenenti database con oggetti In memoria attivi nell'istanza di uso generale.  
Per informazioni sulle istruzioni Restore, vedere [Istruzioni RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Broker di servizio

Il broker di servizio tra istanze non è supportato:

- `sys.routes` - prerequisito: selezionare un indirizzo da sys.routes. L'indirizzo deve essere LOCALE in ogni route. Vedere [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` - non è possibile usare `CREATE ROUTE` con `ADDRESS` diverso da `LOCAL`. Vedere [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` - non è possibile eseguire `ALTER ROUTE` con `ADDRESS` diverso da `LOCAL`. Vedere [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Stored procedure, funzioni, trigger

- `NATIVE_COMPILATION` non è supportato nel livello utilizzo generico.
- Le opzioni [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` non è supportata. Vedere [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportata. Vedere [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- Le `Extended stored procedures` non sono supportate, incluse `sp_addextendedproc` e `sp_dropextendedproc`. Vedere [Stored procedure estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportate. Vedere [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Modifiche nel comportamento

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')` restituisce il valore 8. Questa proprietà identifica in maniera univoca un'istanza gestita. Vedere [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` restituisce NULL perché il concetto di istanza valido per SQL Server non si applica a un'istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` restituisce il nome DNS completo "collegabile", ad esempio my-managed-instance.wcus17662feb9ce98.database.windows.net. Vedere [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` restituisce il nome DNS completo "collegabile", ad esempio `myinstance.domain.database.windows.net` per le proprietà 'name' e 'data_source'. Vedere [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` restituisce NULL perché il concetto di servizio valido per SQL Server non si applica a un'istanza gestita. Vedere [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'account di accesso di Azure AD non è presente in sys.syslogins. Vedere [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` non è supportata. Restituisce dati errati (problema noto temporaneo). Vedere [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).

## <a name="Issues"></a> Problemi noti e limitazioni

### <a name="tempdb-size"></a>Dimensioni di TEMPDB

Dimensione file massima di `tempdb` non può essere maggiore di 24 GB/core nel livello utilizzo generico. Max `tempdb` dimensione nel livello Business Critical è limitata con le dimensioni di archiviazione di istanza. `tempdb` vengono sempre suddivisi in file di 12 dati. Queste dimensioni massime per file non possono essere modificate ed è possibile aggiungere nuovi file a `tempdb`. Alcune query potrebbero restituire un errore se necessitano più di 24GB / core in `tempdb`.

### <a name="cannot-restore-contained-database"></a>Non è possibile ripristinare i database indipendenti

Non è possibile ripristinare l'istanza gestita [database indipendenti](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Point-in-time ripristinare i database indipendenti esistenti non funzionano in istanza gestita. Questo problema verrà rimossa a breve e nel frattempo è consigliabile rimuovere l'opzione di indipendenza dai database che vengono inseriti in istanza gestita e non utilizzano l'opzione di contenimento per i database di produzione.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, e `RESTORE DATABASE` istruzioni potrebbero non riuscire perché l'istanza può raggiungere il limite di archiviazione di Azure.

Ogni istanza gestita di generale scopo dispone archiviazione di 35 TB riservata per lo spazio su disco Premium di Azure e ogni file di database viene posizionato su un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato su disco non viene conteggiato, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni delle risorse di archiviazione, a causa della frammentazione interna.

Ad esempio, un'istanza gestita scopo generale può avere uno file 1,2 TB di dimensioni contenute conservata sul disco da 4 TB e 248 file (ogni 1 GB di dimensioni) che vengono inseriti in dischi separati da 128 GB. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ciò dimostra che, in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza gestita può raggiungere i 35 TB riservati ad Azure Premium Disk allegato anche quando non previsto.

In questo esempio, i database esistenti continueranno a funzionare e potranno crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. È possibile tuttavia che non vengano creati o ripristinati nuovi database a causa dello spazio insufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite di dimensioni dell'istanza. L'errore restituito in questo caso potrebbe non essere chiaro.

È possibile [identificare il numero dei file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizzando viste di sistema. Se sta per essere raggiunto questo limite tenta [vuoti e l'eliminazione di alcuni dei file più piccoli utilizzando l'istruzione DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare [livello Business Critical che non hanno questo limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configurazione non corretta della chiave di firma di accesso condiviso durante il ripristino del database

L'istruzione `RESTORE DATABASE` che legge il file con estensione bak potrebbe cercare continuamente di leggere il file e restituire un errore dopo un lungo periodo di tempo se la firma di accesso condiviso in `CREDENTIAL` non è corretta. Eseguire RESTORE HEADERONLY prima di ripristinare un database per avere la certezza che la chiave di firma di accesso condiviso sia corretta.
Assicurarsi di rimuovere il carattere `?` iniziale dalla chiave SAS generata con il portale di Azure.

### <a name="tooling"></a>Strumenti

SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) potrebbero riscontrare problemi durante l'accesso a un'istanza gestita.

- L'uso di entità server (account di accesso) e di utenti di Azure AD (**anteprima pubblica**) con SSDT non è attualmente supportato.
- La creazione di script per le entità server (account di accesso) e gli utenti di Azure AD (**anteprima pubblica**) non è supportata in SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomi di database errati in alcune viste, log e messaggi

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID, in quanto potrebbero essere sostituiti dai nomi effettivi dei database in futuro.

### <a name="database-mail"></a>Posta elettronica database

`@query` nel parametro [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedura non funziona.

### <a name="database-mail-profile"></a>Profilo di posta elettronica database

Il profilo di posta elettronica database utilizzato da SQL Agent deve essere chiamato `AzureManagedInstance_dbmail_profile`. Sono non disponibili restrizioni riguardanti altri nomi di profilo di posta elettronica database.

### <a name="error-logs-are-not-persisted"></a>I log degli errori non sono persistenti

I log degli errori che sono disponibili in Istanza gestita non sono persistenti e le relative dimensioni non sono incluse nel limite di archiviazione massimo. I log degli errori potrebbero essere cancellati automaticamente in caso di failover.

### <a name="error-logs-are-verbose"></a>I log degli errori sono di tipo dettagliato

Un'istanza gestita inserisce informazioni dettagliate nei log degli errori e molte di esse non sono pertinenti. La quantità di informazioni nei log degli errori verrà ridotta in futuro.

**Soluzione alternativa**: usare una procedura personalizzata per la lettura dei log degli errori che filtrano alcune voci non pertinenti. Per informazioni dettagliate, vedere [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) (Istanza gestita: sp_readmierrorlog).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L'ambito della transazione in due database nella stessa istanza non è supportato

`TransactionScope` classe in .NET non funziona se le due query vengono inviate per i due database nella stessa istanza nello stesso ambito di transazione:

```C#
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

Sebbene questo codice funzioni con i dati nella stessa istanza, è necessario MSDTC.

**Soluzione alternativa**: usare [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per usare un altro database nel contesto della connessione invece di usare due connessioni.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>I moduli CLR e i server collegati talvolta non riescono a fare riferimento all'indirizzo IP locale

I moduli CLR inseriti in un'istanza gestita e i server collegati o le query distribuite che fanno riferimento all'istanza corrente talvolta non riescono a risolvere l'indirizzo IP dell'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa**: usare connessioni di contesto nel modulo CLR, se possibile.

### <a name="tde-encrypted-databases-with-service-managed-key-dont-support-user-initiated-backups"></a>Database di crittografia TDE con chiavi gestite dal servizio non supportano backup avviati dall'utente

Non è possibile eseguire `BACKUP DATABASE ... WITH COPY_ONLY` in un database crittografato con gestite dal servizio Transparent Data Encryption (TDE). TDE gestita dal servizio e impone i backup deve essere crittografato con chiave TDE interna e la chiave non può essere esportata, in modo non sarà possibile ripristinare il backup.

**Soluzione alternativa**: Usare i backup automatici e ripristino temporizzato in oppure usare [gestite dal cliente (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) invece o disabilitare la crittografia nel database.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle istanze gestite, vedere [Informazioni sull'istanza gestita](sql-database-managed-instance.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per una guida introduttiva che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
