---
title: Ripristinare un'istanza di Azure SQL Data Warehouse (API REST) | Microsoft Docs
description: "Attività dell'API REST per il ripristino di un'istanza di Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 8739429342d3c8bbe0f09041976758276a9c3228
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Ripristinare un'istanza di Azure SQL Data Warehouse (API REST)
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Portale][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse usando l'API REST.

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere altre DTU, vedere come [richiedere una modifica della quota DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso
Per ripristinare un database:

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino usando l'operazione [Create database restore request][Create database restore request].
3. Monitorare lo stato del ripristino tramite l'operazione [Database Operation Status][Database operation status].

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Per ripristinare un database eliminato:

1. Elencare tutti i database eliminati ripristinabili tramite l'operazione [List Restorable Dropped Databases][List restorable dropped databases].
2. Ottenere i dettagli del database eliminato da ripristinare tramite l'operazione [Get Restorable Dropped Database][Get restorable dropped database].
3. Iniziare il ripristino usando l'operazione [Create database restore request][Create database restore request].
4. Monitorare lo stato del ripristino tramite l'operazione [Database Operation Status][Database operation status].

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino][Configure your database after recovery].
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, vedere [Panoramica sulla continuità aziendale del database SQL di Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
