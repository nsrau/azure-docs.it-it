---
title: Creare un&quot;istanza di SQL Data Warehouse con PowerShell | Documentazione Microsoft
description: Creare un&quot;istanza di SQL Data Warehouse con PowerShell
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Creare SQL Data Warehouse con PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare, è necessario:

* **Account di Azure**: vedere la [versione di valutazione gratuita][versione di valutazione gratuita] o [Crediti Azure MSDN][Crediti Azure MSDN] per creare un account.
* **Server di Azure SQL**: per maggiori dettagli, vedere [Creare un server logico del database SQl di Azure usando il portale di Azure][Creare un server logico del database SQl di Azure usando il portale di Azure] o [Creare un server logico del database SQl di Azure usando PowerShell][Creare un server logico del database SQl di Azure usando PowerShell].
* **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL o vedere [Come creare un gruppo di risorse][Come creare un gruppo di risorse].
* **PowerShell versione 1.0.3 o successiva**: è possibile verificare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**.  È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][Installazione guidata piattaforma Web Microsoft].  Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][Prezzi di SQL Data Warehouse].
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Creare un SQL Data Warehouse
1. Aprire Windows PowerShell.
2. Eseguire questo cmdlet per accedere a Gestione risorse di Azure.
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. Selezionare la sottoscrizione da usare per la sessione corrente.
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Creare il database. Questo esempio crea un database denominato "mynewsqldw", con un livello di obiettivo di servizio "DW400" nel server denominato "sqldwserver1" incluso nel gruppo di risorse "mywesteuroperesgp1".
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

I parametri obbligatori sono:

* **RequestedServiceObjectiveName**: quantità di [DWU][DWU] richiesta.  I valori supportati sono: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
* **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
* **ServerName**: il nome del server che si sta usando per la creazione (deve essere V12).
* **ResourceGroupName**: il gruppo di risorse in uso.  Per trovare i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
* **Edition**: per creare un'istanza di SQL Data Warehouse, deve essere "DataWarehouse".

I parametri facoltativi sono:

* **CollationName**: se non specificate, le regole di confronto predefinite sono SQL_Latin1_General_CP1_CI_AS.  Non è possibile modificare le regole di confronto in un database.
* **MaxSizeBytes**: la dimensione massima predefinita di un database è 10 GB.

Per informazioni dettagliate sulle opzioni dei parametri, vedere [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [CREATE DATABASE (Azure SQL Data Warehouse)][CREATE DATABASE (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile provare a [caricare dati di esempio][caricare dati di esempio] o scoprire come eseguire le attività di [sviluppo][sviluppo], [caricamento][caricamento] o [migrazione][migrazione].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, vedere l'articolo [Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse][Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrazione]: ./sql-data-warehouse-overview-migrate.md
[sviluppo]: ./sql-data-warehouse-overview-develop.md
[caricamento]: ./sql-data-warehouse-load-with-bcp.md
[caricamento di dati di esempio]: ./sql-data-warehouse-load-sample-databases.md
[Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[regole del firewall]: ../sql-database-configure-firewall-settings.md

[Come installare e configurare Azure PowerShell]: ../powershell-install-configure.md
[Procedura: Creare un database SQL Data Warehouse nel portale di Azure]: ./sql-data-warehouse-get-started-provision.md
[Creare un server logico del database SQL di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creare un server logico del database SQL di Azure con Powershell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[CREATE DATABASE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Credito Azure mensile per sottoscrittori di Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


