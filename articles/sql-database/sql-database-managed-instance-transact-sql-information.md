---
title: Differenze di SQL Database Managed Instance T-SQL di Azure | Microsoft Docs
description: Questo articolo illustra le differenze T-SQL tra un'istanza gestita in database SQL di Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 07/07/2019
ms.custom: seoapril2019
ms.openlocfilehash: 6b0e10ce48088853090958dca9d8c1fad20780e7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723251"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server

Questo articolo vengono riepilogati e vengono illustrate le differenze nella sintassi e il comportamento tra istanza gestita di Azure SQL Database e motore di Database locale SQL Server. Vengono illustrati gli argomenti seguenti: <a name="Differences"></a>

- [Disponibilità](#availability) include le differenze nei [Always-On](#always-on-availability) e [backup](#backup).
- [Sicurezza](#security) include le differenze nei [il controllo](#auditing), [certificati](#certificates), [credenziali](#credential), [cryptographic provider](#cryptographic-providers), [gli account di accesso e utenti](#logins-and-users)e il [chiave di servizio e chiave master del servizio](#service-key-and-service-master-key).
- [Configuration](#configuration) include le differenze nei [estensione del pool di buffer](#buffer-pool-extension), [regole di confronto](#collation), [i livelli di compatibilità](#compatibility-levels), [il mirroring del database ](#database-mirroring), [opzioni di database](#database-options), [SQL Server Agent](#sql-server-agent), e [Opzioni tabella](#tables).
- [Le funzionalità](#functionalities) comprende [BULK INSERT o OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transazioni distribuite](#distributed-transactions), [eventi estesi](#extended-events), [librerie esterne](#external-libraries), [filestream e FileTable](#filestream-and-filetable), [ricerca semantica full-text](#full-text-semantic-search), [server collegati](#linked-servers), [PolyBase](#polybase), [replica](#replication), [RESTORE](#restore-statement), [servizio Broker](#service-broker), [stored procedure, funzioni e trigger](#stored-procedures-functions-and-triggers).
- [Le impostazioni di ambiente](#Environment) , ad esempio le configurazioni di reti virtuali e subnet.
- [Le istanze gestite di funzioni che hanno un comportamento diverso in](#Changes).
- [Problemi noti e limitazioni temporanee](#Issues).

L'opzione di distribuzione Istanza gestita assicura una compatibilità elevata con il motore di database di SQL Server locale. La maggior parte delle funzionalità del motore di database di SQL Server è supportata in un'istanza gestita.

![Migrazione](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilità

### <a name="always-on-availability"></a>Always On

[Disponibilità elevata](sql-database-high-availability.md) è incorporata in istanza gestita e non può essere controllato dagli utenti. Non sono supportate le istruzioni seguenti:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Il [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) clausola delle [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) istruzione

### <a name="backup"></a>Backup

Le istanze gestite sono backup automatici, in modo che gli utenti possono creare completo del database `COPY_ONLY` i backup. Backup differenziali, log e backup di snapshot di file non sono supportati.

- Con un'istanza gestita, è possibile eseguire il backup di un database di istanza solo per un account di archiviazione Blob di Azure:
  - È supportato solo `BACKUP TO URL`.
  - `FILE`, `TAPE`, e non sono supportati i dispositivi di backup.
- La maggior parte delle generali `WITH` opzioni sono supportate.
  - `COPY_ONLY` è obbligatorio.
  - `FILE_SNAPSHOT` non è supportata.
  - Opzioni nastro: `REWIND`, `NOREWIND`, `UNLOAD`, e `NOUNLOAD` non sono supportati.
  - Opzioni specifiche dei log: `NORECOVERY`, `STANDBY`, e `NO_TRUNCATE` non sono supportati.

Limitazioni: 

- Con un'istanza gestita, è possibile eseguire il backup di un database di istanza da un backup con un massimo di 32 strisce, sufficiente per i database fino a 4 TB se viene utilizzata la compressione dei backup.
- La dimensione massima striscia di backup usando il `BACKUP` comando in un'istanza gestita corrisponde a 195 GB, ovvero le dimensioni massime del blob. Aumentare il numero di set di stripe nel comando backup per ridurre le dimensioni dei singoli set di stripe e restare nel limite consentito.

    > [!TIP]
    > Per aggirare questa limitazione, quando si esegue il backup di un database da SQL Server in un ambiente locale o in una macchina virtuale, è possibile:
    >
    > - Eseguire il backup su `DISK` anziché il backup su `URL`.
    > - Caricare i file di backup nell'archiviazione Blob.
    > - Ripristina nell'istanza gestita.
    >
    > Il `Restore` comando in un'istanza gestita supporta le dimensioni più grandi del blob nei file di backup poiché viene utilizzato un tipo di blob diverso per l'archiviazione dei file di backup caricati.

Per informazioni sui backup con T-SQL, vedere [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Security

### <a name="auditing"></a>Controllo

Le principali differenze tra il controllo nei database nel database SQL di Azure e quello in SQL Server sono le seguenti:

- Con l'opzione di distribuzione di istanza gestita di Database SQL di Azure, il controllo funziona a livello di server. Il `.xel` i file di log vengono archiviati nell'archiviazione Blob di Azure.
- Con le opzioni di distribuzione dei database singoli e dei pool elastici nel database SQL di Azure, il controllo viene eseguito a livello del database.
- In SQL Server on-premises o le macchine virtuali, il controllo funziona a livello di server. Gli eventi vengono archiviati nel file system o i registri eventi di Windows.
 
Il controllo XEvent in Istanza gestita supporta le destinazioni di Archiviazione BLOB di Azure. Log di file e di Windows non sono supportati.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- Una nuova sintassi `TO URL` condizione che è possibile usare per specificare l'URL del contenitore di archiviazione Blob di Azure in cui il `.xel` i file vengono inseriti.
- La sintassi `TO FILE` non è supportata perché istanza gestita non può accedere a condivisioni file di Windows.

Per altre informazioni, vedere: 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificati

Un'istanza gestita non può accedere a condivisioni file e cartelle di Windows, in modo che si applicano i vincoli seguenti:

- Il `CREATE FROM` / `BACKUP TO` file non è supportato per i certificati.
- Il `CREATE` / `BACKUP` certificato `FILE` / `ASSEMBLY` non è supportato. I file di chiavi private non possono essere usati. 

Vedere [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Soluzione alternativa**: Creare uno script per il certificato o chiave privata, archiviare come file con estensione SQL e creare dal file binario:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credenziali

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Un'istanza gestita non può accedere a file, in modo che non è possibile creare provider di crittografia:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Account di accesso e utenti

- Account di accesso SQL creati usando `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, e `FROM SID` sono supportati. Vedere [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) entità server (accessi) create con il [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) sintassi o il [CREATE dall'account di accesso utente [account di accesso AD Azure]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sintassi sono supportate (anteprima pubblica). Questi account di accesso vengono creati a livello di server.

    Istanza gestita supporta le entità di database di Azure AD con la sintassi `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Questa funzionalità è nota anche come gli utenti del database indipendente di Azure AD.

- Account di accesso Windows creati con la `CREATE LOGIN ... FROM WINDOWS` sintassi non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- L'utente di Azure AD che ha creato l'istanza ha [privilegi amministrativi illimitati](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Gli utenti di Azure AD non amministratore a livello di database possono essere creati usando il `CREATE USER ... FROM EXTERNAL PROVIDER` sintassi. Vedere [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Entità del server Azure AD (accessi) supportano le funzionalità di SQL all'interno di una sola istanza gestita. Le funzionalità che richiedono l'interazione tra istanze, indipendentemente da se si è all'interno di Azure AD stesso tenant o tenant diversi, non sono supportate per gli utenti di Azure AD. Tra queste funzionalità sono incluse:

  - Replica transazionale di SQL.
  - Server di collegamento.

- L'impostazione di un account di accesso di Azure AD mappato a un gruppo di Azure AD come proprietario del database non è supportata.
- Rappresentazione delle identità a livello di server di Azure AD con altre entità di Azure AD è supportata, ad esempio la [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) clausola. Sono le limitazioni di EXECUTE AS:

  - EXECUTE AS USER non è supportata per gli utenti di Azure AD quando il nome è diverso dal nome dell'account di accesso. Un esempio è quando l'utente viene creato tramite la sintassi CREATE USER [myAadUser] dall'account di accesso [john@contoso.com] e viene tentata la rappresentazione tramite EXEC AS USER = _myAadUser_. Quando si crea una **utente** da un'entità server Azure AD (account di accesso), specificare la funzione user_name come la stessa login_name dal **LOGIN**.
  - Solo il livello di Server SQL entità (accessi) che fanno parte di `sysadmin` ruolo può eseguire le operazioni seguenti che usano le entità di Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limiti dell'anteprima pubblica delle entità del server Azure AD (account di accesso):

  - Limitazioni di amministratore Directory attive per l'istanza gestita:

    - L'amministratore di Azure AD usato per configurare l'istanza gestita non può essere utilizzato per creare un Azure AD entità server (account di accesso) all'interno dell'istanza gestita. È necessario creare l'entità server Active Directory (account di accesso) di Azure prima con un account di SQL Server che un `sysadmin` ruolo. Questa limitazione temporanea verrà rimossi dopo l'entità del server Azure AD (accessi) diventa disponibile a livello generale. Se si tenta di usare un account di amministratore di Azure AD per creare l'account di accesso, viene visualizzato l'errore seguente: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Attualmente, il primo accesso di Azure AD creato nel database master deve essere creato dall'account di SQL Server standard (non di Azure AD) di un `sysadmin` ruolo usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) dal PROVIDER esterno. Dopo la disponibilità generale, questa limitazione verrà rimossa. È possibile creare un'iniziale di accesso AD Azure con l'amministratore di Active Directory per l'istanza gestita.
    - Utilizzato con SQL Server Management Studio o SqlPackage DacFx (esportazione/importazione) non è supportata per gli account di accesso di Azure AD. Questa limitazione verrà rimossa dopo l'entità del server Azure AD (accessi) diventa disponibile a livello generale.
    - Utilizzo di entità del server Azure AD (account di accesso) con SQL Server Management Studio:

      - Gli account di accesso di Azure AD che usano qualsiasi account di accesso autenticato di scripting non è supportato.
      - IntelliSense non riconosce l'istruzione CREATE LOGIN da PROVIDER esterni e visualizza una sottolineatura rossa.

- Solo il server di accesso entità a livello, che viene creato tramite l'istanza gestita il provisioning di processo, i membri dei ruoli del server, ad esempio `securityadmin` o `sysadmin`, o altri account di accesso con autorizzazione ALTER ANY LOGIN a livello di server possono creare Azure AD entità server (accessi) nel database master per l'istanza gestita.
- Se l'account di accesso è un'entità SQL, solo gli account di accesso che fanno parte di `sysadmin` ruolo può usare il comando di creazione per creare gli account di accesso per un account Azure AD.
- L'account di accesso di Azure AD deve essere un membro di un Azure Active Directory all'interno della stessa directory utilizzata per l'istanza gestita di Azure SQL Database.
- Entità del server Azure AD (accessi) sono visibili in Esplora oggetti, a partire da SQL Server Management Studio 18.0 preview 5.
- È consentita la sovrapposizione di entità server (account di accesso) di Azure AD con un account amministratore di Azure AD. Entità del server Azure AD (accessi) hanno la precedenza sull'amministratore di Azure AD quando è risolvere l'entità e applicare le autorizzazioni per l'istanza gestita.
- Durante l'autenticazione, viene applicata la sequenza seguente per risolvere l'entità che esegue l'autenticazione:

    1. Se l'account Azure AD esiste come direttamente il mapping a entità di server di Azure AD (account di accesso), che è presente in sys. server_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'entità del server Azure AD (account di accesso).
    2. Se l'account Azure AD è un membro di un gruppo di Azure AD che viene eseguito il mapping a entità di server di Azure AD (account di accesso), che è presente in sys. server_principals come "X" di tipo, concedere l'accesso e applicare le autorizzazioni dell'account di accesso di gruppo Azure AD.
    3. Se l'account Azure AD è una speciale configurato al portale di amministrazione di Azure AD per l'istanza gestita, non esiste nelle viste di sistema di istanza gestita, si applicano autorizzazioni speciali fisse dell'amministratore di Azure AD per l'istanza gestita (modalità legacy).
    4. Se l'account Azure AD esiste come mappata direttamente a un utente di Azure AD di un database, che è presente in sys. database_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'utente del database di Azure AD.
    5. Se l'account Azure AD è un membro di un gruppo di Azure AD che viene eseguito il mapping a un utente di Azure AD di un database, che è presente in sys. database_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso di gruppo Azure AD.
    6. Se è presente un account di accesso di Azure AD con mappata a un account utente di Azure AD o un account di gruppo Azure AD, che vengono risolte all'utente che esegue l'autenticazione, vengono applicate tutte le autorizzazioni per questo account di accesso di Azure AD.

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- [Backup della chiave master](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) non è supportato (gestito dal servizio di Database SQL).
- [Ripristino della chiave master](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) non è supportato (gestito dal servizio di Database SQL).
- [Backup della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) non è supportato (gestito dal servizio di Database SQL).
- [Ripristino della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) non è supportato (gestito dal servizio di Database SQL).

## <a name="configuration"></a>Configurazione

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- [Estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) non è supportato.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportata. Vedere [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- Livelli di compatibilità supportati sono 100, 110, 120, 130 e 140.
- I livelli di compatibilità inferiori a 100 non sono supportati.
- Il livello di compatibilità predefinito per i nuovi database è 140. Per i database ripristinati, il livello di compatibilità rimane invariato se era 100 e versioni successive.

Vedere [Livello di compatibilità ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mirroring del database

Il mirroring del database non è supportato.

- Le opzioni `ALTER DATABASE SET PARTNER` e `SET WITNESS` non sono supportate.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` non è supportata.

Per altre informazioni, vedere [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opzioni di database

- I file di log multipli non sono supportati.
- Gli oggetti in memoria non sono supportati nel livello di servizio per utilizzo generico. 
- È previsto un limite di 280 file per ogni istanza di uso generale, che implica un massimo di 280 file per ogni database. I file di log sia i dati nel livello utilizzo generico sono conteggiati per il limite. [Il livello Business Critical supporta 32.767 file per ogni database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Il database non può contenere filegroup che contengono dati filestream. Ripristino ha esito negativo se con estensione bak contiene `FILESTREAM` dei dati. 
- Ogni file viene inserito in Archiviazione BLOB di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file.

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le limitazioni seguenti si applicano a `CREATE DATABASE`:

- Non possono essere definiti file e filegroup. 
- Il `CONTAINMENT` opzione non è supportata. 
- `WITH` opzioni non sono supportate. 
   > [!TIP]
   > In alternativa, usare `ALTER DATABASE` dopo `CREATE DATABASE` per impostare le opzioni di database per aggiungere file o impostazione del contenimento. 

- Il `FOR ATTACH` opzione non è supportata.
- Il `AS SNAPSHOT OF` opzione non è supportata.

Per altre informazioni, vedere [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Istruzione ALTER DATABASE

Alcune proprietà di file non possono essere impostate o modificate:

- Non è possibile specificare un percorso di file nei `ALTER DATABASE ADD FILE (FILENAME='path')` istruzione T-SQL. Rimuovere `FILENAME` dallo script in quanto un'istanza gestita inserisce i file automaticamente. 
- Un nome di file non può essere modificato usando il `ALTER DATABASE` istruzione.

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

- Abilitazione e disabilitazione di SQL Server Agent non è supportato in istanza gestita. SQL Agent è sempre in esecuzione.
- Le impostazioni di SQL Server Agent sono di sola lettura. La procedura `sp_set_agent_properties` non è supportata in istanza gestita. 
- Processi
  - I passaggi dei processi T-SQL sono supportati.
  - I processi di replica seguenti sono supportati:
    - Lettore di log delle transazioni
    - Snapshot
    - Database di distribuzione
  - I passaggi di processo SSIS sono supportati.
  - Non sono attualmente supportati altri tipi di passaggi di processo:
    - Il passaggio di processo di merge della replica non è supportato. 
    - La lettura della coda non è supportata. 
    - Shell dei comandi non è ancora supportata.
  - Le istanze gestite non possono accedere alle risorse esterne, ad esempio, condivisioni di rete tramite robocopy. 
  - SQL Server Analysis Services non sono supportati.
- Le notifiche sono supportate in modo parziale.
- Notifica tramite posta elettronica è supportata, anche se è necessario configurare un profilo di posta elettronica Database. SQL Server Agent è possibile usare un solo profilo di posta elettronica Database e deve essere chiamato `AzureManagedInstance_dbmail_profile`. 
  - Pager non è supportato.
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.
- EventLog non è supportata.

Le seguenti funzionalità di SQL Agent non sono attualmente supportate:

- Proxy
- Pianificazione dei processi su una CPU inattiva
- Abilitazione o disabilitazione di un agente
- Avvisi

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

Non sono supportate nelle tabelle seguenti:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Per informazioni su come creare e modificare le tabelle, vedere [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funzionalità

### <a name="bulk-insert--openrowset"></a>Inserimento bulk/openrowset

Un'istanza gestita non può accedere a condivisioni file e cartelle di Windows, in modo che i file devono essere importati dall'archiviazione Blob di Azure:

- `DATASOURCE` è necessaria la `BULK INSERT` comando durante l'importazione di file dall'archiviazione Blob di Azure. Vedere [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` è necessaria la `OPENROWSET` funzionare quando si legge il contenuto di un file dall'archiviazione Blob di Azure. Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Un'istanza gestita non può accedere a condivisioni file e cartelle di Windows, in modo che si applicano i vincoli seguenti:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` non è supportata. Vedere [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Istruzioni DBCC non documentate abilitate in SQL Server non sono supportate nelle istanze gestite.

- I `Trace flags` non sono supportati. Visualizzare [flag di traccia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` non è supportata. Vedere [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` non è supportata. Vedere [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transazioni distribuite

MSDTC e [transazioni elastiche](sql-database-elastic-transactions-overview.md) attualmente non sono supportate nelle istanze gestite.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per gli eventi estesi (XEvent) non sono supportate:

- Il `etw_classic_sync` destinazione non è supportata. Store `.xel` file nell'archivio Blob di Azure. Vedere [Destinazione etw_classic_sync_target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Il `event_file` destinazione non è supportata. Store `.xel` file nell'archivio Blob di Azure. Vedere [Destinazione event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Librerie esterne

In-database R e Python, librerie esterne non sono ancora supportate. Vedere [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- i dati FILESTREAM non sono supportati.
- Il database non può contenere filegroup con `FILESTREAM` dei dati.
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

- Destinazioni supportate sono SQL Server e Database SQL.
- Le destinazioni che non sono supportate sono file, Analysis Services e altri RDBMS.

Operazioni

- Le transazioni di scrittura tra istanze non sono supportate.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Il `OPENROWSET` funzione può essere utilizzata per eseguire query solo su istanze di SQL Server. Possono essere gestiti in locale, o in macchine virtuali. Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- Il `OPENDATASOURCE` funzione può essere utilizzata per eseguire query solo su istanze di SQL Server. Possono essere gestiti in locale, o in macchine virtuali. Solo le `SQLNCLI`, `SQLNCLI11`, e `SQLOLEDB` sono supportati come provider di valori. Un esempio è `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Tabelle esterne che fanno riferimento che non sono supportati i file in HDFS o Blob di Azure nell'archivio. Per informazioni su PolyBase, vedere [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

[La replica transazionale](sql-database-managed-instance-transactional-replication.md) è disponibile per l'anteprima pubblica sull'istanza gestita con alcune limitazioni:
- Tipi di tutti i partecipanti di replica (server di pubblicazione, server di distribuzione, sottoscrittore Pull e Push sottoscrittore) possono essere inseriti in istanza gestita, ma server di pubblicazione e server di distribuzione non può trovarsi in istanze diverse.
- Sono supportati i tipi di replica transazionale, Snapshot e bidirezionali. Replica di tipo merge, la replica Peer-to-peer e le sottoscrizioni aggiornabili non sono supportate.
- Istanza gestita può comunicare con le versioni recenti di SQL Server. Vedere le versioni supportate [qui](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
- La replica transazionale ha alcuni [requisiti di rete aggiuntivi](sql-database-managed-instance-transactional-replication.md#requirements).

Per informazioni sulla configurazione della replica, vedere [esercitazione replica](replication-with-sql-database-managed-instance.md).

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
  - `FROM URL` (Archivio Blob di azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportata.
  - I set di backup non sono supportati.
- `WITH` non sono supportate le opzioni, ad esempio no `DIFFERENTIAL` o `STATS`.
- `ASYNC RESTORE`: Ripristino continua anche se si interrompe la connessione client. Se la connessione viene interrotta, è possibile controllare il `sys.dm_operation_status` visualizzazione dello stato di un'operazione di ripristino e per un database CREATE e DROP. Vedere [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Le seguenti opzioni di database sono set o sottoposto a override e non può essere modificato in un secondo momento: 

- `NEW_BROKER` Se il broker non è abilitato nel file con estensione bak. 
- `ENABLE_BROKER` Se il broker non è abilitato nel file con estensione bak. 
- `AUTO_CLOSE=OFF` Se un database nel file con estensione bak ha `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Se dispone di un database nel file con estensione bak `SIMPLE` o `BULK_LOGGED` modalità di ripristino.
- Un filegroup ottimizzato per la memoria viene aggiunto e denominato XTP se non fosse nel file con estensione bak di origine. 
- Qualsiasi filegroup con ottimizzazione per la memoria esistente viene rinominato come XTP. 
- `SINGLE_USER` e `RESTRICTED_USER` vengono convertiti in Opzioni `MULTI_USER`.

Limitazioni: 

- `.BAK` non è possibile ripristinare i file che contengono più set di backup. 
- `.BAK` non è possibile ripristinare i file che contengono più file di log.
- Ripristino ha esito negativo se con estensione bak contiene `FILESTREAM` dei dati.
- Impossibile ripristinare i backup contenenti database con oggetti in memoria attivi in un'istanza di uso generale. Per informazioni sulle istruzioni di ripristino, vedere [istruzioni RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Broker di servizio

Il broker di servizio tra istanze non è supportato:

- `sys.routes`: Come prerequisito, è necessario selezionare l'indirizzo da Sys. Routes. L'indirizzo deve essere locale in ogni route. Vedere [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Non è possibile usare `CREATE ROUTE` con `ADDRESS` diverso da `LOCAL`. Vedere [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Non è possibile usare `ALTER ROUTE` con `ADDRESS` diverso da `LOCAL`. Vedere [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>I trigger, funzioni e stored procedure

- `NATIVE_COMPILATION` non è supportato nel livello utilizzo generico.
- Le opzioni [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` non è supportata. Vedere [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportata. Vedere [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` non sono supportate, che include `sp_addextendedproc`  e `sp_dropextendedproc`. Visualizzare [stored procedure estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportate. Vedere [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Vincoli dell'ambiente

### <a name="subnet"></a>Subnet
- Nella subnet sono riservate per l'istanza gestita è possibile inserire qualsiasi altra risorsa (ad esempio macchine virtuali). Queste risorse vengano inserite in altre subnet.
- Subnet deve essere un numero sufficiente di disponibili [gli indirizzi IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Valore minimo è 16, mentre è consigliato disporre di almeno 32 indirizzi IP nella subnet.
- [Gli endpoint di servizio non possono essere associati a subnet dell'istanza gestita](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Assicurarsi che l'opzione endpoint di servizio viene disabilitato quando si crea la rete virtuale.
- Tipi di istanze che è possibile distribuire in un'area e il numero di Vcore presentano alcuni [limiti e vincoli](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Esistono tuttavia alcuni [regole di sicurezza che devono essere applicate nella subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Rete virtuale
- Rete virtuale può essere distribuita usando il modello di risorse: modello di distribuzione classica per rete virtuale non è supportato.
- Alcuni servizi, ad esempio ambienti del servizio App, App per la logica e le istanze gestite (usato per la replica geografica, la replica transazionale, o tramite i server collegati) non possono accedere le istanze gestite in aree diverse se le reti virtuali sono connesse tramite [peering globale](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). È possibile connettersi a queste risorse tramite ExpressRoute o della rete virtuale a rete virtuale tramite gateway di rete virtuale.

## <a name="Changes"></a> Modifiche nel comportamento

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')` Restituisce il valore 8. Questa proprietà identifica in maniera univoca un'istanza gestita. Vedere [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Restituisce NULL perché il concetto di istanza perché esiste per SQL Server non è possibile in un'istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Restituisce nome DNS completo "collegabile", ad esempio,-instance.wcus17662feb9ce98.database.windows.net gestito my. Vedere [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Restituisce, ad esempio nome DNS completo "collegabile", `myinstance.domain.database.windows.net` per le proprietà "nome" e "data_source." Vedere [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Restituisce NULL perché il concetto di servizio perché è presente per SQL Server non è possibile in un'istanza gestita. Vedere [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'account di accesso di Azure AD non è in sys. syslogins. Vedere [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` non è supportata. Viene restituiti dati non corretto, che è una variabile temporanea problema noto. Vedere [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Problemi noti e limitazioni

### <a name="tempdb-size"></a>Dimensioni di TEMPDB

Le dimensioni massime di `tempdb` non può essere maggiore di 24 GB per ogni core in un livello utilizzo generico. Il valore massimo `tempdb` dimensioni su un livello Business Critical sono limitata con le dimensioni di archiviazione di istanza. `tempdb` dimensioni file di log sono limitata a 120 GB sia a per utilizzo generico e livelli Business Critical. Il `tempdb` database sempre è suddiviso in file di 12 dati. Queste dimensioni massime per ogni file non possono essere modificata e non possono essere aggiunti al nuovo file `tempdb`. Alcune query potrebbero restituire un errore se necessitano più di 24 GB per ogni core di `tempdb` o se producono più di 120 GB di log. `tempdb` viene sempre ricreato quando un database vuoto all'avvio dell'istanza o il failover e qualsiasi modifica effettuata in `tempdb` non verrà mantenuta. 

### <a name="cant-restore-contained-database"></a>Non è possibile ripristinare i database indipendenti

Non è possibile ripristinare l'istanza gestita [database indipendenti](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Point-in-time ripristinare i database indipendenti esistenti non funziona con istanza gestita. Questo problema verrà risolto a breve. Nel frattempo, è consigliabile rimuovere l'opzione di indipendenza dai database che vengono inseriti in istanza gestita. Non usare l'opzione di contenimento per i database di produzione. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, e `RESTORE DATABASE` istruzioni potrebbero non riuscire perché l'istanza può raggiungere il limite di archiviazione di Azure.

Ogni istanza gestita generico ha fino a 35 TB di spazio di archiviazione riservati per lo spazio su disco Premium di Azure. Ogni file di database si trova su un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Non viene addebitato lo spazio inutilizzato su disco, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare i 35 TB Azure limita alle dimensioni di archiviazione a causa della frammentazione interna.

Ad esempio, un'istanza gestita di uso generale potrebbe contenere un file che è posizionata su un disco da 4 TB di dimensioni pari a 1,2 TB. Può anche avere 248 file ogni 1 GB di dimensioni che vengono inseriti in dischi separati da 128 GB. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio illustra che in determinate circostanze, a causa di una distribuzione specifica di file, un'istanza gestita può raggiungere il limite di 35 TB riservata per un disco Premium di Azure collegato quando potrebbe essere non previsto.

In questo esempio, i database esistenti continuano a funzionare e può crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. Impossibile creare nuovi database o ripristinati perché non c'è spazio sufficiente per nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite delle dimensioni di istanza. L'errore restituito in questo caso non è chiaro.

È possibile [identificare il numero di file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizzando viste di sistema. Se si raggiunge questo limite, tentare [vuoto ed eliminare alcuni dei file più piccoli, usando l'istruzione DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello Business Critical, che non hanno questo limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Configurazione errata della chiave di firma di accesso condiviso durante il database di ripristino

`RESTORE DATABASE` che legge il file con estensione bak potrebbe essere costantemente nuovo tentativo di leggere l'estensione bak di file e restituisce un errore dopo un lungo periodo di tempo se la firma di accesso condiviso in `CREDENTIAL` non è corretto. Eseguire RESTORE HEADERONLY prima di ripristinare un database per verificare che la chiave di firma di accesso condiviso sia corretta.
Assicurarsi di rimuovere il leader `?` dalla chiave di firma di accesso condiviso generato usando il portale di Azure.

### <a name="tooling"></a>Strumenti

SQL Server Management Studio e SQL Server Data Tools potrebbero presentano alcuni problemi quando accedono a un'istanza gestita.

- Uso di entità del server Azure AD (account di accesso) e gli utenti (anteprima pubblica) con SQL Server Data Tools attualmente non è supportata.
- Script per l'entità del server Azure AD (account di accesso) e gli utenti (anteprima pubblica) non è supportato in SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomi di database errati in alcune viste, log e messaggi

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID perché vengono sostituiti con i nomi dei database effettivo in futuro.

### <a name="database-mail"></a>Posta elettronica database

Il `@query` parametro il [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedura non funziona.

### <a name="database-mail-profile"></a>Profilo di posta elettronica database

Il profilo di posta elettronica Database usato da SQL Server Agent deve essere chiamato `AzureManagedInstance_dbmail_profile`. Non sono previste restrizioni per gli altri nomi di profilo di posta elettronica Database.

### <a name="error-logs-arent-persisted"></a>Non sono salvati in modo permanente i log degli errori

I log degli errori che sono disponibili nell'istanza gestita non sono persistenti e le relative dimensioni non sono inclusa nel limite di archiviazione massima. I log degli errori potrebbero essere cancellati automaticamente se si verifica il failover.

### <a name="error-logs-are-verbose"></a>I log degli errori sono di tipo dettagliato

Un'istanza gestita inserisce informazioni dettagliate nel log degli errori e gran parte di esso non è rilevante. In futuro verrà ridotta la quantità di informazioni nel log degli errori.

**Soluzione alternativa:** Usare una procedura personalizzata per leggere i log degli errori che esclude alcune voci irrilevanti. Per altre informazioni, vedere [istanza gestita: sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ambito di transazione in due database nella stessa istanza non è supportato

Il `TransactionScope` classe in .NET non funziona se le due query vengono inviate a due database nella stessa istanza nello stesso ambito di transazione:

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

Sebbene questo codice funziona con i dati nella stessa istanza, è necessario MSDTC.

**Soluzione alternativa:** Usare [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) usare un altro database in un contesto di connessione invece di usare due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>In alcuni casi i server collegati e i moduli CLR non possono fare riferimento a un indirizzo IP locale

I moduli CLR collocati in un'istanza gestita e i server collegati o le query distribuite che fanno riferimento a un'istanza corrente, in alcuni casi non è possibile risolvere l'indirizzo IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa:** Se possibile, utilizzare connessioni di contesto in un modulo CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Database crittografato con TDE con una chiave gestita dal servizio non supportano backup avviati dall'utente

Non è possibile eseguire `BACKUP DATABASE ... WITH COPY_ONLY` in un database crittografato con gestite dal servizio Transparent Data Encryption (TDE). TDE gestita dal servizio e impone i backup da crittografare con una chiave interna di Transparent Data Encryption. Impossibile esportare la chiave, in modo che non è possibile ripristinare il backup.

**Soluzione alternativa:** Usare i backup automatici e ripristino temporizzato in oppure usare [gestite dal cliente (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) invece. È anche possibile disabilitare la crittografia nel database.

### <a name="point-in-time-restore-follows-time-by-the-time-zone-set-on-the-source-instance"></a>Ripristino temporizzato in segue ora dal fuso orario impostato nell'istanza di origine

Ripristino temporizzato in attualmente interpreta tempo per il ripristino dal seguente fuso orario dell'istanza di origine invece da UTC seguenti.
Controllare [problemi noti relativi all'istanza gestita fuso orario](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone#known-issues) per altri dettagli.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [che cos'è un'istanza gestita?](sql-database-managed-instance.md)
- Per una funzionalità e un elenco di confronto, vedere [confronto tra le funzionalità di Database SQL di Azure](sql-database-features.md).
- Per una Guida introduttiva che illustra come creare una nuova istanza gestita, vedere [creare un'istanza gestita](sql-database-managed-instance-get-started.md).
