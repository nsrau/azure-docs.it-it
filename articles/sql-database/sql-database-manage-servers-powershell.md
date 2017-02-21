---
title: 'PowerShell: creare e gestire server di database SQL di Azure | Documentazione Microsoft'
description: Informazioni di riferimento rapido sulla creazione e gestione di server di database SQL di Azure con PowerShell.
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 835702c8381fcd184b056c76054514348c675bbf
ms.openlocfilehash: 9f1905f7e5aad0e43d81f7095089b9f3492a82ea


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>Creare e gestire server di database SQL di Azure con PowerShell

È possibile creare e gestire un server di database SQL di Azure usando il [portale di Azure](https://portal.azure.com/), PowerShell, l'API REST o C#. Questo argomento illustra l'uso di PowerShell. Per il portale di Azure, vedere [Creare e gestire server con il portale di Azure](sql-database-manage-servers-portal.md). 

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
> Per uno script di esempio, vedere [Creare un database SQL con uno script di PowerShell](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Risorse aggiuntive
* Per una panoramica degli strumenti di gestione, vedere [Panoramica degli strumenti di gestione](sql-database-manage-overview.md).
* Per informazioni su come eseguire le attività di gestione usando il portale di Azure, vedere [Gestire un database SQL di Azure tramite il portale di Azure](sql-database-manage-portal.md).
* Per informazioni su come eseguire le attività di gestione usando PowerShell, vedere [Gestire il database SQL di Azure con PowerShell](sql-database-manage-powershell.md).
* Per informazioni su come eseguire le attività di gestione usando SQL Server Management Studio, vedere [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Per informazioni sul servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). 
* Per informazioni sui server di database di Azure e sulle funzionalità di database, vedere [Funzionalità](sql-database-features.md).



<!--HONumber=Feb17_HO1-->


