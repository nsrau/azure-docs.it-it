<properties
   pageTitle="Ripristinare un'istanza di Azure SQL Data Warehouse (API REST) | Microsoft Azure"
   description="Attività dell'API REST per il ripristino di un'istanza di Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/31/2016"
   ms.author="lakshmir;barbkess"/>


# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Ripristinare un'istanza di Azure SQL Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [REST][]

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse usando l'API REST.

## <a name="before-you-begin"></a>Prima di iniziare

**Verificare la capacità in DTU.**  Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso

Per ripristinare un database:

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][] .

>[AZURE.NOTE] Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][].

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato

Per ripristinare un database eliminato:

1.  Elencare tutti i database eliminati ripristinabili mediante l'operazione [List Restorable Dropped Databases][] .
2.  Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database][] .
3.  Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
4.  Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][] .

>[AZURE.NOTE] Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino][]. 


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, leggere [Panoramica sulla continuità aziendale del database SQL][].

<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale del database SQL]: ./sql-database-business-continuity.md
[richiedere una modifica della quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurare il database dopo il ripristino]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
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



<!--HONumber=Oct16_HO2-->


