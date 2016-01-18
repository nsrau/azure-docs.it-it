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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Transazioni in SQL Data Warehouse

Come è prevedibile, SQL Data Warehouse offre il supporto per tutte le proprietà transazionali. Tuttavia, per garantire che le prestazioni di SQL Data Warehouse siano mantenute al massimo livello, alcune funzionalità sono limitate rispetto a SQL Server. In questo articolo sono evidenziate le differenze e vengono elencati le altre.

## Livelli di isolamento delle transazioni
SQL Data Warehouse implementa le transazioni ACID. Tuttavia, l'isolamento del supporto delle transazioni è limitato a `READ UNCOMMITTED` e non può essere modificato. È possibile implementare numerosi metodi di codifica per evitare letture dirty dei dati se ciò costituisce un problema. I metodi più diffusi usano CTAS e il cambio della partizione di tabella (spesso noto come modello di finestra temporale scorrevole) per impedire agli utenti di eseguire query sui dati ancora in fase di preparazione. Anche le visualizzazioni che filtrano preventivamente i dati costituiscono un approccio comune.

## Stato della transazione
SQL Data Warehouse usa la funzione XACT\_STATE() per segnalare una transazione non riuscita con il valore -2. Ciò significa che la transazione non è riuscita ed è contrassegnata solo per il rollback.

> [AZURE.NOTE]L'uso di -2 da parte della funzione XACT\_STATE per indicare una transazione non riuscita rappresenta un comportamento diverso da SQL Server. SQL Server usa il valore -1 per rappresentare una transazione di cui non è possibile eseguire il commit. SQL Server è in grado di tollerare alcuni errori all'interno di una transazione senza doverne indicare l'impossibilità di eseguire il commit. Ad esempio, SELECT 1/0 causa un errore, ma non applica alla transazione lo stato per cui non è possibile eseguire il commit. SQL Server consente anche letture nella transazione di cui non è possibile eseguire il commit. In SQL Data Warehouse ciò non è possibile. Se si verifica un errore in una transazione di SQL Data Warehouse, passerà automaticamente allo stato -2, inclusi gli errori di SELECT 1/0. È quindi importante verificare il codice dell'applicazione per vedere se usa XACT\_STATE().

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

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->