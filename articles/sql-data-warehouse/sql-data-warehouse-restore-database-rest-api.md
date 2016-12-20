---
title: Ripristinare un&quot;istanza di Azure SQL Data Warehouse (API REST) | Microsoft Docs
description: "Attività dell&quot;API REST per il ripristino di un&quot;istanza di Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Ripristinare un'istanza di Azure SQL Data Warehouse (API REST)
> [!div class="op_single_selector"]
> * [Panoramica][Panoramica]
> * [Portale][Portale]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse usando l'API REST.

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.**  Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la quota di DTU necessaria o per richiedere altri DTU, vedere [richiedere una modifica della quota DTU][richiedere una modifica della quota DTU].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso
Per ripristinare un database:

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino tramite l'operazione [Create database restore request][Create database restore request].
3. Tenere traccia dello stato del ripristino mediante l'operazione [Database Operation Status][Database Operation Status].

> [!NOTE]
> Dopo aver completato il ripristino, è possibile configurare il database ripristinato seguendo [Configurare il database dopo il ripristino][Configurare il database dopo il ripristino].
> 
> 

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Per ripristinare un database eliminato:

1. Elencare tutti i database eliminati ripristinabili mediante l'operazione [List Restorable Dropped Databases][List Restorable Dropped Databases].
2. Ottenere i dettagli del database eliminato che si desidera ripristinare tramite l'operazione [Get Restorable Dropped Database][Get Restorable Dropped Database].
3. Iniziare il ripristino tramite l'operazione [Create database restore request][Create database restore request].
4. Tenere traccia dello stato del ripristino mediante l'operazione [Database Operation Status][Database Operation Status].

> [!NOTE]
> Per configurare il database dopo aver completato il ripristino, vedere [Configurare il database dopo il ripristino][Configurare il database dopo il ripristino].
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle funzionalità di continuità aziendale delle edizioni di database SQL di Azure, leggere [Panoramica sulla continuità aziendale del database SQL][Panoramica sulla continuità aziendale del database SQL].

<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale del database SQL]: ../sql-database/sql-database-business-continuity.md
[richiedere una modifica della quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurare il database dopo il ripristino]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Come installare e configurare Azure PowerShell]: ./powershell-install-configure.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database Operation Status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get Restorable Dropped Database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List Restorable Dropped Databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[portale di Azure]: https://portal.azure.com/
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


