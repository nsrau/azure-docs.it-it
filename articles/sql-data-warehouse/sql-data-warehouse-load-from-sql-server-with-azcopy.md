<properties
   pageTitle="Caricare dati da SQL Server in Azure SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Uso di bcp per esportare dati da SQL Server a file flat, di AZCopy per importare dati nell'archivio BLOB di Azure e di PolyBase per inserire i dati in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# Caricare dati da SQL Server in Azure SQL Data Warehouse (AZCopy)

Usare le utilità della riga di comando bcp e AZCopy per caricare dati da SQL Server all'archivio BLOB di Azure. Usare quindi PolyBase o Azure Data Factory per caricare i dati in Azure SQL Data Warehouse.


## Prerequisiti

Per eseguire questa esercitazione, è necessario:

- Un database di SQL Data Warehouse
- Utilità della riga di comando bcp installata
- Utilità della riga di comando SQLCMD installata

>[AZURE.NOTE] È possibile scaricare le utilità bcp e sqlcmd dall'[Area download Microsoft][].

## Importare i dati in SQL Data Warehouse

In questa esercitazione verrà creata una tabella in Azure SQL Data Warehouse e verranno importati dati nella tabella.

### Passaggio 1: Creare una tabella in Azure SQL Data Warehouse

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

>[AZURE.NOTE] Per altre informazioni sulla creazione di una tabella in SQL Data Warehouse e sulle opzioni disponibili con la clausola WITH, vedere [Overview of tables in SQL Data Warehouse][] \(Panoramica delle tabella in SQL Data Warehouse) o la sintassi di [CREATE TABLE][].

### Passaggio 2: Creare un file di dati di origine

Aprire il Blocco note, copiare le righe di dati seguenti in un nuovo file di testo e quindi salvare il file nella directory temporanea locale, C:\\Temp\\DimDate2.txt.

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

> [AZURE.NOTE] È importante ricordare che bcp.exe non supporta la codifica UTF-8 del file. Usare i file ASCII o con codifica UTF-16 quando si usa bcp.exe.

### Passaggio 3: Connettersi e importare i dati
bcp permette di connettersi e importare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

È possibile verificare che i dati siano stati caricati eseguendo la query seguente con sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Dovrebbero essere visualizzati i risultati seguenti:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Passaggio 4: creare le statistiche sui dati appena caricati

SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento relativo alle [statistiche][] nel gruppo di argomenti sullo sviluppo. Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.

Da un prompt di sqlcmd, eseguire le istruzioni CREATE STATISTICS seguenti:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## Esportare i dati da SQL Data Warehouse
In questa esercitazione verrà creato un file di dati da una tabella in SQL Data Warehouse. I dati creati in precedenza verranno esportati in un nuovo file denominato DimDate2\_export.txt.

### Passaggio 1: Esportare i dati

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

>[AZURE.NOTE] A causa della natura dei sistemi distribuiti, è possibile che l'ordine dei dati non sia uguale nei database di SQL Data Warehouse. Un'altra opzione consiste nell'usare la funzione **queryout** di bcp per scrivere un estratto di query invece di esportare l'intera tabella.

## Passaggi successivi
Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse][]. Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Caricare i dati in SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Overview of tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-overview.md
[statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0907_2016-->