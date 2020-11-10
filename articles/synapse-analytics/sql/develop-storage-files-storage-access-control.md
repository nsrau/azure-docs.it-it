---
title: Controllare l'accesso agli account di archiviazione per il pool SQL serverless (anteprima)
description: Informazioni sul modo in cui il pool SQL serverless (anteprima) accede ad Archiviazione di Azure e su come sia possibile controllare l'accesso all'archiviazione per il pool SQL serverless in Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 958f371a0018d20331e73d0eabba9354614d121c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315731"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Controllare l'accesso agli account di archiviazione per il pool SQL serverless (anteprima) in Azure Synapse Analytics

Una query del pool SQL serverless legge i file direttamente da Archiviazione di Azure. Le autorizzazioni per accedere ai file in Archiviazione di Azure sono controllate a due livelli:
- **Livello di archiviazione** : l'utente deve disporre dell'autorizzazione per accedere ai file di archiviazione sottostanti. L'amministratore di archiviazione deve consentire all'entità di Azure AD di leggere/scrivere file o generare una chiave di firma di accesso condiviso che verrà usata per accedere all'archiviazione.
- **Livello di servizio SQL** : l'utente deve disporre dell'autorizzazione `SELECT` per leggere i dati da una [tabella esterna](develop-tables-external-tables.md) o dell'autorizzazione `ADMINISTER BULK ADMIN` per eseguire `OPENROWSET` oltre all'autorizzazione per l'uso delle credenziali per accedere all'archiviazione.

Questo articolo descrive i tipi di credenziali che è possibile usare e il modo in cui viene eseguita la ricerca di credenziali per gli utenti di SQL e Azure AD.

## <a name="supported-storage-authorization-types"></a>Tipi di autorizzazione supportati per l'archiviazione

Un utente connesso a un pool SQL serverless deve essere autorizzato ad accedere e a eseguire query sui file di Archiviazione di Azure se non sono disponibili pubblicamente. È possibile usare tre tipi di autorizzazione per accedere all’archiviazione non pubblica: [Identità utente](?tabs=user-identity), [Firma di accesso condiviso](?tabs=shared-access-signature) e [Identità gestita](?tabs=managed-identity).

> [!NOTE]
> **Pass-through di Azure AD** è il comportamento predefinito quando si crea un'area di lavoro.

### <a name="user-identity"></a>[Identità utente](#tab/user-identity)

L' **identità utente** , anche nota come "pass-through di Azure AD", è un tipo di autorizzazione per cui per autorizzare l'accesso ai dati viene usata l'identità dell'utente di Azure AD connesso al pool SQL serverless. Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'utente di Azure AD. Come indicato nella tabella seguente, il tipo di utente SQL non è supportato.

> [!IMPORTANT]
> Per accedere ai dati con l'identità utente, è necessario avere il ruolo di proprietario/collaboratore/lettore dei dati dei BLOB di archiviazione.
> Anche se si è proprietari di un account di archiviazione, è comunque necessario aggiungere se stessi in uno dei ruoli dei dati dei BLOB di archiviazione.
>
> Per altre informazioni sul controllo di accesso in Azure Data Lake Store Gen2, vedere l'articolo [Controllo di accesso in Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

### <a name="shared-access-signature"></a>[Firma di accesso condiviso](#tab/shared-access-signature)

**Firma di accesso condiviso** : fornisce accesso delegato alle risorse in un account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse in un account di archiviazione, senza condividere le chiavi dell'account. La firma di accesso condiviso offre un controllo granulare sul tipo di accesso concesso ai client a cui viene assegnata, tra cui intervallo di validità, autorizzazioni concesse, intervallo di indirizzi IP accettabile e protocollo accettabile (HTTPS/HTTP).

Per ottenere un token di firma di accesso condiviso, passare a **Portale di Azure -> Account di archiviazione -> Firma di accesso condiviso -> Configurazione autorizzazioni -> Genera firma di accesso condiviso e stringa di connessione.**

