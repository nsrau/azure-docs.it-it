<properties
   pageTitle="Rinominare in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per rinominare oggetti e database in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Rinominare in SQL Data Warehouse
SQL Server supporta la ridenominazione del database mediante la stored procedure ```sp_renamedb```. SQL Data Warehouse usa la sintassi DDL per ottenere lo stesso obiettivo. Il comando DDL è ```RENAME DATABASE```.

## Rinominare il database

Per rinominare un database, eseguire il comando seguente.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

È importante ricordare che non è possibile rinominare un oggetto o un database se altri utenti sono connessi o stanno usando tale oggetto o database. Potrebbe essere necessario terminare prima di tutto le sessioni aperte. Per terminare una sessione, è necessario usare il comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Usare il comando ```KILL``` con attenzione. Dopo l'esecuzione del comando, le sessioni interessate verranno terminate e verrà eseguito il rollback di eventuale lavoro non sottoposto a commit.

> [AZURE.NOTE]Le sessioni in SQL Data Warehouse sono precedute dal prefisso 'SID'. Quando si richiama il comando KILL, sarà necessario includere tale prefisso e il numero di sessione. Ad esempio, ```KILL 'SID1234'``` permette di terminare la sessione 1234, se si hanno le autorizzazioni necessarie per eseguirlo.

## Terminare le sessioni
Per rinominare un database, potrebbe essere necessario terminare le sessioni connesse a SQL Data Warehouse. La query seguente genererà un elenco distinto di comandi KILL per cancellare le connessioni, ad eccezione della sessione corrente.

```
SELECT
    'KILL ''' + session_id + ''''
FROM
    sys.dm_pdw_exec_requests r
WHERE
    r.session_id <> SESSION_ID()
    AND EXISTS
    (
        SELECT
            *
        FROM
            sys.dm_pdw_lock_waits w
        WHERE
            r.session_id = w.session_id
    )
GROUP BY
    session_id;
```

## Cambio di schemi
Se si vuole cambiare lo schema a cui appartiene un oggetto, è possibile usare `ALTER SCHEMA`:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!---HONumber=Oct15_HO4-->