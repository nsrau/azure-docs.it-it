<properties
   pageTitle="Caricare i dati di esempio in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su come caricare i dati di esempio in SQL Data Warehouse"
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

# Caricare i dati di esempio in SQL Data Warehouse

Durante la creazione dell'istanza di SQL Data Warehouse è possibile caricarvi facilmente alcuni dati di esempio. Se questo passaggio non viene eseguito durante il provisioning, è anche possibile [caricare manualmente i dati di esempio][].

Di seguito viene fornita una breve descrizione di come caricare AdventureWorksDW nel database. Questo set di dati modella una struttura di data warehouse di esempio per una società fittizia denominata AdventureWorks con dati che rappresentano le vendite e i clienti.

## Aggiunta di dati di esempio durante la creazione
È possibile assicurarsi che i dati di esempio vengano caricati nell'istanza di SQL Data Warehouse durante la distribuzione seguendo questa procedura:

1. Per avviare il processo di creazione è necessario trovare SQL Data Warehouse nel [portale di Azure classico][] facendo clic su '+ Nuovo' e quindi su 'Dati e archiviazione' oppure in Marketplace cercando 'SQL Data Warehouse'.

2. Dopo l'avvio del processo, fare clic sull'opzione 'Selezionare l'origine' e impostarla su 'Esempio'. Se non si intende creare un nuovo server, verrà inoltre chiesto di fornire l'accesso per il server da usare.


> [AZURE.NOTE] Per caricare dati di esempio nell'istanza, è infatti necessario consentire ai servizi Azure di accedere al server (tale funzionalità in genere è abilitata per impostazione predefinita durante la creazione di un nuovo server). Se non si fornisce l'accesso, il caricamento avrà esito negativo, ma sarà comunque possibile [caricare manualmente i dati di esempio][].


## Uso di Power BI per l'analisi di Adventureworks

L'uso del set di dati di esempio può essere il modo ideale per imparare a eseguire Power BI. Dopo aver caricato i dati di esempio, è infatti possibile aprire una connessione a SQL Data Warehouse facendo clic sul pulsante 'Apri in Power BI' nel portale di Azure classico o passando a [Power BI][] ed [eseguendo la connessione a SQL Data Warehouse][]. Dopo la connessione, dovrebbe venire creato un nuovo set di dati con lo stesso nome del data warehouse in uso. Per semplificare l'analisi, è stata creata una vista denominata 'AggregateSales' con alcune delle metriche fondamentali per analizzare le vendite della società. È possibile fare clic sul nome di questa vista per espanderla e visualizzare le colonne che contiene, ma anche creare alcune visualizzazioni rapide seguendo questa procedura:

1. Per iniziare, è possibile creare facilmente una mappa di tutte le vendite facendo clic sulle colonne 'PostalCode' e 'SalesAmount'. Power BI è persino in grado di riconoscere automaticamente tali informazioni come dati geografici e di inserirle direttamente in una mappa.

2. A questo punto, se si desidera creare un grafico a barre delle vendite, è sufficiente fare clic sulla colonna 'SalesAmount' perché Power BI lo crei in modo automatico. Per aggiungere profondità, è possibile trascinare il grafico 'CustomerIncome' sul campo 'Axis' a sinistra di 'AggregateSales' per visualizzare le vendite in base agli scaglioni di reddito dei clienti.

3. Se infine si desidera creare una sequenza temporale delle vendite, è possibile fare clic su 'SalesAmount', 'OrderDate' e 'Line Chart' (la prima icona nella seconda riga sotto la voce relativa alle visualizzazioni).

È possibile salvare in qualsiasi momento lo stato di avanzamento facendo clic sul pulsante di salvataggio nell'angolo superiore sinistro e salvando le visualizzazioni come report.

## Connessione all’esempio ed esecuzione di query relative

I dati di esempio possono essere analizzati anche con mezzi tradizionali. Come descritto nella documentazione relativa alla [connessione ed esecuzione di query][], è possibile connettersi al database tramite SQL Server Data Tools in Visual Studio. Dopo aver caricato alcuni dati di esempio in SQL Data Warehouse, è possibile eseguire rapidamente alcune query per iniziare.

È possibile eseguire un'istruzione select semplice per ottenere tutte le informazioni sui dipendenti:

```sql
SELECT * FROM DimEmployee;
```

È inoltre possibile eseguire una query più complessa utilizzando costrutti come GROUP BY per visualizzare il volume totale di tutte le vendite di ogni giorno:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey
;
```

È anche possibile utilizzare la clausola WHERE per filtrare gli ordini prima di una determinata data:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey
;
```

In realtà, SQL Data Warehouse supporta quasi tutti i costrutti T-SQL di SQL Server ed è possibile trovare alcune delle differenze nella documentazione relativa [alla migrazione del codice][].

## Passaggi successivi
Dopo essersi esercitati con i dati di esempio, è possibile vedere come [sviluppare][], [caricare][] o [eseguire la migrazione][].

<!--Image references-->

<!--Article references-->
[eseguire la migrazione]: ./sql-data-warehouse-overview-migrate.md
[sviluppare]: ./sql-data-warehouse-overview-develop.md
[caricare]: ./sql-data-warehouse-overview-load.md
[connessione ed esecuzione di query]: ./sql-data-warehouse-get-started-connect.md
[alla migrazione del codice]: ./sql-data-warehouse-migrate-code.md
[caricare manualmente i dati di esempio]: ./sql-data-warehouse-get-started-manually-load-samples.md
[portale di Azure classico]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[eseguendo la connessione a SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=AcomDC_0330_2016-->