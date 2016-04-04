<properties
   pageTitle="Rinominare in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la ridenominazione di tabelle in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Rinominare in SQL Data Warehouse
Mentre il server SQL supporta la ridenominazione del database tramite la procedura di archiviazione ```sp_renamedb```, SQL Data Warehouse usa la sintassi DDL per ottenere lo stesso scopo. Il comando DDL è ```RENAME OBJECT```.

## Rinominare tabella

Attualmente, solo le tabelle possono essere rinominate. La sintassi per rinominare una tabella è:

```
RENAME OBJECT dbo.Customer TO NewCustomer;
```

Quando si rinomina una tabella, tutti gli oggetti e le proprietà associati alla tabella vengono aggiornati per fare riferimento al nuovo nome della tabella. Ad esempio, le definizioni della tabella, gli indici, i vincoli e le autorizzazioni vengono aggiornati. Le visualizzazioni non vengono aggiornate.

## Rinominare una tabella esterna

La ridenominazione di una tabella esterna modifica il nome della tabella in SQL Data Warehouse. Non influisce sulla posizione dei dati esterni alla tabella.

## Modificare uno schema di tabella
Se si vuole cambiare lo schema a cui appartiene un oggetto, usare l'istruzione ALTER SCHEMA:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## La ridenominazione della tabella richiede un blocco esclusivo sulla tabella

È importante ricordare che non è possibile rinominare una tabella mentre è in uso. Una ridenominazione di una tabella richiede un blocco esclusivo sulla tabella. Se la tabella è in uso, si potrebbe dover terminare la sessione utilizzando la tabella. Per terminare una sessione, è necessario usare il comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Prestare attenzione quando si utilizza ```KILL``` nel momento in cui si termina una sessione e verrà eseguito il rollback di eventuale lavoro non sottoposto a commit. Alle sessioni in SQL Data Warehouse viene aggiunto il prefisso 'SID'. È necessario includere questo e il numero della sessione quando si richiama il comando KILL. Ad esempio, ```KILL 'SID1234'```. Per altre informazioni sulle [sessioni], vedere l'articolo relativo alle connessioni


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[sessioni]: sql-data-warehouse-develop-connections.md

<!---HONumber=AcomDC_0323_2016-->