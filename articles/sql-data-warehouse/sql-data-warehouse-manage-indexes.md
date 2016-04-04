<properties
   pageTitle="Gestione degli indici | Microsoft Azure"
   description="Indicazioni per consentire agli utenti di gestire gli indici"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestione degli indici
Questo articolo illustra alcune delle tecniche principali per la gestione degli indici.

## Ottimizzazione della ricompilazione dell'indice
È possibile ottimizzare la ricompilazione dell'indice in due modi:

1. Partizionare la tabella ed eseguire ricompilazioni dell'indice a livello di partizione.
2. Usare [CTAS][] per ricreare la partizione dei dati in una nuova tabella ed eseguire un cambio di partizione nella nuova tabella.

## Ricompilazione dell'indice partizionato

Di seguito è riportato un esempio di ricompilazione di una singola partizione:

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

L'istruzione ALTER INDEX..REBUILD è più adatta per volumi di dati più piccoli, in particolare su indici columnstore. I gruppi di righe aperti, chiusi e compressi sono tutti inclusi nella ricompilazione. Tuttavia, se la partizione è piuttosto grande l'operazione più efficiente è `CTAS`. Di seguito è riportato un esempio di ricompilazione di un indice completo:

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Per altre informazioni su questa sintassi, vedere l'articolo relativo a [ALTER INDEX][].

## Utilizzo di CTAS per ricompilare una partizione

Di seguito è riportato un esempio di ricompilazione di una partizione tramite CTAS:

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## Passaggi successivi
Per altre indicazioni su come creare e ridimensionare le partizioni, vedere l'articolo relativo al [partizionamento delle tabelle][]. L'articolo contiene anche un esempio che permette di identificare i limiti delle partizioni.

Per altri suggerimenti relativi alla gestione, vedere la panoramica sulla [gestione][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[partizionamento delle tabelle]: sql-data-warehouse-develop-table-partitions.md
[gestione]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/it-IT/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->