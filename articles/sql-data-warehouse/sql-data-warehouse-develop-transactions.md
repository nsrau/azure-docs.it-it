<properties
   pageTitle="Transazioni in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'implementazione di transazioni in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transazioni in SQL Data Warehouse

Come è prevedibile, SQL Data Warehouse offre il supporto per tutte le proprietà transazionali. Tuttavia, per garantire che le prestazioni di SQL Data Warehouse siano mantenute al massimo livello, alcune funzionalità sono limitate rispetto a SQL Server. Questo articolo evidenzia le differenze ed elenca le altre.

## Livelli di isolamento delle transazioni
SQL Data Warehouse implementa le transazioni ACID. Tuttavia, l'isolamento del supporto delle transazioni è limitato a `READ UNCOMMITTED` e non può essere modificato. È possibile implementare numerosi metodi di codifica per evitare letture dirty dei dati se ciò costituisce un problema. I metodi più diffusi usano CTAS e il cambio della partizione di tabella (spesso noto come modello di finestra temporale scorrevole) per impedire agli utenti di eseguire query sui dati ancora in fase di preparazione. Anche le visualizzazioni che filtrano preventivamente i dati costituiscono un approccio comune.

## Dimensioni delle transazioni
Le dimensioni di una singola transazione di modifica dati sono limitate. Il limite è attualmente applicato "per ogni distribuzione". Per ottenere la cifra totale è quindi necessario moltiplicare il limite per il numero di distribuzioni. Per calcolare approssimativamente il numero massimo di righe nella transazione, dividere il limite di distribuzione per le dimensioni totali di ogni colonna. Per le colonne di lunghezza variabile valutare la possibilità di usare una lunghezza di colonna media invece delle dimensioni massime.

Ecco alcuni presupposti riportati nella tabella seguente:
* Si è verificata una distribuzione uniforme dei dati 
* La lunghezza media delle righe è 250 byte

| DWU | Limite per ogni distribuzione (GiB) | Numero di distribuzioni | Dimensioni MAX delle transazioni (GiB) | N. di righe distribuzione | Righe max per transazione |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4\.000.000 | 240\.000.000 |
| DW200 | 1,5 | 60 | 90 | 6\.000.000 | 360\.000.000 |
| DW300 | 2\.25 | 60 | 135 | 9\.000.000 | 540\.000.000 |
| DW400 | 3 | 60 | 180 | 12\.000.000 | 720\.000.000 |
| DW500 | 3,75 | 60 | 225 | 15\.000.000 | 900\.000.000 |
| DW600 | 4\.5 | 60 | 270 | 18\.000.000 | 1\.080.000.000 |
| DW1000 | 7\.5 | 60 | 450 | 30\.000.000 | 1\.800.000.000 |
| DW1200 | 9 | 60 | 540 | 36\.000.000 | 2\.160.000.000 |
| DW1500 | 11,25 | 60 | 675 | 45\.000.000 | 2\.700.000.000 |
| DW2000 | 15 | 60 | 900 | 60\.000.000 | 3\.600.000.000 |

Il limite delle dimensioni delle transazioni viene applicato per transazione o per operazione. Non viene applicato in tutte le transazioni simultanee. A ogni transazione è quindi consentito scrivere questa quantità di dati nel log.

Per ottimizzare e ridurre la quantità di dati scritti nel log, vedere l'articolo relativo alle [procedure consigliate per le transazioni][].

> [AZURE.WARNING] Le dimensioni massime delle transazioni possono essere ottenute solo per le tabelle distribuite HASH o ROUND\_ROBIN in cui i dati sono distribuiti in modo uniforme. Se la transazione scrive dati in modo asimmetrico nelle distribuzioni, è probabile che il limite venga raggiunto prima di raggiungere le dimensioni massime delle transazioni.
<!--REPLICATED_TABLE-->

## Stato della transazione
SQL Data Warehouse usa la funzione XACT\_STATE() per segnalare una transazione non riuscita con il valore -2. Ciò significa che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [AZURE.NOTE] L'uso di -2 da parte della funzione XACT\_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, SELECT 1/0 causa un errore, ma non applica alla transazione lo stato per cui non è possibile eseguire il commit. SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. In SQL Data Warehouse ciò non è possibile. Se si verifica un errore in una transazione di SQL Data Warehouse, passerà automaticamente allo stato -2, inclusi gli errori di SELECT 1/0. È quindi importante verificare il codice dell'applicazione per vedere se usa XACT\_STATE().

In SQL Server può essere visualizzato un frammento di codice simile al seguente:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Si noti che l'istruzione `SELECT` si verifica prima dell'istruzione `ROLLBACK`. Notare anche che l'impostazione della variabile `@xact` usa DECLARE e non `SELECT`.

In SQL Data Warehouse il codice dovrà essere simile al seguente:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Si noti che il rollback della transazione deve essere eseguito prima della lettura delle informazioni sull'errore nel blocco `CATCH`.

## Funzione Error\_Line()
È importante sottolineare anche che SQL Data Warehouse non implementa né supporta la funzione ERROR\_LINE(). Se è contenuta nel codice sarà necessario rimuoverla per renderlo compatibile con SQL Data Warehouse. Anziché implementare una funzionalità equivalente, usare etichette di query nel codice. Per altre informazioni su questa funzionalità, vedere l'articolo relativo alle [etichette di query].

## Uso di THROW e RAISERROR
THROW è l'implementazione più moderna per la generazione di eccezioni in SQL Data Warehouse, ma è supportata anche RAISERROR. Esistono tuttavia alcune differenze a cui vale la pena prestare attenzione.

- I numeri dei messaggi di errore definiti dall'utente non possono essere compresi nell'intervallo da 100.000 a 150.000 per THROW.
- I messaggi di errore di RAISERROR sono fissati a 50.000.
- L'uso di sys.messages non è supportato.

## Limitazioni
SQL Data Warehouse presenta qualche altra limitazione relativa alle transazioni.

Ecco quali sono:

- Nessuna transazione distribuita
- Non sono consentite transazioni annidate
- Non sono consentiti punti di salvataggio

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[procedure consigliate per le transazioni]: sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->