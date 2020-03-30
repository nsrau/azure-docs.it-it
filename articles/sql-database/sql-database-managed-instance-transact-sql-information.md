---
title: Differenze T-SQL dell'istanza gestita
description: Questo articolo illustra le differenze T-SQL tra un'istanza gestita in database SQL di Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365495"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Differenze e limitazioni t-SQL dell'istanza gestita

Questo articolo riepiloga e illustra le differenze nella sintassi e nel comportamento tra l'istanza gestita del database SQL di Azure e il motore di database di SQL Server locale. L'opzione di distribuzione dell'istanza gestita assicura una compatibilità elevata con il motore di database di SQL Server locale. La maggior parte delle funzionalità del motore di database di SQL Server è supportata in un'istanza gestita.

![Migrazione](./media/sql-database-managed-instance/migration.png)

Esistono alcune limitazioni PaaS che vengono introdotte nell'istanza gestita e alcune modifiche di comportamento rispetto a SQL Server.There are some PaaS limitations that are introduced in Managed Instance and some behavior changes compared to SQL Server. Le differenze sono suddivise nelle seguenti categorie:<a name="Differences"></a>

- [La disponibilità](#availability) include le differenze nei backup e nei [gruppi](#backup)di [disponibilità AlwaysOnAlways On Availability Groups](#always-on-availability-groups) .
- [La sicurezza](#security) include le differenze tra [controllo,](#auditing) [certificati,](#certificates) [credenziali,](#credential) [provider di crittografia,](#cryptographic-providers)account di [accesso e utenti](#logins-and-users)e la chiave di servizio e la chiave master [del servizio](#service-key-and-service-master-key).
- [La configurazione](#configuration) include le differenze tra le opzioni di [buffer pool, collation,](#buffer-pool-extension) [livelli di compatibilità,](#compatibility-levels) [mirroring del database,](#database-mirroring) [opzioni del database,](#database-options)Agente SQL [Server](#sql-server-agent)e [opzioni di tabella](#tables). [collation](#collation)
- [Le funzionalità](#functionalities) includono [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), CLR , [DBCC](#clr), [transazioni distribuite](#distributed-transactions), eventi [estesi](#extended-events), [librerie esterne](#external-libraries), [filestream e FileTable](#filestream-and-filetable), [ricerca semantica full-text](#full-text-semantic-search), [server collegati](#linked-servers), [PolyBase](#polybase), [replica](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [stored procedure, funzioni e trigger](#stored-procedures-functions-and-triggers). [DBCC](#dbcc)
- [Impostazioni di ambiente,](#Environment) ad esempio reti virtuali e configurazioni di subnet.

La maggior parte di queste funzionalità sono vincoli architetturali e rappresentano le funzionalità del servizio.

I problemi noti temporanei rilevati nell'istanza gestita e che verranno risolti in futuro sono descritti nella [pagina delle note sulla versione.](sql-database-release-notes.md)

## <a name="availability"></a>Disponibilità

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Gruppi di disponibilità AlwaysOn

[La disponibilità elevata](sql-database-high-availability.md) è incorporata nell'istanza gestita e non può essere controllata dagli utenti. Le istruzioni seguenti non sono supportate:The following statements aren't supported:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREARE UN GRUPPO DI DISPONIBILITÀCREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [MODIFICARE IL GRUPPO DI DISPONIBILITÀ](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- La clausola [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) dell'istruzione [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Le istanze gestite dispongono di `COPY_ONLY` backup automatici, in modo che gli utenti possano creare backup completi del database. I backup differenziali, di log e di snapshot dei file non sono supportati.

- Con un'istanza gestita è possibile eseguire il backup di un database dell'istanza solo in un account di archiviazione BLOB di Azure:With a managed instance, you can back up an instance database only to an Azure Blob storage account:
  - È supportato solo `BACKUP TO URL`.
  - `FILE`, `TAPE`e i dispositivi di backup non sono supportati.
- La maggior `WITH` parte delle opzioni generali sono supportate.
  - `COPY_ONLY`è obbligatorio.
  - `FILE_SNAPSHOT` non è supportata.
  - Opzioni `REWIND`nastro: `NOREWIND` `UNLOAD`, `NOUNLOAD` , e non sono supportate.
  - Opzioni specifiche del `NORECOVERY` `STANDBY`log: `NO_TRUNCATE` , e non sono supportate.

 Limitazioni: 

- Con un'istanza gestita, è possibile eseguire il backup di un database dell'istanza in un backup con un massimo di 32 stripe, che è sufficiente per i database fino a 4 TB se viene utilizzata la compressione del backup.
- Non è possibile `BACKUP DATABASE ... WITH COPY_ONLY` eseguire in un database crittografato con Crittografia dati trasparente (TDE( Transparent Data Encryption) gestita dal servizio. TDE gestito dal servizio impone la crittografia dei backup con una chiave TDE interna. La chiave non può essere esportata, quindi non è possibile ripristinare il backup. Utilizzare i backup automatici e il ripristino temporizzato oppure utilizzare [TDE gestito dal cliente (BYOK).](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) È inoltre possibile disabilitare la crittografia nel database.
- La dimensione massima dello stripe di backup tramite il `BACKUP` comando in un'istanza gestita è 195 GB, ovvero la dimensione massima del BLOB. Aumentare il numero di strisce nel comando di backup per ridurre la dimensione delle singole strisce e rimanere entro questo limite.

    > [!TIP]
    > Per aggirare questa limitazione, quando si esegue il backup di un database da SQL Server in un ambiente locale o in una macchina virtuale, è possibile:To work around this limitation, when you back up a database from either SQL Server in an on-premises environment or in a virtual machine, you can:
    >
    > - Eseguire il `DISK` backup su anziché eseguire il backup su `URL`.
    > - Caricare i file di backup nell'archivio BLOB.
    > - Eseguire il ripristino nell'istanza gestita.
    >
    > Il `Restore` comando in un'istanza gestita supporta dimensioni di BLOB più grandi nei file di backup perché viene usato un tipo di BLOB diverso per l'archiviazione dei file di backup caricati.

Per informazioni sui backup con T-SQL, vedere [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Security

### <a name="auditing"></a>Controllo

Le principali differenze tra il controllo nei database nel database SQL di Azure e quello in SQL Server sono le seguenti:

- Con l'opzione di distribuzione dell'istanza gestita nel database SQL di Azure, il controllo funziona a livello di server. I `.xel` file di log vengono archiviati nell'archivio BLOB di Azure.The log files are stored in Azure Blob storage.
- Con le opzioni di distribuzione dei database singoli e dei pool elastici nel database SQL di Azure, il controllo viene eseguito a livello del database.
- Nelle macchine virtuali o locali di SQL Server, il controllo funziona a livello di server. Gli eventi vengono archiviati nel file system o nei registri eventi di Windows.Events are stored on file system or Windows event logs.
 
Il controllo XEvent nell'istanza gestita supporta le destinazioni di Archivio BLOB di Azure. I registri di file e Windows non sono supportati.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- Viene fornita `TO URL` una nuova sintassi che è possibile usare per `.xel` specificare l'URL del contenitore di archiviazione BLOB di Azure in cui sono inseriti i file.
- La `TO FILE` sintassi non è supportata perché un'istanza gestita non può accedere alle condivisioni file di Windows.The syntax isn't supported because a managed instance can't access Windows file shares.

Per altre informazioni, vedere: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificati

Un'istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, pertanto si applicano i vincoli seguenti:A managed instance can't access file shares and Windows folders, so the following constraints apply:

- `CREATE FROM` / Il `BACKUP TO` file non è supportato per i certificati.
- Il `CREATE` / / `ASSEMBLY` certificato `FILE` da non è supportato. `BACKUP` I file di chiavi private non possono essere usati. 

Vedere [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Soluzione alternativa:** anziché creare il backup del certificato e ripristinare il backup, [ottenere il contenuto binario del certificato e la chiave privata, archiviarlo come file con estensione sql e creare da binario](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credenziale

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Un'istanza gestita non può accedere ai file, pertanto non è possibile creare provider di crittografia:A managed instance can't access files, so cryptographic providers can't be created:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Account di accesso e utenti

- Gli account di `FROM CERTIFICATE`accesso `FROM ASYMMETRIC KEY`SQL `FROM SID` creati utilizzando , e sono supportati. Vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Sono supportate le entità server (account di accesso) di Azure Active Directory (Azure AD) create con la sintassi [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) o [CREATE USER FROM LOGIN [Azure AD Login].](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) Questi account di accesso vengono creati a livello di server.

    L'istanza gestita supporta le entità `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`di database di Azure AD con la sintassi . Questa funzionalità è nota anche come utenti di database indipendente di Azure AD.

- Gli account di `CREATE LOGIN ... FROM WINDOWS` accesso di Windows creati con la sintassi non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- L'utente di Azure AD che ha creato l'istanza dispone di privilegi di [amministratore senza restrizioni.](sql-database-manage-logins.md)
- Gli utenti a livello di database di Azure `CREATE USER ... FROM EXTERNAL PROVIDER` AD non amministratori possono essere creati usando la sintassi. Vedere [CREATE USER ... DA PROVIDER ESTERNO](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Le entità server di Azure AD (account di accesso) supportano le funzionalità SQL all'interno di una sola istanza gestita. Le funzionalità che richiedono l'interazione tra istanze, indipendentemente dal fatto che si trovano nello stesso tenant di Azure AD o in tenant diversi, non sono supportate per gli utenti di Azure AD. Tra queste funzionalità sono incluse:

  - Replica transazionale SQL.
  - Collegare il server.

- L'impostazione di un account di accesso di Azure AD mappato a un gruppo di Azure AD come proprietario del database non è supportata.
- È supportata la rappresentazione delle entità a livello di server di Azure AD tramite altre entità di Azure AD, ad esempio la clausola [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Le limitazioni di EXECUTE AS sono:

  - EXECUTE AS USER non è supportato per gli utenti di Azure AD quando il nome è diverso dal nome di accesso. Un esempio è quando l'utente viene creato tramite la sintassi CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] e la rappresentazione viene tentata tramite EXEC AS USER , _myAadUser_. Quando si crea un **UTENTE** da un'entità server di Azure AD (accesso), specificare il user_name come lo stesso login_name da **LOGIN**.
  - Solo le entità a livello di SQL Server (account di accesso) che fanno parte del `sysadmin` ruolo possono eseguire le operazioni seguenti destinate alle entità di Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- L'esportazione/importazione di database tramite file bacpac è supportata per gli utenti di Azure AD in un'istanza gestita che usa [SSMS V18.4 o versione successiva](/sql/ssms/download-sql-server-management-studio-ssms)oppure [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Le seguenti configurazioni sono supportate utilizzando il file bacpac del database: 
    - Esportare/importare un database tra istanze di gestione diverse all'interno dello stesso dominio di Azure AD.
    - Esportare un database dall'istanza gestita e importarlo nel database SQL all'interno dello stesso dominio di Azure AD. 
    - Esportare un database dal database SQL e importarlo in un'istanza gestita all'interno dello stesso dominio di Azure AD.
    - Esportare un database dall'istanza gestita e importarle in SQL Server (versione 2012 o successiva).
      - In questa configurazione tutti gli utenti di Azure AD vengono creati come entità di database SQL (utenti) senza account di accesso. Il tipo di utenti è elencato come SQL (visibile come SQL_USER in sys.database_principals). Le autorizzazioni e i ruoli rimangono nei metadati del database di SQL ServerSQL Server e possono essere utilizzati per la rappresentazione. Tuttavia, non possono essere utilizzati per accedere e accedere a SQL Server utilizzando le credenziali.

- Solo l'account di accesso dell'entità a livello di server, creato dal `securityadmin` `sysadmin`processo di provisioning dell'istanza gestita, i membri dei ruoli del server, ad esempio o , o altri account di accesso con autorizzazione ALTER ANY LOGIN a livello di server, possono creare entità server di Azure AD (account di accesso) nel database master per l'istanza gestita.
- Se l'account di accesso è un'entità `sysadmin` SQL, solo gli account di accesso che fanno parte del ruolo possono usare il comando create per creare account di accesso per un account Azure AD.
- L'account di accesso di Azure AD deve essere membro di un'istanza di Azure AD all'interno della stessa directory usata per l'istanza gestita del database SQL di Azure.The Azure AD login must be member of an Azure AD within the same directory that's used for Azure SQL Database managed instance.
- Le entità server di Azure AD (account di accesso) sono visibili in Esplora oggetti a partire da SQL Server Management Studio 18.0 preview 5.Azure AD server principals (logins) are visible in Object Explorer starting with SQL Server Management Studio 18.0 preview 5.
- È consentita la sovrapposizione di entità server (account di accesso) di Azure AD con un account amministratore di Azure AD. Le entità server di Azure AD (account di accesso) hanno la precedenza sull'amministratore di Azure AD quando si risolvono l'entità e si applicano le autorizzazioni all'istanza gestita.
- Durante l'autenticazione, viene applicata la sequenza seguente per risolvere l'entità di autenticazione:During authentication, the following sequence is applied to resolve the authenticating principal:

    1. Se l'account Azure AD esiste come direttamente mappato all'entità server di Azure AD (accesso), presente in sys.server_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'entità server (login) di Azure AD.
    2. Se l'account Azure AD è membro di un gruppo di Azure AD mappato all'entità server di Azure AD (accesso), presente in sys.server_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    3. Se l'account Azure AD è un amministratore speciale di Azure AD configurato dal portale per l'istanza gestita, che non esiste nelle visualizzazioni del sistema dell'istanza gestita, applicare autorizzazioni fisse speciali dell'amministratore di Azure AD per l'istanza gestita (modalità legacy).
    4. Se l'account Azure AD esiste come direttamente mappato a un utente di Azure AD in un database, presente in sys.database_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'utente del database di Azure AD.
    5. Se l'account Azure AD è membro di un gruppo di Azure AD mappato a un utente di Azure AD in un database, presente in sys.database_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    6. Se è stato eseguito il mapping di un account di accesso di Azure AD a un account utente di Azure AD o a un account di gruppo di Azure AD, che viene risolto all'utente che esegue l'autenticazione, vengono applicate tutte le autorizzazioni di questo account di accesso di Azure AD.

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- [Il backup della chiave master](/sql/t-sql/statements/backup-master-key-transact-sql) non è supportato (gestito dal servizio di database SQL).
- [Il ripristino della chiave master](/sql/t-sql/statements/restore-master-key-transact-sql) non è supportato (gestito dal servizio di database SQL).
- [Il backup](/sql/t-sql/statements/backup-service-master-key-transact-sql) della chiave master del servizio non è supportato (gestito dal servizio di database SQL).
- [Il ripristino](/sql/t-sql/statements/restore-service-master-key-transact-sql) della chiave master del servizio non è supportato (gestito dal servizio di database SQL).

## <a name="configuration"></a>Configurazione

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- [L'estensione](/sql/database-engine/configure-windows/buffer-pool-extension) del pool di buffer non è supportata.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportata. Vedere [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Regole di confronto

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- I livelli di compatibilità supportati sono 100, 110, 120, 130, 140 e 150.Supported compatibility levels are 100, 110, 120, 130, 140 and 150.
- I livelli di compatibilità inferiori a 100 non sono supportati.
- Il livello di compatibilità predefinito per i nuovi database è 140. Per i database ripristinati, il livello di compatibilità rimane invariato se era 100 e versioni successive.

Vedere [Livello di compatibilità ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mirroring del database

Il mirroring del database non è supportato.

- Le opzioni `ALTER DATABASE SET PARTNER` e `SET WITNESS` non sono supportate.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` non è supportata.

Per altre informazioni, vedere [ALTER DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opzioni di database

- I file di log multipli non sono supportati.
- Gli oggetti in memoria non sono supportati nel livello di servizio per utilizzo generico. 
- C'è un limite di 280 file per istanza di general Purpose, che implica un massimo di 280 file per database. I file di dati e di log nel livello Uso generale vengono conteggiati in base a questo limite. [Il livello Business Critical supporta 32.767 file per database.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- Il database non può contenere filegroup che contengono dati filestream. Il ripristino non `FILESTREAM` riesce se .bak contiene dati. 
- Ogni file viene inserito in Archiviazione BLOB di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file.

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le seguenti limitazioni `CREATE DATABASE`si applicano a:

- Non possono essere definiti file e filegroup. 
- L'opzione `CONTAINMENT` non è supportata. 
- `WITH`opzioni non sono supportate. 
   > [!TIP]
   > Come soluzione alternativa, utilizzare `ALTER DATABASE` dopo `CREATE DATABASE` per impostare le opzioni di database per aggiungere file o per impostare il contenimento. 

- L'opzione `FOR ATTACH` non è supportata.
- L'opzione `AS SNAPSHOT OF` non è supportata.

Per altre informazioni, vedere [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE - istruzione

Alcune proprietà di file non possono essere impostate o modificate:

- Non è possibile specificare un `ALTER DATABASE ADD FILE (FILENAME='path')` percorso di file nell'istruzione T-SQL. Rimuovere `FILENAME` dallo script in quanto un'istanza gestita inserisce i file automaticamente. 
- Non è possibile modificare un nome `ALTER DATABASE` di file utilizzando l'istruzione .

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

Per altre informazioni, vedere [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- L'abilitazione e la disabilitazione di Agente SQL Server non sono attualmente supportate nell'istanza gestita. SQL Agent è sempre in esecuzione.
- Le impostazioni di Agente SQL Server sono di sola lettura. La `sp_set_agent_properties` procedura non è supportata nell'istanza gestita. 
- Processi
  - I passaggi dei processi T-SQL sono supportati.
  - I processi di replica seguenti sono supportati:
    - Lettore di log delle transazioni
    - Snapshot
    - Database di distribuzione
  - I passaggi del processo SSIS sono supportati.
  - Altri tipi di passaggi di processo non sono attualmente supportati:
    - Il passaggio del processo di replica di tipo merge non è supportato. 
    - La lettura della coda non è supportata. 
    - La shell dei comandi non è ancora supportata.
  - Le istanze gestite non possono accedere alle risorse esterne, ad esempio le condivisioni di rete tramite robocopy.Managed instances can't access external resources, for example, network shares via robocopy. 
  - SQL Server Analysis Services non sono supportati.
- Le notifiche sono supportate in modo parziale.
- La notifica tramite posta elettronica è supportata, anche se richiede la configurazione di un profilo di Posta elettronica database. SQL ServerSQL Server Agent può utilizzare un solo `AzureManagedInstance_dbmail_profile`profilo di Posta elettronica database e deve essere chiamato . 
  - Pager non è supportato.
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.
- EventLog non è supportato.

Le seguenti funzionalità di SQL Agent attualmente non sono supportate:

- Proxy
- Pianificazione dei processi su una CPU inattiva
- Abilitazione o disabilitazione di un agente
- Avvisi

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

I seguenti tipi di tabella non sono supportati:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [TABELLA EXTERNAL](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (non supportato solo nel livello Scopo generale)

Per informazioni su come creare e modificare tabelle, vedere [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funzionalità

### <a name="bulk-insert--openrowset"></a>Inserimento bulk / OPENROWSET

Un'istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, pertanto i file devono essere importati dall'archivio BLOB di Azure:A managed instance can't access file shares and Windows folders, so the files must be imported from Azure Blob storage:

- `DATASOURCE`è necessario `BULK INSERT` nel comando durante l'importazione di file dall'archivio BLOB di Azure.Is required in the command while you import files from Azure Blob storage. Vedere [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`È necessario `OPENROWSET` nella funzione quando si legge il contenuto di un file dall'archiviazione BLOB di Azure.Is required in the function when you read the content of a file from Azure Blob storage. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`può essere usato per leggere dati da altri database SQL di Azure, istanze gestite o istanze di SQL Server.Can be used to read data from other Azure SQL single databases, managed instances or SQL Server instances. Altre origini, ad esempio database Oracle o file di Excel, non sono supportate.

### <a name="clr"></a>CLR

Un'istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, pertanto si applicano i vincoli seguenti:A managed instance can't access file shares and Windows folders, so the following constraints apply:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [CREATE ASSEM BLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` non è supportata. Vedere [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Posta elettronica database (db_mail)
 - `sp_send_dbmail`non può @file_attachments inviare allegati utilizzando il parametro. Il file system locale e le condivisioni esterne o Archiviazione BLOB di Azure non sono accessibili da questa procedura.
 - Vedere i problemi `@query` noti relativi ai parametri e all'autenticazione.
 
### <a name="dbcc"></a>DBCC

Le istruzioni DBCC non documentate abilitate in SQL ServerSQL Server non sono supportate nelle istanze gestite.

- Sono supportati solo un numero limitato di flag di traccia globale. I livelli `Trace flags` di sessione non sono supportati. Consultate [Flag di traccia.](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funzionano con il numero limitato di flag di traccia globali.
- Non è possibile utilizzare [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) con opzioni REPAIR_ALLOW_DATA_LOSS, `SINGLE_USER` REPAIR_FAST e REPAIR_REBUILD perché il database non può essere impostato in modalità, vedere [Differenze ALTER DATABASE](#alter-database-statement). I potenziali danneggiamenti del database vengono gestiti dal team di supporto di Azure.Potential database corruptions are handled by Azure support team. Contattare il supporto di Azure se si nota il danneggiamento del database che deve essere risolto.

### <a name="distributed-transactions"></a>Transazioni distribuite

MsDTC e [le transazioni elastiche](sql-database-elastic-transactions-overview.md) attualmente non sono supportate nelle istanze gestite.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per gli eventi estesi (XEvents) non sono supportate:Some Windows-specific targets for Extended Events (XEvents) aren't supported:

- La `etw_classic_sync` destinazione non è supportata. Archiviare i file nell'archivio BLOB di Azure.Store `.xel` files in Azure Blob storage. Vedere [Destinazione etw_classic_sync_target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- La `event_file` destinazione non è supportata. Archiviare i file nell'archivio BLOB di Azure.Store `.xel` files in Azure Blob storage. Vedere [Destinazione event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Librerie esterne

R e Python nel database, le librerie esterne non sono ancora supportate. Vedere [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- I dati Filestream non sono supportati.
- Il database non può contenere filegroup con `FILESTREAM` dati.
- `FILETABLE` non è supportata.
- Le tabelle non possono contenere tipi `FILESTREAM`.
- Le funzioni seguenti non sono supportate:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Per altre informazioni, vedere [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTable](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Ricerca semantica full-text

La [ricerca semantica](/sql/relational-databases/search/semantic-search-sql-server) non è supportata.

### <a name="linked-servers"></a>Server collegati

I server collegati nelle istanze gestite supportano un numero limitato di destinazioni:

- Le destinazioni supportate sono istanze gestite, database singoli e istanze di SQL ServerSQL Server . 
- I server collegati non supportano le transazioni scrivibili distribuite (MS DTC).
- Le destinazioni non supportate sono file, Analysis Services e altri sistemi RDBMS. Provare a usare l'importazione CSV `BULK INSERT` `OPENROWSET` nativa da Archiviazione BLOB di Azure usando o come alternativa per l'importazione di file.

Operazioni

- Le transazioni di scrittura tra istanze non sono supportate.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La `OPENROWSET` funzione può essere utilizzata per eseguire query solo su istanze di SQL Server.The function can be used to execute queries only on SQL Server instances. Possono essere gestiti, locali o in macchine virtuali. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- La `OPENDATASOURCE` funzione può essere utilizzata per eseguire query solo su istanze di SQL Server.The function can be used to execute queries only on SQL Server instances. Possono essere gestiti, locali o in macchine virtuali. Solo `SQLNCLI`i `SQLNCLI11`valori `SQLOLEDB` , e sono supportati come provider. Un esempio è `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- I server collegati non possono essere utilizzati per leggere file (Excel, CSV) dalle condivisioni di rete. Provare a usare BULK INSERT o OPENROWSET che legge i file CSV da Archiviazione BLOB di Azure.Try to use [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) or [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) that reads CSV files from Azure Blob Storage. Tenere traccia di queste richieste [nell'elemento Commenti e suggerimenti dell'istanza gestitaTrack](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) this requests on managed instance Feedback item|

### <a name="polybase"></a>PolyBase

Le tabelle esterne che fanno riferimento ai file nell'archiviazione BLOB di Azure o HDFS non sono supportate. Per informazioni su PolyBase, vedere [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

- Sono supportati i tipi di replica snapshot e bidirezionale. La replica di tipo merge, la replica peer-to-peer e le sottoscrizioni aggiornabili non sono supportate.
- [La replica transazionale](sql-database-managed-instance-transactional-replication.md) è disponibile per l'anteprima pubblica nell'istanza gestita con alcuni vincoli:Transactional Replication is available for public preview on managed instance with some constraints:
    - Tutti i tipi di partecipanti alla replica (server di pubblicazione, server di distribuzione, Sottoscrittore pull e Sottoscrittore Push) possono essere inseriti nelle istanze gestite, ma il server di pubblicazione e il server di distribuzione devono trovarsi sia nel cloud che in locale.
    - Le istanze gestite possono comunicare con le versioni recenti di SQL ServerSQL Server.Managed instances can communicate with the recent versions of SQL ServerSQL Server. Per altre informazioni, vedere la matrice delle [versioni supportate.](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)
    - La replica transazionale prevede alcuni requisiti di [rete aggiuntivi.](sql-database-managed-instance-transactional-replication.md#requirements)

Per altre informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:For more information about configuring transactional replication, see the following tutorials:
- [Replica tra un server di pubblicazione MI e un server di sottoscrizione](replication-with-sql-database-managed-instance.md)
- [Replica tra un server di pubblicazione MI, un server di distribuzione MI e un server di sottoscrizione di SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Istruzione RESTORE 

- Sintassi supportata:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintassi non supportata:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origine: 
  - `FROM URL`(Archiviazione BLOB di Azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportata.
  - I set di backup non sono supportati.
- `WITH`opzioni non sono supportate, `DIFFERENTIAL` `STATS`ad esempio no o .
- `ASYNC RESTORE`: il ripristino continua anche se la connessione client si interrompe. Se la connessione viene interrotta, è possibile controllare lo `sys.dm_operation_status` stato di una procedura di ripristino nella vista e di un database CREATE e DROP. Vedere [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Le seguenti opzioni di database vengono impostate o sostituite e non possono essere modificate in un secondo momento: 

- `NEW_BROKER`se il broker non è abilitato nel file .bak. 
- `ENABLE_BROKER`se il broker non è abilitato nel file .bak. 
- `AUTO_CLOSE=OFF`se un database nel file `AUTO_CLOSE=ON`bak dispone di . 
- `RECOVERY FULL`se un database nel file `SIMPLE` `BULK_LOGGED` con estensione bak ha o la modalità di ripristino.
- Un filegroup ottimizzato per la memoria viene aggiunto e denominato XTP se non era nel file bak di origine. 
- Qualsiasi filegroup ottimizzato per la memoria esistente viene rinominato XTP. 
- `SINGLE_USER`e `RESTRICTED_USER` le opzioni `MULTI_USER`vengono convertite in .

 Limitazioni: 

- I backup dei database danneggiati potrebbero essere ripristinati a seconda del tipo di danneggiamento, ma i backup automatici non verranno eseguiti fino a quando il danneggiamento non viene risolto. Assicurarsi di `DBCC CHECKDB` eseguire nell'istanza di `WITH CHECKSUM` origine e utilizzare il backup per evitare questo problema.
- Il `.BAK` ripristino del file di un database che contiene `FILESTREAM` eventuali `FILETABLE` limitazioni descritte in questo documento (ad esempio, o oggetti) non può essere ripristinato nell'istanza gestita.
- `.BAK`non è possibile ripristinare i file che contengono più set di backup. 
- `.BAK`i file che contengono più file di registro non possono essere ripristinati.
- I backup che contengono database di dimensioni superiori a 8 TB, oggetti OLTP attivi in memoria o numero di file che supererebbero i 280 file per istanza non possono essere ripristinati in un'istanza di General Purpose. 
- I backup che contengono database di dimensioni superiori a 4 TB o oggetti OLTP in memoria con dimensioni totali superiori alle dimensioni descritte nei [limiti delle risorse](sql-database-managed-instance-resource-limits.md) non possono essere ripristinati nell'istanza Business Critical.
Per informazioni sulle istruzioni di ripristino, vedere [Istruzioni RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Le stesse limitazioni si applicano all'operazione di ripristino temporizzato incorporata. Ad esempio, il database General Purpose maggiore di 4 TB non può essere ripristinato nell'istanza Business Critical. Non è possibile ripristinare il database Business Critical con file OLTP in memoria o più di 280 nell'istanza di General Purpose.

### <a name="service-broker"></a>Service Broker

Il broker di servizio tra istanze non è supportato:

- `sys.routes`: come prerequisito, è necessario selezionare l'indirizzo da sys.routes. L'indirizzo deve essere LOCAL su ogni percorso. Vedere [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: non è `CREATE ROUTE` possibile `ADDRESS` utilizzare `LOCAL`con un valore diverso da . Vedere [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: non è `ALTER ROUTE` possibile `ADDRESS` utilizzare `LOCAL`con un valore diverso da . Vedere [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Stored procedure, funzioni e trigger

- `NATIVE_COMPILATION`non è supportato nel livello Uso generale.
- Le opzioni [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` non è supportata. Vedere [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportata. Vedere [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`sono non supportati, `sp_addextendedproc`  tra `sp_dropextendedproc`cui e . Vedere [Stored procedure estese](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportate. Vedere [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funzioni e variabili di sistema

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')`restituisce il valore 8. Questa proprietà identifica in maniera univoca un'istanza gestita. Vedere [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`restituisce NULL perché il concetto di istanza esistente per SQL Server non si applica a un'istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`restituisce un nome DNS "collegabile" completo, ad esempio my-managed-instance.wcus17662feb9ce98.database.windows.net. Si [@SERVERNAMEveda il numero](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`restituisce un nome DNS "collegabile" completo, ad `myinstance.domain.database.windows.net` esempio per le proprietà "name" e "data_source". Vedere [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`restituisce NULL perché il concetto di servizio esistente per SQL Server non si applica a un'istanza gestita. Si [@SERVICENAMEveda il numero](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'account di accesso di Azure AD non è in sys.syslogins. Vedere [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` non è supportata. Vengono restituiti i dati errati, che è un problema noto temporaneo. Vedere [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Vincoli ambientali

### <a name="subnet"></a>Subnet
-  Non è possibile inserire altre risorse( ad esempio le macchine virtuali) nella subnet in cui è stata distribuita l'istanza gestita. Distribuire queste risorse usando una subnet diversa.
- Subnet deve disporre di un numero sufficiente di [indirizzi IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponibili. Il valore minimo è 16, mentre è consigliabile avere almeno 32 indirizzi IP nella subnet.
- [Gli endpoint del servizio non possono essere associati alla subnet dell'istanza gestita.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Assicurarsi che l'opzione degli endpoint del servizio sia disabilitata quando si crea la rete virtuale.
- Il numero di vCore e tipi di istanze che è possibile distribuire in un'area hanno alcuni [vincoli e limiti.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- Alcune regole di [sicurezza devono essere applicate nella subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Rete virtuale
- La rete virtuale può essere distribuita usando Il modello di risorse - Il modello classico per la rete virtuale non è supportato.
- Dopo la creazione di un'istanza gestita, lo spostamento dell'istanza gestita o della rete virtuale in un altro gruppo di risorse o sottoscrizione non è supportato.
- Alcuni servizi, ad esempio Ambienti del servizio app, app per la logica e istanze gestite (usate per la replica geografica, la replica transazionale o tramite server collegati) non possono accedere alle istanze gestite in aree diverse se le reti virtuali sono connesse tramite [peering globale.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) È possibile connettersi a queste risorse tramite ExpressRoute o da VNet a VNet tramite gateway di rete virtuale.

### <a name="tempdb"></a>Tempdb

La dimensione massima `tempdb` del file non può essere superiore a 24 GB per core in un livello Di utilizzo generale. La `tempdb` dimensione massima in un livello Business Critical è limitata dalla dimensione di archiviazione dell'istanza. `Tempdb`la dimensione del file di registro è limitata a 120 GB nel livello Scopigenerici generali. Alcune query potrebbero restituire un errore se sono necessari `tempdb` più di 24 GB per core in o se producono più di 120 GB di dati di log.

### <a name="msdb"></a>MSDB

I seguenti schemi MSDB nell'istanza gestita devono essere di proprietà dei rispettivi ruoli predefiniti:

- Ruoli generali
  - TargetServersRole
- [Ruoli predefiniti del database](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Ruoli DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - Databasemailuserrole
- [Ruoli dei servizi di integrazione](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> La modifica dei nomi di ruolo predefiniti, dei nomi degli schemi e dei proprietari dello schema da parte dei clienti influirà sul normale funzionamento del servizio. Tutte le modifiche apportate a questi verranno ripristinate ai valori predefiniti non appena rilevati, o al successivo aggiornamento del servizio al più recente per garantire il normale funzionamento del servizio.

### <a name="error-logs"></a>Log degli errori

Un'istanza gestita inserisce informazioni dettagliate nei log degli errori. Esistono molti eventi di sistema interni che vengono registrati nel log degli errori. Utilizzare una procedura personalizzata per leggere i log degli errori che filtra alcune voci irrilevanti. Per altre informazioni, vedere [istanza gestita sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) o [estensione dell'istanza gestita (anteprima)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) per Azure Data Studio.For more information, see managed instance - sp_readmierrorlog or managed instance extension(preview) for Azure Data Studio.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [Che cos'è un'istanza gestita?](sql-database-managed-instance.md)
- Per un elenco di funzionalità ed elenco di confronto, vedere Confronto tra le funzionalità del database SQL di Azure.For a features and comparison list, see [Azure SQL Database feature comparison](sql-database-features.md).
- Per gli aggiornamenti delle versioni e lo stato dei problemi noti, vedere [Le note sulla versione del database SQLFor](sql-database-release-notes.md) release updates and known issues state, see SQL Database release notes
- Per una guida introduttiva che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita.](sql-database-managed-instance-get-started.md)
