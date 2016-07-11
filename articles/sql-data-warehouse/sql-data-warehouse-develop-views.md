<properties
   pageTitle="Viste in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di viste Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="06/27/2016"
   ms.author="jrj;barbkess;sonyama"/>


# Viste in SQL Data Warehouse

Le viste sono particolarmente utili in SQL Data Warehouse. Risultano utili in molti modi diversi per migliorare la qualità della soluzione.

Questo articolo evidenzia alcuni esempi di come migliorare la soluzione mediante l'implementazione di viste. Esistono alcune limitazioni che è opportuno considerare.

> [AZURE.NOTE] La sintassi per `CREATE VIEW` non viene illustrata in questo articolo. Per informazioni di riferimento, vedere l'articolo [CREATE VIEW][] su MSDN.

## Astrazione dell'architettura
Un modello di applicazione molto comune consiste nel ricreare le tabelle usando CREATE TABLE AS SELECT (CTAS) seguito da un modello di ridenominazione di oggetti durante il caricamento dei dati.

L'esempio seguente aggiunge nuovi record a una dimensione data. Si noti come un nuovo oggetto, DimDate\_New, viene creato e poi rinominato per sostituire la versione originale dell'oggetto.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ciò può tuttavia comportare la comparsa e scomparsa di oggetti tabella dalla visualizzazione dell'utente in Esplora oggetti di SQL in SSDT. Le viste possono essere usate per fornire ai consumer del data warehouse un livello di presentazione coerente, mentre gli oggetti sottostanti vengono rinominati. Fornire l'accesso ai dati tramite una vista significa che gli utenti non devono necessariamente avere la visibilità delle tabelle sottostanti. Questo approccio offre un'esperienza utente coerente, assicurando che le finestre di progettazione del data warehouse siano in grado di evolvere il modello di dati e anche di ottimizzare le prestazioni usando CTAS durante il processo di caricamento dei dati.

## Ottimizzazione delle prestazioni
Le visualizzazioni sono un modo efficace per applicare join tra le tabelle ottimizzati per le prestazioni. Ad esempio, la vista può incorporare una chiave di distribuzione ridondante come parte dei criteri di join per ridurre al minimo lo spostamento dei dati. Un altro motivo potrebbe essere l'applicazione di una query specifica o un hint di join. Ciò assicura che il join sia sempre eseguito in modo ottimale e non dipenda dalla possibilità che l'utente ricordi di costruire il join correttamente.

## Limitazioni
Le viste in SQL Data Warehouse sono solo metadati.

Non sono quindi disponibili le opzioni seguenti:
- 	Non esiste alcuna opzione di binding dello schema
- 	Le tabelle di base non possono essere aggiornate tramite la vista
- 	Non è possibile creare visualizzazioni sulle tabelle temporanee
- 	Non è previsto alcun supporto per gli hint EXPAND/NOEXPAND
- 	Non sono disponibili viste indicizzate in SQL Data Warehouse


## Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][]. Per la sintassi di `CREATE VIEW`, vedere [CREATE VIEW][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/it-IT/library/ms187956.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->