> [!IMPORTANT]
> Quando viene generato, il token di firma di accesso condiviso è preceduto da un punto interrogativo ('?'). Per usare il token nel pool SQL serverless, è necessario rimuovere il punto interrogativo ('?') quando si creano le credenziali. Ad esempio:
>
> Token di firma di accesso condiviso: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Per abilitare l'accesso tramite il token di firma di accesso condiviso, è necessario creare una credenziale con ambito database o con ambito server 

### <a name="managed-identity"></a>[Identità gestita](#tab/managed-identity)

**Identità gestita** : nota anche come MSI, è una funzionalità di Azure Active Directory (Azure AD) che fornisce i servizi di Azure per il pool SQL serverless. Inoltre, distribuisce un'identità automaticamente gestita in Azure AD. Questa identità può essere usata per autorizzare la richiesta di accesso ai dati in Archiviazione di Azure.

Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'identità gestita. La concessione di autorizzazioni all'identità gestita viene eseguita allo stesso modo che per qualsiasi altro utente di Azure AD.

### <a name="anonymous-access"></a>[Accesso anonimo](#tab/public-access)

È possibile accedere ai file disponibili pubblicamente presenti negli account di archiviazione di Azure che [consentono l'accesso anonimo](/azure/storage/blobs/storage-manage-access-to-resources).

---

### <a name="supported-authorization-types-for-databases-users"></a>Tipi di autorizzazione supportati per gli utenti di database

Nella tabella seguente è possibile trovare i tipi di autorizzazione disponibili:

| Tipo di autorizzazione                    | *Utente SQL*    | *Utente di Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identità utente](?tabs=user-identity#supported-storage-authorization-types)       | Non supportate | Supportato      |
| [Firma di accesso condiviso](?tabs=shared-access-signature#supported-storage-authorization-types)       | Supportato     | Supportato      |
| [Identità gestita](?tabs=managed-identity#supported-storage-authorization-types) | Non supportate | Supportato      |

### <a name="supported-storages-and-authorization-types"></a>Tipi di autorizzazione e archiviazione supportati

È possibile usare le combinazioni seguenti di tipi di autorizzazione e tipi di archiviazione di Azure:

| Tipo di autorizzazione  | Archiviazione BLOB   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [Firma di accesso condiviso](?tabs=shared-access-signature#supported-storage-authorization-types)    | Supportato\*      | Non supportato   | Supportato\*     |
| [Identità gestita](?tabs=managed-identity#supported-storage-authorization-types) | Supportato      | Supportato        | Funzionalità supportata     |
| [Identità utente](?tabs=user-identity#supported-storage-authorization-types)    | Funzionalità supportata\*      | Funzionalità supportata\*        | Funzionalità supportata\*     |

\* È possibile usare un token di firma di accesso condiviso e l'identità di Azure AD per accedere a spazio di archiviazione non protetto con firewall.

> [!IMPORTANT]
> Quando si accede a uno spazio di archiviazione protetto da firewall, è possibile usare solo l'identità gestita. È necessario usare l'impostazione [Consenti servizi Microsoft attendibili](../../storage/common/storage-network-security.md#trusted-microsoft-services) e [assegnare un ruolo di Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) in modo esplicito all'[identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/overview.md) per tale istanza della risorsa. In questo caso l'ambito di accesso dell'istanza corrisponde al ruolo di Azure assegnato all'identità gestita.
>

## <a name="credentials"></a>Credenziali

Per eseguire query su un file in Archiviazione di Azure, l'endpoint del pool SQL serverless deve avere credenziali che contengono le informazioni di autenticazione. Vengono usati due tipi di credenziali:
- Le credenziali a livello di server vengono usate per le query ad hoc eseguite con la funzione `OPENROWSET`. Il nome della credenziale deve corrispondere all'URL di archiviazione.
- Le credenziali con ambito database vengono usate per le tabelle esterne. La tabella esterna fa riferimento a `DATA SOURCE` con le credenziali che devono essere usate per accedere all'archiviazione.

Per consentire a un utente di creare o eliminare una credenziale, l'amministratore può concedere, negare o alterare qualsiasi credenziale:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Gli utenti del database che accedono all'archiviazione esterna devono disporre delle autorizzazioni per usare le credenziali.

### <a name="grant-permissions-to-use-credential"></a>Concedere le autorizzazioni per l'uso delle credenziali

Per usare le credenziali, l'utente deve disporre dell'autorizzazione `REFERENCES` per una credenziale specifica. Per concedere un'autorizzazione `REFERENCES` a un utente specifico per una credenziale specifica, eseguire:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Per garantire un'esperienza uniforme con il pass-through di Azure AD, per impostazione predefinita tutti gli utenti hanno il diritto per l'uso della credenziale `UserIdentity`.

## <a name="server-scoped-credential"></a>Credenziali con ambito server

Le credenziali con ambito server vengono usate quando l'account di accesso SQL chiama la funzione `OPENROWSET` senza `DATA_SOURCE` per leggere i file in un account di archiviazione. Il nome delle credenziali con ambito server **deve** corrispondere all'URL di archiviazione di Azure. Per aggiungere una credenziale, eseguire [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Sarà necessario specificare un argomento CREDENTIAL NAME, che deve corrispondere a una parte o all'intero percorso dei dati in Archiviazione (vedere di seguito).

> [!NOTE]
> L'argomento `FOR CRYPTOGRAPHIC PROVIDER` non è supportato.

Il nome delle credenziali a livello di server deve corrispondere al percorso completo dell'account di archiviazione (e facoltativamente al contenitore) nel formato seguente: `<prefix>://<storage_account_path>/<storage_path>`. I percorsi degli account di archiviazione sono descritti nella tabella seguente:

| Origine dati esterna       | Prefisso | Percorso dell'account di archiviazione                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Archiviazione BLOB di Azure         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

Le credenziali con ambito server consentono l'accesso ad Archiviazione di Azure usando i tipi di autenticazione seguenti:

### <a name="user-identity"></a>[Identità utente](#tab/user-identity)

Gli utenti di Azure AD possono accedere a qualsiasi file nell'archiviazione di Azure se hanno il ruolo `Storage Blob Data Owner`, `Storage Blob Data Contributor` o `Storage Blob Data Reader`. Gli utenti di Azure AD non necessitano di credenziali per accedere all'archiviazione. 

Gli utenti di SQL non possono usare l'autenticazione di Azure AD per accedere all'archiviazione.

### <a name="shared-access-signature"></a>[Firma di accesso condiviso](#tab/shared-access-signature)

Lo script seguente crea una credenziale a livello di server che può essere usata dalla funzione `OPENROWSET` per accedere a qualsiasi file in Archiviazione di Azure tramite il token di firma di accesso condiviso. Creare questa credenziale per abilitare l'entità SQL che esegue la funzione `OPENROWSET` alla lettura di file protetti con la chiave di firma di accesso condiviso in Archiviazione di Azure che corrisponde all'URL nel nome della credenziale.

Sostituire < *mystorageaccountname* > con il nome dell'account di archiviazione effettivo e < *mystorageaccountcontainername* > con il nome del contenitore effettivo:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Identità gestita](#tab/managed-identity)

Lo script seguente crea una credenziale a livello di server che può essere usata dalla funzione `OPENROWSET` per accedere a qualsiasi file in Archiviazione di Azure con l'identità gestita dell'area di lavoro.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Accesso pubblico](#tab/public-access)

Le credenziali con ambito database non sono necessarie per consentire l'accesso a file disponibili pubblicamente. Creare un'[origine dati senza credenziali con ambito database](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) per accedere ai file disponibili pubblicamente in Archiviazione di Azure.

---

## <a name="database-scoped-credential"></a>Credenziali con ambito database

Le credenziali con ambito database vengono usate quando un'entità chiama la funzione `OPENROWSET` con `DATA_SOURCE` o seleziona i dati da una [tabella esterna](develop-tables-external-tables.md) che non ha accesso ai file pubblici. Le credenziali con ambito database non devono corrispondere al nome dell'account di archiviazione. Verranno usate in modo esplicito nell'ORIGINE DATI che definisce la posizione di archiviazione.

Le credenziali con ambito database consentono l'accesso ad Archiviazione di Azure usando i tipi di autenticazione seguenti:

### <a name="azure-ad-identity"></a>[Identità di Azure AD](#tab/user-identity)

Gli utenti di Azure AD possono accedere a qualsiasi file nell'archiviazione di Azure se hanno almeno il ruolo `Storage Blob Data Owner`, `Storage Blob Data Contributor` o `Storage Blob Data Reader`. Gli utenti di Azure AD non necessitano di credenziali per accedere all'archiviazione.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Gli utenti di SQL non possono usare l'autenticazione di Azure AD per accedere all'archiviazione.

### <a name="shared-access-signature"></a>[Firma di accesso condiviso](#tab/shared-access-signature)

Lo script seguente crea una credenziale usata per accedere ai file nell'archivio tramite il token di firma di accesso condiviso specificato nella credenziale. Lo script creerà un'origine dati esterna di esempio che usa questo token di firma di accesso condiviso per accedere alla risorsa di archiviazione.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Identità gestita](#tab/managed-identity)

Lo script seguente crea una credenziale con ambito database che può essere usata per una rappresentazione dell'utente di Azure AD attuale come identità gestita del servizio. Lo script creerà un'origine dati esterna di esempio che usa l'identità dell'area di lavoro per accedere alla risorsa di archiviazione.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Le credenziali con ambito database non devono corrispondere al nome dell'account di archiviazione perché verranno usate in modo esplicito nell'origine dati che definisce il percorso di archiviazione.

### <a name="public-access"></a>[Accesso pubblico](#tab/public-access)

Le credenziali con ambito database non sono necessarie per consentire l'accesso a file disponibili pubblicamente. Creare un'[origine dati senza credenziali con ambito database](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) per accedere ai file disponibili pubblicamente in Archiviazione di Azure.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Le credenziali con ambito database vengono usate nelle origini dati esterne per specificare il metodo di autenticazione che verrà usato per accedere a questa risorsa di archiviazione:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Esempi

### <a name="access-a-publicly-available-data-source"></a>**Accedere a un'origine dati disponibile pubblicamente**

Usare lo script seguente per creare una tabella che accede all'origine dati disponibile pubblicamente.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

L'utente del database può leggere il contenuto dei file dall'origine dati usando una tabella esterna o la funzione [OPENROWSET](develop-openrowset.md) che fa riferimento all'origine dati:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Accedere a un'origine dati tramite credenziali**

Modificare lo script seguente per creare una tabella esterna che accede ad Archiviazione di Azure tramite il token di firma di accesso condiviso, l'identità di Azure AD dell'utente o l'identità gestita dell'area di lavoro.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

L'utente del database può leggere il contenuto dei file dall'origine dati usando una [tabella esterna](develop-tables-external-tables.md) o la funzione [OPENROWSET](develop-openrowset.md) che fa riferimento all'origine dati:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire query su diversi tipi di file e cartelle e su come creare e usare le viste, vedere gli articoli seguenti:

- [Eseguire query su un singolo file CSV](query-single-csv-file.md)
- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)
- [Eseguire query su file specifici](query-specific-files.md)
- [Eseguire query su file Parquet](query-parquet-files.md)
- [Creare e usare viste](create-use-views.md)
- [Eseguire query su file JSON](query-json-files.md)
- [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md)
