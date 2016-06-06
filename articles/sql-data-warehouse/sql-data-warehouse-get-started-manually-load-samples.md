<properties
   pageTitle="Caricare i dati di esempio in SQL Data Warehouse | Microsoft Azure"
   description="Caricare i dati di esempio in SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Caricare i dati di esempio in SQL Data Warehouse

Dopo aver [creato un'istanza del database SQL Data Warehouse][create a SQL Data Warehouse database instance], il passaggio successivo consiste nel creare e caricare alcune tabelle. È possibile usare gli script di esempio Adventure Works creati per SQL Data Warehouse per creare e caricare tabelle per la società fittizia chiamata Adventure Works. Questi script usano sqlcmd per eseguire SQL e bcp per caricare i dati. Se questi strumenti non sono già installati, usare i collegamenti seguenti per [installare bcp][] e [sqlcmd][].

Seguire questi semplici passaggi per caricare il database di esempio Adventure Works in SQL Data Warehouse.

1. Scaricare gli [script di esempio Adventure Works per SQL Data Warehouse][].

2. Estrarre i file dallo zip scaricato in una directory nel computer locale.

3. Modificare il file estratto aw\_create.bat e impostare le variabili seguenti nella parte superiore del file. Assicurarsi di non lasciare alcun spazio vuoto tra "=" e il parametro. Di seguito sono riportati alcuni esempi dell'aspetto che le modifiche potrebbero assumere.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Da un prompt dei comandi di Windows, eseguire il file aw\_create.bat modificato. Assicurarsi di trovarsi nella directory in cui è stata salvata la versione modificata del file aw\_create.bat. Questo script consentirà di eseguire le operazioni seguenti:
	* Eliminare le tabelle o le visualizzazioni Adventure Works già esistenti nel database
	* Creare le tabelle e le visualizzazioni Adventure Works
	* Caricare tutte le tabelle Adventure Works tramite bcp
	* Convalidare il conteggio delle righe per tutte le tabelle Adventure Works
	* Raccogliere le statistiche per ogni colonna di ogni tabella Adventure Works


##Eseguire query sui dati di esempio

Dopo aver caricato alcuni dati di esempio in SQL Data Warehouse, è possibile eseguire rapidamente alcune query. Per eseguire una query, connettersi al database Adventure Works appena creato in SQL Data Warehouse di Azure mediante Visual Studio e SSDT, come descritto nel documento relativo alla [connessione][].

Esempio di un'istruzione select semplice per ottenere tutte le informazioni sui dipendenti:

```sql
SELECT * FROM DimEmployee;
```

Esempio di una query più complessa che usa costrutti come GROUP BY per visualizzare il volume totale di tutte le vendite di ogni giorno:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Esempio di un'istruzione SELECT con clausola WHERE per filtrare gli ordini prima di una determinata data:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse supporta quasi tutti i costrutti T-SQL supportati da SQL Server. Eventuali differenze sono illustrate nella documentazione relativa alla [migrazione del codice][].

## Passaggi successivi
Ora che sono state sperimentate alcune query con i dati di esempio, è possibile scoprire come [svilupparli][], [caricarli][] o [eseguirne la migrazione][] in SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[eseguirne la migrazione]: ./sql-data-warehouse-overview-migrate.md
[svilupparli]: ./sql-data-warehouse-overview-develop.md
[caricarli]: ./sql-data-warehouse-overview-load.md
[connessione]: ./sql-data-warehouse-get-started-connect.md
[migrazione del codice]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[installare bcp]: ./sql-data-warehouse-load-with-bcp.md
[sqlcmd]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[script di esempio Adventure Works per SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0525_2016-->