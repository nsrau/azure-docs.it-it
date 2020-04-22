---
title: "Panoramica: Eseguire query sui dati nell'archiviazione con SQL su richiesta (anteprima)"
description: Questa sezione contiene query di esempio che è possibile usare per provare la risorsa SQL su richiesta (anteprima) all'interno di Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421295"
---
# <a name="overview-query-data-in-storage"></a>Panoramica: Eseguire query sui dati nell'archiviazione

Questa sezione contiene query di esempio che è possibile usare per provare la risorsa SQL su richiesta (anteprima) all'interno di Azure Synapse Analytics.
I file attualmente supportati sono: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Prerequisiti

Gli strumenti necessari per eseguire query:

- Client SQL preferito:
    - Azure Synapse Studio (anteprima)
    - Azure Data Studio
    - SQL Server Management Studio

Inoltre, i parametri sono i seguenti:

| Parametro                                 | Descrizione                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Indirizzo dell'endpoint di servizio di SQL su richiesta    | Verrà usato come nome del server.                                   |
| Area dell'endpoint di servizio di SQL su richiesta     | Verrà usato per determinare l'archiviazione usata negli esempi. |
| Nome utente e password per l'accesso all'endpoint | Verranno usati per accedere all'endpoint.                               |
| Database che verrà usato per creare le viste     | Questo database verrà usato come punto di partenza per gli esempi.       |

## <a name="first-time-setup"></a>Prima configurazione

Prima di usare gli esempi inclusi più avanti in questo articolo, completare due passaggi:

- Creare un database per le viste (se si vogliono usare)
- Creare le credenziali che devono essere usate da SQL su richiesta per accedere ai file nell'archiviazione

### <a name="create-database"></a>Creazione del database

Per creare le viste, è necessario un database. Questo database verrà usato in alcune delle query di esempio in questa documentazione.

> [!NOTE]
> I database vengono usati solo per la visualizzazione di metadati, non per i dati effettivi.  Prendere nota del nome del database usato, perché sarà necessario in un secondo momento.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Creare le credenziali

Prima di poter eseguire le query, è necessario creare le credenziali. Queste credenziali verranno usate dal servizio SQL su richiesta per accedere ai file nell'archiviazione.

> [!NOTE]
> Per eseguire correttamente le procedure illustrate in questa sezione, è necessario usare il token di firma di accesso condiviso.
>
> Per iniziare a usare i token di firma di accesso condiviso, è necessario eliminare l'oggetto UserIdentity descritto nell'[articolo](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through) seguente.
>
> Per impostazione predefinita, SQL su richiesta usa sempre il pass-through di AAD.

Per altre informazioni su come gestire il controllo di accesso all'archiviazione, vedere questo [collegamento](develop-storage-files-storage-access-control.md).

> [!WARNING]
> È necessario creare le credenziali per un account di archiviazione che si trova nella stessa area dell'endpoint. Sebbene SQL su richiesta possa accedere ad account di archiviazione di aree diverse, la disponibilità dell'account e dell'endpoint nella stessa area offrirà un'esperienza migliore in termini di prestazioni.

Per creare le credenziali per i contenitori CSV, JSON e Parquet, eseguire il codice seguente:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Dati demo forniti

I dati demo contengono i set di dati seguenti:

- Taxi di New York: record dei viaggi e parte del set di dati pubblico di New York
  - Formato CSV
  - Formato Parquet
- Set di dati sulla popolazione
  - Formato CSV
- File parquet di esempio con colonne annidate
  - Formato Parquet
- Manuali JSON
  - Formato JSON

| Percorso della cartella                                                  | Descrizione                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Cartella padre per i dati in formato CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Cartelle con file di dati della popolazione in formati CSV diversi. |
| /csv/taxi/                                                   | Cartella con i file di dati pubblici di New York in formato CSV              |
| /parquet/                                                    | Cartella padre per i dati in formato Parquet                     |
| /parquet/taxi                                                | File di dati pubblici di New York in formato Parquet, partizionati per anno e mese tramite lo schema di partizionamento Hive/Hadoop. |
| /parquet/nested/                                             | File parquet di esempio con colonne annidate                     |
| /json/                                                       | Cartella padre per i dati in formato JSON                        |
| /json/books/                                                 | File JSON con i dati dei manuali                                   |

## <a name="validation"></a>Convalida

Eseguire le tre query seguenti e verificare se le credenziali sono state create correttamente.

> [!NOTE]
> Tutti gli URI nelle query di esempio usano un account di archiviazione che si trova nell'area Europa settentrionale di Azure. Assicurarsi di aver creato le credenziali appropriate. Eseguire la query seguente e verificare che l'account di archiviazione sia presente nell'elenco.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Se non è possibile trovare le credenziali appropriate, vedere [Prima configurazione](#first-time-setup).

### <a name="sample-query"></a>Query di esempio

L'ultimo passaggio della convalida consiste nell'eseguire la query seguente:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

La query precedente deve restituire questo numero: **8945574**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile continuare con gli articoli seguenti:

- [Eseguire query su un singolo file CSV](query-single-csv-file.md)

- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)

- [Eseguire query su file specifici](query-specific-files.md)

- [Eseguire query su file Parquet](query-parquet-files.md)

- [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md)

- [Eseguire query su file JSON](query-json-files.md)

- [Creazione e uso delle viste](create-use-views.md)
