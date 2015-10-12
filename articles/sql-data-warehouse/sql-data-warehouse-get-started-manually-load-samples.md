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
   ms.date="09/22/2015"
   ms.author="lodipalm;barbkess"/>

#Caricare i dati di esempio in SQL Data Warehouse

Dopo aver configurato un'istanza di SQL Data Warehouse è possibile caricare facilmente alcuni dati di esempio al suo interno. Quanto riportato di seguito consente di creare un set di dati denominato AdventureWorksPDW2012 nel database. Questo set di dati modella una struttura di warehouse di dati di esempio per una società fittizia denominata AdventureWorks. Si noti che è necessario disporre di BCP installato per la procedura seguente. Se attualmente non si dispone di BCP installato, installare [Microsoft Command Line Utilities for SQL Server][].

1. Per iniziare fare clic per scaricare gli [script di dati di esempio][].

2. Dopo aver scaricato il file, estrarre il contenuto del file AdventureWorksPDW2012.zip e aprire la nuova cartella AdventureWorksPDW2012.

3. Modificare il file aw\_create.bat e impostare i valori seguenti nella parte superiore del file:

   a. **Server**: il nome completo del server di SQL Data Warehouse risiede in

   b. **Utente**: l'utente per il server indicato sopra
   
   c. **Password**: la password dell’account di accesso d fornito
   
   d. **Database**: il nome dell'istanza di SQL Data Warehouse su cui si desidera caricare i dati
   
   Assicurarsi che non vi sia alcun spazio vuoto tra '=' e questi parametri.
   

4. Eseguire aw\_create.bat dalla directory in cui si trova. Verrà creato lo schema e verranno caricati i dati in tutte le tabelle tramite BCP.


## Connessione all’esempio ed esecuzione di query relative

Come descritto nella documentazione relativa alla [connessione ed esecuzione di query][], è possibile connettersi al database tramite Visual Studio e SSDT. Dopo aver caricato alcuni dati di esempio in SQL Data Warehouse, è possibile eseguire rapidamente alcune query per iniziare.

È possibile eseguire un'istruzione select semplice per ottenere tutte le informazioni sui dipendenti:

	SELECT * FROM DimEmployee;

È inoltre possibile eseguire una query più complessa utilizzando costrutti come GROUP BY per visualizzare il volume totale di tutte le vendite di ogni giorno:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

È anche possibile utilizzare la clausola WHERE per filtrare gli ordini prima di una determinata data:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

In realtà, SQL Data Warehouse supporta quasi tutti i costrutti T-SQL di SQL Server ed è possibile trovare alcune delle differenze nella documentazione relativa [alla migrazione del codice][].

## Passaggi successivi
Dopo esserci esercitati con i dati di esempio, esaminiamo come [sviluppare][], [caricare][] o [migrare][].

<!--Image references-->

<!--Article references-->
[migrare]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-migrate/
[sviluppare]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-develop/
[caricare]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-load/
[connessione ed esecuzione di query]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-get-started-connect-query/
[alla migrazione del codice]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/it-IT/download/details.aspx?id=36433

<!--Other Web references-->
[script di dati di esempio]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=Oct15_HO1-->