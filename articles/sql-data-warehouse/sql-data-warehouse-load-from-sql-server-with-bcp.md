<properties
   pageTitle="Caricare dati da SQL Server in Azure SQL Data Warehouse (bcp) | Microsoft Azure"
   description="Per dimensioni ridotte dei dati è possibile usare bcp per esportare i dati da SQL Server a file flat e importare i dati direttamente in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Caricare dati da SQL Server in Azure SQL Data Warehouse (file flat)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Per set di dati di piccole dimensioni è possibile usare l'utilità della riga di comando bcp per esportare i dati da SQL Server e quindi caricarli direttamente in Azure SQL Data Warehouse.

In questa esercitazione si userà bcp per eseguire queste operazioni:

- Esportare una tabella da SQL Server usando il comando bcp out oppure creare un semplice file di esempio.
- Importare la tabella da un file flat in SQL Data Warehouse.
- Creare statistiche sui dati caricati.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Prima di iniziare

### Prerequisiti

Per eseguire questa esercitazione, è necessario:

- Un database di SQL Data Warehouse
- Utilità della riga di comando bcp installata
- Utilità della riga di comando sqlcmd installata

È possibile scaricare le utilità bcp e sqlcmd dall'[Area download Microsoft][].

### Dati in formato ASCII o UTF-16

Se si prova a eseguire questa esercitazione con dati personalizzati, è necessario che i dati usino la codifica ASCII o UTF-16, perché bcp non supporta UTF-8.

PolyBase supporta UTF-8 ma non supporta ancora UTF-16. Si noti che se si vuole associare bcp con PolyBase, sarà necessario trasformare i dati in UTF-8 dopo l'esportazione da SQL Server.


## 1\. Creare una tabella di destinazione

Definire una tabella in SQL Data Warehouse da usare come tabella di destinazione per il caricamento. Le colonne della tabella devono corrispondere ai dati in ogni riga del file di dati.

Per creare una tabella, aprire un prompt dei comandi e usare sqlcmd.exe per eseguire i comandi seguenti:


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


## 2\. Creare un file di dati di origine

Aprire il Blocco note, copiare le righe di dati seguenti in un nuovo file di testo e quindi salvare il file nella directory temporanea locale, C:\\Temp\\DimDate2.txt. I dati hanno formato ASCII.

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

(Facoltativo) Per esportare i dati personalizzati da un database di SQL Server, aprire un prompt dei comandi ed eseguire il comando seguente. Sostituire TableName, ServerName, DatabaseName, Username e Password con le informazioni personalizzate.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## 3\. Caricare i dati
Per caricare i dati, aprire un prompt dei comandi ed eseguire il comando seguente, sostituendo i valori per nome server, nome database, nome utente e password con le informazioni personalizzate.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Usare questo comando per verificare che i dati siano stati caricati correttamente.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

I risultati dovrebbero avere l'aspetto seguente:

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

## 4\. Creare le statistiche

SQL Data Warehouse non supporta ancora la creazione automatica o l'aggiornamento automatico delle statistiche. Per ottenere le prestazioni migliori per le query, è importante creare statistiche su tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo qualsiasi modifica significativa ai dati. Per una spiegazione dettagliata delle statistiche, vedere [Statistiche][].

Eseguire il comando seguente per creare le statistiche nella tabella appena caricata.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5\. Esportare i dati da SQL Data Warehouse
Se si vuole, è possibile esportare i dati appena caricati da SQL Data Warehouse. Il comando per l'esportazione corrisponde esattamente all'esportazione da SQL Server.

I risultati presentano tuttavia una differenza. Poiché i dati vengono archiviati in posizioni distribuite in SQL Data Warehouse, quando si esportano i dati ogni nodo di calcolo scrive i rispettivi dati nel file di output. L'ordine dei dati nel file di output sarà probabilmente diverso dall'ordine dei dati nel file di input.

### Esportare una tabella e confrontare i risultati esportati

Per visualizzare i dati esportati, aprire un prompt dei comandi ed eseguire questo comando usando parametri personalizzati. ServerName è il nome del server SQL logico di Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Per verificare che i dati siano stati esportati correttamente, aprire il nuovo file. I dati nel file devono corrispondere al testo seguente, ma è probabile che abbiano un ordinamento diverso:

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

### Esportare i risultati di una query

È possibile usare la funzione **queryout** di bcp per esportare i risultati di una query invece di esportare l'intera tabella.

## Passaggi successivi
Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse][]. Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][]. Per altre informazioni sulla creazione di una tabella in SQL Data Warehouse, vedere [Overview of tables in SQL Data Warehouse][] (Panoramica delle tabella in SQL Data Warehouse) o la sintassi di [CREATE TABLE][].

<!--Image references-->

<!--Article references-->

[Caricare i dati in SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Overview of tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-overview.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0706_2016-->