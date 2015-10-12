<properties
   pageTitle="SQL dinamico in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni."
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL dinamico in SQL Data Warehouse
Durante lo sviluppo di codice dell'applicazione per SQL Data Warehouse potrebbe essere necessario utilizzare SQL dinamico per offrire soluzioni flessibili, generiche e modulari. Attualmente SQL Data Warehouse non supporta i tipi di dati BLOB. Ciò potrebbe limitare le dimensioni delle stringhe poiché i tipi di BLOB includono tipi varchar(max) e nvarchar(max). Se sono stati utilizzati questi tipi nel codice dell'applicazione durante la creazione di stringhe molto grandi, è necessario separare il codice in blocchi e utilizzare invece l'istruzione EXEC.

Un semplice esempio:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa è corta, è possibile utilizzare [sp\_executesql][] come di consueto.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/it-IT/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->