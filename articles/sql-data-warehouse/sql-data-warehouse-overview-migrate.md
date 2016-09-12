<properties
   pageTitle="Eseguire la migrazione della soluzione in SQL Data Warehouse | Microsoft Azure"
   description="Indicazioni sulla migrazione per spostare la soluzione nella piattaforma Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>  

# Eseguire la migrazione della soluzione in SQL Data Warehouse

SQL Data Warehouse è un sistema di database distribuito che può essere scalato in modo elastico secondo le esigenze. Per mantenere le prestazioni e la scalabilità, non tutte le funzionalità di SQL Server vengono implementate all'interno di SQL Data Warehouse. Gli argomenti seguenti trattano alcuni fattori principali da considerare per eseguire la migrazione della propria soluzione in SQL Data Warehouse. La progettazione di data warehouse ai fini della scalabilità introduce modelli di progettazione diversi, pertanto gli approcci tradizionali non si rivelano sempre ottimali. È possibile scegliere di adattare la soluzione esistente per sfruttare completamente i vantaggi della piattaforma distribuita offerta da SQL Data Warehouse.

È anche importante tenere presente che SQL Data Warehouse è una piattaforma basata su Microsoft Azure. Parte della migrazione potrebbe quindi comportare il trasferimento dei dati nel cloud. Il trasferimento dei dati è un elemento a parte da prendere in considerazione con attenzione, soprattutto man mano che i volumi aumentano. Il trasferimento dei dati e il caricamento di dati sono argomenti distinti.

## Indicazioni sulla migrazione

Prima di intraprendere la procedura di migrazione, leggere gli articoli seguenti per essere certi di avere compreso alcune delle differenze di prodotto e i concetti principali.

- [Eseguire la migrazione dello schema][]
- [Eseguire la migrazione dei dati][]
- [Eseguire la migrazione del codice][]

## Passaggi successivi

Il team CAT (Customer Advisory Team) dispone anche di utili istruzioni su SQL Data Warehouse che pubblica tramite blog. Per ulteriori istruzioni sulla migrazione, fare riferimento all'articolo [Migrazione dei dati in SQL Data Warehouse di Azure in pratica][].

<!--Image references-->

<!--Article references-->
[Eseguire la migrazione dello schema]: sql-data-warehouse-migrate-schema.md
[Eseguire la migrazione dei dati]: sql-data-warehouse-migrate-data.md
[Eseguire la migrazione del codice]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->  
[Migrazione dei dati in SQL Data Warehouse di Azure in pratica]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

<!---HONumber=AcomDC_0831_2016-->