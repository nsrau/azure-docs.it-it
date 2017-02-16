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
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 2b78101f6abd675487c7879de5440021832af181


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Creare SQL Data Warehouse con PowerShell
> [!div class="op_single_selector"]
> * [portale di Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare, è necessario:

* **Account Azure**: per creare un account, vedere la [versione di valutazione gratuita][Azure Free Trial] o [MSDN Azure Credits][MSDN Azure Credits] (Crediti Azure MSDN).
* **Server SQL di Azure**: per altre informazioni dettagliate, vedere [Create an Azure SQL Database logical server with the Azure Portal][Create an Azure SQL Database logical server with the Azure Portal] (Creare un server logico del database SQL di Azure con il portale di Azure) o [Creare un server logico del database SQL di Azure con PowerShell][Create an Azure SQL Database logical server with PowerShell].
* **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL oppure vedere [Creare un gruppo di risorse](../azure-resource-manager/resource-group-portal.md).
* **PowerShell versione 1.0.3 o successiva**: è possibile verificare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**.  È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][Microsoft Web Platform Installer].  Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per altre informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][SQL Data Warehouse pricing].
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

Per altre informazioni dettagliate sulle opzioni dei parametri, vedere [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [Create Database (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile provare a [caricare dati di esempio][loading sample data] o scoprire come eseguire le attività di [sviluppo][develop], [caricamento][load] o [migrazione][migrate].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, vedere l'articolo sull'uso dei [cmdlet di PowerShell e le API REST][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-logical-server-bk
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Dec16_HO1-->


