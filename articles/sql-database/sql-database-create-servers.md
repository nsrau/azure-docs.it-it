---
title: Creare server di database SQL di Azure | Documentazione Microsoft
description: Informazioni di riferimento rapido su come creare database SQL di Azure usando il portale di Azure e PowerShell.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>Creare server di database SQL di Azure

È possibile creare un server di database SQL di Azure usando il [portale di Azure](https://portal.azure.com/), PowerShell, l'API REST o C#. 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>Creare un database SQL di Azure usando il portale di Azure

1. Aprire il pannello **SQL Server** nel [portale di Azure](https://portal.azure.com/). 

    ![server SQL](./media/sql-database-get-started/new-sql-server.png)

2. Fare clic su **Aggiungi** per creare un server SQL

    ![aggiungere nuovo server sql](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> Per un'esercitazione introduttiva sull'uso del portale di Azure e di SQL Server Management Studio, vedere [Introduzione ai server di database SQL di Azure, ai database e alle regole del firewall usando il portale di Azure e SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="create-an-azure-sql-database-server-using-powershell"></a>Creare un server di database SQL di Azure usando PowerShell

Per creare un server di database SQL, usare il cmdlet [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver). Sostituire *server1* con il nome del server. I nomi dei server devono essere univoci nell'ambito di tutti i server di database SQL di Azure. Se il nome del server è già in uso, viene visualizzato un errore. Il completamento di questo comando può richiedere alcuni minuti. Il gruppo di risorse deve già esistere nella sottoscrizione.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Risorse aggiuntive
* Per una panoramica degli strumenti di gestione, vedere [Panoramica degli strumenti di gestione](sql-database-manage-overview.md).
* Per informazioni su come eseguire le attività di gestione usando il portale di Azure, vedere [Gestire un database SQL di Azure tramite il portale di Azure](sql-database-manage-portal.md).
* Per informazioni su come eseguire le attività di gestione usando PowerShell, vedere [Gestire il database SQL di Azure con PowerShell](sql-database-manage-powershell.md).
* Per informazioni su come eseguire le attività di gestione usando SQL Server Management Studio, vedere [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Per informazioni sul servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). 
* Per informazioni sui server di database di Azure e sulle funzionalità di database, vedere [Funzionalità](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


