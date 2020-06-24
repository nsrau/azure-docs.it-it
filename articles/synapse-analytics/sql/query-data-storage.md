---
title: "Panoramica: Eseguire query sui dati nell'archiviazione con SQL su richiesta (anteprima)"
description: Questa sezione contiene query di esempio che è possibile usare per provare la risorsa SQL su richiesta (anteprima) all'interno di Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d42dac0899c5ab9c4f817df40095dd08891fb8e7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207703"
---
# <a name="overview-query-data-in-storage"></a>Panoramica: Eseguire query sui dati nell'archiviazione

Questa sezione contiene query di esempio che è possibile usare per provare la risorsa SQL su richiesta (anteprima) all'interno di Azure Synapse Analytics.
I formati di file attualmente supportati sono:  
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

Il primo passaggio consiste nel **creare un database** in cui verranno eseguite le query. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati per leggere i dati in questi esempi.

> [!NOTE]
> I database vengono usati solo per la visualizzazione di metadati, non per i dati effettivi.  Prendere nota del nome del database usato, perché sarà necessario in un secondo momento.

```sql
CREATE DATABASE mydbname;
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

### <a name="sample-query"></a>Query di esempio

L'ultimo passaggio della convalida consiste nell'eseguire la query seguente:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
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
