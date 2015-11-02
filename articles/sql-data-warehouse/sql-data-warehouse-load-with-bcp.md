<properties
   pageTitle="Usare bcp per caricare i dati in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su bcp e sul relativo uso per scenari di data warehousing."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="mausher;barbkess"/>


# Caricare dati con bcp
**[bcp][]** è un'utilità di caricamento bulk da riga di comando che permette di copiare dati tra SQL Server, file di dati e SQL Data Warehouse. Usare bcp per importare numeri elevati di righe nelle tabelle di SQL Data Warehouse o per esportare i dati dalle tabelle di SQL Server ai file di dati. bcp richiede competenze in ambito di Transact-SQL solo quando viene usato con l'opzione queryout.

bcp costituisce un modo semplice e rapido per spostare set di dati di dimensioni ridotte da e verso un database di SQL Data Warehouse. La quantità esatta di dati che è consigliabile caricare/estrarre tramite bcp dipenderà dalla connessione di rete per il data center di Azure. In genere, le tabelle delle dimensioni possono essere caricate ed estratte, ma è possibile che tabelle dei fatti di dimensioni abbastanza elevate richiedano una quantità di tempo significativa per il caricamento o l'estrazione.

con bcp è possibile:

- Utilizzare una semplice utilità della riga di comando per caricare dati nell’SQL Data Warehouse.
- Utilizzare una semplice utilità della riga di comando per estrarre dati dall’SQL Data Warehouse.

In questa esercitazione verranno illustrate le attività seguenti:
 
- Importare dati in una tabella tramite l'utilità bcp nel comando
- Esportare dati da una tabella tramite l'utilità bcp dal comando

## Prerequisiti
Per eseguire questa esercitazione, è necessario:

- Un database di SQL Data Warehouse
- L’utilità della riga di comando bcp installata
- L’utilità della riga di comando SQLCMD installata

>[AZURE.NOTE]È possibile scaricare le utilità bcp e sqlcmd dall'[Area download Microsoft][].

##Importare i dati in SQL Data Warehouse
In questa esercitazione verrà creata una tabella in Azure SQL Data Warehouse e verranno importati dati nella tabella.

### Passaggio 1: Creare una tabella in Azure SQL Data Warehouse
Da un prompt dei comandi connettersi all'istanza usando il comando seguente, sostituendo i valori in base alla necessità:

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
Dopo la connessione, copiare lo script di tabella seguente nel prompt di sqlcmd e quindi premere INVIO:

```
CREATE TABLE DimDate2 (DateId INT NOT NULL, CalendarQuarter TINYINT NOT NULL, FiscalQuarter TINYINT NOT NULL);
```

Nella riga seguente immettere il terminatore di batch GO e premere INVIO per eseguire l'istruzione:

```
GO
```

### Passaggio 2: Creare un file di dati di origine

Aprire il Blocco note e copiare le righe di dati seguenti in un nuovo file.

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

Salvare il file nella directory temporanea locale, C:\\Temp\\DimDate2.txt.

> [AZURE.NOTE]È importante ricordare che bcp.exe non supporta la codifica UTF-8 del file. Utilizzare i file codificati ASCII o la codifica UTF-16 per i file quando si utilizza bcp.exe.

### Passaggio 3: Connettersi e importare i dati
bcp permette di connettersi e importare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Per verificare il caricamento dei dati, connettersi con sqlcmd come indicato in precedenza ed eseguire il comando TSQL seguente:

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
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

## Esportare i dati da SQL Data Warehouse
In questa esercitazione verrà creato un file di dati da una tabella in SQL Data Warehouse. I dati creati in precedenza verranno esportati in un nuovo file denominato DimDate2\_export.txt.

### Passaggio 1: Esportare i dati

L'utilità bcp permette di connettersi ed esportare i dati usando il comando seguente, sostituendo i valori in base alla necessità:

```
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

>[AZURE.NOTE]A causa della natura dei sistemi distribuiti, è possibile che l'ordine dei dati non sia uguale nei database di SQL Data Warehouse. È facoltativamente possibile usare il parametro queryout per specificare la query Transact-SQL da eseguire.

## Passaggi successivi
Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse][]. Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Caricare i dati in SQL Data Warehouse]: ./sql-data-warehouse-overview-load/
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Area download Microsoft]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=Oct15_HO4-->