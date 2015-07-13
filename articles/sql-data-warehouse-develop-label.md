<properties
   pageTitle="Usare etichette per instrumentare query in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Usare etichette per instrumentare query in SQL Data Warehouse
SQL Data Warehouse supporta un concetto detto etichette di query. Prima di approfondire il concetto, eccone un esempio:

	```
	SELECT *
	FROM sys.tables
	OPTION (LABEL = 'My Query Label')
	;
	```

L'ultima riga contrassegna la stringa 'My Query Label' per la query. Ciò è particolarmente utile in quanto l'etichetta supporta la query tramite le DMV. In questo modo si dispone di un meccanismo per tenere traccia di query problematiche e per identificare lo stato di avanzamento tramite un'esecuzione ETL.

Una buona convenzione di denominazione è estremamente utile in questo caso. Ad esempio, una stringa simile a ' PROJECT : PROCEDURE : STATEMENT : COMMENT' può facilitare l'identificazione della query in tutto il codice nel controllo del codice sorgente.

Per la ricerca in base all'etichetta, è possibile usare la query seguente che usa le viste a gestione dinamica:

	```
	SELECT  *
	FROM    sys.dm_pdw_exec_requests r
	WHERE   r.[label] = 'My Query Label'
	;
	``` 

> [AZURE.NOTE]È essenziale racchiudere tra parentesi quadre o virgolette doppie la parola label durante l'esecuzione della query. Label è una parola riservata e causa un errore se non viene delimitata.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->