---
title: Eseguire la migrazione della soluzione in SQL Data Warehouse | Microsoft Docs
description: Indicazioni sulla migrazione per spostare la soluzione nella piattaforma Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Eseguire la migrazione della soluzione in SQL Data Warehouse
SQL Data Warehouse è un sistema di database distribuito che può essere scalato in modo elastico secondo le esigenze. Per mantenere le prestazioni e la scalabilità, non tutte le funzionalità di SQL Server vengono implementate all'interno di SQL Data Warehouse. Gli argomenti seguenti trattano alcuni fattori principali da considerare per eseguire la migrazione della propria soluzione in SQL Data Warehouse. La progettazione di data warehouse ai fini della scalabilità introduce modelli di progettazione diversi, pertanto gli approcci tradizionali non si rivelano sempre ottimali. È possibile scegliere di adattare la soluzione esistente per sfruttare completamente i vantaggi della piattaforma distribuita offerta da SQL Data Warehouse.

È anche importante tenere presente che SQL Data Warehouse è una piattaforma basata su Microsoft Azure. Parte della migrazione potrebbe quindi comportare il trasferimento dei dati nel cloud. Il trasferimento dei dati è un elemento a parte da prendere in considerazione con attenzione, soprattutto man mano che i volumi aumentano. Il trasferimento dei dati e il caricamento di dati sono argomenti distinti.

## <a name="migration-guidance"></a>Indicazioni sulla migrazione
Prima di intraprendere la procedura di migrazione, leggere gli articoli seguenti per essere certi di avere compreso alcune delle differenze di prodotto e i concetti principali.

* [Eseguire la migrazione dello schema][Migrate your schema]
* [Eseguire la migrazione dei dati][Migrate your data]
* [Eseguire la migrazione del codice][Migrate your code]

## <a name="next-steps"></a>Passaggi successivi
Il team CAT (Customer Advisory Team) dispone anche di utili istruzioni su SQL Data Warehouse che pubblica tramite blog.  Per altre istruzioni sulla migrazione, vedere l'articolo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migrazione dei dati in Azure SQL Data Warehouse in pratica).

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

