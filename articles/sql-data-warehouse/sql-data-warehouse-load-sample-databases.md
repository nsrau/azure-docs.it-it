---
title: Caricare i dati di esempio in SQL Data Warehouse | Documentazione Microsoft
description: Caricare i dati di esempio in SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9edad2037787a7a2da4e2a6fcce15ace51d41032


---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Caricare i dati di esempio in SQL Data Warehouse
Seguire questi semplici passaggi per caricare ed eseguire query nel database di esempio Adventure Works. Per iniziare, questi script usano sqlcmd per eseguire SQL e creare tabelle e viste. Dopo aver creato le tabelle, gli script usano bcp per caricare i dati.  Se questi strumenti non sono già installati, usare i collegamenti seguenti per [installare bcp][installare bcp] e [installare sqlcmd][installare sqlcmd].

## <a name="load-sample-data"></a>Caricare dati di esempio
1. Scaricare il file zip contenente gli [script di esempio Adventure Works per SQL Data Warehouse][script di esempio Adventure Works per SQL Data Warehouse].
2. Estrarre i file dallo zip scaricato in una directory nel computer locale.
3. Modificare il file estratto aw_create.bat e impostare le variabili seguenti nella parte superiore del file.  Assicurarsi di non lasciare alcun spazio vuoto tra "=" e il parametro.  Di seguito sono riportati alcuni esempi dell'aspetto che le modifiche potrebbero assumere.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. Da un prompt dei comandi di Windows, eseguire il file aw_create.bat modificato.  Assicurarsi di trovarsi nella directory in cui è stata salvata la versione modificata del file aw_create.bat.
   Questo script consentirà di eseguire le operazioni seguenti:
   
   * Eliminare le tabelle o le visualizzazioni Adventure Works già esistenti nel database
   * Creare le tabelle e le visualizzazioni Adventure Works
   * Caricare tutte le tabelle Adventure Works tramite bcp
   * Convalidare il conteggio delle righe per tutte le tabelle Adventure Works
   * Raccogliere le statistiche per ogni colonna di ogni tabella Adventure Works

## <a name="query-sample-data"></a>Eseguire query sui dati di esempio
Dopo aver caricato alcuni dati di esempio in SQL Data Warehouse, è possibile eseguire rapidamente alcune query.  Per eseguire una query, connettersi al database Adventure Works appena creato in Azure SQL Data Warehouse mediante Visual Studio e SSDT, come descritto nel documento relativo alle [query con Visual Studio][query con Visual Studio].

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

SQL Data Warehouse supporta quasi tutti i costrutti T-SQL supportati da SQL Server.  Eventuali differenze sono illustrate nella documentazione relativa alla [migrazione del codice][migrazione del codice].

## <a name="next-steps"></a>Passaggi successivi
Ora che sono state sperimentate alcune query con dati di esempio, è possibile scoprire come [svilupparli][sviluppo], [caricarli][caricamento] o [eseguirne la migrazione][migrazione] in SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md
[caricamento]: sql-data-warehouse-overview-load.md
[query con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrazione del codice]: sql-data-warehouse-migrate-code.md
[installare bcp]: sql-data-warehouse-load-with-bcp.md
[installare sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[script di esempio Adventure Works per SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip



<!--HONumber=Nov16_HO3-->


