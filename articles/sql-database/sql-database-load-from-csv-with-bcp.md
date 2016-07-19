<properties
   pageTitle="Caricare dati da un file CSV al database SQL di Azure (bcp) | Microsoft Azure"
   description="Per dati di piccole dimensioni, usa bcp per importare dati nel database SQL di Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="carlrab"/>


# Caricare dati da CSV in Azure SQL Data Warehouse (file flat)

È possibile usare l'utilità della riga di comando bcp per importare dati da un file CSV in un database SQL di Azure.

## Prima di iniziare

### Prerequisiti

Per eseguire questa esercitazione, è necessario:

- Un server logico e un database SQL di Azure
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
    ;
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


## Passaggi successivi

Per eseguire la migrazione di un database SQL Server, vedere [Migrazione di un database SQL Server al database SQL nel cloud](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0713_2016-->