---
title: Differenze di T-SQL tra SQL Server & SQL di Azure Istanza gestita
description: Questo articolo illustra le differenze di Transact-SQL (T-SQL) tra un Istanza gestita SQL di Azure e SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 11/10/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 873bebc462ce4756d38f966a87edda167bd49501
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506380"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Differenze di T-SQL tra SQL Server & SQL di Azure Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo riepiloga e spiega le differenze nella sintassi e nel comportamento tra Istanza gestita SQL di Azure e SQL Server. 


SQL Istanza gestita garantisce una compatibilità elevata con il motore di database di SQL Server e la maggior parte delle funzionalità è supportata in un Istanza gestita SQL.

![Facile migrazione da SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Esistono alcune limitazioni di PaaS introdotte in SQL Istanza gestita e alcune modifiche del comportamento rispetto a SQL Server. Le differenze sono divise nelle categorie seguenti: <a name="Differences"></a>

- La [disponibilità](#availability) include le differenze nei [gruppi di disponibilità always on](#always-on-availability-groups) e nei [backup](#backup).
- La [protezione](#security) include le differenze tra il [controllo](#auditing), i [certificati](#certificates), le [credenziali](#credential), i [provider di crittografia](#cryptographic-providers), [gli account di accesso e gli utenti](#logins-and-users)e la [chiave del servizio e la chiave master del servizio](#service-key-and-service-master-key).
- La [configurazione](#configuration) include le differenze nell' [estensione del pool di buffer](#buffer-pool-extension), le regole di [confronto](#collation), i [livelli di compatibilità](#compatibility-levels), il [mirroring del database](#database-mirroring), le opzioni di [database](#database-options), [SQL Server Agent](#sql-server-agent)e le [Opzioni di tabella](#tables).
- Le [funzionalità](#functionalities) includono [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transazioni distribuite](#distributed-transactions), [eventi estesi](#extended-events), [librerie esterne](#external-libraries), [FileStream e FileTable](#filestream-and-filetable), [ricerca semantica full-text](#full-text-semantic-search), [server collegati](#linked-servers), [polibase](#polybase), [replica](#replication), [ripristino](#restore-statement), [Service Broker](#service-broker), [stored procedure, funzioni e trigger](#stored-procedures-functions-and-triggers).
- [Impostazioni dell'ambiente](#Environment) , ad esempio le configurazioni di reti virtuali e subnet.

La maggior parte di queste funzionalità sono vincoli di architettura e rappresentano le funzionalità del servizio.

I problemi noti temporanei individuati in SQL Istanza gestita e verranno risolti in futuro verranno descritti nella [pagina note sulla versione](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Disponibilità

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Gruppi di disponibilità AlwaysOn

La [disponibilità elevata](../database/high-availability-sla.md) è incorporata in SQL istanza gestita e non può essere controllata dagli utenti. Le istruzioni seguenti non sono supportate:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Clausola [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) dell'istruzione [ALTER database](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Istanza gestita dispone di backup automatici, in modo che gli utenti possano creare backup completi del database `COPY_ONLY` . I backup differenziali, di log e di snapshot di file non sono supportati.

- Con un Istanza gestita SQL è possibile eseguire il backup di un database di istanza solo in un account di archiviazione BLOB di Azure:
  - È supportato solo `BACKUP TO URL`.
  - `FILE`i `TAPE` dispositivi di backup, e non sono supportati.
- La maggior parte delle `WITH` Opzioni generali sono supportate.
  - `COPY_ONLY` è obbligatorio.
  - `FILE_SNAPSHOT` non è supportata.
  - Opzioni nastro: `REWIND` , `NOREWIND` , `UNLOAD` e `NOUNLOAD` non sono supportate.
  - Opzioni specifiche per i log: `NORECOVERY` , `STANDBY` e `NO_TRUNCATE` non sono supportate.

Limitazioni 

- Con un Istanza gestita SQL è possibile eseguire il backup di un database di istanza in un backup con un massimo di 32 striping, che è sufficiente per i database fino a 4 TB se viene utilizzata la compressione dei backup.
- Non è possibile eseguire `BACKUP DATABASE ... WITH COPY_ONLY` in un database crittografato con Transparent Data Encryption gestiti dal servizio (Transparent Service). La crittografia Transparent gestita dal servizio impone la crittografia dei backup con una chiave Transparent Data Encryption. La chiave non può essere esportata, quindi non è possibile ripristinare il backup. Utilizzare i backup automatici e il ripristino temporizzato oppure utilizzare la crittografia [BYOK (Customer-Managed)](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) . È anche possibile disabilitare la crittografia nel database.
- Le dimensioni massime dello striping del backup tramite il `BACKUP` comando in SQL istanza gestita sono 195 GB, ovvero le dimensioni massime del BLOB. Aumentare il numero di strisce nel comando di backup per ridurre la dimensione delle singole strisce e rimanere entro questo limite.

    > [!TIP]
    > Per ovviare a questa limitazione, quando si esegue il backup di un database da SQL Server in un ambiente locale o in una macchina virtuale, è possibile:
    >
    > - Eseguire il backup di `DISK` anziché eseguire il backup in `URL` .
    > - Caricare i file di backup nell'archivio BLOB.
    > - Eseguire il ripristino in SQL Istanza gestita.
    >
    > Il `Restore` comando in SQL istanza gestita supporta dimensioni BLOB maggiori nei file di backup, perché per l'archiviazione dei file di backup caricati viene usato un tipo di BLOB diverso.

Per informazioni sui backup con T-SQL, vedere [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sicurezza

### <a name="auditing"></a>Controllo

Le differenze principali tra il controllo in Microsoft Azure SQL e in SQL Server sono:

- Con SQL Istanza gestita, il controllo funziona a livello di server. I `.xel` file di log vengono archiviati nell'archivio BLOB di Azure.
- Con il database SQL di Azure, il controllo funziona a livello di database. I `.xel` file di log vengono archiviati nell'archivio BLOB di Azure.
- Con SQL Server, in locale o in macchine virtuali, il controllo funziona a livello di server. Gli eventi vengono archiviati nei registri eventi di file system o di Windows.
 
Il controllo XEvent in SQL Istanza gestita supporta le destinazioni di archiviazione BLOB di Azure. I log di file e di Windows non sono supportati.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- Viene fornita una nuova sintassi `TO URL` che è possibile usare per specificare l'URL del contenitore di archiviazione BLOB di Azure in cui `.xel` vengono inseriti i file.
- La sintassi `TO FILE` non è supportata perché SQL istanza gestita non è in grado di accedere alle condivisioni file di Windows.

Per altre informazioni, vedere: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificati

SQL Istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, pertanto si applicano i vincoli seguenti:

- Il `CREATE FROM` / `BACKUP TO` file non è supportato per i certificati.
- Il `CREATE` / `BACKUP` certificato da `FILE` / `ASSEMBLY` non è supportato. I file di chiavi private non possono essere usati. 

Vedere [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Soluzione alternativa** : anziché creare il backup del certificato e ripristinare il backup, [ottenere il contenuto binario e la chiave privata del certificato, archiviarlo come file con estensione SQL e crearlo da binario](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credenziale

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Il Istanza gestita SQL non può accedere ai file, pertanto non è possibile creare i provider di crittografia:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Account di accesso e utenti

- Gli account di accesso SQL creati usando `FROM CERTIFICATE` , `FROM ASYMMETRIC KEY` e `FROM SID` sono supportati. Vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Sono supportate Azure Active Directory (Azure AD) entità server (account di accesso) create con la sintassi [Create Login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) o [Create User FROM login [Azure ad login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) . Questi account di accesso vengono creati a livello di server.

    SQL Istanza gestita supporta Azure AD entità di database con la sintassi `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Questa funzionalità è nota anche come Azure AD utenti del database indipendente.

- Gli account di accesso di Windows creati con la `CREATE LOGIN ... FROM WINDOWS` sintassi non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- Il Azure AD utente che ha creato l'istanza dispone di [privilegi amministrativi senza restrizioni](../database/logins-create-manage.md).
- Gli utenti non amministratori Azure AD a livello di database possono essere creati utilizzando la `CREATE USER ... FROM EXTERNAL PROVIDER` sintassi. Vedi [Crea utente... DA PROVIDER esterno](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD entità server (account di accesso) supportano le funzionalità SQL all'interno di un solo SQL Istanza gestita. Le funzionalità che richiedono l'interazione tra istanze, indipendentemente dal fatto che si trovino nello stesso tenant Azure AD o in tenant diversi, non sono supportate per gli utenti Azure AD. Tra queste funzionalità sono incluse:

  - Replica transazionale di SQL.
  - Server di collegamento.

- L'impostazione di un account di accesso di Azure AD mappato a un gruppo di Azure AD come proprietario del database non è supportata.
- È supportata la rappresentazione di entità Azure AD a livello di server utilizzando altre entità Azure AD, ad esempio la clausola [Execute As](/sql/t-sql/statements/execute-as-transact-sql) . Le limitazioni EXECUTE AS sono:

  - L'opzione EXECUTE AS USER non è supportata per gli utenti di Azure AD quando il nome è diverso dal nome dell'account di accesso. Un esempio è quando l'utente viene creato tramite la sintassi CREATE USER [myAadUser] FROM LOGIN [ john@contoso.com ] e la rappresentazione viene tentata tramite exec come user = _myAadUser_. Quando si crea un **utente** da un'entità di Azure ad server (account di accesso), specificare il user_name come lo stesso Login_name da **login**.
  - Solo le entità a livello di SQL Server (account di accesso) che fanno parte del `sysadmin` ruolo possono eseguire le operazioni seguenti destinate a Azure ad entità:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Per rappresentare un utente con l'istruzione EXECUTE AS, l'utente deve essere mappato direttamente a Azure AD entità server (account di accesso). Gli utenti che sono membri dei gruppi di Azure AD di cui è stato eseguito il mapping in Azure AD entità server non possono essere rappresentati in modo efficace con l'istruzione EXECUTE AS, anche se il chiamante dispone delle autorizzazioni IMPERSONATE per il nome utente specificato.

- L'esportazione/importazione di database con i file BACPAC è supportata per gli utenti Azure AD in SQL Istanza gestita con [SSMS v 18,4 o versioni successive](/sql/ssms/download-sql-server-management-studio-ssms)oppure [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Le configurazioni seguenti sono supportate tramite il file BACPAC del database: 
    - Esportare/importare un database tra diverse istanze di gestione all'interno dello stesso dominio Azure AD.
    - Esportare un database da SQL Istanza gestita e importarlo nel database SQL nello stesso dominio Azure AD. 
    - Esportare un database dal database SQL e importarlo in SQL Istanza gestita nello stesso dominio Azure AD.
    - Esportare un database da SQL Istanza gestita e importarlo in SQL Server (versione 2012 o successiva).
      - In questa configurazione tutti gli utenti Azure AD vengono creati come SQL Server entità di database (utenti) senza account di accesso. Il tipo di utenti è elencato come `SQL` e sono visibili come `SQL_USER` in sys.database_principals). Le autorizzazioni e i ruoli rimangono nei metadati del database SQL Server e possono essere utilizzati per la rappresentazione. Tuttavia, non possono essere usati per accedere e accedere al SQL Server usando le proprie credenziali.

- Solo l'account di accesso dell'entità di livello server, creato dal processo di provisioning di SQL Istanza gestita, i membri dei ruoli del server, ad esempio `securityadmin` o `sysadmin` , o altri account di accesso con autorizzazione ALTER ANY login a livello di server possono creare Azure ad entità server (account di accesso) nel database master per SQL istanza gestita.
- Se l'account di accesso è un'entità di sicurezza SQL, solo gli account di accesso che fanno parte del ruolo `sysadmin` possono usare il comando per creare gli account di accesso per un account Azure AD.
- L'account di accesso Azure AD deve essere un membro di un Azure AD all'interno della stessa directory usata per Istanza gestita SQL di Azure.
- Azure AD entità server (account di accesso) sono visibili Esplora oggetti a partire da SQL Server Management Studio 18,0 Preview 5.
- È consentita la sovrapposizione di entità server (account di accesso) di Azure AD con un account amministratore di Azure AD. Azure AD entità server (account di accesso) hanno la precedenza sull'amministratore Azure AD quando si risolve l'entità e si applicano le autorizzazioni a SQL Istanza gestita.
- Durante l'autenticazione, viene applicata la sequenza seguente per risolvere l'entità di autenticazione:

    1. Se l'account Azure AD esiste come mappato direttamente all'entità di Azure AD server (account di accesso), presente in sys.server_principals come tipo "E", concedere l'autorizzazione di accesso e applicare le autorizzazioni dell'entità di Azure AD server (account di accesso).
    2. Se l'account Azure AD è un membro di un gruppo di Azure AD di cui è stato eseguito il mapping all'entità di Azure AD server (account di accesso), presente in sys.server_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    3. Se l'account Azure AD è uno speciale Azure AD amministratore configurato per il portale per SQL Istanza gestita, che non esiste nelle viste di sistema Istanza gestita SQL, applicare autorizzazioni fisse speciali dell'amministratore Azure AD per SQL Istanza gestita (modalità legacy).
    4. Se l'account Azure AD esiste come mappato direttamente a un utente di Azure AD in un database, presente in sys.database_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'utente del database Azure AD.
    5. Se l'account Azure AD è membro di un gruppo di Azure AD di cui è stato eseguito il mapping a un utente Azure AD in un database, che è presente in sys.database_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo di Azure AD.
    6. Se è presente un account di accesso Azure AD mappato a un account utente Azure AD o a un account di gruppo Azure AD, che viene risolto nell'utente che esegue l'autenticazione, vengono applicate tutte le autorizzazioni da questo Azure AD account di accesso.

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- Il [backup della chiave master](/sql/t-sql/statements/backup-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL).
- Il [ripristino della chiave master](/sql/t-sql/statements/restore-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL).
- Il [backup della chiave master del servizio](/sql/t-sql/statements/backup-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL).
- Il [ripristino della chiave master del servizio](/sql/t-sql/statements/restore-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL).

## <a name="configuration"></a>Configurazione

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- L' [estensione del pool di buffer](/sql/database-engine/configure-windows/buffer-pool-extension) non è supportata.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportata. Vedere [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Regole di confronto

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- I livelli di compatibilità supportati sono 100, 110, 120, 130, 140 e 150.
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
- È previsto un limite di 280 file per ogni istanza per utilizzo generico, che implica un massimo di 280 file per database. I dati e i file di log nel livello per utilizzo generico vengono conteggiati per questo limite. [Il livello business critical supporta 32.767 file per database](./resource-limits.md#service-tier-characteristics).
- Il database non può contenere filegroup contenenti dati FILESTREAM. Il ripristino ha esito negativo se. bak contiene `FILESTREAM` dati. 
- Ogni file viene inserito in Archiviazione BLOB di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file.

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le limitazioni seguenti si applicano a `CREATE DATABASE` :

- Non possono essere definiti file e filegroup. 
- L' `CONTAINMENT` opzione non è supportata. 
- `WITH` le opzioni non sono supportate. 
   > [!TIP]
   > Come soluzione alternativa, usare `ALTER DATABASE` after `CREATE DATABASE` per impostare le opzioni di database per aggiungere file o per impostare l'indipendenza. 

- L' `FOR ATTACH` opzione non è supportata.
- L' `AS SNAPSHOT OF` opzione non è supportata.

Per altre informazioni, vedere [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE - istruzione

Alcune proprietà di file non possono essere impostate o modificate:

- Non è possibile specificare un percorso di file nell' `ALTER DATABASE ADD FILE (FILENAME='path')` istruzione t-SQL. Rimuovere `FILENAME` dallo script perché SQL istanza gestita inserisce automaticamente i file. 
- Non è possibile modificare il nome di un file utilizzando l' `ALTER DATABASE` istruzione.

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

- L'abilitazione e la disabilitazione di SQL Server Agent non sono attualmente supportate nell'istanza gestita di SQL. SQL Agent è sempre in esecuzione.
- SQL Server Agent impostazioni sono di sola lettura. La procedura `sp_set_agent_properties` non è supportata in SQL istanza gestita. 
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
    - La shell comandi non è ancora supportata.
  - SQL Istanza gestita non è in grado di accedere alle risorse esterne, ad esempio le condivisioni di rete tramite Robocopy. 
  - SQL Server Analysis Services non è supportato.
- Le notifiche sono supportate in modo parziale.
- La notifica tramite posta elettronica è supportata, anche se richiede la configurazione di un profilo di Posta elettronica database. SQL Server Agent possibile utilizzare un solo profilo di Posta elettronica database e deve essere chiamato `AzureManagedInstance_dbmail_profile` . 
  - Pager non è supportato.
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.
- EventLog non è supportato.
- Per creare, modificare o eseguire processi di SQL Agent, è necessario che l'utente sia mappato direttamente a Azure AD entità server (account di accesso). Utenti che non sono mappati direttamente, ad esempio gli utenti che appartengono a un gruppo di Azure AD che dispone dei diritti per creare, modificare o eseguire i processi di SQL Agent, non saranno in grado di eseguire tali azioni. Ciò è dovuto a Istanza gestita rappresentazione ed [esecuzione come limitazioni](#logins-and-users).

Attualmente non sono supportate le funzionalità di SQL Agent seguenti:

- Proxy
- Pianificazione di processi in una CPU inattiva
- Abilitazione o disabilitazione di un agente
- Avvisi

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

I tipi di tabella seguenti non sono supportati:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [TABELLA FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Tabella esterna](/sql/t-sql/statements/create-external-table-transact-sql) (polibase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (non supportato solo nel livello per utilizzo generico)

Per informazioni su come creare e modificare le tabelle, vedere [Create Table](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funzionalità

### <a name="bulk-insert--openrowset"></a>Inserimento bulk/OPENROWSET

SQL Istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, quindi i file devono essere importati dall'archivio BLOB di Azure:

- `DATASOURCE` è necessario nel `BULK INSERT` comando quando si importano file dall'archiviazione BLOB di Azure. Vedere [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` è obbligatorio nella `OPENROWSET` funzione quando si legge il contenuto di un file dall'archiviazione BLOB di Azure. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` può essere usato per leggere dati dal database SQL di Azure, da Istanza gestita di Azure SQL o da istanze di SQL Server. Altre origini, ad esempio i database Oracle o i file di Excel, non sono supportate.

### <a name="clr"></a>CLR

Un Istanza gestita SQL non può accedere a condivisioni file e cartelle di Windows, pertanto si applicano i vincoli seguenti:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [create assembly from binary](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` non è supportata. Vedere [creare un assembly da un file](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Posta elettronica database (db_mail)
 - `sp_send_dbmail` Impossibile inviare allegati utilizzando il @file_attachments parametro. Le file system locali e le condivisioni esterne o l'archiviazione BLOB di Azure non sono accessibili da questa procedura.
 - Vedere i problemi noti relativi al `@query` parametro e all'autenticazione.
 
### <a name="dbcc"></a>DBCC

Le istruzioni DBCC non documentate abilitate in SQL Server non sono supportate in SQL Istanza gestita.

- Sono supportati solo un numero limitato di flag di traccia globali. Il livello di sessione `Trace flags` non è supportato. Vedere [flag di traccia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funzionano con il numero limitato di flag di traccia globali.
- Impossibile utilizzare [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) con opzioni REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD perché il database non può essere impostato in `SINGLE_USER` modalità. vedere [differenze di alter database](#alter-database-statement). Un potenziale danneggiamento del database è gestito dal team di supporto di Azure. Se sono presenti indicazioni relative al danneggiamento del database, contattare il supporto tecnico di Azure.

### <a name="distributed-transactions"></a>Transazioni distribuite

Il supporto parziale per [le transazioni distribuite](../database/elastic-transactions-overview.md) è attualmente disponibile in anteprima pubblica. Gli scenari supportati sono:
* Le transazioni in cui i partecipanti sono solo istanze gestite da SQL di Azure che fanno parte del [gruppo di attendibilità del server](./server-trust-group-overview.md).
* Transazioni avviate da .NET (TransactionScope Class) e Transact-SQL.

Azure SQL Istanza gestita attualmente non supporta altri scenari che sono supportati regolarmente da MSDTC in locale o in macchine virtuali di Azure.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per gli eventi estesi (XEvent) non sono supportate:

- La `etw_classic_sync` destinazione non è supportata. Archiviare `.xel` i file nell'archivio BLOB di Azure. Vedere [Destinazione etw_classic_sync_target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- La `event_file` destinazione non è supportata. Archiviare `.xel` i file nell'archivio BLOB di Azure. Vedere [Destinazione event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Librerie esterne

Le librerie esterne in-database R e Python sono supportate in un'anteprima pubblica limitata. Vedere [Machine Learning Services in istanza gestita SQL di Azure (anteprima)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- I dati FILESTREAM non sono supportati.
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

I server collegati in SQL Istanza gestita supportano un numero limitato di destinazioni:

- Le destinazioni supportate sono SQL Istanza gestita, database SQL, Azure sinapsi SQL e istanze di SQL Server. 
- I server collegati non supportano le transazioni scrivibili distribuite (MS DTC).
- Le destinazioni che non sono supportate sono file, Analysis Services e altri RDBMS. Provare a usare l'importazione CSV nativa dall'archiviazione BLOB di Azure usando `BULK INSERT` o `OPENROWSET` come alternativa per l'importazione di file.

Operazioni: 

- Le transazioni di scrittura tra istanze non sono supportate.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La `OPENROWSET` funzione può essere utilizzata per eseguire query solo su istanze SQL Server. Possono essere gestite, locali o in macchine virtuali. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- La `OPENDATASOURCE` funzione può essere utilizzata per eseguire query solo su istanze SQL Server. Possono essere gestite, locali o in macchine virtuali. Solo i `SQLNCLI` `SQLNCLI11` valori, e `SQLOLEDB` sono supportati come provider. Un esempio è `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Non è possibile usare i server collegati per leggere i file (Excel, CSV) dalle condivisioni di rete. Provare a usare [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) o [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) che legge i file CSV dall'archiviazione BLOB di Azure. Tieni traccia delle richieste sull' [elemento feedback di SQL istanza gestita](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

L'unico tipo supportato di origine esterna è RDBMS, per il database SQL di Azure e altri Istanza gestita SQL di Azure. Per informazioni su polibase, vedere [polibase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

- Sono supportati i tipi di replica snapshot e bidirezionali. La replica di tipo merge, la replica peer-to-peer e le sottoscrizioni aggiornabili non sono supportate.
- La [replica transazionale](replication-transactional-overview.md) è disponibile per l'anteprima pubblica in SQL istanza gestita con alcuni vincoli:
    - Tutti i tipi di partecipanti alla replica (server di pubblicazione, server di distribuzione, sottoscrittore pull e Sottoscrittore push) possono essere inseriti in SQL Istanza gestita, ma il server di pubblicazione e il server di distribuzione devono trovarsi sia nel cloud sia in locale.
    - SQL Istanza gestita è in grado di comunicare con le versioni recenti di SQL Server. Per ulteriori informazioni, vedere la [matrice di versioni supportate](replication-transactional-overview.md#supportability-matrix) .
    - La replica transazionale presenta [requisiti di rete aggiuntivi](replication-transactional-overview.md#requirements).

Per ulteriori informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:
- [Replica tra un server di pubblicazione e un Sottoscrittore SQL mi](replication-between-two-instances-configure-tutorial.md)
- [Replica tra un server di pubblicazione SQL MI, un server di distribuzione SQL MI e un Sottoscrittore SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md)

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
  - `FROM URL` (Archiviazione BLOB di Azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportata.
  - I set di backup non sono supportati.
- `WITH` le opzioni non sono supportate. I tentativi di ripristino, tra cui like,, e così `WITH` `DIFFERENTIAL` `STATS` `REPLACE` via, avranno esito negativo.
- `ASYNC RESTORE`: Il ripristino continua anche se la connessione client si interrompe. Se la connessione viene eliminata, è possibile controllare la `sys.dm_operation_status` vista per lo stato di un'operazione di ripristino e per creare ed eliminare un database. Vedere [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Le opzioni di database seguenti sono impostate o sottoposte a override e non possono essere modificate in un secondo momento: 

- `NEW_BROKER` Se il broker non è abilitato nel file con estensione bak. 
- `ENABLE_BROKER` Se il broker non è abilitato nel file con estensione bak. 
- `AUTO_CLOSE=OFF` Se è presente un database nel file con estensione bak `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` Se un database nel file con estensione bak ha `SIMPLE` o `BULK_LOGGED` modalità di ripristino.
- Un filegroup ottimizzato per la memoria viene aggiunto e denominato XTP se non era presente nel file con estensione bak di origine. 
- Qualsiasi filegroup ottimizzato per la memoria esistente viene rinominato in XTP. 
- `SINGLE_USER` le `RESTRICTED_USER` Opzioni e vengono convertite in `MULTI_USER` .

Limitazioni 

- I backup dei database danneggiati potrebbero essere ripristinati a seconda del tipo di danneggiamento, ma i backup automatici non verranno eseguiti finché il danneggiamento non viene risolto. Assicurarsi di eseguire nel `DBCC CHECKDB` istanza gestita SQL di origine e usare il backup per `WITH CHECKSUM` evitare questo problema.
- Il ripristino del `.BAK` file di un database che contiene qualsiasi limitazione descritta in questo documento (ad esempio `FILESTREAM` , `FILETABLE` oggetti o) non può essere ripristinato in SQL istanza gestita.
- `.BAK` non è possibile ripristinare i file che contengono più set di backup. 
- `.BAK` i file che contengono più file di log non possono essere ripristinati.
- Non è possibile ripristinare i backup che contengono database di dimensioni maggiori di 8 TB, oggetti OLTP in memoria attivi o numero di file che superano 280 file per ogni istanza in un'istanza di per utilizzo generico. 
- Non è possibile ripristinare i backup che contengono database di dimensioni maggiori di 4 TB o di oggetti OLTP in memoria con dimensioni totali superiori alle dimensioni descritte nei [limiti delle risorse](resource-limits.md) in business critical istanza.
Per informazioni sulle istruzioni RESTORE, vedere [istruzioni RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Le stesse limitazioni si applicano all'operazione di ripristino temporizzato incorporata. Ad esempio, non è possibile ripristinare per utilizzo generico database di dimensioni maggiori di 4 TB nell'istanza di business critical. Non è possibile ripristinare business critical database con file OLTP in memoria o più di 280 file nell'istanza di per utilizzo generico.

### <a name="service-broker"></a>Service Broker

Il broker di servizio tra istanze non è supportato:

- `sys.routes`: Come prerequisito, è necessario selezionare l'indirizzo da sys. routes. L'indirizzo deve essere locale in ogni route. Vedere [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Non è possibile usare `CREATE ROUTE` con `ADDRESS` diversi da `LOCAL` . Vedere [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Non è possibile usare `ALTER ROUTE` con `ADDRESS` diversi da `LOCAL` . Vedere [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Stored procedure, funzioni e trigger

- `NATIVE_COMPILATION` non è supportato nel livello per utilizzo generico.
- Le opzioni [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` non è supportata. Vedere [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportata. Vedere [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` non sono supportati, che include `sp_addextendedproc` e `sp_dropextendedproc` . Vedere [stored procedure estese](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportate. Vedere [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funzioni e variabili di sistema

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')` Restituisce il valore 8. Questa proprietà identifica in modo univoco un Istanza gestita SQL. Vedere [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` restituisce NULL perché il concetto di istanza esistente per SQL Server non si applica a SQL Istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Restituisce un nome DNS "connettibile" completo, ad esempio my-managed-instance.wcus17662feb9ce98.database.windows.net. Vedere [@ @SERVERNAME ](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Restituisce un nome DNS "connettibile" completo, ad esempio `myinstance.domain.database.windows.net` per le proprietà "Name" e "data_source". Vedere [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` restituisce NULL perché il concetto di servizio esistente per SQL Server non si applica a SQL Istanza gestita. Vedere [@ @SERVICENAME ](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'account di accesso Azure AD non è incluso in sys.sysaccount di accesso. Vedere [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` non è supportata. Vengono restituiti dati errati, ovvero un problema noto temporaneo. Vedere [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Vincoli di ambiente

### <a name="subnet"></a>Subnet
-  Non è possibile inserire altre risorse, ad esempio macchine virtuali, nella subnet in cui è stato distribuito il Istanza gestita SQL. Distribuire queste risorse usando una subnet diversa.
- La subnet deve avere un numero sufficiente di [indirizzi IP](connectivity-architecture-overview.md#network-requirements)disponibili. Il valore minimo è 16, mentre la raccomandazione prevede almeno 32 indirizzi IP nella subnet.
- Gli [endpoint di servizio non possono essere associati alla subnet di SQL istanza gestita](connectivity-architecture-overview.md#network-requirements). Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
- Il numero di Vcore e i tipi di istanze che è possibile distribuire in un'area presentano [vincoli e limiti](resource-limits.md#regional-resource-limitations).
- [Per la subnet è necessario applicare alcune regole di sicurezza](connectivity-architecture-overview.md#network-requirements).

### <a name="vnet"></a>Rete virtuale
- VNet può essere distribuito usando il modello di risorse. il modello classico per VNet non è supportato.
- Dopo la creazione di un Istanza gestita SQL, lo stato di trasferimento di SQL Istanza gestita o VNet a un altro gruppo di risorse o a una sottoscrizione non è supportato.
- Alcuni servizi come gli ambienti del servizio app, le app per la logica e i Istanza gestita SQL (usati per la replica geografica, la replica transazionale o tramite server collegati) non possono accedere a SQL Istanza gestita in aree diverse se i reti virtuali sono connessi tramite il [peering globale](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). È possibile connettersi a queste risorse tramite ExpressRoute o da VNet a VNet tramite gateway VNet.

### <a name="failover-groups"></a>Gruppi di failover
I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Pertanto, gli scenari che dipendono da oggetti dei database di sistema non saranno possibili nell'istanza secondaria, a meno che gli oggetti non vengano creati manualmente sul database secondario.

### <a name="failover-groups"></a>Gruppi di failover
I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Pertanto, gli scenari che dipendono da oggetti dei database di sistema non saranno possibili nell'istanza secondaria, a meno che gli oggetti non vengano creati manualmente sul database secondario.

### <a name="tempdb"></a>TEMPDB

La dimensione massima del file `tempdb` non può essere maggiore di 24 GB per core in un livello per utilizzo generico. Le dimensioni massime di `tempdb` un livello business critical sono limitate dalle dimensioni di archiviazione di SQL istanza gestita. `Tempdb` le dimensioni del file di log sono limitate a 120 GB nel livello per utilizzo generico. Alcune query potrebbero restituire un errore se hanno bisogno di più di 24 GB per core in `tempdb` o se producono più di 120 GB di dati di log.

### <a name="msdb"></a>MSDB

Gli schemi MSDB seguenti in SQL Istanza gestita devono essere di proprietà dei rispettivi ruoli predefiniti:

- Ruoli generali
  - TargetServersRole
- [Ruoli predefiniti del database](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Ruoli DatabaseMail](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Ruoli di Integration Services](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> La modifica dei nomi di ruolo predefiniti, dei nomi degli schemi e dei proprietari dello schema da parte dei clienti influirà sul normale funzionamento del servizio. Tutte le modifiche apportate a questi verranno ripristinate ai valori predefiniti non appena rilevati o al successivo aggiornamento del servizio al più recente per garantire la normale operazione del servizio.

### <a name="error-logs"></a>Log degli errori

SQL Istanza gestita inserisce informazioni dettagliate nei log degli errori. Nel log degli errori di sono stati registrati molti eventi di sistema interni. Utilizzare una procedura personalizzata per leggere i log degli errori che filtrano alcune voci irrilevanti. Per ulteriori informazioni, vedere [sql istanza gestita – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) o [SQL istanza gestita Extension (anteprima)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) per Azure Data Studio.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su SQL Istanza gestita, vedere [che cos'è sql istanza gestita?](sql-managed-instance-paas-overview.md)
- Per un elenco di funzionalità e confronto, vedere [confronto tra le funzionalità di istanza gestita SQL di Azure](../database/features-comparison.md).
- Per gli aggiornamenti della versione e lo stato dei problemi noti, vedere le [Note sulla versione di SQL istanza gestita](../database/doc-changes-updates-release-notes.md)
- Per una guida introduttiva che illustra come creare un nuovo Istanza gestita SQL, vedere [creare una istanza gestita SQL](instance-create-quickstart.md).
