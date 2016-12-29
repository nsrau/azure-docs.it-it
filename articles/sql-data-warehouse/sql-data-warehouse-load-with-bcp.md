---
title: Usare bcp per caricare i dati in SQL Data Warehouse | Documentazione Microsoft
description: Informazioni su bcp e sul relativo uso per scenari di data warehousing.
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5a0519a0fdbfc428530e5b30a3f8e4e8bcdaa50b


---
# <a name="load-data-with-bcp"></a>Caricare i dati con BCP
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** è un'utilità di caricamento bulk da riga di comando che permette di copiare dati tra SQL Server, file di dati e SQL Data Warehouse. Usare bcp per importare numeri elevati di righe nelle tabelle di SQL Data Warehouse o per esportare i dati dalle tabelle di SQL Server ai file di dati. bcp richiede competenze in ambito di Transact-SQL solo quando viene usato con l'opzione queryout.

bcp costituisce un modo semplice e rapido per spostare set di dati di dimensioni ridotte da e verso un database di SQL Data Warehouse. La quantità esatta di dati che è consigliabile caricare/estrarre tramite bcp dipenderà dalla connessione di rete per il data center di Azure.  In genere, le tabelle delle dimensioni possono essere caricate ed estratte facilmente con bcp, tuttavia bcp non è consigliato per il caricamento o l'estrazione di grandi volumi di dati.  Lo strumento consigliato per il caricamento e l'estrazione di grandi volumi di dati è Polybase, perché offre risultati migliori con l'architettura MPP (Massively Parallel Processing, elaborazione parallela massiva) di SQL Data Warehouse.

Con bcp è possibile:

* Utilizzare una semplice utilità della riga di comando per caricare dati nell’SQL Data Warehouse.
* Utilizzare una semplice utilità della riga di comando per estrarre dati dall’SQL Data Warehouse.

In questa esercitazione verranno illustrate le attività seguenti:

* Importare dati in una tabella tramite l'utilità bcp nel comando
* Esportare dati da una tabella tramite l'utilità bcp dal comando

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un database di SQL Data Warehouse
* Utilità della riga di comando bcp installata
* Utilità della riga di comando SQLCMD installata

> [!NOTE]
> È possibile scaricare le utilità bcp e sqlcmd dall' [Area download Microsoft][Area download Microsoft].
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
> Per altre informazioni sulla [creazione di una tabella][creazione di una tabella] in SQL Data Warehouse e sulle opzioni disponibili con la clausola WITH, vedere la panoramica delle tabelle o la [sintassi di CREATE TABLE][sintassi di CREATE TABLE].
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
SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][Statistiche] nel gruppo di argomenti sullo sviluppo. Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.

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
Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse][Caricare i dati in SQL Data Warehouse].
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][Panoramica sullo sviluppo per SQL Data Warehouse].

<!--Image references-->

<!--Article references-->

[Caricare i dati in SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[creazione di una tabella]: ./sql-data-warehouse-tables-overview.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[sintassi di CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Nov16_HO2-->


