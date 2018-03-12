---
title: Usare bcp per caricare i dati in SQL Data Warehouse | Documentazione Microsoft
description: Informazioni su bcp e sul relativo uso per scenari di data warehousing.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 146c6fdada651551c05b2cbcadc3e1248a40b613
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="load-data-with-bcp"></a>Caricare dati con bcp

**[bcp](/sql/tools/bcp-utility.md)** è un'utilità di caricamento bulk da riga di comando che permette di copiare dati tra SQL Server, file di dati e SQL Data Warehouse. Usare bcp per importare numeri elevati di righe nelle tabelle di SQL Data Warehouse o per esportare i dati dalle tabelle di SQL Server ai file di dati. bcp richiede competenze in ambito di Transact-SQL solo quando viene usato con l'opzione queryout.

bcp costituisce un modo semplice e rapido per spostare set di dati di dimensioni ridotte da e verso un database di SQL Data Warehouse. La quantità esatta di dati che è consigliabile caricare/estrarre tramite bcp dipende dalla connessione di rete ad Azure.  Piccole tabelle delle dimensioni possono essere caricate ed estratte facilmente con bcp. Per il caricamento e l'estrazione di grandi volumi di dati, invece, lo strumento consigliato è Polybase, non bcp. PolyBase è progettato per l'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing) di SQL Data Warehouse.

Con bcp è possibile:

* Usare un'utilità della riga di comando per caricare dati in SQL Data Warehouse.
* Usare un'utilità della riga di comando per estrarre dati da SQL Data Warehouse.

Questa esercitazione:

* Importa dati in una tabella con il comando in di bcp
* Esporta dati da una tabella con il comando out di bcp

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un database di SQL Data Warehouse
* Utilità della riga di comando bcp e sqlcmd, scaricabili dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=36433) 

### <a name="data-in-ascii-or-utf-16-format"></a>Dati in formato ASCII o UTF-16
Se si prova a eseguire questa esercitazione con dati personalizzati, è necessario che i dati usino la codifica ASCII o UTF-16, perché bcp non supporta UTF-8. 

PolyBase supporta UTF-8 ma non supporta ancora UTF-16. Per usare bcp per l'esportazione e quindi PolyBase per il caricamento di dati, è necessario trasformare i dati in UTF-8 dopo che sono stati esportati da SQL Server. 

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

Per altre informazioni sulla creazione di una tabella, vedere la [panoramica delle tabelle](sql-data-warehouse-tables-overview.md) o la sintassi di [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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
> È importante ricordare che bcp.exe non supporta la codifica UTF-8 del file. Quando si usa bcp.exe, usare file ASCII o con codifica UTF-16.
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

La query restituirà i risultati seguenti:

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
Dopo il caricamento dei dati, un passaggio finale consiste nel creare o aggiornare le statistiche. Questo consente di migliorare le prestazioni delle query. Per altre informazioni, vedere l'articolo relativo alle [statistiche](sql-data-warehouse-tables-statistics.md). L'esempio di sqlcmd seguente crea statistiche sulla tabella contenente i dati appena caricati.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Esportare i dati da SQL Data Warehouse
Questa esercitazione crea un file di dati da una tabella in SQL Data Warehouse. I dati importati nella sezione precedente vengono esportati e i risultati vengono inseriti in un file denominato DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Passaggio 1: Esportare i dati
L'utilità bcp permette di connettersi ed esportare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Per verificare che i dati siano stati esportati correttamente, aprire il nuovo file. I dati nel file dovrebbero corrispondere al testo seguente:

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
Per progettare il processo di caricamento, vedere [Panoramica del caricamento](sql-data-warehouse-design-elt-data-loading).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
