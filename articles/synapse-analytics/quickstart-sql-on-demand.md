---
title: Uso di SQL su richiesta (anteprima)
description: In questa guida di avvio rapido verrà illustrato come è facile eseguire query su diversi tipi di file tramite SQL su richiesta (anteprima).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 43f361fbaf4ab0462af0a720d7711f219134a165
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692162"
---
# <a name="quickstart-using-sql-on-demand"></a>Guida introduttiva: Uso di SQL su richiesta

Synapse SQL su richiesta (anteprima) è un servizio di query serverless che consente di eseguire le query SQL sui file presenti in Archiviazione di Azure. In questa guida di avvio rapido verrà illustrato come eseguire query su diversi tipi di file tramite SQL su richiesta.

Sono supportati i tipi di file seguenti: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Prerequisiti

Scegliere un client SQL per l'esecuzione delle query:

- [Azure Synapse Studio](quickstart-synapse-studio.md) è uno strumento Web che può essere usato per esplorare i file nell'archiviazione e creare query SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) è uno strumento client che consente di eseguire query SQL e notebook sul database su richiesta.
- [SQL Server Management Studio](sql/get-started-ssms.md) è uno strumento client che consente di eseguire query SQL sul database su richiesta.

Parametri per la guida di avvio rapido:

| Parametro                                 | Descrizione                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Indirizzo dell'endpoint di servizio di SQL su richiesta    | Usato come nome del server                                   |
| Area dell'endpoint di servizio di SQL su richiesta     | Usata per determinare quale archiviazione si userà negli esempi |
| Nome utente e password per l'accesso all'endpoint | Usati per accedere all'endpoint                               |
| Database usato per creare le viste         | Database usato come punto di partenza negli esempi       |

## <a name="first-time-setup"></a>Prima configurazione

Prima di usare gli esempi:

- Creare un database per le viste (se si vogliono usare le viste)
- Creare le credenziali che devono essere usate da SQL su richiesta per accedere ai file nell'archiviazione

### <a name="create-database"></a>Creazione del database

Creare un database personalizzato a scopo dimostrativo. Questo database verrà usato per creare le viste e per le query di esempio in questo articolo.

> [!NOTE]
> I database vengono usati solo per visualizzare i metadati, non i dati effettivi.
>Prendere nota del nome del database per usarlo successivamente nella guida di avvio rapido.

Usare la query seguente, sostituendo `mydbname` con un nome a scelta:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Creare le credenziali

Per eseguire query con SQL su richiesta, creare le credenziali per SQL su richiesta da usare per accedere ai file nell'archiviazione.

> [!NOTE]
> Per eseguire correttamente gli esempi illustrati in questa sezione, è necessario usare un token di firma di accesso condiviso.
>
> Per iniziare a usare i token di firma di accesso condiviso, è necessario eliminare l'oggetto UserIdentity descritto nell'[articolo](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through) seguente.
>
> Per impostazione predefinita, SQL su richiesta usa sempre il pass-through di AAD.

Per altre informazioni su come gestire il controllo di accesso alle risorse di archiviazione, vedere l'articolo[Controllare l'accesso all'account di archiviazione per SQL su richiesta](sql/develop-storage-files-storage-access-control.md).

Eseguire il frammento di codice seguente per creare le credenziali usate negli esempi di questa sezione:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Esecuzione di query sui file CSV

L'immagine seguente è un'anteprima del file su cui eseguire le query:

![Prime 10 righe del file CSV senza intestazione, nuova riga in stile Windows.](./sql/media/query-single-csv-file/population.png)

La query seguente mostra come leggere un file CSV che non contiene una riga di intestazione, con il carattere di nuova riga in stile Windows e colonne delimitate da virgole:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

È possibile specificare lo schema in fase di compilazione della query.
Per altri esempi, vedere [Eseguire query su file CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Esecuzione di query su file Parquet

L'esempio seguente mostra le funzionalità di inferenza di schema automatica per l'esecuzione di query sui file Parquet. Restituisce il numero di righe per settembre 2017 senza specificare lo schema.

> [!NOTE]
> Non è necessario specificare le colonne nella clausola `OPENROWSET WITH` durante la lettura dei file Parquet. In questo caso, SQL su richiesta usa i metadati del file Parquet e associa le colonne in base al nome.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Vedere altre informazioni sull'[esecuzione di query su file Parquet](sql/query-parquet-files.md).

## <a name="querying-json-files"></a>Esecuzione di query sui file JSON

### <a name="json-sample-file"></a>File di esempio JSON

I file sono archiviati nella cartella *books* del contenitore *json* e contengono voci di singoli libri con la struttura seguente:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="querying-json-files"></a>Esecuzione di query sui file JSON

La query seguente mostra come usare [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare valori scalari (titolo, editore) da un libro intitolato *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> L'intero file JSON viene letto come singola riga/colonna, quindi FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR sono impostati su 0x0b perché non è previsto trovarli nel file.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile continuare con gli articoli seguenti:

- [Eseguire query su un singolo file CSV](sql/query-single-csv-file.md)
- [Eseguire query su cartelle e più file CSV](sql/query-folders-multiple-csv-files.md)
- [Eseguire query su file specifici](sql/query-specific-files.md)
- [Eseguire query su file Parquet](sql/query-parquet-files.md)
- [Eseguire query su tipi annidati di Parquet](sql/query-parquet-nested-types.md)
- [Eseguire query su file JSON](sql/query-json-files.md)
- [Creazione e uso delle viste](sql/create-use-views.md)
- [Creazione e uso di tabelle esterne](sql/create-use-external-tables.md)
- [Archiviare i risultati delle query in Archiviazione di Azure](sql/create-external-table-as-select.md)
- [Eseguire query su un singolo file CSV](sql/query-single-csv-file.md)
