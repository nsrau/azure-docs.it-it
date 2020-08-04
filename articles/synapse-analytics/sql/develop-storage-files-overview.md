---
title: Accedere ai file di archiviazione in SQL su richiesta (anteprima)
description: Questo articolo descrive come eseguire query sui file di archiviazione con le risorse di SQL su richiesta (anteprima) all'interno di Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d7f990b059346c4c782ca923e663997317c4df16
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046879"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>Accedere alle risorse di archiviazione esterne in Synapse SQL (su richiesta)

Questo documento descrive in che modo gli utenti possono eseguire la lettura dei dati dai file archiviati in Archiviazione di Azure in Synapse SQL (su richiesta). Per accedere alle risorse di archiviazione, sono disponibili le opzioni seguenti:

- Funzione [OPENROWSET](develop-openrowset.md) che abilita l'esecuzione di query ad hoc sui file in Archiviazione di Azure.
- [Tabella esterna](develop-tables-external-tables.md) che è una struttura di dati predefinita basata su un set di file esterni.

L'utente può usare [metodi di autenticazione diversi](develop-storage-files-storage-access-control.md) ad esempio l'autenticazione pass-through di Azure AD (impostazione predefinita per le entità di sicurezza di Azure AD) e l'autenticazione tramite firma di accesso condiviso (impostazione predefinita per le entità di sicurezza SQL).

## <a name="openrowset"></a>OPENROWSET

La funzione [OPENROWSET](develop-openrowset.md) consente all'utente di eseguire la lettura dei file dall'archiviazione di Azure.

### <a name="query-files-using-openrowset"></a>Eseguire query sui file con OPENROWSET

OPENROWSET consente agli utenti di eseguire query sui file esterni nell'archiviazione di Azure se sono autorizzati ad accedere alle risorse di archiviazione. Per eseguire la lettura del contenuto dei file nell'archiviazione di Azure, l'utente connesso all'endpoint Synapse SQL su richiesta dovrà usare la query seguente:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

L'utente può accedere all'archiviazione usando le regole di accesso seguenti:

- Utente di Azure AD: OPENROWSET userà l'identità di Azure AD del chiamante per accedere all'archiviazione di Azure o accederà all'archiviazione con l'accesso anonimo.
- Utente di SQL: OPENROWSET accederà all'archiviazione con l'accesso anonimo.

Le entità di sicurezza di SQL possono inoltre usare OPENROWSET per eseguire direttamente query sui file protetti tramite token di firma di accesso condiviso o l'identità gestita dell'area di lavoro. Se un utente di SQL esegue questa funzione, un utente Power User con autorizzazione `ALTER ANY CREDENTIAL` deve creare credenziali con ambito server corrispondenti all'URL nella funzione (usando il nome e il contenitore di archiviazione) e concedere l'autorizzazione REFERENCES per tali credenziali al chiamante della funzione OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Se non sono presenti credenziali a livello di server corrispondenti all'URL oppure l'utente di SQL non dispone dell'autorizzazione REFERENCES per queste credenziali, verrà restituito un errore. Le entità di sicurezza di SQL non possono essere rappresentate da un'identità di Azure AD.

> [!NOTE]
> Questa versione di OPENROWSET è progettata per l'esplorazione rapida e semplice tramite l'autenticazione predefinita. Per sfruttare la rappresentazione o l'identità gestita, usare OPENROWSET con DATASOURCE, come descritto nella sezione successiva.

### <a name="querying-data-sources-using-openrowset"></a>Esecuzione di query sulle origini dati con OPENROWSET

OPENROWSET consente all'utente di eseguire query sui file posizionati in un'origine dati esterna:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

L'utente Power User con l'autorizzazione CONTROL DATABASE deve creare un oggetto DATABASE SCOPED CREDENTIAL che verrà usato per accedere all'archiviazione e all'oggetto EXTERNAL DATA SOURCE che specifica l'URL dell'origine dati e le credenziali da usare:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

L'autorizzazione DATABASE SCOPED CREDENTIAL specifica come accedere ai file nell'origine dati a cui si fa riferimento (attualmente firma di accesso condiviso e identità gestita).

Il chiamante deve disporre di una delle autorizzazioni seguenti per eseguire la funzione OPENROWSET:

