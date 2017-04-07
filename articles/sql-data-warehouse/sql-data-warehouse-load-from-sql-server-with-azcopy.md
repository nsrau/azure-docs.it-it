---
title: Caricare dati da SQL Server in Azure SQL Data Warehouse (PolyBase) | Documentazione Microsoft
description: Uso di bcp per esportare dati da SQL Server a file flat, di AZCopy per importare dati nell&quot;archivio BLOB di Azure e di PolyBase per inserire i dati in Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/22/2017



---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Caricare dati da SQL Server in Azure SQL Data Warehouse (AZCopy)
Usare le utilità della riga di comando bcp e AZCopy per caricare dati da SQL Server all'archivio BLOB di Azure. Usare quindi PolyBase o Azure Data Factory per caricare i dati in Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un database di SQL Data Warehouse
* Utilità della riga di comando bcp installata
* Utilità della riga di comando SQLCMD installata

> [!NOTE]
> È possibile scaricare le utilità bcp e sqlcmd dall'[Area download Microsoft][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importare i dati in SQL Data Warehouse
In questa esercitazione verrà creata una tabella in Azure SQL Data Warehouse e verranno importati dati nella tabella.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Passaggio 1: Creare una tabella in Azure SQL Data Warehouse
Al prompt dei comandi usare sqlcmd per eseguire la query seguente per creare una tabella nell'istanza:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> Per altre informazioni sulla creazione di una tabella in SQL Data Warehouse e sulle opzioni disponibili con la clausola WITH, vedere [Panoramica delle tabelle][Table Overview] o la [sintassi di CREATE TABLE][CREATE TABLE syntax].
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Passaggio 2: Creare un file di dati di origine
Aprire il Blocco note, copiare le righe di dati seguenti in un nuovo file di testo e quindi salvare il file nella directory temporanea locale, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> È importante ricordare che bcp.exe non supporta la codifica UTF-8 del file. Usare i file ASCII o con codifica UTF-16 quando si usa bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Passaggio 3: Connettersi e importare i dati
bcp permette di connettersi e importare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

È possibile verificare che i dati siano stati caricati eseguendo la query seguente con sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Dovrebbero essere visualizzati i risultati seguenti:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passaggio 4: creare le statistiche sui dati appena caricati
SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][Statistics] nel gruppo di argomenti sullo sviluppo. Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.

Da un prompt di sqlcmd, eseguire le istruzioni CREATE STATISTICS seguenti:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Esportare i dati da SQL Data Warehouse
In questa esercitazione verrà creato un file di dati da una tabella in SQL Data Warehouse. I dati creati in precedenza verranno esportati in un nuovo file denominato DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Passaggio 1: Esportare i dati
L'utilità bcp permette di connettersi ed esportare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Per verificare che i dati siano stati esportati correttamente, aprire il nuovo file. I dati del file devono corrispondere al testo seguente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> A causa della natura dei sistemi distribuiti, è possibile che l'ordine dei dati non sia uguale nei database di SQL Data Warehouse. Un'altra opzione consiste nell'usare la funzione **queryout** di bcp per scrivere un estratto di query invece di esportare l'intera tabella.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse][Load data into SQL Data Warehouse].
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

