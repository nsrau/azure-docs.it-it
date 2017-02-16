---
title: Gestire la potenza di calcolo in Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: "Attività di PowerShell per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5749b37408be8eeab6a3fba40d6768ca1d035096


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gestire la potenza di calcolo in Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Panoramica](sql-data-warehouse-manage-compute-overview.md)
> * [Portale](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Ridimensionare le prestazioni tramite la scalabilità orizzontale delle risorse di calcolo e della memoria per soddisfare le diverse esigenze del carico di lavoro. Risparmiare sui costi ridimensionando le risorse durante le ore non di punta o sospendendo completamente il calcolo.

Questa raccolta di attività usa il portale di Azure per:

* Ridimensionare le risorse di calcolo
* Sospendere le risorse di calcolo
* Riavviare le risorse di calcolo

Per altre informazioni, vedere [Gestire la potenza di calcolo][Manage compute overview].

## <a name="before-you-begin"></a>Prima di iniziare
### <a name="install-the-latest-version-of-azure-powershell"></a>Installare la versione più recente di Azure PowerShell.
> [!NOTE]
> Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell 1.0.3 o versione successiva.  Per verificare la versione corrente, eseguire il comando **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente usando l'[Installazione guidata piattaforma Web Microsoft][Microsoft Web Platform Installer].  Per altre informazioni, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introduzione ai cmdlet di Azure PowerShell
Attività iniziali

1. Aprire Azure PowerShell.
2. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Ridimensionare la potenza di calcolo
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare il numero di DWU usare il cmdlet PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Sospendere le risorse di calcolo
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere l'esecuzione di un database, usare il cmdlet [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. L'esempio seguente sospende il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

> [!NOTE]
> Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Come variazione, il database dell'esempio seguente viene recuperato nell'oggetto $database. L'oggetto viene quindi inviato tramite pipe a [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. I risultati vengono archiviati nell'oggetto resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Riavviare le risorse di calcolo
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per avviare un database, usare il cmdlet [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]. L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, il database dell'esempio seguente viene recuperato nell'oggetto $database. L'oggetto viene quindi inviato tramite pipe a [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] e i risultati vengono archiviati in $resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Per altre attività di gestione, vedere [Panoramica della gestione][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/



<!--HONumber=Dec16_HO2-->