- Una delle autorizzazioni seguenti per eseguire OPENROWSET:
  - `ADMINISTER BULK OPERATIONS` consente di accedere per eseguire la funzione OPENROWSET.
  - `ADMINISTER DATABASE BULK OPERATIONS` consente all'utente con ambito database di eseguire la funzione OPENROWSET.
- L'autorizzazione REFERENCES DATABASE SCOPED CREDENTIAL per le credenziali a cui viene fatto riferimento in EXTERNAL DATA SOURCE

#### <a name="accessing-anonymous-data-sources"></a>Accesso a origini dati anonime

L'utente può creare un oggetto EXTERNAL DATA SOURCE senza credenziali che farà riferimento a una risorsa di archiviazione con accesso pubblico OPPURE usare l'autenticazione pass-through di Azure AD:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```

## <a name="external-table"></a>EXTERNAL TABLE

L'utente con le autorizzazioni per la lettura della tabella può accedere ai file esterni usando un oggetto EXTERNAL TABLE creato nei file e nelle cartelle di archiviazione di Azure.

L'utente con [autorizzazioni per la creazione della tabella esterna](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions), ad esempio CREATE TABLE e ALTER ANY CREDENTIAL o REFERENCES DATABASE SCOPED CREDENTIAL, può usare lo script seguente per creare una tabella nell'origine dati di archiviazione di Azure:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

L'utente con l'autorizzazione CONTROL DATABASE deve creare credenziali di tipo DATABASE SCOPED CREDENTIAL che verranno usate per accedere all'archiviazione e all'origine EXTERNAL DATA SOURCE che specifica l'URL dell'origine dati e le credenziali da usare:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

L'autorizzazione DATABASE SCOPED CREDENTIAL specifica come accedere ai file nell'origine dati a cui si fa riferimento.

### <a name="reading-external-files-with-external-table"></a>Lettura di file esterni con EXTERNAL TABLE

EXTERNAL TABLE consente di eseguire la lettura dei dati dai file a cui viene fatto riferimento tramite l'origine dati usando l'istruzione SQL SELECT standard:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Il chiamante deve disporre delle autorizzazioni seguenti per la lettura dei dati:
- Autorizzazione `SELECT` per la tabella esterna
- Autorizzazione `REFERENCES DATABASE SCOPED CREDENTIAL` se `DATA SOURCE` contiene `CREDENTIAL`

## <a name="permissions"></a>Autorizzazioni

Nella tabella seguente sono elencate le autorizzazioni necessarie per le operazioni indicate in precedenza.

| Query | Autorizzazioni necessarie|
| --- | --- |
| OPENROWSET(BULK) senza origine dati | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` o l'account di accesso SQL deve avere REFERENCES CREDENTIAL::\<URL> per l'archiviazione protetta tramite firma di accesso condiviso |
| OPENROWSET(BULK) con origine dati senza credenziali | `ADMINISTER BULK OPERATIONS` o `ADMINISTER DATABASE BULK OPERATIONS` |
| OPENROWSET(BULK) con origine dati con credenziali | `REFERENCES DATABASE SCOPED CREDENTIAL` e un'autorizzazione tra `ADMINISTER BULK OPERATIONS` o `ADMINISTER DATABASE BULK OPERATIONS` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` e `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Per creare la tabella: `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` e `ALTER ANY EXTERNAL FILE FORMAT`. Per la lettura dei dati: `ADMINISTER BULK OPERATIONS` o `REFERENCES CREDENTIAL` o `SELECT TABLE` per ogni tabella/vista/funzione nella query + autorizzazione di lettura/scrittura per l'archiviazione |

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile continuare con gli articoli seguenti:

- [Eseguire query sui dati nell'archiviazione](query-data-storage.md)

- [Eseguire query su file CSV](query-single-csv-file.md)

- [Eseguire query su cartelle e più file](query-folders-multiple-csv-files.md)

- [Eseguire query su file specifici](query-specific-files.md)

- [Eseguire query su file Parquet](query-parquet-files.md)

- [Eseguire query sui tipi annidati](query-parquet-nested-types.md)

- [Eseguire query su file JSON](query-json-files.md)

- [Creazione e uso delle viste](create-use-views.md)
